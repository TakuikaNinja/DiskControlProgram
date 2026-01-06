# Sunsoft Disk Control Program Serial Transfer Protocol

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
7. XOR the destination byte with $FF.
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

TODO

