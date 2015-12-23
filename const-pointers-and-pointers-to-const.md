# const-pointers-and-pointers-to-const

Pointer to a const object of type foo that does not allow modification
of the data:

const foo * name;

Const pointer that does not allow changing of what it points to, it
cannot be pointed to another

object, only to this one object:

foo * const name;

int x = 5;
int y = 23;
const int* p = &x;
 *p = 12; // Error, cannot change x
    
 p = &y; // Ok, can reassign pointer
int* const q = &x;
 *q = 50; // Ok, can change x

q = &y; // Error, cannot reassing pointer
