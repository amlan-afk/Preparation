# ⚛️ Performance Optimization, Routing, SSR & Modern React (Q76 – Q100)

---

### Q76: How do you diagnose and measure slow re-renders using React DevTools Profiler?
**Direct Answer:**  
1. Open Chrome DevTools $\rightarrow$ **Profiler** tab.
2. Enable **"Record why each component rendered while profiling"** in settings.
3. Record an interaction and inspect the **Flamegraph** and **Ranked chart**:
   - **Yellow/Orange bars:** Heavy renders taking significant CPU time.
   - **Hovering:** Shows the exact reason ("Props changed", "Hook 1 changed", or "Parent component rendered").
4. Use the `why-did-you-render` library in development to log unnecessary re-renders directly in the browser console.

---

### Q77: What is Code Splitting and how do `React.lazy()` and `<Suspense>` work?
**Direct Answer:**  
Code splitting breaks a large single JavaScript bundle into small chunks loaded on-demand:
```javascript
import { lazy, Suspense } from 'react';

const HeavyDashboard = lazy(() => import('./HeavyDashboard'));

function App() {
    return (
        <Suspense fallback={<Spinner />}>
            <HeavyDashboard />
        </Suspense>
    );
}
```
- **How it works:** Webpack / Vite generates a separate chunk file. When `<Suspense>` mounts, `React.lazy` triggers a dynamic `import()`. React catches the thrown promise, renders the `fallback` UI, and renders the resolved component once the script downloads.

---

### Q78: What is List Virtualization (Windowing) and how does it work?
**Direct Answer:**  
Rendering only the items currently visible inside the viewport (plus a small overscan buffer), rather than rendering thousands of DOM elements:
- In a list of 10,000 items, virtualization keeps only ~20 DOM nodes in memory at any moment.
- As the user scrolls, elements moving out of view are recycled or unmounted, keeping memory footprint low and scrolling smooth at 60/120 FPS.
- **Popular Libraries:** `@tanstack/react-virtual`, `react-window`.

---

### Q79: What is the React Compiler (React Forget) introduced in React 19?
**Direct Answer:**  
An optimizing, ahead-of-time (AOT) compiler built by the React team:
- **Problem it solves:** Developers historically spent significant effort manually managing `useMemo`, `useCallback`, and `React.memo` to prevent re-renders.
- **How it works:** The compiler analyzes JavaScript semantics and automatically injects fine-grained memoization of values and component outputs at build time. It eliminates manual `useMemo` / `useCallback` boilerplate while achieving maximum runtime performance.

---

### Q80: What is the difference between SPA (CSR), SSR, SSG, and ISR?
**Direct Answer:**  
| Paradigm | Rendering Location & Timing | First Load (FCP) | SEO | Best For |
|---|---|---|---|---|
| **CSR (SPA)** | Browser builds DOM via JS | Slow | Weak | Admin dashboards, internal tools |
| **SSR** | Node.js server renders HTML on every request | Fast | Excellent | Dynamic feeds, personalized e-commerce |
| **SSG** | Generated at build time into static HTML files | Instant (CDN) | Perfect | Marketing blogs, documentation |
| **ISR** | Static generation + revalidated in background | Instant (CDN) | Perfect | High-traffic e-commerce product catalogs |

---

### Q81: What is Hydration and what causes Hydration Mismatch errors?
**Direct Answer:**  
- **Hydration:** The process where the client-side React runtime attaches event listeners to the pre-rendered static HTML delivered by the server, turning it into an interactive React application.
- **Hydration Mismatch:** Occurs when the server-rendered HTML differs from the initial client render tree:
  - Common causes: Rendering `new Date().toLocaleTimeString()`, `Math.random()`, accessing `window` or `localStorage` during initial render, or invalid HTML nesting (e.g. `<p>` inside `<p>`, or `<div>` inside `<span>`).

---

### Q82: What is Selective Hydration and Streaming SSR with React 18 Suspense?
**Direct Answer:**  
- **Traditional SSR:** The server had to render the entire page before sending anything, and the client had to download all JS and hydrate the entire tree before anything became interactive (all-or-nothing bottleneck).
- **Streaming SSR (`renderToPipeableStream`):** The server streams HTML chunks over HTTP as they become ready.
- **Selective Hydration:** Components wrapped in `<Suspense>` hydrate independently. If a user clicks on an un-hydrated interactive element, React prioritizes hydrating that specific subtree immediately!

---

### Q83: What are React Server Components (RSC) and how do they differ from SSR?
**Direct Answer:**  
- **SSR:** Executes client components on the server to produce initial HTML; the JavaScript bundle for those components is **still downloaded** and hydrated by the browser.
- **RSC (Server Components):** Components that execute **strictly on the server and NEVER download JavaScript to the client**:
  - Direct database access (`db.query()`), private secret keys, and heavy server libraries (like Markdown parsers) have **zero impact on client bundle size**.
  - RSC renders to a special JSON-like virtual tree stream, not raw HTML.

