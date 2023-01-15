# Locality

## Example

Consider 

```c
int array[SIZE];
int sum = 0;

for(int i = 0; i< 20000; i++){
    for(int j = 0; j < SIZE; j++){
        sum += array[j];
    }
}
```

todo add plot

We would except a linear growth as the SIZE changes.
However, as the SIZE increases, when dataset < cache size, it grows linear, but when dataset > cache size, its slope changes drastically.

## Processor-Memory bottleneck

TODO add image

We use caches to reduce the number of memory accesses.

# Cache memories

- Cache memories are small, SRAM-based memories that are used to store recently used data and instructions.

- CPU first checks the cache memory to see if the data is there. If it is, it is used. If not, it is fetched from the main memory.

## General cache concepts

TODO add plots

