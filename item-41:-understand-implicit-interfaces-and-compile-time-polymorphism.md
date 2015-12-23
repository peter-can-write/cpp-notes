# item-41:-understand-implicit-interfaces-and-compile-time-polymorphism

Non-template object are said to have an explicit interface (an object
has an explicitly set, defined interface from its class) and run-time
polymorphism, because the dynamic binding and polymorphism happens at
run-time. 

Template types (the T in template<class T>) have an implicit interface,
because it is implicitly determined by the functions and operations that
are supposed to be performed on it, and compile-time polymorphism,
because template functions, when called, and template classes are
instantiated for a specific type at compile-time.

// Implicit interfaces

template<class T>

void foo(T t)

{

     t.bar(); // The interface is defined implicitly. A T object’s
interface must have a bar() method.

}

// Compile-time polymorphism

Bar object;

foo(object);

// Compiler created this function at compile-time

void foo(Bar t)

{

     t.bar();

}


