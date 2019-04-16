# V: Memory

## i. Types of Memory
- **RAM** (read/write)
    - Volatile - loses everything when power is off
    - Stores programs and data computer needs when executing programs
    - 2 Types of chips used to build RAM:
        1. **DRAM** (Dynamic RAM)
            - Has tiny capacitors that leak electricity
            - Requires recharge every few ms
                - To maintain data
            - Denser than SRAM (bits/chip)
            - Uses less power and generates less heat
            - Used in main memory
        2. **SRAM** (Static RAM)
            - Holds contents as long as power available
            - Faster, more expensive
            - Used for cache
- **ROM** (read only)
    - Typically stores critical info for OS
        - e.g.: Program to boot the computer
    - Used when program need not change


## ii. Memory Hierarchy
- Faster memories are also costlier
- Mix of memories used to balance speed vs cost
- Memory hierarchy: (size increases as you go down)
    - Registers (1 - 2 ns access time)
    - Level 1 Cache (3 - 10ns)
    - Level 2 Cache (25 - 50 ns)
    - Main Memory (30 - 90 ns)
    - Fixed Rigid Disk (5 - 20 ms)
    - Optical Disk (100ms - 5s)
    - Magnetic Tape (10s - 3m)
- Processor sends request to fastest, smallest partition of memory
    - Typically this is the cache
    - Registers tend to be for special purpose
    - If found in cache, data can be loaded quickly
        - Otherwise, the request is forwarded to the next lower level
        - Once found, block that data is in is transferred to cache
            - This helps since the other data in block is likely to be referenced in future

### ii-1 Locality of Reference
- **Temporal locality** - Recently accessed items to be accessed again
- **Spatial locality** - Accesses tend to be clustered in the address space
- **Sequential locality** - Instructions tend to be accessed in order


## iii. Cache
- Accessed by content -- not by address
- Made of SRAM (main memory uses DRAM)
- Cache location for new block depends on:
    1. **Cache mapping policy**
    2. **Cache size** - is there enough room for a new block?

### iii-1 Cache Mapping Schemes
- CPU converts main memory address into a cache location
    - This is how CPU knows where things are in cache once moved there
- Address conversion done by dividing main memory address of the data into distinct groups (called fields)
    - Usually this will lead to 2 - 3 fields
- One field of main memory address points to a location in cache
    - Either the data will be there or it is the location in which it will go to
- Cache block referenced is validated
- Tag field in cache block then compared to tag field of address
    - If same, then we have found desired cache block (a cache hit)
- Now the desired word must be located in the block
    - Done using the word field
- Cache block typically has:
    - block number/id
    - tag
    - data
    - valid (0 or 1)

#### Direct Mapped Cache
- Assigns cache mappings in chunks
- If cache has blocks (0 - 9) then memory block 0 maps to cache 0
    - m1 -> c1, ..., m9 -> c9
    - m10 -> c0, ..., m19 ->c9
    - m20 -> c0, ... you get the picture
    - So memory blocks ending in 0 all compete for cache block 0
- CPU knows which block actually resides in cache block by the tag field
- Format of main memory address with direct mapping:
    - **| TAG | BLOCK | WORD |**
    - Size of each fields depends on physical limitations
    - WORD identifies a word from a specific block
        - must contain appropriate number of bits to do this uniquely
    - BLOCK must select a unique block of cache
    - TAG is whatever is left over
        - Uniquely identifies the block
    - TAG.size + BLOCK.size + WORD.size = Main memory address number of bits
- Example:
    - given:
        - memory consists of 2^14 words
        - cache has 16 blocks
        - 8 words/block
    - Memory has 2^11 blocks (2048)
        - (2^14)/8  =  (2^14)/(2^3)  =  2^11
    - Each main memory address requires 14 bits
        - 2^14 words in memory, ergo 14 bits/address
    - 3 rightmost bits for WORD
        - 8 words/block
        - 8 = 2^3, => 3 bits/word
    - 4 bits to the left of WORD is BLOCK
        - 4 bits as cache has 16 blocks = 2^4
    - 14 - 4 - 3 = 7 bits for TAG
    - **| TAG, 7 | BLOCK, 4 | WORD, 3 |**
- Example 2:
    - given:
        - 15-bit memory addresses, 64 blocks of cache
        - 8 Words/block
    - How many bits for WORD field?
        - 3:=>  8 words/block and 8 = 2^3
    - How many bits for BLOCK field?
        - 6:=>  64 blocks = 2^6 blocks
    - How many bits for TAG field?
        - 6:=> 15 - 6 - 3 = 6
    - So if CPU generates mem address (1028):
        - `000010 000000 100`
        - CPU looks in block `0` of cache
        - If tag `000010` is found, then the word at offset `100` (4) in this block would be returned to CPU
