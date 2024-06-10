# lecture 10

## I/O performance metrics
- **I/O bandwidth (throughput)**
    - amount of information that can be input (output) and communicated per unit time
    - how much data can we move through the system in a certain time
    - how many I/O operations can we do per unit time

- **I/O response time (latency)**
    - total elapsed time to accomplish an input or output operation
    - an especially important performance metric in real-time systems

> why do we need I/O modules?
what functions are supported by I/O modules?
how to implement the functions of I/O modules?

## the need for I/O modules
- why don’t directly connect external devices using system bus?

- two considerations— **data transfer rate and logic**
    - system bus has a high data transfer rate
    - external devices are much slower
    - also, there are a variety of devices with various transfer rates which makes it hard to match (e.g. inefficiency)

- we need something more than just a connector— we need a logic to control and translate the data formats of the various external devices
    - **BUS**: a shared communication link (a single set of wires used to connect multiple subsystems) that needs to support a range of devices with widely varying latencies and data transfer rates
    
- an I/O module is more than a “connector”, it is like a tiny processor with two major functions
    - **interface to the processor and memory** via the system bus or some central switch
    - **interface to one or more external devices** by some data links

### bus
- **advantages**:
    - versatile – new devices can be added easily and can be moved between computer systems that use the same bus standard
    - low cost – a single set of wires is shared in multiple ways

- **disadvantages**:
- creates a communication bottleneck – bus bandwidth limits the maximum I/O throughput
- the maximum bus speed is largely limited by
    - the length of the bus
    - the number of devices on the bus

## external/peripheral devices

| buffer  | temporarily hold data being transferred between I/O module and outside environment |
| --- | --- |
| transducer  | conversion between electrical energy (bits) and other forms of energy (it is device-specific) |

## control and timing
- need to coordinate the flow of traffic between internal resources and external devices
    - processor may communicate with multiple external devices at any time and it’s often unpredictable
    - the communication relies on internal resources (main memory and system bus), which are shared by external devices
    - an I/O module is like a driver in a car (data flow) – coordinate when to transfer the data

### the typical scenario of data transfer
1. processor asks the I/O module to check the status of device
2. I/O module returns the device status
3. if device is ready, processor sends a command to request data
4. I/O module obtains units of data from external device
5. data is transferred from I/O module to processor

## processor communication
- three types of information: **command, data, status**
    
    | processor sends commands to I/O module through control bus and data bus (e.g. READ SECTOR for disk drive) |
    | --- |
    | exchange data between processor and I/O module |
    | status: I/O module reports status of the external device since it is usually very slow (e.g. status could be BUSY and READY) |

## device communication
- similar but with different codes (e.g. PCL, PS)
- particularly important is the address of a device
- each device is associated with unique addresses and the I/O module needs to recognize the addresses for the devices it controls – in the eye of processor, **each I/O has a unique address or a set of unique addresses**

### keyboard input as an example
- international reference alphabet (IRA)
    - basic unit of exchange is the character
        - associated with each character is a code
        - each character in this code is represented by a unique 7-bit binary code. 128 different characters can be represented
    - characters are of two types:
        - **printable**: alphabetic, numeric, and special characters that can be printed on paper or displayed on a screen
        - **control**: have to do with controlling the printing or
        displaying of characters

- keyboard codes
    - when the user depresses a key, it generates an electronic signal that is interpreted by the transducer in the keyboard and translated into the bit pattern of the corresponding IRA code
    - this bit pattern is transmitted to the I/O module in the computer
    - on output, IRA code characters are transmitted to an external device from the I/O module
    - the transducer interprets the code and sends the required electronic signals to the output device either to **display the indicated character** or **perform the requested control function**

> user depress a key (input from the outside word) → generate electronic signal and translated into bit pattern, which is one code (done by transducer) → buffer → transmitted to I/O module → memory/processor

## data buffering and error detection
- data from processor and devices are temporarily buffered in I/O module so they can cope with different data transfer rates
- error detection refers to either detecting mechanical and electrical malfunctions or detecting soft errors using error detection code (parity bits)

- I/O modules allow the processor to view a wide range of devices in a simple-minded way
- I/O modules hide the details of timing, data formats, and other specifications from the processor which simply issues read/write commands

