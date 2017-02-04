## Flow of Execution

* What
* Control Trasfer
	* Transition from one intruction to another
* Control Flow
	* Sequence of conition trasfers
* What control flow results in a smooth flow of execution
	* Sequencing
* What control flow results in abrupt changes in the flow of execution
	* Sequencing, repetition, cal-exec-ret
* Exceptional control flow
	* Execution must be able to react to events that are urgent/anomanous/exceptional
	* Require special processing
* Event
	* Change in the processor's state
	* That may or may not be related to the current instruction
	* Processor state
	* Bits and signals inside the processor
* Example
	* program
		* Request I/O from OS (printf)
			* Share info via a program's concurrent
			* Software exception handling processes
	* OS
		* Request I/O from hardware
		* Deals with other hardware (request from processes)
	* Hardware
		* Device does I/O task and then interrupts to say its done
	
## Exception

* What
	* Is a nonlocal jump that abruptly changes the flow of execution as a result of some event	
	* Sidesteps the usual stack flow of execution
	* Can be from both hardware and software
* Whats the difference between a synchronous vs an asychronous exception
* Asynch
	* **Interrupts**
		* Events that is external to processes
		* Can happen at any time
* Synch
	* **Execption**
		* Events results from the current instruction
		* Traps, Faults, Aborts
		
## Exception Handling

* Exception Table
	* On system boot OS allocates a jump table for exception handling
* Indirect Function Call
	* ETBR (Exception Table Base Register) == array base address
	* Exception # == index 
	* Address of exception handler == Element values

1. Push on to a stack the return address.
	* Address might be Icurr or Inext
2 Push on to a stack the processor's state
	* To enable restarting the current process
3. Run the exception handler
	* If kernel handler used then processor changes mode from user/protected to kernel/unprotected mode
	* So it has access to all the system resources	

**for 1 and 2, NOTE:** if OS kernel takes control then they are pushed onto the kernel stack rather than that process's stack

## Kinds of Exception
1. **Interrupts**
	* Provides a way for Hardware to notify the system that it needs attention
	* Signal from external device
	* Asynch
	* returns to next instruction
	* How?
		1. Pin on CPU is set to signal exception, Exception number is coded on system bus
		2. Current process finishes its intruction, control transfered to handler
		3. Interrupt handler runs
		4. Control is transfered back to original process and process continues as if nothing happened
	* Vs. Polling
		* Check device periodically - inefficient
			* like checking your phone every 10 seconds
		* Interrupts are better like phone ringing to signal it needs attention
2. **Traps**, aka System Call, ~~aka Dupstep~~
	* Enable processes to interact with OS
	* Intentional exception
	* Synch
	* returns to next instruction
	* How?
		1. App makes a system call indicating which service is needed by the sys call number
		2. Control transferes to handler and system call is done
		3. Control transferes back to original process starts next instruction and continues like a function return
3. **Faults**
	* Handles problems with current instruction
	* page vs seg faults
	* Potentially recoverable error
	* Synch
	* Might return to current instruction and re-excecute it	
4. **Aborts**
	* Cleanly ends a process freeing its memeory
	* nonrecoverable fatal error
		* Corrupted memory
	* synchronous
	* dones't return

## IA-32 & Linux

* Exception Types
* Each exception type is assigned a unique non-negative int
* 0-31 are defined by processor
	* 0 -> Divide by 0
	* 13 -> Protection fault - Linux  Seg Fault
	* 14 -> Page Fault
	* 18 -> Hardware error or Machine check
32-255 are defined by OS
	* 128 -> system call
* System Calls
	* convention
	* System call number is placed in %eax
	* other gen purpose registers used for arguments except for %esp
	* First 4:
		1. exit
		2. fork
		3. read
		4. write
* System Call Example

```
include <stdlit.h>
int main(){
	write(1, "hello World\n", 12);
	exit(0);
}
```

## Processes
* What
* Is an instance of a running program which at any point in time has a context
	* Memory Segments - gets its own phyiscal pages
	* CPU - registers - must be preserved when another process uses the CPU
	* OS - open file descrtipor, page table, process table, maintains these for each process
* Why
* Provides key illusions that process excluseley uses
	1. The CPU - Independent logical control flow
 	2. Memory - Virtual address space (VAS) as if each process is running by itself
* Who is the illusionist
	* OS virtualizes: CPU, Memory, Devices
* Concurrency
	* AKA multitasking
	* Overlapping execution of 2 or more processes
	* interleaved execution
	* parallel execution

## User/Kernel Modes

* what?
	* Are different privliedge levels a program can run at in the CPU
* Kernel Mode: Can execute any instruction - Has CPU switch modes no I/O
* Supervisor
	* Can access any memory address
	* Initates start in user mode
	* switch to kernel mode only via an exception
	* OS Handler can switch back since its in kernel mode
* Kernel
	* Memory resident part of OS
	* Kernel address space can't be accessed by user process

## Context

* What?
	* Kernel maintains a (partial) context for each processs
	* Everything the kernel needs to restart a pre-empted process
	* Includes
		1. CPU State
		2. User's stack
		3. Kernel's Stack
		4. Kernel's data stack count
			* Page table
			* process table
			* file table
* Context Swtich
	* Occurs during kernel exception as a result of some exception
	* Enables the scheduler to run another process
	* Enables the exceptions to be processed
		1. Save context of current process
		2. Restore context of some other process
		3. Pass control to the restored process
	* Context switches are expensive!!!
* **Negative** conflict due to causing cache pollution
* Example
	* read()
	1. In user mode process A requests to read from disk (trap)
	2. Switched to kernel mode and run handler request DMA and interrupt when done
	3. Reading from disk is slow
		* Context switch to process B 
	4. Swtich back to user mode and run process B
	5. Disk interrupts - switch to kernel mode and run handler
	6. Context Switch back to process A
	7. Switch back to user mode and process A continues aftter read

## Signals

* What?
	* Is a small message sent to a destination process to notify it that some kind of system event occurred
	* Linux has 30 signal types (`% man 7 signals`)
* Why?
	* Informs user - level process about
	1. Low-level hardware exception
		* Divide by 0 (exeception 0) -> exception handler -> signals SIGFPE (#8)
		* Illegal memeory ref (exception 13) -> exception handler -> signals SIGSEGV (#11)
	2. High-level events in kernel (or user processes)
		* typing ctrl+c -> keyboard interrupt -> exeception handler -> signals SIGINT (#2) of foreground process
* Enables inter-process communication
* Enables a high-level software form of exception control flow

