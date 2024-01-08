## 1. Definitions
**(GNU) make**: A utility that executes the necessary compilation. *Makefile* specifies what compilations are necessary.

**Makefile Format**: 
```
target: dependencies
[tab] system command
```

**Object File (.o)**: Binary file containing binary machine instructions. Cannot be directly executed, contains unresolved references (external libraries, static data). Data in object file has illegal but relocatable address.

**Executable File (.exe)**: Binary file containing binary machine instructions. Can be directly executed. Is the output of linking object files.

## 2. Compilation Lifecycle:
1. **Preprocess**: Macro substitution, stripping comments, file inclusion
```
g++ -E
```

2. **Compilation**: Input: C++; Output: Assembly code
```
g++ -S
```

3. **Assembly**: Input: Assembly code; Output: Binary code (object code)
```
g++ -C
```

4. **Linking**: Input: Object file; Output: Executable file; Symbol resolution; Relocation

**C++ Example**:
```
$ g++ -c insert.cc -o insert.o // -c: compile source files without linking
									generates object file
$ g++ insert.o search.o main.o -o linked_list // This step links 3 objects into an 
												executable file
```