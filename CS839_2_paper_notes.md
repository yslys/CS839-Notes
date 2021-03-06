### Concepts:
+ NVMM
	+ Non-volatile Main Memory
+ Optane DIMM
	+ Intel's Optane DC Persistent Memory Module
	
+ NVDIMM: non-volatile dual in-line memory module
	+ NV(DIMM)
	+ NV: Non-volatile memory.
	+ DIMM: "Dual in-line" identifies the memory as using the DIMM package.
		+ DIMM: dual in-line memory module, aka. RAM stick, comprises a series of dynamic random-access memory integrated circuits, is a type of computer memory that is natively 64 bits, enabling fast data transfer.
	+ NVDIMMs integrate non-volatile NAND flash memory with dynamic random access memory (DRAM) and dedicated backup power on a single memory subsystem.
	+  NVDIMMs improve application performance and system crash recovery time.

+ Memory access granularity
	+ the number of bytes that CPU reads/writes a chunk of data at a time.

+ Memory access boundary
	+ where each of the groups of bytes begins. The groups of bytes are always addressed at even multiples of the granularity - if it's double-byte granularity they start on even addresses, if it's quad-byte granularity they're at multiples of 4.
	+ Good [source](http://www.songho.ca/misc/alignment/dataalign.html).

+ [Processor die](https://superuser.com/questions/324284/what-is-meant-by-the-terms-cpu-core-die-and-package#:~:text=with%20other%20cores.-,Processor%20die,-is%20a%20single), processor package, dual-core processor

+ NUMA
	+ Non-uniform memory access (NUMA) is a computer memory design used in multiprocessing, where the memory access time depends on the memory location relative to the processor.
	+ Under NUMA, a processor can access its own local memory faster than non-local memory (memory local to another processor or memory shared between processors).
	+ A system can starve several processors at the same time, notably because only one processor can access the computer's memory at a time. ([src](https://en.wikipedia.org/wiki/Non-uniform_memory_access#:~:text=Now%20a%20system%20can%20starve%20several%20processors%20at%20the%20same%20time%2C%20notably%20because%20only%20one%20processor%20can%20access%20the%20computer%27s%20memory%20at%20a%20time))
		+ NUMA attempts to address this problem by providing separate memory for each processor, avoiding the performance hit when several processors attempt to address the same memory.
	+ NUMA explanation in Linux Memory Management Documentation: [link](https://www.kernel.org/doc/html/v5.0/vm/numa.html)
		+ [Symmetric multi-processor](https://en.wikipedia.org/wiki/Symmetric_multiprocessing) (SMP)
			+ A shared memory multiprocessor where the cost of accessing a memory location is the same for all processors, i.e., it has uniform access costs when the access actually is to memory. If the location is cached, the access will be faster, but still it is symmetric with respect to processors. (might be a typo on Wikipedia)
		+ NUMA vs SMP ([src](https://www.quora.com/What-is-the-difference-between-SMP-and-NUMA-architectures#:~:text=In%20a%20Symmetric,among%20them%20Asymmetrically.))
			+ SMP: In a Symmetric Multiprocessor, the architectural ???distance??? to any memory location is the same for all processors, i.e. ???symmetric???
			+ NUMA: In a NonUniform Memory Access machine, each processor is ???closer??? to some memory locations than others; i.e. memory is partitioned among them Asymmetrically.

+ Asynchrnous DRAM refresh (ADR): ([src](https://link.springer.com/chapter/10.1007/978-1-4842-4932-1_2#:~:text=Asynchronous%20DRAM%20Refresh,WBINVD%20machine%20instructions.))
	+ ADR is a feature supported on Intel products which flushes the write-protected data buffers and places the DRAM in self-refresh. 
	+ This process is critical during a power loss event or system crash to ensure the data is in a safe and consistent state on persistent memory. 
	+ By default, ADR does not flush the processor caches. 
	+ A platform that supports ADR only includes persistent memory and the memory controller???s write pending queues within the persistence domain. This is the reason data in the CPU caches must be flushed by the application using the CLWB, CLFLUSHOPT, CLFLUSH, non-temporal stores, or WBINVD machine instructions.

+ Hold-up time ([src](https://www.fsp-group.com/en/knowledge-prd-10.html))
	+ Hold up time indicates the length of time a power supply is able to maintain a consistent output voltage at a regular output level, after it is shut down or in a power shortage.

+ Integrated memory controller (iMC)
	+ Support 3 channels each.
	+ Sits within the asynchronous DRAM refresh (ADR) domain.
	+ Maintains read pending queues (RPQ), write pending queues (WPQ).
	+ Write pending queues are within ADR domain.

+ Wear leveling
	+ A technique for prolonging the service life of some kinds of erasable computer storage media, such as flash memory (used in solid-state drives (SSDs) and USB flash drives), and phase-change memory.

+ Bad block ([src](https://www.micron.com/-/media/client/global/documents/products/technical-note/nand-flash/tn2959_bbm_in_nand_flash.pdf))
	+ Bad blocks are blocks that contain one or more invalid bits whose reliability is not guaranteed. Bad blocks may be present when the device is shipped, or may develop during the lifetime of the device.
	+ Devices with bad blocks have the same quality level and the same AC and DC characteristics as devices where all the blocks are valid. 
	+ A bad block does not affect the performance of valid blocks because it is isolated from the bit line and common source line by a select transistor.
	+ Bad block management
		+ Bad block management software, block replacement software, and the error correction code (ECC) software are necessary to manage the error bits in NAND Flash devices.

+ on-DIMM controller (XPController)
	+ Memory accesss to the NVDIMM arrive first at the on-DIMM controller.
	+ It coordinates access to the Optane media.
	+ It performs an internal address translation for wear-leveling and bad-block management.
	+ It maintains an **address indirection table (AIT)** for the translation.
	+ It has a small **write-combining buffer (XPBuffer)** to merge adjacent writes.

+ write-combining buffer (XPBuffer)
	+ All updates that reach the XPBuffer are already persistent because XPBuffer resides within the ADR (Asynchrnous DRAM refresh domain).

+ XPLine
	+ 3D-XPoint physical media access granularity is 256 bytes.
	+ XPController translates smaller requests into larger 256-byte accesses.
	+ The above translation will cause "write amplification" because small stores become read-modify-write operations.

+ Write amplification
	+ An undesirable phenomenon associated with flash memory and solid-state drives (SSDs) where the actual amount of information physically written to the storage media is a multiple of the logical amount intended to be written.
	+ Cause:
		+ Due to the nature of flash memory's operation, data cannot be directly overwritten as it can in a hard disk drive.
		+ When data is first written to an SSD, the cells all start in an erased state so data can be written directly using pages (4KB or 8KB) at a time.
		+ The SSD controller on the SSD, which manages the flash memory and interfaces with the host system, uses a logical-to-physical mapping system known as logical block addressing (LBA) and that is part of the flash translation layer (FTL).

+ Memory fence (Memory barrier)
	+ A type of barrier instruction that causes a central processing unit (CPU) or compiler to enforce an ordering constraint on memory operations issued before and after the barrier instruction. 
	+ This typically means that operations issued prior to the barrier are guaranteed to be performed before operations issued after the barrier.

+ Blocking I/O and Non-blocking I/O
	+ Blocking IO: a given thread cannot do anything more until the IO is fully received (in the case of sockets this wait could be a long time). 
	+ Non-blocking: an IO request is queued straight away and the function returns. The actual IO is then processed at some later point by the kernel.

### Background knowledge:

+ Optane DIMM sits on memory bus, connects to processor's integrated memory controller (iMC).
+ Stores are only persistent once they reach the WPQs.
+ Optane DIMM performs an internal address translation for wear-leveling and bad-block management, and maintains an address indirection table (AIT) for this translation. ([src](https://thememoryguy.com/whats-inside-an-optane-dimm/#:~:text=The%20internal%20error,within%20the%20DRAM.))
	+ The internal error correction, the encryption, and the fact that 3D XPoint Memory wears out and must use wear leveling, all cause the Optane DIMM???s critical timing path to be slower than the critical path in a DRAM DIMM, rendering the Optane DIMM unsuitable for code execution.  This, and the fact that XPoint **writes are slower than its reads**, all help to explain why an Optane DIMM is never used as the only memory in a system: there is always a DRAM alongside the Optane DIMM to provide faster access to a subset of the Optane DIMM???s data that is cached within the DRAM.
+ Memory access path
	+ data -> on-DIMM controller for requests' addresses translation -> access to storage media (3D-Xpoint Media).

+ Cache block size (cache line): sizeof each cache block, 64 Bytes in Optane.
+ Cache size: 4 KB (DRAM) in Optane's memory mode.

+ Two modes of operation: Memory & AppDirect ([src p. 3](https://www.intel.com/content/dam/support/us/en/documents/memory-and-storage/data-center-persistent-mem/Intel-Optane-DC-Persistent-Memory-Quick-Start-Guide.pdf))
	+ Memory mode (volatile)
		+ uses Optane to **expand main memory capacity without persistence (volatile)**.
		+ CPU and OS see Optane DIMM as a larger **volatile** portion of **main memory**.
		+ Combines an Optane DIMM with DRAM DIMM on the same memory channel. DRAM serves as a direct-mapped cache for NVDIMM.
		+ CPU's memory controller manages the cache (DRAM).
	+ App Direct mode (byte addressable persistent memory)
		+ provides persistence, not using DRAM cache.
		+ Optane DIMM appears as a separate, persistent memory device.
		+ Requires an OS or virtualization environment enabled with a persistent memory-aware file system.
		+ Applications can be modified to access App Direct capacity with direct load/store mechanisms using a persistent memory aware file system. This completely bypasses the kernel and provides the shortest code path to the persistent memory.
	+ Two operating modes may be configured to run concurrently.
	+ In both modes:
		+ Optane memory can be (optionally) interleaved across channels and DIMMs

+ ISA support (in App Direct mode)
	+ Applications and fily systems can access the Optane DIMMs with CPU instructions - to control store ordering.
	+ Applications access Optane DIMM's content using store instructions (persistent store).
	+ Problem:
		+ Cache hierarchy can reorder stores, making recovery after a crash challenging.
	+ https://blog.csdn.net/qq_41961459/article/details/108439637 best source
	+ Solution:
		+ Use instructions to flush cache lines back to memory.
		+ clflush, clflushopt, clwb, ntstore.
		+ Since all the above instructions are non-blocking, the program must issue an ```sfence``` to ensure a previous cache flush, cache write back, or non-temporal store is complete and persistent.	

+ Write back cache & Write through cache
	+ Write through cache
		+ Data is simultaneously updated to cache and memory.
		+ Simpler and more reliable.
		+ Used when there are no frequent writes to the cache
		+ Problem: not taking the advantage of having a cache in write operation, (as the whole point of using a cache was to avoid multiple access to the main memory).
	+ Write back cache (write deferred)
		+ The data is updated only in the cache and updated into the memory at a later time. 
		+ Data is updated in the memory only when the cache line is ready to be replaced. 
		+ (cache line replacement is done using Belady???s Anomaly, Least Recently Used Algorithm, FIFO, LIFO, and others depending on the application). 
	+ Dirty bit
		+ Each Block in the cache needs a bit to indicate if the data present in the cache was modified(Dirty) or not modified(Clean). If it is clean there is no need to write it into the memory. 
		+ It is designed to reduce write operation to a memory. 
		+ If Cache fails or if the System fails or power outages the modified data will be lost. Because it???s nearly impossible to restore data from cache if lost. 

+ Write allocation & Write around
	+ If write occurs to a location that is not present in the Cache (Write Miss), we use two options, Write Allocation and Write Around.
	+ Write allocation
		+ Data is loaded from the memory into cache and then updated.
		+ Write allocation works with both Write back and Write through. 
		+ It is generally used with Write Back because it is unnecessary to bring data from the memory to cache and then updating the data in both cache and main memory. Thus Write Through is often used with No write Allocate.
	+ Write around
		+ Data is directly written/updated to the main memory without disturbing the cache. 
		+ It is better to use this when the data is not immediately used again.

+ What those instructions are:
	+ clflush: (cache line flush) flush cachelines back to memory.
	+ clflushopt: (clflush + optimized) clflush with weaker ordering constraints.
	+ clwb: (cache line write back) write back (but not evict) cache lines.
	+ ntstore: (non-temporal stores) software can use ntstore to bypass the cache hierarchy and write directly to memory, meaning that the data being stored is not going to be read again soon (i.e., no "temporal locality"). So there is no benefit to keeping the data in the processor's cache(s). 

+ ```pmem``` namespace
	+ Linux manages persistent memory by creating ```pmem``` namespaces over a contiguous span of physical memory.
	+ 

### Findings:
+ Optane DIMM has **lower latency, higher read bandwidth**, presents a memory address-based interface instead of a block-based NVMe interface.



### Summary: your sense of what the paper is about.
### Contributions: what you think was new, interesting, or what you got out of this paper.
### Strengths: what you think the paper did well.
### Weaknesses: what you think the paper gave bad assumptions, or forgot about some important cases.
### Questions: what you think is the hardest part to understand.
+ Is Optane connected to PCIe?
	+ PCIe is an I/O bus. Optane DIMM (memory device) is connected to the memory bus. 
	+ Optane is a storage technology, and you can package it as a DIMM - a memory device, or you can package it as an SSD.
	+ Optane memory DIMM is connected to memory bus, Optane SSDs are I/O devices, and connect to PCIe bus.
+ What is the relationship between SSD and memory?
	+ SSD is slower than memory.
+ Why Optane DIMMs are interleaved?
	+ The reason is why people build RAID storage devices. Interleaving is really RAID0.
