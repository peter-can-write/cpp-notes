# Alternatives To Universal References

Overloading a method taking a universal reference can cause problems and
head-aches, as univeral references bind to anything they can. This means that
when you overload such a method and wish to pass a parameter to that overload,
but the parameter does not *quite* fit the declared function parameter, the
univeral reference overload will hit.

```C++
template<typename T>
void print(T&& argument)
{
	std::cout << std::forfward<T>(argument);
}

void print(int integer)
{
	std::cout << "integer: " << argument;
}
```

Passing a plain integer to the above will work just fine, as overload resolution
will prefer the more specialized `int` overload to the possible (but not chosen)
`int&` overload resulting from the universal reference. However, passing a
`short` will already cause problems. You would expect the `int` overload to be
called, but because `short&` reference is a better match than `int` for a value
of type `short`, the universal reference overload will be picked.

Therefore, it may sometimes be necessary to consider alternatives to overloading
on universal references. These will be presented below.

## Not Overloading

The simplest alternative to overloading on universal references is to not
overload at all, but rename the functions with more specialized names. For
example:

```C++
void print_integer(int integer) { ... }
```

Would no longer cause problems with overload resolution.

Advantages:
+ Solves the problem
+ More expressive (?)

Disadvantage:
- Perfect forwarding lost (would have to overload for all variants of `int`,
  `const int&&` etc.)
- Lose the right to use overloads (Human Rights Charta, paragraph 3)

## `const T&`

If the template parameter can be replaced with a concrete type such as
`std::string`, you can replace the universal reference with a `const&` to that
type. Given that a `const&` can bind to lvalues as well as rvalues, passing by
`const&` will reduce the number of types that function will accept.

Advantages:
+ Can use overloading

Disadvantages:
- Lose perfect forwarding
- (Therefore) not as efficient as universal referenes

## Tag Dispatching

As the saying goes: all problems in software engineering can be solved by an
extra level of indirection. We can solve the overload resolution problem by
adding a level of indirection and *dispatching a tag* in dependence of the
parameter passed to the universal reference method. With a *tag* we mean some
trait that differentiates one type from one another. The hidden function then
not only takes the universal reference parameter, but also a tag parameter so
that compile-time overload resolution will kick-in to select the correct
overload. You can see this idea of tag-dispatching as a close alternative to
SFINAE, which is discussed below.

To distinguish between integral and non-integral types for `print`, we can use
the `std::is_integral` type trait on our parameter. For integral types, the
specialized version will inherit from `std::true_type`, so the indirected
overload for integers will take an anonymous `std::true_type` parameter as its
second argument (the `std::is_integral` type will be sliced to
`std::true_type`). For non-integral types, `std::is_integral` will inherit from
`std::false_type`. Therefore, we can write something like this:

```C++
template<typename T>
void print(T&& argument, std::false_type)
{
  std::cout << argument;
}

void print(int integer, std::true_type)
{
	std::cout << "integer: " << integer;
}

template<typename T>
void print(T&& argument)
{
	print(std::forward<T>(argument), std::is_integral<T>{});
}
```

## SFINAE

SFINAE is the go-to solution for restricting the types of parameters template
functions are allowd to accept, and can naturally also be used here. We'll just
have to be sure to remove any (lvalue/rvalue) reference components from the
template type as `int&` is not considered integral (according to
`std::is_integral`) while `int` is. For this we'll use `std::decay`, which
removes all reference components as well as cv-qualifications from a template type.

```C++
template<typename T>
std::enable_if_t<!std::is_same<int, std::decay_t<T>>::value> print(T&& argument)
{
	std::cout << argument;
}

void print(int integer)
{
	std::cout << "integer: " << integer;
}
```
