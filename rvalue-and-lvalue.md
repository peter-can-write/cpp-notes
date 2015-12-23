# rvalue-and-lvalue

An lvalue can be referred to, it has an identity / a name.

An rvalue cannot be referred to and has no identity. One form of rvalue
is a temporary object, e.g. non-reference object return value of a
function, or intermediary objects during longer expression ( x * (y * z)
-> y*z = temporary), which are deleted as soon as the expression in
which it is used is fully evaluated. Also literals (5, ‘A’, false) are
rvalues.

An rvalue may be assigned to an lvalue.

Example:

T& fooReference();
T fooNotReference();
int main(int argc, char * argv [])
 {
     T t;
    
     // x = lvalue, 5 = rvalue
     // other rvalues: 'c', true etc.
     // Constructors return rvalues!
     T x = t;
    
     // fooReference() = lvalue because
     // it returns a reference
     x = fooReference();
    
     // fooNotReference() = rvalue because
     // it returns a non-reference
     x = fooNotReference();
    
     // rvalues can temporarily act as lvalues
     fooNotReference() = t;
    
     fooNotReference().someMemberFunction();
    
     // Because fooReference() returns a reference
     // to an object which has an identity and can
     // be referred to, r can be a new reference to
     // this object
     T& r = fooReference();
    
     // Error! Because fooNotReference() returns a
     // non-reference temporary rvalue, this object
     // has no identity and can not be referred to
     // by a non-const reference
     T& nr = fooNotReference();
    
     // But const references work with rvalues
     // (this is simply a C++ rule ...)
     const T& cnr = fooNotReference();
    
     // Function names are rvalues
     T& (*ptr)() = fooReference;
    
     T arr [4];
    
     // Error! Array names are rvalues
     // (return a pointer to the first
     // element of the array), but
     // ra is a non-const reference and
     // can thus only bind to lvalues
     T& ra = arr;
    
     // In C++11 you can use rvalue
     // references to bind temporary
     // objects and other rvalues
     // Note the && instead of &
     T&& rvr = fooNotReference();
    
     // These temporary variables can
     // then be used further
     rvr.doSomething();

}


