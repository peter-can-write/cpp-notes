# Comma Operator

The comma operator is an operator that is ubiquitous in C++ programs but rarely discussed. It is a binary operator with the following basic semantics: In an expression `a, b` containing the trivial (non-overloaded) comma operator `,`:

1. The left-hand-side expression of the operator is fully evaluated, such that all side effects have taken place,
2. Any return-value resulting from (1) is discarded,
3. The right hand side is evaluated and its value returned.

For example, in `x = (a, b)` where `a, b, x` are integers, the value of `a` would be evaluated and discarded, while the value of `b` would be evaluated and returned (i.e. assigned to `x`). If an expression contains multiple, chained occurrences of the comma operator, such as in `a, b, c, d`, this sequence is evaluated left to right and identical to `(((a, b), c), d)` -- i.e. the right-most value would again be returned.

The first of the aforementioned three steps is arguably the most important: the comma operator guarantees sequencing. That is, the arguments to the comma operator are evaluated fully and completely in order, from left to right. This means an expression like `int x = 0; int y = (++x, ++x);` is perfectly well-formed and well-defined, with both `x` and `y` holding the value `2` at the end.

Another noteworthy property of the comma operator is that it has the lowest precedence *of all operators* -- even the assignment operator. Interestingly, this means that `int x = a, b;` is actually not equivalent to `int x = (a, b)`, but really `(int x = a), b;`, which may be unexpected.

So we know that the comma operator guarantees sequencing, yet we are told that in a function call like `f(a, b)`, the order of evaluation of function arguments is undefined. The (arguably subtle) reason for this is that the comma inside a function call is actually not the comma operator. It simply delimits the arguments of the function and shares no semantics at all with the comma operator.

Lastly, it's good to know that you *can* overload the comma operator too (at least for class-types). It's good to know it, just so that you can never do it. Overloading the comma operator means that it loses its sequencing guarantees and has the same undefined order of argument evaluation as any other function call.

```cpp
int x = 0l
++x, ++x; // previously guaranteed to be sequenced as 1, 2

void operator,(const T& first, const T& second) {
  // funny code
}

++x, ++x; // now could be operator,(3, 4) or operator,(4, 3)!
```
