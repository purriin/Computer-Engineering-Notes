### 1. Hierarchy
- Computer memory hierarchy is a trade-off of capacity and speed
	1. CPU
	2. CPU Cache
	3. Memory (RAM)
	4. Non-volatile Memory (NVMe)
	5. SATA Solid State Disk (SSD)
	6. Hard Disk Drive (HDD)
	7. Tape Drives
- We want to hide hierarchy from the user
	- Each level wants to pretend it has the speed of the layer above it and the capacity of the layer below it
	- The memory used by all the processes my exceed the amount of physical memory 
		- Not all of them may be in use at the same time
	- Only keep referenced pages in memory, put others on disk 
		- Swap pages back to memory when they’re needed
- Demand paging
	- We use memory as a cache for the file system
	- Map memory pages to file system blocks 
		- Only load them into memory when they’re used through the page fault handler
	- If the page doesn’t represent a file, we can map it to swap space 
		- Move it to disk if we need to use more physical memory
- A process' working set should fit in physical memory
	- Given an amount of time, the number of pages your process uses is called its *working set*
	- If you cannot fit your working set into physical memory your process will *thrash* 
		- It’s constantly moving entries in and out of cache
### 2. Page Replacement Algorithms
1. Optimal 
	- Replace the page that won’t be used for the longest 
2. Random 
	- Replace a random page 
3. First-in First-out (FIFO) 
	- Replace the oldest page first
	- Problems with FIFO:
		- Bélády’s Anomaly says more page frames cause more faults, this is a problem with FIFO algorithms
		- Does not exist with LRU or “stack-based algorithms”
		- [Paper in 2010](https://arxiv.org/abs/1003.1336) found that this FIFO anomaly is unbounded
			- You could construct a sequence to get any arbitrary page fault ratio
		- For other algorithms: increasing the number of page frames decreases the number of page faults
4. Least Recently Used (LRU) 
	- Replace the page that hasn’t been used in the longest time
	- Problem with LRU:
		- Implementing LRU in hardware has to search all pages
			- You could implement it by keeping a counter for each page 
			- For each page reference, save the system clock into the counter 
			- For replacement, scan through the pages and find the one with the oldest clock
		- Implementing LRU in software is too expensive
			- Create a doubly linked list of pages 
			- For each page reference, move it to the front of the list 
			- For replacement, remove from the back of the list 
			- It requires 6 pointer updates for each page reference, and also creates a high contention bottleneck for multiple processors
		- Implementing LRU in practice isn't going to work
			- We settle for approximate LRU 
				- LRU is an approximation of the optimal case anyways 
			- There’s lots of different tweaks you can do to implement it more efficiently 
			- We’ll be looking at the clock algorithm, but there’s also: 
				- Least Frequently Used (LFU), 2Q, Adaptive Replacement Cache (ARC)
### 3. Clock Page Replacement
#### 3.1 Clock Page Algorithm
- Is an approximation of LRU
- Data structures: 
	- Keeps a circular list of pages in memory 
	- Uses a reference bit for each page in memory
	- Has a “hand” (iterator) pointing to the last element examined \
- Algorithm, to insert a new page:
	- Check the hand’s reference bit, if it’s 0 then place the page and advance hand
	- If the reference bit is 1, set it to 0, advance the hand, and repeat 
- For page accesses, set the reference bit to 1
#### 3.2 Other
- For performance you may choose to disable swapping
- Memory is cheap, and has quite high capacity 
	- You’d rather know you need more memory than run slowly 
		- Linux runs an OOM (out of memory) killer, that SIGKILLs the memory hog 
- Larger page sizes allow for speedups (2 MiB or 1 GiB page size) 
	- Trade more fragmentation for more TLB coverage