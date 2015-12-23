# item-30:-strive-for-exception-safe-code

Exception-safe functions leak no resources and allow no data structures
to become corrupted, even when exceptions are thrown. Such functions
offer the basic, strong or nowthrow guarantee:

-   THE BASIC GUARANTEE promises that if an exception is thrown,
    everything in the program remains in a valid state. No objects or
    data structures become corrupted. 

-   THE STRONG GUARANTEE promises that if an exception is thrown, the
    state of the program is unchanged (atomic function). If they
    succeed, they succeed completely and if they fail, they
    fail completely. This can often be achieved through copy-and-swap,
    meaning you copy the item to be changed to a temporary object,
    change things in the copy and then swap the copy’s data back to
    the original. That way, if an exception is thrown, the original
    object remains unchanged. However, this can be inefficient due to
    copying. 

-   THE NOTHROW GUARANTEE promises never to throw an exception. 


