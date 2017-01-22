# Don't write code that depends on the order of evaluation of function arguments

The C and C++ standards provide no guarantees on any particular order of evaluation of arguments in a function call expression. That is, in `f(++x, ++x)` where `x` previously zero, it is undefined (i.e. entirely up to the compiler) whether that expression is equivalent to `f(1, 2)` or `f(2, 1)`.

The reason for this is simply to given compilers an extra degree of freedom in allocating registers for function calls. To some extent, this is a relict of older times. Either way, this Item simply sheds light on this fact and also provides the simple solution: sequence the expressions explicitly, simply by moving at least one of them outside the function call:

```cpp
f(++x, ++x); // bad

auto first = ++x;
auto second = ++x; // optional
f(first, second);  // well-defined
```
