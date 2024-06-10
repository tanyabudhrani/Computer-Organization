# lecture 8

# internal memory
- **internal** means that the CPU can have direct access — main memory, registers, cache, and ROM (read only memory)

| implemented using semiconductor technology |
| --- |
| random access method |
| cope with the speed of CPU |

## internal memory types
- **random access memory (RAM)**: it is a misuse of term— all the above memory use random access method; however, we usually use RAM to refer to main memory

## memory cell
- despite of the memory types, all semiconductor memory cells share common properties
    - two stable states, used to represent 0 and 1
    - can be written into (**set the state**)
    - can be read to (**sense the state**)

- three terminals for one cell:
    
    | select | select the cell for read or write operations |
    | --- | --- |
    | control | to write or to read |
    | data | the bit |

## implementation technologies
- inside the cell, there are two common semiconductor technologies for RAM

- based on **capacitors**
    - dynamic RAM
    - like a small rechargeable battery there is charge or no charge inside it

- based on **transistors**
    - static RAM
    - it can act as a switch (ON/OFF)— three terminals: one serves as the controller of the switch

## dynamic RAM cells
- data is stored as the charge of capacitors

- high level of charge = 1

- low level of charge = 0
- **dynamic**: DRAM requires periodic charge refreshing to maintain data storage (capacitors have the tendency to leak charge)

- **write**: high/low voltage is applied to bit line to charge the capacitor for 1/0
- **read**: charge stored in the capacitor is fed out onto the bit line
    - a **sense amplifier** can compare the capacitor voltage to a reference value

## static RAM cells

- **binary bit** is stored using flip-flop logic gates configurations— essentially, **transistors (as switches) and voltage**
- **static**: the cell will hold the data as long as power is supplied to it (no need to refresh)
- four transistors (T1, T2, T3, T4) are cross connected in an arrangement that produces stable states to represent 1/0
    - address line uses T5 and T6 to select this cell

- **read**:
    - **stable 1**: C1 is high, C2 is low -> T1 and T4 are OFF, T2 and T3 are ON -> read high voltage from bit line B stable
    - **state 0**: C1 is low, C2 is high -> T1 and T4 are ON, T2 and T3 are OFF -> read low voltage from bit line B

- **write**: apply high voltage (1) or low voltage (0) to bit line B → force the transistors into the proper states → corresponding stable states

### DRAM vs. SRAM
- **common**: volatile — need continuous power
- differences:
    1. DRAM cell is simpler and smaller, thus denser (more cells per unit area) and less expensive
    2. SRAM is faster and for cache however, DRAM requires the supporting refresh circuitry
    3. DRAM tends to be favored for large memory requirement and main memory

## read-only memory

| nonvolatile, no need for continuous power |
| --- |
| contains permanent data that cannot be changed (cannot write)— not exactly, depending on the types of ROM |
| data are burned into the chip during fabrication process— relatively large fixed cost, and there is no room for error |
- **usage**: system programs, library subroutines for frequently wanted functions

### programmable ROM
- like ROM, it can be written into only once (after fabrication process) - customers can use special equipment to electrically write once
    - **idea**: each bit is locked by a fuse; initially all 1's in the chip, if burn the fuse, change 1 to 0 (write once)

No problem! Here is the content converted into a properly formatted Markdown table:

### Read-Mostly Memory

- Re-write the memory, but at a higher cost
- Useful for applications in which read operations are far more frequent than write operations, but for which nonvolatile storage is required
- Three common types:

| Type | Description |
| --- | --- |
| Erasable Programmable ROM (EPROM) | Before a write operation, all the storage cells must be erased to the same initial state by exposure to ultraviolet radiation. Then write electronically. Read— exposure— write. More flexible than PROM. |
| Electronically Erasable Programmable ROM (EEPROM) | Updates (write operations) happen at byte level (not entire chip). However, write operation takes considerably longer than read (several hundred microseconds per byte). More expensive and less dense (fewer bits per chip) than EPROM. |
| Flash Memory | Intermediate between EPROM and EEPROM. Erasure at block level (compared to byte-level and chip level). Uses an electrical erasing technology, does not provide byte-level erasure. Microchip is organized so that a section of memory cells are erased in a single action or “flash”. |
# chip logic
- semiconductor memory comes in **package chips**
    - each chip contains an array of memory cells
    - the essential task provide address to chip, and access bits— how to organize and wire the cells in chips to satisfy the needs for addressing
- key issue is how to group cells into a logical piece of data

- **one extreme**: 1 bit a time
- **the other extreme**: one word a time (word: the unit for data processing in CPU)
- **in between**: k bits for one chip, combine multiple chips to get a word

### example of 64 cells
- read/write 1 bit a time (logical data unit = 1 bit) - each cell needs to have an address
- $64 = 2^6$, we need 6 address lines
- we can use a **6-to-64 decoder**— 6 input address lines, 64 output select lines each is connected to the select terminal of the cell
    - in the chip package, 6 address pins, 1 data pin

