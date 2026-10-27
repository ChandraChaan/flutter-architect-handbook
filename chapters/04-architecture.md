# 4. Architecture

[← State management](03-state-management.md) · [Contents](../README.md) · [Next: Performance →](05-performance.md)

---

### 🟢 What is "clean architecture" in a Flutter app, in practical terms?

Three layers with dependencies pointing inwards:

- **Presentation**: widgets and state holders. Knows about the domain.
- **Domain**: models and repository **interfaces**, plus business rules. Pure Dart, knows about nothing else.
- **Data**: implementations of those interfaces, talking to HTTP, databases and plugins.

The payoff is replaceability and testability: screens can be tested with a fake repository, and swapping a backend touches one layer. Don't add use-case classes that only forward a call; add a layer when it carries real logic.

### 🟢 Feature-first or layer-first folders?

**Feature-first** (`features/orders/{domain,data,presentation}`) scales better. A change to "orders" stays in one folder, features can be owned by different people, and dead features are easy to delete. Layer-first (`models/`, `screens/`, `services/`) is fine for tiny apps, but turns into folders with hundreds of unrelated files.

### 🟡 How do you handle dependency injection?

Construct dependencies in one place (the composition root, usually `main.dart` or a provider graph), and pass interfaces in. Options:

- **Riverpod** providers: DI and state in one, with overrides for tests.
- **get_it** (plus injectable): a service locator, simple but resolves at runtime.
- Plain **constructor injection**: the most explicit, fine for small graphs.

Whatever you choose, nothing in `domain` or `presentation` should call `http.Client()` or `FirebaseFirestore.instance` directly.

### 🟡 What does a good repository look like?

- It returns **domain models**, never raw JSON or DTOs.
- It throws (or returns) **typed domain errors**, not `DioException` or `SocketException`.
- It hides where data comes from: network, cache, or both. Callers don't care.
- It's an interface with one production implementation and one fake for tests.

### 🟡 How do you design for offline support?

Decide per feature: **read-only cache** (show stale data with a banner), or **offline-first** (the local database is the source of truth and syncs in the background). For offline writes, store pending operations in a queue with client-generated IDs, replay them when online, and make the server operations idempotent so retries can't create duplicates. Decide conflict rules up front: last-write-wins, server-wins, or merge.

### 🔴 When should you split an app into packages?

When the boundaries are already clear and the cost of one big package shows: slow builds, features reaching into each other's internals, or several apps sharing code. Typical splits are `core` (networking, design system), one package per large feature, and shared domain models. Use Dart **pub workspaces** (or Melos) to manage them. Splitting too early creates churn across packages for every change.

### 🔴 How do you document and communicate architecture decisions?

With short **Architecture Decision Records** (ADRs) in the repo: context, decision, alternatives considered, consequences. One page each, numbered, never rewritten (superseded instead). They answer "why on earth did we do this?" two years later, help onboarding, and turn arguments into a reviewable document.

---

[← State management](03-state-management.md) · [Contents](../README.md) · [Next: Performance →](05-performance.md)
