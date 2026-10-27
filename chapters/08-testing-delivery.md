# 8. Testing and delivery

[← Security](07-security.md) · [Contents](../README.md) · [Next: Mobile system design →](09-system-design.md)

---

### 🟢 Unit, widget and integration tests: what does each cover?

- **Unit**: one class or function, no Flutter. Fast; use them for business logic, parsing and state holders.
- **Widget**: one screen or component in a fake environment (`testWidgets`, `pumpWidget`). Still fast, and the best value in most apps: they check what users see.
- **Integration** (`integration_test`): the real app on a device or emulator. Slow and sometimes flaky; keep them for a few critical journeys like sign-in and checkout.

### 🟡 How do you make code testable?

Inject dependencies through constructors or providers instead of creating them inside the class, depend on interfaces, keep `build` methods free of logic, and pass in sources of time and randomness (a `Clock`, a seeded `Random`) so tests are deterministic. If a test needs a lot of setup, that's usually a design problem, not a testing problem.

### 🟡 Mocks or fakes?

A **fake** is a small, working implementation (an in-memory repository). A **mock** (`mocktail`) records calls and returns scripted answers. Prefer fakes for things many tests use, like repositories: tests read naturally and don't break when internals change. Use mocks to check an interaction actually happened, such as "the analytics event was sent once".

### 🟡 What are golden tests good for?

They render a widget to an image and compare it with a stored "golden" file, catching visual regressions in design-system components. Keep them focused on stable components, render with a fixed font and size, and run them on one platform in CI, because font rendering differs between operating systems.

### 🟡 What should a Flutter CI pipeline do?

On every pull request: `dart format --set-exit-if-changed`, `flutter analyze`, `flutter test` (with coverage), and a build for at least one platform. On the main branch and for releases: build signed artifacts with the release environment, upload debug symbols, and distribute to testers (TestFlight, Play internal testing). Tools: GitHub Actions, Codemagic, Bitrise, plus Fastlane for store steps.

### 🔴 How do you release safely to millions of users?

- **Staged rollouts**: start at 1–5% and watch crash-free users and key metrics before increasing.
- **Feature flags** (remote config) to turn features off without a release, and to ship code dark.
- **Forced update** mechanism for critical fixes or API changes.
- **Monitoring**: crash-free rate (aim for 99.8%+ users), ANRs, slow frames, API error rates, with alerts.
- **Backwards-compatible APIs**: old app versions stay in use for months, so the backend must support them.

---

[← Security](07-security.md) · [Contents](../README.md) · [Next: Mobile system design →](09-system-design.md)
