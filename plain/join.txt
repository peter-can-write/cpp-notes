# join

template<typename Itr>
std::string join(Itr begin, Itr end, const std::string& s =
std::string())
 {
std::ostringstream stream;
while(begin != end)
 {
 stream << *begin;
if (++begin != end) stream << s;
 }
return stream.str();

}
