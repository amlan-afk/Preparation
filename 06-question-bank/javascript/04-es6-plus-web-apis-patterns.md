# 🟨 JavaScript Web APIs, DOM, Security & Patterns (Q76 – Q100)

---

### Q76: Describe the Critical Rendering Path (CRP) in the browser.
**Direct Answer:**  
The sequence of steps a browser takes to convert HTML, CSS, and JavaScript into pixels on the screen:
1. **DOM Tree Construction:** HTML parser converts raw bytes $\rightarrow$ characters $\rightarrow$ tokens $\rightarrow$ nodes $\rightarrow$ Document Object Model (DOM).
2. **CSSOM Tree Construction:** CSS parser processes stylesheets into CSS Object Model (CSSOM). CSS is **render-blocking**.
3. **Render Tree:** Combines DOM and CSSOM, ignoring elements that are not visible (e.g. `display: none` or `<head>`).
4. **Layout (Reflow):** Calculates the exact geometric coordinates, width, and height for each visible box on the viewport.
5. **Paint:** Fills in pixels (colors, borders, shadows, text).
6. **Compositing:** Draws layers to the screen in the correct stacking order using the GPU.

---

### Q77: What is the difference between Reflow and Repaint? How do you optimize for 60fps?
**Direct Answer:**  
- **Reflow (Layout):** Recomputes geometry and positions of elements on the page. Extremely expensive because altering one element can trigger recursive layout recalculations for its ancestors and descendants (e.g. changing `width`, `height`, `margin`, `fontSize`, or reading `offsetTop`).
- **Repaint:** Visual changes that do not affect geometry or layout (e.g. changing `color`, `background-color`, `visibility`). Faster than reflow.
- **Optimization Rule:** Animate **ONLY** properties that bypass layout and paint and run directly on the GPU compositor: `transform` (translate, scale, rotate) and `opacity`.

---

### Q78: Explain Event Propagation: Capturing, Target, and Bubbling phases.
**Direct Answer:**  
When an event occurs on a DOM node:
1. **Capturing Phase (Trickling):** The event travels down from `window` $\rightarrow$ `document` $\rightarrow$ `<body>` down through ancestor nodes to the target element.
2. **Target Phase:** The event arrives at the target element where the user clicked.
3. **Bubbling Phase (Default):** The event bubbles back up from the target element through all its ancestor nodes up to `window`.  
`element.addEventListener('click', handler, useCapture)`: If `useCapture` is `true`, handler executes during Capturing; if `false` (default), during Bubbling.

---

### Q79: What is Event Delegation and why is it essential for performance?
**Direct Answer:**  
Instead of attaching individual event listeners to hundreds of separate child elements (e.g. 1,000 list items `<li>`), you attach a **single event listener to their common parent container** and leverage **Event Bubbling**:
```javascript
document.getElementById('parent-list').addEventListener('click', (event) => {
    const li = event.target.closest('li');
    if (li && this.contains(li)) {
        console.log(`Clicked on item: ${li.dataset.id}`);
    }
});
```
**Benefits:** Drastically reduces memory consumption (1 listener vs 1,000), and automatically works for new dynamically added child elements without reattaching listeners!

---

### Q80: What is the difference between `preventDefault()`, `stopPropagation()`, and `stopImmediatePropagation()`?
**Direct Answer:**  
- `event.preventDefault()`: Prevents the browser's default native action for the event (e.g. stops a form submit from reloading the page, stops a link `<a>` from following URL). Does **NOT** stop propagation.
- `event.stopPropagation()`: Stops the event from bubbling up (or trickling down) to parent nodes.
- `event.stopImmediatePropagation()`: Stops the event from bubbling AND prevents any **other event listeners attached to the exact same element** from executing!

---

