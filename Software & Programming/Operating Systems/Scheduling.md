## 1. Basic Scheduling
**Preemptible Resource**: can be taken away and used for something else
- Resource is shared through scheduling
- Ex. CPU
**Non-Preemptible Resource**: can not be taken away without acknowledgment
- The resource is shared through allocations and deallocations
	- Note: Parallel and distributed systems may allow you to allocate a CPU
- Ex. Disk Space

**Dispatcher**: is a low level mechanism responsible for context-switching
**Scheduler**: is a high-level policy responsible for deciding which processes to run
- A dispatcher and scheduler work together
- The scheduler runs whenever a process changes state
	- First let’s consider non-preemptable processes 
		- Once the process starts, it runs until completion 
		- In this case, the scheduler will only make a decision when the process terminates 
	- Preemptive allows the operating system to run the scheduler at will 
	- Check `uname -v`, your kernel should tell you it’s preemptable
### 1.1 Metrics
1. Minimize waiting time and response time
	- Don’t have a process waiting too long (or too long to start)
2. Maximize CPU utilization 
	- Don’t have the CPU idle 
3. Maximize throughput 
	- Complete as many processes as possible 
4. Fairness 
	- Try to give each process the same percentage of the CPU
### 1.2 First Come First Served (FCFS)
- The most basic form of scheduling 
- The first process that arrives gets the CPU 
- Processes are stored in a FIFO queue in arrival order
### 1.3 Shortest Job First (SJF)
- A slight tweak to FCFS, we always schedule the job with the shortest burst time first 
- We’re still assuming no preemption
- Minimizes average wait time over FCFS
- Is not practical
	- It is probably optimal at minimizing average wait time (if no preemption) 
	- You will not know the burst times of each process 
		- You could use the past to predict future executions 
	- You may starve long jobs (they may never execute)
### 1.4 Shortest Remaining Time First (SRTF)
- Changing SJF to run with preemptions requires another tweak 
- We’ll assume that our minimum execution time is one unit 
- Similar to SJF, this optimizes the average waiting time
### 1.5 Round Robin (RR)
- So far we haven’t handled fairness (it’s a trade off with other metrics) 
- The operating system divides execution into time slices (or quanta)]
	- An individual time slice is called a quantum 
- Maintain a FIFO queue of processes similar to FCFS 
	- Preempt if still running at end of quantum and re-add to queue
- Note: on ties (a new process arrives while one is preempted), favor the new one
- RR Performance depends on quantum length and job length
	- RR has low response good interactivity 
		- Fair allocation of CPU 
		- Low average waiting time (when job lengths vary) 
	- The performance depends on the quantum length 
		- Too high and it becomes FCFS 
		- Too low and there’s too many context switches (overhead) 
	- RR has poor average waiting time when jobs have similar lengths
## 2. Advanced Scheduling
- We could add priorities
- We may favor some processes over others 
	- Assign each process a priority 
- Run higher priority processes first, round-robin processes of equal priority 
	- Can be preemptive or non-preemptive

- Priorities can be assigned an integer
	- We can pick a lower, or higher number, to mean high priority 
		- In Linux -20 is the highest priority, 19 is the lowest 
	- We may lead processes to starvation if there’s a lot of higher priority processes 
	- One solution is to have the OS dynamically change the priority 
		- Older processes that haven’t been executed in a long time increase priority

- Priority inversion is a new issue
	- We can accidentally change the priority of a low priority process to a high one 
		- This is caused by dependencies, e.g. a high priority depends a low priority 
	- One solution is **priority inheritance** 
		- Inherit the highest priority of the waiting processes 
		- Chain together multiple inheritances if needed 
		- Revert back to the original priority after dependency

- A foreground process can receive user input, background process cannot
	- Unix background process when: process group ID differs from its terminal group ID 
		- You do not need to know this specific definition 
	- The idea is to separate processes that users interact with: 
		- **Foreground processes** are interactable and need good response time 
		- **Background processes** may not need good response time, just throughput

