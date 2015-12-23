# prefer-insert-over-operator[]-for-maps

You should use std::map::insert rather than std::map::operator[] when
inserting new items, because calling operator[] for insertion (not for
update) means first default-constructing the value type, then assigning,
whereas insert() results in only one direct construction and insertion.
At the same time, updating is better with operator[] than insert,
because operator[] will return a reference, whereas insert() would
result in construction of a temporary object and an assignment.

For C++17 it is planned that there will be a std::map::insert_or_assign
method, but otherwise, it could look like so:

template<class Map, typename Key, typename Value>
typename Map::iterator insert_or_update(Map& map, const Key& key, const
Value& value)
 {
// Lower bound performs a logarithmic lookup of
// the key, if it finds it, the iterator returned
// points to the pair, if the key is not yet in the
// map, the iterator points to where *it should/would be*
auto itr = map.lower_bound(key);
// If the key is already in the map
if (itr != map.end())
 {
// simply update
 itr->second = value;
return itr;
 }
// else call the efficient insert method for new insertion
// the iterator then acts as the hint, i.e. the insertion
// operation is constant regarding lookup (the pair is
// inserted just there)
else return map.insert(itr, {key, value});
 }
int main(int argc, char * argv[])
 {
std::map<int,int> map;
// Bad:
// map[1] looks to see if 1 is already in the container,
// if not, the key is inserted and its value is default-
// constructed. Then, after unnecessary construction, the
// value is returned by reference by operator[] and then
// the assignment operator of the value type is called.
// this means the first default-construction was unnecessary
 map[1] = 4;
// Good:
// Only one single construction of the pair
// and both the key and the value
 map.insert({2, 5});
// Bad:
// Pair is constructed, then the pair whose key is 1
// is looked for, then the values are assigned, then
// the pair is destructed. map::insert looks for the
// value and if it finds it, returns a pair whose
// first value is the iterator and the second value
// is whether or not there really was an insertion,
// so first is the iterator to the pair here, and
// first->second is the value of the pair
 map.insert({1, 10}).first->second = 10;
// Good:
// The value is returned by reference and a direct
// assignment takes place, no temporary object
 map[2] = 20;
// Better:
insert_or_update(map, 5, 6);
insert_or_update(map, 5, 7);

        for (const auto& i : map) print(i.first, " ", i.second);

}


