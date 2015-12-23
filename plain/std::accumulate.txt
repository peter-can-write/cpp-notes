# std::accumulate

Use std::accumulate to summarize or generally accumulate ranges. Note
that the function predicate passed to std::accumulate, according to the
standard, may not have side-effects, however this concern concretizes
rarely to never.

Signature:

template<typename Itr, typename T, typename Predicate>

T accumulate(Itr begin, Itr end, T value, Predicate predicate);

std::list<std::size_t> ints = {1, 2, 3, 4, 5};
// Sum integral values
println(std::accumulate(ints.begin(), ints.end(), 0)); // 15
std::list<float> floats = {1.5, 0.3, -7.5, 10.123};
// Sum floating point values, not the 0.0
println(std::accumulate(floats.begin(), floats.end(), 0.0)); // 4.423
// MULTIPLY FLOATS YEEEEAH
println(std::accumulate(floats.begin(),
 floats.end(),
1.0,
std::multiplies<float>())); // -34.165125
std::vector<std::string> strings = {"hello", " ", "world", "!!"};
// Concatenate strings
// hello world!!
println(std::accumulate(strings.begin(), strings.end(), std::string()));
// Sum string lengths
// The function predicate must take the current sum
// as its first argument, the next string/element to update
// the sum as its second argument and return the updated sum
println(std::accumulate(strings.begin(),
 strings.end(),
0,
 [](std::size_t sofar, const std::string& next)
 { return sofar + next.length(); }));

// 13
