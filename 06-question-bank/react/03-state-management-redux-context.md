# ⚛️ State Management: Redux Toolkit, Context, Zustand & TanStack Query (Q51 – Q75)

---

### Q51: What is Prop Drilling and what are the 4 main architectural patterns to avoid it?
**Direct Answer:**  
Prop drilling is the process of passing props through several intermediate layers of components that do not need the data themselves, merely to reach deeply nested children.  
**Architectural Solutions:**
1. **Component Composition:** Pass the child element directly as `children` or explicit slots (`<Layout sidebar={<Nav user={user} />} />`).
2. **React Context:** For global/ambient values (theming, authenticated user session, localization).
3. **External State Stores:** Zustand, Redux Toolkit, or Jotai for complex relational state.
4. **Server Cache Tools:** TanStack Query for remote asynchronous state, eliminating manual prop passing of API responses.

---

### Q52: Why is React Context NOT a true state management solution?
**Direct Answer:**  
React Context is a **dependency injection / transport mechanism**, not a state manager:
- It does not store or manage state on its own; it merely passes a value down the tree.
- Context lacks built-in selector capabilities (cannot subscribe to a slice of state).
- When a context value changes, **all consuming components re-render**, leading to catastrophic performance issues in large-scale applications with frequent updates.
- Redux, Zustand, and TanStack Query provide middleware, devtools, selectors, caching, and state transitions.

---

### Q53: How do you prevent unnecessary re-renders in React Context consumers?
**Direct Answer:**  
1. **Context Splitting:** Split read-heavy state and write dispatch actions into separate contexts:
   ```jsx
   const TodoStateContext = createContext();
   const TodoDispatchContext = createContext();
   ```
2. **Memoize Provider Values:**
   ```jsx
   const value = useMemo(() => ({ user, settings }), [user, settings]);
   return <Context.Provider value={value}>{children}</Context.Provider>;
   ```
3. **Component Decomposition with Memoization:** Extract the consumer into a wrapper component that reads context and passes primitives to a `React.memo` child.

---

### Q54: What is Redux and what are its three fundamental principles?
**Direct Answer:**  
Redux is a predictable, centralized state container for JavaScript apps.  
**Three Fundamental Principles:**
1. **Single Source of Truth:** The global state of the entire application is stored in an object tree within a single Redux store.
2. **State is Read-Only:** The only way to change state is to emit an **action** (an object describing what happened).
3. **Changes are Made with Pure Functions:** **Reducers** are pure functions that take `(previousState, action)` and return a `newState` without mutating previous state.

---

### Q55: What is Redux Toolkit (RTK) and what boilerplate problems does it solve?
**Direct Answer:**  
RTK is the official, opinionated standard for Redux development.  
**Problems solved:**
- Eliminates manual action creator and action type constant declarations via `createSlice`.
- Solves accidental state mutation bugs by integrating **Immer**.
- Pre-configures Redux DevTools and Redux Thunk middleware out of the box via `configureStore`.
- Drastically reduces boilerplate from ~50 lines per action to concise 5-line slice reducers.

---

### Q56: How does Immer work inside Redux Toolkit to allow "mutating" state syntax?
**Direct Answer:**  
In standard Redux, state must be copied manually (`return { ...state, count: state.count + 1 }`).  
RTK uses **Immer** via a JavaScript `Proxy`:
```javascript
const counterSlice = createSlice({
    name: 'counter',
    initialState: { value: 0 },
    reducers: {
        increment: (state) => {
            state.value += 1; // Looks like direct mutation!
        }
    }
});
```
- **How it works:** Immer wraps the state in a proxy "draft". It tracks all property accesses and assignments. When the reducer returns, Immer automatically produces a brand-new, deeply immutable state object containing only the necessary structural sharing modifications.

---

### Q57: What is Redux Thunk and how does it handle asynchronous actions?
**Direct Answer:**  
A Redux middleware that allows action creators to return a **function** instead of an action object:
```javascript
export const fetchUser = (id) => async (dispatch) => {
    dispatch({ type: 'user/pending' });
    try {
        const res = await api.getUser(id);
        dispatch({ type: 'user/fulfilled', payload: res.data });
    } catch (err) {
        dispatch({ type: 'user/rejected', error: err.message });
    }
};
```
- If an action dispatched to the store is a function, Thunk intercepts it and invokes it with `(dispatch, getState)`, delaying the final plain object dispatch until async operations complete.

---

### Q58: What is Redux Saga vs Redux Thunk?
**Direct Answer:**  
- **Redux Thunk:** Uses JavaScript **Promises**. Simple, easy to learn, ideal for 90% of web apps doing standard REST/GraphQL fetching.
- **Redux Saga:** Uses ES6 **Generator functions (`function*`)** and declarative effect descriptors (`call`, `put`, `takeLatest`, `fork`).
- **When Saga is preferred:** Complex enterprise workflows, handling race conditions, debouncing/throttling actions directly in state logic, socket reconnection streams, and cancellation of concurrent tasks.

---

