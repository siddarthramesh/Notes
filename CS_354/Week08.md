# Different levels of programming

* C
  * Higher level language for us
  * more productive
  * type checking
  * portable
* Assembly
  * Textual Representation of Machine code
  * Still human readable
  * very machine dependent
* Machine Code
  * Elementary of CPU in binary
  * Exactly what machine understands
  * Executable
* Why learn assembly
  * Understand compiler options
  * Examine compiled code for inefficiencies
  * Better Understand virtual address space and memory address
  * Identify code vulnerabilities
  * Out focus - read assembly

|C|Intel|Assembly Suffix|size in Bytes|
|:---:|:---:|:---:|:---:|
|char|byte|b|1|
|short|word|w|2 (not 4 bytes)|
|int|double word|l|4|
|long int |double word|l|4|
|long long int |N/A|N/A|8 (maps into 2 double words)|
|char*|double word|l|4|
|float|single precision|s|4|
|double|double precision|l|8 (quad word)|
|long double|extended precision|t|10 (compilers change to 12)|

* ISA (Instruction set architecture)
  * Format and behavoir of machine-level programs
  * Memory treated as a large byte array (virtual address space)

* IA-32
  * x86
  * Intel Architecture 32-bit
  * 32-bit address
  * 2^32 = 4GB address space
  * byte addressable
  * 32 bit data
  * `gcc option -m32`
* x86-64
  * x64
  * 64-bit address and data
  * default ISA for cs linux machine
  * 2^64 bytes of memory

# Compiling Steps
* **Compiler**
	* Uses rules higher level language, machine ISA, OS conventions to paroduce adn executable program
	* Reorder instruction - remove useless code
	* Replace slow code with faster code
	* Replace some recursive code with iterative code
	* `gcc -01` okay level for us to study Assembly
	* `gcc -02` minimum standard for performance
* Source
1. **Preprocessor**
   * Edits source
   * Includes (Expands the source by pasting in the name "HEADER" file)
   * `#define` substitutes 
   * `gcc -E`
2. **Compiler** (.s file)
   * Translates Higher Level Language to assembly in a particular format
   * `gcc -S` => makes .s file
   * format at AT&T assembly <-- textbook
   * Intel gcc option -mgsm=intel
3. **Assembler** (.0 files)
   * Translates to Machine Instruction
   * `gcc -c` => Make .o file
   * Object files - don't resolve globals
4. **Linker**
	* Merges .o files and must have one with main function
	* Fills in addresss for globals and other things going into the data and bss memory areas
5. **Executable**
	

* What aspect of machine does C hide from us?
  * Machine instruction
  * Registers (General, Condition codes, frame pointer)
  * Program counter
* C's vs machine view of data
  * C: vars, types, components structs and arrays
  * Machine: large array of bytes
* What does assembly remove
	* High Level language program flow if/while var types/composites
* IA-32 Machine Code
  * Machine instruction have unquie  encodings are 1-15 bytes in length where common instructions are smaller

## Registers

* What?
  * Locations that stone adders/data that can be directly accessed by all
* Program Counter
  * `%eip`
  * stores address of next instruction to be executed
* General registers (Reg files)
  * 8 prenamed locations that store 32-bit info
	
|[31:]|[:16]|[15:8]|[7:0]|
|:---:|:---:|:---:|:---:|
|%eax|%ax|%ah|%al|
|%ecx|%cx|%ch|%cl|
|%edx|%dx|%dh|%dl|
|%ebx|%bx|%bh|%bl|
|%esi|%si| | |
|%edi|%di| | |
|%esp|%sp| |(stack pointer)|
|%ebp|%bp| |(frame pointer)|

* Condition code registers
  * store status of most recent ALU instruction
  * Used for conditioned changes in the program's flow (if,while)
* Floating point registers
  * Frame pointer calls
