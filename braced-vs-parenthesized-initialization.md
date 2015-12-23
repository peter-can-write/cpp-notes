# braced-vs-parenthesized-initialization

In C++11, there are generally four different ways to initialize a
variable:

```C++
int a(0);  // parenthesized initialization

int b = 0; // copy-initialization (calls copy constructor)

int c{0}; // braced-initialization

int d = {0}; // same as above
```

The latter two, "braced-initialization", are a new intialization-syntax introduced in C++11 that supposedly offer a uniform means of initialization across a variety of situations. Uniform initialization -- also termed "braced-initialization" -- prevails in all of the following contexts:

1. Initializing an object with a(n) (initializer) list of items:

```C++
std::vector<int> v = {1, 2, 3};
std::vector<int> v{1, 2, 3};
```

Such initialization was not possible with C++98, and items would have had to be inserted individually. No other initialization method ( `()` or `=` ) allow such specification of initial elements of a container.

2. Braces can be used to specify default initialization values for non-static data members of a class. The ability to perform such initialization outside of a constructor was generally only added with C++11. Brace-initialization shares this capability the `=`-syntax, while it is not allowed with parentheses:

```C++
class Foo
{
public:

	...
private:

	int x = 5; // OK
	int y{7};  // OK
	int z(y);  // not allowed (!)
}
```

3. Uniform initialization is also available when dealing with the distinction between direct and copy-initialization. Objects with explicit constructors may only be constructed via parentheses, or now also with braces (both direct initialization), but never with `=` (copy initialization):

```C++
std::unique_ptr<int> p(new int(5)); // OK
std::unique_ptr<int> p{new int(5)}; // OK
std::unique_ptr<int> p = new int(5); // not allowed (!)
```

As one can see, uniform initialization is possible in all of these cases, while one of the other forms is not always allowed. Uniform initialization has other properties too:

* It prevents narrowing conversions. In detail, if a constructor were called with an argument that had to be cast to the type of the constructor's argument, and that cast might be lossy, i.e. the original value may not be representable with that type, then braced-initialization will prevent such a constructor to be called. This feature was not introduced for the other initialization methods (for legacy-code reasons):

```C++
int x{1.0 + 2.0}; // Error, type 'double' cannot be narrowed to 'int'

double y{static_cast<int>(5)}; // OK (static_cast for clarity)

int n = 5;
long z = {n}; // OK
```

* It also solves the "most-vexing-parse" problem, which is the result of a rule that anything that can be parsed as a (function) declaration must be intepreted as one. It is this case:

```C++
std::string s(); // Default construct? No! Function declaration.
```

While, of course, you should just not put any form of parentheses at all to call the default-constructor, braces would have saved you from thinking about it:

```C++
std::string s{}; // Same as: std::string s;
```

However, there is one __major__ detriment to braces and it is `std::initializer_list`-related. If a class declares a constructor taking a `std::initializer_list`, it is __strongly__ favored over other constructors. If there is *any* way to convert types to make the constructor taking a `std::initializer_list` the best match, the compiler __will__ interpret the code as such. More precisely, it will not shy away from any type-conversions, even considering class' implicit conversion-operators, to try to convert the arguments of the brace-list to the type of the `std::initializer_list`. The compiler will even go as far as to ignore constructors with *exact-match* arguments in favor of picking a constructor with an extremely odd and bad-match initializer list, just to find out that the conversion that would work outside of the context of uniform initialization (the braces), is not allowed in the braces because it is a narrowing-conversion, upon which it will throw an exception *rather than picking the best-match constructor*.

```C++
struct Foo
{
	Foo(int, double); // (1)

	Foo(double, char); // (2)

	Foo(std::initializer_list<bool>); // (3)
};

Foo(3, 4.5);   // Calls (1) OK
Foo(2.1, 'x'); // Calls (2) OK

Foo{3, 4.5}; // Calls (3), throws an error because conversion would narrow!
Foo{2.1, 'x'} // Calls (3), throws an error because conversion would narrow!

// Also implicit conversions:

struct Bar { operator int() { return 5; } };

Foo{Bar(), Bar()}; // Calls (3), throws an error because conversion would narrow!
```

This may also affect move and copy construction when classes have implicit conversion operators:

```C++
struct Foo
{
 	Foo(std::initializer_list<int>); // (1)

	Foo(const Foo&); // (2)

	Foo(Foo&&); (3)

	operator int() { return 5; }
};

Foo foo;

Foo bar(foo); // Calls (2), as expected

Foo baz = std::move(foo); // Calls (3), as expected


Foo bar{foo}; // Calls (1) !!

Foo baz{std::move(foo)}; // Calls (1) !!
```

There are two more pieces of information:
1. By convention/standard, empty braces call the constructor taking no arguments, and not one taking a `std::initializer_list` (else the whole most-vexing-parse story would be even more vexing). To pass an empty initializer-list to the constructor taking a `std::initializer_list`, wrap the empty braces in parentheses or braces: `std::vector({})` or `std::vector{{}}`.
2. It can be hard to decide which constructor to call in a forwarding template class. E.g. if you have a factory method, which initialization is better? Did the user expect braces or parantheses? `std::unique_ptr` had this problem, and they picked parentheses, which they document.

My rules:

-   Use parentheses to call non-initializer-list constructors, unless
    braces are necessary to prevent a narrowing-conversion or to avoid the most vexing parse.
-   Use braces exclusively for initializer-lists as well as for not specifying the type explicitly in a return statement or nested construction. (e.g. return {a, b}; for a pair)
