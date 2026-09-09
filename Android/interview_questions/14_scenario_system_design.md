# 14. Scenarios & System Design — 20 Questions

> Open-ended design, debugging, and judgment questions. These test how you reason, not what you memorized.
> There is rarely one right answer — state assumptions, name trade-offs, and justify the decision.

---

### Q1. Design a chat application. `[Senior]`

**Answer**
**Data layer**
* Room is the single source of truth. `messages` has a `status` column (`SENDING`, `SENT`, `DELIVERED`, `READ`, `FAILED`) and a **client-generated UUID** as well as the server ID.
* A WebSocket (or high-priority FCM data messages when backgrounded) writes incoming messages into Room. The UI never reads the socket.
* Sending uses the **outbox pattern**: insert locally with `SENDING`, enqueue a WorkManager job, update on success. Offline sending works with no extra code.
* History pages via Paging 3 with a `RemoteMediator`, so the UI pages from Room.

**Presentation**
* One `StateFlow<ConversationUiState>` combining messages, connection status, and typing indicators.
* Read receipts debounced and batched — one call per burst of visible messages.

**Key decisions to justify**
* Client-generated IDs make sending idempotent, so retries cannot duplicate.
* Room's `Flow` means a socket message updates the UI with no extra plumbing.
* WorkManager rather than a coroutine, because the send must survive process death.

**Follow-up:** *The same message arrives from the socket and from a history fetch. How do you avoid duplicates?*
> `@Insert(onConflict = REPLACE)` on the server message ID, with the client UUID as a secondary key to reconcile the optimistic local row. The database deduplicates, so nothing above it needs to.

---

### Q2. Design an offline-first note-taking app with sync across devices. `[Senior]`

**Answer**
* **Local-first writes**: every edit is written to Room immediately with a `dirty` flag and a `lastModified` timestamp. The UI never waits on the network.
* **Sync**: a WorkManager job pushes dirty notes and pulls changes since a server cursor.
* **Conflict resolution** is the real design question. Options, in increasing order of quality:
  * **Last-write-wins** — simple, silently loses data. Acceptable only for low-value data.
  * **Server-side merge with a conflict copy** — like Dropbox; never loses data, but the user has to reconcile.
  * **CRDT / operational transform** — correct automatic merging for text, but substantial complexity.
* For notes, I would use per-field last-write-wins plus a conflict copy when both sides changed the body, and state that a CRDT is the right answer if collaborative editing is ever required.

**Follow-up:** *Why not just use timestamps for last-write-wins?*
> Device clocks are unreliable and can be wrong by hours. Use a server-assigned version or a logical clock (a vector clock or Lamport timestamp), so ordering does not depend on device time.

---

### Q3. The app is slow to start. Walk me through your investigation. `[Senior]`

**Answer**
1. **Quantify** — `adb shell am start -W`, and Vitals for field data at p90 segmented by device tier. Your device is not the population.
2. **Trace** — Perfetto on a cold start, looking at `Application.onCreate`, `ContentProvider` initialization, the first `inflate`/composition, and the first frame.
3. **Common findings**, in order of frequency:
   * Work in `Application.onCreate` — analytics, SDK initialization, DI graph construction.
   * Auto-initializing `ContentProvider`s from libraries, each instantiated before your first Activity line.
   * Blocking the first frame on network or database.
   * No Baseline Profile, so everything is interpreted or JIT-compiled on first run.
4. **Fix and verify** with Macrobenchmark `StartupTimingMetric` on a release build, 10+ iterations.

**Follow-up:** *If you could only do one thing?*
> Add a Baseline Profile. 20–40% typical improvement, hours of work, no logic change. It is the highest ratio of benefit to risk available.

---

### Q4. Users report the app crashes but you cannot reproduce it. `[Senior]`

**Answer**
1. **Crashlytics** — group by device model, Android version, and app version. A crash concentrated on one OEM or one API level is a strong signal.
2. **Custom keys** — attach device tier, data-set size, feature flags, and the current screen so future reports carry context.
3. **Breadcrumbs** — `Crashlytics.log()` on key transitions, so the trace shows what led there.
4. **`getHistoricalProcessExitReasons`** — distinguish crashes from low-memory kills, which are often reported as "the app closed".
5. **Check the obvious environmental causes**: data scale (a user with 10,000 rows), low-end hardware, slow networks, and process death (test with `adb shell am kill`).
6. **Verify the mapping file was uploaded** — an unreadable trace is often the actual blocker.

