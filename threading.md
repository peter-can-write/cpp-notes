# threading

Concurrent programming models:

Multiprocessing

-   Each process has one thread running
-   Communicate via interprocess communication
-   - Slow to start, OS has to devote resources to manage the process
-   + Can run on distributed systems

Multithreading

-   Multiple thread in one process.
-   Communicate via shared memory.
-   + Fast to Start
-   + Low OS overhead
-   + Shared memory communication much faster than interprocess
    communication
-   - Difficult to implement
-   - Can’t run on distributed system

#include <thread>

std::thread thread; // nothing running, assign to function later

std::thread thread([] () { return 2; }); // starts to run function right
away

thread.join(); // wait for thread to finish

thread.detach(); // thread will run freely, daemon process

You can only join or detach a thread once.

Most vexing parse also here:

std::thread thread(Functor()); // function declaration

arguments to the constructor of std::thread are always passed by value!
(even if the function takes a reference)

Unless you use a reference wrapper: std::ref(argument)

And you can still std::move(arguments)

Or use pointers.

Threads cannot be copied, only moved.

All threads have IDs, get them via:

std::this_thread::get_id(); // main thread

thread.get_id(); // child id (or call this_thread the thread)

TOO MANY THREADS: _OVERSUBSCRIPTION_. Causes context-switching, slow
performance.

std::thread::hardware_concurrency(); // how many cores you have,
indication of how many threads you should run

#include <mutex>

std::mutex mutex; // global

mutex.lock();

// shared memory operation

mutex.unlock();

If the operation before unlock() throws, much badness

std::lock_guard<std::mutex> guard(mutex); // RAII

std::cout is a bad example for use with a lock, because it’s a global
variable. More realistic with a file in a class, for example. Always
encapsulate the shared object! Else a thread may access it outside (if
accessed through a getter).

The interface of a class can cause data-races. Consider a stack:

class Stack

{

public:

     T& top() const;

     void pop();

};

And a function f:

void f(Stack& stack)

{

     auto value = stack.top();

     stack.pop();

     process(value);

}

This function is not at all thread-safe! For two threads there will be:

left) value = stack.top(); // value1

right) value = stack.top(); // value1 (same)

left) stack.pop(); // pops value1

right) stack.pop(); // pops next value without using!

left) process(value); // processes value1

right) process(value); // processes value1

Here the interface is bad for threading because the function is split up
into two functions. It is inherently not thread-safe. You should wrap it
into two functions (or change the interface, if you can, like here.)

Avoid data-race:

-   Use mutex to synchronize data access.
-   Never leak a handle of data to outside.
-   Design interface appropriately.

dead-lock:

thread 1:

lock(mutex1); // locks mutex1

lock(mutex2); // here mutex2 will be locked

thread2:

lock(mutex2); // locks mutex2

lock(mutex1); // here mutex1 will be locked

Correction: Lock mutexes in the same order.

Or let C++ help you:

std::lock(mutex1, mutex2);

std::lock_guard<std::mutex>(mutex1, std::adopt_lock);

std::lock_guard<std::mutex>(mutex2, std::adopt_lock);

Considerations:

-   Prefer locking a single mutex at a time.
-   Try not to lock the mutex and call some user-function (which may do
    more locking).
-   Use std::lock() with std::adopt_lock to lock more than one mutex.
-   Lock mutexes in same order

Locking granularity:

-   Fine-grained lock: protects small amount of data
    -   Too much becomes tricky
-   Coarse-grained lock: protects big amounts of data
    -   Losing opportunities for concurrency

Use std::unique_lock<std::mutex> to lock() and unlock() multiple times
(can’t do that with std::lock_guard):

std::unique_lock<std::mutex> locker(mutex);

// ...

lock.unlock();

locker.lock();

// ...

You can also defer the initial locking:

std::unique_lock<std::mutex> locker(mutex, std::defer_lock);

// not locked yet

locker.lock(); // now locked

std::unique_lock is heavier/less efficient than std::lock_guard.

