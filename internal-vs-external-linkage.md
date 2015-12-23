# internal-vs-external-linkage

A translation unit refers to an implementation file and all header files
it includes. If an object or function inside such a translation unit has
internal linkage, it is only accessible within that translation unit. If
an object or function has external linkage, it is also accessible from
other translation units. The ‘static’ keyword, when used in the global
namespace, forces an object or function to have internal linkage. The
‘extern’ keyword results in an object having external linkage.

Non-const global variables have external linkage by default. Const
global variables have internal linkage by default.

The most important cases in which linkage is important is for global
variables and for static variables.

The common idiom to create a global variable is to declare it in one
header with the ‘extern’ keyword, which tells the compiler that this
variable has external linkage and that is defined in some other
translation unit (where it is accessible due to the extern). You then
define the variable in ONE and only one implementation file. If you
didn’t specify the extern keyword and would just declare and define the
variable in the header directly, then that would incur multiple
definitions of the same symbol. The reason why is that #include-ing the
file in each implementation file would result in the same definition
(#include just copy-pastes).

The static keyword in the global scope (totally different from
function/class/block scope) means that a variable or function will only
be accessible from the current translation unit. This does _not_ that
the variable is private to the translation unit, it only means that each
translation unit (each implementation file in which the header with the
static variable is included) will receive its own copy of the variable:

header.hpp

...

static int variable = 0;

...

file1.cpp

#include “header.hpp"

void function1()

{

     variable = 10;

}

file2.cpp

#include “header.hpp"

void function2()

{

     variable = 123;

}

If you now were to include file1.hpp and file2.hpp and header.hpp and
call function1() and function()2, you would see that the variable from
header.hpp is not changed, while the respective copies of the variable
in file1.hpp/cpp and file2.hpp/cpp did indeed change. 

The proper way to do this, though, is to use an anonymous namespace,
which also forces internal linkage:

namespace

{

     int variable = 0;

}

equivalent to:

static int variable = 0;
