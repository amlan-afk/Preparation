# 📱 Animations, Gestures, Native Modules & JSI (Q51 – Q75)

---

### Q51: Why is the built-in `Animated` API often insufficient for complex gestures?
**Direct Answer:**  
- **Bridge Bottlenecks:** Built-in `Animated` calculates frame values either in JavaScript (causing frame drops when the JS thread is busy) or offloads limited transforms to native driver.
- **Gesture Decoupling:** Continuous gestures (dragging, swiping) generate high-frequency touch events on the native UI thread. Passing each touch event over to JS to calculate animation values introduces latency, causing gestures to lag behind the user's finger.

---

### Q52: What does `useNativeDriver: true` do, and what properties can and CANNOT be offloaded?
**Direct Answer:**  
- **How it works:** Serializes the entire animation configuration and sends it across to the native thread once before the animation starts. The native UI thread updates views frame-by-frame on the CADisplayLink / Choreographer without touching the JS thread.
- **Supported Properties (Non-layout):**
  - `transform` (`translateX`, `translateY`, `scale`, `rotate`)
  - `opacity`
- **UNSUPPORTED Properties (Triggers error with native driver):**
  - `width`, `height`, `margin`, `padding`, `top`, `left`, `backgroundColor`, `borderRadius`. (These require Yoga layout recalculation, which the legacy native driver cannot perform).

---

### Q53: What is React Native Reanimated and what is a "Worklet"?
**Direct Answer:**  
Reanimated (v2/v3) is a modern animation library that runs JavaScript code directly on the native UI thread:
- **Worklet:** A tiny JavaScript function tagged with the `'worklet';` directive that is compiled by Babel to run inside a separate JavaScript runtime executing directly on the **UI Thread**:
  ```javascript
  function myWorklet(x) {
      'worklet';
      return x * 2;
  }
  ```
- Eliminates bridge hops completely, delivering guaranteed 60/120 FPS animations regardless of heavy background JavaScript activity.

---

### Q54: How do `useSharedValue` and `useAnimatedStyle` work in Reanimated?
**Direct Answer:**  
- **`useSharedValue`:** A reactive mutable container that lives on the UI thread. Updating `sharedValue.value = 100` does **NOT trigger a React component re-render**:
  ```javascript
  const offset = useSharedValue(0);
  ```
- **`useAnimatedStyle`:** Connects shared values to component styles, executing the worklet synchronously on the UI thread to update native view properties directly:
  ```javascript
  const animatedStyles = useAnimatedStyle(() => ({
      transform: [{ translateX: offset.value }],
  }));
  return <Animated.View style={[styles.box, animatedStyles]} />;
  ```

---

### Q55: What is the difference between `withTiming`, `withSpring`, and `withDecay` in Reanimated?
**Direct Answer:**  
1. **`withTiming(toValue, { duration, easing })`:** Curve-based animation transitioning over a fixed duration (ease-in, ease-out, linear).
2. **`withSpring(toValue, { damping, stiffness, mass })`:** Physics-based simulation modeling a real-world spring. No fixed duration; feels organic, responsive, and interruptible by user touches.
3. **`withDecay({ velocity, deceleration })`:** Physics-based inertial deceleration simulation (used when a user flings a scroll view or draggable card, gliding smoothly to a stop).

---

### Q56: What is `react-native-gesture-handler` and why is it superior to `PanResponder`?
**Direct Answer:**  
- **`PanResponder`:** Uses React Native's JS touch responder system. Every touch event journeys from native $\rightarrow$ bridge $\rightarrow$ JS thread to decide gesture victory, causing noticeable latency.
- **`react-native-gesture-handler`:** Uses native platform gesture recognizers (`UIGestureRecognizer` on iOS, Android native gesture detectors).
  - Gesture recognition and cancellation happen **entirely on the native UI thread**.
  - Pairs seamlessly with Reanimated worklets for 120 FPS fluid pan/pinch/drag interactions.

---

### Q57: How do you combine gestures using `GestureDetector` in Reanimated v3?
**Direct Answer:**  
Using the modern `Gesture` API:
```javascript
const pan = Gesture.Pan().onChange((e) => {
    translationX.value += e.changeX;
});
const tap = Gesture.Tap().onEnd(() => {
    scale.value = withSpring(1.2);
});

// Simultaneous: Both detect concurrently
const gesture = Gesture.Simultaneous(pan, tap);

// Exclusive: Tap fires only if Pan fails
const gestureExclusive = Gesture.Exclusive(pan, tap);

return <GestureDetector gesture={gesture}><Animated.View /></GestureDetector>;
```

---

