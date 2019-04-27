# VI: Input/Output

## i. Amdahl's Law

- Overall speedup of a system depends on speedup in component and how much component is used
- Amdahl's law symbolically:
    - `S = 1 / ( (1 - f) + (f/k) )`
        - _S_: speedup
        - _f_: fraction of work performed by improved component
        - _k_: speedup of new component


## ii. I/O Architectures

- **Protocol**:  form and meaning of signals between sender and receiver
- **Handshake** protocols:  receiver must acknowledge commands/data sent to it
    - or indicate ready status to receive
- Devices handling large data blocks, like printers usually have buffer memory
    - allows data sent over without worrying about mechanical bottlenecks 

### ii-1 I/O Control Methods
- Four general I/O control methods
    - **programmed I/O**
        - at least one register for the exclusive use of each I/O device
        - CPU polls registers (constantly asking, 'are you ready?')
        - Constant register polling is a problem
        - CPU is in a continual "busy wait" loop until it starts servicing an I/O request
        - best suited for special-purpose systems
            - e.g.: ATMs
    - **interrupt-driven I/O**
        - devices tell the CPU when they have data
        - CPU proceeds with other tasks until a device requesting service interrupts it
        - signaled with a bit in the CPU flags register called an interrupt flag
        - once the interrupt flag is set:
            - OS interrupts program currently executing
                - saving that program's state and variable information
            - system fetches address vector that points to the address of the I/O service routine
            - when done CPU goes back to program it was executing
        - service routines can be modified to accommodate hardware changes
        - data moved at one-byte or in small blocks
        - often used by small systems (what you probably use most days)
    - **direct memory access (DMA)**
        - CPU offloads execution of IO instructions
        - CPU provides DMA controller with:
            - location of bytes to be transferred
            - number of bytes
            - destination device or memory address
        - when values are placed, CPU signals DMA and proceeds
            - DMA takes care of details with IO
        - DMA interrupts CPU at completion/error time
        - bus master priority given to IO over CPU due to tight timing parameters for IO
            - IO bus usage typically bursty or in chunks
            - if not enough time in between IO usage, system may appear to crawl during IO
        - block-oriented; interrupts CPU only after completion
        - CPU responsible if failure occurs
    - **channel-attached I/O**
        - typically used by mainframes as an intelligent form of DMA interface
        - IO processors control various IO pathways (channel paths)
        - channels combined for slow devices
            - one controller to manage many devices
        - fast devices use _selector channels_
        - slow devices use _multiplexor channels_
        - channels driven by cmalls CPUs called IO processors (IOPs)
        - IO programs have special words to control IO devices
        - when IO program is in memory:
            - start subchannel command issued - location of program
            - completion info goes to memory
            - interrupt sent to CPU
            - CPU acts on info given
        - must steal memory cycles from CPU (like DMA)

### ii-2 I/O Bus Operation
- memory bus = synchronous (sender and receiver share common clock)
- IO buses = asynchronous (also require clock for bit timing and signal transitions)
- IO devices negotiate with each other to access bus


## iii. Magnetic Disk Technology
- **Sector**: unit of storage on a disk drive
    - foreach sector, same number of bytes
- **Track**: a division of disk drive with many sectors
    - one concentric circle in the disk drive
    - foreach track, same number of sectors (usually)
    - Consecutively numbered
        - starting at the outer edge with 0

### iii-1 Rigid Disk Drives
- contain control circuitry + one+ platters
    - platters have a thin film of magnetizable material
- **Head crash**: when read/write head touches the surface of the disk
    - read/write heads float a few microns over the disk/platters
- **Seek time**: time for disk arm to get to position over required track
- **Disk directory**: maps logical file info
- **Rotational delay**: time for required sector to position under read/write head
- **Access time** = seek_time + rotational_delay
- **Transfer time** = access_time + time to read data from disk
- **Latency**: time taken for sector to move beneath r/w head after disk arm positioned over track
    - Latency = ( ((60 seconds / disk rotation speed) * (100 ms / 1 second)) / 2 )


## iv. Optical Disks

### iv-1 CD-ROM
- Plastic disk with reflective aluminum film
- Aluminum reflects light
- Light from laser diode beneath disk
- Reflected light passes thru a prism into a photodetector
- Photodetector converts light to electrical signals
- Electrical signals sent to decoder electronics
- **Lands** and **pits** are flat and bump areas on disk
    - Pits make 0s and lands make 1s
- Tracks at center have same bit density as outer edge
    - Unlike magnetic storage
- Data stored in 2352-byte sectors
- Sectors have 98 588-bit channel frames
- Channel frames consist of:  synchronizing info, header, & 33 17-bit symbols (payload)
    - 17-bit symbols encoded using an RLL(2,10) code called **EFM** (8 to 14 modulation)
- Disk drive electronics interpret channel frames to make **small frames**
- Small frames are 33 bytes wide
    - 32 for user data
    - 1 for subchannel info
        - 8 subchannels: P,Q,R,S,T,U,V,W
            - P denotes start/stop times
            - Q has control info
            - R,S,T,U,V,W only for audio applications

### iv-2 DVD
- Digital versatile disks
- Quad-density cds
- Rotate 3x faster than CDs