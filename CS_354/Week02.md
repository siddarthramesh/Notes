# Structures
* what?
	* Define a new type that is a collection of related data members that can be of any type
* why?
	* Enables organizing complex data often of different types into a unit

**don't forget semicolon at end of struct declaration!!!;**

```
struct Date {
	int month;
	int mDay;
	int year;
};
```

* Takes 12 bytes of data, 4 bytes for each int
* start of Date object is address `int month`
* *convention* that user define types are capitalize

```
struct Date today;
today.month = 9;
today.mDay = 19;
today.year = 2016;
```

* `.` dot operator does member access

* could of initialized list with `today = {9,19,2016};`

```
void printDate(struct Date date) {
	printf("%i/%i/%i", date.month, date.mDay, date.year);
}
```

* Pass-by-value so date struct is copied
* If struct is huge

# Typedef
* what?
	* Define a new type name for the global name space
* why?
	* Can make code less cluttered

* Changes to use typedef approach:

```
typedef struct Date {
	int month;
	int mDay;
	int year;
} Date;

Date today;
today.month = 9;
today.mDay = 19;
today.year = 2016;

void printDate(Date date) {}
```

* Use typedef in limited way, can really make code messy, so use drink and typedef responsibilities

# Pointers to Struct
* why?
	* dynamic allocation
	* Avoid copying overhead with pass-by-value of structs
	* Allow functions to change struct params
	* Enable creating linked structures such as linked lists and trees
* how?
	* declare a pointer to a pokemon
	`Pokemon  * pmPtr;`
	* Allocate space
		`pmPtr = malloc(sizeof(Pokemon));`
	* Assigned values
	```
	(*pmPtr).name = "Abra"; //---- THIS IS WRONG
	(*pmPtr).weight = 43.0;

	strcpy((*pmPtr).name, "Abra");
	strcpy((*pmPtr).type, "Psychic");

	//(*ptr).tag === ptr->tag [PREFERRED WAY]

	pmPtr->caught.month = 11;
	pmPtr->caught.mDay = 22;
	pmPtr->caught.year = 2016;

	free(pmPtr); //DONT FORGET
	```
	
# STRINGS
* what?
	* Are a sequence of chars whose end is marked with a null char `"\0"`
	* Represented using an array of chars which needs to be the strings length +1 for 0
* how?
	`char str[11] = "cs 354";`
	* copies these characters into the memory of str
	`char *sptr1 - "cs 354";`
```
str1 = "foldeno" //compiler error incompatibles types
strcpy(str1, "formication"); //too big, off by 1, no space for null character
							 //Will compiles, runs, but is a buffer overflow due to "\0"
sptr1 = "mumpsimus"; //compiles, runs, no probz
strcpy(sptr1, "vomitory"); //compiles, runs
						   //worries: memory leak, bad address in ptr, buffer overflow, non-writeable memory segments
```

## string.h library
* what?
	* A collection of functions to manipulate strings
	* strcat == make sure destination has enough space for adding src characters