### Q58: How do you build a 60 FPS interactive Swipeable Bottom Sheet?
**Direct Answer:**  
1. Manage position using a `useSharedValue(COLLAPSED_Y)`.
2. Attach a `Gesture.Pan()` handler updating `translateY.value` on the UI thread.
3. On gesture release (`onEnd`), check velocity:
   - If flung upward (`e.velocityY < -500`), animate to `withSpring(EXPANDED_Y)`.
   - If flung downward (`e.velocityY > 500`), animate to `withSpring(COLLAPSED_Y)`.
4. Apply animated styles to an `<Animated.View style={animatedStyle}>`.

---

### Q59: What are Layout Animations in Reanimated?
**Direct Answer:**  
Declarative animations for components being added, removed, or changing position in the layout, without writing manual state transitions:
```jsx
<Animated.View
    entering={FadeInDown.duration(400)}
    exiting={FadeOutUp.duration(300)}
    layout={Layout.springify()}
/>
```
Automatically animates list reordering, item deletions, and insertions smoothly on the UI thread.

---

### Q60: What is React Native Skia (`@shopify/react-native-skia`)?
**Direct Answer:**  
A high-performance 2D vector graphics library bringing Google's Skia rendering engine (the same engine powering Chrome and Flutter) directly into React Native:
- Renders directly to GPU surfaces via C++.
- Enables custom paths, shaders, complex gradients, SVG filters, canvas charts, and particle animations running at 120 FPS.

---

### Q61: How do you create a legacy Native Module for iOS (Objective-C / Swift)?
**Direct Answer:**  
1. **Objective-C / Swift:** Implement `RCTBridgeModule` protocol:
   ```objc
   // CalendarModule.m
   #import <React/RCTBridgeModule.h>
   @interface RCT_EXTERN_MODULE(CalendarModule, NSObject)
   RCT_EXTERN_METHOD(createEvent:(NSString *)name location:(NSString *)location)
   @end
   ```
2. **Swift Implementation:**
   ```swift
   @objc(CalendarModule)
   class CalendarModule: NSObject {
       @objc func createEvent(_ name: String, location: String) {
           NSLog("Event: \(name) at \(location)")
       }
   }
   ```
3. In JS: `NativeModules.CalendarModule.createEvent('Meeting', 'Office');`.

---

### Q62: How do you create a legacy Native Module for Android (Java / Kotlin)?
**Direct Answer:**  
1. Extend `ReactContextBaseJavaModule`:
   ```kotlin
   class DeviceModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {
       override fun getName() = "DeviceModule"

       @ReactMethod
       fun getBatteryLevel(promise: Promise) {
           // Read battery from Android BatteryManager
           promise.resolve(85)
       }
   }
   ```
2. Register in a `ReactPackage` and add to `MainApplication.kt`.
3. In JS: `const level = await NativeModules.DeviceModule.getBatteryLevel();`.

---

### Q63: How do you write a TurboModule with C++ / TypeScript Codegen?
**Direct Answer:**  
1. **Define Specification (`NativeCalculator.ts`):**
   ```typescript
   import { TurboModule, TurboModuleRegistry } from 'react-native';
   export interface Spec extends TurboModule {
       add(a: number, b: number): number;
   }
   export default TurboModuleRegistry.getEnforcing<Spec>('NativeCalculator');
   ```
2. **Run Codegen:** Generates C++ header `NativeCalculatorSpec.h`.
3. **Implement in C++ / Obj-C / Kotlin:** Implement the pure virtual C++ class or platform interface.
4. Calls execute synchronously through JSI without bridge serialization!

---

### Q64: How do you write a Fabric Native Component?
**Direct Answer:**  
1. **Define Component Spec (`CustomButtonNativeComponent.ts`):**
   ```typescript
   import codegenNativeComponent from 'react-native/Libraries/Utilities/codegenNativeComponent';
   import type { ViewProps } from 'react-native';

   interface NativeProps extends ViewProps {
       color?: string;
   }
   export default codegenNativeComponent<NativeProps>('CustomButton');
   ```
2. Codegen generates C++ ShadowNode descriptors.
3. Native implementation subclasses `RCTViewComponentView` (iOS) or `SimpleViewManager` (Android), receiving strongly typed props in real time.

---

### Q65: What is JSI Direct Binding and how do C++ Host Objects work?
**Direct Answer:**  
- A C++ class inherits from `jsi::HostObject` and overrides `get()` and `set()`.
- The host object is injected into the JavaScript global namespace (`global.myNativeAPI = hostObject`).
- When JS invokes `global.myNativeAPI.fastCompute()`, the C++ engine executes the native method in-process, reading raw memory pointers with zero serialization latency.

---

### Q66: How do you emit events from native code to JavaScript?
**Direct Answer:**  
- **iOS:** Subclass `RCTEventEmitter`, declare `supportedEvents`, and call:
  `[self sendEventWithName:@"onStatusChange" body:@{@"status": @"connected"}];`
- **Android:**
  `reactContext.getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class).emit("onStatusChange", params);`
