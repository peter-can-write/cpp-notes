# swap-trick-for-shrink_to_fit

To trim a standard container's excess capacity, you can nowadays use
std::container<T>::shrink_to_fit.

Before this method was available, you could use the "swap trick"

std::container<T> c;

c.reserve(1E6); // too much space

std::container<T>(c).swap(c);

The copy constructed container will only allocate as much space as it
needs (= c.size()) or as much as the implementation allows (some
implementations have a minimum capacity or needs to a power of two). It
will also contain the exact same elements. By swapping the containers, c
then has the data without excess capacity and the temporary one does,
which will then go out of scope and destruct and free all the capacity.
