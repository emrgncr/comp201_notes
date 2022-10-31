# realloc, Memory Bugs

*I have started using github copilot to write these notes. Which helps a lot in terms of speed but I trust autocomplate a bit too much so there might be wrong information.*

## Recap

Mostly recap of [lecture 8](./lec8.md).

## Realloc
    
```c
    void *realloc(void *ptr, size_t size);
```
- The realloc function takes an existing allocation pointer and enlarges to a new requested size. It returns a new pointer.
- If there is enough immidiate space after the existing allocation, it will use that space.
- If there is not enough space, it will allocate a new space and copy the old data to the new space. It will free the old space.
- If size is smaller than the old size, it will truncate the data.

```c
char* str = strdup("Hello");
assert(str != NULL);
...
//want to make str longer to hold Hello World!
char* addition = "World";
str = realloc(str, strlen(str) + strlen(addition) + 1);
assert(str != NULL);

strcat(str, addition);
printf("%s\n", str); //Hello World
free(str);
```

- realloc only accepts pointers that were allocated with malloc, calloc or realloc. **DO NOT use realloc on stack memory or middle of the heap memory.**

## Cleaning up with free and realloc

Realloc uses free on the previous pointer, calling free one time at the end is enough.

# Heap allocation summary

```c
void* malloc(size_t size);
void* calloc(size_t nmemb, size_t size);
void* realloc(void* ptr, size_t size);
char* strdup(char* s);
void free(void* ptr);
```
These functions guarantee:
- NULL on failure. (Check with assert)
- Memory is contiguous. It is not recycled unless freed.
- realloc preserves existing data.
- calloc zeros out the memory for you, malloc and realloc does not.
Undefined behavior:
- If you overflow.
- If you free the same pointer twice.
- If your realloc/free non-heap memory.

# Stack vs Heap

Stack:
- Fast (as in implementation speed)
- Convenient
- Reasonable type safety
- Limited size (8MB)
- Somewhat inflexible

Heap:
- Plentiful (a lot of memory on demand)
- Very flexible
- Scope under your control
- Lots of opportunity for error

Stack and Heap
- Generally prefer to use local variables.

# Memory Bugs

## Pointers and Working with Dynamic Memory

Be carefull of:
- storage used after free,
- free multiple times,
- insufficient space for a dynamically allocated variable (overflow)
- freeing unallocated space,
- freeing stack memory,
- memory leakage (not freeing memory and losing the pointer),
- assignment to incompatible types,
- returning a pointer to a local variable,
- dereferencing a wrong type,
- dereferencing uninitialized or invalid pointer,
- incorrect use of pointer arithmetic,
- array index out of bounds (compiler doesn't check this)

