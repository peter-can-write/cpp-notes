# consider-using-emplace-instead-of-insert

Consider this code:

std::vector<std::string> strings;

strings.push_back("hello");

Here, what happens in the call to push_back is:

1.  A temporary std::string is constructed from the const char[] passed
    to push_back
2.  When a new std::string is constructed in the uninitialized memory of
    the vector, the temporary string from (1) is passed to the
    move-constructor of the actual std::string in the vector.
3.  The temporary std::string is destructed.

However, imagine the “hello” literal could have been passed to the
std::string in the vector directly, then the temporary std::string would
never have been constructed. This is why C++11 adds emplace_back:

strings.emplace_back("hello");

Which directly constructs the object in the vector with the arguments.
It uses perfect forwarding and its signature is:

template< class... Args >

void emplace_back( Args&&... args );

I.e. it simply passes all the arguments on to the actual object being
constructed in the memory of the vector.

Simple test, given this class:

class X
 {
public:
 X(int a) { std::cout << "Constructor\n"; }
 X(const X& other) { std::cout << "Copy\n"; }
 X(X&& other) noexcept { std::cout << "Move\n"; }
X& operator=(const X& other) { std::cout << "Assign\n"; return *this; }
 ~X() { std::cout << "Destruct\n"; }

};

And a vector of x:

std::vector<X> v;

push_back:

// Constructor
// Move

// Destruct

v.push_back(5);

emplace_back:

// Constructor

v.emplace_back(5);

Much better! This also works for emplace_front and emplace instead of
push_front or insert (emplace takes an iterator). At the end of the day,
emplacement functions will very often outperform insertion functions,
but not always. They are never slower, though. Heuristics to judge if
they are faster (else _equally_ fast):

-   The value being added is constructed into the container,
    not assigned. Basically, when you add the element into memory where
    there is not yet an element, usually front or back. Because if you
    emplace at a position where there already exists an element,
    move-assignment will take place and this requires construction of a
    temporary object, which would have also been created via insert.
-   The argument type(s) differ from the type held by the container.
    This is where emplacement really shines, since the arguments can be
    passed to the constructor of the object in the container directly.
    If you pass an object of the type, it will be just as fast as
    insertion (same operation then).
-   The container is unlikely to reject the value as a duplicate. For
    insertion, a temporary object would be created and compared against
    nodes in the container. If none match (no duplicate), a new node is
    created and the temporary object is move-constructed into the object
    in the node. For emplacement, usually a node is created either way.
    If there is no duplicate, this node is linked in right away, so in
    that case emplacement would be faster (no extra move-assignment +
    destruction). If however, the object is already present, the node is
    destroyed and the construction goes wasted. Then there will be one
    construction + one destruction, just like when you insert and there
    is a duplicate. So in that case it would not be faster.

However, you have to be careful with resource management and
emplacement. Imagine you have a container of shared_ptrs:

std::list<std::shared_ptr<int>> l;

Now, you want emplace_back a new shared_pointer in the list:

l.emplace_back(new int(5));

But what if, during emplacement, an out-of-memory exception is thrown?
The resource is leaked. This would not have happened with the temporary
shared_ptr created with push_back. So in this case, either use push_back
or remember to be explicit the construction.

Lastly, note that emplacement uses direct-initialization (Object
object(arg)) and not copy-initialization (Object object = arg), meaning
you can pass arguments to explicit constructors which wouldn’t work with
push_back:

std::list<std::unique_ptr<int>> l;

l.push_back(new int(5)); // no matching constructor -> explicit

l.push_back(std::make_unique<int>(5)); // would need this

But with emplacement it would work:

l.emplace_back(new int(5));


