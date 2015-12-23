# general-things

-   Use always const and & for function parameters, when possible.

-   Check expressions in bool-returning functions directly in
    return statement.

-   Pass functions as parameters just via: _return_type_ NAME (_args_)

-   In functions returning VOID, you can nevertheless do: RETURN; //
    leaves function

-   Declare static, always constant items in FUNCTIONS as static, for
    example a string which is always searched. It is then only declared
    once and stored without re-declaring each time

-   Use CONST member functions when possible

-   Think recursively, from big problem down to small problem

-   Don’t forget to consider whether you could use a reference instead
    of a pointer

-   Use const and & for any parameters except built-in types and std::
    iterators and functions

-   RAII - Always use objects to manage allocated resources!

-   Define all functions in implementation files. 
-   You can use forward declarations for function return types.
-   Use STD::MOVE to move resources from one unique pointer to the next.
-   The order of evaluation of function parameters is undefined.
-   Use the COPY-AND-SWAP IDIOM for copy-assignment operators.
-   Use std::size_t for any sizes (e.g. loop indices) if a higher range
    than unsigned int is required
-   For class templates, the class name (e.g. Foo) is shorthand for
    Foo<T> inside the class.
-   Use explicit when applicable, mainly for single-argument
    constructors (no implicit type conversions allowed)
-   Use using name = type instead of typedef type name
-   Inline is almost redundant, because function definitions in headers
    are always inlined
-   Inline is a demand to the compiler to inline a method, it will still
    decide what is best
-   Specify the input capture lambdas as precisely as possible, don’t
    always use & automatically
-   Don’t forget to still use const if necessary for range-based for
    loops
-   Try to use constexpr when possible and necessary
-   Make use of the OVERRIDE and FINAL keywords