- We can use multiple queues for other purposes
	- We could create different queues for foreground and background processes: 
		- Foreground uses RR 
		- Background uses FCFS 
	- Now we have to schedule between queues! 
		- RR between the queues 
		- Use a priority for each queue

- Scheduling can get complicated
	- There’s no “right answer”, only trade-offs 
	- We haven’t talked about multiprocessor scheduling yet 
	- We’ll assume symmetric multiprocessing (SMP) 
		- All CPUs are connected to the same physical memory 
		- The CPUs have their own private cache (at least the lowest levels)

- One approach is to use the same scheduling for all CPUs
	- There’s still only one scheduler
	- It just keeps adding processes while there’s available CPUs 
	- Advantages:
		- Good CPU utilization 
		- Fair to all processes 
	- Disadvantages: 
		- Not scalable (everything blocks on global scheduler) 
		- Poor cache locality 
	- This was the approach in Linux 2.4

- We Can Create Per-CPU Schedulers 
	- When there’s a new process, assign it to a CPU 
	- One strategy is to assign it to the CPU with the lowest number of processes 
	- Advantages:
		- Easy to implement 
		- Scalable (there’s no blocking on a resource) 
		- Good cache locality 
	- Disadvantages:
	- Load imbalance 
	- Some CPUs may have less processes, or less intensive ones


- We Can Compromise between Global and Per-CPU 
	- Keep a global scheduler that can rebalance per-CPU queues 
	- If a CPU is idle, take a process from another CPU (work stealing) 
	- You may want more control over which processes can switch 
	- Some may be more sensitive to caches 
	- Use processor affinity 
	- The preference of a process to be scheduled on the same core 
	- This is a simplified version of the O(1) scheduler in Linux 2.6

- Another Strategy is **“Gang” Scheduling** 
	- Multiple processes may need to be scheduled simultaneously 
	- The scheduler on each CPU cannot be completely independent 
	- “Gang Scheduling” (Coscheduling) Allows you to run a set of processes simultaneously (acting as a unit) 
	- This requires a global context-switch across all CPUs

- Real-Time Scheduling is Yet Another Problem 
	- Real-time means there are time constraints, either for a deadline or rate e.g. audio, autopilot 
	- A hard real-time system 
		- Required to guarantee a task completes within a certain amount of time 
	- A soft real-time system 
		- Critical processes have a higher priority and the deadline is met in practice 
		- Linux is an example of soft real-time

- Linux Also Implements FCFS and RR Scheduling 
	- You can search the source tree: FCFS (SCHED_FIFO) and RR (SCHED_RR) 
	- Use a multilevel queue scheduler for processes with the same priority 
	- Also let the OS dynamically adjust the priority 
	- Soft real-time processes: Always schedule the highest priority processes first 
	- Normal processes: Adjust the priority based on aging

- Real-Time Processes Are Always Prioritized 
	- The soft real-time scheduling policy will either be SCHED_FIFO or SCHED_RR 
	- There are 100 static priority levels (0—99) 
	- Normal scheduling policies apply to the other processes (SCHED_NORMAL) 
	- By default the priority is 0 Priority ranges from [−20, 19] 
	- Processes can change their own priorities with system calls: nice, sched_setscheduler

- Linux Priority Unifies Soft Real-time and Normal Processes

- Linux Scheduler Evolution 
	- 2.4—2.6, a O(N) global queue Simple, but poor performance with multiprocessors and many processes 
	- 2.6—2.6.22, a per-CPU run queue, O(1) scheduler Complex to get right, interactivity had issues No guarantee of fairness 
	- 2.6.23—Present, the completely fair scheduler (CFS) Fair, and allows for good interactivity

