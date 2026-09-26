# ☕ Java Collections & Data Structures (Q26 – Q50)

---

### Q26: Describe the hierarchy of the Java Collections Framework.
**Direct Answer:**  
The root interface is `java.util.Collection`, which has three major sub-interfaces:
1. `List` (ordered, indexed, duplicates allowed): `ArrayList`, `LinkedList`, `Vector`.
2. `Set` (no duplicates): `HashSet`, `LinkedHashSet`, `TreeSet` (extends `SortedSet`).
3. `Queue` (FIFO / priority ordering): `PriorityQueue`, `ArrayDeque`, `LinkedList` (implements `Deque`).
*Note: `Map` (`HashMap`, `TreeMap`, `LinkedHashMap`) does NOT extend `Collection`; it represents key-value pairs.*

---

### Q27: How does `ArrayList` work internally vs `LinkedList`?
**Direct Answer:**  
- `ArrayList`: Backed by a dynamic array. Default initial capacity is 10. When full, it grows by $50\%$ (`newCapacity = oldCapacity + (oldCapacity >> 1)`). Provides $O(1)$ random access by index; $O(N)$ insertions/deletions in the middle due to element shifting.
- `LinkedList`: Backed by a Doubly-Linked List (Node with `prev`, `next`, `item`). $O(1)$ insertion/deletion at head/tail; $O(N)$ access by index because it must traverse from head or tail. High memory overhead per node. In 99% of enterprise cases, `ArrayList` is faster due to CPU cache locality.

---

### Q28: How does `HashMap` work internally in Java 8?
**Direct Answer:**  
`HashMap` is an array of `Node<K, V>` buckets.
1. **Hashing & Indexing:** `index = (n - 1) & hash(key)`. The hash function mixes higher bits down (`key.hashCode() ^ (h >>> 16)`) to minimize collisions.
2. **Bucket Storage:**
   - Keys with matching bucket indices form a linked list.
   - **Treeification (Java 8):** If the number of items in a bucket reaches **8** and total map capacity $\ge 64$, the linked list is converted into a **Red-Black Tree** (`TreeNode`).
   - If bucket size shrinks to **6** during resizing, it converts back to a linked list.
3. **Resizing:** When `size > capacity * loadFactor` (default $16 \times 0.75 = 12$), capacity doubles.

---

### Q29: What happens when two distinct keys produce the same `hashCode()`?
**Direct Answer:**  
This is a **Hash Collision**.
1. Both keys map to the same bucket index `(n - 1) & hash`.
2. `HashMap` traverses the bucket's linked list or Red-Black Tree.
3. For each node, it checks: `node.hash == hash && (node.key == key || key.equals(node.key))`.
4. If `equals()` returns `true`, the value is overwritten.
5. If `equals()` returns `false` for all nodes, a new node is appended to the tail of the list (or inserted into the Red-Black tree).

---

### Q30: Why is `HashMap` capacity always a power of 2?
**Direct Answer:**  
For bitwise optimization. To map a hash value to a bucket index within array bounds `[0, n - 1]`:
- Standard modulo `hash % n` is computationally expensive on the CPU.
- If $n$ is a power of 2, `hash % n` is mathematically equivalent to the bitwise AND operation:
  $$\text{index} = (n - 1) \& \text{hash}$$
Bitwise AND is executed in a single CPU cycle. Furthermore, $(n - 1)$ has all lower bits set to `1` (e.g. $16 - 1 = 15 = 00001111_2$), ensuring uniform distribution of hash bits across buckets.

---

### Q31: What is the difference between `HashMap`, `LinkedHashMap`, and `TreeMap`?
**Direct Answer:**  
- `HashMap`: No order guarantee. $O(1)$ average time for `get()` and `put()`. Allows one `null` key.
- `LinkedHashMap`: Extends `HashMap` with a doubly-linked list running through all entries. Maintains **insertion order** (or **access order** for LRU cache). $O(1)$ operations with slight memory overhead.
- `TreeMap`: Implements `NavigableMap` backed by a Red-Black Tree. Maintains **sorted natural order** (or custom `Comparator`). $O(\log N)$ for `get()`, `put()`, `remove()`. Does NOT allow `null` keys.

---

### Q32: What is the difference between `HashMap` and `Hashtable`?
**Direct Answer:**  
- `HashMap`: Non-synchronized (not thread-safe), allows one `null` key and multiple `null` values, fast, introduced in Java 1.2.
- `Hashtable`: Obsolete legacy class from Java 1.0. All methods are `synchronized` (locks entire table), does NOT allow `null` keys or values, slower due to lock contention. Replaced by `ConcurrentHashMap`.

