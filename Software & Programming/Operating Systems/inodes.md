### 1. Introduction
**inode**: Describes a file system object (files and directories)
- Aim to be efficient for small files and support large ones
- UNIX inodes hold metadata and pointers to blocks 
- Smaller files only use direct pointers 
- Larger files have additional index nodes with pointers to additional blocks 
- Very small files can have its contents directly inside the inode
### 2. Hard Links
- Hard Links are pointers to inodes
	- A directory entry (aka filename) is called a *hard link*
	- A hard link points to one inode
- Multiple hard links can point to the same inode
	- Deleting a file only removes the hard link (the file can be hard linked somewhere else)
	- POSIX has the `unlink` call rather than a delete call
### 3. Soft Links
- Soft Links are paths to another file
	- When resolving the file, the file system is redirected somewhere else, so:
		- Soft link targets do not need to exist
		- Soft link targets can be deleted without notice of the soft link
		- Unresolvable soft links lead to an exception
### 4. UNIX
- In UNIX, everything is a file
	- Directories are files of type “directory”
	- Additional types are:
		- “regular file”, “block device” (HDDs, SSDs), “pipe”, “socket” etc.
	- Directory inodes do not store pointers to data blocks but rather filenames and pointers to inodes
### 5. Data in inode
- Data stored in an inode:
	- File size
	- File type
	- Number of hard links
	- Access Rights
	- Timestamps
	- File contents (sometimes)
	- Ordered list of data blocks
- Data not stored in an inode:
	- Filename
	- Directory name
	- Number of soft links
	- Location of soft links
	- Location of hard links
### 6. Filesystem
- Filesystem caches speed up writing to disks
	- Writing data to the disk is slow, we can use a cache to speed it up
	- File blocks are cached in main memory in the *filesystem cache*
		- Referenced blocks are likely to be referenced again (temporal locality) 
		- Logically near blocks are likely to be referenced (spatial locality)
	- A kernel thread (or daemon) writes changes periodically to disk
	- `flush` and `sync` system calls trigger a permanent write
- Journaling filesystem
	- Deleting a file on a Unix file system involves three steps:
		1. Removing its directory entry.
		2. Releasing the inode to the pool of free inodes.
		3. Returning all disk blocks to the pool of free disk blocks.
	- Crashes could occur between any steps, leading to a storage leak
	- The journal contains operations in progress, so if a crash occurs we can recover