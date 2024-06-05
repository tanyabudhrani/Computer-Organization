# lecture 3

Created: January 22, 2024 7:26 PM

### hello, assembly world

![Screenshot 2024-01-22 at 8.13.06 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.13.06_PM.png)

- `.text`: the following lines are the source code for the program
- `.globl main`, the identifier “main” will be used outside of this source file (i.e., used globally) as the label of a particular location in main memory; it defines a **symbolic address**, which is a name in the source code referring to a location in the memory
    - in this example, “main” stands for the address of the first machine instruction (0x00400000)

# bitwise logic operations

- essentially the manipulation of bit patterns
    - in the view of ALU (computer), the **operands are bit patterns**, each instruction conduct some manipulations of the bit patterns
- there is an immediate operands within the instruction (e.g. `ori $8, $0, 0x2`)
    
    ![Screenshot 2024-01-22 at 8.19.02 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.19.02_PM.png)
    

## zero extension

- **ori** will do zero extension to the immediate operand — padding with zeroes on the left
    
    ![Screenshot 2024-01-22 at 8.20.51 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.20.51_PM.png)
    
- constants can only be non-negative integers
    - for a negative number that starts with 1, after zero extension, it will become positive which is meaningless

| ori instruction  | ALU will zero extend a first bit pattern, then do bitwise OR with a second bit pattern  | ori $8, $0, 0x2  |
| --- | --- | --- |
| andi instruction | zero extend a first bit pattern (constant), do AND operation with a second bit pattern (s), store the result in d  | addi $8, $0, 0x2 |
| xori instruction | 1st operand: 0011
2nd operand: 0101

result: 0110  | xori $8, $0, 0x2 |

| and | or | xor | nor |
| --- | --- | --- | --- |
| and d,s,t | or d,s,t | xor d,s,t | nor d,s,t  |
| $d ← $s and $t  | $d ← $s or $t | $d ← $s xor $t | $d ← $s nor $t |

### example

- NOT flip using NOR with $0
    
    ```nasm
    nor d, s, $0 #$d <- bitwise NOT of $s
    ```
    

## shift operations

- **shift left logical**: `sll d, s, shft`
    - right most bits are replaced by 0s and left most bits are discarded (e.g. 0110 → 1100)
    - equivalent to multiplying the number by 2
- there are restrictions on using shift left logical to implement multiplication such as unsigned representation and no 1 bit discarded

### in place shift

```nasm
sll $8, $8, 2

#ALU will copy data from register to ALU, shift, then store result back 
```

```nasm
sll $0, $0, 0

#NO-OP happens when trying to change the content of $0
```

- **shift right logical**: `srl d, s, shft`
- there are some restrictions on using shift right logical to implement division such as unsigned representation or non-negative integers in 2’s complement
    - e.g. 0001 → 0000 (causes a rounding issue)

- **shift right arithmetic**: `sra d, s, shft`
    - equivalent to division for negative integers in 2’s complement form

![Screenshot 2024-01-22 at 8.37.54 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.37.54_PM.png)

- exercise
    - translate to C
        - assume that the variables f, g, h, i, and j are assigned to registers $s0, $s1, $s2, $s3, and $s4 respectively— assume that the base address of the arrays A and B are in registers $s6 and $s7
        
        ```wasm
        lw $s0, 16($s7)
        sll $s0, $s0, 2 
        add $s0, $s0, $s6
        lw $s0, 0($s0)
        sub $s0, $s1, $s0 
        ```
        
        ![Screenshot 2024-01-29 at 5.22.03 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-29_at_5.22.03_PM.png)
        

# integer arithmetic

- integers in MIPS can be represented as unsigned form or 2’s complement form
    - use 32 bits to represent integers
- the algorithms to add two integers in these two forms are the same — as a result, the instructions are the same (except for add); the difference lies in the overflow detection mechanism

## overflow detection

- **unsigned form**
    - the carry out bit of the highest column is 1 (e.g. 1010 + 1100 = 1 0110)

- **2’s complement form**
    - if both operands have the same sign but the result has the opposite sign

## addition instructions

| addu | • s+t → d (s,t,d are all registers)

• “u” indicates that overflow is ignored |
| --- | --- |
| add | • exactly the same as addu: s+t → d

• will detect overflow and cause an interrupt |
| addiu | • adds immediate value (addiu d, s, const)

• we have an issue here where add s (32 bits) and const (16 bits), so we use sign-extension to copy the 15th bit |
- exercise
    - for the 16-bit immediate value: 1010 1101 1110 1101, is it in unsigned form or 2's complement? is it positive or negative?
        - in a 16-bit binary number, if the left-most bit is 1, it indicates that the number is in 2’s complement form
            - invert: 0101 0010 0001 0010
            - add 1: 0101 0010 0001 0011
            - result: -21011
    

## subtraction instructions

| subu/sub | • s-t → d (s,t,d are all registers)

