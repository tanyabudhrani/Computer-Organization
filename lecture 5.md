# lecture 5

Created: February 14, 2024 10:54 AM

### one-bit binary adder example

| A | B | C[in] | S | C[out] |
| --- | --- | --- | --- | --- |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |
- $S = A’B’C + A’BC’ + AB’C’ + ABC$
- $C[out] = A’BC + AB’C + ABC’ + ABC$

### simplification example

- $C[out] = A’BC + AB’C + ABC’ + ABC$
    - $A’BC + AB’C + ABC’ + ABC + ABC + ABC$ — idempotence
    - $(A’BC + ABC) + (AB’C + ABC) + (ABC’ + ABC)$ — association
    - $(A’ + A)BC + A(B’ + B)C + AB(C’ + C)$ — reverse distribution
    - $BC + AC + AB$ — complement

## three methods for simplification

| algebraic simplication | applying the properties of boolean algebra  |
| --- | --- |
| karnaugh map  | a systematical way to deal with expressions with few variables  |
| quine-mckluskey table  | used for extremely complex expressions |

# karnaugh map

- a representation of a boolean function
- an array of squares, for n variables, there are $2^n$ squares, where **each square represents a combination of input values**

![Screenshot 2024-02-14 at 11.24.29 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.24.29_AM.png)

- for each square, a combination of inputs represents a corresponding **product term**
    - general rule: if 1, use X; if 0, use NOT X
- mark the squares — if a square is marked with 1, the corresponding “product term” is included in the SOP form of that boolean function

![Screenshot 2024-02-14 at 11.25.37 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.25.37_AM.png)

- example
    
    ![Screenshot 2024-02-14 at 11.25.56 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.25.56_AM.png)
    

## using k-map for simplification

- given any boolean, how to do simplification using k-map?
    1. transform the boolean function to canonical form where each term contains every variable
        - example: consider three variables, $AB = AB(C + C’) = ABC + ABC’$
    2. use k-map to represent this canonical form
    3. do some operations to achieve simplification
    4. operation: group “1-squares” to blocks 
        - **“1-square”**: a product term in the boolean function
    5. group two 1-squares into one block — now each block corresponds to one product term 

### step 1: grouping squares into blocks

- group **adjacent** 1-squares into one block
- one block can contain $2^i$ 1-squares
- the goal of grouping 1-squares into blocks is to **maximize the size of the blocks while minimizing the number of blocks**

![Screenshot 2024-02-14 at 11.33.06 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.33.06_AM.png)

- grouping exercises
    
    ![Screenshot 2024-02-14 at 11.33.48 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.33.48_AM.png)
    
    ![Screenshot 2024-02-14 at 11.33.53 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.33.53_AM.png)
    
    ![Screenshot 2024-02-14 at 11.33.58 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.33.58_AM.png)
    

### step 2: derive term from block

- each block represents a term
    - cancel the variable which has inconsistent variables in the block

![Screenshot 2024-02-14 at 11.35.24 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.35.24_AM.png)

- examples
    
    ![Screenshot 2024-02-14 at 11.36.04 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.36.04_AM.png)
    

## don’t care conditions

- for some problems, we do not need to define a complete truth table — that is, some combinations of input values are not meaningful
    - e.g. consider a decimal incrementor where input = X, and output = (X+1)%10 where X ranges from 0 to 9— if we use 4 bits to represent the input and output, there are redundant terms
    
    ![Screenshot 2024-02-14 at 11.37.54 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.37.54_AM.png)
    
- you can specify the value of “d” (1 or blank) in the k-map to **reduce the number of blocks and increase the size of blocks**

![Screenshot 2024-02-14 at 11.38.33 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.38.33_AM.png)

## k-map for SOP vs. POS

### minterm

- product term in SOP form
- **notation**: m_{a decimal value}
    - e.g: $m_5 = 0101 → A’BC’D$
- $SOP = ∑(m_i, m_j, ...)$

![Screenshot 2024-02-14 at 11.42.49 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.42.49_AM.png)

### maxterm

- sum term in POS form
- notation: M_{a decimal value}
    - e.g: $M_5: 5 = 0101 → A+B’+C+D’$
- POS = $∏(M_i, M_j, ...)$

![Screenshot 2024-02-14 at 11.43.56 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.43.56_AM.png)

- difference
    - group the ‘0’
    - note the mapping: 0101 → A+B’+C+D

![Screenshot 2024-02-14 at 11.44.31 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.44.31_AM.png)

# quine-mckluskey tables

- good for any number of variables
    - $AB + AB’ = A(B+B’) = A$
    - $ABC + ABC’ = AB(C+C’) = AB$
    - if two m1 and m2 only differ in exact one position, we can simplify m1+m2
