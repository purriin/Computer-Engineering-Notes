## 1. Definitions
**Data Race**: When 2 concurrent actions access the same variable and at least 1 of them is a write operation.

**Atomic Operations**: Are indivisible; any atomic instruction you may assume happens all at once.

**Three Address Code (TAC)**: Intermediate code used by compilers mostly used for analysis and optimization.
- Have the form ```result:= operand1 operand operand2```
- ```GIMPLE``` is the TAC used by gcc

**Critical Section**: means only 1 thread executes instructions at a time
- Safety (aka mutual exclusion): There should only be a single thread in a critical section at once
- Liveness (aka progress): if multiple threads reach a critical section 1 must process. The critical sections cannot depend on outside threads. Can mess up and **deadlock** (threads don't make progress)
- Bounded waiting (aka starvation free): a waiting thread must eventually proceed

- Critical sections should also have minimal overhead:
	- Efficient: don't want to consume resources while waiting
	- Fair: Want each thread to wait approximately the same time
	- Simple: should be easy to use and hard to misuse

## 2. Mutex
**Static Creation**:
```c
pthread_mutex_t m1 = PTHREAD_MUTEX_INITIALIZER;
```

**Dynamic Creation**:
```c
pthread_mutex_t m2;
pthread_mutex_init(&m2, NULL);
```

**Destruction**:
```c
pthread_mutex_destroy(&m);
```

**Locking**:
```c
// Code
pthread_mutex_lock(&m);
// Critical Section
pthread_mutex_unlock(&m);
// More Code
```

## 3. Semaphores
**Semaphores**: Are used for signaling, have a value that is shared between threads (optionally processes).

**Semaphore Include**:
```c
#include <semaphore.h>
```

**Semaphore Functions**:
```c
int sem_init(sem_t* sem, int pshared, unsigned int value);

int sem_destroy(sem_t* sem);

// Decrement value atomically, will not return until value > 0:
int sem_wait(sem_t* sem);
int sem_trywait(sem_t* sem);

// Increment value atomically:
int sem_post(sem_t* sem);
```
- All functions return 0 on success
- ```pshared``` argument is a ```boolean``` set to 1 for IPC

## 4. Conditional Variables
- You can create your own custom queue of threads

**Conditional Include**:
```c
#include <pthread.h>
```

**Conditional Functions**:
```c
int pthread_cond_init(pthread_cond_t *cond, const pthread_condattr_t *attr);

int pthread_cond_destroy(pthread_cond_t *cond);

int pthread_cond_signal(pthread_cond_t *cond); 

int pthread_cond_broadcast(pthread_cond_t *cond); 

int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex); 
int pthread_cond_timedwait(pthread_cond_t *cond, pthread_mutex_t *mutex, const struct timespec *abstime); 
```
- ```wait``` functions add this thread to the queue 
	- Must already hold the mutex, other calls do not
	- 1 mutex can protect multiple condition variables
	- Does not contain data races
- ```signal``` wakes up one thread 
- ```broadcast``` wakes up all threads

- The thread calling ```wait```: 
	1. Adds itself to the queue for the condition variable 
	2. Unlock the mutex 
	3. Gets blocked (it can no longer be scheduled to run)
- The thread calling ```wait``` needs another thread to call ```signal``` or ```broadcast```, then if it’s selected: 
	1. Gets unblocked (it can be scheduled to run) 
	2. Tries to lock the mutex again, wait returns when it gets it

- Condition variables *must* be paired with a mutex
	- Any calls to wait must already hold the mutex 
		- (signal and broadcast may not)
		- Think that wait needs to add itself to the queue safely (no data races) 
			- It needs the mutex as an argument to unlock it before going to sleep 
			- The thread will hold the locked mutex before and after the call 
		- One mutex can also protect multiple condition variables 

- The `wait` call does not contain data races
	- Understand what wait does (for condition variables!)
	- The thread calling wait:
		1. Adds itself to the queue for the condition variable 
		2. Unlock the mutex 
		3. Gets blocked (it can no longer be scheduled to run)
	- The thread calling wait needs another thread to call signal or broadcast, then if it’s selected:
		1. . Gets unblocked (it can be scheduled to run) 
		2. Tries to lock the mutex again, wait returns when it gets it

- Condition variables serve a similar purpose as semaphores
	- You can think of semaphores as a special case of condition variables
	- They go to sleep when the value is 0, when it’s greater than 0 they wake up
	- You can implement one using the other, however it can get messy 
	- For complex conditions condition variables offer much better clarity
# 5. Locking Overheads
- Locking granularity is the extent of your locks
	- You need locks to prevent data races
	- Lock large sections of your program, or divide the locks and use smaller sections? 
		- What if you want to parallelize your hash table?
	- Things to consider about locks:
		1. Overhead
		2. Contention
		3. Deadlocks

- Locking Overheads
	- Memory allocated 
	- Initialization and destruction time 
	- Time to acquire and release locks
- The more locks you have, the greater each cost is going to be

- You do not want deadlocks
	- The more locks you have, the more you have to worry about deadlocks
- Conditions for deadlocking:
	1. Mutual Exclusion (of course for simple locks) 
	2. Hold and Wait (you have a lock and try to acquire another) 
	3. No Preemption (we can’t take simple locks away) 
	4. Circular Wait (waiting for a lock held by another process)
- Can prevent a deadlock by using `trylock`
	- Remember, for `pthread` there’s `trylock` that returns 0 if it gets the lock