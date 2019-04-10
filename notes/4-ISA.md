# IV: Instruction Set Architecture

## i. Instruction Formats

### i-1 Design Decisions for ISAs
- ISAs are measured by:
    - Amount of space a program requires
    - Complexity of instruction set
        - i.e: the amount of decoding needed to execute an instruction
    - Length of instruction
    - Total number of instructions
- Fixed length instructions easier to decode
    - Could waste space
    - Does not imply fixed number of operands
- If mem has multi-byte words then a single character can only be reached if mem is byte-addressable


### i-2 Little vs. Big Endian
- Endian meaning "byte order"
- Storing from LSB to MSB or MSB to LSB
- **Little Endian**: byte at lower address has lower significance
    - Most PCs
    - Example storing 4-byte integer:
        - ```
            Base address + 0 = Byte0
            Base address + 1 = Byte1
            Base address + 2 = Byte2
            Base address + 3 = Byte3
            ```
- **Big Endian**: MSB first followed by LSB. MSB at lower address.
    - Most UNIX machines and newer RISCs
    - Example storing 4-byte integer:
        - ```
            Base address + 0 = Byte3
            Base address + 1 = Byte2
            Base address + 2 = Byte1
            Base address + 3 = Byte0
            ```
- Example problem:
    - Byte-addressable machine
    - 0x12345678 (32-bits) stored at 0
        - Each digit requires 4 bits = 1/2 byte => 2 digits/byte
    - ```
        Address       | 00 | 01 | 10 | 11 |
        -----------------------------------
        Big Endian    | 12 | 34 | 56 | 78 |
        Little Endian | 78 | 56 | 34 | 12 |
        ```
- Big endian reads more naturally
- Must be accounted for as big endian software must have byte order reversed to work on little endian machine and vice versa
- Big endian conversion to little takes longer as addition is required


### i-3 Stacks vs. Registers (Internal Storage on CPU)
- 3 ways a CPU could store data (could be designed 1/3 ways):
    1. Stack architecture
        - Operands found on top of stack
        - Cannot be accessed randomly
            - Difficult to generate efficient code
    2. Accumulator architecture
        - One operand in accumulator
        - Minimize complexity of machine with short instructions
        - High memory traffic
            - Due to accumulator only being temp
    3. General purpose register (GPR) architecture
        - Most widely accepted today
        - Register sets are faster than memory
        - Easy for compilers to deal with
        - Large number of registers may be used at low cost
        - Longer instructions (all operands must be named) => longer fetch/decode time
            - _Short instruction times are the holy grail of ISA designers_
- If memory is fast => stack is good
- If memory is slow => GPRs are best
- Main differences between GPRs:
    - Number of operands
    - How operands are addressed

#### General Purpose Architecture Classifications (3)
1. **Memory-memory**
    - May have 2 or 3 operands in memory
        - Allows instruction to perform an operation without operand in register
2. **Register-memory**
    - One operand in register and one in memory
3. **Load-store**
    - data must be in registers before any operations on data


### i-4 Number of Operands and Instruction Length
- Instructions can be formatted in fixed length or variable length
- Instructions must be word-aligned for addressing
    - Instructions 1/2 word size waste 1/2 word of space
    - _Variable length instructions still need to be word aligned_
- Common instruction formats have 0 - 3 operands
- Most arithmetic ops have 2 operands
    - Did it with 1 in MARIE - accumulator was implicit
- Third operand could be for destination of results
- Common instruction formats:
    - `OPCODE`
    - `OPCODE + Address` (usually memory address)
    - `OPCODE + Address1 + Address2` (registers or register+memory)
    - `OPCODE + Address1 + Address2 + Address3` (registers or reg+mem combos)
- Stack allows for 0 operands
- `Add` instruction in stack architecture (commutative):
    1. pop the top two operands
    2. push sum onto stack
- `Subtract` instruction in stack architecture (non-commutative):
    - top stack subtracted FROM next top of stack
    1. a = pop(); b = pop();
    2. push(b-a)
- **Infix**:  `x + y`
    - `(x + y) * (w - z) + 2`
- **Prefix**:  `+ x y`
- **Postfix**:  `x y +`
    - `x y + w z - * 2 +`
    - _Walk through postfix problem_:
        - `sumxy diffwz * 2 +`
        - `prodSumxyDiffwz 2 +`
        - `sumOfProdSumxyDiffwzAndTwo`

