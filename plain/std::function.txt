# std::function

std::function is a wrapper around any callable object and can be used to
store lambda expressions, calls to std::bind or any other functions or
callable objects (functors). Useful for callback functions! (See bottom)
Should also use instead of template predicates because

1.  Expresses intent better / clearer
2.  No longer need to define the function inside the header!

// E.g. std::function<void(int, int)>

std::function<return-type(arguments)> foo;

Check if an std::function currently points to a function (or is empty):

if (foo) … // implicit conversion to bool

In all of these examples, the functions can be referred to without the
&, because it’s implicit. But for clarity’s sake.

Use cases:

(1) Can be used to store any function with that signature:

void foo(int x)
 {
     std::cout << x << std::endl;
 }
int main(int argc, char * argv[])
 {
     std::function<void(int)> function = &foo;
    
     function(5); // 5

}

(2) Can be used to store a lambda with that signature:

std::function<int(int)> factorial = [&] (int x)
 {
     return x ? x * factorial(x - 1) : 1;

};

As you can see, this way the lambda can also be referred to internally
(e.g. for recursive functions like here)!

(3) Can be used to store calls to std::bind for free functions (so you
no longer need to pass the same argument repeatedly):

void foo(int x)
 {
     std::cout << x << std::endl;
 }
int main(int argc, char * argv[])
 {
     std::function<void()> function = std::bind(&foo, 5);
     function();

}

(4) Can be used to store calls to std::bind for member functions, for
different objects:

class Foo
 {
public:
    
     Foo(int x)
     : x_(x)
     { }
    
     void foo() const
     {
         std::cout << x_ << std::endl;
     }
    
 private:
    
     int x_;
 };
int main(int argc, char * argv[])
 {
     Foo a(2), b(7);
    
     std::function<void(const Foo&)> function = &Foo::foo;

    function(a);

}

(5) Can be used to store calls to std::bind for member functions, for
one object:

class Foo
 {
public:
    
     Foo(int x)
     : x_(x)
     { }
    
     void foo(int x) const
     {
         std::cout << x + x_ << std::endl;
     }
    
 private:
    
     int x_;
 };
int main(int argc, char * argv[])
 {
     Foo a(2);
    
     std::vector<int> vec = {1, 2, 3};
    
     std::function<void(int x)> function = std::bind(&Foo::foo, a,
std::placeholders::_1);
     std::for_each(vec.begin(), vec.end(), function);

}

Could be replaced with a lambda of course:

int main(int argc, char * argv[])
 {
     Foo a(2);
    
     std::vector<int> vec = {1, 2, 3};
     std::for_each(vec.begin(), vec.end(), [&] (int x) { a.foo(x); });

}

(6) Can be used to store and accept callback functions in an appropriate
class:

class Foo
 {
public:
    
     void setCallback(const std::function<void()>& callback)
     {
         callback_ = callback;
     }
    
     void operator()()
     {
         for (std::size_t i = 0; i < 10; ++i)
         {
             if (i == 5) callback_();
         }
     }
    
 private:
    
     std::function<void()> callback_;
    
 };
class Bar
 {
public:
    
     void bar()
     {
         std::cout << "The number was 5!" << std::endl;
     }
 };
int main(int argc, char * argv[])
 {
     Foo foo;
    
     Bar bar;
    
     auto callback = std::bind(&Bar::bar, bar);
    
     foo.setCallback(callback);
    
     foo();

}
