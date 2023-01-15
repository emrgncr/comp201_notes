# Linking

## Example C program

```c
int sum(int* a, int n);

int array[2] = {1,2};

int main(){
    int val = sum(array, 2);
    return val;
}
```

```c
int sum(int* a, int n){
    int i = 0;
    int s = 0;
    for(i = 0; i < n; i++){
        s += a[i];
    }
    return s;
}
```

### Static linking

Programs are translated and linked using a compiler driver:

```bash
gcc -0g -o prog main.c sum.c
```

TODO add image

main.c -> main.o
sum.c -> sum.o

linker (main.o + sum.o) -> prog

## Why linkers?

Reason1 - Modularity:
-   Program can be written as a collection of smaller source files rather than one large file
-   Can build libraries of common functions (more on this later)

Reason2 - Efficiency:
-   Time: Seperate compilation
    -   Don't need to recomplie unchanged files
    -   Change one source file, recompile than relink
-   Space: Libraries (Shared libraries)
    -   Don't need to copy code into every program
    -   TODO

## What do Linkers do?

Step1: Symbol resolution

-   Programs define and reference symbols (global variables, functions, etc)
    - void swap(){} /* define symbol swap */
    - swap(); /* reference symbol swap */
    - int *xp = &x /\* define symbol xp, reference symbol x */
-   Symbol definitions are stored in object file (by assembler) in symbol table.
    - Symbol table is an array of structs
    - Each entry includes dame, size and location of symbol
    - During symbol resolution, the linker uses one symbol definition to resolve all references to that symbol (TODO)

Step2: Relocation

- Merges seperate code and data sections into single sections.
- Relocates symbols from their relative locations in the .o file to their final absolute memory locations in the executable.
- Updates references to symbols to point to the new locations.

### Three kinds of object files

- Relocatable object file (.o file)
    - Contains code and data in the form that can be combined with other object files to form an executable file.
        - Each .o file is generated form one .c file

- Executable object file (a.out file)
    - Contains code and data in the form that can be loaded into memory and executed.

- Shared object file (.so file)
    - Special type of relocatable object file that can be loaded into memory and linked with other object files dynamically, at either load time or run time.
    - In windows, .dll files are shared object files.

### Executable and Linkable format (ELF)

TODO

### Linker Symbols

- Global Symbols
    - Symbols defined by module m that can be referenced by other modules
    - non-static C functions and non-static global variables

- External Symbols
    - Global symbols that are referenced by module m but defined by some other module.

- Local Symbols
    - Symbols defined by module m that are not global symbols.
    - static C functions and static global variables

