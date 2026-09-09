# 11. Testing & Security — 25 Questions

> Testing pyramid, JUnit/MockK/Turbine, Robolectric, Espresso, Compose test, Keystore, TLS, OWASP MASVS.
> Reference material: [`../testing_security.md`](../testing_security.md).

---

### Q1. Describe the testing pyramid for Android and what belongs where. `[Junior]`

**Answer**
* **Unit** (`src/test/`, JVM, milliseconds) — ViewModels, repositories, mappers, use cases. Hundreds to thousands.
* **Integration / Robolectric** (`src/test/`, JVM with a shadowed framework) — DAOs, anything needing `Context`. Tens to hundreds.
* **Instrumented** (`src/androidTest/`, device, seconds each) — genuine end-to-end journeys. Tens.

**Follow-up:** *Why does the shape matter so much on Android specifically?*
> Instrumented tests are 100–1000× slower and far flakier. An inverted pyramid produces a suite that takes 40 minutes and fails randomly, so developers stop trusting it — at which point it provides no safety at all.

---

### Q2. Why does a ViewModel test fail with "Module with the Main dispatcher had failed to initialize"? `[Mid]`

**Answer**
`viewModelScope` uses `Dispatchers.Main`, which has no Android main looper on the JVM. Replace it for the test:

```kotlin
class MainDispatcherRule(
    private val dispatcher: TestDispatcher = StandardTestDispatcher()
) : TestWatcher() {
    override fun starting(d: Description) = Dispatchers.setMain(dispatcher)
    override fun finished(d: Description) = Dispatchers.resetMain()
}
```

**Follow-up:** *Why must `resetMain` run in `finished`?*
> `setMain` is global. Leaving it set leaks the test dispatcher into later tests, producing failures that depend on execution order — the hardest kind to debug.

---

### Q3. What is Turbine and why not just use `toList()`? `[Mid]`

**Answer**
Turbine collects a Flow into a queue with `awaitItem()` / `awaitComplete()` / `awaitError()`, and **fails the test if an emission is left unconsumed**.

`toList()` requires the flow to complete, which a `StateFlow` never does, and it hides intermediate emissions. Turbine tests the sequence of states, which is usually the actual behavior under test.

```kotlin
viewModel.uiState.test {
    assertThat(awaitItem()).isEqualTo(UiState.Idle)
    viewModel.load()
    assertThat(awaitItem()).isEqualTo(UiState.Loading)
    assertThat(awaitItem()).isInstanceOf(UiState.Content::class.java)
    cancelAndIgnoreRemainingEvents()
}
```

**Follow-up:** *Why is "fails on unconsumed emission" valuable?*
> It catches extra emissions — a duplicate Loading, a state emitted twice — that a test reading only the final value would pass through silently.

---

### Q4. Fake, stub, mock, or spy — how do you choose? `[Mid]`

**Answer**
* **Stub** — returns canned data.
* **Fake** — a working lightweight implementation (in-memory repository). **The default choice.**
* **Mock** — records interactions for `verify`.
* **Spy** — a real object with some methods overridden.

Prefer a fake for anything you own: it encodes the contract once and survives refactoring. Use a mock only when the **interaction itself** is the behavior — "we log this event exactly once", "we do not call the API when offline".

**Follow-up:** *Why is `verify(exactly = 1) { repo.load() }` often a bad assertion?*
> It asserts *how* the ViewModel works rather than *what* it produces. Renaming a method, adding a cache check, or reordering calls breaks the test without any behavior change.

---

### Q5. What is virtual time in `runTest`? `[Mid]`

**Answer**
`runTest` uses a `TestCoroutineScheduler` that fast-forwards `delay`. A `delay(30_000)` completes instantly when time is advanced, so testing a 30-second timeout takes microseconds.

* `advanceTimeBy(n)` — advance by n ms.
* `advanceUntilIdle()` — run everything queued.
* `runCurrent()` — run only what is due now.

**Follow-up:** *Why is `Thread.sleep` in a coroutine test always wrong?*
> It blocks real time without advancing the virtual scheduler, so the coroutines you are waiting for still do not run. It makes the test slow and does not fix the timing problem it was added for.

---

### Q6. How do you test a Room DAO fast? `[Mid]`

