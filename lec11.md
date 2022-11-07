# More Generics and Function Pointers

## More Generics Examples

### Rotate Array

```c
    void rotate(void* front, void* seperator, void* end);
```
Where seperator is somewhere inbetween in an array.
Puts the part (seperator - end) at the start of the array and the part (front - seperator) at the end of the array.

Implementation:
```c
void rotate(void* front, void* seperator, void* end){
    //Here when doing pointer arithmatic you may need to cast void* to char*
    char tmp[end - seperator];
    memcpy(tmp, seperator, end - seperator);
    memmove(end - (seperator - front), front, seperator - front);
    memcpy(front, tmp, end - seperator);
}
```

## Memset

```c
void* memset(void* dest, int c, size_t n);
```
Memset sets a specified number of bytes at one address to a certain value.

```c
int counts[5];
memset(counts, 0, sizeof(counts));
```

## Function Pointers

### Bubble Sort
*Insert bubble sort algorithm explanation here*

Implementation on integers:
```c
void bubble_sort_int(int* arr, size_t nelems){
    while(true){
        bool swapped = false;
        for(size_t i = 0; i < nelems - 1; i++){
            if(arr[i] > arr[i+1]){
                swap(&arr[i], &arr[i+1], sizeof(int));
                swapped = true;
            }
        }
        if(!swapped){
            return;
        }
    }
}
```

Now let's implement this as a generic function.

**A Porblem:**

```c
void buble_sort(void* arr, size_t one_elem_size, size_t nelems){
    while(true){
        bool swapped = false;
        for(size_t i = 0; i < nelems - 1; i++){
            void* p_next_elem = (char*)arr + (i+1)*one_elem_size;
            void* p_curr_elem = (char*)arr + i*one_elem_size;
            //?? How to compare 2 void pointers?
        }
    }
}
```
**We need a way to compare 2 void pointers.**
We will ask the user to provide us a compare function!

```c
void buble_sort(void* arr, size_t one_elem_size, size_t nelems, bool (*compare_fn)(void* a, void* b)){
    while(true){
        bool swapped = false;
        for(size_t i = 0; i < nelems - 1; i++){
            void* p_next_elem = (char*)arr + (i+1)*one_elem_size;
            void* p_curr_elem = (char*)arr + i*one_elem_size;
            if(compare_fn(p_curr_elem, p_next_elem)){
                swap(p_curr_elem, p_next_elem, one_elem_size);
                swapped = true;
            }
        }
        if(!swapped){
            return;
        }
    }
}
// Now we need comparison functions for different types
```
Example usage.
```c
bool integer_compare(void* p1, void* p2);
bool string_compare(void* p1, void* p2);
bool student_compare(void* p1, void* p2);
...
bubble_sort(arr, sizeof(int), 5, integer_compare);
bubble_sort(arr, sizeof(char*), 5, string_compare);
bubble_sort(arr, sizeof(Student), 5, student_compare); //assume Student is a struct
```

## Function Pointers

```
[return type] (*[name])([parameters]);
```

Implementing function pointers:
- Cast void* to certain type
- Dereference
- Compare

```c
bool int_compare(void* p1, void* p2){
    int* ip1 = (int*)p1;
    int* ip2 = (int*)p2;
    return *ip1 > *ip2;
}
```

### Comparison Function
In C, in general a `compare(a,b)` function returns:
- 0 if equal
- \>0 if a > b
- <0 if a < b

```c
int int_compare(void* p1, void* p2){
    int* ip1 = (int*)p1;
    int* ip2 = (int*)p2;
    return *ip1 - *ip2;
}

int string_compare(void* p1, void* p2){
    char* str1 = *(char**)p1;
    char* str2 = *(char**)p2;
    return strcmp(str1, str2);
}
```

### Example Function Pointers

- A function that compares 2 elements of a given type
- A function that prints an element of a given type
- A function that frees an element of a given type

### Function Pointers as Variables

```c
int do_smt(char* str){
    ...
}
int main(...){
    int (*fptr)(char*) = do_smt;
    fptr("Hello");
    return 0;
}
```

## Generic C standart library functions

- `qsort` - Can sort an array of any type (quick sort)
- `bsearch` - Can search an array of any type (binary search)
- `lfind` - Can search an array of any type (linear search)
- `lsearch` - Can search an array of any type (linear search, can add new keys)
- `scandir` - Creates directory listings (? not sure what this does)