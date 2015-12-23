# item-23:-prefer-non-member-non-friend-functions-to-member-functions

The object-oriented principle of data encapsulation means hiding the
data as much as possible. Having too many member functions access member
data is not good encapsulation, which is why it is often better to have
non-member non-friend functions outside the class handle the available
member functions. E.g., if a class has two member functions and a third
one that accesses the first two, it is better to make the third
non-member non-friend because it doesn’t really need to have access to
all the data inside. 

This also has the advantage of being able to split up non-member
non-friend functions into multiple files, possibly within the same
namespace. 


