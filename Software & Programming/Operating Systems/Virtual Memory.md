## 1. Virtual Memory Checklist
- [ ] Multiple processes must be able to co-exist 
- [ ] Processes are not aware they are sharing physical memory 
- [ ] Processes cannot access each others data (unless allowed explicitly) 
- [ ] Performance close to using physical memory 
- [ ] Limit the amount of fragmentation (wasted memory)
## 2. Segmentation
 - Remember, memory is byte addressable
	- The smallest unit you can use to address memory is one byte 
	- You can read or write one byte at a time at minimum 
	- Each “address” is like an index of an array

- Segmentation or segments are coarse grained
	- Divide the virtual address space into segments for: code, data, stack, and heap 
		- Note: this looks like an ELF file, large sections of memory with permissions 
	- Each segment is a variable size, and can be dynamically resized 
		- This is an old legacy technique that’s no longer used 
	- Segments can be large and very costly to relocate 
		- It also leads to fragmentation (gaps of unused memory) 
	- No longer used in modern operating systems
### 2.1 Segmentation Details
- Each segment contains a: base, limit, and permissions 
	- You get a physical address by using: segment selector:offset 
- The MMU checks that your offset is within the limit (size) 
	- If it is, it calculates base + offset, and does permission checks 
	- Otherwise, it’s a segmentation fault 
- For example 0x1:0xFF with segment 0x1 base = 0x2000, limit = 0x1FF 
	- Translates to 0x20FF 
- Note: Linux sets every base to 0, and limit to the maximum amount
First Insight: Divide Memory into Fixed-Sized Chunks
## 3. Memory Management Unit (MMU)
**Memory Management Unit (MMU)**: Maps virtual address to physical address. Also checks permissions
- One technique is to divide memory up into fixed-size pages (typically 4096 bytes)
	- A page in virtual memory is called a page 
	- A page in physical memory is called a frame
- You typically do not use all 64 virtual address bits
	- CPUs may have different levels of virtual addresses you can use 
		- Implementation ideas are the same 
	- We’ll assume a 39 bit virtual address space used by RISC-V and other architectures 
		- Allows for 512 GiB of addressable memory (called Sv39) 
	- Implemented with a page table indexed by Virtual Page Number (VPN) 
		- Looks up the Physical Page Number (PPN)
- The Page Table Translates Virtual to Physical Addresses
- The Page Table Entry (PTE) Also Stores Flags in the Lower Bits
- The Kernel Handles Translating Virtual Addresses
- Each Process Gets Its Own Page Table
	- When you fork a process, it will copy the page table from the parent 
		- Turn off the write permission so the kernel can implement copy-on-write 
	- The problem is there are 227 entries in the page table, each one is 8 bytes 
		- This means the page table would be 1 GiB 
	- Note that RISC-V translates a 39-bit virtual to a 56-bit physical address
		- It has 10 bits to spare in the PTE and could expand 
		- Page size is 4096 bytes (size of offset field)
- `vfork` shares all memory with the parent It’s undefined behavior to modify anything 
	- Only used in very performance sensitive programs
## 4. Page Tables
- We can make our page table fit on a page
- Multi-level page tables save space for sparse allocations
- Page allocation uses a free list
	- Given physical pages, the operating system maintains a free list ([linked list](Linked%20Lists.md))
- The unused pages themselves contain the next pointer in the free list 
	- Physical memory gets initialized at boot 
- To allocate a page, you remove it from the free list 
	- To deallocate a page you add it back to the free list
- Insight: use a page for each smaller page table
	- There are 512 ($2^9$) entries of 8 bytes($2^3$) each, which is 4096 bytes 
	- The PTE for L(N) points to the page table for L(N-1) 
	- You follow these page tables until L0 and that contains the PPN
- Smaller page tables are just like arrays

- Processes use a register like `satp` to set the root page table
- Alignment: Memory eventually lines up with byte 0

### 4.1 How Many Levels do I need?
- We want each page table to fit into a single page
- log2(#PTEs per Page) is the number of bits to index a page table
$n_{levels} = \frac{virtual - offset}{index}$ where virtual, offset and index are bits

- Using the page tables for every memory access is slow
	- We need to follow pointers across multiple levels of page tables! 
	- We’ll likely access the same page multiple times (close to the first access time) 
	- A process may only need a few VPN → PPN mappings at a time 
	- Our solution is another computer science classic: caching

### 4.2 Effective Access Time (EAT)
Assume a single page table (there’s only one additional memory access in the page table) 
- TLB_Hit_Time = TLB_Search + Mem
- TLB_Miss_Time = TLB_Search + 2 × Mem
- EAT = α × TLB_Hit_Time + (1 − α) × TLB_Miss_Time

- Context switches require handling the TLB
	- You can either flush the cache, or attach a process ID to the TLB 
	- Most implementation just flush the TLB 
		- RISC-V uses a sfence.vma instruction to flush the TLB 
	- On x86 loading the base page table will also flush the TLB

- Use `sbrk` for userspace allocation
	- This call grows or shrinks your heap (the stack has a set limit) 
	- For growing, it’ll grab pages from the free list to fulfill the request 
		- The kernel sets PTE_V (valid) and other permissions 
	- In memory allocators this is difficult to use, you’ll rarely shrink the heap 
		- It’ll stay claimed by the process, and the kernel cannot free pages 
	- Memory allocators use mmap to bring in large blocks of virtual memory

- The kernel can provide fixed virtual addresses
	- It allows the process to access kernel data without using a system call 
	- For instance `clock_gettime` does not do a system call 
		- It just reads from a virtual address mapped by the kernel

- Page faults allow the operating system to handle virtual memory
	- Page faults are a type of exception for virtual memory access 
		- Generated if it cannot find a translation, or permission check fails 
	- This allows the operating system to handle it 
		- We could lazily allocate pages, implement copy-on-write, or swap to disk
