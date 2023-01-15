# Memory abstraction

- Write
    - Transfer data from CPU to memory
    - Store operation

- Read
    - Transfer data from memory to CPU

TODO here

## Traditional Bus structure

- A bus is a collection of parallel wires that carry address, data and control signals

- Busses are typically shared by multiple devices

### Memory read transaction

Load operation: `movq A, %rax`

- CPU places address A on the memory bus.
- The main memory reads A from the memory bus, retrieves word x, and places it on the bus.
- CPU read word x from the bus and copies it into register %rax.

Store operation: `movq %rax, A`

- CPU places address A on bus. Main memory reads it and waits for the corresponding data to arrive.
- CPU places data word y on the bus.
- Main memory reads word y from the bus and stores it at address A.

## Storage technologies and trends

### Random-Access Memory (RAM)

- Key Features
    - RAM is traditionally packaged as a chip
    - Basic storage unit is normally a `cell` (one bit pre cell)
    - Multiple RAM chips form a memory.

- Static RAM: SRAM
    - SRAM is fast (1x time), but expensive (100x)
    - SRAM is volatile
    - SRAM is used for cache memories
    - Maybe needs EDC


- Dynamic RAM: DRAM
    - DRAM is slow (10x time), but cheap (1x)
    - DRAM is volatile
    - DRAM is used for main memory
    - Needs refresh
    - Needs EDC

EDC: Error Detection and Correction

#### Enhanced DRAM (EDRAM)

- Operation of DRAM cell has not changed since its invention in 1970

- DRAM cores with better logic and faster io: 
    - Synchronus DRAM : SDRAM
    - Double Data Rate SDRAM : DDR SDRAM
        - DDR: 2 bit prefetch buffer
        - DDR2: 4 bit prefetch buffer
        - DDR3: 8 bit prefetch buffer
        - DDR4: 16 bit prefetch buffer

### Storage Technologies

- Nonvolatile memory
    - Flash memory

- Magnetic disk
    - Hard disk drive (HDD)
    - Solid-state drive (SSD)

> Volitile memory: Loses information when powered off

(too much writing, I wont take notes today)
(TODO ALL)