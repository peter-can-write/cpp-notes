# using-find,-equal_range-and-other-algorithms

~~Edit~~



USING find, equal_range AND OTHER ALGORITHMS


There are many ways to find or lookup an element in a sequence:

    * `find`
    * `equal-range`
    * `binary_search`
    * `count`
    * `lower_bound`
    * `upper_bound`

However each answer a different question.


Unsorted Ranges

To search for the first occurrence of an element in an unsorted sequence
you should use std::find:

    std::vector<std::size_t> v = {1, 3, 2, 7, 5};

    auto itr = std::find(v.begin(), v.end(), 2);

    if (itr != v.end()) std::cout << "Found!";

    else std::cout << "Not found!";

If you want to know how many elements of a given value are in a
sequence, you would use std::count. You may also be tempted to use
std::count for existance check, however it is less efficient than
std::find, because for a successful search std::find would terminate
while std::count would look through the entire range.


Sorted Ranges

For a sorted range (but not a set/map), things are very different. To
check for existence, use std::binary_search:

    std::vector<std::size_t> v = {1, 2, 3, 4, 5};

    if (std::binary_search(v.begin(), v.end(), 3)) std::cout << "Found!";

    else std::cout << "Not found!";

The next part is IMPORTANT. To look for the first occurrence of the
value, do not use std::find. Also do not use std::lower_bound, but
rather std::equal_range:

    std::vector<std::size_t> v = {1, 2, 3, 4, 5};

    auto pair = std::equal_range(v.begin(), v.end(), 3);

    // Do something with pair.first
    if (pair.first != pair.second) std::cout << "Found!";

    else std::cout << "Not found!";

Why not std::lower_bound? Because you would have to first check if the
returned iterator is not the end, and then if the returned value is the
value you are looking for (since lower_bound returns the first value
_not less_, i.e. _greater or equal_ to whatever you passed it). You
would most likely do it like so:

    std::vector<std::size_t> v = {1, 2, 3, 4, 5};

    auto itr = std::lower_bound(v.begin(), v.end(), 3);

    if (itr != v.end() && *itr == 3) std::cout << "Found!";

    else std::cout << "Not found!";

However, std::lower_bound works with _equivalence_, not equality! Thus
it would have to look like so, which is too much of a hassle:

    std::vector<std::size_t> v = {1, 2, 3, 4, 5};

    auto itr = std::lower_bound(v.begin(), v.end(), 3);

    if (itr != v.end() && ! (*itr < 3) && !(3 < *itr)) std::cout << "Found!";

    else std::cout << "Not found!";

Moreover, you can also get a cheaper counting-algorithm through
std::equal_range than through the linear std::count, because the count,
i.e. the number of values matching the value you passed
std::equal_range, is equal to std::distance(pair.begin(), pair.end()).


Associative containers (set/map)

For std::set, std::mulitset, std::map and std::multimap, the main rule
is to use their member methods rather than the non-member algorithms,
i.e. std::set::count instead of std::count, for example. Same also goes
for std::list, by the way.


Putting it all together

  Your question                       Unsorted Range   Sorted Range                       std::set/std::map
  ----------------------------------- ---------------- ---------------------------------- -------------------
  Does the value exist?               std::find        std::binary_search                 member find
  Where is the first such value?      std::find        std::equal_range                   member find
  How many objects have that value?   std::count       std::equal_range + std::distance   member count

