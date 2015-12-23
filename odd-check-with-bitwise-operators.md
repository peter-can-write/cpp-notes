# odd-check-with-bitwise-operators

n & 1 // true if odd, else false

int n = 33;

int m = 12;

if ( n & 1) // odd, because first bit of odd number is set, thus ANDing
with 0b00000001 will yield 1

if (m & 1) // not odd, because 0b00001100 & 0b00000001 = 0b00000000,
which is 0 (false)