**Follow-up:** *The stack trace is entirely framework code with none of yours. What now?*
> That usually means a resource or lifecycle misuse rather than a logic bug — a bad `Bundle`, an inflation failure on a specific configuration, or an OEM-specific framework bug. Group by device and configuration, and look at what the app did just before, using breadcrumbs rather than the trace.

---

### Q5. Design an image-heavy feed like Instagram. `[Senior]`

**Answer**
* **Paging 3** with a `RemoteMediator` so the feed is offline-capable and pages from Room.
* **Coil** with a memory cache at ~25% of the heap and a bounded disk cache, requesting images sized to the actual view.
* **Server-side variants** — request a thumbnail size appropriate to the device, not the original. This matters more than any client optimization.
* **Prefetch** the next page and its images a few items before the end, so scrolling never blocks.
* **Stable keys and content types** in the list so recycled compositions match.
* **Placeholder with the correct aspect ratio** from server-provided dimensions, so the layout does not jump when images load.

**Follow-up:** *Scrolling still stutters on low-end devices. What is left?*
> Decode cost. Use a smaller server variant, consider `RGB_565` for opaque photos, cap concurrent decodes, and confirm with a trace that time is in decode rather than in bind. Also verify a Baseline Profile covers the scroll path.

---

### Q6. A screen occasionally shows stale data. How do you find the cause? `[Senior]`

**Answer**
The likely causes, in order:
1. **A race between two loads** — the first request's response arrives after the second's. `flatMapLatest` fixes it by cancelling the in-flight request.
2. **A ViewModel scoped too widely** (`activityViewModels`) retaining a previous screen's state.
3. **Two sources of truth** — the screen reads from the network on some paths and from cache on others.
4. **A cache with no invalidation** — a write updates the server but not the local store.
5. **`remember` without a key**, so a value derived from a changed parameter goes stale.

**Follow-up:** *How do you make this class of bug structurally impossible?*
> Single source of truth: the UI observes only the database, and every write goes through it. There is then no path by which two parts of the app can disagree.

---

### Q7. Design a video streaming app. `[Senior]`

**Answer**
* **Media3/ExoPlayer** with adaptive streaming (HLS or DASH), so bitrate adapts to bandwidth automatically.
* **Playback in a `MediaSessionService`** foreground service with `foregroundServiceType="mediaPlayback"`, so it survives backgrounding and integrates with the lock screen, Bluetooth, Auto, and Wear.
* **One player instance**, re-pointed at new `MediaItem`s — devices support only a handful of `MediaCodec` instances, so a player per list item eventually breaks all playback.
* **Downloads** via `DownloadManager` (Media3) with a WorkManager-driven queue, plus DRM licence handling for offline.
* **Audio focus** handled (`setAudioAttributes(..., handleAudioFocus = true)`) and `handleAudioBecomingNoisy` so unplugging headphones pauses.
* **Analytics** on startup time, rebuffer ratio, and bitrate — the metrics that define perceived quality.

**Follow-up:** *A user reports playback failing after browsing for a while. What is your first hypothesis?*
> Leaked players. Each holds a `MediaCodec`, and once the device's decoder instances are exhausted every subsequent playback fails. Check that `release()` is called in `onRelease`/`onDispose` for every player created.

---

### Q8. How would you migrate a large View-based app to Compose? `[Senior]`

**Answer**
Incrementally, never as a rewrite.
1. **New screens in Compose** first — no migration risk, and the team learns on greenfield code.
2. **Leaf components next** — a card, a row — hosted via `ComposeView` inside existing layouts.
3. **Whole screens** as they need substantial change anyway. Never migrate a stable screen just to migrate it.
4. **Shared theming** — derive the Compose `MaterialTheme` from the XML theme attributes, or extract both from one token source, so the two systems cannot drift.
5. **Navigation last** — mixing navigation systems is the most disruptive step; do it when most screens are already Compose.

**Non-obvious requirement:** set `ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed` on every `ComposeView` in a Fragment, or back-stacked compositions leak.

