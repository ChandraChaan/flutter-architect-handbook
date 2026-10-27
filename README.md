# Flutter Architect Handbook

**From junior developer to Flutter architect: the questions that come up in interviews and on real projects, answered clearly.**

67 questions across 10 chapters, each tagged by level, with short answers, code where it helps, and the trade-offs that separate a good answer from a memorised one.

| Level | Who it's for |
|---|---|
| 🟢 **Junior** | Fundamentals every Flutter developer should know cold |
| 🟡 **Senior** | Trade-offs, production experience, debugging |
| 🔴 **Architect** | Systems, teams, risk and long-term decisions |

## Contents

| # | Chapter | Questions | 🟢 | 🟡 | 🔴 |
|---|---|:-:|:-:|:-:|:-:|
| 1 | [Dart essentials](chapters/01-dart.md): null safety, the event loop, isolates, sealed classes, records, extension types | 9 | 3 | 5 | 1 |
| 2 | [Flutter internals](chapters/02-flutter-internals.md): the three trees, constraints, keys, the frame pipeline, Impeller | 8 | 3 | 4 | 1 |
| 3 | [State management](chapters/03-state-management.md): ephemeral vs app state, Provider vs Riverpod vs BLoC, side effects, migrations | 7 | 2 | 3 | 2 |
| 4 | [Architecture](chapters/04-architecture.md): clean architecture, feature-first folders, DI, repositories, offline, packages, ADRs | 7 | 2 | 3 | 2 |
| 5 | [Performance](chapters/05-performance.md): lists, profiling, images, raster jank, startup, app size, regressions | 7 | 2 | 4 | 1 |
| 6 | [Platform integration](chapters/06-platform-integration.md): channels, Pigeon, FFI, Bluetooth LE, background work, plugins | 6 | 1 | 4 | 1 |
| 7 | [Security](chapters/07-security.md): secure storage, API keys, SSL pinning, obfuscation, FinTech security | 6 | 2 | 3 | 1 |
| 8 | [Testing and delivery](chapters/08-testing-delivery.md): test types, fakes vs mocks, goldens, CI, safe releases | 6 | 1 | 4 | 1 |
| 9 | [Mobile system design](chapters/09-system-design.md): feeds, chat, payments, wearable health sync, poor networks | 5 | 0 | 2 | 3 |
| 10 | [Leading as an architect](chapters/10-leadership.md): wrong decisions, reviews, tech debt, disagreements, growing people | 6 | 0 | 2 | 4 |

## How to use it

- **Preparing for an interview?** Read the chapter for your target level, then answer each question aloud **before** reading the answer. Being able to explain a trade-off matters more than knowing the answer.
- **Leading a team?** Use the 🔴 questions in architecture reviews and the 🟢 and 🟡 questions to plan what juniors learn next.
- **Interviewing candidates?** The answers list what a strong response covers. Look for the trade-offs, not the keywords.

## Related projects

- [flutter-production-starter](https://github.com/ChandraChaan/flutter-production-starter): a template app that puts chapters 3, 4, 7 and 8 into practice
- [ble_gatt_parser](https://github.com/ChandraChaan/ble_gatt_parser) and [ble_device_simulator](https://github.com/ChandraChaan/ble_device_simulator): parse and simulate Bluetooth LE health devices (chapter 6)
- [india_validators](https://github.com/ChandraChaan/india_validators): PAN, GSTIN, Aadhaar, IFSC and UPI validation for Indian apps

## Contributing

Found something wrong or out of date, or have a question that belongs here? Contributions are very welcome. See [CONTRIBUTING.md](CONTRIBUTING.md). Questions and discussion go in [Discussions](https://github.com/ChandraChaan/flutter-architect-handbook/discussions).

If the handbook helps you, a ⭐ helps others find it.

## About the author

Written by [Chandra ObulaReddy](https://chandrachaan.com), a Principal Software Engineer and Flutter architect with 8+ years of building mobile, web and cloud products used by 100K+ people, and of mentoring engineers on the way to senior and architect roles.

## License

[MIT](LICENSE). Share and adapt freely, with credit.
