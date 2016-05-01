# Perfect-Forwarding Failure Cases

Item 30 in Scott Meyers' *Effective Modern C++* speaks to failure cases of C++11
perfect forwarding. Here, we define a *failure case* as any time we wish to
forward arguments passed to `f` to another (wrapped) function `g`, and:

1. The call to `f` fails at compile-time (usually for type-deduction reasons)
2. The call to `f` produces a different result than the direct call to the
   underlying function `g`.

For this, let us define the function `f` as:

```C++
template<typename... Args>
void f(Args&&... args) {
	g(std::forward<Args>(args)...);
}
```

The most important failure cases are then the following:

1. Braced-initializer lists,
2. `0` or `NULL` as pointers,
3. Static const integral values,
4. Overloaded functions,
5. Bitfields.

## Braced-Initializer Lists

Braced-initializers lists fail to be forwarded perfectly for the first reason
(the call to `f` does not succeed). The reason why is that the C++ standard
specifies that braced-init lists are a *non-deduced context*, meaning they may
not be deduced to `std::initializer_list` (for reasons why, see [here]
(http://stackoverflow.com/questions/17582667/why-do-auto-and-template-type-deduction-differ-for-braced-initializers))
and the call `f({1, 2, 3, 4, 5});` will thus fail at compile-time. At the same
time, an equivalent call to the underlying function `g` does compile if `g`
takes a parameter where conversion from braced-init lists is allowed
(e.g. `std::vector`).

The obvious workaround for this is to pre-declare the braced-init list as a
`std::initializer_list`:

```C++
std::initializer_list<int> list = {1, 2, 3, 4, 5};
f(list);
```

Currently (!) in C++14, declaring `list` as `auto` also deduces a
`std::initializer_list`. However, this may be subject to change in future
standards.

## `0` or `NULL` as Pointers

Another failure case is the attempt to pass `0` or `NULL` as a null-pointer
(since `NULL` is a macro for `0`, everything described henceforth for `0` shall
also be true for `NULL`). Because `f` is a template function and `0` is really
an integer, passing `0` will deduce an integral type. While you can assign `0`
to a pointer to mean a null-pointer, that does not mean that you can assign an
integer whose value is `0` to a pointer:

```C++
int null = 0;
int* pointer = 0; // OK
int* pointer = null; // Not OK
```

This is because, simply, `int` and `int*` are two different, unrelated types.

Thus, if `g` has the signature `void g(int* x)` and we call `f(0)`, the call,
and thus the perfect-forwarding, will fail.

## Static Const Integral Values

When you define a static member in a class in C++, in all cases, you must define
it ouside of the class definition. However, there exists one exception to this
rule: when the data member is `const` and integral or `constexpr`. In that case,
you may already provide an initializer alongside the declaration:

```C++
struct Foo {
	// Note that this is still only a declaration
	static const int x = 5;
};
```

The compiler will then simply replace any occurrence of `Foo:x:` with the
literal value that was supplied as an initializer (here: `5`). However, this is
fine only if we do not require the address of the static value. If we do try to
access the address of the static variable, that would require the variable to
have storage. Because we only declared the variable (along with providing an
initializer), taking its address will result in a *linker-error*. As such,
passing the static member to our forwarding function `f`, which always deduces
an (rvalue or lvalue) reference type, will fail. At the same time, the
equivalent call to `g` does compile and link if `g` only takes the member by
value:

```C++
void g(int x) {
	/* ... */
}

g(Foo::x); // OK
f(Foo::x); // Linker-error!
```

The workaround is to define the static member out-of-line.

## Overloaded Functions

Say we have two function overloads for `h`:

```C++
void h();
void h(int);
```

If we have `g` taking a function pointer `void (*fp)(int)` or function signature
`void (fp)(int)` (same thing) and we pass `h` to `g`, the compiler is smart
enough to pick the right overload (choosing the overload that takes
integer). However, as `f` is a template function, it will not know which
overload we mean when we reference the *symbol* `h`. As such, the call will fail
for reasons of ambiguity:

```C++
f(h); // Which one? "No matching function call"
```

We can overcome this by casting `h` to the function pointer we want:

```C++
f(static_cast<void(*)(int)>(h)); // OK
```

Note that you cannot cast to a signature:

```C++
f(static_cast<void(int)>(h)); // Error
```

## Bitfields

Bitfield members can be taken by value or by `const&`. Therefore, calling `f`
with a bitfield member will fail (as a non-`const` lvalue reference would be
deduced):

```C++
struct Bitfield {
	std::uint8_t a: 3, b: 4, c: 1;
};

Bitfield bitfield;

f(bitfield.a); // Error! "Non-const reference cannot bind to bit-field 'a'"
```

The solution is to copy the member into a variable beforehand:

```C++
std::uint8_t a = bitfield.a;
f(a); // OK
```
