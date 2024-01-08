## 1. Introduction
- C++ source files use .cpp, .cxx or .cc extensions
- C++ program starts executing in ```int main(int argc, char* argv[])``` 

**Reference**: Is an alias or alternative name to an existing variable. 
- Primary use: function formal parameter so the function works on the original copy of the variable
- Ex. ```void foo(int &num)```
- Safer but less powerful pointer:
	- Strict type checking by compiler
	- Reference cannot be reassigned
	- Must be assigned at initialization
	- No separate memory allocation
	- Cannot be NULL

## 2. Input & Output
**Standard Output**:
```cpp
cout << [] << endl;
```

**Standard Input**:
```cpp
cin >> X;
```

**File Input**:
```cpp
#include <fstream>
ifstream inFile("filename");

// OR

ifstream inFile;
inFile.open("filename");

int a, b, c;
inFile >> a >> b >> c; // Writes to variables

inFile.close();
```

**File Output**:
```cpp
#include <fstream>
ofstream outFile("filename");

// OR

ofstream outFile;
outFile.open("filename");

int a = 6;
output << "a = " << a << endl; // a = 6

outFile.close();
```
- Output will not be immediately written to file, instead temporarily store output in buffer write out to file periodically in chunks
	- Reduces difference between input and output speed
	- To force output: `output.flush()` or `outFile << endl` 

- 2 ways to open a file for output:
	1. Open for writing (default): if file already exists, it is first erased; otherwise it is created
	2. Open for append: use `outfile.open("filename", ios::app);`
- When opening a file:
	- `"filename"`: search in current directory
	- `"dir/filename"`: relative path
	- `"../filename"`: relative path
	- `"/exc/file"`: absolute path
## 3. I/O Error Handling
- Detecting input failures:
	- `cin.fail()`: Returns true if fail (`inFail.fail()` for files, can use on `cout` and `ofstream` too)
	- `cin.eof()`: Returns true if end of file reached, used to read entire file
- Handling input failures:
	- `cin.clear()`: Clear error state flag, resets the stream state
	- `cin.ignore()`: Discords # of `ints` up to first `char` (whichever comes first) (`.ignore(int, char)`)

**String Stream**: Treating string as streams using `<<` and `>>` on strings
- Stream variables cannot be passed by value, pass by reference instead (`streamstream & str`)
```cpp
#include <string>
#include <sstream>

stringstream ss;

ss << "output" << endl;
cout << ss.str(); // Outputs
ss >> var1 >> var2; // Input
```
## 4. Introduction to Classes
**Class**: Expansion of `struct`, contains both data and code. Typically defined in a `.h` file
```cpp
class Time {
private:
	// Hidden from outside, can only be accessed from within class
	int minute;
	int second;
public:
	// Accessible outside of class
	void resetTime();
	int getSeconds();
}
```

- Objects are instantiations of classes, each member has concrete value
- Functions are defined by using class name followed by `::` 
	- Ex. `void Time::resetTime() {`
- Can access member data/variable in member functions
	- Ex. `Time t; t.resetTime();`
## 5. Member Functions
**Constructor**: Special member functions whose job is to initialize data in object, called automatically when object is instantiated. Never called directly by any function.
- Has the same name as the class, no return value (not even void) must be public
- Can initialize objects in different ways with multiple constructors, must have the same name but different parameters
- There is a constructor even if not declared
Ex.
```cpp
class Time {
private:
	int minute;
	int second;
public:
	Time();
	Time(int, int) // Overload function
}

Time::Time() { // Constructor
	second = 0;
	minute = 0;
}

Time::Time(int m, int s) {
	minute = m;
	second = s;
}
```

**Destructor**: Complement of constructor, automatically called when an object is destroyed not required
- `~` followed by class name, cannot take any parameter, cannot return any type, must be public
- if you used a `new` or `malloc` anywhere in class, destructor with `delete` needed
- Class can only have 1 destructor
Ex.
```cpp
B::~B {
	if (name != NULL) {
		delete[] name;
	}
}
```

