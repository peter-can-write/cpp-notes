# default-and-value-initialization

If you do not explicitly define a constructor for a class, the compiler
will generate a default-constructor, which usually does nothing. More
precisely, it is equivalent to a user-defined constructor with an empty
body and initializer-list.

class Foo

{

public:

     int x;

};

Foo foo; // x contains garbage

However, if there is no user-defined constructor and the class
is constructed with the Foo() syntax, the compiler will not actually
call the default-constructor, but value-intialize the class. This only
happens in these cases:

Foo foo = Foo(); // x = 0

Foo* foo = new Foo(); // foo->x = 0

Source: http://stackoverflow.com/questions/2417065/does-the-default-constructor-initialize-built-in-types