**Follow-up:** *How do you justify the migration to a sceptical manager?*
> Not as "Compose is better". Frame it as: new screens are faster to build, the two systems coexist safely, migration happens as a side effect of work already planned, and there is no big-bang risk. If the honest answer is that a stable screen has no reason to change, say so.

---

### Q9. The app's APK grew 8 MB in one release. How do you find out why? `[Mid]`

**Answer**
```bash
apkanalyzer apk compare old.apk new.apk                       # Exact per-entry delta
apkanalyzer files list --exclude-dirs new.apk | sort -k2 -h -r | head -30
apkanalyzer dex packages --defined-only new.apk | sort -k2 -nr | head -20
./gradlew :app:dependencies --configuration releaseRuntimeClasspath > new.txt
diff old.txt new.txt                                          # New transitive dependencies
```
Most common causes: a new dependency pulling a large transitive tree, uncompressed raster assets, a keep rule that disabled shrinking, or `minifyEnabled` accidentally off.

**Follow-up:** *How do you stop it happening again?*
> An APK size budget checked in CI that fails the build on growth beyond a threshold, and quoting the Play Console download size rather than the raw APK so the number reflects what users actually get.

---

### Q10. Design authentication with token refresh, biometrics, and session timeout. `[Senior]`

**Answer**
* **Token storage** — refresh token encrypted with a Keystore key requiring authentication; access token in memory only, so a device compromise while backgrounded yields less.
* **Refresh** — an OkHttp `Authenticator` triggered on 401, with a lock so concurrent 401s cause exactly one refresh, and a retry counter so a failing refresh logs out rather than looping.
* **Biometrics** — `BiometricPrompt` with a `CryptoObject` and `BIOMETRIC_STRONG`, so the key is unusable without authentication. Handle `KeyPermanentlyInvalidatedException` by clearing credentials and forcing re-login.
* **Session timeout** — track last-interaction time; on resume past the threshold, require re-authentication. Re-authenticate again for high-value actions regardless of session age.
* **Logout** — clear the Keystore entry, the database, and the HTTP cache; and revoke the refresh token server-side, since clearing it locally does not invalidate it.

**Follow-up:** *Why is the access token in memory only?*
> It is short-lived, so persisting it adds attack surface for little benefit. If the process dies, the refresh token — which is protected by biometrics — obtains a new one.

---

### Q11. How do you handle a breaking API change from the backend? `[Mid]`

**Answer**
The correct order:
1. **Prevention** — `ignoreUnknownKeys = true` so added fields never break clients, and API versioning so a breaking change ships as `/v2`.
2. **Detection** — contract tests against a recorded or mocked server, run in CI.
3. **Mitigation** — DTOs separate from domain models, so the change is absorbed in the mapper.
4. **Response** — a feature flag (Remote Config) that can disable the affected feature without a release, since a Play rollout takes days.

**Follow-up:** *The change already shipped and old clients are crashing. What do you do first?*
> Fix it server-side — accept the old shape, or version the endpoint by client version. A client fix reaches users over days or weeks; a server fix is immediate. Then ship the client fix and add the contract test that would have caught it.

---

### Q12. Design analytics for a large app. `[Senior]`

**Answer**
* **A typed event schema**, not string literals: a sealed hierarchy of events with typed parameters, so a rename is a compile error and event names cannot drift.
* **A single `Analytics` interface** injected everywhere, with implementations per backend, so switching or adding a provider touches one class.
* **Automatic screen tracking** via a navigation listener, rather than a manual call per screen that someone will forget.
* **Batching** — buffer locally and flush via WorkManager, so events survive process death and offline periods.
* **Privacy** — no PII in parameters, a consent gate before any collection, and a documented retention policy.
* **Validation** — a test asserting every event conforms to the schema, and a debug build that logs events so QA can verify them.

**Follow-up:** *Why is a typed event schema worth the ceremony?*
> String-literal event names diverge silently — `"purchase_complete"` and `"purchase_completed"` become two events in the dashboard and nobody notices for months. A sealed hierarchy makes that impossible, and it documents the schema in code.

---

### Q13. A feature works on your device but fails for 5% of users. How do you narrow it down? `[Senior]`