- When using `new[]` must free with `delete[]`, if allocated without `[]` must free without `[]` otherwise undefined behavior
- Memory leak when `new` memory regions have no name associated with them (reference lost), always set pointer to `NULL` after deleting

**Accessors**: Member functions that *get* private member variables.

**Mutators**: Member functions that *set* private member variables.
## 6. Variable Scope
- 4 ways to hold data:
	1. **Global Variable**: Declared outside of any function
	2. **Arguments**: Passed in on function calls
	3. **Local Variables**: Declared inside of function or code block
	4. **Dynamic Variable**: Allocated with `new` and have no name

- How they are created or destroyed:

| Type | Created | Destroyed |
| ---- | ---- | ---- |
| Global Variables | Start of program | End of program |
| Arguments | Start of function invocation | End of function execution |
| Local Variables | Start of code block | End of code block |
| Dynamic Variables | With `new` | With `delete` |

**Memory Layout**:

| Kernel Memory |
| ---- |
| **User Stack (Created at runtime, local variables, argument)** |
| $\downarrow$ |
| $\uparrow$ |
| **Memory mapped region for shared libraries** |
| $\uparrow$ |
| **Runtime Heap created by malloc (Dynamic variables)** |
| **Read/Write Segment (data, ..., bss, Global variables, loaded from .exe)** |
| **Read only segment (.init, .text, .vodata, Instructions, loaded from .exe)** |
| 0 |
## 7. Operator Overloading
- Operators can be seen as function calls, we can overload/redefine the function
Ex.
```cpp
Complex operator+(complex x, complex y)
	return (Complex(x.getReal() + y.getReal(), x.getImag() + y.getImag()));
```
- Can also make it a member function: `Complex Complex::operator+(const Complex& x) const`
## 8. Object Passing & Copying
| Pass-by-Value | Pass-by-Reference |
| ---- | ---- |
| Provides function with its own copy; guarantees the function can't change your data accidentally | Efficient no copying is done on function call |
| The function can change its local copy without affecting the rest of the program | Can be used to return multiple variables to the called |
| Inefficient: the parameter needs to be copied on every call | The function cannot make "private" or "local" copies of the parameter without first making a copy |
**Copy Constructor**: A special constructor used to make a copy of an existing object instance.
- Compiler provides a default copy constructor for every class
```cpp
MyClass(const MyClass & other); // Prototype
```
- Must pass object by reference 
- `const` since non-`const` references cannot bind to temp objects 

- Copy Constructor called when:
	- instantiating (object from another of the same type)
	- an object is passed by value
	- object returned by value
- Default copy constructor: performs member wise copy (shallow copy), limited when class contains *pointers*
- If class requires 1 of 3 (destructor, constructor, assignment op) it requires all 3
## 9. Inheritance
**Inheritance**: 2 classes (base and derived class) share a lot of properties yet the derived class has its own properties. Used to reuse code/features from other classes.
- Allows a class to inherit the content of another class
- Also called *superclass/subclass*

- Derived class is always larger than the base class
	- Derived class receives all of the member variables and functions of the base class
	- Therefore, in derived class, we can directly use base members (can extend, customize or override)

Syntax:
```cpp
class DerivedClass:public BaseClass {
	// Only list inherited members if you want to change
	// + New members
}
```

- Derived classes *cannot* access private members of base class but are inherited
	- If we want it to be accessible to derived: use *protected* instead of private
- Mixing types: parent $\rightarrow$ derived is okay but derived $\rightarrow$ parent is not

**Polymorphism**: A class that declared or inherits a virtual function is called a *polymorphic class*

- If we never intended to use a certain base class (but derived class uses it) then we do not fill dummy function definition
```cpp
virutal int area() = 0; // Pure virtual function
```

- A class that contains at least 1 pure `virtual` function is called an *abstract class*
	- Also called an interface, you cannot instantiate an object of an abstract class