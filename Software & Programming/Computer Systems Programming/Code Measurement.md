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
```
meson setup -Db_coverage=true build 
meson compile -C build 
ninja -C build coverage
```
#### 1.4.2 `gprof` Usage
- Use the `-pg` flag with `gcc` when compiling (also linking)
- Run your program as you normally would (it now generates a `gmon.out` file) 
- Use `gprof` to interpret the results `gprof <executable` 
- Example commands using `meson`, assuming the output `calc`: 
```
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
