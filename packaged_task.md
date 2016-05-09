# `std::packaged_task`s

C++11's concurrency API is primarily centered around task-based parallelism,
with `std::future` and `std::promise` forming the read/write-pair for
communciation across threads. With these two primitives, a function may be
called asynchronously like so:

```C++
int f(int x) {
	return x + 1;
}

// This will be the write-end
std::promise<int> promise;
// This will be the read-end
auto future = promise.get_future();
// Launch f asynchronously
std::thread thread([&promise] (int x) {
  promise.set_value(f(x));
  },
  5
);

// Get the value
std::cout << future.get() << std::endl;

// Make sure the main thread doesn't die
// before the children threads (or detach
// the child thread)
thread.join();
```

Note that it is only through promises and futures that we can even retrieve a
return value from a thread (without using global or static variables).

As you can see above, there is quite a lot of boilerplate code associated with
retrieving a value from a task. A `std::packaged_task` can make this a little
easier. Essentially, a `std::packaged_task` associates a task (i.e. callable)
with a promise (and the future you get from it). Such as packaged-task has its
call operator overloaded to invoke the function. However, do note that this
invocation is entirely synchronous. It is only by passing the `packaged_task` to
a `std::thread` that we can run it asynchronously:

```C++
// Package a task
std::packaged_task<int(int)> task(f);

// Get the future
auto future = task.get_future();

// Need to move the task into the thread
std::thread thread(std::move(task), 5);

std::cout << future.get() << std::endl;
thread.join();
```

A lot less boilerplate! From the example you can see that all a
`std::packaged_task` does is associate a promise with the return value of a task
and provide access to the future of that promise. Note also how we need to move
the task into the thread object. Also, be aware that calling `task()` in the
main thread would be equivalent to calling `f()` directly, as a
`std::packaged_task` itself is not in any way concurrent.
