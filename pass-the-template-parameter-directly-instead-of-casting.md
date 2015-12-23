# pass-the-template-parameter-directly-instead-of-casting

Pass the template parameter T directly to a template function by calling
function<T>(a, b) if you need implicit conversions.

std::vector<int> v = {1, 2, 3};
std::vector<int>::const_iterator itr = v.begin();
// Doesn't work, v.begin() and itr are
// not of the same type and itr (const_iterator)
// doesn't cast to the type of the first argument
// (iterator) according to which the function
// template is instantiated.
std::size_t d = std::distance(v.begin(), itr);
// First solution
std::vector<int>::const_iterator begin = v.begin();
std::distance(begin, itr); // OK
// Second, nicer solution
std::distance<std::vector<int>::const_iterator>(v.begin(), itr);
// Also possible

std::distance(static_cast<std::vector<int>::const_iterator>(v.begin()),
itr);
