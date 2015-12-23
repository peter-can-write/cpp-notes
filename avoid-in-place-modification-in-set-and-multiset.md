# avoid-in-place-modification-in-set-and-multiset

std::sets return const_iterators, always. its iterator is a typedef for
const_iterator, so you can’t modify a value. However, you may have the
situation where you’d like to modify an attribute of an item stored
inside the set that does not actually change its ordering. In that case,
you should move-construct a new value, erase the old one with your
iterator and re-insert. You can also const_cast, but that’s unsafe and
unintuitive.

struct Weather
 {
int day;
std::string description;
 };
int main(int argc, char * argv[])
 {
auto less = [&] (const Weather& a, const Weather& b)
 { return a.day < b.day; };
// want your weather sorted by days
std::set<Weather, decltype(less)> days(less);
 days.insert({0, "sunny"});
 days.insert({1, "rainy"});
for (const auto& i : days) print(i.description); // sunny rainy
// it was really more cloudy that day, not that much sun
auto day = days.begin();
// error, not allowed for const reference
//day->description = "cloudy";
// bad solution, don't use casts
//const_cast<Weather&>(*day).description = "cloudy";
Weather temp = std::move(*day);
 temp.description = "cloudy";
 day = days.erase(day);
 days.insert(day, temp);
for (const auto& i : days) print(i.description); // cloudy rainy

}
