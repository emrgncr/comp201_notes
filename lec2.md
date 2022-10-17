# Representing Integers

Demo, unexpected behaviour:
```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char** argv){
    uint8 x = 1;
    while( ... ){
    printf("x: %d\n",x);
    x++;
    }
    //will print negative x after a while
}
```

8 bits = 1 byte.
Memory is an array of bytes, byte-adressable.

Base 10:

```math
5 9 3 4
= 5\times1000 + 9\times100 + 3\times10 + 4\times1
```
Base 2:

```math
1011_2
= 1\times 2^3 + 0\times2^2 + 1 \times2^1 + 1\times 2^0
= 11_{10}
```
## Getting base 2:
1- get the largest power of 2 smaller than X, a.
repeat this process for X - 2^a

example for 6:

```
1- a = 2
2- 6 - 2^2 = 2
3- a = 1
4- 2 - 2^1 = 0
```

so

```math
6_{10} = 2^2 + 2^1 + 0 = 110_2
```

Minimum base 10 value in a single byte: 0

Maximum base 10 value in a single byte: 255 = 2^8 - 1

### Multiplying by base

```math
1450_{10} \times 10 = 14500_{10}
```

```math
1100_2 \times 2_{10} = 11000_2
```

```math
1450_{10} / 10 = 145_{10}
```

```math
1100_2 / 2_{10} = 110_2

```

## Hexadecimal

We often have numbers with 32 or 64 bits.

hexadecimal is base16.

```
0110 1010 0011 (base2)
6A3            (hexadecimal base-16)
```

Since hexadecimal can have up to 15 per digit, we map numbers 10 - 15.

```
10 - A
11 - B
12 - C
13 - D
14 - E
15 - F
```

We distinguish hexadecimal numbers by prefixing with 0x, binary with 0b.
eg.

```c
int x = 0xf5;
int y = 0b11110101;
```

# Integer Representations
**Unsigned integers:** No negative numbers.
**Signed intgegers:** Can have negative values. An extra negative bit.
**Floating point numbers:** Real numbers (kind of).

```c
int         //4 bytes
double      //8 bytes
float       //4 bytes
char        //1 bytes
char*       //8 bytes (was 4 before 64 bit pc)
short       //2 bytes
long        //8 bytes (was 4 before 64 bit pc)
```

The name 64 bit and 32 bit computer means the pointer size. 32 bit systems can store up to 4 GB RAM at most!
Now the RAM limit is 16 Exabytes. (1024^3 GB)

## Unsigned integers
Nonnegative numbers.
The range of unsigned number is 0 -> 2^w - 1, where w is the number of bits we have.

## Signed integers
Use the left most bit to indicate sign (positive - negative). This is not efficient, we lose 1 representation. A better idea is to having addition work regardless of the sign of the number.

We use two's complement.
0101 -> 1011
0011 -> 1101
0000 -> 0000

in the new table,
    +       -
0   0000    0000
1   0001    1111
2   0010    1110
...
7   0111    1001
8   ?       1000

Flip the binary representation, add 1.
100100 -> 011100

In two's complement, we represent a positive number as itself and its negatice equivalent as its two's complement.

This works also in the reverse direction.

Most significant bit still represents the sign!

## Overflow

Exceeding the maximum value of the bit representation, you continue from 0. If you go below 0, go from maximum value.

```c
//type              bytes    min    max
char x;             //1     -128    127
unsigned char y;    //1     0       256
short z;            //2
unsigned short k;   //2
int a;              //4
unsigned int b;     //4
long c;             //8
unsigned long d;    //8

INT_MIN
INT_MAX
UINT_MIX
```