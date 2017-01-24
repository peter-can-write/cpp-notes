# Be clear what kind of class you're writing.

Just as there are design patterns for object-oriented programming, so there are patterns in the classes we usually write. Item 32 advises to know of these usual kinds of classes, and describes the following taxonomy:

## Value Classes

*Value classes* the most common tool in our class-toolshed. They are the basic, no-magic workhorse and identified by the following characteristics:

* They have a public destructor, copy constructor and assignment operator,
* They have no virtual functions,
* They are intended to be used as concrete classes and not as base classes,
* They are usually instantiated on the stack or held in another class.

## Base Classes

Base classes are any classes that are used as "internal nodes" in our inheritance hierarchy, i.e. any class that one or more other classes derive from. They share the following properties:

* They have a virtual destructor,
* They establish an interface through virtual functions,
* They are usually instantiated on the heap.

We could split this up into two further variants of this type of class:

1. An *abstract class* is intended to be used in the context of polymorphism, i.e. pointers of their type will point to base classes,
2. A *base class* is a class that refactors common functionality of its subclasses to avoid code duplication, but is not intended to be used for polymorphism.

Note that both should be implemented in the usual ways (with virtual methods). The distinction is more in nomenclature and use.

## Trait Classes

A trait class is a template class that carries information about types. It is useful in the context of template metaprogramming. Such classes usually:

* Contain only `typedef`s (aliases) and static functions, but do not carry state,
* Are never instantiated (this can be enforced by `delete`ing constructors).

## Exception Classes

Exception classes are interesting in that they usually have both *value and reference* semantics: they are thrown by value but caught by reference. An exception class:

* Has a public destructor and noexcept (copy) constructors. Otherwise you risk an abortion.
* May have virtual functions.
* Should derive from `std::exception` (or a subclass thereof).
