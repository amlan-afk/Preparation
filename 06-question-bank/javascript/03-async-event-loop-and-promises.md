# 🟨 JavaScript Asynchronous Engine & Event Loop (Q51 – Q75)

---

### Q51: Describe the Browser Event Loop architecture.
**Direct Answer:**  
JavaScript is **single-threaded** with a non-blocking concurrent runtime provided by the host environment (Browser / Node.js):
1. **Call Stack:** Executes synchronous functions in LIFO order.
2. **Web APIs:** Browser threads handle asynchronous operations (timers, DOM events, fetch requests) in background C++ threads.
3. **Microtask Queue:** Highest priority asynchronous queue. Holds callbacks from `Promise.then/catch/finally`, `queueMicrotask`, and `MutationObserver`.
4. **Macrotask (Task) Queue:** Holds callbacks from `setTimeout`, `setInterval`, `setImmediate`, I/O, and UI events.
5. **Event Loop Rule:**
   - Run synchronous code until the Call Stack is empty.
   - Drain the **ENTIRE Microtask Queue** completely (including any microtasks queued while processing microtasks!).
   - Render UI updates (if needed).
   - Pick the **SINGLE oldest Macrotask** from the Task Queue and push it to the Call Stack.
   - Repeat.

---

### Q52: What is the difference between Microtasks and Macrotasks?
**Direct Answer:**  
- **Microtasks (`Promise`, `queueMicrotask`, `MutationObserver`):** High priority. Checked and drained immediately after every synchronous stack frame clears, **before any rendering and before the next macrotask**.
- **Macrotasks (`setTimeout`, `setInterval`, I/O):** Low priority. Processed one per event loop turn.
- **Danger (Microtask Starvation):** Recursively queuing microtasks (`queueMicrotask(() => loop())`) starves the event loop, freezing the UI completely because macrotasks and browser rendering can never execute!

---

### Q53: Trace the output of this classic Event Loop interview puzzle:
```javascript
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve()
    .then(() => {
        console.log('3');
        return Promise.resolve('4');
    })
    .then(res => console.log(res));
queueMicrotask(() => console.log('5'));
console.log('6');
```
**Answer:** `1`, `6`, `3`, `5`, `4`, `2`.  
**Step-by-Step Explanation:**
1. Sync: `console.log('1')` $\rightarrow$ prints `1`.
2. Macrotask: `setTimeout` queues `'2'` to Macrotask Queue.
3. Microtask: `Promise.resolve().then(...)` queues `'3'` callback to Microtask Queue.
4. Microtask: `queueMicrotask` queues `'5'` to Microtask Queue.
5. Sync: `console.log('6')` $\rightarrow$ prints `6`.
6. Stack empty $\rightarrow$ Drain Microtasks:
   - Run first promise callback $\rightarrow$ prints `3`, returns promise queuing `'4'`.
   - Run next microtask $\rightarrow$ prints `5`.
   - Run chained promise microtask $\rightarrow$ prints `4`.
7. Microtasks empty $\rightarrow$ Run Macrotask:
   - Run `setTimeout` callback $\rightarrow$ prints `2`.

---

### Q54: What are the three states of a Promise? What is "settled"?
**Direct Answer:**  
1. **Pending:** Initial state, neither fulfilled nor rejected.
2. **Fulfilled:** Operation completed successfully; has a permanent value.
3. **Rejected:** Operation failed; has a permanent reason (error).  
- **Settled:** A promise that is either fulfilled or rejected.
- **Immutability:** Once a promise transitions from `pending` to a settled state, it is locked; its state and value can never change, and resolving or rejecting it a second time is a silent no-op.

---

### Q55: Compare `Promise.all()`, `Promise.allSettled()`, `Promise.race()`, and `Promise.any()`.
**Direct Answer:**  
- **`Promise.all([p1, p2])`:** Fails fast. Resolves when **ALL** promises resolve (returns array of values); rejects immediately if **ANY single** promise rejects.
- **`Promise.allSettled([p1, p2])`:** Never short-circuits. Waits for all promises to finish (whether resolved or rejected). Returns array of `{ status: 'fulfilled', value }` or `{ status: 'rejected', reason }`.
- **`Promise.race([p1, p2])`:** Settles as soon as the **FIRST** promise settles (resolves or rejects).
- **`Promise.any([p1, p2])`:** Resolves as soon as the **FIRST promise RESOLVES** (ignores rejections). Rejects with `AggregateError` only if **ALL** input promises reject.

---

### Q56: Write a polyfill for `Promise.all()`.
**Direct Answer:**  
```javascript
function myPromiseAll(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Arguments must be an array'));
        }
        const results = [];
        let completed = 0;
        const total = promises.length;

        if (total === 0) return resolve(results);

        promises.forEach((p, index) => {
            Promise.resolve(p).then(
                value => {
                    results[index] = value; // Preserves original ordering!
                    completed++;
                    if (completed === total) {
                        resolve(results);
                    }
                },
                error => {
                    reject(error); // Short-circuit on first failure
                }
            );
        });
    });
}
```

---

