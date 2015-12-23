# associative-container’s-new-values

New values in associative containers are value-initialized, not
default-initialized! I.e. this is safe:

std::map<std::string, int> map;

map[“asdf”]++;

map[“asdf”] will now be 1, i.e. 0 at value-initialization and then
incremented. It will not hold a garbage value, as you thought.
