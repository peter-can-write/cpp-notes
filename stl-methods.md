# stl-methods

container<T>::iterator

container<T>::const_iterator

container<T>::reverse_iterator // starts from end, ++ goes backwards

container<T>::size_type

_container<T> c;_

c.begin()

c.end()

c.rbegin() // returns reverse_iterator, end of non-reversed

c.rend()   // start of non-reversed

c.empty() // returns bool

c.insert(pos,start_iter,end_iter)

c.erase(itr)

c.erase(start,end)

container<T>(c2) // copy constructor

container<T>(n)  // allocates memory for n items, if primitive type, all
= 0 (value initialized)

container<T>(n, x) // allocates container with n * ‘x' items in it

container<T>.insert(pos, begin, end) // inserts the range from begin to
end at pos

container<T>.assign(begin, end) // sets the (entire) contents of the
container to the range from begin to end

container<T>.erase(begin, end) // erases the range from begin to end of
the container

 container<T>(begin, end) // constructs the container with the contents
of the range from begin to end (like assign)


