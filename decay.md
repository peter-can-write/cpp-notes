# std::decay

`std::decay` is a C++11 type trait that removes all cv-qualifications as well as
refernce components from a template type. I.e. it strips template type `T` of
all non-type components. Moreover, it decays arrays and functions to
pointers. This conversion is precisely the decay that *all* function parameters
passed by value to a function undergo. It is especially useful to compare types,
since `int&` and `int` and `const int` are not the same, for example.

```C++
template<typename T>
std::enable_if_t<std::is_same<int, std::decay_t<T>> f(T&& argument)
{
	/* ... */
}
```
