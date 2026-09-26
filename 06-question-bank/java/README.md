# ☕ Java Interview Mastery: Top 100 Questions & Answers

A comprehensive collection of **100 most-frequently-asked Java interview questions and answers** for SDE 2 and Senior Software Engineers. Organized into 4 structured modules covering language foundations, data structures, multithreading, and JVM internals.

---

## 🗺️ Curriculum Module Index

| Module | Topic Range | File Link | Question Count | Core Subjects Covered |
|:---:|---|---|:---:|---|
| **01** | **Core OOP & Language Fundamentals** | [01-core-oop-and-memory.md](./01-core-oop-and-memory.md) | **Q1 – Q25** | Pillars of OOP, String Pool, `equals()`/`hashCode()`, pass-by-value, exceptions, Generics, Enums, Records. |
| **02** | **Collections & Data Structures** | [02-collections-and-generics.md](./02-collections-and-generics.md) | **Q26 – Q50** | `HashMap` internals (Java 8 treeification), `ConcurrentHashMap`, fail-fast vs fail-safe, LRU cache, `ArrayDeque`. |
| **03** | **Concurrency & Multithreading** | [03-concurrency-and-multithreading.md](./03-concurrency-and-multithreading.md) | **Q51 – Q75** | Monitor locks, `wait`/`notify`, deadlocks, CAS, `ReentrantLock`, `ThreadPoolExecutor`, `CompletableFuture`, Virtual Threads. |
| **04** | **JVM, GC & Modern Java** | [04-jvm-gc-and-modern-java.md](./04-jvm-gc-and-modern-java.md) | **Q76 – Q100** | Memory model, G1 GC, ZGC, Metaspace, ClassLoaders, Streams, `Optional`, JIT optimizations, production tuning. |
| | **TOTAL** | | **100 Questions** | **100% Comprehensive SDE 2 Mastery** |

---

## 🎯 Master List of All 100 Questions

### Module 01: Core OOP & Memory (Q1 – Q25)
1. What are the core pillars of OOP in Java and how does Java implement them?
2. What is the difference between an abstract class and an interface in modern Java?
3. Why are Strings immutable in Java?
4. What is the difference between String, StringBuilder, and StringBuffer?
5. How does the JVM handle String creation: `String s = "abc"` vs `new String("abc")`?
6. What is the difference between `==` and `.equals()`?
7. Why must `equals()` and `hashCode()` always be overridden together?
8. What are the rules of the `equals()` contract in Java?
9. What is pass-by-value vs pass-by-reference? How does Java pass parameters?
10. What are Wrapper classes and Autoboxing/Unboxing? What is the hidden trap?
11. What is the difference between `final`, `finally`, and `finalize()`?
12. Can a `finally` block ever NOT execute?
13. What is the difference between Checked and Unchecked Exceptions?
14. How does Try-With-Resources work in Java 7+?
15. What is Method Overloading vs Method Overriding?
16. What is a Covariant Return Type?
17. Can you override a `static` or `private` method in Java?
18. What is the difference between Shallow Copy and Deep Copy?
19. What is the `transient` keyword in Java?
20. What is the `volatile` keyword in Java?
21. What is the difference between `Comparable` and `Comparator`?
22. What are Generics and Type Erasure?
23. What are Wildcards in Generics: `<? extends T>` vs `<? super T>` (PECS Rule)?
24. What is an Enum in Java and why is it better than integer constants?
25. How do Java Records (Java 14/16+) differ from traditional POJOs?

### Module 02: Collections & Data Structures (Q26 – Q50)
26. Describe the hierarchy of the Java Collections Framework.
27. How does `ArrayList` work internally vs `LinkedList`?
28. How does `HashMap` work internally in Java 8?
29. What happens when two distinct keys produce the same `hashCode()`?
30. Why is `HashMap` capacity always a power of 2?
31. What is the difference between `HashMap`, `LinkedHashMap`, and `TreeMap`?
32. What is the difference between `HashMap` and `Hashtable`?
33. How does `ConcurrentHashMap` achieve high concurrency in Java 8?
34. How does `HashSet` work under the hood?
35. What is the difference between Fail-Fast and Fail-Safe Iterators?
36. How does `CopyOnWriteArrayList` work and when should it be used?
37. What is the difference between `ArrayDeque` and `LinkedList` as a Queue/Stack?
38. What data structure backs `PriorityQueue` and what are its time complexities?
39. What is the Load Factor in `HashMap` and why is 0.75 the default?
40. How does `Collections.unmodifiableList(list)` differ from `List.of()` in Java 9+?
41. What is the difference between `Arrays.asList()` and `new ArrayList<>()`?
42. How do you implement a simple LRU Cache using `LinkedHashMap`?
43. Can `null` be used as a key in `HashMap`, `TreeMap`, and `ConcurrentHashMap`?
44. What is the worst-case time complexity of `HashMap.get()` in Java 7 vs Java 8?
45. What is `IdentityHashMap` and how does it differ from `HashMap`?
46. What is `WeakHashMap` and how does it prevent memory leaks?
47. How does `Collections.synchronizedMap()` compare to `ConcurrentHashMap`?
48. How does Java 8 `Map.computeIfAbsent()` work and why is it preferred?
49. What is `EnumSet` and why is it extremely fast?
50. How does `BlockingQueue` facilitate Producer-Consumer architectures?

