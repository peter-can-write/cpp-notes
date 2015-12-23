# casting

Prefer C++style casting over old-style, C casts and generally, try to
avoid casting, especially dynamic_cast<> as it has very poor
performance.

static_cast<type>(object) // Casts an object between similar types, e.g.
int to double

reinterpret_cast<type>(object) // Casts an object from a completely
different type to another type, e.g. class Foo() { } to char* 

dynamic_cast<type>(object) // Casts between inheritance hierarchies,
e.g. from Base pointer to Derived to Derived pointer

const_cast<type>(object) // Casts a way the const of an object


