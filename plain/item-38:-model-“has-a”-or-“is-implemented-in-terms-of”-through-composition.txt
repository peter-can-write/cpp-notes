# item-38:-model-“has-a”-or-“is-implemented-in-terms-of”-through-composition

Composition means modeling one type as a compound of other types. It can
be described as a “has-a” relationship and should be used when “is-a”
does not apply. E.g., a Person has a name, a Person _is_ not a name.

Is-implemented-in-terms of is used when public inheritance could be
used, but would violate the “is-a” principle. E.g., a list can contain
any items any number of times. To model a set (no duplicates) with a
list, one could inherit the set from list publicly. However, every set
would need to be a list, and because a list can hold duplicates and a
set not, it is not true that a set IS a list. Rather, the set should be
composed of a list. The set is then said to be “implemented in terms of”
a list.