---

### Q84: What can and cannot be done inside a Server Component vs Client Component?
**Direct Answer:**  
- **Server Component (Default in Next.js App Router):**
  - **CAN:** Access filesystem, query databases directly, use async/await top-level, use server secrets.
  - **CANNOT:** Use React hooks (`useState`, `useEffect`), use browser APIs (`localStorage`, `window`), or attach event listeners (`onClick`).
- **Client Component (`'use client'`):**
  - **CAN:** Use all hooks, handle clicks, animations, browser storage, and Web APIs.
  - **CANNOT:** Directly query databases or access server environment secrets securely.

---

### Q85: How does client-side routing work under the hood in React Router?
**Direct Answer:**  
Uses the browser's **HTML5 History API (`history.pushState`, `history.replaceState`)** and the **`popstate` event**:
1. When a `<Link to="/profile">` is clicked, React Router intercepts the native browser link navigation (`e.preventDefault()`).
2. Calls `history.pushState({}, '', '/profile')` to update the URL bar without reloading the page.
3. React Router internal state updates, matching the new path against the route configuration table and rendering the corresponding route component.

---

### Q86: What are Loaders, Actions, and Data APIs in React Router 6.4+ / Remix?
**Direct Answer:**  
- **`loader`:** An async function paired with a route that fetches data **before** the route component begins rendering, eliminating "render-then-fetch" waterfalls.
- **`action`:** An async function handling route mutations (POST, PUT, DELETE) triggered by React Router `<Form>` submissions:
```javascript
export async function loader({ params }) {
    return fetchUser(params.id);
}
export async function action({ request }) {
    const formData = await request.formData();
    return updateUser(formData);
}
```

---

### Q87: What is the difference between Next.js Pages Router and App Router?
**Direct Answer:**  
- **Pages Router (`/pages`):** Component-based routing, uses `getServerSideProps` and `getStaticProps`, all components are client-side components with SSR pre-rendering.
- **App Router (`/app`):** Built on React Server Components (RSC), uses nested layouts (`layout.js`, `page.js`, `loading.js`, `error.js`), supports Streaming SSR and Server Actions natively.

---

### Q88: How do Server Actions work in Next.js / React 19?
**Direct Answer:**  
Asynchronous functions executed strictly on the server that can be called directly from client forms or buttons without manually creating REST API endpoints:
```jsx
// Server Action:
async function updateName(formData) {
    'use server';
    const name = formData.get('name');
    await db.user.update({ where: { id: 1 }, data: { name } });
}

// Client Component:
<form action={updateName}>
    <input name="name" />
    <button type="submit">Save</button>
</form>
```
Under the hood, Next.js generates an automated POST endpoint and handles serialization.

---

### Q89: How do you optimize web vital LCP (Largest Contentful Paint) in React?
**Direct Answer:**  
1. **Preload priority images:** Use `<link rel="preload" as="image" href="..." />` or Next.js `<Image priority />`.
2. **Implement SSR / SSG:** Serve pre-rendered HTML containing hero content instead of empty `<div id="root"></div>`.
3. **Optimize Web Fonts:** Use `font-display: swap` or self-host fonts with zero layout shift.
4. **Remove render-blocking resources:** Defer non-critical scripts and split bundles.

---

### Q90: How do you optimize INP (Interaction to Next Paint) and eliminate long tasks?
**Direct Answer:**  
INP measures UI responsiveness to user interactions. Long tasks (>50ms) block the main thread:
1. **Break up long tasks:** Yield execution back to the browser using `scheduler.yield()` or `setTimeout(0)`.
2. **Use React Concurrent Features:** Wrap slow non-urgent rendering in `useTransition` or `startTransition`.
3. **Debounce event listeners:** Debounce resize, scroll, and rapid search inputs.
4. **Move heavy calculations to Web Workers:** Keep the UI thread free for input dispatch.

---

### Q91: How do you optimize CLS (Cumulative Layout Shift) in React?
**Direct Answer:**  
1. **Explicit Dimensions:** Always set `width` and `height` (or `aspect-ratio` in CSS) on images and video containers before assets load.
2. **Skeleton Screens:** Reserve space for asynchronously loaded cards or ads with placeholder skeleton loaders.
3. **Avoid inserting elements above existing content:** Render banners or notifications in fixed containers or append them without shifting page layout.

---

### Q92: What are Web Workers and how do you offload heavy computations in React?
**Direct Answer:**  
Web Workers run JavaScript scripts in background threads independent of the main UI thread:
```javascript
// worker.js
self.onmessage = (e) => {
    const result = heavyCalculation(e.data);
    self.postMessage(result);
};

// React component
const worker = new Worker(new URL('./worker.js', import.meta.url));
worker.postMessage(data);
worker.onmessage = (e) => setCalculatedData(e.data);
```
Prevents UI freezes, dropped frames, and input latency during heavy cryptographic, parsing, or data-crunching tasks.

