# const, structs

## const

- We can use const to declare global constants. Constants cannot be changed after they are created.

```c
const double PI = 3.14159265358979323846;
const int DAYS_IN_WEEK = 7;
```

- We can use const to indicate that the data the pointer points to cannot be changed.

```c
char str[6] = "hello";
const char *s = str;

//cannot use s to change characters it points to
s[0] = 'H'; //error
```

- We sometimes use const in function parameters to indicate that parameter cannot/should not be changed.

```c
int countUppercase(const char *s){
    int count = 0;
    for(int i = 0; i< strlen(s); i++){
        if(isupper(s[i])){
            count++;
        }
    }
}
```

- We need to be consistent when using consts. We cannot create a non-const variable and assign it to a const pointer.

```c
int countUpper(const char *s){
    char *str_to_modify = s; //warning and error
    ...
}
```

### const can get confusing

```c
const char c = 'h'; //cannot change c

const char *str = ... //cannot modify chars pointed by str

const char **strptr = ... //cannot modify chars pointed by *strptr
```

```c
char buf[] = "Hello";
const char *s = buf;
s[2] = 'A'; //error
buf[2] = 'A'; //ok
s = "World"; //ok
```

## structs
A struct is a way to define our new data types.

```c
struct date{ //declaring struct type
    int month;
    int day;
}; //don't forget the semicolon here
...

struct date today; //construct structure instances
today.month = 1;
today.day = 28;

struct date new_years_eve = {12, 31}; //shorter initializer
```

To not write `struct` before everyting, we can use `typedef`

```c
typedef struct date{
    int month;
    int day;
} date; //we can also name it something else

date today;
...

date new_years_eve = {12, 31};
```

When we pass structs as function parameters, a copy of it is passed. If we want to modify the struct, we need to pass a pointer to it.


```c
void advance_day(date d){
    d.day++; //doesn't really work
}

void advance_day(date *d){
    (*d).day++; //works
}

void advance_day_shorter(date *d){
    d->day++; //works
}
```

We can also use structs as return values:

```c
date create_new_years_date(){
    date d = {12, 31};
    return d;
}

int main(...){
    date new_years_eve = create_new_years_date();
    printf("%d\n", new_years_eve.day); //prints 31
    return 0;
}
```

**sizeof with structs**

```c
int main(...){
    date d = {12, 31};
    printf("%d\n", sizeof(d)); //prints 8 since 2 ints are 4 bytes each
    return 0;
}
```

### Arrays of structs

```c
typedef struct my_struct{
    int x;
    char c;
} my_struct;

...

my_struct arr[5];
arr[0] = (my_struct){1, 'a'};

arr[1].x = 2;
arr[1].c = 'b';
```

## Generic Stack Example

- A stack is a data structre representing a stack of things.
- You can only access the top of the stack, and you can push and pop things from the top.
- Main functions of a stack are `push(value)`, `pop()`, `peek()`
- A stack is often implemented using a **linked list** internally. `"bottom"` is the tail of the linked list and `"top"` is the head of the linked list.

In an object oriented language we can represent a stack as a class but in C, we need to use structs.

### Int stack

```c
typedef struct int_node{
    int data;
    struct int_node *next;
} int_node;

typedef struct int_stack{
    int_node *top;
    int nelems;
} int_stack;

int* int_stack_create(){
    int_stack *s = malloc(sizeof(int_stack));
    s->top = NULL;
    s->nelems = 0;
    return s;
}

void int_stack_push(int_stack *s, int value){
    int_node *new_node = malloc(sizeof(int_node));
    new_node->data = value;

    new_node->next = s->top;
    s->top = new_node;
    s->nelems++;
}

void int_stack_pop(int_stack* s){
    if(s->nelems == 0){
        error(1,0,"Stack is empty");
    }
    int_node *old_top = s->top;
    int value = old_top->data;

    s->top = old_top->next;

    free(old_top);
    s->nelems--;

    return value;
}
```

let's test:
```c
int main(...){
    int_stack* s = int_stack_create();
    for(int i = 0; i < 10; i++){
        int_stack_push(s, i);
    }
    for(int i = 0; i < 10; i++){
        printf("%d %d\n",s->nelems, int_stack_pop(s));
    }
    free(s);
    return 0;
}
```

### Generic stack

This is a bit more complicated.

First the structs:

```c
    typedef struct node{
        struct node *next;
        void *data;
    } node;

    typedef struct stack{
        node *top;
        int elem_size_bytes;
        int nelems;
    } stack;
```

Now the functions:

```c
    //github copilot wrote this whole block
    
    stack* stack_create(int elem_size_bytes){
        stack *s = malloc(sizeof(stack));
        s->top = NULL;
        s->elem_size_bytes = elem_size_bytes;
        s->nelems = 0;
        return s;
    }

    void stack_push(stack *s, const void *value){
        node *new_node = malloc(sizeof(node));
        new_node->data = malloc(s->elem_size_bytes);
        memcpy(new_node->data, value, s->elem_size_bytes);

        new_node->next = s->top;
        s->top = new_node;
        s->nelems++;
    }

    void stack_pop(stack* s, void *value){
        if(s->nelems == 0){
            error(1,0,"Stack is empty");
        }
        node *old_top = s->top;

        memcpy(value, old_top->data, s->elem_size_bytes);

        s->top = old_top->next;

        free(old_top->data);
        free(old_top);
        s->nelems--;
    }
```

Example usage:

```c

stack* intstack = stack_create(sizeof(int));
for(int i = 0; i < 10; i++){
    stack_push(intstack, &i);
}
int pop = 0;
for(int i = 0; i < 10; i++){
    stack_pop(intstack, &pop);
    printf("%d %d\n",intstack->nelems, pop);
}
```