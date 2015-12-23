# `decltype`-deduction-rules

The great thing about decltype is that it (almost) does not have any
fancy rules regarding what types it deduces (returns). In 99% of all
cases, the type you get from decltype(x) is the type with which you
declared x:

    // decltype(i) = const int
    const int i = 0;

    // decltype(object) = const T&
    // decltype(f) = bool(const T&)
    tempalte<typename T>
    bool f(const T& object);

    // decltype(obj) = Object
    Object obj;

    f(obj); // decltype(f(obj)) = bool

    template<typename T>
    class Foo
    {
        /* ... */
    };

    // decltype(foo) = Foo<int>
    Foo<int> foo;

Known corner case: decltype(object.member)

Normally, accessing an object's member either using dot-notation
(object.member) or using arrow-notation with pointers (object->member)
yields a reference to the member, i.e. if the member is an int the type
of object.member will be int&. However, this expectation does not
reflect in the result of a call to decltype for this scenario:

    Object object;

    decltype(object.member); // yields type of member, not type&

    decltype((&object)->member); // also type&

The solution introduces the second peculiarity for decltype(), that
decltype(x) can differ from decltype((x)). More specifically, in all
cases but the above, calling decltype with a name prodcuces the type of
that name, while wrapping that name in parantheses gives you an
_expression_. An expression is more complicated than a name and _almost
always_ produces a reference to the type of the name:

    int x = 5;

    decltype(x); // int

    decltype((x)); // int&

This solves the problem for above (member access):

    Object object;

    decltype(object.member); // type

    decltype((object.member)) // type&
