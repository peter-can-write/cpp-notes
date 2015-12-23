# use-decltype-on-auto&&-parameters-to-std::forward-them

So you wanna write one of these lambdas:

void f(int& x) { }

void f(int&& x) { }

auto lambda = [] (auto&& x) { f(x); };

But consider the call to f(x). It’s not right, because the parameter
should be perfect-forwarded — it’ll always call the overload taking an
lvalue reference. So how to forward them? Answer, use decltype:

auto lambda = [] (auto&& x) { f(std::forward<decltype(x)>(x)); };

Template collapsing rules will ensure that even if decltype(x) yields an
rvalue reference in the call to std::forward:

std::forward<int&&>(x)

The result will be equivalent to

std::forward<int>(x)

Because std::forward removes the reference. Also works for variadic auto
arguments:

auto lambda = [] (auto&&... x) { f(std::forward<decltype(x)>(x)...); };


