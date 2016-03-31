# Universal References

C++11 introduced the concept of *rvalue references*, denoted by two ampersands:
`&&`. In some cases, they really are what they're called: references to
rvalues. I.e., the only bind to objects whose value category is *rvalue*,
therefore including moveable *xvalues* with identity, resulting from calls to
`std::move`, as well as standard moveable and identity-less *prvalues*:

```C++
void f(int& lv) { std::cout << lv; }

void f(int&& rv) { std::cout << rv; }
```

However, rvalue references don't always only bind to rvalues. Under certain
conditions, rvalue references become more powerful *universal references*,
which can bind to all parameters of all value categories. These conditions are:

1. The form of the type is `T&&`, where `T` is a template parameter.
2. Type deduction takes place.
3. Reference collapsing occurs.

## `T&&` (1)

This first point refers to the form of the parameter for it to be considered a
universal reference. The rules are fairly strict, in the sense that the form
must be precisely `T&&`, where `T` is some template parameter. Most importantly
this means that *no cv-qualifications may be present*. Neither `const T&&` nor
`volatile T&&` denote universal references, but simply *rvalue references* to
`const`/`volatile` objects of type `T`.

## Type Deduction Takes Place (2)

This point states that in `T&&`, `T` must be a template parameter in a deduced
context. That is why `int&&` cannot be a universal reference -- no type
deduction takes place in determining the type `int`. Most interestingly, this
means that when `T&&` is not in the immediate context of the signature of a
function or the type of a class, but inside the body of that function or class
such that `T` refers to an at that point *already deduced type*, meaning that no
more type deduction takes place for `T&&`, `T&&` will denote an *rvalue*
reference to the type deduced for `T`:

```C++
template<typename T>
struct Foo
{
	using R = T&&; // R is an rvalue reference!

	// T&& is an rvalue reference to the type deduced for T, not a universal reference!
	void foo(T&& argument);
};

template<typename T>
void f(T&& argument) // type deduction takes place here
{
	// No type deduction any more, T&& is an
	// rvalue reference to the type deduced for T
	T&& ref = argument;
}
```

## Reference Collapsing Occurs (3)

Normally, C++ disallows references to references:

```C++
int x = 5;
int& y = x;   // ok
int& & z = y; // not ok
```

However, the compiler is allowed to *temporarily* construct references to
references in certain circumstances, such as template
instantiation. *Temporarily* here refers to the fact that such double references
are simplified in a process referred to as *reference collapsing*. Reference
collapsing is the process by which the compiler will reduce references to
references of same or differing value category to a single reference. The rules
for reference collapsing are simple:

> If either reference is an lvalue reference, the result is an lvalue
> reference. Otherwise (i.e. if both are rvalue references), the result is an
> rvalue reference.

Basically, lvalues always dominate over rvalues. CV-qualifications do not matter
(they just stay).

In a table:

| first | second | result |
|:------|:-------|:-------|
|  `&`  |   `&`  |   `&`  |
|  `&&` |   `&`  |   `&`  |
|  `&`  |   `&&` |   `&`  |
|  `&&` |   `&&` |   `&&` |

Reference collapsing occurs in four distinct situations:

1. Template type deduction
2. Auto type deduction
3. Alias or typedef declarations
4. For arguments to `decltype`

## The Deduction Process

So, given, a function template `f` taking a universal reference parameter:

```C++
template<typename T>
void f(T&& t) { /* ... */ }
```

What type will be deduced for `T` and for the parameter type when we throw
things at `f`? The answer is such:

> If the parameter passed to `f` is an lvalue (reference *or not*), `T` is
> deduced to be an lvalue reference. If the
> parameter is an rvalue (reference *or not*), `T` is deduced to be a
> __non-reference__ type.

This results in the following deductions:

```C++
int  x = 123;
int& y = x;
const int& z = x;
int&& r = 5;

f(x); // T => int&; parameter type: int& && => int&

f(y); // T => int&; parameter type: int& && => int&

f(z); // T => const int&; parameter type: const int& && => const int&

f(r); // T => int; parameter type: int&& => int&& (!)
```

As you can see, `T` is deduced as a non-reference for rvalues (`r`), but `T &&`
naturally collapses to `T&&`, leaving an *rvalue reference*.

## `std::forward`

This can lead us to understand how `std::forward` works:

```C++
template<typename T>
T&& forward(std::remove_reference_t<T>& argument)
{
	return static_cast<T&&>(argument);
}
```

in tandem with a function that makes use of perfect forwarding:

```C++
template<typename T>
void foo(T&& argument)
{
	bar(forward<T>(argument));
}
```

When we pass an lvalue to `foo`, say of type `int`, `T` is deduced as an lvalue
reference to `int`, i.e. `int&`. Therefore, the following instantiation of
forward is produced:

```C++
int& && forward(std::remove_reference_t<int&>& argument)
{
	return static_cast<int& &&>(argument);
}
```

which collapses to:

```C++
int& forward(std::remove_reference_t<int&>& argument)
{
	return static_cast<int&>(argument);
}
```

and then resolves to:

```C++
int& forward(int& argument)
{
	return static_cast<int&>(argument);
}
```

such that the lvalue reference is (without effect) cast to an lvalue reference
and passed on to `bar` as such.

When, however, we pass an rvalue of type `int` to `foo`, e.g. an integer literal
`5`, `T` will be deduced to non-reference type `int`. Thus the following
template is instantiated:

```C++
int&& forward(std::remove_reference_t<int>& argument)
{
	return static_cast<int>(argument);
}
```

Which resolves to:

```C++
int&& forward(int& argument)
{
	return static_cast<int>(argument);
}
```

Now, because we passed an rvalue to __`foo`__, the type of that parameter will
be `int&&`, while `T` was deduced to `int`. Because inside the function that
parameter has an identity (and its memory address can be taken) it is not,
however an rvalue anymore (inside `foo`). Thus it will bind to the lvalue
parameter of `forward` (note there exists also an rvalue overload of
`std::forward`, it's just not as relevant). Because `int` was passed to
`forward` as `T`, the return value and "cast-target" (inside the `static_cast`)
for the parameter inside `forward` will be of rvalue type. As such, `forward`
transforms the lvalue back into an rvalue.

This is how universal references and reference collapsing all tie together.