• “u” indicates that overflow is ignored while the sub will detect overflow and cause an interrupt  |
| --- | --- |
- there is no subi/subiu — if you want to subtract an immediate value, you would use addiu (e.g. `addiu $8, $10, -3`)
- exercise
    - fill in the blanks:
        
        ![Screenshot 2024-01-22 at 8.46.46 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.46.46_PM.png)
        
        ![IMG_9298.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/IMG_9298.png)
        

## multiplication instructions

- generally the product of two integers expressed with N-bit binary may need **2N bits** — that is, we now need two registers to store the result
    
    ![Screenshot 2024-01-22 at 8.48.01 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.48.01_PM.png)
    

| mult/multu s,t | automatically stores the result in hi and lo

mult is for 2’s complement form while multu is for unsigned

neither mult and multu would check for overflow |
| --- | --- |

```nasm
mfhi d # move from hi to register d
mflo d # move from lo to register d 
```

- exercise
    
    ![Screenshot 2024-01-22 at 8.50.24 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_8.50.24_PM.png)
    
    ![IMG_9299.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/IMG_9299.png)
    
    - why do we only use `mflo`, not `mfhi`? because the operands are small, so `mfhi` is filled with zeros

## division instructions

- with N-bit integer division, we again have two results of N-bit each
- **op1/op2:** op1 = op2 x quotient + remainder

| div s,t/divu s,t | divides s by t

div for 2’s complement

divu for unsigned |
| --- | --- |

# memory access

- how to copy data from memory into registers, and from registers to memory
    - all operations are conducted over registers — need to move operands in registers first
- **alignment restriction**: the address of a word is always a multiple of four
    - an instruction is **one-word long** — the address of an instruction is always word aligned

| lw / sw  | lw d, offset (b)
sw t, offset (b) | b is a register storing the base address, offset is a 16-bit number in 2’s complement form

address = base address + offset  |
| --- | --- | --- |
| lui   | lui d, const | copy constant to the upper two bytes of the register d, while the lower two bytes are 0  |
| ori | ori d, s, imm | if we use $0, equivalently, we set the lower two bytes of d as imm |

### setting base address

- address = base address + offset
    - we need to load data in memory into registers, so we need the 32 bit address of the memory but we cannot directly refer to the address in one instruction — we need **two 16-bit immediate values**
- once base address is set, we can vary the offset to access memory

> `sw` (store word) is used to store a 32-bit word (4 bytes) in the memory (e.g. `sw $t0, 4($s0)`)
> 

> `lw` (load word) is used to load a 32-bit word (4 bytes) to a register (e.g. `lw $v0, 8($s1)`)
> 

## symbolic address

- we can use names/symbols to refer to memory locations

![Screenshot 2024-01-22 at 9.02.59 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.02.59_PM.png)

- `.data`**:** indicates the start of the data section of memory
- `.word`: puts a 32-bit 2’s complement integer here

