# III: CPU and Memory

## i. Introduction

### i-1 CPU Basics and Organization

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
  
  
### i-2 Buses
- How CPU communicates with other components
- What a Bus actually is:
  - A shared data path to connect sub-systems within the system
- Allows parallel movement of bits with multiple lines
- Low cost
- Only one device may use at a time
- Speed of bus affected by:
  - length
  - number of devices sharing it
- Devices using bus are divided as:
  - _master_: initiates actions
  - _slave_: responds to requests from master
- May connect point to point or be a common pathway (multipoint bus)
- Bus usually has:
  - _Data lines_
    - contains actual info
  - _Control lines_
    - indicates permissions for devices
    - transfer acks, interrupts, clock sync signals
  - _Address lines_
  - _Power line_
- Types of buses:
  - _Processor-memory_:  short, high speed, very design specific
  - _I/O_: longer, more generic
  - _Backplane_: built into chassis
    - connects processor, IO devices, and memory
- What a PC has:
  - _System bus_:  connects CPU, memory, internal components
  - _External buses_:  for external devices, expansion slots, IO ports
  - _Local buses_:  connect peripheral device direct to CPU
- A bus must be reserved by a master prior to use
  - Only one master can get inundated with tasks
- _Bus arbitration_:
  - for systems with >1 master device
  - gives priority to certain masters
  - balances making sure lower priority devices are not starved out
  - four categories of bus arbitration schemes:
    1. **Daisy chain arbitration**
        - "grant bus" control line from highest to lowest priority
        - does not ensure fairness to lowers
    2. **Centralized parallel arbitration**
        - each device has a request control line
        - centralized arbiter selects who gets bus
        - bottlenecks common
    3. **Distributed arbitration using self-selection**
        - each device has request control line
        - devices themselves determine highest priority
    4. **Distributed arbitration using collision detection**
        - each device may make request
        - if collision detected, device must request again
          - ethernet uses this arbitration

#### Synchronous Buses
- Devices are sync'd by clock
- Bus cycle occurs only at clock ticks
  - Bus clock rate of 133MHz => bus cycle = 1/133,000,000 = 7.52 ns
- Clock inaccuracy leads to problems
- Bus cycle time <= time it takes info to traverse bus

#### Asynchronous Buses
- Handshake protocol enforces timing (not clock)
  - e.g.: 
    1. `reqREAD`
    2. `ReadyDATA`
    3. `ACK`


### i-3 Clocks

- 1 Hz = 1 cycle/second
- Instruction performance measured in clock cycles
- _Clock cycle_: time between clock ticks
- Clock keeps everything in sync (0 to 1 to 0 ...)
- Most machines have a master clock signal
  - regulates CPU and system
- Clock cycle must be long enough to allow register outputs to propagate through circuits to next register
  - If too short: some values will not reach registers
    - Leads to inconsistent state - VERY BAD
- **Minimum clock cycle time** >= maximum propagation delay of the circuit
  - Can't simply shorten the circuit and add registers
    - Registers only change at clock intervals
      - Result would be increase in cycles per execution
- **CPU Time = instructions/program * cycles/instruction * seconds/cycle**
- Architecture of machine has large effect on performance too
  - So two machines with same clock time may not execute instructions in same number of cycles
- _Overclocking_: pushes the bounds of system components to improve performance
  - can cause damage and unreliable performance
  - runs component at or above upper bound specs


### i-4 I/O Subsystem

- Transfer of data between memory and I/O peripherals
- I/O devices connect to an interface
  - Not directly to CPU
  - Interface converts to format acceptable to given device
- CPU communicates to devices via I/O registers
- Interrupts notify CPU that input or output is available for use


### i-5 Memory Organization and Addressing

- Memory is like a matrix of bits
  - Each row is implemented by a register (memory location)
  - Length of row = word size of machine (8-bit, 16-bit etc...)
- Address usually is represented by unsigned int
- Usually byte-addressable
  - each byte has a unique address
  - if machine handles multi-byte words and uses single-byte architecture then least sig byte dictates address
- _Word addressable_: each word, not byte, has own address
  - **MOST CURRENT MACHINES ARE BYTE ADDRESSABLE WITH >= 32 bit words**
- If byte-addressable and words are > 1 byte, must make sure that:
  - word was stored on a natural alignment boundary
  - access starts on boundary
  - i.e.: accomplished with 32-bit words by requiring address to be multiple of 4
    - 4 * 1 byte = 4 * 8 bits = 32 bits
  - Some architectures allow unaligned access
- Memory is built from RAM chips
- 4M x 16 memory means:
  - memory is 4M long and 16 bits wide
  - word = 16 bits, 4M words
  - We would need 2^22 addresses
    - start at 0, end at ((2^22) - 1)
      - Amount of bits required for memory address:  22
- **_2^n_** addressable units of memory requires **_n_** bits to uniquely address each byte

#### RAM Chips
- Chips combined in single memory module for desired size
  - e.g.: to obtain 32K x 16 with only 2K x 8 RAM chips:
    - Simply connect 16 rows and 2 columns together like so:
    - **32K x 16:**
    ```
    Row0:  2Kx8 | 2Kx8
    Row1:  2Kx8 | 2Kx8
    ...
    Row15: 2Kx8 | 2Kx8
    ```
    - Addresses for this memory would be 15 bits
      - ie: 32K = 2^5 * 2^10 = 2^15


### i-6 Interrupts

- Events that alter the normal flow of execution
- Triggered by:
  - IO requests
  - Arithmetic errors (eg: div by 0)
  - Arithmetic underflow/overflow
  - Hardware malfunction (eg: memory parity error)
  - User defined break points (eg: debugging a program)
  - Page faults
  - Invalid instructions
  - Other miscellaneous reasons



