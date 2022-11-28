# Introduction to x86_64 Assembly

## GCC

- GCC converts human readable code into machine-readable instructions.
- C and other languages are high level abstractions. Computer's don't really understand data strucutres, variable types, etc.
- Pure machine code is binary. But we can read it in a human-readable form called assembly.
- There may be multiple assembly instructions needed to encode a single C instruction.

## Our first assembly

```c
int sum_array(int arr[], int n) {
    int sum = 0;
    for (int i = 0; i < n; i++) {
        sum += arr[i];
    }
    return sum;
}
```

```
00000000004004ed <sum_array>: (function name and location in memory)
  4004ed:	55                   	push   %rbp
  4004ee:	48 89 e5             	mov    %rsp,%rbp
  4004f1:	48 89 7d e8          	mov    %rdi,-0x18(%rbp)
  4004f5:	89 75 e4             	mov    %esi,-0x1c(%rbp)
  4004f8:	c7 45 fc 00 00 00 00 	movl   $0x0,-0x4(%rbp)
  4004ff:	c7 45 f8 00 00 00 00 	movl   $0x0,-0x8(%rbp)
  400506:	eb 1d                	jmp    400525 <sum_array+0x38>
  400508:	8b 45 f8             	mov    -0x8(%rbp),%eax
  40050b:	48 98                	cltq   
  40050d:	48 8d 14 85 00 00 00 	lea    0x0(,%rax,4),%rdx
  400514:	00 
  400515:	48 8b 45 e8          	mov    -0x18(%rbp),%rax
  400519:	48 01 d0             	add    %rdx,%rax
  40051c:	8b 00                	mov    (%rax),%eax
  40051e:	01 45 fc             	add    %eax,-0x4(%rbp)
  400521:	83 45 f8 01          	addl   $0x1,-0x8(%rbp)
  400525:	8b 45 f8             	mov    -0x8(%rbp),%eax
  400528:	3b 45 e4             	cmp    -0x1c(%rbp),%eax
  40052b:	7c db                	jl     400508 <sum_array+0x1b>
  40052d:	8b 45 fc             	mov    -0x4(%rbp),%eax
  400530:	5d                   	pop    %rbp
  400531:	c3                   	retq 
```

### Assembly Abstraction

- C abstracts away the low-level details of the machine code. It lests us work using variables, variable types and other high-level abstractions.
- Assembly code is just bytes.
- Assembly is processor-spesific.

### Registers

Special storage units located in the central processing unit (CPU).

There are 16 registers in x86_64 architecture.

```
- %rax
- %rbx
- %rcx
- %rdx
- %rsi
- %rdi
- %rbp
- %rsp
- %r8
- %r9
- %r10
- %r11
- %r12
- %r13
- %r14
- %r15
```

Operations on the CPU is much faster compared to the main memory. Registers are not located in memory.

- A register is a 64-bit space inside the processor.
- There are 16 registers with unique names.
- Registers can store some intermediate values. Data calculated or manuplated is moved to registers first. Operations are performed on registers.
- Registers also hold parameters and return values for functions.
- Registers are extremely fast
- Processor instructions mostly move data in/out of registers and performe arithmetic operations on them.

### Machine level code

- One instruction adds two numbers in registers
- One instruction transfers data from register to memory
- One instruction transfers data from memory to register

There is also ALU (Arithmetic Logic Unit) which performs arithmetic operations inside CPU.

#### Example of an assembly level abstraction of C code

```c
int sum = x + y;
```

abstraction:
- copy x into register 1
- copy y into register 2
- add register 1 to register 2
- write register 1 to memory for sum

### Instruction set architecture (ISA)

A contract between program and hardware:
- Defines which operations the CPU can perform
- data read/write
- control mechanism

Intel originally designed their instruction set in 1978
- Legacy support is a huge issue
- Originally 16-bit processors, then 32, then 64

These design choices dictated register sizes

## mov

The mov instruction copies bytes from one place to another, is similar to (`=`) operator in C.

```asm
mov src,dst
```

These src, dst can be each of:
- Immediate (constant value like a number (*only src*)) `$0x104`
- Register `%rbx`
- Memory Location (**at most one of** src, dst) `0x6005c0`

If there is a `$`, it means a constant, if not, it means a memory location.

#### Operand forms: Immediate

```asm
mov $0x104, %rax
```

#### Operand Forms: Register

```asm
mov %rax, %rbx
```

#### Operand Forms: Memory

```asm
mov 0x6005c0, %rax
```

#### Operand Forms: Indirect

```asm
mov (%rax), %rbx
```

Using paranthesis means indirect addressing. It uses the value stored in the register as memory address.

#### Operand Forms: Displacement

```asm
mov 0x8(%rax), %rbx
```

This means the value stored in the memory location 8 bytes after the value stored in the register `%rax`.

#### Operand Forms: Indexed

```asm
mov (%rax,%rbx), %rcx
```

This means the value stored in the memory location whose address is `%rax` + `%rbx`.

#### Operand Forms: Scaled Indexed

```asm
mov (,%rdx,4), %rax
```

means the memory address 4 * value stored in `%rdx`

```asm
mov 2(%rax,%rbx,4), %rcx
```
means the memory address 4 * `%rbx` + `%rax` + 2 

**The scale must be a 1, 2, 4 or 8.**

