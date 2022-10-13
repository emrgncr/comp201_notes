## First C Program
```c
/*
comment
*/
#include <stdio.h>
int main(int argc, char *argv[]){ //argc -> argument count argv -> arguments, char array array
    prntf("Hello, world!\n");
    return 0; // 0 is success
}
```
## Syntax
```c

int x = 42 + 7 * -5;
double pi = 3.14159;
char c = 'Q';

for (int i = 0;i<10; i ++){
    ...
}


```

## Booleans

```c
#include <stdbool.h> //to inclode boolean
```
else c thinks any nonzero value is true.

## Printf

```c
printf(text, arg1, arg2, arg3);

char *prefix = "COMP";
int number = 201;

printf("class: %s%d", prefix, number);

```
%s for string, %d for integer, %f for double

