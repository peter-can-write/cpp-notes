# noexcept-and-exception-specifications

It is possible to specify whether and what exceptions a function may
throw. While a now deprecated feature, one could formerly specify
exceptions of a function like so:

void foo() throw();

void bar() throw(std::runtime_error);

However, this lead to certain problems and is thus no longer
supported. Nevertheless, in C++11 you can still specify that a function
will not throw any exception (nothrow guarantee):

void foo() noexcept;

This is not always necessary, but may lead to performance improvements
(http://stackoverflow.com/questions/10787766/when-should-i-really-use-noexcept):

Using noexcept in the big 4 (constructors, assignment, not destructors
as they'll already noexcept) will likely cause the best improvements
as noexcept checks are 'common' in template code such as in std
containers. For instance, std::vector won't use your class's move unless
it's marked noexcept (or the compiler can deduce it otherwise).

I’d say, use noexcept for template constructors, copy-constructors and
copy-assignment operators and for MOVE OVERLOADS (CONSTRUCTORS/
COPY-ASSIGNMENT OPERATORS).

When you overload the copy constructor and the copy assignment operator of a class for the sake of move semantics, it is very much recommended that you do the following:

-   Strive to write your overloads in such a way that they cannot throw exceptions. That is often trivial, because move semantics typically do no more than exchange pointers and resource handles between two objects.
-   If you succeeded in not throwing exceptions from your overloads, then make sure to advertise that fact using the new noexcept keyword.

If you don't do both of these things, then there is at least one very common situation where your move semantics will not be applied despite the fact that you would very much expect it: when an std::vector gets resized, you certainly want move semantics to happen when the existing elements of your vector are being relocated to the new memory block. But that won't happen unless both of 1. and 2. above are satisfied.

http://thbecker.net/articles/rvalue_references/section_09.html
