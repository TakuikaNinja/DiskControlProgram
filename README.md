# Sunsoft Disk Control Program disassembly

DA65 disassembly of Sunsoft's Disk Control Program for the Famicom Disk System.

This program utilises a custom serial protocol to read/write memory and execute commands to manipulate disks. See the included `.md` files for more information.

The disassembled file will never be hosted on this repo, and is not intended to be reassembled.

## Generating the disassembly

1. Install the [CC65](https://cc65.github.io) suite. Make sure to add the binaries to `PATH`.
2. Clone this repo.
3. Acquire the Disk Control Program and place it in the repo directory.
4. Run `da65 -i tusin_os.cfg` to generate the `tusin_os.s` disassembly.

## Acknowledgements

The Disk Control Program was developed by Sunsoft and originates from the dev disk lot which was released on Lost Levels.

- [TCRF documentation](https://tcrf.net/Development:Sunsoft_Dev_Disks#Disk_Control)
- [NESdev forum thread](https://forums.nesdev.org/viewtopic.php?t=26408)

