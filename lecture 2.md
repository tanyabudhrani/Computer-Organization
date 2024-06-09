# lecture 2
- recap
    - what are the three basic components in a computer system?
    - how is a program executed in a computer?
        - what is an instruction cycle?
        - what are the steps for executing an instruction?
        - what are the important registers?

# ALU
- the actual part of the computer that performs arithmetic and logic operations on data
- all other parts cooperate together to send data to ALU for processing

| control unit | give instructions on what to do  |
| --- | --- |
| left registers | hold input data  |
| flags | indicate status of instruction result  |
| right registers | buffers to hold output data |

## simple adder
- we use unsigned number of n bits
    - A = 10101010, B = 00110011
- but, is this unsigned number representation enough?
    
- what if B = 10101010?
    - this would lead to overflow!
- what if A and B are signed numbers?
    - then the first bit would indicate the sign bit which would involve sign extension

## sign-magnitude representation
- using the leftmost bit to represent the sign
- for an n bit system, how many different numbers can a sign-magnitude system represent?
    - for a given sign, there are $2(^n(^-)^1)$ values— since we have two signs (positive and negative), however, if the magnitude part is filled with all zeros, we have two representations so it can only represent $2^n-1$ numbers

## two’s complement representation
- positive numbers and zero are represented in the same fashion as in the sign-magnitude system 
- negative numbers are represented as the complement of the corresponding positive number + 1
    - given a number A (n binary bits), we say B is the 2’s complement if A+B = $2^n$(10000000)(n+1 bits)
        - e.g. given n = 3, 001 → 111 is the complement since 001 = 110 + 1 = 111
    
- exercise
    - suppose we have 3 bits
        - first write the representation of +3, +2, +1, and 0, then write the representation of -3, -2, and -1
    
    | +3 | 011 |
    | --- | --- |
    | +2 | 010 |
    | +1 | 001 |
    | 0 | 000 |
    | -1 | 111 |
    | -2 | 110 |
    | -3 | 101 |

### how to get the two’s complement

- by definition of two’s complement
    - B is A’s 2’s complement if A + B = $2^n$
    - given A = 00110101, B = $2^n - 1$
    - e.g. 100000000 - 00110101 (A) = 1100 1011 (B)

- flip the bits then add 1
    - given A = 0011 0101
    - flip the bits: 1100 1010
    - add 1: 1100 1011 = B
    - verify: 1100 1011 + 0011 0101 = 100000000
 
- exercise
    - represent -18 in 2’s complement form
        - 18 = 0001 0010
        - flip: 1110 1101
        - add 1: 1110 1110
    - represent -16 in 2’s complement form
        - 16 = 0001 0000
        - flip: 1110 1111
        - add 1: 1111 0000

### two’s complement to decimal
- first bit 0 → positive | 1 → negative
    - if it starts with 0, just perform sign-magnitude
    - if it starts with 1, get its 2’s complement (which starts with 0), then add a negative sign to D

## addition in two’s complement
- the addition of two numbers in two’s complement does not need any sign detection
    - e.g. 1101 (-3) + 0010 (2) = 1111 ??? 1111’s two’s complement is 0001 (which is 1), so 1111 is -1

### overflow detection
- what is the problem with -1 + (-2)?
    - -1 = 1111, -2 = 1110
    - 1111 + 1110 = 1 1101 = -3
    - negative + negative = negative (possible)

- what is the problem with -5 + (-4)?
    - -5 = 1011, -4 = 1100
    - 1101 + 1100 = 1 0111 = 7
    - negative + negative = positive (not possible)
- **ALU’s overflow detection rule**: if two numbers are added, and they are both positive or both negative, overflow occurs if and only if the result has the opposite sign

## subtraction in two’s complement
- operation A - B can be computed by **A + (-B)** where -B is the two’s complement of B
    - e.g. 0011 1100 - 0010 1101 = 0011 1100 + (-0010 1101) — 0010 1101 = 1101 0011 =  10000 1111

## multiplication
- the bits in **multiplier** controls the operation
    - bit = 1 → shift and add
    - bit = 0 → shift only

### multiplication with negatives
- ignore the sign, compute the multiplication, then take the 2s complement of the result

## fraction number representation
- integer part + radix point (.) + fractional part
    - e.g. $1001.1010 = 2^3 + 2^0 + 2(^-(^1)) + 2(^-(^3))$
