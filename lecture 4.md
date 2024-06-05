# lecture 4

Created: February 1, 2024 4:05 PM

# subroutine linkage

- subroutine is a logical division of code that can be regarded as a **self-contained operation**
    - all high-level languages have the concept of subroutine (procedure, function, method)
- using subroutines can improve readability

## first try with jump instruction

- suppose we use **sub** to label the starting address of the subroutine
    - call the subroutine = **jump to sub**
- in the main program, we jump to sub (call the subroutine)— after finishing the subroutine, we need to return to the main program, how? jump to `ret` (ret labels the instruction after j sub)

![Screenshot 2024-02-01 at 4.39.30 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_4.39.30_PM.png)

### problem with jump instruction

- at the end of the subroutine, where should we jump back to? the subroutine needs to know a dynamic return address (associated with the calling instruction), so that the program knows where to return to

## $ra register

- `$ra ($31)` is the register used for **linkage**
    - it holds the return address for a subroutine — when we call a subroutine, we store the return address in `$ra`; when the subroutine is finished, the program returns to that address
- the implementation is a pair of instructions `jal` and `jr`

## jal

1. set `$ra` to PC + 4
    - the result is that `$ra` stores the address of the **(n+2)th** instruction
2. set PC to sub

## jr

- set PC to $ra

### example

- what will happen when `jal` is executed?
    
    
    | fetch  | when jal is fetched, PC = 0x00400014 |
    | --- | --- |
    | increment  | PC is incremented to 0x00400018  |
    | execute  | $ra ← 0x0040001C = 0x00400018 + 4
    
    PC ← 0x00400100 (sub) |
    

![Screenshot 2024-02-01 at 4.45.50 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_4.45.50_PM.png)

# calling convention

- agreement about how subroutines are called and how control is returned
    - an agreement at the software level
    - different languages and different operating systems for the same processor usually have different calling convention

## use of registers

- caller (main routine) and callee (subroutine) both use registers to store data
    
    
    | conflict may happen — caller stored some data in a register and the callee may also use the register and change the data  |
    | --- |
    | caller and callee might be written by different programmers |
    | it’s silly to go back to the caller to check which registers are used when writing subroutines — we need agreement among programmers |

### different roles of registers

| $t0-$t9  | subroutines can use without caution |
| --- | --- |
| $s0-$s7  | saved registers; subroutines should not use (if need to use it, save the value first and restore the value after use) |
| $a0-$a3 | contain arguments for the subroutine |
| $v0-$v1 | contain values returned from the subroutine |

```bash
add $t0, $s5, $s3
jal somesub #call a subroutine
nop #branch delay 
mul $s4, $t0, $v1 #multiply the sum 

#the main routine used the temporary register $t0, 
#for which any subroutine can use without caution
```

## simple MIPS calling convention

- use `jal` and `jr` with `$ra` to call subroutines
- do not call another subroutine within a subroutine
- obey the conventional use of registers

### chain of subroutines

- main calls sub A, then sub A calls sub B
    - there is **only one $ra** that saves the return address

# stack

- a section in memory that stores dynamically generated data in programs which will grow or reduce dynamically

## stack in mips

- an upside down organization
    - **stack pointer register** `$sp ($29)` points to the top (smallest address) word of stack
- access rule is last in first out

## push

- to push an item onto stack, subtract 4 from $sp then store the item at the address in $sp
    
    ```bash
    #push the item in $t0 
    subu $sp, $sp, 4 #point to the place for the new item 
    sw $t0, ($sp) #store the contents of $t0 as the new top 
    ```
    
    - `sw $t0, ($sp)` is actually `sw $t0, 0 ($sp)`— in practice, "extended assembler" can recognize
    "pseudo-instructions" for conveniences of programming
    

## pop

- to pop an item from stack, copy the item pointed at by $sp and then add 4 to $sp
    
    ```bash
    #POP item into $t0
    lw $t0, ($sp) 
    addu $sp, $sp, 4 
    ```
    

![Screenshot 2024-02-01 at 5.03.20 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.03.20_PM.png)

![Screenshot 2024-02-01 at 5.03.28 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.03.28_PM.png)

> **pseudo-instructions**: they are not in the instruction set of MIPS, but for convenience, we create some commonly used pseudo-instructions— then extended assembler can translate pseudo-instructions into instructions
> 

## chain of subroutines

1. the use of `$ra`
    - when main calls sub A, a return address (in main) is stored in `$ra`
    - solution: using “push” and “pop” operations, store `$ra` into stack and pop `$ra` before `jr $ra`
2. the register problem 
    - **caller should use $s0-$s7**, **callee should use $t0-$t9**— when main calls sub A, sub A should not use $s0-$s7 while sub A should not use $t0-$t9
    - solution: allow sub A to use $s0-$s7 but push $s0 - $s7 to stack before use, and restore when `jr $ra`

## stack-based linkage

| subroutine call (done by caller) | push registers $t0 - $t9 to stack (callee may use them)

put argument values into $a0 - $a3 |
| --- | --- |
| subroutine beginning (done by callee)  | if this subroutine will call another subroutine, push $ra to stack

