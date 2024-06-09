# lecture 6

## 3-bit synchronous counter
- the counter will count through 000, 001, 111, then get back to 000— each count (e.g, 001) is a **state of the counter** (machine), and the number of possible states is finite

# finite state machine
- abstract model to describe real-world systems that uses states to represent the situations that the system is in
- it specifies how the states of the system would change based on the **external input**, and how the system would produce external output accordingly

> the 3-bit synchronous counter is a special case because it does not have external input and output — however, it did demonstrate an essential part of FSM – **the memory component to store the state**

## formal model of FSM
- a computation model that can be used to simulate sequential logic — $FSM = ( S, I, O, π)$

| S | the finite set of states |
| --- | --- |
| I | the finite set of external inputs |
| O | the finite set of external outputs |
| π | state transition function: define the relations among input, output, current state, next state |

- **next state**
    - external input + current state

- **external output**
    - external input + current state
- (i.e. the next state depends on the current state and the external input)

### mealy machine vs. moore machine
- mealy machine is defined as a machine whose output values are determined by both its current state and current inputs
- moore machine is defined as a machine whose output values are determined only by its current state

## state diagram
- **circles**: represent states
    - labelled with $S_k$ to denote the state (or a binary encoding)

- **directed arcs**: represent the transitions between states
    - labelled with input/output for that state transition

### example

## restrictions on state diagram
| time is discretized: use clock signal to control the timing of the transitioning of states (synchronous!) — we usually use time t and t+1 to denote the timing for the current state and next state |
| --- |
| FSM can only be in one state at a time — therefore, only in one state (or one circle) at a time |
| mealy machine and moore machine can be labelled differently

mealy machine: label directed arcs with input/output for that state transition
moore machine: since output depends only on state, we can label directed arcs with input for that state transition, and label state circles with $S_k$ output |

### example

## state table

| current state | next state | next state | output | output |
| --- | --- | --- | --- | --- |
|  | x=0 | x=1 | x=0 | x=1 |
| S0 |  |  |  |  |
| S1 |  |  |  |  |
| S2 |  |  |  |  |
| S3 |  |  |  |  |
- exercise
    | current state | input X | input Z | output  |
    | --- | --- | --- | --- |
    | S0 | 0 | 0 | {0, 0} |
    | S0 | 1 | 0 | {1, 0} |
    | S1 | 0 | 1 | {0, 1} |
    | S1 | 1 | 1 | {1, 1} |
    

## essential task for FSM
- given state diagram/state table, derive the functions that specify the relations among next state, output, current state, and input
    - next state = f(current state, input)
    - output = g(current state, input)
- the design of sequential circuits relies on the above two functions using **truth tables and k-maps**

### example: pattern detector
- design a machine that will detect a specific bit pattern
- it receives bit stream and it has one output
    - if the bit pattern appears in the bit stream, the output Z = 1; otherwise, Z = 0

## modeling the problem using FSM
- we use the following states to model the possible situations that the machine is in (detect pattern 1101)
    
    | S0 | the initial state (0/4 success) |
    | --- | --- |
    | S1 | the first desired symbol (1) is detected (1/4 success) |
    | S2 | the desired sub-pattern 11 is detected (2/4 success) |
    | S3 | the desired sub-pattern 110 is detected (3/4 success) |

- its important to note:
    1. the transitions from S0 to S1 to S2 to S3 only when desired bit is received
    2. for most of the cases when undesired bit is received, it goes back to S0 (restart the detection again)
    3. some special cases: S2 goes back to S2 (11 when received 1); S3 goes to S1 (110 when received 1)

### from state diagram to functions
- in the truth table:
    1. we use two bits P1,P0 to denote the current state; use N1, N0 for the next state
        - if the input is 0 after S0, we return back to S0
        - if the input is 1 after S0, we can move to S1
        - if the input is 0 after S1, we go back to S0, and so on
    2. (P1,P0,X) are the variables, N1, N0, Z are the function values
    3. given a combination of (P1,P0,X), we need to find the value for N1, N0, Z from the state diagram

# J-K flip flops
- **characteristics table**: defines the next state of flip-flop in terms of input and current state 
- **excitation table**: defines the flip-flop input variable as a function of the current state and next state

- we are given the state diagram of FSM— we can drive the functions:
    - next state = **f(external input, current state)**
    - external output = **g(external input, current state)**
    - f, g can be realized using combinational logic— however, we need to use flip-flops to represent the states

## implementation using flip-flops
- the key question is to decide the input of the FF
    
    > note: we already know the relationships among external output, external input, current state and next state (we use the output of FF to denote both the current and next state)
    
    > it is the input to the FF that decides **how to drive the current state to the next state**

### implementation using D-flip flops
- characteristic equation: Q_{next} = D
- next state = f(external input, current state)
- D = f(external input, current state)

### implementation using other flip-flops
- we will use the excitation table of that flip-flop to derive the excitation table of the circuit
- **excitation table**: what inputs of the flip-flop are required to transit from one state to the next state
    - this excitation table of the circuit is also a truth table — the inputs of the flip-flop are the function values and others are variables

### steps in designing sequential circuits
- **FSM model**: define the states and state transitions
- state table + excitation table of flip-flops → excitation table of the circuit
    
- use K-map to derive the SOP form of two functions:

- **input of flip-flops** = current state (output of flip-flops) + external input

- **external output** = current state (output of flip-flops) + external input
