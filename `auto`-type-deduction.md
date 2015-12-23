# `auto`-type-deduction

The type deduction rules for auto are largely the same as for templates.
In an expression like this:

const auto x = 5;

auto takes the role of T in template type deduction and the _type
specifier_ (const auto) takes the role of the parameter type Type.

Thus all these cases will be the same as for template type deduction:

    auto x = 5; // auto deduces to int

    const auto& rx = x; // auto deduces to int, the specifier to const int&

    auto&& urx1 = x; // auto deduces to int&, the specifier too

    auto&& urx2 = 27; // auto decues to int, the specifier to int&&

    auto* px = &x; // auto deduces to int, the specifier to int*

    int arr [2] = {1, 2};

    auto a = arr; // auto deduces to int* (array decays)

    auto& ra = arr; // auto deduces to int (&) [2]

    void f(int, std::string);

    auto g = f; // auto deduces to void (*) (int, std::string)

    auto& g = f; // auto deduces to void (&) (int, std::string)

There is __one__ important difference, regarding initializer-lists:
template-type deduction for a brace-initialized sequence fails, while
auto will deduce it as an std::initializer_list<T> after T has been
deduced from the elements in the list (if possible, i.e. if all elements
in the list have a single type, else fails too). Examples:

    auto a = 5;     // auto -> int
    auto b(5);      // auto -> int
    auto c = {5};   // auto -> std::initializer_list<int>
    auto d{5};      // auto -> std::initializer_list<int> or int -- CAREFUL!
    auto e = {5, 1.0};   // fails (not only one type)

Special care must be taken for d. As of now, this does initialize an
std::initializer_list. To ensure uniform initialization capabilities,
however, this will _very soon_ change to initialize an integer and many
compilers already implement it while others warn about it. So if you
want to store an initializer list in an auto variable, use = {...} and
not {...}.

Why does auto differ from template type deduction for initializer lists?
Because template type deduction never implicitly converts anything.
Consider this case:

    template<typename T>
    void assign(T& target, const T& source);

    std::vector<int> v;

    assign(v, {1, 2, 3});

In this case, were {1, 2, 3} automatically deduces to be
std::initializer_list<int>, this method call would fail because the
types for T would differ. Without this automatic deduction (currently in
place), T is deduces to be std::vector<int> and {1, 2, 3} initializes
the source vector correctly.

The fact that auto automatically deduces a brace-list as an
std::initializer_list is a polarizing topic and there exist proposals
also to ban it.

A last note about auto in lambdas or return types: there, template type
deduction rules apply and not auto type deduction rules:

    template<typename Function>
    void f(Function function)
    {
        // Fails, template type deduction does not automatically
        // deduce this as std::initializer_list
        function({1, 2, 3});    
    }

    auto g()
    {
        // Also fails for same reasons as above.
        return {1, 2, 3};
    }

    // Initializes a valid std::initializer_list
    auto list = {1, 2, 3};