**Answer**
An in-memory database under Robolectric, on the JVM.

```kotlin
@RunWith(RobolectricTestRunner::class)
class UserDaoTest {
    private val db = Room.inMemoryDatabaseBuilder(
        ApplicationProvider.getApplicationContext(), AppDatabase::class.java
    ).build()

    @Test fun `upsert replaces an existing row`() = runTest {
        db.userDao().upsert(UserEntity(1, "Ada"))
        db.userDao().upsert(UserEntity(1, "Ada Lovelace"))
        db.userDao().observeAll().test {
            assertThat(awaitItem().single().name).isEqualTo("Ada Lovelace")
        }
    }
}
```

**Follow-up:** *What can an in-memory database not test?*
> Migrations — they need `MigrationTestHelper` with a real file. Also anything depending on file-level behavior such as SQLCipher or WAL mode.

---

### Q7. How do you test a Room migration? `[Senior]`

**Answer**
Export schemas (`ksp { arg("room.schemaLocation", ...) }`) and use `MigrationTestHelper`.

```kotlin
@Test fun migrate1To2() {
    helper.createDatabase(TEST_DB, 1).apply {
        execSQL("INSERT INTO user (id, name) VALUES (1, 'Ada')")   // Insert real data
        close()
    }
    val db = helper.runMigrationsAndValidate(TEST_DB, 2, true, MIGRATION_1_2)
    db.query("SELECT age FROM user WHERE id = 1").use {
        it.moveToFirst(); assertThat(it.getInt(0)).isEqualTo(0)
    }
}
```

**Follow-up:** *Why insert data before migrating?*
> A migration that drops and recreates a table passes schema validation while destroying every row. Only asserting on data afterwards catches that.

---

### Q8. Why is `MockWebServer` better than mocking a Retrofit interface? `[Mid]`

**Answer**
Mocking the interface skips serialization, interceptors, and error mapping — exactly the layers where the bugs live. `MockWebServer` runs a real HTTP server on localhost, so the full stack is exercised with a controlled response.

```kotlin
@Test fun `unknown fields do not break parsing`() = runTest {
    server.enqueue(MockResponse().setBody("""{"id":1,"full_name":"Ada","new_field":"x"}"""))
    assertThat(api.getUser(1).fullName).isEqualTo("Ada")
}
```

**Follow-up:** *What other failure modes can you simulate with it?*
> `setResponseCode(500)`, `setBodyDelay` for timeout behavior, `setSocketPolicy(DISCONNECT_AT_START)` for connection failures, and malformed bodies for parser robustness — all things a mocked interface cannot express.

---

### Q9. How does Espresso avoid flakiness, and where does it fail? `[Mid]`

**Answer**
Before each interaction Espresso waits until the main-thread message queue is empty and all registered `IdlingResource`s are idle — so no `sleep` is needed.

It fails for work it cannot see: background coroutines, OkHttp calls, and anything on a thread pool. The robust fix is not an `IdlingResource` but injecting test doubles so the asynchrony is deterministic.

**Follow-up:** *Why disable animations in UI tests?*
> Running animations keep the message queue busy, so Espresso's idle detection interacts badly with them. `testOptions { animationsDisabled = true }`, or the three `adb shell settings put global *_scale 0` commands.

---

### Q10. When do you need UI Automator instead of Espresso? `[Mid]`

**Answer**
When the target is outside your app's process: permission dialogs, the notification shade, system settings, the share sheet, or another app entirely.

```kotlin
val device = UiDevice.getInstance(InstrumentationRegistry.getInstrumentation())
device.findObject(UiSelector().textMatches("(?i)allow|while using the app")).click()
```

**Follow-up:** *Why is UI Automator flakier?*
> It matches on text and resource IDs of UI you do not control, which vary by OEM, Android version, and locale. Keep such tests few and match defensively with regexes.

---

### Q11. How do you test a Compose screen? `[Mid]`

**Answer**
`createComposeRule()` (or `createAndroidComposeRule<Activity>()`) with finders against the **semantics tree**.

```kotlin
compose.onNodeWithText("Wireless Mouse").assertIsDisplayed().performClick()
compose.onNode(hasTestTag("list")).performScrollToNode(hasText("Item 95"))
compose.waitUntil(5_000) {
    compose.onAllNodesWithTag("home").fetchSemanticsNodes().isNotEmpty()
}
```

