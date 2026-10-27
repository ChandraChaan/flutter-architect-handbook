# 9. Mobile system design

[← Testing and delivery](08-testing-delivery.md) · [Contents](../README.md) · [Next: Leading as an architect →](10-leadership.md)

Mobile system design interviews aren't about drawing a backend. They're about how the app behaves on a slow network, with little battery, across versions, at scale. Use this outline for any question:

1. **Clarify** users, core flows, scale, offline needs and constraints.
2. **Sketch** screens, the data they need, and the API between app and server.
3. **Design** the client layers: state, caching, sync, errors.
4. **Go deep** on the hard parts the interviewer cares about.
5. **Cover** the edges: offline, failures, security, observability, rollout.

---

### 🟡 Design an infinite feed (social posts, products, news).

- **Pagination**: cursor-based (`?after=<cursor>`), not page numbers, so new items don't shift pages and cause duplicates.
- **Prefetch** the next page when the user is a few items from the end.
- **Cache** the first page locally so the app opens instantly with content, then refresh.
- **Images**: resized URLs from a CDN, decoded at display size, cached on disk.
- **Updates**: "N new posts" pill instead of jumping the list, keep scroll position across rebuilds.
- **Failures**: an inline retry row at the end of the list, not a full-screen error.

### 🟡 Design a chat feature.

- **Transport**: WebSocket (or MQTT) while in the foreground, push notifications when backgrounded.
- **Local database** as the source of truth: messages render from it; the network fills it.
- **Sending**: write locally with a client-generated ID and a "sending" state, then confirm with the server ID. Retry on reconnect; the client ID makes retries idempotent.
- **Ordering**: by server timestamp or sequence number, not the device clock.
- **Sync**: on reconnect, fetch everything after the last known sequence number.
- **Extras**: typing indicators and read receipts as lightweight events, media uploaded separately with progress.

### 🔴 Design a payments flow in a FinTech app.

- **Idempotency**: every payment request carries an idempotency key created on the device, so a retry after a timeout can't charge twice.
- **State machine**: `created → authorising → pending → success | failed`, persisted locally, so the app can recover if it's killed mid-payment, and reconciled with the server, which is the source of truth.
- **Unknown outcomes**: after a timeout, poll or wait for a webhook-driven push instead of telling the user it failed.
- **Security**: no card data stored on the device (use the gateway's SDK or tokenisation), step-up authentication, pinning, attestation.
- **Observability**: trace every payment with a correlation ID across app, backend and gateway.

### 🔴 Design an app that syncs health data from wearables.

- **Sources**: Apple HealthKit, Health Connect, and direct BLE devices, each behind one `HealthSource` interface.
- **Normalise** everything into one model with units, source and device, and **deduplicate**, because the same steps can arrive from a watch and the phone.
- **Incremental sync**: store a per-source anchor or change token and fetch only what changed.
- **Background**: use the platform's background delivery where available, and catch up on app open otherwise.
- **Upload** in batches with retries and backoff; aggregate on the server.
- **Privacy**: explicit consent per data type, minimal collection, encryption, and a way to delete everything.

### 🔴 How do you design for an app that must keep working on poor networks?

- Short timeouts with retries and exponential backoff with jitter, but only for idempotent requests.
- Smaller payloads: pagination, compression, fields selection, efficient image sizes.
- Optimistic UI for user actions, with a pending queue persisted to disk.
- Show connectivity state, but rely on actual request failures, not on the connectivity API, which can say "connected" without real internet.
- Test on purpose: network link conditioners, airplane-mode toggling, and killing the app mid-request.

---

[← Testing and delivery](08-testing-delivery.md) · [Contents](../README.md) · [Next: Leading as an architect →](10-leadership.md)
