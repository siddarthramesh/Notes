## Sending/Receiving/Delivering

* **Sending**
	* [Letter put in main]
	* Signals are sent to destination process by kernel execption handler when
		1. a system event occurs
		2. Some process sends a signal (process can signal itself)
* **Receiving**
	* [Letter gets to addressee]
	* Is when destination process is forced by kernel to react to signal by either
		1. Ignoring/blocking itself
		2. Terminating itself
		3. Catching the signal with a signal handler
* **Delivering**
	* [Letter is in the addressee's mailbox]
	* Is when kernel records for destination process that a signal has been sent but kernel has not yet forced the process to react
		* Pending signals
	* Are delivered but not yet received each pending signal can be received at most once
		* bit vector
	* Linux kernel has both a pending and blocked bit vector for process
	* Signals 'n' is pending if set to 1
		* blocking	
	* forces a signal to remain in blocked bit vector until the process unblocks it
	
## Process Groups and IDs

* What?
	* Each process belongs to exactly 1 group
	* Process (PID) and group (PGID)  are identified by positive intergers
* Why?
	* Easier to ID Processes with numbers
	* Linux	
		* PS/jobs
		* Job: represents a process created as a result of evaluation a single command line
* How?
	* setpgid(2) Linux online manual section 2 - system calls
		* `%man 2 setpgid`
	* `#include <unistd.h>`
	* `int setpgid(pid_t pid, pid_t pgid);`
	* sets a process pid to a specific group pgid

## Sending Signal

* What?
* Signals can be sent to a process from command line or from within a process
* Command
	* `Kill(1) `
		* linux manual section 1 - shell commands
		* `%man 1 kill`
		* kill <signal> <pid or job number>
		* `kill -9 71265`
* System Calls
	* `kill(2)`
		* sends a signal to process PID
	
* `#include <signal.h>`
	* int kill(pid_t pid, int sig); returns 0 on succuess, -1 if error
* Alarm Function
	* `alarm(2)`
		* sets an alarm clock for delivery of SIGALRM signal
* `#include <unistd.h>`
	* unsigned int alarm(unsigned int seconds)
		* Returns number of seconds remaining for any previous set alarm
		
## Receiving Signals
* What?
	 * When kernel returns to a process from an exeception handler it will make that process receive any singal that is pending for it.
	* The signal is received by the corresponding handler or if none processed.
	* A default action occured
* Default actions
	* terminate the process 
		* SIGINT (ctrl+c)
	* terminate the process and dump core
		* SIGSEGV core(5)
	* stop the process
		* SIGTSTOP (ctrl+z)
	* continue the process if its currently stopped
		* SIGCONT
	* ignore the singal
		* SIGWNCH (window size change)
1. Code Signal Handlers
	* process can catch some singals and handle them (not SIGKILL)
	* looks like a regular function, but its called asynch
	* `void hdlrSIGALRM() {...}`
2. Register Signal Handlers
	* dont use -> signal(2)
	* use -> sigaction(2)
		* POSIX allows you to examine and change signals actions
		* POSIX is more compatiable with OSs
	
	```
	#include <signals.h>
	int sigaction(int signum, struct sigaction *act, struct sigaction *oldact);
	
	struct sigaction sa;
	sa.sa_handler = hdlrSIGALRM; //function pointer
	sa.sa_flags = 0; 
	if (sigaction(SIGALRM, &sa, NULL) != 0)
		printf("ERROR Setting SIGALRM");
	```

## Issue with Multiple Signals 
* What:
	* Multiple signals can be sent within the same period and even while a signal handler is running
* Issues:
	* Can a signal handler be interrupted by other signals? 
		* yes, but...signals of the same type currently being handled typically wont interrupt in linux.
		* It becomes pending and it must wait until the either the handler returns
		* Other signals don't care and will interrupt the currently handler so you need to block them if you want
		* **NOTE** calling sigemptyset(&sa.sa_mask); blocks signals from interrupting
	* Does the system queue multiple signals of the same type for a process?
		* No...There can be at most one signal delivered per type for standard signals...It is recorded in a bit vector
		* When a signal is received it means it may have been delivered one or more times
		* Multiple signals of different types are received in order from lowest to highest signal number
* Real-time signals
	* Linux has 33 additional signals that are application defined
	* multiple signals of the same type are queued in the order they're received 
	* multiple signals of different types are treated in the same order as standard signals
* Can a system call be interrupted by a signal?
	* yes, for slow system calls
* Slow system calls
	* Have potential of taking a long time (example: read())
	* in some system calls don't resume after being interrupted, instead they immeditaley return without error
	* **NOTE** if you set sa.sa_flags = SA_RESTART will attempt to restart interrupted system calls
	
	
## Forward Declaration

* What?
	* Where you tell the compiler about certain aspects of an identifier before it is fully definded
	* C requires identifiers must be declared before they are used
* Why?
	* compiler can ensure that the identifier exists and is being correctly used
	* large programs can be divided into modules that can be independently compiled
	* Mutural recursion is possible
		* function A --> function B --> function A
* Declaration vs Definition
	* **Declaring**
		* Telling compiler about certain aspects
		* variables: name, type
		* function: prototyoe name, return type, params
	* **Defining**
		* provides the compiler with full details
		* variables: where the memory allocation occurs
		* function: body of how the function works

```		
int i;
int j = 11; //initalizing
// these are delcaring and defining

extern myVar;
//declared and not defined
```
	
## Multifile coding

* what?
	* divides a program into functional units, each having a source file and possibly a header file
* header files
	* contains delcaration (and macro definitions) that you which to share (public)
	* `#include` guard used to prevent #define from being included multiple times
		* without it linker errors!
* source files
	* Cotains difinition of things declared in header file also contains declarton and definitions
	
## Multifile compilation

* Compilation
	* gcc is a compiler driver
		* main.c ---> main.i ---> main.s ---> main.o ---> Executable
	* **Preprocessor**  (.c --> .i)
	* **compiler** (.i --> .s)
	* **assembler** (.s --> .o)
	* **linker** (.o --> exe)
* Compiling all at once
	* `gcc align.c mem.c -o align`
* Compiling separately
	* `gcc -c align.c `
		* sends to preprocessor, compiler, and assembler to get align.o
	* `gcc -c mem.c`
	* `gcc align.o mem.o -o align`
* **NOTE:**Compiling separately is more efficent on large programs if a source file is changed will just recompile that source file
* **Linking**
	* combines the assemblers' .o files into a single file that can be loaded into memory and run
* **Object files**
	* contain binary code and data
* Relocatable object file (ROF) - produced by assembler
* Executable object file (EOF) - produced by linker
* Shared object file (SOF) - produced by assembler

## Makefiles

* What?
	* are text files named "Makefile" that have rules for building a program
* Why?
	* To save yo time
	* only if you play by the rules
* rules

```
<target>:<files target depends on>
	<tab><command for making target>
```

## Static Linking

* What?
	* Generates a fully linked executable
	* vs dynamic linking: 
		* makes a smaller executable where libraries are linked in during load or run time
* How
	* Linkers need a minimal understanding of target machine since they're just combing ROFs into a single EOF
* What issues arise from combining ROFs
	1. addresses of symbols will change as a result of code and data section being combined by linker - Relocation
	2. Symbols must be checked that they have at least one definitions - Resolution
* A programmer can declare globals and functions in their code and then use those things that aren't in the corresponding header file
* How do you truly make them private?
	* declare as static any functions and globals that you want to truly make private
	
## Relocatable object File (ROF)

* what
	* An object module stored on a disk (its a file)
	* And is in a format that can be easily combined by a linker with other ROFs
* Executable and Linkable Format (ELF)
	* Object file formats vary, ELF command in linux
	* HDRS used by linker to parse ROFs