**Follow-up:** *A test with an infinite animation hangs. Fix?*
> `compose.mainClock.autoAdvance = false`, then drive time with `advanceTimeBy`. The composition is otherwise never idle and `waitForIdle` never returns.

---

### Q12. How do you replace dependencies in an instrumented test with Hilt? `[Mid]`

**Answer**
```kotlin
@HiltAndroidTest
class LoginTest {
    @get:Rule(order = 0) val hilt = HiltAndroidRule(this)
    @get:Rule(order = 1) val compose = createAndroidComposeRule<MainActivity>()

    @BindValue @JvmField val repo: AuthRepository = FakeAuthRepository()
}
```
Rule order matters: Hilt must build the component before the Activity launches.

**Follow-up:** *Why is removing the real network from UI tests the single biggest reliability win?*
> Every test otherwise depends on staging availability, data state, and latency. Injecting fakes makes the tests deterministic and about ten times faster.

---

### Q13. What should code coverage exclude, and what does the number mean? `[Mid]`

**Answer**
Exclude generated code — `*_Hilt*`, `*Binding*`, `R`, `BuildConfig`, databinding — or the number is dominated by thousands of uncovered generated lines.

The number means little on its own. Coverage of getters proves nothing; what matters is that decision logic and **error paths** are covered. Use it to find untested branches, not as a target.

**Follow-up:** *What is the danger of a coverage target in CI?*
> It incentivizes tests that execute code without asserting anything. A test calling every method and asserting nothing gives 100% coverage and zero safety.

---

### Q14. How should an Android CI pipeline be staged? `[Mid]`

**Answer**
| Stage | When | Budget |
|---|---|---|
| Lint + detekt | Every push | < 2 min |
| Unit tests | Every push | < 5 min |
| Instrumented tests | Pull request | < 20 min |
| Screenshot tests | Pull request | < 10 min |
| Build + upload AAB | Tag / main merge | < 15 min |

Cache Gradle, disable animations on emulators, and cancel superseded runs with a concurrency group.

**Follow-up:** *Why run instrumented tests on PRs rather than every push?*
> They dominate CI time and cost. Pushes need fast feedback; the expensive suite belongs at the merge gate, where it is a genuine quality bar rather than a tax on every commit.

---

### Q15. What is the Android Keystore and what does it actually protect? `[Mid]`

**Answer**
Keys are generated and used inside the TEE or a secure element; your app receives a **handle**, never the key material. Crypto operations execute inside the secure hardware.

It protects the **key**, not the plaintext. An attacker with root on a running device can read decrypted values from your process memory.

```kotlin
KeyGenParameterSpec.Builder(alias, PURPOSE_ENCRYPT or PURPOSE_DECRYPT)
    .setBlockModes(BLOCK_MODE_GCM)
    .setEncryptionPaddings(ENCRYPTION_PADDING_NONE)
    .setUserAuthenticationRequired(true)             // Hardware-enforced
    .setInvalidatedByBiometricEnrollment(true)       // New fingerprint destroys the key
    .build()
```

**Follow-up:** *What is `KeyPermanentlyInvalidatedException` and why must you handle it?*
> Changing the screen lock or enrolling a new biometric invalidates the key. Unhandled, the app crashes for that user permanently until reinstall. Catch it, delete the key and the unreadable data, and force re-authentication.

---

### Q16. Why is `BiometricPrompt` without a `CryptoObject` weak? `[Senior]`

**Answer**
Without a `CryptoObject`, the success callback is just a boolean your code trusts — and on a rooted device an attacker can invoke it directly or patch the branch.

With a `CryptoObject`, the Keystore key is **unusable** until the TEE observes a successful authentication. The proof of authentication is the ability to decrypt, not the callback.

**Follow-up:** *Why must you use `BIOMETRIC_STRONG` for this?*
> `BIOMETRIC_WEAK` (some face-unlock implementations) cannot back a `CryptoObject` — the platform will not bind a key to it, because the false-accept rate is too high.

---

### Q17. Where should secrets live in an Android app? `[Mid]`

**Answer**
Nowhere on the client, ideally. `BuildConfig` fields, `strings.xml`, and native `.so` constants are all extractable — `BuildConfig` values are plain strings in the DEX.

