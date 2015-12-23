# rvalue-references,-collapsing-rules-and-perfect-forwarding

http://thbecker.net/articles/rvalue_references/section_08.html

In pre-C++11, rvalues cannot be bound to non-const references:

// Error! Because fooNotReference() returns a
// non-reference temporary rvalue, this object
// has no identity and can not be referred to
// by a non-const reference

T& nr = fooNotReference();

However, in C++11 there now exist RVALUE REFERENCES, denoted by &&
instead of the & for lvalue (normal) references:

// In C++11 you can use rvalue
// references to bind temporary
// objects and other rvalues

// Note the && instead of &

// With a normal / lvalue

// reference this would fail

T&& r = fooNotReference();

These rvalue references ONLY bind to rvalues / temporary variables (e.g.
if T = int and fooNotReference() returns int) which can then be used or
manipulated further. Rvalue references do not bind to lvalues. A useful
feature is that specific combinations of references collapse to simpler
reference types when cast (implicitly or using static_cast), as shown
here:

TR  R (in parameter list)

(^passed)

T&   &  -> T&  // lvalue reference to cv TR -> lvalue reference to T T&   && -> T&  // rvalue reference to cv TR -> TR (lvalue reference to T) T&&  &  -> T&  // lvalue reference to cv TR -> lvalue reference to T T&&  && -> T&& // rvalue reference to cv TR -> TR (rvalue reference to T)

Note that these are _TEMPLATE_ deduction rules_._

_
_

The last two rows are interesting. By taking an argument as an rvalue
reference, we can make use of collapsing rules to get lvalue references
or rvalue references depending on the situation.

Important special _TEMPLATE_ deduction rules: Given a function
template<typename T> bar(T&& var)

-   When bar is called on an lvalue of type X, T resolves to X&, i.e.
    the special template deduction rules turn X into an X
    reference (X&). var is then X& && which collapses to 
-   When bar is called on an rvalue of type X, T resolves to X (not
    reference; the rvalue itself) and var is then X &&. 

void foo(int&& t)
 {
     std::cout << "rvalue supplied: " << t << std::endl;
 }
void foo(int& t)
 {
     std::cout << "lvalue supplied: " << t << std::endl;
 }

template<typename T>
void callFoo(T&& t)

{

     // Rvalue reference in callFoo because

    // rvalues cannot be passed to lvalue

    // references (T&), unless const, but

    // const may not be wanted in the constructor

    // and one would thus require overloads for

    // const and non-const

    // so all that remains is T&&  

    // Depending on whether the object

     // passed was an lvalue or an rvalue

     // reference, it collapsed to a different

     // reference type (again, either rvalue

     // or lvalue reference). So first, the

    // collapsing rules apply here.

   
     // However, even if the parameter passed
     // was an rvalue, it is now (in this
     // function) an lvalue, because it can
     // be referred to by the lvalue 't'
     // and thus has a name
    
     // Now say we wanted to re-determine
     // whether the parameter passed was
     // an rvalue or an lvalue reference,
     // giving us the possibility to call
     // the right overload of foo()
    
     // Rememeber that we cannot call
     // foo(t) directly, because in here
     // t is an lvalue, so it would always
     // call the lvalue reference overload of
     // foo ( foo(T& t))
    
     // foo(t); -> Wrong! Will always call foo(T& t)

   

    // The only way to distinguish between

    // whether an rvalue or lvalue reference
     // was passed to callFoo() is by turning
     // the parameter back into either an lvalue
     // or an rvalue reference:
    
     // foo(static_cast<T&&>(t));
    
     // We do this by simply casting the now
     // lvalue t back to T&&
     // If the t passed to callFoo() was a
     // normal lvalue reference (T&) in the
     // first place, this cast does nothing:
     // T& && -> T&
     // However, if t was an rvalue before,
     // it is cast back to an rvalue:
     // T&& && -> T&&
    
     // A nicer way of calling static_cast
     // is by calling std::forward, which
     // does exactly this cast (but looks nicer)
    
     foo(std::forward<T>(t)); // = static_cast<T&&>(t);
 }
int main(int argc, char * argv [])
 {
     int x = 666;
    
     callFoo(5); // rvalue supplied: 5
    
     callFoo(x); // lvalue supplied: 666

}
