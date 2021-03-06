# X86 Assembly 

* `b` == byte 8 bits
* `w` == word 16 bits
* `l` == long (double word)32 bits

* What is wrong with the data format in the instructions below?
	* `movb $0xF (%bl)`
	 	* must use 32bit regs for memory access
	* `movl %ax (%esp)`
	 	* size must match
	 	* movl = 32, %ax = 16
	* `movw (%eax), 4(%esp)`
	 	* S & D cannot both be memory accesses
	* `movb %ah, %sh`
	 	* There is no register %sh
	* `movl %eax, $0x123`
		* destination cannot be an immediate (constant)
	* `movl %eax, %dx`
	 	* size mismatch
	* `movb %si, 8(%ebp)`
		* size mismatch

###	Arithmetic Operations

* Load Effective Address
	* `leal S, D`
		* D <- %s D gets the eff address of s
		* S must be memeory access
		* M[S] is not accessed
		* Doesn't change the condition/code regs
	* LEAL vs MOV
		* Not exactly equivlent
		* a differnt leal doesn't change %ebx where %bl does change %ebx
	

### Condition Codes

* Flow Control
	* Programs need to jump to other locationsin code
	* Based on results of the prior instr (selection or repetition)
	* as a result of a function call
* Condition code
	* 1-but registers
	* store the conditions of the most recent ALU instruction
* **CF** - Carry Flag
	* unsigned overflow
	
		> 1000 + 1000 => 1
		>
		> 0111 + 0001 => 0
		>
		> 0000 - 0001 => 1
		>
		> 1000 - 0001 => 0
* **ZF** - Zero Flag
	* Result is zero
* **SF** - Sign Flag
	* Result is negative
	* unsigned always 0
	* signed MSB is 1
* **OF** - Overflow Flag
	* 2's compliment overflow 4-bit signed ints [-7:8]
	
		> 0111 + 0001 => 1 
		>
		> 1000 + 0001 => 0 
		>
		> 1000 + 1000 => 1 
	
### Comparison and Test

* What	
	* Used to set the CC regs only
* How 
	* `CMP S2, S1`
		* cmpb, cmpw, cmpl
		* condition code for => S1 - S2
	* `TEST S2, S1`
		* testb,testw,testl
		* condition code for => S1 & S2
		
### Accessing Condition Codes
* What
	* Set class of instruction
	* Conditional jumps
	* Conditional moves
* How
	* set a byte or memory location to 0 or 1 based on Condition Code from previous ALU instructions
		* sete, setne, sets, setns
		* Unsigned -> setb, setbe, seta, setae
		* Signed -> setl, setle, setg, setge
	  
### Jump

* What?
	* Enable execution to go to a different location in the program. Two approaches
		* **Unconditional**: always jumps to target address
		* How
			* direct jump
				* target address is encoded in the instructions
				* `jmp Label`
			* indirect jump
				* target address is in a 32bit reg or 32bit memeory location
				* jmp *Operand
					* `jmp *%eax` 
				* uses %eax's value as target address
					* `jmp *(%eax)`
				* uses value in memory locaton specified by the op as target address
		* **Conditional**: may or may not jump depending on the condition of the previous instruction
		* How
			* Like set instruction (also have synonyms)
			* can only be direct

### Encoding Targets

* What?
	* jump instruction need to say where to jemp (target address)
	* two command approaches:
		* **absolute**: jump to specific address
		* How?
			* Jump instruction includes a 32bit target address
		* **relative**: jump a distance away from PC
		* How?
			* Jump instruction include a 2's compliment distance thats difference of the address of instruction after the jump and target address
			* Distance can be 1,2,4 bytes
* **NOTE**: CPU architecture x86 updates the PC as a first step before performing the instruction

```
int isMatch(int a, int b) {
	//a is in %ecx
	//b is in %edx
	
	int match = 0; //movl $0, %eax
	if (a == b)  //cmpl %edx, %ecx
				  //jne .L1m
		match = 1  //move $1, %eax
				   
	return match; //.L1 return
}
	Assume jne at 0x080483AE
		move 0x2
```
	
### Converting Loops

* Most compilers base loop assembly code on the do-while goto loop