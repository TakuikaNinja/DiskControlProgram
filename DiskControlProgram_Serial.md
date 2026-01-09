# Sunsoft Disk Control Program Serial Transfer Protocol

Note: Data transfer directions are from the console's perspective. 

## Interface

### Data Output/Status ($4016 read/write)

 7  bit  0
 ---- ----
 xxxx xDSx
       ||
       |+-- Status bit (read)
       +--- Data Output bit (write)

### Data Input ($4017 read)

 7  bit  0
 ---- ----
 xxxD DDDx
    | |||
    +-+++-- Data Input

4-bit parallel input. The data read is inverted due to expansion port inputs.

## Transfer protocol

### Read byte

1. Wait until the status bit is set.
2. Output a 1 bit, then wait until the status bit is cleared.
3. Read 4 bits. Shift into upper nybble of destination byte.
4. Output a 0 bit, then wait until the status bit is set.
5. Output a 1 bit, then wait until status bit is cleared.
6. Read 4 bits. Shift into lower nybble of destination byte.
7. XOR the destination byte with 0xFF.
8. Output a 0 bit.

### Write byte

1. Perform a short delay?
2. Output a 1 bit.
3. Wait until the status bit is set.
4. Shift out the least significant bit from the source byte. This will be the source bit.
5. Output the inverted source bit and wait until the status bit is cleared.
6. Repeat from step 3 until all 8 bits have been output.
7. Wait until the status bit is set.
8. Output a 0 bit and wait until the status bit is cleared.

## Data Formats

Serial modes >2 are ignored. Due to a pre-decrement check, a length byte of 0 = 256 bytes.

### Serial Mode 0: Read serial data

Input format: 0x00 byte, length, destination address hi, destination address lo, data bytes

### Serial Mode 1: Write (output) serial data

Input format: 0x01 byte, length, source address hi, source address lo
Output format: data bytes

### Serial Mode 2: Read packets

Read multiple mode 0 packets until a length = 0 byte is read.
Input format: 0x02 byte, (length, destination address hi, destination address lo, data bytes), ..., 0x00 byte

