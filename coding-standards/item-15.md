# Use `const` proactively.

Item 15 discusses the importance of using `const` proactively throughout your code. One of the most essential and helpful properties of `const` is that they give you one and only one point to look at when wanting to understand a variable's value. When you see `const <type> <name> = <value>`, you know immediately that throughout the lifetime of `<name>`, it will always contain `<value>` (assuming you do not use `const_cast`, which you should not unless for a very good reason). This improves maintenance greatly.

Do make note of one particular quirk of C++: declaring a function parameter `const` does not affect the signature of the function. That is, the following two function signatures are equivalent:

```cpp
void f(int x);       // same
void f(const int x); // same
```

This means that if you have a function taking an argument by value and want to make it `const` inside your function, you can make the declaration non-`const` and only `const` the argument in the definition. Be wary, however, that this does not apply to `T&` vs `const T&` (same for pointers), as in that case the difference is in the "pointee". This quirk would just mean that `T&` and `T& const` would be the same, if such a thing as a constant reference would exist (all references are constant, i.e. unmodifiable, since you cannot "repoint" them).
