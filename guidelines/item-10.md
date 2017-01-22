# Minimize global and shared data

Sharing data between units in your code (in a single thread) or between processes or threads is often unmaintainable and increases complexity of your program. Sometimes, it will also impact performance in a negative way.

As such, a general guideline is to try to not declare variables with external linkage at namespace or global scope. Not only do the latter pollute the global namespace, but they also give rise to subtle bugs as the order of initialization of global variables in different translation units is undefined (that is, variables defined in the same translation unit will be initialized sequentially, but when referencing a global variable in an other TU you cannot be sure that it has yet been initialized [1]). In a multi-threaded application, shared data may cause contention and reduce performance. Message queues should be used instead.

1. http://stackoverflow.com/questions/3746238/c-global-initialization-order-ignores-dependencies/3746249#3746249
