# unique_ptr

## General Information

* `std::unique_ptr` is an exclusive-ownership smart-pointer class that should be your first choice for a smart-pointer.
* A non-null `std::unique_ptr` always owns what it points to.
* Moving a `std::unique_ptr` transfers ownership from the source pointer to the destination pointer, setting the source-pointer to `null`.
* Copying a `std::unique_ptr` is disallowed, because it would violate the exlusive-ownership semantics of `std::unique_ptr`. Two unique_ptrs could otherwise point to the same resource, each thinking it owned and should therefore destroy that resource.
* `std::unique_ptr` is thus a *move-only type*.
* Upon destruction, a non-null `std::unique_ptr` destroys its resource, which is accomplished using the built-in `delete`-operator on the underlying raw-pointer by default.
* This deletion-behavior can be overridden with a custom deletion-function.
* There are two variants of `std::unique_ptr`:
	* One for single-objects: `std::unique_ptr<T>`
	* One for arrays: `std::unique_ptr<T[]>`
* `std::shared_ptr`s can be constructed from `std::unique_ptr`s (moved)
* `std::unique_ptr` is perfect for returning from factory functions, because you can assign the result to either a unique or a shared pointer.

## Custom-Deleters

Both variants of `std::unique_ptr` accept a custom deletion-function to override the default deletion-mechanism (buil-in `delete`). To pass such a deletion-function to a new instance of `std::unique_ptr`, one must pass the type of the deletion-function as a template-argument and the function itself as the second parameter to the constructor of `std::unique_ptr`, next to the raw-pointer to be managed:

```
auto custom_deleter = [] (auto* resource) {
	make_log_entry(*resource);
	delete resource;
};

std::unique_ptr<int, decltype(custom_deleter)> ptr(
	new int(5),
	custom_deleter
);
```

Note that it is not possible to specify a custom-deletion function via `std::make_unique` (doesn't fit the interface because of the variadic templates).

Normally, you can reasonably assume that the size of a `std::unique_ptr` is the same as that of a raw pointer (zero-cost abstraction). However, with custom deleters, this may no longer be the case. If you use an actual function (not lambda or functor), the size of `std::unique_ptr` usually grows from one word to two (second pointer). Also, it probably won't be inlined by the compiler. For function objects (functors or closure objects), the extra size depends on how much state is stored in the function object. Stateless function objects (e.g. from lambda expressions with no captures) incur *no* size penalty. Therefore, capture-less lambdas are generally very much preferred over actual function.

However, don't think wrapping a function call into a lambda will help, as you have to capture the function in the closure of the lambda.

## Notes on the two variants

The most commonly known variant of `std::unique_ptr` is the one for single objects, but there is also a specialization for array-types. The single-object form (`std::unique_ptr<T>`) has no subscript operator (`operator[]`), while the array-form (`std::unique_ptr<T[]>`) has no dereferencing (unary `operator*`) or arrow-operator (`operator->`).

You should never use `std::unique_ptr<T[]>` for any arrays of your own. `std::array`, `std::vector` or `std::string` are always the better choice. The only situation where `std::unique_ptr<T[]>` can be useful is if you are interacting with a C-API with a function that returns a raw pointer to a heap array that you assume ownership of.
