# `shared_ptr`

C++'s `shared_ptr` combines the best of two worlds: automatic memory management as known from garbage-collection on the one hand, and deterministic and generalized destruction enabled by destructors.

Simply put, `shared_ptr`s each store a pointer to a (hopefully) dynamically allocated resource, as well as a pointer to a *reference-count*. When one `shared_ptr` `a` copies from another `shared_ptr` `b`, `a` will point to the object pointed to by `b`, and no longer the object `a` pointed to before. Thus, the reference-count for the object previously pointed to by `a` decreases by one, and the reference-count for the object pointed to by `b` (and now `a`) increases by 1. When the reference-count for an object goes from 1 to 0, the last `shared_ptr` pointing to it will destroy the shared resource.

## Implementation

As mentioned, `shared_ptr`s store meta-information such as the *reference-count* in a *control-block*, such that a `shared_ptr` looks like this conceptually:

`std::shared_ptr`
[ T* ] ------------------ [Object]
[ ControlBlock* ]
    |
    |
    |
[ Reference Count ]
[ Weak Count      ]  <-- Control Block
[ Custom Deleter  ]
[ Allocator       ]

Such a control-block is allocated for every new object, i.e. when:

* you call `std::make_shared` (always constructs new object)
* you construct a `std::shared_ptr` from `std::unique_ptr` (`shared_ptr` assumes ownership)
* you create a `std::shared_ptr` from a raw pointer

## Information

* `shared_ptr`s are twice the size of a raw pointer, i.e. two words. They store one pointer to the shared resource and another pointer to a control-block, containing the reference-count, the weak-count (how many `std::weak_ptr`s point to the object) as well as other data such as custom deleters and allocators.

* Memory for the reference-count (i.e. the control-block) must be dynamically allocated. However, when you use `std::maked_shared`, the cost of this allocation can be avoided, as `std::make_shared` allocates memory for the object as well as for the control-block in one allocation.

* Manipulation of the reference-count must be atomic. Multiple threads may try to increment or decrement (i.e. read and write) the count "at the same time", so this operation must be synchronized. This is one problem of `std::shared_ptr`, as atomic operations are usually more expensive than non-atomic ones.

* Move-operations are virtually free, or at least a lot cheaper than copy-operations, as moving one `shared_ptr` into another just means assigning the pointer of the source `shared_ptr` to the pointer of the destination `shared_ptr`, and setting the pointer in the source `shared_ptr` to null. The reference-count need not be incremented or decremented (atomically), because you are just "moving the pointer from one container to another".

* `shared_ptr` can be constructed from `std::unique_ptr`, but not vice-versa. That's why `std::unique_ptr` is such a great choice for return-types of factory functions, because you can assign the result to either a `shared_ptr` or a `unique_ptr`.

* A custom deleter is not part of the type of a shared_ptr, because it is not stored in a `shared_ptr` object explicitly, but in the control-block. I.e., you can have multiple shared_ptrs with different deleters but both pointing to objects of the same type, all stored in one container:

```C++
std::shared_ptr<T> a(new T, [] (T* t) { delete t; });
std::shared_ptr<T> b(new T, [] (T* t) { std::cout << 1; delete t; });

std::vector<std::shared_ptr<T>> v = {a, b};
```

This is not possible with `std::unique_ptr`:

```C++
auto d1 = [] (T* t) { delete t; };
auto d2 = [] (T* t) { std::cout << 1; delete t; }

std::unique_ptr<T, decltype(d1)> a(new T, d1);
std::unique_ptr<T, decltype(d2)> b(new T, d2);

std::vector<std::unique_ptr<T, ???>>
```

* One very important note to make about `shared_ptr`s is that it is crucial that you always only have one control-block per object. Multiple control-blocks mean multiple reference-counts, which in turn result in multiple deletions, where already more than one results in undefined behavior. Thus, it is a bad idea to construct `shared_ptr`s from pointers stored in variables, because you could create two `shared_ptr`s:

```C++
auto p = new T;

std::shared_ptr<T> a(p); // first control-block, OK
std::shared_ptr<T> b(p); // second control-block, BAD!!
```

Therefore, you should prefer using `std::make_shared`, if you need no custom deleter, or `new`-ing the variable right in the constructor if you do need to pass custom-deleter.

* If you want to construct `shared_ptr`s from the `this`-pointer, you inherit your class `T` from `std::enable_shared_from_this<T>`. You can then call `shared_from_this()` to create a `shared_ptr` from within the class. The point is that you want to prevent creating new control-blocks if `shared_ptr`s outside the class already have control-blocks for the object. `shared_from_this` will modify a control-block that already exists for the object (this information about the control-block is stored in `enable_shared_from_this`). However, if there exists no previous control-block, `shared_from_this` will throw an exception. It's thus a good idea to declare constructors in such a class private and rely only on public factory functions:

```C++
class Foo : public std::enable_shared_from_this
{
public:

	template<typename... Args>
	static std::shared_ptr<Foo> Create(Args&&... args)
	{
		// Create Foo object
	}

private:

	Foo() { }
}
```

* `shared_ptr`s may seem expensive with dynamically allocated control-blcoks, arbitrarily large deleters and allocators, virtual function machinery (inside the control-block) and atomic reference-count manipulation, but if you cannot rely on `std::unique_ptr`, they are still worth it in most cases (no special deleters, allocaters etc.)
