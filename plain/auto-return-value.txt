# auto-return-value

You can use auto as a return type and the compiler will infer the return
type from the return statement:

auto foo(int x) // -> std::string
 {
return std::to_string(x);

}

You can also explicitly specify the return type when using auto via the
-> arrow syntax. This is especially useful if the return type depends on
the argument passed. Consider this (possible) implementation of
std::end, which will return a const_iterator for const (cv-qualified)
types and an iterator for non-const (non-cv-qualified) types:

template<typename Container>
auto end_itr(Container& container) -> decltype(container.end())
 {
return container.end();
 }
int main(int argc, char * argv[])
 {
std::vector<int> non_const = {1, 2, 3};
const std::vector<int> is_const = {1, 2, 3};
auto a = end_itr(non_const);
auto b = end_itr(is_const);
// NSt3__111__wrap_iterIPiEE
std::cout << typeid(a).name() << std::endl;
// NSt3__111__wrap_iterIPKiEE
std::cout << typeid(b).name() << std::endl;
std::cout << std::boolalpha
   << (typeid(a).hash_code() == typeid(b).hash_code()) // false
           << std::endl;

}

Previously, you would have had to write two overloads:

template<typename Container>
typename Container::iterator end(Container& container)
 {
return container.end();
 }
template<typename Container>
typename Container::const_iterator end(const Container& container)
 {
return container.end();

}


