# ☕ Java Concurrency & Multithreading (Q51 – Q75)

---

### Q51: What is the difference between a Process and a Thread?
**Direct Answer:**  
- **Process:** An independent execution unit with its own dedicated virtual memory address space (heap, code, file descriptors). Processes are isolated from each other by the OS; inter-process communication (IPC) requires sockets or shared memory.
- **Thread:** A lightweight sub-execution path within a process. Multiple threads share the same process heap and metaspace, but each thread has its own private **call stack** and program counter (PC). Thread switching is significantly faster than process switching.

---

### Q52: What are the lifecycle states of a Thread in Java (`Thread.State`)?
**Direct Answer:**  
1. **NEW:** Thread instance created, not yet started (`start()` not called).
2. **RUNNABLE:** Executing in the JVM or ready/waiting for OS CPU time.
3. **BLOCKED:** Waiting to acquire an intrinsic monitor lock to enter/re-enter a `synchronized` block/method.
4. **WAITING:** Waiting indefinitely for another thread to perform a specific action (`wait()`, `join()`, `LockSupport.park()`).
5. **TIMED_WAITING:** Waiting for a specified time interval (`sleep(ms)`, `wait(ms)`, `join(ms)`).
6. **TERMINATED:** Thread has finished execution (`run()` method completed or exception thrown).

---

### Q53: What is the difference between `Runnable` and `Callable`?
**Direct Answer:**  
- `Runnable`: `public void run()` — Cannot return a result and cannot throw checked exceptions. Used with `Thread` or `ExecutorService`.
- `Callable<V>`: `public V call() throws Exception` — Returns a parameterized result `V` and can throw checked exceptions. Used with `ExecutorService.submit()`, returning a `Future<V>`.

---

### Q54: How does the `synchronized` keyword work under the hood?
**Direct Answer:**  
Every Java object has an internal **Monitor** (implemented by `ObjectMonitor` in C++ HotSpot).  
When a thread enters a synchronized block, the bytecode executes `monitorenter`:
1. It attempts to increment the monitor's recursion counter from 0 to 1 and set the thread as the owner.
2. If already owned by another thread, the current thread enters the monitor's `EntryList` and blocks (`BLOCKED` state).
3. If owned by the same thread, the counter increments (**Reentrancy**).  
Upon exiting, `monitorexit` decrements the counter; when counter reaches 0, the lock is released.

---

### Q55: What is the difference between Synchronized Method and Synchronized Block?
**Direct Answer:**  
- **Synchronized Method:** Acquires the lock on `this` (instance method) or `ClassName.class` (static method) for the **entire duration** of the method. Coarse-grained; increases lock contention.
- **Synchronized Block:** Acquires a lock on a specific target object (`synchronized(customLock)`) for only the critical section lines of code. Fine-grained; minimizes contention and allows locking on non-public objects.

---

### Q56: Why must `wait()`, `notify()`, and `notifyAll()` be called inside a `synchronized` block?
**Direct Answer:**  
To prevent the **Lost Wakeup Condition (Race Condition)**.  
A thread must hold the object's monitor lock before checking the condition predicate and calling `wait()`. If `wait()` could be called without holding the lock, a producer thread could call `notify()` *between* the consumer checking the predicate and calling `wait()`, causing the notification to be missed and the consumer to sleep forever. Calling `wait()` atomically releases the monitor lock and suspends the thread.

---

### Q57: Why are `wait()`, `notify()`, and `notifyAll()` defined in `Object` rather than `Thread`?
**Direct Answer:**  
Because locks/monitors in Java belong to **individual objects on the heap**, not to threads. Threads acquire monitors from objects. When a thread calls `wait()`, it is waiting on a shared resource/condition associated with that specific object, not waiting on a thread.

---

### Q58: What is the difference between `Thread.sleep()` and `Object.wait()`?
**Direct Answer:**  
- `Thread.sleep(ms)`: Pauses current thread for a duration; **DOES NOT release acquired locks**. Can be called anywhere.
- `Object.wait()`: Puts thread to sleep; **RELEASES the object's monitor lock**, allowing other threads to acquire it. Must be called inside a `synchronized` context.

---

