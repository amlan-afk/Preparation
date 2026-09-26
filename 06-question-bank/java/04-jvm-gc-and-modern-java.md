# ☕ JVM Internals, Garbage Collection & Modern Java (Q76 – Q100)

---

### Q76: Describe the memory structure of the JVM runtime data areas.
**Direct Answer:**  
1. **Heap (Shared):** Stores all class instances and arrays. Divided into Young Gen (Eden, S0, S1) and Old/Tenured Gen.
2. **Metaspace (Shared, Java 8+):** Stores class metadata, bytecode, method tables, and constant pools in native OS memory (replaces PermGen).
3. **JVM Stack (Per-Thread):** Stores stack frames for method executions (local variables, operand stack, return addresses).
4. **Program Counter (PC) Register (Per-Thread):** Holds the address of the current JVM bytecode instruction being executed.
5. **Native Method Stack (Per-Thread):** Manages native C/C++ (JNI) method calls.

---

### Q77: How is the JVM Heap partitioned and how do objects age?
**Direct Answer:**  
- **Young Generation:**
  - **Eden:** New objects are allocated here.
  - **Survivor Spaces (S0 / S1):** Two identical spaces. When Eden fills, a Minor GC moves surviving objects to one survivor space and increments their age counter (tenuring threshold, default 15). One survivor space is always empty (`To-space`).
- **Old (Tenured) Generation:** Objects that survive repeated Minor GCs (age $\ge 15$) or very large objects that bypass Young Gen are promoted here.

---

### Q78: What is the difference between Minor GC, Major GC, and Full GC?
**Direct Answer:**  
- **Minor GC:** Cleans only the Young Generation. Fast, frequent, cleans up short-lived objects.
- **Major GC:** Cleans the Old Generation. Slower than Minor GC.
- **Full GC:** Cleans the entire heap (Young Gen, Old Gen) and Metaspace. Most expensive Stop-The-World pause.

---

### Q79: How does the JVM determine which objects are eligible for Garbage Collection?
**Direct Answer:**  
Java uses **Root Reachability Analysis**, NOT Reference Counting (which fails with cyclic references).  
An object is alive if it can be reached via a reference chain starting from a **GC Root**:
- **GC Roots include:**
  1. Active thread local variables and parameters on the JVM stack.
  2. Static variables in loaded classes.
  3. JNI global/local native references.
  4. Synchronized monitor locks held by active threads.

---

### Q80: What is a Stop-The-World (STW) pause?
**Direct Answer:**  
A pause where the JVM halts all application threads (mutators) to bring them to a "Safe Point" so that GC threads can inspect object references and move objects without the heap mutating concurrently. Modern collectors (ZGC, Shenandoah) aim for sub-millisecond STW pauses.

---

### Q81: Compare modern Garbage Collectors: Serial, Parallel, G1 GC, and ZGC.
**Direct Answer:**  
- **Serial GC (`-XX:+UseSerialGC`):** Single-threaded. Suitable only for small CLI apps or embedded devices.
- **Parallel GC (`-XX:+UseParallelGC`):** Multi-threaded throughput collector. Minimizes total CPU overhead; longer pauses.
- **G1 GC (`-XX:+UseG1GC`, Default in Java 9+):** Partitions heap into equal 1MB–32MB regions. Prioritizes regions with the most garbage ("Garbage-First"). Balances throughput and predictable pause times (`-XX:MaxGCPauseMillis=200`).
- **ZGC (`-XX:+UseZGC`, Java 15/21):** Ultra-low-latency collector. Performs marking, relocation, and reference processing concurrently with app threads using colored pointers and load barriers. Pauses are consistently $< 1\text{ms}$ on multi-terabyte heaps.

---

### Q82: PermGen (Java 7) vs Metaspace (Java 8+): Why was PermGen removed?
**Direct Answer:**  
- **PermGen:** Part of the fixed contiguous JVM heap (`-XX:MaxPermSize`). Prone to frequent `java.lang.OutOfMemoryError: PermGen space` when apps dynamically loaded classes (Spring, Hibernate, Tomcat).
- **Metaspace:** Uses **off-heap native OS memory**. It automatically expands up to available system RAM by default (configurable via `-XX:MaxMetaspaceSize`). Eliminates out-of-memory errors caused by class metadata.

---

### Q83: How do you diagnose and resolve `OutOfMemoryError: Java heap space`?
**Direct Answer:**  
1. **Enable Heap Dump on Crash:** `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/dumps/heap.hprof`.
2. **Analyze in Eclipse Memory Analyzer Tool (MAT) / JProfiler:**
   - Look at the **Dominator Tree** and **Leak Suspects** report to find objects holding the largest retained heap.
