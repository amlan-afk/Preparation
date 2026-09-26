# ☕ Java Core: OOP, Language Fundamentals & Memory (Q1 – Q25)

---

### Q1: What are the core pillars of Object-Oriented Programming (OOP) in Java, and how does Java implement them?
**Direct Answer:**  
The four pillars are **Encapsulation** (data hiding via `private` fields and public getters/setters), **Abstraction** (hiding implementation details via `interface` and `abstract class`), **Inheritance** (`extends` keyword for code reuse and subtyping), and **Polymorphism** (compile-time via method overloading; runtime via method overriding and dynamic method dispatch).

**Code Example:**
```java
// Abstraction & Polymorphism
public interface PaymentProcessor {
    void process(BigDecimal amount);
}

// Encapsulation
public class CreditCardProcessor implements PaymentProcessor {
    private final String merchantId; // private data

    public CreditCardProcessor(String merchantId) {
        this.merchantId = merchantId;
    }

    @Override
    public void process(BigDecimal amount) {
        // Implementation detail hidden
        charge(merchantId, amount);
    }
}
```
**Follow-up / Trap:** Java does not support multiple class inheritance to avoid the Diamond Problem, but allows multiple interface inheritance.

---

### Q2: What is the difference between an `abstract class` and an `interface` in modern Java (Java 8+)?
**Direct Answer:**  
- **State:** Abstract classes can have instance state (fields), instance constructors, and non-static member fields. Interfaces can only have `public static final` constants.
- **Inheritance:** A class can extend only one abstract class, but can implement multiple interfaces.
- **Methods:** Since Java 8, interfaces support `default` and `static` methods; Java 9 added `private` methods. Use an interface to define a contract/capability (`Runnable`, `Comparable`), and an abstract class for a shared base with internal state.

---

### Q3: Why are Strings immutable in Java?
**Direct Answer:**  
Strings are immutable for four critical reasons:
1. **String Pool Caching:** Allows multiple references to point to the same literal in the String Intern Pool without side effects.
2. **Security:** Strings are used for class loading, database URLs, usernames, and file paths; mutability would allow malicious modification after validation.
3. **Thread Safety:** Immutable objects are inherently thread-safe without synchronization.
4. **HashCode Caching:** `hashCode()` is computed once lazily and cached, making Strings ideal keys in `HashMap`.

---

### Q4: What is the difference between `String`, `StringBuilder`, and `StringBuffer`?
**Direct Answer:**  
- `String`: Immutable sequence of characters; every concatenation creates a new object on the heap.
- `StringBuilder`: Mutable sequence of characters, not thread-safe, fastest for single-threaded string concatenation.
- `StringBuffer`: Mutable sequence of characters, thread-safe (methods are `synchronized`), slower due to synchronization overhead.

---

### Q5: How does the JVM handle String creation: `String s = "abc"` vs `String s = new String("abc")`?
**Direct Answer:**  
`String s = "abc"` looks up `"abc"` in the **String Intern Pool** (inside Heap/Metaspace). If found, it returns the existing reference; otherwise, it creates it in the pool.  
`String s = new String("abc")` explicitly creates a **new object in heap memory** outside the pool, even if `"abc"` already exists in the pool, resulting in two objects if `"abc"` wasn't previously pooled.

---

### Q6: What is the difference between `==` and `.equals()`?
**Direct Answer:**  
`==` compares **memory references** (checks if both references point to the exact same memory address).  
`.equals()` is a method on `java.lang.Object` that, by default, checks reference equality (`==`), but is overridden by classes (like `String`, `Integer`, `Date`) to compare **value/state equality**.

---

### Q7: Why must `equals()` and `hashCode()` always be overridden together?
**Direct Answer:**  
Because of the **HashCode Contract**: If two objects are equal according to `equals()`, they **MUST** have the same `hashCode()`.  
If you override `equals()` but not `hashCode()`, two logically equal objects will produce different hash codes. When used as keys in a `HashMap` or elements in a `HashSet`, the second object will be routed to a different bucket and lookups will fail (`map.get(key)` returns `null`).

---

