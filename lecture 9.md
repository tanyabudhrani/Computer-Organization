# lecture 9

Created: March 31, 2024 11:23 PM

# magnetic disk

![Screenshot 2024-03-31 at 11.25.16 PM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-03-31_at_11.25.16_PM.png)

| long term, non-volatile storage |
| --- |
| lowest level memory is slow, large inexpensive |
| a rotating platter |
| a movable read/write head to access data |

## platter

- nonmagnetic material covered with magnetic “coats”
- on the magnetic surface, there are many magnetized spots and each spot stores one bit— **each magnetized spot has two polarities** (S and N)
- the orientations (S-N or N-S) are represented as 0 or 1
    - **example**: 120Gb hard disk drive contains over 120 billion spots
- traditionally, the substrate has been an aluminum or aluminum alloy material, but recently glass substrates have been introduced

### interaction between head and platter

- the essence is the **electromagnetic fields**
    - the change of current will cause the change the magnetic fields, and vice versa
- **head** is covered with magnetic fields and electronic wires
- read and write essentially have the same mechanism: the direction of the current in the head corresponds to the orientation of the magnetized spot
    - **two states of the directions of current = two states of the orientations** (S-N or N-S)

- **read**: magnetic (platter) → current (head)

- **write**: current (head) → magnetic (platter)

### tracks and sectors on platter

| platter | the platter contains a set of concentric rings, called tracks |
| --- | --- |
| track  | each track contains a number of sectors |
| sector | sector = a number of magnetized spots (a block of data)— the size of a sector may vary |

![Screenshot 2024-03-31 at 11.56.14 PM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-03-31_at_11.56.14_PM.png)

## how are bits organized on platter

- the size of the head is the same as the the width of the track (the head will read/write the tracks)
    - **inter-sector gap and inter-track gap**: separate the sectors, to minimize the errors due to misalignment of the head or simply reduce the interference of magnetic fields

![Screenshot 2024-03-31 at 11.59.08 PM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-03-31_at_11.59.08_PM.png)

## issue with organization

- there could be **different date rates for different tracks**
    - the head is fixed while the platter is spinning
    - inner and outer tracks have different speeds relatively to the head which results in different data rates when reading/writing bits stored in inner and outer tracks

### arrangements

| constant angular velocity | ideal: rotate the platter at a constant speed, but the densities of spots (bits) on different tracks are different

platter is divided into a number of pie-shaped sectors

advantage: blocks of data can be addressed by tracks and sectors

disadvantage: amount of data that can be stored on long outer tracks is only the same as what can be stored on short inner tracks (waste of space) |
| --- | --- |
| multiple zone recording | platter are organized into zones where each zone has multiple tracks

outer track has more sectors

the densities of the sectors are the same— increased capacity

various data transfer rate (for some disk designs, the rotation speed may change to achieve constant transfer rate) |

## components of disk drive

- **disk → platters → tracks → sectors**
- head has the same width as that of tracks (or sectors)— read/write by sectors (one sector usually contain 512 bytes)

![Screenshot 2024-04-01 at 12.05.49 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.05.49_AM.png)

## how to locate sector positions within track?

- use extra control data as marks
    - extra control data – used only by disk drive, not accessible by users
- format of disk (the organization of user data and control data)
- **winchester disk format**: each track has 30 sectors, each sector has 600 bytes (512 for data storage)

![Screenshot 2024-04-01 at 12.07.43 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.07.43_AM.png)

| synch byte | the beginning |
| --- | --- |
| head number | because there are multiple platters and heads |
| CRC | error correction code |

## timing of disk i/o transfer

- an important performance measurement since time does not just depend on how fast the disk can read/write bits – many other considerations

- **wait for device**: when process (task) issues an I/O request, it must first wait in a queue for the device to be available

- **wait for channel**: multiple I/O devices may share the channel between processor and devices – need to wait for the channel to be available

### access time

- the time for the head to get to the right position

- **seek time**: the head moves to the right track

- **rotational delay**: the desired sector rotates to the head
- factors range from how compact is the platter (size of platter) to rotation speed

### data transfer

- the actual time to read/write data as platter moves under head
- factors range from the rotation speed to the number of sectors
    - typical rotation speed: **3600 – 20,000 rpm** (revolutions per min)

![Screenshot 2024-04-01 at 12.11.51 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.11.51_AM.png)

# redundant array of independent disks

![Screenshot 2024-04-01 at 12.12.25 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.12.25_AM.png)

- there are 7 levels ranging from **RAID 0 to RAID 6**
    - these levels are NOT hierarchical, they are different designs (disks)
- a logic disk drive to the operating system

- **data striping**: divide logically sequential data (file) into segments (strips) and distribute them to different physical devices

- **redundant**: will store some redundant data (except for RAID 0)

- RAID wants to address two main issues of single disk
    - **I/O speed**: parallel the I/O operations across many disks
    - **reliability**: introduce redundancy

## RAID 0 — NO REDUNDANCY

![Screenshot 2024-04-01 at 12.17.40 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.17.40_AM.png)

- data are stripped across all disks using **round robin** stripping
- increased speed
    - a set of data is likely to be stripped across multiple disks
    - disks seek in parallel
    - failure of one disk may cause the loss of multiple files

