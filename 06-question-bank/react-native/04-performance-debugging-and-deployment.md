# 📱 Performance Optimization, Debugging, Security & CI/CD (Q76 – Q100)

---

### Q76: How do you profile and diagnose React Native performance?
**Direct Answer:**  
1. **React Native Performance Monitor:** Built-in in-app overlay showing UI thread and JS thread FPS counters.
2. **React DevTools Profiler:** Pinpoints un-memoized component re-renders, hook recalculations, and render durations.
3. **Hermes Sampling Profiler:** Records CPU traces of the JavaScript engine, viewable directly in Chrome DevTools or Speedscope to detect heavy JS functions.
4. **Platform Native Profilers:** Xcode Instruments (Time Profiler, Leaks) and Android Studio Profiler (CPU, Memory, Energy).

---

### Q77: How do you diagnose and fix JavaScript thread frame drops vs UI thread frame drops?
**Direct Answer:**  
- **If JS Thread FPS drops (<60) while UI Thread is 60:**
  - The UI is smooth, but inputs/taps are unresponsive.
  - Fix: Offload heavy array mapping/sorting to Web Workers (`react-native-threads`) or C++ via JSI; memoize selectors; break up large JSON parsing.
- **If UI Thread FPS drops (<60):**
  - Scrolling and animations are choppy.
  - Fix: Enable `useNativeDriver: true` or switch to Reanimated; optimize images; reduce view hierarchy depth; enable view flattening; use `FlashList`.

---

### Q78: What causes Memory Leaks in React Native and how do you profile them?
**Direct Answer:**  
**Common Causes:**
1. Event listeners (`AppState`, `NetInfo`, `Dimensions`) not removed in `useEffect` cleanup.
2. Dangling timers (`setInterval`) retaining component closures.
3. Retaining large bitmap images in memory without releasing references.
4. Retaining detached native views in custom native modules.
**Diagnosis:** Open Xcode $\rightarrow$ **Instruments** $\rightarrow$ **Leaks / Allocations**, record app navigation (push and pop a screen 20 times), and inspect if memory persistently climbs and objects remain un-deallocated.

---

### Q79: How do you optimize high-resolution remote images (`expo-image` / `FastImage`)?
**Direct Answer:**  
Never render raw 4K camera photos directly in `<Image>`.
- Use **`expo-image`** or **`react-native-fast-image`**:
  - Aggressive disk and memory LRU caching.
  - Direct hardware decoding on background threads.
  - Downsamples images at decode time to the exact display dimensions, avoiding loading 20MB bitmaps into GPU VRAM.
  - Built-in Progressive loading and Blurhash placeholders.

---

### Q80: What are Over-The-Air (OTA) updates and how do CodePush / EAS Update work?
**Direct Answer:**  
OTA updates allow developers to publish JavaScript bundle and asset updates directly to user devices without going through App Store or Google Play review:
1. Developer bundles JS and assets (`index.bundle`).
2. Bundle is uploaded to CodePush / EAS Update CDN.
3. On app launch, the native client checks the CDN for a new release manifest.
4. Downloads the new bundle in the background and applies it on next restart or reload.
*Note: Cannot update native code (`Podfile`, `build.gradle`, native modules).*

---

### Q81: What are the store guidelines and legal risks regarding OTA code updates?
**Direct Answer:**  
- **Apple Guideline 3.3.2:** Explicitly allows downloading interpreted code (JavaScript via WebKit/Hermes), **provided the update does not materially change the primary purpose of the application**.
- **Violation Risks:** If an e-commerce app pushes an OTA update transforming itself into a gambling or cryptocurrency app, Apple and Google will permanently terminate the developer account.
- Any change requiring new native permissions or SDKs **must** go through formal app store binary review.

---

### Q82: How do you reduce Android APK / AAB bundle size?
**Direct Answer:**  
1. **Enable R8 / ProGuard:** Shrinks, obfuscates, and strips unused Java bytecode (`minifyEnabled true`, `shrinkResources true`).
2. **Hermes Bytecode:** Hermes pre-compilation removes JavaScript parser code and compresses bundle size.
3. **Android App Bundles (AAB) & ABI Splitting:** Generates architecture-specific slices (`arm64-v8a`, `armeabi-v7a`, `x86_64`) so users download only the binaries matching their hardware CPU.
4. **Convert PNGs to WebP:** WebP images are 30–50% smaller with identical visual quality.

---

### Q83: How do you optimize iOS IPA bundle size?
**Direct Answer:**  
1. **App Thinning & Asset Catalogs:** Store all images in `.xcassets` so Apple generates device-specific asset slices.
2. **Dead Code Stripping:** Ensure `Dead Code Stripping` is set to `YES` in Xcode Release build settings.
3. **Remove Unused Frameworks & Architectures:** Exclude simulator slices (`x86_64`) from production archive builds.
4. **Optimize Custom Fonts:** Strip unused glyphs (subsetting) from large custom font files (TTF/OTF).

