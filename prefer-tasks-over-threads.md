# Prefer task-based programming to thread-based

Item 34 of Scott Meyers' *Effective Modern C++11* gives the advice to prefer
task-based (`std::async`) programming over thread-based (`std::thread`). The
simple reason for this is that if you do not explicitly pass the
`std::launch:async` option to `std::async`, `std::async` will have a very
particular behavior. Namely, it will not *always* create a new thread for the
callable passed. Rather, it will leave it up to the scheduler of the operating
system to decide if it should create a new thread for the task or not. Reasons
why not may be *oversubscription*, which is the situation where the OS must
handle too many software threads relative to the number of hardware threads
(cores) it can handle. This reduces cache-efficiency and overall performance of a
program.

When `std::async` decides to not create a new thread, it will instead defer the
launch of the function until a call to either `wait()` or `get()`, upon which it
will *synchronously* run the function.

Another clear disadvantage of the thread API over task-based programming is the
lack of return-value communictation methods (one would need to manually pass a
`std::promise` or use a `std::packaged_task`). Moreover, it should be noted that
if any child thread throws an exception, the *entire* program (i.e. the main
thread) is *terminated* via `std::terminate`. This is only the case for
non-detached (non-daemon) threads though, so you can avoid this last point by
calling `std::thread::detach` on the thread.

There do exist situations where threads may be favorable, however:

1. When you need access to the API of the underlying threading implementation.
   The concurrency API is typically implemented on top of pthreads or Windows
   Threads. These APIs are currently richer than the standard's, so you may
   access them via the `std::thread::native_handle` method.
2. You need to and are able to optimize thread usage for your application.
   If you need and can implement load balancing yourself, for example for a
   server of known and fixed hardware, then you can create thread pools with
   `std::thread`s.