---

### Q33: How does `ConcurrentHashMap` achieve high concurrency in Java 8?
**Direct Answer:**  
Java 8 completely abandoned the Java 7 Segment locks (which partitioned into 16 lock segments):
1. **CAS (Compare-And-Swap):** When inserting into an empty bucket, it uses lock-free hardware CAS operations (`Unsafe.compareAndSwapObject`).
2. **Synchronized per Bucket:** If a collision occurs and the bucket already contains nodes, it locks **only the first node (head)** of that specific bucket using `synchronized(node)`.
3. **Concurrent Reads:** Read operations (`get()`) are completely lock-free because node values and pointers are marked `volatile`.
4. Different threads can read and write to different buckets simultaneously with zero blocking.

---

### Q34: How does `HashSet` work under the hood?
**Direct Answer:**  
`HashSet` is internally backed by a `HashMap`:
```java
private transient HashMap<E, Object> map;
private static final Object PRESENT = new Object(); // dummy value
```
When you call `hashSet.add(element)`, it internally executes `map.put(element, PRESENT)`. If `put()` returns `null`, the element was successfully added; if it returns `PRESENT`, it was a duplicate and ignored.

---

### Q35: What is the difference between Fail-Fast and Fail-Safe Iterators?
**Direct Answer:**  
- **Fail-Fast:** Operates directly on the collection's data structure. Maintains a `modCount` internal counter. If the collection is structurally modified during iteration (except through the iterator's own `remove()` method), it immediately throws `ConcurrentModificationException` (e.g. `ArrayList`, `HashMap`, `HashSet`).
- **Fail-Safe (Weakly Consistent):** Operates on a clone or snapshot of the collection (or traverses without throwing). Does NOT throw `ConcurrentModificationException` if modified concurrently (e.g. `CopyOnWriteArrayList`, `ConcurrentHashMap`).

---

### Q36: How does `CopyOnWriteArrayList` work and when should it be used?
**Direct Answer:**  
Every write operation (`add()`, `set()`, `remove()`) makes a fresh copy of the entire underlying array, modifies the copy, and swaps the internal volatile array reference.  
- **Reads:** Completely lock-free, blazing fast.
- **Writes:** Extremely expensive ($O(N)$ copy).
- **Use Case:** High read-to-write ratio (e.g. event listener registries, routing tables).

---

### Q37: What is the difference between `ArrayDeque` and `LinkedList` as a Queue/Stack?
**Direct Answer:**  
`ArrayDeque` is backed by a circular resizable array without node allocation overhead; `LinkedList` allocates a new `Node` object for every element.  
`ArrayDeque` is significantly faster than `LinkedList` for both Stacks and Queues due to cache locality and zero GC allocation overhead. `ArrayDeque` does not permit `null` elements.

---

### Q38: What data structure backs `PriorityQueue` and what are its time complexities?
**Direct Answer:**  
`PriorityQueue` is backed by a **Binary Min-Heap** stored in a dynamic array (`Object[] queue`).
- `offer()` / `add()`: $O(\log N)$ (bubble-up)
- `poll()` / `remove()`: $O(\log N)$ (bubble-down / heapify)
- `peek()` / `element()`: $O(1)$ (reads root at index 0)
- `contains()`: $O(N)$ (linear scan)

---

### Q39: What is the Load Factor in `HashMap` and why is 0.75 the default?
**Direct Answer:**  
The load factor is the threshold ratio:
$$\text{Threshold} = \text{Capacity} \times \text{Load Factor}$$
When `size > threshold`, the array doubles in size and rehashes.
- **Default 0.75:** Represents the optimal trade-off between time complexity ($O(1)$ lookups) and space utilization. A higher factor (e.g. 0.9) saves RAM but increases collisions and traversal times. A lower factor (e.g. 0.5) wastes 50% of array memory and triggers frequent expensive resizes.

---

### Q40: How does `Collections.unmodifiableList(list)` differ from `List.of()` in Java 9+?
**Direct Answer:**  
- `Collections.unmodifiableList(originalList)`: A **read-only view wrapper**. If the underlying `originalList` is modified later, the unmodifiable view reflects those mutations!
- `List.of(...)`: A **truly immutable collection**. No underlying list exists to mutate, rejects `null` elements, and is more memory-efficient.

