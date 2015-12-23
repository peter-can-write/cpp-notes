# std::bind

std::bind is used to store a function or functor (any callable object)
to _something_. (3) is important.

(1) Bind a function to a parameter

void foo(int x)
 {
     std::cout << x << std::endl;
 }
int main(int argc, char * argv[])
 {
     auto f = std::bind(&foo, 5);
    
     f();

}

or:

void foo(int x)
 {
     std::cout << x << std::endl;
 }
int main(int argc, char * argv[])
 {
     std::mt19937 generator(static_cast<unsigned>(time(0)));
    
     std::normal_distribution<double> distribution;
    
     auto random = std::bind(distribution, generator);
    
     for (std::size_t i = 0; i < 10; ++i)
     {
         std::cout << random() << std::endl;

    }

}

(2) Bind a member function to an object with no parameters:

class Foo
 {
public:
    
     void foo()
     {
         std::cout << "foo" << std::endl;
     }
    
 };
int main(int argc, char * argv[])
 {
     Foo object;
    
     auto bound = std::bind(&Foo::foo, object);
    
     bound();

}

(3) Bind a member function to an object taking parameters:

class Foo
 {
public:
    
     void add(int x, int y)
     {
         std::cout << x + y << std::endl;
     }
    
 };
int main(int argc, char * argv[])
 {
     Foo object;
    
     int x = 5, y = 10;
    
     auto bound = std::bind(&Foo::add, object, std::placeholders::_1,
std::placeholders::_2);
    
     bound(x, y);

}

or (say you want to call a member function with the same arguments but
for different instances)

class Foo
 {
public:
    
     void add(int x, int y)
     {
         std::cout << x + y << std::endl;
     }
    
 };
int main(int argc, char * argv[])
 {
     Foo a, b, c;
    
     int x = 5, y = 10;
    
     auto bound = std::bind(&Foo::add, std::placeholders::_1, x, y);
    
     bound(a);
    
     bound(b);
    
     bound(c);

}

or as lambda:

auto bound = [&] (Foo& foo) { foo.add(x, y); };


