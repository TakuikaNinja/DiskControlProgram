# Sunsoft Disk Control Program Serial Transfer Protocol

## Interface

### RTS, RxD/CTS ($4016 read/write)

```
 7  bit  0
 ---- ----
 xxxx xDSx
       ||
       |+-- RTS (read)
       +--- RxD/CTS (write)
```

Appears to follow level-shifted RS232. In 5V TTL terms, this would be the inverted forms of /RTS, RX, and /CTS.

### Parallel TxD data ($4017 read)

```
 7  bit  0
 ---- ----
 xxxD DDDx
    | |||
    +-+++-- Parallel TxD data
```

4-bit parallel data, likely converted from serial data to take advantage of the extra inputs. In 5V TTL terms, this would be the inverted form of TX in 4-bit parallel.

## Transfer protocol

The transfer process is initialised when the program first detects that RTS is set.

The serial protocol appears to use a variant of level-shifted RS232 with RTS/CTS flow control, where TxD is 4-bit parallel data.

### Receive byte from interface

1. Wait until RTS is set.
2. Set CTS, then wait until RTS is cleared.
3. Read 4 bits from TxD. Shift into upper nybble of destination byte.
4. Clear CTS, then wait until RTS is set.
5. Set CTS, then wait until RTS is cleared.
6. Read 4 bits from TxD. Shift into lower nybble of destination byte.
7. XOR the destination byte with 0xFF.
8. Clear CTS.

### Transmit byte to interface

1. Perform a short delay, probably to ensure reliable stop/idle -> start detection?
2. Write start bit to RxD.
3. Wait until RTS is set.
4. Shift out the least significant bit from the source byte. This will be the source bit.
5. Write the inverted source bit to RxD and wait until RTS is cleared.
6. Repeat from step 3 until all 8 bits have been written.
7. Wait until RTS is set.
8. Write stop/idle bit to RxD and wait until RTS is cleared.

## Data Formats

Serial modes >2 are ignored. Due to a pre-decrement check, a length byte of 0 = 256 bytes except in mode 2 (where it is used as a terminator).

### Serial Mode 0: Receive packet from interface

Data from interface: 0x00 byte, length, destination address hi, destination address lo, data bytes

### Serial Mode 1: Transmit data to interface

Data from interface: 0x01 byte, length, source address hi, source address lo

Data to interface: data bytes

### Serial Mode 2: Receive packets from interface

Receive multiple mode 0 packets until a length = 0 byte is received.

Data from interface: 
0x02 byte, (length, destination address hi, destination address lo, data bytes), ..., 0x00 byte

