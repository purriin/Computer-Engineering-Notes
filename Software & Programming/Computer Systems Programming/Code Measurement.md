## 1. Introduction to Profiling
- We could time the functions we are interested in using the `CLOCK_PROCESS_CPUTIME_ID` clock
- It measures actual CPU time consumed by the process 
- However this too much manual effort, and we may not know where to focus

- We need to measure our programs with profilers
- We have to **profile** to see what is taking up execution time in a program 
- Profilers have two kinds of output: flat, and call-graph 
- They collect data from: statistical sampling, or instrumentation

**Flat Profiler**: Only computes the average time in a particular function. Does not include anymore information such as: callee’s

**Call-graph Profiler**: Computes the call times, frequency of function calls, call graph, showing what called the function

### 1.1 How Sampling Profilers Collect Data
- Mostly done by taking samples of the system state at a set rate 
- At a sample: check the system state 
- Will have some slowdown, but not much

### 1.2 How Instrumenting Profilers Collect Data
- Add additional instructions at specified program points 
- You can do this at compile time or run time (expensive) 
- Also, either manually or automatically (like conditional breakpoints)

### 1.3 The Basic Guidelines for Large Software Projects
- Write clear and consise code, not trying to do any premature optimizations (focus on correctness) 
- Profile to get a baseline of your performance 
	- Allows you to easily track any performance changes 
	- Allows you to re-design your program before it’s too late 
- Focus your optimization efforts on the code that matters!

- Some sanity checks:
	- Time is spent in the right part of the system 
	- Majority of time should not be spent in any error-handling, non-critical code or exceptional cases 
	- Time is not unnecessarily spent in the operating system

### 1.4 Important Tools
- `gcov` is a coverage tool that instruments your program 
	- It tells you how many times each line executes (needs debugging information) 
	- This does not give you performance numbers!
