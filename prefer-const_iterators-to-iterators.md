# prefer-const_iterators-to-iterators

In C+98, it was quite the hassle to use const_iterators. For example, to
find a value with const iterators, and then insert at the location the
iterator points to, you had to write something like this:

void find_and_insert(std::vector<int>& container, int target, int
new_value)
 {
typedef std::vector<int>::const_iterator const_itr_t;
typedef std::vector<int>::iterator itr_t;
const_itr_t c_itr =
std::find(static_cast<const_itr_t>(container.begin()),
   static_cast<const_itr_t>(container.end()),
   target);
std::size_t distance =
std::distance(static_cast<const_itr_t>(container.begin()), c_itr);
itr_t itr = container.begin() + distance;
 container.insert(itr, new_value);

}

This is extremely ugly and inefficient code (all those casts!), thus
const_iterators were rarely worth it. You couldn’t use them for
insertion, and conversion between iterators and const_iterators was
difficult. This is now different in C++11/14. There exist `cbegin()` and
`cend()` member functions for all standard containers, which return
const_iterators for all containers, not only const ones. Moreover,
insertion now also works with const_iterators for all standard
containers. Thus this is the same code in C++11 (first version):

void find_and_insert(std::vector<int>& container, int target, int
new_value)
 {
auto c_itr = std::find(container.cbegin(), container.cend(), target);
 container.insert(c_itr, new_value);

}

However, this is not yet maximally generic. Say we wanted to do this for
all standard containers, also arrays and also third-party libraries with
free `begin` and `end` functions. In C++14 this would be:

template<typename Container>
void find_and_insert(Container& container, int target, int new_value)
 {
// ADL for free begin/end functinos
using std::cbegin;
using std::cend;
auto c_itr = std::find(cbegin(container), cend(container), target);
 container.insert(c_itr, new_value);

}

However in C++11, there exist only free std::begin and std::end
functions, not their constant counterparts, thus you would have to
define those yourself:

template<typename Container>
auto cbegin(const Container& container) ->
decltype(std::begin(container))
 {
// The container's const-ness makes this return a const-iterator
return std::begin(container);

}
