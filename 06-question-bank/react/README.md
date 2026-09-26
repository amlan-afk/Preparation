# ⚛️ React Interview Mastery: Top 100 Questions & Answers

A comprehensive collection of **100 high-yield, production-grade React interview questions and answers** for SDE 2 and Senior Frontend/Full-Stack Engineers. Structured into 4 organized modules covering the Fiber reconciler, hooks internals, modern state management, and enterprise performance/SSR architectures.

---

## 🗺️ Curriculum Module Index

| Module | Topic Range | File Link | Question Count | Core Subjects Covered |
|:---:|---|---|:---:|---|
| **01** | **Architecture, VDOM & Components** | [01-rendering-jsx-and-components.md](./01-rendering-jsx-and-components.md) | **Q1 – Q25** | Virtual DOM, React Fiber, Diffing $O(N)$, JSX Compilation, Immutability, `key` prop, SyntheticEvent, Error Boundaries, React 18 Batching, Portals. |
| **02** | **Hooks In-Depth: Mechanics & Patterns** | [02-hooks-in-depth.md](./02-hooks-in-depth.md) | **Q26 – Q50** | Rules of Hooks, Fiber `memoizedState` linked list, `useState`, `useEffect` vs `useLayoutEffect`, `useTransition`, `useDeferredValue`, Stale Closures, Custom Hooks (`useDebounce`, `usePrevious`, `useOnClickOutside`). |
| **03** | **State Management, Redux & Data Fetching** | [03-state-management-redux-context.md](./03-state-management-redux-context.md) | **Q51 – Q75** | Context vs Redux, Context Splitting, Redux Toolkit (RTK), Immer Proxy, Reselect Memoization, Zustand, TanStack Query (React Query), Optimistic Updates, React Hook Form + Zod. |
| **04** | **Performance, Routing, SSR & Modern React** | [04-performance-routing-and-ssr.md](./04-performance-routing-and-ssr.md) | **Q76 – Q100** | React Profiler, Code Splitting (`React.lazy`/`Suspense`), List Virtualization, React Compiler (React 19), SSR vs SSG vs ISR, Hydration & Streaming, RSC, Next.js App Router, Web Vitals (LCP, INP, CLS), React Testing Library. |
| | **TOTAL** | | **100 Questions** | **100% Comprehensive Senior React Mastery** |

---

## 🎯 Master List of All 100 Questions

### Module 01: Core Architecture, Virtual DOM & Components (Q1 – Q25)
1. What is the Virtual DOM and how does it work?
2. What is React Fiber and why was the reconciler rewritten?
3. How does the Reconciliation / Diffing Algorithm achieve $O(N)$ complexity?
4. What is JSX and how does it compile?
5. Why is State Immutable in React? What happens if you mutate state directly?
6. Why are `key` props required in lists? What happens if you use `index` as a key?
7. What are Controlled vs Uncontrolled Components?
8. How does React's SyntheticEvent system work?
9. What are Error Boundaries and what errors can they NOT catch?
10. What is `React.memo` and how does it prevent re-renders?
11. What is Automatic Batching in React 18?
12. What is StrictMode in React 18 and why do components mount twice in development?
13. What are React Portals and when should you use them?
14. What is the trap with conditional rendering using the `&&` operator?
15. How does Component Composition replace Inheritance in React?
16. What is the difference between `React.Fragment` and a standard `<div>` wrapper?
17. What are Higher-Order Components (HOC)?
18. What is the difference between Element and Component in React?
19. What is the difference between `shadow DOM` and `virtual DOM`?
20. How do you prevent unnecessary re-renders in children when passing callbacks?
21. What is the Compound Component Pattern in React?
22. What is the Polymorphic `as` Prop Pattern?
23. Why can't React components return multiple siblings without a Fragment or Array?
24. What is the difference between mounting, updating, and unmounting?
25. How do you clean up side effects to prevent memory leaks on unmount?

---