- logical data unit = 1 bit
    - in each cycle, we provide an address to access 1 bit— **64 cycles to access 64 bits** which too slow

- logical data unit = 8 bits
    - cells are organized into 8 groups, each group containing 8 cells— one group share the same address with 3 address pins, 8 data pins, and less memory access cycles

- logical data unit = 4 bits
    - we can use two chips A and B
    - access 4 bits from A, and 4 bits from B using the same address— together, it is like accessing 8 bits one time which is more flexible for different processors

> the size of the logical data unit is a **key design parameter**

### example of 16-mbit DRAM
- it is implemented using four 4mbit memory components, where each 4mbit component is a 2048x2048 square of cells (2^22)

- use **address** to locate (select) the cell
    - 2048 x 2048 square of cells = 2^{22} cells, intuitively, we need 22 address lines, however, there are only 11 address pins (A0 – A10)

- **solution**: use an external select logic (a multiplexer), where input is the 22 address lines and output is the 11 address pins

- **effect**: divided the 22 address lines into 2 groups, which are fed into the chip through 11 address pins separately

- 4 data lines (pins, D1 - D4), each connecting to one square

- **write**: apply high/low voltage to the data lines to charge the capacitors

- **read**: use a sense amplifier to sense the voltage
- two pins to control write/read: **WE (write enable)** and **OE (output enable)**
    - we can use multiple chips to read/write a word at a time depending on the processor

# memory errors

| hard failure | - permanent physical defect to cells
- replacement |
| --- | --- |
| soft error | - content of the cell is modified, due to random, non-destructive event
- the function of cell is still OK

- example: the charging to the capacitor is not sufficient: 1 → 0 |
| error detection | - knowing that there are errors in a block of data |
| error correction | - detect, and recover the correct contents
- correction is harder than detection

- example: we know there are an even number of 1's in the data (detection is easy) but there could be multiple combinations of errors |

## coding theory
- **general idea**: use redundancy to encode the conditions for which correct data should satisfy
    - redundancy is a function of original data: **K = f(M)**

### example
- we want to download a file M— along with the file, we can also download a hash value of the file: SHA1 = Hash(M)
- after download, we can generate SHA1' = Hash(M')
    - **by comparing SHA1' and SHA1, we can know whether M' = M**

## single parity code
- settings
    - original data block M has 7 bits (abuse of term, M = 7)
    - attach a single bit K to the end of block (K=1)
    - the code thus has 8 bits, **MǁK**— this is a simple encoding process
- given M, we need to decide K

### encoding rule
- function f(D) = number of 1's in data block D
- rule: add K such that f(M ǁ K) is even (even parity)
    - example: M = 000 0011, f(M) = 2 → set K = 0 | M = 110 0111, f(M) = 5 → set K = 1
- we also could have odd parity

## one error detection
- the single parity code is able to detect one error
    - suppose the stored data is D, we can compute f(D)— if f(D) is even → no error; else one error
- note that the above detection algorithm relies on one essential assumption: there is at most one error

- case 1: no error
- case 2: yes error

> also note that, we can only detect error, we do not know the position of the error (cannot correct the error)


## general case
- consider one error correcting code
    - original data M bits, redundancy K bits; **together N = M + K bits**
    - our goal is to correct one error (if happens)— in other words, know the position of the error
- there are a total of (N+1) cases:
    
    | case 0 | no error |
    | --- | --- |
    | case 1 | error at position 1 |
    | case t | error at position t |
    - the essence of error correction code: use K bits to encode (N+1) cases— 2^{K} >= M + K + 1
    

> **coding theory** deals with the realization of the above encoding process using mathematical tools

- **overhead**: K/M, which is the redundancy ratio
    - M = 4 -> K = 3 ( 2^3 = 8 >= 4 + 3 +1); K/M = 3/4
    - M = 8 -> K = 4 ( 2^4 = 16 >= 8 + 4 +1); K/M = 4/8
    - M = 16 -> K = 5 ( 2^5 = 32 >= 16 + 5 +1); K/M = 5/16
    - **as M increases, the overhead decreases**

> so is M the larger the better? no! the fundamental assumption is at most one error, so as M increases, the probability of having more errors also goes up

## (7-4) hamming code
- the classical one-error code, with (7-4) the most common setting
    - **N = 7, M = 4 (original data), K = 3 (redundancy bits)**
- similar to the single parity code, however, we will now use three parity bits
    - data bits: d1, d2, d3, d4
    - parity bits: p1, p2, p3, where each parity bit covers 3 data bits
- there are a total of 16 valid code words (i.e. 4 “free” data bits)
    - **hamming distance (d)**: the number of different bits— hamming distance between every two code words is 3

### example
- circle B is correct (even parity)
- error happens in A and C (assume one error)
- the common erroneous bit is 0
