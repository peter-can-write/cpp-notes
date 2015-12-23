# inheritance-and-the-slicing-problem

When passing an object of derived type to a function that takes an
object of base type by value, the function will intialize the base type
parameter only with the parts of the derived type that are also part of
the base class (only the members that actually belong to the base
class).

E.g.:

// A base class with a virtual function

class Base

{

public:

     

     virtual void something() {std::cout << “Base”; }

int a;

};

// Derived class

class Derived : public Base

{

public:

     void something() { std::cout << “Derived”; }

int b;

};

Now, given a function that takes a Base object by value, passing it an
object

of Derived will “slice” that Derived object, constructing the “base”
parameter

only with the members that are part of Base. Also, calling a virtual
function

will therefore call the Base’s version. 

void foo(Base base)

{

     base.b; // won’t work - base has no member “b"

     base.something(); // will print “Base"

}

I mean, it does make sense, since Polymorphism only works with pointers
or their auto-dereferenced brothers, references.

// What you’d want:

void foo(Base* base);

// Or:

void foo(const Base& base);


