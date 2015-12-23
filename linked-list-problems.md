# linked-list-problems

~~Edit~~



LINKED-LIST PROBLEMS


There are two important techniques for solving linked-list problems:

1.  The _Runner_ Technique

For the runner technique, which can be used to solve many
linked-list-related algorithm problems, one uses two pointers to
traverse the linked-list: a slower pointer [] (the turtle) and a
“faster” pointer [] (the hare). The faster pointer is either ahead by a
fixed amount of nodes, or hops more nodes than the slower pointer per
iteration. For example, to determine whether or not a linked-list
contains a cycle, one would have one pointer – the _turtle_ – hop one
node per iteration, and another, faster, pointer – the _hare_ – hop two
nodes per iteration. If the list contains a cycle, the faster pointer
would catch up with the slower one because it moves away one spot more
from the slower pointer, per iteration.

Another good application: moving an iterator into the middle.

Approach 1: First find the size [] of the list by iterating through the
whole thing. Then iterate only [] steps with another pointer from the
start.

Approach 2: Have [] move one step and [] two steps at a time. When []
reaches the end, [] is in the middle.

Also applicable for other ratios, e.g. moving [] into [] by moving []
three steps at a time towards the end.

2.  The _Recursive_ Technique

Recursion may also often yield efficient solutions. For example, to
check whether a linked-list is a palindrome, one move as far as possible
into the list recursively, then at the end start to compare the last
node with the first node (passed as a reference), then go back one
stack, compare this node with the second node (first->next) and so on.

Note that recursion is a great way to move backwards in a singly-linked
list.

Note also that recursion always requires [] extra space for [] stacks of
recursion.

Moreover, it is very important to ASK YOUR INTERVIEWER IF THE LISTS ARE
SINGLY OR DOUBLY-LINKED!

Problems:


Remove duplicates from a linked-list.

Solution: Use a hash-table (or set in general).

Further problem: You can’t use any extra space

Solution: [] search (but [] extra space).


Find the []-th to last element in a linked-list.

Solution 1: Use recursion to move to the back of the list and pass in a
counter, decrementing the counter when going up, then return the value
when the counter reaches zero.

Solution 2: Keep two pointers [] and [], where [] is [] steps ahead of
[]. Iterate through the list with both. When [] reaches the end, [] is
at [] positions before that.


Implement an algorithm to delete an element in the middle of the list, given only the element itself (and no other pointers).

You can’t return the next pointer or anything, you have to manipulate
the pointers as they are.

SOLUTION 1 (Bad): Move the values of all nodes to the right one to the
left.

SOLUTION 2 (Good): Move the value of the next node [] into the node to
delete [], then connect p_1.next with p_2.next and delete [] (it was
replaced with []).


Partition a linked-list around some value x.

SOLUTION 1: Keep two iterators i and j, have both at the head-node
initially. Iterate while j != end. If the value at j is greater than x,
increment only j, leaving i put. If, on the other hand, j is less than
x, swap i’s value with j’s value (so that the greater value is swapped
with the lesser one). Then if i == j at that point (i.e. if they weren’t
actually separated), increment both, else set i to j and increment j (so
that i is at the last value that was greater again).

SOLUTION 2: If stability is needed (i.e. retain the relative ordering of
values), keep a new linked-list for the values less than x and another
linked-list for the values greater. Iterate through the old list,
attaching nodes to the respective list. At the end, splice the two.

SOLUTION 3: If no stability needed, keep only one linked list and a head
and tail pointer. Insert nodes with values less than x at the head and
append nodes with values greater x at the end.

Solution 4: Three-way partitioning without the end. Have a iterator
‘left' before the iterator. If the iterator points to a value that is
greater, increment it but not left (‘left’ will always point to the
first value larger than the pivot). If the value at the iterator is less
than the pivot, swap the data at ‘left’ and the iterator. LOL.

template<typename Node, typename T>

void partition_list(Node* node, const T& pivot)

{
if (! node || ! node->next) return;
for (auto left = node; node; node = node->next)
 {
if (node->data < pivot)
 {
std::swap(left->data, node->data);
 left = left->next;
 }
 }

}


You have two numbers represented as linked-lists, where each node is a single digit of a number, in reverse order (i.e. smaller digits to the left). Write a function that adds the two lists.

_Example_: 123 + 4567

3->2->1
 +
 7->6->5->4

SOLUTION: Create a new sum linked-list, iterate over the two lists and
just carry over any rest from the addition.