---

### Q84: What is the difference between Expo Managed Workflow, Bare Workflow, and Expo Prebuild?
**Direct Answer:**  
- **Managed Workflow:** You write only JS/TS. Expo manages all native project folders behind the scenes. Zero native code touch, but historically limited in custom native modules.
- **Bare Workflow:** A traditional React Native project with exposed `ios/` and `android/` directories. Maximum native control, manual maintenance of Gradle and CocoaPods.
- **Expo Prebuild (Config Plugins - Modern Standard):** The best of both worlds. Native folders are generated deterministically from `app.json` config plugins at build time (`npx expo prebuild`), enabling any custom native module while keeping the project clean and upgradable.

---

### Q85: How do you implement SSL Pinning in React Native?
**Direct Answer:**  
SSL Pinning prevents Man-in-the-Middle (MITM) attacks by validating that the server's public key certificate matches a hardcoded public key hash embedded within the mobile app binary:
- Libraries: `react-native-ssl-pinning` or native network layer interceptors (OkHttp on Android, `URLSession` on iOS).
- When an attacker attempts to route traffic through a proxy (Charles, Burp Suite), the handshake fails immediately and the request is aborted.

---

### Q86: How do you securely store sensitive tokens and secrets?
**Direct Answer:**  
Use **`react-native-keychain`** or **`expo-secure-store`**:
- **iOS:** Stores secrets inside the hardware **iOS Keychain** (encrypted with hardware-backed Secure Enclave keys).
- **Android:** Stores secrets in encrypted `SharedPreferences` backed by the **Android KeyStore**.
- Never store auth tokens, private keys, or passwords in plaintext `AsyncStorage` or Redux state!

---

### Q87: Why should you NEVER store API keys or secrets in `.env` files in React Native?
**Direct Answer:**  
- Mobile client code runs in an untrusted environment on the user's physical device.
- All `.env` variables injected via Babel or Webpack compile directly into plaintext strings inside the JavaScript bundle (`index.android.bundle`).
- Anyone can extract your `.env` secrets in 10 seconds by unzipping the APK/IPA and running `strings index.android.bundle | grep API_KEY`.
- **Rule:** Client apps should hold only public client IDs. Secrets belong exclusively on backend servers.

---

### Q88: How do you detect Jailbroken (iOS) or Rooted (Android) devices?
**Direct Answer:**  
Using libraries like `react-native-jail-monkey` or `free-root`:
- Checks for known root/jailbreak binaries (`/system/bin/su`, Cydia, substrate), test keys, mock locations, and hooked native debuggers.
- In high-security banking or healthcare applications, detection triggers an immediate session kill or warns the user of security vulnerabilities.

---

### Q89: How do you protect React Native JavaScript bundles against reverse engineering?
**Direct Answer:**  
1. **Enable Hermes:** Hermes compiles source code into binary bytecode, making reverse engineering significantly harder than reading plain JS text.
2. **Jscrambler / JavaScript Obfuscator:** Renames variables, scrambles control flow, and injects tamper-detection traps into the JS bundle.
3. **Native ProGuard/R8 Obfuscation:** Obfuscates Java/Kotlin class names and symbols in Android binaries.

---

### Q90: How do you handle crash reporting using Sentry in React Native?
**Direct Answer:**  
Using `@sentry/react-native`:
1. **Dual-Layer Crash Reporting:** Catches unhandled JavaScript exceptions AND native crashes (Objective-C/Swift EXC_BAD_ACCESS and Android JNI SIGSEGV).
2. **Source Maps & Symbolication:** Automatically uploads JS source maps and native dSYM (iOS) / ProGuard mapping files (Android) to Sentry during CI builds so stack traces point to exact TypeScript lines.
3. **Breadcrumbs:** Tracks user navigation, network calls, and console logs leading up to the crash.

---

### Q91: How do you implement Automated E2E Testing using Detox?
**Direct Answer:**  
Detox is a gray-box end-to-end testing framework for mobile apps:
- **Gray-Box Synchronization:** Automatically monitors the app's internal threads, network requests, and animation queues. Tests execute an action **only when the app is completely idle**, eliminating arbitrary `sleep()` statements.
- **Example Test:**
  ```javascript
  it('should login successfully', async () => {
      await element(by.id('email_input')).typeText('test@example.com');
      await element(by.id('password_input')).typeText('secret123');
      await element(by.id('login_button')).tap();
      await expect(element(by.text('Welcome'))).toBeVisible();
  });
  ```

---