### Q59: What is `createAsyncThunk` in RTK?
**Direct Answer:**  
An RTK utility that generates actions and dispatches promise lifecycles automatically:
```javascript
export const fetchUserById = createAsyncThunk(
    'users/fetchById',
    async (userId, thunkAPI) => {
        const response = await fetch(`/api/users/${userId}`);
        return await response.json();
    }
);

// Handled in extraReducers:
extraReducers: (builder) => {
    builder
        .addCase(fetchUserById.pending, (state) => { state.loading = true; })
        .addCase(fetchUserById.fulfilled, (state, action) => {
            state.loading = false;
            state.data = action.payload;
        })
        .addCase(fetchUserById.rejected, (state, action) => {
            state.loading = false;
            state.error = action.error.message;
        });
}
```

---

### Q60: What are Redux Selectors and why should you use `createSelector` (Reselect)?
**Direct Answer:**  
A selector is a function that extracts a specific slice of data from the store (`state => state.users.items`).  
**Why use `createSelector`:**
- Provides **memoized selectors**. If the input state slices haven't changed reference, the selector recalculates nothing and returns the cached result:
```javascript
export const selectActiveUsers = createSelector(
    [(state) => state.users.items],
    (items) => items.filter(u => u.isActive) // Runs ONLY when items array reference changes!
);
```
Without memoization, running `.filter()` in a standard `useSelector` returns a new array reference on every store action, causing continuous re-renders.

---