![Screenshot 2024-04-01 at 12.19.23 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.19.23_AM.png)

## RAID 1

![Screenshot 2024-04-01 at 12.19.47 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.19.47_AM.png)

## RAID 2 and 3 — redundancy through error correction

- the spindles of disk drives are synchronized so that **each disk head is in the same position on each disk at any given time**
- read the bits in the same position in parallel
    - the size of each strip is very small (e.g., a single byte or word)

> RAID 2 and RAID 3 differ in error correction code used
> 

![Screenshot 2024-04-01 at 12.22.35 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.22.35_AM.png)

- data bits and parity bits are read/write simultaneously
    - codes are calculated “on-the-fly”
    - correct error **in real time**
    - good for low quality disks which has frequent errors
- still lots of redundancy (although better than RAID 1)
- expensive, not commonly used

![Screenshot 2024-04-01 at 12.22.48 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.22.48_AM.png)

- only one redundant disk, no matter how large the array
    - more efficient than RAID 2
- it can handle hardware failure
- suppose one disk fails, can fully recover the data
- not good for soft failures (bit level errors), since it can **only detect one error**

## RAID 4-6

- compared to RAID 3
    
    
    | disks are accessed independently (not in parallel)  |
    | --- |
    | strips have much larger size (block-level), good for high I/O read request rate (since one request can handled by one disk) |
    | also computes a single bit parity for corresponding data bits (the distribution of parity bits are different in RAID 4, 5, 6) |

![Screenshot 2024-04-01 at 12.30.36 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.30.36_AM.png)

![Screenshot 2024-04-01 at 12.31.02 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.31.02_AM.png)

![Screenshot 2024-04-01 at 12.31.09 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.31.09_AM.png)

| category | level | description | disk required | data available | large I/O data transfer capacity | small I/O request rate |
| --- | --- | --- | --- | --- | --- | --- |
| striping | 0 | nonredundant | N | lower than single disk | very high | very high for both |
| mirroring | 1 | mirrored | 2N | higher than RAID 2, 3, 4, or 5; lower than RAID 6 | higher than single disk for read;
similar to single disk for write | up to twice that of a
single disk for read; similar to single disk for write |
| parallel access | 2 | redundant via hamming code | N+m  | much higher than single disk;
comparable to RAID 3, 4, or 5 | highest of all listed alternatives | approximately twice that of a single disk |
| parallel access | 3 | bit-interleaved parity | N+1 | much higher than single disk;
comparable to RAID 2, 4, or 5 | highest of all listed alternatives | approximately twice that of a single disk |
| independent access | 4 | block-interleaved parity | N+1 | much higher than single disk;
comparable to RAID 2, 3, or 5 | similar to RAID 0 for read;
significantly lower than single
disk for write | similar to RAID 0 for
read; significantly lower than single disk for write |
| independent access | 5 | block-interleaved distributed parity | N+1 | much higher than single disk;
comparable to RAID 2, 3, or 4 | similar to RAID 0 for read;
lower than single
disk for write | similar to RAID 0 for read; generally lower than single disk for write |
| independent access | 6 | block-interleaved dual distributed parity | N+2 | highest of all listed alternatives | similar to RAID 0 for read;
lower than RAID 5 for write | similar to RAID 0 for
read; significantly lower
than RAID 5 for
write |

# SSD

- non-volatile, non-mechanical, fast access than HDD
- **controller + memory component**
- specifically, memory component uses NAND flash memory (since 1990s)

- **basic element**: NAND flash cell that can store 1 bit

- **design**: the organization of cells and read/write operations

![Screenshot 2024-04-01 at 12.41.31 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.41.31_AM.png)

- surrounding circuitry can change the states of the floating gate
- **read/write though the control gate**
    - overall, this cell can store one bit

## from cells to pages to blocks

![Screenshot 2024-04-01 at 12.42.20 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.42.20_AM.png)

![Screenshot 2024-04-01 at 12.42.34 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.42.34_AM.png)

## read and write operations

| basic unit: read – page; write – page 
(when the page is empty, i.e., there is no direct over-write)  |
| --- |
| the feature of flash memory is the need to erase data before overwrite (update) |
| data is erased in blocks (using high voltage, time consuming) |
| as a result, if we want to overwrite some pages, we need to erase a block first, then write the pages |

### example

![Screenshot 2024-04-01 at 12.44.51 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.44.51_AM.png)

![Screenshot 2024-04-01 at 12.45.01 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.45.01_AM.png)

## garbage collection

- copy “good data” E,F,G,H and A’,B’,C’,D’ into a new empty block Y

- erase block X (garbage A, B, C,D are collected – more empty pages)
- this is done in the background, and is controlled by the **controller component** in SSD

## SSD architecture

![Screenshot 2024-04-01 at 12.46.13 AM.png](lecture%209%20ef14a8d651674b39945e3c4087940990/Screenshot_2024-04-01_at_12.46.13_AM.png)

- SSDs contain a number of NAND flash parts
    - **components**: 10 to upwards of 60 or 70
    - **controller**: takes the raw data storage in the NAND flash and makes it look and act like hard disk drive
        - contains the micro controller, buffer, error correction, and flash interface modules