# When to use `std::move` and `std::forward`

There are three scenarios where the use of `std::move` and `std::forward` should
be discussed, and where it may be the right or the wrong choice:

1. To forward arguments inside functions.
2. To forward arguments in the return value of functions returning by value.
3. To forward local variables upon returning them.

## Inside Functions

This scenario represents the case where you accept an argument by rvalue or
universal reference in a function, and then use that value inside the
functino. This is the most obvious scenario where `std::move` and `std::forward`
can and *should* be used. However, you must take care *when* to actually call
these functions.

The first distinction is when to call either of the two functions. The simple
and obvious rule is that if you accept an rvalue reference and wish to use it
inside the function, and retain its value category, you should use
`std::move`:

```C++
// Accepting rvalue references
void f(Widget&& widget) {
	// Must forward them as rvalue references
	g(std::move(widget));
}
```

When the argument is a universal reference (i.e. an rvalue
reference of form `T&&` where type deduction takes place for`T`), you should use
`std::forward`:

```C++
// Accepting universal references
template<typename T>
void f(T&& value) {
	// Should use std::forward here
	g(std::forward<T>(value));
}
```

Theoretically, you could also have used `std::forward` in the first case, if you
pass the parameter's type with any references or with rvalue reference type to
`std::forward`:

```C++
void f(Widget&& widget) {
	// Works, but don't do it (not expressive)
	g(std::forward<Widget>(widget));
	// Or
	g(std::forward<Widget&&>(widget));
}
```

## When returning by value

Naturally, you should also employ `std::move` and `std::forward` to forward
rvalue and universal references when returning from functions by value:

```C++
template<typename T>
T f(T&& value) {
	// Will incur copy
	return value;
	// Will only conditionally incur copy
	return std::forward<T>(value);
}
```

The more important thing to take away from this section is *when* to forward the
arguments. Especially when accepting universal references, one may often be
inclined to just forward them wherever they are used. However, this can have
nasty side effects when the universal reference is bound to an rvalue reference,
and a premature forward causes the object to be modified before its final use:

```C++
template<typename T>
void f(T&& value) {
	// Its a universal reference, so just forward it, right?
	g(std::forward<T>(value));

	return std::forward<T>(value);
}
```

Consider in the above code the situation that you pass an rvalue to `f`, such as
by calling `f(std::string("foo"))`. Because of premature forwarding, the
`std::string` will be forwarded as an rvalue to `g`. Maybe, `g` has an overload
accepting rvalues specifically to move the contents of its argument to some
other place. In that case, after the call to `g` inside `f`, `value` would have
undefined value. In the worst case, the return statement would actually be
returning an empty string! The solution and the proactive insight one should
take away from this is thus to only forward arguments __the last time they are
used__:

```C++
template<typename T>
void f(T&& value) {
	// Don't want it to be modified
	// Still is taken by reference, so not necessarily copied
	g(value);

	return std::forward<T>(value);
}
```

## When returning local variables

Consider that you have a function with a local variable `var`, which you return
by value:

```C++
Class f() {
	Class var;
	/* ... */
	return var;
}
```

Now imagine a call to f: `auto result = f();`. What construction and destruction
takes place here? Naively, you may think the following:

1. `var` is constructed in `f`.
2. Memory for the return value of `f` is allocated.
3. `var` is copied into the memory of the return value.
5. The return value is copied into the `result` object.


You may think you can speed this up by employing C++11 move semantics, and
`std::move` `var` into the return value inside `f`:

```C++
Class f() {
	Class var;
	/* ... */
	// Such smart, much wow
	return std::move(var);
}
```

And thus reduce the sequence to:

1. Construct `var` in `f`.
2. Allocate memory for the return value.
3. *Move* `var` into the return value.
4. Copy the return value into `result`.

This understanding would be correct, if it weren't for the *return value
optimization*
([RVO](http://stackoverflow.com/questions/12953127/what-are-copy-elision-and-return-value-optimization)). The
return value optimization is an optimzation technique encouraged but not
mandated by the standard, that many compilers employ. Basically, it allows the
compiler to *elide* (omit) the copy or move of an unnamed (temporary) object
(regular RVO), or named object (Named RVO; NRVO) when certain criteria are
met. For our example, the RVO states that if:

1. the type of the local variable is identical to that of the return value, and
2. it is precisely the local variable that is returned (not the result of
   passing it to some function)

then the compiler may construct the local variable in the memory allocated for
the return value. From then on, source and target (i.e. local variable and
return value) would refer to one and the same memory location and destruction of
the object takes place when the later destructor of the two objects would have
been normally called. This copy elision may take place even if the constructor
has side-effects, which is why you should not rely on logic inside constructors
that depend on it being called a certain number of times. What this means for us
is that `var` will be constructed directly in the memory of the return
value. Moreover, it could be that the compiler also elides the copy of the
return value into the target variable `result`. So either of these two outcomes
are possible:

1. `var` is constructed in the memory for the return value.
2. The return value is copied into `result`.

or

1. `var` is constructed in the memory for `result`.

This is entirely implementation-dependent. So why do we care? Because in our
"smart" second version, we are ruining the compiler's chances of RVO. The reason
why is that `std::move(var)` is not the local variable itself, but the result of
calling `std::move` on it. This violates requirement (2) for copy
elision. Therefore, we would incur the cost of creating an extra variable `var`
and its move into the return value.

Lastly, another reason why *not* to use `std::move` here is that the *compiler
does it anyway*. The standard states that if the compiler *could* use
copy-elision, but does not, it must treat the local variable as if it were an
rvalue reference. Meaning, even if `var` is not constructed in the memory of
the return value, it will be moved implicitly.
