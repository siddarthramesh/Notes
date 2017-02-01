## Implicit Heap Allocator (stdlib.h)--

* Whats in stdlib.h?
	* Conversions: `atof`, `atoi`, `atol`
	* Execution Flow: `exit`, `abort`
	* Math: `abs`
	* Searching: `bsearch`
	* Sorting: `qsort`
	* Random Numbers: `rand`, `srand`

## Heap Memory Allocators

* **malloc**
	* or returns NULL if allocation fails
	* blocks are multiples of 8 (double word aligned)
* **calloc**
	* returns the generic pointers to heap memory
	* or returns NULL if allocation fails
* **realloc**
	* or returns NULL if allocation fails
	* `if (NULL == ptr) return malloc(size);`
	* `if (0 == size) { free(ptr); return NULL; }`
* **free**
	* if ptr is NULL, does nothing
	* if ptr block was already free, you jump into the deep depths of the unknown, undefined
* **NOTE:** If `malloc`, `calloc`, `realloc`returns NULL then exit your program


## Allocator Design
* Goals
	* **Throughput:**
		* more operations per secs
		* free's work is INDEPENDENT of the number of blocks (constant time O(1) )
		* malloc's work is DEPENDENT on the number of blocks (linear time O(n) )
	* **Memory Utilization:**
		* Memory requested/size of heap
		* Typically increasing one decreases the other (can't have both, life sucks, fuck society, man holding me down)
* Requirements of heap allocator
	1. Must handle arbitrary sequences of request
	2. Provide an immediate response
	3. Don't mess with allocated blocks
	4. Allocation use only the heap (mmap --- memory map)
	5. Follow alignment requirements
* Design considerations
* Free block organization
* Placement policy
* Splitting free block to create a better fit
* coalescing free blocks to make a larger block

## A Bit of Linux

* `size <file exe>`
* `!g`  -  finds command that started with 'g'
* *ctrl+c* to kill...Kill everything
* *ctrl+z* to suspend
* `bg`  -  puts in background, noted with '&' symbol (see with ls)
* `fg`  -  brings back (fg = foreground, bg = background)
* `a.out&`  -  will start in background and show process ID
* `jobs`  -  shows jobs running
* `ps`  -  shows processes
	* "bash" is name of command shell on linux
* `kill <processID>`  -  kills certain process
* `cat /proc/<processID>/maps`   -  shows virtual memory mapping
* `cat /proc/self/maps`   -  shows virtual memory mapping of the process "cat"

## Simple view of Heap
* double word aligned blocks: block sizes must be a multiple of 8
* Call `p1 = NULL;` after `free(p1);`
* **Internal Fragmentation:**
	* Memory in a block that is used for other things than storing the payload (ex: padding)
	* External Fragmentation:
	* Free blocks that aren't being used because of their size
* **Free Block Organization**
	* How does allocator know how big each block is?
	* p1, p2, p3, p4... are in the user's program
	* The allocator knows nothing...for now...but seriously **NOTHING**
* **Explict Free List:**
	* A structured is used to store addresses of just free blocks
	* **-** need to set aside space to store the list of free blocks pointers and block size
	* **+** Alloc needs only search through free blocks rather than entire heap
* **Implicit Free List:**
	* No separate structure block size status (alloc/free) kept as a header to each block
	* **+** code simpler, don't need a list of pointers
	* **-** Alloc needs to search through entire heap

## Implicit Free List

* Each block maintains a header storing the block size and status (alloc/free)
* status bit - a (bit 0)
	* 1 means allocated
	* 0 means free
* malloc returns pointer of the payload
* **Placement Policies:**
	* The algorithm used to find the free block to satisfy the heap request
	* First Fist (FF):
		* Start from front first block that's big enough is selected
		* **+** Doesn't splinter entire heap - leaves larger block at rear
		* **-** Must search through splinters at front for larger request
		* **-** Might result in fragmentation
	* Next Fist (NF):
		* Start from last (time) alloc'd block, next block that's big enough is selected
		* **+** faster than FF not always stepping through splinters at front
		* **-** Memory utilization is typically poorer
	* Best Fist (BF):
		* Start from front, select free block that is closest to desired size
		* **+** Best memory utilization
		* **-** Slow performance in worst case most look at all free blocks to determine which is closest to it

## Free Block - Too much/too little

* Entire block is used
	* **+** Fast/Easiest
	* **-** Internal fragmentation
* Split the block into smaller blocks
	* **+** Memory utilization is better
	* **-** Some what slower
	* first part - Allocated
	* last part - free  (size >= min block size)
* What to do if no space left?
* Ask kernel for more heap `brk()`
* return null - last resort

## how to make easy to toggle comments
```
/*
Code
//*/
```