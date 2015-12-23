# re-allocation-for-string-and-vector

When you request to push back an element in a string or vector where
size() == capacity() or when you wish to resize() the container to a
size greater than the capacity(), a re-allocation will take place. This
involves the following four steps:

-   Allocation of new memory block. The number of elements that can be
    stored in this new memory will be some integer multiple of the
    number that could be stored in the previous block (mostly a
    factor 2).
-   Copying of old objects to new memory (move in C++11, if move
    constructor is no-except).
-   Destroy all objects in old memory (Call their destructor).
-   De-allocate old memory

As can be seen, this is a very expensive process, which is why you
should use reserve() or even resize() when you can. Moreover,
re-allocation invalidates all iterators, pointers and references into
the old memory.

Even if you are unsure about _exactly _how many objects you will want to
store, you can use reserve() with the maximum you think you’ll need and
then shrink_to_fit() afterwards.
