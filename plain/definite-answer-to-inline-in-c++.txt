# definite-answer-to-inline-in-c++

inline is more like static or extern than a directive telling the
compiler to inline your functions. extern, static, inline are linkage
directives, used almost exclusively by the linker, not the compiler.

It is said that inline hints to the compiler that you think the function
should be inlined. That may have been true in 1998, but a decade later
the compiler needs no such hints. Not to mention humans are usually
wrong when it comes to optimizing code, so most compilers flat out
ignore the 'hint'.

static the variable/function name cannot be used in other compilation
units. Linker needs to make sure it doesn't accidentally use a
statically defined variable/function from another compilation unit.

extern use this variable/function name in this compilation unit but
don't complain if it isn't defined. The linker will sort it out and make
sure all the code that tried to use some extern symbol has its address.

inline this function will be defined in multiple compilation units,
don't worry about it. The linker needs to make sure all compilation
units use a single instance of the variable/function.

Note: Generally declaring templates inline is pointless, as they have
the linkage semantics of inline already. However, explicit
specialization and instantiation of templates require inline to be used.

------------------------------------------------------------------------

So specific answers to your questions:

-   When should I write the keyword 'inline' for a function/method in
    C++?

Only when you want the function to be defined in a header. More exactly
only when the function's definition can show up in multiple compilation
units. It's a good idea to define small (as in one liner) functions in
the header file as it gives the compiler more information to work with
while optimizing your code. It also increases compilation time.

-   When should I not write the keyword 'inline' for a function/method
    in C++?

Don't add inline when you think your code will run faster if the
compiler inlines it.

-   When will the the compiler not know when to make a function/method
    'inline'?

Generally the compiler will be able to do this better than you. However,
the compiler doesn't have the option to inline code if it doesn't have
the function definition. In maximally optimized code usually all private
methods are inlined whether you ask for it or not.

As an aside to prevent inlining in GCC
use __attribute__(( noinline )) and in visual studio
use __declspec(noinline).

-   Does it matter if an application is multithreaded when one writes
    'inline' for a function/method?

Multithreading doesn't affect inlining in any way.


