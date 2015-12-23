# operator-precedence

Precedence
Operator
Description
Associativity
1
::
Scope resolution
Left-to-right
2
++   --
Suffix/postfix increment and decrement
type()  type{}
Function-style cast
()
Function call
[]
Array subscripting
.
Member access
->
Member access through pointer
3
++   --
Prefix increment and decrement
Right-to-left
+   -
Unary plus and minus
!   ~
Logical NOT and bitwise NOT
(type)
C-style explicit cast
*
Indirection (dereference)
&
Address-of
sizeof
Size-of[note\ 1]
new, new[]
Dynamic memory allocation
delete, delete[]
Dynamic memory deallocation
4
.*   ->*
Pointer to member
Left-to-right
5
*   /   %
Multiplication, division, and remainder
6
+   -
Addition and subtraction
7
<<   >>
Bitwise left shift and right shift
8
<   <=
For relational operators < and ≤ respectively
>   >=
For relational operators > and ≥ respectively
9
==   !=
For relational operators = and ≠ respectively
10
&
Bitwise AND
11
^
Bitwise XOR (exclusive or)
12
|
Bitwise OR (inclusive or)
13
&&
Logical AND
14
||
Logical OR
15
?:
Ternary conditional[note\ 2]
Right-to-left
throw
throw operator
=
Direct assignment (provided by default for C++ classes)
+=   -=
Compound assignment by sum and difference
*=   /=   %=
Compound assignment by product, quotient, and remainder
<<=   >>=
Compound assignment by bitwise left shift and right shift
&=   ^=   |=
Compound assignment by bitwise AND, XOR, and OR
16
,
Comma
Left-to-right
