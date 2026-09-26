# 🟨 JavaScript Objects, Prototypes & ES6+ (Q26 – Q50)

---

### Q26: What is the difference between `prototype` and `__proto__`?
**Direct Answer:**  
- `prototype`: A property that exists **only on functions / constructor functions**. It defines the blueprint of properties and methods that will be inherited by instances created via `new MyFunc()`.
- `__proto__` (or `Object.getPrototypeOf(obj)`): An internal accessor property that exists on **every object instance**. It points directly to the prototype object from which this instance was constructed.
- **Invariant:** `const a = new Foo(); a.__proto__ === Foo.prototype`.

---

### Q27: How does the Prototype Chain work? What happens when a property is looked up?
**Direct Answer:**  
When accessing `obj.property`:
1. The JS engine checks if `property` exists directly on `obj` (own property: `obj.hasOwnProperty('property')`).
2. If not found, it traverses up to `obj.__proto__`.
3. It continues walking up the prototype chain (`obj.__proto__.__proto__`) until it either finds the property or reaches `Object.prototype.__proto__`, which is `null`.
4. If `null` is reached without finding the property, it returns `undefined`.

---

### Q28: What is `Object.create(null)` and why is it used?
**Direct Answer:**  
`Object.create(null)` creates a completely **pure dictionary object with zero prototype chain** (`obj.__proto__ === undefined`).  
It does not inherit any methods or properties from `Object.prototype` (no `toString`, `hasOwnProperty`, `valueOf`, `constructor`).  
- **Use Case:** Safe key-value maps and hash tables immune to prototype pollution attacks or accidental key collisions with built-in method names.

---

### Q29: How does ES6 `class` work under the hood?
**Direct Answer:**  
ES6 classes are **syntactic sugar over prototypal inheritance and constructor functions**:
```javascript
class Person {
    constructor(name) { this.name = name; }
    greet() { return `Hi, ${this.name}`; }
}
```
Is functionally compiled by Babel/V8 to:
```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.greet = function() {
    return `Hi, ${this.name}`;
};
```
*Key differences:* Classes are always in strict mode, cannot be called without `new`, and class methods are non-enumerable by default.

---

### Q30: What are Private Class Fields (`#field`) in modern JavaScript?
**Direct Answer:**  
Introduced in ES2022. Prefixing a field or method with `#` enforces **true hard privacy at the language level**:
```javascript
class BankAccount {
    #balance = 0; // private field

    deposit(amount) {
        this.#balance += amount;
    }

    getBalance() {
        return this.#balance;
    }
}
const account = new BankAccount();
console.log(account.#balance); // SyntaxError: Private field '#balance' must be declared in an enclosing class
```
Unlike TypeScript `private` (which is erased at compile-time and accessible at runtime), JS private fields cannot be accessed outside the class even using `Object.keys()` or bracket notation.

---

### Q31: What is the difference between `for...in` and `for...of`?
**Direct Answer:**  
- `for...in`: Iterates over the **enumerable property names / keys** of an object (including inherited properties on the prototype chain). Should NOT be used for arrays.
- `for...of`: Iterates over the **values** of an **iterable collection** (Arrays, Strings, Maps, Sets, NodeLists) that implements the `Symbol.iterator` protocol. Cannot iterate over plain objects unless `Object.entries(obj)` is used.

---

### Q32: What is the `Symbol` primitive type and what are Well-Known Symbols?
**Direct Answer:**  
A `Symbol` is a unique and immutable primitive value (`const s = Symbol("desc")`). Even if two symbols share the same description, `Symbol("a") === Symbol("a")` is always `false`.  
- **Use Cases:** Object property keys that must not collide with other keys, and creating private/internal metadata.
- **Well-Known Symbols (Built-in engine hooks):**
  - `Symbol.iterator`: Defines the default iterator for an object.
  - `Symbol.hasInstance`: Customizes `instanceof` behavior.
  - `Symbol.toPrimitive`: Controls how an object converts to primitive.

---

### Q33: How do the Iterable and Iterator protocols work?
**Direct Answer:**  
- **Iterable Protocol:** An object is iterable if it implements a method at key `[Symbol.iterator]()` that returns an iterator object.
- **Iterator Protocol:** An iterator is an object that implements a `next()` method, which returns an object `{ value: any, done: boolean }`.
```javascript
const customIterable = {
    [Symbol.iterator]() {
        let step = 0;
        return {
            next() {
                if (step < 3) return { value: ++step, done: false };
                return { value: undefined, done: true };
            }
        };
    }
};
for (const val of customIterable) console.log(val); // 1, 2, 3
```

---

