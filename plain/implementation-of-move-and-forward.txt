# implementation-of-move-and-forward

#include <type_traits>
template<typename T>
typename std::remove_reference<T>::type&& move(T&& t)
 {
     return static_cast<typename std::remove_reference_t<T>&&>(t);
 }
template<typename T, typename U>
 T&& forward(U&& u)
 {
     return static_cast<T&&>(u);
 }
void foo(int& x)
 {
     std::cout << "lvalue" << std::endl;
 }
void foo(int&& x)
 {
     std::cout << "rvalue" << std::endl;
 }

int main(int argc, char * argv[])
 {
     int x = 5;
    

    foo(x);               // lvalue

    foo(move(x));         // rvalue

    foo(forward<int>(x)); // rvalue

}
