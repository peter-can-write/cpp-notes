# item-24:-declare-non-member-functions-when-type-conversions-should-apply-to-all-parameters

Parameters are eligible for implicit type conversion only if they are
listed in the parameter list, therefore it is often a good idea to make
functions with type conversions on both sides (this-> side and parameter
side) non-member, because that will ensure that both sides are casted,
if necessary.

E.g.

Foo operator* (const Foo& other);

This will only work for foo * 2, but not for 2 * foo because 2 isn’t
converted automatically.

Therefore, having a non-member function is better:

Foo operator* (const Foo& left, const Foo& right);

Now both sides work.


