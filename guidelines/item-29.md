# Consider overloading to avoid implicit type-conversions

How often do you write code that compares a string object to a string literal:

```cpp
std::string s = ...;

if (s == "...") {
  // ...
}
```

If the standard had been lazy, it would simply have needed to define an implicit constructor from `const char*` for `std::string` and implicit only one kind of comparison operator for `std::string` against `std::string` (for all supported relations). However, this would, each time, incur the cost of string conversion. Fortunately, there standard defines comparison operator to `const char*` directly to avoid this cost.

Item 29 advises to do the same, when appropriate: consider writing overloads that avoid heavy construction when possible. This fits well with the bounds of *don't pessimize prematurely* (Item 9) and doesn't yet fall into the category of *don't optimize prematurely* (Item 8).
