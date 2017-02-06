## Programmer`s Memory Model for Java

* Call Stack
	* Activation Records for each called method
	* Contain Local Variables (Parameters Variables)
	* "Birth" happens at declaration 
	* "Death" When it falls out of scope
* Heap
	* Arrays and Objects
	* Need a reference variable to access them
	* "Birth" at a new allocation
	* "Death" when no longer referenced 
		* Garbage Collector 
* Static Data
	* Static because it is for the length of the entire program
	* Literals
	* Class variables (static)
	* Allocation Doesn't Change
	* "Birth" at program execution start
	* "death" at execution end
* What is a collection?
	* A group of items gathered into a container 
		* Items (Data) - Individual members of the collection 
			* Simple - Numbers (Primitives)
			* Composite - Student Record, album (References)
		* Container (Data Structure) - Structure used to store the collection
* What operations can you do on a collection* Which are the most fundamental*
	* Add
	* Remove
	* Get/Search
* Example: Bags
	* Concept
* General Container 
	* It can store any type of item
	* It can store different types of items in the same bag
	* Duplicates are ok
	* Unordered container	
	* There is no explicit internal structure
	* Can add and remove really fast
	* Remove gets a random item
	* Look up is slow
* Operations
	* Void add (item)
	* Item remove()
	* Boolean isEmpty()
* Problems 
	* Remove when bag is empty
	* Adding when bag is full - not a problem, we will expand the capacity

## ADTs - Abstract Data Types
* Why
	* The applications
	* Likely to have many uses if your ADT is general
* What
	* A wall separating the `Why` from the `How`
	* The ADT
	* The operations you can do
	* `List` interface
* How
	* Implementation 
	* Code that says how the collection is structured
	* How the operations work
	* ArrayList or LinkedList
* ADTs
	* Specifies what you can do
	* Java Docs
		* Conceptional Description
		* List of Operations
	* Java Interface 
	* Used to code the ADT in Java
Data Structure
	* A construction within the implementation class that used to store the collections of items
	* Ex. Arrays, Chain of nodes
* Code fragment
	* Put the numbers 0 through 99 into a BagADT named bag

	```
	BagADT bag = new 
	for (int I = 0, I < 100, i++) bag.add(i);
	```
	
* Java AutoBoxing
	* Java automatically converts between primitives and their wrapper classes
	
```
Public static void printBag(BagADT bag){
	While (bag has stuff){
		Print ( bag.remove() )
	}
}
```

* What makes software good
 * Works
 * Easy to use/modify
 * Reasonability efficient
* What makes code reusable
	* Documentation
	* Modularity
	* Methods
	* Classes
	* Interfaces
	* Generality

```
Public interface BagADT{ 
	//abstract methods
	Void add(object item)
	Object remove() throws NoSuchElement
	Boolean isEmpty()
}
```

* Using Object class for generality 
	* In Java an object reference can refer to any object in Java

```
Public class ArrayBag implements BadADT{
	//instance variables
	Private Object[] items;
	Private int numItems;
	//how the data is structured
	
	//constructor
	Public ArrayBag(){
		Items = new Object[100];
		numItems =  0;	
	}

	//BadADT methods
	//how operations work
	Public Boolean isEmpty(){
		Return numItems == 0;	
	}

	Public void add (Object item){
		//add items to first free element
	}
}
```

* Bag ADT Design and Interface (the WHAT)
* Bag ADT Implementation (the HOW)
* Bag ADT Use (the WHY)

* Using a general ADT and its implementation to instantiate a container:
	* ADT_Name Object_Name = new Impl_Class_Name()
	* ADT_Name - Java Interface
	* Object_Name - Java Identifier 
	* Impl_Class_Name - Java Class for the specific implementation you want to use

* Write a statement that makes a Bag ADT container named bag
	* `BagADT bag = new ArrayBag();`
* Assume `Die` is a class representing dice and has a zero parameter contructor... add 6 die to `bag`
	
	```
	for (int i = 0; i < 6; i++){
		Bag.add(new Die());	
	}
	```
	