> the data section in MIPS starts at address 0x100000000 — as a result, the symbolic address x = 0x100000000 and the symbolic address poly = 0x100000004 (word = 4 bytes)
> 
- exercise
    
    ![Screenshot 2024-01-22 at 9.05.12 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.05.12_PM.png)
    
    [https://www.notion.so](https://www.notion.so)
    

# program flow control

- program is an ordered sequence of instructions where instructions are executed sequentially

### jump instruction

- **fetch — update PC — execute**
- fetch j address — update PC — execute j address (PC = address) ?? NOT THAT EASY

## branch delay slot

- MIPS is **pipelined**
    - to increase speed, processor will cleverly fetch multiple instructions and starts working on them all
- the instruction following jump is almost completed when jump is executed so instead of wasting efforts, processor will allow to complete that instruction after jump instruction
- the instruction right after the jump instruction is called the **branch delay slot**, and it will always be executed
    - to deal with this, we can put a no-op instruction right after jump
        
        ```nasm
        j cont 
        sll $0, $0, 0 
        
        #basically, when jump is executed, the instructions following it 
        #is already almost completed -- but if it doesn't have to run, then 
        #it is a waste of effort, so we should wait for jump to execute before 
        #beginning to execute the instructions after jump 
        ```
        
- importantly, PC will be set to address after the branch delay slot is executed (not after j addr is executed)

### example one

| address | instruction  | PC after instruction has executed |
| --- | --- | --- |
|  |  | 00400000 |
| 00400000 | load | 00400004 |
| 00400004 |  add | 00400008 |
| 00400008 | jump 0x00400000 | 0040000C |
| 0040000C | no-op | 00400000 |
- PC should have been set as 00400000 in the jump instruction, instead, it was set to 00400000 only after the branch delay slot is executed

### example two

```nasm
##jump.asm 
##
	.text 
	.globl main
main:
	sll $0, $0, 0
	sll $0, $0, 0
	sll $0, $0, 0
	sll $0, $0, 0
	j main
	addiu $8, $8, 1
```

- after the loop has executed 5 times, the value of **$8 = 5**
    - `addiu $8, $8, 1` will always execute

## conditional branch instructions

- a conditional branch instruction branches to a new address only if a certain condition is true

| branch on equal | beq u,v, addr | if $u == $v, branches to addr |
| --- | --- | --- |
| branch on not equal | bne u,v, addr | if $u != $v, branches to addr |
| branch on less than zero | bltz s, addr | if $s < 0, branches to addr (note that $s is in 2’s complement form)  |
| branch on greater than or equal to zero  | bgez s, addr | addr # if $s>=0 |

![Screenshot 2024-01-22 at 9.18.06 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.18.06_PM.png)

```nasm
	... #load values into $8 and $9
	beq $8, $9, cont #branch if equal 
	sll $0, $0, 0 #branch delay slot 
	...
	... #conditionally executed statements
	...
cont:
	add $10, $10, $11 #always executed 
```

```nasm
	... #load values into $8 and $9
	beq $8, $9, equal #branch if equal 
	sll $0, $0, 0 #branch delay slot 
	...
	... #false branch 
	...
	j cont 
	sll $0, $0, 0 
equal:
	...
	... #true branch 
	...
cont:
	add $10, $10, $11 #always executed
```

![Screenshot 2024-01-22 at 9.18.11 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.18.11_PM.png)

### example

- calculate the absolute value of A stored at a five address (i.e. if A < 0: store -A; else, do nothing)
    
    ```nasm
    	.text
    	.globlmain
    main:
    	lui $10, 0x1000 #initialize the base register
    	lw $8, 0 ($10) #load A to register $8
    
    	srl $9, $8, 31 #shift the sign bit to position 0 (MSB)
    	beq $0, $9, done #check if $9 == 0 (i.e. if A is negative)
    	sll $0, $0, 0 #branch delay slot 
    
    	subu $8, $0, $8 #calculate -A 
    	sw $8, 0($10) #store -A back to the address
    
    done:
    	.data
    A: .word -1
    ```
    

## count loop

- execute for some fixed times, controlled by an integer
    1. initialize the counter 
    2. test the counter value and end the loop on the correct value 
    3. increment the counter 
    
    ```c
    int j;
    j = 0; 
    while(j<10) {
    	j++;
    }
    
    //this will execute 10 times 
    ```
    
- a top-driven loop in assembly language — check the condition on the top, and use a combination of conditional branch, jump, and conditional set instructions

![Screenshot 2024-01-22 at 9.22.43 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.22.43_PM.png)

## conditional set instructions

- used to implement relational operations
    - set a register to 1 or 0 to show the relation between two values (often used as a flag)

| slt d,s,t | $s and $t contains signed integers in 2’s complement 

set $d = 1 if $s < $t; else, set $d = 0  |
| --- | --- |
| sltu d,s,t | used with unsigned integers |
| slti d,s,imm | set on less than immediate

if $s < imm, set $d = 1, else set $d = 0  |
| sltiu d,s,imm | used with unsigned integers |

### example

![Screenshot 2024-01-22 at 9.25.14 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.25.14_PM.png)

- for A, $7 = 1; for B, $7 = 0
    - addiu is used to perform addition with a constants as unsigned integers so in A, $5 and $6 are both treated as unsigned integers, so when we sign extend, -25 will be much bigger than 25

# sum of array elements

- compute the sum of array elements, sum of positive array elements, sum of negative array elements
    - use a count loop structure — for each element, decide if it is negative or positive, then do the addition
- in MIPS, we can use **directive .word** to declare integers
    
    ![Screenshot 2024-01-22 at 9.28.23 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.28.23_PM.png)
    

![Screenshot 2024-01-22 at 9.29.36 PM.png](lecture%203%207add6abaf39c4c76b00ddcd86610618f/Screenshot_2024-01-22_at_9.29.36_PM.png)

```wasm
	.text
	.globl main
main:
	ori $8, $0, 0 #initialize count = 0 
	ori $11, $0, 0 #initialize sum = 0
	ori $12, $0, 0 #initialize neg = 0 
	ori $13, $0, 0 #pos = 0
 
	lui $9, 0x1000 #set size = 0x1000
	lw $15, 0($9) #load size into $15 
	addiu $9, $9, 4 #point to the first element (+4)

loop: 
	beq $8, $15, done #check that count >= 15 (array.size)
	sll $0, $0, 0 #NO-OP

	lw $10, 0($9) #load the first element into $10 
	sll $0, $0, 0 #NO-OP
	addiu $11, $11, $10 #add first element to sum 

	slti $14, $10, 0x0 #set $14 to check if element is < 0
	bne $14, $0, neg #if 14 == 1, branch neg
	sll $0, $0, 0 #NO-OP
	addu $13, $13, $10 #otherwise, add to positive counter
	j ifend #jump to ifend 
	sll $0, $0, 0 #NO-OP

neg: 
	addu $12, $12, $10 #add to negative counter

ifend:
	addiu $8, $8, 1 #count++
	addiu $9, $9, 4 #move to next element 
	j loop #go back to start of loop
	sll $0, $0, 0 #NO-OP

done:
	sll $0, $0, 0 
```