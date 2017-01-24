# Minimize definitional dependencies to avoid cyclic ones

Item 22 highlights the importance of not creating dependencies on definitions between separate classes. This mainly deals with forward-declaring classes rather than including their definition. More precisely, there are really only two (!) cases where you must include the header file (i.e. definition) of a class you want to use:

1. You need to know the size of the class, because you are allocating it. If you follow the general rule of moving all your implementation code into the implementation rather than the header file, this should really only be the case when you're defining a class that holds the included class as a member. Otherwise, also the case of stack allocation in a function falls into this category. But again, this should happen only in an implementation (`.cpp`) file.
2. You need to access a member on the class. This would normally also be an implementation-side issue.

Notably, it is not necessary to include the class just because one of your public functions *returns* that type. Indeed, if the user calls that function, he or she will need its definition (to be able to declare a variable holding the result of that function). But by forcing the user to do so, you are really just enforcing the principle of *include what you use*.

One result of following this Item's advice is fewer cases of cyclic dependencies. Cyclic dependency can occur and may not necessarily be a problem, as it is a requirement for some designs. For example, you may need a tree structure where nodes have links back to the tree. However, cyclic dependencies between files should *not occur* if you could avoid them by forward declaring. Also, sometimes, you may want to reconsider your design and maybe factor out some functionality into a separate base class, or simply redesign your implementation altogether (e.g. in a way that does not require backlinks).
