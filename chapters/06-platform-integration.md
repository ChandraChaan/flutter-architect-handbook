# 6. Platform integration

[← Performance](05-performance.md) · [Contents](../README.md) · [Next: Security →](07-security.md)

---

### 🟢 `MethodChannel` vs `EventChannel`?

A **MethodChannel** is request and response: Dart calls `invokeMethod('getBattery')` and awaits one result from native code (or the other way round). An **EventChannel** is a stream from native to Dart: sensor readings, Bluetooth notifications, download progress. Both send messages asynchronously through a codec, so they're for coarse-grained calls, not tight loops.

### 🟡 Why use Pigeon instead of hand-written channels?

Hand-written channels pass maps and strings, so a typo in a method name or key only fails at runtime, on one platform. **Pigeon** generates type-safe Dart, Kotlin, Swift (and other) code from one Dart definition, so the compiler catches mismatches on every side. Use it for any channel with more than a couple of methods.

### 🟡 When would you use FFI instead of a channel?

When you call a **C API** (or C-compatible Rust, C++, or system library) directly: image codecs, crypto, databases like SQLite, or heavy computation. `dart:ffi` calls are synchronous and much cheaper than channel messages, with no platform-specific glue code. `ffigen` generates the bindings from C and Objective-C headers, and `jnigen` does the same for Java and Kotlin.

### 🟡 Walk through connecting to a Bluetooth LE device.

1. **Permissions**: Android 12+ needs `BLUETOOTH_SCAN` and `BLUETOOTH_CONNECT` (location only if you derive location from scans); iOS needs `NSBluetoothAlwaysUsageDescription`.
2. **Scan**, filtering by service UUID so you don't list every nearby device.
3. **Connect**, then **discover services**.
4. **Subscribe** to notifications on the characteristic you need, and **parse** the bytes according to the Bluetooth SIG spec (flags byte first, little-endian fields, IEEE 11073 floats).
5. Handle **disconnects** with a reconnect strategy and a visible state in the UI.

Most production bugs are in step 5 and in parsing optional fields that some devices don't send.

### 🟡 How does background work differ between Android and iOS?

Android lets you schedule deferrable work (`WorkManager`) and run foreground services with a visible notification. iOS is much stricter: background fetch and processing tasks run when the system decides, with short time limits, and long-running work is only allowed for specific modes (audio, location, VoIP, BLE accessories). Design features so they don't depend on precise background timing, and use push notifications to wake the app when the server knows something changed.

### 🔴 How do you design a plugin that several apps in a company will share?

- Use the **federated plugin** structure: an app-facing package, a platform interface package, and one implementation package per platform, so platforms can evolve and be tested independently.
- Define the API in Dart first, generate the bridge with Pigeon, and keep native code thin.
- Expose typed errors and streams, not raw platform exceptions.
- Ship a **fake implementation** for tests and an example app that runs on CI.
- Version it with semantic versioning and a changelog. Apps upgrade on their own schedule.

---

[← Performance](05-performance.md) · [Contents](../README.md) · [Next: Security →](07-security.md)
