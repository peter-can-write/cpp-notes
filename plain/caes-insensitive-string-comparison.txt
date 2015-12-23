# caes-insensitive-string-comparison

std::string a = "apple", b = "AbPlE";
// Just returns true or false
bool result = std::equal(a.begin(),
 a.end(),
 b.begin(),
 b.end(),
 [] (char x, char y)
 { return std::tolower(x) == std::tolower(y); });
// Returns a pair with the iterators where the string first mismatched
auto position = std::mismatch(a.begin(),
   a.end(),
   b.begin(),
   b.end(),
   [] (char x, char y)
   { return std::tolower(x) == std::tolower(y); });

println(result, ", ", std::distance(a.begin(), position.first));

Implementations:

template<typename I, typename J, typename Predicate>
bool eq(I i, I j, J k, J l, Predicate predicate)
 {
for ( ;i != j && k != l; ++i, ++k)
 {
if (! predicate(*i, *k)) return false;
 }
return i == j && k == l;
 }
template<typename I, typename J, typename Predicate>
std::pair<I, J> mism(I i, I j, J k, J l, Predicate predicate)
 {
for ( ;i != j && k != l; ++i, ++k)
 {
if (! predicate(*i, *k)) break;
 }
return {i, k};

}
