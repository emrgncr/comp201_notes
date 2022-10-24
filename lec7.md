# Arrays and Pointers

## Pointers Recap

```c
int x = 2;
int* xPtr = &x;         //& means "address of"
printf("%d", xPtr);     //prints memory location of x
printf("%d", *xPtr);    //prints 2 by "dereferencing".
                        //Use * to access the data in that memory location. ("dereferencing")
```

## char*

```c
char str[6] = "apple";

char* ptr1 = str;
char* ptr2 = &str[0];
char* ptr3 = &str;
//all these three are equivalent!
```

Adding offset to a pointer, gives us a substring as in:
```c
char* str1 = "apple";   //0xff0
char* str2 = str1 + 1;  //0xff1
char* str3 = str1 + 3;  //0xff3

printf("%s",str1); //apple
printf("%s",str2); //pple
printf("%s",str3); //le
```

When we use square brackets with a pointer, it performs pointer arithmatic!

```c
char* str = "apple";

char thirdLetter1 = str[3];     //'l'
char thirdLetter2 = *(str + 3); //'l'
//  thirdLetter1 == thirdLetter2
//  lol then str[3] == 3[str]
```

## Strings as Parameters

Because strings are character arrays, we can modify strings in functions!

```c
void myFunc(char* x){
    x[4] = 'X';
}

int main(int ac, char** av){
    char str[6] = "apple";
    myFunc(str);        //create a copy of &str[0] (a memory address) and pass to the function
    printf("%s",str);   //applX
}
```

### Some examples

```c
char str[6] = "apple";
str = str + 1; //Compile error (cannot reassign array)
...
```

```c
char* str = "apple";
str = str + 1;
str[1] = 'u'; //segfault
...
```

```c
char arr[6] = "apple";
char* str = arr;
str = str + 1;
str[1] = 'u';
printf("%s\n",str); //pule
```

----------

## C parameters

When you pass a value as a parameter, C passes a copy of that value.
However, if you pass pointers, you can make persistent changes.

```c
void valuefun(int x){
    x = 8;
    ...
}

void ptrfun(int* x){
    *x = 8;
    ...
}

int main(...){
    int num = 4;
    valuefun(num);  //wont modify num
    ptrfun(&num);   //will modify num
}
```

### char*

A char pointer points to a single character.

## Double Pointers

```c
void skipSpaces(char** strptr){
    int numspaces = strspn(*strptr, " "); //*strptr is our char* aka string.
    *strptr += numspaces; //equal to str += numspace, hence offsets our string!
}
int main(...){
    char* str = "    hello";
    skipSpaces(&str);   //passes a pointer pointing to the str(which is a char pointer)
    printf("%s\n",str); //hello
}
```
