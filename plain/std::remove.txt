# std::remove

Understand that std::remove does not actually erase elements from a
container, because it _cannot_. To erase an element from a container, it
would have to call its erase function, to which it has no access because
the algorithm is given only iterators into the container. However, it
partitions the container such that you can easily erase the elements you
wish to remove.

Nevertheless, it does not follow a simple partitioning algorithm such
as:

template<typename Itr, typename T>
 Itr rm(Itr begin, Itr end, const T& value)
 {
for (--end; ; )
 {
while (begin != end && *begin != value) ++begin;
while (begin != end && *end == value) --end;
if (begin == end) break;
std::swap(*begin, *end);
 }
return *begin == value ? begin : ++begin;

}

Rather, it uses an algorithm that overwrites values that should be
removed with those that should not be removed.

template<typename Itr, typename T>
 Itr rm(Itr begin, Itr end, const T& value)
 {
 Itr first = end;
for (Itr last = first; begin != end; ++begin)
 {
if (*begin == value)
 {
if (first == last) first = begin;
 last = std::next(begin);
 }
else if (first != last)
 {
 *first++ = *begin;
 ++last;
 }
 }
return first;

}

int main(int argc, char * argv[])
 {
std::list<std::size_t> l = {0, 1, 3, 3, 4};
auto first = rm(l.begin(), l.end(), 3);
// 0, 1, 4, 3, 4
printall(l.begin(), l.end());
// 3
println(std::distance(l.begin(), first));

}

Note that now in the sequence, after, the values starting at the
iterator at the start of the range of elements to be erased are not
necessarily all the value that need to be erased. This is due to the way
the algorithm operates (it overwrites matching values, doesn’t swap them
-> efficiency).


