# lecture 1

Created: January 5, 2024 10:20 AM

# computer organization

- a computer is a very completed system consisting of many functional components
- **organization** is the function of each component and how these components interact with each other as a whole to achieve certain goals

## abstract model of a computer

![Screenshot 2024-01-05 at 10.28.16 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.28.16_AM.png)

- roughly speaking, a computer needs to get the input, store the input, and process the input the get the output, store the output, and send the output to the outside

![Screenshot 2024-01-05 at 10.28.38 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.28.38_AM.png)

## computer architecture

- identification of the functionality of a computer and the components that will lead to the design of functionality (**what**)

## computer organization

- proper arrangement and organization of the components based on their own functions to achieve the desired functionality (**how**)

## functions of computer components

- all functions are related to four parts
    - data processing
    - data storage
    - data movement
    - control and management

## top-down view approach

![Screenshot 2024-01-05 at 10.44.14 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.44.14_AM.png)

![Screenshot 2024-01-05 at 10.44.30 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.44.30_AM.png)

| central processing unit  | CPU = control unit + arithmetic and logic unit (ALU) |
| --- | --- |
| main memory  | temporarily stores the program and results |
| input output modules | get instructions and data into the computer system and output the results |

## number system

- computers will execute programs in CPU, where a **program** is an ordered sequence of instructions, and an **instruction** is an arithmetic or logic operation

![Screenshot 2024-01-05 at 10.53.41 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.53.41_AM.png)

## CPU instruction cycles

- one instruction cycle is composed of two steps: **fetch and execution**
    
    ![Screenshot 2024-01-05 at 10.54.46 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_10.54.46_AM.png)
    
- steps are all controlled by the control unit using a built-in clock/cycle (synchronization) — an instruction typically consumes more than one clock cycle

## registers

| program counter (PC) | stores the memory address of the next instruction and will increment right after fetching said instruction  |
| --- | --- |
| instruction register (IR) | stores the instruction currently being executed |
| memory address register (MAR) | stores the memory address from which data (also instructions) will be fetched to the CPU  |
| memory buffer register (MBR)  | stores the data being transferred to and from the immediate access storage (like a buffer to store data temporarily)  |
- how will the CPU fetch an instruction from memory?
    - CPU knows the memory address of the next instruction (PC), so it places the address into the MAR, and the data is placed in the MBR then loaded into the IR — when fetching is completed, PC is incremented unless told otherwise

## CPU actions in execution cycle

- depends on instructions
    - data transfers between CPU and main memory
    - **data processing**: some arithmetic or logic operations in ALU
    - **control**: alteration of sequence of operations

![Screenshot 2024-01-05 at 11.03.19 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.03.19_AM.png)

## MIPS assembly language

- how do operations between registers and main memory using instructions to achieve certain functionalities

![Screenshot 2024-01-05 at 11.05.12 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.05.12_AM.png)

| .text, .globl main | used to declare the main function as the entry point  |
| --- | --- |
| ori $8, $0, 0x2 | loads the immediate value 0x2 into register $8 using the bitwise OR operation with 0  |
| ori $9, $0, 0x3 | loads the immediate value 0x3 into register $9 using the bitwise OR operation with 0  |
| addu $10, $8, $9 | adds the values in registers $8 and $9 and stores the result in register $10 using unsigned addition |

## digital logic

- operations in computers are based on the processing of digital and binary language

![Screenshot 2024-01-05 at 11.06.44 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.06.44_AM.png)

- **combinational logic**: digital logic in which logical decisions are made based only on combinations of the input using boolean functions

# computer memory

- refers to any physical device capable of storing information
- trade-off between performance and cost

## concept of cache memory

![Screenshot 2024-01-05 at 11.12.26 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.12.26_AM.png)

- cache is fast but has a relatively small capacity
- **basic mechanism**: cache contains the copy of portions of main memory (blocks)
    - when CPU wants to read a word, if the word is in cache, we access the word, otherwise a block of the main memory containing that word is reach into the cache, then the word is delivered to the CPU
    
    ![Screenshot 2024-01-05 at 11.13.47 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.13.47_AM.png)
    

# overview of number systems

- a base B, where B is a number

## decimal

![Screenshot 2024-01-05 at 11.14.56 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.14.56_AM.png)

## binary

![Screenshot 2024-01-05 at 11.15.16 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.15.16_AM.png)

## hexadecimal

![Screenshot 2024-01-05 at 11.15.34 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.15.34_AM.png)

## general rule

- for an integer X with n digits, we write it as:
    - $X = (d_n$$_-$$_1 … d_2d_1d_0)_B$, where its decimal value is expressed as $X = ∑^n$$^-$$^1$$_i$$_=$$_0$ $d_ixB_i$
- given a real number with a fraction (n: number of integers, m: number of fractions):
    - $X = (d_n$$_-$$_1 … d_2d_1d_0.$$d_-$$_1d_-$$_2…d_-$$_m)_B$, where its decimal value is expressed as $X = ∑^n$$^-$$^1$$_i$$_=$$_0$ $d_ixB_i$ $+$ $∑^m$$_j$$_=$$_0$ $d_j(1/B)^j$

- exercise
    - compute the decimal value of:
    1. 1101101.101$_2$
        - 1101101 = 109
        - .101 = 0.625
        - 1101101.101 = 109.625$_1$$_0$
    2. 103.42$_1$$_6$
        - 103 = 259 → 1x16^2 + 0x16^1 + 3x16^0
        - .42 = 0.258 → 4x16^-1 + 2x16^-2
        - 103.42$_1$$_6$ = 259.258$_1$$_0$

## converting decimal to binary

![Screenshot 2024-01-05 at 11.22.35 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.22.35_AM.png)

## converting decimal fractional number

![Screenshot 2024-01-05 at 11.23.29 AM.png](lecture%201%2056518a1ce76549d2900376a3851ba9a6/Screenshot_2024-01-05_at_11.23.29_AM.png)

- exercise
    1. get the binary representation of F = 0.35
        - 0.35 = 0.01011001
    2. how about F = 0.25? 
        - 0.25 = 0.01
    3. F = 123.23?
        - 123 = 1111011
        - 0.23 = 001110101
        - 123.23 = 1111011.001110101