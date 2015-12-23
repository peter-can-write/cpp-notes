# base-class-specific,-inherited-static-member

If you want to make a certain static member part of an interface of a
set of classes, meaning you would like to already include it
in the (maybe abstract) base class’ declaration, but want the method to
have a different definition for different derived classes, make the base
class a template class and derive it with the type of the derived class:

template<typename T>
class Base
 {
public:
    
     static int x;
 };
template<typename T>
int Base<T>::x = 0;
// Base<DerivedA> class definition is instantiated
// by the compiler (compile-time polymorphism)
// So it inherits from its own, specific base class
// And thus DerivedA::x is different from any other
// derived class' x

class DerivedA : public Base<DerivedA>

{};

// Inherits from Base<DerivedB>, which is a separate
// definition from Base<DerivedA>, so has its own
// static x member, which is inherited by DerivedB
class DerivedB : public Base<DerivedB>

{ };

int main(int argc, char * argv [])
 {
     DerivedA::x = 2;
    
     DerivedA a, b;
    
     DerivedB::x = 5;
    
     DerivedB c, d;
    
     // Both have their own static version of x
     std::cout << a.x << "\t" << b.x << std::endl;
    
     std::cout << c.x << "\t" << d.x << std::endl;

}

Output:

>>> 2     2

>>> 5     5

This way, a new version of Base’s declaration is instantiated for each
Derived class (compile-time polymorphism), so that Derived inherits its
own version of the static member and doesn’t have to share it. Else,
this would happen:

class Base
 {
public:
    
     static int x;
 };
int Base::x = 0;

class DerivedA : public Base

{ };

class DerivedB : public Base

{ };

int main(int argc, char * argv [])
 {
     DerivedA::x = 2;
    
     DerivedA a, b;
    
     DerivedB::x = 5;
    
     DerivedB c, d;

   

    std::cout << a.x << "\t" << b.x << std::endl;
    
     std::cout << c.x << "\t" << d.x << std::endl;

}

Output:

>>> 5     5

>>> 5     5

(the static member is shared by all derived classes)
