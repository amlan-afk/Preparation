# 📱 React Native Interview Mastery: Top 100 Questions & Answers

A comprehensive collection of **100 high-yield, production-grade React Native interview questions and answers** for SDE 2, Senior Mobile, and Lead Full-Stack Engineers. Structured into 4 organized modules covering the New Architecture (JSI, Fabric, TurboModules, Hermes), list virtualization & device storage, Reanimated & native modules, and CI/CD/security pipelines.

---

## 🗺️ Curriculum Module Index

| Module | Topic Range | File Link | Question Count | Core Subjects Covered |
|:---:|---|---|:---:|---|
| **01** | **Architecture, Threading & The New Architecture** | [01-architecture-threading-and-bridge.md](./01-architecture-threading-and-bridge.md) | **Q1 – Q25** | Old Bridge bottlenecks, New Architecture 4 pillars (JSI, Fabric, TurboModules, Codegen), Hermes engine, Threading model (UI, JS, Shadow), Yoga Flexbox, Metro bundler, Points vs Pixels, Fast Refresh. |
| **02** | **Components, Navigation, Storage & Native APIs** | [02-core-components-navigation-and-storage.md](./02-core-components-navigation-and-storage.md) | **Q26 – Q50** | `FlatList` virtualization & tuning, `FlashList` cell recycling, `@react-navigation/native-stack`, Deep linking & Universal links, `MMKV` vs `AsyncStorage`, WatermelonDB, Safe Area, Keyboard occlusion, Camera, Geolocation, Push Notifications, Biometrics. |
| **03** | **Animations, Gestures, Native Modules & JSI** | [03-animations-gestures-and-native-modules.md](./03-animations-gestures-and-native-modules.md) | **Q51 – Q75** | `useNativeDriver`, Reanimated Worklets & Shared Values, `react-native-gesture-handler` (GestureDetector), 60 FPS Bottom Sheets, Skia, Custom iOS (Swift/Obj-C) & Android (Kotlin) Native Modules, TurboModules with Codegen, Fabric Native Components, JSI host objects. |
| **04** | **Performance, Debugging, Security & CI/CD** | [04-performance-debugging-and-deployment.md](./04-performance-debugging-and-deployment.md) | **Q76 – Q100** | React Native Profiler & Hermes tracing, Memory leaks in Xcode/Android Studio, Image caching (`expo-image`), OTA updates (CodePush/EAS), APK/AAB size reduction (R8, ABI split), SSL Pinning, Keychain/Keystore security, Jailbreak detection, Sentry, Detox E2E, Fastlane CI/CD, Monorepos. |
| | **TOTAL** | | **100 Questions** | **100% Comprehensive Senior React Native Mastery** |

---

## 🎯 Master List of All 100 Questions

### Module 01: Core Architecture, Threading & The New Architecture (Q1 – Q25)
1. What is React Native and how does it fundamentally differ from hybrid web apps and Flutter?
2. Explain the Old Bridge Architecture and why it became a performance bottleneck.
3. What is the New Architecture in React Native and what are its four pillars?
4. What is JSI (JavaScript Interface) and how does it eliminate asynchronous JSON serialization?
5. What is Fabric and how does it revolutionize the rendering pipeline?
6. What are TurboModules and how do they differ from legacy Native Modules?
7. What is Codegen and why is it essential in the New Architecture?
8. What is the Hermes JavaScript engine and why is it superior on mobile?
9. Explain the Threading Model in React Native (UI Main Thread, JS Thread, Shadow Thread).
10. What causes dropped frames (UI vs JS thread FPS drops) and how do they feel differently to the user?
11. What is Yoga and how does layout calculation work in React Native?
12. How does React Native Flexbox differ from Web CSS Flexbox?
13. How does Metro Bundler work and how does it bundle assets for mobile?
14. What happens during the React Native App Startup / Bootstrap Sequence?
15. What is the difference between `<View>` and a native `UIView` / `android.view.ViewGroup`?
16. How does `<Text>` work in React Native and why can't raw text be placed directly in `<View>`?
17. What is `<Image>` in React Native and what are the pitfalls of rendering remote images?
18. What is the difference between `dp` (density-independent pixels) on Android, `points` on iOS, and physical device pixels?
19. What is `PixelRatio` and `Dimensions` vs `useWindowDimensions`?
20. What is `Platform.select` and platform-specific file extensions?
21. What is the difference between Controlled and Uncontrolled `<TextInput>` in React Native?
22. How does the event dispatching mechanism work from native views back to JS?
23. What is Fast Refresh and how does it preserve component state?
24. What are Concurrent Features in the context of React Native New Architecture?
25. How do you migrate an existing React Native app from the Old Bridge to the New Architecture?

---

