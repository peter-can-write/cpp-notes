# prefer-member-functions-to-algorithms-with-the-same-name

All associative containers provide the following methods, which perform
the same function as the equally-named standard algorithms:

-   find
-   count
-   lower_bound
-   upper_bound
-   equal_range

At the same time, lists provide the following methods with the same
names as standard algorithms:

-   remove
-   remove_if
-   unique
-   sort
-   merge
-   reverse

You should always prefer these methods over the appropriate standard
algorithms, because (1) they are usually more efficient as they are
specialized for this type and do not have to provide general behavior
for any range of iterators. Note also that (2) they may have different
behavior than standard-algorithms.

Regarding (1):

Imagine that you have a std::set of a million integers and you want to
find a specific value x. These are your options:

std::set<std::size_t> s;
/* Fill ... */
auto itr = std::find(s.begin(), s.end(), x);
if (itr != s.end()) /* ... .*/;
 itr = s.find(x);

if (itr != s.end()) /* ... */;

The first, standard method will look at A MILLION VALUES in the worst
case as it does a O(N) linear search across the container. At the same
time, std::set::find() will perform AROUND 20 lookups in O(lg N) time,
as it, knowing that it is an ordered tree structure (red-black) can
perform a simple binary search. The standard algorithm must also support
unsorted sequences and thus cannot do a binary search.

Note also that for std::map and std::multimap, std::count would use
equality and compare pairs, i.e. key and value, std::map::count and
std::multimap::count consider only keys and use EQUIVALENCE, the proper
way to assess “sameness” for associative containers:

using map_t = std::multimap<std::string, std::size_t>;
map_t m = {{"a", 1}, {"a", 2}};
// standard-algorithm, has to look for a pair because dereferncing
// an associative container's iterators yields std::pairs
auto count = std::count(m.begin(), m.end(), map_t::value_type("a", 1));
println(count);
// However you could do this too nowadays
 count = std::count_if(m.begin(), m.end(),
   [] (const std::pair<std::string, std::size_t>& p)
   { return p.first == "a"; });
 println(count);
// But the member algorithm will really only look at keys and also
// use equivalence, i.e. !(a<b) && !(b<a), rather than equality, i.e.
// a == b, which makes more sense for maps if you passed them a
different
// comparision function
// Again also very much shorter
 count = m.count("a");
// Why the above if fucked for std::map, because you can't have more
// than one key-value pair in a map anyway, since it doesn't
// allow duplicates. WORKS FOR STD::MULTIMAP, THOUGH!

println(count);

Regarding (2):

While std::remove and std::remove_if and std::unique cannot actually
modify the range that they are working on because they do not have
access to the container and its, for example, erase() method, the
variants of those algorithms for std::list can:

std::list<std::size_t> l = {1, 2, 2, 2, 3, 4, 5};
// Using std::remove, which can only re-arrange things
// Also less efficient for list because it has to copy
// values while the list methods can swap pointers
 l.erase(std::remove(l.begin(), l.end(), 2), l.end());
// member algorithm, also much shorter, and more efficient

l.remove(2);

 

   
