# stl-sorting-algorithms

There are multiple sorting algorithms that should be used under
different circumstances: these are: sort, stable_sort, partial_sort,
nth_element, partition, stable_partition and a few of those also as
member functions.

SORT

Sort, of course, simply sorts a sequence of objects according to some
order, by default std::less. For list, use std::list::sort (member
function).

std::vector<std::size_t> v = {2, 1, 4, 5, 3};
// default, uses std::less as the comparator, sorts in non-decreasing
order
 std::sort(v.begin(), v.end());
// Equivalent to
 std::sort(v.begin(), v.end(), [](std::size_t a, std::size_t b) { return
a < b; });
// equivalent to
struct
 {
bool operator()(std::size_t a, std::size_t b)
 {
return a < b;
 }
 } less_than;
 std::sort(v.begin(), v.end(), less_than);
 printall(v.begin(), v.end()); // 1, 2, 3, 4, 5
// opposite of previous, sorts in non-ascending order
 std::sort(v.begin(), v.end(), std::greater<std::size_t>());

printall(v.begin(), v.end()); // 5, 4, 3, 2, 1

STABLE_SORT

There is one important disadvantage of the standard sorting algorithm:
it is not stable (probably uses quicksort). On some implementations the
algorithm will be stable, however it is not guaranteed by the standard.
With std::stable_sort, it is, however, guaranteed that the oder of equal
elements is preserved.

std::vector<std::string> v = {"apple", "bob", "boy", "carl", "camp",
"alpha"};
// First sort lexicographically
std::sort(v.begin(), v.end());
// alpha, apple, bob, boy, camp, carl
printall(v.begin(), v.end());
// Then sort by length
std::sort(v.begin(), v.end(),
   [&] (const std::string& a, const std::string& b)
   { return a.size() < b.size(); });
// On this implementation prints
// bob, boy, camp, carl, alpha, apple
// i.e. it is stable, however, this is not guranteed
printall(v.begin(), v.end());
// better, if really stability is necessary,
// to use std::stable_sort
std::stable_sort(v.begin(), v.end(),
   [&] (const std::string& a, const std::string& b)
   { return a.size() < b.size(); });
// In this case same as above

printall(v.begin(), v.end());

PARTIAL_SORT

std::partial_sort is useful “if you have a vector, string, deque or
array and you need to put only the top _N_ elements in order”.

std::array<std::size_t, 10> a = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3};
// Get the largest 3 elements
 std::partial_sort(a.begin(), // begin
   a.begin() + 3, // middle (sort elements into this range)
   a.end(), // end
   std::greater<std::size_t>()); // condition/predicate

printall(a.begin(), a.end());  // 9, 8, 7, 2, 4, 5, 1, 6, 0, 3

Possible implementation:

template<typename Itr>
void partial(Itr begin, Itr middle, Itr end)
 {
std::nth_element(begin, middle, end);
std::sort(begin, middle);

}

NTH_ELEMENT

Nth_Element is based on the quick-select algorithm and has two
functions: first of all, it identifies the nth-element in some specific
order; second of all it puts all elements before the nth-element to its
left. Thus, nth_element is like partial_sort, but with the elements left
of the element than the element not being in sorted order. Use it if
you, for example, only need the top 20 elements but not in order. “The
element placed in the nth position is exactly the element that would
occur in this position if the range was fully sorted.”

std::array<std::size_t, 10> a = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3};

// Get the 5th smallest element into index 4

std::nth_element(a.begin(),

                 a.begin() + 4,  

                 a.end(),

                 std::greater<std::size_t>());

// 6, 7, 9, 8, *5*, 2, 1, 4, 0, 3

printall(a.begin(), a.end());

To get the median, you would use a.begin() + a.size()/2 (i.e. the
middle).

Possible implementation:

template<typename Itr>
 Itr partition(Itr begin, Itr end)
 {
 Itr middle = begin + std::distance(begin, end--)/2;
 Itr first = begin++;
std::swap(*first, *middle);
while (begin != end)
 {
while (begin != end && *begin < *first) ++begin;
while (begin != end && *end > *first) --end;
if (begin == end) break;
std::swap(*begin, *end);
 }
if (*begin > *first) std::swap(*first, *(--begin));
else std::swap(*first, *begin);
return begin;
 }
template<typename Itr>
void nth_element(Itr begin, Itr nth, Itr end)
 {
while (begin != end)
 {
 Itr itr = partition(begin, end);
if (itr < nth) begin = ++itr;
else if (itr > nth) end = itr;
else break;
 }

}

PARTITION

A partition operation separates a sequence of object into two halves,
the one satisfying the condition and the other not. Neither half is
sorted. std::partition returns an iterator to first element not
satisfying the condition.  There is also stable_partition. Both
partitioning algorithms also work for bidirectional iterators.

std::array<std::size_t, 10> a = {5, 7, 4, 2, 8, 6, 1, 9, 0, 3};
// Partition into two halves, one of values less than 3 and the other
// not less than three, i.e. greater than or equal 3
// first should thus be at index 3

auto first = std::partition(a.begin(),

                                                       a.end(),  

                            [&] (std::size_t a)  

                            { return a < 3; });

// 0, 1, 2, 4, 8, 6, 7, 9, 5, 3
printall(a.begin(), a.end());
// Indeed: 3

println(std::distance(a.begin(), first));

Possible implementation:

template<typename Itr, typename Predicate>
 Itr partition(Itr begin, Itr end, Predicate predicate)
 {
for (--end; ; )
 {
while (begin != end && predicate(*begin)) ++begin;
while (begin != end && ! predicate(*end)) --end;
if (begin == end) break;
std::swap(*begin, *end);
 }
return predicate(*begin) ? ++begin : begin;

}
