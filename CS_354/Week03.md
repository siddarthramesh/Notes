# Pointer Basics

* what
	* A Pointer is a variable that stores a memory address typically of another variables
* why
	* Indirect access to same memory from different parts of code
	* Access machine hardware that is memory mapped
	* They are used in C libraries and OS code

`int *ptr;`
 
`int *ptr, ptr2; //only p1 is an int*`

**DANGER: uninitialized - whatever it pattern was in its memory**

```
printf("%p\n", ptr);
printf("%i\n", *ptr);
```

* `&` is *Address of* operator
* `*` means *is a pointer* in a declaration means access pointee elsewhere dereference

`segmentation fault (core dumped)`

**DONT DEREFERENCE NULL POINTERS**

**DONT DEREFERENCE UNINITIALIZED POINTERS**

## 1D array and pointers

* what
	* an array is a continuous, fixed-size block of memory that is accessed through a singe name and has parts called elements that can be individually accessed
* why
	* Store collections of data of the same type with fast access to individual members (much easier than declaring individual vars for the collection)

* **Note:** array name is the address of first element

`*a = 0; //sets first element in array` 		 	

`*(a + 2) = 0; //sets 3rd element in array`

## 2D Arrays and Points to Points

```
int m[2][4] = {{0,1,2,3},{4,5,6,7}};
 for (int i = 0; i < 2; i++) {
	for (int j = 0; j < 4; j++)
	printf("%i", m[i][j]);
 printf("\n");
 }
```

* Ints have 4 bytes
* Memory is viewed as a sequence of bytes
* static arrays are contiguous blocks of memory

## 1D Arrays of the Heap

* What
	* Heap memory is a segment of memory used for dynamic allocation
	* Blocks of Heap can be allocated and freed in any arbitrary order
* Why
	* enables a program to access more memory during execution then what was allocated by the compiler
* How
	* `malloc(size_in_bytes)`
		* Memory ALLOCation
		* function in `<stdlib.h>`
		* reserves block of heap memory of the specified number of bytes and returns a generic pointers to that block (void *)
	* sizeof(operand)
		* Operator that returns the size in bytes for its operand
		* `sizeof(double) (8 bytes)`
		* `sizeof(char) (1 byte)`
		* `sizeof(int) (4 byte)`

```
int *a = malloc(sizeof(int) * 5);

*a = 0;
a[i] = 11;
*(a+2) = 22;
```

* Call Stack Tracing
	* Manually executing code (with functions) in a manner that mimics the machine
	* Each function call gets a box (activation records/in which is stored local variables stack ) ( including parameters)
* Argument
	* AKA Actual parameters value info to be shared that's listed in the function call
* Parameter
	* AKA formal parameter var location where the function stores the arguments parameter variable are declared in function header
* Pass/Call-by-value
	* Argument is copied into its parameter changing the parameter doesn't change its argument, used for sharing basic types
* Pass/Call-by-reference
	* sort of like passing references in java but it requires more syntax
	* Address of argument is copied into its parameter which is a pointer changing the parameters pointee does change its argument
	* used for sharing complex types like arrays
	* used to allow multiple arguments to be changed by a function
* What
	*Information entered at the command prompt
	* ```gcc -Wall -Werror -m32 -std=gnu99 myProg.c```
* why
	* Enables a program to receive arguments and options/flags
* Argc
	* `arg` count of whitespace separated CLAs
	* `argv` ARG values in an array of char ptrs where each points to c style string
* stdin, stdout, stderr
	* Predefined file pointers
	* `print("test") === fprint(stdout, "test");`
	* `FILE *fopen();`
		* If NULL is returned meas don't have access
* Exit 0 == success
* Exit 1 or any non zero == not successful

## Program execution

* Programs exe read from the ".exe" by "exec"
1. Program Code [text]
2. Initialized Data [data]
	* Initialized to zero by "exec"
3. Uninitialized Data [bss (block started by symbol)]
4. Heap
5. Stack
	* Memory Mapped Segment (space between Heap and Stack)
	* Used in Linux for dynamic libraries, file i/o, large heap allocations

## Memory Layout

1. Text Segment
	* Typically read only
	* Typically sharable
2. Initialized Data Segment
	* Read-Only area contains: constants and literals
	* Read-Write area contains: globals, extern, and static variables
3. Uninitialized Data Segment
	* Contains: globals, externs, and static variables
4. Heap
	* Lifetime: Programmer managed from malloc/calloc/realloc to free
	* Contains: Dynamic memory allocations
5. Stack
	* Lifetime: from declaration to end of its scope
	* Contains: Stack frames with non-static local variables (others - later)
	* Last In First Out: When a function is called a new stack frame is added to "top" of stack, and it is removed when function returns
	* Stack Frame: Set of values that are pushed onto the stack for one function call
		* Stack's top ("bottom of the stack")
		* Command line arguments
		* Environmental Variables

* Arrays, structs, pointers can "live" in any of the data segments
* Pointers can store any address including those in the kernel space

### Heap Terms

* Dynamic Memory:
	* Memory allocated during run time to satisfy run-time memory needs
* Why not just static it before?
	* Wastes memory - likely too much than typically needed
	* Also it could still be too little
* **Heap** 
	* A collection of various sized memory blocks that are either allocated or free
* **Block** 
	* A contiguous chunk of virtual memory
* **Allocator** 
	* code used to manage blocks of heap memory
* **Payload** 
	* Part of a block that is used by the program requesting the heap memory
* Approaches
	* **Implicit**
		* Java: new/garbage collector
		* dont need to specify size needed or to free when done
	* **Explicit**
		* C: malloc/free
		* C++: new/delete


### brk (unistd.h)
* unistd.h
* header file to access POSIX API
* (Portable OS Interface) <--- POSIX #IEEE
* DIY Heap via Unix Calls
	* brk
	* "program break" pointer to top of heap where heap and unused space is divided
	* End of the program's Data segment
		* 'Data Segment' is the Heap, Initialized Data, and Uninitialized Data
	*`int brk(void *addr)`
		* function that sets the program's data space at the given address
		* returns 0 if success, 1 if a failure... like Brady
		* clears the memory by setting all bytes to 0
	* `void *sbrk(intptr_t incr)`
		* changes the program break by the specified increments in bytes
		* returns old brk on success
		* -1 as type void* on failture (0xFFFF_FFFF_FFFF_FFFF)
* These functions are used by malloc internally (typical)
* Use malloc in your own programs since its more portable