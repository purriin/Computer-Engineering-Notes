### The C Standard Library
#### Function Prototypes:
```c
// Standard IO:
#include <stdio.h>
```
```c
// Memory Allocation:
#include <stdlib.h>
```
```c
#include <string.h>
```
```c
#include <math.h>
```
```c
#include <time.h>
```
```c
// Boolean:
#include <stdbool.h>
```
```c
// C++:
#include <iostream>
```

#### Variables:
1. **Integers**
	- int (32 bits), short (16 bits), long int (>= 32 bits)
	- signed (default) or unsigned
	- $-2^{31}$ to $2^{31}-1$
2. **Real**
	- double (64 bits), float (32 bits), long double
3. **Logic**
	- bool (8 bits / 1 byte)
4. **Void**
	- void, NULL
5. **Character**
	- char (1 byte)
	- C++ supports string
	- char = (' '), str = (" ")
	- Special characters:
		- **'\\n'**: new line
		- **'\\\\'**: print \
		- **'\\"'**: print "
		- **\\t**: tab
		- **'\\0'**: null character

- Variable naming conventions:
	- Camel Case: inputName
	- Snake Case: input_name
##### Arithmetic
- Uses BEDMAS
- int $\div$ int = int 
- Increment: i++, i--
- Typecasting: (double)i, (int)i

```c
/* #include <math.h> Functions: */

double sqrt(x);
double pow(double x, double y); // x^y
double log(double x); // log(x)
double log10(double x); // log_10(x)
double exp(double x); // e^x
double fabs(double x); // |x|, int abs(x) from standard library
double sin(double x);
double cos(double x); // and other trigonometric functions...
fmax(x, y);
fmin(x, y);
double floor(double x); // largest int <= x
double ceil(double x); // smallest int <= x
double fmod(x, y); // remainder for doubles
double rint(double x); // round to nearest int

/* #include <math.h> Constants: */
M_PI // pi
M_E // e
```

#### Random Number Generator
```c
int rand();
```
- Takes no argument
- $0$ to $2^{31} - 1$ ($2^{31} - 1$ is RAND_MAX)
- Pseudo random number generator (same number if used multiple times if not seeded)

```c
void srand(unsigned int seed) // Can use time(NULL) for input
```

- Ex. rand()%6 + 1 - 1~6, rand()%51\*2 - even 0~100

#### Basic Functions
```c
// Basic output, outputs string to terminal
printf(char* str);
```
```c
// Basic input, save user input to variable input
scanf("%d", &input);
```

#### Logic & Boolean
```c
if (<expression>) {
	// Code
} else if (<expression>) {

} else {

}
```

- Boolean operators:
	- <, >, <=, >=, =\=, !=, &&, ||
- Demorgan's Law:
	- !(P&&Q) = !P || !Q
	- !(P||Q) = !P && !Q

#### Loops
```c
while (<condition>) {
	// Code
}
```
```c
break; // Get out of loop
continue; // go to next iteration of loop
```
```c
for (<initialization>; <condition>; <increment>) {
	// Code
}
// Note if skip initialization, still need to put ;
```
```c
do { // Will run at least once
	// Code
} while (<condition>);
```

#### Pointers
- A physical address to some data
- 8 bytes

1. Declaration:
```c
int *p;
```

2. Initialize:
```c
p = &x
```

3. Dereference:
```c
*p = 7; // x = 7
x = *p + 1 // x = x + 1
```

#### Arrays
```c
// 1D array:
int num[];

// 2D array:
int num[][]
```

- Is syntactic sugar for pointers
- Points to the first element of the array, elements are stored consecutively
- Another way to access elements in array:
```c
// 1D array:
*(num + i);

// 2D array:
*(*(num + i) + j);
```

#### Dynamic Memory Allocation
```c
// Allocating memory:
int size = // Some number of elements
int *p = (int *)malloc(size*sizeof(int));
```
```c
// Freeing memory:
free(p);
```

#### Strings
#strings
- Only in C we use NULL terminated strings

#### Structs
- A composite data type declaration that defines a physically grouped list of variables under 1 name in a block of memory

```c
typedef struct [name] {
	// Assignments
} [object name] // Without this, have to call struct [name]
```

- To access elements:
```c
[object name] *p = &[object];
(*p).element = []; // Dot syntax
p->element = []; // Arrow syntax
```
