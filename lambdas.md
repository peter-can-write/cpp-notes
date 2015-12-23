# lambdas

[ input capture ] ( parameters ) { something; }

For input capture:

[&] take closure by reference

[=] take closure by value

[=, &var] // Take by value by default, but take the variable “var" by
reference

E.g.

[&] (int d) { return d*d; }

Usually the return type is derived automatically, however you can
specify it like so:

[&] (int d) -> DOUBLE { return d*d; } // now returns double

For example, you might need a functor to use std::for_each

char str [] = “hello”;

Then, you can just use a lambda for the function:

std::for_each(str, str + 5, [&] (char& c) { c++; });

std::cout << str << std::endl;


