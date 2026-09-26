# 📱 React Native Core: Architecture, Threading & The New Architecture (Q1 – Q25)

---

### Q1: What is React Native and how does it fundamentally differ from hybrid web apps and Flutter?
**Direct Answer:**  
- **React Native:** Executes JavaScript logic on a background engine (Hermes), but renders **100% genuine native platform widgets** (`UIView` on iOS, `android.view.ViewGroup` on Android) rather than running inside an embedded web view.
- **Hybrid Web Apps (Cordova, Capacitor, Ionic):** Render HTML/CSS inside a full-screen mobile browser (`WKWebView` / Android `WebView`). DOM reflows and CSS painting cause high input latency, sluggish scrolling, and poor gesture fidelity.
- **Flutter:** Completely bypasses native platform UI widgets. It compiles Dart to ARM machine code and draws every single pixel manually onto a canvas using its own rendering engine (Impeller / Skia). While consistent, it reimplements platform behaviors (text selection, accessibility) from scratch.

---

### Q2: Explain the Old Bridge Architecture and why it became a performance bottleneck.
**Direct Answer:**  
In the legacy architecture (prior to the New Architecture):
1. **Three Isolated Realities:**
   - **JavaScript Thread:** Runs React components, state, hooks, and business logic.
   - **Native Main Thread (UI Thread):** Handles user gestures, screen rendering, and native platform events.
   - **Shadow Thread:** Computes layout dimensions using the Yoga engine.
2. **The Asynchronous Bridge:** All communication between JavaScript and Native layers occurred across an asynchronous, batched, JSON-serialized message queue.
3. **The Bottleneck:**
   - Serializing data into JSON strings, passing them across the C++ bridge, and deserializing them on the other side introduces high CPU overhead and serialization latency.
   - Because bridge communication is strictly asynchronous, simultaneous events (e.g. continuous scrolling on the UI thread while JS computes new list items) lead to visible blank spaces ("white screens") and frame drops.

---

### Q3: What is the New Architecture in React Native and what are its four pillars?
**Direct Answer:**  
The New Architecture is a complete rewrite of React Native's core internals to eliminate the asynchronous JSON bridge and bring React 18+ concurrent features to mobile.  
**Four Pillars:**
1. **JSI (JavaScript Interface):** A lightweight C++ abstraction layer allowing JavaScript to hold direct reference pointers to C++ host objects and invoke native methods synchronously.
2. **Fabric:** The modern C++ rendering engine that handles component tree creation and synchronization directly with native platform views.
3. **TurboModules:** The next-generation native modules system providing lazy loading and direct synchronous/asynchronous C++ calls.
4. **Codegen:** A build-time tool that converts static TypeScript or Flow type definitions into strongly typed C++ interfaces for Fabric and TurboModules.

---

### Q4: What is JSI (JavaScript Interface) and how does it eliminate asynchronous JSON serialization?
**Direct Answer:**  
JSI is a unified C++ API that allows the JavaScript virtual machine (Hermes, V8, JSC) to directly manipulate C++ host objects:
- **No JSON Serialization:** Instead of converting a JavaScript object into a JSON string `{"x":10,"y":20}`, passing it over a socket, and parsing it in Objective-C/Java, JSI lets JavaScript call a C++ function directly with zero translation overhead.
- **Direct Memory Reference:** The JavaScript runtime holds a direct pointer to the C++ object and vice versa.
- **Synchronous Execution:** Native functions can be called synchronously (crucial for animations, gestural tracking, and instant measurement).

---

### Q5: What is Fabric and how does it revolutionize the rendering pipeline?
**Direct Answer:**  
Fabric is React Native's modern rendering engine:
1. **Unified C++ Core:** The component tree and layout logic (Yoga) exist in shared C++, eliminating duplicate platform implementations across iOS and Android.
2. **Immutable Element Trees:** Fabric uses immutable React element trees. Render updates generate new trees via structural sharing, ensuring thread safety.
3. **Multi-Threaded Rendering:** Rendering work can happen across multiple threads:
   - Synchronous priority renders (user inputs, gestures) can execute directly on the UI thread without waiting for a round-trip from the JS thread.
   - Background non-urgent transitions can be computed off-thread.
4. **Seamless React 18 Support:** Unlocks Suspense, `useTransition`, and Concurrent React on native mobile.

---