Further problem: The lists are in forward order (i.e. abnormal to the order of magnitude of numbers).

_Example_: 123 + 4567

1->2->3
 +
 4->5->6->7

Solution: Use recursion (again: best way to go backwards in
singly-linked lists). Have to be careful about the lists not being of
equal length. Just go as far as possible into either list, if no longer
can go further with the one list but still can for the other, the other
list is just longer (more significant digits).


Find the starting point of a loop in a linked-list.

SOLUTION: This solution uses the _runner_ method. Its explanation should
be broken into three steps:

1.  Determining whether or not there is a cycle.

    First, it should be discussed how to determine whether or not there
    is a cycle in the list. For this, the runner-method would be
    used, i.e. have one iterator move at speed of 1 and another at a
    speed of 2 steps per iteration. If there is a loop, the second,
    faster iterator will catch up with the first. It is important to
    note here that when the first iterator [] (slower) is at position
    [], the second iterator is always twice as far, i.e. at position [].
    Moreover, it should be understood that if [] were to stop, [] would
    get closer at a rate of [].

2.  Determining where the collision point happened.

    When iterator [] enters the loop, it is [] positions into the list.
    Therefore, [] is [] positions into the loop. More precisely, because
    the length of the list may be much larger than the length of the
    loop such that [] may have looped many times already when [] arrives
    at the start of the loop, [] is really [] steps into the loop, where
    [] is the length of the loop. The distance between [] and [] is thus
    [] in the backward direction and [] in the forward direction, when
    [] enters the loop. When in the loop, as noted previously, [] will
    approach [] at a rate of one index per iteration. Because the two
    iterators are distanced [] steps apart, it will take [] steps for
    the hare to catch the turtle. This _collision point_ must thus be []
    indices into the loop, because at the same time [] moved []
    indices further. By counting indices while iterating, math could be
    done to already now compute the loop start.

3.  Finding the loop start without counting.

    First take the case that [] where [] such that [] (i.e. modulo
    operation has had no effect yet). Then it should again be understood
    that [] (and []) are [] positions into the loop. The distance []
    must travel to the start of the loop is thus []. This is the same
    distance as from the start of the list to the start of the loop.
    Therefore, to find the loop start, just have one iterator move from
    the start of the list forward and also have [] move forward. The two
    will collide at the loop start. If [], this is still true, just []
    might loop a few times before colliding with that other iterator.

    template<typename T>
    struct Node
    {
        Node(const T& value, Node* node = nullptr)
        : next(node)
        , data(value)
        { }

        Node* next;

        T data;
    };

    template<typename T>
    Node<T>* find_loop_start(Node<T>* head)
    {
        using node_t = Node<T>;

        auto turtle = head;
        auto hare = head;

        do
        {
            if (! hare->next) return nullptr;

            turtle = turtle->next;

            hare = hare->next->next;
        }

        while (hare != turtle);

        while (head != turtle)
        {
            head = head->next;
            turtle = turtle->next;
        }

        return head;
    }

MIND = BLOWN.


Implement a function to check if a linked-list is a palindrome.

SOLUTION 1: (If we’re allowed to modify the list) In moving to the
middle of the list, reverse the left half. Then one pointer will be in
the middle. Have another pointer at the left end of the now reversed
half. Iterate while the second pointer (which was at the middle) is at
the end, checking if the elements are the same.

SOLUTION 2: (If not allowed to modify the list), move an iterator into
the middle using the _runner_ technique. Then pass to a recursive
function that iterator i and an iterator/pointer j to the first node.
When reaching null with the i (which started at middle), bottom out.
Then, while unwinding the stacks, compare i to j, i.e. the last node to
the first node, and before unwinding one further stack, set j to
j->next, such that when that stack unwinds one more, i will be at the
second to last position and j at the second position.

