# stl-item-5:-prefer-range-member-functions-over-single-element-methods

As opposed to single-element insertion, assignment, erasure or other
methods like this:

std::copy(destination, begin, end, std::back_inserter(container));

which repeatedly call the single-element insert(iterator, value)
function, use range-based STL sequence methods:

container.insert(destination, begin, end);

These include:

// Constructs the container with the elements in the range from begin to
end

std::container<T>(begin, end);

// Inserts the range from begin to end at destination

std::container<T>::insert(destination, begin, end);

or

// Inserts ‘value’ ‘count’ times starting at the destination iterator

std::container<T>::insert(destination, count, value);

// Sets the contents of the container to the range from begin to end

std::container<T>::assign(begin, end);

// Erases the range from begin to end within the container

std::container<T>::erase(begin, end);

Benefits include clarity of code, clarity of intent and three main
performance bottleneck fixes:

1.  Reduced number of function calls, as only one call to the range
    based method occurs, where all the action happens at once, opposed
    to many individual function calls to the single-element methods
2.  Reduced move operations for elements of the container, as
    range-based methods will move all incumbent elements of the
    container to their final position (end - begin positions further
    up), while single-element methods will cause repeated move
    operations for each element every time the function is called
3.  Reduced allocations, as many contiguous-memory STL
    sequence containers allocate memory by doubling their capacity every
    time the size matches the capacity, so individual single-element
    methods could cause a maximum of log_2 N allocations (8 insertions
    on a container with size 1 = 3 new allocations), while range-based
    methods immediately allocate all the required memory for the
    operations

All of the above improvements except for the function call reduction
apply to all forward iterators or derived trait categories, but not to
input or output iterators. Moreover, std::list is a node-based
container, so move operations do not apply and elements are always
allocated singly, but range-based methods may improve the assignment
efficiency of node pointers (e.g. only one assignment of each input
iterator’s next pointer, normally two, once when inserted to point to
next item, another time when the next item is inserted to point to that
item)


