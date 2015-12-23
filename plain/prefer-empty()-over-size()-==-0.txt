# prefer-empty()-over-size()-==-0

All STL containers guarantee that their empty() member executes in
constant time, while std::list may not store an internal size member, so
calling size() on a list may require O(n) as the list must traverse and
count each node. This is like so, so that std::list can ensure constant
time for std::list::splice(), which would otherwise require counting the
new elements, thus not making splice constant time anymore, even though
size() would then be constant time.
