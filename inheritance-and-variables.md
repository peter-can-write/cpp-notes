# inheritance-and-variables

You can declare variables in a derived class with the same identifier as
a variable from a base class. In the base class, you then use the base
class variable and in the derived class you use the derived version. You
access the base version via base::variable. When using a pointer of type
base, it always picks the base version of the variable and never the
derived version (when pointing to an object of type derived), i.e.
variables are bound statically (early, at compile-time) and not
dynamically (late, at run-time).

struct A
 {
int x = 5;
 };
struct B : public A
 {
int x = 12;
 };
int main(int argc, char * argv[])
 {
A a;
print(a.x); // 5
B b;
print(b.x); // 12
A* ptr = new A;
print(ptr->x); // 5
delete ptr;
 ptr = new B;
print(ptr->x); // 5
delete ptr;

}
