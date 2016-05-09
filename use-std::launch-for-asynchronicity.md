# Use `std::launch` for asynchronicity

`std::async` allows the specification of a *launch policy*, being one or many
of:

1. `std::launch::async`: Run the thread asynchronously for sure.
2. `std::launch:deferred`: Run the thread synchronously for sure, blocking on
   calls to `wait()` and `get()` if such calls are ever made.

By default, the launch policy is `std::launch::any`, which is the bitwise-OR of
the above two. This lets the underlying scheduler decide which policy is
best. However, note that from an expression like `auto future = std::async(f)`:

1. It's not possible to predict whether `f` will run concurrently or
   synchronously.
2. It's not possible to predict whether `f` will run on a different thread than
   the calling thread.
3. It's may not be possible to predict whether `f` will run *at all*.

Points (1) and (2) make it particularly hard to determine which thread-local
variables (declared with `thread_local`) will be modified in a program.

It also affects calls to the `wait_(until|for)` family of methods. For example,
we may wish to do something while waiting for a thread:

```C++
using namespace std::chrono_literals;

auto future = std::async(function);

// Wait (block) for 100ns, see if the future is ready,
// if not do something else
while (future.wait_for(100ns) != std::future_status::ready) {
	/* Do something */
}
```

The problem is that if the task was in fact deferred, it will return
`std::future_status::deferred` and thus never be ready (as the deferred task was
also not yet run with `wait()` or `get()`). There is unfortunately no intuitive
way to determine if a task has been deferred or not. Therefore, to handle the
above possible bug, one would have to wait for zero seconds and see if the
return status is `std::future_status::deferred`:

```C++
using namespace std::chrono_literals;

auto future = std::async(function);

if (future.wait_for(0s) == std::future_status::deferred) {
	/* ... */
} else {
	while (future.wait_for(100ns) != std::future_status::ready) {
	  /* ... */
  }
}
```

So when is this ambiguity about whether or not a task was run asynchronously
important? For sure, it is fine if the followind conditions are fulfilled:

1. The task need not run concurrently with the thread calling `get` or `wait`.
2. It doesn't matter which thread's TLS is read or written.
3. Either there's a guarnatee that `get()` or `wait()` will be called on the
   future returned by `std::async` somewhere along the path of execution, or
   it's acceptable that the function never runs.
4. Code using `wait_for` or `wait_until` takes the possibility of the task being
   deferred into account.

If the above do not apply, you should specify the `std::launch::async` policy
explicitly. Also, you may create a utility function like so:

```C++
template<typename Function, typename... Args>
auto launch_async(Function&& function, Args&&... args) {
  return std::async(
    std::launch::async,
	std::forward<Function>(function),
	std::forward<Args>(args)...
  );
}
```
