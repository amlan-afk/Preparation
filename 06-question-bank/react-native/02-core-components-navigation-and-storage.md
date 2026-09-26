# 📱 Components, Navigation, Storage & Native APIs (Q26 – Q50)

---

### Q26: How does `FlatList` work internally and why is it preferred over `ScrollView`?
**Direct Answer:**  
- **`ScrollView`:** Renders **all child components simultaneously in memory**, even if the list has 10,000 items. This causes massive memory spikes, long initial render times, and eventual app crashes.
- **`FlatList` (Virtualization):**
  - Renders only the items currently visible within the visible "window" (plus a configurable render buffer).
  - Replaces off-screen items with blank empty spacer views when scrolled away, keeping memory consumption bounded and constant regardless of list size.

---

### Q27: What are the key performance props for `FlatList`?
**Direct Answer:**  
1. **`getItemLayout`:**
   ```javascript
   getItemLayout={(data, index) => ({ length: ITEM_HEIGHT, offset: ITEM_HEIGHT * index, index })}
   ```
   **Crucial Optimization:** Skips dynamic asynchronous measurement of item heights, enabling instantaneous scrolling to any index!
2. **`initialNumToRender`:** How many items to render on initial mount (set to exactly fill the screen, e.g. 8-10).
3. **`maxToRenderPerBatch`:** Number of items rendered per scroll batch.
4. **`windowSize`:** Multiplier determining how many screens worth of content are kept rendered (default is 21; lowering to 5-7 cuts RAM usage drastically).
5. **`removeClippedSubviews={true}`:** Detaches off-screen native views from the native hierarchy to free GPU memory.

---

### Q28: What is `FlashList` (Shopify) and how does cell recycling outperform `FlatList`?
**Direct Answer:**  
- **The Problem with `FlatList`:** As you scroll fast, `FlatList` creates and destroys native views continuously, causing garbage collection pauses and blank white areas.
- **`FlashList` Cell Recycling:**
  - Instead of destroying views that exit the screen, `FlashList` **recycles existing native views** and merely updates their data props (similar to Android's `RecyclerView` and iOS's `UICollectionView`).
  - Achieves **5x to 10x faster performance** and completely eliminates blank spaces during rapid flick scrolling.

---

### Q29: When should you use `SectionList` vs `FlatList`?
**Direct Answer:**  
- Use **`FlatList`** for single, flat, homogeneous or heterogeneous lists of items.
- Use **`SectionList`** when data is naturally grouped into categorized sections with **sticky section headers** (e.g. Contacts grouped alphabetically A-Z, or Chat history grouped by Date):
```jsx
<SectionList
    sections={[{ title: 'A', data: ['Alice', 'Adam'] }, { title: 'B', data: ['Bob'] }]}
    renderSectionHeader={({ section: { title } }) => <Header title={title} />}
    renderItem={({ item }) => <Item name={item} />}
    stickySectionHeadersEnabled={true}
/>
```

---

### Q30: What is React Navigation and how does it differ from Native Navigation?
**Direct Answer:**  
- **React Navigation (`@react-navigation`):** The official JavaScript-based navigation solution. Renders screens and transitions in JavaScript/C++, highly customizable, customizable gestures, works identically across platforms.
- **Native Navigation (`react-native-navigation` by Wix):** Wraps underlying native navigation controllers (`UINavigationController` on iOS, `FragmentTransaction` on Android). Pure native performance, but complex setup and less flexible for custom cross-platform animations.

---

### Q31: What are the differences between `@react-navigation/stack` and `@react-navigation/native-stack`?
**Direct Answer:**  
| Feature | `@react-navigation/stack` (JS Stack) | `@react-navigation/native-stack` (Recommended) |
|---|---|---|
| **Underlying Engine** | JavaScript + Reanimated + Gesture Handler | Native iOS `UINavigationController` & Android `Fragment` |
| **Performance** | Good, but consumes JS thread during transitions | **Near-zero JS thread usage**; 100% native 120 FPS transitions |
| **Native Integration** | Custom simulated headers and gestures | Native iOS large titles, native search bar, native swipe-back |
| **Customization** | Unlimited custom JS animations | Limited to native platform transition primitives |

