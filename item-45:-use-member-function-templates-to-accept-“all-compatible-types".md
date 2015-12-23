# item-45:-use-member-function-templates-to-accept-“all-compatible-types"

If you create a template class with pointers (e.g. smart pointer),
consider whether you might need to create a GENERALIZED COPY
CONSTRUCTOR and/or GENERALIZED COPY-ASSIGNMENT OPERATOR.  

template<typename T>
class SmartPointer
 {
public:
    
     SmartPointer(T* ptr)
     : ptr_(ptr)
     { }
    

    // Have to neverthelesss implement the normal

    // copy constructor because else the compiler

   // will generate it (it’s still needed), and it

   // might generate it in a way you don’t want.

   // Note that in template

    // classes, the argument of the copy constructor
     // can just be the class without the template
     // As here: SmartPointer and not SmartPointer<T>!
     SmartPointer(const SmartPointer& other)
     : ptr_(other.get())
     { }
    
     // General copy constructor
     // E.g. to convert the base class pointer
     // in this class to a pointer of a derived
     // type. If types don't fit, the compiler
     // will complain when the pointers are
     // assigned, but without this generalized
     // copy constructor, no assignment would
     // be possible in the first place (not even
     // for base/derived pairs, where it should
     // be allowed)
     template<typename U>
     SmartPointer(const SmartPointer<U>& other)
     : ptr_(other.get())
     { }
    
     ~SmartPointer()
     { delete ptr_; }
    
     T* get() const
     { return ptr_; }
    
 private:
    
     T* ptr_;

};
