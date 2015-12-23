# allocators

Allocators are special template classes used by the standard containers
to allocate and deallocate memory. If you want to define your own,
follow this advice:

-   Make your allocator a template, with the template parameter T
    representing the type of objects for which you are
    allocating memory.
-   Provide the internal typedefs ‘pointer' and ‘reference’, but always
    have ‘pointer’ be T* and ‘reference’ be T&.
-   Never give your allocators 'per-object state’, i.e. do not let them
    have non-static data (all allocators of one type should be
    the same).
-   Provide a nested re-bind template struct to make it possible to use
    the same allocator but for a different type.

The last point refers to the fact that all node-based (associative)
containers in the standard do not use the allocator you pass to it (e.g.
Allocator<int> for list<int>) but actually need to use the same
allocator type, but for its private ‘node’ types (that hold the ’next’
and ‘previous’ pointers for a linked list). I.e.:

template<typename T>
struct Custom_Allocator
 {
typedef T value_type;
typedef T* pointer;
typedef T& reference;
 T* allocate(std::size_t numberOfObjects);
void deallocate(T* ptr, std::size_t numberOfObjects);
// There is no such thing as a templated
// typedef or using declaration, so you
// provide this nested struct that is
// itself templated and that just provides
// the typedef to have access to the allocator
// class but for a different type. If there
// were no rebind struct there would be no way
// to access the un-templated type of an allocator
// passed to a container or so, since an instance
// of a template class for a certain type is entirely
// unaware that it is an instance of a template class
// (since it is simply an instance of the statically,
// compiler-generated version of the class for type T)
template<typename U>
struct rebind
 {
typedef Custom_Allocator<U> other;
 };
 };
template<typename Alloc>
void foo(const Alloc& allocator)
 {
// Alloc has no idea that it is actually of
// type Custom_Allocator<T>
typename Alloc::template rebind<std::string>::other
allocator_for_string;
// bla
 }
int main(int argc, char * argv[])
 {
Custom_Allocator<int> allocator_for_int;
foo(allocator_for_int);

}

Note also the differences in signature between ‘operator new’ and
the ‘allocate’ method of the Allocator:

void* operator new(std::size_t numberOfBytes);

T* Allocator<T>::allocate(std::size_t numberOfObjects);