### Q81: Compare Web Storage: `localStorage`, `sessionStorage`, `IndexedDB`, and `Cookies`.
**Direct Answer:**  
| Storage | Capacity | Lifetime | Sent with HTTP Requests? | Best Use Case |
|---|---|---|---|---|
| **Cookies** | 4 KB | Configurable via `Expires`/`Max-Age` | **YES** (Sent in headers) | Session tokens (`HttpOnly`), auth state |
| **`sessionStorage`** | ~5 MB | Survives page reloads; cleared when tab closes | NO | Multi-step form drafts in single tab |
| **`localStorage`** | ~5-10 MB | Persistent until explicitly deleted | NO | User theme preference, cached UI settings |
| **`IndexedDB`** | 250MB+ (Gigabytes) | Persistent transactional NoSQL DB | NO | Offline PWA caching, large datasets |

---

### Q82: What are the security attributes of Cookies: `HttpOnly`, `Secure`, and `SameSite`?
**Direct Answer:**  
- **`HttpOnly`:** Prevents JavaScript (`document.cookie`) from reading the cookie. **Critical defense against XSS token theft**.
- **`Secure`:** Cookie is transmitted **only over HTTPS** encrypted connections.
- **`SameSite`:** Controls whether cookies are sent on cross-site requests (**Defends against CSRF**):
  - `SameSite=Strict`: Cookie is sent *only* if the request originates from the same domain.
  - `SameSite=Lax` (Default): Sent on top-level navigation (clicking a link), but blocked on cross-site POST requests or images.
  - `SameSite=None; Secure`: Sent on all cross-site requests (third-party tracking).

---

### Q83: What is Cross-Site Scripting (XSS) and how do you prevent it?
**Direct Answer:**  
XSS allows attackers to inject malicious client-side JavaScript into web pages viewed by other users:
- **Types:** Stored (saved in DB), Reflected (in URL query params), DOM-based (client-side `innerHTML` manipulation).
- **Impact:** Stealing session tokens, logging keystrokes, impersonating users.
- **Prevention:**
  1. Never use `element.innerHTML = rawInput` or React's `dangerouslySetInnerHTML`. Use `textContent` or sanitize with **DOMPurify**.
  2. Store sensitive authentication tokens in **`HttpOnly` cookies**, not `localStorage`.
  3. Deploy a strict **Content Security Policy (CSP)**.

---

### Q84: What is Cross-Site Request Forgery (CSRF) and how do you prevent it?
**Direct Answer:**  
An attack where a malicious site tricks an authenticated user's browser into sending unauthorized commands to a vulnerable web application (e.g. `POST /api/transfer-funds`):
- **Defense:**
  1. Use `SameSite=Lax` or `SameSite=Strict` cookie attribute.
  2. **Anti-CSRF Tokens:** Server issues a cryptographically random token in the initial page; client includes it in a custom header (`X-CSRF-TOKEN`).
  3. Stateless REST APIs with `Authorization: Bearer <jwt>` are inherently immune to CSRF.

---

### Q85: What is Content Security Policy (CSP)?
**Direct Answer:**  
An HTTP response header (`Content-Security-Policy`) that restricts which dynamic resources (scripts, images, stylesheets, fonts) the browser is allowed to load and execute:
```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trustedscripts.com; object-src 'none';
```
By restricting script execution to trusted domains and blocking inline scripts (`'unsafe-inline'`), CSP renders injected XSS scripts inert.

---

### Q86: How does the `IntersectionObserver` API work and why is it preferred for Infinite Scroll and Lazy Loading?
**Direct Answer:**  
Historically, lazy loading images required attaching a scroll event listener and calling `getBoundingClientRect()`, which forced continuous **synchronous layout reflows** on the main thread.  
**`IntersectionObserver`:** Asynchronously monitors when a target element enters or exits the browser viewport:
```javascript
const observer = new IntersectionObserver((entries, obs) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const img = entry.target;
            img.src = img.dataset.src; // load image
            obs.unobserve(img);
        }
    });
}, { threshold: 0.1 });

document.querySelectorAll('img[data-src]').forEach(img => observer.observe(img));
```
Runs off the main thread; zero performance penalty on scrolling.

---