- given $F = ∑(m_i, m_j, ...)$, we can check every pair – but, this is not efficient, so a better way is to group the minterms, according to the number of 1’s— then, check each pair of minterms from the two groups
    - group A = {minterms that have two 1’s}
    - group B = {minterms that have three 1’s}
    - check $m_i$ from A with $m_j$ from B – A+B could possibly simplify

### how to do QM tables!

![Screenshot 2024-02-14 at 11.46.42 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.46.42_AM.png)

![Screenshot 2024-02-14 at 11.46.56 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.46.56_AM.png)

![Screenshot 2024-02-14 at 11.47.10 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.10_AM.png)

![Screenshot 2024-02-14 at 11.47.22 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.22_AM.png)

![Screenshot 2024-02-14 at 11.47.33 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.33_AM.png)

![Screenshot 2024-02-14 at 11.47.43 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.43_AM.png)

![Screenshot 2024-02-14 at 11.47.51 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.51_AM.png)

![Screenshot 2024-02-14 at 11.47.59 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.47.59_AM.png)

### steps

1. arrange the given minterms according to the number of ones present in their binary representation in ascending order
2. take the minterms from the continuous group if there is only a ONE-BIT change to make a pair 
3. place the “-” symbol where there is a bit change, and keep the remaining bits the same
4. repeat steps 2 to 3 until we get all prime applicants (when all the bits present in the table are different) 
    - an implicant is a group of 1s — a prime implicant is the largest possible group of 1s
5. make a prime applicant table that consists of the prime applicants as rows and the given minterms as columns 
6. place 1s in the minterm which are covered by each prime implicant 
7. observe the table, if the minterm is covered by only one prime implicant then it is an ESSENTIAL prime implicant 
8. **simplified SOP = sum of essential prime implicants**

- exercise
    - simply $F(A,B,C,D) = ∑_m(0,1,2,4,6,8,9,11,13,15)$
    
    | group | minterm | a | b | c | d |
    | --- | --- | --- | --- | --- | --- |
    | 0 | 0 | 0 | 0 | 0 | 0 |
    | 1 | 1
    2
    4
    8 | 0
    0
    0
    1 | 0
    0
    1
    0 | 0
    1
    0
    0 | 1
    0
    0
    0 |
    | 2 | 6
    9 | 0
    1 | 1
    0 | 1
    0 | 0
    1 |
    | 3 | 11
    13 | 1
    1 | 0
    1 | 1
    0 | 1
    1 |
    | 4 | 15 | 1 | 1 | 1 | 1 |
    
    | group | minterm | a | b | c | d |
    | --- | --- | --- | --- | --- | --- |
    | 0 | (0,1)
    (0,2)
    (0,4)
    (0,8) | 0
    0
    0
    - | 0
    0
    -
    0 | 0
    -
    0
    0 | -
    0
    0
    0 |
    | 1 | (1,9)
    (2,6)
    (4,6)
    (8,9) | -
    0
    0
    1 | 0
    -
    1
    0 | 0
    1
    -
    0 | 1
    0
    0
    - |
    | 2 | (9,11)
    (9,13) | 1
    1 | 0
    - | -
    0 | 1
    1 |
    | 3 | (11,15)
    (13,15) | 1
    1 | -
    1 | 1
    - | 1
    1 |
    
    | group | minterm | a | b | c | d |
    | --- | --- | --- | --- | --- | --- |
    | 0 | (0,1,8,9)
    (0,2,4,6) | -
    0 | 0
    - | 0
    - | -
    0 |
    | 1 | (9,11,13,15) | 1 | - | - | 1 |
    
    | prime implicants | minterms (0,1,2,4,6,8,9,11,13,15) |
    | --- | --- |
    | B’C’ (0,1,8,9) | 2: 1 
    4: 1 
    11: 1 
    13: 1 |
    | A’D’(0,2,4,6) | 4: 1
    8: 1
    9: 1
    11: 1 |
    | AD(9,11,13,15) | 9: 1
    11: 1
    13: 1
    15: 1 |
    - simplified boolean function = B’C’ + A’D’ + AD

# procedure of designing combinational circuits

## example: access control circuit

- there are 12 students, with ID 0 to 11

- students with ID 2, 3, 4, 8, 11 can enter the lab
- design a digital lock to achieve access control — can achieve this using mathematical modeling
    - define the input/output and the relation (switching function)
        - use 4 bits ABCD to represent the ID
        - use a single bit Y to represent the decision (Y=1 → access granted)
        - use a truth table to represent the relation

![Screenshot 2024-02-14 at 11.52.02 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_11.52.02_AM.png)

- C’D’ + B’CD + A’B’C

## example: shifter

- **input**: vector of bits $W = w_3w_2w_1w_0$ and a signal S = 0/1

