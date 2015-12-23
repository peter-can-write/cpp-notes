# make-sure-const-functions-are-thread-safe

You should make const member-functions thread-safe, unless you are 100%
sure they’ll not be used in a concurrent context (rare nowadays).
Consider this case:

class LazyInitializer
 {
public:
int calculate() const
 {
if (! _have_calculated)
 {
 _have_calculated = true;
// _value = very_expensive_computation
 }
return _value;
 }
private:
mutable bool _have_calculated;
mutable bool _value;

};

Here is a class which, as a side-effect/utility, can calculate some
value that does not change the state of the object conceptually (e.g.
this class holds an equation, and that stays const, so the method is
conceptually const). There are two cases of how this method is not
thread-safe:

1.  Thread One calls calculate() because _have_calculated is false
2.  Before _have_calculated is set to true by Thread One, also Thread
    Two makes it through the if-statement
3.  The very-expensive computation is performed twice

1.  Thread One calls calculate() because _have_calculated is false
2.  _have_calculated becomes true in Thread One
3.  Thread Two calls calculate() and there _have_calculated is true,
4.  So it goes on to return _value, which is not yet initialized!
5.  Thread One calculates the value ...

So use a mutex (std::atomic won’t work, because both value and
_have_calculated would have to be atomic and atomic works best with only
one variable):

class LazyInitializer
 {
public:
int calculate() const
 {
std::lock_guard<std::mutex> lock(_mutex);
if (! _have_calculated)
 {
 _have_calculated = true;
// _value = very_expensive_computation
 }
return _value;
 }
private:
mutable bool _have_calculated;
mutable bool _value;
mutable std::mutex _mutex;

};