#### Example:  z = (x + y) + (w * u)
- 3 operands:
    - first operand usually is register (could be destination or source)
    - (destination in following example)
    - ```
        Mult    R1, x, y
        Mult    R2, w, u
        Add     z, R2, R1
        ```
- 2 operands:
    - at least one operand is a register
    - first operand is destination in this example
    - ```
        Load    R1, x
        Mult    R1, y
        Load    R2, w
        Mult    R2, u
        Add     R1, R2
        Store   z, R1
        ```
- 1 operand:
    - must assume a register (like accumulator) is implied as destination
    - ```
        Load    x
        Mult    y
        Store   temp
        Load    w
        Mult    u
        Add     temp
        Store   z
        ```
- O address instructions (stack):
    - has only push/pop operations
    - ```
        Push    x
        Push    y
        Mult
        Push    w
        Push    u
        Mult    
        Add
        Store   z
        ```


## ii. Instruction Types

#### Data Movement
- Most common instruction type
- Data from mem->reg, reg->reg, reg->mem

#### Arithmetic
- Instructions that use ints and floats

#### Boolean
- Instruction for performing AND, NOT, OR, XOR ops

#### Bit manipulation (shift and rotate)
- Used to set/reset individual bits in a word
- Shift and rotate instructions

#### I/O
- Basic schemes for handling I/O:
    - programmed I/O
    - interrupt-driven I/O
    - DMA devices

#### Transfer of control
- Branches
    - unconditional or conditional
- Skips
    - branch instruction with implied addresses
- Procedure calls
    - Special branch instruction that saves the return address

#### Special purpose
- String processing
- High-level language support
- Protection, flag control
- Cache management


## iii. Addressing

### iii-1 Data Types
- Must be hardware support for a data type if instruction references it
- Numeric types: integers, floating points
    - And their subsequent types (signed vs unsigned; size)
- Nonnumeric types:
    - Strings
        - Copy, move, search, modify
    - Booleans
        - AND, OR, XOR, NOT
    - Pointers (addresses in memory)
        - Treated differently than numeric types

### iii-2 Address Modes
- Addressing modes allow for operand bits to represent different things
    - i.e.: operand bits could be a pointer to could be the memory address among other things
        - could also be a constant, register, etc...

#### Immediate Addressing
- Value to be referenced follows the opcode in instruction
- Value is actual value -- not an address
- E.g.: `Load 008` = numeric value 8 is loaded into the AC
- Fast but inflexible

#### Direct Addressing
- Memory address is part of instruction
- E.g.: `Load 008` means the value at mem location 008 is loaded into AC
- Fast, flexible

#### Register Addressing
- Like direct addressing, but with register instead of memory

#### Indirect Addressing
- Bits in address field specify memory address that is to be used as a pointer
- E.g.: `Load 008` means data at address 008 is the actual address of desired operand
- Provides exceptional flexibility

#### Indexed and Based Addressing
- Index Register has an offset that is added to the operand
- E.g.: `Load X`, (R1 = 1) => effective address = X+1
- Based Addressing just uses a Base Address Register instead
- Both uses for accessing array elements, chars in strings

#### Stack Addressing
- Operand is on the stack

#### Other Addressing Modes
- Many variations or combinations of the above
- Auto-increment and auto-decrement
    - inc/dec the register used
    - reduces code size and used in embedded systems

#### Example with Different Addressing Modes
```
// instruction:
Load 800

// register
R1: 800

// memory
___________
Memory     |
800  | 900 |
...  |     |
900  | 1000|
...  |     |
1000 | 500 |
...  |     |
1100 | 600 |
...  |     |
1600 | 700 |
___________
```
Mode:  Value actually loaded into AC  
- **Immediate: _800_**
    - 800 from the instruction
- **Direct: _900_**
    - Memory at 800 = 900
- **Indirect: _1000_**
    - Memory at 800 = 900 which tells us to go to 900
    - Memory at 900 = 1000
- **Indexed: _700_**
    - Instruction operand = 800
    - 800 + R1 = 800 + 800 = 1600
    - Go to 1600 => 700



## iv. Instruction-Level Pipelining
- Pipelining refers to fetch-decode-execute cycle steps that are broken down into smaller steps that can be performed in parallel
    - Result is faster execution
