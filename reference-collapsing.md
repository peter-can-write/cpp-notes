# Reference Collapsing

With the introduction of rvalue references in C++11, there now exist scenarios
where the compiler may deduce *references to references* (either rvalue or
lvalue references, respectively):

~~~C++
template<typename T>
void f(T&& t)
{
	std::cout << t;
}
~~~

Imagine now that this function is called with an lvalue reference object:

~~~C++
int x = 5;

f<int&>(x);
~~~

In this case, `T` is (explicitly) deduced as `int&`, meaning `f`'s parameter
would be of type `int& &&`. However, the C++ language forbids references to
references (e.g. `int& & y = x` is ill-formed). Also, what exactly should an
rvalue reference to an lvalue reference signify? Very little. So, there exists a
phenomenon called *reference collapsing*, by which the compiler will reduce
references to references to simpler expressions under certain
circumstances.

Reference collapsing is closely coupled with universal references (rvalue
references in template contexts), which have the following semantics:
