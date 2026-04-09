---
skill: react-native
version_covered: React Native 0.83+ (New Architecture mandatory, React 19.2)
depth: comprehensive
last_updated: 2026-03-21
source: web-research
use_when: >
  Load this skill when building cross-platform mobile applications with
  React Native, working with native modules, implementing mobile-specific
  UI patterns, optimizing mobile performance, using Expo (SDK 55+), or
  bridging JavaScript with iOS/Android native code.
---

# React Native — Expert Reference

> **Quick Summary**: React Native is a cross-platform mobile framework by Meta that lets you build native iOS and Android apps using React and JavaScript/TypeScript. The **New Architecture** (JSI, Fabric, TurboModules) is mandatory since 0.82 and delivers near-native performance. React Native 0.83 ships with **React 19.2** (Activity, useEffectEvent), **experimental Hermes V1**, and **React Compiler support**. Expo SDK 55 (Feb 2026) is the recommended starting point with file-based routing, native tabs, and OTA bytecode diffing.

---

## 1. Overview

### What is React Native?

React Native is an open-source framework for building **truly native** mobile applications using React. Unlike web-view frameworks, React Native renders real native UI components — `UIView` on iOS, `android.view.View` on Android — controlled by JavaScript running in a dedicated engine (Hermes).

### Why React Native?

- **Cross-platform**: Single codebase for iOS and Android (with platform-specific escape hatches)
- **Native performance**: Renders real native components, not web views
- **React mental model**: Same component/hook patterns as React for web
- **Hot reloading**: Instant feedback during development (Fast Refresh)
- **New Architecture**: JSI eliminates the bridge bottleneck; Fabric unlocks concurrent rendering
- **Massive ecosystem**: Navigation, state management, animations, and thousands of libraries
- **Used in production**: Instagram, Facebook, Discord, Shopify, Bloomberg, Coinbase

### React Native vs. Alternatives

| Feature            | React Native            | Flutter            | Native (Swift/Kotlin) |
| ------------------ | ----------------------- | ------------------ | --------------------- |
| Language           | JavaScript/TypeScript   | Dart               | Swift / Kotlin        |
| UI Rendering       | Native components       | Custom Skia engine | Native components     |
| Code sharing (web) | ✅ React Native Web     | ⚠️ Limited         | ❌ None               |
| Learning curve     | Low (if you know React) | Medium             | High                  |
| Performance        | Near-native             | Near-native        | Native                |
| App size           | ~8-15 MB starter        | ~6-12 MB starter   | ~2-5 MB               |
| Ecosystem maturity | ✅ Huge                 | ✅ Growing fast    | ✅ Platform SDKs      |
| Hot reload         | ✅ Fast Refresh         | ✅ Hot reload      | ⚠️ Limited            |

---

## 2. Core Concepts

### Architecture Overview (New Architecture — Mandatory since 0.82)

```
┌─────────────────────────────────────────────────┐
│                  Your React Code                │
│            (JSX, Hooks, Components)             │
├─────────────────────────────────────────────────┤
│                     Hermes                      │
│           (JavaScript Engine, AOT)              │
├─────────────────────────────────────────────────┤
│           JSI (JavaScript Interface)            │
│        Direct sync JS ↔ C++ calls               │
├──────────────────┬──────────────────────────────┤
│     Fabric       │       TurboModules           │
│  (Rendering)     │    (Native APIs)             │
│  Concurrent UI   │    Lazy-loaded               │
│  Shadow tree     │    Type-safe via Codegen     │
├──────────────────┴──────────────────────────────┤
│              Native Platform                    │
│         iOS (UIKit)  /  Android (View)          │
└─────────────────────────────────────────────────┘
```

**Key Components:**

| Component        | Role                                                       | Key Benefit                       |
| ---------------- | ---------------------------------------------------------- | --------------------------------- |
| **JSI**          | Replaces the async bridge; direct synchronous JS↔C++ calls | Eliminates serialization overhead |
| **Fabric**       | New rendering system; concurrent UI updates                | Smoother, more responsive UI      |
| **TurboModules** | Lazy-loaded native modules with type-safe interfaces       | Faster startup, less memory       |
| **Codegen**      | Auto-generates type-safe JS↔native bindings                | Compile-time error checking       |
| **Hermes**       | AOT-compiled JS engine optimized for React Native          | Faster startup, lower memory      |

### Core Components

React Native provides platform-agnostic components that map to native counterparts:

| React Native          | iOS                   | Android             | Web Equivalent   |
| --------------------- | --------------------- | ------------------- | ---------------- |
| `<View>`              | `UIView`              | `android.view.View` | `<div>`          |
| `<Text>`              | `UITextView`          | `TextView`          | `<p>` / `<span>` |
| `<Image>`             | `UIImageView`         | `ImageView`         | `<img>`          |
| `<ScrollView>`        | `UIScrollView`        | `ScrollView`        | `<div overflow>` |
| `<TextInput>`         | `UITextField`         | `EditText`          | `<input>`        |
| `<TouchableOpacity>`  | Touch handling        | Touch handling      | `<button>`       |
| `<Pressable>`         | Modern touch API      | Modern touch API    | `<button>`       |
| `<FlatList>`          | Virtualized list      | Virtualized list    | Virtual scroll   |
| `<SectionList>`       | Grouped list          | Grouped list        | Grouped list     |
| `<Modal>`             | Modal presentation    | Dialog              | Modal dialog     |
| `<ActivityIndicator>` | `UIActivityIndicator` | `ProgressBar`       | Spinner          |
| `<Switch>`            | `UISwitch`            | `Switch`            | Checkbox         |
| `<SafeAreaView>`      | Safe area insets      | Status bar insets   | N/A              |

### Styling (Flexbox-based)

React Native uses **JavaScript objects** for styling with a Flexbox-first layout engine (Yoga). All dimensions are **density-independent pixels** (dp), not CSS pixels.

```jsx
import { StyleSheet, View, Text } from "react-native";

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: "column", // Default (unlike web's 'row')
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#1a1a2e",
    padding: 16,
  },
  title: {
    fontSize: 24,
    fontWeight: "700",
    color: "#e94560",
    marginBottom: 8,
  },
  card: {
    backgroundColor: "#16213e",
    borderRadius: 12,
    padding: 20,
    // Shadow (iOS)
    shadowColor: "#000",
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.25,
    shadowRadius: 4,
    // Shadow (Android)
    elevation: 5,
  },
});
```

**Key Styling Differences from Web CSS:**

- `flexDirection` defaults to `'column'` (web defaults to `'row'`)
- No CSS units — just numbers (dp) and percentages
- No cascading — styles don't inherit (except some `Text` properties)
- `boxShadow` and `filter` are available in 0.76+ (New Architecture)
- Use `Platform.select()` for platform-specific styles

### Key Terms

| Term                | Definition                                                               |
| ------------------- | ------------------------------------------------------------------------ |
| **Bridge (Legacy)** | Old async communication layer between JS and native (removed in 0.82)    |
| **JSI**             | JavaScript Interface — direct, synchronous JS↔C++ calls                  |
| **Fabric**          | New rendering system with concurrent support                             |
| **TurboModules**    | New native module system with lazy loading and type safety               |
| **Hermes**          | Meta's JS engine optimized for React Native (default engine)             |
| **Hermes V1**       | Next-gen Hermes with improved ES6 support and performance (experimental) |
| **Metro**           | JavaScript bundler for React Native (v0.82+ with 3x faster startup)      |
| **Expo**            | Framework and platform for universal React apps                          |
| **Expo Router**     | File-based routing for React Native (v7 in SDK 55)                       |
| **Fast Refresh**    | Hot reloading that preserves component state                             |
| **Codegen**         | Auto-generates native bindings from TypeScript definitions               |
| **Yoga**            | Cross-platform Flexbox layout engine                                     |
| **EAS**             | Expo Application Services — cloud build/submit/update                    |
| **DOM Node APIs**   | React Native 0.82+ provides DOM-like node access via refs                |
| **React Compiler**  | Build-time auto-memoization (supported since RN 0.78)                    |

---

## 3. Syntax / API / Command Reference

### Project Setup

```bash
# Recommended: Expo (streamlined setup, managed workflow)
npx create-expo-app@latest MyApp
cd MyApp
npx expo start

# Bare React Native (full native control)
npx @react-native-community/cli init MyApp
cd MyApp
npx react-native run-ios    # or run-android
```

### Basic App Structure

```jsx
// App.tsx
import React from "react";
import {
  SafeAreaView,
  View,
  Text,
  StyleSheet,
  StatusBar,
  Platform,
} from "react-native";

export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <StatusBar barStyle="light-content" />
      <View style={styles.content}>
        <Text style={styles.title}>Hello, React Native!</Text>
        <Text style={styles.subtitle}>
          Running on {Platform.OS} ({Platform.Version})
        </Text>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#0f0f23",
  },
  content: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    padding: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#ffffff",
    marginBottom: 8,
  },
  subtitle: {
    fontSize: 16,
    color: "#ccccdd",
  },
});
```

### Navigation (React Navigation)

```bash
npm install @react-navigation/native @react-navigation/native-stack
npm install react-native-screens react-native-safe-area-context
```

```jsx
// App.tsx
import { NavigationContainer } from "@react-navigation/native";
import { createNativeStackNavigator } from "@react-navigation/native-stack";

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen
          name="Home"
          component={HomeScreen}
          options={{ title: "Welcome" }}
        />
        <Stack.Screen
          name="Details"
          component={DetailsScreen}
          options={({ route }) => ({ title: route.params?.name })}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// Tab Navigation
import { createBottomTabNavigator } from "@react-navigation/bottom-tabs";

const Tab = createBottomTabNavigator();

function MainTabs() {
  return (
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ color, size }) => {
          const icons = { Home: "home", Search: "search", Profile: "person" };
          return <Icon name={icons[route.name]} size={size} color={color} />;
        },
        tabBarActiveTintColor: "#e94560",
        tabBarInactiveTintColor: "#888",
      })}
    >
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Search" component={SearchScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}

// Navigate between screens
function HomeScreen({ navigation }) {
  return (
    <View>
      <Pressable
        onPress={() =>
          navigation.navigate("Details", { id: 42, name: "Product" })
        }
      >
        <Text>Go to Details</Text>
      </Pressable>
    </View>
  );
}

// Receive params
function DetailsScreen({ route, navigation }) {
  const { id, name } = route.params;
  return (
    <Text>
      Details for {name} (ID: {id})
    </Text>
  );
}
```

### Lists (FlatList & FlashList)

```jsx
import { FlatList, View, Text, StyleSheet } from "react-native";

function UserList({ users }) {
  const renderItem = ({ item }) => (
    <View style={styles.item}>
      <Text style={styles.name}>{item.name}</Text>
      <Text style={styles.email}>{item.email}</Text>
    </View>
  );

  return (
    <FlatList
      data={users}
      renderItem={renderItem}
      keyExtractor={(item) => item.id.toString()}
      ItemSeparatorComponent={() => <View style={styles.separator} />}
      ListEmptyComponent={<Text>No users found</Text>}
      ListHeaderComponent={<Text style={styles.header}>Users</Text>}
      onEndReached={loadMore}
      onEndReachedThreshold={0.5}
      refreshing={refreshing}
      onRefresh={handleRefresh}
      // Performance optimizations:
      getItemLayout={(data, index) => ({
        length: ITEM_HEIGHT,
        offset: ITEM_HEIGHT * index,
        index,
      })}
      windowSize={5}
      maxToRenderPerBatch={10}
      removeClippedSubviews={true}
    />
  );
}

// For even better performance, use FlashList:
// npm install @shopify/flash-list
import { FlashList } from "@shopify/flash-list";

function FastUserList({ users }) {
  return (
    <FlashList
      data={users}
      renderItem={({ item }) => <UserCard user={item} />}
      estimatedItemSize={80} // Required: estimated row height
      keyExtractor={(item) => item.id}
    />
  );
}
```

### Animations (Reanimated)

```bash
npm install react-native-reanimated
```

```jsx
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
  withRepeat,
  withSequence,
  Easing,
  interpolate,
  FadeIn,
  SlideInRight,
} from "react-native-reanimated";
import { Gesture, GestureDetector } from "react-native-gesture-handler";

// Basic spring animation
function BouncyButton() {
  const scale = useSharedValue(1);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
  }));

  const handlePressIn = () => {
    scale.value = withSpring(0.9, { damping: 10, stiffness: 200 });
  };

  const handlePressOut = () => {
    scale.value = withSpring(1, { damping: 10, stiffness: 200 });
  };

  return (
    <Pressable onPressIn={handlePressIn} onPressOut={handlePressOut}>
      <Animated.View style={[styles.button, animatedStyle]}>
        <Text style={styles.buttonText}>Press Me</Text>
      </Animated.View>
    </Pressable>
  );
}

// Gesture-based animation (drag)
function DraggableCard() {
  const translateX = useSharedValue(0);
  const translateY = useSharedValue(0);

  const gesture = Gesture.Pan()
    .onUpdate((e) => {
      translateX.value = e.translationX;
      translateY.value = e.translationY;
    })
    .onEnd(() => {
      translateX.value = withSpring(0);
      translateY.value = withSpring(0);
    });

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: translateX.value },
      { translateY: translateY.value },
    ],
  }));

  return (
    <GestureDetector gesture={gesture}>
      <Animated.View style={[styles.card, animatedStyle]}>
        <Text>Drag me!</Text>
      </Animated.View>
    </GestureDetector>
  );
}

// Layout animations (entering/exiting)
function AnimatedListItem({ item, index }) {
  return (
    <Animated.View
      entering={FadeIn.delay(index * 100).springify()}
      exiting={SlideInRight}
    >
      <Text>{item.name}</Text>
    </Animated.View>
  );
}
```

### Platform-Specific Code

```jsx
import { Platform, StyleSheet } from "react-native";

// Method 1: Platform.select
const styles = StyleSheet.create({
  container: {
    ...Platform.select({
      ios: { shadowColor: "#000", shadowOffset: { width: 0, height: 2 } },
      android: { elevation: 4 },
      default: { boxShadow: "0 2px 4px rgba(0,0,0,0.2)" },
    }),
  },
});

// Method 2: Platform.OS
if (Platform.OS === "ios") {
  // iOS-specific logic
} else if (Platform.OS === "android") {
  // Android-specific logic
}

// Method 3: Platform-specific files
// Button.ios.tsx    ← loaded on iOS
// Button.android.tsx ← loaded on Android
// Import just uses: import Button from './Button';
```

### Networking

```jsx
// Fetch API (built-in)
async function fetchUsers() {
  try {
    const response = await fetch("https://api.example.com/users", {
      headers: {
        Authorization: `Bearer ${token}`,
        "Content-Type": "application/json",
      },
    });
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error("Fetch error:", error);
    throw error;
  }
}

// With TanStack Query (recommended for production)
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";

function UserList() {
  const { data, isLoading, error, refetch } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
    staleTime: 5 * 60 * 1000, // 5 min cache
  });

  if (isLoading) return <ActivityIndicator />;
  if (error) return <Text>Error: {error.message}</Text>;

  return (
    <FlatList
      data={data}
      renderItem={({ item }) => <UserCard user={item} />}
      keyExtractor={(item) => item.id}
      onRefresh={refetch}
      refreshing={isLoading}
    />
  );
}
```

### Storage

```jsx
// AsyncStorage (key-value, async)
import AsyncStorage from "@react-native-async-storage/async-storage";

await AsyncStorage.setItem("user", JSON.stringify(user));
const user = JSON.parse(await AsyncStorage.getItem("user"));
await AsyncStorage.removeItem("user");

// MMKV (faster alternative — synchronous)
import { MMKV } from "react-native-mmkv";

const storage = new MMKV();
storage.set("token", "abc123"); // Sync!
const token = storage.getString("token");
storage.delete("token");
```

---

## 4. Patterns & Best Practices

### ✅ Do

- **Use Expo (SDK 55+)** for new projects — simplifies setup, builds, updates, and native modules
- **Ensure New Architecture** — it's mandatory since 0.82; legacy architecture is no longer supported
- **Organize by feature**: `features/auth/`, `features/profile/`, not `components/`, `screens/`
- **Use TypeScript** — catch bugs at compile time, better IDE support, type-safe TurboModules
- **Use `FlatList` / `FlashList`** for lists — never `ScrollView` with `map()` for dynamic data
- **Use Reanimated for animations** — runs on the UI thread, 60fps guaranteed
- **Compress images** — use WebP, resize to device resolution, cache with `react-native-fast-image`
- **Clean up effects** — return cleanup in `useEffect` to avoid memory leaks
- **Use `Pressable`** over `TouchableOpacity` — modern API with more flexibility
- **Separate state types**: server state (TanStack Query), app state (Zustand), local state (`useState`)
- **Test on real devices** — simulators miss performance issues, touch behavior, and camera/GPS
- **Profile with Flipper/DevTools** — identify bottlenecks before they reach production
- **Use `SafeAreaView`** or `useSafeAreaInsets` — handle notches and status bars correctly
- **Batch API calls** — reduce network overhead with combined endpoints or GraphQL
- **Use MMKV over AsyncStorage** for frequently accessed data — it's synchronous and much faster

### ❌ Don't

- **Don't use `ScrollView` for long lists** — it renders all items upfront, consuming massive memory

  ```jsx
  // ❌ BAD — renders all 1000 items
  <ScrollView>
    {items.map(item => <ItemCard key={item.id} item={item} />)}
  </ScrollView>

  // ✅ GOOD — virtualizes, renders only visible items
  <FlatList data={items} renderItem={({ item }) => <ItemCard item={item} />} />
  ```

- **Don't use inline functions in render** for frequently re-rendered components

  ```jsx
  // ❌ BAD — new function every render
  <FlatList
    renderItem={({ item }) => <Card onPress={() => handlePress(item.id)} />}
  />;

  // ✅ GOOD — stable reference
  const handlePress = useCallback((id) => {
    /* ... */
  }, []);
  const renderItem = useCallback(
    ({ item }) => <Card onPress={() => handlePress(item.id)} />,
    [handlePress],
  );
  ```

- **Don't ignore platform differences** — test on both iOS and Android; use `Platform.select()`
- **Don't skip error boundaries** — unhandled errors crash the entire app
- **Don't overuse global state** — keep state as local as possible
- **Don't use `Animated` API for complex animations** — Reanimated runs on the UI thread
- **Don't hardcode dimensions** — use `Dimensions`, `useWindowDimensions`, or flexbox
- **Don't store sensitive data in AsyncStorage** — use `react-native-keychain` or `expo-secure-store`
- **Don't delay React Native version upgrades** — breaking changes accumulate and make future upgrades painful
- **Don't ignore edge-to-edge layouts on Android** — mandatory in Expo SDK 54+ and Android 15+
- **Don't stay on legacy architecture** — it's removed in 0.82; migrate now to avoid breaking

---

## 5. Common Pitfalls & Gotchas

### 1. Treating React Native Like Web React

React Native renders native components, NOT HTML. There is no DOM.

```jsx
// ❌ These don't exist in React Native
<div>, <span>, <p>, <h1>, <button>, <input>

// ✅ Use native equivalents
<View>, <Text>, <Text>, <Text>, <Pressable>, <TextInput>
```

### 2. flexDirection Defaults to 'column'

```jsx
// Web CSS default: row (horizontal)
// React Native default: column (vertical)

// If you want horizontal layout:
<View style={{ flexDirection: "row" }}>
  <View style={{ flex: 1 }} />
  <View style={{ flex: 1 }} />
</View>
```

### 3. Text Must Be Inside `<Text>` Components

```jsx
// ❌ CRASH — bare text outside <Text>
<View>
  Hello World
</View>

// ✅ Always wrap text
<View>
  <Text>Hello World</Text>
</View>
```

### 4. Shadows Work Differently per Platform

```jsx
// iOS uses shadow* properties
shadowColor: '#000',
shadowOffset: { width: 0, height: 2 },
shadowOpacity: 0.25,
shadowRadius: 4,

// Android uses elevation
elevation: 5,

// Use Platform.select for cross-platform
...Platform.select({
  ios: { shadowColor: '#000', shadowOffset: { width: 0, height: 2 }, shadowOpacity: 0.25, shadowRadius: 4 },
  android: { elevation: 5 },
})

// React Native 0.76+ (New Architecture): use boxShadow
boxShadow: '0 2px 4px rgba(0,0,0,0.25)', // Web-like syntax!
```

### 5. Keyboard Covering Inputs

```jsx
// ❌ Input hidden behind keyboard
<View>
  <TextInput placeholder="Type here..." />
</View>;

// ✅ KeyboardAvoidingView
import { KeyboardAvoidingView, Platform } from "react-native";

<KeyboardAvoidingView
  behavior={Platform.OS === "ios" ? "padding" : "height"}
  style={{ flex: 1 }}
>
  <TextInput placeholder="Type here..." />
</KeyboardAvoidingView>;
```

### 6. FlatList Performance Issues

```jsx
// Common mistakes:
// 1. Not using keyExtractor
// 2. Not memoizing renderItem
// 3. Not using getItemLayout for fixed-height items
// 4. Wrapping FlatList in ScrollView (breaks virtualization!)

// ❌ BAD
<ScrollView>
  <FlatList data={items} ... />  {/* Virtualization disabled! */}
</ScrollView>

// ✅ Use ListHeaderComponent / ListFooterComponent instead
<FlatList
  data={items}
  ListHeaderComponent={<Header />}
  ListFooterComponent={<Footer />}
  ...
/>
```

### 7. Memory Leaks with Event Listeners

```jsx
// ❌ BUG — listener never removed
useEffect(() => {
  const subscription = Dimensions.addEventListener("change", handleChange);
}, []);

// ✅ FIX — cleanup
useEffect(() => {
  const subscription = Dimensions.addEventListener("change", handleChange);
  return () => subscription.remove();
}, []);
```

### 8. Environment Setup Pain

- Android requires: JDK, Android SDK, environment variables (`ANDROID_HOME`)
- iOS requires: Xcode, CocoaPods (`cd ios && pod install`)
- **Expo significantly reduces these pain points** — recommended for most projects

### 9. Debugging Behind Native Walls

- React Native errors sometimes surface as cryptic native crashes
- **Use Flipper or React Native DevTools** for JS debugging
- **Use Xcode Instruments / Android Profiler** for native-level issues
- **Sentry or Bugsnag** for production crash reporting

---

## 6. Advanced Topics

### The New Architecture Deep Dive

#### JSI (JavaScript Interface)

JSI replaces the old async bridge with direct, synchronous calls between JavaScript and C++/native code:

```
// Old Bridge (async, serialized):
JS → JSON.stringify → Bridge Queue → JSON.parse → Native

// New JSI (sync, direct):
JS → JSI → C++ Host Object → Native
```

This eliminates serialization overhead and enables shared ownership of objects between JS and native.

#### Fabric (Rendering System)

Fabric enables:

- **Concurrent rendering**: Multiple UI versions prepared simultaneously
- **Synchronous layout**: `useLayoutEffect` works correctly
- **Priority-based updates**: Urgent user interactions get priority
- **Direct shadow tree manipulation**: No bridge roundtrip for layout

#### TurboModules

```typescript
// spec/NativeCalculator.ts — Type-safe module specification
import type { TurboModule } from "react-native";
import { TurboModuleRegistry } from "react-native";

export interface Spec extends TurboModule {
  add(a: number, b: number): number; // Sync method
  fetchData(url: string): Promise<string>; // Async method
}

export default TurboModuleRegistry.getEnforcing<Spec>("Calculator");
```

Key advantages over legacy NativeModules:

- **Lazy loading**: Modules load only when first accessed
- **Type safety**: Codegen validates JS↔native interface at compile time
- **Sync support**: JSI allows synchronous calls when appropriate

### React 19 Integration (since RN 0.78)

React Native 0.78+ ships with React 19, bringing all React 19 features to mobile:

```jsx
import { useActionState, useOptimistic, use } from "react";

// useActionState for forms
const [state, formAction, isPending] = useActionState(
  async (prev, formData) => {
    const result = await api.submit(formData);
    return result;
  },
  { error: null },
);

// useOptimistic for instant feedback
const [optimisticItems, addOptimistic] = useOptimistic(
  items,
  (current, newItem) => [...current, { ...newItem, pending: true }],
);

// ref as prop (no more forwardRef!)
function CustomInput({ ref, ...props }) {
  return <TextInput ref={ref} {...props} />;
}
```

### React Compiler Support (since RN 0.78)

The React Compiler (v1.0 stable, Oct 2025) auto-memoizes components, reducing the need for manual `useMemo`, `useCallback`, and `React.memo`.

```bash
# Install
npm install babel-plugin-react-compiler

# babel.config.js
module.exports = {
  plugins: ['react-compiler'],
};
```

### Hermes V1 (Experimental, since RN 0.82)

Hermes V1 is the next-generation JavaScript engine with improved ES6 support, faster bundle loading, and better Time to Interactive (TTI). Opt-in via Expo or build configuration.

```json
// app.json (Expo SDK 55+)
{
  "expo": {
    "jsEngine": "hermes",
    "experiments": {
      "hermesV1": true
    }
  }
}
```

### Custom Native Modules (Bridging)

When JavaScript can't do the job, write native code:

```swift
// iOS: CalendarModule.swift
@objc(CalendarModule)
class CalendarModule: NSObject {
  @objc
  func createEvent(_ name: String, location: String, date: Double) {
    // Access iOS Calendar API
  }

  @objc
  static func requiresMainQueueSetup() -> Bool { return false }
}
```

```kotlin
// Android: CalendarModule.kt
class CalendarModule(reactContext: ReactApplicationContext)
  : ReactContextBaseJavaModule(reactContext) {

  override fun getName() = "CalendarModule"

  @ReactMethod
  fun createEvent(name: String, location: String, date: Double) {
    // Access Android Calendar API
  }
}
```

```jsx
// JavaScript usage
import { NativeModules } from "react-native";
const { CalendarModule } = NativeModules;
CalendarModule.createEvent("Meeting", "Office", Date.now());
```

### Performance Monitoring

```jsx
// Track performance metrics
import { PerformanceObserver } from "react-native";

// Key metrics to monitor:
// 1. Cold start time (< 2s target)
// 2. Time to interactive on key screens
// 3. JS frame rate (target: 60fps)
// 4. Memory growth over time
// 5. Crash-free session rate (> 99.5%)

// Profile with React DevTools:
// npx react-devtools
// → Profiler tab → Record → Identify slow renders
```

### Over-the-Air Updates (Expo EAS Update)

```bash
# Install EAS CLI
npm install -g eas-cli

# Configure updates
eas update:configure

# Push an OTA update
eas update --branch production --message "Fix login bug"
```

OTA updates let you push JavaScript/asset changes without going through app store review — critical for rapid bug fixes.

---

## 7. Ecosystem & Tooling

| Tool/Library                      | Purpose                           | When to Use                            |
| --------------------------------- | --------------------------------- | -------------------------------------- |
| **Expo SDK 55**                   | Framework + build/deploy platform | Default choice for new projects        |
| **EAS Build**                     | Cloud native builds               | CI/CD for app store submissions        |
| **EAS Update**                    | Over-the-air JS updates           | Push fixes without app store review    |
| **Expo Router v7**                | File-based routing (web + mobile) | Default routing for Expo projects      |
| **React Navigation 7**            | Navigation (stack, tab, drawer)   | Navigation for non-Expo projects       |
| **React Native Navigation (Wix)** | Native navigation controllers     | Complex UIs needing pure native nav    |
| **Reanimated 4**                  | UI-thread animations              | All animations (default choice)        |
| **Gesture Handler**               | Native gesture recognition        | Touch, swipe, drag interactions        |
| **FlashList**                     | High-perf virtualized list        | Large lists (replaces FlatList)        |
| **MMKV**                          | Fast synchronous storage          | Frequently read/written data           |
| **AsyncStorage**                  | Async key-value storage           | Simple persistence needs               |
| **TanStack Query 5**              | Server state & caching            | API data fetching and caching          |
| **Zustand 5.x**                   | Lightweight state management      | App/session state                      |
| **Redux Toolkit**                 | Predictable global state          | Large/complex enterprise apps          |
| **Expo Image**                    | Performant image loading          | Image-heavy apps (replaces fast-image) |
| **react-native-svg**              | SVG rendering                     | Icons, charts, custom graphics         |
| **react-native-maps**             | Map integration                   | Google/Apple Maps                      |
| **expo-video / expo-audio**       | Media playback                    | Video and audio features               |
| **Sentry / Bugsnag**              | Crash reporting                   | Production error monitoring            |
| **React Native DevTools**         | Debugging tool                    | JS, layout, performance debugging      |
| **Detox**                         | E2E testing framework             | Automated integration/E2E tests        |
| **React Native Testing Library**  | Component testing                 | Unit/integration tests                 |
| **Notifee**                       | Local notifications               | Scheduled/triggered notifications      |
| **Firebase**                      | Backend services                  | Auth, analytics, push notifications    |
| **React Compiler**                | Automatic memoization             | All projects (since RN 0.78)           |

---

## 8. Quick Reference Cheat Sheet

### React Native CLI Commands

```bash
# Start development server
npx expo start                    # Expo
npx react-native start           # Bare RN

# Run on device/simulator
npx expo run:ios
npx expo run:android
npx react-native run-ios
npx react-native run-android

# Build for production (Expo)
eas build --platform ios
eas build --platform android

# OTA update
eas update --branch production

# Install pods (iOS, bare RN)
cd ios && pod install

# Clean and rebuild
cd android && ./gradlew clean
cd ios && xcodebuild clean

# Link native dependencies (rare now with autolinking)
npx react-native link
```

### Layout Cheat Sheet

```jsx
// Center content
{ flex: 1, justifyContent: 'center', alignItems: 'center' }

// Row layout
{ flexDirection: 'row' }

// Space between items
{ flexDirection: 'row', justifyContent: 'space-between' }

// Absolute positioning
{ position: 'absolute', top: 0, left: 0, right: 0 }

// Full screen
{ ...StyleSheet.absoluteFillObject }

// Responsive dimensions
import { Dimensions, useWindowDimensions } from 'react-native';
const { width, height } = useWindowDimensions(); // Reactive to rotation!
```

### Common Import Pattern

```jsx
import {
  View,
  Text,
  Image,
  Pressable,
  TextInput,
  FlatList,
  ScrollView,
  Modal,
  SafeAreaView,
  StatusBar,
  Platform,
  StyleSheet,
  Dimensions,
  Alert,
  Linking,
  ActivityIndicator,
  KeyboardAvoidingView,
} from "react-native";
```

---

## 9. Worked Examples

### Example 1: Contact List with Search & Pull-to-Refresh

```jsx
import React, { useState, useCallback, useMemo } from "react";
import {
  View,
  Text,
  TextInput,
  FlatList,
  Image,
  SafeAreaView,
  StyleSheet,
  RefreshControl,
  Pressable,
} from "react-native";

const CONTACTS = [
  {
    id: "1",
    name: "Alice Johnson",
    phone: "+1-555-0101",
    avatar: "https://i.pravatar.cc/150?img=1",
  },
  {
    id: "2",
    name: "Bob Smith",
    phone: "+1-555-0102",
    avatar: "https://i.pravatar.cc/150?img=2",
  },
  {
    id: "3",
    name: "Charlie Brown",
    phone: "+1-555-0103",
    avatar: "https://i.pravatar.cc/150?img=3",
  },
  {
    id: "4",
    name: "Diana Prince",
    phone: "+1-555-0104",
    avatar: "https://i.pravatar.cc/150?img=4",
  },
  {
    id: "5",
    name: "Eve Adams",
    phone: "+1-555-0105",
    avatar: "https://i.pravatar.cc/150?img=5",
  },
];

function ContactCard({ contact, onPress }) {
  return (
    <Pressable
      style={({ pressed }) => [styles.contactCard, pressed && styles.pressed]}
      onPress={() => onPress(contact)}
    >
      <Image source={{ uri: contact.avatar }} style={styles.avatar} />
      <View style={styles.contactInfo}>
        <Text style={styles.contactName}>{contact.name}</Text>
        <Text style={styles.contactPhone}>{contact.phone}</Text>
      </View>
      <Text style={styles.chevron}>›</Text>
    </Pressable>
  );
}

export default function ContactListScreen() {
  const [search, setSearch] = useState("");
  const [refreshing, setRefreshing] = useState(false);

  const filteredContacts = useMemo(() => {
    if (!search.trim()) return CONTACTS;
    const query = search.toLowerCase();
    return CONTACTS.filter(
      (c) => c.name.toLowerCase().includes(query) || c.phone.includes(query),
    );
  }, [search]);

  const handleRefresh = useCallback(async () => {
    setRefreshing(true);
    await new Promise((resolve) => setTimeout(resolve, 1500)); // Simulate fetch
    setRefreshing(false);
  }, []);

  const handlePress = useCallback((contact) => {
    Alert.alert("Contact", `Call ${contact.name}?`);
  }, []);

  const renderItem = useCallback(
    ({ item }) => <ContactCard contact={item} onPress={handlePress} />,
    [handlePress],
  );

  return (
    <SafeAreaView style={styles.container}>
      <Text style={styles.header}>Contacts</Text>
      <TextInput
        style={styles.searchInput}
        placeholder="Search contacts..."
        placeholderTextColor="#888"
        value={search}
        onChangeText={setSearch}
        clearButtonMode="while-editing"
      />
      <FlatList
        data={filteredContacts}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
        refreshControl={
          <RefreshControl refreshing={refreshing} onRefresh={handleRefresh} />
        }
        ListEmptyComponent={
          <Text style={styles.emptyText}>No contacts found</Text>
        }
        contentContainerStyle={
          filteredContacts.length === 0 && styles.emptyList
        }
      />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: "#0f0f23" },
  header: {
    fontSize: 34,
    fontWeight: "bold",
    color: "#fff",
    paddingHorizontal: 20,
    paddingTop: 16,
  },
  searchInput: {
    backgroundColor: "#1a1a3e",
    color: "#fff",
    borderRadius: 12,
    paddingHorizontal: 16,
    paddingVertical: 12,
    margin: 16,
    fontSize: 16,
  },
  contactCard: {
    flexDirection: "row",
    alignItems: "center",
    paddingHorizontal: 20,
    paddingVertical: 12,
  },
  pressed: { backgroundColor: "rgba(255,255,255,0.05)" },
  avatar: { width: 48, height: 48, borderRadius: 24, marginRight: 14 },
  contactInfo: { flex: 1 },
  contactName: {
    fontSize: 17,
    fontWeight: "600",
    color: "#fff",
    marginBottom: 2,
  },
  contactPhone: { fontSize: 14, color: "#888" },
  chevron: { fontSize: 24, color: "#555" },
  emptyText: {
    textAlign: "center",
    color: "#888",
    fontSize: 16,
    marginTop: 40,
  },
  emptyList: { flex: 1, justifyContent: "center" },
});
```

### Example 2: Animated Card with Gesture (Reanimated + Gesture Handler)