### Q57: How does `async/await` work under the hood?
**Direct Answer:**  
`async/await` is syntactic sugar over **Generators (`function*`) and Promises**:
- An `async` function always returns a `Promise`.
- The `await` keyword pauses the execution of the generator function (yielding a promise).
- An automatic runner recursively calls `generator.next(value)` when the yielded promise resolves, resuming execution inside the function, or `generator.throw(err)` if rejected.

---

### Q58: What is the sequential `await` loop anti-pattern and how do you fix it?
**Direct Answer:**  
**The Anti-Pattern (Waterfall):**
```javascript
// BAD: Each request waits for the previous one, taking N * 2s!
for (const id of userIds) {
    const user = await fetchUser(id); 
    results.push(user);
}
```
**The Parallel Fix:**
```javascript
// GOOD: All requests initiate concurrently, taking only 2s total!
const results = await Promise.all(userIds.map(id => fetchUser(id)));
```

---

### Q59: How do you cancel in-flight HTTP requests using `AbortController`?
**Direct Answer:**  
```javascript
const controller = new AbortController();
const signal = controller.signal;

fetch('https://api.example.com/data', { signal })
    .then(res => res.json())
    .catch(err => {
        if (err.name === 'AbortError') {
            console.log('Fetch successfully aborted');
        }
    });

// To cancel:
controller.abort();
```
Essential in typeahead search inputs to cancel stale network requests when the user types a new letter.

---

### Q60: Implement a retry utility with exponential backoff and jitter.
**Direct Answer:**  
```javascript
async function retryWithBackoff(fn, retries = 3, baseDelay = 1000) {
    try {
        return await fn();
    } catch (error) {
        if (retries <= 0) throw error;
        // Exponential backoff + Full Jitter
        const delay = Math.random() * (baseDelay * Math.pow(2, 3 - retries));
        await new Promise(r => setTimeout(r, delay));
        return retryWithBackoff(fn, retries - 1, baseDelay);
    }
}
```

---

### Q61: Why does `fetch()` NOT reject on HTTP 404 or 500 status codes?
**Direct Answer:**  
The `fetch()` Promise rejects **ONLY on network failures** (DNS resolution failure, loss of internet connection, CORS blocks, connection refused).  
An HTTP `404 Not Found` or `500 Internal Server Error` is a valid HTTP response from a server. You must manually inspect `response.ok`:
```javascript
const res = await fetch(url);
if (!res.ok) { // res.ok is true for status 200-299
    throw new Error(`HTTP Error: ${res.status}`);
}
const data = await res.json();
```

---

### Q62: What is `process.nextTick()` in Node.js and where does it fit in the Event Loop?
**Direct Answer:**  
In Node.js, `process.nextTick()` does **NOT** belong to the standard event loop phases:
- The `nextTickQueue` runs **immediately after the current operation completes**, before both the Microtask Queue and the next phase of the event loop.
- It has higher priority than `Promise.then()`.
- Used to allow users to handle errors, cleanup resources, or run callbacks after the current stack unwinds but before any I/O occurs.

---

### Q63: What is `setImmediate()` vs `setTimeout(fn, 0)` in Node.js?
**Direct Answer:**  
- `setTimeout(fn, 0)`: Belongs to the **Timers Phase** of the Node.js event loop.
- `setImmediate(fn)`: Belongs to the **Check Phase** of the event loop.
- Inside an I/O cycle (e.g. `fs.readFile` callback), `setImmediate` is **always executed before** `setTimeout(fn, 0)`, regardless of timer granularity.

---

### Q64: What is `requestAnimationFrame` (rAF) and why is it preferred over `setTimeout` for animations?
**Direct Answer:**  
- `setTimeout(fn, 16)`: Executes independent of screen refresh rates. May fire in the middle of a screen refresh cycle, causing dropped frames, screen tearing, and running even when the browser tab is hidden/minimized.
- `requestAnimationFrame(callback)`: Synchronized with the browser's **V-Sync display refresh rate** (typically 60Hz or 120Hz). Fires right before the browser performs layout and paint, guarantees smooth 60fps animations, and automatically pauses when the user switches tabs to save CPU and battery.

---

### Q65: How do Web Workers enable true multithreading in JavaScript?
**Direct Answer:**  
Web Workers run code in a background operating system thread separate from the main browser UI thread:
- **Zero UI Blocking:** Heavy computations (image processing, crypto, ray tracing) will never cause the UI to stutter or freeze.
- **Shared-Nothing:** Workers do not have access to the `window`, `document`, or DOM.
- **Communication:** Via message passing (`worker.postMessage(data)` and `onmessage`), which clones data using the structured clone algorithm, or using zero-copy `ArrayBuffer` transferables and `SharedArrayBuffer` with `Atomics`.

---

### Q66: How do you limit concurrent asynchronous promises (`p-limit` implementation)?
**Direct Answer:**  
```javascript
function pLimit(concurrency) {
    const queue = [];
    let activeCount = 0;

    const next = () => {
        if (activeCount < concurrency && queue.length > 0) {
            activeCount++;
            const { fn, resolve, reject } = queue.shift();
            fn().then(resolve, reject).finally(() => {
                activeCount--;
                next();
            });
        }
    };

    return (fn) => new Promise((resolve, reject) => {
        queue.push({ fn, resolve, reject });
        next();
    });
}
```

