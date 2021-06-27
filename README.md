# qemu-hello-world
1. Objective is to emulate ARM device using QEMU
2. Implementation to transmit message from QEMU tool
3. Use GDB to remote connect for target debug

# Prerequisite
In order to execute the project, you will need to install:
- ARM GCC toolchain.
- QEMU emulator for ARM systems.

### ARM GCC toolchain setup:

If you prefer, you can install ARM GCC toolchain from command line:
```apt-get install gcc-arm-none-eabi```

But I prefer to download paticular version of ARM toolchain tar packages and set the environment PATH to the extracted toolchain location.
The code is compiled and executed using gcc-arm-none-eabi-9-2020-q2-update-x86_64-linux.tar.bz2.
Toolchain manual instructions as below:
```
$ cd ~/dev/
$ mkdir toolchain && cd toolchain
$ wget https://armkeil.blob.core.windows.net/developer/Files/downloads/gnu-rm/9-2020q2/gcc-arm-none-eabi-9-2020-q2-update-x86_64-linux.tar.bz2
$ tar -xf gcc-arm-none-eabi-9-2020-q2-update-x86_64-linux.tar.bz2
$ export PATH=$PATH:~/dev/toolchain/gcc-arm-none-eabi-9-2020-q2-update/bin
```

### QEMU installation command:
QEMU for ARM systems can be installed using command ```apt-get install qemu```

# Project Build Instructions
Since the objective is to demonstrate and for understanding purpose, I am building the project from command line
```
$ arm-none-eabi-as -mcpu=arm926ej-s -g startup.S -o startup.o
$ arm-none-eabi-gcc -c -mcpu=arm926ej-s -g qemu_hello.c -o qemu_hello.o
$ arm-none-eabi-ld -T layout.ld qemu_hello.o startup.o -o qemu_hello.elf
$ arm-none-eabi-objcopy -O binary qemu_hello.elf qemu_hello.bin
```
Feel free to write your own makefile

# Project Execution

### Without GDB connection
- The program prints “Hello world from QEMU!” in the terminal and runs indefinitely until user issues exit command; The command to start and run the program:
```$ qemu-system-arm -M versatilepb -m 128M -nographic -kernel qemu_hello.bin```
- To exit QEMU, press “Ctrl + a” and then “x”.

### With GDB connection
- QEMU implements a gdb interface using a TCP connection. The command to start and run the program in QEMU with GDB support
```$ qemu-system-arm -M versatilepb -m 128M -nographic -s -S -kernel test.bin```
- This command freezes the system before executing any guest code, and waits for a connection on the TCP port 1234. On another terminal, start the GDB connection using command
```$ arm-none-eabi-gdb```
- Inside gdb specify the binary file and target address to connect. If prompted to restart GDB execution, select yes. After binary loading is successful, run the program with continue command.
```
$ target remote localhost:1234
$ file qemu_hello.bin
$ continue
```
- As soon as continue command is issued by GDB tool, qemu outputs "Hello world frm QEMU!" in the terminal.
- The exit command in gdb closes both the debugger and the emulator.

# Reference
- https://balau82.wordpress.com/2010/02/28/hello-world-for-bare-metal-arm-using-qemu/
- http://www.bravegnu.org/gnu-eprog/hello-arm.html
