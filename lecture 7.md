# lecture 7

## computers need memory
- CPU needs to interact with memory to complete computational tasks: fetch instruction, load data, store data, etc

### fetch execution
- the execution of instructions related to memory operations take much longer time (hundreds of CPU clock cycles) — in fact, the performance of most modern computers are limited by the bandwidth of the connection between the CPU and the main memory, the so-called **memory bottleneck**

## memory bottleneck in computer
- computer system’s processing capability is limited due to the **RAM being insufficient**
    - fastest memory also has drawbacks (expensive, small capacity)

# computer memory
- computer memory refers to any physical device capable of storing information
    - **characteristics of memory**: a wide range of storage technologies, resulting in various access speed, capacity, cost, etc
- **memory organization**: select the right memory under design constraints, and put it to the right place

## technologies
- memory can be categorized based on technologies
    
    | SRAM (static random access memory) |
    | --- |
    | DRAM (dynamic random access memory) |
    | PROM (programmable read-only memory) |
    | magnetic disk |

## access method — sequential access
- memory is organized into units of data (**records**)
- we can access each record in sequence: move from a current location to the desired location sequentially— as a result, the **time to access** an arbitrary record is highly variable

## access method — direct/random access
- each location in the memory has a **unique address**
- **direct access** often refers to secondary devices such as disks (access the block of data) while **random access** often refers to main memory or cache
- the **time to access** a given location is typically independent of the sequence of prior accesses and is often constant

### latency for access time

| random access | the time it takes to perform a read or write operation |
| --- | --- |
| non-random  | from the time the instruction is issued to the time the data position is located for read/write operation |

## memory cycle time
- primarily applied to random access memory (RAM)
- it consists of the access time plus any additional time required before a second access can be made (cycle)
- an associated concept is **transfer rate = 1/memory cycle time**

-----
- DDR3: a type of RAM technology
- MT/s is short for megatransfers (or million transfers) per second and is a more accurate measurement for the effective data rate (speed) of DDR
- DDR: allows to transfer data on both rising edge and falling edge of clock signal (so **transfer rate > clock frequency**)

## physical features

| volatile | need power to maintain the stored information (memory is lost when power is off), e.g.,  registers, RAM |
| --- | --- |
| non volatile | no need to be charged all the time, e.g., hard drive |
| erasable | can be deleted or rewritten with new data, e.g., registers, hard drive |
| non erasable | cannot be deleted/rewritten, e.g., ROM (Read Only Memory) |

- **internal**:
    - registers in processor, main memory, cache

- **external**:
    - the outside storage devices, such as disk and tape, are accessible to the processor via I/O controllers

> our goal is to have a large, fast, and cheap main memory, but we cannot achieve this using a single type of storage— we need to use a **hierarchal system of memories**

## expose hierarchy
- let the programmer to decide how to best allocate the memory resources for each particular computation

## hide hierarchy

- **for programmer**: single memory with single address space
- a memory system “behind the scene” to move the data between the levels of the hierarchy
    - it needs to automatically arrange for the right data to be in the right place at the right time

### example: two-level memory
- the processor has access to two levels of memory

- level-1: capacity C1 = 100 words, access time T1 = 0.1s

- level-2: capacity C2 = 1000 words, access time T2 = 1s
- processor wants to find some data in the memory
    - **access mechanism**: CPU checks Level-1 memory, if some data is in Level-1, directly access the data, otherwise, move the data from Level-2 to Level-1 first, and then access the data from Level-1
- **access time: T1 (hit) vs. T1 + T2 (miss)**

- if the hit ratio is high, the average access time is close to T1— we can reduce the average access time only if "frequently-accessed data" are placed in faster memory at a higher chance

## in the hierarchy
- **closer to the processor**: we have faster but lower-capacity, more expensive memory

- **further to the processor**: we have slower but higher-capacity, less expensive memory
- we can "allocate" the data such that frequently accessed data is stored in faster memory

## locality of reference
- during program execution, memory accesses by processor tend to cluster
    - access to address X at time t implies that access to address X+∆ X at time t+∆t becomes more probable as ∆X and ∆t approach— programs typically contain loops/subroutines so they repeatedly access a small set of instructions

