# prefer-lambdas-over-std::bind

Lambdas are more readable and clear as compared to calls to std::bind,
for sure. But what else? They are:

DYNAMIC

Calling another function in a lambda with some arguments means those
arguments will be evaluated at the point of the call, while the same
call to a std::bind means arguments you pass to std::bind to be
forwarded to the function are evaluated at the point of instantiation of
the bind object:

void f(const std::string& name,
   const std::chrono::system_clock::time_point& time)
 {
print::ln(
 name,
" was just born with timestamp: ",
 time.time_since_epoch().count()
 );

}

// Every time you call this lambda, std::chrono::system_clock::now()
// will be called, yielding the CURRENT time_point
auto lambda = [] (const std::string& name) {
 f(name, std::chrono::system_clock::now());
 };
// std::chrono::system_clock::now() is evaluated right there when
creating
// the bound object. f() will be called with the same time_point every
time!
auto bound = std::bind(
 f,
 std::placeholders::_1,
 std::chrono::system_clock::now()

);

Also, overloads are much better supported by lambdas. Really do consider
that in the lambda, you are not passing a function, you are calling it
from within the body. Meanwhile, for lambdas, you must pass the
specific, concrete function. If there are overloads, function names are
ambiguous and you must static cast function names to their signatures:

// old f still there

void f(const std::string& name,
   const std::string& location,
   const std::chrono::system_clock::time_point& time)
 {
print::ln(
 name,
" was just born in ",
 location,
" with timestamp: ",
 time.time_since_epoch().count()
 );

}

// All ok, still calls two-argument version of
auto lambda = [] (const std::string& name) {
f(name, std::chrono::system_clock::now());
 };
// Error! Which concrete version of f?
auto bound = std::bind(
 f,
 std::placeholders::_1,
 std::chrono::system_clock::now()
 );
// Ok, but, well, fuck
auto bound = std::bind(
static_cast<void(*)(const std::string&, const
std::chrono::system_clock::time_point&)>(f),
 std::placeholders::_1,
 std::chrono::system_clock::now()

);

BETTER PERFORMING

Lambdas are very likely to be inlined, while std::bind calls less so.
The reason why is that std::bind objects hold function pointers, and
compilers don’t like dereferencing function pointers, so the body of f
is quite unlikely to be inlined. Meanwhile, the compiler is more likely
to inline the body of the lambda and then the body of f.

EASIER FOR CAPTURING REFERENCES

std::bind always stores its arguments (that are forwarded to the
function) _by-value_. This is implicit in std::bind’s interface and not
as explicitly visible and expressive as in the capture of lambdas. If
you want to pass something to std::bind by reference, you must use
std::ref.

void f(const ExpensiveObject& object) { }

ExpensiveObject object;
// Can explicitly declare to take by value or reference
auto lambda_by_value = [object] () { f(object); };
auto lambda_by_reference = [&object] () { f(object); };
// Nobody knows from the call if object will be passed by value or
reference...
auto bound_by_value = std::bind(f, object);

auto bound_by_reference = std::bind(f, std::ref(object));

There remain reasons to use std::bind IN C++11 (NOT C++14!):

-   Moving values into lambdas (see other note or Item 32 in Effective
    Modern C++)
-   Function _objects_ (functors, doesn’t work with template functions)
    with call operators taking template arguments, because in C++11
    lambdas cannot take _auto_ parameters:

struct F
 {
template<typename T>
void operator()(const T& t) { }
 };

F f;
// auto OK in C++14, not in C++11
auto lambda = [&f] (auto& param) { f(param); };
// OK in C++11

auto bound = std::bind(f, std::placeholders::_1);