- if the radix point is in a fixed location, the range of data is very limited (seeing as the total number of different numbers that could be represented is the same, only the range is different)
    - e.g. 976,000,000,000,000 → 9.76 x 10^{14}
    - we dynamically slide the decimal point to a convenient location and use the exponent of 10 to keep track of the location

# floating point
- the number can be stored in a binary word with three fields
    
    | sign: plus or minus |
    | --- |
    | significand S  |
    | exponent E  |
    | base — often implicit |

### significand and the radix point
- permit us different ways of representing the same number
    - 0.110 x $2^5$ → 110 x $2^2$ → 0.0110 x $2^6$

- radix point is on the right of the left-most bit (there is only one bit on the left of the radix point)

- the left-most digit of the significand is non-zero (for base 2, it is always 1) — **normalization**
- a normalized nonzero number always has the form **+- $1.bbbx2^E$**, where b is either 0 or 1

### biased exponent E

- the actual value of **E = biased exponent - fixed bias**
    - **fixed bias**: $2(^k(^-)^1) - 1$, where k is the number of bits of the biased exponent field (k=8)
- e.g. k=8, bias = $2^7-1$ = 127 → if E = 20, the biased exponent = E + bias = 1001 0011
- first row: E = 20 → biased exponent = 127 + 20 = 147
- fourth row: E = -20 → biased exponent = 127 - 20 = 107

## conversion

- given a binary number, find its FP bit pattern
    - binary: -0.001010
    - normalization: $-1.01 x 2(^-(^3))$
    - biased exponent: E = -3 → -3+127 = 124 = 0111 1100
    - significand: 01 0 0000 0000 0000…
    - result: 1 0111 1100 010 0000 0000 0000…

- exercise
    - find the FP bit pattern of 0011 0101 1101 0010 0000…
        - significand: 0 → positive
        - biased exponent: 0110 1011 = 107 → 107-127 = -20 = E
        - normalization: .101 0010 0000 = 0.640
        - result: $(-1)^0 * (1+0.640) * 2(^-(^2(^0))) = 1.640*2(^-(^2(^0)))$

---

# assembly
- processors can be categorized by architecture wherein which each processor family has a specific language
    - assembly language is **hardware-dependent**

### machine vs. assembly
- machine language uses binary representation (encoding)
- assembly language uses symbolic representation

## producing an executable file

| program | often contains several modules |
| --- | --- |
| module | often contains references to other modules (to use data or subroutines)  |
| linker | combine independently assembled object files and resolve references |

## roles of assembly language
1. high level languages are transformed to assembly using a compiler 
2. directly used as a language to write programs or as the output of a compiler 

- **main advantages**:
    - speed by not using a compiler
    - can exploit some hardware features that are not available for high-level languages
    - e.g.embedded systems, designing a control unit, etc

- **main disadvantages**:
    - machine specific — must be totally rewritten depending on the computer architecture
    - takes longer than high-level languages, making it more time consuming and prone to bugs

# instruction set architecture
- a set of instructions supported by the processor
    - computers can be divided into two types based on the instruction set: **reduced instruction set computer** (RISC) or **complex instruction set computer** (CISC)

## RISC
- a small set of simple instructions which build more complex functions
- e.g. MIPS, ARM

## CISC
- one instruction corresponds to very complex operation which takes more than one CPU cycle

> the primary goal of CISC is to complete a task in as few lines of assembly as possible | the goal of RISC is to use only simple instructions that can be executed within one clock cycle

### example
- let’s say we want to multiply two numbers together— the numbers are placed on a grid and stored using coordinates (e.g. 2:3, 5:3)

```nasm
#RISC

LOAD A, 2:3
LOAD B, 5:2
PROD, A, B
STORE, 2:3, A
```

```nasm
#CISC

MULT 2:3, 5:2

```

# MIPS

- microprocessor without interlocked pipeline stages
- register based architecture — CPU performs operations on registers based on two categories:
    1. memory access (load and store data) 
    2. ALU operations (operations between registers)

## MIPS registers

- 32 general-purpose registers (some reserved)
    - starts with “$”
    - directly use register number $0 - $31
    
    | zero  | the value 0  |
    | --- | --- |
    | $at | assembler temporary  |
    | $v0 | value from expression or function return  |
    | $v1 | value from expression or function return  |
    | $a0 | first argument to a function |
    | $a1 | second argument to a function |
    | $a2 | third argument to a function |
    | $a3 | fourth argument to a function |
    | $t0..$t7 | temporary values |
    | $s0..$s7 | safe function variable  |
    | $k0..$k1 | kernel use  |
    | $gp | global pointer |
    | $sp | stack pointer |
    | $fp | frame pointer |
    | $ra | return address of most recent caller  |
