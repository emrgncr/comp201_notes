# Caching

All caching depends on the locality.

# Writing cache friendly code

- Make the common case go fast
    - Focus on the inner loops of the core functions

- Minimize the misses in the inner loops
    - Repeated references to variables
    - Stride-1 reference patterns are good (immediate next element)

## Rearranging loops (exploit spatial locality)

### Example: Matrix Multiplication

$$
c_{ij} = \sum
$$

Multiply NxN matrices
Matrix elements are doubles

## Using blocking (exploit temporal locality)





# Optimization

- Targeted, intentional optimizations alleviate bottlenecks and resut in big gains. But only optimize where necessary.

**In Summary**
-   If doing something is rare, and only done on small inputs, don't optimize it.
-   If doing things alot or on big inputs, make it's big O cost reasonable
-   let gcc do its magic
-   optimize explicitly as a last resort

## gcc optimization

```sh
gcc -O0
gcc -O2
gcc -O3     # more aggressive but trades for file size
gcc -Os     # optimize for size
gcc -Ofast  # ignore conventions
``` 

### gcc optimizations

-   Static instruction count
-   Dynammic instruction count
-   Cycle count / execution time


#### Constant folding

gcc pre-calculates constants at compile-time where possible.

```c
int seconds = 60 * 60 * 24 * n_days;
```

Compiler pre-calculates `60 * 60 *24 berfore hand`

```c
int fold(int param){
    char arr[5];
    int a = 0x107;
    int b = a * sizeof(arr);
    int c = sqrt(2.0);
    return a * param + (a + 0x15 / c + strlen("hello") * b - *x37)/4;
}
```

TODO add assm codes

#### Common subexpression elimination

prevents recalculation of the same value many times.

```c
int a = param2 + 0x201;
int b = param1 * (param2 + 0x201) + a;
return a * (param2 + 0x201) + b * (param2 + 0x201);
```

TODO add asm code
This optimization is even done with -O0!

#### Dead Code

Dead code elimination removes the code that doesn't serve any purpose.

```c
if(param1 < param2 && param1 > param2){
    ...
}

if(param1 == param2){
    x ++;
}else{
    x ++;
}

if(param1 == 0){
    return 0;
}else{
    return param1;
}
```
#### Strength Reduction

Strength reduction changes divide to multiply, multiply to add/shift, and mod to AND to avoid using costly instructions.

#### Code motion

Moves code outside of loops if possible.

```c
for(int i = 0; i < n; i++){
    sum += arr[i] + foo * (bar + 3);
}
```

Calculates `foo * (bar + 3)` outside of the loop.

#### Tail Recursion

Gcc identifies (sometimes) recursive patterns and converts them to iterative loops.

```c
long factorial(int n){
    if(n == 0){
        return 1;
    }else{
        return n * factorial(n - 1);
    }
}
```

**Tail Recursion:** Final action of a recursive call.

```c
long factorial(int n, long acc){
    if(n == 0){
        return acc;
    }else{
        return factorial(n - 1, n * acc);
    }
}
```

```c
long factorial(int n){
    long acc = 1;
    for(int i = 1; i <= n; i++){
        acc *= i;
    }
    return acc;
}
```
#### Loop Unrolling

Do n iterations's worth of work per actual loop iteratipn so we can save ourselves from doing loop overhead (test and jump) every time.

```c
for(int i = 0; i<= n-4; i+=4){
    sum += arr[i];
    sum += arr[i+1];
    sum += arr[i+2];
    sum += arr[i+3];
} //after the loop handle any leftovers
```

### limitations of gcc optimizations

GCC can't optimize everything! 

```c
int char_sum(char* s){
    int sum = 0;
    for(size_t i = 0; i < strlen(s); i++){
        sum += s[i];
    }
    return sum;
}
```

What is the bottleneck? strlen is called every time
Code motion moves strlen outside of the loop

```c
int char_sum(char* s){
    int sum = 0;
    for(size_t i = 0; i < strlen(s); i++){
        if(s[i] >= 'A' && s[i] <= 'Z'){
            s[i] -= ('A' - 'a');
        }
    }
    return sum;
}
```

Here, because the string changes, gcc can't move strlen outside of the loop.

## Optimizing the code

- Explore various optimizations to reduce instruction count and runtime.

    - More efficent big-O
    - Use callgrind to find bottlenecks
    TODO

### Compiler optimization

Why not just compile with -O2?

- It is harder to debug
- Optimizations may not always improve the program. The compiler does its best, but may not work or slow things down. Experiment to see what works the best.