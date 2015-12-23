# different-string-implementations

String implementations between different libraries may differ in:

-   Whether or not they are reference-counted. Many implementations use
    reference counting to avoid unnecessary allocations for
    copied strings. This can be good if strings are often copied, but
    can be bad if they are not or in multi-threaded systems, as
    concurrency support has high overhead.
-   String objects may range in size form one to at least seven times
    the size of char* pointers, because they store more data, e.g. the
    size and capacity or even because they have a static fixed-size
    array to store small strings.
-   Creation of new strings may require zero (if put into a fixed-size
    array), one (if allocating only the character buffer) or two dynamic
    allocations (if allocating an implementation struct and then also
    the character buffer).
-   String objects may share information to different extents, i.e. some
    share their character buffers (via ref-counting), some share nothing
    because they do not use ref-counting or they may share all data
    including size and other members if they share pointers
    to implementation.
-   Some strings have a minimum allocation policy, i.e. even if the user
    requests to store only a small string of size 5, it will allocate
    memory for, e.g., 32 objects, for efficiency, to reduce the number
    of allocations.

E.g. on my system:

#include <string>

#inclue <iostream>

int main(int argc, char * argv[])
 {
std::string s;
std::cout << s.size() << ", " << s.capacity();
// Output: 0, 22

}
