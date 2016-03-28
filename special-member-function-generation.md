# special-member-function-generation

In C++11, there are five “special" member functions:

-   Constructor
-   Copy Constructor
-   Copy-Assignment Operator
-   Move Constructor
-   Move-Assignment Operator
-   Destructor

These are the special member functions the compiler will generate for
you, WHEN NEEDED. If the necessity for all arises, the compiler will
silently define all five of them for you. Moreover, the compiler makes
them:

-   Public
-   Inline
-   Non-virtual, unless it is the destructor and it is also declared
    virtual in a base-class
-   Perform member-wise operations, i.e. copies/moves

Note for the last that the default-move-constructor will perform
member-wise moves, but that those move-operations will really only be
move-operations if the objects/members define move-constructors (which
may not be the case for many legacy C++98 classes). For all of these
operations/methods, the compiler makes the assumption that resource
management is standard or trivial, i.e. there is nothing _special_ about
how copies/moves/construction/destruction should work. However, when you
yourself define either the copy or move operations or the destructor, it
is most likely because there _is_ something special about how you, for
example, perform copy-operations. Therefore, the compiler, in C++11,
will restrict itself. This yields the following rules for special member
function generation:

-   COPY-CONSTRUCTOR: only generated if you do not yourself define a
    move operation (such a definition would imply naïve member-wise
    copying does not suit for the resources/objects managed by
    the class). Still generated if you define a copy-assignment-operator
    or destructor, _but deprecated_.
-   COPY-ASSIGNMENT OPERATOR: only generated if you do not yourself
    define a move operation. Generation alongside user-defined
    copy-constructor or destructor deprecated.
-   MOVE-CONSTRUCTOR: generated only for classes lacking
    _any _explicitly declared copy-operations, move-operations
    and destructors._ I.e. the presence of either the copy-constructor
    or copy-assignment-operator, a destructor or the accompanying
    move-operation, here the move-assignment operator, will cause the
    move-constructor not to be default-defined.
-   MOVE-ASSIGNMENT OPERATOR: generated only for classes lacking any
    explicitly declared copy-operations, move-operations
    and destructors. I.e. not generated in the presence of an explicitly
    declared move-constructor.
-   CONSTRUCTOR: Declared when needed, unaffected by C++11.
-   DESTRUCTOR: Disallows default-generation of both move-operations and
    default-generation of copy-operations is deprecated. In C++11 now
    also implicitly declared _noexcept._

_
_

if copy-constructor not defined:

     if copy-constructor needed:

          if destructor not defined and copy-assignment-operator not
defined:          

               generate copy-constructor

          else:

               generate copy-constructor (deprecated)

     if copy-assignment-operator not defined:

          if destructor not defined:

               if no move-operation defined:

                    if move-constructor needed:

                         generate move-constructor

                    if move-assignment-operator needed:

                          generate move-assignment-operator

if copy-assignment-operator not defined:

     if copy-assignment-operator needed:

          if destructor not defined and copy-constructor not defined:

               generate copy-assignment-operator

          else:

               generate copy-assignment-operator (deprecated)

Last point of interest: explicitly-declared _template_ member functions
don’t count as regular function, thus a template-copy-constructor would
not prohibit a default-generated move-constructor, for example. 
