### Introduction

Persistent memory - a new technology, also called non-volatile memory, or storage class memory.

+ When refering to the real physical device (hardware), we use non-volatile memory, or storage class memory.
+ Persistent memory (software) is the programming abstraction for memory that is persistent.

Features:
+ Persistent: 
	+ When you store data into persistent memory, it stays there even when your power goes off or system crashes.
+ Having a Memory-like interface: 
	+ When talking to a hard disk, the way you do it is to do some memory-mapped I/O where you submit a request, to do an operation to a queue, the device processes that request and then provides a response - either acknowledging you wrote some data, or returning the data that you want. So you do not directly do a load instruction against the location on disk, instead, you tell the OS to fetch the block on disk, copy into memory and then in memory you do a load instruction against memory. 
	+ With persistent memory, we have a storage technology where you can have persistent data that can survive from crashes, and you can do direct load/store against it. So you do not have to go to the kernel to do an I/O operation to copy things into memory.

+ Short access time:
	+ Almost as fast as DRAM (although still slower than DRAM).
	+ Latency(fetch from hard disk) = milliseconds (4~5 million cycles on 1 GHz processor)
	+ Latency(fetch from SSD) = microseconds (50~100 thousand cycles on 1 GHz processor)
	+ Latency(fetch from DRAM) = 70~100 nanoseconds
	+ Latency(fetch from NVM) = 150~300 nanoseconds
+ Asymmetric property:
	+ Write is 3 or 4 times slower than read

How persistent memory is attached to the system?

The way you attach persistent memory to the system is you have a processor, and a memory controller on the processor which talks to the memory. The memory controller can both talk to DRAM (the volatile data that goes away when no power or crash), and the non-volatile memory. i.e. you can have a mix of volatile memory and non-volatile memory in our system. The interesting thing is to determine what to put on each device.

Currently there is one dominant form of NVM available called 3D XPoint (cross point). Internally it has a 3D structure, it also has wires connected at cross points.

### The Era of persistent memory

+ **Potential change in architecture:**

	+ This is potentially the major change of architecture. If we go back to the first hard drive or the first virtual memory in 1963 or so, we had volatile memory, we have hard disk (persistent storage that was much slower) - we had to move things back and forth, we could do swapping, we could do file systems, and that have not changed. Now we have persistent memory, which could potentially change a lot of things.
	+ For instance, how do you want to change computer architectures? We might want features that makes it easier to manage data in persistence. In particular, the **central problem** is: suppose we are building a data structure, and we want to add a new node here, if we have just allocated the node and filled in the data, then crashed before linking it in to the tree, then there is a potential that we might lose the data here, and there would be a memory leak. One solution is to fill in the pointer before we fill in the data, but if system crashes before filling in the data, we will result in a node in a tree that has garbage data in it, which is in-consistent. So we need to worry about how do we make sure if we crash then the system is able to come back in a consistent state. 
	+ Normally in regular programs, we do not need to worry about the program being interrupted in the middle of updating a data structure and then coming back because if we crash in the middle, we lose the whole data structure. Therefore, NVM has the potential to change the architecture. 

+ Potential change in OS:
	+ We can think about OS how it changes because there is a new kind of memory to manage. And we have to think how do we allocate, how to virtualize all these things. 

+ Potential change in file systems:
	+ File systems normally deal with durable data through files and directories that are stored on SSDs or hard disks. We now need to think how we could use NVM in a file system. We might use a little bit of it to make file system faster; we might put all of our data into it; we might want a different design. 

+ Potential change in DBMS:
	+ Databases might want to start store data in NVM using hash table, which is faster than storing into hard disk.

+ Potential change in applications: 
	+ General applications might think about putting data structures in persistent memory, which is more persistent, and more faster to access than in hard disk which are accessed through complex database API. 

+ Potential change in programming languages:
	+ In programming languages, since we now have a consistency problem, we might also have a problem where we have some volatile data that does not survive from failure, but you might accidently put it into the data structure in persistent memory. So if crashed and recovered, the data structure which stores something in volatile memory would be lost. Programming languages can help us prevent this problem by forcing that you can never have data in persistent memory pointing to data in volatile memory.

+ Potential change in data structures:
	+ We might want different types of data structures for NVM.

### Paper reading requirements
+ Summary: your sense of what the paper is about.
+ Contributions: what you think was new, interesting, or what you got out of this paper.
+ Strengths: what you think the paper did well.
+ Weaknesses: what you think the paper gave bad assumptions, or forgot about some important cases.
+ Questions: what you think is the hardest part to understand.