### Q6: What are TurboModules and how do they differ from legacy Native Modules?
**Direct Answer:**  
- **Legacy Native Modules:** All native modules (Bluetooth, Camera, Geolocation, Storage) were eagerly registered, initialized, and loaded into memory on application launch, severely degrading startup time even if the user never opened those features.
- **TurboModules:**
  1. **Lazy Loading:** Modules are initialized **only when first invoked in JavaScript**, drastically accelerating cold app startup time.
  2. **Direct JSI Invocation:** Calls bypass the JSON bridge, communicating directly through C++ bindings generated by Codegen.
  3. **Synchronous & Asynchronous:** Can return values synchronously when needed (e.g. reading from high-speed storage).

---

### Q7: What is Codegen and why is it essential in the New Architecture?
**Direct Answer:**  
Codegen is an automated build-time compiler:
1. Takes a static TypeScript/Flow specification file (e.g. `NativeCameraSpec.ts`).
2. Generates the corresponding C++ glue code, Objective-C protocols, and Java/Kotlin interface bindings.
3. **Guarantees Type Safety Across Boundaries:** If JavaScript passes a string where native code expects an integer, compilation fails at build time rather than crashing the mobile app at runtime. It eliminates manual JNI (Java Native Interface) and Objective-C boilerplate.

---

### Q8: What is the Hermes JavaScript engine and why is it superior on mobile?
**Direct Answer:**  
Hermes is an open-source JavaScript engine optimized by Meta specifically for running React Native on mobile:
1. **Ahead-of-Time (AOT) Bytecode Compilation:** JavaScript code is pre-compiled into compact bytecode (`index.android.bundle` $\rightarrow$ Hermes bytecode) during app build time, eliminating runtime parsing and compilation on the user's device.
2. **Instant App Launch (TTI):** Cuts Time-to-Interactive by 50%+ because the VM executes bytecode immediately upon startup.
3. **Drastically Lower Memory Footprint:** Bytecode is mapped directly into read-only memory, reducing RAM consumption and preventing Out-Of-Memory (OOM) background kills on budget Android devices.
4. **Optimized Garbage Collector:** Generational, non-contiguous GC tailored for mobile memory limits.

---

### Q9: Explain the Threading Model in React Native.
**Direct Answer:**  
React Native operates across three primary threads:
1. **UI Main Thread (Platform Thread):** Responsible for native Android/iOS view rendering, user touch gesture events, and screen display updates (60/120 Hz).
2. **JavaScript Thread (JS Thread):** Runs the JavaScript VM (Hermes), React component lifecycles, state updates, business logic, API calls, and event handlers.
3. **Shadow / Layout Thread:** In the legacy architecture, this background thread calculated Flexbox layouts via Yoga. In the New Architecture, Fabric executes layout computations across threads safely using C++.
*Note: Background native worker queues handle network requests, file I/O, and audio.*

---

### Q10: What causes dropped frames (UI vs JS thread FPS drops) and how do they feel differently to the user?
**Direct Answer:**  
- **UI Thread Frame Drops (< 60 FPS):**
  - **Feel:** Jerky native scrolling, stuttering transitions, frozen UI, unresponsive touch feedback.
  - **Causes:** Complex view hierarchies, heavy drawing on canvas, blocking native main thread with long-running operations or un-optimized images.
- **JS Thread Frame Drops (< 60 FPS):**
  - **Feel:** Scrolling remains silky smooth (handled natively), but **interactions feel dead or delayed**—pressing a button triggers the ripple/highlight, but the screen does not transition or react until seconds later.
  - **Causes:** Massive synchronous array operations, heavy JSON parsing, un-memoized re-renders of large component subtrees.

---

### Q11: What is Yoga and how does layout calculation work in React Native?
**Direct Answer:**  
Yoga is an open-source, cross-platform C++ layout engine implementing the W3C Flexbox specification:
1. React Native components define layout using standard Flexbox style properties (`flex: 1`, `flexDirection`, `justifyContent`, `alignItems`).
2. React Native passes these styles to the Yoga C++ engine.
3. Yoga calculates the precise physical bounding box coordinates (`x`, `y`, `width`, `height`) for every element on the target device screen.
4. These computed coordinates are passed to the platform UI layer to position `UIView` (iOS) or `android.view.View` (Android).

---

