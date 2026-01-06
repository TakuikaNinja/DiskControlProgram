# Sunsoft Disk Control Program Commands

## Command Format

$E0 = command
$E1 = must be nonzero to execute the command

These variables and all of the command parameters below are likely intended to be updated using the program's serial transfer protocol.

## Commands

### 10-DISK DELETE

Delete files from the inserted disk and update the directory. This is done by simply altering the file count.
Parameter: $DFCA = new file count
The new file count must be <= the original file count, otherwise error 31 is thrown.

### 20-DISK SAVE

Save a file to the inserted disk and update the directory.

Parameters:
- $DF08 = File ID copied to $DFCB
- $6000-$600E = rest of File Header struct copied to $DFCC
- $600F = File ID override (If nonzero, the File ID in the header is used instead of $D80F)
- $6010- = File Data to save

Note that $DF08 is also used by the program to detect ejected disks ($FF = ejected, others = inserted) - this is probably intended for defining boot files. Even if the BIOS disk I/O routines didn't throw an error due to a missing disk, files saved with ID $FF can never be loaded by the BIOS.

### 40-DIRECTORY

Fetch the directory listing for the current disk into $D800. All the other commands either implicitly perform this command or update the directory listing.

Directory Format:
- 13 bytes from the disk info block after the `*NINTENDO-HVC*` verification string
- disk capacity (16-bit word, big endian)
- file count (from file amount block)
- then for each file: file header block contents with a $00 byte between the File Address & File Size fields

### 80-DISK LOAD

Perform a File Header match test on the current disk's files. When successful, the last matching File Header struct is loaded into $6000 and its File Data is loaded into $6010. There are no checks are in place to prevent loads from multiple matching files, or from loads overwriting the running program.

Parameter: $DFC0 = File Header struct to test/load ($FF only by default)
File Header format: File Number, File ID, File Name (8 byte string), File ID override

- $FF can be placed in File ID to skip the File Name check
- If File ID override != 0, the File ID in the File Header is used instead of $D80F for the next disk save command

Error List (BCD):
- $40 = no files matched
- $41 = file count is 0
- $42 = File Number >= file count

