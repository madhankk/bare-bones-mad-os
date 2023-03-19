# Bare Bones OS - MAD OS

This is just a simple bare bones operating system that I built following along the terrific resource available to write a smallest possible OS running on x86 compatible architectures - https://wiki.osdev.org/Barebones

Note that the OS doesn't do anything useful, but also might be the most secure and most efficient x86 OS every created.

Along the way there were a few issues and small issues which I want to highlight here as my notes.

## Notes
- I compiled the OS on Ubuntu 22.04 LTS server OS
- The first step is really building the cross compiler and you can follow along the cross compiler instructions available at https://wiki.osdev.org/GCC_Cross-Compiler. Additional resources are also available at https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler/ which helps with understanding why you really really need a cross compiler and what that actually does.
- I used the combination of GCC and BinUtils from the versions I found installed in Ubuntu 22.04 LTS which was BinUtils (2.38) and GCC (11.3.0)
- If you have any trouble compiling GCC consider running ./contrib/download_prerequisites which seemed to make some errors go away
- If you have errors finding xorriso when running grub-mkrescue, install the following

`sudo apt-get install libisoburn1`
`sudo apt-get install xorriso`
- To run qemu to test the OS you just built, install it first
`sudo apt install qemu-system-x86`
- When running qemu to boot the ISO file, note that if you are running in a SSH prompt without GTK or any display, you need to turn it off by doing the following
 `qemu-system-i386 -display curses -cdrom myos.iso`
- To exit out of the OS, you will have to press Alt + 2 and then type in 'quit' in the QEMU prompt to exit out of the OS

## Simple instructions
You should really read the full instructions available at https://wiki.osdev.org/Barebones but the basic commands are provided here for completeness sake.
- Assuming you have a cross compiler built and available, ensure you set the path to the cross compiler in your PATH

`export PATH="/home/<yourpath>/opt/cross/bin:$PATH"`
- Next assemble boot.s

`i686-elf-as boot.s -o boot.o`
- Compile Kernel

`i686-elf-gcc -c kernel.c -o kernel.o -std=gnu99 -ffreestanding -O2 -Wall -Wextra`
- Link the Kernel

`i686-elf-gcc -T linker.ld -o mados.bin -ffreestanding -O2 -nostdlib boot.o kernel.o -lgcc`

- Create the ISO image
`mkdir -p isodir/boot/grub`
`cp myos.bin isodir/boot/mados.bin`
`cp grub.cfg isodir/boot/grub/grub.cfg`
`grub-mkrescue -o mados.iso isodir`

- Start the ISO image in QEMU (no GTK display)

`qemu-system-i386 -display curses -cdrom mados.iso`

- If everything worked you should see the OS booting up in all it's glory
