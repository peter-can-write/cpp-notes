# typedef

Use typedef to create generic types (wrappers) from another type.

Use standard lengths when possible, such as ::size_type or std::size_t

typdef int myint; // myint can now be used instead of int

typedef vector<type>::size_type size_t;

size_t size = myvec.size()

or in C++11:

using size_t = std::vector<type>::size_type;


