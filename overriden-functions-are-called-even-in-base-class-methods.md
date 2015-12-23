# overriden-functions-are-called-even-in-base-class-methods

class Base
 {
public:
    
     virtual void foo() const
     {
         bar();
     }
    
     virtual void bar() const
     {
         std::cout << "Base" << std::endl;
     }
 };
class Derived : public Base
 {
public:
    
     virtual void bar() const override
     {
         std::cout << "Derived" << std::endl;
     }
 };
int main(int argc, char * argv [])
 {
     Base* base = new Derived;

   

    base->foo(); // Derived!

   
     delete base;

}