%23%20Linked-List%20Problems%0A%0AThere%20are%20two%20important%20techniques%20for%20solving%20linked-list%20problems%3A%0A%0A1.%20The%20*Runner*%20Technique%0A%0AFor%20the%20runner%20technique%2C%20which%20can%20be%20used%20to%20solve%20many%20linked-list-related%20algorithm%20problems%2C%20one%20uses%20two%20pointers%20to%20traverse%20the%20linked-list%3A%20a%20slower%20pointer%20%24p_1%24%20%28the%20turtle%29%20and%20a%20%22faster%22%20pointer%20%24p_2%24%20%28the%20hare%29.%20The%20faster%20pointer%20is%20either%20ahead%20by%20a%20fixed%20amount%20of%20nodes%2C%20or%20hops%20more%20nodes%20than%20the%20slower%20pointer%20per%20iteration.%20For%20example%2C%20to%20determine%20whether%20or%20not%20a%20linked-list%20contains%20a%20cycle%2C%20one%20would%20have%20one%20pointer%20--%20the%20*turtle*%20--%20hop%20one%20node%20per%20iteration%2C%20and%20another%2C%20faster%2C%20pointer%20--%20the%20*hare*%20--%20hop%20two%20nodes%20per%20iteration.%20If%20the%20list%20contains%20a%20cycle%2C%20the%20faster%20pointer%20would%20catch%20up%20with%20the%20slower%20one%20because%20it%20moves%20away%20one%20spot%20more%20from%20the%20slower%20pointer%2C%20per%20iteration.%0A%0AAnother%20good%20application%3A%20moving%20an%20iterator%20into%20the%20middle.%0A%0AApproach%201%3A%20First%20find%20the%20size%20%24N%24%20of%20the%20list%20by%20iterating%20through%20the%20whole%20thing.%20Then%20iterate%20only%20%24N/2%24%20steps%20with%20another%20pointer%20from%20the%20start.%0A%0AApproach%202%3A%20Have%20%24p_1%24%20move%20one%20step%20and%20%24p_2%24%20two%20steps%20at%20a%20time.%20When%20%24p_2%24%20reaches%20the%20end%2C%20%24p_1%24%20is%20in%20the%20middle.%0A%0AAlso%20applicable%20for%20other%20ratios%2C%20e.g.%20moving%20%24p_1%24%20into%20%24%5Cfrac%7B1%7D%7B3%7DN%24%20by%20moving%20%24p_3%24%20three%20steps%20at%20a%20time%20towards%20the%20end.%0A%0A2.%20The%20*Recursive*%20Technique%0A%0ARecursion%20may%20also%20often%20yield%20efficient%20solutions.%20For%20example%2C%20to%20check%20whether%20a%20%20linked-list%20is%20a%20palindrome%2C%20one%20move%20as%20far%20as%20possible%20into%20the%20list%20recursively%2C%20then%20at%20the%20end%20start%20to%20compare%20the%20last%20node%20with%20the%20first%20node%20%28passed%20as%20a%20reference%29%2C%20then%20go%20back%20one%20stack%2C%20compare%20this%20node%20with%20the%20second%20node%20%28%60first-%3Enext%60%29%20and%20so%20on.%0A%0ANote%20that%20recursion%20is%20a%20great%20way%20to%20move%20backwards%20in%20a%20singly-linked%20list.%0A%0ANote%20also%20that%20recursion%20always%20requires%20%24O%28N%29%24%20extra%20space%20for%20%24N%24%20stacks%20of%20recursion.%0A%0AMoreover%2C%20it%20is%20very%20important%20to%20__ask%20your%20interviewer%20if%20the%20lists%20are%20singly%20or%20doubly-linked%21__%0A%0AProblems%3A%0A%0A%23%23%20Remove%20duplicates%20from%20a%20linked-list.%0A%0ASolution%3A%20Use%20a%20hash-table%20%28or%20set%20in%20general%29.%0A%0A%23%23%23%20Further%20problem%3A%20You%20can%27t%20use%20any%20extra%20space%0A%0ASolution%3A%20%24O%28N%5E2%29%24%20search%20%28but%20%24O%281%29%24%20extra%20space%29.%0A%0A%23%23%20Find%20the%20%24k%24-th%20to%20last%20element%20in%20a%20linked-list.%0A%0ASolution%201%3A%20Use%20recursion%20to%20move%20to%20the%20back%20of%20the%20list%20and%20pass%20in%20a%20counter%2C%20decrementing%20the%20counter%20when%20going%20up%2C%20then%20return%20the%20value%20when%20the%20counter%20reaches%20zero.%0A%0ASolution%202%3A%20Keep%20two%20pointers%20%24p_1%24%20and%20%24p_2%24%2C%20where%20%24p_2%24%20is%20%24k%24%20steps%20ahead%20of%20%24p_1%24.%20Iterate%20through%20the%20list%20with%20both.%20When%20%24p_2%24%20reaches%20the%20end%2C%20%24p_1%24%20is%20at%20%24N%20-%20k%24%20positions%20before%20that.%0A%0A%23%23%20Implement%20an%20algorithm%20to%20delete%20an%20element%20in%20the%20middle%20of%20the%20list%2C%20given%20only%20the%20element%20itself%20%28and%20no%20other%20pointers%29.%0A%0AYou%20can%27t%20return%20the%20next%20pointer%20or%20anything%2C%20you%20have%20to%20manipulate%20the%20pointers%20as%20they%20are.%0A%0A**Solution%201**%20%28Bad%29%3A%20Move%20the%20values%20of%20all%20nodes%20to%20the%20right%20one%20to%20the%20left.%0A%0A**Solution%202**%20%28Good%29%3A%20Move%20the%20value%20of%20the%20next%20node%20%24p_2%24%20into%20the%20node%20to%20delete%20%24p_1%24%2C%20then%20connect%20%60p_1.next%60%20with%20%60p_2.next%60%20and%20delete%20%24p_2%24%20%28it%20was%20replaced%20with%20%24p_1%24%29.%0A%0A%23%23%20Partition%20a%20linked-list%20around%20some%20value%20%60x%60.%0A%0A**Solution%201%3A**%20Keep%20two%20iterators%20%60i%60%20and%20%60j%60%2C%20have%20both%20at%20the%20head-node%20initially.%20Iterate%20while%20%60j%20%21%3D%20end%60.%20If%20the%20value%20at%20%60j%60%20is%20greater%20than%20%60x%60%2C%20increment%20only%20%60j%60%2C%20leaving%20%60i%60%20put.%20If%2C%20on%20the%20other%20hand%2C%20%60j%60%20is%20less%20than%20%60x%60%2C%20swap%20%60i%60%27s%20value%20with%20%60j%60%27s%20value%20%28so%20that%20the%20greater%20value%20is%20swapped%20with%20the%20lesser%20one%29.%20Then%20if%20%60i%20%3D%3D%20j%60%20at%20that%20point%20%28i.e.%20if%20they%20weren%27t%20actually%20separated%29%2C%20increment%20both%2C%20else%20set%20%60i%60%20to%20%60j%60%20and%20increment%20%60j%60%20%28so%20that%20%60i%60%20is%20at%20the%20last%20value%20that%20was%20greater%20again%29.%0A%0A**Solution%202**%3A%20If%20stability%20is%20needed%20%28i.e.%20retain%20the%20relative%20ordering%20of%20values%29%2C%20keep%20a%20new%20linked-list%20for%20the%20values%20less%20than%20%60x%60%20and%20another%20linked-list%20for%20the%20values%20greater.%20Iterate%20through%20the%20old%20list%2C%20attaching%20nodes%20to%20the%20respective%20list.%20At%20the%20end%2C%20splice%20the%20two.%0A%0A**Solution%203**%3A%20If%20no%20stability%20needed%2C%20keep%20only%20one%20linked%20list%20and%20a%20%60head%60%20and%20%60tail%60%20pointer.%20Insert%20nodes%20with%20values%20less%20than%20%60x%60%20at%20the%20head%20and%20append%20nodes%20with%20values%20greater%20%60x%60%20at%20the%20end.%0A%0A%23%23%20You%20have%20two%20numbers%20represented%20as%20linked-lists%2C%20where%20each%20node%20is%20a%20single%20digit%20of%20a%20number%2C%20in%20reverse%20order%20%28i.e.%20smaller%20digits%20to%20the%20left%29.%20Write%20a%20function%20that%20adds%20the%20two%20lists.%0A%0A*Example*%3A%20123%20+%204567%0A%0A%603-%3E2-%3E1%60%0A%60+%60%0A%607-%3E6-%3E5-%3E4%60%0A%0A**Solution**%3A%20Create%20a%20new%20%60sum%60%20linked-list%2C%20iterate%20over%20the%20two%20lists%20and%20just%20carry%20over%20any%20rest%20from%20the%20addition.%0A%0A%23%23%23%20Further%20problem%3A%20The%20lists%20are%20in%20forward%20order%20%28i.e.%20abnormal%20to%20the%20order%20of%20magnitude%20of%20numbers%29.%0A%0A*Example*%3A%20123%20+%204567%0A%0A%601-%3E2-%3E3%60%0A%60+%60%0A%604-%3E5-%3E6-%3E7%60%0A%0ASolution%3A%20Use%20recursion%20%28again%3A%20best%20way%20to%20go%20backwards%20in%20singly-linked%20lists%29.%20Have%20to%20be%20careful%20about%20the%20lists%20not%20being%20of%20equal%20length.%20Just%20go%20as%20far%20as%20possible%20into%20either%20list%2C%20if%20no%20longer%20can%20go%20further%20with%20the%20one%20list%20but%20still%20can%20for%20the%20other%2C%20the%20other%20list%20is%20just%20longer%20%28more%20significant%20digits%29.%0A%0A%23%23%20Find%20the%20starting%20point%20of%20a%20loop%20in%20a%20linked-list.%0A%0A**Solution**%3A%20This%20solution%20uses%20the%20*runner*%20method.%20Its%20explanation%20should%20be%20broken%20into%20three%20steps%3A%0A%0A1.%20Determining%20whether%20or%20not%20there%20is%20a%20cycle.%0A%0A%20%20%20%20First%2C%20it%20should%20be%20discussed%20how%20to%20determine%20whether%20or%20not%20there%20is%20a%20cycle%20in%20the%20list.%20For%20this%2C%20the%20runner-method%20would%20be%20used%2C%20i.e.%20have%20one%20iterator%20move%20at%20speed%20of%201%20and%20another%20at%20a%20speed%20of%202%20steps%20per%20iteration.%20If%20there%20is%20a%20loop%2C%20the%20second%2C%20faster%20iterator%20will%20catch%20up%20with%20the%20first.%20It%20is%20important%20to%20note%20here%20that%20when%20the%20first%20iterator%20%24p_1%24%20%28slower%29%20is%20at%20position%20%24k%24%2C%20the%20second%20iterator%20is%20always%20twice%20as%20far%2C%20i.e.%20at%20position%20%242k%24.%20Moreover%2C%20it%20should%20be%20understood%20that%20if%20%24p_1%24%20were%20to%20stop%2C%20%24p_2%24%20would%20get%20closer%20at%20a%20rate%20of%20%24%5Cfrac%7B1%20%5Ctext%7B%20step%7D%7D%7B%5Ctext%7Biteration%7D%7D%24.%0A%20%20%20%20%0A2.%20Determining%20where%20the%20collision%20point%20happened.%0A%0A%20%20%20%20When%20iterator%20%24p_1%24%20enters%20the%20loop%2C%20it%20is%20%24k%24%20positions%20into%20the%20list.%20Therefore%2C%20%24p_2%24%20is%20%242k%24%20positions%20into%20the%20loop.%20More%20precisely%2C%20because%20the%20length%20of%20the%20list%20may%20be%20much%20larger%20than%20the%20length%20of%20the%20loop%20such%20that%20%24p_2%24%20may%20have%20looped%20many%20times%20already%20when%20%24p_1%24%20arrives%20at%20the%20start%20of%20the%20loop%2C%20%24p_2%24%20is%20really%20%242k%20%5Ctext%7B%20mod%20%7D%20L%20%3D%20K%24%20steps%20into%20the%20loop%2C%20where%20%24L%24%20is%20the%20length%20of%20the%20loop.%20The%20distance%20between%20%24p_1%24%20and%20%24p_2%24%20is%20thus%20%24K%24%20in%20the%20backward%20direction%20and%20%24L%20-%20K%24%20in%20the%20forward%20direction%2C%20when%20%24p_1%24%20enters%20the%20loop.%20When%20in%20the%20loop%2C%20as%20noted%20previously%2C%20%24p_2%24%20will%20approach%20%24p_1%24%20at%20a%20rate%20of%20one%20index%20per%20iteration.%20Because%20the%20two%20iterators%20are%20distanced%20%24L%20-%20K%24%20steps%20apart%2C%20it%20will%20take%20%24L%20-%20K%24%20steps%20for%20the%20hare%20to%20catch%20the%20turtle.%20This%20*collision%20point*%20must%20thus%20be%20%24L%20-%20K%24%20indices%20into%20the%20loop%2C%20because%20at%20the%20same%20time%20%24p_1%24%20moved%20%24L%20-%20K%24%20indices%20further.%20By%20counting%20indices%20while%20iterating%2C%20math%20could%20be%20done%20to%20already%20now%20compute%20the%20loop%20start.%0A%0A3.%20Finding%20the%20loop%20start%20without%20counting.%0A%0A%20%20%20%20First%20take%20the%20case%20that%20%24L%20%3E%20k%24%20where%20%24k%24%20such%20that%20%24K%20%3D%20k%24%20%28i.e.%20modulo%20operation%20has%20had%20no%20effect%20yet%29.%20Then%20it%20should%20again%20be%20understood%20that%20%24p_1%24%20%28and%20%24p_2%24%29%20are%20%24L%20-%20k%24%20positions%20into%20the%20loop.%20The%20distance%20%24p_1%24%20must%20travel%20to%20the%20start%20of%20the%20loop%20is%20thus%20%24k%24.%20This%20is%20the%20same%20distance%20as%20from%20the%20start%20of%20the%20list%20to%20the%20start%20of%20the%20loop.%20Therefore%2C%20to%20find%20the%20loop%20start%2C%20just%20have%20one%20iterator%20move%20from%20the%20start%20of%20the%20list%20forward%20and%20also%20have%20%24p_1%24%20move%20forward.%20The%20two%20will%20collide%20at%20the%20loop%20start.%20If%20%24L%20%3C%20k%24%2C%20this%20is%20still%20true%2C%20just%20%24p_1%24%20might%20loop%20a%20few%20times%20before%20colliding%20with%20that%20other%20iterator.%0A%0A%60%60%60C++%0Atemplate%3Ctypename%20T%3E%0Astruct%20Node%0A%7B%0A%09Node%28const%20T%26%20value%2C%20Node*%20node%20%3D%20nullptr%29%0A%09%3A%20next%28node%29%0A%09%2C%20data%28value%29%0A%09%7B%20%7D%0A%09%0A%09Node*%20next%3B%0A%09%0A%09T%20data%3B%0A%7D%3B%0A%0Atemplate%3Ctypename%20T%3E%0ANode%3CT%3E*%20find_loop_start%28Node%3CT%3E*%20head%29%0A%7B%0A%09using%20node_t%20%3D%20Node%3CT%3E%3B%0A%09%0A%09auto%20turtle%20%3D%20head%3B%0A%09auto%20hare%20%3D%20head%3B%0A%09%0A%09do%0A%09%7B%0A%09%09if%20%28%21%20hare-%3Enext%29%20return%20nullptr%3B%0A%09%09%0A%09%09turtle%20%3D%20turtle-%3Enext%3B%0A%09%09%0A%09%09hare%20%3D%20hare-%3Enext-%3Enext%3B%0A%09%7D%0A%09%0A%09while%20%28hare%20%21%3D%20turtle%29%3B%0A%09%0A%09while%20%28head%20%21%3D%20turtle%29%0A%09%7B%0A%09%09head%20%3D%20head-%3Enext%3B%0A%09%09turtle%20%3D%20turtle-%3Enext%3B%0A%09%7D%0A%09%0A%09return%20head%3B%0A%7D%0A%60%60%60%0A%0A**Mind%20%3D%20blown.**%0A%0A%23%23%20Implement%20a%20function%20to%20check%20if%20a%20linked-list%20is%20a%20palindrome.%0A%0A**Solution%201**%3A%20%28If%20we%27re%20allowed%20to%20modify%20the%20list%29%20In%20moving%20to%20the%20middle%20of%20the%20list%2C%20reverse%20the%20left%20half.%20Then%20one%20pointer%20will%20be%20in%20the%20middle.%20Have%20another%20pointer%20at%20the%20left%20end%20of%20the%20now%20reversed%20half.%20Iterate%20while%20the%20second%20pointer%20%28which%20was%20at%20the%20middle%29%20is%20at%20the%20end%2C%20checking%20if%20the%20elements%20are%20the%20same.%0A%0A**Solution%202**%3A%20%28If%20not%20allowed%20to%20modify%20the%20list%29%2C%20move%20an%20iterator%20into%20the%20middle%20using%20the%20*runner*%20technique.%20Then%20pass%20to%20a%20recursive%20function%20that%20iterator%20%60i%60%20and%20an%20iterator/pointer%20%60j%60%20to%20the%20first%20node.%20When%20reaching%20%60null%60%20with%20the%20%60i%60%20%28which%20started%20at%20middle%29%2C%20bottom%20out.%20Then%2C%20while%20unwinding%20the%20stacks%2C%20compare%20%60i%60%20to%20%60j%60%2C%20i.e.%20the%20last%20node%20to%20the%20first%20node%2C%20and%20before%20unwinding%20one%20further%20stack%2C%20set%20%60j%60%20to%20%60j-%3Enext%60%2C%20such%20that%20when%20that%20stack%20unwinds%20one%20more%2C%20%60i%60%20will%20be%20at%20the%20second%20to%20last%20position%20and%20%60j%60%20at%20the%20second%20position.%0A

