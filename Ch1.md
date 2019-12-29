Our hello program begins life as a source program (or source file) that the
programmer creates with an editor and saves in a text file called hello.c. The
source program is a sequence of bits, each with a value of 0 or 1, organized
in 8-bit chunks called bytes. Each byte represents some text character in the
program.
Most modern systems represent text characters using the ASCII standard that
represents each character with a unique byte-sized integer value. 

In order to run hello.c on the system, the individual C statements must be translated by other programs
into a sequence of low-level machine-language instructions. These instructions are
then packaged in a form called an executable object program and stored as a binary
disk file. Object programs are also referred to as executable object files.

On a Unix system, the translation from source file to object file is performed
by a compiler driver. 

the gcc compiler driver reads the source file hello.c and translates it into
an executable object file hello. The translation is performed in the sequence
of four phases shown in Figure 1.3. The programs that perform the four phases
(preprocessor, compiler, assembler, and linker) are known collectively as the
compilation system.
- Preprocessing phase.The preprocessor (cpp) modifies the original C program
according to directives that begin with the # character. For example, the
#include <stdio.h> command in line 1 of hello.c tells the preprocessor
to read the contents of the system header file stdio.h and insert it directly
into the program text. The result is another C program, typically with the .i
suffix.
- Compilation phase. The compiler (cc1) translates the text file hello.i into
the text file hello.s, which contains an assembly-language program. Each
statement in an assembly-language program exactly describes one low-level
machine-language instruction in a standard text form. Assembly language is
useful because it provides a common output language for different compilers
for different high-level languages. For example, C compilers and Fortran
compilers both generate output files in the same assembly language.
- Assembly phase. Next, the assembler (as) translates hello.s into machinelanguage instructions, packages them in a form known as a relocatable object
program, and stores the result in the object file hello.o. The hello.o file is
a binary file whose bytes encode machine language instructions rather than
characters. If we were to view hello.o with a text editor, it would appear to
be gibberish.
- Linking phase.Notice that our hello program calls the printf function, which
is part of the standard C library provided by every C compiler. The printf
function resides in a separate precompiled object file called printf.o, which
must somehow be merged with our hello.o program. The linker (ld) handles
this merging. The result is the hello file, which is an executable object file (or
simply executable) that is ready to be loaded into memory and executed by
the system.


**Buses** 
Running throughout the system is a collection of electrical conduits called buses
that carry bytes of information back and forth between the components. Buses
are typically designed to transfer fixed-sized chunks of bytes known as words. The
number of bytes in a word (the word size) is a fundamental system parameter that
varies across systems. Most machines today have word sizes of either 4 bytes (32
bits) or 8 bytes (64 bits). 

**I/O Devices**
Input/output (I/O) devices are the system’s connection to the external world. Our
example system has four I/O devices: a keyboard and mouse for user input, a
display for user output, and a disk drive (or simply disk) for long-term storage of
data and programs. Initially, the executable hello program resides on the disk.
Each I/O device is connected to the I/O bus by either a controller or an adapter.
The distinction between the two is mainly one of packaging. Controllers are chip
sets in the device itself or on the system’s main printed circuit board (often called
the motherboard). An adapter is a card that plugs into a slot on the motherboard.
Regardless, the purpose of each is to transfer information back and forth between
the I/O bus and an I/O device.

**Main Memory**
The main memory is a temporary storage device that holds both a program and
the data it manipulates while the processor is executing the program. Physically,
main memory consists of a collection of dynamic random access memory (DRAM)
chips. Logically, memory is organized as a linear array of bytes, each with its own
unique address (array index) starting at zero. In general, each of the machine
instructions that constitute a program can consist of a variable number of bytes.
The sizes of data items that correspond to C program variables vary according to
type. For example, on an IA32 machine running Linux, data of type short requires
two bytes, types int, float, and long four bytes, and type double eight bytes.

**Processor**

