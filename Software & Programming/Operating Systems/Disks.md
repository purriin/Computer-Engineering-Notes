### 1. Introduction
#### 1.1 Solid State Drives (SSD)
- Solid State Drives (SSD) Are More Modern
	- Use transistors (like RAM) to store data rather than magnetic disks
	**Pros:**
	- No moving parts or physical limitations  
	- Higher throughput, and good random access  
	- More energy efficient  
	- Better space density  
	**Cons:**
	- More expensive  
	- Lower endurance (number of writes)  
	- More complicated to write drivers for  
- SSDs Using NAND Flash Are Much Faster Than HDDs
	- Pages are typically 4 KiB  
	- Reading a page: 10 μs  
	- Writing a page: 100 μs  
	- Erasing a block: 1 ms  
### 1.2 NAND Flash Programming
- NAND Flash Programming Uses Pages and Blocks
	- You can only read complete pages and write to freshly erased pages  
	- Erasing is done per block (a block has 128 or 256 pages)  
	- An entire block needs to be erased before writing  
	- Writing is slow (may need to create a new block)  
- The OS Can Help Speed Up SSDs
	- SSDs need to garbage collect blocks  
	- Move any pages that are still alive to a new block (may be overhead)  
	- The disk controller doesn’t know what blocks are still alive  
	- SSD may think the disk is full, even if a file was deleted  
	- The OS can use the **TRIM** command to inform the SSD a block is unused  
	- The SSD can then freely erase the block without moving overhead  
- So Far We’ve Been Talking About Single Devices
	- Sometimes called **Single Large Expensive Disk (SLED)**  
	  - Just one large disk for data  
	  - Single point of failure  
	- There’s also **Redundant Array of Independent Disks (RAID)**  
	  - Data distributed on multiple disks  
	  - Use redundancy to prevent data loss  
	  - Use redundancy to increase throughput  
## 2. RAID 0
- RAID 0 is Called a Striped Volume
- Data stripes (e.g., 128KB and 256KB) are distributed over disks  
- RAID 0 is For Performance Only
- Data is stripped across all disks in the array (can be more than 2)  
**Pro:**  
- Faster parallel access, roughly N times speed  
**Con:**  
- Any disk failure results in data loss (more points of failure)  
### 3. RAID 1
- RAID 1 Mirrors All Data Across All Disks
- RAID 1 is Simple, But Wasteful
- Every disk in the array has a mirrored copy of all the data  
**Pro:**  
- Good reliability: as long as one disk remains, no data loss  
- Good read performance  
**Con:**  
- High cost for redundancy  
- Write performance is the same as a single disk  
### 4. RAID 4
-  RAID 4 introduces **parity**
	- Data stripes distributed over disks with a dedicated parity disk  
	- Parity stores xor (⊕) of copies 1–3  
	- Any one copy can be reconstructed  
- RAID 4 can use the parity drive to recover
	- With parity, we can use (N − 1) / N of the available space  
	- Requires at least 3 drives  
**Pro:**  
- We get (N − 1) times performance (excluding parity disk)  
- We can replace a failed disk and rebuild  
**Con:**  
- Write performance can suffer (must write to parity disk every time)  
### 5. RAID 5
- RAID 5 distributes parity across all disks
	- Data stripes distributed over disks  
	- Each disk takes turns storing the parity blocks  
- RAID 5 is an improved RAID 4
	- Same pros as RAID 4  
	- Write performance is improved (no single parity bottleneck)  
### 5. RAID 6
- RAID 6 adds another parity block per stripe
- Similar to RAID 5 but with two parity blocks per stripe  
- RAID 6 Can Recover from 2 Simultaneous Drive Failures
	- Due to the extra parity, usable space is (N − 2) / N  
	- Requires at least 4 drives  
	- Write performance slightly less than RAID 5 (extra parity calculation)  