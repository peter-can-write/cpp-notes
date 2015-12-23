# unicode

-   It can represent all 1,114,112 Unicode characters.
-   Most C code that deals with strings on a byte-by-byte basis still
    works, since UTF-8 is fully compatible with 7-bit ASCII.
-   Characters usually require fewer than four bytes.
-   STRING SORT ORDER IS PRESERVED. IN OTHER WORDS, SORTING UTF-8
    STRINGS PER-BYTE YIELDS THE SAME ORDER AS SORTING THEM PER-CHARACTER
    BY LOGICAL UNICODE VALUE.
-   A missing or corrupt byte in transmission can only affect a single
    character—you can always find the start of the sequence for the next
    character just by scanning a couple bytes.
-   There are no byte-order/endianness issues, since UTF-8 data is a
    byte stream.

MULTIBYTE-STRING: A string with an encoding that _allows_ more character
to be stored with more than one byte.

WIDE-CHARACTER: A string where each character is the same-size (usually
32-bit integer) and represents a unicode code-point.


