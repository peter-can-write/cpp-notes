# constructing-smart-pointers

Make sure that no exception can occur between the allocation of a
resource and the passing of that resource to the constructor of a
resource-managing class. 

do(std::unique_ptr(new Widget), callToFunction());

In the above statement, there are three statements the compiler will
execute:

-   NEW WIDGET to allocate the resource
-   std::unique_ptr(…) The passing of the widget to the constructor of
    std::unique_ptr
-   CALLTOFUNCTION() some other function that needs to be called

However, the compiler is free to choose the order in which to execute
these three statements. If the compiler happens to first allocate the
resource and then executes CALLTOFUNCTION() because that makes the code
more efficient for some reason, the situation could occur where an
exception in CALLTOFUNCTION() may prevent the previously allocated
widget to be passed on to the resource-managing class, std::unique_ptr.
In that case, the resources would leak!

Therefore, make sure allocations are exception safe:

std::unique_ptr ptr(new Widget);

do(ptr,callToFunction());

Nothing can happen between the allocation and the passing on of the
resource in this case.

OR, IN C++14:

std::make_unique<Widget>(); // Returns std::unique_ptr<Widget>


