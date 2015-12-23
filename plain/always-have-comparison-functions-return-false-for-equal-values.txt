# always-have-comparison-functions-return-false-for-equal-values

Associative containers work with equivalence, i.e. you pass them one
comparison function for a < b and it then determines whether two values
are _equivalent_ (similar to equal) by testing if !(a < b) && !(b < a).
It does so more precisely by using the function you pass as a predicate,
i.e. ! pred(a, b) && ! pred(b, a). This predicate MUST RETURN FALSE FOR
EQUAL VALUES. Else you corrupt the container. The reason why is that
when you insert a value into an associative container, it needs to check
if the value already exists:

std::set<int> set{1};
 set.insert(1);

print(set.size()); // 1

If you now were to pass a predicate that returns true for equal values,
the set would go ahead and test if:

!(a<=b) && !(b<=a)

if now a and be are equal, this yields:

!true && !true

which is

false && false

which ultimately evaluates to false. In explanation, this means that if
you try to insert a value into a set that already exists in that set,
the set would determine that the value is not yet present (because the
equivalence check failed for all values) and thus insert it again,
making the set not a set but something ugly.

int main(int argc, char * argv[])
 {
auto predicate = [&] (int a, int b) { return a <= b; };
std::set<int, decltype(predicate)> set({1}, predicate);
 set.insert(1);
print(set.size()); // 2

}

Therefore don’t ever do it!
