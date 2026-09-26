# ⚛️ React Core: Architecture, Virtual DOM & Components (Q1 – Q25)

---

### Q1: What is the Virtual DOM and how does it work?
**Direct Answer:**  
The Virtual DOM (VDOM) is a lightweight, in-memory JavaScript object representation of the real DOM tree:
1. When state or props change, React invokes the component's render function to construct a **new Virtual DOM tree**.
2. **Reconciliation (Diffing):** React compares the new VDOM tree with the previous snapshot of the VDOM tree.
3. **Batch Updates:** React calculates the minimal set of real DOM mutations needed and applies them in a single batch pass to the browser's real DOM, minimizing expensive browser layout reflows and repaints.

---

### Q2: What is React Fiber and why was the reconciler rewritten?
**Direct Answer:**  
- **Legacy Stack Reconciler (React 15 and earlier):** Recursive, synchronous diffing. Once rendering started, it could not be paused. On complex component trees, it occupied the main thread for 100ms+, causing dropped frames and laggy input responses.
- **React Fiber (React 16+):** A complete rewrite of the reconciliation engine based on a **singly-linked-list virtual stack frame** (Fiber nodes).
- **Capabilities:**
  1. **Incremental Rendering (Time-Slicing):** Splits rendering work into small chunks. Yields execution back to the browser if a higher-priority task (user typing, animation) arrives.
  2. **Prioritization:** Assigns priority levels to updates (Immediate, User-Blocking, Normal, Low, Idle).
  3. **Concurrency:** Powers React 18 Concurrent features (`useTransition`, Suspense).

---

### Q3: How does the Reconciliation / Diffing Algorithm achieve $O(N)$ complexity?
**Direct Answer:**  
A general tree-matching algorithm takes $O(N^3)$ complexity. React uses two heuristic assumptions to achieve **$O(N)$ linear time**:
1. **Different Component Types:** Two elements of different types produce completely different trees (e.g. `<div>` replaced by `<span>` unmounts the entire `<div>` subtree and mounts a fresh `<span>` tree).
2. **Keys in Lists:** The developer provides a stable `key` prop so React can match children across renders, detecting moves, insertions, and deletions without tearing down identical DOM nodes.

---

### Q4: What is JSX and how does it compile?
**Direct Answer:**  
JSX is a syntax extension for JavaScript that looks like HTML:
```jsx
const element = <h1 className="title">Hello World</h1>;
```
- **Legacy Babel Transform:** Compiles to:
  ```javascript
  const element = React.createElement("h1", { className: "title" }, "Hello World");
  ```
- **Modern JSX Transform (React 17+):** Automatically imports the JSX runtime without requiring `import React from 'react'`:
  ```javascript
  import { jsx as _jsx } from 'react/jsx-runtime';
  const element = _jsx("h1", { className: "title", children: "Hello World" });
  ```

---

### Q5: Why is State Immutable in React? What happens if you mutate state directly?
**Direct Answer:**  
```javascript
// BROKEN: Direct mutation
state.items.push(newItem);
setState(state);
```
**Why Immutability is Mandatory:**
1. **Shallow Reference Equality (`Object.is`):** React compares `previousState === nextState`. If you mutate the object directly, both variables point to the same memory reference, causing React to assume nothing changed and **skip re-rendering**!
2. **Predictable Debugging:** Enables time-travel debugging, undo/redo, and pure component memoization (`React.memo`).
3. **Concurrency Safety:** Mutable state can be read in an inconsistent, partially-mutated state during concurrent time-sliced renders.

---

### Q6: Why are `key` props required in lists? What happens if you use `index` as a key?
**Direct Answer:**  
The `key` prop uniquely identifies an item across renders:
- **Using `index` as key causes severe bugs when items are added, deleted, or reordered:**
  1. If an item is prepended at index 0, every subsequent item's index shifts ($0 \rightarrow 1, 1 \rightarrow 2$). React matches items by index and assumes every existing item mutated its props, destroying performance!
  2. **State Corruption:** Uncontrolled child inputs (`<input />`) or local state remain bound to the DOM index rather than the logical item. Deleting item 0 causes item 1 to inherit item 0's input text!
- **Rule:** Always use a stable, unique business ID (`key={user.id}`).

---

### Q7: What are Controlled vs Uncontrolled Components?
**Direct Answer:**  
- **Controlled Component:** Form input data is handled by the **React component state** (`value={text}` and `onChange={e => setText(e.target.value)}`). React is the single source of truth. Enables instant validation, conditional disabling, and dynamic formatting.
- **Uncontrolled Component:** Form input data is handled directly by the **DOM**. Values are retrieved on-demand using a `ref` (`inputRef.current.value`). Faster for huge forms without re-rendering per keystroke (e.g. React Hook Form).

