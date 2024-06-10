# lecture 9

# magnetic disk

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

## how are bits organized on platter
- the size of the head is the same as the the width of the track (the head will read/write the tracks)
    - **inter-sector gap and inter-track gap**: separate the sectors, to minimize the errors due to misalignment of the head or simply reduce the interference of magnetic fields

## issue with organization
- there could be **different date rates for different tracks**
    - the head is fixed while the platter is spinning
    - inner and outer tracks have different speeds relatively to the head which results in different data rates when reading/writing bits stored in inner and outer tracks

### arrangements

| type | description |
| --- | --- |
| constant angular velocity | ideal: rotate the platter at a constant speed, but the densities of spots (bits) on different tracks are different. platter is divided into a number of pie-shaped sectors. **Advantage**: blocks of data can be addressed by tracks and sectors. **disadvantage**: amount of data that can be stored on long outer tracks is only the same as what can be stored on short inner tracks (waste of space). |
| multiple zone recording | platters are organized into zones where each zone has multiple tracks. outer track has more sectors. the densities of the sectors are the same—increased capacity. various data transfer rates (for some disk designs, the rotation speed may change to achieve a constant transfer rate). |

## components of disk drive

- **disk → platters → Ttacks → sectors**
- head has the same width as that of tracks (or sectors)—read/write by sectors (one sector usually contains 512 bytes)
- 
## how to locate sector positions within track?
- use extra control data as marks
    - extra control data – used only by disk drive, not accessible by users
- format of disk (the organization of user data and control data)
- **winchester disk format**: each track has 30 sectors, each sector has 600 bytes (512 for data storage)

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

# redundant array of independent disks
- there are 7 levels ranging from **RAID 0 to RAID 6**
    - these levels are NOT hierarchical, they are different designs (disks)
- a logic disk drive to the operating system

- **data striping**: divide logically sequential data (file) into segments (strips) and distribute them to different physical devices
- **redundant**: will store some redundant data (except for RAID 0)

- RAID wants to address two main issues of single disk
    - **I/O speed**: parallel the I/O operations across many disks
    - **reliability**: introduce redundancy

## RAID 0 — NO REDUNDANCY

- data are stripped across all disks using **round robin** stripping
- increased speed
    - a set of data is likely to be stripped across multiple disks
    - disks seek in parallel
    - failure of one disk may cause the loss of multiple files

## RAID 2 and 3 — redundancy through error correction
- the spindles of disk drives are synchronized so that **each disk head is in the same position on each disk at any given time**
- read the bits in the same position in parallel
    - the size of each strip is very small (e.g., a single byte or word)

> RAID 2 and RAID 3 differ in error correction code used

----
- data bits and parity bits are read/write simultaneously
    - codes are calculated “on-the-fly”
    - correct error **in real time**
    - good for low quality disks which has frequent errors
- still lots of redundancy (although better than RAID 1)
- expensive, not commonly used
  
----
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

- surrounding circuitry can change the states of the floating gate
- **read/write though the control gate**
    - overall, this cell can store one bit

## read and write operations

| basic unit: read – page; write – page 
(when the page is empty, i.e., there is no direct over-write)  |
| --- |
| the feature of flash memory is the need to erase data before overwrite (update) |
| data is erased in blocks (using high voltage, time consuming) |
| as a result, if we want to overwrite some pages, we need to erase a block first, then write the pages |

## garbage collection
- copy “good data” E,F,G,H and A’,B’,C’,D’ into a new empty block Y

- erase block X (garbage A, B, C,D are collected – more empty pages)
- this is done in the background, and is controlled by the **controller component** in SSD

## SSD architecture
**components**: 10 to upwards of 60 or 70
    
    - controller: takes the raw data storage in the NAND flash and makes it look and act like hard disk drive
        
        - contains the micro controller, buffer, error correction, and flash interface modules