### Module 03: Concurrency & Multithreading (Q51 – Q75)
51. What is the difference between a Process and a Thread?
52. What are the lifecycle states of a Thread in Java (`Thread.State`)?
53. What is the difference between `Runnable` and `Callable`?
54. How does the `synchronized` keyword work under the hood?
55. What is the difference between Synchronized Method and Synchronized Block?
56. Why must `wait()`, `notify()`, and `notifyAll()` be called inside a `synchronized` block?
57. Why are `wait()`, `notify()`, and `notifyAll()` defined in `Object` rather than `Thread`?
58. What is the difference between `Thread.sleep()` and `Object.wait()`?
59. What is a Deadlock and what are the 4 Coffman conditions required for it?
60. How do you prevent Deadlocks in Java?
61. What are Atomic Classes (`AtomicInteger`, `AtomicReference`) and how does CAS work?
62. What is `ReentrantLock` and how does it compare to `synchronized`?
63. What is `ReentrantReadWriteLock`?
64. What is the difference between `CountDownLatch` and `CyclicBarrier`?
65. What is a `Semaphore`?
66. How does `ThreadPoolExecutor` work internally?
67. What are the 4 built-in `RejectedExecutionHandler` policies?
68. Why is `Executors.newFixedThreadPool()` dangerous in production?
69. What is `ThreadLocal` and how does it cause memory leaks?
70. What is the difference between `Future` and `CompletableFuture`?
71. What is the difference between `thenApply()`, `thenAccept()`, and `thenCompose()`?
72. How does `CompletableFuture.allOf()` handle multiple parallel tasks?
73. What is the ForkJoinPool and the Work-Stealing Algorithm?
74. How does Thread Interruption work in Java?
75. What are Java 21 Virtual Threads (Project Loom) and how do they differ from Platform Threads?

### Module 04: JVM, GC & Modern Java (Q76 – Q100)
76. Describe the memory structure of the JVM runtime data areas.
77. How is the JVM Heap partitioned and how do objects age?
78. What is the difference between Minor GC, Major GC, and Full GC?
79. How does the JVM determine which objects are eligible for Garbage Collection?
80. What is a Stop-The-World (STW) pause?
81. Compare modern Garbage Collectors: Serial, Parallel, G1 GC, and ZGC.
82. PermGen (Java 7) vs Metaspace (Java 8+): Why was PermGen removed?
83. How do you diagnose and resolve `OutOfMemoryError: Java heap space`?
84. What causes `StackOverflowError` and how does it differ from `OutOfMemoryError`?
85. What is the JVM ClassLoader Hierarchy and the Delegation Principle?
86. What are the core Functional Interfaces in `java.util.function`?
87. What is the difference between Intermediate and Terminal Stream operations?
88. What is the difference between `map()` and `flatMap()` in Streams?
89. How does `Optional` prevent `NullPointerException` and what are its anti-patterns?
90. When do Parallel Streams hurt performance?
91. What are Sealed Classes in Java 17?
92. What is Pattern Matching for `switch` in Java 21?
93. Can an object leak in Java if Garbage Collection is automatic?
94. What is the JIT (Just-In-Time) Compiler and Tiered Compilation?
95. What is Escape Analysis and Scalar Replacement in JIT?
96. What are the most critical JVM flags for production Spring Boot microservices?
97. What is the difference between `-Xms` and `-Xmx`? Why set them equal in production?
98. How do you take and analyze a Thread Dump to diagnose a frozen Java app?
99. What are Weak, Soft, and Phantom References in Java?
100. How do Java 21 Scoped Values and Structured Concurrency improve thread safety?
