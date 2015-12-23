# abstract-classes-without-pure-virtual-methods

If you want a class without pure virtual functions to be solely an
abstract class, make the destructor pure virtual:

class Base

{

public:

     void do(){ }

     virtual ~Base() = 0;

};

I.e.: you want a base class for inheritance/polymorphism but don't want
the base class to be instantiateable. 

That way, giving it a pure virtual function makes it an abstract class.
However, you must implement that destructor somewhere, as the compiler
will call it when a derived class’ destructor is called. 