### Q34: What are Generator Functions (`function*`) and `yield`?
**Direct Answer:**  
A generator is a function that can **pause its execution** and resume later, maintaining its variable state across yields:
- Calling a generator returns a Generator Iterator.
- Invoking `.next()` runs code until the next `yield <value>` expression.
- `yield*`: Delegates to another generator or iterable.
```javascript
function* idGenerator() {
    let id = 1;
    while (true) {
        yield id++;
    }
}
const gen = idGenerator();
gen.next().value; // 1
gen.next().value; // 2
```

---

### Q35: When should you use `Map` instead of a plain `Object`?
**Direct Answer:**  
Use `Map` when:
1. **Keys are not strings:** `Map` supports any key type (objects, functions, primitives). Objects only support string and symbol keys.
2. **Key order matters:** `Map` guarantees insertion order during iteration.
3. **Frequent insertions/deletions:** `Map` is specifically optimized for high-frequency writes and deletes ($O(1)$).
4. **Size lookup:** `map.size` is $O(1)$; an Object requires $O(N)$ `Object.keys(obj).length`.
5. **No prototype collisions:** `Map` has no default keys (safe from prototype pollution).

---

### Q36: How does `WeakMap` differ from `Map` and how does it prevent memory leaks?
**Direct Answer:**  
1. **Weak References:** Keys in a `WeakMap` **must be objects**. The map holds **weak references** to its keys.
2. **Automatic Garbage Collection:** If there are no other strong references to a key object anywhere in the application, the key and its associated value are automatically reclaimed by the GC.
3. **Not Iterable:** Cannot call `.size`, `.keys()`, or iterate over a `WeakMap` because GC timing is non-deterministic.
4. **Use Case:** Associating metadata with DOM nodes without preventing DOM node garbage collection when the element is removed from the DOM.

---

### Q37: What is the difference between Nullish Coalescing (`??`) and Logical OR (`||`)?
**Direct Answer:**  
- `a || b`: Returns `b` if `a` is **any falsy value** (`false`, `0`, `""`, `null`, `undefined`, `NaN`).
  - *Bug:* `const count = 0; const final = count || 10;` $\rightarrow$ evaluates to `10`!
- `a ?? b`: Returns `b` **ONLY if `a` is `null` or `undefined`**.
  - `const count = 0; const final = count ?? 10;` $\rightarrow$ evaluates to `0` (correct!).

---

### Q38: What is the difference between CommonJS and ES Modules (ESM)?
**Direct Answer:**  
- **CommonJS (`require` / `module.exports`):**
  - Synchronous loading at runtime.
  - Dynamically evaluated (can `require()` inside `if` statements).
  - Copies values (primitive export mutations are not reflected).
  - Used by legacy Node.js.
- **ES Modules (`import` / `export`):**
  - Asynchronous / static structure analyzed at parse time before code runs.
  - Enables **Tree Shaking** (dead code elimination).
  - Live read-only bindings (export mutations are immediately visible to importers).
  - Modern web standard and Node.js (`"type": "module"`).

---

### Q39: What is Tree Shaking and why does it require ES Modules?
**Direct Answer:**  
Tree shaking is an optimization performed by bundlers (Webpack, Rollup, Vite) that eliminates unused exported code from the production JavaScript bundle.  
Because ES Module `import` and `export` statements are **static** (must appear at the top level, cannot be placed inside `if` blocks), the bundler can construct the complete dependency graph and determine with 100% mathematical certainty which functions are never imported, stripping them out. CommonJS `require()` is dynamic, making static analysis impossible.

---

### Q40: How do `Proxy` and `Reflect` work in modern JavaScript?
**Direct Answer:**  
- **`Proxy(target, handler)`:** Wraps a target object and intercepts fundamental operations (property lookup, assignment, enumeration, function invocation) via "traps" (`get`, `set`, `has`, `deleteProperty`). Powers the reactivity systems of **Vue 3** and MobX.
- **`Reflect`:** Built-in object providing static methods that mirror Proxy traps, ensuring default language semantics are executed cleanly:
```javascript
const user = { name: "Amlan" };
const reactiveUser = new Proxy(user, {
    get(target, prop, receiver) {
        console.log(`Accessing ${prop}`);
        return Reflect.get(target, prop, receiver);
    },
    set(target, prop, value, receiver) {
        console.log(`Setting ${prop} = ${value}`);
        return Reflect.set(target, prop, value, receiver);
    }
});
```

---

### Q41: Why does `0.1 + 0.2 !== 0.3` in JavaScript? How do you handle financial math?
**Direct Answer:**  
JavaScript numbers are 64-bit binary floating-point numbers conforming to the **IEEE 754 standard**.  
Numbers like $0.1$ and $0.2$ cannot be represented precisely in base-2 binary fractions, creating an infinite repeating fraction:
`0.1 + 0.2 = 0.30000000000000004`.  
**Solutions:**
1. Store currency as **integer cents** ($10.99 \rightarrow 1099$).
2. Use `Number.EPSILON`: `Math.abs((0.1 + 0.2) - 0.3) < Number.EPSILON`.
3. Use specialized arbitrary-precision libraries (`decimal.js`, `bignumber.js`).

