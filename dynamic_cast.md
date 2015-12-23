# dynamic_cast

Use dynamic_cast when you want to perform operations on a Derived
object that are part of the Derived class’ interface, but only have a
pointer-to-Base, pointing to the Derived object. We know that when
assigning a pointer-to-Base to an object of Derived type, the
pointer-to-base will only able to access the “Base”-part of the Derived
object (methods/functions that are part of Base’s interface) and if any
(virtual) methods have been overridden in the Derived class, these
methods are accessed polymorphically. dynamic_cast is used to convert
that pointer-to-Base to a pointer-to-Derived, so that you can access the
“Derived"-part of the object as well.

struct Base
 {
     virtual void foo()
     {
         std::cout << "Base::foo()" << std::endl;
     }
 };
struct Derived : public Base
 {
     virtual void foo() override
     {
         std::cout << "Derived::foo()" << std::endl;
     }
    
     void bar()
     {
         std::cout << "Derived::bar()" << std::endl;
     }
 };
int main(int argc, char * argv [])
 {
     Base* base = new Derived;
    
     base->foo();

   

    base->bar() // fail

   
     Derived* derived = dynamic_cast<Derived*>(base);
    
     derived->foo();
    
     derived->bar(); // now ok

}

Conditions:

-   Downcasts, i.e. Base* = new Base -> Derived* =
    dynamic_cast<Derived*>(base), yield nullptrs. C++ does not let them
    succeed (… fully, no exception is thrown after all), because the
    Base object would not have the full interface that a Derived* must
    have access to. Moreover, the Base class must be polymorphic,
    meaning it must have at least one virtual function, otherwise the
    Run-Time Type Identification (RTTI) system, which lets the compiler
    figure out the dynamic type of an object, is not created for the
    object (dynamic_cast needs this system to perform its checks)
-   Upcasts are allowed, if the cast object (the parameter) is of the
    type being cast to (<T>) or derived from it, since the type T has
    access to its own interface, and can access the “T”-part of any of
    its derived classes:

Derived* derived = new Derived;

// dynamic_cast allowed for type

// of or derived class of Base*

Base* base = dynamic_cast<Base*>(derived);

Generally, for pointers to types A, B, C:

A* a = dynamic_cast<B*>(C);

C must be of type or derived from B, and B must be of type or derived
from A, and C must be of type or derived from A, because A must
ultimately have access to the full A-part of the objects interface. Thus
its base classes wouldn’t fit as C, because the base class (may) not
have the full A interface, but a smaller one
