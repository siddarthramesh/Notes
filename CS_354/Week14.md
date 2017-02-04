## Linker Symbol table

* What?
	* Built bt assembler using symbols exported by the compiler (in .s file)
	* an array of ELF symbol structures
* `int name`
	* byte offset in the .strtab containing a NULL terminated string
	* name itself is in the string table
* `int value`
	* Symbols address 
	* ROF - offset from beginning of section
	* EOF - Absolute (virtual) address
* `int size`
	* number of bytes
* `char type`: 4
	* data (object), function 
	* binding: 4
		* local or Global
* `char section`
	* index into section header table for this symbol
		* `.text` 1
		* `.rodata` 2
* pseduo sections:
	* `ABS` 
		* Symbols that arent relocated
	* `UND` (Undefined)
		* Symbols that are referenced in this ROF, but defined in another ROF
	* `COM` (Common)
		* Uninitialized data (.bss)
		* value
			* Gives alignment requirement
		* size
			* Minimum size
			
**readelf** - used to looking at object files (.symtab)

## Symbol Resoultion

* **Compiler**
	* Ensures local symbols have exactly one definition for their modules, and static locals have unique names for the linker
	* Assumes global symbols that aren't definded will be definded in some other module and leaves it to the linker to resolve them
* **Linker**
	* Ensures global symbols have exactly one definition for all modules
	* if no definition -> linker error
	* if multiple definition uses Strong and weak
* **Strong and Weak Symbols**
	* strong:
	* 	function or initialized global variable
	* weak:
	* 	uninitialized global variable
* Rules for resolving globals
	1. Multiple strong definitions are not allowed (linker error)
	2. Choose strong over weak 
	3. Pick any weak of those given (only if no strong)
* `gcc --fno-common`

## Relocation

* What
	* Merges sections of ROFs and then assigned addresses to each symbol
* How
	1. Merge the same sections of ROFs into an aggregate section for each section type
	2. assigned run-time addressed to each aggregate section and each symbol
	3. Update symbol references within .text and .data sections using the info in the relocation sections
		* `.rel.text` and `.rel.data` (linker uses these to do relocation check)
* Creates linera address space when combining to executable

## Excutable Object File

* What
	* Is an object modules on disk that is the binary file produced by the linker
* Format
	* similar to ROFs
* ELF Header
	* Entry Point
* Segment Header Table
	* Info for segments to be loaded into memory for execution
	* For each segment offset in file
	* virtual/physical addresses
	* alignments (4k)
	* size in file
	* size in memory
	* run-time permissions
* Code Segments
	* 	`[.init, .text, .rodata ]` read only
	* 	`[.data, .bss]` data segments, read/write
	* 	`[.symtab, .debug, .line, .strtab]`
* Why no relocation section?
	* `.rel.text` and `.rel.data`
	* EOF is fully linked so they are not needed but can be added with compiler option
* Why is it likely for the data segment that its file size is smaller than its memory size?
	* In the EOF .bss is size 0, but in memory it needs to be the appropriate size for the uninitialized variables
	
## Loader

* What
	* Is kernel code that starts a program executing
	* Can invoke the loader using system call execve
* Loading
	1. copies code and data segments of EOF into memory
	2. Starts program executing by jumping to its entry point
* Execution
	* `% ./a.out`
	1. Shell forks a child process
	2. Child process invokes the loader with execve
	3. Loader creates the memory image
		* Deletes virtual memory segments
		* creates new code data heap and stack segments
		* sets heap and stack to zero
		* EOF code and data segments are mapped into page size chunks
		* actual code/data is loaded as a result of a page fault		
	4. Loader jumps to _start address
		* `call __libc_init_first`
			* Make sure correct libraries are called
		* `call _init`
			* getting other aspects ready
		* `call atexit`
			* registers the functions that need to be called to correctly clean program at exit
		* `call main`
			* calls the main function (where programs execute)
		* `call _exit`