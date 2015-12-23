# stl-containers

Do not write container-independent code! Iterator invalidation,
insertion, deletion, push_back and push_front patterns, iterator
capabilities differ widely between STL containers. Rather, ENCAPSULATE
operations and interactions and use TYPEDEF or USING= to not have to
specify the container everywhere (makes changing later easier).

Standard STL sequence containers: vector, string, deque and list

Standard STL associative containers: set, multiset, map, multimap

Standard non-STL containers: array, bitset, stack, queue

vector: default

list: frequent insertions and deletions in the middle of the sequence

deque: insertions and deletions at the beginning and end of sequence

CONTIGUOUS-MEMORY CONTAINERS store items sequentially, contiguously in
dynamically allocated chunks of memory, like an array. More than one
item is stored per chunk, so insertions and deletions effect other
elements and require them to be moved.

NODE-BASED CONTAINERS store single items in single chunks of dynamically
allocated memory and connect them via pointers. Insertion or erasing
does not affect any other nodes / elements other than the node itself
and its immediate neighbors. 

_Do you need to be able to insert a new element at an any position in
the container? _NO ASSOCIATIVE CONTAINERS

_Do you care about order?_ NO HASHED CONTAINERS

_
_

_What iterator categories are required?_ RANDOMACCESSITERATORS ->
VECTOR, DEQUE; BIDIRECTIONAL -> LIST

_Is lookup speed important?_ HASHED CONTAINERS, SORTED VECTORS,
ASSOCIATE CONTAINERS.

_Do you need to minimize iterator invalidation? _NODE-BASED CONTAINERS. 


