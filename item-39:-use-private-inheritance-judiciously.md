# item-39:-use-private-inheritance-judiciously

When you inherit a derived class from a base class PRIVATELY, you
inherit only the implementation and none of its interface. Moreover, you
cannot actually access a privately inherited derived class through a
pointer to base class! Use private inheritance when you need to redefine
a virtual function of another class, but don’t want to make it part of
your interface. 

class Base {virtual void foo();};

// Derived can now redefine foo and is thus IMPLEMENTED IN TERMS OF
BASE. Yet, Base is not part of its interface.

class Derived : private Base { virtual void foo() override final;};

To use composition, you would need to create a nested class and define
foo. Another option would be to pass a function to an independent Base
class that the Base class calls whenever needed. 


