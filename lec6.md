# More Strings, Pointers

## String Search Operations

`strchr` returns the pointer to the first occurance of a character in a string.
```c
char daisy[6];
strcpy(daisy,"Daisy");
char* letterA = strchr(daisy,'a');
printf("%s\n",daisy);   //Daisy
printf("%s\n",letterA); //aisy
```
You can use `strrchr` to get the last occurance of a character.

`strstr` returns a pointer to the first occurance of the second string in the first one, else return NULL.

```c
char daisy[10];
strycpy(daisy, "Daisy Dog");
char* substr = strstr(daisy,"Dog");
printf("%s\n",daisy);   //Daisy Dog
printf("%s\n",substr);  //Dog
```

## String Spans

`strspn` returns the length of the initial part of the first string which contains all the characters in the second string.

`strcspn` is similar but returns the part of the first string that does not contain any of the characters in the second string.

```c
char daisy[10];
strycpy(daisy, "Daisy Dog");
int spanLength = strspn(daisy, "aDeoi");    //3
int spanLength2 = strcspn(daisy, "driso");  //2
```

Note that you can find more string.h functions in [lecture 5](./lec5.md).

# C Strings as Parameters

```c
int doSomething(char *str){
    char secondChar = str[1];
    ...
}
//is the same thing as
int doSomething(char str[]){
    ...
}
```

# Array of Strings

```c
char *stringArray[5]; //space to store 5 chars

char *stringArray[] = {
    "Hello",
    "Hi",
    ...
}; //there needs to be a semicolon here!

printf("%s\n",stringArray[1]);

```

When an array is passed as an argument in c, it is converted to a pointer to the first element of the array.

The main function of c has argc and argv, note that argv is `char **argv`, that is the pointer to the first element of a string array!

```c
void doSomething(char *array[]){
    ...
}
//is equivalent to
void doSomething(char **array){
    ...
}
```

# Pointers
- A pointer is a variable that stores a memory address.
- There is no pass-by-reference in c, we use pointers.
- One (8 byte) pointer can refer to any memory location.
- Pointers are also essential for allocating memory on the heap.
- Pointers also let us refer the memory dynamically.

## Memory
- Memory is a big array of bytes.
- Each byte has a unique numeric index that is generally written in hexadecimal.
- A pointer stores one of these memory addresses.

## Looking Closely at C

- All parameters in C are "pass by value". For efficiency purposes, array (and strings etc.) passed in parameters are converted to pointers.
- To make the changes on the parameter persistent, pass the pointer instead!


```c
int x = 2;

int *xPtr = &x; // & means "address of" / * means pointer

//use * again to go to that address (dereferencing)
printf("%d\n", *xPtr);  //prints 2
printf("%d\n", xPtr);   //prints ?????
```

### Example


Passing pointer:

```c
void myfunc(int* intptr){
    *intptr = 3; //change the value at that memory location
}

int main(int argc, char** argv){
    int x = 2;
    myFunc(&x); //pass the memory location of x
    printf("%d\n",x); // 3!
    ...
}
```
Passing integer:
```c
void myfunc(int integer){ //creates a copy of "x", so x is not affected by change
    integer = 3;
}

int main(int argc, char** argv){
    int x = 2;
    myFunc(x); //pass x
    printf("%d\n",x); // 2!
    ...
}
```

You can print the value of a pointer with `printf(" ... %p ... ", pointer);`

## Strings in Memory

1) If we create a string using `char[]` we can edit its characters because its memory lives in our stack space.

2) We cannot set `char[]` to another value because it is not a pointer, it refers to the block of memory reserved for the original array. The array's size cannot be changed as well.

3) If we pass a `char[]` as a parameter or set something equal to it, it is automatically converted to `char*`.

4) If we create a new string with new characters as a `char*`, we cannot modify its value as it lives in the data segment.
    ```c
    char* mystring = "Hello World";
    printf("%s\n",mystring); //"Hello World"
    mystring[0] = 'h'; //   crashes!


    char* sptr = "Hello World"; //an pointer that points to a read only section
    char sarr[] = "Hello World"; //an array that is on the heap
    ```

### Memory Locations

For each of these snippets, decide if their characters can be modified?

```c
char mystr[6];          // can be modified

char* mystr = "Hi";     // cannot be modified

char buf[6];
strcpy(buf,"Hi");
char *mystr = buf;      //can be modified

char* otherstr = "Hi";
char* mystr = otherstr. //cannot be modified
```

