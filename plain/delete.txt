# delete

The special ‘= delete’ syntax, when used after a function declaration,
declares that function cannot be used. This is especially (or only)
useful to delete implicitly/compiler generated functions such as the
copy-constructor and copy-assignment operator. Note that only the
specific overload where '= delete' is declared is actually deleted,
other overloads without the '= delete' are left untouched. Moreover, the
'= delete' must be specified (or not) the first time the function is
declared (i.e. in the header, mostly) and cannot be added later.

class Uncopyable

{
public:
 Uncopyable(int x = 0)
 : _x(x)
 { }
 Uncopyable(const Uncopyable& other) = delete;
 Uncopyable(Uncopyable&& other);
Uncopyable& operator=(const Uncopyable& a) = delete;
void foo() = delete;
void foo(int x) { _x = x; }
private:
int _x;
 };
int main(int argc, char * argv[])
 {
Uncopyable a(5); // OK, not a deleted overload of Uncopyable()
Uncopyable b(a); // Error: Call to deleted constructor of Uncopyable
Uncopyable c(std::move(a)); // OK, not a deleted overload of
Uncopyable()
 c = a; // Error: Overload resolution selected deleted operator '='
 c.foo(5); // OK, not a deleted overload of foo()
 c.foo(); // Error: Attempt to use a deleted function

}
