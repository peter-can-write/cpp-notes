# size-of-string-literal-at-compile-time-with-constexpr

template<std::size_t N>
constexpr std::size_t lower(const char (&string) [N], std::size_t i = 0,
std::size_t count = 0)
 {
     return i == N ?
             count :
             string[i] >= 'a' && string[i] <= 'z' ?
                  lower(string, i + 1, count + 1) :
                  lower(string, i + 1, count);
 }
int main(int argc, char * argv [])
 {
     std::cout << lower("Hello World!");

}