- If block is occupied when a new block must be placed:
    - block in cache is written back to main memory if modified
    - or overwritten if unchanged
- Inexpensive, restrictive
    - Issues if certain blocks get used much more than others -- inefficient use of cache

#### Fully Associative Cache
- Allows a main memory block to be placed anywhere in cache as opposed to having a unique location specified for each main memory block
- Only way to find a block is to search all of cache
    - A single search compares requested tag to all tags in cache to determine if data block is in cache
- Expensive - requires special hardware
- Main memory address is two pieces:  **| TAG | WORD |**
- WORD and TAG still calc'd the same way as Direct Mapping
- When there is no match, we have a _cache miss_
    - Block transferred to cache from main memory
- If cache blocks are full and a new block must be placed:
    - Replacement decided with an algorithm (varies)
        - examples of algorithms:
            - FIFO
            - Least recently used
- Very expensive, not restrictive
- Efficient use of cache space, but so costly to look up each time

#### Set Associative Cache
- Combo of Direct Mapping and Fully Associative
- Uses address to map block to certain cache location like Direct Mapping
    - Address maps to a _set_ of many cache blocks rather than one
- So addresses look like:  **| TAG | SET | WORD |**
    - The SET will have _N_ BLOCKS in it
- Basically, Direct Mapping is this where _N_ = 1
- Example: 
    ```
    SET-0(
        (TAG, BLOCK-0, VALID?), 
        (TAG, BLOCK-1, VALID?), 
        ..., 
        (TAG, BLOCK-N, VALID?)
    ),
    SET-1(
        (TAG, BLOCK-0, VALID?), 
        (TAG, BLOCK-1, VALID?), 
        ..., 
        (TAG, BLOCK-N, VALID?)
    ), 
    ..., 
    SET-K(
        (TAG, BLOCK-0, VALID?), 
        (TAG, BLOCK-1, VALID?), 
        ..., 
        (TAG, BLOCK-N, VALID?)
    )
    ```
- Mapping example:
    - Given:
        - memory = 2^14 words
        - cache = 16 blocks
        - 2-way set assoc mapping
        - 8 words/block
    - 2 blocks per set b/c 2-way set
    - 8 sets
    - set field must have 3 bits
        - 2^3 = 8
    - 14 bits for address in total (11 left now)
    - 3 bits for word field
        - 2^3 = 8
        - 8 words/block
    - TAG = 14 - 3 - 3 = 8


### iii-2 Replacement Policies
- Replacement policy:  which block in cache should be replaced by new one
- Direct mapping does not need replacement policy
    - Location for each block is predetermined
- **Optimal alrgorithm** is benchmark to measure all other replacement policy algorithms
    - Has lowest _miss rate_
        - Lowest number of times that we look into cache and do not find our block
- Goal:  replace the block that will not be used for the longest amount of time
- Example algorithms:
    - **Least Recently Used** (LRU)
        - Requires timestamps added to blocks to keep history of access to blocks
            - This is very expensive
            - There are ways to approximate this though
    - **FIFO**
    - **Randomly select** victim block
        - LRU and FIFO can thrash (throwing out a block and bring it back repeatedly)
        - Difficult to implement truly random
        - May decrease avg performance


### iii-3 Effective Access Time and Hit Ratio
- **Effective Access Time (EAT)**:  average time per access in which memory performance is measured
- Weighted avg using hit ratio and relative access times
    - e.g.:  `cache access = 10ns;  main memory access time = 200ns;  cache hit rate = 99%;`
        - `EAT = .99(10ns) + 0.01(200ns) = 9.9ns + 2ns = 11.9ns`
- For a two-level memory:  `EAT = CacheHitRate * CacheAccessTime + (1 - CacheHitRate) * MainMemoryAccessTime`


### iii-4 When Caching Breaks Down
- Caching does not work well when programs exhibit bad locality
- Object-oriented programming can cause programs to exhibit less than optimal locality
- Another example of bad locality can be seen in two-dimensional array access
    - If cache can hold all but one row of the array:
    - And if a program accesses the array one row at a time, the first row access produces a miss, but once the block is transferred into cache, all subsequent accesses to that row are hits
    - 5 x 4 array would produce 5 misses and 15 hits over 20 accesses


### iii-5 Cache Write Policies
- When a cache block is modified (dirty blocks of cache), write policy determines when main memory is updated to match
- **Write-through** updates cache and memory simultaneously on every write
    - Slower - requires memory access each write
    - Ensures cache consistent with memory
    - Most accesses are reads, so slow down negligible
- **Write-back** updates memory when cache block will be removed from cache
    - Faster
    - Memory traffic reduced
    - Downside if a process terminates/crashes before write to memory then data in cache may be lost


## iv. Virtual Memory
- 