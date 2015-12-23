# stream-operator-overloading

class Foo
 {
public:
    
     friend std::ostream& operator<<(std::ostream& stream, const Foo&
foo)
     {
         stream << foo.x;
        
         return stream;
     }
    
     friend std::istream& operator>>(std::istream& stream, Foo& foo)
     {
         stream >> foo.x;
        
         return stream;
     }
    
 private:
    
     int x;

};


