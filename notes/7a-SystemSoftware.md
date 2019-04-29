# VII: System Software

## i. RISC vs CISC
- RISC has:
    1. simple instruction set
        - executed at high speed
        - uses hard-wired CPU with pipelined instructions
            - fetch next instruction while executing current instruction
    2. register-oriented instructions
        - only a few memory access instructions (LOAD, STORE)
    3. fixed length and format instructions
    4. limited addressing modes
        - leads to simplified CPU design
        - 1 or 2 modes
    5. large bank of registers
        - uses registers to avoid memory access

## ii. Pipelines and Superscalar Processing
- Pipelining:  one instruction is decoded while another is fetched and another is executed, etc...
- **Superscalar**: processor with multiple execution units
    - Scalar would be a processor with one execution unit
    - Fetch/decode/execution are separate components to improve pipelining
- Scalar processing:
    - ```
        Instruction 4 | - | - | - | f | d | e
        Instruction 3 | - | - | f | d | e | -
        Instruction 2 | - | f | d | e | - | -
        Instruction 1 | f | d | e | - | - | -
        ```
- Superscalar processing (2 units)
    - ```
        Instruction 4 | - | f | d | e
        Instruction 3 | - | f | d | e
        Instruction 2 | f | d | e | -
        Instruction 1 | f | d | e | -
        ```
- **Multiprogramming**: running more than one program at a time
    - many programs in main memory executed with single CPU
    - one CPU
    - uses context switching

- **Multiprocessing**: using more than one CPU at a time
    - processing many processes simultaneously via many CPUs
    - multiple CPUs
    - parallel processing

- **Multithreading**
    - Allows for concurrent processing of the same process
    - Increases utilization of a single core to increase throughput
    
## iii. Operating Systems
- OS controls memory management, IO, GUI
- OS are event driven
- Main factors in OS design:
    1. Performance
    2. Power
    3. Cost
    4. Compatibility
- OS is interface between hardware and users, apps
- Any 2 processes that need to work together rely on OS to facilitate their inter process
- **Context switch**: when a process gives up CPU, state is saved and another process is resumed
    - All of resumed or new process state loaded in registers/mem as well
- **Thread**: smallest schedulable unit in a system
    - Share same execution env as parents
    - Context switching in threads generates less overhead than with entire process
- Manages file and directory deletion/creation
- Protects system:
    - Active processes limited to execution within memory space allocated
    - All IO requests pass through OS
    - Protects against unauthorized use by executing commands as correct user
    - Controls user access (username and pass)
    - Protects CPU from infinite loops
    - Protects against buggy software from affecting other processes unrelated

#### The Kernel
- **Kernel**: core of the OS
    - Used by:
        - Scheduler
        - Process manager
        - Resource manager
        - IO manager
    - Responsible for:
        - scheduling
        - synchronization
        - protection/security
        - memory management
        - dealing with interrupts
    - Has primary control over system hardware, includes:
        - interrupts
        - control registers
        - status words
        - timers
    - Loads all device drivers
    - Provides common utilities
    - Coordinates IO activity
    - Must know specifics of the HW to make this all work
- Microkernel vs Monolithic Kernels
    - Microkernel provide basic OS functions and rely on other modules to perform specific tasks
    - Monolithic provide all essential functions in a single process
    - Windows is Micro
    - Linux, Mac, DOS are Monolithic

#### Resource Management
- 3 Major resources managed by OS:
    1. CPU
        - Controlled by the scheduler
    2. Memory
        - Translating virtual address to physical addresses
        - Transfer pages to and from disk
        - Maintaining memory page tables
        - Determines main memory allocation
        - Tracks free frames
        - Garbage collection as memory space is deallocated
            - Coalescing small portions of free mem to larger, more usable chunks
    3. IO
        - Mediates IO usage
        - Allows apps to request IO service through OS
        - Calls device drivers for IO


## Programming Tools

#### Compilers
- Read entire source code before producing binary stream
1. **Lexical analysis**
    - extract meaningful language primitives/tokens from source code
        - tokens are reserved words, operators, literals, variables
        - create a symbol table from all of this
    - Lexical errors occur when characters not in the language are found
2. **Syntax analysis**
    - creates a parse tree
    - if invalid variable exists, error
    - detects illegal constructions (e.g.: a = b + c = d)
3. **Semantic analysis**
    - Uses parse tree as input
    - Checks for appropriate data types by checking symbol table
4. **Synthesis phase**
    - creates a pseudo-assembly code from parse tree
    - outputs three address code
    - output enables compilers to be portable to many diff't computers
5. **Optimizer**
    - 3-address code translated to form ISA can understand
        - typically 2-address code
    - removes unnecessary temp vars
    - collapses repeated expressions into single ones
    - flags dead code
    - takes into account different memory and register organizations
6. Binary object code suitable for linking and execution created

#### Interpreters
- Produce one source statement at a time
- Slower execution speed than compilers
- Does 5/6 steps done by compilers in real time
    - Does not allow for code optimization
- Error detection limited to syntax and var checking
- Allow source-level debugging (good for beginners and end users)