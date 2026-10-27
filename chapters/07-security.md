# 7. Security

[← Platform integration](06-platform-integration.md) · [Contents](../README.md) · [Next: Testing and delivery →](08-testing-delivery.md)

---

### 🟢 Where should tokens and secrets be stored on the device?

In the platform's secure storage: **Keychain** on iOS and **Keystore**-backed encryption on Android (for example via `flutter_secure_storage`). Never in `SharedPreferences`, plain files or logs. Keep access tokens short-lived and refresh them, so a leaked token expires quickly.

### 🟢 Can you keep an API key secret inside a mobile app?

No. Anything shipped in the app, including `--dart-define` values, assets and obfuscated code, can be extracted. Treat app-embedded keys as public: restrict them (by app signature, bundle ID or referrer), and keep real secrets on your server, which the app reaches with the user's own credentials.

### 🟡 What is SSL pinning, and what are the risks?

Pinning makes the app trust only a specific certificate or public key for your API, instead of any certificate the device trusts. That defeats interception by a compromised or user-installed CA on hostile networks.

The risk is **bricking the app**: if the server's certificate changes and the app pins the old one, every request fails until users update. Mitigate by pinning the intermediate CA or public key (not the leaf certificate), shipping a backup pin, monitoring certificate expiry, and having a remote kill switch or a forced-update path.

### 🟡 What does `--obfuscate` protect against, and what doesn't it?

`--obfuscate --split-debug-info=<dir>` renames symbols in compiled Dart code, which makes reverse engineering harder and the app slightly smaller. It doesn't hide strings, assets, API endpoints or business logic from a determined attacker. Keep the debug-info files so you can symbolicate crash reports.

### 🟡 How do you protect sensitive screens?

- Prevent screenshots and screen recording on screens like card details or OTP (Android `FLAG_SECURE`; on iOS, detect capture and blur the content).
- Blur or cover the app in the app switcher.
- Clear sensitive data from memory and text fields when leaving the screen.
- Don't log personal data, and scrub it from crash reports.

### 🔴 How would you approach security for a FinTech app?

Start from a standard: the **OWASP MASVS** for requirements and the MASTG for testing. Then, in layers:

- **Device**: root and jailbreak detection, app attestation (Play Integrity, App Attest) checked **on the server**, since client-side checks can be patched out.
- **Data**: secure storage, encryption for local databases holding personal data, no secrets in the binary.
- **Network**: TLS everywhere, pinning with a rotation plan, and signed requests for critical operations.
- **Auth**: biometric unlock tied to Keystore/Keychain keys, short sessions, step-up authentication for payments.
- **Process**: dependency scanning, regular penetration tests, and a plan for responding to incidents.

---

[← Platform integration](06-platform-integration.md) · [Contents](../README.md) · [Next: Testing and delivery →](08-testing-delivery.md)
