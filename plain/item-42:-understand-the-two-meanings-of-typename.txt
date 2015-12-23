# item-42:-understand-the-two-meanings-of-typename

When declaring template parameters, CLASS and TYPENAME are identical:

template<class T> = template<typename T>

However prefer TYPENAME, because it denotes that the passed type must
not be a class (e.g. can be a function predicate).

Use TYPENAME to identify nested dependent type names.

DOESN’T WORK:

template<typename T>
void foo(const T& t)
 {
     // Will not work, how do we know T::iterator is
     // a type? Could also be a static data variable!
     T::iterator itr = t.begin();

}

WORKS:

template<typename T>
void foo(const T& t)
 {
     // Will work with "typename", because we
     // tell the compiler explicitly that
     // T::iterator is a type!
     typename T::iterator itr = t.begin();

}


