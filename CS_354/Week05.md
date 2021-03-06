## Coalescing Free Blocks 

* Header goes from "8/1" to "8/0" when free'd
* **Problem:*** False (External) Fragmentation
	* The heap isn't able to satisfy the request due to false fragmentation
	* This is an area of the heap that has that been divided into blocks that are too small for the request
* **Solution:** Coalesce (merge)
	* **Immediate**
		* Merge with adjacent blocks right when the new block is free'd
	* **Delayed**
		* Merge when you need a larger block

## Footers

* Header 
	* block size 10pa (4 ending bits)
* footer (aka Boundary Tag)
	* A copy of the block header in the last word of the block
	* P (bit 1)
		* 0 = Pred is Free
		* 1 = Pred is allocated
* All blocks don't need footers, only free blocks do, so we can determine the block size
* Given a pointer to a payload, how do you find the block footer of the previous block
	* Ptr - 4bytes - 5bytes (but only if current block p == 0

## Explicit Free List
* Allocation is dependent only on numer of (linear time with respects to free blocks) free blocks
* Freeing
	* If coalescing done: with footers then there is no search (constant O(1) )
* If coalescing is NOT done: must search through the free list to find insert location
* What changes are require to link a freed block into the free list above
	1. Next's predessor to freed (If last free block skip)
	2. Freed's successor to next (If last free block to Null)
	3. Previou's successor to freed (If first free block head to freed)
	4. Freed's predessor to prev (If first free block to Null)
* Allocation is dependent only on number of free blocks
	* linear time with respect to free blocks
* Header = block size|0pa
* pred Free Block
* succ Free Block
* Possibly Padding
* Possibly Footer = Header
* Minimum block size = 16 bytes including payload

## Abstraction 

* **Process's View**
	* how memory looks to a running program
* **Hardware's View**
	* how memory looks in the actual machine
	* Caches, storage, types of Ram
* **System's View**
	* how memory looks to the OS
* mapping behind the scenes addresses from a table
* OS enables multiple processes to run concurrently on a single machine
* OS maps virtual views to the physical view
