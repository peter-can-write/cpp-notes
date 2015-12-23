# template-type-deduction

Normal Arguments

When dealing with template-type deduction and non-array and non-function
types, i.e. normal data types such as int or std::string, there are
three cases for template type deduction. In all cases, consider the
following function signature:

    template<typename T>
    void f(Type parameter)
    {
        /* ... */
    }

    f(expression)

where f is a template function, T is the _template type_, Type the type
deduced for parameter, f(expression) a function call to f and expression
an expression passed to f.

Case 1: Type is a reference or pointer, but not a universal reference
(T&&).

In this case, there are two common rules for what T will be depending on
Type:

1.  If Type is a reference, ignore it.
2.  Then pattern-match T against Type, ignoring common qualifiers
    between Type and expression for the deduction of T.

_Example 1: T&_

    template<typename T>
    void f(T& parameter)
    {
        /* ... */
    }

    int x = 5;          // int
    const int cx = x;   // const-int
    const int& rx = x;  // reference to const-int

    // Type is int&, for T, ignore the &. Then T = int
    f(x);

    // Type is const int&, ignore the &.
    // Pattern-matching T& against const int&
    // makes T have to be const int
    f(cx);

    // Type is const int& (& & collapses to &), ignore the &
    // Same as above then, T is const int
    f(rx);

_Example 2: const T&_

    template<typename T>
    void f(const T& parameter)
    {
        /* ... */
    }

    int x = 5;          // int
    const int cx = x;   // const-int
    const int& rx = x;  // reference to const-int

    // Type is const int&. For T: ignore the &, "fill in" `int` as `T` in const T&
    f(x);

    // Type is const int&. For T: ignore the &, pattern-match cont int& of
    // the expression against the const int& of the Type, and fill in the
    // unknown, you're left with T being int (ignore qualifiers already
    // present in Type and expression)
    f(cx);

    // Type is const int& (& & collapses to &), then same as above
    f(rx);

For pointers, it is quite the same.

_Example 3: T*_

    template<typename T>
    void f(T* parameter)
    {
        /* ... */
    }

    int x = 5;          // int
    const int* px = &x; // pointer to const-int

    // Type is int*, fill in `int` for `T`
    f(&x);

    // Type is const int*, ignore the *, then fill in `const int` for `T`
    f(px);

Case 2: Universal References

Universal references are declared as T&& and use special _reference
collapsing rules_, whereby, for example int& && collapses to int& or
int&& && collapses to int&&. There are important rules regarding whether
or not the expresssion passed is an lvalue or an rvalue:

-   If the expression is an lvalue, T and Type are deduced as
    __lvalue references__. This means that this is the only situation in
    which T can be a reference type!
-   If the expression is an rvalue, the normal deduction rules of case
    1 apply.

    template<typename T>
    void f(T&& parameter)
    {
        /* ... */
    }

    int x = 27;         // int
    const int cx = x;   // const-int
    const int& rx = x;  // reference to const-int


    // x is an lvalue, thus it is passed as an lvalue reference.
    // `int& &&` then collapses to `int&` for the `Type` as well as `T`
    // (You can again think of filling in the `int&` for `T`)
    f(x);

    // same collapsing rules and explanation as above, making `Type` and `T`
    // `const int&` (lvalues)
    f(cx);

    // same as f(x), just here the reference is explicit
    // while it is implicitly added for f(x)
    f(rx);

    // Now the expression is an rvalue instead of an lvalue, so nothing special
    // happens (no implicit lvalue to reference-to-lvalue conversion)
    // Type is `int&&` and filling in for `T` makes T = int
    f(27);

Case 3: Pass-by-value

When the parameter signature does not include a pointer or reference,
the rules are slightly difference, but very sensical. The important
thing to note is that pass-by-value means copying, which in turn means
that just because an expression was cv-qualified does not mean that the
copy also has to be cv-qualified. The rules for deduction of T are thus:

1.  Ignore the & in expression's type (as before).
2.  Ignore also cv-qualifications.

_Example 1: passing non-pointers_

    template<typename T>
    void f(T parameter)     // note the pass-by-value
    {
        /* ... */
    }

    int x = 5;          // int
    const int cx = x;   // const-int
    const int& rx = x;  // reference to const-int

    // T and Type are int
    f(x);

    // Just because cx is const does not mean we'll not want to modify
    // its copy (else we'd take a reference), thus ignore the cv-qualifications
    // (ignore the const, if it were volatile, also ignore that)
    // Type = T = int
    f(cx);


    // Ignore the & as always (specifically here though the copy should be
    // a new object, not a reference to the old one), and then also ignore
    // the cv-qualifications. Type = T = int
    f(rx);

For pointers, the situation is a little different. Specifically,
consider the case that you have a pointer pointing to a const-object
(const T*). You do not wish to modify this object. When you pass a
pointer by value, the copied pointer will point to the same memory
address. However, it should also not modify the original value, because
it is not a new value as in the non-pointer case, thus it only makes
sense that the deduced type be const T* in such a case, However, the
argument that "copied values should be independent of their source
objects" still applies. If the pointer itself is const, i.e. T* const,
then that means you cannot point that pointer anywhere else anymore.
That would apply to this pointer, but shouldn't apply to a copy by the
same logic as for non-pointer values. Thus the deduced type would be T*.
In summary const T* const would thus deduce to const T* because the
const after the * applies to the pointer itself while the const before
the T refers to the type pointed to by T.

_Example 2: pointers_

    template<typename T>
    void f(T parameter)     // note the pass-by-value
    {
        /* ... */
    }

    int x = 5;          // int
    const int* px = &x; // pointer to const-int
    const int* const cpx = &x; // const-pointer to const-int

    // Type and T deduced to int* (* is not removed)
    f(&x);

    // The copy of cx should also not modify x, thus Type = T = const int*
    f(cx);

    // The copy of cx should also not modify x, but just because we cannot
    // re-point cpx doesn't mean we shouldn't be able to re-point its copy
    // which is entirely independent of cpx. Thus Type = T = const int*
    f(rx);


Array Arguments

For array arguments, e.g. int a [3];, there are two main rules:

1.  If Type is T, the array decays to a pointer to the first element in
    the array.
2.  If Type is T&, the array does not decay and T really is a reference
    to an array type, e.g. int (&) [N]. This means also the length N is
    passed to the function. CV-qualifiers are not dropped.

_Example 1: T_

    template<typename T>
    void f(T array)
    {
        /* ... */
    }

    int array [] = {1, 2, 3};

    // The array will decay to a pointer, making `T = int*`
    f(array);

_Example 2: T&_

    template<typename T>
    void f(T& array)
    {
        /* ... */
    }

    const int array [] = {1, 2, 3};

    // Array is taken by reference, T is of type `const int [3]`
    f(array);

Note that the above function signature is equivalent to the following:

    template<typename T, std::size_t N>
    void f(T (&array) [N]);

When using [] additionally it must include the size, though. Meaning
this is not valid:

    template<typename T>
    void f(T (&array) []);

_Example 3: Determining the length of an array due to template type
deduction._

    template<typename T, std::size_t N>
    constexpr std::size_t array_length(const T (&) [N]) noexcept
    {
        return N;
    }