---

### Q8: How does React's SyntheticEvent system work?
**Direct Answer:**  
React wraps native browser events in a cross-browser wrapper (`SyntheticEvent`) conforming to the W3C spec:
1. **Cross-Browser Consistency:** Normalizes differences across Chrome, Safari, Firefox, and Edge.
2. **Event Delegation to Root (React 17+):** Instead of attaching listeners to every DOM node, React attaches a single listener for each event type to the root DOM container (`#root`). When an event occurs, it bubbles to `#root`, where React dispatches it through the component tree.
3. *Note: Event Pooling was completely removed in React 17; event properties can now be safely accessed asynchronously.*

---

### Q9: What are Error Boundaries and what errors can they NOT catch?
**Direct Answer:**  
An Error Boundary is a class component that catches JavaScript errors in its child component tree, logs the error, and renders a fallback UI:
```javascript
class ErrorBoundary extends React.Component {
    state = { hasError: false };
    static getDerivedStateFromError(error) { return { hasError: true }; }
    componentDidCatch(error, errorInfo) { logToService(error, errorInfo); }
    render() {
        if (this.state.hasError) return <h1>Something went wrong.</h1>;
        return this.props.children;
    }
}
```
**What Error Boundaries CANNOT Catch:**
1. Event handlers (`onClick={() => { throw new Error(); }}`) — Must use `try/catch`.
2. Asynchronous code (`setTimeout`, `fetch`).
3. Server-Side Rendering (SSR).
4. Errors thrown inside the Error Boundary component itself.

---

### Q10: What is `React.memo` and how does it prevent re-renders?
**Direct Answer:**  
`React.memo` is a Higher-Order Component for functional components:
- Performs a **shallow comparison (`===`)** of incoming props against previous props.
- If all props are identical, React skips re-rendering the component and reuses the last rendered output.
- **Custom Comparison:** Accepts a second argument `(prevProps, nextProps) => boolean`:
```javascript
export default React.memo(UserCard, (prev, next) => prev.user.id === next.user.id);
```

---

### Q11: What is Automatic Batching in React 18?
**Direct Answer:**  
- **React 17 and earlier:** State updates were batched **only inside React event handlers** (`onClick`). Updates inside `setTimeout`, promises, or native fetch callbacks triggered individual re-renders per state setter call!
- **React 18:** **Automatic Batching everywhere by default**. Multiple state updates inside promises, timeouts, and native event handlers are grouped into a single re-render pass:
```javascript
// React 18 triggers only ONE re-render!
fetch('/api').then(() => {
    setCount(c => c + 1);
    setFlag(f => !f);
});
```
To opt-out, use `ReactDOM.flushSync()`.

---

### Q12: What is StrictMode in React 18 and why do components mount twice in development?
**Direct Answer:**  
`<React.StrictMode>` is a development-only verification tool.  
In React 18, StrictMode **intentionally unmounts and remounts components twice** on initial load (`mount -> cleanup -> mount`) and runs render functions twice:
- **Purpose:** To uncover missing cleanup functions in `useEffect` and detect impure side effects during rendering, ensuring components are compatible with Reusable State and Concurrent React.
- Has zero impact on production builds.

---

### Q13: What are React Portals and when should you use them?
**Direct Answer:**  
`ReactDOM.createPortal(child, domNode)` renders a component into a different DOM subtree outside its parent component's DOM hierarchy, while **retaining full React context and event bubbling**:
```jsx
function Modal({ children }) {
    return ReactDOM.createPortal(
        <div className="modal-overlay">{children}</div>,
        document.getElementById('modal-root')
    );
}
```
- **Use Cases:** Modals, tooltips, dialogs, and popovers that must break out of parent containers with `overflow: hidden` or `z-index` stacking context constraints.

---

### Q14: What is the trap with conditional rendering using the `&&` operator?
**Direct Answer:**  
```jsx
// DANGEROUS:
{items.length && <ItemList items={items} />}
```
If `items.length` is `0`, JavaScript's `&&` operator evaluates the first falsy operand and **renders the number `0` on the screen**!  
**Safe alternatives:**
```jsx
{items.length > 0 && <ItemList items={items} />}
// Or ternary:
{items.length ? <ItemList items={items} /> : null}
```

---

### Q15: How does Component Composition replace Inheritance in React?
**Direct Answer:**  
React uses a powerful composition model rather than OOP class inheritance:
1. **Containment (Children prop):** Generic container components render whatever arbitrary children are passed (`<Card>{children}</Card>`).
2. **Specialization:** A specific component renders a more generic one with pre-configured props:
```jsx
function DangerDialog(props) {
    return <Dialog color="red" title="Warning" {...props} />;
}
```

---

