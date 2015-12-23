# passing-multidimensional-arrays-to-functions

When passing multidimensional arrays to functions, you have to specify
all dimensions except the FIRST. 

void foo( int arr [ ] [ 4 ])

{ }

Since it knows the size of the columns, doing arr[2][2] inside the
function means

doing arr[ 2 * 4] [ 2 ].

It’s all SEQUENTIAL after all. 


