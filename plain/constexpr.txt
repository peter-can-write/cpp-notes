# constexpr

A constexpr declaration for a variable or function indicates that the
compiler should evaluate the expression at compile time. For this, it
must follow certain rules:

“The constexpr specifier declares that it is possible to evaluate the
value of the function or variable at compile time. Such variables and
functions can then be used where only compile time constant
expressions are allowed (provided that appropriate function arguments
are given). A constexpr specifier used in an object declaration
implies const. A constexpr specifier used in an function declaration
implies inline."

A CONSTEXPR VARIABLE must satisfy the following requirements:

-   its type must be a LiteralType. (A “literal” is a constant value)
-   it must be immediately constructed or assigned a value.
-   the constructor parameters or the value to be assigned must contain
    only literal values, constexpr variables and functions.
-   the constructor used to construct the object (either implicit
    or explicit) must satisfy the requirements of constexpr constructor.
    In the case of explicit constructor, it must
    have constexpr specified.

A CONSTEXPR FUNCTION must satisfy the following requirements:

-   it must not be virtual
-   its return type must be LiteralType
-   each of its parameters must be literal type
-   there exists at least one argument value such that an invocation of
    the function could be an evaluated subexpression of a core constant
    expression (for constexpr function templates, at least one
    specialization must satisfy this requirement)

-   the function body must be either deleted or defaulted or contain
    only the following:

-   null statements
-   static_assert declarations
-   typedef declarations and alias declarations that do not define
    classes or enumerations
-   using declarations
-   using directives
-   exactly one return statement.

(until C++14)
-   the function body must be either deleted or defaulted or contain ANY
    STATEMENTS EXCEPT:

-   an asm declaration
-   a goto statement
-   a try-block
-   a definition of a variable of non-literal type
-   a definition of a variable of static or thread storage duration
-   a definition of a variable for which no initialization is performed.

The return types of a constexpr function can only be a LiteralType,
which includes:

-   void
-   scalar values (primitive types, i.e. integers, chars, etc.)
-   references
-   An array of literal type
-   Class type if:
    -   it has a trivial (non-virtual) destructor
    -   has at least one constexpr constructor
    -   all of its data members are non-volatile

A CONSTEXPR CONSTRUCTOR must satisfy the following requirements:

-   each of its parameters must be literal type
-   the class must have no virtual base classes
-   the constructor must not have a function-try-block


