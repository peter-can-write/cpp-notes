# Hide information

Hiding and abstracting information is an essential element of good design. It reduces and/or tightens the dependencies between a class'/unit's users and its implementation. This has the benefit that changes need only be made and felt in few places without any client code broken and also strengthens the invariants of your code.

One ground rule for this is to never expose data members of a class, unless it is a pure C-style struct with only data (in that case the data is the interface). Rather, use functions or abstract further.

One exception to this rule is often testing. One may have a very tight interface but nevertheless many individual steps in the implementation of that interface. When wanting to unit test those steps, the tests may require friend access to the class.
