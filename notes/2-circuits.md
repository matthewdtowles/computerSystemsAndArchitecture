# II. Circuits

## I. Combinational Circuits

### A. Boolean Algebra

- Not operator:  A'
- And operator:  
  - A . B
  - A ^ B
  - A B
- Or operator:
  - A + B
  - A v B
- Order of operations:
  1. parentheses
  2. not
  3. and
  4. or
- 0 = false; 1 = true
- Basic laws:
  - a + 0 = a
  - a . 0 = 0
  - a + 1 = 1
  - a . 1 = a
  - a + a' = 1
  - a . a' = 0
  - a + b = b + a
  - a . b = b . a
  - a + (b + c) = (a + b) + c
  - a . (b . c) = (a . b) . c
  - a . (b + c) = a . b + a . c
  - a + b . c = (a + b) . (a + c)
  - (a + b)' = a' . b'
  - (a . b)' = a' + b'
  - (a')' = a

- _Minterm_ - possible combinations of values of the input variables in a truth table.
- Setting up truth table example:
  - M = A'B'C' + A'B'C + A'BC' + AB'C':
  
Minterm | Inputs | Output

        |A |B |C |   M
    
    0    0  0  0    1
    1    0  0  1    1
    2    0  1  0    1
    3    0  1  1    0
    4    1  0  0    1
    5    1  0  1    0
    6    1  1  0    0
    7    1  1  1    0
    
- The truth table above: alternating 0s and 1s frequency decreases as we move from column c to a
  - Conversely, all 0s then all 1 outcomes in first column


### B. Karnaugh Maps (K-maps)

_Refer to paper notes for example map_
- Make it easy to simplify expressions with <= 6 vars
- Adjacent squares are minterms that differ by only 1 bit
  - Can be rep'd by fewer vars, e.g:
    - `AB` same as `ABC + ABC'` or `AB(C + C')`
      - Fewer vars => fewer gates
- Prime implicant - any cluster that can be formed
  - Groups of adjacent cells can be clustered into groups that represent fewer vars
- Essential Prime Implicants - remaining prime implicants after prime implicant clusters are dropped
  - Includes all cells with a 1
- Output of 1 means the minterm is included in solution to the boolean expression
  - Output of 0 means it is not
- Procedure to Simplify Expressions with K-Maps:
  1. Plug in 1/0/x in each minterm cell based on truth table values
  2. Create clusters of adjacent minterm cells of 2 or 4 or 8 1s on the map
      - x's may count as 1s if it helps form larger clusters
      - a cluster may not contain a 0
      - clusters may overlap
  3. Determine prime implicant clusters
      - Eliminate those in which all 1s are covered by another prime imp cluster
      - Remaining are the essential prime imp clusters
      - _Refer to paper notes graph 2.13 for example_
   4. Logically OR the variable expressions that remain to form the simplified minterm expression.


### C. Basic Combinational Logic

#### Logic Gates

- Basic gates (3): 
  - AND
  - OR
  - NOT
- Gates made from basic gates:
  - NAND
    - Not AND
    - Oppposite of AND
  - NOR
    - Opposite of OR
    - Not OR
  - XOR
    - Exclusive OR
- Circuit tracing:
  - Shows how a set of inputs in translates into output
  - How to do it:
    1. assign input values at first set of gates
    2. calc output from those gates - this is the input for the next set of gates
    3. Repeat 1 and 2 until circuit is complete
- Sum-of-products (SOP) form
  - aka disjunctive normal form (DNF)
  - Built with AND gates inputting to OR gate
    - This could be replaced by NAND gates
  - Uses only AND, OR, NOT

#### Other ways to create circuits:
- Product of Sums (POS)
  - Could be built with OR input to AND for the circuit output
    - Could use NOR gates instead too
  - Uses the M = 0 minterms
  - How to do it:
    1. Using all 0 outputs from truth table - write equation for M'
    2. Use DM's law to convert M' to M
      - i.e: the ANDs become ORs and the complements are reversed
      - e.g: ` M' = A'BC ` => ` M = A + B' + C' `


### D. Some Important Circuit Building Blocks

_The following circuits provide the building blocks for CPU and memory_

#### Multiplexers (MUXs)

- Simple MUX has 1 out, 2 sets of inputs
  - One set of inputs is "inputs"
  - Other set of inputs is "selects"
- A MUX with 2^n inputs will have n selects
- MUX connects the single input line selected by the "selects" to the output
  - The output will be same as input line specified by selects
- May include an enable input that must be activated for MUX to work
  - If not activated, output is always 0
- Used in **buses**
- Used whenever a single terminal must obtain one of several possible inputs

#### Decoders

- _n_ inputs will have _2^n_ outputs
- Only one output will be 1 - all others 0
  - The output equal to 1 will correspond with the binary num rep'd by the input
- Must be clear which input is the most significant bit (MSB)
  - Typically noted by largest subscript
- May have an 'enable' input
  - If not enabled, all outputs are 0
- Used to implement combinational circuits
- They form the addressing circuitry of read-only and RAM

#### Half-Adders and Full-Adders

- A full-adder to two half-adders that are connected
- Half-adder:
  - adds two inputs
  - outputs: sum and a carry
  - does not consider carry from previous column
    - another half-adder needed to do this
    
_Half-adder table_:  
__A__B__Carry__Sum  
__0__0___0_____0  
__0__1___0_____1  
__1__0___0_____1  
__1__1___1_____0  

- Sum output = output of XOR gate
- Carry output = output of AND gate
- To perform arithmetic with carry in, must have 2 half-adders connected (making a full-adder)
- _n_-bit words in a computer, _n_ adders required to add 2 _n_-bit words

### E. Arithmetic Logic Unit (ALU)

- Can perform any arithmetic or logical computation
- A set of _n_ one-bit ALUs used to process _n_-bit words
- The rest of section E. is in notebook as it requires graphing and notes on graph


## II. Sequential Circuits

_This section heavily uses pictures and so most notes are handwritten_

### A. Flip-Flops

#### RS Latch

#### JK Flip-Flop

- Having J=K=0 as inputs, 0 or 1 could be output depending on current state

#### D (Data) Flip-Flop
#### T (Toggle) Flip-FLop

- JK and D flip flops:
  - Both have **level-triggered** behavior:
     - if the value of inputs change while clock is 1, outputs change with inputs
- **Edge-triggered** flip flops:
  - Samples the inputs on the rising edge or negative edge of the clock
  - Solves problem of unstable oscillations that can happen when outputs are fed back to inputs
- RS, JK, D, and T inputs all called **synchronous** inputs
  - They use the input values at the positive edge of the clock to determine next state
- Many flip flops also have asynchronous terminals
  - **preset** (pr) forces output of 1
  - **clear** (clr) force output of 0
  - Used to ensure desired values at power up of circuit


### B. Equations for a General Sequential Circuit

_Circuit, explanation, and equations for A Sequential Circuit With Two Inputs and Two Flip-Flops in notebook_

### C. Some Important Sequential Circuit Building Blocks

_These circuits used in building larger circuits like CPU and memory_

#### Counters
- A sequential circuit
- No external inputs
- Consists of simple sequential transitions between states
- Cycles through states 0, 1, ..., _n_-1, 0, ...
- Needs _m_ flip-flops for a counter with _2^m_ different states
  - So if there are 3 flip flops, we can represent states 0 - 7
- State of of flip flops represented in each state by bits
  - E.g.: state _3_ would be `011`
    - This represents the state _FF1 = 0, FF2 = 1, FF3 = 1_

#### Registers
- Contain several interconnected flip-flops
  - Flip flops can store and process single bits of info
- Provide ability to store and manipulate entire words
- Often built with _D flip-flops_

#### Programmable Logic Devices (PLDs)
- Used to realize sequential circuits
- Individual chips that contain a large number of components
- Can be programmed for complex circuit, many circuits simultaneously, or even a simple microprocessor


## III. Finite State Machines

- Finite state machines are a tool to model the desired behavior of a seq. system
- Much like counters, but more complex

