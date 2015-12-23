# factorial-in-c++11

std::function<int(int)> factorial = [&] (int x)
 {
     return x ? x * factorial(x - 1) : 1;

};

Fuck yeah.

std::function<int(int)>f=[&](int x){return x?x*f(x-1):0;};
