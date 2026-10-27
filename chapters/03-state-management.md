# 3. State management

[← Flutter internals](02-flutter-internals.md) · [Contents](../README.md) · [Next: Architecture →](04-architecture.md)

---

### 🟢 Ephemeral state vs app state?

**Ephemeral** state belongs to one widget and nobody else cares: the current tab, whether a password is visible, an animation's progress. `setState` in a `StatefulWidget` is the right tool.

**App** state is shared across screens or outlives them: the signed-in user, a cart, cached data, settings. That needs a state management solution. Most over-engineering comes from putting ephemeral state into a global store.

### 🟢 Why is calling an API inside `build()` a bug?

`build` can run many times a second (on every rebuild, rotation, keyboard change), so the request fires again each time. Start async work in `initState`, in a provider or bloc, or in response to an event, and let `build` only read the current state.

### 🟡 Provider vs Riverpod vs BLoC: how do you choose?

| | Provider | Riverpod | BLoC |
|---|---|---|---|
| Model | `InheritedWidget` wrappers | Providers outside the widget tree | Events in, states out (streams) |
| Strength | Simple, familiar | Compile-safe, no `BuildContext` needed, great for async and testing | Very explicit, traceable, enforced structure |
| Cost | Runtime `ProviderNotFoundException`, tied to the tree | New concepts (`ref`, families, auto-dispose) | Boilerplate for small features |
| Good fit | Small apps | Most new apps | Large teams that value strict patterns |

The honest answer in an interview: the library matters less than **consistent rules**. Where does state live, who may change it, how are side effects handled, and how is it tested?

### 🟡 How should loading, data and error be modelled?

As one value with exactly one state at a time, never three separate booleans that can disagree:

```dart
// Riverpod's AsyncValue, or your own sealed class
switch (ref.watch(ordersProvider)) {
  AsyncData(:final value) => OrderList(value),
  AsyncError(:final error) => ErrorView(error),
  _ => const Spinner(),
}
```

Also decide what a **refresh** shows: keeping the old data visible while reloading usually feels better than a full-screen spinner.

### 🟡 Where do side effects like navigation and snackbars go?

Not in `build`, and not in the state object itself (it shouldn't know about `BuildContext`). Listen for state transitions and react in the UI: `ref.listen`, `BlocListener`, or a `ValueListenable` listener. Emit one-off **events** separately from long-lived **state**, so a snackbar doesn't reappear after rotation.

### 🔴 How do you keep state consistent between screens that show the same data?

Have a **single source of truth** per entity, usually a repository or cache layer that screens observe, instead of each screen fetching and holding its own copy. Updates (a "like", an edit) go through that layer, and every listening screen updates. For lists plus details, cache entities by ID, and let the list hold IDs. That avoids stale copies after an edit.

### 🔴 How would you migrate a large app from one state management approach to another?

Never as a big bang:

1. Write down the target pattern and an example feature.
2. Make new features use the new approach. Wrap old state so new code can read it (for example a Riverpod provider that exposes an existing `ChangeNotifier`).
3. Migrate old features when they're touched anyway, starting with the most painful.
4. Track progress (count of old-style files) and delete the bridge when it reaches zero.

---

[← Flutter internals](02-flutter-internals.md) · [Contents](../README.md) · [Next: Architecture →](04-architecture.md)