---

### Q32: How do you handle Deep Linking and Universal Links in React Navigation?
**Direct Answer:**  
1. **Configure Schemes & Domains:**
   - URL Schemes (e.g. `myapp://profile/123`) in `Info.plist` and `AndroidManifest.xml`.
   - Universal Links (iOS `apple-app-site-association`) and Android App Links (`assetlinks.json`).
2. **React Navigation Linking Configuration:**
   ```javascript
   const linking = {
       prefixes: ['myapp://', 'https://myapp.com'],
       config: {
           screens: {
               Home: '',
               Profile: 'profile/:userId',
               Details: 'details/:id'
           }
       }
   };
   <NavigationContainer linking={linking}>...</NavigationContainer>
   ```

---

### Q33: How do you prevent memory leaks when navigating between deeply nested navigation stacks?
**Direct Answer:**  
1. **Unmount Inactive Screens:** Use `unmountOnBlur: true` for heavy tabs or memory-intensive camera/map screens.
2. **Reset Navigation State:** When logging out or finishing multi-step checkout funnels, use `navigation.reset()` instead of pushing screens to destroy previous screens from the stack:
   ```javascript
   navigation.reset({ index: 0, routes: [{ name: 'Login' }] });
   ```
3. **Clean Up Listeners:** Remove event listeners and timers in `navigation.addListener('beforeRemove', ...)` or within `useEffect` cleanup blocks.

---

### Q34: What is `AsyncStorage` and what are its performance limitations?
**Direct Answer:**  
`AsyncStorage` is an unencrypted, asynchronous key-value storage system:
- **Limitations:**
  1. Relies on the asynchronous JSON bridge; reading/writing data requires serialization and multi-thread switching.
  2. Slow for frequent reads or large payloads (>2MB).
  3. No synchronous access (cannot initialize state synchronously on app startup, causing layout flashes).
  4. Stores data as plaintext (insecure for auth tokens without encryption).

---

### Q35: What is `react-native-mmkv` and why is it up to 30x faster than `AsyncStorage`?
**Direct Answer:**  
MMKV is an open-source mobile key-value storage framework developed by Tencent:
- **Direct JSI Integration:** Uses JSI to bind directly to C++ memory-mapped files (`mmap`).
- **Zero Bridge Overhead:** Reads and writes execute **synchronously** in less than 0.05ms without JSON stringification.
- **Encryption:** Built-in AES-CFB-128 encryption.
- **Multi-Process Concurrency:** Multiple app processes and extensions (widgets) can safely read/write concurrently.

---

### Q36: When should you choose SQLite, WatermelonDB, or Realm for offline-first databases?
**Direct Answer:**  
- **SQLite (`op-sqlite` / `expo-sqlite`):** Best for standard relational data, SQL queries, migrations, and medium datasets (<100K rows).
- **WatermelonDB:** Optimized for large datasets (10K–1M+ records) in React Native. Uses lazy loading (loads records into memory only when rendered) and observes changes via RxJS, maintaining high FPS.
- **Realm:** Object-oriented NoSQL database with automatic live-object synchronization and built-in offline cloud sync.

---

### Q37: How does React Native handle App State transitions (`AppState`)?
**Direct Answer:**  
`AppState` tracks whether the app is in the foreground, background, or inactive:
- **`active`:** App is running in the foreground and responding to user touches.
- **`background`:** App is minimized, running in the background, or user is on the home screen.
- **`inactive`:** Transition state occurring during phone calls, iOS App Switcher mode, or notification center pull-downs.
```javascript
useEffect(() => {
    const subscription = AppState.addEventListener('change', nextAppState => {
        if (nextAppState === 'active') {
            refreshSession();
        }
    });
    return () => subscription.remove();
}, []);
```

---

