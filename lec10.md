# Generics and void*

How to write code that works with any data type?

## Generics

- We want to write code as general-purpose as possible
- Generic code reduces core duplication so making improvements or fixing bugs is easier (in the sense that modifying a single function is enough but writing generic code is not that easy imo)

## Generic Swap

A function that swaps two numbers:

```c
void swap_int(int* a, int* b){
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main(...){
    int x = 5;
    int y = 8;
    swap_int(&x,&y);
    ...
}
```

A function that swaps two shorts :roll_eyes::

```c
void swap_short(short* a, short* b){
    short temp = *a;
    *a = *b;
    *b = temp;
}

int main(...){
    short x = 5;
    short y = 8;
    swap_short(&x,&y);
    ...
}
```

A function that swaps two strings :angry::

```c
void swap_string(char** a, char** b){
    char* temp = *a;
    *a = *b;
    *b = temp;
}

int main(...){
    char* x = "hello";
    char* y = "world";
    swap_string(&x,&y);
    ...
}
```

A function for a custom struct :scream::
```c
...
```

*(thanks github copilot)*

See, all these functions do basically the same thing. We can combine them to a single function using generics.

### Psuedocode
```c
void swap(pointer_to data1, pointer_to data2){
    //store data1 in temp
    //copy data2 to location of data1
    //copy data in temp to location of data2
}
```
**Problem**: Each type has a different size. How do we know how much to copy?

In the previous functions, c knows the size of temp and pointers because of the data types. However, we don't know the size of the data type in the generic function.

### void*

We can use void* to denote any kind of pointer. We still don't know the size of the data type. 
We can ask the user to pass the size of the data type as a parameter.

```c
void swap(void* data1, void* data2, size_t nbytes){
    //store data1 in temp
    char temp[nbytes]; //since sizeof(char) is 1 byte, store nbytes as char[]
    //copy data2 to location of data1
    //copy data in temp to location of data2
}
```

But `temp = *data1` does not work as we cannot dereference void pointers.

### `memcopy` and `memset`

First, we need to learn these functions:

```c
void* memcpy(void* dest, const void* src, size_t n);
void* memmove(void* dest, const void* src, size_t n);
```

These functions copy `n` bytes from `src` to `dest`. The difference between `memcpy` and `memmove` is that `memcpy` assumes that the memory regions do not overlap, while `memmove` does.

The `const` keyword means that `src` will not be modified.

### Completing generic swap

```c
void swap(void* data1, void* data2, size_t nbytes){
    char temp[nbytes];
    memcpy(temp, data1, nbytes);    //copy data1 to temp
    memcpy(data1, data2, nbytes);   //copy data2 to data1
    memcpy(data2, temp, nbytes);    //copy temp to data2
    //note that *data1 = *data2 does not work as we cannot dereference void*
}

int main(...){
    int x = 5;
    int y = 8;
    swap(&x,&y,sizeof(int));
    ...
    char s1[] = "hello";
    char s2[] = "world";
    swap(&s1,&s2,sizeof(s1));
    ...
}
```

## void* Pitfalls

- void* is powerful but dangerous, C cannot do much checking. (example, you can swap half of an int)

## Generic Array Swap

### Swap Ends

Write a function that swaps the first and last elements of an array of numbers.

```c
void swap_ends_int(int* arr, size_t nelems){
    int temp = arr[0];
    arr[0] = arr[nelems-1];
    arr[nelems-1] = temp;
}

int main(...){
    int arr[] = {1,2,3,4,5};
    size_t nelems = sizeof(arr)/sizeof(arr[0]);
    swap_ends_int(arr,nelems);
    ...
}
```

Now let's write a generic version of this function.

```c
void swap_ends(void* arr, size_t nelems, size_t nbytes){
    swap(arr,(char*)(arr) + ((nelems - 1)*nbytes), nbytes);
}
```

**A couple notes on pointer arithmethic:**
- Since by default, the pointer arithmatic adds the length of the data type to the pointer, we need to multiply the number of elements by the size of the data type.
- Also C cannot do pointer arithmethic on void pointers, so we need to cast the pointer to a char pointer. (as char is 1 bytes long)

Usage:
```c
int main(...){
    int arr[] = {1,2,3,4,5};
    size_t nelems = sizeof(arr)/sizeof(arr[0]);
    swap_ends(arr,nelems,sizeof(arr[0]));
    
    char s[] = "hello";
    nelems = sizeof(s)/sizeof(s[0]);
    swap_ends(s,nelems,sizeof(s[0]));
    ...
}
```