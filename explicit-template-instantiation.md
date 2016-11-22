# Explicit Template Instantiation

Normally, it is not possible to split a template class' method definitions into a separate implementation file. That is, the methods must be defined inline, in the header. If you do not, upon compilation, the compiler will instantiate a the template class *declaration* for your requested type. However, not seeing the definitions, the compiler will not generate appropriate instances of the member functions you defined in the implementation file. The result is undefined symbols during the linking phase.

adder.hpp
```C++
template<typename T>
class Adder {
 public:
  T add(const T& t);
};
```

adder.cpp
```C++
#include "adder.hpp"

template<typename T>
T Adder<T>::add(const T& t) {
  return t + 1;
}
```

main.cpp
```C++
auto main() -> int {
  // Linker error!
  std::cout << Adder<int>().add(5) << std::endl;
}
```

There exists, however, a possibility to nevertheless define your template class' methods in an external file. For this, however, you must know the type(s) you want to instantiate your class with beforehand. If you do, you can simply *explicitly instantiate* your template class after the external definitions of the member functions. Given that the member functions will have been defined before these instantiations, the compiler can not only generate the proper declaration of the template class (which is probably included), but also the method definitions. Then, upon linkage, you will no longer get a linker error, as long as you only use the types you explicitly requested.

adder.cpp
```C++
#include "adder.hpp"

template<typename T>
T Adder<T>::add(const T& t) {
  return t + 1;
}

template class Adder<int>;
```

main.cpp
```C++
auto main() -> int {
  // Ok!
  std::cout << Adder<int>().add(5) << std::endl;

  // Linker error!
  std::cout << Adder<float>().add(3.14) << std::endl;
}
```

Note the special syntax `template class Adder<int>;`. This is the syntax to request an explicit instantiation of the template. It is different from the syntax for an *explicit specialization*, which would be `template<> class Adder<int> { /* ... */};`
