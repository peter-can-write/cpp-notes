# how-you-could-use-a-union

struct Bits
 {
union
 {
std::uint8_t byte;
struct { std::uint8_t b0:1, b1:1, b2:1, b3:1, b4:1, b5:1, b6:1, b7:1; };
 };
 };
int main(int argc, const char* argv[])
 {
Bits bits;
 bits.b0 = false;
 bits.b1 = true;
std::cout << static_cast<int>(bits.byte);

}