## functional views
- **goal of I/O**: usually, get data from I/O and store
data in memory (input) or get data from memory and send data to I/O (output)

- **key**: the interaction between CPU, memory, I/O modules over the system bus

## I/O techniques
1. **processor** serves as the intermediate party between I/O and memory— **programmed I/O and interrupt-driven I/O**
2. memory and I/O interact directly without CPU— **direct memory access**

|  | no interrupts | use of interrupts |
| --- | --- | --- |
| I/O to memory transfer through process | programmed I/O  | interrupt-driven I/O  |
| direct I/O to memory transfer |  | direct memory access (DMA)  |

## programmed I/O
- processor issues I/O commands to I/O modules and directly control the I/O process
- **key feature**: processor will wait for the completion of the I/O command
    - processor sends the command to I/O module
    - I/O module will do the job and set the I/O status register after finishing the job— I/O module will not alert processor
    - processor needs to periodically check the status to see it the job has been completed

## I/O commands
- I/O module may receive four types of I/O commands from the processor:
    
    | control  | to activate a device and tell it what to do – tailored to the particular type of device |
    | --- | --- |
    | test | to obtain the various status of I/O module and device; check the status registers; example: ready? completed? error? |
    | read | cause I/O module to obtain data from device and put data in internal buffer, then data bus |
    | write | the reverse direction of read |

## another important issue — addressing
- processor needs to know which I/O device it is interacting
- each device is associated with unique identifiers (addresses)
- as a result, each I/O device is associated with a set of addresses
- when processor issues a device address, **each I/O module needs to recognize the address and determine if the address is for itself**
- what’s this address? – two modes of addressing

### memory mapped I/O
- memory and devices share the same address space
- intuition: devices contain different registers, and processor actually wants to access these registers (check status, data,…) – these registers are “part of memory”
    - example: 10 address lines, first 512 locations for memory, latter 512 locations for devices
- in the view of processor, devices are similar to memory— processor use instructions (containing address) to access device

### isolated I/O
- separated address space for memory and device
    - use **I/O or memory select lines** (to differentiate the addresses transmitted over the address bus)
    - system will have separate instructions for I/O operations where the identifier of the device is hard-coded into the instructions

## interrupt-driven I/O
1. processor issues I/O commands to I/O module
2. processor continues its other tasks while the module performs I/O operations
3. the module signals the processor when I/O operations are finished, meaning that it’s ready to transfer data, via interrupt
4. processor responds by interrupt handler, e.g., by executing an interrupt service routine

- the key difference is the **inner loop** which periodically checks the status of the I/O

## handling interrupt
- combination of hardware and software resources to handle interrupt
- need extra hardware resources to store the state (PC, contents of CPU registers, etc) of the current program— **the state is stored in stack**, which is a special type of memory)
- **new PC value = software (program) to handle interrupt**

----
- **software**: designated programs to handle I/O devices (different for various I/O devices)
- **designated programs**: interrupt-service routine and device-service routine
----

## changes in memory and register for an interrupt
- typically, the **interrupt handler** will begin by saving the contents of all registers on the stack, so when a user program is interrupted after the instruction at location N, the contents of all of the registers plus the address of the next instruction (N + 1) are pushed onto the stack
- the **stack pointer** is updated to point to the new top of stack, and the **program counter** is updated to point to the beginning of the interrupt service routine, then the **interrupt handler** next processes the interrupt— this includes an examination of status information relating to the I/O operation or other event that caused an interrupt
- when interrupt processing is complete, the **saved register values** are retrieved from the stack and restored to the registers

### design issues for interrupt-driven I/O
| cause  | there are various devices, causing interrupts at any time |
| --- | --- |
| two issues | how to identify which device has issued the interrupt? (someone is knocking on the door, but who?)

how to decide which one to proceed if multiple interrupts occur (e.g. priority)? |
| hardware solution | separate interrupt lines (different doors)— still, there could be multiple devices using the same interrupt line
for sending the signal

use extra software solutions combined with hardware solution |
| software solution | polling mechanism— combine interrupt-service routine and device-service routine |

## software poll
- interrupt-service routine
    - goal: find which device causes the interrupt
    - could have different ways — check the addressable status register in each device in order
    - once the device is identified, run the corresponding device-service routine
    - priority: the order determines the priority (first check, first serve)
