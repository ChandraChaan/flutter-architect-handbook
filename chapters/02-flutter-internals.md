# 2. Flutter internals

[← Dart essentials](01-dart.md) · [Contents](../README.md) · [Next: State management →](03-state-management.md)

---

### 🟢 What are the three trees?

- **Widgets** are cheap, immutable descriptions of UI, rebuilt constantly.
- **Elements** are the long-lived instances that hold a widget's place in the tree, its `State`, and its `BuildContext` (a `BuildContext` *is* an `Element`).
- **RenderObjects** do layout, painting and hit-testing, and are expensive, so Flutter reuses them.

On rebuild, an element compares the new widget with the old one. If `runtimeType` and `key` match, it **updates** the existing element and render object instead of recreating them. That's why rebuilding is cheap and why keys matter.

### 🟢 "Constraints go down, sizes go up, parent sets position." What does that mean in practice?

A parent passes **constraints** (min and max width and height) to each child. The child picks a **size** within them and reports it up. The parent then decides where the child goes.

Most layout bugs come from this: a `ListView` inside a `Column` gets unbounded height and throws, a `Container(width: 100)` inside a tight constraint ignores its width. Ask "what constraints does this widget receive?" before adding `Expanded` or `SizedBox` at random.

### 🟢 When do you need a `Key`?

When widgets of the **same type** move, get inserted or get removed among siblings, and they carry state, such as a reorderable list of text fields. Without keys, Flutter matches by position and the state ends up on the wrong item. Use `ValueKey(item.id)` for data-backed items. `GlobalKey` is for reaching a widget's state or moving it across the tree; it's expensive, so avoid it in lists.

### 🟡 Why does `const` matter for performance?

A `const` widget is the same instance on every build, so Flutter sees the old and new widget are `identical` and skips rebuilding that subtree. Mark leaf widgets `const` wherever you can (the lints will tell you), and pull static parts of a large `build` method into `const` widgets.

### 🟡 What happens between `setState` and pixels on screen?

1. `setState` marks the element **dirty**.
2. On the next frame (vsync), the **build** phase rebuilds dirty elements.
3. **Layout** runs on render objects marked as needing layout.
4. **Paint** records drawing commands into layers. A `RepaintBoundary` isolates a subtree's layer.
5. **Compositing** sends the layer tree to the engine, and the **raster** thread turns it into GPU commands.

Jank on the UI thread means build, layout or paint is too slow. Jank on the raster thread means the scene is too expensive to draw: too many `saveLayer` calls (opacity, clips, shadows), or a huge image. DevTools shows which thread is over budget.

### 🟡 What did Impeller change?

Impeller is Flutter's renderer and the default on iOS and modern Android. It compiles its shaders ahead of time, so the first-run "shader compilation jank" of the older Skia backend is gone. When profiling, check which renderer is active: performance characteristics differ, and some custom shaders or very old devices behave differently.

### 🟡 How do `InheritedWidget` and `BuildContext.dependOnInheritedWidgetOfExactType` work?

An `InheritedWidget` holds data for a subtree. A descendant that calls `dependOnInheritedWidgetOfExactType` (what `Theme.of`, `MediaQuery.of` and Provider use underneath) registers a dependency. When the inherited widget changes and `updateShouldNotify` returns true, **only those dependents** rebuild. This is the foundation most state management libraries build on.

Prefer the narrow accessors like `MediaQuery.sizeOf(context)` over `MediaQuery.of(context)`. They only rebuild your widget when that one property changes, not on every keyboard or padding change.

### 🔴 A screen rebuilds far too often. How do you find and fix it?

1. Turn on **"Track widget rebuilds"** in DevTools (or `debugPrintRebuildDirtyWidgets`) and interact with the screen.
2. Find what triggers the rebuild: usually a provider or `setState` that's too high in the tree, or an `InheritedWidget` dependency that's too broad.
3. Fix by narrowing: move state down to the widget that needs it, select a slice of state (`ref.watch(p.select(...))`, `context.select`), split large `build` methods into widgets, and add `const`.
4. Confirm the fix with the same rebuild counter, not by feel.

---

[← Dart essentials](01-dart.md) · [Contents](../README.md) · [Next: State management →](03-state-management.md)
