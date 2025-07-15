- Applications may pass through multiple layers of libraries
- What an operating system is depends on the application
	- Android and Debian both use the Linux kernel, but the applications are different 
	- Maybe they’re the same OS if you only care about terminal applications 
	- “Linux” distributions may be considered GNU/Linux GNU distributes the standard C library and common utilities 
	- An OS consists of a kernel and libraries required for your application
- Static Libraries Are Included At Link Time

### Dynamic Libraries
- Dynamic Libraries are for reusable code
	- The C standard library is a dynamic library (.so), like any other on the system Basically a collection of .o files containing function definitions
	- Multiple applications can use the same library
- Dynamic Libraries are included at runtime
- Useful command line utility for dynamic libraries:
```
ldd <executable>
```
- Shows which dynamic libraries an executable uses

### Static vs Dynamic Libraries
- Another option is to statically link your code
- Basically copies the .o files directly into the executable
- The drawbacks compared to dynamic libraries:
	- Statically linking prevents re-using libraries (commonly used libraries have many duplicates)
	- Any updates to a static library requires the executable to be recompiled
- Dynamic libraries updates can break executables
	- A dynamic library update may subtly change the ABI causing a crash
	- Consider the following in a dynamic library: A struct with multiple fields corresponding to a specific data layout (C ABI)
		- An executable accesses the fields of the struct used by a dynamic library
		- Now if a dynamic library reorders the fields 
		- The executable uses the old offsets and is now wrong
		- Note: this is OK if the dynamic library never exposes the fields of a struct
- Dynamic libraries allow easier debugging

- C Uses a Consistent ABI for structs
	- structs are laid out in memory with the fields matching the declaration order 
	- C compilers ensure the ABI of structs are the consistent for an architecture
