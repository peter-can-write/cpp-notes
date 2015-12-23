# inserting-and-erasing-from-a-reverse-iterator

Any container’s insert() method only accepts non-reverse, non-const
iterators. Therefore, you must convert using base(), but do a few more
alterations:

TO INSERT:

Insertion always happens in a way that the new element is inserted _at_
the position of the iterator (i.e. the new element will be before the
old one). For forward iterators, this means:

[0, 1, 2, 3]

      ^ insert 7 here

Result: [0, 7, 1 ,2, 3]

For a reverse iterator, you would call itr.base(). This returns an
iterator to one position to the right of the reverse iterator’s position
(to handle rbegin/end). Now, you could say the insertion doesn’t do what
you want, from the view of a forward iterator:

[0, 1, 2, 3]

      ^ insert 7 here (from reverse iterator)

call insert(itr.base(), 7)

[0, 1, 2, 3]

          ^ itr.base() points here

Result: [0, 1, 7, 2, 3]

So not “before" the reverse iterator? But if you think about it, it did
insert it _before_ the reverse iterator, if you consider that it moves
from right to left. So the corresponding action to insert for a reverse
iterator is really to insert at itr.base(), but consider if it matches
your expectations.

TO ERASE:

To erase, you really want a pointer to the item being pointed to by the
reverse iterator. So your first intuition may be to do the standard
thing: convert to base and decrement (base() returns iterator to the
following element):

container.erase(—itr.base(), 7)

However, _OFTEN_ iterators are typedefs for pointers (for strings and
vectors), and C++ doesn’t allow modification of rvalue pointers. So just
increment the reverse iterator before:

container.erase((++itr).base())
