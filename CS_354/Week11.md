## Hacking Code

* How?
* Get code onto computer
	* 	it exploits some vulnerability in the system through Program's input
	* 	contains encodings of machine instructions
	* 	contains shell code to access OS functions
* Cause the exploit code to run
	* 	overflow input buffer to overwrite the return address to point to our exploit code
* Cover tracks
	* fix stack to allow the program to appear to end normally
* What can the hacker do?
	* Discover and access your files
	* Start more sophisticated exploits
		* (something unintentionally gain access)
	* share files
	* keyloggers - watch for passwords
	* use your computers for other tasks (DDOS)
	* YES! time to get my Slow Loris game on!!!
	
## Buffer Overflow

**SAVE TIME**: Watch [This video](https://www.youtube.com/watch?v=1S0aBV-Waeo), best explaination ever... also Dr. Mike Pound is TEH BEST!!!"

* What happens when you execute the code?
	* Best case: seg fault - know something is wrong 
	* Worst case: more likely it paints something lower in call stack
	* \* C does NOT check that array accesses are within the bounds of the array
* Example

```
void echo(){
	char bufr[8];
	gets(bufr);
	puts(buft);

}
```

* Gets allows the buffer to be overrun
	* [0-7] no problem
	* [8-11] corrupting saved %ebx
	* [12-15] corrupting saved %ebp
	* [16-19] corrupting return address - which is a security vulnerability
* Buffer overflow is/were a common security vulnerability

## Stack Smashing Worm

* What?
* Worm
	* Is alive and can run by istelf
	* Propagate itself onto other machines
* SSW exploits buffer overflow on stack
* Morris Worm
	* First internet worm on November of 1988
	* Robert Tappan Morris was the handsome and sexy Cornell grad student
	* used "gets" buffer overflow on UNIX "finger" service
	* Problem with predictability
		* It makes computers easier to exploit
		1. Address space layout
			* You can figure out where things are at to exploit them
		2. Executability of memory areas
			* you can determine where to put your exploit coode.
		3. Stack layout	
			* You can corrupt the execution of a process.
		4. System calling procedure - later talk about
		5. Library function code 
		6. OS code
		Can search for code vulnerability for #5/6 causes its open

## Solution

* Today's 64-bit computer systems with ASLR and limited executable areas makes buffer overflow attacks far more difficult
1. Prgrogrammer 
	* Use programming languages that aren't vulnerability to buffer overflow 
2. Prgrogrammer
	* Use safe libaries and safe code
	* use fgets instead of get
	* use strlcat instead of strcat
	* use strlcpy instead of strcpy
3. OS
	* Address space layout randomization (ASLR)
	* Randomizes starting points of stack/heap/live functions so you can't predict exact address
	* First presented in Vista....good job Microsoft!
4. Compiler
	* Add stack protector which is a sentinel value placed before the return address.
	* If protector changes - return addess likely corrupted so seg fault
5. OS/Architecture
	* Limits executable areas can't run code in stack or heap