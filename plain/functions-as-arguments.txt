# functions-as-arguments

void takes_function(void f(int))

{

     f(5);

}

same as

void takes_function(void (*f)(int))

{

     f(5);

}

In both cases you could pass a nullptr or 0. Not with a function
reference:

void takes_function(void (&f)(int))

{

     f(5);

}

takes_function(nullptr); // no matching function
