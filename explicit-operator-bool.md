# explicit-operator-bool

C++11 adds explicit operator bool(), which is like the old operator
bool(), but differs in that it only converts under certain circumstances
— contextual conversion. First, consider the old/plain operator bool():

class BoolTest
 {
public:

operator bool() { return true; }

};

An object of type BoolTest will convert to a boolean in if-clauses
(wanted), but also for arithmetic operations (bad):

BoolTest b;
if (b) print::ln("!");

1 + b;

Now, take explicit operator bool:

class BoolTest
 {
public:
explicit operator bool() { return true; }

};

By adding explicit, we enable contextual conversion, i.e. it will
convert only when really expecting a boolean, but not for arithmetic
operations:

BoolTest b;
if (b) print::ln("!");

1 + b; // Invalid operands to binary expression 'int' and 'BoolTest'

These “special circumstances” are:

-   Negation: !t (5.3.1 p9).
-   Logical AND: t&&s (5.14).
-   Logical OR: t||s (5.15).
-   Conditional operator: t?"yup":"nope" (5.16 p1).
-   Selection statement (other
    than switch): if (t) or if (Testable t{}) (6.4 p4).
-   for statement, for(;t;) //..., and
-   while statement, while(t) //.... The wording isn't used directly for
    these, and they are actually defined in section 6.5, but 6.4 p2 says
    "The rules for conditions apply both to _selection-statements_ and
    to the for and while statements (6.5)."
-   do statement: do {//...} while (t); (6.5.2 p1).
-   static-assert_declaration: static_assert(t); (note you will
    need constexpr here) (7 p4).
-   Exception specification: SomeType foo() noexcept(t); (note you will
    need constexpr here) (15.4 p1).
-   NullablePointer concept: Any type P that can be used where the
    standard library requires a type fulfilling the NullablePointer
    concept, it is required that an instance of P can be contextually
    converted to bool (17.6.3.3 p3).
-   Any algorithm in the <algorithm> header that takes a template
    parameter named Predicate, then for an instance pred of that type,
    it must support pred(*first) being contextually converted to
    type bool. (25.1 p8) 
     Similarly, for a BinaryPredicate binary_pred, it is required
    that binary_pred(*first1, *first2) or binary_pred(*first1, value) can
    be contextually converted to type bool. (25.1 p9)
-   For any algorithm taking a comparator type Compare, for an
    instance Compare comp, the return value when contextually converted
    to type bool must convert to true if the first argument is less than
    the second, and false otherwise. (25.4 p2)


