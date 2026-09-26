# 🟨 JavaScript Interview Mastery: Top 100 Questions & Answers

A comprehensive collection of **100 high-yield, production-grade JavaScript interview questions and answers** for SDE 2 and Senior Frontend/Full-Stack Engineers. Divided into 4 structured modules covering engine execution, prototypal architecture, asynchronous event loop mechanics, and DOM/security patterns.

---

## 🗺️ Curriculum Module Index

| Module | Topic Range | File Link | Question Count | Core Subjects Covered |
|:---:|---|---|:---:|---|
| **01** | **Engine, Execution Context & Scopes** | [01-engine-execution-and-scope.md](./01-engine-execution-and-scope.md) | **Q1 – Q25** | V8 engine (Ignition/TurboFan), Hoisting & TDZ, Closures, `this` binding, `call`/`apply`/`bind`, Debounce & Throttle. |
| **02** | **Objects, Prototypes, Classes & ES6+** | [02-objects-prototypes-and-classes.md](./02-objects-prototypes-and-classes.md) | **Q26 – Q50** | `__proto__` vs `prototype`, ES6 classes, Private fields `#`, Symbols, Iterators & Generators, Map/Set, Tree Shaking. |
| **03** | **Asynchronous JS, Event Loop & Promises** | [03-async-event-loop-and-promises.md](./03-async-event-loop-and-promises.md) | **Q51 – Q75** | Event loop, Microtasks vs Macrotasks, `Promise.all` polyfill, `async/await`, `AbortController`, Web Workers, DataLoader. |
| **04** | **Web APIs, DOM, Security & Patterns** | [04-es6-plus-web-apis-patterns.md](./04-es6-plus-web-apis-patterns.md) | **Q76 – Q100** | Critical Rendering Path, Reflow vs Repaint, Event Delegation, XSS & CSRF defense, CSP, IntersectionObserver, Virtualization. |
| | **TOTAL** | | **100 Questions** | **100% Comprehensive SDE 2 Mastery** |

---

## 🎯 Master List of All 100 Questions

### Module 01: Engine, Execution Context & Scopes (Q1 – Q25)
1. How does the V8 JavaScript Engine execute code?
2. What is an Execution Context and what happens during its two phases?
3. What is Hoisting and the Temporal Dead Zone (TDZ)?
4. Explain Lexical Scope and Scope Chain.
5. What is a Closure and what are its practical use cases?
6. Can Closures cause Memory Leaks? How?
7. What are the 7 Primitive Data Types in JavaScript? How do they differ from Objects?
8. Why does `typeof null` return "object"? How do you reliably check for null?
9. What is the difference between `==` and `===`?
10. What are the 8 Falsy values in modern JavaScript?
11. What is the difference between undefined, null, undeclared, and NaN?
12. How does the `this` keyword work in JavaScript? (The 5 Binding Rules)
13. What are the differences between `call()`, `apply()`, and `bind()`?
14. How do Arrow Functions differ from Regular Functions?
15. What is Strict Mode ("use strict") and what does it prevent?
16. How does Garbage Collection work in V8? (Scavenger vs Mark-Sweep)
17. What is the difference between Shallow Copy and Deep Copy? How do you deep clone?
18. What is Currying in JavaScript and how do you write a generic curry function?
19. What is Debounce vs Throttle? When do you use each?
20. Write an implementation of debounce with immediate (leading-edge) support.
21. Write an implementation of throttle using timestamps.
22. What are Pure Functions and why do they matter in frontend frameworks?
23. What is the difference between `Object.freeze()` and `Object.seal()`?
24. What are Property Descriptors in JavaScript?
25. How does JavaScript pass arguments: By Value or By Reference?

### Module 02: Objects, Prototypes & ES6+ (Q26 – Q50)
26. What is the difference between `prototype` and `__proto__`?
27. How does the Prototype Chain work? What happens when a property is looked up?
28. What is `Object.create(null)` and why is it used?
29. How does ES6 `class` work under the hood?
30. What are Private Class Fields (`#field`) in modern JavaScript?
31. What is the difference between `for...in` and `for...of`?
32. What is the Symbol primitive type and what are Well-Known Symbols?
33. How do the Iterable and Iterator protocols work?
34. What are Generator Functions (`function*`) and `yield`?
35. When should you use `Map` instead of a plain `Object`?
36. How does `WeakMap` differ from `Map` and how does it prevent memory leaks?
37. What is the difference between Nullish Coalescing (`??`) and Logical OR (`||`)?
38. What is the difference between CommonJS and ES Modules (ESM)?
39. What is Tree Shaking and why does it require ES Modules?
40. How do `Proxy` and `Reflect` work in modern JavaScript?
41. Why does `0.1 + 0.2 !== 0.3` in JavaScript? How do you handle financial math?
42. What is the new ES2023 non-mutating Array methods?
43. Write a polyfill for `Function.prototype.bind()`.
44. Write a polyfill for `Array.prototype.flat()`.
45. How do Tagged Template Literals work?
46. What is the difference between `Object.entries()` and `Object.fromEntries()`?
47. How does Destructuring with aliasing and default values work?
48. What are Set operations in ES2024?
49. What is `Intl.DateTimeFormat` and why is it preferred over `Date.toLocaleDateString()`?
50. How do you implement a robust GroupBy helper using `Array.prototype.reduce()` or `Object.groupBy()`?

