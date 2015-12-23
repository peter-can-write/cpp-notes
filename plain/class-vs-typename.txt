# class-vs-typename

for template parameters, class and typename are more or less equivalent:

template<typename T> void foo(){} <==> template<class T> void foo(){}

However, for _template template parameters_ you must use class:

template<typename T, typename Alloc, template<typename,typename> class
Container>

void foo(Container<T, Alloc>) {}

Using class hints that the type will be of user-defined class type on
which certain operations can be performed, i.e. for which you can call
methods. So use class where you expect a user-defined class-type and use
typename where also primitive types such as int or char are allowed, or
for functions (predicates).
