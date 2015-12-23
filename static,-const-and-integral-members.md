# static,-const-and-integral-members

Normally, you must define static members of a class outside of its
scope:

class Foo
 {
public:
    
     static std::string foo;
 };

std::string Foo::foo = "hehe";

However, if the static member is CONST and INT, you can define it inside
the scope of the class:

class Foo
 {
public:
    
     static const int foo = 5;
 };


