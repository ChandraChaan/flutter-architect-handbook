# 1. Dart essentials

[← Contents](../README.md) · [Next: Flutter internals →](02-flutter-internals.md)

---

### 🟢 What does sound null safety actually guarantee?

If a variable's type is non-nullable (`String`), it can never hold `null` at runtime. The compiler enforces this everywhere, so the checks can be removed from compiled code. `String?` opts back in, and you must handle `null` before using the value (`?.`, `??`, `if (x != null)` promotion, or `!` when you're sure).

The `!` operator is a runtime assertion. Every `!` is a place your app can still crash, so treat them as code smells to justify.

### 🟢 `final` vs `const` vs `late`?

- `final`: assigned once, at runtime.
- `const`: a compile-time constant. Identical `const` objects are canonicalised, so Flutter can skip rebuilding a `const` widget subtree entirely.
- `late`: "I promise this is set before it's read". Reading it too early throws a `LateInitializationError`. It also makes initialisers lazy: `late final x = expensive();` runs on first access.

### 🟢 How does Dart run asynchronous code on one thread?

Each isolate has one thread and an **event loop** with two queues:

1. The **microtask queue**, emptied completely first (`scheduleMicrotask`, completed `Future` callbacks).
2. The **event queue**: I/O, timers, gestures, frame callbacks.

`await` doesn't create a thread. It suspends the function and schedules the rest as a callback. Long synchronous work still blocks the UI.

```dart
print('1');
Future(() => print('4'));            // event queue
scheduleMicrotask(() => print('3')); // microtask queue
print('2');
// 1 2 3 4
```

### 🟡 When do you need an isolate, and what's the cheapest way to use one?

When CPU work takes longer than a few milliseconds on the UI isolate: parsing a large JSON payload, image processing, encryption, diffing big lists. A frame has about 16 ms at 60 Hz (8 ms at 120 Hz).

`Isolate.run` is the simplest way: it spawns, runs a function, returns the result and exits.

```dart
final articles = await Isolate.run(() => parseArticles(hugeJsonString));
```

Isolates don't share memory. Whatever the function captures is copied into the new isolate; the result comes back through `Isolate.exit`, which hands it over without a copy. For repeated work, keep a long-lived isolate and talk to it over `SendPort`, rather than paying the spawn cost each time.

### 🟡 `Future` vs `Stream` vs `Stream.asBroadcastStream`?

A `Future` produces one value or error. A `Stream` produces many over time.

A **single-subscription** stream (most streams, like a file or HTTP body) allows one listener and buffers until it subscribes. A **broadcast** stream allows many listeners but doesn't buffer, so late listeners miss earlier events. Choose based on whether events are a sequence someone must consume in full, or notifications anyone may observe.

### 🟡 How do sealed classes and patterns change error handling?

A `sealed` class can only be extended in its own library, so the compiler knows every subtype and checks that a `switch` covers all of them:

```dart
sealed class Result<T> {}
final class Ok<T> extends Result<T> { Ok(this.value); final T value; }
final class Err<T> extends Result<T> { Err(this.error); final Object error; }

String describe(Result<int> r) => switch (r) {
  Ok(:final value) => 'Got $value',
  Err(:final error) => 'Failed: $error',
}; // Adding a third subtype makes this a compile error until it's handled.
```

That makes states (loading, data, error) and domain errors explicit instead of stringly typed.

### 🟡 Records vs small classes?

Records (`(int, String)`, `({int id, String name})`) are anonymous, immutable, value-equal tuples. They're great for returning several values from a private function or for pattern matching. Use a class when the type has a name in your domain, behaviour, documentation or a public API, because records can't carry methods or evolve without breaking callers.

### 🟡 What are extension types, and when are they useful?

An extension type wraps a representation type with a new static type and **no runtime cost**:

```dart
extension type UserId(String value) {}
void load(UserId id) {}
load(UserId('42'));   // OK
load('42');           // Compile error: a String isn't a UserId
```

Use them to stop IDs, units and tokens from being mixed up. They disappear at runtime, so they can't add safety against values that arrive as `dynamic`.

### 🔴 How would you introduce strict typing into a large, loosely typed codebase?

Turn it on in stages so the team isn't blocked:

1. Enable `strict-casts`, `strict-inference` and `strict-raw-types` in `analysis_options.yaml` for **new packages** first.
2. Fix the shared core (models, networking) next, because every feature benefits.
3. Track the count of `dynamic`, `!` and `// ignore` in CI and make sure it only goes down.
4. Parse JSON at the edge into typed models (or use code generation), so `Map<String, dynamic>` never leaks past the data layer.

---

[← Contents](../README.md) · [Next: Flutter internals →](02-flutter-internals.md)
