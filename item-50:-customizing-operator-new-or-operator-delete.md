# item-50:-customizing-operator-new-or-operator-delete

Reasons to overload operator new() / operator delete():

-   To detect usage errors: you may want to perform some additional
    checks on your memory

-   To improve efficiency: C++’s globally-scoped operator new() is very
    general, for small and large allocations, many and few, short-
    and long-lived. It may be beneficial to optimize the new function
    for your class’ memory usage

-   To collect usage statistics: to do the above, it is a good idea to
    profile your program to see whether the allocation/deallocation
    patterns are random order, LIFO, FIFO, what sizes they are, their
    lifespan etc.

-   To obtain unconventional behavior. This varies depending on the
    problem at hand, but one example is overwriting deleted memory with
    zeros for extra security.