### Q38: How do you handle Safe Area boundaries using `react-native-safe-area-context`?
**Direct Answer:**  
Safe areas prevent content from being clipped by device notches, camera holes, rounded display corners, and home indicator bars:
```jsx
import { SafeAreaProvider, useSafeAreaInsets } from 'react-native-safe-area-context';

function Screen() {
    const insets = useSafeAreaInsets();
    return (
        <View style={{ paddingTop: insets.top, paddingBottom: insets.bottom }}>
            <Text>Content safely positioned</Text>
        </View>
    );
}
```
- **Why it beats legacy `<SafeAreaView>`:** Works with dynamic insets, supports asynchronous measurements, and provides exact pixel padding values for custom header/footer math.

---

### Q39: How do you handle keyboard occlusion in React Native?
**Direct Answer:**  
When an input is focused, the soft keyboard covers the bottom half of the screen:
1. **`<KeyboardAvoidingView>`:** Built-in component that shifts, pads, or resizes the view based on `behavior={Platform.OS === 'ios' ? 'padding' : 'height'}`.
2. **`react-native-keyboard-controller`:** High-performance modern alternative that synchronizes layout height with interactive keyboard dragging at 60/120 FPS.
3. Dismissing keyboard on tap outside:
   ```jsx
   <TouchableWithoutFeedback onPress={Keyboard.dismiss}>
       <View>...</View>
   </TouchableWithoutFeedback>
   ```

---

### Q40: How do you manage runtime device permissions on iOS and Android?
**Direct Answer:**  
Using `react-native-permissions`:
1. **Declaration:** Declare permission usage strings in iOS `Info.plist` (`NSCameraUsageDescription`) and Android `AndroidManifest.xml` (`<uses-permission android:name="android.permission.CAMERA" />`).
2. **Check & Request Cycle:**
   ```javascript
   import { check, request, PERMISSIONS, RESULTS } from 'react-native-permissions';

   const status = await check(PERMISSIONS.IOS.CAMERA);
   if (status === RESULTS.DENIED) {
       const result = await request(PERMISSIONS.IOS.CAMERA);
       if (result === RESULTS.GRANTED) openCamera();
   } else if (status === RESULTS.BLOCKED) {
       openSettings(); // Direct user to OS settings
   }
   ```

---

### Q41: How do you access device camera and photo libraries in React Native?
**Direct Answer:**  
- **Photo Selection:** `react-native-image-picker` or `expo-image-picker` for launching the native gallery picker or basic camera capture.
- **High-Performance Real-Time Camera:** `react-native-vision-camera`:
  - Direct 60 FPS camera frame access.
  - Supports custom C++ Frame Processors using Worklets for real-time ML (barcode scanning, face detection, OCR) without passing frames over the JS bridge.

---

### Q42: How do you implement background geolocation tracking and geofencing?
**Direct Answer:**  
Using libraries like `react-native-background-geolocation`:
- Standard HTML5 `navigator.geolocation` stops executing once the app enters the background or OS suspends the JS thread.
- Background tracking requires native Android Foreground Services (with persistent notification) and iOS Significant Location Change / Background Location entitlements (`location` in `UIBackgroundModes`).
- Coordinates are queued in local SQLite and synced when connectivity is established.

---

### Q43: How do you implement Push Notifications (FCM / APNs) using Notifee or Firebase?
**Direct Answer:**  
1. **Device Registration:** Request authorization $\rightarrow$ Retrieve unique APNs / FCM token $\rightarrow$ Send token to backend server.
2. **Notification Handlers:**
   - **Foreground Notifications:** Intercepted by `@notifee/react-native` or `@react-native-firebase/messaging` and displayed as custom local heads-up banners.
   - **Background / Killed State Notifications:** Processed by a background task registered via `setBackgroundMessageHandler`.
3. **Notification Tap Navigation:** Read initial notification payload via `getInitialNotification()` to deep-link to the target screen upon app boot.

---

