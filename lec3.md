# Bitwise Operations


- All data is stored in memory in binary.
- When we declare a variable, it is stored in binary.

```c
int x = 5; // actually 0b0...0101 in binary
```
## Casting and combining types

Comparing signed and unsigned integers implicitly casts the signed argument to unsigned, and then does the comparison!

```c
0 == 0U     //Unsigned  1   correct
-1 < 0      //Signed    1   correct
-1 < 0U     //Unsigned  0   not correct
```

## Expanding bit representations

- Sometimes we want to convert between two integers of different sizes. (short to int or int to long).
- Usually smaller -> bigger expension.
- For unsigned values, just add leading zeros to the representation.
- For signed values, fill with leading 0's or 1's depending on sign.

**When comparing different types, smaller type will be expanded to the bigger type.**

```c
unsigned short s = 4; // s = 0000 0000 0000 0100b (2 bytes)
unsigned int i = s; // i = 0000 0000 0000 0000 0000 0000 0000 0100b

short s = -4; // s = 1111 1111 1111 1100b
int i = s;    // i = 1111 1111 ...  1100b

```

**For truncation, discard the more significant bits**

```c
int x = 53191; // 0000 0000 0000 0000 1100 1111 1100 0111
short sx = x; // 1100 1111 1100 0111 -12345
int y = sx;   // 1111 1111 1111 1111 1100 1111 1100 0111    y is negative!  -12345
```

## The sizeof Operator

```c
long sizeof(type);

long x = sizeof(int);
```

## Byte ordering

*How are bytes within a multi-byte word ordered in memory?*
- Conventions
    - Big Endian: Sun, PPC MAC, Internet
        - least significant byte has the highest adress
    - Little Endian: x64, ARM
        - least significant byte has the lowest address

- Example
    - Variable x has a 4-byte value of 0x01234567
    - Adress given by &x is 0x100
        Big Endian: 01 23 45 67 where 0x100 has 01
        Small Endian: 67 45 23 01 where 0x100 has 67

## ASCII
ASCII is an encoding from common characters to bit representations.
'A' is 0x41

## Bitwise operators

- Arithmetic operators: +, -, *, /, %
- Comparison operators: ==, !=, <, >, <=, >=
- Logical operators: &&, ||, !!

## AND (&)

## OR (|)

## NOT(~)

## XOR (^)

```
 0110
&1100

 0100

 0110
|1100
 1110

 0110
^1100
 1010

~1100
 0011
```

These operations are different than logical operations.

## Bitmasks

Lets represent current courses taken using a char.
```
0 0 1 0 0 0 1 1
```
*How to find the union of two sets?*

Use bitwise OR
```
0 0 1 0 0 0 1 1
0 1 1 0 0 0 0 1
|
0 1 1 0 0 0 1 1
```

Use bitwise AND for intersection


*How to update the bit representation to to change a bit?*

```
0 0 1 0 0 0 1 1
0 0 0 0 1 0 0 0
|
0 0 1 0 1 0 1 1
```

```c
#define COMP100 0x1
#define COMP132 0x2
#define COMP106 0x4
...

char myClasses = ...;
myClasses = myClasses | COMP132; //add COMP132
myClasses |= COMP132; //Same thing with the previous line
```
What if we want to drop COMP106?

```
0 0 1 0 0 0 1 1
1 1 0 1 1 1 1 1 // ~COMP132
&
0 0 0 0 0 0 1 1
```

```c
myClasses &= ~COMP132;
```

How to check if a student has taken a class?

```
0 0 1 0 0 0 1 1
0 0 1 0 0 0 0 0 // 1COMP132
&
0 0 1 0 0 0 0 0 // true
```

```c
if(myClasses & COMP132){
//Taken COMP132
...
}
```

How to check if we haven't taken COMP132
```c
if(!(myClasses & COMP132)){
//Not taken COMP132
...
}

if((myClasses & COMP132) ^ COMP132){
//Not taken COMP132
...
}
```


Example: Getting the lowest byte in an integer

```c
int j = ...;
int k = j & 0xff;
```
## Left Shift (<<)
Most right bits will be filled with 0's

## Right Shift (>>)
Like left shift but for signed integers fill with sign bit.

Logical right shift, fill new bits with 0's.
Arithmatic right shift, fill with sign bit.
Most machines use the arithmatic one.

**Operation precedence can be tricky!**
```c
1<<2 + 3 << 4 = 1 << (2 + 3) << 4
```
**Use paranthesis to be sure!**

```c
(1 << 2) + (3 << 4)
```

## Bit operation pitfalls

```c
long num = 1 << 32; // does not work
long num = 1L << 32; //does work
```
All number literals are assumed to be integers in the code!