### Q87: What are Detached DOM Nodes and how do they cause Memory Leaks?
**Direct Answer:**  
A detached DOM node occurs when an element has been removed from the DOM tree (`element.remove()`), but a JavaScript variable still retains a reference to it:
```javascript
let buttonRef = document.getElementById('btn');
document.body.removeChild(buttonRef);
// buttonRef still holds the element in memory!
```
Because the JS reference is alive, the garbage collector cannot reclaim the element or any of its subtree and event listeners.  
**Diagnosis:** In Chrome DevTools $\rightarrow$ Memory $\rightarrow$ Take Heap Snapshot $\rightarrow$ Filter for "Detached".

---

### Q88: How does a `DocumentFragment` optimize batch DOM insertions?
**Direct Answer:**  
A `DocumentFragment` is a lightweight in-memory container that has no representation in the actual DOM tree:
```javascript
const fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
    const li = document.createElement('li');
    li.textContent = `Item ${i}`;
    fragment.appendChild(li); // Pure in-memory manipulation
}
document.getElementById('list').appendChild(fragment); // Triggers exactly ONE reflow!
```
Inserting 1,000 nodes one-by-one triggers 1,000 separate reflows; appending via a `DocumentFragment` triggers **only a single reflow**.

---

### Q89: How does DOM Virtualization / Windowing work for rendering 100,000 items?
**Direct Answer:**  
Rendering 100,000 DOM nodes creates millions of DOM elements, freezing the browser and exhausting hundreds of megabytes of RAM.  
**Virtualization (e.g. `react-window` / `react-virtualized`):**
1. Only renders the small slice of elements that are currently **visible in the viewport** (e.g. 20 items) plus a small overscan buffer (5 items above/below).
2. Calculates total container height using `total_items * item_height`.
3. As the user scrolls, it dynamically swaps out the rendered nodes and adjusts `transform: translateY(px)`, maintaining a constant count of ~30 DOM nodes regardless of whether the list has 1,000 or 1,000,000 rows!

---

### Q90: What is Shadow DOM and how does it encapsulate styles in Web Components?
**Direct Answer:**  
Shadow DOM provides **true scoped CSS and DOM encapsulation**:
```javascript
class UserCard extends HTMLElement {
    constructor() {
        super();
        const shadow = this.attachShadow({ mode: 'open' });
        shadow.innerHTML = `
            <style>
                p { color: red; } /* This CSS CANNOT leak out or be affected by outer styles! */
            </style>
            <p>User Profile</p>
        `;
    }
}
customElements.define('user-card', UserCard);
```
Outer stylesheets cannot accidentally style shadow DOM internals, and shadow CSS cannot leak into the global document.

---

### Q91: What triggers a CORS Preflight `OPTIONS` request?
**Direct Answer:**  
The browser sends an automatic preflight `OPTIONS` request before the actual request if it is **NOT a Simple Request**:
- **Triggers for Preflight:**
  1. HTTP Methods other than `GET`, `HEAD`, `POST`.
  2. Custom request headers (e.g. `Authorization`, `X-Custom-Header`).
  3. `Content-Type` other than `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain` (e.g. **`application/json` triggers preflight!**).
