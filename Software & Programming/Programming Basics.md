## 1. Program Organization
- Having *multiple* files is good practice, speeds up compilation (only compiles .cc files when changes), separates interface from implementation

- Header file: declarations of functions and data structures (.h file)
- Function implementations and main function in .cc files
	- Every C++ program must have 1 global function named ```main```

**#include**: Copies content of file into current file, belongs to a class of instructions called *preprocessor directives*
- Including .cc file instead of .h is bad practice
- ```< >```: processor searches in an implementation-defined manner in directories predesignated by compiler/IDE
- ```" "```: used to include programmer-defined header files

**#ifndef**: include guard; prevents the .h file from being included multiple times
- Placed in the .h file to be included
- Always guard first; ```#ifndef [name]``` then ```#define [name]```

**Stack**: Stores local variables and parameters. Always stars at main in the beginning of execution, stack is empty with a pointer at the start to point to it (local).

**Heap**: Memory used by all parts of the application. A hierarchical data structure.

## 2. Recursion
- A recursive algorithm is one which breaks down the problem to be solved into smaller, discrete steps that are solved using the same algorithm
- A recursive function is an implementation of a recursive algorithm, it makes a call to itself to perform each step