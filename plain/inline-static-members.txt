# inline-static-members

The reason why you normally can’t define a static member inline is
because YOU CAN ONLY TAKE THE ADDRESS OF A STATIC MEMBER IF IT HAS AN
OUT-OF-CLASS DEFINITION. The emphasis here is on “take the address of”.
So if you declare static const integral types inline, the linker will
only not complain if you don’t ever take the address of the value, i.e.
don’t pass it by (const-)reference to a method for example. If you only
use it as a constant or pass it by value, the code will compile and link
just fine.