---

### Q42: What is the new ES2023 non-mutating Array methods?
**Direct Answer:**  
Historically, `sort()`, `reverse()`, and `splice()` mutated the original array in place.  
ES2023 introduced non-mutating equivalents that return a fresh array copy:
1. `arr.toSorted()` (instead of `arr.sort()`)
2. `arr.toReversed()` (instead of `arr.reverse()`)
3. `arr.toSpliced(start, deleteCount, ...items)`
4. `arr.with(index, value)` (replaces element at index immutably)

---

### Q43: Write a polyfill for `Function.prototype.bind()`.
**Direct Answer:**  
```javascript
Function.prototype.myBind = function(context, ...bindArgs) {
    if (typeof this !== 'function') {
        throw new TypeError('Not callable');
    }
    const fn = this;
    return function(...callArgs) {
        return fn.apply(context, [...bindArgs, ...callArgs]);
    };
};
```

---

### Q44: Write a polyfill for `Array.prototype.flat()`.
**Direct Answer:**  
```javascript
Array.prototype.myFlat = function(depth = 1) {
    function flatten(arr, currentDepth) {
        return arr.reduce((acc, item) => {
            if (Array.isArray(item) && currentDepth > 0) {
                acc.push(...flatten(item, currentDepth - 1));
            } else {
                acc.push(item);
            }
            return acc;
        }, []);
    }
    return flatten(this, depth);
};
```

---

### Q45: How do Tagged Template Literals work?
**Direct Answer:**  
A tagged template invokes a parsing function with an array of raw strings and the interpolated expression values:
```javascript
function highlight(strings, ...values) {
    return strings.reduce((acc, str, i) => 
        acc + str + (values[i] ? `<b>${values[i]}</b>` : ''), ''
    );
}
const user = "Amlan";
const role = "Admin";
const html = highlight`User ${user} has role ${role}.`;
// "User <b>Amlan</b> has role <b>Admin</b>."
```
Powers libraries like **styled-components** (`styled.div\`color: red;\``) and SQL tag builders.

---

### Q46: What is the difference between `Object.entries()` and `Object.fromEntries()`?
**Direct Answer:**  
- `Object.entries(obj)`: Transforms an object into an array of `[key, value]` pairs: `{ a: 1, b: 2 } -> [['a', 1], ['b', 2]]`.
- `Object.fromEntries(iterable)`: Reverses the operation, transforming an array of key-value pairs or a `Map` back into a plain object: `[['a', 1], ['b', 2]] -> { a: 1, b: 2 }`.

---

### Q47: How does Destructuring with aliasing and default values work?
**Direct Answer:**  
```javascript
const response = { user_name: "Amlan", age: null };
const { 
    user_name: username = "Guest", // Aliased to 'username'
    role = "USER",                  // Default value
    age = 25                        // Note: null does NOT trigger default value! (only undefined does)
} = response;
// username = "Amlan", role = "USER", age = null
```

---

### Q48: What are Set operations in ES2024?
**Direct Answer:**  
ES2024 added native mathematical set methods:
```javascript
const setA = new Set([1, 2, 3]);
const setB = new Set([2, 3, 4]);

setA.intersection(setB);         // Set(2) { 2, 3 }
setA.union(setB);                // Set(4) { 1, 2, 3, 4 }
setA.difference(setB);           // Set(1) { 1 }
setA.symmetricDifference(setB);  // Set(2) { 1, 4 }
setA.isSubsetOf(setB);           // false
```

---

### Q49: What is `Intl.DateTimeFormat` and why is it preferred over `Date.toLocaleDateString()`?
**Direct Answer:**  
Creating new locale strings directly in loops is slow because the browser repeatedly reconstructs locale collators.  
`Intl.DateTimeFormat` pre-compiles and reuses the locale formatter instance, resulting in **10x to 50x faster date formatting** across large data grids and tables:
```javascript
const formatter = new Intl.DateTimeFormat('en-US', { dateStyle: 'medium', timeStyle: 'short' });
console.log(formatter.format(new Date()));
```

---

### Q50: How do you implement a robust GroupBy helper using `Array.prototype.reduce()` or `Object.groupBy()`?
**Direct Answer:**  
- **Modern ES2024 standard:**
  ```javascript
  const inventory = [{ type: "fruit", name: "apple" }, { type: "veg", name: "carrot" }];
  const grouped = Object.groupBy(inventory, item => item.type);
  ```
- **Reduce Polyfill:**
  ```javascript
  function groupBy(arr, keyFn) {
      return arr.reduce((acc, item) => {
          const key = keyFn(item);
          (acc[key] = acc[key] || []).push(item);
          return acc;
      }, {});
  }
  ```