```jsx
import React from "react";
import { View, Text, StyleSheet, Dimensions } from "react-native";
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  interpolate,
  Extrapolation,
  runOnJS,
} from "react-native-reanimated";
import {
  Gesture,
  GestureDetector,
  GestureHandlerRootView,
} from "react-native-gesture-handler";

const { width: SCREEN_WIDTH } = Dimensions.get("window");
const SWIPE_THRESHOLD = SCREEN_WIDTH * 0.3;

function SwipeableCard({ title, subtitle, onSwipeLeft, onSwipeRight }) {
  const translateX = useSharedValue(0);
  const rotate = useSharedValue(0);

  const gesture = Gesture.Pan()
    .onUpdate((e) => {
      translateX.value = e.translationX;
      rotate.value = interpolate(
        e.translationX,
        [-SCREEN_WIDTH / 2, 0, SCREEN_WIDTH / 2],
        [-15, 0, 15],
        Extrapolation.CLAMP,
      );
    })
    .onEnd((e) => {
      if (Math.abs(e.translationX) > SWIPE_THRESHOLD) {
        // Swiped past threshold
        translateX.value = withSpring(
          e.translationX > 0 ? SCREEN_WIDTH : -SCREEN_WIDTH,
          { damping: 20 },
        );
        if (e.translationX > 0) {
          runOnJS(onSwipeRight)();
        } else {
          runOnJS(onSwipeLeft)();
        }
      } else {
        // Snap back
        translateX.value = withSpring(0);
        rotate.value = withSpring(0);
      }
    });

  const cardStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: translateX.value },
      { rotate: `${rotate.value}deg` },
    ],
  }));

  const likeOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(
      translateX.value,
      [0, SWIPE_THRESHOLD],
      [0, 1],
      Extrapolation.CLAMP,
    ),
  }));

  const dislikeOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(
      translateX.value,
      [-SWIPE_THRESHOLD, 0],
      [1, 0],
      Extrapolation.CLAMP,
    ),
  }));

  return (
    <GestureDetector gesture={gesture}>
      <Animated.View style={[styles.card, cardStyle]}>
        <Animated.Text style={[styles.label, styles.likeLabel, likeOpacity]}>
          LIKE ❤️
        </Animated.Text>
        <Animated.Text
          style={[styles.label, styles.dislikeLabel, dislikeOpacity]}
        >
          NOPE 👎
        </Animated.Text>
        <Text style={styles.cardTitle}>{title}</Text>
        <Text style={styles.cardSubtitle}>{subtitle}</Text>
      </Animated.View>
    </GestureDetector>
  );
}

export default function SwipeScreen() {
  return (
    <GestureHandlerRootView style={styles.container}>
      <SwipeableCard
        title="React Native"
        subtitle="Build native apps with React"
        onSwipeLeft={() => console.log("Swiped left!")}
        onSwipeRight={() => console.log("Swiped right!")}
      />
    </GestureHandlerRootView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#0f0f23",
  },
  card: {
    width: SCREEN_WIDTH * 0.85,
    height: 400,
    backgroundColor: "#1a1a3e",
    borderRadius: 20,
    padding: 24,
    justifyContent: "flex-end",
    shadowColor: "#e94560",
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 10,
    elevation: 8,
  },
  cardTitle: {
    fontSize: 28,
    fontWeight: "bold",
    color: "#fff",
    marginBottom: 8,
  },
  cardSubtitle: { fontSize: 16, color: "#aaa" },
  label: {
    position: "absolute",
    top: 40,
    fontSize: 32,
    fontWeight: "bold",
    padding: 8,
  },
  likeLabel: {
    right: 20,
    color: "#4ecdc4",
    borderColor: "#4ecdc4",
    borderWidth: 3,
    borderRadius: 8,
  },
  dislikeLabel: {
    left: 20,
    color: "#e94560",
    borderColor: "#e94560",
    borderWidth: 3,
    borderRadius: 8,
  },
});
```

### Example 3: Authentication Flow with Context

```jsx
// contexts/AuthContext.tsx
import React, {
  createContext,
  useContext,
  useState,
  useEffect,
  useCallback,
} from "react";
import * as SecureStore from "expo-secure-store";

const AuthContext = createContext(undefined);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    // Check for stored token on app start
    async function bootstrap() {
      try {
        const token = await SecureStore.getItemAsync("authToken");
        if (token) {
          const userData = await fetchUserProfile(token);
          setUser(userData);
        }
      } catch (err) {
        console.error("Auth bootstrap failed:", err);
        await SecureStore.deleteItemAsync("authToken");
      } finally {
        setIsLoading(false);
      }
    }
    bootstrap();
  }, []);

  const signIn = useCallback(async (email, password) => {
    const response = await fetch("https://api.example.com/login", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ email, password }),
    });
    if (!response.ok) throw new Error("Invalid credentials");
    const { token, user: userData } = await response.json();
    await SecureStore.setItemAsync("authToken", token);
    setUser(userData);
  }, []);

  const signOut = useCallback(async () => {
    await SecureStore.deleteItemAsync("authToken");
    setUser(null);
  }, []);

  return (
    <AuthContext.Provider value={{ user, isLoading, signIn, signOut }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) throw new Error("useAuth must be used within AuthProvider");
  return context;
}

// App.tsx — Navigation based on auth state
function App() {
  return (
    <AuthProvider>
      <NavigationContainer>
        <RootNavigator />
      </NavigationContainer>
    </AuthProvider>
  );
}

function RootNavigator() {
  const { user, isLoading } = useAuth();

  if (isLoading) return <SplashScreen />;

  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      {user ? (
        <Stack.Screen name="Main" component={MainTabs} />
      ) : (
        <Stack.Screen name="Auth" component={AuthStack} />
      )}
    </Stack.Navigator>
  );
}
```

---

## 10. Go Deeper — Resources

- [Official React Native Docs](https://reactnative.dev/) — Core reference and guides
- [Expo Documentation](https://docs.expo.dev/) — Managed workflow, EAS, SDK 55 reference
- [React Native New Architecture](https://reactnative.dev/docs/new-architecture-intro) — Migration guide
- [React Native 0.83 Release Notes](https://reactnative.dev/blog) — React 19.2, no breaking changes
- [React Navigation Docs](https://reactnavigation.org/) — Navigation library guide
- [Expo Router v7 Docs](https://docs.expo.dev/router/introduction/) — File-based routing
- [Reanimated Documentation](https://docs.swmansion.com/react-native-reanimated/) — Animation API reference
- [FlashList Documentation](https://shopify.github.io/flash-list/) — High-performance list
- [React Native Directory](https://reactnative.directory/) — Searchable library catalog
- [Callstack Blog](https://www.callstack.com/blog) — Advanced RN engineering articles
- [React Native Release Schedule](https://reactnative.dev/contributing/release-stable-minor) — 6 releases/year cadence
- [React Compiler Docs](https://react.dev/learn/react-compiler) — Setup for React Native

---

_Generated by skill-learning. Reload this file to restore expert-level knowledge of React Native._
_Source: web-research_
