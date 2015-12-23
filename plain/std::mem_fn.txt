# std::mem_fn

A std::mem_fn object is a wrapper around a pointer to a member.

class Foo
 {
public:
    
     void add(int x, int y)
     {
         std::cout << x + y << std::endl;
     }
    
     int var = 7;
    
 };
int main(int argc, char * argv[])
 {
     Foo a;
    
     auto fn = std::mem_fn(&Foo::add);
    
     fn(a, 10, 2);
    
     auto m = std::mem_fn(&Foo::var);
    
     std::cout << m(a) << std::endl; // 7

}

Equivalent with std::function and std::bind:

int main(int argc, char * argv[])
 {
     using namespace std::placeholders;
    
     Foo a;
    
     // Or just 'auto'
     std::function<void(Foo&, int, int)> fn = std::bind(&Foo::add, _1,
_2, _3);
    
     fn(a, 10, 2);
    
    
     // Or just auto
     std::function<int(Foo&)> m = std::bind(&Foo::var, _1);
    
     std::cout << m(a) << std::endl; // 7

}

Equivalent with lambdas:

int main(int argc, char * argv[])

{  

    Foo a;
    
     auto fn = [&] (Foo& f, int x, int y) { f.add(x, y); };
    
     fn(a, 10, 2);
    
     auto m = [&] (Foo& f) { return f.var; };
    
     std::cout << m(a) << std::endl; // 7

}