---

### Q67: What is the DataLoader pattern in JavaScript?
**Direct Answer:**  
DataLoader solves the N+1 problem in GraphQL and API layers by **batching and caching**:
1. When multiple resolvers call `loader.load(id)` within the same single tick of the event loop, DataLoader gathers all individual IDs into a queue.
2. In the next microtask (via `process.nextTick` or `Promise.resolve()`), it executes a single batch function: `batchLoad([id1, id2, id3]) -> db.query("WHERE id IN (?, ?, ?)")`.
3. It maps the batch results back to the individual caller promises.

---

### Q68: How do Service Workers work and how do they enable Progressive Web Apps (PWAs)?
**Direct Answer:**  
A Service Worker is an event-driven programmable network proxy sitting between the browser client and the internet:
1. **Lifecycle:** `Register -> Install -> Activate`.
2. **Fetch Interception:** Intercepts every network request (`self.addEventListener('fetch', event)`).
3. **Offline Caching:** Can serve cached responses from the Cache Storage API (`caches.match(event.request)`) even when the device is completely offline.

---

### Q69: What are Async Iterators and the `for await...of` loop?
**Direct Answer:**  
Used for iterating over asynchronous streams of data (e.g. reading chunks from a socket or paginated API results):
- Implements `[Symbol.asyncIterator]()`.
- Each iteration returns a Promise that resolves to `{ value, done }`:
```javascript
async function* fetchPages(urls) {
    for (const url of urls) {
        const response = await fetch(url);
        yield await response.json();
    }
}
for await (const page of fetchPages(urls)) {
    console.log(page);
}
```

---

### Q70: What is the Streams API in modern JavaScript?
**Direct Answer:**  
Allows processing network data incrementally as raw byte chunks arrive, without waiting for the entire multi-megabyte payload to buffer into memory:
```javascript
const response = await fetch('/large-file.csv');
const reader = response.body.getReader();

while (true) {
    const { done, value } = await reader.read(); // value is a Uint8Array chunk
    if (done) break;
    processChunk(value);
}
```

---

### Q71: How do you implement a Pub-Sub (EventEmitter) from scratch in JavaScript?
**Direct Answer:**  
```javascript
class EventEmitter {
    constructor() {
        this.events = new Map();
    }

    on(event, listener) {
        if (!this.events.has(event)) this.events.set(event, []);
        this.events.get(event).push(listener);
        return () => this.off(event, listener); // returns unsubscribe
    }

    emit(event, ...args) {
        if (!this.events.has(event)) return;
        this.events.get(event).forEach(listener => listener(...args));
    }

    off(event, listener) {
        if (!this.events.has(event)) return;
        this.events.set(event, this.events.get(event).filter(l => l !== listener));
    }

    once(event, listener) {
        const unsubscribe = this.on(event, (...args) => {
            unsubscribe();
            listener(...args);
        });
    }
}
```

---

### Q72: What is the difference between synchronous code, Microtasks, Macrotasks, and Animation Frames in rendering timing?
**Direct Answer:**  
Order in a single frame cycle:
1. **Synchronous JavaScript Call Stack** executes to completion.
2. **Microtasks Queue** is completely drained.
3. **`requestAnimationFrame` callbacks** execute right before rendering.
4. **Style calculations, Layout (Reflow), and Paint** occur on the screen.
5. **Idle Period:** `requestIdleCallback` runs if idle time remains before next V-Sync.
6. **Macrotask Queue:** The next macrotask executes.

---

### Q73: What is `queueMicrotask()` and when should you use it?
**Direct Answer:**  
Standard API to queue a callback to the Microtask Queue explicitly without the syntax overhead of `Promise.resolve().then(fn)`:
- Guarantees consistent asynchronous execution of callbacks before browser rendering.
- Used when writing APIs that sometimes return synchronously from cache but must maintain asynchronous consistency for callers.

---

### Q74: What is Top-Level `await`?
**Direct Answer:**  
In ES Modules (`.mjs` or `<script type="module">`), you can use `await` at the top level of a file without wrapping it in an `async function`:
```javascript
// Valid in ES Modules:
const db = await connectToDatabase();
export { db };
```
Blocks execution of dependent child modules until the promise resolves, simplifying asynchronous dependency initialization.

---

### Q75: How do you prevent race conditions when searching with dynamic async API calls?
**Direct Answer:**  
**The Race Condition:** User types "cat" (Request 1 sent). User types "dog" (Request 2 sent). If Request 1 takes 2 seconds and Request 2 takes 200ms, Request 1 resolves *last*, overwriting the screen with results for "cat"!  
**Solutions:**
1. **`AbortController`:** Abort previous in-flight request before launching the new fetch.
2. **Sequence / Request ID:** Store `latestRequestId`; in the callback, ignore results if `responseId !== latestRequestId`.
