## Factorial Example

```
int factorial(int n) {
						// assume n is in %edx
	int result = 1; 	// movl $1, %eax
	
	do {				// .L1: 
		result *= n;	// imull %edx %eax
		n = n -1;   	// subl $1, %edx
	} while (n > 1); 	// cmpl $1, %edx
						// jg .L1	
	return result;		// %eax is used for return
}
```

## Conditional Moves

* What?
	* added in mid 1990's to pentium pro
	* copies value from S (register or memory) to R (register) given a condition is true
	* S and R can be 16 or 32 bits
	* don't use operand suffic w or l instead R name implies size
* Why?
	* results in more efficient use of the ALU by keping its pipeline busy
* Example?
	* C: `return (index + 1 == size) ? p : index + 1;`
	* GOTO Form: `if (index + 1 == size) goto FALSE;`
* Assembly: Assume %eax return val, %ecx index, %edx size	

	```
	movel $0, %eax
	incl %ecx
	cmpl %edx, %ecx
	cmovne %ecx, %eax
	```
	
## CALL STACK

```
int inc(int index, int size) { 
	int incindex = index + 1;
	if (incindex == size) return 0;
	return incindex;
}

int dequeue(int *queue, int *front,
					int rear, int *numitems, int size) { 
	if (*numitem == 0) return -1; 
	int dqitem = queue[*front]; 
	*front = inc(*front, size); 
	*numitems -= 1; 
	return dqitem;
}

int main(void) { 
	int queue[5] = {11,22,33};  // {11,22,33,0,0} f=0 q=2 n=3
	int front = 0; 
	int rear = 3; 
	int numitems = 3; 
	int qitem = dequeue(queue, &front, rear, &numitems, 5);
	...
}
```

## Stack Frame

* (AKA Allocation record)
* A block of stack memeory used for a single function call typically size is a multiple of 16 bytes for alignment 
* Parameter Passing
	* Space for params is not in the callee instead its in the caller's frame
	* %ebp Frame ptr aka stack 
	* %ebp + 4 (skip the return address) + 4 to get to argl
	* %ebp + 4 + N*4
* Local Variables
	* Stack allocation is used if
		* Not enough regs
		* Locals variables are too big - arrays or structs composites
		* Code uses & "Address of" operator with local variable so it must have a memory address
* **Instructions**
	* call label 
	* call *Operand
		* Works like jmp but it calls a function
	* leave 
		* Prerades the stack for return
	* ret
		* Jumps back to caller using the return address
* **call**
	1 Push return address on to the stack
		* return address is address of instruction immediately after the call
	2 Jumps to start of called function
* **leave**
	1 Deallocate the callee's frame 
		* `movl %ebp, %esp`
	2 restore saved callers %ebp
		* and set %esp to top of caller's frame
		* `popl %ebp`
* **ret**
	1 pops return address of the stack into %eip
	2 jumps to that address
		
## Register Usage Conventions

* Return Value
	* use %eax for return value
* Frame/Stack Base Point %ebp
	* Callee uses to access 
		* its argumets 
			* positive (+) Offset from esp
		* locals
			* negative (-) Offset from esp
* Stack Pointer %esp
	* Caller uses to set up argumets
		* movl {arg}, D(%esp) [<1st> 0] [<2nd> 4] [<3rd> 8], etc
		* save return address call to push PC [<1st> 4] [<2nd> 8] [<3rd> 12]
	* Callee uses to restore return address return pop PC
		* Save/restore frame pointer pushl/popl
* Local Variables Allocations
	1. use a register 
		* + Speed
		* 1,2,or 4 bytes
	2. use function stack frame
		* + Size
	* Potentential problem with using registers
		* registers are a shared resource
		* caller and callee must have consistent approuch for sharing registers
		* otherwise callee might overwrite something in a register the caller needed
* Saving Registers
	* Caller-save-convention 
		* Caller must save before calling a function
		* `eax, %ecx, %edx`
	* Callee-save-convention
		* Callee must save before using the register
		* `%ebx, %esi, %edi`

## Recursion

* Keypoint: Infinite recursion causes stack overflow because each recursive call consumes a bit of the stack

## Arrays in C
* what happens with the C code
	1. A contiguous region of the stack of L*N bytes is allowed, but its not initialized 
	2. Gives an identifier 'A' which is associated with the address of the element at indeo 0
		* 'A' can be used 'like' a pointer, but it cannot be changed to a new address
* Recall Array indexing
	* A[i] == Xa + L*i 
		* address of element i where Xa is the starting address of A
	* if C is a char array, I is an int array, s is a short int array, and D is a double array
	
|C code | element size | total size | address of ith element|
|:---:|:---:|:---:|:---:|
|I[11]	|4| 44	|Xi  + 4;|
|C[7]	|1| 7	|Xc  + 1;|
|D[11]	|8| 88	|Xd  + 8;|
|S[43]	|2| 84	|Xs  + 2;|
|*C[13]	|4| 52	|Xc  + 4;|
|\*\*I[11]|4| 44	|Xi  + 4;|
|\*D[7]	|4| 28	|Xd  + 4;|
	 
## Arrays in Assembly 

* Arrays start from stack top (0x00000000)
* Accessing Arrays on stack	
	* IA-32 Addressing modes are designed to make array access simple
	* if start address in %edx data and index is in %ecx count
		* `movl (%edx, %ecx, 4), %eax`    =>   M[Xa + 4*i] === *(A + i) => c address arithmetic auto scales
* Array start address is in %edx and the index i is in %ecx
	
	| C code   | type | assembly to move value into accumulative register | 
	|:---:|:---:|:---:|
	|	I	   | int*		|movl %edx, %eax    		  	  => Xi|
	|   I[0]   |  int		|	movl (%edx), %eax  		  	  => M[Xi]|
	|   *I     |  int		|	movl (%edx), %eax         	  => M[Xi]|
	|   I[i]   |  int		|	movl (%edx, %ecx,4), %eax 	  => M[Xi + 4*i]|
	|   &I[2]  |  int*		|leal 8(%edx), %eax 		 	  => Xi + 4*2|
	|   I+i-1  |  int*		| |
	| *(I+i-3) |  int		|	movl -12(%edx, %ecx, 4), %eax => M[Xi + 4*i - 4*3]|
	|   S[3]   | short		| |
	|   S+1    | short*		| |
	|  &S[i]	|   short*	| |
	| S[4*i+1] | short		|movw 2(%edx, %ecx, 8), %eax   => M[Xi, 2*4*i + 2*1]|
	|  S+i-5   | short*		| |