The central processing unit (CPU), or simply processor, is the engine that interprets (or executes) instructions stored in main memory. At its core is a word-sized
storage device (or register) called the program counter (PC). At any point in time,
the PC points at (contains the address of) some machine-language instruction in
main memory.1
From the time that power is applied to the system, until the time that the
power is shut off, a processor repeatedly executes the instruction pointed at by the
program counter and updates the program counter to point to the next instruction.
A processor appears to operate according to a very simple instruction execution
model, defined by itsinstruction set architecture. In this model, instructions execute
in strict sequence, and executing a single instruction involves performing a series
of steps. The processor reads the instruction from memory pointed at by the
program counter (PC), interprets the bits in the instruction, performs some simple
operation dictated by the instruction, and then updates the PC to point to the next
instruction, which may or may not be contiguous in memory to the instruction that
was just executed.
There are only a few of these simple operations, and they revolve around
main memory, the register file, and the arithmetic/logic unit (ALU). The register
file is a small storage device that consists of a collection of word-sized registers,
each with its own unique name. The ALU computes new data and address values.
Here are some examples of the simple operations that the CPU might carry out
at the request of an instruction:

- Load: Copy a byte or a word from main memory into a register, overwriting
the previous contents of the register.
- Store: Copy a byte or a word from a register to a location in main memory,
overwriting the previous contents of that location.
- Operate: Copy the contents of two registers to the ALU, perform an arithmetic
operation on the two words, and store the result in a register, overwriting the
previous contents of that register.
- Jump: : Extract a word from the instruction itself and copy that word into the
program counter (PC), overwriting the previous value of the PC.

We say that a processor appears to be a simple implementation of its instruction set architecture, but in fact modern processors use far more complex
mechanisms to speed up program execution. Thus, we can distinguish the processor’s instruction set architecture, describing the effect of each machine-code
instruction, from its microarchitecture, describing how the processor is actually
implemented. 

**Overview of running the Hello program** 

Initially, the shell program is executing its instructions, waiting for us to type
a command. As we type the characters “./hello” at the keyboard, the shell
program reads each one into a register, and then stores it in memory, as shown in
Figure 1.5.
When we hit the enter key on the keyboard, the shell knows that we have
finished typing the command. The shell then loads the executable hello file by
executing a sequence of instructions that copies the code and data in the hello
object file from disk to main memory. The data include the string of characters
“hello, world\n” that will eventually be printed out.
Using a technique known as direct memory access (DMA, discussed in Chapter 6), 
the data travels directly from disk to main memory, without passing through
the processor. This step is shown in Figure 1.6.
Once the code and data in the hello object file are loaded into memory, the
processor begins executing the machine-language instructions in the hello program’s main routine. These instructions copy the bytes in the “hello, world\n”
string from memory to the register file, and from there to the display device, where
they are displayed on the screen. 

**Caches Matter** 
Because of physical laws, larger storage devices are slower than smaller storage devices. And faster devices are more expensive to build than their slower
counterparts. For example, the disk drive on a typical system might be 1000 times
larger than the main memory, but it might take the processor 10,000,000 times
longer to read a word from disk than from memory.
Similarly, a typical register file stores only a few hundred bytes of information,
as opposed to billions of bytes in the main memory. However, the processor can
read data from the register file almost 100 times faster than from memory. Even
more troublesome, as semiconductor technology progresses over the years, this
processor-memory gap continues to increase. It is easier and cheaper to make
processors run faster than it is to make main memory run faster.
To deal with the processor-memory gap, system designers include smaller
faster storage devices called cache memories (or simply caches) that serve as
temporary staging areas for information that the processor is likely to need in
the near future. Figure 1.8 shows the cache memories in a typical system. 
An LI cache on the processor chip holds tens of thousands of bytes and can be accessed
nearly as fast as the register file. A larger L2 cache with hundreds of thousands
to millions of bytes is connected to the processor by a special bus. It might take 5
times longer for the process to access the L2 cache than the L1 cache, but this is
still 5 to 10 times faster than accessing the main memory. The L1 and L2 caches are
implemented with a hardware technology known as static random access memory
(SRAM). Newer and more powerful systems even have three levels of cache: L1,
L2, and L3. The idea behind caching is that a system can get the effect of both
a very large memory and a very fast one by exploiting locality, the tendency for
programs to access data and code in localized regions. By setting up caches to hold
data that is likely to be accessed often, we can perform most memory operations
using the fast caches.

This notion of inserting a smaller, faster storage device (e.g., cache memory)
between the processor and a larger slower device (e.g., main memory) turns out
to be a general idea. In fact, the storage devices in every computer system are
organized as a memory hierarchy similar to Figure 1.9. As we move from the top
of the hierarchy to the bottom, the devices become slower, larger, and less costly
per byte. The register file occupies the top level in the hierarchy, which is known
as level 0, or L0. We show three levels of caching L1 to L3, occupying memory
hierarchy levels 1 to 3. Main memory occupies level 4, and so on.

