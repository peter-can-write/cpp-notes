# viewing-types

How do you view types (automatically deduced) at runtime? Easy guess is
typeid, but it has one caveat. Its specification mandates that it
interprets the type as if it had been passed to template function taking
its argument by value, meaning it removes references and
cv-qualifications:

    template<typename T>
    void f(const T& value)
    {
        std::cout << typeid(value).name() << std::endl;
    }

    int x = 5;

    // Prints int (i) rather than const int& (KiR)
    f(x);

The solution is to use the boost::typeindex library:

    #include <boost/type_index.hpp>

    template<typename T>
    void f(const T& value)
    {
        using boost::typeindex::type_id_with_cvr;

        auto representation = type_id_with_cvr<decltype(value)>();

        std::cout << representation.pretty_name() << std::endl;
    }

    int x = 5;

    // Prints int const&
    f(x);