- special registers are inaccessible to the programmer
- **hi and lo registers** are used for multiplication and division, and are also not directly addressable
    - use special instructions `mfhi` and `mflo`

## MIPS memory layout

- only a portion of the memory can be used by user program
    - the top from **0x80000000 to 0xFFFFFFFF** is for the operating system and ROM— the part that can be used for user programs are the text, data, and stack segment
- the basic unit in memory is a **byte**; there are a total of $2(^3(^2))$ bytes in memory where each byte has an address of 32

- **load**:
    - copy a bit pattern (could be data or instruction) in a designated address in memory to a register (memory → register)

- **store**:
    - copy a bit pattern from register to memory at a designated address (register → memory)
- memory is often accessed in contiguous bytes (a group of bytes) where one byte = 8 bits, one word = 32 bits, and a double word = 64 bits
- memory is used to store instructions and data
    - **instructions** have a fixed length of one word while **data** can have various bytes

> **note**: for contiguous bytes, we often use the address of the first byte (i.e. the byte with the **lowest address**)
> 

## r-type instructions

- **three arguments**: two source registers (rt and rs) and one destination register (rd)
    
    ```nasm
    add $t0, $t1, $t2 
    ```

| op or opcode | “operation code”; indicate the type of instruction — all r-type instructions have an opcode of 000000 |
| --- | --- |
| rs, rt, rd | indicate which register — we use 5 bits since it allows for 32 different registers, since $2^5 = 32$ |
| shamt | used in shift instructions  |
| funct | indicates which instructions |

- exercise
    - write a line of code to implement 3+10
        
        ```nasm
        add $t1, $t3, $t5 
        ```
        
    - translate it into machine code
        
        ```nasm
        op: 00000000
        rs: 00011
        rt: 01010
        rd: 00000
        shamt: 00000
        funct: 01101
        ```
        

## l-type instructions

- **three arguments**: two registers (rt and rs) and a 16-bit immediate value which stores the value **directly** within the instruction
    - **immediate value**: could be a constant serving as one operand in the arithmetic operation; or an address in load/store instruction

```nasm
ori $8, $0, 0x2 #takes the bit-wise OR between value stored in rs and imm 
```

- MIPS will zero-extend the 16 bit operand imm to 32 bits
    - loads the value stored in imm to the target register by using the constant value in $0
- another example is **load word (lw)** which loads a word from memory at a designated address to a register using **base address + offset**

```nasm
lw $8, 0x60($10)
#suppose $10 stores 0x00400000
#memory address = 0x00400060
#load the word in memory (0x00400060) to $8
```

- **store word (sw)** copies the data stored in register t to the memory at address using base address in **basereg + offset**
    
    ```nasm
    sw $12, 0x50($13) 
    ```
    
- exercise
    - use ori to load 17 into $t1
    
    ```nasm
    ori $t1, $0, $17
    ```
    

## j-type instructions

- controls the program flow to a given instruction
    
    ```nasm
    j addr #jump to instruction at address addr
    ```

| address | instruction | PC after the instruction has been executed  |
| --- | --- | --- |
|  |  | 00400000 |
| 00400000 | load | 00400004 |
| 00400004 | add | 00400008 |
| 00400008 | jump 00400000 | 0040000C |
| 0040000C | no-op | 00400000 
effect of the jump — LOOP |
- exercise
    - fill in the blanks
    
    | address | instruction | PC after the instruction has been executed  |
    | --- | --- | --- |
    |  |  | 00450000 |
    | 00450000 | load | 00450004 |
    | 00450004 | load | 00450008 |
    | 00450008 | add | 0045000C |
    | 0045000C | store | 00450010 |
    | 00450010 | jump 0x004500000 | 00450014 |
    | 0x450014 | no-op | 00450000 |
    

### jump instruction

- one instruction occupies 4 bytes in memory and we use the lowest byte to refer to contiguous bytes, **however, the address of the lowest byte must be a multiple of 4**
    - as a result, the right-most two bits in the address of an instruction are always 00, and the left-most 4 bits in the address are the content in the PC