### Q8: What are the rules of the `equals()` contract in Java?
**Direct Answer:**  
The `equals()` method must implement an equivalence relation that is:
1. **Reflexive:** `x.equals(x)` is `true`.
2. **Symmetric:** `x.equals(y)` returns `true` iff `y.equals(x)` returns `true`.
3. **Transitive:** If `x.equals(y)` and `y.equals(z)`, then `x.equals(z)`.
4. **Consistent:** Repeated invocations return the same result unless fields are modified.
5. **Null comparison:** `x.equals(null)` must return `false` (never throw `NullPointerException`).

---

### Q9: What is the difference between pass-by-value and pass-by-reference? How does Java pass parameters?
**Direct Answer:**  
**Java is strictly PASS-BY-VALUE, always.**  
For primitive types (`int`, `boolean`), a copy of the primitive value is passed.  
For reference types (objects), **a copy of the reference (pointer address)** is passed by value. Modifying fields of the object inside the method mutates the original object, but reassigning the reference variable itself inside the method has zero effect on the caller's variable.

---

### Q10: What are Wrapper classes and Autoboxing / Unboxing? What is the hidden trap?
**Direct Answer:**  
Wrapper classes (`Integer`, `Double`, `Boolean`) wrap primitives into objects. **Autoboxing** is automatic conversion from primitive to wrapper (e.g. `int` to `Integer`); **Unboxing** is the reverse.  
**Trap:**  
1. Unboxing a `null` wrapper throws a `NullPointerException` at runtime.
2. `Integer.valueOf()` caches values from `-128` to `127`. For numbers in this range, `Integer a = 100; Integer b = 100; a == b` is `true`. For `a = 200; b = 200; a == b` is `false`!

---

### Q11: What is the difference between `final`, `finally`, and `finalize()`?
**Direct Answer:**  
- `final`: Keyword. Final variable = constant; final method = cannot be overridden; final class = cannot be subclassed.
- `finally`: Block in exception handling (`try-catch-finally`) that executes regardless of whether an exception is thrown, used for resource cleanup.
- `finalize()`: Deprecated method in `Object` historically invoked by GC before object reclamation. Never rely on it (use `AutoCloseable` instead).

---

### Q12: Can a `finally` block ever NOT execute?
**Direct Answer:**  
Yes, only in extreme scenarios:
1. `System.exit(0)` is called.
2. The JVM crashes (e.g. `OutOfMemoryError` or SIGKILL).
3. The underlying operating system power shuts down or hardware fails.
4. An infinite loop or deadlock occurs inside the `try` block.

---

### Q13: What is the difference between Checked and Unchecked (Runtime) Exceptions?
**Direct Answer:**  
- **Checked Exceptions:** Subclasses of `Exception` (excluding `RuntimeException`). Enforced by the compiler at compile-time; must be declared in `throws` or handled in `try-catch` (e.g. `IOException`, `SQLException`). Used for recoverable conditions.
- **Unchecked Exceptions:** Subclasses of `RuntimeException` or `Error`. Not checked at compile-time (e.g. `NullPointerException`, `IllegalArgumentException`, `OutOfMemoryError`). Represent programmer errors or unrecoverable system failures.

---

### Q14: How does Try-With-Resources work in Java 7+?
**Direct Answer:**  
Try-with-resources automatically closes any resource that implements `java.lang.AutoCloseable` or `java.io.Closeable` when exiting the `try` block, even if an exception is thrown. It eliminates boilerplate nested `finally` blocks and properly suppresses secondary exceptions.

**Code Example:**
```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    return br.readLine();
} // Automatically invokes br.close()
```

---

### Q15: What is Method Overloading vs Method Overriding?
**Direct Answer:**  
- **Overloading:** Same method name, different parameter lists (count, types, or order) within the same class. Resolved at **compile-time** (Static Polymorphism).
- **Overriding:** Subclass provides a specific implementation of a method defined in its superclass with the exact same signature and return type (or covariant return). Resolved at **runtime** via virtual method table (`vtable`) lookup (Dynamic Polymorphism).

---

