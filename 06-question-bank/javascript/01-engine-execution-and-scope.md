# 🟨 JavaScript Engine, Execution Context & Scopes (Q1 – Q25)

---

### Q1: How does the V8 JavaScript Engine execute code?
**Direct Answer:**  
V8 parses JavaScript source code into an **Abstract Syntax Tree (AST)**:
1. **Ignition (Interpreter):** Compiles the AST into bytecode and starts executing immediately for fast startup.
2. **Profiler:** Collects runtime type feedback and identifies "hot functions" (code executed repeatedly).
3. **TurboFan (Optimizing Compiler):** Recompiles hot bytecode into highly optimized native machine code using type feedback (inline caching).
4. **Deoptimization:** If an assumption fails (e.g. a function that always received integers suddenly receives a string), TurboFan deoptimizes the code back to Ignition bytecode.

---

### Q2: What is an Execution Context and what happens during its two phases?
**Direct Answer:**  
An Execution Context is an environment in which JavaScript code is evaluated and executed (Global Execution Context, Function Execution Context, Eval).  
Every execution context has two distinct phases:
1. **Creation Phase:**
   - Allocates memory for variables and functions.
   - Sets up the Scope Chain (`OuterEnv`).
   - Determines the binding of `this`.
   - Variables declared with `var` are initialized to `undefined`; functions are fully hoisted; `let` and `const` remain uninitialized in the **Temporal Dead Zone (TDZ)**.
2. **Execution Phase:**
   - Runs code line-by-line, assigning values to variables and executing function calls.

---

### Q3: What is Hoisting and the Temporal Dead Zone (TDZ)?
**Direct Answer:**  
**Hoisting:** The process where the JavaScript engine allocates memory for declarations before executing code.  
- `function declarations`: Fully hoisted with their body. Can be invoked before their declaration line.
- `var`: Hoisted and initialized to `undefined`. Accessing before assignment yields `undefined`.
- `let` and `const`: Hoisted into block scope, but **NOT initialized**.
- **Temporal Dead Zone (TDZ):** The time/code span between entering the block scope and the line where `let` or `const` is declared. Accessing the variable in the TDZ throws a `ReferenceError: Cannot access 'x' before initialization`.

---

### Q4: Explain Lexical Scope and Scope Chain.
**Direct Answer:**  
- **Lexical Scope (Static Scope):** A function's scope is determined by where it is physically written in the source code, NOT where it is invoked.
- **Scope Chain:** When resolving an identifier, the JavaScript engine inspects the local execution context environment. If not found, it traverses upward to the outer enclosing lexical environment, continuing up to the Global Object. If still not found, it throws a `ReferenceError`.

---

### Q5: What is a Closure and what are its practical use cases?
**Direct Answer:**  
A closure is the combination of a function bundled together with references to its surrounding lexical environment. In JavaScript, **every inner function maintains access to the variables of its outer enclosing function even after the outer function has finished executing and its stack frame has returned**.  
**Use Cases:**
1. **Data Encapsulation / Private State:**
   ```javascript
   function createCounter() {
       let count = 0; // private variable
       return {
           increment: () -> ++count,
           getCount: () -> count
       };
   }
   const counter = createCounter();
   counter.increment();
   console.log(counter.getCount()); // 1 (count cannot be mutated directly)
   ```
2. **Memoization / Caching:** Storing previous computation results.
3. **Currying and Partial Application:** Pre-binding parameters.
4. **Event Handlers & Callback Timers.**

---

### Q6: Can Closures cause Memory Leaks? How?
**Direct Answer:**  
**Yes.** Because a closure retains a live reference to its outer lexical environment, the Garbage Collector **cannot collect** any variables in that outer scope as long as the inner closure function remains reachable. If an inner closure references a huge array or DOM element and is attached to a long-lived global event listener or timer, the memory will leak until the listener/closure is detached.

---

### Q7: What are the 7 Primitive Data Types in JavaScript? How do they differ from Objects?
**Direct Answer:**  
The 7 primitives are:
1. `string`
2. `number`
3. `boolean`
4. `null`
5. `undefined`
6. `symbol` (ES6)
7. `bigint` (ES2020)  
**Differences:** Primitives are immutable and stored directly by value (often on the stack or in optimized memory). Objects (`Object`, `Array`, `Function`, `Date`, `Map`) are reference types stored on the heap; variables hold references (pointers) to the memory location.

---

### Q8: Why does `typeof null` return `"object"`? How do you reliably check for `null`?
**Direct Answer:**  
`typeof null === 'object'` is a historical bug from the original 1995 JavaScript implementation. Values were stored in 32-bit units with a 3-bit type tag. The type tag for an object was `000`. `null` was represented as the NULL pointer (`0x00`), which had all zeros, tricking `typeof` into reporting `"object"`.  
**Reliable checks:**
```javascript
value === null;
// Or Object.prototype.toString:
Object.prototype.toString.call(value) === '[object Null]';
```

