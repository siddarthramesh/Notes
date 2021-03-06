## Basic Cache Practice

* Given a 32-bit address layout for a memory cache
  * t bits [13] - s bits [13] - b bits [6]
  * tag, index, block offset
* How big are blocks (bytes)
  * B = 2^b = 2^6 = 64 bytes
* How many memory blocks map to the same set
	* 2^t = 2^13 = 2^3 * 2^10= 8K
* If each set has one line, how big is the cache
  * C = S * B = 2^13 * 2^6 = 512K
	
> Given 0x07515E2B
>
> 0000_0111_0101_0001_0101_1110_0010_1011

* What happens when two different memory blocks map to the same set?
  * Conflict miss
* Improvements
  * Alllow more lines per set
  * Let E be the number of lines set
  * (S, E, B, m) Tuple describe cache\
  * E == 1 direct mapped cache
  * E > 1 E-Way set associative cache
	  * E is never > 2^t
*Operation
	* Select set using s bits
	* Check all tag is set simultaneously for a valid match	
* How big is a cache given 10s bits, 5 b bits, and 4 lines per set?
  * C = S * B * E
  * 2^10 * 2^5 * 2^2 = 128K
* **NOTE:** Programmers can't easily exploit replacement policies directly to improve your code's performance
* LRU (least recently used)
  * need trck recency of used
  * log2(E) where E = 4, 2 Recency bits
* LFU (Least frequently used)
* need to track frequency of use

## Fully associative Caches

* Fully associative cache has only one set no s-bits required 
* Is this cache required to have 2^t lines
  * NO! `E = C/B`
  * S = 1;
* Why isn't it possible for `E > C/B`

	```
	E = S*B*E
	E > SBE/B; S=1
	E > BE/B
	E > E
	```

	*Appropriate only for small caches

## Writing to a cache
* **Write Hits**
  * writing to a block that is cached in a line
* **Write Through**
  * Write change to next lower level immediatly 
  * **+** Simple
  * **-** Slow
  * **-** Generates lots of bus traffic
* **Write Back**
  * Write change to next lower level only when the block is evicted
  * **+** Much less bus traffic
  * **-** Need to know if a block in a line changes
  * use a "Dirty Bit" 1 = changed, 0 = not
* **Write Misses**
  * writing to block that isn't cached
* **Write Allocate**
  * Load the memory block into cache then change it
  * **+** If code exhibits locallity 
  * **-** Moving things around again, Bus traffic
* **No Write Allocate**
  * **+** Simpler
  * **+** Faster bypass write buffer
  * **-** Doesn't take advantage of locallity
* Which best exploits locallity?
  * Symetric with read typical for bottom level cache and also OS manages virtual memory

## Cache Performance Metrics
* **Miss Rate**
  * #Misses/#memory reference
  * lower is better
* **Hit Rate**
  * #Hits/#Memory reference - 1 - miss rate
  * Higher better
* **Hit Time**
	* Time to deliver a word to CPU = set selection + Line identifications + word extraction
	* Lower better
	* L1 =~ 3-4 cycles
* **Miss Penalty**
	* Additional time required due to a miss
	* From L2 7-10 cycles
	* From L3 30-40 cycles
	* MM 70-100 cycles

## Cache Parameters and Performance
* Bigger Cache
  * **Hit Rate**
	* Better
  * **Hit Time**
	* Worse
  * **Miss Penalty**
	* No effect
	* There fore caches that needs to be fast are smaller
* Bigger Block
  * **Hit Rate**
	* Better for spartial locallity
	* Worse for Temp locallity
	* Assuming cache size stays same
  * **Hit Time**
	* No effect
  * **Miss Penalty**
	* Worse
	* Lengthens block transfer time 
	* 32-64 byte blocks
* More ELines (associativity)
  * **Hit Rate**
	* Better
	* Fewer conflict misses
  * **Hit Time**
	* Worse
	* Line identifications
  * **Miss Penalty**
	* Worse
	* More time to select victim block	
* Write Strategy
  * Write through is simple
  * write back/write allocate
* Stride affects
  * Stride misses
	* `min(1, (wordSize*k)/B)`
	* B is blockSize, k is stride in wordSize*k
	
## Coding for Caches

* Make the common case fast
  * determine where code typically spends mostof its time and ensures that code runs fast
* Minimize cache misses	
  * Spatial - code your inner loops to minimize stride
  * Temporial - keep your cache working set small enough to fit in the cache
* Memory Blocking
  * Divide application data into approximtally cache sized blocks which can improve your performance by maximizing temporal locallity
* Dependent Varaiable 
  * read throughput MB/s-bits
* Independent Variables
  * `Stride - (Spartial) 1 - 32 double wordSize*k`
  * `Size - (Temporial) 2k - 64MB`

**Memory access isn't characterized by a single value!!!**
