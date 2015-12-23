# std::generate

You can fill a range with a value using std::fill:

std::vector<std::size_t> v(10);

std::fill(v.begin(), v.end(), 5); // fills the range with fives

But if you have a function that returns different values at each
subsequent call, use the std::generate:

std::size_t foo()

{

     static std::size_t count = 0;

     return count++;

}

std::generate(v.begin(), v.end(), foo); // sets *itr = foo() at each
call

or assign to only a certain number of elements with std::generate_n:

template<typename Itr, typename Function>

std::generate_n(Itr destination, std::size_t count, Function function);

std::generate(v.begin(), 3, foo); // assign foo() to three elements
starting at v.begin()
