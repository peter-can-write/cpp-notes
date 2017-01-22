# Know when and how to code for scalability

This item warns about the danger of asymptotic explosions that may be unanticipated or ignored. That is, one may often be inclined to use simpler data structures or algorithms with less-than-optimal complexity guarantees, based on the assumption that "we'll never have that much data". This may pan out badly over time. In general, you should keep an eye on the algorithmic complexity class of any critical piece of code you write, i.e. code that will likely have large amounts of data flowing through it, either today or in the future. To decide what pieces of code are *critical* like that, is up to the programmer to analyze, of course. The guideline should be as follows:

1. Try to use algorithms that scale linear in the size of the input, or even better (constant, logarithmic) if possible. Linear is acceptable, logarithmic is better and constant is best, of course.
2. Only when there is no other obvious possibility, use a quadratic or linearithmic algorithm.
3. Avoid exponential algorithms at all cost. Just one more data point can double, triple etc. the runtime of your computation!

In summary, just know to always keep an eye on algorithmic complexity. Aim for constant (amortized) operations per operation and only use logarithmic, linear or worse guarantees if necessary.
