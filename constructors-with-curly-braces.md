# constructors-with-curly-braces

You don’t need to use ‘=‘ for initializer lists. Moreover, when a class
has no constructor that takes an std::initializer_list, an appropriate
normal constructor is called, so this is actually cleaner:

foo f {1,2};

than

foo(1,2);

unless foo has a constructor that takes an std::initializer_list, the
first will call the second, but avoid some pitfalls like unwanted
function declaration.


