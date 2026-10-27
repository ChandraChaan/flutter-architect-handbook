# 5. Performance

[← Architecture](04-architecture.md) · [Contents](../README.md) · [Next: Platform integration →](06-platform-integration.md)

---

### 🟢 Why `ListView.builder` instead of `ListView(children: ...)`?

`ListView(children: [...])` builds every child up front. `ListView.builder` builds only what's visible (plus a small cache area) and recycles elements while you scroll. For long or unbounded lists the builder is the difference between smooth scrolling and building thousands of widgets at once. Give items a fixed `itemExtent` or `prototypeItem` when you can, so the list doesn't have to measure each one.

### 🟢 How do you measure performance correctly?

Run in **profile mode** (`flutter run --profile`) on a **real, mid-range device**. Debug mode is many times slower and simulators don't match real GPUs. Then use DevTools: the Performance view for frame times on the UI and raster threads, and the CPU profiler for what's slow.

### 🟡 How do images hurt performance, and how do you fix it?

A 4000×3000 photo shown in a 100×100 avatar is decoded at full size: about 48 MB of memory and slow decoding. Fixes:

- Decode at display size: `Image.network(url, cacheWidth: 200)` (account for device pixel ratio).
- Ask the server or CDN for resized images.
- Cache on disk (`cached_network_image` or similar) and precache images you'll need next (`precacheImage`).

### 🟡 What makes the raster thread slow?

Expensive effects: `Opacity` and `ShaderMask` on large subtrees, clips with anti-aliasing, `BackdropFilter`, many shadows, and anything that triggers `saveLayer`. Use `AnimatedOpacity` or `FadeTransition` instead of rebuilding an `Opacity`, use colours with alpha instead of `Opacity` on simple shapes, and wrap independently animating parts in a `RepaintBoundary` so the rest isn't repainted.

### 🟡 How do you improve app startup time?

- Do less before the first frame: move initialisation (analytics, remote config, non-critical SDKs) to after the first frame or to first use.
- Avoid large synchronous work in `main`, such as reading big files or decoding JSON.
- Make the first screen cheap. Show cached content immediately and refresh it.
- Measure with `flutter run --profile --trace-startup`, and track cold start in production analytics.

### 🟡 How do you reduce app size?

Build per-ABI (`--split-per-abi`, or App Bundles on Android), use `--obfuscate --split-debug-info` (smaller and harder to reverse), compress and resize assets, remove unused fonts and packages, and check what's big with `flutter build apk --analyze-size`.

### 🔴 How do you stop performance from regressing?

Make it measurable and automatic:

1. Pick a few **key scenarios** (startup, scrolling the main feed, opening a product).
2. Write **integration tests** that record timelines (`traceAction`) and fail on budgets: frame build time, jank count.
3. Run them on real devices in CI (Firebase Test Lab or a device farm) for release branches.
4. Watch production metrics too: slow and frozen frames, ANR rate, crash-free users.

---

[← Architecture](04-architecture.md) · [Contents](../README.md) · [Next: Platform integration →](06-platform-integration.md)