---

### Q41: What is the difference between `Arrays.asList()` and `new ArrayList<>()`?
**Direct Answer:**  
`Arrays.asList(arr)` returns a fixed-size wrapper around the original array (`java.util.Arrays$ArrayList`).
- Calling `add()` or `remove()` throws `UnsupportedOperationException`.
- Mutating an element (`list.set(0, "val")`) directly modifies the underlying array!  
`new ArrayList<>(Arrays.asList(arr))` creates an independent, modifiable dynamic array.

---

### Q42: How do you implement a simple LRU Cache using `LinkedHashMap`?
**Direct Answer:**  
Enable `accessOrder = true` in the constructor and override `removeEldestEntry()`:
```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true); // true = access-order
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity; // evicts least recently accessed
    }
}
```

---

### Q43: Can `null` be used as a key in `HashMap`, `TreeMap`, and `ConcurrentHashMap`?
**Direct Answer:**  
- `HashMap`: **YES** (allows 1 `null` key, stored at bucket index 0).
- `LinkedHashMap`: **YES**.
- `TreeMap`: **NO** (throws `NullPointerException` because it must invoke `compareTo()` to sort keys).
- `ConcurrentHashMap`: **NO** (throws `NullPointerException` for both keys and values to prevent ambiguity in multithreaded environments where `map.get(key) == null` could mean "key not found" or "value is null").

---

### Q44: What is the worst-case time complexity of `HashMap.get()` in Java 7 vs Java 8?
**Direct Answer:**  
- **Java 7:** $O(N)$ (if malicious keys cause all entries to collide into a single bucket's linked list, leading to HashDoS attacks).
- **Java 8:** $O(\log N)$ (because buckets exceeding 8 elements transform into balanced Red-Black Trees).

---

### Q45: What is `IdentityHashMap` and how does it differ from `HashMap`?
**Direct Answer:**  
`IdentityHashMap` compares keys using **reference equality (`==`)** rather than value equality (`equals()`), and uses `System.identityHashCode(key)` instead of `key.hashCode()`. Used internally by serialization frameworks, deep-copy engines, and JVM proxies to track object graph cycles.

---

### Q46: What is `WeakHashMap` and how does it prevent memory leaks?
**Direct Answer:**  
In `WeakHashMap`, keys are stored as **Weak References (`WeakReference`)**. If a key object no longer has any strong references pointing to it outside the map, the garbage collector reclaims the key on its next pass, and the map automatically purges the entry. Frequently used for temporary metadata or memory-sensitive caches.

---

### Q47: How does `Collections.synchronizedMap()` compare to `ConcurrentHashMap`?
**Direct Answer:**  
- `Collections.synchronizedMap(map)`: Wraps the map in an object where every method acquires a **single global mutex lock (`synchronized (mutex)`)**. All threads block each other on every read and write, causing severe contention.
- `ConcurrentHashMap`: Lock-free reads, CAS on empty buckets, bucket-level fine-grained locking. Highly scalable across multicore CPUs.

---

### Q48: How does Java 8 `Map.computeIfAbsent()` work and why is it preferred?
**Direct Answer:**  
`computeIfAbsent(key, mappingFunction)` atomically computes the value only if the key is not already present, inserts it, and returns the result. It eliminates the anti-pattern of `get()` $\rightarrow$ check `null` $\rightarrow$ `put()`, preventing race conditions in `ConcurrentHashMap`:
```java
map.computeIfAbsent("users", k -> new ArrayList<>()).add(user);
```

---

### Q49: What is `EnumSet` and why is it extremely fast?
**Direct Answer:**  
`EnumSet` is a specialized `Set` implementation for enum types. Internally, elements are represented as a **single bitmask (`long` bit vector)**. Operations like `contains`, `add`, and set intersections (`retainAll`) translate to ultra-fast bitwise CPU instructions (`AND`, `OR`, `NOT`) without heap allocation or hashing overhead.

---

### Q50: How does `BlockingQueue` facilitate Producer-Consumer architectures?
**Direct Answer:**  
`BlockingQueue` (`ArrayBlockingQueue`, `LinkedBlockingQueue`) provides thread-safe operations that wait for conditions to be satisfied:
- `put(e)`: Blocks the producer thread if the queue is full until space becomes available.
- `take()`: Blocks the consumer thread if the queue is empty until an element arrives.  
Internally implemented using `ReentrantLock` with two `Condition` variables (`notFull` and `notEmpty`).
