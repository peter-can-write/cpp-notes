# explicit-constructors

Declaring a constructor EXPLICIT ensures that there will never be
implicit type conversion of an object.

This class’ constructor takes an integer:

class Foo

{

public:

     explicit Foo(int x);

};

This function takes an object of Foo as its parameter, meaning that

any value passed to bar will be used to construct foo by calling the

appropriate constructor. Usually, this means that a Foo object will

be passed and that foo will be constructed using Foo’s copy constructor:

void bar(Foo foo);

Foo f;

bar(f); // Ok!

bar(5); // Not Ok!

By declaring Foo’s constructor explicit, we forbid the compiler to
initialize foo from

anything else than a Foo object, since you could also call bar with an
integer and the

compiler would construct foo from the integer.


