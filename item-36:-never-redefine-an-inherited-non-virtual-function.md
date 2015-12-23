# item-36:-never-redefine-an-inherited-non-virtual-function

You should never redefine a non-virtual function, only virtual function.
A non-virtual function means you intend to have an INVARIANT OVER
SPECIALIZATION (shouldn’t be re-implemented —> use FINAL). Otherwise
make all inherited re-defined functions virtual, because it should have
the same behavior no matter whether you call the derived function from a
pointer to base class (would call base version if non-virtual) or from a
pointer to derived class (would call derived version). Otherwise it is
not IS-A inheritance. 