---

### Q9: What is the difference between `==` and `===`?
**Direct Answer:**  
- `===` (Strict Equality): Compares both **type** and **value** without type conversion. If types differ, returns `false`.
- `==` (Loose Equality): Performs **implicit type coercion** using the Abstract Equality Comparison Algorithm before comparing:
  - `null == undefined` $\rightarrow$ `true`
  - `'5' == 5` $\rightarrow$ `true` (string converted to number)
  - `0 == false` $\rightarrow$ `true`
  - `[] == false` $\rightarrow$ `true` (array coerced to `""`, then to `0`)
  - **Rule:** Always use `===` in production to prevent unexpected coercion bugs.

---

### Q10: What are the 8 Falsy values in modern JavaScript?
**Direct Answer:**  
When coerced to boolean, exactly 8 values evaluate to `false`:
1. `false`
2. `0`, `-0`, `0n` (BigInt zero)
3. `""` (empty string)
4. `null`
5. `undefined`
6. `NaN`
7. `document.all` (historical browser quirk)  
*Everything else is truthy (including `[]`, `{}`, `"0"`, and `"false"`!).*

---

### Q11: What is the difference between `undefined`, `null`, `undeclared`, and `NaN`?
**Direct Answer:**  
- `undefined`: A variable that has been declared but has not yet been assigned a value; or functions without an explicit `return`.
- `null`: An intentional assignment representing the absence of an object value.
- `undeclared`: An identifier that has never been declared in any reachable scope. Accessing it throws `ReferenceError`.
- `NaN` ("Not a Number"): A numeric value (`typeof NaN === 'number'`) representing an invalid or unrepresentable mathematical result (e.g. `0 / 0` or `parseInt("abc")`). `NaN !== NaN` is always `true`.

---

### Q12: How does the `this` keyword work in JavaScript? (The 5 Binding Rules)
**Direct Answer:**  
`this` is determined at **runtime based on how a function is called**:
1. **Default Binding:** Free function invocation: `this` points to `window` (non-strict) or `undefined` (strict mode).
2. **Implicit Binding:** Method invocation (`obj.method()`): `this` points to the object before the dot (`obj`).
3. **Explicit Binding:** Using `call()`, `apply()`, or `bind()` to specify `this`.
4. **`new` Binding:** Calling constructor (`new MyClass()`): `this` points to the newly allocated object.
5. **Lexical Binding (Arrow Functions):** Arrow functions do NOT have their own `this`; they capture `this` lexically from their enclosing scope.

---

### Q13: What are the differences between `call()`, `apply()`, and `bind()`?
**Direct Answer:**  
All three explicitly set the `this` context:
- `fn.call(thisArg, arg1, arg2, ...)`: Invokes the function immediately, passing arguments individually as a comma-separated list.
- `fn.apply(thisArg, [argsArray])`: Invokes the function immediately, passing arguments as an array.
- `fn.bind(thisArg, arg1, ...)`: Does NOT invoke immediately; returns a **new bound function** permanently bound to `thisArg` with preset partial arguments.

---

### Q14: How do Arrow Functions differ from Regular Functions?
**Direct Answer:**  
Arrow functions (`() => {}`):
1. **No `this` binding:** Inherit `this` lexically from outer scope; cannot be overridden by `call`/`apply`/`bind`.
2. **Cannot be constructors:** Calling with `new` throws `TypeError` (no `[[Construct]]` internal method).
3. **No `prototype` property:** Cannot be used for prototypal inheritance.
4. **No `arguments` object:** Must use rest parameters (`(...args) => {}`).
5. **No `super` or `new.target`.**

---

### Q15: What is Strict Mode (`"use strict"`) and what does it prevent?
**Direct Answer:**  
Enables a restricted variant of JavaScript:
1. Prevents accidental globals: Assigning to an undeclared variable (`x = 10`) throws `ReferenceError`.
2. Fails loudly on silent errors: Assigning to read-only properties throws `TypeError`.
3. Sets `this` in plain functions to `undefined` instead of `window`.
4. Disallows duplicate parameter names (`function foo(a, a) {}` is a syntax error).
5. Forbids `with` statement and octal numeric literals (`010`).

---

### Q16: How does Garbage Collection work in V8? (Scavenger vs Mark-Sweep)
**Direct Answer:**  
V8 uses a **Generational Garbage Collection** strategy based on the Weak Generational Hypothesis (most objects die young):
- **Young Generation (New Space):** Handled by the **Scavenger** (Semi-Space algorithm). Memory is divided into `From` and `To` spaces. Surviving live objects are evacuated and packed into `To` space in a few milliseconds.
- **Old Generation (Old Space):** Objects surviving repeated scavenger cycles are promoted to Old Space. Handled by the **Major GC (Mark-Sweep-Compact)**:
  1. *Marking:* Traces live objects from roots.
  2. *Sweeping:* Reclaims dead memory slots.
  3. *Compacting:* Defragments heap memory by relocating live objects.

