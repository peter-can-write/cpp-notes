# logical-and-bitwise-const

Bitwise const-correctness is the idea that objects, physically, in
memory, in terms of their bits, stay constant. 

class Foo
 {

public:

    Foo(int x)
     : x_(x)
     { }
    
     void foo(int x) const
     {
         x_ = x; // Error, function marked const

    }

private:
    
     int x_;

};

Logical const-correctness argues that even though a function may be
marked const, this only applies to the interface of the class, or to
any private data members that the client may ever interact with. It may
be wanted to modify other data members, that are only important for the
implementation (e.g. capacity length for string classes, not the actual
length, which the user interacts with). You can make it possible to
ensure logical const-correctness by marking private data members
as “mutable”:

class Foo
 {
public:
    
     Foo(int x)
     : x_(x)
     { }
    
     void foo(int x) const
     {
         x_ = x; // OK!
     }
    
 private:
    
     mutable int x_;
 };


