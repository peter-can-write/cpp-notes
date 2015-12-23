# item-15:-provide-access-to-raw-resources-in-resource-managing-classes

Whenever you design a RAII class to manage any heap-allocated resources,
make sure you grant the client access to the raw data in some way. A
RAII class doesn’t necessarily need to encapsulate its data, its real
purpose is managing the allocation and destruction of a resource.
Provide access either through:

-   EXPLICIT CONVERSION: Provide a method for retrieving the data,
    like getData() or just get().
-   IMPLICIT CONVERSION: Implicit conversion would be either overloading
    the dereferencing operators (* and ->) or by operating some
    conversion operator (e.g. operator double())

Note that explicit conversion is safer, but implicit conversion may be
more convenient and easier. 


