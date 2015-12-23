# slicing-raw-arrays

Slice an array in C++ by setting a pointer to the memory address of an
element in it.

int arr[] =  {1,2,3,4,5}

int * subarr = &arr[2] // {3,4,5}

or

int* subarr = arr + 2;

Will share same memory with arr, though.


