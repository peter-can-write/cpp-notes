# iomanip

#include <iomanip>

// sets the number of digits to x, integers that have more digits are
left alone, floating point numbers

// are either rounded if x > than total number of digits (before . and
after) or else the whole number is shown in scientific notation

<< setprecision(x) <<

// setw (set width), specifies how wide the input should be at least,
inputs that have a length greater

// than the specified values are left alone

<< setw(x) <<

// set alignment in accordance with setw, left to have the padding on
the right and vice versa

<< setw(x) << left <<

// sets the character that setw pads with, default is a space (if no
setfill given)

<< setw(x) << setfill(char)<<

// 1) sets universal stream precision to x and 2) returns previous
stream precision

int previous = cout.precision(x)

char fillchar = cout.fill(char)

int widt = cout.width(x)

// sets base to x e.g. 10,8,16,2

<< setbase(x) <<


