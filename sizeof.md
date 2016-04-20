# `sizeof`

`sizeof` is an operator, the distinction when you have to put parantheses around
its argument and not is:

* If its argument is an *expression*, you don't need to use parantheses: `int x;
  sizeof x;` is legal. However, `sizeof` has very high precedence (same as `&`
  and `*`, lower than `.`), so `sizeof a + b` is really `sizeof(a) + b`.

* If its argument is a *type*, you *do* need to put parantheses around it:
  `sizeof int` is not legal, `sizeof(int)` is.

Note that because it is an operator, any expression you pass to it is evaluated
at compile time and no longer at runtime, so `sizeof a++` will never increment
`a++` at runtime.
