# making-classes-uncopyable

Explicitly disallowing compiler-generated constructors,
copy-constructors, destructors or assignment operators:

If I have a class Foo:

class Foo

{

public:

     Foo(){ }     

};

… that I don’t want to have copied, what do I do? If I don’t implement
the copy-constructor, the compiler will do so for me.

Step 1: Make the copy-constructor private:

class Foo

{

public:

     Foo() { }

private:

     Foo(const Foo& other);

     Foo& operator=(const Foo& other);

};

This means that no non-member or non-friend will be able to call the
copy constructor. However, friends and members will be able to do so … 

Step 2: Don’t implement the functions

If I don’t implement those two functions, calling them will create
linker errors!

Step 3: Make the linker error a compile-time error by making a special
base class

class Uncopyable

{

protected:

     Uncopyable(){ }

     ~Uncopyable(){ }

private:

     Uncopyable(const Uncopyable&); // Don’t need to give parameter
names!

     Uncopyable& operator=(const Uncopyable&);

};

class Foo : private Uncopyable

{

public:

     Foo(){ }

};

Making the Uncopyable class’s copy-constructors private and leaving them
unimplemented means that if Foo inherits from it (private, meaning
Uncopyable() will become private)

and if the compiler then tries to create a default copy-constructor,
that would mean that it would also have to initialize the base class
members, so it would call the base class copy

constructor, which is, however, private, so Foo wouldn’t have access and
copying a Foo object would create a compile-time error!

C++11:

class Uncopyable

class Uncopyable
 {
public:
 Uncopyable(int x = 0)
 : _x(x)
 { }
 Uncopyable(const Uncopyable& other) = delete;
 Uncopyable(Uncopyable&& other);
Uncopyable& operator=(const Uncopyable& a) = delete;
private:
int _x;
 };
int main(int argc, char * argv[])
 {
Uncopyable a(5); // OK, not a deleted overload of Uncopyable()
Uncopyable b(a); // Error: Call to deleted constructor of Uncopyable
Uncopyable c(std::move(a)); // OK, not a deleted overload of
Uncopyable()
 c = a; // Error: Overload resolution selected deleted operator '='

}