push registers $s0 - $s7 to stack (caller may use them) |
| subroutine body | may use $t and $s if pushed to stack

may call another subroutine |
| subroutine ending | put returned values in $v0 - $v1

pop registers $s0 - $s7 from stack (to restore the value)

pop $ra

return to caller using jr $ra |
- exercise
    1. addiu $t1, $t2, 0x1234abcd 
        - add a 32-bit constant with $t2 and store the result in $t1 — use a sequence of three instructions: lui, ori, addu, and another register
        
        ```wasm
        lui $t0, 0x1234 
        ori $t0, $t0, 0xabcd 
        addu $t1, $t2, $t0 
        ```
        
    2. bgt $t1, $t2, Label 
        - if $t1 > $t2, branch to Label — use a sequence of two instructions slt, bne, and another register $t3
        
        ```wasm
        slt $t3, $t2, $t1 
        bne $t3, $0 Label 
        ```
        
- exercise
    - suppose we have three variables a,b,c that are already stored at the register $t0, $t1, and $t2— we also have two arrays U and V, where each element in the array is a 32-bit unsigned integer
        - assume that the base address of the array U and V are stored in registers $t5 and $t6, respectively
    1. translate a = b + c + V[3] 
        
        ```wasm
        #use addu, lw, addu and store the result in $t0 
        
        addu $t0, $t1, $t2 
        lw $t3, 12($t6) 
        addu $t0, $t0, $t3 
        ```
        
    2. translate a = b + U[V[3]]
        
        ```wasm
        #use lw, sll, addu, lw, addu and store result in $t0 
        
        lw $t3, 12($t6) 
        sll $t3, $t3, 2 #t3 = t3*4
        addu $t4, $t3, $t5 
        lw $t5, 0($t4) #load the value of U[V[3]] to $t9 
        addu $t0, $t1, $t5 
        ```
        
- exercise
    - translate
        
        ```c
        int num = 0
        for(int i=0; i<n-1; i++) {
        	if(B[i] == B[i+1]) {
        		num = num+1; 
        	}
        }
        ```
        
        ```wasm
        addi $s1, $s1, -1
        li $s2, _
        
        Loop: 
        
        ```
        

# digital logic

- analysis of discrete/binary data in computer system
    - represent real-world signals as binary data
    - logical operations over binary data
    - the design and implement of digital circuit: logic gates, basic components (adder, decoder, etc.), circuits

### example

- consider an elevator which has 0 to 9 floors, if you press a button (0-9), you want to display the number— how do you implement this real-world functionality?
    
    ![Screenshot 2024-02-01 at 5.37.20 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.37.20_PM.png)
    
- **input**: use 10 inputs $X_0,…X_9$, one for each button— if the button $i$ is pressed, set $X_i = 1$
- **output**: use 7 outputs $Y_0…Y_6$, one for each LED light— if $Y_j = 1$, the j-th LED light is on
- **design the boolean functions $F_0…F_6$ to implement the function**: given a combination of inputs, the corresponding LED lights are on

![Screenshot 2024-02-01 at 5.40.29 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.40.29_PM.png)

# boolean algebra

- **implementation**
    - given a desired boolean function, boolean algebra can be applied to develop a simplified implementation of that function, then easier circuits

- **analysis**
    - boolean algebra provides an efficient way to describe the function of a digital circuit

![Screenshot 2024-02-01 at 5.42.17 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.42.17_PM.png)

![Screenshot 2024-02-01 at 5.42.25 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.42.25_PM.png)

## variables and logic operations

- a variable may take on the value 1 (true) or 0 (false)
    
    
    | A AND B = $A ⋅ B$ |
    | --- |
    | A OR B = $A + B$ |
    | NOT A = $A’$ |
    | A NAND B = NOT (A AND B) = $A’ ⋅ B’$ |
    | A NOR B = NOT (A OR B) = $A’ + B’$ |
    | A XOR B = $A ⊕ B $
    
    (exclusive-or: 1 if only one of A and B is 1) |

## switching functions

- we can use all the logical operations to construct a **switching function** $Z = f (A,B, C, ...)$
    - input takes value 0 or 1, output also takes value 0 or 1 — this is why it’s called “**switch**”
    - switch function **defines a mapping from a combination of input values to an output value**
    

### example

- $Z = f (A,B)$
    - there are four different combinations of input values (0,0), (0,1), (1,0), (1,1) — **different way of mapping defines different switching functions**

## truth table

- defines the mapping from the combination of input values to output
    
    
    | A | B | Z = f(A,B)  |
    | --- | --- | --- |
    | 0 | 0 | 1 |
    | 0 | 1 | 0 |
    | 1 | 0 | 1 |
    | 1 | 1 | 1 |
- consider switching functions defined over N variables, how many possible switching functions could we have?
    - we have $2^N$ combinations of input values (rows in truth table), and for each row, we have 2 options for the output value— the total number of switching functions is **2^[2^N]**

## properties of boolean algebra

