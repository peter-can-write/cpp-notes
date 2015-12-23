# item-33:-avoid-hiding-inherited-names

When a base class has overloads for a function and  you redefine that
function in a derived class, only the overload that you redefine will be
visible, all other overloads will be hidden.

class Base

{

public:

     virtual void foo();

     virtual void foo(int x);

};

class Derived : public Base

{

public:

     void foo();

};

Derived d;

d.foo(); // works, calls Derived::foo

d.foo(5); // fails, foo(int x) is hidden by Derived

To make all versions visible, use a USING statement:

class Derived : public Base

{

public:

     using Base::foo;

     void foo();

};

Now all overloads of Base::foo() are visible:

Derived d.

d.foo(); // works, calls Derived::foo

d.foo(5); // now works too, calls Base::foo(int x)