- Server must respond with `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, and `Access-Control-Allow-Headers`.

---

### Q92: What are `ArrayBuffer`, `TypedArray`, and `DataView`?
**Direct Answer:**  
- **`ArrayBuffer`:** A fixed-length raw binary memory buffer allocated in memory. Cannot be read or modified directly.
- **`TypedArray` (`Uint8Array`, `Int32Array`, `Float64Array`):** A typed view onto an `ArrayBuffer` providing indexed array-like access with native endianness.
- **`DataView`:** A flexible low-level view providing explicit control over byte offset, data types, and endianness (`getInt16(0, true)` for little-endian). Essential for binary protocols, WebSockets, and WebGL.

---

### Q93: What is Prototype Pollution and how do you mitigate it?
**Direct Answer:**  
A vulnerability where an attacker injects properties into `Object.prototype` (typically via recursive object merge or query string parsers):
`obj["__proto__"]["isAdmin"] = true` $\rightarrow$ Every object in the entire application now inherits `isAdmin = true`!  
**Mitigation:**
1. Validate and sanitize keys (strip `__proto__`, `constructor`, `prototype`).
2. Use `Object.create(null)` for user-supplied key maps.
3. Freeze `Object.prototype`: `Object.freeze(Object.prototype)`.
4. Use `Map` instead of plain objects for dynamic dictionary storage.

---

### Q94: How do you implement the Strategy Pattern in JavaScript?
**Direct Answer:**  
In JavaScript, functions are first-class citizens, making the strategy pattern simple without heavyweight class hierarchies:
```javascript
const shippingStrategies = {
    fedex: (weight) => weight * 2.5,
    ups: (weight) => weight * 3.0,
    usps: (weight) => weight * 1.8
};

function calculateShipping(carrier, weight) {
    const strategy = shippingStrategies[carrier];
    if (!strategy) throw new Error("Unknown carrier");
    return strategy(weight);
}
```

---

### Q95: How do you implement the Module Pattern and Revealing Module Pattern?
**Direct Answer:**  
Uses an IIFE (Immediately Invoked Function Expression) and closures to expose only public APIs:
```javascript
const UserModule = (function() {
    let privateApiToken = "secret_123"; // private
    
    function logAccess() { console.log("Accessed API"); } // private
    
    function getUser() {
        logAccess();
        return { name: "Amlan", token: privateApiToken };
    }
    
    // Reveal public methods:
    return {
        getUser
    };
})();
```

---

### Q96: What are `ResizeObserver` and `MutationObserver`?
**Direct Answer:**  
- **`MutationObserver`:** Asynchronously watches for changes to the DOM tree (child node additions/removals, attribute mutations, text content changes).
- **`ResizeObserver`:** Asynchronously reports changes to the dimensions (content box / border box) of specific individual DOM elements, enabling responsive component-level queries without global window resize listeners.

---

### Q97: What is the difference between `Node.cloneNode(true)` and `Node.cloneNode(false)`?
**Direct Answer:**  
- `element.cloneNode(false)`: Performs a shallow copy of the node and its attributes only; does NOT clone child elements or text nodes.
- `element.cloneNode(true)`: Performs a deep recursive clone of the node, all its child elements, attributes, and text nodes.
- *Gotcha:* Event listeners attached via `addEventListener()` are **never cloned**!

---

### Q98: How do you profile memory leaks using Chrome DevTools?
**Direct Answer:**  
1. Open DevTools $\rightarrow$ **Memory** tab.
2. Select **Allocation instrumentation on timeline** or **Heap snapshot**.
3. Perform the suspected action (e.g. open and close a modal 10 times).
4. Look at **Shallow Size** (memory held by object itself) vs **Retained Size** (memory freed if object is garbage collected).
5. Inspect the **Distance** from GC Root; if retained size remains high after GC forced (Trash icon), trace the reference tree to find the leak holder.

---

### Q99: What is the difference between `window.onload` and `DOMContentLoaded`?
**Direct Answer:**  
- **`DOMContentLoaded`:** Fires as soon as the HTML has been completely parsed and the DOM tree is built. Does **NOT** wait for external stylesheets, images, or async frames to finish downloading.
- **`window.onload`:** Fires much later, only after the HTML, DOM, and **ALL external dependent resources** (images, stylesheets, fonts) have fully finished downloading.

---

### Q100: What is Module Federation in Webpack 5 / Modern Bundlers?
**Direct Answer:**  
An architecture that allows a JavaScript application to dynamically load code from another independent build (Micro-frontend) at runtime:
- Host application loads remote components over HTTP (`import('remoteApp/Header')`).
- Shared dependencies (e.g. `react`, `react-dom`) are deduplicated and shared in memory, preventing loading duplicate copies of React.
- Enables autonomous micro-frontend deployments without rebuilding or redeploying the container shell.
