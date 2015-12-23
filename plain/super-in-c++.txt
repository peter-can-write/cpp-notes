# super-in-c++

Just use a typedef/alias!!!!!!

class Base
 {
public:
void foo()
 {
 }
 };
class Derived : public Base
 {
public:
using super = Base;
 Derived()
 {
super::foo();
 }

};