### Module 03: Asynchronous JS, Event Loop & Promises (Q51 – Q75)
51. Describe the Browser Event Loop architecture.
52. What is the difference between Microtasks and Macrotasks?
53. Trace the output of this classic Event Loop interview puzzle.
54. What are the three states of a Promise? What is "settled"?
55. Compare `Promise.all()`, `Promise.allSettled()`, `Promise.race()`, and `Promise.any()`.
56. Write a polyfill for `Promise.all()`.
57. How does `async/await` work under the hood?
58. What is the sequential `await` loop anti-pattern and how do you fix it?
59. How do you cancel in-flight HTTP requests using `AbortController`?
60. Implement a retry utility with exponential backoff and jitter.
61. Why does `fetch()` NOT reject on HTTP 404 or 500 status codes?
62. What is `process.nextTick()` in Node.js and where does it fit in the Event Loop?
63. What is `setImmediate()` vs `setTimeout(fn, 0)` in Node.js?
64. What is `requestAnimationFrame` (rAF) and why is it preferred over `setTimeout`?
65. How do Web Workers enable true multithreading in JavaScript?
66. How do you limit concurrent asynchronous promises (`p-limit` implementation)?
67. What is the DataLoader pattern in JavaScript?
68. How do Service Workers work and how do they enable Progressive Web Apps (PWAs)?
69. What are Async Iterators and the `for await...of` loop?
70. What is the Streams API in modern JavaScript?
71. How do you implement a Pub-Sub (EventEmitter) from scratch in JavaScript?
72. What is the difference between synchronous code, Microtasks, Macrotasks, and Animation Frames in rendering timing?
73. What is `queueMicrotask()` and when should you use it?
74. What is Top-Level `await`?
75. How do you prevent race conditions when searching with dynamic async API calls?

### Module 04: Web APIs, DOM, Security & Patterns (Q76 – Q100)
76. Describe the Critical Rendering Path (CRP) in the browser.
77. What is the difference between Reflow and Repaint? How do you optimize for 60fps?
78. Explain Event Propagation: Capturing, Target, and Bubbling phases.
79. What is Event Delegation and why is it essential for performance?
80. What is the difference between `preventDefault()`, `stopPropagation()`, and `stopImmediatePropagation()`?
81. Compare Web Storage: `localStorage`, `sessionStorage`, `IndexedDB`, and `Cookies`.
82. What are the security attributes of Cookies: `HttpOnly`, `Secure`, and `SameSite`?
83. What is Cross-Site Scripting (XSS) and how do you prevent it?
84. What is Cross-Site Request Forgery (CSRF) and how do you prevent it?
85. What is Content Security Policy (CSP)?
86. How does the `IntersectionObserver` API work and why is it preferred for Infinite Scroll and Lazy Loading?
87. What are Detached DOM Nodes and how do they cause Memory Leaks?
88. How does a `DocumentFragment` optimize batch DOM insertions?
89. How does DOM Virtualization / Windowing work for rendering 100,000 items?
90. What is Shadow DOM and how does it encapsulate styles in Web Components?
91. What triggers a CORS Preflight `OPTIONS` request?
92. What are `ArrayBuffer`, `TypedArray`, and `DataView`?
93. What is Prototype Pollution and how do you mitigate it?
94. How do you implement the Strategy Pattern in JavaScript?
95. How do you implement the Module Pattern and Revealing Module Pattern?
96. What are `ResizeObserver` and `MutationObserver`?
97. What is the difference between `Node.cloneNode(true)` and `Node.cloneNode(false)`?
98. How do you profile memory leaks using Chrome DevTools?
99. What is the difference between `window.onload` and `DOMContentLoaded`?
100. What is Module Federation in Webpack 5 / Modern Bundlers?
