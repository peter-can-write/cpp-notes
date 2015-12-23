# declare-overriding-functions-override

For functions in derived classes to actually override their base-class
counterparts, some things have to be taken into account:

-   The base-class method has to be declared “override"
-   The function names must be identical (except for destructors)
-   The parameter types of the base and derived functions must be
    _identical_ (not only compatible).
-   The const-ness of the base and derived functions must be identical.
-   The return types and exception specifications of the base and
    derived functions must be _compatible._
-   The reference-qualifieres of the base and derived functions must be
    _identical_. 

Thus in this case, there is not a single method in the derived class
that overrides its base-class counterpart:

class Base

{

public:

     virtual void f1() const;

     virtual void f2(int x);

     virtual void f3() &;

     void f4() const;

};

class Derived : public Base

{

public:

     virtual void f1();

     virtual void f2(unsigned int x);

     virtual void f3() &&;

     void f4() const;

};

1.  wrong const-nesss
2.  non-identical types
3.  wrong reference-qualifiers
4.  not virtual in base class

By using override after the functions, all of those errors would have
shown!

class Base
 {
public:
virtual void f1() const;
virtual void f2(int x);
virtual void f3() &;
void f4() const;
 };
class Derived : public Base
 {
public:
virtual void f1() override; // 'f1' marked 'override' but does not
override any member functions
virtual void f2(unsigned int x) override; // 'f2' marked 'override' but
does not override any member functions
virtual void f3() && override; // 'f3' marked 'override' but does not
override any member functions
void f4() const override; // Only virtual member functinos can be marked
'override'

};

Thus, use override!
