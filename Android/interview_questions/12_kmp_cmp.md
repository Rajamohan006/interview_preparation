# 12. Kotlin Multiplatform & Compose Multiplatform — 20 Questions

> KMP architecture, expect/actual, Skiko rendering, Swift interop, memory model, testing, adoption.
> Reference material: [`../kmp_cmp.md`](../kmp_cmp.md).

---

### Q1. What is KMP and what does it actually share? `[Junior]`

**Answer**
Kotlin Multiplatform compiles one Kotlin codebase to several targets — JVM/Android bytecode, native binaries for iOS via Kotlin/Native, JS/Wasm for web. It shares **logic**, not UI: models, networking, business rules, storage.

Compose Multiplatform is a separate, optional layer that also shares the UI.

**Follow-up:** *How is this different from React Native or Flutter?*
> Those replace the entire UI layer with their own runtime and rendering. KMP compiles to a real native framework that Swift/SwiftUI calls like any other library, so the iOS app stays fully native and adoption is incremental — you can share one repository and nothing else.

---

### Q2. Explain `expect`/`actual`. `[Mid]`

**Answer**
`expect` declares an API in `commonMain` with no body; each target provides an `actual` implementation. The compiler enforces that every target has one.

```kotlin
// commonMain
expect class PlatformStorage() { fun save(key: String, value: String) }

// androidMain
actual class PlatformStorage actual constructor() {
    actual fun save(key: String, value: String) { /* SharedPreferences */ }
}

// iosMain
actual class PlatformStorage actual constructor() {
    actual fun save(key: String, value: String) { NSUserDefaults.standardUserDefaults.setObject(value, key) }
}
```

**Follow-up:** *When is an interface plus DI better than `expect`/`actual`?*
> Almost always, for anything non-trivial. An interface can have several implementations, can be faked in tests, and does not require a compiler-enforced 1:1 mapping. Reserve `expect`/`actual` for genuinely platform-intrinsic things — a UUID generator, a platform name, a file path.

---

### Q3. Describe the KMP source-set hierarchy. `[Mid]`

**Answer**
```
commonMain              # Shared by everything
├── androidMain         # Android; can use the JVM and Android SDK
├── iosMain             # Shared by all iOS targets
│   ├── iosArm64Main    # Device
│   ├── iosX64Main      # Intel simulator
│   └── iosSimulatorArm64Main
└── desktopMain
```
Intermediate source sets (`iosMain`) let several targets share code without duplicating it per architecture.

**Follow-up:** *Why do you need three iOS targets?*
> Device (arm64), Intel simulator (x64), and Apple Silicon simulator (simulatorArm64) are distinct architectures. All three are packed into an XCFramework so Xcode picks the right slice automatically.

---

### Q4. How does Compose Multiplatform render on iOS? `[Senior]`

**Answer**
It does **not** map to UIKit views. The Compose runtime and layout logic are identical to Android; only the drawing backend differs. On iOS, Compose draws through **Skiko** (Kotlin bindings for Skia) into a `CAMetalLayer` backed by Metal.

The whole Compose UI is therefore one native view containing custom-drawn content, synchronized to `CADisplayLink` for VSync.

**Follow-up:** *What are the consequences of not using UIKit views?*
> Platform fidelity is approximated rather than inherited — iOS-specific behaviors like text selection callouts, accessibility integration, and system keyboard interactions must be implemented rather than coming for free. It also means UI updates do not automatically match iOS version changes.

---

### Q5. What is Ktor and why is it used instead of Retrofit? `[Junior]`

**Answer**
Retrofit is JVM-only. Ktor Client is multiplatform, with a pluggable engine per target — OkHttp on Android, Darwin (NSURLSession) on iOS, CIO or Js elsewhere.

```kotlin
fun createClient(engine: HttpClientEngine) = HttpClient(engine) {
    install(ContentNegotiation) { json(json) }
    install(HttpTimeout) { requestTimeoutMillis = 30_000 }
    install(HttpRequestRetry) { retryOnServerErrors(3); exponentialDelay() }
}
```

**Follow-up:** *Why must serialization be kotlinx.serialization?*
> Gson and Moshi rely on JVM reflection, which Kotlin/Native does not have. kotlinx.serialization is compiler-plugin based, generating serializers at build time, so it works on every target.

---