### Q59: What is a Deadlock and what are the 4 Coffman conditions required for it?
**Direct Answer:**  
A deadlock occurs when two or more threads are permanently blocked, each holding a lock that the other needs.  
The 4 necessary conditions are:
1. **Mutual Exclusion:** Resources cannot be shared; held exclusively.
2. **Hold and Wait:** A thread holds at least one resource and waits to acquire another.
3. **No Preemption:** Resources cannot be forcibly taken away from a thread holding them.
4. **Circular Wait:** Thread A waits for Thread B, which waits for Thread A.

---

### Q60: How do you prevent Deadlocks in Java?
**Direct Answer:**  
1. **Lock Ordering (Primary Fix):** Always acquire multiple locks in a strict global alphabetical or numeric order across the entire codebase.
2. **Lock Timeouts:** Use `Lock.tryLock(timeout, timeUnit)` instead of intrinsic `synchronized`. If a lock cannot be acquired within 2 seconds, back off, release all held locks, and retry.
3. **Deadlock Detection:** Use `jstack <pid>`, Java Mission Control, or `ThreadMXBean.findDeadlockedThreads()`.

---

### Q61: What are Atomic Classes (`AtomicInteger`, `AtomicReference`) and how does CAS work?
**Direct Answer:**  
Atomic classes provide lock-free thread safety using **Compare-And-Swap (CAS)** CPU instructions (`CMPXCHG` on x86).  
- **CAS takes 3 arguments:** Memory location $V$, Expected old value $A$, New value $B$.
- If current value at $V == A$, it atomically updates $V = B$ and returns `true`. If mismatched, it returns `false`, and the loop retries.
- Zero OS context-switching overhead compared to `synchronized`.

---

### Q62: What is `ReentrantLock` and how does it compare to `synchronized`?
**Direct Answer:**  
`ReentrantLock` implements `java.util.concurrent.locks.Lock`:
- **Capabilities over `synchronized`:**
  1. **Interruptible lock acquisition:** `lockInterruptibly()`.
  2. **Non-blocking / timed attempts:** `tryLock(5, TimeUnit.SECONDS)`.
  3. **Fairness option:** `new ReentrantLock(true)` guarantees FIFO thread acquisition.
  4. **Multiple Condition variables:** `lock.newCondition()` allows separate wait sets for consumers vs producers.
- **Drawback:** Must be explicitly unlocked in a `finally` block to prevent leaks.

---

### Q63: What is `ReentrantReadWriteLock`?
**Direct Answer:**  
Maintains a pair of associated locks:
- **Read Lock:** Multiple threads can hold the read lock simultaneously, as long as no thread holds the write lock.
- **Write Lock:** Exclusive; only one thread can hold the write lock, blocking all readers and writers.  
Ideal for read-heavy systems where reads occur 95% of the time.

---

### Q64: What is the difference between `CountDownLatch` and `CyclicBarrier`?
**Direct Answer:**  
- `CountDownLatch`: One or more threads wait until a counter reaches zero (`await()`). Other threads decrement the counter (`countDown()`). **Cannot be reset or reused** once it hits zero.
- `CyclicBarrier`: A group of $N$ threads must all arrive at a common barrier point (`await()`) before any thread is allowed to continue. **Can be reset and reused** cyclically across iterations.

---

### Q65: What is a `Semaphore`?
**Direct Answer:**  
A `Semaphore` maintains a set of permits. Threads request permits via `acquire()` (blocking if none available) and return permits via `release()`. Used to throttle concurrency and bound access to limited shared resources (e.g. bounding concurrent database connections to 20).

---

### Q66: How does `ThreadPoolExecutor` work internally?
**Direct Answer:**  
When a task is submitted to `ThreadPoolExecutor`:
1. If `activeThreads < corePoolSize`, spawn a new thread to run the task.
2. If `activeThreads >= corePoolSize`, enqueue the task into `BlockingQueue`.
3. If queue is full and `activeThreads < maximumPoolSize`, spawn a new thread.
4. If queue is full and `activeThreads >= maximumPoolSize`, invoke the `RejectedExecutionHandler`.

---

### Q67: What are the 4 built-in `RejectedExecutionHandler` policies?
**Direct Answer:**  
1. `AbortPolicy` (Default): Throws `RejectedExecutionException`.
2. `CallerRunsPolicy`: The thread calling `submit()` (often the main web request thread) executes the task itself, creating natural backpressure.
3. `DiscardPolicy`: Silently drops the rejected task with no error.
4. `DiscardOldestPolicy`: Drops the oldest unhandled task at the head of the queue and retries the new task.

---

