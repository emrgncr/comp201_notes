# Representing Real numbers

**Problem:** There are infinitely many real numbers and we have limited number of bits.

*We will use fixed-bit representation. We will lot have ALL the real numbers.*

**Problem:** We cannot represent all numbers like 1/3 in base 10 or 1/10 in base 2 (0.000110011001100110011...).

## Fixed Point

Like in base 10 add binary decimal places.
```
5 9 3 4 . 2 1 6 (base10)

1 0 1 1 .  0  1   1 (base 2)
8 4 2 1   .5 .25 .125
```

Arithmatic is easy. We know exactly where the decimal point is (how much presicion we have).

**Problem:** Where to put the decimal point? Fixing the decimal point either represents very large numbers or very small.

## IEEE Floating Point

Represent in scientific notation:
```math
x\times2^y
```

With this 32-bit floats represent numbers in range 1.2 * 10^-38 to 3.4 * 10^38
**But we still don't have all the numbers!**

```
s: Sign bit (0 = positive)
y: exponent (8 bits)
x: fraction (23 bits)
```

### Exponent

```
exponent(8 bits)

11111111    RESERVED
11111110    127
11111101    126
...
00000011    -124
00000010    -125
00000001    -126
00000000    RESERVED
```

When calculating exponent, we do not use two's compliment. We get actual value of exponent by `value = binary value - 127 (bias)`

### Fraction

- Just encode whatever we want on x. But there is a trick to make the most out of the bits.

**Base 10:**
- 42.4 x 10^5 = 4.24 x 10^6
- 0.624 x 10^5 = 6.24 x 10^4

**Base 2:**
- 10.1 x 2^5 = 1.01 x 2^6
- 0.110 x 2^5 = 1.10 x 2^4

So we shift the number, in base 2, the number before the decimal point is always 1! We just don't store it.

X will always be 1.xxxxxxx...

### Skipping the numbers

When close to 0, we have good presicion. But as the number gets too large the presicion drops.

### Representing zeros

If the number is all zeros, the number is zero.

### Representing small numbers

If the exponent is all zeros, we switch into denormalized mode.

We now treat the exponent as -126 and the fraction as without the leading 1.

This allows us to represent small numbers as precisely as possible.

### Infinity

If exponent is all ones and fraction is all zeros, depending on the sign bit + - infinity.

### NaN

If exponent is all ones and fraction is nonzero, it is NaN (not a number).

## Number ranges

32 bit floating point (float)
64 bit floating point (double)

(sign exp fraction)

Single presicion    1 8  23         (32 bits)
Double presicion    1 11 52         (64 bits)
Extended presicion  1 15 63 (or 64) (80 bits)

## Tiny floating point (for learning)

8 bit: 1 4 3
exponent bias = 7

## Floating point in c

C guarantees two levels, float and double.

Conversions / Casting:
-   casting between float, double -> int:
    -   rounding towards 0, truncate fractional part
-   int -> double
    -   exact conversion as long as int has <= 53 bit word size
-   int -> float
    -   rounded according to round mode