- The O(1) Scheduler Has Issues with Modern Processes 
	- Foreground and background processes are a good division 
	- Easier with a terminal, less so with GUI processes 
	- Now the kernel has to detect interactive processes with heuristics 
	- Processes that sleep a lot may be more interactive 
	- This is ad hoc, and could be unfair 
	- How would we introduce fairness for different priority processes? 
		- Use different size time slices 
	- The higher the priority, the larger the time slice 
	- There are also situations where this ad hoc solution could be unfair
### 2.1 Ideal Fair Scheduling (IFS)
- Assume you have an infinitely small time slice If you have n processes, each runs at 1/n rate
- CPU usage is divided equally among every process

- IFS is the Fairest but Impractical Policy 
- This policy is fair, every process gets an equal amount of CPU time 
- Boosts interactivity, has the ideal response time 
- However, this would perform way too many context switches You have to constantly scan all processes, which is O(N)
### 2.2 Completely Fair Scheduler (CFS) 
- For each runnable process, assign it a “virtual runtime” 
- At each scheduling point where the process runs for time t Increase the virtual runtime by t × weight (based on priority) 
- The virtual runtime monotonically increases 
- Scheduler selects the process based on the lowest virtual runtime 
- Compute its dynamic time slice based on the IFS 
- Allow the process to run, when the time slice ends repeat the process

- CFS is Implemented with [Red-Black Trees](Trees.md)
- A red-black tree is a self-balancing binary search tree Keyed by virtual runtime O(lgN) insert, delete, update, find minimum 
- The implementation uses a red-black tree with nanosecond granularity 
- Doesn’t need to guess the interactivity of a process CFS tends to favour I/O bound processes by default 
- Small CPU bursts translate to a low virtual runtime It will get a larger time slice, in order to catch up to the ideal
## 3. Priority Scheduling and Memory Mapping  
### 3.1  Dynamic Priority Scheduling  
This may also be called: **Feedback Scheduling**  
- We let the algorithm manage the priorities  
- We use set time slices, and measure CPU usage  
- Increase the priority of processes that don’t use their time slice  
- Decrease the priority of processes that use their full time slice  
- We pick the lowest number as highest priority  
- Each process gets assigned a priority when started, `Pn`  
- Pick the lowest priority number to schedule  
	- If it yields, pick the next lowest number  
	- Break ties with arrival order  
	- If a lower priority number becomes ready, switch to it  
- Record how much time each process executes for in this priority interval, `Cn`  
- Timer interrupts still occur  
- At the end of the priority interval:  
	- Update the priority of each process  
	- Reset the value of `Cn` back to 0  
### 3.2 We Can Control Our Processes’ Virtual Memory  
- `mmap` is used to map files to a process’s virtual address space  
- The pointer (virtual address) returned allows direct file access  
- Eliminates the need for `read` and `write` system calls  
### 3.3 The `mmap` API  
`mmap` takes 6 arguments:  
1. `void *addr`: suggested starting address (`NULL` means you don’t care)  
2. `size_t length`: number of bytes to map  
3. `int prot`: protection flags (read/write/execute)  
4. `int flags`: mapping flags (shared/private/anonymous)  
   - `anonymous` means the mapping isn’t backed by a file  
5. `int fd`: file descriptor to map (ignored for anonymous)  
6. `off_t offset`: offset to start the mapping (must be a multiple of page size)  

- `mmap` is lazy
- It just sets up the page tables  
- It doesn’t actually read from the file  
- It creates an invalid PTE during the `mmap` call  
- The kernel uses the remaining bits of the PTE for bookkeeping  
  - Where on the disk is this entry  
- The first access to the page generates a page fault  
- The kernel reads from disk into memory  
- Ensures only the used parts of the file get read  

### 3.4 How Much Space Would the Kernel Need for Page Tables?  
- Someone posted you’d need 40 MB of page tables  
- Someone clarified the calculation  
- Is this correct? Why or why not?  
### 3.5 How Much Space Do Our Page Tables Need In the Best Case?  
- These are how many PTEs we need across only the L0 page tables  
- Each L1 page table can point to 512 L0 page tables  
- So we’d need 10260 full page tables  