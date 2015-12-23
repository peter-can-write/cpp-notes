# inserters

Standard (STL) algorithms that copy elements into another range expect
that that other range already holds initialized and dereferenceable
values. E.g. for

std::copy(source_begin, source_end, destination_begin)

It should be that there is space for std::distance(source_begin,
source_end) elements in the destination, starting at destination_begin,
since any such algorithm will be of the lines of:

template<typename I, typename J>

void algorithm(I begin, I end, J destination)

{

     for ( ; begin != end; ++begin)

     {

          /* something */

          *destination++ = *begin;

          /* something */

     }

}

I.e. destination will be incremented for each value between begin and
end. If that space is not valid, initialized memory, behavior is
undefined, but horrible either way.

There are generally two solutions:

1) Resize before, if not enough already space (cost of default
initialization)

2) Reserve space, then use an INSERTER

There are three types of inserters:

std::back_inserter(container): will call push_back on the container to
insert a new value

std::front_inserter(container): will call push_front on the container to
insert a new value (for std::list, and std::deque)

std::inserter(container, iterator to that container), will insert
continuously at that position

std::front_inserter would insert at the front always, thus if you call
it sequentially on a range, the values will be in reverse order. To undo
this, iterate not from begin() to end(), but from rbegin() to rend().

std::vector<std::size_t> source = {1, 2, 3, 4, 5};
 std::vector<std::size_t> a, b = {10, 11, 12};
 std::list<std::size_t> c;
 std::copy(source.begin(), source.end(), std::back_inserter(a));
// 1 2 3 4 5
for (const auto& i : a) print(i, " ");
 println();
std::copy(source.begin(), source.end(), std::inserter(b, b.begin() +
1));
// 10 1 2 3 4 5 11 12 (before always 11)
for (const auto& i : b) print(i, " ");
 println();
 std::copy(source.begin(), source.end(), std::front_inserter(c));
// 5 4 3 2 1 (reverse order)
for (const auto& i : c) print(i, " ");
 println();
 c.clear();
// Do note the rbegin() and rend()
 std::copy(source.rbegin(), source.rend(), std::front_inserter(c));
// 1 2 3 4 5 (normal order again, equivalent to back_inserter)

for (const auto& i : c) print(i, " ");