### Q6. SQLDelight vs Room for KMP. `[Mid]`

**Answer**
Both work multiplatform now. SQLDelight is SQL-first: you write `.sq` files and it generates typed Kotlin APIs, with the driver differing per platform (`AndroidSqliteDriver`, `NativeSqliteDriver`). Room's multiplatform support is newer, and is attractive when an existing Android codebase already uses it.

SQLDelight's advantage is that the SQL is explicit and verified; Room's is familiarity and the existing migration path.

**Follow-up:** *What is the threading gotcha on iOS?*
> SQLDelight queries must be mapped on a background dispatcher — `.asFlow().mapToList(Dispatchers.IO)`. Doing it on the main dispatcher blocks the iOS UI thread, and unlike Android there is no StrictMode to warn you.

---

### Q7. Why is Koin used for DI in KMP rather than Hilt? `[Mid]`

**Answer**
Hilt depends on Android component lifecycles and annotation processing over Android types — it cannot compile for `commonMain` or iOS. Koin is pure Kotlin with no codegen, so its DSL lives in `commonMain` and platform bindings come from an `expect fun platformModule(): Module`.

**Follow-up:** *How does Swift resolve a dependency from Koin?*
> Not through `by inject()` — reified inline extensions do not export to Objective-C. Expose plain functions from a `KoinComponent` object: `object KoinHelper : KoinComponent { fun repo(): UserRepository = get() }`.

---

### Q8. What are the main Kotlin→Swift interop constraints? `[Senior]`

**Answer**
Kotlin exports to Objective-C, and Swift consumes that, so anything Objective-C cannot express is lost:
* **Generics** are largely erased — `List<User>` arrives as `NSArray`.
* **Sealed classes** become plain classes, so Swift `switch` is not exhaustive.
* **Default arguments** do not exist; every call must pass all parameters.
* **`suspend` functions** become completion-handler methods.
* **Flows** are not directly consumable.
* Name mangling: `init` becomes `doInit`, and clashes get prefixes.

**Follow-up:** *What does SKIE do about this?*
> It generates a Swift layer on top: sealed classes become real Swift enums with exhaustive `switch`, `Flow` becomes `AsyncSequence`, default arguments are preserved, and suspend functions become `async` throwing functions. It substantially improves the iOS developer experience and is close to standard in serious KMP projects.

---

### Q9. How do you consume a Kotlin `Flow` from Swift? `[Senior]`

**Answer**
With SKIE it becomes a Swift `AsyncSequence`:

```swift
@MainActor
class ProductObservable: ObservableObject {
    @Published var state = ProductListState.companion.initial
    private let viewModel = ProductListViewModel()
    private var task: Task<Void, Never>?

    func start() {
        task = Task { for await value in viewModel.state { self.state = value } }
    }
    // Cancelling is MANDATORY — otherwise the Kotlin collection leaks
    func stop() { task?.cancel() }
}
```
Without SKIE, use KMP-NativeCoroutines or hand-write a wrapper exposing a cancellable callback subscription.

**Follow-up:** *What happens if the Swift side never cancels?*
> The Kotlin coroutine keeps collecting forever, holding the ViewModel and everything upstream. There is no Android-style lifecycle to save you — cancellation is entirely the Swift caller's responsibility.

---

### Q10. Explain Kotlin/Native's memory model. `[Senior]`

**Answer**
The current (new) memory manager removes the old freezing/immutability restrictions: objects can be shared across threads freely and concurrency works like the JVM's.

Memory is managed by a **tracing GC** on the Kotlin side, but objects crossing into Objective-C participate in **ARC** reference counting. The consequence is that a reference cycle spanning both worlds — a Kotlin object holding a Swift closure that captures back into Kotlin — is collected by neither.

**Follow-up:** *How do you avoid those cycles?*
> `[weak self]` in Swift closures passed to Kotlin, and explicitly clearing Kotlin-side callback references when the Swift owner deinitializes. Xcode Instruments' leak tooling sees the ARC side; the Kotlin side needs explicit teardown.

---

### Q11. What is an XCFramework and why is it the distribution format? `[Mid]`

**Answer**
An XCFramework is a bundle containing compiled binaries for **multiple architectures and platforms** (device arm64, simulator x64, simulator arm64), so Xcode selects the correct slice automatically.

