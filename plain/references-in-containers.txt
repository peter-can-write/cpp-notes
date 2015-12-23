# references-in-containers

You thought it was not possible to use references in standard
containers:

 

int x;

std::vector<int&> v = {x}; // goes insane with errors

However, you can use a std::reference_wrapper<T>:

int x;

std::vector<std::reference_wrapper<int>> v = {x}; // works

std::reference_wrapper<T> is also what the function std::ref(argument)
returns.
