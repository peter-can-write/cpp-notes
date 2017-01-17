# My Style Guide

## Prefer Anonymous Namespaces Over Private Static Functions

In C++, anonymous namespaces effectively give its members internal linkage, as if they had been declared with the `static` keyword. More precisely, the compiler will give the namespace a unique name that is visible only to the current translation unit. This is useful for helper functions. However, we ask: why a helper function in an anonymous namespace as opposed to (1) a private method or (2) a private static function? Generally, the rule should be to prefer the anonymous namespace over (2) and (2) over (1). Prefer

1. (2) over (1) because a private static function is not part of the instance.
2. Anonymous namespaces over (2) because (2) could still access private members, i.e. it still has some relation to the class, while a function in an anonymous namespace is fully decoupled.

For both the first and second case, it is assumed that you are not passing an instance of the class to the function. That wouldn't make sense. So since you are not passing an instance to the helper function, you are not making use of the static private functions' private access to the class anyway. As such, there's not need to use static private functions anyway.

Decision:

1. If you need to access an instance variable, use a private method.
2. If you need to access a private static member, use a private static method.
3. If neither (1) nor (2) apply, put the function in an anonymous namespace.

Further, to allow for documentation, the anonymous namespace shall be placed in
its own header (called something like `<class>-helpers.hpp`).

Note that this also has the benefit of not needing to recompile dependent classes when changing such a helper method's prototype (which you would, if you had it as a private (static) function).

Resources:
* http://stackoverflow.com/questions/357404/why-are-unnamed-namespaces-used-and-what-are-their-benefits
* http://stackoverflow.com/questions/154469/unnamed-anonymous-namespaces-vs-static-functions
*  http://www.comeaucomputing.com/techtalk/#nostatic
