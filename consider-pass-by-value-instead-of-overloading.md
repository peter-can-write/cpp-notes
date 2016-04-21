# Consider Pass-by-Value Instead of Overloading

Given C++11 move semantics, it is often a good idea to allow for moving objects
into your types. For example, we could have a class `Person` with a name, and
corresponding access function to that name. Because we are very smart and value
performance, we add overloads for lvalue and rvalue strings to set the person's
name:

```C++
class Person {
public:
	/* ... */

	void name(const std::string& name) {
		_name = name;
	}

	void name(std::string&& name) {
		_name = std::move(name);
	}
private:
	std::string _name;
};
```

However, one disadvantage of this ist that we now have to maintain *two*
functions instead of one. This also bloats our object code. An alternative would
be to use universal reference overloads. This would also allow for more
efficient copying/moving of `const char*`, for example. However, using universal
references itself bears problems (e.g. when introducing new non-template
overloads that) and it does not solve the code-bloat problem, because the
respective overloads (and in this case probably even more) will still be
instantiated.

Item 41 in Effective Modern C++ suggests a counter-intutive alternative to the
above dilemma: passing by value:

```C++
class Person {
public:
	void name(std::string name) {
		_name = std::move(name);
	}
private:
	std::string _name;
};
```

The advantage of this is that we only need to maintain one function. Also, this
operation need not be as inefficient as it looks. Ultimately, it will incur
exactly one more move operation relative to the operations that needed to be
performed for the overloads previously. If an lvalue is passed, it will be
copied into the parameter, and then moved into _name. If we had just used a
`const&` overload, we had had only the copy, but not the move. For rvalues, we
will incur exactly one more move operation, adding to the one we would have
needed for the `&&` overload.

However, this may still be a problematic overhead. The factors listed below make
the idiom particularly ineffective for certain cases:

* The class is not copyable at all. For example, `std::unqiue_ptr` objects can
  only be taken by `&&` anyway. Therefore, we had only needed one function in
  the first place and the pass-by-value idiom will only decrease performance

* Move operations are not cheap on the object. Move operations are not
  implicitly more efficient than copies, but can sometimes (e.g. for
  `std::array`s) be just as expensive as a copy. In those cases, the additional
  move is something we most definitely cannot tolerate.

* The object is not always copied into the destination. Let us consider a
  function that takes a string as above, but only copies it under certain
  conditions, such as that its size is less than some maximum. In that case, we
  will have incurred the cost of one copy (when an lvalue is passed), while the
  overloaded versions had cost nothing at all.

* The class is a base class and slicing may occur. Taking a parameter by value
  is subject to the slicing problem, while taking a parameter by reference
  preserves its dynamic type.

In general, this idiom will never be more efficient than standard
methods. It can only be a good compromise between code brevity and
style on the one hand, and performance on the other hand.
