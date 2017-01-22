# Given one entity one cohesive responsibility

This item explains the idea that you should prefer to give each entity, such as a variable, class, function, namespace, module or library, *one well-defined responsibility*. That is, a function should do one thing and only one thing. A class should represent one idea and only one idea. If there is something in your function that does something different than what the name of the function implies, then give that piece of code a new name and refactor it.

Bad examples of where this is not done are `std::basic_string`, which duplicates functionality with its own `find` or `subtr` function (which could be replaced with `std::find` or `std::string`'s constructor).