If a client-side secret is unavoidable: generate it per-install at runtime and store it in the Keystore. For third-party API keys, proxy the call through your backend so the key never ships.

**Follow-up:** *"But it is obfuscated by R8" — is that an answer?*
> No. R8 renames symbols, not string constants. `strings app.apk | grep -i key` finds them in seconds, and `jadx` produces readable code around them.

---

### Q18. Explain Network Security Config and certificate pinning. `[Mid]`

**Answer**
An XML file declaring the app's TLS policy — applied process-wide, so it covers OkHttp, WebView, and native code alike.

```xml
<base-config cleartextTrafficPermitted="false">
    <trust-anchors><certificates src="system" /></trust-anchors>
</base-config>
<domain-config>
    <domain includeSubdomains="true">api.example.com</domain>
    <pin-set expiration="2027-01-01">
        <pin digest="SHA-256">primary=</pin>
        <pin digest="SHA-256">backup=</pin>   <!-- Mandatory -->
    </pin-set>
</domain-config>
<debug-overrides>
    <trust-anchors><certificates src="system" /><certificates src="user" /></trust-anchors>
</debug-overrides>
```

**Follow-up:** *What is the operational risk of pinning, and what does pinning not protect against?*
> Without a backup pin, a certificate rotation disconnects every installed client and you cannot fix it server-side. And it does not stop a determined attacker on a rooted device — Frida patches it out. Pinning protects **users** from network attackers, not you from the device owner.

---

### Q19. How is HTTPS traffic intercepted during a security test, and why does it fail on modern apps? `[Mid]`

**Answer**
Install a proxy CA (Burp, mitmproxy) on the device and route traffic through it. It fails because:
1. Since **Android 7 (API 24)**, apps do not trust user-installed CAs by default.
2. Certificate pinning rejects the proxy certificate even if the CA is trusted.

Bypasses in a legitimate engagement: a debug build with `<debug-overrides>` trusting user CAs, or Frida to hook the pinning check on a rooted test device.

**Follow-up:** *Why is that "bypass" not a vulnerability report?*
> Both require either the developer's own debug build or full control of a rooted device. Neither is available to a remote attacker against a normal user, so the finding is "pinning can be bypassed with device control" — expected, not a vulnerability.

---

### Q20. What is BOLA and how do you test for it? `[Mid]`

**Answer**
Broken Object Level Authorization: the server returns an object based on an ID in the request without checking that the caller is authorized for **that** object.

Test by authenticating as user A and requesting user B's resource ID. If it returns data, it is BOLA.
```
GET /api/orders/1001   Authorization: Bearer <user A token>   → 200 with user B's order
```

**Follow-up:** *Why is this the most common serious mobile API bug?*
> The mobile client only ever requests its own IDs, so it never surfaces in normal use or in QA. The check is missing on the server and nothing on the happy path reveals it.

---

### Q21. What manifest misconfigurations does a pentester check first? `[Mid]`

**Answer**
```xml
<application
    android:allowBackup="false"          <!-- true allows adb backup extraction -->
    android:debuggable="false"           <!-- true allows run-as and debugger attach -->
    android:usesCleartextTraffic="false"
    android:networkSecurityConfig="@xml/network_security_config">

    <activity android:name=".Internal" android:exported="false" />
    <provider android:exported="true" android:permission="${applicationId}.permission.SYNC" />
</application>
```
Plus: exported components with no permission, `MANAGE_EXTERNAL_STORAGE`, over-broad permissions, and secrets in `strings.xml`.

**Follow-up:** *Why is a debuggable release build so severe?*
> It enables `adb run-as`, giving full read/write access to app-private storage on **any** device, and allows attaching a debugger to inspect and modify memory. It effectively removes the sandbox.

---

### Q22. How much value is there in root detection and anti-tamper checks? `[Senior]`

**Answer**
Limited, and worth stating honestly: the attacker controls the device, so every client-side check can be patched out. Their value is raising cost and filtering low-effort attacks.

The only control with real strength is **server-side verification** of a Play Integrity token. Client heuristics should be reported to the server as **risk signals**, feeding a decision the server makes — never used to hard-exit on device, which just tells the attacker which branch to patch.

