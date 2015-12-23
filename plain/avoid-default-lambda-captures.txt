# avoid-default-lambda-captures

Lambdas can captures in five different ways:

-   Not at all (captures no variables, can not use anything from
    enclosing scope): []
-   Default by reference (captures all variables by reference): [&] 
-   Default by value (captures all variables by value): [=]
-   Explicit by reference (captures the specified variable _x_ by
    reference): [&x]
-   Explicit by value (captures the specified variable _x_ by value):
    [x]

Item 31 in Effective Modern C++ suggests to avoid default captures, i.e.
[=] and [&], because they can lead to a variety of problems.

1. DANGLING REFERENCES

If you capture by reference by default, you run the risk of capturing
local variables by reference. This is really nasty if you return the
lambda, as, on return, the value you captures and hold a reference to
will be destroyed, and your reference in the lambda will dangle
(undefined behavior):

std::function<int()> make_adder(int x, int y)
 {
return [&] () { return x + y; };

}

For example here, this lambda takes _x_ and _y_ by reference. However,
as these parameters were themselves taken by value, the references to
the captured variables would dangle. Bad!

2. UNEXPECTED CAPTURING

First, it is important to know that LAMBDA CAPTURES CAPTURE ONLY LOCAL,
NON-STATIC VARIABLES, i.e. not static variables declared in a function,
class or file, nor global variables defined in a namespace or outside
one (really globally). Now, see this code:

class FunctionMaker
 {
public:
 FunctionMaker(int x)
 : _x(x)
 { }
std::function<int()> make_adder(int y)
 {
return [&] () { return _x + y; };
 }
private:
int _x;

};

Here, you would say “of course, the capture has to be by value, because
else the reference to the parameter will definitely dangle, and the
reference to the member variable as soon as the object is destroyed,
thus the solution is to make the default-capture by value:”

...

std::function<int()> make_adder(int y)

{

     return [=] () { return _x + y; };

}

...

Wrong! This would copy the parameter y, yes. But remember that lambda
captures only capture local, non-static variables. So does this code not
compile, then? No, it does. What is captured? _this_. The pointer to the
current object (because _x is replaced with this->_x during
compilation). So this is still horrible, because if the object is
destroyed, the _this_-pointer will dangle. You could have seen this
problem (or not made it compile) by not using default captures:

...

std::function<int()> make_adder(int y)

{

     return [_x] () { return _x + y; }; // error! no local _x to
capture!

}

...

Another case where unexpected capturing will happen is with static
variables. You may think that taking them by value will copy the
variables, but remember that LAMBDA CAPTURES ONLY CAPTURE _NON-STATIC_,
LOCAL VARIABLES. Thus, static variables will not be copied and if you
change them later, the values you thought you copied in the capture will
reflect those changes (which you probably didn’t want):

std::function<int()> get_function_returning_current_static_value()
 {
static int x = 0;
// doesn't capture anything!
auto lambda = [=] () { return x; };
 ++x; // changes x in lambda
return lambda;
 }
int main(int argc, const char* argv[])
 {
auto f = get_function_returning_current_static_value();
print::ln(f()); // prints 1, not 0!!!

}

Again, had you not default-captured, this problem would have been become
apparent:

// 'x' cannot be captured because it does not have automatic storage
duration

auto lambda = [x] () { return x; };
