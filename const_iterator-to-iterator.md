# const_iterator-to-iterator

Just as pointers implicitly convert to const pointers, iterators
implicitly convert to const_iterators. Therefore, you can get a
const_iterator from an iterator by assignment:

std::vector<int> vec = {1, 2, 3};

std::vector<int>::const_iterator = vec.begin(); // returns iterator, is
cast to const_iterator

or via a static_cast:

std::vector<int>::const_iterator c_itr = vec.begin() + 10;

std::distance(vec.begin(),  itr); // error, wrong types

std::distance(static_cast<std::vector<int>::const_iterator>(vec.begin()),
itr); // OK
