# std::vector<bool>

std::vector<bool> is implemented as a partial template specialization of
vector that stores its data not as individual objects, but stores each
boolean in one bit of a certain number of bytes. This is a problem,
because

std::vector<bool> vec;

bool* ptr = &vec[0];

should actually compile to be a compliant STL container, but does not
because operator[] doesn’t return a reference to the boolean at that
index (can’t reference a bit), but returns some proxy object. bool*
cannot be bound to proxy*.

To solve the problem, use std::bitset (has a fixed number of bits) or
std::deque<bool>, which really stores bools.
