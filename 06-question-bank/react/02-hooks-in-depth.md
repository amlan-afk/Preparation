# ⚛️ React Hooks In-Depth: Mechanics, Pitfalls & Patterns (Q26 – Q50)

---

### Q26: What are the Rules of Hooks and why must hooks only be called at the top level?
**Direct Answer:**  
1. **Call hooks only at the top level:** Never inside loops, conditions, or nested functions.
2. **Call hooks only from React function components or custom hooks.**

**Under the Hood (Fiber Architecture):**
- In React Fiber, each component instance stores its hooks in a **singly linked list** attached to `fiber.memoizedState`.
- Each hook node contains `{ memoizedState, next, queue, ... }`.
- React does **not** identify hooks by name or key; it identifies them **strictly by invocation order** across render passes.
- If a hook is placed inside an `if` block that evaluates to false on re-render, the call order shifts. React will match Hook #3 to Hook #2's state slot, corrupting internal state pointers and throwing fatal runtime errors.

---

### Q27: How does `useState` work under the hood in React Fiber?
**Direct Answer:**  
- **Mount Phase (`mountState`):** React creates a Hook object, initializes `hook.memoizedState` to `initialState`, creates a circular linked queue of updates (`hook.queue`), and returns `[hook.memoizedState, dispatch]`. The `dispatch` function is bound to the current Fiber and its update queue.
- **Update Phase (`updateState`):** React iterates through the queued updates in `hook.queue.pending`, executes updater functions against the previous state, computes the new state, stores it in `hook.memoizedState`, and returns `[newState, dispatch]`.
- **Bailout Optimization:** If the new state calculated is identical to the current state according to `Object.is(prevState, nextState)`, React bails out early without re-rendering the component subtree.

---

### Q28: Why does `setState` take an updater function (`setCount(c => c + 1)`) vs a direct value?
**Direct Answer:**  
Because React state updates are scheduled and batched asynchronously:
```javascript
// BROKEN: All 3 calls read the stale closure count (e.g. 0)
setCount(count + 1); // 0 + 1 = 1
setCount(count + 1); // 0 + 1 = 1
setCount(count + 1); // 0 + 1 = 1
// Result: count becomes 1, not 3!

// FIXED: Functional updater guarantees reading the freshest pending state
setCount(c => c + 1); // receives 0 -> returns 1
setCount(c => c + 1); // receives 1 -> returns 2
setCount(c => c + 1); // receives 2 -> returns 3
// Result: count becomes 3!
```

---

### Q29: What is a "stale closure" in React hooks and how do you fix it?
**Direct Answer:**  
A stale closure occurs when a callback or effect function captures state or props variables from an earlier render pass, continuing to reference outdated values across time:
```javascript
useEffect(() => {
    const timer = setInterval(() => {
        // 'count' is captured from mount closure (always 0)
        console.log(count);
    }, 1000);
    return () => clearInterval(timer);
}, []); // Empty deps captures initial render only!
```
**Solutions:**
1. Include all referenced variables in the hook's **dependency array**.
2. Use **functional state updates** (`setCount(prev => prev + 1)`).
3. Store mutable values in a **`useRef`**, which preserves the identical container reference across renders.

---

### Q30: What is the exact difference between `useEffect` and `useLayoutEffect`?
**Direct Answer:**  
| Feature | `useEffect` | `useLayoutEffect` |
|---|---|---|
| **Execution Timing** | **Asynchronous / Deferred** after the browser has completed layout and paint. | **Synchronous** immediately after DOM mutations, before browser paint. |
| **Main Thread Blocking** | Non-blocking (does not delay frame render). | **Blocks painting** until code execution finishes. |
| **Primary Use Cases** | Data fetching, subscriptions, timers, logging. | Measuring DOM dimensions (getBoundingClientRect), scroll position adjustments, preventing visual flicker. |

---