### Q68: Why is `Executors.newFixedThreadPool()` dangerous in production?
**Direct Answer:**  
Because it uses an **unbounded `LinkedBlockingQueue`** (`Integer.MAX_VALUE` capacity):
```java
// Danger: Unbounded queue
new LinkedBlockingQueue<Runnable>()
```
If traffic spikes and downstream services slow down, tasks queue up infinitely, consuming RAM until the JVM crashes with `java.lang.OutOfMemoryError: Java heap space`. Always use custom `ThreadPoolExecutor` with a bounded queue and `CallerRunsPolicy`.

---

### Q69: What is `ThreadLocal` and how does it cause memory leaks?
**Direct Answer:**  
`ThreadLocal` provides thread-scoped isolated variables.  
**Memory Leak Trap:** Each thread holds a reference to a `ThreadLocalMap`. In web servers (Tomcat), worker threads are pooled and reused for hundreds of requests. If `threadLocal.remove()` is not called in a `finally` block after the request completes, the data remains referenced in the thread's map indefinitely, leaking memory and leaking tenant/security data into the next request!

---

### Q70: What is the difference between `Future` and `CompletableFuture`?
**Direct Answer:**  
- `Future`: Legacy interface (Java 5). Getting the result is **blocking** (`get()`), and futures cannot be chained, combined, or manually completed.
- `CompletableFuture`: Added in Java 8. Supports **non-blocking asynchronous pipelines**, functional callbacks (`thenApply`, `thenCompose`, `thenCombine`), exception handling (`exceptionally`), and manual completion (`complete(value)`).

---

### Q71: What is the difference between `thenApply()`, `thenAccept()`, and `thenCompose()`?
**Direct Answer:**  
- `thenApply(Function<T, R>)`: Transforms the result $T$ into $R$ (equivalent to `map` in streams).
- `thenAccept(Consumer<T>)`: Consumes the result $T$, returns `CompletableFuture<Void>` (terminal action).
- `thenCompose(Function<T, CompletableFuture<R>>)`: Flattens nested futures (equivalent to `flatMap`). Used to chain one async stage that depends on the output of a prior async stage without nesting.

---

### Q72: How does `CompletableFuture.allOf()` handle multiple parallel tasks?
**Direct Answer:**  
`allOf(f1, f2, f3)` returns a new `CompletableFuture<Void>` that completes when all input futures complete. To collect results without blocking sequentially:
```java
List<CompletableFuture<String>> futures = ...;
CompletableFuture<Void> allDone = CompletableFuture.allOf(futures.toArray(new CompletableFuture[0]));

CompletableFuture<List<String>> result = allDone.thenApply(v -> 
    futures.stream().map(CompletableFuture::join).toList()
);
```

---

### Q73: What is the ForkJoinPool and the Work-Stealing Algorithm?
**Direct Answer:**  
`ForkJoinPool` is designed for divide-and-conquer tasks (`ForkJoinTask`). Each worker thread has its own double-ended queue (deque) of tasks:
- A worker pushes/pops subtasks from the **head** of its own deque (LIFO).
- If a worker's deque becomes empty, it **steals tasks from the tail** of other busy workers' deques (FIFO).  
Powers Java 8 Parallel Streams and default `CompletableFuture` operations.

---

### Q74: How does Thread Interruption work in Java?
**Direct Answer:**  
Interruption is a **cooperative mechanism**, not a forceful thread kill:
1. `thread.interrupt()` sets the thread's internal interrupt status flag.
2. If the thread is blocked in `sleep()`, `wait()`, or `join()`, it throws `InterruptedException` and **clears** the interrupt flag.
3. If running CPU computations, the thread must explicitly check `Thread.currentThread().isInterrupted()` and exit cleanly.

---

### Q75: What are Java 21 Virtual Threads (Project Loom) and how do they differ from Platform Threads?
**Direct Answer:**  
- **Platform Threads:** 1:1 mapping to OS kernel threads. Heavyweight (~1MB stack memory), expensive context switches, limited to a few thousand per JVM.
- **Virtual Threads:** $M:N$ lightweight user-mode threads managed by the JVM runtime over a pool of carrier platform threads. Consume only a few hundred bytes, can be spawned in millions (`Thread.ofVirtual().start(task)`).
- When a virtual thread performs blocking I/O, the JVM unmounts it from the OS carrier thread, freeing the carrier thread to execute other virtual threads!
