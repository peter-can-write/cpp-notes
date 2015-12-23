# reassigning-references

References cannot be reassigned, meaning that a reference cannot change
the object it points to,

as references are internally stored as constant pointers.

int a = 0, b = 1;

int& ref = a; // make ref point to a

ref = b; // this doesn’t actually make ref point to b, it makes a, which
ref points to, equal to the value of b!

std::cout << ref; // prints 1