- **JavaScript Listener:**
  ```javascript
  const emitter = new NativeEventEmitter(NativeModules.MyModule);
  const sub = emitter.addListener('onStatusChange', e => console.log(e.status));
  ```

---

### Q67: How do you handle native threading and concurrency inside a Native Module?
**Direct Answer:**  
- **Android:** Never perform blocking I/O (network, SQLite, heavy cryptographic hashing) on the calling thread. Dispatch work to background thread pools:
  ```kotlin
  CoroutineScope(Dispatchers.IO).launch {
      val data = fetchHeavyData()
      promise.resolve(data)
  }
  ```
- **iOS:** Explicitly declare `methodQueue`:
  ```objc
  - (dispatch_queue_t)methodQueue {
      return dispatch_queue_create("com.app.backgroundQueue", DISPATCH_QUEUE_SERIAL);
  }
  ```

---

### Q68: How do you integrate third-party native SDKs into React Native?
**Direct Answer:**  
1. Add the native SDK via CocoaPods (`pod 'Stripe'`) on iOS and Gradle (`implementation 'com.stripe:stripe-android'`) on Android.
2. Build a Native Module wrapper that imports the native SDK classes.
3. Expose promise-based or event-based wrappers matching the SDK's lifecycle.
4. Handle native lifecycle events by implementing `LifecycleEventListener` on Android and observing `UIApplication` notifications on iOS.

---

### Q69: How do you implement a Native Splash Screen (`react-native-bootsplash`) without white flashes?
**Direct Answer:**  
- **Why white flashes occur:** The OS native splash screen dismisses as soon as the native binary boots, before Hermes has loaded the JS bundle and before React has rendered the first frame.
- **Solution (`react-native-bootsplash`):**
  - Keeps the native storyboard/drawable splash screen visible over the window during JS bootstrap.
  - When the root React component finishes mounting and initial data fetching completes, call:
    `RNBootSplash.hide({ fade: true });`.

---

### Q70: How do you manage Dynamic App Icons and Quick Actions?
**Direct Answer:**  
- **Dynamic App Icons (iOS):** Use `UIApplication.shared.setAlternateIconName()` (icons must be pre-declared in `Info.plist` under `CFBundleAlternateIcons`).
- **Quick Actions (3D Touch / Long Press on App Icon):** Configured via `UIApplicationShortcutItems` on iOS and Android App Shortcuts. Handled in JS via `react-native-quick-actions`.

---

### Q71: How do you handle native video playback with `react-native-video`?
**Direct Answer:**  
- Wraps native platform video players (`AVPlayer` on iOS, `ExoPlayer` / `Media3` on Android).
- Renders to a native hardware-accelerated video decoding surface (`AVPlayerLayer` / `SurfaceView`).
- Avoids keeping video buffers on the JS thread; controls (play, pause, seek) are dispatched as lightweight native commands.

---

### Q72: How do you implement background audio recording and playback?
**Direct Answer:**  
- Enable iOS `UIBackgroundModes: audio` and Android `FOREGROUND_SERVICE_MEDIA_PLAYBACK`.
- Configure the iOS `AVAudioSessionCategoryPlayback` to prevent audio interruption when the screen locks.
- Use libraries like `react-native-track-player` to handle lock-screen media controls (Now Playing metadata, skip, scrub) connected to platform media control centers.

---

### Q73: How do you handle orientation changes and lock specific screens?
**Direct Answer:**  
Using `react-native-orientation-locker`:
- Set default application orientation to portrait in `Info.plist` and `AndroidManifest.xml`.
- When entering a video player or chart modal:
  ```javascript
  useEffect(() => {
      Orientation.lockToLandscape();
      return () => Orientation.lockToPortrait();
  }, []);
  ```

---

### Q74: How do you implement Haptic Feedback (`react-native-haptic-feedback`)?
**Direct Answer:**  
Triggers physical haptic vibration motors (`Taptic Engine` on iOS, `Vibrator` / `HapticFeedbackConstants` on Android):
```javascript
import ReactNativeHapticFeedback from 'react-native-haptic-feedback';

ReactNativeHapticFeedback.trigger('impactMedium', {
    enableVibrateFallback: true,
    ignoreAndroidSystemSettings: false,
});
```
Used for key micro-interactions: pull-to-refresh snaps, toggle switches, button presses, and error states.

---

### Q75: What is the Bridging Header in iOS and why is it necessary?
**Direct Answer:**  
A header file (`<ProjectName>-Bridging-Header.h`) required by Xcode when using **Swift** inside an Objective-C project:
- React Native's core historically consists of Objective-C headers (`RCTBridgeModule.h`).
- The Bridging Header exposes Objective-C classes to Swift code, allowing your Swift classes to import and implement React Native protocols without compilation errors.