## clustering effect
- during some specific time period, processor wants to access a portion of main memory (one cluster), so over time, processor accesses different clusters

# cache memory
- computer memory of short access time used for storage of frequently or recently used instructions or data
- cache memories are **small, fast SRAM-based memories** **managed automatically in hardware**—located in CPU or on motherboard close to CPU
- CPU looks first for data in caches (e.g., L1, L2, and L3), then in main memory
  
### concept
- cache is fast but has relatively small capacity
- main memory contains many blocks (each block consists of many words) while cache contains copies of some blocks
    - when CPU wants to read a word: if the word is in cache, access the word, else a block of main memory (containing that word) is read into cache, then the the word is delivered to CPU

## locality
- we can exploit locality to increase the chance that the data is found in cache

- **temporal locality**: the same thing again

- **spatial locality**: something near that thing

```c
total = 0;
for(i = 0; i<n; i++) {
	total += a[i];
return total;
```

- due to **locality of reference**, the hit ratio is high (words can be found in cache with high chance)— as a result, there is memory with large capacity and fast access speed

- **word**: the basic memory unit (could have different lengths in different systems)
    - each word has a unique n-bit address – so, the memory is composed of $2^n$ addressable words

- **block**: consists of a fixed number of K words; together we have **M = $2^n/K$ blocks**
- the cache consists of m lines (**cacheline**)**,** where each line has a length of K words (the size of one block in memory)— m lines in cache vs M blocks in memory
- **access mechanism**: when we want to access a word in the memory, the whole block containing that word is copied into one line of cache— **cacheline is the basic access unit for cache**
    - one cacheline cannot be permanently allocated to one memory block— use the **tag** to indicate which block is stored in cache
      
### basic element of cache design
- the essential problem we want to address: mapping M memory blocks to m cache lines (m << M)

## mapping

- a mapping function defines where the needed blocks in memory are loaded to cache (M blocks, m positions)

- **direct mapping**: a kind of fixed mapping

- **associative mapping**: any block can go to any cache line

> **Q = M/m**, Q blocks are mapped to one single cache line

### example
- map all students to 10 seats using direct mapping
    - suppose SID has 8 digits, split SID into 2 parts: first 7 digits and the last digit
    - use your last digit as your seat number—  all students are divided into 10 groups, it is a **fixed mapping**
    - a student can use the first 7 digits as a **tag**— the tag is unique for that particular student
    - **SID** **= memory address** (tag in cache is a portion of memory address)

- equivalently, the mapping can be implemented through **modular operation**: i = cache line number, j = main memory block number, m = number of lines in cache

# direct mapping
- use main memory address to implement direct mapping— bear in mind that **cacheline = memory block** (they contain the same number of words)
    - together, there are **2^(s+w)** words in main memory

- the s bits in the address tell us which line in the cache is used to store that block
    - e.g. s = 5 bits, r = 3 bits— 01011
- given a memory address (s + w) bits, we know **which block it is through the s bits**— among the s bits, there are r bits, which tell us **the line number in cache that stores this block**
    - as there are more blocks than lines (s > r), the **remaining (s - r) bits serve as a tag for the cache line**, telling us which block this line is currently storing

- $m = 2^r$
- how does CPU access a word with an address?
    1. given an address **(s+w) bits**
    2. use r bits to locate the line in cache
    3. compare the (s-r) bits with the tag:
        - if match, the line stores the desired block; use w bits to identify the desired word
        - if no match, access the memory, copy the block to the line

### example

- s = 4 bits, r = 2 bits
    - 01 11 — 11 determines which line you should go to: 00 11, 01 11, 10 11, 11 11
- the four blocks ending with 11 will go to the same line since they are only differentiated by the tag (first two bits)
- **modular**: 01 11 = $0*2^3 + 1*2^2 + 3$
    - 01 11 % 4 = 3

- exercise
    - word = one byte, block = 4 words (4 bytes)
    - main memory has 16 Mbytes (note: 1 MB = 1024 KB; 1 KB = 1024B); that is, 2^{24} bytes
    - cache has 64 KB = 2^{16} bytes
    - **how many lines are there in the cache?**
        - 2^{14} lines → among the 22 bits, r = 14 bits, 8 bits for the tag

