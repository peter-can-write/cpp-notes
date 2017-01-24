# Prefer minimal classes to monolithic classes

Small classes are easier to write, read, test and implement bug-free. They are often also more reusable and much better follow the principle that each entity in your design should have one coherent, cohesive responsibility.

Item 33 discusses benefits of minimal classes over monolithic ones. Those include:

* A minimal class usually better embodies only a single coherent, cohesive concept, while a large class is more likely to pack in more functionality than strictly necessary.
* A minimal class is easier to understand.
* A minimal class that does one thing well is more easily reusable.
* A minimal class is easier to deploy.
* Monolithic classes are less well encapsulated. Often "fat" classes have many member functions that need not be members. Rather than giving them access to all the class' privates and breaking encapsulation, it would be better to define them as non-member functions operating on the interface of the class. In that case it is also more important to define a minimal but strong interface for the class.
* Monolithic classes usually try to solve all possible problems. This is usually not possible, anyway.
* Monolithic classes are often more error-prone and less easily maintainable.

An example that violates this principle is `QString`, which has an insane amount of functionality to solve virtually all problems, but also has many of the shortcomings associated with monolithic classes that are discussed above. Another example is `std::string`, which duplicates a lot of functionality found in the standard's algorithms.

A good example of this principle is `std::vector`, which delegates a lot of functionality, like element search, to non-member functions in the `std` namespace.
