# size-of-array-with-constexpr

template<std::size_t N>
constexpr std::size_t size(char (&a) [N])

{

    return N - 1; // null terminator

}
int main(int argc, char * argv [])
 {
     char string [] = "Hello";
    
     std::cout << size(string) << std::endl;

}

Also used in the std::end implementation for arrays. Here a replication:

template<typename T, std::size_t N>
 T* end_itr(T (&array) [N])
 {
return array + N;
 }
int main(int argc, char * argv[])
 {
int arr [] = {1, 2, 3};
std::copy(std::begin(arr), // equivalent to arr itself
   end_itr(arr),   // equivalent to arr + 3
   std::ostream_iterator<int>{std::cout, " "});
// Output: 1 2 3

}
