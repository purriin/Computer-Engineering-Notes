### 1. Static Allocation
- Static allocation is the simplest strategy
- Create a fixed size allocation in your program 
	- e.g. `char buffer[4096]; `
- When the program loads, the kernel sets aside that memory for you 
- That memory exists as long as your process does, no need to free
### 2. Dynamic Allocation
- Dynamic allocation is often required
	- You may only conditionally require memory 
		- Static allocations are sometimes wasteful 
	- You may not know the size of the allocation 
		- Static allocations need to account for the maximum size 
	- Where do you allocate memory? 
		- You can either allocate on the stack or on the heap

- Stack allocations is mostly done for you in C
	- Think of normal variables 
		- e.g. `int x;` 
	- The compiler internally inserts `alloca` calls 
		- e.g. `int *px = (int*) alloca(4); `
	- Whenever the function that called `alloca` returns, it frees all the memory 
		- This just restores the previous stack pointer 
	- This won’t work if you try to use the memory after returning

- Dynamic allocation uses the `malloc` family of functions
	- The most flexible way to use memory, but is also the most difficult to get right 
	- You have to properly handle your memory lifetimes, and free exactly once 
	- Also, there’s a new concern — fragmentation
### 3. Fragmentation
- Fragmentation is a unique issue for dynamic allocation
- You allocate memory in different sized contiguous blocks 
	- Compaction is not possible and every allocation decision is permanent 
- A fragment is a small contiguous block of memory that cannot handle an allocation 
	- You can think of it as a “hole” in memory, wasting space 
- There are 3 requirements for fragmentation:
	1. Different allocation lifetimes
	2. Different allocation sizes
	3. Inability to relocate previous allocations
#### 3.1 External Fragmentation
- External fragmentation occurs when you allocate different sized blocks 
- There’s no room for an allocation between the blocks
#### 3.2 Internal Fragmentation
- Internal fragmentation occurs when you allocate fixed sized blocks 
- There’s wasted space within a block

- We want to minimize fragmentation
- Fragmentation is just wasted space, which we should prevent 
- We want to reduce the number of “holes” between blocks of memory 
	- If we have holes, we’d like to keep them as large as possible 
- Our goal is to keep allocating memory without wasting space
### 4. Implementations
- Allocator implementations usually use a free list
- They keep track of free blocks of memory by chaining them together 
	- Implemented with a linked list 
- We need to be able to handle a request of any size 
- For allocation, we choose a block large enough for the request 
	- Remove it from the free list 
- For deallocation, we add the block back to the free list 
	- If it’s adjacent to another free block, we can merge them
#### 4.1 Allocation Strategies
- There are 3 general heap allocation strategies:
	1. Best fit: choose the smallest block that can satisfy the request 
		- Needs to search through the whole list (unless there’s an exact match) 
	2. Worst fit: choose largest block (most leftover space) 
		- Also has to search through the list 
	3. First fit: choose first block that can satisfy request

- Best fit and worst fit are both slow
	- Best fit: tends to leave very large holes and very small holes 
		- Small holes may be useless 
	- Worst fit: simulation says it’s the worst in terms of storage utilization 
	- First fit: tends to leave “average” size holes
### 5. Buddy Allocator
- The Buddy Allocator restricts the problem
	- Typically, allocation requests are of size $2^n$
	- Restrict allocations to be powers of 2 to enable a more efficient implementation 
		- Split blocks into 2 until you can handle the request 
	- We want to be able to do fast searching and merging
- You can implement the Buddy Allocator using multiple lists
	- We restrict the requests to be $2^k$ , 0 ≤ k ≤ N (round up if needed)
	- Our implementation would use N + 1 free lists of blocks for each size
	- For a request of size $2^k$ , search the free list until we find a big enough block
		- Search k, k + 1, k + 2, ... until we find one 
		- Recursively divide the block if needed until it’s the correct size 
		- Insert “buddy” blocks into free lists
	- For deallocations, we coalesce the buddy blocks back together 
		- Recursively coalesce the blocks if needed
- Buddy Allocators are used in Linux
	- Advantages 
		- Fast and simple compared to general dynamic memory allocation 
		- Avoids external fragmentation by keeping free physical pages contiguous
	- Disadvantages 
		- There’s always internal fragmentation 
		- We always round up the allocation size if it’s not a power of 2
### 6. Slab Allocators
- Slab Allocators take advantage of fixed size allocations
	- Allocate objects of same size from a dedicated pool 
		- All structures of the same type are the same size 
	- Every object type has it’s own pool with blocks of the correct size 
		- This prevents internal fragmentation
- Slab is a cache of "Slots"
	- Each allocation size has a corresponding slab of slots (one slot holds one allocation) 
	- Instead of a linked list, we can use a bitmap (there’s a mapping between bit and slot) 
		- For allocations, we set the bit and return the slot 
		- For deallocations, we just clear the bit 
	- The slab can be implemented on top of the buddy allocator
- Each slab can be allocated using the Buddy Allocator
	- We can reduce internal fragmentation if Slabs are located adjacently