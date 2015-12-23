# prefer-deleted-functions-to-private-undefined-ones

In C++98, when you wanted to prevent a user to use a certain
compiler-generated method (e.g. Copy-constructor,
copy-assignment-operator etc.), you would make it private (so that
outsiders couldn’t use it) and not define it (so that there would be a
linker error if internally you tried to use it — or friends):

class Example

{

public:

     void foo();    

private:

     Example(const Example& other); // not defined

     Example& operator=(const Example& other); // not defined

};

That’s quite a hack and also not very favorable as it only complains at
link-time in the worst case. The more modern, C++11 solution is to use
the “delete" keyword. When you say “<R> function(<T>) = delete;”, you
explicitly declare the function as deleted and the compiler will
complain when you try to call it. Here IT IS IMPORTANT TO MAKE THOSE
DELETED MEMBER FUNCTIONS PUBLIC, because a compiler will check
accessibility before deletion-status, meaning if you declare the
function private and delete it, then try to call it from outside the
class, the compiler will generate an error for accessing a private
function. This is less expressive as if it had generated an error for
the function being deleted, so DECLARE THE FUNCTION PUBLIC.

There are two more important use-cases for the _delete_ keyword:
non-member functions and template functions. For the former, it can be
quite nice to avoid certain unwanted overloads via explicit deletions.
Imagine you have such a function:

void function(int x);

Where you would really like the x to be an integer — not a double,
float, bool, char, but an _int_. You can achieve this by deleting the
respective overloads:

void function(int x); // same as before

void function(bool) = delete; // rejects bools

void function(double) = delete; // rejects floats and doubles
(double-overload preferred over int for float)

void function(char) = delete; // reject chars

When calling the function with any of those unwanted types, overload
resolution will direct the call to the respective overload. This then
produces a compiler-error because those functions were deleted.

The second use-case is for removing some unwanted specializations of a
template (like specialization, but in the opposite direction). Say you
have a template function for processing pointers, but do not want to
have the function take char*, because that points to a string, or void*,
because you can’t do much with it. You can delete the unwanted
specializations for char* and void*:

template<typename T>

void process_pointer(T* pointer)

{

     /* … */

}

template<>

void process_pointer<char>(char* pointer) = delete; // no want

template<>

void process_pointer<void>(void* pointer) = delete; // no want

THIS IS ESPECIALLY IMPORTANT BECAUSE YOU CAN’T DELETE SOME
SPECIALIZATIONS WITH THE PRIVATE-UNDEFINED METHOD, AS IT IS REQUIRED
THAT ALL SPECIALIZATIONS OF A TEMPLATE FUNCTION HAVE THE SAME
ACCESS-SPECIFIER AS THE BASE TEMPLATE!

Note also that the function specialization above is equivalent to making
a non-template overload and deleting it:

void process_pointer(void* pointer) = delete; // Same thing
