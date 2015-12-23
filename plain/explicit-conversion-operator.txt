# explicit-conversion-operator

You can mark a conversion operator as ‘explicit’, then it will allow the
converted type to be constructed from your user-defined type (that
defines the conversion operator), but will not allow implicit assignment
(also not copy-construction via ‘=').

class Foo
 {
public:
operator int()
 {
return 5;
 }
 };
class Bar
 {
public:
explicit operator int()
 {
return 10;
 }
 };
int main(int argc, char * argv[])
 {
int x;
Foo foo;
 x = foo; // OK
std::cout << x << std::endl;
Bar bar;
 x = bar; // Error, assigning to 'int' from incompatible type 'Bar'
int y(bar); // OK, allowed by 'explicit'
std::cout << y << std::endl;
int z = bar; // Also not allowed

}