- **output**: vector of bits $Y = y_3y_2y_1y_0$ and an extra bit k
- **requirement**: when S = 1, shift to the right by one position $(y_3=0,y_2=w_3,y_1=w_2,y_0=w_1,k=w_0)$; when S = 0, Y = W and k=0 — can achieve this using 2-1 MUX
    - in this case, the control signal S selects between the input vector W and the shifted output Y along with the extra bit k — so, the shifter effectively shifts the input vector to the right by one position when the control signal is 1 and outputs the original input vector with k=0 when control signal is 0

## example: barrel shifter

- rotate shift a vector $W = w_3w_2w_1w_0$ to the right, then fill in the left vacant positions by the “shifted-out” bits
- design a barrel shifter that can shift to the right by 0,1,2,3 positions (controlled by two signals S0 and S0) — can achieve this using four 4-to-1 MUX

> shifts a data word by a specified number of bits using only combinational logic — we can use a sequence of multiplexers where the output of one multiplexer is connected to the input of the next multiplexer depending on the shift distance
> 

![Screenshot 2024-02-16 at 11.45.24 AM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-16_at_11.45.24_AM.png)

# combinational circuits

- combinational circuits are memoryless
    - the output depends only on the current
    

> there is another class of logic circuits with such property: the outputs depend not only on the current inputs, but also on the past behavior of the circuit — there are storage elements (memory) in the circuits
> 

# sequential circuits

- the **contents of the storage elements** represent the **state of the circuit**
- the input may leave the circuit in the same state, or cause it to a new state; **output (next state) = current input + current state**
- over time, the circuit changes through a sequence of states as a result of changes in the inputs

> unlike combinational circuits, which only depend on the current input values to produce outputs, sequential circuits depend on both the current inputs and previous state stored in memory elements
> 

## flip-flops

- a circuit with two stable states that can be used to store binary data

- it is a **bistable** device — meaning that it exists in one of two states and, in the absence of input, remains in that (stable) state

- it has two outputs, **Q and Q’** (the complement of each other)

## S-R latch

- this simple flip-flop circuit has a set input (S) and a reset input (R)— in this system, when you set S as active, the output Q would be high and Q’ would be low
    - use the output Q as the state of the latch with two stable states: Q = 1 and Q = 0

- to show Q = 1 is a stable state: check $Q = 1,Q’ = 0$, we can find inputs R=S=0

- to show Q = 0 is a stable state: check $Q=0,Q’ = 1$, we can find inputs S=R=0

| S | R | Q | Q’ |
| --- | --- | --- | --- |
| 0 | 0 | 0 | 1 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | inf | inf |
- note that **stable state** means that the circuit will stay in this state if the inputs do not change
- we can see that S-R Latch can be used as a storage element to store 1 bit (1-bit memory)

### changing the inputs

- output depends on input and state— as in, given the current state, what would the outputs be if we change the inputs?

- case 1: $Q = 0,Q’ = 1$, change the inputs as $S = 1, R = 0$, what will Q become?
    - $Q = 1,Q’ = 0$— that is $S =1$ writes bit 1 to the memory (**S is called Set**)
    

- case 2: $Q = 1,Q’ = 0$, change the inputs as
$S =0, R = 1$, what will Q become?
    - $Q = 0,Q’ = 1$— that is $R =1$ writes bit 0 to the memory (**R is called Reset**)

## characteristic table

- use characteristic table to describe a sequential circuit
    - describe the relations between next state (output) and input & current state

![Screenshot 2024-02-14 at 12.03.40 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.03.40_PM.png)

| S | R | Q(n+1) | state |
| --- | --- | --- | --- |
| 0 | 0 | Qn | no change |
| 0 | 1 | 0 | reset |
| 1 | 0 | 1 | set |
| 1 | 1 | x | invalid |

![Screenshot 2024-02-25 at 10.00.16 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-25_at_10.00.16_PM.png)

### timing diagram of S-R latch

![Screenshot 2024-02-14 at 12.04.03 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.04.03_PM.png)

### synchronization issue due to gate delay

- the changes of inputs could happen at any time
    - because of delay, unexpected error may happen when different inputs change in a short period
    - so, we want to control the timing of input changes — the solution is to use a **clock signal** for control

![Screenshot 2024-02-14 at 12.04.49 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.04.49_PM.png)

- using a clock to control the timing would result in **edge-triggered flip-flops**
    - the input changes at the edge of the clock signal (when the clock signal changes)

![Screenshot 2024-02-14 at 12.05.27 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.05.27_PM.png)

## edge-triggered flip flops

- a **modification to the latch** which allows the state to only change during a small period of time when the clock pulse is changing from 0 to 1