- basic postulates — stated without proof
    
    ![Screenshot 2024-02-01 at 5.49.55 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.49.55_PM.png)
    
    - commutative law implies that the order of the input does not matter (imagine two wires to a gate)
    - distributive law — similar to algebra, multiplication (AND) has the distributive property over adding while (OR) has the distributive property over AND — $A+BC = (A+B)(A+C$)

- exercise
    - build the truth table for A+BC and (A+B)(A+C)
        
        ![IMG_0178.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/IMG_0178.png)
        

## duality of boolean algebra

- if one expression is correct, its dual is also correct
- how to get the dual of an expression?
    1. replace AND with OR; replace OR with AND
    2. replace constant 1 with 0; replace constant 0 with 1
    3. check duality in the previous table

### other properties

![Screenshot 2024-02-01 at 5.52.53 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_5.52.53_PM.png)

- **algebraic simplification**: these properties are used to transform one expression to another form with specific features

### absorption theorem

- $A(A+B) = A; A+AB = A$
- here, both operations are necessarily **idempotent**

### demorgan’s theorem

- $(AB)’ = A’ + B’; A’ + B’ = (AB)’$
    - in the first equation, no AND operation (change AND to OR)
    - in the second equation, no OR operation (change OR to AND)
    - reduce the types of operations — important for implementation
- **completeness of functionality** — a set of operations (gates) that can be used to implement all logical expressions

### consensus theorem

- $AB + A’C + BC = AB + A’C; (A + B)(A’ + C)(B + C) = (A + B)(’A + C)$
    - there must be three variables
    - each variable must be repeated twice
    - any one of the variables can be a complement
- simplified, less operations

### prove (AND, NOT) is a complete set of gates

- need to show that A+B can be expressed by AND and NOT operations on A and B using demorgan’s law

### prove (NAND) is a complete set of gates

![Screenshot 2024-02-01 at 6.06.29 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.06.29_PM.png)

- there is an obvious shortcoming — we use 3 NAND gates to implement 1 AND gate
    
    ![Screenshot 2024-02-01 at 6.03.54 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.03.54_PM.png)
    

# basic logic gates

- each gate is defined in three ways and has two inputs and a single output

![Screenshot 2024-02-01 at 6.04.38 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.04.38_PM.png)

- a **gate** is the fundamental building block of all digital logic circuits
    - itself is a electronic circuit, producing an output signal that is a simple boolean operation on its input signals
    - **gate delay**: when one input value changes, the correct output signal appears almost instantaneously, delayed only by the propagation time of signals through the gate

## three representations of a circuit

- a circuit is a larger gate using multiple inputs and a single output
    1. **switching function**
    2. **circuit** 
    3. **truth table** 

![Screenshot 2024-02-01 at 6.07.28 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.07.28_PM.png)

## fundamental implementation problem

- how to obtain a circuit? the convenient way is to use the **sum of product form** of logical expressions
    - its a sum of terms, where term is the product of input (or negation of inputs)

![Screenshot 2024-02-01 at 6.08.36 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.08.36_PM.png)

### how to get SOP?

![Screenshot 2024-02-01 at 6.09.00 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.09.00_PM.png)

![Screenshot 2024-02-01 at 6.09.15 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.09.15_PM.png)

## product of sum form

![Screenshot 2024-02-01 at 6.10.04 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.10.04_PM.png)

### POS to circuit

![Screenshot 2024-02-01 at 6.10.23 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.10.23_PM.png)

### which type of 2-level circuit should we choose?

- **less 1’s in F — use SOP**

- **less 0’s in F — use POS**
- shorter expressions means less gates, however we should also consider the types of gates available

# digital components

- a collection of gates that have a specific function
- the design of digital circuits is often at the component level
    - digital component is also called **integrated circuit/IC component**

## multiplexer

- **N input to 1 output unit (N-to-1) MUX**: choose one of the N inputs as the output depending on control signal (also known as **data selector**)
    - $D_i$ is the input data
    - $S$ is called the selection

![Screenshot 2024-02-01 at 6.13.07 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.13.07_PM.png)

- the main advantage is the reduction of wire usage — multiple input wires vs. control wires and one single output wire

### mux implementation

![Screenshot 2024-02-01 at 6.13.50 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.13.50_PM.png)

## demultiplexer

- a decoder with an enabler
    - **less input (A,B); more output (F)**

- when D = 0, all output = 0

- when D = 1, A,B control which output wire is “on”

![Screenshot 2024-02-01 at 6.15.08 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.15.08_PM.png)

- used to connect the source signal to multiple destinations, where each **output wire is connected to one destination**
    - the source signal (AB) decides which destination is on

# binary adder

- one-bit adder (1+1 = 1 0)

- **input**: A, B

- **output**: S (sum), C (carry forward)

![Screenshot 2024-02-01 at 6.17.03 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.17.03_PM.png)

## binary adder

- one bit adder with carry input — the carry forward bit from the previous bit position

![Screenshot 2024-02-01 at 6.17.40 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.17.40_PM.png)

### example

- simplification of C_[out]
    
    ![Screenshot 2024-02-01 at 6.18.17 PM.png](lecture%204%2080e77239a1124cb9ace8f34b3971a974/Screenshot_2024-02-01_at_6.18.17_PM.png)