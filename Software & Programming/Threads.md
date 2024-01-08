## 1. Definitions
#### 1.1 Concurrency versus Parallelism
**Concurrency**: Switching between 2 or more things (can get interrupted). Goal is to make progress on multiple things.

**Parallelism**: Running 2 or more things at the same time (they are independent). Goal is to run as fast as possible.
#### 1.2 Types of Threads
**Joinable Threads**: The default; wait for someone to call join then they release their resources.

**Detached Threads**: Threads that release their resources when they terminate.
#### 1.3 Multithreading Models
**User Threads**: Are completely in user space, kernel does not treat your threaded process any differently.
- Very fast to create and destroy, no system call, no context switches
- One thread blocks, it blocks the entire process (kernel can't distinguish)

**Kernel Threads**: Are implemented in kernel space, kernel manages everything for you and can treat threads specially.
- Slower, creation involves system calls
- If one thread blocks, the kernel can schedule another one

- All threading libraries you use run in user mode, the thread library maps user threads to kernel threads

**Many-to-One**: Threads completely implemented in user space, the kernel only sees 1 process.

**One-to-One**: One user thread maps directly to one kernel thread, the kernel handles everything.

**Many-to-Many**: Many user level threads map to many kernel level threads.
### 2. Processes & Threads Comparison 

- **Threads** are like processes with *shared memory* (by default)
	- Threads have their own registers, program counter and stack. Have the same address space so no changes appear in each thread.
	- You need to explicitly state if any memory is specific to a thread (TLS)

- One process can have multiple threads

| Processes | Threads |
| ---- | ---- |
| Independent code/data/heap | Shared code/data/heap |
| Independent execution | Must live within an executing process |
| Has its own stack and registers | Has its own stack and registers |
| Expensive creation and context switching | Cheap creation and context switching |
| Completely removed from OS on exit | Stack removed from process on exit |
## 3. POSIX Threads API
- Windows has Win32 threads, UNIX has POSIX threads

**Include**:
```c
#include <pthread.h>
```

**Create Thread**:
```c
int pthread_create(pthread_t* thread, const pthread_attr_t* attr, void* (*start_routine)(void*), void* arg);
```
- ```thread``` - creates a handle to a thread at pointer location
- ```attr``` - thread attributes (NULL for defaults, more details later)
- ```start_routine``` - function to start execution
- ```arg``` - value to pass to start routine
- Returns 0 on success, error number otherwise (contents of \*thread  are undefined)

**Join Threads**:
```c
int pthread_join(pthread_t thread, void** retval);
```
- ```thread``` - wait for this thread to terminate (thread must be joinable)
- ```retval``` - stores exit status of thread (set by pthread_exit) to the location pointed by \*retval If cancelled returns ```PTHREAD_CANCELED```, ```NULL``` is ignored
- Returns 0 on success
- Only call this 1 time per thread, multiple calls lead to undefined behaviour
- Is the equivalent of ```wait``` for threads

**Exit Thread**:
```c
void pthread_exit(void* retval);
```
- ```retval``` - return value passed to function that calls ```pthread_join```
- Called implicitly when ```start_routine``` of a thread returns

**Detach Thread**:
```c
int pthread_detach(pthread_t thread);
```
- Returns 0 on success, error number otherwise

## 4. Other APIs

#### 4.1 TailQ Linked Lists
- Implements linked lists for you

**Include**:
```c
#include <sys/queue.h>
```

**Element Struct Example**:
```c
// Initialize linked list struct:
struct list_entry {
    int id;
    TAILQ_ENTRY(list_entry) pointers;
};
TAILQ_HEAD(list_head, list_entry);
static struct list_head list_head;
```

**Initialize List**:
```c
// Initialize linked list:
TAILQ_INIT(&list_head);
```

**Add Entry**:
```c
// Add element to linked list:
struct list_entry e1;
e1.id = 1;
TAILQ_INSERT_TAIL(&list_head, &e1, pointers);
```

**Delete Entry**:
```c
// Remove element from linked list:
TAILQ_REMOVE(&list_head, &e1, pointers);
```

**Print List**:
```c
// Print list:
struct list_entry* e;
TAILQ_FOREACH(e, &list_head, pointers) {
	printf(" %d", e->id);
}
```

#### 4.2 UContext
- Think of ucontext as a save state or save point variable

**Include**:
```c
#include <ucontext.h>
```

**Functions**:
```c
// Set a save point in ucontext:
int getcontext(ucontext_t *);

// Go back to save point in ucontext:
int setcontext(const ucontext_t *); 

// Sets the save point of ucontext to the beginning of a function:
// Must have a stack set and getcontext previously set
void makecontext(ucontext_t *, (void *)(), int, ...); 

// Saves context of the first ucontext and swaps over to the context of the second:
int swapcontext(ucontext_t *, const ucontext_t *);
```

**Struct Members**:
- ```ucontext_t* uc_link``` - pointer to the context that will be resumed when this context returns
- ```sigset_t uc_sigmask``` - the set of signals that are blocked when this context is active
- ```stack_t uc_stack``` - the stack used by this context
- ```mcontext_t uc_mcontext``` - a machine-specific representation of the saved context