### Q31: What is `useInsertionEffect` and when should it be used?
**Direct Answer:**  
Introduced in React 18, `useInsertionEffect` fires **synchronously before any DOM mutations occur**.  
- **Purpose:** Specifically built for CSS-in-JS libraries (like styled-components and Emotion) to inject `<style>` tags into the DOM before layout and layout effects run.
- **Why it matters:** Injecting styles during `useLayoutEffect` or `useEffect` forces the browser to recalculate layouts multiple times. `useInsertionEffect` guarantees styles are present before layout measurement occurs. Application developers should almost never use it directly.

---

### Q32: When should you use `useRef` vs `useState`? Can updating a ref trigger a re-render?
**Direct Answer:**  
- **`useState`:** For data that impacts the rendered visual output. Mutating state schedules a re-render pass.
- **`useRef`:** A mutable object `{ current: initialValue }` that persists for the entire lifetime of the component.
- **Key Difference:** **Mutating `ref.current` NEVER triggers a re-render!**
- **Use Cases:**
  1. Accessing native DOM elements (`<input ref={inputRef} />`).
  2. Storing mutable timer IDs, previous props, or flags without causing layout reflows.

---

### Q33: What is `useMemo` vs `useCallback`? When is using them an anti-pattern?
**Direct Answer:**  
- **`useMemo(() => computeValue(a, b), [a, b])`:** Caches the **result** of a computationally expensive calculation.
- **`useCallback(fn, deps)`:** Caches the **function instance** reference itself across renders (`useCallback(fn, deps)` is syntactic sugar for `useMemo(() => fn, deps)`).
- **Anti-Pattern Warning:** Memoization is not free. Creating closures, dependency arrays, and executing reference comparisons consumes CPU and memory. Do not wrap primitive calculations (like basic string formatting or array slicing under 100 items) or callbacks passed to standard native DOM elements (`<button onClick={...}>`). Use them when:
  1. Passing callbacks to deeply nested `React.memo` children.
  2. Passing objects/functions as dependencies to other hooks (`useEffect`).

---

### Q34: How does `useContext` work under the hood and what is its re-render pitfall?
**Direct Answer:**  
When `<MyContext.Provider value={value}>` updates its `value` reference (checked via `Object.is`):
- **Pitfall:** **EVERY component that calls `useContext(MyContext)` unconditionally re-renders**, completely bypassing `React.memo` on those consumer components!
- **Solution:**
  1. **Split contexts:** Separate rapidly changing state from static dispatch functions (`UserContext` vs `UserDispatchContext`).
  2. **Wrap provider values in `useMemo`:** Prevent creating a new object literal reference on every parent render.
  3. Use selectors via external state libraries (Zustand) for granular subscriptions.

---

### Q35: When should you choose `useReducer` over `useState`?
**Direct Answer:**  
Choose `useReducer` when:
1. **Complex State Logic:** State involves nested objects, arrays, or multiple sub-values that transition together based on specific action types.
2. **Next State Depends on Previous State:** Multiple interdependent transitions (e.g. `FETCH_INIT`, `FETCH_SUCCESS`, `FETCH_ERROR`).
3. **Optimizing Prop Drilling:** You can pass `dispatch` down through React Context instead of passing dozens of individual callback functions, as `dispatch` is guaranteed to have a stable identity.

---

### Q36: What is `forwardRef` and `useImperativeHandle`? How does React 19 change `ref` as a prop?
**Direct Answer:**  
- **Legacy React (<19):** Functional components could not accept a `ref` prop directly; `React.forwardRef((props, ref) => ...)` was required.
- **`useImperativeHandle(ref, createHandle, [deps])`:** Customizes the exposed instance value given to the parent ref, hiding internal DOM details and exposing only specific imperatively callable methods (`focus()`, `scrollIntoView()`).
- **React 19 Improvement:** `forwardRef` is **deprecated**! `ref` is now passed as a standard prop to function components directly:
```jsx
// React 19:
function MyInput({ placeholder, ref }) {
    return <input ref={ref} placeholder={placeholder} />;
}
```

---