### Q16: What is a Covariant Return Type?
**Direct Answer:**  
When overriding a method, the subclass method can return a **subtype** of the return type declared in the superclass.
```java
class SuperClass {
    public Number getNum() { return 1; }
}
class SubClass extends SuperClass {
    @Override
    public Integer getNum() { return 1; } // Integer is a subtype of Number
}
```

---

### Q17: Can you override a `static` or `private` method in Java?
**Direct Answer:**  
No.
- `private` methods are not visible to subclasses and cannot be overridden.
- `static` methods are bound to the class at compile-time (method hiding, not overriding). If a subclass declares the same static method, it hides the superclass version rather than overriding it.

---

### Q18: What is the difference between Shallow Copy and Deep Copy?
**Direct Answer:**  
- **Shallow Copy:** Copies the object's primitive fields, but for reference fields, it copies only the memory reference. Both the original and cloned objects share references to the same internal objects.
- **Deep Copy:** Recursively clones the object and all nested objects it references, creating completely independent object graphs.

---

### Q19: What is `transient` keyword in Java?
**Direct Answer:**  
The `transient` keyword marks a field to be **skipped during serialization**. When an object is serialized using `Serializable`, transient fields are not written to the byte stream and are restored to their default values (e.g. `null`, `0`) upon deserialization. Frequently used for sensitive data (passwords) or runtime caches.

---

### Q20: What is `volatile` keyword in Java?
**Direct Answer:**  
The `volatile` keyword guarantees **Visibility** and **Ordering** across threads:
1. **Visibility:** Writes to a volatile variable are immediately flushed to main memory, and reads bypass CPU caches to read directly from main memory.
2. **Ordering:** Prevents CPU instruction reordering via memory barriers (Happens-Before guarantee).
*Note: `volatile` does NOT guarantee Atomicity (e.g. `count++` is not atomic).*

---

### Q21: What is the difference between `Comparable` and `Comparator`?
**Direct Answer:**  
- `Comparable<T>`: Defines the **natural ordering** of an object; implemented inside the class itself via `compareTo(T o)` (e.g. `String`, `Integer`).
- `Comparator<T>`: Defines **custom/external sorting logic** separate from the class via `compare(T o1, T o2)`. Multiple comparators can exist for the same class (e.g. sort by age, sort by name).

---

### Q22: What are Generics and Type Erasure?
**Direct Answer:**  
Generics provide compile-time type safety (`List<String>`).  
**Type Erasure:** To maintain backward compatibility with pre-Java 5 bytecode, the Java compiler strips away all generic type parameters during compilation, replacing unbounded types with `Object` and inserting casts where necessary. At runtime, `List<String>` and `List<Integer>` are both just raw `List`.

---

### Q23: What are Wildcards in Generics: `<? extends T>` vs `<? super T>` (PECS Rule)?
**Direct Answer:**  
The **PECS** rule stands for **Producer Extends, Consumer Super**:
- **`<? extends T>` (Producer):** If your generic collection produces items (read-only), use `extends`. You can read `T` from it, but cannot add anything except `null`.
- **`<? super T>` (Consumer):** If your generic collection consumes items (write-only), use `super`. You can safely write `T` into it.

---

### Q24: What is an Enum in Java, and why is it better than `public static final int` constants?
**Direct Answer:**  
A Java `Enum` is a type-safe class that extends `java.lang.Enum`.  
Advantages:
1. **Type Safety:** Compiler prevents passing invalid integer values.
2. **Rich Behavior:** Enums can have fields, constructors, methods, and implement interfaces.
3. **Singleton Guarantee:** JVM guarantees only one instance per enum constant, making Enum the simplest and safest way to implement a thread-safe Singleton with serialization protection.

---

### Q25: How do Java Records (Java 14/16+) differ from traditional POJOs?
**Direct Answer:**  
A `record` is a concise syntax for immutable data-carrier classes:
```java
public record UserDto(Long id, String name, String email) {}
```
The compiler automatically generates private final fields, a canonical constructor, getters (`id()`, `name()`), `equals()`, `hashCode()`, and `toString()`. Records cannot extend other classes (they implicitly extend `java.lang.Record`), but can implement interfaces.
