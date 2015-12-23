# the-friend-keyword

friend keyword grants a class or function access to private members of a
class. It should be mentioned that friendship is not bi-directional,
meaning if A declares B it’s friend, this does not mean that A also has
access to B’s internals.

class B;
class A
 {
private:
    
     friend class B;
    
     int x = 5;
 };
class B
 {
public:
    
     B()
     {
         A a;
        
         std::cout << a.x << std::endl;
     }
 };
int main(int argc, char * argv [])

{

    B b;

}

Output:

>>> 5
