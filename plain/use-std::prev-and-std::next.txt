# use-std::prev-and-std::next

Often, you needed a iterator to the preceding position of another
iterator, but didn’t want to change that other iterator. Use std::prev
or std::next for that!!!

std::vector<int> v = {1, 2, 3};

auto begin = v.begin();
// Doesn't modify begin
auto previous = std::prev(begin);
// Else:
// auto previous = begin;
// --previous;
// Or:
// auto previous = v.begin();

// auto begin = previous--;