### Q44: How do you implement Biometric Authentication (FaceID / Fingerprint)?
**Direct Answer:**  
Using `react-native-biometrics`:
```javascript
import ReactNativeBiometrics, { BiometryTypes } from 'react-native-biometrics';

const rnBiometrics = new ReactNativeBiometrics();
const { biometryType } = await rnBiometrics.isSensorAvailable();

if (biometryType === BiometryTypes.FaceID || biometryType === BiometryTypes.Biometrics) {
    const { success } = await rnBiometrics.simplePrompt({ promptMessage: 'Confirm identity' });
    if (success) unlockApp();
}
```
For cryptographic security, use `createKeys()` to store a private key inside the iOS Secure Enclave / Android KeyStore, signed during biometric confirmation.

---

### Q45: How does Dark Mode / System Theme synchronization work in React Native?
**Direct Answer:**  
1. **`useColorScheme()` Hook:** Returns `'light'`, `'dark'`, or `null`. Updates reactively when the user changes OS theme in device settings.
2. **Theming Architecture:**
   ```javascript
   const theme = useColorScheme() === 'dark' ? darkTheme : lightTheme;
   return <View style={{ backgroundColor: theme.background }} />;
   ```
3. Set `userInterfaceStyle: "automatic"` in `app.json` (Expo) or `UIViewControllerBasedStatusBarAppearance` in iOS `Info.plist`.

---

### Q46: How do you handle multi-language localization (i18n) and RTL layouts?
**Direct Answer:**  
1. **Localization:** Use `i18next` and `react-i18next` with language detection from `react-native-localize`.
2. **RTL (Right-to-Left for Arabic, Hebrew):**
   - Yoga supports RTL automatically when using directional-agnostic Flexbox properties:
     - Use `marginHorizontal`, `marginStart`, and `marginEnd` instead of `marginLeft` and `marginRight`.
   - Force RTL testing via `I18nManager.forceRTL(true)`.

---

### Q47: How do you manage network connectivity changes using `@react-native-community/netinfo`?
**Direct Answer:**  
```javascript
import NetInfo from '@react-native-community/netinfo';

useEffect(() => {
    const unsubscribe = NetInfo.addEventListener(state => {
        console.log('Connection type:', state.type);
        console.log('Is connected?', state.isConnected);
        console.log('Is internet reachable?', state.isInternetReachable);
    });
    return () => unsubscribe();
}, []);
```
Use `state.isInternetReachable` (not just `isConnected`) to verify the device can truly reach the public web behind captive WiFi portals.

---

### Q48: What is the difference between Foreground Services and Background Tasks on Android?
**Direct Answer:**  
- **Android Background Tasks (WorkManager):** Scheduled tasks (periodic sync) that run opportunistically when the OS allows, heavily restricted by Android Battery Optimization and Doze Mode.
- **Android Foreground Service:** A long-running native service that **displays a persistent, non-dismissible notification in the status bar** (e.g. music playback, active navigation, call active). Android guarantees it will not be killed by the OS under low-memory conditions.

---

### Q49: How do you handle In-App Purchases (IAP) and Subscriptions in React Native?
**Direct Answer:**  
Using RevenueCat (`react-native-purchases`) or `react-native-iap`:
1. Products and subscriptions configured in Apple App Store Connect and Google Play Console.
2. App initializes SDK on mount and fetches offerings.
3. User triggers purchase: Native platform checkout modal opens.
4. **Server-Side Receipt Validation:** Essential! Send the purchase token to a secure backend or RevenueCat webhook to validate against Apple/Google servers before unlocking premium entitlements.

---

### Q50: How do you build an offline-first sync engine in React Native?
**Direct Answer:**  
1. **Local Persistent Cache:** Store all mutations and data locally in MMKV / WatermelonDB.
2. **Optimistic UI:** Update local UI immediately upon user action.
3. **Mutation Queue:** Append network mutations to an offline action queue if disconnected.
4. **Reconnection Sync:** When `@react-native-community/netinfo` detects online status, replay queued mutations sequentially, handling conflict resolution via timestamp or server-wins strategies.