**Answer**
Segment the failures along every available axis:
* **Device model and OEM** — Chinese OEMs kill background work; some have framework bugs.
* **Android version** — a behavior change gated on `targetSdk` or API level.
* **Locale** — RTL layout bugs, decimal separators, date parsing.
* **Screen size and font scale** — clipped layouts, missed touch targets.
* **Network** — captive portals, IPv6-only carriers, corporate proxies.
* **Data scale** — a user with far more data than any test account.
* **App version** — an incomplete rollout or a migration that only affects upgraders.

Then reproduce on the narrowest matching configuration.

**Follow-up:** *Which axis is most often the answer and least often checked?*
> Data scale and locale. Both are trivially testable — seed 10,000 rows, run with `locale = "ar"` — and both are routinely skipped because the happy path with five English rows always works.

---

### Q14. Design a location-tracking fitness app. `[Senior]`

**Answer**
* **Foreground service** with `foregroundServiceType="location"` and `FOREGROUND_SERVICE_LOCATION`. This is genuinely continuous user-visible work; WorkManager cannot do it.
* **Permissions** — request foreground location first, explain the value, then request background location separately. Requesting both together gets both denied.
* **Batching** — buffer points in Room, flush via WorkManager. A network gap must not lose the run.
* **Battery** — `PRIORITY_BALANCED_POWER_ACCURACY` with `setMinUpdateDistanceMeters` for most activity; high accuracy only when the user is actively recording.
* **Robustness** — the service can still be killed on aggressive OEMs, so persist continuously and reconstruct on restart. Guide the user to the OEM autostart setting when the manufacturer is known to be aggressive.
* **Play policy** — background location requires a video justification and a clear in-app disclosure.

**Follow-up:** *A user's run is missing 20 minutes in the middle. What happened?*
> The process was killed — by the LMK or an OEM battery manager — and the service did not restart. Persist every point as it arrives (never buffer in memory only), use `START_STICKY`, and reconstruct state from the database on restart so a kill costs seconds, not minutes.

---

### Q15. How would you improve the reliability of a flaky test suite? `[Senior]`

**Answer**
1. **Measure** — track flake rate per test. A suite people do not trust provides no safety, so this is the first metric.
2. **Remove the network** — inject fakes with `@BindValue`. External dependencies are the single largest flake source.
3. **Disable animations** — they interact badly with Espresso's idle detection.
4. **Remove `Thread.sleep`** — replace with `waitUntil` or an `IdlingResource`.
5. **Isolate test state** — each test sets up and tears down its own data. Shared state produces order-dependent failures that only appear in CI.
6. **Move tests down the pyramid** — most instrumented tests are testing logic that belongs in a unit test.
7. **Quarantine with a deadline** — a quarantined test is a test that does not exist. Fix it or delete it.

**Follow-up:** *A test fails 1 in 50 runs. Is that acceptable?*
> No. With 200 tests at that rate, most full runs fail somewhere, so people start re-running until green — which means real failures are also re-run away. Flakiness is not a nuisance; it destroys the suite's purpose.

---

### Q16. Design a feature-flag system. `[Mid]`

**Answer**
* **Remote Config** as the source, with **in-app defaults** so the app works on first launch and offline.
* **Typed accessors**, not string lookups: a `FeatureFlags` class exposing `val checkoutV2: Boolean`, so a typo is a compile error.
* **`fetchAndActivate` at startup only**, so flags do not flip mid-session and produce inconsistent behavior across screens.
* **A debug override screen** so QA can force any flag without a server change.
* **Cleanup discipline** — a flag has an owner and a removal date. Permanent flags become permanent complexity, and every combination is a code path nobody tests.

**Follow-up:** *Why not activate flags mid-session?*
> Different screens read flags at different times, so the user can be halfway through a v1 checkout when v2 turns on. Activating only at startup makes behavior consistent for the whole session.

---

### Q17. The team wants to adopt KMP. How do you evaluate it? `[Senior]`

**Answer**
**Evaluate the organization, not just the technology** — KMP fails organizationally more often than technically.

Questions to answer:
* Do Android and iOS genuinely duplicate business logic today, and has it diverged? If not, the main benefit does not apply.
* Will iOS engineers accept reading and debugging Kotlin? If not, it will be resisted regardless of technical merit.
* Who owns the shared module? Without an answer, it becomes nobody's.
* Can you absorb the build-time cost, particularly the Kotlin/Native link step iOS developers feel on every build?