**Follow-up:** *Why is blocking every rooted device usually the wrong product decision?*
> It excludes legitimate power users and developers, root detection has false positives, and it is trivially bypassed by the attackers it targets. A risk score that escalates authentication for high-value actions is both more secure and less hostile.

---

### Q23. What are the OWASP MASVS control groups? `[Mid]`

**Answer**
MASVS-**STORAGE**, -**CRYPTO**, -**AUTH**, -**NETWORK**, -**PLATFORM**, -**CODE**, -**RESILIENCE**, -**PRIVACY**. MASVS states the requirements; MASTG gives the test procedures for each.

**Follow-up:** *Which group covers exported components and WebView, and why is it the most commonly failed?*
> MASVS-PLATFORM. It is failed most often because these are ordinary development decisions — an `exported="true"` copied from a sample, `javaScriptEnabled = true` to make a page work — that carry security consequences no build tool flags.

---

### Q24. Why is WebView the most dangerous component in most Android apps? `[Senior]`

**Answer**
It executes remote content inside your app's sandbox. The dangerous combinations:
* `addJavascriptInterface` — page JavaScript can call into your app; historically a direct RCE.
* `allowUniversalAccessFromFileURLs` — a same-origin bypass allowing a local file to read any origin.
* `allowFileAccess` — page content can read app-private files.
* Unrestricted navigation — a compromised page can redirect anywhere.

```kotlin
webView.settings.apply {
    javaScriptEnabled = false
    allowFileAccess = false
    allowUniversalAccessFromFileURLs = false
    mixedContentMode = WebSettings.MIXED_CONTENT_NEVER_ALLOW
}
webView.webViewClient = object : WebViewClient() {
    override fun shouldOverrideUrlLoading(v: WebView, r: WebResourceRequest) =
        r.url.host !in ALLOWED_HOSTS      // true = block
}
```

**Follow-up:** *When is `addJavascriptInterface` acceptable?*
> Only with content you fully control and serve over TLS with pinning, exposing the minimum possible surface with `@JavascriptInterface` on individual methods. For arbitrary web content it should never be enabled.

---

### Q25. Design a testing and security strategy for a banking app. `[Senior]`

**Answer**
**Testing**
* Unit tests for every use case and ViewModel, including error paths — a banking app's error paths *are* the product.
* Property-based tests for money arithmetic (rounding, currency conversion) — the class of bug unit tests with hand-picked values miss.
* Room migration tests for every version, because a failed migration means data loss on real accounts.
* `MockWebServer` contract tests, including 401 refresh, 500, timeout, and malformed responses.
* A small instrumented suite for the critical journeys: login, transfer, logout.
* Screenshot tests for dark theme and 200% font scale.

**Security**
* Keystore-backed encryption with `setUserAuthenticationRequired`, and `KeyPermanentlyInvalidatedException` handled.
* `BiometricPrompt` with a `CryptoObject` and `BIOMETRIC_STRONG` only.
* Network Security Config: no cleartext, pinning with a backup pin and an expiry, system CAs only in release.
* `FLAG_SECURE` on every screen showing balances or card details, blocking screenshots and Recents thumbnails.
* `allowBackup="false"` plus `dataExtractionRules` excluding sensitive files.
* Play Integrity verified **server-side**; client heuristics reported as risk signals only.
* R8 with `repackageclasses` and log stripping; mapping files uploaded.
* No secrets in the client; third-party keys proxied through the backend.
* Session timeout with re-authentication for high-value actions.

**Process**
* Threat model at design time, not a pentest at the end.
* Dependency scanning in CI, plus an annual third-party pentest against MASVS.

**Follow-up:** *If you could only implement three of those, which?*
> Server-side authorization (defeating BOLA), TLS with a correctly-operated pinning setup, and Keystore-backed storage with `FLAG_SECURE`. Those cover the network attacker, the lost-device case, and the most damaging server-side flaw. Everything else is defense in depth on top.

---

## Related

* [`../testing_security.md`](../testing_security.md) — full testing and security reference
* [`05_coroutines_concurrency.md`](./05_coroutines_concurrency.md) — testing coroutines and flows
* [`13_build_release_gradle.md`](./13_build_release_gradle.md) — R8 hardening, signing, CI
* [`00_INDEX.md`](./00_INDEX.md) — full index
