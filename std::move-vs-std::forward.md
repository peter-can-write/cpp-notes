# std::move-vs-std::forward

std::move is similar to std::forward, as both are just casts.

std::move casts an object to an rvalue reference of the same type. It
also denotes that the original object should no longer be used.

std::forward lets you specify to what to cast the object to, and is used
mostly for perfect forwarding.

std::move(var) is the same as std::forward<T&&>(var), where T is the
type of var.
