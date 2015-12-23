# values

An expression can fall into one of three categories:

-   lvalue (identity, not movable)
-   xvalue (identity, movable)
-   prvalue (no identity, movable)

Which are further generalized into two categories:

-   glvalue (identity)
-   rvalue(movable)

Which are all:

-   Expressions

-> the stroustrup W

LVALUE: non-movable objects with identity.

Properties:

-   Has identity and name (can be referred to) 
-   Address can be taken
-   Can have incomplete type
-   Can be polymorphic (its dynamic type must not be the same as its
    static type)
-   Non-temporary
-   _Can_ be on the left side of a built-in assignment. Not always
    (arrays, string literals or const-qualified objects cannot)

Examples:

-   Variables with names
-   The result of member access into an lvalue
-   The result of derefering an lvalue pointer, including via the
    subscript notation a[i]
-   String literals
-   Functions returning lvalue references
-   Explicit lvalue casts

There is implicit lvalue to rvalue conversion, e.g. When returning by
value from a function or when using arithmetic operators for lvalues (x
+ y) they are converted to rvalues. 

PRVALUE: movable objects without identity.

Properties: 

-   Is non-polymorphic
-   

------------------------------------------------------------------------

Conflicting modification on 22 Jul 2015 09:07:40:

------------------------------------------------------------------------

An expression can fall into one of three categories:

-   lvalue (identity, not movable)
-   xvalue (identity, movable)
-   prvalue (no identity, movable)

Which are further generalized into two categories:

-   glvalue (identity)
-   rvalue(movable)

Which are all:

-   Expressions

-> the stroustrup W

LVALUE: non-movable objects with identity.

Properties:

-   Has identity and name (can be referred to) 
-   Address can be taken
-   Can have incomplete type
-   Can be polymorphic (its dynamic type must not be the same as its 
-   Non-temporary
-   _Can_ be on the left side of a built-in assignment. Not always
    (arrays, string literals or const-qualified objects cannot)

Examples:

-   Variables with names
-   The result of member access into an lvalue
-   The result of derefering an lvalue pointer, including via the
    subscript notation a[i]
-   String literals
-   Functions returning lvalue references
-   Explicit lvalue casts
-   Always binds to an lvalue reference overload of a function

There is implicit lvalue to rvalue conversion, e.g. When returning by
value from a function or when using arithmetic operators for lvalues (x
+ y) they are converted to rvalues. 

PRVALUE: movable objects without identity.

Properties: 

-   Is non-polymorphic (dynamic type = static type)
-   Cannot have incomplete type
-   No identity
-   Address cannot be taken
-   Always binds to an rvalue reference overload of a function

Examples:

-   Numerical literals and generally all non-string literals (nullptr,
    43, true)
-   The result of built-in arithmetic, comparison, shift, logical or
    bitwise operators
-   The result of a function call that returns by value
-   The result of taking the address of an object
-   The result of the built-in postincrement and postdecrement operators
-   The result of a cast to object type (i.e. the return value of
    a cast)
-   The result of sizeof
-   Lambda expressions

XVALUE: an expression that identifies an eXpiring object with or without
identity, that can be moved from.

Properties:

-   Same as glvalues and rvalues

Examples:

-   The result of a function that returns an rvalue reference
-   The result of calls to std::move or std::forward (if the argument
    passed to std::forward was an rvalue), i.e. generally any explicit
    cast to an rvalue reverence.

------------------------------------------------------------------------

Conflicting modification on 22 Jul 2015 09:45:34:

------------------------------------------------------------------------

An expression can fall into one of three categories:

-   lvalue (identity, not movable)
-   xvalue (identity, movable)
-   prvalue (no identity, movable)

Which are further generalized into two categories:

-   glvalue (identity)
-   rvalue(movable)

Which are all:

-   Expressions

-> the stroustrup W

LVALUE: non-movable objects with identity.

Properties:

-   Has identity and name (can be referred to) 
-   Address can be taken
-   Can have incomplete type
-   Can be polymorphic (its dynamic type must not be the same as its
    static type)
-   Non-temporary
-   _Can_ be on the left side of a built-in assignment. Not always
    (arrays, string literals or const-qualified objects cannot)

Examples:

-   Variables with names
-   The result of member access into an lvalue
-   The result of derefering an lvalue pointer, including via the
    subscript notation a[i]
-   String literals
-   Functions returning lvalue references
-   Explicit lvalue casts
-   Always binds to an lvalue reference overload of a function

There is implicit lvalue to rvalue conversion, e.g. When returning by
value from a function or when using arithmetic operators for lvalues (x
+ y) they are converted to rvalues. 

PRVALUE: movable objects without identity.

Properties: 

-   Is non-polymorphic (dynamic type = static type)
-   Cannot have incomplete type
-   No identity
-   Address cannot be taken
-   Always binds to an rvalue reference overload of a function

Examples:

-   Numerical literals and generally all non-string literals (nullptr,
    43, true)
-   The result of built-in arithmetic, comparison, shift, logical or
    bitwise operators
-   The result of a function call that returns by value
-   The result of taking the address of an object
-   The result of the built-in postincrement and postdecrement operators
-   The result of a cast to object type (i.e. the return value of
    a cast)
-   The result of sizeof
-   Lambda expressions

XVALUE: an expression that identifies an eXpiring object with or without
identity, that can be moved from.

Properties:

-   Same as glvalues and rvalues

Examples:

-   The result of a function that returns an rvalue reference
-   The result of calls to std::move or std::forward (if the argument
    passed to std::forward was an rvalue), i.e. generally any explicit
    cast to an rvalue reverence.


