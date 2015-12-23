# item-34:-differentiate-between-inheritance-of-interface-and-inheritance-of-implementation

Know about the three types of function inheritance:

Pure-virtual functions specify that the derived class inherits the
public interface of its base class but no default implementation

Simple virtual functions specify that the derived class inherits the
base class’ interface and a default implementation

Non-virtual function inheritance inherits the base class’ interface and
a MANDATORY implementation (note: use FINAL keyword)

NOTE THAT YOU CAN SPECIFY A DEFAULT IMPLEMENTATION FOR PURE-VIRTUAL
FUNCTIONS! Then the base classes must still define it, but can access
and use the base class implementation by calling base::function() from
inside derived::function()!

Use this feature when you want all derived classes to implement the
pure-virtual function, while some classes may make use of the default
behavior and others not, who then still have to implement it themselves.