### Q12: How does React Native Flexbox differ from Web CSS Flexbox?
**Direct Answer:**  
1. **Default Direction:** In React Native, `flexDirection` defaults to **`column`** (vertical orientation suitable for mobile phones). In Web CSS, it defaults to `row`.
2. **Flex Shrink Default:** In React Native, `flexShrink` defaults to `0` (elements do not shrink automatically). In Web CSS, it defaults to `1`.
3. **Unitless Numbers:** Layout sizes are specified as pure unitless density-independent numbers (`width: 100`), not strings with `px`, `rem`, or `em`.
4. **No Cascading / Inheritance:** Styles do not cascade down the tree (except for nested `<Text>` components inheriting font styles from parent `<Text>`).
5. **No Grid / Floats:** Standard CSS Grid and float properties do not exist in Yoga.

---

### Q13: How does Metro Bundler work and how does it bundle assets for mobile?
**Direct Answer:**  
Metro is the dedicated JavaScript bundler for React Native:
1. **Resolution:** Starts from root entry (`index.js`), builds a dependency graph resolving `import`/`require` statements, supporting platform-specific extensions (`.ios.js`, `.android.js`, `.native.js`).
2. **Transformation:** Transpiles code using Babel (converting JSX, TypeScript, and modern ES features into compatible JS).
3. **Serialization:** Combines all processed modules into a single monolithic bundle file (`index.bundle`) along with asset catalogs (images, fonts).
4. **Fast Refresh Engine:** Uses WebSockets during development to send delta bundles instantly upon file modification without reloading the entire app.

---

### Q14: What happens during the React Native App Startup / Bootstrap Sequence?
**Direct Answer:**  
1. **Native App Launch:** OS launches the native application process (`AppDelegate` on iOS, `MainApplication` on Android).
2. **VM Initialization:** Native app boots the JavaScript engine (Hermes).
3. **Bridge / JSI Setup:** Instantiates the C++ runtime, binds JSI host functions, and sets up Fabric/TurboModules.
4. **JS Bundle Execution:** Hermes loads and executes the pre-compiled bytecode bundle, registering the root component via `AppRegistry.registerComponent()`.
5. **Initial Render:** React constructs the element tree, Yoga calculates layout, Fabric generates native views, and the native UI thread displays the first frame.

---

### Q15: What is the difference between `<View>` and a native `UIView` / `android.view.ViewGroup`?
**Direct Answer:**  
- `<View>` is the fundamental React element for building UI layouts (equivalent to a `<div>` on web).
- Under the hood, `<View>` renders as:
  - **iOS:** An instance of `RCTView`, which inherits directly from Apple's native `UIView`.
  - **Android:** An instance of `ReactViewGroup`, which inherits from `android.view.ViewGroup`.
- **View Flattening:** Fabric / Yoga automatically flattens views that only serve styling/layout purposes (views with no background color, borders, or touch handlers) to avoid creating deep native view hierarchies, saving memory and GPU draw calls.

---

### Q16: How does `<Text>` work in React Native and why can't raw text be placed directly in `<View>`?
**Direct Answer:**  
- **Why raw text fails:** In React Native, `<View>` maps to a native container widget (`UIView` / `ViewGroup`) that has **no native ability to render typography**.
- Placing raw text directly inside `<View>` (`<View>Hello</View>`) throws a fatal runtime exception: *"Text strings must be rendered within a `<Text>` component."*
- `<Text>` maps to `RCTTextView` (iOS) and `ReactTextView` (Android).
- **Text Nesting:** Placing `<Text>` inside `<Text>` allows typographic styling inheritance (e.g. bolding a single word in a paragraph) while preserving inline layout flow.

---

### Q17: What is `<Image>` in React Native and what are the pitfalls of rendering remote images?
**Direct Answer:**  
`<Image source={...} />` renders bitmap graphics:
- **Local Assets:** `source={require('./logo.png')}` — bundled into the native app binary.
- **Remote Network Images:** `source={{ uri: 'https://example.com/pic.jpg' }}` — **MUST have explicit `width` and `height` styles defined**, otherwise it renders with dimensions `0x0`!
- **Pitfalls:** React Native's built-in `<Image>` component has poor caching, aggressive memory consumption, and no placeholder/blurhash support. Production apps use `@shopify/react-native-skia`, `expo-image`, or `react-native-fast-image` for disk caching and memory-mapped decoding.

---

### Q18: What is the difference between `dp` (density-independent pixels) on Android, `points` on iOS, and physical device pixels?
**Direct Answer:**  
- **React Native sizes are always in Points (iOS) or DP (Android), NEVER raw physical hardware pixels.**
- **Physical Pixel:** The actual microscopic light-emitting diode on the device screen (e.g., iPhone 15 Pro has $2556 \times 1179$ physical pixels).
- **Point / DP:** An abstract coordinate unit scaled by the device pixel density ratio (`PixelRatio.get()`):
  $$\text{Physical Pixels} = \text{Points} \times \text{PixelRatio}$$
