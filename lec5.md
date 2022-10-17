# Representing Text

## Characters

1 byte long.
```c
char letterA = 'A';
char plus = '+';
char space = ' ';
char newLine = '\n';
char tab = '\t';
char singleQuote = '\'';
char backSlash = '\\';
```

### ASCII

A character is an 8 bit integer value, ASCII value.
- Uppercase letters are numbered sequentally
- Lowercase letters are numbered sequentally
- Digits are sequentially numbered
- Lowercase letters are 32 more than their uppercase equivalents (bit flip!)

```c
'A' == 65 //true
'a' == 97 //true
'0' == 48 //true
'0' + 4 == '4' //true
'f' > 'c' //true
'c' - 'a' == 2 //true
```

```c
for(char ch = 'a'; ch <= 'z'; ch++){
    printf("%c", ch); //%c to print a char
}
```

### Unicode

Unicode can represent all characters from all languages with 16-bits.

Because Unicode is somewhat wasteful for English documents, usually UTF-8 is used. (Unicode Transformation Format)

Emojis are included in Unicode (version 15). (like U+1F600)

### ctype.h

```c
isalpha(char) //true if a letter
islower(char) //'a' -> 'z'
isupper(char) //'A' -> 'Z'
isspace(char) //space, tab, newline...
isdigit(char) //'0' -> '9'
toupper(char) //returns uppercase
tolower(char) //returns lowercase
```

## Strings

C has no special string data type. Strings are char arrays with special ending sentinel value `'\0'`.
```c
"Hello" = ['H','e','l','l','o','\0']
```

`'\0'` is a null termination character.

Getting the length of a string:
```c
int len = strlen(myString);
```

**Getting the length of a string is O(N). Save it if you are going to use it later.**

### Strings as Parameters

```c
int doSomething(char* str){
    ...
    str[0] = 'c'; //modifies string (outside the scope too)
    ...
}

char myString[6]; //char array with length 6
...
doSomething(myString);
```

### Common String Operations

#### string.h

```c
strlen(str);

strcmp(str1,str2); //compares 2 strings alphabetically
strncmp(str1,str2,n); //first n letters

strchr(str,ch); //searches a char in a string. Returns a pointer or NULL
strrchr(str,ch); //finds the last occurance

strstr(haysack,needle); // searches a substring in a string, returns a pointer or NULL

strcpy(dst,src); //copy a string onto another, includes \0, more info below
strncpy(dst,src,n); //stops after n and does not include \0

strcat(dst,src); //copies characters in src to dst, replacing old \0 and adding new \0 to the end.
strncat(dst,src,n); //stops copying after n characters

strspn(str,accept); //returns the length of the initial part of str which contains characters only from accept. (?)
strcspn(str,reject); //returns the length of the initial part of str which does not contain any characters from reject. (?)
```

#### Comparing strings

```c
void doSomething(char *str1, char *str2){
    if(str1 > str2) ... //compares memory locations!
    if(strcmp(str1,str2)) ...
        //returns 0 if identical
        //returns <0 if str1 is before str2 in alphabet
        //returns >0 if str1 is after str2 in alphabet
}
```

#### Copying strings

```c
char str1[6];
strcpy(str1, "hello");

char str2[6];
strcpy(str2,str1);
str2[0] = 'c';

printf("%s\n%s\n",str1,str2); 
/*
hello
cello
*/
```

##### Overflow

```c
char str[6];
strcpy(str, "hello, world!"); //overwrites other memory
```
This is a buffer overflow. Overwrites other memory.
Accessing the string would be fine but it might overwrite something important :moyai:.


#### Copying Strings - strncpy

`strncpy(dst,src,n)`, copies at most n bytes from src into dst, if there is no null terminator in these bytes, will not add null terminator :moyai:.

```c
char str2[5];
ctrncpy(str2, "hello, world!", 5);
strlen(str2); // ?????
//str2 = ['h','e','l','l','o'] NO NULL TERMINATOR!
str2[5] = '\0'; //manually set null terminator.
```
#### Concatenating Strings

Cannot add two strings with `+`, it adds memory locations.

```c
char str1[13];  //enough space for hello world!\0
strcpy(str1, "hello, ");
strcat(str1, "world!"); //removes old \0, adds new \0 at end
printf("%s",str1); //hello world!
```

#### Substrings and char*

You can create `char*` yourself that points to an adress within a string!

```c
char myString[3];
myString[0] = 'H';
myString[1] = 'i';
myString[2] = '\0';

char *otherStr = myString; //Points to 'H'
printf("%s", otherStr); // Hi
printf("%c", *(otherStr + 1)); // i

char str[8];
strcpy(str,"racecar");

char* str2 = str + 4;
printf("%s",str2); //car
```