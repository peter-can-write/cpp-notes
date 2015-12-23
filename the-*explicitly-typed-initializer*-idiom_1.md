# the-*explicitly-typed-initializer*-idiom_1

Use the _explicitly-typed-initializer_ idiom when casting. E.g. when you
have a function returning a double, but you want to use less memory and
are fine with a float, you can just store it in a float. However, rather
than specifying float as a type, you should rather deliberately make
clear that you are reducing the precision of the type. Same goes for
integral types (where a warning for a reduction of bitwidth is issued
anyway).

    double calculate_epsilon()
    {
        return /* ... */;
    }

    // NO!
    float epsilon = calculate_epsilon();

    // YES!
    auto epsilon = static_cast<float>(calculate_epsilon());
