# Logic and Arithmetic Operations

## Data Sizes

- A **byte** is 1 byte.
- A **word** is 2 bytes.
- A **double word** is 4 bytes.
- A **quad word** is 8 bytes.

Assembly instructions can have suffixes to refer these sizes:

- `b` for byte
- `w` for word
- `l` for double word
- `q` for quad word

So, combining these with c:
- char -> b -> 1 -> Byte
- short -> w -> 2 -> Word
- int -> l -> 4 -> Double Word
- long -> q -> 8 -> Quad Word

## Register Sizes

- %rax: 64 bits
- %eax: 32 bits (32 bits of rax)
- %ax: 16 bits (16 bits of eax)
- %al: 8 bits (8 bits of ax)

(works kind of the same for all registers)

For the new 8-bit registers, the suffixes are `b` and `w`:
- %r8b: 8 bits
- %r8w: 16 bits

**Some registers take on special responsibilities during program execution.**

- %rax stores the return value
- %rdi stores the first parameter of a function
- %rsi stores the second parameter of a function
- %rdx stores the third parameter of a function
- %rip stores the address of the next instruction to be executed
- %rsp stores the address of the top of the stack

## mov Variants

mov can take suffixes to specify the size of the data to be moved.
*movb, movw, movl, movq*

- mov only updates the spesific register bytes or memeory location indicated.
- **Exception: movl will also set high order (to the left) 4 bytes to 0.**

Also there is a **movabsq** instruction which moves a 64-bit immediate value into a register. (a constant value, only as a source)

- The regular movq instruction can only take at most 32 bit immediates

### movz and movs

- Two different move instructions to copy a smaller source to a larger destination.

- movz: fills the remaining bytes with zeros
- movs: fills the remaining bytes with the sign-extension.

For these two instructions, source must a memory address or register, and destination must be a register.

```
movsbw
movsbl
...
movslq
cltq -> sign-extend %eax to %rax
```

## The lea instruction

The **lea** instruction copies an "effective address" from one place to another

```nasm
lea stc,dest
```

`lea 6(%rax), %rdx` -> copy 6 + (%rax) into %rdx (does not actually load the value at that address)

**mov copies data at the address to the source destination, lea copies the address itself.**

## Unary instructions

```asm
inc D -> D + 1  Increment
dec D -> D - 1  Decrement
neg D -> -D     Negate
not D -> ~D     Complement
```
```asm
incq 16(%rax) -> increment the data at 16(%rax) (in the memory)
dec %rdx -> decrement the value in %rdx
not %rcx
```

## Binary instructions

```asm
add S,D  D = D + S
sub S,D  D = D - S
imul S,D D = D * S
xor S,D  D = D ^ S
and S,D  D = D & S
or S,D   D = D | S
```

```
addq %rcx, (%rax) -> the data at the memory (%rax) becomes %rcx + the data at the memory (%rax)
xorq $16, (%rax, %rdx, 8)
```

## Large Multiplication

- Multiplying 64-bit numbers can produce 128-bit results, how does x86-64 support this with only 64-bit registers?
- If you store two operands to **imul**, it multiplies them together and truncates until it fits in a 64-bit register.
    `imul S, D D <- D * S`

- If you specify one operand, it multiplies that by %rax, and splits the product across 2 registers. It puts the high order bits in %rdx, and the low order bits in %rax.
    `imul S S <- S * %rax`

## Division and Remainder

divident / divisor = quotient + remainder

```
idivq S -> signed
divq S -> unsigned
```

- The high order 64-bits of the divident are in %rdx, the low order bits are in %rax. The divisor is the operand of the instruction.

- The quoitiend is in %rax, the remainder is in %rdx.

- Most division only uses 64-bit dividents. The `cqto` instruction sign-extends 64-bit value in %rax into %rdx to fill both registers with the divident as the divison expects.

## Shift instructions

k: shift amound
D: destination

```
sal k,D (shift left)
sar k,D (shift right, arithmetic)
shr k,D (shift right, logical)
shl k,D (shift left, same as sal)
```

When using %cl, the width of what you are shifting determines what portion %cl is used. (also you can only use %cl register or an immediate value)

shlb shifts by 7 bits, shlw shifts by 15 bits if %cl = 0xff.

```
shll $3, (%rax)
shrl %cl, (%rax, %rdx, 8)
```

We can use [compiler explorer](godbolt.com).

All instructions:
![All istructions](http://minnie.tuhs.org/CompArch/Lectures/Figs/mipsasmtable.jpg)

All registers:
![All registers](https://miro.medium.com/max/1150/1*4ipwUzIWd4eqUvcEmZ5tMQ.png)