---

### Q93: How do you implement debouncing and throttling for search inputs or scroll listeners?
**Direct Answer:**  
- **Debounce:** Waits for a pause in events before invoking the callback (ideal for search autocomplete):
  ```javascript
  const debouncedSearch = useMemo(
      () => debounce((query) => fetchResults(query), 300),
      []
  );
  ```
- **Throttle:** Guarantees execution at most once every $X$ milliseconds (ideal for scroll progress or window resize handlers).

---

### Q94: What is the difference between shallow rendering vs deep rendering in React testing?
**Direct Answer:**  
- **Shallow Rendering (Enzyme legacy):** Renders only the component itself, mocking all child components as empty tags. Fragile, tests implementation details rather than user experience.
- **Deep Rendering (React Testing Library):** Mounts the full component tree and renders real DOM nodes into a simulated JSDOM environment, testing actual user-visible behavior.

---

### Q95: Why does React Testing Library prioritize querying by Accessibility Roles (`getByRole`)?
**Direct Answer:**  
RTL's guiding principle: *"The more your tests resemble the way your software is used, the more confidence they can give you."*
- `getByRole('button', { name: /submit/i })` mirrors how real users and assistive screen readers find elements.
- Tests written against test IDs or class names pass even if buttons are unclickable or inaccessible to disabled users.

---

### Q96: How do you test asynchronous code and hooks using React Testing Library?
**Direct Answer:**  
1. **Asynchronous elements (`findBy` and `waitFor`):**
   ```javascript
   test('loads and displays user', async () => {
       render(<UserProfile id="1" />);
       expect(screen.getByText(/loading/i)).toBeInTheDocument();
       // findBy queries poll the DOM until the element appears:
       const userName = await screen.findByText('John Doe');
       expect(userName).toBeInTheDocument();
   });
   ```
2. **Testing Custom Hooks:**
   ```javascript
   const { result } = renderHook(() => useCounter());
   act(() => { result.current.increment(); });
   expect(result.current.count).toBe(1);
   ```

---

### Q97: How do you mock API calls in React tests using Mock Service Worker (MSW)?
**Direct Answer:**  
MSW intercepts network requests at the network layer using Service Workers (in browser) or NodeJS interceptors:
```javascript
import { setupServer } from 'msw/node';
import { http, HttpResponse } from 'msw';

export const server = setupServer(
    http.get('/api/user', () => {
        return HttpResponse.json({ name: 'Alice' });
    })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```
Avoids mocking `fetch` or `axios` instances directly, allowing real HTTP request pipelines to be verified.

---

### Q98: What are Micro-Frontends in React and how does Webpack Module Federation work?
**Direct Answer:**  
Micro-frontends decompose a monolithic frontend into independent, autonomously deployable applications:
- **Webpack Module Federation:** Allows an application dynamically to import remote JavaScript bundles at runtime from a different build / server as if they were local npm packages.
- A "Host" app imports a "Remote" component (`import('navbar/Header')`) without rebuilding the host app when the navbar team pushes updates.

---

### Q99: How do you secure React applications against XSS and CSRF?
**Direct Answer:**  
1. **XSS (Cross-Site Scripting):**
   - React automatically escapes strings embedded in JSX before rendering them to the DOM.
   - **Never use `dangerouslySetInnerHTML`** with untrusted user input without sanitizing via `DOMPurify.sanitize()`.
   - Validate and sanitize URLs used in `<a href={userUrl}>` to prevent `javascript:` execution attacks.
2. **CSRF (Cross-Site Request Forgery):**
   - Store authentication tokens in `HttpOnly`, `SameSite=Strict`, `Secure` cookies.
   - Include CSRF anti-forgery tokens in custom headers for state-mutating requests (POST, PUT, DELETE).

---

### Q100: How do you design and structure an enterprise-grade React codebase for scalability?
**Direct Answer:**  
1. **Feature-Based Architecture:** Group code by domain feature rather than technical type:
   ```text
   src/
   ├── features/
   │   ├── auth/
   │   │   ├── components/
   │   │   ├── hooks/
   │   │   ├── api/
   │   │   └── types/
   │   └── billing/
   ├── components/ui/ (Shared atomic design system)
   ├── lib/ (Axios/TanStack clients, utilities)
   └── routes/
   ```
2. **Strict Public API Boundaries:** Use `index.ts` files inside each feature folder to export only the public contract, preventing unorganized cross-feature imports.
3. **Automated Quality Guards:** Strict TypeScript configs, ESLint rules, Prettier formatting, Husky pre-commit hooks, and CI unit/E2E test pipelines.