- `gprof` is a profiler that uses sampling and instrumentation and tells you how long each function executes R
- Reference: [GCC Instrumentation Options](https://gcc.gnu.org/onlinedocs/gcc-14.2.0/gcc/Instrumentation-Options.html)
#### 1.4.1 `gcov` Usage
- Use the compiler flag `--coverage `when compiling and linking (adds the `-fprofile-arcs` and `-ftest-coverage` flags) (or use `b_coverage=true` with `meson`) 
- When you run your program every object file generates a `.gcda` and `.gcno` Generate a report using `gcovr`, or using `meson`: 
```powershell
meson setup -Db_coverage=true build 
meson compile -C build 
ninja -C build coverage
```
#### 1.4.2 `gprof` Usage
- Use the `-pg` flag with `gcc` when compiling (also linking)
- Run your program as you normally would (it now generates a `gmon.out` file) 
- Use `gprof` to interpret the results `gprof <executable` 
- Example commands using `meson`, assuming the output `calc`: 
```powershell
meson setup -Dc_args=-pg -Dc_link_args=-pg build
meson compile -C build 
build/calc 
gprof build/calc
```
### 1.5 Flat Profile Reference
- There is one function per line, the columns are: 
- **time**: the percent of the total execution time in this function 
- **self**: seconds in this function 
- **cumulative**: addition of this function plus any above in table 
- **calls**: number of times this function was called 
- **self ns/call**: just self nanoseconds / calls 
- **total ns/call**: average time of function execution, including any other calls the function makes
### 1.6 Reading the Call Graph
- The line with the index is the current function being looked at **(primary line)** 
	- Lines above are functions which called this function (callers) 
	- Lines below are functions which were called by this function (callees) 
- **Primary Line** 
	- **time**: total percentage of time spent in this function and it’s children \
	- **self**: same as flat profile 
	- **children**: time spent in all calls made by the function (it should be equal to self + children of all functions below)
#### 1.6.1 Reading the Callers in the Call Graph
- The callers are the functions above the primary line 
- **self**: time spent in primary function, when called from current function 
- **children**: time spent in primary function’s children, when called from current function 
- **called**: number of times primary function was called from current function divided by number of nonrecursive calls to primary function
#### 1.6.2 Reading the Callees in the Call Graph
- The callees are the functions below the primary line 
- **self**: time spent in current function when called from primary function 
- **children**: time spent in current function’s children calls when called from primary function 
	- **Note**: self + children is an estimate of time spent in current function when called from primary function 
- **called**: number of times current function was called from primary function divided by number of nonrecursive calls to current function
## 2. CPUs and Compilers
### 2.1 CPU Architectures History
- How do we measure a single faster core?
	- There are many factors to consider including:
		1. **IPS** (Instructions Per Second, you can prefix with B for billions)
		2. **FLOPS** (Float Point Operations Per Second)
		3. **IPC** (Instructions Per Clock)
		4. **CPI** (Cycles Per Instruction)
			- CPI is the reciprocal of IPC
	- Note: These are all measures of throughput
#### 2.1.1 Intel's First CPU - 4004
- It was a 4-bit processor @ 108KHz with 2300 transistors
- None of the following:
	- Virtual memory
	- Interrupts
	- Pipelining
- It also had 3 stack *registers*
- Continued until the Intel 8086 in 1978, a 16-bit CPU @ 10 MHz (beginning of x86 instruction set)
- Faster clocks meant CPI can stay the same but the end result is faster

- Old CPUs executed instructions sequentially
- **Typical processor units**:
	- IF: Instruction Fetch
	- ID: Instruction Decode
	- EX: Execute
	- MEM: Memory Access
	- WB: Register Write Back
### 2.2 Architecture Techniques
#### 2.2.1 Basic

#### 2.2.2 RISC and Pipelining
- The idea behind pipelining is that every processor unit runs on each cycle
- Once the pipeline is full, ideally CPI = 1
- Note modern pipelines are deep (around 19 for Zen)
#### 2.2.3 Branch Prediction
- Branches cause issues
	- If we're decoding a branch instruction, what's the next one we fetch?
		- It depends on the result
	- Solution 1: Stall
		- Delay the fetch until we know the result
	- It turns out branches are 10-15% of instructions for a typical program
	- Effective CPI >1

- Modern CPUs have very good branch predictors
	- Instead of stalling, make an educated guess (could use tables to store history)
	- The code after the branch executes speculatively (speculative execution)
	- This pushes the CPI back closer to 1 (branch predictors are quite good)
	- Can we get a CPI < 1?
#### 2.2.4 Instruction Level Parallelism
- Multiple instructions execute in the same cycle
- This is possible if the two instructions are independent
	- This makes the decode more complicated
- Assuming the numbers correspond to the sequential order in our program

| Cycle | Instructions (Finished) |
| ----- | ----------------------- |
| 1     | 1, 2                    |
| 2     | 3                       |
| 3     | 4, 5                    |
#### 2.2.5 Out of Order Execution
- Instructions that cause a cache miss are very slow, we can execute others

| Cycle | Instructions (Finished) |
| ----- | ----------------------- |
| 1     | 1, 2                    |
| 2     | 3, 6                    |
| 3     | 4, 5                    |
#### 2.2.6 Simultaneous Multithreading (SMT)
- For normal threaded applications we may have:

| Cycle | Instructions (Finished) |
| ----- | ----------------------- |
| 1     | 1, 2                    |
| 2     | 3                       |
| 3     | 4, 5                    |
|       | Context Switch          |
| 4     | **1**                   |
| 5     | **2**                   |
| 6     | **3, 4**                |
- For SMT (also called Hyperthreading), if resources are ideal, the other thread could use, execute them

| Cycle | Instructions (Finished) |
| ----- | ----------------------- |
| 1     | 1, 2                    |
| 2     | 3, **1**                |
| 3     | 4, **2**                |
| 4     | **3, 4**, 5             |
#### 2.2.7 Architecture Summary
| Technique              | CPI |
| ---------------------- | --- |
| Basic                  | n   |
| Pipelining             | »1  |
| Branch Prediction      | >1  |
| ILP                    | <1  |
| Out of Order Execution | «1  |
| SMT                    | «1  |
### 2.3 The Compiler
- Let's look at the optimizations the compiler performs
- Optimizations relate to performance, avoid doing these by yourself since:
	1. You'll likely waste time
	2. Make your code more unreadable
- Compilers have a host of optimization options, we'll look at `gcc`
#### 2.3.1 GCC Optimization Levels
1. `O1 (-O)`
	- Reduce code size and execution time
	- No optimizations that increase compilation time
2. `-O2`
	- All optimizations except space-speed tradeoff ones
3. `-O3`
	- All optimizations
4. `-O0` (default, plain)
	- Fastest compilation time, debugging performs as expected
5. `-Ofast`
	- All `-O3` optimizations and non-standard compliant optimizations namely `-ffast-math`
	- Turns off exact implementations of IEEE or ISO rules/specifications for math functions
	- Generally, if you don't care about the exact results, you can use this for a speedup
#### 2.3.2 Types of Optimizations
1. **Scalar Optimizations**
	- Are the First Class, ex. Constant Folding
	- `i = 1024 * 1024`
		- The compiler will not emit code that does the multiplication at runtime, it will simply use the computed value
		- `i = 1048576`
		- Enabled at all optimization levels
2. **Common Subexpression Elimination**
	- `-fgcse`
		- Perform a global common subexpression elimination pass 
		- This pass also performs global constant and copy propagation 
		- Enabled with `-O2, -O3`
		- Example:
```c++
// Instead of computing b * c twice, we compute it once and reuse the value in each expression:
a = b * c * g;
d = b * c * d;
```
3. **Constant Propagation**
	- Moves the constant values from definition to use
	- Valid if there's no redefinition of the variable
	- Example:
```c++
/* Without constant propagation: */
int x = 14;
int y = 7 - x / 2;
return y * (28 / x + 2);
/* With constant propagation would produce: */
int x = 14;
int y = 0;
return 0;
```
4. **Copy Propagation**
	- Replaces direct assignments with their values, usually required to run after common subexpression elimination
	- Example:
```c++
/* Without copy propagation: */
int x = x;
int z = 3 + y;
/* With copy propagation would produce: */
int z = 3 + x;
```
5. **Dead Code Elimination**
	- `-fdce`
		- Remove any code that is guaranteed not to execute
		- Enabled at all optimization levels
	- Example:
```c++
// This would not be included in the final executable:
if (0) {
	z = 3 + x;
}
```
6. **Loop Unrolling**
	- `funroll-loops`
		- Unroll any loops with a set number of iterations
	- Example:
```c++
/* Without Loop Unrolling: */
for (int i = 0; i < 4; ++i)
	f(i)
/* With Loop Unrolling: */
f(0)
f(1)
f(2)
f(3)
```
7. **Loop Interchange**
	- `floop-interchange`
	- Example:
```c
for (int i = 0; i < N; ++i) 
	for (int j = 0; j < M; ++j) 
		a[j][i] = a[j][i] * c 
// would be transformed to this: 
for (int j = 0; j < M; ++j) 
	for (int i = 0; i < N; ++i) 
		a[j][i] = a[j][i] * c
// since C is row-major (meaning a[1][1] is beside a[1][2]), the other possibility is column-major
```
8. **Loop Fusion**
	- There is a trade-off here between data locality and loop overhead, the opposite of this is called **Loop Fission**
	- Example:
```c++
for (int i = 0; i < 100; ++i) 
	a[i] = 4 
for (int i = 0; i < 100; ++i) 
	b[i] = 7
// would be transformed to this: 
for (int i = 0; i < 100; ++i) { 
	a[i] = 4 
	b[i] = 7 
}
```
9. Loop-Invariant Code Motion
	- Moves invariants out of the loop
	- Also called **Loop Hoisting**
	- Example:
```c++
for (int i = 0; i < 100; ++i) { 
	s = x * y; 
	a[i] = s * i; 
} 
// would be transformed to this: 
s = x * y; 
for (int i = 0; i < 100; ++i) { 
	a[i] = s * i; 
} 
// This reduces the amount of work we have to do for each iteration of the loop
```
10. Devirtualization
	- `-fdevirtualize`
		- Attempt to convert calls to virtual functions to direct calls
		- Enabled with `-O2, -O3`
		- Virtual functions impose some overhead, for instance in C++, you must read the objects RTTI (run-time type information) then effectively branch to the correct function 
	- Example:
```
class A {
	virtal void m();
};
class B : public A {
	virutal void m();
};
int main(int argc, char *argv[]) {
	std::unique_ptr t(new B); 
	t.m();
};
// could eliminate reading the RTTI and just insert a call to B’s m
```
11. Scalar Replacement of Aggregates
	- `-fipa-sra`
		- Replace references by values when appropriate 
		- Enabled at `-O2` and `-O3`
	- Example:
```
{ 
	std::unique_ptr a(new Apple); 
	std::cout << color(a) << std::endl; 
}
// Would be optimized to:
std::cout << "Red" << std::endl;
// if the compiler knew what color does
```
12. Tail Recursion Elimination
	- `-foptimize-sibling-calls`
		- Optimize sibling and tail recursive calls
		- Enabled at `-O2` and `-O3`
	- Example:
```
int bar(int N) {
	if (A(N)) 
		return B(N);
	else 
		return bar(N); 
}
// We can just replace the call to bar by a goto at the compiler level, this way we avoid having overhead of a function call and increasing our call stack
```
#### 2.3.3 Three Address Code
**Three Address Code (TAC)**: is a representation of intermediate code used by compilers, mostly used for analysis and optimization
- Statements represent one fundamental operation (for the most part, we can consider each operation **atomic**)
- Useful to reason about your program, and easier to read than assembly (as long as you seperate out memory reads/writes)
- Statements have the form: $result := operand_1 \; operator \; operand_2$
#### 2.3.4 GIMPLE
**GIMPLE**: Is the three address code used by `gcc`
- To see the GIMPLE representation of your compilation use the `-fdump-tree-gimple` flag
- To see all of the three address code generated by the compiler use `-fdump-tree-all`, you’ll probably just be interested in the optimized version
- Use this if you want to reason about your code at a low-level without having to read assembly
#### 2.3.5 `restrict`
- `restrict` is a keyword in C/C++

> “A new feature of C99: The restrict type qualifier allows programs to be written so that translators can produce significantly faster executables.”

- For C99 standard in `gcc` use the `-std=c99` flag (we use C23)
- If you declare a pointer with restrict, you are ensuring to the compiler that the pointer will never **alias** (another pointer will not point to the same data) for the lifetime of the pointer
- If you have a bunch of pointers declared with `restrict`, you are saying that these will never point to the same data

- Use `restrict` whenever you know the pointer will not alias another pointer (also declare as `restrict`)
	- The compiler may be not able to know whether pointers alias, so you must provide this
- This allows the compiler to do better optimization for your code (and therefore run faster)
	- Caveat: don’t lie to the compiler, or else you will get **undefined behaviour**
- Aside: this not the same as `const`, `const` data can still be changed through a different pointer
#### 2.3.6 Aliasing and Pointer Analysis
- We’ve seen using restrict to tell the compiler variables do not alias
- *Pointer analysis* tracks the variables in your program to determine whether or not they alias
	- If they don’t alias, we can reorder them and do other types of optimizations
#### 2.3.7 Call Graph
- A directed graph that shows relationships between functions
- Relativity simple in C, hard for virtual function calls (C++/Java)
- Virtual calls require pointer analysis
- Having the call graph allows us to know if the following can be optimized:
```c++
int n; 
int f() { /* opaque */ } 
int main() { 
	n = 5; 
	f(); 
	printf("%d\n", n); 
}
```
- We could propagate the constant value 5, as long as we know that `f()` does not write to `n`
#### 2.3.8 Branch Predictions
- `GCC` attempts to guess the probability of branches in order to do the best code ordering
	- You can use `__builtin_expect(expr, value)` to help GCC, if you know the run-time characteristics of your program
#### 2.3.9 Architecture Specific
- Two common ones `march` and `mtune` (`march` implies `mtune`)
	- These enable using specific instructions that not all CPUs may support (`AVX512`, etc.)
- Example: `-march=arrowlake` 
	- Good to use on your local machine, not so much for shipped code
#### 2.3.10 Resources
- [**Full List of Optimization Options**](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html)
- [**Compiler Explorer**](https://godbolt.org/)
## 3. Performance Examples
### 3.1 About C Code
- Common things in C code:
	- `define macros`
	- `void*`
- The language itself is not very large, its also low level
- C++ since C++11 has made major strides toward readability and efficiency (light-weight abstractions)
### 3.2 Inlining
- One of the most impactful compiler optimizations is **Inlining**
	- Example:
```c++
class Point { 
public: 
	int getX() { 
		return x; 
	} 
private: 
	int x; 
}; 
int main(void) { 
	Point p = /* ... */; 
	std::cout << p.getX() << std::endl; 
} 
// would get optimized to: 
int main(void) { 
	Point p = /* ... */;
	std::cout << p.x << std::endl; 
}
```
- Tradeoff:
	- You will avoid the overhead of a function call and return
	- However, the program size may increase
		- At runtime you may have worse performance due to instruction cache
	- Inlining may allow other compiler optimizations to happen as well
	- The `inline` keyword is a suggestion to the compiler, it can ignore you
### 3.3 Vector vs List Problem
- Generate **N** random integers and insert them into (sorted) sequence
	- Example: 3, 4, 2, 1
3
3 4
2 3 4
1 2 3 4
- Remove **N** elements one at a time by going to a random position and removing the element
	- Example: 2, 0, 1, 0
1 2 4
2 4
2
- For which **N** is better to use a list than a vector (or array)?
#### 3.3.1 Theoretical Complexity
**Vector**
- Inserting
	- $O(logn)$ for binary search
	- $O(n)$ for insertion (on average, move half the elements)
- Removing
	- $O(1)$ for accessing
	- $O(n)$ for deletion (on average, move half of the elements)
**List**
- Inserting
	- $O(n)$ for linear search
	- $O(1)$ for insertion
- Removing
	- $O(n)$ for accessing
	- $O(1)$ for deletion

- Therefore, based on their complexity, lists should be better
#### 3.3.2 Reality of Vectors and Lists
- Vectors dominate lists performance wise
	- Binary search vs. linear search complexity dominates
	- The amount of memory lists use is far higher
- 64 bit machines:
	- Vector: 4 bytes per element
	- List: At least 29 bytes per element
- Memory access is slow and indirect and comes in blocks
	- Lists are all over memory, so there is a large number of cache misses
	- A cache miss for a vector will bring a lot more usable data
### 3.4 `perf`
- `perf` is a Linux specific profiler that lets you access hardware counters
- You can run it before any command, some good arguments:
```powershell
perf stat -B -e task-clock,cycles,instructions 
perf stat -B -e cache-references,cache-misses,branches,branch-misses,page-faults
```
### 3.5 More Performance Tips
- Don't store unnecessary data in your program
- Keep your data as compact as possible
- Access memory in a predictable manner
- Use vectors instead of lists by default
- Programming abstractly can save a lot of time

- Giving the compiler more information produces better code 
- Data structures can be very important, more so than complexity
- Low-level code != Efficient
- You should think at a low level if you need to optimize anything
- Readable code is good code to start with (different hardware will have different optimizations)
## 4. Program Optimizations
### 4.1 Combining Data
- We want to be generic, to combine we’ll accumulate the result of an operation between all elements of the vector
- It’ll have an operation, OP, and identity value IDENT, where x OP IDENT == IDENT
- For for a sum, OP is + and IDENT is 0
- For for a product, OP is * and IDENT is 1
### 4.2 Side Notes
- The compiler did not lift `vec_length` out of the loop
	- Since it’s in a different compliation unit, the compiler has to assume it has side effects and it may not be deterministic
	- Side effects may include reading or writing global state that may change
		- For example, if you did a `printf` in `vec_length` moving it would change the behaviour of your program
	- Also, without knowing the final link step, even if it knew the implementation, you may not actually use that function at runtime

- Don't overuse pointers in C/C++
	- It’s very difficult for the compiler to reason about raw pointers (especially when basically anything is possible)
	- You should use local variables whenever possible
		- The compiler can reason about the lifetime of local variables
	- Only update global state when you have to

- Some of your biggest optimizations come from domain-specific knowledge
	- For example, what if we were computing the product of all elements?
	- Is there an optimization we could make under certain conditions to skip the calculation?
### 4.3 Takeaways
- Always profile to make sure you’re optimizing the right thing!
- Get the most out of your compiler before going manual
	- Trade-off: manual optimization vs readable/maintainable code
- Limit use of pointers, prefer local variables
	- Reduce pointer-based arrays and pointer arithmetic
	- Function pointers and virtual functions (unfortunately)
- For highly performance-critical code:
	- Look at assembly for optimization opportunities
	- Consider the instruction-parallelism capabilities of CPU
## 5. Advanced Compiler Usage
### 5.1 Other Compilers
- There's more than one compiler
	- Clang is another compiler that uses LLVM as an optimizer
	- LLVM IR is in SSA (single static assignment) form, and is quite readable
#### 5.1.1 Clang
- Pretty much every build system the environment variable
- `CC` specifies the C compiler
- You can set it for just a command:
```powershell
CC=clang meson setup build 
meson compile -C build
```
- Note: CXX is the C++ compiler
### 5.2 Link-time Optimization (LTO)
- This helps the compiler optimize at more global level
- It keeps the code at an IR level for optimization, until the final linking step
- The compiler doesn’t have to assume the worst for functions you write in other source files
- The compiler generates machine code as last as possible
#### 5.2.1 Using LTO with Meson
- Messing with the flags is a bit of a pain with other build tools
- To use LTO, set the following:
```powershell
meson setup -Db_lto=true build
meson compile -C build
```
- Clang is much more aggressive with LTO
- You should prefer to use LTO
	- It takes more time and more memory than traditional builds (LLVM also supports thinLTO for large builds that run out of memory)
	- Also, things can go very poorly if you mess with linking, or lie to the compiler
	- Since, as we saw, it can remove a lot of code, you should absolutely not use it for development / debugging
### 5.3 Profile Guided Optimization
- Your compiler can also use profiling data to generate better code
- It’s especially useful for knowing how to organize code blocks in memory
- Thankfully, it’s also an option for Meson, `b_lto`, the options are: `off`, `generate`, and `use`
- First, we need to compile with generate:
```powershell
meson setup -Db_pgo=generate build
```
### 5.4 Generating and Using Profiling Data
- It’s best to include benchmarks for your project
	- Your benchmarks should be representative workloads
- After, we can compile with generate, benchmark, and re-compile:
```powershell
cd build ninja # Compile 
ninja benchmark # Run all the benchmarks 
meson configure -Db_pgo=use # Change setting to use the profile data 
ninja # Re-compile
```
- Just like running with `gcov`, it’ll generate `.gcda` files
- Note: don’t be like me, your program must call `exit` to generate `.gcda` files
### 5.5 Summary
- LTO and PGO may save you a lot of manual effort
	- It gives your compiler much more information it can use
	- You’ll likely have better performance gains from applying domain-specific knowledge (the fastest computation is a skipped one)
	- Again, your compiler will not pick data structures or data types for you
		- This is also where you’ll get a lot of your performance