The older fat-framework approach could not contain both device arm64 and simulator arm64, because they are the same architecture for different platforms.

```kotlin
kotlin {
    val xcf = XCFramework()
    listOf(iosX64(), iosArm64(), iosSimulatorArm64()).forEach {
        it.binaries.framework { baseName = "Shared"; xcf.add(this) }
    }
}
```

**Follow-up:** *CocoaPods or SPM for integration?*
> SPM is Apple's direction and needs no Ruby toolchain, but requires publishing the XCFramework somewhere resolvable. CocoaPods integrates more smoothly with a local Gradle build during development. Many teams use CocoaPods locally and SPM for consuming published releases.

---

### Q12. How do you write tests that run on every KMP target? `[Mid]`

**Answer**
Put them in `commonTest` using `kotlin.test`, `kotlinx-coroutines-test`, and Ktor's `MockEngine`.

```kotlin
class UserRepositoryTest {
    @Test fun `parses a user identically on every platform`() = runTest {
        val engine = MockEngine { respond("""{"id":1,"full_name":"Ada"}""",
            headers = headersOf(HttpHeaders.ContentType, "application/json")) }
        assertEquals("Ada", UserRepositoryImpl(createClient(engine)).getUser(1).name)
    }
}
```
```bash
./gradlew :shared:jvmTest                  # Fast local loop
./gradlew :shared:allTests                 # Full matrix, in CI
```

**Follow-up:** *Why is running only `jvmTest` insufficient?*
> Kotlin/Native has different concurrency and memory semantics, and some library behavior differs by engine. Bugs that exist only on iOS are real and only `iosSimulatorArm64Test` finds them.

---

### Q13. Why do MockK and Robolectric not work in `commonTest`? `[Mid]`

**Answer**
Both depend on JVM facilities — bytecode manipulation and the JVM class loader — which Kotlin/Native does not have. `commonTest` must use hand-written fakes and library-provided test doubles like Ktor's `MockEngine`.

**Follow-up:** *Is that actually a problem?*
> It is a mild constraint that tends to improve the tests. Hand-written fakes couple to the contract rather than to the call sequence, so they survive refactoring better than mocks do.

---

### Q14. Can you share ViewModels across platforms? `[Mid]`

**Answer**
Yes — `androidx.lifecycle.ViewModel` and `viewModelScope` are multiplatform artifacts. The shared ViewModel exposes a `StateFlow`, and each platform binds it: `collectAsStateWithLifecycle` on Android, an `ObservableObject` bridge on iOS.

**Follow-up:** *What does iOS lose compared with Android?*
> Automatic lifecycle-scoped cancellation. On Android `viewModelScope` is cleared by the framework; on iOS the Swift side must call `onCleared()` (or `clear()`) itself when the SwiftUI view disappears, or the ViewModel and its collections live on.

---

### Q15. What should you share first, and what last? `[Senior]`

**Answer**
In order of value-to-risk:
1. **Models and DTOs** — pure Kotlin, immediate duplication removed.
2. **Networking** — endpoint definitions and error mapping are identical by nature.
3. **Business logic / use cases** — the highest-value layer, where divergence is most damaging.
4. **Local storage** — schema and queries identical, driver platform-specific.
5. **Presentation / ViewModels** — good value, needs a Swift bridge.
6. **UI (Compose Multiplatform)** — last, and optional.

**Follow-up:** *Why is starting with shared UI the classic mistake?*
> It is the highest-risk, highest-visibility layer, so any rough edge is immediately attributed to KMP by the whole team — and it is exactly the layer where iOS users notice non-native behavior. Starting with the data layer produces an unambiguous win that builds trust.

---

### Q16. When is Compose Multiplatform for iOS a good choice, and when not? `[Senior]`

**Answer**
**Good:** internal and enterprise apps, content-heavy apps with custom design systems, apps where a brand-specific UI already diverges from platform conventions, and small teams where a single UI implementation is the difference between shipping and not.

**Poor:** consumer apps where iOS platform fidelity is a competitive factor, apps depending heavily on native iOS integrations (widgets, Live Activities, deep system UI), and teams with a strong existing SwiftUI investment.

**Follow-up:** *What are the concrete costs to state?*
> Framework binary size, weaker accessibility integration than native SwiftUI, text input and selection behaviors that need extra work, and a smaller ecosystem for iOS-specific UI problems.

