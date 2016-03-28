# Noexcept and Exception Specifications

It is possible to specify whether and what exceptions a function may
throw. While a now deprecated feature, one could formerly specify exceptions of
a function like so:

void foo() throw();

void bar() throw(std::runtime_error);

However, this lead to certain problems and is thus no longer
supported. Nevertheless, in C++11 you can still specify that a function will not
throw any exception (nothrow guarantee):

void foo() noexcept;

This is not always necessary, but may lead to performance improvements
(http://stackoverflow.com/questions/10787766/when-should-i-really-use-noexcept):

Using noexcept in the big 4 (constructors, assignment, not destructors as
they'll already noexcept) will likely cause the best improvements as noexcept
checks are 'common' in template code such as in std containers. For instance,
std::vector won't use your class's move unless it's marked noexcept (or the
compiler can deduce it otherwise).

I’d say, use noexcept for template constructors, copy-constructors and
copy-assignment operators and for move overloads (constructors/ copy-assignment
operators), swap functions and memory (de-)allocation functions. Destructors are
implicitly declared noexcept, unless any data member (inherited or not) declares
its destructors noexcept(false).

When you overload the copy constructor and the copy assignment operator of a
class for the sake of move semantics, it is very much recommended that you do
the following:

* Strive to write your move overloads in such a way that they cannot throw
  exceptions. That is often trivial, because move semantics typically do no more
  than exchange pointers and resource handles between two objects.
* If you succeeded in not throwing exceptions from your overloads, then make
  sure to advertise that fact using the new noexcept keyword.

If you don't do both of these things, then there is at least one very common
situation where your move semantics will not be applied despite the fact that
you would very much expect it: when an std::vector gets resized, you certainly
want move semantics to happen when the existing elements of your vector are
being relocated to the new memory block. But that won't happen unless both
of 1. and 2. above are satisfied.

http://thbecker.net/articles/rvalue_references/section_09.html

When you declare a method noexcept, the compiler can make certain optimizations
that would not be possible with a C++98 throw() declaration or no exception
specification at all. More specifically, the compiler is allowed to terminate a
program as soon as an exception is emitted in a.  noexcept function, therefore
not releasing the exception to the lower stacks for error handling. Also, the
C++11 noexcept keyword is more strict than the C++98 throw() specification: for
a function declared noexcept, the compiler need not keep the stack of the
function in an unwindable state, meaning variables with automatic storage
duration allocated on the stack within the function may not be deallocated (as
is required in normal situations). The program would just be aborted on the spot
(http://stackoverflow.com/questions/2331316/what-is-stack-unwinding). Lastly, if
the compiler does choose to unwind the stack (destruct local variables), it
doesn’t have to destruct the variables in inverse order of creation.

Note that also lambdas may be declared `noexcept`:

~~~C++
auto may_throw = [] {};
auto no_throw = [] () noexcept {};
~~~

## `noexcept` function

`noexcept` is two things:

1. A keyword, specifying that a function will never throw an exception.
2. A function, that determines if a function throws an exception or not.

(2) means that if you call a function within a noexcept expression, it will
yield a boolean saying whether or not the function called will throw:

~~~C++
auto may_throw = [] {};
auto no_throw = [] () noexcept {};

std::cout << std::boolalpha
          << noexcept(may_throw()) // false
		  << noexcept(no_throw()); // true
~~~

When the functional version of `noexcept` appears next to a function
declaration, it determines its exception specifier. In that sense,
`noexcept` is really syntactic sugar for `noexcept(true)`. Likewise, to specify
that a function definitely will throw, you can write `noexcept(false)`. However,
more importantly, this can be used to dynamically set the
`noexcept`-specification for a function in dependence of some expression (most
often another function called by the the function):

~~~C++
void bar() noexcept { }

void foo() noexcept(noexcept(bar())) // = noexcept(true)
{
	bar();
}
~~~

Note that the outer `noexcept` is for the function declaration and expects a
boolean, while the inner `noexcept` is to evaluate the `noexcept`-specification
of `bar`.
