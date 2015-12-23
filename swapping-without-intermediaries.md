# swapping-without-intermediaries

There are two methods of swapping two values a and b without
intermediaries:

1.  Using addition
2.  Using XOR/bit-manipulation

In both methods, the idea is to store b in a, then take b out of this
commong value, leaving a (the new b), then taking the new b (the old a)
out of the common value, leaving the old b for the new a.

    int x = 5;
    int y = 10;

    //Unsequenced: x -= y = (x += y) - y;
    x += y;
    y = x - y;
    x -= y;

    // Unsequenced: x ^= y ^= x ^= y;
    x ^= y;
    y = x ^ y;
    x ^= y;

    // After swapping twice x should be 5 and y should be 10
    print::ln(x, y);
