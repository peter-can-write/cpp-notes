# item-48:-template-meta-programming-(tmp)

Template Meta Programming (TMP) is a programming methodology that makes
it possible to shift program logic from runtime to compile time, using
solely types and templates. This can be a lot more memory efficient, can
have better performance and allows for earlier error detection based on
types.

“Template metaprogramming can shift work from runtime to compile-time,
thus enabling earlier error detection and higher runtime performance."

Factorial using Template Meta Programming:

template<std::size_t n>
struct Factorial
 {
     enum { value = n * Factorial<n-1>::value };
template<>
struct Factorial<0>
 {
     enum { value = 1 };

};

Output:

>>>  std::cout << Factorial<4>::value;

>>> 24

Loop using Template Meta Programming:

template<std::size_t i, std::size_t n>
class Loop
 {
public:
    
     static void notaloophehe()
     {
         std::cout << i << std::endl;
        

        Print<i+1, n>::notaloophehe();

    }

};
template<std::size_t i>
class Loop<i, i>
 {
    
 public:
    
     static void notaloophehe()
     {
         std::cout << i << std::endl;
     }

};
