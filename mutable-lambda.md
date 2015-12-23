# mutable-lambda

By default, by-value-captured data-members of a closure-class are const,
because the call-operator (operator()) is declared const inside the
produced closure class:

int x = 5;
 [=] { ++x; }(); // Not allowed, 'Cannot assign to a variable captured
by copy in a non-mutable lambda'

[x] { ++x; }(); // Not allowed, 'Cannot assign to a variable captured by
copy in a non-mutable lambda'

The rationale of the standard is that "a function object should produce
the same result every time it’s called”. Workaround is to declare it
“mutable”:

int x = 5;
 [=] () mutable { ++x; } (); // OK

[x] () mutable { ++x; } (); // OK

Side note: you must declare the empty parameter list ‘()’ before the
mutable keyword, while you normally don’t need the empty parentheses if
the lambda takes no arguments (as written above).

Big Note: If the call operator is const, why can you write this code?

int x = 5;
auto f = [&x] () { ++x; };
 f();

print::ln(x); // 6

Isn’t the reference to x const in the call operator of the generated
closure class? No, because when you write ++x, you are not modifying the
_reference,_ but the _referred value_. Understand for this that all
references are inherently const, i.e. int& x is conceptually like int&
const, making the reference unchangeable, just like int* const is _const
pointer to an int_. However, int& const does not exist because
references are inherently const (you cannot re-point them to another
variable). This means that modifying a value captured by reference is
always allowed, because you are never modifying the reference, but the
value that is referenced to (i.e. the const on the call operator has no
effect).