- the first two hexadecimal digits serve as the **tag**

- blocks in the same row are mapped to the same cache line, but they have a distinct tag number
- multiple blocks to one line
    
    
    | address | s + w bits |
    | --- | --- |
    | cache will interpret it as three fields | tag (s-r), line (r), word (w) |
    | line (r) | determines which line in cache the block will map to |
    | tag (s-r) | determines the current block that line is storing |
    - CPU can directly check that line, and compare the tag field to determine whether the block is now in the cache

## pros and cons
- simple and inexpensive to implement
- **main disadvantage**: multiple blocks in memory are mapped to a fixed cache line
    - if a program happens to access words repeatedly from two different blocks that map into the same line*,* **the two blocks will be continually swapped in the cache**— low hit ratio

# associative mapping
- a block in memory can be mapped to any line in cache— cache will interpret the address as two fields tag and word
- address = s + w bits; 2^s blocks in memory
    - **s serves as the tag**, and it is stored together with the data

> note: there is no field to determine the line number

### example
- memory has 2^{22} blocks
- each block has 4 bytes (data = 32 bits)
- address is 24 bits = 22 bits (s) + 2 bits (w)
- tag: 22 bits
- a block can be mapped to any line in cache— as long as there is a seat, you can sit which means better space utilization (higher hit ratio!)

## implementation of associative
- CPU needs to check every tag (sequential or parallel) to determine if there is a hit

## pros and cons
- no block-level conflicts
    - **for direct mapping**: two blocks are assigned to same cache line, may need to replace one block in case of conflict
    - **for associative mapping**: no conflict as long as there is still space in cache
- checking tags is more complex
    - **parallel reads** – lots of reading
    - **serial reads** – lots of waiting

### direct vs associative:

|  | direct | associative |
| --- | --- | --- |
| tag size | smaller | larger |
| SRAM overhead | less | more |
| controller logic | less | more |
| speed | faster | slower |
| price | less | more |
| scalability  | very  | not very |
| # of conflict misses | lots | zero |
| hit rate | low | high |

## replacement algorithms for DM
- this is not a problem for direct mapping
    - multiple blocks are mapped to the same cache line— when a new Block B comes, if the line is occupied by Block A, replace A with B

| LRU  | replace the block that has been in the cache longest with no reference to it 

​there requires some extra index to record the time when a line is referenced – the price need to pay 

simple for two-way set-associative mapping: use a single bit USE to indicate the most recently used line in the set |
| --- | --- |
| FIFO | replace that block that has been in the cache longest  |
| LFU | associate a counter to each line |
| random | randomly pick one to replace  |

# write operation

- **read**: about finding the word/block in cache

- **write**: save word to memory (including cache)

- the problem is the inconsistency between cache and main memory
    - cache stores the copy of the same data in main memory but two pieces of data can be modified from different sources

### why does inconsistency happen?
- **multiple cores**: each core has its own cache, which may store the same block in memory
- I/O can directly access main memory

## write policy
| write through | all writes go to cache as well as main memory | • multiple cores need to monitor main memory traffic to keep local (with respect to core) cache up to date
• lots of traffic
• slows down writes |
| --- | --- | --- |
| write back | updates initially made in cache only, then memory | • use an extra bit along with each cache line to indicate whether there's update in this line (set the bit if there's update)

• when this cache line needs to be replaced, check this bit, write back to memory if the bit is set (such that updates are not lost) |

## cache performance metrics
- we need to measure how good a cache design is
- miss rate = 1 – hit rate (ratio)
- fraction of memory accesses not found in the cache (misses/accesses)— typically, 3 - 10% for L1 cache; can be quite small (< 1%) for L2 cache, depending on size, etc

### hit time
- time to deliver a word in the cache to the processor (includes time to determine whether the word is in the cache)
- typically, 1-2 clock cycle for L1; 5 -20 clock cycles for L2

### miss penalty
- additional time required because of a miss (need to access main memory)
- typically, 50 – 200 clock cycles for main memory
