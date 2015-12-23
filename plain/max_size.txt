# max_size

You can retrieve an STL container’s maximum number of elements it can
store via the non-static max_size() method.

int main(int argc, char * argv[])
 {
std::cout << std::vector<int>().max_size() << std::endl;
// Output: 4611686018427387903

}
