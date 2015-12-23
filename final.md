# final

The final keyword can be used for classes to make them non-derivable:

class A final { };

class B : public A { }; // Error: Base 'A' is marked 'final'

Or for virtual functions to make them non-overrideable in derived
classes:

class A
 {
public:
virtual void foo() const final { }
 };
class B : public A
 {
public:
void foo() const; // Error: Declaration of 'foo' overrides a 'final'
function

};

Note that final is not a language keyword, it is technically an
identifier (variable name). It only gains special meaning when used in
those specific contexts. In any other location, they can be used as as
valid identifiers:

int final = 5;

std::cout << final << std::endl;