The main idea of a memory hierarchy is that storage at one level serves as a
cache for storage at the next lower level. Thus, the register file is a cache for the
L1 cache. Caches L1 and L2 are caches for L2 and L3, respectively. The L3 cache
is a cache for the main memory, which is a cache for the disk. On some networked
systems with distributed file systems, the local disk serves as a cache for data stored
on the disks of other systems.

**The Operating System manages the hardware**

The operating system has two primary purposes: (1) to protect the hardware
from misuse by runaway applications, and (2) to provide applications with simple
and uniform mechanisms for manipulating complicated and often wildly different
low-level hardware devices. The Operating System achieves both goals via processes, 
virtual memory, and files. Files are abstractions for I/O devices, virtual memory
is an abstraction for both the main memory and disk I/O devices, and processes
are abstractions for the processor, main memory, and I/O devices. 

The operating system keeps track of all the state information that the process
needs in order to run. This state, which is known as the context, includes information such as the current values of the PC, the register file, and the contents
of main memory. At any point in time, a uniprocessor system can only execute
the code for a single process. When the operating system decides to transfer control from the current process to some new process, it performs a context switch
by saving the context of the current process, restoring the context of the new
process, and then passing control to the new process. The new process picks up
exactly where it left off. Figure 1.12 shows the basic idea for our example hello
scenario.
There are two concurrent processes in our example scenario: the shell process
and the hello process. Initially, the shell process is running alone, waiting for input
on the command line. When we ask it to run the hello program, the shell carries 
out our request by invoking a special function known as a system call that passes
control to the operating system. The operating system saves the shell’s context,
creates a new hello process and its context, and then passes control to the new
hello process. After hello terminates, the operating system restores the context
of the shell process and passes control back to it, where it waits for the next
command line input.

**Threads** 
Although we normally think of a process as having a single control flow, in modern
systems a process can actually consist of multiple execution units, called threads,
each running in the context of the process and sharing the same code and global
data. Threads are an increasingly important programming model because of the
requirement for concurrency in network servers, because it is easier to share data
between multiple threads than between multiple processes, and because threads
are typically more efficient than processes. 


**Virtual Memory**
Virtual memory is an abstraction that provides each process with the illusion that it
has exclusive use of the main memory. Each process has the same uniform view of
memory, which is known as its virtual address space. 

The virtual address space seen by each process consists of a number of welldefined areas, each with a specific purpose. 
You will learn more about these areas later in the book, but it will be helpful to look briefly at each, starting with the
lowest addresses and working our way up:
- Program code and data.Code begins at the same fixed address for all processes,
followed by data locations that correspond to global C variables. The code and
data areas are initialized directly from the contents of an executable object file,
in our case the hello executable. 
- Heap. The code and data areas are followed immediately by the run-time heap.
Unlike the code and data areas, which are fixed in size once the process begins
running, the heap expands and contracts dynamically at run time as a result
of calls to C standard library routines such as malloc and free. 
- Shared libraries.Near the middle of the address space is an area that holds the
code and data for shared libraries such as the C standard library and the math
library. The notion of a shared library is a powerful but somewhat difficult
concept. 
- Stack. At the top of the user’s virtual address space is the user stack that
the compiler uses to implement function calls. Like the heap, the user stack
expands and contracts dynamically during the execution of the program. In
particular, each time we call a function, the stack grows. Each time we return
from a function, it contracts. 
- Kernel virtual memory. The kernel is the part of the operating system that is
always resident in memory. The top region of the address space is reserved for
the kernel. Application programs are not allowed to read or write the contents
of this area or to directly call functions defined in the kernel code.

For virtual memory to work, a sophisticated interaction is required between
the hardware and the operating system software, including a hardware translation
of every address generated by the processor. The basic idea is to store the contents 
of a process’s virtual memory on disk, and then use the main memory as a cache
for the disk.

**Files**
A file is a sequence of bytes, nothing more and nothing less. Every I/O device,
including disks, keyboards, displays, and even networks, is modeled as a file. All
input and output in the system is performed by reading and writing files, using a
small set of system calls known as Unix I/O.

This simple and elegant notion of a file is nonetheless very powerful because
it provides applications with a uniform view of all of the varied I/O devices that
might be contained in the system. For example, application programmers who
manipulate the contents of a disk file are blissfully unaware of the specific disk
technology. Further, the same program will run on different systems that use
different disk technologies.


