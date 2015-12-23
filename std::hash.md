# std::hash

Make sure the hash function is const!!!!!

std::hash is in <functional>

class A

{
public:
 A(int x, const std::string& name)
 : _x(x)
 , _name(name)
 { }
private:
// If you don't have appropriate getters
// to retrieve all values for hashing
friend struct std::hash<A>;
int _x;
std::string _name;
 };
namespace std
 {
// Explicitly specialize the std::hash struct
template<>
struct hash<A>
 {
using argument_type = A;
using result_type = std::size_t;
result_type operator()(const argument_type& argument) const
 {
const result_type first = std::hash<int>()(argument._x);
const result_type second = std::hash<std::string>()(argument._name);
// Use all bits, mix things up
return first ^ (second << 1);
 }
 };
 }
int main(int argc, char * argv[])
 {
A a(5, "a");
std::hash<A> hash;
print(hash(a));

}
