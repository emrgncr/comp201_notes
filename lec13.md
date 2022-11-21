# Compiling C programs

## What happens in GCC

- The Preprocessor
- The Compiler
- The Assembler
- The Linker

### Compiling a C program

```bash
gcc -g -O0 hello.c -o hello
```

Where `-g` is for debugging and `-O0` is for optimization level 0.

### The GNU Compiler Collection

Source code (.c, .cpp, .h) -> Preprocessing (Include Header, Expand Macro [#include, #define]) -> Compiler (Assembly code .s) -> Assembler (Machine code .o, .obj) -> Linking (static library .lib, .a) -> Executable


### The Preprocessor

#### Object Macros

```c
#define BUFFER_SIZE 1024

foo = (char *) malloc(BUFFER_SIZE);
```

After preprocessing, the code becomes:

```c

foo = (char *) malloc(1024);
```  

The `#define` directive can be used to set up symbolic replacements in the source.

#### Function Macros

```c
#define min(X,Y) ((X) < (Y) ? (X) : (Y))
#define twice(X) (2 * (X))

y = min(1,2)
y = twice(1 + 1)
```

After preprocessing, the code becomes:

```c
y = ((1) < (2) ? (1) : (2))
y = (2 * (1 + 1))
```

**Note**: Here, the paranteses are important. If we do not use paranteses, the code will become `y = 2 * 1 + 1;` which is equal to 3 not 4.

#### Imports

The `#include` directive just copies and pastes in the contents of the file.

header.h:

```c
char* test(void);
```

program.c:

```c
#include "header.h"

int x;
int main(int a, char** b){
    puts(test());
}
```

After preprocessing, the code becomes:

```c
char* test(void);

int x;
int main(int a, char** b){
    puts(test());
}
```

#### Demo

```bash
gcc -E -o hello.i hello.c
```

The resulting .i file is the preprocessed version of the source code.

### The Compiler

We will learn more about it in the following lectures.

The compiler parses source code and generates assembly code.

```bash
gcc -S -o hello.s hello.i
```

### The Assembler

#### Demo

```bash
as -o hello.o hello.s
```

#### ELF

ELF: Executable and Linkable Format

A standard, cross platform, os independent way of representing the object code.

```bash
readelf -e hello.o
```
It has sections:

- .text: executable code (x86 assembly)
- .data: any global or static variables
- .rodata: read only data
- .bss: all uninitialized data; global variables and static variables initialized to zero or nor explicitly initialized in source code
- .comment: comments about generated ELF

```bash
nm hello.o
```

Reads the name of functions.

### The Linker

We will speak more about this but in general:

Static linking: the machine code of external programs are copied into the executable.
Static libraries have extension `.a` in unix.

Dynamic linking: the machine code of external programs are not copied into the executable. Only an offset table is created in the executable. The OS loads the external programs into memory when the executable is run.
Dynamic libraries have extension `.so` in unix.

```bash
ld --dynamic-linker /lib64/ld-linux-x86-64.so.2 -o hello hello.o -lc --entry main
```

- --dynamic_liner is used to specify the linker we must use to load stdlib
- -lc is used to link the C standard library
- --entry main is used to specify the entry point of the program

#### Linking Multiple Files and Library

```bash
gcc -c myfile1.c
gcc -c myfile2.c
gcc -g myfile1.o myfile2.o -lm -o myprogram
```
`-lm` is used to link the math library.

## Make and Makefiles

A tool which controls the generation of executables.

**Example:**
- Target: simple
- Ingredients: simple.c
- Recipe: gcc -o simple simple.c

**Makefile Demo**

```makefile
    simple: simple.c
        gcc -o simple simple.c
```

Make is not a shorter GCC!
It is more general, we can have any targets and any shell commands!

```makefile
    clean:
        rm -f simple
```

### Advantages of Make

- General: not just compiling C programs
- Fast: Only rebuilds what is necessary
- Shareable: End user just uses `make`

### Makefiles

- A list of rules
- Rule: Tells Make the commands to build a target from 0 or more dependencies

```makefile
    target: dependencies
        commands
```

Makefiles must be indented with \t (tab) characters.

If dependencies have changed or don't exist, rebuilds them. Then executes these commands.

#### Toy Example - Far

Assume we want to buid a tool called Far.
- Target: Far (Executable)
- Sources: Far.c Far.h vector.c vector.h

- Target: Far
- Ingredients: Far.o vector.o
- Recipe: gcc -o Far Far.o vector.o

Makefile:

```makefile
CC=gcc
CFLAGS=-g -std=c99 -pedantic -Wall
all: Far
Far: Far.o vector.o
    ${CC} ${CFLAGS} $^ -o $@

Far.o: Far.c Far.h vector.h
    ${CC} ${CFLAGS} -c Far.c

vector.o: vector.c vector.h
    ${CC} ${CFLAGS} -c vector.c

clean:
    rm -f Far *.o
```

`$@` is the filename of the target of the rule
`$^` is the names of all the dependencies of the rule with spaces between them

If I update only `Far.c`, then make Far, which commands does Make run?
- `Far.o: Far.c Far.h vector.h`
- `Far: Far.o vector.o`
- Also vector.o is not going to be rebuilt.