- Mini-steps of fetch-decode-execute cycle:
    1. Fetch instruction
    2. Decode opcode
    3. Calculate effective address of operands
    4. Fetch operands
    5. Execute instruction
    6. Store result
- Once fetch (s1) is completed, next instruction can be started on s1
    - Computer can work on a different step for each different instruction
- Pipeline conflicts prevent the goal of executing one instruction per clock cycle

### iv-1 Pipeline Conflicts

#### Resource Conflicts
- One instruction storing to memory and another wants to fetch from memory at same time
- Resolved by allowing executing instruction to continue
- Some can be resolved by providing separate pathways

#### Data Dependencies
- When result of an instruction that is not available yet, is needed for a following instruction
- Can be resolved with special hardware to detect this conflict
    - Works by inserting a brief delay in the pipeline
    - Could also work by rerouting through special path
- Could be resolved if compiler is able to do it

#### Conditional Branch Statements
- These alter the flow of execution
- Branch prediction guesses which instructions will be needed next
- Compilers rearrange machine code to cause a delayed branch
    - Attempt to reorder useful instructions or add no-op instructions to keep pipeline full
- Another approach:  start fetches on both conditional paths and then just follow the true path



## v. Other Notes and JVM
- Compiled languages -> assembly -> machine code
    - C, C++, Ada, FORTRAN, COBOL
- Interpreted languages -> reinterpreted each time code is run
    - platform independent
    - PHP, LISP, Perl, Python, Tcl
    - Slower than compiled (approx. 100x)
- P-Code languages (compiled and interpreted) -> compiled to intermediate form (p-code) -> interpreted
    - Slower than compiled (approx 10x)
    - Python, Perl, Java

#### JVM
- .java file -> java compiler -> .class file (actual bytecode), (+ JAVA API files) -> JVM class loader -> JVM execution engine



## vi. Module
- Instruction set with _n_-bit op code may perform up to 2^ _n_ different operations.
- 0 op:
    - ```
        PUSH
        PUSH
        MUL
        ```
    - Multiplies the top two operands on the stack
- 1 op:
    - `MUL A` accumulator = accumulator * A
- 2 op:
    - `MUL A, B` A = A * B
- 3 op:
    - `MUL A, B, C` A = B * C

#### Boolean Logic Instructions
- Operates on bit-by-bit basis
- E.g.: 
    - ```
        A:   1011 0011
        B:   1100 0101
        ----------------
        A.B: 1000 0001
        ```
        - AND result is 1 only if both bits are 1

#### Single-Operand Manipulation Instructions
- **Complementing**: flip all bits
    - Complement of 1001 = 0110
- **Negating**: complement and then increment by 1
    - Negation of 1001 = 0110 + 1 = 0111

#### Shift and Rotate Instructions
- **Logical Shift Left**
    - Shift each bit to the left by 1
    - LSB vacancy filled by 0
    - MSB is chopped off
    - `1011 -> 0110`
- **Logical Shift Right**
    - MSB vacancy filled by 0
    - LSB chopped off
    - `1011 -> 0101`
- **Arithmetic Shift Left**
    - The two MSBs must be same to preserve sign of number
    - Double the number when shifted
    - `0011(3) -> 0110(6) -> overflow error`
        - ASL on 0110 would cause the MSB to change which is a change in sign resulting in overflow.
- **Arithmetic Shift Right**
    - Performs integer division by 2 on number
        - i.e.: 3/2 = 2
    - `1010(-6) -> 1101(-3) -> 1110(-2) -> 1111(-1)`
- **Rotate Left**
    - Bits shifted to the left with MSB becoming LSB
    - `1001 -> 0011 -> 0110 -> 1100 -> 1001`
    - _n_ bits being shifted _n_ times results in original number
- **Rotate Right**
    - Just like left but to the right and so LSB becomes MSB
    - `1001 -> 1100 -> 0110 -> 0011 -> 1001`

#### Stack
- Steps to easily convert to postfix:
    - (3 + 4) * (5 + 6)
    - (3 4 +) * (5 6 +)
    - 3 4 + 5 6 + *
    - Stack:
        - `3`
        - `4 3`
        - `7`
        - `5 7`
        - `6 5 7`
        - `11 7`
        - `77`