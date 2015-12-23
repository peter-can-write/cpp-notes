# operator-new-vs-new-operator

Operator new is the function used to allocate raw memory, it's signature
is:

void* operator new(size_t bytes)

And you would have to cast the raw memory to a pointer to your class if
you used it.

The "new operator", better called the "new expression" is what you call
with new:

int* ptr = NEW int(5);

The new operator (expression) uses operator new.
