# item-35:-consider-alternatives-to-virtual-functions

This item mentioned that as an alternative to virtual functions, you
could use two different methods if the situation requires it. 

1. NON-VIRTUAL INTERFACE IDIOM: make a pure virtual function private in
the base class, so that the derived class needs to implement it, but
cannot use it. provide a default implementation as a virtual function. 

class Base

{

public:

     virtual void defaultFoo();

private:

     virtual void foo() = 0;

};

class Derived : public Base

{

public:

     // needs to implement foo()

     virtual void foo() override

     { Base::defaultFoo(); }

};

2. MOVE FUNCTION IMPLEMENTATION OUT OF CLASS. 

class Derived : public Base { Derived(function pointer) : Base (function
pointer); void foo() { return function-pointer(); }};

+ accessors to access and change implementation at run time. Can also be
used with an object hierarchy of algorithms which derive from a base
implementation class. This is then called the STRATEGY PATTERN. 