### Q37: What is `useId` and why shouldn't you use `Math.random()` for SSR IDs?
**Direct Answer:**  
`useId` generates unique, stable IDs across server and client rendering:
```jsx
const id = useId();
return (
    <div>
        <label htmlFor={id}>Email:</label>
        <input id={id} type="email" />
    </div>
);
```
- **Why `Math.random()` fails:** In Server-Side Rendering (SSR), `Math.random()` generates ID "123" on the Node.js server and ID "456" during browser hydration. This mismatch breaks accessibility attributes and triggers React **Hydration Mismatch Errors**.

---

### Q38: What is `useTransition` and how does it prevent UI freezing during heavy updates?
**Direct Answer:**  
`useTransition` is a Concurrent React hook that marks state updates as **non-urgent transitions**:
```jsx
const [isPending, startTransition] = useTransition();

function handleSearch(e) {
    setInputValue(e.target.value); // Urgent: Keep typing responsive
    startTransition(() => {
        setSearchQuery(e.target.value); // Non-urgent: Heavy filtering
    });
}
```
- **Engine Mechanics:** Urgent updates (keystrokes, clicks) interrupt non-urgent transitions. If the user types another character before the heavy search list finishes rendering, React abandons the in-progress transition render and processes the new keystroke immediately!

---

### Q39: What is `useDeferredValue` and how does it differ from debouncing/throttling?
**Direct Answer:**  
`const deferredQuery = useDeferredValue(query);`  
- Delays updating a value until urgent rendering work is complete.
- **Difference from Debounce/Throttle:**
  - **Debounce/Throttle:** Based on fixed arbitrary time delays (`setTimeout(300ms)`). They introduce noticeable lag even on high-end 120Hz displays.
  - **`useDeferredValue`:** Deeply integrated into React Fiber's scheduler. It executes immediately after the main thread is free—zero artificial millisecond delays on fast hardware, yet gracefully degrades without dropping frames on slow devices.

---

### Q40: What is `useSyncExternalStore` and why was it introduced in React 18?
**Direct Answer:**  
Introduced to solve **"Tearing"** in Concurrent React when subscribing to external, non-React stores (like Redux, Zustand, or browser APIs like `window.navigator.onLine`):
- **Tearing:** During concurrent time-slicing, React may pause rendering a component tree. If an external store mutates while rendering is paused, different components in the same render pass read different store values, displaying an inconsistent, torn UI.
- `useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot)` forces synchronous snapshot reads, guaranteeing atomic consistency.

---

### Q41: How do you build a custom hook `useDebounce`?
**Direct Answer:**  
```javascript
import { useState, useEffect } from 'react';

export function useDebounce(value, delayMs = 300) {
    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(() => {
        const handler = setTimeout(() => {
            setDebouncedValue(value);
        }, delayMs);

        return () => {
            clearTimeout(handler); // Resets timer if value changes within delayMs
        };
    }, [value, delayMs]);

    return debouncedValue;
}
```

---

### Q42: How do you build a custom hook `usePrevious`?
**Direct Answer:**  
```javascript
import { useEffect, useRef } from 'react';

export function usePrevious(value) {
    const ref = useRef();

    useEffect(() => {
        ref.current = value; // Runs AFTER render has painted
    }, [value]);

    return ref.current; // Returns the value stored from the PREVIOUS render
}
```

---

### Q43: How do you build a custom hook `useOnClickOutside`?
**Direct Answer:**  
```javascript
import { useEffect } from 'react';

export function useOnClickOutside(ref, handler) {
    useEffect(() => {
        const listener = (event) => {
            // Do nothing if clicking ref's element or descendent elements
            if (!ref.current || ref.current.contains(event.target)) {
                return;
            }
            handler(event);
        };

        document.addEventListener('mousedown', listener);
        document.addEventListener('touchstart', listener);

        return () => {
            document.removeEventListener('mousedown', listener);
            document.removeEventListener('touchstart', listener);
        };
    }, [ref, handler]);
}
```

---

