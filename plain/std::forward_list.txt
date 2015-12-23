# std::forward_list

std::forward_list is a new singly-linked list container in the standard
(as opposed to std::list, which is doubly-linked). If you do not need
bidirectional iteration, use this list, as it is more space-efficient
(one pointer less).

std::forward_list<int> list;
 list.emplace_front(5);
 list.insert_after(list.begin(), 6);
// ForwardIterator

for (auto i : list) print::ln(i);
