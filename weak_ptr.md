# weak_ptr

`std::weak_ptr` is a kind of smart-pointer that does not take any active or
*strong* ownership over a resource, but serves only to be able to check if
another `shared_ptr` that really owns the resource has *expired*, i.e. a
`weak_ptr` tells you whether the resource it points to has been destroyed and if
the `weak_ptr` thus *dangles*. A `weak_ptr` does not affect the reference-count
of an object, but only its *weak-count*, which is also stored in the
control-block. You cannot dereference a `weak_ptr`, but it is possible to
promote a `weak_ptr` to a `shared_ptr`, and the access the resource. Good use cases for `weak_ptr` are caching (return resources, cache them, keep weak_ptrs to see if you have to re-load them) or the observer (signal/slot) pattern.

A `weak_ptr` is created via assignment/construction from a `shared_ptr`:

```C++
auto shared = std::make_shared<T>();

std::weak_ptr<T> weak = shared; // or weak(shared);
```

Dereferencing a `weak_ptr` is not possible, as there could be a race-condition
between checking if a `weak_ptr` has expired and dereferencing it (the last
`shared_ptr` pointing to the resource could be destroyed in another thread
meanwhile, causing the `weak_ptr` to dangle when it is dereferenced). There are
two ways, however, to *promote* a `weak_ptr` to a `shared_ptr`:

1. Via `std::weak_ptr::lock`:

This method will yield a `std::shared_ptr`, either pointing to the resource the
`weak_ptr` tracked if it is still alive, or else being set to `null`.

```C++
auto shared = std::make_shared<T>();

std::weak_ptr<T> weak = shared;

if (! weak.expired()) auto new_shared_ptr = weak.lock();

shared.reset();

auto null_shared_ptr = weak.lock();

std::cout << std::boolalpha << static_cast<bool>(p) << std::endl; // false
```

2. In the constructor of `std::shared_ptr`:

Constructing a `shared_ptr` from an expired `weak_ptr` will throw an `std::bad_weak_ptr` exception.

```C++
auto p = std::make_shared<int>(5);

std::weak_ptr<int> weak = p;

std::shared_ptr<int> good(weak);

p.reset();

std::shared_ptr<int> bad(weak);
```

In terms of performance, a `std::weak_ptr` is the same size as a `std::shared_ptr`.
