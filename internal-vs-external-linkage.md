# internal-vs-external-linkage

## Declarations and Definitions

Quick recap: declaration vs. definition. A *declaration* tells the compiler
about the existence of a certain symbol (e.g. a variable, function, class
etc.). A *definition* tells the compiler how much memory it must allocate for
that symbol.

For functions:

```C++
int f();               // declaration
int f() { return 42; } // definition
```

For variables, it is a bit different, as declaration and defintion are usually
not explicitly separate. Most importantly, this:

```C++
int x;
```

Does not just declare `x`, but also define it, here by calling the default
constructor of `int` (which does nothing). You can, however, explicitly separate
the declaration of a variable from its definition by using the `extern` keyword:

```C++
extern int x; // declaration
int x = 42;   // definition
```

However, when `extern` is prepended to the declaration and an initialization is
provided as well, then the expression turns into a definition and the `extern`
keyword essentially becomes useless:

```C++
extern int x = 5; // is the same thing as
int x = 5;
```

## Overview

A *translation unit* (TU) refers to an implementation file and all header files
it includes. If an object or function inside such a translation unit has
*internal linkage*, then that specific symbol is only accessible to the linker
within that translation unit. If an object or function has *external linkage*,
the linker can also access from other translation units. The `static` keyword, when
used in the global namespace, forces an object or function to have internal
linkage. The `extern` keyword results in an object having external linkage.

Linkage is usually determined by default by the compiler:

* *Non-const* global variables have *external* linkage by default.
* *Const* global variables have *internal* linkage by default.
* Functions have *external* linkage by default.

## External Linkage with `extern`

The common idiom to create a global symbol (function or variable) is to declare
it in one header with the `extern` keyword, which tells the compiler that this
symbol has *external linkage*. You then define that symbol in some other
translation unit (where it is accessible due to the `extern`). It has to be
another translation unit, because if you were to declare the variable within the
same TU, you would be redeclaring the symbol, causing a duplicate symbol linker
error. You then define the variable in ONE and only one implementation file (one
definition rule). If you would not only declare but also *define* the symbol in
the same file as you declare it, you would redefine the symbol each time it is
included in some other translation unit. And because the symbol would have
*external* linkage, the linker would see multiple definitions for the same
(globally visible) symbol.

Above it was mentioned that *const* global variables have internal linkage by
default, and *non-const* global variables have external linkage by default. That
means that `int x;` is the same as `extern int x;`. However, because `int x;`
not only declares, but also defines `x` (by calling its default-constructor),
not prepending the `extern` to `int x;` is just as bad as defining a variable
when declaring it as `extern`:

```C++
int x;          // is the same as
extern int x(); // which will both likely cause linker errors.

extern int x; // while this only declares the integer, which is ok.
```

What the above also means it that `const int x;` is the same as `static const
int x`. When you want the const variable to have external linkage, you can
prepend the `extern` keyword. However, because `const` values always need an
immediate definition, this is again a bad idea.

## Internal Linkage with `static`

The static keyword in the global scope (totally different from
function/class/block scope) means that a certain variable or function (a symbol) will
only be accessible from the current translation unit, i.e. the linker will only
use this symbol for the current translation unit. This does *not* that the
variable is private to the translation unit (that would anyway not make sense
because of how `#include` works), it only means that each translation
unit will receive its own copy of the variable. I.e., the compiler will
literally allocate an entire new, unique copy for each translation unit.

So when and why would one make use of internal linkage? For objects, it is
probably a very bad idea to make use of it, because the memory cost can
obviously be very high for large objects when each translation unit needs its
own copy. However, one interesting use case could be to hide
translation-unit-local helper functions from the global scope. Imagine you have
a helper function `foo` in your `file1.hpp` which you use in `file1.cpp`, but
then you also have a helper function `foo` in your `file2.hpp` which you use in
`file2.cpp`. The first `foo` does something completely different than the second
`foo`, but you cannot think of a better name for them. So, you can declare them
both `static`. Unless you include `file1.hpp` and `file2.hpp` in some same
translation unit, this will hide the respective `foo`s from each other.

### Example

Here an example:

#### header.hpp

```C++
static int variable = 0;
```

#### file1.hpp

```C++
void function1();
```

#### file2.hpp

```C++
void function2();
```

#### file1.cpp

```C++
#include “header.hpp"

void function1() { variable = 10; }
```

#### file2.cpp

```C++
#include “header.hpp"

void function2() { variable = 123; }
```

#### main.cpp

```C++
#include "header.hpp"
#include "file1.hpp"
#include "file2.hpp"

#include <iostream>

auto main() -> int
{
	function1();
	function2();

	std::cout << variable << std::endl;
}
```

Because `variable` has internal linkage, each translation unit that includes
`header.hpp` gets its own unique copy of `variable`. Here, there are three
translation units:

1. file1.cpp
2. file2.cpp
3. main.cpp

When `function1` is called, `file1.cpp`'s copy of `variable` is set to 10. When
`function2` is called, `file2.cpp`'s copy of `variable` is set to 123. However,
the value printed out in `main.cpp` is `variable`, unchanged: 0.

### Anonomous Namespaces

In C++, there exists another way to declare one or more symbols to have internal
linkage: anyonmous namesapces. Such a namespace simply has no name. It ensures
that the symbols declared inside it are visible only within the current
translation unit. It is, in essence, just a way to declare many symbols as
`static`. In fact, for a while, the `static` keyword for the use of declaring a
symbol to have internal linkage was deprecated in favor of anonyomous
namespaces. However, it was recently *undeprecated*, because it is useful to
declare a single variable or function to have internal linkage.

In any case, this:

```C++
namespace { int variable = 0; }
```

Does the exact same thing as this:

```C++
static int variable = 0;
```

### References

* http://stackoverflow.com/questions/154469/unnamed-anonymous-namespaces-vs-static-functions
* http://stackoverflow.com/questions/4726570/deprecation-of-the-static-keyword-no-more
* http://www.geeksforgeeks.org/understanding-extern-keyword-in-c/
