# arrow-operator

An arrow operator overload should expose the underlying raw pointer
(memory) object. There is special syntax governing the arrow operator in
that it is applied recursively to itself until a pointer is exposed.

class A

{

public:

int* operator->() { return &x; }

const int x = 5;

}

class B

{

public:

A operator->() { return A(); }

}

B b.

b->x // ((b.operator->().)operator->())->x;

13.5.6/1[...] _An expression x->m is interpreted
as (x.operator->())->m for a class object x of type T
if T::operator->() exists and if the operator is selected as the best
match function by the overload resolution
mechanism_ If x->operator->() yields a pointer, it gets dereferenced, if
it yields an object of a type that overloads operator->() that operator
gets called.
