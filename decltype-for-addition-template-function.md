# decltype-for-addition-template-function

template<typename T, typename U>
auto add(const T& t, const U& u) -> decltype(t + u)
 {
return t + u;

}