3. **Common Causes:** Unbounded caches (missing eviction policy), unclosed database connections/result sets, static collections growing infinitely, large file loads into memory without streaming.

---

### Q84: What causes `StackOverflowError` and how does it differ from `OutOfMemoryError`?
**Direct Answer:**  
- `StackOverflowError`: Thrown when a thread's call stack exceeds its allocated stack size (`-Xss`, default 1MB). Caused by infinite recursion, deep call chains, or circular method calls.
- `OutOfMemoryError`: Thrown when the JVM Heap or Metaspace has no available memory left and GC cannot free any more memory to allocate an object.

---

### Q85: What is the JVM ClassLoader Hierarchy and the Delegation Principle?
**Direct Answer:**  
**Hierarchy:**
1. **Bootstrap ClassLoader:** Written in C/C++, loads core runtime classes (`java.base`, `rt.jar`).
2. **Platform / Extension ClassLoader:** Loads standard platform extensions.
3. **Application / System ClassLoader:** Loads classes from application classpath / dependencies.  
**Parent Delegation Principle:** When a class loader needs to load a class, it always delegates to its parent first. Only if the parent fails to find the class does the child attempt to load it. Prevents untrusted code from overriding core classes (e.g. malicious `java.lang.String`).

---

### Q86: What are the core Functional Interfaces in `java.util.function`?
**Direct Answer:**  
1. `Predicate<T>`: `boolean test(T t)` — Evaluates condition (filtering).
2. `Function<T, R>`: `R apply(T t)` — Transforms input $T$ to output $R$.
3. `Consumer<T>`: `void accept(T t)` — Performs action on input without returning a result.
4. `Supplier<T>`: `T get()` — Factory producing an instance without input.
5. `BiFunction<T, U, R>`, `UnaryOperator<T>`, `BinaryOperator<T>`.

---

### Q87: What is the difference between Intermediate and Terminal Stream operations?
**Direct Answer:**  
- **Intermediate Operations:** Return a new `Stream<T>`. They are **lazy** and execute only when a terminal operation is invoked (e.g. `filter()`, `map()`, `sorted()`, `distinct()`).
- **Terminal Operations:** Produce a non-stream result (value, collection) or side-effect. They trigger the pipeline execution and consume the stream (e.g. `collect()`, `forEach()`, `reduce()`, `count()`). A stream cannot be reused after a terminal operation.

---

### Q88: What is the difference between `map()` and `flatMap()` in Streams?
**Direct Answer:**  
- `map(Function<T, R>)`: One-to-one transformation. Transforms each element into another element: `Stream<T> -> Stream<R>`.
- `flatMap(Function<T, Stream<R>>)`: One-to-many transformation and flattening. Transforms each element into a stream and flattens multiple nested streams into a single stream: `Stream<List<T>> -> Stream<T>`.

---

### Q89: How does `Optional` prevent `NullPointerException` and what are its anti-patterns?
**Direct Answer:**  
`Optional<T>` is a container that explicitly signals whether a return value may be missing.  
**Best Practices:**
```java
// Good:
return Optional.ofNullable(user)
    .map(User::getEmail)
    .orElse("default@example.com");
```
**Anti-Patterns:**
1. Calling `optional.get()` without `isPresent()` (throws `NoSuchElementException`).
2. Using `Optional` as method parameters, class fields, or collections (`List<Optional<T>>` wastes memory).
3. Returning `null` from a method whose return type is `Optional`!

---

### Q90: When do Parallel Streams hurt performance?
**Direct Answer:**  
Parallel streams split data across the common `ForkJoinPool`. They degrade performance when:
1. The dataset is small ($< 10,000$ elements; thread orchestration overhead exceeds parallel gains).
2. Tasks perform blocking I/O (database, HTTP calls), starving the shared `ForkJoinPool` for other application tasks.
3. Operations involve shared mutable state or boxing/unboxing overhead.
4. The underlying collection is not easily splittable (e.g. `LinkedList` has terrible spliterator performance compared to `ArrayList` or arrays).

---

### Q91: What are Sealed Classes in Java 17?
**Direct Answer:**  
Sealed classes restrict which other classes can extend or implement them:
```java
public sealed interface Shape permits Circle, Rectangle, Triangle {}
public final class Circle implements Shape {}
public non-sealed class Rectangle implements Shape {}
```
Subclasses must be marked `final` (cannot be extended), `sealed`, or `non-sealed`. Enables exhaustive compile-time pattern matching without requiring a redundant `default` switch branch.

---