Lazy Initialization is what you do when you wait to open a file/call a
function/do something until first use. It is thus often called the
“initialization-on-first-use-idiom”. for this idiom, this would not be
thread safe:

void Class::f(path)

{

     if (! _file.is_open()) // two threads can run through this while
the file is being opened

     {

          std::lock_guard<std::mutex>(_mutex);

          file.open(path);

     }

     // ...

}

So you might come up with this:

void Class::f(path)

{

     {

          std::lock_guard<std::mutex>(mutex2);

          if (! _file.is_open()) // two threads can run through this
while the file is being opened

          {

               std::lock_guard<std::mutex>(_mutex);

               file.open(path);

          }

     }

     // ...

}

But now you’re doing all this useless locking/unlocking once the file is
opened. The standard provides a solution for this: std::once_flag and
std::call_once(). The first is a flag object  which you should keep as a
member. The second is a function taking such a flag object (to
check/set) and a function to call. It is the correct and thread-safe
solution here:

void Class::f(path)

{

     std::call_once(_flag, [] () { _f.open(path); });

     // ...

}

Don’t use mutex when sleeping (you’re blocking other threads!)

Use a std::condition_variable to synchronize execution behavior across
threads.

std::condition_variable condition;
std::queue<int> queue;
std::mutex mutex;
void produce()
 {
// unique_lock so that we can lock()/unlock()
std::unique_lock<std::mutex> lock(mutex, std::defer_lock);
for (int i = 0; i < 10; ++i)
 {
 lock.lock();
// produce data
 queue.push(i);
 lock.unlock();
//condition.notify_one(); // notifies one waiting thread
 condition.notify_all();
std::this_thread::sleep_for(std::chrono::seconds(1));
 }
 }
void consume()
 {
std::unique_lock<std::mutex> lock(mutex, std::defer_lock);
int value;
do
 {
 lock.lock();
// takes lock object to unlock it if it is asleep
// as to not block other threads. Also waits
// until condition.notify_one/all() is called
// then re-locks the lock. That's why you have
// to use std::unique_lock (with lock/unlock
// methods) not std::lock_guard.
 condition.wait(lock);
// Can also wake up itself without notification
// if the predicate given returns true.
// Called 'spurious wake'
// condition.wait(lock, [] () { return ! queue.empty(); });
 value = queue.front();
 queue.pop();
 lock.unlock();
 }
while (value != 9);

}

For return values from one thread, use std::future and std::async.
std::async takes a function and its parameters, and returns whatever the
function returns, contained in an std::future<ReturnType>. Then, you can
use the std::future object’s get() method to retrieve the return value
ONCE (MORE TIMES CAUSES CRASH). Always capture the return value
of std::async (even with std::future<void> if needbe).

// return type for clarity, use auto normally.

std::future<std::size_t> future = std::async(fibonacci, 5);

print::ln(future.get());

// called in this thread
auto future_here = std::async(std::launch::deferred, fibonacci, 5);
// called in new thread
auto future_there = std::async(std::launch::async, fibonacci, 5);
// determined by implementation (default value)
auto future_anywhere = std::async(std::launch::deferred |
std::launch::async,
   fibonacci,

  5);

So use std::launch::async to ensure that a new thread is created. Use
std::promise<T> to transmit value to other thread later on.

// note the reference
void wait_for_future(std::future<int>& future)
 {
/* ... */
// waits for value
int value = future.get();
print::ln(value);
/* ... */
 }
int main(int argc, const char* argv[])
 {
std::promise<int> promise;
std::future<int> future = promise.get_future();
std::thread(wait_for_future, std::ref(future)).detach();
/* ... */
 promise.set_value(5);

}

For many threads, use std::shared_future<T> (can assign to
std::future<T>::share()). You can assign shared_futures, don’t have to
move. All threads waiting will stop waiting with shared_future.

You can also call std::thread with member functions:

std::thread thread(&Class::method, *args);

Use std::packaged_task<function> in conjunction with std::bind to create
a a callable object which returns a std::future. The object returned by
std::bind is also a callable object, but with no sense of threading. So
if we call it in another thread, we’ll basically have no way of
retrieving its return value. With std::packaged_task, we can block and
then retrieve the value with a std::future.

