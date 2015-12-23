# template-specializations

There is no such thing as a partial template specialization for
functions, there are simply overloads.

template<typename T> void f(T); // One template function

template<typename T> void f(T*); // Not a partial template
specialization, just simply an overload

Overloads of template functions are called ‘base templates’.

template<> void<int> f(int); // full/explicit specialization of function
template

Don’t fully/explicitly specialize function template, just make a plain
old non-template function

void f(int); // will be called for an integer, not template functions.