![Screenshot 2024-02-14 at 12.05.59 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.05.59_PM.png)

![Screenshot 2024-02-14 at 12.06.12 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.06.12_PM.png)

- usage: 1-bit memory
    - characteristic table and timing diagram to
    describe FF
    - use S and R to control the writing of bit 0/1, or use clock to control when to write when the flip-flop is “triggered”

## D flip-flop

- one problem with S-R flip-flop is that $R = S = 1$ should be avoided— D flop-flop uses a single input source: **it is a S-R flip-flop with $S = R’$**

![Screenshot 2024-02-14 at 12.07.28 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.07.28_PM.png)

- the output is always equal to the most recent input value D using storage for one bit of data

![Screenshot 2024-02-14 at 12.08.03 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.08.03_PM.png)

| clock | D | Q | Q’ |
| --- | --- | --- | --- |
| ↓ » 0 | 0 | 0 | 1 |
| ↑ » 1 | 0 | 0 | 1 |
| ↓ » 0 | 1 | 0 | 1 |
| ↑ » 1 | 1 | 1 | 0 |

## J-K flip flop

- all combinations of two input values are valid (including 1 and 1)
- two steps:
    - first a table of J, K, Q_n, Q_{n+1},
    - then reduce to a simpler table

![Screenshot 2024-02-14 at 12.08.45 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.08.45_PM.png)

- when input is (1,1), it is a toggle function: the output is reversed
    - in other words, if J and K data input are different (i.e. high and low), then the output Q takes the value of J at the next clock edge | if J and K are both low, then no change occurs | if J and K are both high, then the output will toggle from one state to another
    
    ![Screenshot 2024-02-14 at 12.09.01 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.09.01_PM.png)
    

![Screenshot 2024-02-25 at 10.04.29 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-25_at_10.04.29_PM.png)

## registers — using flip flops

- used in CPU to store one or more bits (multiple flip-flops)

- **parallel registers**: a set of 1-bit memories that change state simultaneously

- **shift registers**: states are changed sequentially

### 8-bit parallel

![Screenshot 2024-02-14 at 12.09.45 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.09.45_PM.png)

### 5-bit shift

![Screenshot 2024-02-14 at 12.09.51 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.09.51_PM.png)

## counters

- a register whose value will increment by 1
    - basically used to count the **number of clock pulses** applied to a flip-flop
- for a counter composed of N flip-flops, the value ranges from 0 to $2^N−$ 1; that is, the **output of each flip-flop serves as one bit of the N-bit number**

- **asynchronous counter**: states of flip-flops will NOT change at the same time

- **synchronous counter**: states of flip-flops WILL change at the same time

### ripple counter

- an asynchronous counter is also referred to as a ripple counter, where the change that occurs to increment the counter starts at one end and “ripples” through to the other end

![Screenshot 2024-02-14 at 12.11.14 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.11.14_PM.png)

- how to design ripple counter using JK FFs?
    - states will toggle
    - Q0 determines the change of Q1

![Screenshot 2024-02-14 at 12.14.14 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.14.14_PM.png)

- all the inputs to the JK flip-flops are 1 the clock controls the toggling of output— if there is a **clock pulse**,
    1. the output is toggled, the JK flip-flops are sequentially connected, and the output of the previous flip-flop serves as the clock (control signal) of the next flip-flop
    
    ![Screenshot 2024-02-14 at 12.14.41 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_12.14.41_PM.png)
    

### synchronous counter

- use the outputs of N flip-flops to denote the number
- **states change at the same time**
- use synchronous counter as the example to show the design process of sequential circuits — from truth table to SOP to circuits
- e.g. 3-bit synchronous
    - output of 3 J-K flip-flops: CBA
    - states: 000 -> 001 -> 010 -> 011 -> 100 -> 101 -> 110 -> 111 -> 000
    - the key is to identify the required inputs to change one state to the next state

![Screenshot 2024-02-14 at 10.41.45 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_10.41.45_PM.png)

- example
    
    ![Screenshot 2024-02-14 at 10.42.10 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_10.42.10_PM.png)
    
    - essentially, we take each output and translate it into a k-map to find the simplified function
        - so, for JC, the simplified function is BA
    
    ![Screenshot 2024-02-14 at 10.42.16 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_10.42.16_PM.png)
    
    ![Screenshot 2024-02-14 at 10.42.21 PM.png](lecture%205%20deafc8d671874f54aff74494766b7cbe/Screenshot_2024-02-14_at_10.42.21_PM.png)
    

> in **asynchronous**, we don’t use a universal clock— only the first flip flop is driven by the main clock, and the clock input of the rest of the following flip flop is driven by output of previous flip flops | **synchronous** counters, on the other hand, have one global clock which drives each flip flop so the output changes in parallel
>