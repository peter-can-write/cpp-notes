# argument-dependent-lookup

Argument Dependent Lookup (or Koenig Lookup) is a concept that allows
the C++ compiler to look for certain functions in certain namespaces,
even though those functions were not referred to with their fully
qualified names. Basically, if you call a function foo(argument), the
compiler will not only look for free non-member non-friend functions
“foo” in the current namespace, but also in the namespace of the type of
the argument, if it differs from the current one. For example, the type
of the argument may be Foo::bar (in namespace Foo), then the compiler
will look for a (possibly) better-matching function foo in the namespace
Foo, even though it was not asked for it. ADL enables this simple
program:

#include <iostream>

int main(int argc, const char* argv[])

{

     std::cout << “Hello World!”;

}

Here, << is std::operator<<(std::ostream&, <something>), a free function
defined in the namespace std. Without ADL, you would have to call it
explicitly like so:

std::operator<<(std::cout, “Hello World");

But ADL will look for such a function in the std namespace, because one
of its arguments — std::cout — is _always_ a member of that namespace
(the other may be, too). Another example:

namespace ADL
 {
struct Container
 {
int* begin()
 {
print::ln("!");
return array;
 }
int array [5];
 };
 }
int main(int argc, const char* argv[])
 {
// Enable ADL!
using std::begin;
ADL::Container c;
// Prints "!", even though we're not calling ADL::begin()!
begin(c);

}