### Q61: What is Zustand and why has it become popular over Redux?
**Direct Answer:**  
Zustand is a minimalistic, un-opinionated state management library based on simplified publish-subscribe patterns:
```javascript
import { create } from 'zustand';

export const useStore = create((set) => ({
    count: 0,
    inc: () => set((state) => ({ count: state.count + 1 })),
}));
```
**Key Advantages:**
1. **Zero Provider wrapping:** No `<Provider>` required at the root; state can be read anywhere, even outside React.
2. **Tiny footprint:** (~1KB vs RTK's ~30KB+).
3. **Transient Updates:** Can subscribe to state changes without triggering React re-renders.

---

### Q62: What is Atomic State Management (Jotai / Recoil) vs Single Store?
**Direct Answer:**  
- **Single Store (Redux / Zustand):** The entire application state resides in a monolithic tree. Components select branches.
- **Atomic State (Jotai / Recoil):** State is composed of tiny, independent, decentralized units of state called **atoms**:
  ```javascript
  const countAtom = atom(0);
  const doubleAtom = atom((get) => get(countAtom) * 2);
  ```
- **Benefit:** Modifying an atom only re-renders the exact components subscribed to that specific atom, achieving surgical render performance in complex graphics, canvas apps, or spreadsheet tools.

---

### Q63: Why should Server State be separated from Client State?
**Direct Answer:**  
- **Client State:** Ephemeral, synchronous, 100% owned by the browser (modal open/closed, dark mode toggle, form draft input).
- **Server State:** Asynchronous, shared, remotely persisted, and easily outdated (stale). It requires caching, deduplication, background polling, retry logic, and pagination.
- Storing server data in client state managers (Redux) leads to thousands of lines of boilerplate fetching, loading, error, and caching flags. Using dedicated tools like **TanStack Query** separates concerns cleanly.

---

### Q64: What is TanStack Query (React Query) and what problems does it solve?
**Direct Answer:**  
An asynchronous state management library for fetching, caching, synchronizing, and updating server state in web applications:
```javascript
const { data, isLoading, error } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos
});
```
**Features:**
- Automatic request deduplication across simultaneous component mounts.
- Background cache refetching (on window focus or network reconnect).
- Pagination and infinite scrolling support.
- Automatic retry on network failure.

---

### Q65: How does TanStack Query handle caching: `staleTime` vs `gcTime` (cacheTime)?
**Direct Answer:**  
- **`staleTime` (Default: `0`):** The duration data remains "fresh". As long as data is fresh, subsequent hook invocations return data from the cache **without triggering background network requests**.
- **`gcTime` (formerly `cacheTime`, Default: `5 minutes`):** The duration unused or inactive query data remains in memory before being garbage collected from the cache.
- **Interview Rule:** `staleTime` is for **refetching decisions**; `gcTime` is for **memory cleanup**.

---

### Q66: How do you implement Optimistic Updates in TanStack Query?
**Direct Answer:**  
```javascript
const queryClient = useQueryClient();

const mutation = useMutation({
    mutationFn: updateTodo,
    onMutate: async (newTodo) => {
        await queryClient.cancelQueries({ queryKey: ['todos'] });
        const previousTodos = queryClient.getQueryData(['todos']);

        // Optimistically update cache immediately:
        queryClient.setQueryData(['todos'], (old) => [...old, newTodo]);
        return { previousTodos }; // Context passed to onError
    },
    onError: (err, newTodo, context) => {
        // Rollback on failure!
        queryClient.setQueryData(['todos'], context.previousTodos);
    },
    onSettled: () => {
        // Invalidate to synchronize with server truth
        queryClient.invalidateQueries({ queryKey: ['todos'] });
    },
});
```

---

### Q67: How do you handle cache invalidation in TanStack Query?
**Direct Answer:**  
Use `queryClient.invalidateQueries()`:
```javascript
// Invalidate all queries matching queryKey:
queryClient.invalidateQueries({ queryKey: ['posts'] });

// Invalidate exact query match only:
queryClient.invalidateQueries({ queryKey: ['posts', postId], exact: true });
```
This marks matching queries as stale immediately and triggers an active background refetch if the component is currently mounted on screen.

---

### Q68: What is RTK Query and how does it compare to TanStack Query?
**Direct Answer:**  
- **RTK Query:** An advanced data fetching and caching tool built directly into Redux Toolkit (`createApi`).
- **Comparison:**
  - If your project already uses **Redux Toolkit** extensively for client state, RTK Query eliminates third-party dependencies and integrates seamlessly with Redux devtools and reducers.
  - If your project does not need Redux, **TanStack Query** is framework-agnostic, more flexible, and the industry standard for dedicated server-state caching.

---

### Q69: What is React Hook Form and why is it faster than traditional controlled forms?
**Direct Answer:**  
React Hook Form leverages **uncontrolled inputs using `ref`**:
- In controlled forms (`useState`), typing a single letter in an input field causes the entire form component (and often all other sibling inputs) to re-render.
- React Hook Form isolates re-renders to individual fields using native DOM event listeners, avoiding component re-renders during typing while still offering complete validation, dirty states, and submission handling.

---

### Q70: How do you integrate Schema Validation (Zod) with React Hook Form?
**Direct Answer:**  
Using `@hookform/resolvers/zod`:
```javascript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
    email: z.string().email('Invalid email address'),
    age: z.number().min(18, 'Must be at least 18')
});

function Form() {
    const { register, handleSubmit, formState: { errors } } = useForm({
        resolver: zodResolver(schema)
    });
    const onSubmit = (data) => console.log(data);
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input {...register('email')} />
            {errors.email && <p>{errors.email.message}</p>}
        </form>
    );
}
```

---

### Q71: How do you persist state across page reloads (Redux Persist vs LocalStorage)?
**Direct Answer:**  
1. **Redux Persist:** Automatically saves Redux store slices to `localStorage` or `sessionStorage` on action dispatch, rehydrating store state during initial app boot.
2. **Custom Hook / Direct Sync:**
   ```javascript
   function useLocalStorage(key, initialValue) {
       const [storedValue, setStoredValue] = useState(() => {
           try {
               const item = window.localStorage.getItem(key);
               return item ? JSON.parse(item) : initialValue;
           } catch { return initialValue; }
       });
       const setValue = (val) => {
           setStoredValue(val);
           window.localStorage.setItem(key, JSON.stringify(val));
       };
       return [storedValue, setValue];
   }
   ```

---

### Q72: What is the Finite State Machine (FSM) pattern in React using XState?
**Direct Answer:**  
An FSM guarantees that a component can only exist in **one finite state at a time** (e.g. `idle`, `loading`, `success`, `error`) and transitions only happen through explicit events:
- **Prevents Impossible States:** Eliminates boolean explosion where `{ isLoading: true, isError: true, isSuccess: true }` can accidentally be true simultaneously.
- **XState:** Provides statecharts, visual diagrams, and deterministic execution for mission-critical workflows (checkout funnels, multi-step auth).

---

### Q73: How do you share state between multiple browser tabs in React?
**Direct Answer:**  
1. **`BroadcastChannel` API:** A lightweight browser API for message passing between tabs with the same origin:
   ```javascript
   const channel = new BroadcastChannel('auth_channel');
   channel.postMessage({ type: 'LOGOUT' });
   channel.onmessage = (event) => { if (event.data.type === 'LOGOUT') logout(); };
   ```
2. **`window.addEventListener('storage', callback)`:** Fires exclusively in sibling tabs when `localStorage.setItem` is called.

---

### Q74: What is the Derived State anti-pattern and how should you handle it?
**Direct Answer:**  
- **Anti-Pattern:** Storing values in `useState` that can be calculated on the fly from existing props or state:
  ```javascript
  // BAD: Redundant state and synchronization nightmare
  const [items, setItems] = useState([]);
  const [totalCount, setTotalCount] = useState(0);
  ```
- **Best Practice:** Compute derived state during render:
  ```javascript
  // GOOD: Single source of truth
  const [items, setItems] = useState([]);
  const totalCount = items.length; // Or useMemo for heavy calculations
  ```

---

### Q75: What is Normalized State in Redux/Client state and why is it essential?
**Direct Answer:**  
Organizing data like a relational database: flat dictionaries keyed by ID rather than nested arrays of objects:
```javascript
{
    users: { byId: { "1": { id: "1", name: "Alice" } }, allIds: ["1"] },
    posts: { byId: { "101": { id: "101", authorId: "1", title: "React" } }, allIds: ["101"] }
}
```
**Benefits:**
1. Prevents duplicate data in multiple nested branches.
2. Updates require mutating a single record by ID in $O(1)$ time rather than searching and mapping through nested arrays in $O(N)$.
3. Redux Toolkit provides `createEntityAdapter` to automate state normalization.