### Q44: How do you build a custom hook `useMediaQuery`?
**Direct Answer:**  
```javascript
import { useSyncExternalStore } from 'react';

export function useMediaQuery(query) {
    const subscribe = (callback) => {
        const matchMedia = window.matchMedia(query);
        matchMedia.addEventListener('change', callback);
        return () => matchMedia.removeEventListener('change', callback);
    };

    const getSnapshot = () => window.matchMedia(query).matches;
    const getServerSnapshot = () => false; // SSR fallback

    return useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot);
}
```

---

### Q45: Why should you avoid putting objects or functions in `useEffect` dependency arrays?
**Direct Answer:**  
Because JavaScript compares objects and functions by **reference identity (`===`)**, not structural equality:
```javascript
function Component() {
    const options = { timeout: 1000 }; // New object created in memory on EVERY render!
    
    useEffect(() => {
        api.connect(options);
    }, [options]); // Causes infinite re-render loop if effect updates state!
}
```
**Fixes:**
1. Move the object/function inside the `useEffect`.
2. Memoize the object with `useMemo` or function with `useCallback`.
3. Pass primitive properties into dependencies (`[options.timeout]`).

---

### Q46: How do you cancel an in-flight `fetch` request in `useEffect` using `AbortController`?
**Direct Answer:**  
```javascript
useEffect(() => {
    const controller = new AbortController();

    async function loadData() {
        try {
            const res = await fetch(`/api/users/${userId}`, { signal: controller.signal });
            const data = await res.json();
            setUser(data);
        } catch (err) {
            if (err.name !== 'AbortError') {
                setError(err);
            }
        }
    }

    loadData();

    return () => {
        controller.abort(); // Cancels request on unmount or before userId changes!
    };
}, [userId]);
```

---

### Q47: What happens if an error occurs inside `useEffect`? Can an Error Boundary catch it?
**Direct Answer:**  
- **No!** Error Boundaries only catch errors during the **rendering lifecycle**, constructor, and lifecycle methods of child components.
- Asynchronous errors or errors inside `useEffect` callbacks bubble to the global `window.onerror` handler and crash the React tree without invoking Error Boundary fallbacks.
- **Solution:** Catch errors inside the effect using `try/catch` and update local state (`setError(err)`), allowing the render phase to throw or display an error state.

---

### Q48: What is the `use` hook in React 19?
**Direct Answer:**  
The `use` hook is a new React primitive that unwraps promises or reads React Context:
```javascript
import { use } from 'react';

function UserProfile({ userPromise }) {
    // Suspends the component until the promise resolves!
    const user = use(userPromise);
    return <h1>{user.name}</h1>;
}
```
- **Unique Feature:** Unlike all other React hooks, **`use` can be called inside conditional statements and loops (`if (condition) use(Context)`)**!

---

### Q49: What are `useActionState` and `useOptimistic` in React 19?
**Direct Answer:**  
- **`useActionState` (formerly `useFormState`):** Simplifies handling async form submission actions:
  ```javascript
  const [state, formAction, isPending] = useActionState(async (prevState, formData) => {
      return await updateProfile(formData);
  }, initialState);
  ```
- **`useOptimistic`:** Immediately renders speculative optimistic state while an asynchronous background mutation is in flight, automatically rolling back if the action fails:
  ```javascript
  const [optimisticMessages, setOptimisticMessages] = useOptimistic(messages, (state, newMsg) => [...state, newMsg]);
  ```

---

### Q50: How do you avoid infinite re-render loops in `useEffect`?
**Direct Answer:**  
1. **Never update a state variable that is also listed in the effect's dependency array without an exit condition:**
   ```javascript
   // INFINITE LOOP:
   useEffect(() => { setCount(count + 1); }, [count]);
   ```
2. **Use functional updates:** `setCount(c => c + 1)` and remove `count` from dependencies.
3. **Avoid unstable object/function references in dependencies:** Use primitives, `useMemo`, or `useCallback`.
4. **Distinguish derived state from side effects:** Calculate derived data during render rather than setting state inside an effect.
