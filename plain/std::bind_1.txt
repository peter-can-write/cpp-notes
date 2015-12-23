# std::bind_1

To pass a member function where a function pointer is needed, you can
use std::bind, which binds a method to an object.

class Foo{ void bar(); };

Foo foo;

// Calls Foo::bar and bind it to the foo object, since Foo::bar’s
implicit parameter is an object, which is bound here.

std::sort(begin(), end(), std::bind(&Foo::bar, foo));


