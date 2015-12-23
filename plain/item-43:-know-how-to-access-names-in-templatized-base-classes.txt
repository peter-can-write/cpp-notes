# item-43:-know-how-to-access-names-in-templatized-base-classes

Inheritance doesn’t work out-of-the-box with template classes, because
the compiler can never know the full definition of a template base
class, as all template classes can be specialized (and thus certain
inherited members may be defined in the general template but not in the
specialization):

template<typename T>
class Base
 {
public:
    
     void foo()
     {}
 };

template<> // specialization

class Base<int>
 {
public:
    
     void bar()
     { }
 };

template<typename T>

class DerivedFaulty : public Base<T>

{
public:
    
     void callFoo()

    {

        foo(); // Error, foo undefined (compiler cannot know if Base has
foo until instantiation)

    }

};

template<typename T>
class DerivedWorks : public Base<T>
 {
public:
    
     using Base<T>::foo; // Promise compiler that foo will exist
    
     void callFoo()
     {
         foo(); // now works
     }

};

This also means that DerivedWorks<int> will cause a compile-time error
because Base<int>::foo does not exist.