* Java upcast Die to an Object
	* Specific Die -> general Object... java upcast automatically
	* `Die myDie = bag.remove();` 
		* Error because remove() returns type Object but the Die is need of down casting 
		* General object -> specific Die... Java doesn't downcast automatically
	* Use `instanceof` to check
	* `Object obj = bag.remove()`
	* `if (obj instanceof Die) Die myDie = (Die)obj; `
	
## Java Generics - a better way to make a general Bag ADT
* What changes are needed to make the interface below generic*
	* `Public interface BagADT <E>`
		* `Void add (E item);`
		* `E remove();`
		* `Boolean isEmpty();`
1.	Specify one or more "type params" 
	* List after class/interface name
	* Put them in < >
2.	Replace occurrences of specific type with the type param
	* `Public class ArrayBag<E> implements BagADT<E>{}`
	* (don't have to match generics, but beyond scope of course)
	* Replace `Object` or specific type with E or type param
	* `Items = new E[100];`  - doesn't work, cant instantiate type of parameter array memory
	* `Items = (E[]) new Object[100];`
* How do we use a generic interface and its generic implementation to make a container
	* `ADT_Name<Item_Type> Object_Name = new Impl_Class_Name<Item_Type>();`
	* No downcasting is needed, its automatically done now

## Design - List ADT
* Concept
	* A list is a general container that allows duplicates
	* It is continuous collection
	* Position oriented with zero based indexing
	* Expands capacity
	* Maintains relative ordering
* Operations
	* Adding at a specific location pushes items down
	

## Java API Lists
* Applications - why (App Store) 
* Interface - what (List)	
* Implementations - how (ArrayList, LinkedList, Stack, etc.)
	* `List<String> words = new ArrayList<String>();`

### Design - Iterators
* What are they?
	* Objects used to step through a collection of items
	* An abstraction of a pointer
	* Stores its position in a particular collection
	* Position can change
	* Item at position can be accessed
	* Its external separate from its container
	* Has its own interface and implicating class
	* User can instantate as many interators as desired to access the itesm in the colletions
* Concept
	* Get Iterator from a particular container storing the collection we desire to step through
	* Use Iterator to access the collection of items in the container
* Operations 
	* Container class operation that makes and returns an iterator
	* Iterator class operation that determine if it has another item and Access item at the current position then advances the iterator to next position
	* `Iterable<T> interface` in java.lang - specifies the operation to get an iterator to step through a collection
		* `Iterable<T> iterator()` 
			* Returns an iterator "pointing to" initialize to the first item in the collection (or "end mark" if the container is empty)
			* This method is implemented in the container class
	* `Iterator<T> interface` in java.util - specifies the operation that iterators can do
		* `Boolean  hasNext()`
			* Returns true if iterator is pointing a an item
		* `E next()`
			* Returns a reference to the current item
			* Advances the iterator to next position
	* `Void remove()` //"optional"
		* How do we make an operation optional
* Use - Iterators
	* `Iterator<E> itr = words.iterator();`
* Implementation - options for iterator classes
	* Indirect Access
		* Iterator uses the container's operations to access the items in a collection
	* Direct Access
		* Iterator goes right to the container's private instance variables to access the items in the collection

#### Indirect Access
* Iterator uses the container operations to access the items in a collection
* Container class constructs the iterator by passing a reference to itself (this)
* Indirect access is safer
* Only works if the container class has sufficient operations so that iterator can use them to step through the collection
#### Direct Access
	* Iterator goes right to the container's private instance variable to access the items in the collection
	* Container class constructs the iterator by passing the instance variable needed by the iterator for direct access
	* Direct Access is faster than indirect

```
Private E[] myItems;
Private int myNumItems;
Private int currPos;

ArrayBagIterator( E{} items, int numItems) {
	myItems = items;
	myNumitems = numItems;
	currPos = 0;
}

Public Boolean hasNext(){
	return currPos < myNumItems;
}

public E next(){
	if (currPos >= myNumItems)
		Throw new NoSuchElementExceptio();
	E result = myItems[currPos];
	currPos++;
	return result;
}
```

* ArrayBags don't provide size or a means to get items so we can't code this as an indirect access iterator
* **EXECPTION**
	* When a problem is detected we (or Java) can signal a problem has occurred by throwing an exception
	* When an exception is thrown normal execution ends and switch to exception handling execution
	* Syntax
		* `throw exceptionObject;`
	* Example
	
	```
	//could add to ArrayBag's remove()
	if (numItems <  0) 
		Throw new EmptyBagException();
	```
	
* Try-Catch 
	* try code that might be problematic
	* catch exceptions to handle particular problems

* Normal Execution
	* Start: top of main()  
		* flow through the code in the normal scanner
	* Execute
		* Normal code, code in try blocks, code in any finally blocks wholes try was entered
	* Skip
		* code in catch blocks
	* Switch to Exception Handling Execution
		* when an exception is thrown 
* Exception Handling Execution
	* Skip
		* Normal code including any remaining code in try blocks that were entered
	* Execute
		* do code in finally blocks whose try was entered
	* Switch back to Normal Execution
		* When the exception is caught
* Searching for a Matching Catch
	1.	Locally
		1.	If the code causing the exception is in a try block, then check its catches for a match
		2.	Else terminate the method and throw the exception to its caller
	2.	Remotely
		1.	If an exception is returned from a method call that is in a try block then check its catches for a match
		2.	Else terminate the method and throw the exception to its caller
	3.	Checking for a match Done from top to bottom of catches listed
		1.	Match Found
			1.	Switch back to normal execution
			2.	Execute code in the catch block and corresponding finally block (if any)
			3.	Continue  exception after that try-catch
		2.	No Match Found
			1.	Execute code in corresponding finally block (if any)
			2.	Terminate the methods and throw the exception to its caller
* If main() throws an exception to RTE (Run Time Environment)  then the RTE terminates the programs (crash) and displays an exception message

### Checked vs unchecked
* Unchecked: 
	* for problems that can and should be avoided with carful programming
	* Ex. NullPointerException, IndexOutofBondException
	* `Public class MyException extends RuntimeException {} `
* Checked
	* For problems that cannot be avoided with carful programming
	* Compiler checks that programmer is aware 
	* By looking for 1 of two things
	* Catch block
	* Throw Clause
	* `Public class MyException extends Exception {} `


### The Data Structure 
* Heap full of nodes
	* place to store Data 
	* place to store next
* Array (static data structure)
	* has reference to the heap of array 
	* Can't extend array, have to make a new bigger one
* Chain of Nodes (Dynamic)
	* head node reference to heap
	* can add nodes by just linking them on
	* can insert new nodes just link it in and no shifting needed
* Main Goal
	* Implement ADTs using a chain of nodes as the data structure rather than an array
* Assume code is in main()

|Code|Call Stack|Heap|
|:---:|:---:|:---:|
|Listnodes<String> |n1 = null;	N1[null]|
|N1 = new Listnode<String>("blue");	|N1 ->|	[~blue][null]|
|Listnode<Integer>n2 = new Listnode<Integer>(11);|N2 ->|	[~11][null]|
|Listnode<String> head = n1; Head.setNext(new Listnode<String>("green"));|Head ->|	[~blue][->[green][null]|

* Create a chain of Listnodes containing the Strings "yippie", "ki", "yay" 
	* `Listnodes<String> head = new Listnodes<String>("yippie", new Listnodes<String>("ki", new Listnodes<String>("yay")));`
* It is better to implement a listADT class and add the nodes one-by-one
* Code that can count the numbers of strings in the chain of nodes

```
int count = 0;
while (head != null) { 
	count++;
	head= head.getNext();
}
^that deletes chain of node in process

int count = 0;
Listnode<String> current = head;
while (current != null){
	count++;
	current = current.getNext();
}
```

* Assume head points to the first node in a chain of Listnodes containing strings, write code that adds a node containing "rear" to the end of the chain of nodes. You may assume the chain has at least one item
	1.	Traverse to node at End of chain
	2.	Ink a new nope to end

	```
	Listnode<String> current = head
	while (current.getNext() != null){
		current = current.getNext()
	}
	current.setNext(new Listnode<String>("rear"));
	```

* Remove the 3rd item from chain of nodes
	* `head.getNext().setNext(head.getNext().getNext().getNext()); //MESSY ALEART`
* Remove the nth item from chain of nodes
	1.	Use a current reference to travers to nth-1 node
	2.	Set the next field of the nth-1 node to the value in the next field of the nth node

## Java classes
* Public
	* Accessible to any Code
* Private	
	* Accessible only to code within the same scope (ex. Class)
* Protected
	* Accessible only to code within the same class or any of that class's subclasses
* Package
	* Means package access
	* Accessible only to code within the same package
* Folder/directory 
	* Goal: hide the data structure from others
* Steps
	* Create a folder linkedlist
	* Put in it
	* `Listnode.java {package}`
	* `Linkedlist.java {public}`
	* `LinkedlistIterator.java {public}`
	* User must `import linkedlist.*`
	* `private Listnode<E> head;`
	* Access works if Listnode and linkedlist in the same package
	
```
private int numItems;
public LinkedList(){
	head = null;
	numItems = 0;
}
public void add (E item){
	//check for invalid item
	if (item == null) throw new IllegalArgumentExeception() 
	
	//create a new listnode
	listnode<E> newNode = new Listnode<E>(item);
	
	if (head == null){
		//special case
		head = newNode;
	} else {
		//general case
		lstnode<E> curr = head;
		while (curr.getNext() != null){
			curr = curr.getNext();
			curr.setNext(newNode);
		}
	}
	numItems++;
}
```

**LinkedList**

```
public E get (int pos){
	if (pos < 0 || pos > = numItems)
		throw new IndexOutOfBoundsException();
	listnode<E> curr = head;
	for (int I = 0; I < pos; i++){
		curr = curr.getNext();
	}
	return curr.getNext();
}
```

* indirect vs direct iterator in LinkedList
* Traversing using get() always starts at 0 for indirect
	* 3rd is 3rd item
* Faster since the iterator knows which nodes its at, direct

```
public class LinkedListIterator<E> implements Iterator<E>P
private listnode<E> curr;

linkedListIterator(Listnode<E> head){
	curr = head;
}

public Boolean hasNext() {
	return curr != null;
}

public E next () {
	if (curr == null) throw new NoSuchElementException();
	E item = curr.getData();
	curr = curr.getNext();
	return item;
}

public class LinkedList<E> implements ListADT<E>, Iterable<E>{
	publicIterator<E> iterator() {
		return new LinkedListIterator(head);
	}
}
```

**Tail Referece**
	* Used for direct access to the last node

```
public class LinkedList<E>
private Listnode<E> head;
private Listnode<E> tail;

public LinkedList(){
	head = null;
	tail = null;
	numItems = 0;
}

tail.setNext(newNode);
tail = newNode
```

**Must make sure tail is properly updated**

* Header Node
* Concept
	* used to eliminate special case code in add/remove
* Header data is pointed from head, contains null data, and points to first chain node

```
public LinkedList() {
	head = new Listnode<E>(null); //make header node
	numItems = 0;
}
```

* No need for special case now
	* Make sure your operations (get, contains)  skip counting the header node
	* This is commonly combined with tail reference

### Doubly-Linked Chains of Nodes
* {[Prev][data][next]}
* Head -> {[null][][]} .... {[][][null]}
* Enables both forward and backward traversal
* To added new node
	1.	newNode.setNext(current.getNext());
	2.	current.getNext().setPrev(newNode);
	3.	newNode.setPrev(Current);
	4.	current.setNext(newNode);
	* **NOTE** common to combine this with a tail reference and/or header node

* Circular singly-linked chains of nodes
	* if first and last positions don't matter then just use a current reference, else if they do matter, then just use a tail reference because the head is at tail.getNext()
* Circular doubly-linked chains of nodes
	* If first and last position don't matter then just use a current reference, else if they do matter, then just used a head reference because the tail is at head.getPrev();

## Complexity
* Used to compare different algorithms/code that solves the same problem
* Characterized how the use of resources scales as the problem size grows
* Time
	* Time is basic operations or dominant operations 
* Space
	* Space is amount of memory used
* Problem size
	* The aspect of the problem when changed affects the amount of resources used
	* If problem size doubles and the number of operations
		* Stays the same
			* Constant time complexity
		* Doubles
			* Linear time complexity
		* Quadruples
			* Quadratic time complexity
* complexity classifications are a first-order analysis
* Graph - Linear, quasi Linear, quadratic
	* Complexity analysis is not concerned about particular problem sizes
	* is concerned with how the work/resources used changes as he problem size grows
	* complexity dose affect performance, but not vice versa

* Concept
	* Characterized functions by their growth rates
* Growth rates functions starting with lowest: 1, logN, N, N*logN, N2, N3, Nc, 2n, N!
	* Constant complexity - O(1)
	* Linear complexity - O(N)
	* Quadratic complexity - O(N2)
	* Pronounced "order of..."
* Simplifying equations
	* Time equations T(N) = 4N2 + 3N - 11
	1. Drop low order terms from equations  [ T(N) = 4N2 ]
	2. Drop Multiplicative constant on high order term [T(N) = N2 ]
*Formal Definition
	* A function T(N) - actual resource consumption function is O(F(NGrowthRateFunction)) if for some constant C and some number n=0 such that T(N) <= C * F(N) for all N >  n=0 (problem size must be large enough)
* Choosing growth rate function F(N) that is the lowest upper bounds

### Number guessing Game
* Problem size: N for the upper bounds
* Dominant operation: guess - characterizes the work that is being done/time it takes, so we will count it
* Algorithm 1: guess 1 and count++ 
	* when analysing you want to determine if there are circumstances that result in best, average, and worst case
		* Best case: (Secret # is always 1) - 1 guess => O(1)
		* Worst case: (Secret # is always N) - N guess => O(N)
		* Average case: (All numbers are equally likely) - N/2 guess => O(N)
* Algorithm 2: guess = N/2, step = N/4, change guess by step if wrong, step = step/2 (alternate flooring and ceiling)
	* Ex.) N = 100.... 50, 75, 88, 94, 97, 99, 100
	* Log2(100) = 6.6 -> 0(logN)

* Basic Loops 
	* Loops typically mean multiply
	* Look for a variable in loop whose value is unknown and if changed affects the number of iteration
	* What is the problem size based on: j
	
	```
	for  (i= 0, I < j; i++){ 
		//body of the loop j times
		//sequence of statement
	}
	```
	
* Nested Loops
	* j * O(Nstart) = O(j * Nstart) -> if O(Nstart) is O(1) then *(j), if O(Nstart) is O(j) then O(J2)
	* What is the problem size based on: 
	
	```
	for  (i= 0, i < N; i++){ //N times
		for  (j= 0, j < M; j++){ //M times
		//sequence of statement
		}
	}
	```
	
* = O( N * M * NStart ) 
	* if N=M then O(N2 * Nstart) 
* if N = 5 then O(M*Nstart)
	* Keep all variable in your analysis in case they are related or are discovered to be constants

* Loops with nested method calls (assume problem size based on N)

```
for (i=0; I < N; i++){ //N time
	f1(i); //assume O(1) -> O(N*1) = O(N)
}
for (i=0; I < N; i++){ //N time
	f2(N); //assume O(N) -> O(N*N) = O(N2)
}
for (i=0; I < N; i++){ //unrool loop (0,1,2,3...N-1)
	f3(i); //assume O(i) 
   //sume of f3(i) to N-1 starting at i=0 -> (N/2)(N-1) = (N2/2) - (N/2) = O(N2)
}
```

* Method1:
	* A.length = N
	* O(N) - worse case
	* O(N2) - ANSWER
* Method2: 
	* B.length = N
	* S = i
	* O(1) - because of Method3 - Assume worst case s = 0
* Method3: 
	* C.Length = N 
	* X=i
	* Y = i+1 
	* O(1) - so back to meth2

### Returning N papers to N Students
* Problem size(N) = Number of Students
	* Dominant operation = Looking at name on paper - count for work/time
* What is the complexity of each algorithm below*
* Algorithm 1:
	* Call out each name, have student come forward & pick up
	* Best-case: No
	* Worst-case: No
	* O(N) - Linear
* Algorithm 2:
	* Hand pile to first student, student linearly search through papers & takes his, pass pile to next student who does likewise
	* Best-case: papers are ordered same as students
	* Worst-case: opposite of best (at end of pile)
	* O(N2)