### Module 02: Hooks In-Depth: Mechanics, Pitfalls & Patterns (Q26 – Q50)
26. What are the Rules of Hooks and why must hooks only be called at the top level?
27. How does `useState` work under the hood in React Fiber?
28. Why does `setState` take an updater function (`setCount(c => c + 1)`) vs a direct value?
29. What is a "stale closure" in React hooks and how do you fix it?
30. What is the exact difference between `useEffect` and `useLayoutEffect`?
31. What is `useInsertionEffect` and when should it be used?
32. When should you use `useRef` vs `useState`? Can updating a ref trigger a re-render?
33. What is `useMemo` vs `useCallback`? When is using them an anti-pattern?
34. How does `useContext` work under the hood and what is its re-render pitfall?
35. When should you choose `useReducer` over `useState`?
36. What is `forwardRef` and `useImperativeHandle`? How does React 19 change `ref` as a prop?
37. What is `useId` and why shouldn't you use `Math.random()` for SSR IDs?
38. What is `useTransition` and how does it prevent UI freezing during heavy updates?
39. What is `useDeferredValue` and how does it differ from debouncing/throttling?
40. What is `useSyncExternalStore` and why was it introduced in React 18?
41. How do you build a custom hook `useDebounce`?
42. How do you build a custom hook `usePrevious`?
43. How do you build a custom hook `useOnClickOutside`?
44. How do you build a custom hook `useMediaQuery`?
45. Why should you avoid putting objects or functions in `useEffect` dependency arrays?
46. How do you cancel an in-flight `fetch` request in `useEffect` using `AbortController`?
47. What happens if an error occurs inside `useEffect`? Can an Error Boundary catch it?
48. What is the `use` hook in React 19?
49. What are `useActionState` and `useOptimistic` in React 19?
50. How do you avoid infinite re-render loops in `useEffect`?

---

### Module 03: State Management: Redux Toolkit, Context, Zustand & TanStack Query (Q51 – Q75)
51. What is Prop Drilling and what are the 4 main architectural patterns to avoid it?
52. Why is React Context NOT a true state management solution?
53. How do you prevent unnecessary re-renders in React Context consumers?
54. What is Redux and what are its three fundamental principles?
55. What is Redux Toolkit (RTK) and what boilerplate problems does it solve?
56. How does Immer work inside Redux Toolkit to allow "mutating" state syntax?
57. What is Redux Thunk and how does it handle asynchronous actions?
58. What is Redux Saga vs Redux Thunk?
59. What is `createAsyncThunk` in RTK?
60. What are Redux Selectors and why should you use `createSelector` (Reselect)?
61. What is Zustand and why has it become popular over Redux?
62. What is Atomic State Management (Jotai / Recoil) vs Single Store?
63. Why should Server State be separated from Client State?
64. What is TanStack Query (React Query) and what problems does it solve?
65. How does TanStack Query handle caching: `staleTime` vs `gcTime` (cacheTime)?
66. How do you implement Optimistic Updates in TanStack Query?
67. How do you handle cache invalidation in TanStack Query?
68. What is RTK Query and how does it compare to TanStack Query?
69. What is React Hook Form and why is it faster than traditional controlled forms?
70. How do you integrate Schema Validation (Zod) with React Hook Form?
71. How do you persist state across page reloads (Redux Persist vs LocalStorage)?
72. What is the Finite State Machine (FSM) pattern in React using XState?
73. How do you share state between multiple browser tabs in React?
74. What is the Derived State anti-pattern and how should you handle it?
75. What is Normalized State in Redux/Client state and why is it essential?

---

### Module 04: Performance Optimization, Routing, SSR & Modern React (Q76 – Q100)
76. How do you diagnose and measure slow re-renders using React DevTools Profiler?
77. What is Code Splitting and how do `React.lazy()` and `<Suspense>` work?
78. What is List Virtualization (Windowing) and how does it work?
79. What is the React Compiler (React Forget) introduced in React 19?
80. What is the difference between SPA (CSR), SSR, SSG, and ISR?
81. What is Hydration and what causes Hydration Mismatch errors?
82. What is Selective Hydration and Streaming SSR with React 18 Suspense?
83. What are React Server Components (RSC) and how do they differ from SSR?
84. What can and cannot be done inside a Server Component vs Client Component?
85. How does client-side routing work under the hood in React Router?
86. What are Loaders, Actions, and Data APIs in React Router 6.4+ / Remix?
87. What is the difference between Next.js Pages Router and App Router?
88. How do Server Actions work in Next.js / React 19?
89. How do you optimize web vital LCP (Largest Contentful Paint) in React?
90. How do you optimize INP (Interaction to Next Paint) and eliminate long tasks?
91. How do you optimize CLS (Cumulative Layout Shift) in React?
92. What are Web Workers and how do you offload heavy computations in React?
93. How do you implement debouncing and throttling for search inputs or scroll listeners?
94. What is the difference between shallow rendering vs deep rendering in React testing?
95. Why does React Testing Library prioritize querying by Accessibility Roles (`getByRole`)?
96. How do you test asynchronous code and hooks using React Testing Library?
97. How do you mock API calls in React tests using Mock Service Worker (MSW)?
98. What are Micro-Frontends in React and how does Webpack Module Federation work?
99. How do you secure React applications against XSS and CSRF?
100. How do you design and structure an enterprise-grade React codebase for scalability?
