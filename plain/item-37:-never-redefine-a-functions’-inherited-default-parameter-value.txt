# item-37:-never-redefine-a-functions’-inherited-default-parameter-value

// base class

virtual void foo(int x = 4);

here, the VIRTUAL will make a derived version of this function override
it if called from a base class pointer. This is called DYNAMIC or LATE
BINDING. However, function parameters are bound STATICALLY or EARLY, so
it will not be overriden when called from a base class pointer.

// derived class

virtual void foo(int x = 123) override;

basePointerToDerived->foo(); // calls derived foo, but with
default parameter  X = 4


