# Prefer the canonical forms of arithmetic and assignment operators

This Item gives the advice that when you define one particular kind of arithmetic operator overload for your user-defined type, then you should provide all related operators that are expected. Basically, this means that when you define `operator@` for some operation `@`, you should also define `operator@=` and vice-versa. The easiest way to do this is to first implement `operator@=` and then implement `operator@` in terms of the first. For example:

```cpp
MyType& MyType::operator+=(int value) {
  // ...
}

MyType MyType::operator+(int value) {
  auto copy = \*this;
  copy += value;
  return copy;
}
```

This moves all the logic into just one place, the operator version that performs assignment.

An additional thing to note when implementing custom operators is to make sure that implicit conversions can take place on either side of the operation. That is, if `a @ b` is valid, `b @ a` should do the exact same thing. For this, simply make it a rule to always define the non-assignment version of your operator as a non-member friend function. That is, rather than writing `operator+` as we did above, rather write:

```cpp
class MyType {
public:

  friend MyType operator+(const MyType& first, const MyType& second);
};

MyType operator+(const MyType& first, const MyType& second) {
  auto copy = first;
  copy += second;
  return copy;
}
```

or, even better, just take the first argument by value:

```cpp
MyType operator+(MyType first, const MyType& second) {
  return first += second;
}
```
