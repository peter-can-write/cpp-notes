# comparison-types-for-associative-containers-of-pointers

When you have an associative container of pointers or want to sort a
sequence of pointers, don’t forget to specify your own predicates to
dereference the pointers.

First of all, of course, don’t expect that if you iterate over such a
sequence, that you will get the values to which the pointers point:

int main(int argc, char * argv[])
 {
std::set<int*> ptrs {new int(5), new int(-1), new int(10)};
for (const auto& i : ptrs) print(i); // prints memory addresses
for (auto& i : ptrs) delete i;

}

But more importantly, when you dereference those pointers, they will not
be sorted by their value, but by their memory address (the
pointers’ values):

int main(int argc, char * argv[])
 {
std::set<int*> ptrs {new int(5), new int(-1), new int(10)};
for (const auto& i : ptrs) print(*i); // 5 -1 10
for (auto& i : ptrs) delete i;

}

So the solution is to pass your own comparison function (predicate):

int main(int argc, char * argv[])
 {
auto predicate = [&] (int* a, int* b) { return *a < *b; };
std::set<int*, decltype(predicate)> ptrs({
new int(5),
new int(-1),
new int(10)
 }, predicate);
for (const auto& i : ptrs) print(*i); // -1 5 10
for (auto& i : ptrs) delete i;

}