## ii. MARIE (Machine Architecture that is Really Intuitive and Easy)

### ii-1 The Architecture
- Binary 2's complement
- Store program, fixed word length
- Word addressable
- 4K words of main memory
  - 12 bits per address (2^10 * 2^2 = 2^12)
- Words have 16 bits
- 16-bit instructions
  - 4 bits for opcode
  - 12 bits for the address
- 16-bit accumulator (AC)
- 16-bit instruction register (IR)
- 16-bit memory buffer register (MBR)
- 12-bit program counter (PC)
- 12-bit memory address register (MAR)
- 8-bit input register
- 8-bit output register


### ii-2 Registers and Buses

In MARIE, 7 register locations:
  1. **AC** - the accumulator.
      - holds data values CPU needs to process
      - general purpose
  2. **MAR** - holds memory address of data being referenced
  3. **MBR** - holds data just read from memory or ready to be written to memory
  4. **PC** - holds address of next instruction
  5. **IR** - holds next instruction
  6. **InREG** - holds data from input device
  7. **OutREG** - holds data for output device


### ii-3 Instruction Set Architecture (ISA)

- Specifies instructions computer can perform
- Interface between SW and HW
- For MARIE instruction set is:
  - Bits 12-15 = Opcode
  - Bits 0-11  = Address
  - eg:  0001 0000 0000 1101 means perform opcode 0001 at address 1101
- MARIE Instruction Set
  - `0001` - **Load X**
    - Load the contents of address X into AC.
  - `0010` - **Store X**
    - Store the contents of AC at address X.
  - `0011` - **Add X**
    - Add the contents of address X to AC and store the result in AC.
  - `0100` - **Subt X**
    - Subtract the contents of address X from AC and store the result in AC.
  - `0101` - **Input**
    - Input a value from the keyboard into AC.
  - `0110` - **Output**
    - Output the value in AC to the display.
  - `0111` - **Halt**
    - Terminate the program.
  - `1000` - **Skipcond**
    - Skip the next instruction on condition.
  - `1001` - **Jump X**
    - Load the value of X into PC.


### ii-4 Register Transfer Notation
- An instruction may involve multiple operations
  - e.g.: `Load`:  first address from instruction must be loaded in MAR; then data in memory at this location must be loaded into the MBR; then MBR must be loaded in the AC -- all this to load contents of given mem location into AC register
  - These mini-instructions are called _microoperations_
- Notation for microoperations is _Register Transfer Notation (RTN)_
  - `M[X]` indicates actual data stored at location `X` in memory
  - `<-` indicates a transfer of info

#### RTN for instructions of MARIE
- **Load X**
  - _Note instructions on different lines cannot be exec'd in same cycle_
```
MAR <- X
MBR <- M[MAR], AC <- MBR
```
- **Store X**
```
MAR <- X, MBR <- AC
M[MAR] <- MBR
```
- **Add X**
```
MAR <- X
MBR <- M[MAR]
AC <- AC + MBR
```
- **Subt X**
```
MAR <- X
MBR <- M[MAR]
AC <- AC – MBR
```
- **Input**
```
AC <- InREG
```
- **Output**
```
OutREG <- AC
```
- **Halt**
  - No ops. Machine just stops execution.
- **Skipcond**
```
if IR[11–10] = 00 then           {if bits 10 and 11 in the IR are both 0}
    If AC < 0 then PC <- PC+1
else If IR[11–10] = 01 then      {if bit 11 = 0 and bit 10 = 1}
    If AC = 0 then PC <- PC + 1
else If IR[11–10] = 10 then      {if bit 11 = 1 and bit 10 = 0}
    If AC > 0 then PC <- PC + 1
```
- **Jump X**
```
PC <- X
```


## iii. Instruction Processing

### iii-1 The Fetch-Decode-Execute Cycle
- The steps a computer takes to run a program:
  1. `MAR <- PC` Copy contents of the PC to the MAR
  2. `IR <- M[MAR]` Fetch instruction in memory at address in MAR and place instruction in IR
      - `PC <- PC+1` increment PC 
          - PC now points to next instruction in program
          - PC only incremented by 1 in example because MARIE is word addressable
          - if byte addressable, increment by 2 since 16-bit system (+ by 4 if 32 bit...)
  3. `MAR <- IR[11-0]` Copy rightmost 12 bits (address) of IR into MAR
      - `decode IR[15-12]` decode leftmost 4 bits for opcode
  4. If needed: `MBR <- M[MAR]` use address in MAR to get data from memory and put data into MBR, execute
- Summary of steps:
    ```
    MAR <- PC
    IR <- M[MAR], PC <- PC+1
    MAR <- IR[11-0]
    IR[15-12] {decode}
    MBR <- M[MAR] {if needed}
    ```
- Computers execute millions of these cycles in <1 second


### iii-2 Interrupts and I/O
- InREG holds data transferred from input device (keyboard, mouse)
- OutREG holds data ready to send to output device (monitor, speakers)
- Timing very important
- To avoid missing characters or reading characters multiple times:
  - MARIE uses _interrupt driven I/O_
  - CPU executes an I/O instruction and notifies appropriate I/O device
  - CPU continues doing other stuff til device is ready
  - Device sends an interrupt signal to CPU
  - CPU processes interrupt and continues normal fetch-decode-execute cycle
  - CPU deals with this by checking for interrupts at the beginning of each fetch-decode-execute cycle
- Interrupt sent via special register or a status/flag register



## iv. A Simple Program
