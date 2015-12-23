# make-functors-pure-functions

A _pure function_ is a function whose return values depend only on its
arguments. If _f_  is a pure function and _x_ and _y_ are its
parameters, the return value of _f(x, y)_ can change only if the value
of _x_ or _y_ changes. 

Many algorithms copy functions, thus if a functors return value depends
on its state, the results may be unexpected, as the state will be reset
to the initial state after every copy (unless appropriate copy
constructors have been implemented).

To enforce this, mark the call operators of your functors _CONST_.
