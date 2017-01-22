# Don't optimize prematurely

This item highlights the importance of not optimizing your code prematurely. There are two reasons for this: readability and the fact that you don't always know better.

## Readability

The definition of premature optimization is any design change or plan we make that complicates our code and makes it less readable, in the name of a performance gain that is not (yet) justified. We may often see performance problems where there are none and make our programs unnecessarily complex right from the start. Remember, for this:

> It is far, far easier to make a correct program fast than it is to make a fast program correct.

As such, it is better to first (1) focus on readability and (2) the larger-picture, asymptotic performance of your code, rather than saving an extra addition.

## You don't always know better

Another problem with premature optimization is that we may not always really know what we are doing. Even when we think we know that a certain minor optimization will make our code run faster, we may only look at the high level problem, rather than consider all the complex details of modern computer architectures. This involves branch prediction, cache performance, memory bottlenecks and multiple pipelines. Next to that, already the compiler will make many, many optimizations (hopefully), using hard facts rather than your guesswork.

## Summary

As such, in summary, you should:

1. Focus on the large-scale complexity of your code.
2. Focus on readability over minor optimizations.
3. If you notice problems, measure.
4. If you change something to solve (3), measure again.