---

### Q17: What is the difference between Shallow Copy and Deep Copy? How do you deep clone an object?
**Direct Answer:**  
- **Shallow Copy (`{...obj}`, `Object.assign({}, obj)`):** Copies top-level primitive values, but nested objects are copied as references.
- **Deep Copy:** Clones the entire nested object graph independently.
  - **Modern Standard (ES2022+):** `structuredClone(obj)` — Handles nested objects, arrays, Maps, Sets, Dates, circular references!
  - **`JSON.parse(JSON.stringify(obj))`:** Flawed (drops `undefined`, functions, symbols, and converts `Date` to string; crashes on circular references).
  - **Lodash `cloneDeep()`.**

---

### Q18: What is Currying in JavaScript and how do you write a generic curry function?
**Direct Answer:**  
Currying transforms a function of $N$ arguments into a chain of $N$ functions, each taking a single argument:
`f(a, b, c) -> f(a)(b)(c)`

```javascript
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        }
        return function(...nextArgs) {
            return curried.apply(this, args.concat(nextArgs));
        };
    };
}
const sum = (a, b, c) => a + b + c;
const curriedSum = curry(sum);
console.log(curriedSum(1)(2)(3)); // 6
```

---

### Q19: What is Debounce vs Throttle? When do you use each?
**Direct Answer:**  
- **Debounce:** Delays execution until a specified idle duration has passed without any new events. If invoked again before timer expires, the timer resets.
  - *Use Case:* Search typeahead input, auto-saving drafts, window resize.
- **Throttle:** Guarantees execution at a regular fixed interval (at most once every $X\text{ ms}$), ignoring subsequent events during the cooldown.
  - *Use Case:* Infinite scroll window listener, gaming loop, mousemove/drag tracking.

---

### Q20: Write an implementation of `debounce` with immediate (leading-edge) support.
**Direct Answer:**  
```javascript
function debounce(fn, delay, immediate = false) {
    let timer = null;
    return function(...args) {
        const context = this;
        const callNow = immediate && !timer;
        
        clearTimeout(timer);
        timer = setTimeout(() => {
            timer = null;
            if (!immediate) fn.apply(context, args);
        }, delay);
        
        if (callNow) fn.apply(context, args);
    };
}
```

---

### Q21: Write an implementation of `throttle` using timestamps.
**Direct Answer:**  
```javascript
function throttle(fn, limit) {
    let lastCall = 0;
    return function(...args) {
        const now = Date.now();
        if (now - lastCall >= limit) {
            lastCall = now;
            fn.apply(this, args);
        }
    };
}
```

---

### Q22: What are Pure Functions and why do they matter in modern frontend frameworks?
**Direct Answer:**  
A pure function satisfies two rules:
1. **Deterministic:** Given the same inputs, it always returns the exact same output.
2. **Zero Side Effects:** Does not mutate external variables, modify DOM, make network calls, or read global state.  
*Why it matters:* Pure functions enable predictable state transitions (Redux reducers), memoization (`useMemo`, React compiler optimizations), easy unit testing, and concurrent rendering without race conditions.

---

### Q23: What is the difference between `Object.freeze()` and `Object.seal()`?
**Direct Answer:**  
- `Object.freeze(obj)`: Makes the object completely immutable. Cannot add, delete, or modify existing property values. (Shallow freeze; nested objects remain mutable).
- `Object.seal(obj)`: Prevents adding or deleting properties, but **existing properties can still be modified/written** if `writable: true`.

---

### Q24: What are Property Descriptors in JavaScript?
**Direct Answer:**  
Configured via `Object.defineProperty(obj, prop, descriptor)`:
1. `value`: Current value.
2. `writable`: If `false`, value cannot be changed.
3. `enumerable`: If `false`, property is hidden from `for...in` and `Object.keys()`.
4. `configurable`: If `false`, property cannot be deleted and descriptor cannot be modified.

---

### Q25: How does JavaScript pass arguments: By Value or By Reference?
**Direct Answer:**  
JavaScript is strictly **Pass-By-Value (or Call-by-Sharing)**:
- Primitives are copied by value.
- For objects, a **copy of the reference address** is passed by value.
- Modifying a property inside a function (`obj.name = "Amlan"`) mutates the underlying shared heap object.
- But reassigning the parameter reference variable (`obj = { name: "Bob" }`) simply points the local copy to a new address, having zero effect on the caller's original object!
