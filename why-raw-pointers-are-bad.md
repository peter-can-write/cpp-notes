# Why raw pointers are bad

Raw pointers are bad because:

1. Their declaration does not indicate whether it points to a *single object* or to an *array*.
2. Its declaration reveals nothing about whether you should destroy what it points to when you're done using it, i.e. if the pointer *owns* the thing it points to.
3. If you determine that you should destroy what the pointer points to, there's no way to tell how. Should you use `delete`, or is there a different destruction mechanism, e.g. a dedicated destruction function, the pointer should be passed to?
4. If you manage to find out that `delete` is the way to go, Reason (1) means it may not be possible to know whether to use the single-object form (`delete`) or the array form (`delete[]`). If you use the wrong form, results are __undefined__.
5. Assuming you ascertain that the pointer own what it points to and you discover how to destroy it, it's difficult to ensure that you perform the destruction *exactly once* along every path in your code. Missing a path leads to resource leaks, and doing the destruction more than once leads to undefined behavior.
6. There's typically no way to tell if the pointer *dangles*, i.e. if it points to memory that no longer holds the object the pointer is supposed to point to. Dangling pointers arise when objects are destroyed while non-owning pointers still point to them.
