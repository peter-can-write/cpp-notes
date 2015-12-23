# why-functors

If you can, in C++11, use lambda expressions as predicates. If you need
state for your predicate (i.e. some private variables outside of the
function) or if the lambda body becomes too large, however, you should
prefer functors (i.e. structs or classes with their call operator
overloaded) to functions. Because:

1. Performance

A compiler can and usually will inline your struct’s call operator if
declared inline or defined inside the class body. This means you don’t
have the overhead of a function call, as the compiler has direct access
to the code inside the call operator. At the same time, even if you
declare a function inline, the compiler will not inline the function’s
body, because, when passed to the algorithm as the predicate, the
function decays to a function pointer and the compiler will usually not
access a function’s body through a pointer to it.

struct Average : public std::binary_function<std::size_t, std::size_t,
double>
 {
inline double operator()(std::size_t first, std::size_t second) const
 {
return (first + second)/2.0;
 }
 };
double average(std::size_t first, std::size_t second)
 {
return (first + second)/2.0;
 }
int main(int argc, char * argv[])
 {
std::vector<std::size_t> v = {1, 2, 3};
std::list<std::size_t> l = {4, 5, 6};
// Faster
std::transform(v.begin(),
   v.end(),
   l.begin(),
   std::ostream_iterator<std::size_t>{std::cout, " "},
   Average());
println();
// Slower
std::transform(v.begin(),
   v.end(),
   l.begin(),
   std::ostream_iterator<std::size_t>{std::cout, " "},
   average);

}

2. State

You can store state in a functor much more easily than in a function.
Moreover, you can parameterize your functor according to the situation:

class Raise : public std::unary_function<double, double>
 {
public:
 Raise(double exponent)
 : _exponent(exponent)
 { }
inline double operator()(double value) const
 {
return std::pow(value, _exponent);
 }
private:
double _exponent;
 };
int main(int argc, char * argv[])
 {
std::set<std::size_t> s = {2, 3, 4};
// Square values
std::transform(s.begin(),
   s.end(),
   std::ostream_iterator<double>{std::cout, " "},
   Raise(2));
println();
// Cube values
std::transform(s.begin(),
   s.end(),
   std::ostream_iterator<double>{std::cout, " "},
   Raise(3));

}

3. Adaptability

This is not so much an issue in C++11 because you can use lambdas to,
for example, negate a predicate, but in pre-C++11 it was necessary to
inherit publicly from std::unary_function or std::binary_function to
make a predicate adaptable, because those base structs defined certain
typedefs that the adaptors needed.

So for pre-C++11 and also for shortness’ sake (though careful:
deprecated beginning with C++17):

struct Human
 {
bool happy;
 };
struct Is_Happy : public std::unary_function<Human, bool>
 {
bool operator()(const Human& human)
 {
return human.happy;
 }
 };
int main(int argc, char * argv[])
 {
std::vector<Human> v = {{true}, {true}, {false}, {true}};
auto unhappy = std::find_if(v.begin(), v.end(), std::not1(Is_Happy()));
if (unhappy != v.end()) println(std::distance(v.begin(), unhappy));
else println("Everybody's happy!");

}

But with C++11 and after:

struct Human
 {
bool happy;
 };
int main(int argc, char * argv[])
 {
std::vector<Human> v = {{true}, {true}, {false}, {true}};
auto unhappy = std::find_if(v.begin(),
 v.end(),
 [] (const Human& human)
 { return ! human.happy; });
if (unhappy != v.end()) println(std::distance(v.begin(), unhappy));
else println("Everybody's happy!");

}