---

### Q17. How do you handle platform-specific UI inside CMP? `[Mid]`

**Answer**
`expect`/`actual` composables, so shared screens delegate the platform-specific piece.

```kotlin
// commonMain
@Composable expect fun PlatformDatePicker(onDate: (LocalDate) -> Unit)

// androidMain — Material3
// iosMain — a UIKit interop wrapper around UIDatePicker via UIKitView
```

**Follow-up:** *How does CMP embed a native iOS view?*
> `UIKitView` / `UIKitViewController` composables, which host a native view inside the Compose hierarchy — the iOS equivalent of `AndroidView`. Useful for maps, camera previews, and platform pickers.

---

### Q18. How do you manage resources in CMP? `[Mid]`

**Answer**
`compose.components.resources` generates a typed `Res` object from `composeResources/`, giving compile-checked access to strings, drawables, and fonts across all targets.

```kotlin
Image(painterResource(Res.drawable.logo), contentDescription = null)
Text(stringResource(Res.string.welcome_message, userName))
```

**Follow-up:** *How does localization work compared with Android?*
> The same qualifier idea: `composeResources/values-es/strings.xml`. The generated `Res` resolves per the platform's current locale, so one mechanism covers Android, iOS, and desktop.

---

### Q19. What are the main organizational risks of adopting KMP? `[Senior]`

**Answer**
KMP fails organizationally more often than technically:
* **iOS developers cannot debug the shared module.** Xcode shows Kotlin frames poorly and crashes need symbolication. If the iOS team cannot investigate a bug, they will resist the whole approach.
* **Ownership ambiguity** — who owns `shared`? Without a clear answer it becomes nobody's.
* **Build times** — the Kotlin/Native link step is slow, and iOS developers feel it on every build.
* **Hiring** — a smaller pool than either native stack.

**Follow-up:** *How do you mitigate the debugging problem?*
> Xcode Kotlin debugging support plus a strict rule that shared code has good `commonTest` coverage and clear error types, so most failures are diagnosable from a stack trace and a log rather than from stepping through Kotlin in Xcode.

---

### Q20. Design a KMP architecture for an app with existing Android and iOS codebases. `[Senior]`

**Answer**
**Module structure**
```
shared/
├── commonMain/     models, Ktor client, repositories, use cases, ViewModels
├── androidMain/    OkHttp engine, AndroidSqliteDriver, Android Context bindings
└── iosMain/        Darwin engine, NativeSqliteDriver, NSUserDefaults bindings
androidApp/         Compose UI, Hilt or Koin, Android-specific features
iosApp/             SwiftUI, SKIE-generated bridges
```

**Technology choices to state**
* **Ktor** + **kotlinx.serialization** — the only multiplatform option.
* **SQLDelight** — mature on both platforms.
* **Koin** — Hilt cannot compile for iOS.
* **SKIE** — makes sealed classes exhaustive and Flows into `AsyncSequence` in Swift, which is the difference between a pleasant and a painful iOS experience.
* **`androidx.lifecycle.ViewModel`** shared, bound per platform.

**Adoption sequence**
1. Extract models and DTOs; ship. Both apps still have their own everything else.
2. Move networking; ship.
3. Move business logic and repositories; ship.
4. Move ViewModels behind a SKIE bridge; ship.
5. Consider CMP for new screens only, never as a rewrite.

Each step is independently shippable, so the migration never blocks feature work.

**Non-negotiables**
* `commonTest` coverage before moving any logic, so a regression is caught in the shared layer.
* `allTests` in CI, not just `jvmTest`.
* Clear ownership of `shared`, with both platform teams able to contribute.

**Follow-up:** *After all that, what stays native?*
> Everything platform-intrinsic: navigation, widgets, notifications, biometrics, camera, and — in this plan — the UI itself. That is the point of KMP rather than a cross-platform framework: you share what benefits from being shared and keep native where native wins.

---

## Related

* [`../kmp_cmp.md`](../kmp_cmp.md) — full KMP/CMP reference
* [`09_compose.md`](./09_compose.md) — the Compose runtime CMP reuses unchanged
* [`05_coroutines_concurrency.md`](./05_coroutines_concurrency.md) — coroutines and Flow, which KMP depends on
* [`00_INDEX.md`](./00_INDEX.md) — full index