**If yes**, adopt incrementally: models → networking → business logic → storage → presentation. Each step ships independently. Do not start with shared UI.

**Follow-up:** *What would make you say no?*
> A strong existing SwiftUI investment with no logic duplication, a consumer app where iOS platform fidelity is a competitive factor, or an iOS team that has not agreed to it. Any one of those makes the cost exceed the benefit.

---

### Q18. Design the architecture for an app used offline for days at a time. `[Senior]`

**Answer**
* **Everything local-first.** Every screen reads from Room; the network is a background synchronizer, never a UI dependency.
* **An outbox table** for every mutation, with a client-generated idempotency key, ordered, and replayed by WorkManager on reconnect.
* **Conflict policy decided per entity**, not globally — some fields are last-write-wins, some need a server merge, some need user resolution. Say which and why.
* **A sync cursor** so reconnecting after days pulls a delta, not the whole dataset.
* **Bounded storage** — a retention policy deleting old records, and a size cap on cached media, or the device fills up.
* **Honest UI** — show sync state and pending-change counts. Users offline for days need to know what has not been sent.

**Follow-up:** *The user has 400 pending changes and reconnects on a slow network. What happens?*
> They must be sent in order (later changes may depend on earlier ones), in batches, with resumability. Show progress, allow it to continue in the background via WorkManager, and make each batch idempotent so an interrupted sync resumes rather than restarting.

---

### Q19. How do you decide whether to build a feature natively or with a WebView? `[Mid]`

**Answer**
**WebView is reasonable for:** content that genuinely changes faster than release cycles (terms, help centre, promotional pages), and complex third-party flows (some payment providers) that already ship a web SDK.

**Native for:** anything performance-sensitive, anything using device APIs, anything that must work offline, and anything central to the product's feel.

**The costs to state:** WebView is the app's largest security surface (see `addJavascriptInterface`, file access, navigation control), its performance is materially worse, offline support requires extra work, and the bridge between web and native becomes its own maintenance burden.

**Follow-up:** *The business wants the whole checkout in a WebView so it can be updated without a release. What do you say?*
> Name the trade-off rather than refusing: checkout is the highest-value, highest-security flow, so it gets the worst performance and the largest attack surface exactly where it matters most. Offer the alternative — native checkout with Remote Config for the parts that genuinely need to change quickly — which meets the actual requirement without the cost.

---

### Q20. You have inherited a legacy app: no tests, God Activities, no architecture. Where do you start? `[Senior]`

**Answer**
Not with a rewrite. Rewrites of working software fail more often than they succeed, and they stop delivery for months.

**Order of work:**
1. **Instrument first** — Crashlytics, Vitals, and basic analytics. You cannot improve what you cannot measure, and you need to know what actually breaks for users.
2. **Add a safety net where you will change things** — characterization tests around the areas you are about to touch, not a coverage push everywhere.
3. **Establish a pattern and apply it to new code only.** Every new screen uses the target architecture. The codebase improves as it grows.
4. **Refactor opportunistically** — when a screen needs substantial change anyway, migrate it. Never refactor a stable screen that nobody is touching.
5. **Fix the build** — a fast, cached, reliable build is a force multiplier for everything else, and it is usually low-risk work.
6. **Extract the data layer first** if you must pick one structural change. It is the highest-leverage boundary and the least visible to users.

**What to tell stakeholders:** improvements ship alongside features, not instead of them, and progress is measured in crash rate, ANR rate, startup time, and build time — not in "code quality".

**Follow-up:** *The team wants to rewrite it in Compose from scratch. How do you respond?*
> Ask what problem it solves that incremental migration does not. Usually the honest answer is developer experience, which is real but does not justify halting delivery and reintroducing every bug the old code has already fixed. Compose interop exists precisely so this can be incremental — and if after a year most screens are Compose, you have arrived at the same place without the risk.

---

## Related

* [`../architecture_patterns.md`](../architecture_patterns.md) — the patterns these scenarios apply
* [`04_jetpack_architecture.md`](./04_jetpack_architecture.md) — architecture fundamentals
* [`10_performance_memory.md`](./10_performance_memory.md) — the profiling behind the debugging scenarios
* [`00_INDEX.md`](./00_INDEX.md) — full index
