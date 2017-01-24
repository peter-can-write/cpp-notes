# Don't pessimize prematurely

Just as it is important to not optimize prematurely and sacrifice readability for a few cycles, it is also important to not gratuitously waste opportunities where performance gains do not reduce legibility. Examples of this are using the prefix versions of `++` and `--` instead of postfix when possible, or passing by value when passing by (`const`) reference would do as well.

These should be things that flow naturally and become a habit rather than requiring a lot of extra complexity that result in premature optimization.