### Q92: What is Pattern Matching for `switch` in Java 21?
**Direct Answer:**  
Allows matching types and extracting components directly in `switch` expressions with guard conditions (`when`):
```java
static String formatShape(Shape shape) {
    return switch (shape) {
        case Circle c -> "Circle with radius " + c.radius();
        case Rectangle r when r.width() == r.height() -> "Square";
        case Rectangle r -> "Rectangle of " + r.width() + "x" + r.height();
        case null -> "Null shape";
    };
}
```

---

### Q93: Can an object leak in Java if Garbage Collection is automatic?
**Direct Answer:**  
**YES.** A memory leak occurs when unused objects remain **strongly reachable** from a GC Root:
1. **Static Collections:** Adding objects to a `static List` or `static Map` without removal.
2. **Unclosed Resources:** Database connections, sockets, and streams holding buffer pools.
3. **Listeners / Callbacks:** Registering listeners in long-lived singletons without unregistering them.
4. **ThreadLocal:** Not clearing `ThreadLocal` in thread pools.

---

### Q94: What is the JIT (Just-In-Time) Compiler and Tiered Compilation?
**Direct Answer:**  
The JVM interprets bytecode initially for fast startup. Frequently executed methods ("hot code") are compiled directly into native CPU machine code by the JIT compiler.  
**Tiered Compilation:**
- **Tier 0:** Interpreter.
- **Tier 1–3 (C1 Client Compiler):** Fast compilation with basic profiling.
- **Tier 4 (C2 Server Compiler):** Aggressive, high-performance optimizations (method inlining, loop unrolling, escape analysis).

---

### Q95: What is Escape Analysis and Scalar Replacement in JIT?
**Direct Answer:**  
The JIT compiler analyzes the scope of newly created objects:
- If an object does not "escape" the method where it is allocated (never passed outside or assigned to a field), the JIT compiler can perform **Scalar Replacement**: it breaks the object into its individual primitive fields and stores them in CPU registers or on the call stack, **completely eliminating heap allocation and GC overhead!**

---

### Q96: What are the most critical JVM flags for production Spring Boot microservices?
**Direct Answer:**  
```bash
-Xms2g -Xmx2g                              # Pin heap min/max to avoid runtime resizing
-XX:+UseG1GC                               # High-throughput balanced collector
-XX:MaxGCPauseMillis=200                   # Target pause time
-XX:+UseStringDeduplication                # G1 deduplicates identical Strings in heap
-XX:+HeapDumpOnOutOfMemoryError            # Automatically generate crash dump
-XX:HeapDumpPath=/var/log/heapdump.hprof   # Output dump path
-XX:+ExitOnOutOfMemoryError                # Terminate container so k8s restarts pod
```

---

### Q97: What is the difference between `-Xms` and `-Xmx`? Why set them equal in production?
**Direct Answer:**  
- `-Xms`: Initial heap size at startup.
- `-Xmx`: Maximum allowable heap size.  
**Production Rule:** In containerized environments (Docker/Kubernetes), always set `-Xms` equal to `-Xmx`. If unequal, the JVM repeatedly pauses to request additional memory pages from the OS as load rises, adding unpredictable latency spikes.

---

### Q98: How do you take and analyze a Thread Dump to diagnose a frozen Java app?
**Direct Answer:**  
1. Capture dump: `jstack <pid> > threaddump.txt` or `jcmd <pid> Thread.print`.
2. Inspect for threads in `BLOCKED` state waiting for locks.
3. Check the bottom of the dump for `Found one Java-level deadlock:`.
4. High CPU usage: Match OS thread ID from `top -H -p <pid>` (convert PID to hexadecimal) to the `nid` field in the thread dump to identify the exact looping Java code.

---

### Q99: What are Weak, Soft, and Phantom References in Java?
**Direct Answer:**  
- **Strong Reference:** Normal reference (`Object obj = new Object()`). Never collected while reachable.
- **Soft Reference (`SoftReference`):** Collected only when JVM runs critically low on memory before throwing OOM. Used for memory-sensitive caches.
- **Weak Reference (`WeakReference`):** Collected immediately on the next GC cycle if no strong references exist (`WeakHashMap`).
- **Phantom Reference (`PhantomReference`):** Enqueued in a `ReferenceQueue` after object finalization. Used for safe native resource deallocation.

---

### Q100: How do Java 21 Scoped Values and Structured Concurrency improve thread safety?
**Direct Answer:**  
- **Scoped Values (`ScopedValue`):** Lightweight, immutable alternative to `ThreadLocal`. Bounded to a specific execution scope; child virtual threads inherit values without copying or memory leak risk.
- **Structured Concurrency (`StructuredTaskScope`):** Treats subtasks running in separate threads as a single atomic unit of work. If one subtask fails, all sibling subtasks are automatically cancelled, eliminating thread leaks and orphaned background tasks.