### Module 02: Components, Navigation, Storage & Native APIs (Q26 – Q50)
26. How does `FlatList` work internally and why is it preferred over `ScrollView`?
27. What are the key performance props for `FlatList` (`getItemLayout`, `windowSize`, `maxToRenderPerBatch`, etc.)?
28. What is `FlashList` (Shopify) and how does cell recycling outperform `FlatList`?
29. When should you use `SectionList` vs `FlatList`?
30. What is React Navigation and how does it differ from Native Navigation?
31. What are the differences between `@react-navigation/stack` and `@react-navigation/native-stack`?
32. How do you handle Deep Linking and Universal Links in React Navigation?
33. How do you prevent memory leaks when navigating between deeply nested navigation stacks?
34. What is `AsyncStorage` and what are its performance limitations?
35. What is `react-native-mmkv` and why is it up to 30x faster than `AsyncStorage`?
36. When should you choose SQLite, WatermelonDB, or Realm for offline-first databases?
37. How does React Native handle App State transitions (`AppState`)?
38. How do you handle Safe Area boundaries using `react-native-safe-area-context`?
39. How do you handle keyboard occlusion in React Native?
40. How do you manage runtime device permissions on iOS and Android?
41. How do you access device camera and photo libraries in React Native?
42. How do you implement background geolocation tracking and geofencing?
43. How do you implement Push Notifications (FCM / APNs) using Notifee or Firebase?
44. How do you implement Biometric Authentication (FaceID / Fingerprint)?
45. How does Dark Mode / System Theme synchronization work in React Native?
46. How do you handle multi-language localization (i18n) and RTL layouts?
47. How do you manage network connectivity changes using `@react-native-community/netinfo`?
48. What is the difference between Foreground Services and Background Tasks on Android?
49. How do you handle In-App Purchases (IAP) and Subscriptions in React Native?
50. How do you build an offline-first sync engine in React Native?

---

### Module 03: Animations, Gestures, Native Modules & JSI (Q51 – Q75)
51. Why is the built-in `Animated` API often insufficient for complex gestures?
52. What does `useNativeDriver: true` do, and what properties can and CANNOT be offloaded?
53. What is React Native Reanimated and what is a "Worklet"?
54. How do `useSharedValue` and `useAnimatedStyle` work in Reanimated?
55. What is the difference between `withTiming`, `withSpring`, and `withDecay` in Reanimated?
56. What is `react-native-gesture-handler` and why is it superior to `PanResponder`?
57. How do you combine gestures using `GestureDetector` in Reanimated v3?
58. How do you build a 60 FPS interactive Swipeable Bottom Sheet?
59. What are Layout Animations in Reanimated?
60. What is React Native Skia (`@shopify/react-native-skia`)?
61. How do you create a legacy Native Module for iOS (Objective-C / Swift)?
62. How do you create a legacy Native Module for Android (Java / Kotlin)?
63. How do you write a TurboModule with C++ / TypeScript Codegen?
64. How do you write a Fabric Native Component?
65. What is JSI Direct Binding and how do C++ Host Objects work?
66. How do you emit events from native code to JavaScript?
67. How do you handle native threading and concurrency inside a Native Module?
68. How do you integrate third-party native SDKs into React Native?
69. How do you implement a Native Splash Screen (`react-native-bootsplash`) without white flashes?
70. How do you manage Dynamic App Icons and Quick Actions?
71. How do you handle native video playback with `react-native-video`?
72. How do you implement background audio recording and playback?
73. How do you handle orientation changes and lock specific screens?
74. How do you implement Haptic Feedback (`react-native-haptic-feedback`)?
75. What is the Bridging Header in iOS and why is it necessary?

---

### Module 04: Performance Optimization, Debugging, Security & CI/CD (Q76 – Q100)
76. How do you profile and diagnose React Native performance?
77. How do you diagnose and fix JavaScript thread frame drops vs UI thread frame drops?
78. What causes Memory Leaks in React Native and how do you profile them?
79. How do you optimize high-resolution remote images (`expo-image` / `FastImage`)?
80. What are Over-The-Air (OTA) updates and how do CodePush / EAS Update work?
81. What are the store guidelines and legal risks regarding OTA code updates?
82. How do you reduce Android APK / AAB bundle size?
83. How do you optimize iOS IPA bundle size?
84. What is the difference between Expo Managed Workflow, Bare Workflow, and Expo Prebuild?
85. How do you implement SSL Pinning in React Native?
86. How do you securely store sensitive tokens and secrets?
87. Why should you NEVER store API keys or secrets in `.env` files in React Native?
88. How do you detect Jailbroken (iOS) or Rooted (Android) devices?
89. How do you protect React Native JavaScript bundles against reverse engineering?
90. How do you handle crash reporting using Sentry in React Native?
91. How do you implement Automated E2E Testing using Detox?
92. How do you write unit tests in React Native using React Native Testing Library (RNTL)?
93. How do you mock Native Modules in Jest?
94. What is Fastlane and how do you automate builds and store deployment?
95. How does iOS Code Signing work (Certificates, Provisioning Profiles)?
96. How does Android App Signing work (Upload Keys vs Play App Signing)?
97. What is Monorepo architecture for React Native (Turborepo / Nx)?
98. What is React Native for Web (`react-native-web`)?
99. What is Micro-Apps / Super-App architecture in React Native?
100. How do you structure an enterprise-grade React Native codebase?
