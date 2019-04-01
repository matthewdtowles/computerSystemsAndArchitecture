# III: CPU and Memory

## Introduction

### CPU Basics and Organization

- CPU broken down:
  - _Datapath_ - a network of registers and ALUs connected by buses
    - Timing is controlled by clocks
  - _Control Unit_ - module for sequencing operations
    - Makes sure correct data is where it needs to be at correct time
  - Datapath and Control Unit perform tasks of the CPU:
    - Fetching instructions
    - Decoding them
    - Performing indicated sequence of operations
  - These two components of CPU directly affect the performance of the machine
  
#### Registers
- Places to store data like:
  - Addresses
  - Program counters
  - Data for program execution
- Simply, it stores binary data
- Located on processor - quick access
- One D flip-flop is equivalent to a 1 bit register
- Common register sizes:
  - 16, 32, 64 bits
- Registers may be specialized for only address or only counters, etc...
  - May also just be general
- Manipulated by the control unit
- Some real examples of specialized registers:
  - Shift values
  - Compare values
  - Counting
  - Scratchpad to store temp values
  - Index to control looping
  - Stack pointers to manage stack
  - Status/mode of operation

#### ALU
- Carries out logic operations needed during program execution
  - e.g.: comparisons, arithmetic
- Operations performed here usually affect bits in the status register
- Knows what to do because it is controlled by **signals from Control Unit**

#### Control Unit
- "traffic manager" of CPU
- Monitors execution of instructions
- How it works:
  - Extracts instructions from memory
  - Decodes them
  - Make sure data is in right place at right time
  - Tell ALU which registers to use
  - Services interrupts
  - Turns on correct circuitry in ALU 
- Uses a program counter register to find next instruction
- Uses status register to keep track of:
  - Overflows
  - Carries
  - Borrows, etc...
  
  
### Bus