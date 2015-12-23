# arrays

type name [size] = {…};

Size of an array

sizeof(arr) / sizeof(*arr); // total size divided by size of single
element

------------------------------------------------------------------------

Use with std functions

int a [] = {1, 2, 3};
int b [3];
// Could also use a + 3 (because size is known)
std::copy(a, a + sizeof(a)/sizeof(*a), b);
std::copy(std::begin(b), std::end(b),
std::ostream_iterator<int>{std::cout, " "});
std::cout << "\n";
// or
std::copy(std::begin(a), std::end(a), std::begin(b));

for (int i : {0, 1, 2}) std::cout << b[i] << " ";

std::begin(arr); // equivalent to arr; first element

std::end(arr); // equivalent to arr + sizeof(arr)

------------------------------------------------------------------------


