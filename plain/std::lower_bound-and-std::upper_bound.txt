# std::lower_bound-and-std::upper_bound

template<typename Itr, typename T>
 Itr lb(Itr begin, Itr end, const T& value)
 {
if (begin == end) return end;
auto middle = begin;
std::advance(middle, std::distance(begin, end)/2);
if (value < *middle)
 {
auto result = lb(begin, middle, value);
return result == end ? middle : result;
 }
else if (value > *middle) return lb(std::next(middle), end, value);
else return middle;
 }
template<typename Itr, typename T>
 Itr ub(Itr begin, Itr end, const T& value)
 {
if (begin == end) return end;
auto middle = begin;
std::advance(middle, std::distance(begin, end)/2);
if (value < *middle) return ub(begin, middle, value);
else return ub(std::next(middle), end, value);

}
