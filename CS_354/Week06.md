## Virtual Memory

* **Virtual memory**
	* An abstraction that provides each process a simple and consistaent view of memory
* **Virtual Address** (VA)
	* for us VA is 32 bits process generate VA's that must be translated into corresponding physical addresses to be able to access information on the computer 
* **Virtual Address Space** (VAS)
	* Max addres space avaiable for us is 2^32 ~ 4GB

## Three faces of Memory - Hardware
* **Physical memory**
	* is structured as a multi-level hierarchry that ensures that the "Right" data is closest to the CPU
	* Cashe is Fastest smallest and most expensive per byte
	* HDD are slowest largest and least expensive per byte	
* **Physical Address** (PA)
	* Specified by the CPU's architecture
	* VA != Pa size 
	* `gcc -m32`
* **Physical Address Space** (PAS)
	* PAS installed  << max PAS and max VAS
* Memory Hierachry runs silently and automatically
* Memory Pages
	* Memory is divided into fixed-sized blocks called pages
	* 4KB = 4096 bytes
	* Page table maps virtual pages to physcial pages. Ultimately translating VA to PA
	* OS people to make sure this works
* Why do OS use VM?
	* **Efficency**
		* only virtual pages used by a process are mapped to physcial pages
	* **Sharing**
		* Different process virtual pages can map to same physcial pages
	* **Uniformity**
		* every process has same basic memory format - VAS
	* **Security**
		* Every process gets it own VAS process can't interfere with each other
	
## Locality

* Programs with good locality will run faster than those with poor locality
* Why
	* Locality keeps the right data close to CPU built into Hardware, OS, Apps
* **Temporal Locality**
	* Is when a recent accessed memory location is accessed repeatedly in near future
* **Spatial Locality**
	* Is when a recently accessed memory locality is followed by nearby memory locality's being accessed
* Stride access is invserily poportional to spatial locality
	* better Stride means worse locality

```	
int sumArray(int a[], int size, int stride) {
	int sum = 0;
	for (int i = 0; i < size; i += stride)
		sume += a[i];
	return sum;
}

// i, sum, stride, size  -> sptial locality
```

* Instruction Flow
	* Sequencing good spartial locality
	* selection bad
	* repetition
	* call--execute-return bad
* Searching Algorithms 
	* Linear search on array
* Latency 
	* delay due to transit
* block
	* unit of memory transfered between level which is a group of continguious rates in the address space
	
## Cache

is a smaller gaster storage device that acts as a staging area for data stored in a larger slower device

* **Cache Miss**
	* Data not found in this catch so fetch it from next level
	* **Cold miss**
		* cache block is empty
	* **Conflict miss**
		* cache block collide in restritive placement policy cache
	* **capacity miss**
		* cache is too small for the working set
* **Cache Hit**
	* Data is found in this cache
* Placement Policy
	* Anywhere L1
	* Restrictive (singleton)
		* L2 block # % 32

### Designing a Cache - Blocks
* Different blocks will need to map to the same block in cache
* How do we map the entire address space to particular blocks in cache?
	* Let M be number of unique addresses in as M=2^m, where M is number of addres bits = 2^32 (4GB)
* How big is a block?
	* Let B be number bytes per block 
	* 32 bytes per blok
	* IA-32
* Given a specific address, how do we find a particular word in a block
	* log(32) = 5 bit
	* use our b-bbits as a block offset
	* b-bits[1:0] which byte in word
	* b-bits[4:2] which word in block (offset)	
* How many blocks of memory for a 32-bit address space?
	* 2^(m-b) -> 2^(32-5) = 2^27 = 128MBlocks 

> 32KB cache size --- 1K (number of 32 byte blocks)
>
> 256KB cache size --- 8K (number of 32 byte blocks)
>
> 8KB cache size --- 256K (number of 32 byte blocks)

### Set & Rags
* Using a random placement policy makes finding a block in the cache SLOW! SLOW Like Brady
* Why
	* Most linear search the entire cache buy a restritive placement policy you only have to search a small set of locs in the cache.
	* Divide cache into sets to which blocks are uniquely mapped
* How many set in the cahce
	* Let S be number of s bit sets
* Given a specific address, how do we know which set to look in
	* Use next s bits for set number
* How many blocks of memory map to each set for a 32-bit address space with 256 sets?
	* 2^(m-(b+s)) -> 2^(32-(5+10)) = 2^17
* Implications
	* we need to determine which block is in the set
* How do we identify which block is in a set
	* Use remaining bits in address as t bits for a unique block tag

### lines

* A line is one location in the cache that can store one block of memory
* How do you know if a line in the cache is used or not
	* A - valid bit
		* 0 - means cold or invalidated
		* 1 - means has useful information
* How long is a cache given S sets with blocks having B bytes
	* S x B
* Set Selection
	* Extract s bits and index to correct set
* Line matching
	* extract t bis and compare with the set s tag
		* if no math
			* miss! getch from next level and replace with fetched block
		* if matching
			* then check v bit
			* 1: hit! 3 word extarct using some of bits
			* 0: miss! fetch and replace