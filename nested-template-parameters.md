# nested-template-parameters

template<typename Alloc, template <typename, typename> class C>

std::size_t foo(const C<std::string, Alloc>& cont)

{}

or

template <typename T, typename... Args, template <typename, typename...>
class C>
std::size_t foo(C<T, Args...> &cont)
 {
     return cont.size();

}

foo(std::vector<std::string>()); // works

foo(std::list<std::string>()); // works

foo(std::vector<int>()); // doesn’t work