### Q16: What is the difference between `React.Fragment` and a standard `<div>` wrapper?
**Direct Answer:**  
React components must return a single root element:
- Wrapping in a `<div>` adds an unnecessary DOM node to the browser tree, which can break CSS Flexbox/Grid layouts, table structures (`<tr><td>`), and wastes memory.
- `<React.Fragment>` (or `<>...</>`) groups a list of children **without adding any extra node to the real DOM**.
- The explicit `<React.Fragment key={item.id}>` syntax is required when passing a `key` prop in loops.

---

### Q17: What are Higher-Order Components (HOC)?
**Direct Answer:**  
A Higher-Order Component is a pure function that takes a component as an argument and returns an enhanced new component:
`const EnhancedComponent = withAuth(BaseComponent);`  
Used historically for cross-cutting concerns (authentication guards, analytics tracking, theme injection). In modern React, **Custom Hooks** have largely replaced HOCs because hooks compose without introducing deep component tree nesting ("wrapper hell").

---

### Q18: What is the difference between Element and Component in React?
**Direct Answer:**  
- **React Component:** A function or class that accepts props and returns a React element tree (e.g. `function Button(props) { return <button {...props} />; }`).
- **React Element:** A plain immutable JavaScript object created by `React.createElement()` describing a DOM node or component:
  `{ type: Button, props: { label: "Submit" } }`.

---

### Q19: What is the difference between `shadow DOM` and `virtual DOM`?
**Direct Answer:**  
- **Virtual DOM:** A React-specific JavaScript architecture pattern that abstracts and optimizes real DOM updates via diffing and reconciliation.
- **Shadow DOM:** A native browser standard (part of Web Components) that provides strict CSS scoping and DOM encapsulation for custom HTML elements (`<video>`, custom elements). They solve completely different problems.

---

### Q20: How do you prevent unnecessary re-renders in children when passing callbacks?
**Direct Answer:**  
By combining **`useCallback`** on the parent callback and **`React.memo`** on the child component:
```jsx
// 1. Parent memoizes function reference across renders:
const handleClick = useCallback(() => {
    doSomething(id);
}, [id]);

// 2. Child is wrapped in React.memo:
const ChildButton = React.memo(({ onClick }) => {
    return <button onClick={onClick}>Click</button>;
});
```
Without `React.memo` on the child, `useCallback` provides zero re-render prevention!

---

### Q21: What is the Compound Component Pattern in React?
**Direct Answer:**  
A pattern where multiple components work together sharing implicit state via React Context (like `<select>` and `<option>` in HTML):
```jsx
<Tabs defaultValue="tab1">
    <Tabs.List>
        <Tabs.Trigger value="tab1">Account</Tabs.Trigger>
        <Tabs.Trigger value="tab2">Password</Tabs.Trigger>
    </Tabs.List>
    <Tabs.Content value="tab1"><AccountSettings /></Tabs.Content>
    <Tabs.Content value="tab2"><PasswordSettings /></Tabs.Content>
</Tabs>
```
Provides extreme flexibility for consumers without prop-drilling or rigid prop configuration.

---

### Q22: What is the Polymorphic `as` Prop Pattern?
**Direct Answer:**  
A design system pattern allowing a component to render as different underlying HTML elements or components while retaining consistent styles:
```jsx
function Button<E extends React.ElementType = 'button'>({ as, ...props }: ButtonProps<E>) {
    const Component = as || 'button';
    return <Component className="btn-primary" {...props} />;
}
// Rendered as a link with anchor tags:
<Button as="a" href="/login">Login</Button>
```

---

### Q23: Why can't React components return multiple siblings without a Fragment or Array?
**Direct Answer:**  
Because JSX compiles directly to JavaScript function calls (`React.createElement(type, props, ...children)` or `_jsx()`). In JavaScript, a function cannot return two separate expressions without wrapping them in an array or container object:
`return a, b;` is invalid. Returning `<> <A/> <B/> </>` compiles to a single function call passing children.

---

### Q24: What is the difference between mounting, updating, and unmounting?
**Direct Answer:**  
- **Mounting:** Component is created and inserted into the real DOM for the first time.
- **Updating:** Component re-renders due to changes in its state, props, or parent re-rendering.
- **Unmounting:** Component is destroyed and removed from the real DOM.

---

### Q25: How do you clean up side effects to prevent memory leaks on unmount?
**Direct Answer:**  
Return a **cleanup function** from `useEffect`:
```javascript
useEffect(() => {
    const subscription = dataSource.subscribe(handleData);
    const timer = setInterval(poll, 5000);
    
    return () => {
        // Runs on unmount AND before re-running the effect!
        subscription.unsubscribe();
        clearInterval(timer);
    };
}, []);
```
