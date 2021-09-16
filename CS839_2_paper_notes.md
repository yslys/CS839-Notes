### Concepts:
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
			+ SMP: In a Symmetric Multiprocessor, the architectural “distance” to any memory location is the same for all processors, i.e. “symmetric”
			+ NUMA: In a NonUniform Memory Access machine, each processor is “closer” to some memory locations than others; i.e. memory is partitioned among them Asymmetrically.


### Background knowledge:

+ Optane DIMM sits on memory bus, connects to processor's integrated memory controller (iMC).

### Findings:
+ Optane DIMM has **lower latency, higher read bandwidth**, presents a memory address-based interface instead of a block-based NVMe interface.



### Summary: your sense of what the paper is about.
### Contributions: what you think was new, interesting, or what you got out of this paper.
### Strengths: what you think the paper did well.
### Weaknesses: what you think the paper gave bad assumptions, or forgot about some important cases.
### Questions: what you think is the hardest part to understand.
+ Is Optane connected to PCIe?