std::queue<std::packaged_task<int()>> queue;
std::condition_variable pop_condition;
std::mutex mutex;
bool flag = false;
constexpr inline int factorial(int x)
 {
return x ? x * factorial(x - 1) : 1;
 }
void other_thread()
 {
std::packaged_task<int()> task;
 {
std::unique_lock<std::mutex> lock(mutex);
 pop_condition.wait(lock, [] () { return ! queue.empty(); });
 task = std::move(queue.front());
 queue.pop();
 }
 task(); // execute
 }
int main(int argc, const char* argv[])
 {
// no parameters for the bound object
std::packaged_task<int()> task(std::bind(factorial, 6));
// So that we can retrieve the value later.
std::future<int> future = task.get_future();
// Run in different thread
std::thread thread(other_thread);
 {
// prevent data-race
std::lock_guard<std::mutex> lock(mutex);
// std::packaged_task is move-only
 queue.push(std::move(task));
 }
// retrieve value from other thread!
print::ln(future.get());
 thread.join(); // done

}

3 ways to get a std::future:

-   promise::get_future
-   packaged_task::get_future
-   async() returns a future

TIME CONSTRAINTS:

Make a thread sleep for:

A certain duration:

std::this_thread::sleep_for(std::chrono::milliseconds(5));

Until a given time-point:

std::chrono::steady_clock::time_point piont =
std::chrono::stead_clock::now() + std::chrono::seconds(1);

std::this_thread::sleep_until(point);

Make a UNIQUE-LOCK wait:

std::unique_lock<std::mutex> lock(mutex);

mutex.try_lock(); // throws error if no mutex associated or mutex
already locked by this lock

mutex.try_lock_for(duration);

mutex.try_lock_until(time-point);

Make a CONDITION_VARIABLE wait:

condition.wait_for(unique_mutex, duration);

condition.wait_until(unique_mutex, time-point);

Make a FUTURE wait:

future.wait_for(duration);

future.wait_until(duration);

std::condition_variable condition;
int main(int argc, const char* argv[])
 {
std::mutex mutex;
std::thread thread([&] () {
std::unique_lock<std::mutex> lock(mutex);
 condition.wait_for(lock, std::chrono::seconds(1));
 });
// Will only wait 1 second, then just return
 thread.join();

}

SEMAPHORE

-   A counter that is manipualted atomically through two operations:
    signal and wait.
-   wait(semaphore): decrement count, if counter is zero then block
    until signalled()
-   signal(semaphore): increment counter, wake up one thread if any
    waiting
-   Each semaphore has an associated queue of processes
-   When wait() is called
    -   If semaphore is available, thread continues
    -   Else blocks and waits on queue
-   Signal opens the sempahore()
    -   If threads are waiting on a queue, one thread is unblocked
    -   If none are on the queue, the signal is remembered for the
        next wait() call

A semaphore whose value can be 0 or 1, is a mutex. A semaphore is really
a mutex where more threads are allowed to run than just one (mutex),
with an embedded queue for the threads.

class Semaphore
 {
public:
 Semaphore(std::size_t count)
 : _count(count)
 { }
void wait()
 {
std::unique_lock<std::mutex> lock(_mutex);
 _condition.wait(lock, [this] () { return _count > 0; });
 --_count;
 }
void signal()
 {
std::lock_guard<std::mutex> lock(_mutex);
 ++_count;
 _condition.notify_one();
 }
private:
std::size_t _count;
std::mutex _mutex;
std::condition_variable _condition;
 };
Semaphore semaphore(2);
void try_semaphore(int x)
 {
 semaphore.wait();
print::ln(x);
 semaphore.signal();
 }
int main(int argc, const char* argv[])
 {
std::thread t1(try_semaphore, 5);
std::thread t2(try_semaphore, 2);
std::thread t3(try_semaphore, 10);
 t1.join();
 t2.join();
 t3.join();

}
