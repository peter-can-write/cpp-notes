# lambda-init-capture

In C++11, it was not possible to move objects into lambdas, introducing
possible expensive copies. In C++14, there exists the concept of “init
captures”, also called “generalized lambda captures”, which allow you to
specify, in the capture brackets:

-   the NAME of a data member in the closure class generated from the
    lambda
-   an EXPRESSION INITIALIZING that data member

E.g. you might want to move a std::unique_ptr into a lambda capture:

auto pointer = std::make_unique<int>(5);

auto lambda = [p = std::move(pointer)] () { return *p + 5; }; // note
you can get rid of the empty parameter list ‘()'

This will basically move the pointer object into the data member ‘p’ of
the closure class created from the lambda. In C++11, this was not
possible. One workaround is to emulate the lambda by an explicit class
as would be created from it:

class Lambda
 {
public:
using T = std::unique_ptr<int>;
explicit Lambda(T&& t_) : t(std::forward<T>(t_)) { }
int operator()() const { return *t + 5; }
private:
T t;
 };
int main(int argc, const char* argv[])
 {
auto pointer = std::make_unique<int>(5);

              auto lambda = Lambda(std::move(pointer));

}

Another possibility is to emulate the move capture with a call to
std::bind by

1.  Moving the object to be captured into a function object produced by
    std::bind
2.  Giving the lambda a reference to the “captured” object

auto pointer = std::make_unique<int>(5);
auto lambda = std::bind(

     [] (const std::unique_ptr<int>& p) { return *p + 5; },

     std::move(pointer)

);

Thus, in conclusion:

-   It’s not possible to move-construct an object into a C++11 closure,
    but it’s possible to move-construct an object into a C++11
    bind object.
-   Emulating move-capture in C++11 consists of move-constructing an
    object into a bind object, then passing the move-constructed object
    to the lambda by reference.
-   Because the lifetime of the bind object is the same as that of the
    closure, it’s possible to treat objects in the bind object as if
    they were in the closure.

Note about mutability: By default, the call operator of a lambda is
const, thus any captured by-value data-members are non-modifiable. That
is why in the call to bind, we made the unique_ptr parameter const& (to
emulate that it would have been const if we passed it in the
init-capture). If we wanted to emulate the unique-ptr being modifiable,
we should have written:

auto lambda = std::bind(
      [] (/* non-const */ std::unique_ptr<int>& p) mutable { return *p +
5; },
      std::move(pointer)

);


