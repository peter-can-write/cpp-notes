# prefer-`auto`-to-explicit-type-declarations

Besides the greatly reduced number of characters when specifying types,
auto has four main benefits, listed below. Do note the discrepancy with
initializers lists for auto, though.


auto requires initialization

Often, it may have occurred that you wanted to declare an integer and
use it right away:

    int x;

    for (int i = 0; i < N; ++i) ++x;

The problem is that x, here, may not have been initialized correctly.
More precisely, it is default-initialized, not value-initialized,
meaning it _can_ contain zero, but it can just as well contain any
horrible garbage value.

auto requires direct initialization, so you cannot forget to initialize
the variable any longer:

    auto x = 0;

    for (int i = 0; i < N; ++i) ++x;


Better portability and more exact types

Sometimes, you may want to store a value that you think will not be that
high in an integer of lower bitwidth:

    unsigned size = container.size();

This is fine on 32-bit systems, as there an unsigned int and an
unsigned long (which is any std::container<T>::size_type) are both
32-bit. However, on 64-bit systems you could have serious portability
issues as the std::container<T>::size_type would be 64-bit while the
unsigned int remains 32-bit. Thus, just use std::size_t or auto:

    auto size = container.size();

To decide betweeen std::size_t and auto, I would say use auto for
return-values and std::size_t for declarations (e.g. std::size_t x = 0),
as auto will initialize an int while you may not need the negative
values and also a greater positive range.


Fewer type mismatches

Type mismatches happend, consider this case:

    // Also valid for unordered_map, set and unordered_set
    std::map<std::size_t, std::string> map;

    for (const std::pair<std::size_t, std::string>& pair : map)
    {
        /* ... */
    }

The problem here is that the pair returned by std::map or any other
associative container has its key const, i.e. here the type you get from
derefencing an std::map::iterator is actually
std::pair<const std::size_t, std::string> (note the const for the key).
This will compile, however the pair you get will be a copy of the
original pair, which is less efficient. You could have avoided this
type-mismatch by using auto:

    std::map<std::size_t, std::string> map;

    for (const auto& pair : map)
    {
        /* ... */
    }


Greater maintainability

Then there is also maintainabilty. At the start of your new project, you
may think foo will return a raw pointer:

    int* ptr = foo();

But then you become smarter and have it return an std::unique_ptr. The
code above would then fail, using auto would have adjusted to this
_directly_, i.e. maintained it _for you_:

    auto ptr = foo();
