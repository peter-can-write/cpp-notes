# template-specialization

For template classes it may be necessary to provide single "exceptions
to the rule”, meaning you have a general class template but want the
class template to have specific behavior for a certain type. Such
exceptions are called template specializations. There are full and
partial ones. Full template specializations do not have any template
behavior, but work only for one type. Partial template specializations
are still general in the sense that they take any type T, but only
specific variants of that type T, such as T* or T&.

// Template class
template<typename T>
class Foo
 {
public:
    
     Foo()
     {
         std::cout << "Foo<T> called" << std::endl;
     }
 };
// Full specialization, T is not used
template<>
class Foo<int>
 {
public:
     Foo()
     {
         std::cout << "Foo<int> called" << std::endl;
     }
 };
// Partial specialization, T is used, but in different form: T*
template<typename T>
class Foo<T*>
 {
public:
    
     Foo()
     {
         std::cout << "Foo<T*> called" << std::endl;
     }
 };
int main(int argc, char * argv [])
 {
     Foo<double> foo;
    
     Foo<int> a;
    
     Foo<short*> b;

}

Output:

>>> FOO<T> CALLED

>>> FOO<INT> CALLED

>>> FOO<T*> CALLED
