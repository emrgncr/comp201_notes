# Arrays

When you declare an array, contignious memory is allocated on the stack to store the contents of the entire array.

```c
char str[6]; //6 char length memory locations allocated
strcpy(str,"apple");
```

The array variable is not a pointer, it refers to the entire array. In fact, `sizeof` returns the size of the entire array!

```c
int size = sizeof(str); //returns 6
int strsize = strlen(str) //returns 5
```

**Arrays are not reassignable!**
```c
int n1[] = {1,2,3,4};
int n2[] = {2,3};
n1 = n2; //compile error
```

But if an array is passed as a parameter, it becomes a pointer!

```c
void f(char* str){
    sizeof(str); // 8
}

int main(...){
    char str[] = "emir";
    sizeof(str); //5
    strlen(str); //4
    f(str);
}
```

## Arrays of Pointers

```c
char* stringarray[5];       //space store 5 char pointers!
                            //not all of them have to be strings
char* str0 = stringarray[0];    //first char*
```

## Pointer Arithmatic

Pointer arithmatic does not work in bytes. It works in the *size of* the type it points to!

```c
char* s1 = &str; //0x01
char* s2 = s1 + 1; //0x02

int* nums = &num; //0x10
int* nums2 = nums + 1; //0x14 (next integer in the array)
int* nums3 = nums + 3; //0x1c
int diff = nums3 - nums; //3 (divides by 4)
```

- At compile time, C knows the sizes of different data types and sizes of what they point to.

### Array indexing

Array indexing is just pointer arithmatic. (see [lec7](./lec7.md)).

- C does not check for array boundaries :moyai:

```c
arr[-1]; //compiles
arr[99999]; //compiles
```

# Memory Layout

Stack (8MB) (local variabls and functions) -> (free) - Shared Library Data - (free) <- Heap (grows on demand) - Global data - Text (data segment)

## Stack

Stores all the local variables and the parameters. Stack grows or shrinks if we call a new function. Stack grows **downwards** and shrinks **upwards**.

- A new function **pushes** on a new frame (creates a new frame). When the function is returned, frees that memory (marks it usable not clears it).
- When we use up all the stack memory, we get a stack overflow.

### Limitations of Stack
We need a way to have memory that does not get cleaned up after a function returns.

## the Heap

- The memory in the heap can be managed.
- Unlike the stack, the memory only goes away if we delete it explicitly.
- Unlike stack, grows upwards.

### malloc
```c
void *malloc(size_t size);  //specify the number of bytes to allocate
```
- Returns void*, can be casted to any pointer.
- Returns a pointer to the starting address of the new memory.
- The memory is not cleared before given to you :moyai:.
- If malloc returns null, there was not enough memory.

```c
char* create_string(char ch, int num){ //this works
    char* new_string = malloc(sizeof(char)*(num + 1)); // +1 is for null terminator
    assert(new_string != NULL); //terminate the program
    ... //create string
    new_string[num] = '\0';
    return new_string;
}

char* create_string2(char ch, int num){ //this does not work!
    char new_string[num + 1]; // +1 is for null terminator
    ... //create string
    new_string[num] = '\0';
    return new_string;
}


int main(...){
    char* str = create_string('a',4);
    char* str2 = create_string2('a',4);
    printf("%s\n",str); //aaaa
    printf("%s\n",str2); //null ?
    return 0;
}
```

### calloc
```c
void* calloc(size_t nmemb, size_t size);
```
calloc zeros out the memory for you!
nmemb: number of members.

```c
int* scores = calloc(20,sizeof(int));

//slower
int* scores = malloc(20*sizeof(int));
for(int i = 0; i< 20; i++){
    scores[i] = 0;
}
```

### strdup

```c
char* strdup(char* s); //creates a string with null terminator on the heap for you without the need to use malloc
```
```c
char* str = strdup("hello");
str[0] = 'c'; //this works!
```

#### implementing strdup

```c
char* myStrdup(char* str){
    char* heapstr = malloc(strlen(str) + 1);
    assert(heapstr != NULL);
    strcpy(heapstr, str);
    return heapstr;
}
```
### free
```c
void free(void* ptr);
```
- Takes a pointer on heap and deletes it.
- A heap memory block can only be freed once!
- You must use the same name you used to allocate, you cant use `free(ptr + 1)`!

## Memory Leaks
- A memory leaks happens when we forget to free memory on heap.
- The program should be responsible for cleaning up any memory it allocates.
- If we dont free and allocate large heap space, we can run out of memory.
- Memory leaks rarely cause crashes.
- Strategy: Write your program, then go back and free.
- Valgrind is a very helpful tool for finding memory leaks.
