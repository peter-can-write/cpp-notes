# Thread-Local

Since C++11 variables may also have thread-local storage. For this there exists a specifier `thread_local`, just like `static`, `extern` and so on. Taken from the standard:

The thread_local keyword is only allowed for objects declared at namespace scope, objects declared at block scope, and static data members. It indicates that the object has thread storage duration. It can be combined with static or extern to specify internal or external linkage (except for static data members which always have external linkage), respectively, but that additional static doesn't affect the storage duration.

Thread storage duration. The object is allocated when the thread begins and deallocated when the thread ends. Each thread has its own instance of the object. Only objects declared thread_local have this storage duration. thread_local can appear together with static or extern to adjust linkage.