### Q92: How do you write unit tests in React Native using React Native Testing Library (RNTL)?
**Direct Answer:**  
```javascript
import { render, fireEvent, screen } from '@testing-library/react-native';
import Counter from './Counter';

test('increments counter on press', () => {
    render(<Counter />);
    const button = screen.getByText('Increment');
    fireEvent.press(button);
    expect(screen.getByText('Count: 1')).toBeTruthy();
});
```
Focuses on user-observable behavior (`getByText`, `getByRole`) rather than querying internal component state or implementation details.

---

### Q93: How do you mock Native Modules in Jest?
**Direct Answer:**  
Provide a mock definition in `jest.setup.js`:
```javascript
jest.mock('react-native/Libraries/EventEmitter/NativeEventEmitter');

jest.mock('@react-native-async-storage/async-storage', () =>
    require('@react-native-async-storage/async-storage/jest/async-storage-mock')
);

jest.mock('react-native-reanimated', () => {
    const Reanimated = require('react-native-reanimated/mock');
    Reanimated.default.call = () => {};
    return Reanimated;
});
```

---

### Q94: What is Fastlane and how do you automate builds and store deployment?
**Direct Answer:**  
Fastlane is an open-source ruby-based automation tool for mobile apps:
- **`Fastfile` Lanes:**
  - `lane :beta`: Increments build number $\rightarrow$ Codesigns app $\rightarrow$ Builds IPA/AAB $\rightarrow$ Uploads to TestFlight & Google Play Internal Testing.
  - `lane :release`: Uploads final production builds and release metadata to App Store Connect and Google Play Console.
- Integrates seamlessly into GitHub Actions, GitLab CI, and Bitrise.

---

### Q95: How does iOS Code Signing work (Certificates, Provisioning Profiles)?
**Direct Answer:**  
1. **Development / Distribution Certificate:** Cryptographic public/private key pair issued by Apple certifying the developer's identity.
2. **App ID:** Explicit identifier matching `CFBundleIdentifier` (e.g. `com.company.app`).
3. **Provisioning Profile:** A cryptographically signed document linking the Certificate, App ID, and authorized device UDIDs (or App Store distribution entitlement).
4. Fastlane `match` syncs certificates and profiles across the engineering team via an encrypted Git repo.

---

### Q96: How does Android App Signing work (Upload Keys vs Play App Signing)?
**Direct Answer:**  
- **Upload Key:** Developer generates a Java Keystore (`upload-keystore.jks`) to sign the Android App Bundle (AAB) before uploading to Google Play.
- **Google Play App Signing:** Google manages the actual public app signing key used to sign the APKs delivered to end users. If a developer loses their upload key, Google can reset it, eliminating catastrophic app store re-publishing locks.

---

### Q97: What is Monorepo architecture for React Native (Turborepo / Nx)?
**Direct Answer:**  
A single repository containing both Web (Next.js/React) and Mobile (React Native/Expo) applications:
- **Shared Packages:** Shared TypeScript types, utility functions, state management logic (Zustand/RTK), and API client layers (TanStack Query).
- **Metro Monorepo Config:** Metro must be configured (`watchFolders`, `extraNodeModules`) to resolve hoisted packages in root `node_modules` without symbol duplication.

---

### Q98: What is React Native for Web (`react-native-web`)?
**Direct Answer:**  
A library created by Nicolas Gallagher that translates React Native components and styling primitives into standard HTML5 and CSS:
- `<View>` compiles to `<div>`, `<Text>` compiles to `<span>`, `<Image>` compiles to `<img>`.
- Allows teams to write **a single UI codebase** that compiles to native iOS, native Android, and standard web browsers (used at Twitter/X).

---

### Q99: What is Micro-Apps / Super-App architecture in React Native?
**Direct Answer:**  
Decomposing a large mobile app into independently developed and bundled mini-applications loaded at runtime:
- **Repack / Webpack:** Uses Webpack and Module Federation to dynamically download and mount independent JavaScript bundles on-demand over the network without rebuilding the host shell container app.
- Popular in large enterprises (Grab, WeChat, GoTo) with dozens of independent product teams.

---

### Q100: How do you structure an enterprise-grade React Native codebase?
**Direct Answer:**  
1. **Feature-First Architecture:**
   ```text
   src/
   ├── app/ (Providers, Navigation, Theme)
   ├── features/
   │   ├── auth/ (components, hooks, api, types)
   │   └── checkout/
   ├── components/ (Atomic design: Button, Typography, Input)
   ├── services/ (MMKV storage, API client, Analytics)
   └── assets/ (Images, SVGs, Fonts)
   ```
2. **Strict Public Boundaries:** Only export clean interfaces from each feature directory via `index.ts`.
3. **Automated Quality Pipelines:** TypeScript strict mode, ESLint, Prettier, Husky pre-commit hooks, Jest unit tests, and Detox E2E tests run on every pull request.