- On a `@3x` Retina screen, specifying `width: 100` renders 300 physical pixels wide. To draw a true 1-pixel hairline border:
  `borderBottomWidth: 1 / PixelRatio.get()`.

---

### Q19: What is `PixelRatio` and `Dimensions` vs `useWindowDimensions`?
**Direct Answer:**  
- **`Dimensions.get('window')`:** A static API returning the screen dimensions at the moment of invocation. **Does NOT automatically update** when device orientation changes (portrait $\leftrightarrow$ landscape) or when folding phones unfold.
- **`useWindowDimensions`:** A reactive React hook that automatically triggers a component re-render whenever screen dimensions, font scale, or orientation change.
- **Rule:** Always use `useWindowDimensions` for responsive UI layouts.

---

### Q20: What is `Platform.select` and platform-specific file extensions?
**Direct Answer:**  
Two primary mechanisms to write platform-differentiated code:
1. **`Platform.select`:**
   ```javascript
   const styles = StyleSheet.create({
       container: {
           paddingTop: Platform.select({ ios: 20, android: 0 }),
       }
   });
   ```
2. **Platform-Specific Extensions:** Metro automatically resolves `.ios.js` vs `.android.js`:
   - `Header.ios.tsx` (uses Apple SF Symbols and iOS navigation bar)
   - `Header.android.tsx` (uses Material design toolbar)
   - In code: `import Header from './Header';` (Metro picks the correct file at bundle time with zero runtime overhead).

---

### Q21: What is the difference between Controlled and Uncontrolled `<TextInput>` in React Native?
**Direct Answer:**  
- **Controlled:** `value={text}` and `onChangeText={setText}`. Every keystroke sends an event to JS, updates React state, and sets the native input text.
  - **Flicker Pitfall:** On slow JS threads, typing quickly causes a race condition: the native text changes immediately, JS sends an asynchronous state update, and the input cursor jumps or text flickers!
- **Uncontrolled:** Uses `defaultValue` and reads values imperatively via `ref.current`. Prevents keystroke latency on heavy screens.

---

### Q22: How does the event dispatching mechanism work from native views back to JS?
**Direct Answer:**  
1. User touches the physical screen $\rightarrow$ Hardware touch event is captured by the native UI thread.
2. The native gesture recognizer targets the touched native view.
3. In the New Architecture (Fabric), the native event is directly dispatched into the C++ EventTarget via JSI.
4. C++ invokes the JavaScript event dispatcher, wrapping the event in a SyntheticEvent.
5. React bubbles the event up the virtual component tree to the appropriate handler (`onPress`).

---

### Q23: What is Fast Refresh and how does it preserve component state?
**Direct Answer:**  
Fast Refresh is React Native's hot-reloading developer experience:
- If you edit a file containing **only React components**, Fast Refresh updates the code and re-renders the component **while preserving local state (`useState`, `useRef`)**!
- If you edit a file with exports that are not React components (e.g. constant objects or utility functions), Fast Refresh re-evaluates that file and re-mounts dependent components.
- If you introduce a syntax or runtime error, an error overlay appears; fixing the typo automatically dismisses the overlay without losing app state.

---

### Q24: What are Concurrent Features in the context of React Native New Architecture?
**Direct Answer:**  
Fabric allows React 18 Concurrent features to control mobile rendering:
- **`useTransition`:** Marks slow screen transitions or heavy list filtering as interruptible. If a user taps "Back" while a heavy screen is transitioning, React abandons the transition immediately to maintain UI responsiveness.
- **`<Suspense>`:** Allows native subtrees to suspend while waiting for async data, rendering a native fallback without blocking user interaction in sibling views.

---

### Q25: How do you migrate an existing React Native app from the Old Bridge to the New Architecture?
**Direct Answer:**  
1. **Audit Dependencies:** Check that all third-party native libraries support Fabric and TurboModules (via `react-native-community/directory`).
2. **Enable Hermes:** Set `hermesEnabled: true` in `android/app/build.gradle` and `Podfile`.
3. **Flip the Flag:**
   - **Android:** Set `newArchEnabled=true` in `gradle.properties`.
   - **iOS:** Run `RCT_NEW_ARCH_ENABLED=1 bundle exec pod install`.
4. **Replace Incompatible Native Code:** Convert legacy Native Modules using Codegen spec files (`Native<Name>Spec.ts`).