%23%20Using%20%60find%60%2C%20%60equal_range%60%20and%20other%20algorithms%0A%0AThere%20are%20many%20ways%20to%20find%20or%20lookup%20an%20element%20in%20a%20sequence%3A%0A%0A%09*%20%60find%60%0A%09*%20%60equal-range%60%0A%09*%20%60binary_search%60%0A%09*%20%60count%60%0A%09*%20%60lower_bound%60%0A%09*%20%60upper_bound%60%0A%0AHowever%20each%20answer%20a%20different%20question.%20%0A%0A%23%23%20Unsorted%20Ranges%0A%0ATo%20search%20for%20the%20first%20occurrence%20of%20an%20element%20in%20an%20unsorted%20sequence%20you%20should%20use%20%60std%3A%3Afind%60%3A%0A%0A%60%60%60C++%0Astd%3A%3Avector%3Cstd%3A%3Asize_t%3E%20v%20%3D%20%7B1%2C%203%2C%202%2C%207%2C%205%7D%3B%0A%0Aauto%20itr%20%3D%20std%3A%3Afind%28v.begin%28%29%2C%20v.end%28%29%2C%202%29%3B%0A%0Aif%20%28itr%20%21%3D%20v.end%28%29%29%20std%3A%3Acout%20%3C%3C%20%22Found%21%22%3B%0A%0Aelse%20std%3A%3Acout%20%3C%3C%20%22Not%20found%21%22%3B%0A%60%60%60%0A%0AIf%20you%20want%20to%20know%20how%20many%20elements%20of%20a%20given%20value%20are%20in%20a%20sequence%2C%20you%20would%20use%20%60std%3A%3Acount%60.%20You%20may%20also%20be%20tempted%20to%20use%20%60std%3A%3Acount%60%20for%20existance%20check%2C%20however%20it%20is%20less%20efficient%20than%20%60std%3A%3Afind%60%2C%20because%20for%20a%20successful%20search%20%60std%3A%3Afind%60%20would%20terminate%20while%20%60std%3A%3Acount%60%20would%20look%20through%20the%20entire%20range.%0A%0A%23%23%20Sorted%20Ranges%0A%0AFor%20a%20sorted%20range%20%28but%20not%20a%20set/map%29%2C%20things%20are%20very%20different.%20To%20check%20for%20existence%2C%20use%20%60std%3A%3Abinary_search%60%3A%0A%0A%60%60%60C++%0Astd%3A%3Avector%3Cstd%3A%3Asize_t%3E%20v%20%3D%20%7B1%2C%202%2C%203%2C%204%2C%205%7D%3B%0A%0Aif%20%28std%3A%3Abinary_search%28v.begin%28%29%2C%20v.end%28%29%2C%203%29%29%20std%3A%3Acout%20%3C%3C%20%22Found%21%22%3B%0A%0Aelse%20std%3A%3Acout%20%3C%3C%20%22Not%20found%21%22%3B%0A%60%60%60%0A%0AThe%20next%20part%20is%20__important__.%20To%20look%20for%20the%20first%20occurrence%20of%20the%20value%2C%20do%20not%20use%20%60std%3A%3Afind%60.%20Also%20do%20not%20use%20%60std%3A%3Alower_bound%60%2C%20but%20rather%20%60std%3A%3Aequal_range%60%3A%0A%0A%60%60%60C++%0Astd%3A%3Avector%3Cstd%3A%3Asize_t%3E%20v%20%3D%20%7B1%2C%202%2C%203%2C%204%2C%205%7D%3B%0A%0Aauto%20pair%20%3D%20std%3A%3Aequal_range%28v.begin%28%29%2C%20v.end%28%29%2C%203%29%3B%0A%0A//%20Do%20something%20with%20pair.first%0Aif%20%28pair.first%20%21%3D%20pair.second%29%20std%3A%3Acout%20%3C%3C%20%22Found%21%22%3B%0A%0Aelse%20std%3A%3Acout%20%3C%3C%20%22Not%20found%21%22%3B%0A%60%60%60%0A%0AWhy%20not%20%60std%3A%3Alower_bound%60%3F%20Because%20you%20would%20have%20to%20first%20check%20if%20the%20returned%20iterator%20is%20not%20the%20end%2C%20and%20then%20if%20the%20returned%20value%20is%20the%20value%20you%20are%20looking%20for%20%28since%20%60lower_bound%60%20returns%20the%20first%20value%20*not%20less*%2C%20i.e.%20*greater%20or%20equal*%20to%20whatever%20you%20passed%20it%29.%20You%20would%20most%20likely%20do%20it%20like%20so%3A%0A%0A%60%60%60C++%0Astd%3A%3Avector%3Cstd%3A%3Asize_t%3E%20v%20%3D%20%7B1%2C%202%2C%203%2C%204%2C%205%7D%3B%0A%0Aauto%20itr%20%3D%20std%3A%3Alower_bound%28v.begin%28%29%2C%20v.end%28%29%2C%203%29%3B%0A%0Aif%20%28itr%20%21%3D%20v.end%28%29%20%26%26%20*itr%20%3D%3D%203%29%20std%3A%3Acout%20%3C%3C%20%22Found%21%22%3B%0A%0Aelse%20std%3A%3Acout%20%3C%3C%20%22Not%20found%21%22%3B%0A%60%60%60%0A%0AHowever%2C%20std%3A%3Alower_bound%20works%20with%20*equivalence*%2C%20not%20equality%21%20Thus%20it%20would%20have%20to%20look%20like%20so%2C%20which%20is%20too%20much%20of%20a%20hassle%3A%0A%0A%60%60%60C++%0Astd%3A%3Avector%3Cstd%3A%3Asize_t%3E%20v%20%3D%20%7B1%2C%202%2C%203%2C%204%2C%205%7D%3B%0A%0Aauto%20itr%20%3D%20std%3A%3Alower_bound%28v.begin%28%29%2C%20v.end%28%29%2C%203%29%3B%0A%0Aif%20%28itr%20%21%3D%20v.end%28%29%20%26%26%20%21%20%28*itr%20%3C%203%29%20%26%26%20%21%283%20%3C%20*itr%29%29%20std%3A%3Acout%20%3C%3C%20%22Found%21%22%3B%0A%0Aelse%20std%3A%3Acout%20%3C%3C%20%22Not%20found%21%22%3B%0A%60%60%60%0A%0AMoreover%2C%20you%20can%20also%20get%20a%20cheaper%20counting-algorithm%20through%20%60std%3A%3Aequal_range%60%20than%20through%20the%20linear%20%60std%3A%3Acount%60%2C%20because%20the%20count%2C%20i.e.%20the%20number%20of%20values%20matching%20the%20value%20you%20passed%20%60std%3A%3Aequal_range%60%2C%20is%20equal%20to%20%60std%3A%3Adistance%28pair.begin%28%29%2C%20pair.end%28%29%29%60.%0A%0A%23%23%20Associative%20containers%20%28set/map%29%0A%0AFor%20%60std%3A%3Aset%60%2C%20%60std%3A%3Amulitset%60%2C%20%60std%3A%3Amap%60%20and%20%60std%3A%3Amultimap%60%2C%20the%20main%20rule%20is%20to%20use%20their%20member%20methods%20rather%20than%20the%20non-member%20algorithms%2C%20i.e.%20%60std%3A%3Aset%3A%3Acount%60%20instead%20of%20%60std%3A%3Acount%60%2C%20for%20example.%20Same%20also%20goes%20for%20%60std%3A%3Alist%60%2C%20by%20the%20way.%0A%0A%23%23%20Putting%20it%20all%20together%0A%0A%7C%20Your%20question%20%7C%20Unsorted%20Range%20%7C%20Sorted%20Range%20%7C%20%60std%3A%3Aset%60/%60std%3A%3Amap%60%7C%0A%7C---------------%7C----------------%7C--------------%7C----------------------%7C%0A%7C%20Does%20the%20value%20exist%3F%20%7C%20%60std%3A%3Afind%60%20%7C%20%60std%3A%3Abinary_search%60%20%7C%20member%20%60find%60%7C%0A%7C%20Where%20is%20the%20first%20such%20value%3F%20%7C%20%60std%3A%3Afind%60%20%7C%20%60std%3A%3Aequal_range%60%20%7C%20member%20%60find%60%7C%0A%7C%20How%20many%20objects%20have%20that%20value%3F%20%7C%20%60std%3A%3Acount%60%20%7C%20%60std%3A%3Aequal_range%60%20+%20%60std%3A%3Adistance%60%7C%20member%20%60count%60%7C

