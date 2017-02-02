# Prefer composition over inheritance

Item 34 reiterates and discusses the Gang of Four's advice to prefer composition
over inheritance. They advocate that, in general, if there is more than one way
to express a relationship, the weakest relationship that is practical should be
used. Since inheritance is the second-tightest coupling possible, after
friendship, inheritance should be used only sparingly and with caution.

Composition has quite a few benefits. These include:

* *Greater flexibility of implementation without affecting interface*.
  When using composition, you can switch your class' implementation from holding
  a member by value, to holding a pointer to it or even using the pimpl idiom.
  Any of these changes require modifications only to the class' internals, but
  not to its interface.
* *Composition via pointers removes the necessity of including a header*, which
  reduces compile times.
* *Wider applicability*, as not all classes were designed to be base classes.

The main and most important reason to use inheritance is *substitutability*. That is, when you are faced with a design where you want a pointer or reference of a certain type to be replaceable by a member of a group of concrete classes through an *is-a* relationship, inheritance is your best option. Another reason, next to substitutability, is when you want or must make use of polymorphism, i.e. the ability to override methods.

If you find that inheritance is not necessary, you can use non-member functions to add *behavior* to your classes and use further composition to add *state*.
