# 4. Jetpack Architecture & State — 30 Questions

> ViewModel, Lifecycle, LiveData/StateFlow, Navigation, MVVM/MVI, Clean Architecture, repositories, modularization.
> Reference material: [`../architecture_patterns.md`](../architecture_patterns.md), [`../android.md`](../android.md) Module 4.

---

### Q1. How does a ViewModel survive a configuration change? `[Mid]`

**Answer**
`ViewModelProvider` retrieves ViewModels from a `ViewModelStore` owned by the `ViewModelStoreOwner`. On a configuration change, `ComponentActivity` puts the `ViewModelStore` into `NonConfigurationInstances`, which the framework hands to the recreated Activity. The new instance attaches to the same store and gets the identical ViewModel object.

When the Activity finishes for real (`isFinishing`), the store is cleared and `onCleared()` runs.

**Follow-up:** *So does a ViewModel survive process death?*
> No. It is an in-memory object; process death destroys it along with everything else. Only `SavedStateHandle` (backed by the saved-state Bundle) survives both.

---

### Q2. Why must a ViewModel never hold a reference to a View, Activity, or Context? `[Junior]`

**Answer**
Because the ViewModel outlives them. On rotation the Activity is destroyed but the ViewModel is not — a retained reference leaks the entire Activity and its view hierarchy, and any UI update through it touches a dead window.

If you genuinely need a Context (for resources), inject the **application** context via `AndroidViewModel` or DI, never the Activity context.

**Follow-up:** *You need a string resource inside the ViewModel to build an error message. What is the cleaner approach?*
> Do not resolve strings in the ViewModel. Emit a typed error (`AppError.Offline`) and let the UI map it to a resource. That keeps the ViewModel JVM-testable with no Android dependency and makes the message localizable at the point of display.

---

### Q3. LiveData vs StateFlow vs SharedFlow. `[Mid]`

**Answer**

| | LiveData | StateFlow | SharedFlow |
|---|---|---|---|
| Lifecycle-aware | Built in | No — needs `repeatOnLifecycle` | No |
| Initial value | Not required | **Required** | Not required |
| Conflation | Yes | Yes (and drops equal values) | Configurable replay |
| Operators | Very limited | Full Flow API | Full Flow API |
| Multiplatform | No | Yes | Yes |

**Follow-up:** *Why does `StateFlow` sometimes "miss" an emission?*
> It conflates and compares with `equals`. Emitting the same value twice produces one collection, and a fast producer can drop intermediate values a slow collector never sees. If every value matters, use `SharedFlow` with an appropriate buffer or a `Channel`.

---

### Q4. Why is `repeatOnLifecycle` necessary, and what is wrong with plain `lifecycleScope.launch { flow.collect { } }`? `[Mid]`

**Answer**
`lifecycleScope` is cancelled only at `ON_DESTROY`. A plain collect therefore keeps running while the app is in the background — consuming CPU, network, and battery, and potentially touching a destroyed view.

`repeatOnLifecycle(STARTED)` starts the block when the state is reached, **cancels** it when dropping below, and restarts it on return.

```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        launch { viewModel.state.collect(::render) }
        launch { viewModel.events.collect(::handleEvent) }
    }
}
```

**Follow-up:** *In Compose, what is the equivalent?*
> `collectAsStateWithLifecycle()`, which applies `repeatOnLifecycle` internally. Plain `collectAsState()` has the same background-collection problem.

---

### Q5. Why must one-shot events not be modelled as state? `[Mid]`

**Answer**
State is re-read on every recomposition and after every configuration change. A navigation command or a snackbar stored in state fires again after rotation, sending the user to the same screen twice or repeating a message.

```kotlin
private val _events = Channel<UiEvent>(Channel.BUFFERED)
val events: Flow<UiEvent> = _events.receiveAsFlow()   // Consumed exactly once
```

**Follow-up:** *What about the `Event` wrapper class with a `hasBeenHandled` flag?*
> It works but is error-prone — every collector must remember to check and mark it, and a second collector silently gets nothing. A `Channel` gives the same guarantee structurally, without a manual flag.

---

### Q6. What does `SharingStarted.WhileSubscribed(5_000)` mean and why 5 seconds? `[Mid]`

**Answer**
It keeps the upstream flow active for 5 seconds after the last collector disappears. A configuration change unsubscribes and resubscribes within milliseconds, so the upstream (a database query, a network stream) is not torn down and restarted — but genuinely leaving the screen does stop it.

`Eagerly` runs even with no collectors, wasting resources. `Lazily` never stops once started.

**Follow-up:** *Is 5 seconds magic?*
> No, it is a convention comfortably longer than a rotation and short enough not to matter. The important property is that it exists at all — `Eagerly` on an expensive upstream is the mistake it prevents.

---

### Q7. Explain MVVM vs MVI and what MVI actually fixes. `[Mid]`

**Answer**
Both keep the logic holder ignorant of the View. The difference is state shape.

In MVVM it is common to expose `isLoading`, `items`, and `error` separately — nothing prevents `isLoading = true` **and** `error != null` simultaneously, rendering a spinner over an error.

MVI collapses state into one object, ideally a **sealed hierarchy**, so impossible combinations cannot be constructed, and funnels all user actions through one `onIntent` entry point.

```kotlin
sealed interface UiState {
    data object Loading : UiState
    data class Content(val items: List<Item>, val isRefreshing: Boolean = false) : UiState
    data class Error(val message: String) : UiState
}
```

**Follow-up:** *Is a `data class UiState(val isLoading: Boolean, val error: String?, val items: List<T>)` MVI?*
> Not meaningfully. It still permits every illegal combination. The value of MVI is the sealed hierarchy plus the single intent channel; a flat data class is MVVM with extra naming.

---

### Q8. Where does business logic belong: ViewModel, use case, or repository? `[Senior]`

**Answer**
* **Repository** — data access policy: which source, caching, mapping DTO to domain. No business rules.
* **Use case** — a single business operation, especially one combining several repositories or enforcing a rule ("an order can be cancelled within one hour").
* **ViewModel** — orchestration and presentation: which use case to call, how to shape the result into UI state, and how to handle errors for display.

**Follow-up:** *When is a use case unnecessary?*
> When it is a one-line delegate. `class GetUserUseCase(repo) { operator fun invoke(id) = repo.getUser(id) }` is a file with no reader. Add use cases where logic exists or where several screens share the operation.

---

### Q9. Explain the dependency rule in Clean Architecture. `[Mid]`

**Answer**
Source-code dependencies point **inward only**: presentation → domain ← data. The domain layer knows nothing about Android, Retrofit, or Room.

This works through dependency inversion: the repository **interface** lives in `domain`, the **implementation** in `data`. Data therefore depends on domain, even though data flows outward at runtime.

**Follow-up:** *How do you enforce this so a teammate cannot break it?*
> Put the layers in separate Gradle modules. `domain` declares no dependency on `data`, so an import from `data` into `domain` fails to compile. Without modules the rule is only a convention, and conventions erode.

---

### Q10. What does "single source of truth" mean in the data layer? `[Mid]`

**Answer**
The UI observes exactly one stream — normally the database. The network never writes to the UI; it writes to the database, and the database emits.

```kotlin
override fun observeArticles(): Flow<List<Article>> =
    dao.observeAll().map { it.map(ArticleEntity::toDomain) }   // The ONLY path to the UI

suspend fun refresh(): Result<Unit> = runCatching {
    dao.upsertAll(api.fetch().map { it.toEntity() })           // Emission happens automatically
}
```

**Follow-up:** *Why does this make offline-first almost free?*
> The UI already renders from the database, so with no network it simply shows the last cached data. The only extra work is surfacing the refresh failure without clearing the content.

---

### Q11. How do you handle "show cached data immediately, then refresh, and report a refresh failure without clearing the screen"? `[Senior]`

**Answer**
Model content and refresh state independently, and report the failure as a transient effect.

```kotlin
val uiState = combine(repo.observeArticles(), refreshing, errors) { items, isRefreshing, error ->
    ArticleUiState(items = items, isRefreshing = isRefreshing, error = error)
}.stateIn(viewModelScope, WhileSubscribed(5_000), ArticleUiState())

fun refresh() = viewModelScope.launch {
    refreshing.value = true
    repo.refresh().onFailure { _events.trySend(ShowSnackbar(it.toUserMessage())) }
    refreshing.value = false
}
```
Content stays on screen; the user sees a snackbar rather than an empty state.

**Follow-up:** *What is the common mistake here?*
> Setting state to `Loading` on refresh, which blanks the list the user was reading. Loading is for a cold start with nothing to show; refresh is a property of existing content.

---

### Q12. Explain `Lifecycle` states and events. `[Junior]`

**Answer**
States: `INITIALIZED → CREATED → STARTED → RESUMED`, and `DESTROYED`. Events move between them: `ON_CREATE`, `ON_START`, `ON_RESUME`, `ON_PAUSE`, `ON_STOP`, `ON_DESTROY`.

A `LifecycleObserver` receives these, which lets a component manage its own start/stop instead of the Activity remembering to call it.

```kotlin
class LocationTracker : DefaultLifecycleObserver {
    override fun onStart(owner: LifecycleOwner) = client.requestUpdates(callback)
    override fun onStop(owner: LifecycleOwner) = client.removeUpdates(callback)
}
lifecycle.addObserver(LocationTracker())   // Cleanup is now structurally guaranteed
```

**Follow-up:** *What is `ProcessLifecycleOwner` for?*
> Process-wide foreground/background detection. Its `ON_START` fires when the first Activity starts and `ON_STOP` when the last stops — with a short debounce so a rotation does not register as backgrounding.

---

### Q13. `viewLifecycleOwner` vs `this` in a Fragment. `[Mid]`

**Answer**
The Fragment **instance** lives from `onAttach` to `onDestroy` and survives on the back stack. The Fragment **view** lives from `onCreateView` to `onDestroyView` and is destroyed when the Fragment is back-stacked.

Using `this` for UI observation means the observer outlives the view, holds it in memory, and delivers updates to a detached hierarchy. Always use `viewLifecycleOwner` for anything touching views.

**Follow-up:** *Is there any case where `this` is correct?*
> Yes — observing something that is not view-related and should keep running while back-stacked, such as a long-running upload's completion signal. It is rare; when in doubt, `viewLifecycleOwner`.

---

### Q14. What is type-safe Navigation in Navigation 2.8? `[Mid]`

**Answer**
Routes are `@Serializable` Kotlin types rather than strings, so arguments are checked at compile time and there is no manual URL parsing.

```kotlin
@Serializable data object ProductList
@Serializable data class ProductDetail(val productId: Long, val referrer: String? = null)

NavHost(navController, startDestination = ProductList) {
    composable<ProductList> { ProductListScreen(onClick = { navController.navigate(ProductDetail(it)) }) }
    composable<ProductDetail> { entry -> ProductDetailScreen(entry.toRoute<ProductDetail>().productId) }
}
```

**Follow-up:** *How does the ViewModel read those arguments?*
> `savedStateHandle.toRoute<ProductDetail>()`. Because they live in `SavedStateHandle`, they survive process death without any extra work.

---

### Q15. How do you scope a ViewModel to a multi-screen flow? `[Mid]`

**Answer**
Every `NavBackStackEntry` has its own `ViewModelStore`. Scoping to a nested graph's entry means the ViewModel is created when the flow starts and cleared when the flow is popped.

```kotlin
// Views
private val checkoutVm: CheckoutViewModel by navGraphViewModels(R.id.checkout_graph) {
    defaultViewModelProviderFactory
}

// Compose
val parentEntry = remember(entry) { navController.getBackStackEntry(CheckoutGraph) }
val checkoutVm: CheckoutViewModel = hiltViewModel(parentEntry)
```

**Follow-up:** *Why not just use `activityViewModels()`?*
> It lives for the whole Activity, so an abandoned checkout leaves stale state that reappears when the user starts a new one. Graph scoping gives correct lifetime for free.

---

### Q16. Why should a ViewModel never hold a `NavController`? `[Mid]`

**Answer**
`NavController` is bound to the Activity and its view hierarchy. A ViewModel outlives configuration changes, so holding one leaks the Activity and can navigate on a destroyed controller.

Emit navigation **events**; let the UI, which owns the controller, act on them.

**Follow-up:** *How do you unit-test navigation then?*
> Assert on the emitted event: `assertThat(awaitItem()).isEqualTo(NavigateToDetail(42))`. No Android framework needed, which is precisely the benefit.

---

### Q17. What is `SavedStateHandle` and how does it differ from a ViewModel field? `[Mid]`

**Answer**
A ViewModel field lives in memory and dies with the process. `SavedStateHandle` is backed by the saved-state `Bundle`, written during `onSaveInstanceState` and restored after process death.

It is bounded by the Binder transaction limit, so it holds IDs and small primitives — never lists of models.

**Follow-up:** *What is `getStateFlow` on `SavedStateHandle`?*
> It exposes a saved key as a `StateFlow`, so the value is simultaneously observable and process-death-safe. Writing through `saved["key"] = v` updates both the flow and the saved bundle.

---

### Q18. How do you test a ViewModel? `[Mid]`

**Answer**
Replace the main dispatcher, inject fakes, and assert on emitted state with Turbine.

```kotlin
@get:Rule val mainDispatcher = MainDispatcherRule()

@Test fun `debounce fires one request for three keystrokes`() = runTest {
    viewModel.uiState.test {
        assertThat(awaitItem()).isEqualTo(UiState.Idle)
        viewModel.onQueryChange("ko"); viewModel.onQueryChange("kot"); viewModel.onQueryChange("kotlin")
        advanceTimeBy(301)                              // Virtual time; no real waiting
        assertThat(awaitItem()).isEqualTo(UiState.Loading)
        assertThat(repo.searchCallCount).isEqualTo(1)
        cancelAndIgnoreRemainingEvents()
    }
}
```

**Follow-up:** *Why not just read `viewModel.uiState.value`?*
> It shows only the final value. Intermediate states — Loading, an error that was then replaced — are invisible, and those transitions are usually what the test is actually about.

---

### Q19. Fake vs mock — which for a repository? `[Mid]`

**Answer**
A **fake**, in most cases. A mock (`coEvery { repo.getUser(1) } returns user`) couples the test to how the ViewModel calls the repository; renaming a method or reordering calls breaks tests that should not care.

A fake implements the interface with real in-memory behavior, so it encodes the contract once and survives refactoring.

**Follow-up:** *When is a mock the right tool?*
> When the **interaction itself** is the behavior being tested — "we log this analytics event exactly once", "we do not call the API when offline". Then `verify` is asserting the thing that matters.

---

### Q20. Explain modularization and what actually motivates it. `[Senior]`

**Answer**
Splitting into Gradle modules with explicit dependencies gives:
* **Build speed** — only affected modules rebuild, and independent ones build in parallel.
* **Enforced boundaries** — `:feature:checkout` cannot import `:feature:profile` without declaring the dependency.
* **Ownership** — modules map to teams and CODEOWNERS.
* **Dynamic delivery** — a prerequisite for Play Feature Delivery.

**Follow-up:** *What is the rule that keeps modularization from degenerating?*
> Features must not depend on each other. Cross-feature navigation goes through an interface in `:core:navigation` implemented by `:app`. Direct feature-to-feature dependencies recreate the monolith with extra build files.

---

### Q21. `api` vs `implementation` in Gradle. `[Mid]`

**Answer**
`implementation` keeps the dependency off consumers' compile classpaths, so changing it does not recompile downstream modules. `api` exposes it, so every consumer recompiles.

Use `api` only when a type from that dependency appears in your module's **public** signatures.

**Follow-up:** *What is the practical cost of using `api` everywhere?*
> Every dependency change invalidates the whole downstream graph, so incremental builds degrade toward clean builds. It is one of the most common causes of a slow multi-module build.

---

### Q22. What is a convention plugin and why not `subprojects { }`? `[Senior]`

**Answer**
A convention plugin lives in a `build-logic` included build and encapsulates shared module configuration, applied as one line per module.

`subprojects { }` in the root build script couples every module's configuration together, forces them all to be configured on every build, and **breaks the configuration cache** — which is the largest single build-speed win available on a large project.

**Follow-up:** *What goes in a convention plugin?*
> `compileSdk`, `minSdk`, Java/Kotlin target, common test dependencies, and per-type extras (a `feature` plugin adding `:core:ui`, a `compose` plugin enabling the compiler). Split by concern rather than writing one monolithic plugin.

---

### Q23. Where do mappers belong and why? `[Mid]`

**Answer**
At the boundary — DTO→domain in the data layer, domain→UI model in the presentation layer. This keeps a backend field rename from propagating into the UI, and keeps `@Serializable`/`@Entity` annotations out of the domain.

```kotlin
fun UserDto.toDomain() = User(id = UserId(id), name = fullName, isAdmin = role == "admin")
```

**Follow-up:** *Is a separate UI model always worth it?*
> No. When the domain model is already exactly what the screen renders, an extra model is pure duplication. Introduce one when the screen needs formatting, derived fields, or a shape genuinely different from the domain's.

---

### Q24. What is the Paging 3 architecture? `[Mid]`

**Answer**
* **`PagingSource`** — loads one page from a single source, returning keys for the next and previous pages.
* **`RemoteMediator`** — coordinates network→database so the database can be paged offline.
* **`Pager`** — configuration (`pageSize`, `prefetchDistance`) producing a `Flow<PagingData<T>>`.
* **`PagingDataAdapter`** / `collectAsLazyPagingItems()` — consumes it, exposing `loadState` for headers and footers.

**Follow-up:** *Why does `PagingData` not survive a configuration change by default?*
> It is a stream of loading state, not a snapshot. Call `.cachedIn(viewModelScope)` to multicast it and keep loaded pages across rotation; without it the list reloads from page one.

---

### Q25. Explain the Fragment Result API and why it exists. `[Mid]`

**Answer**
`setFragmentResult(key, bundle)` / `setFragmentResultListener(key) { _, bundle -> }` passes a one-shot result between fragments without either knowing the other's type.

It exists because the alternatives were worse: `setTargetFragment` was deprecated (it holds a hard reference across configuration changes), and an interface implemented by the host Activity coupled both fragments to that host.

**Follow-up:** *Does the result survive process death?*
> Yes — it is stored in the Fragment manager's saved state. But it is delivered only when the receiving Fragment is at least `STARTED`, so a listener registered too late misses nothing but also fires later than you might expect.

---

### Q26. What is `WhileSubscribed` vs `cachedIn` — are they solving the same problem? `[Senior]`

**Answer**
No, though both relate to surviving rotation.
* `stateIn(..., WhileSubscribed(5_000), initial)` keeps an **upstream flow** alive briefly after the last collector, so it is not restarted.
* `cachedIn(viewModelScope)` multicasts a `PagingData` stream and **caches loaded pages**, so the list does not reload.

`WhileSubscribed` is about not restarting work; `cachedIn` is about retaining already-loaded data.

**Follow-up:** *Can you use `stateIn` on a `Flow<PagingData<T>>`?*
> No — `PagingData` is not a value you can hold as state; it is a stream of loading events tied to a collector. `cachedIn` is the correct and only mechanism.

---

### Q27. How would you structure a feature module? `[Senior]`

**Answer**
```
feature/checkout/
├── build.gradle.kts             # id("myapp.android.feature") — one line
└── src/main/kotlin/
    ├── CheckoutRoute.kt         # Public: navigation entry point only
    └── internal/                # Everything else is internal
        ├── CheckoutViewModel.kt
        ├── CheckoutScreen.kt
        └── components/
```
Dependencies: `:domain`, `:core:ui`, `:core:navigation`. Never another feature.

Marking implementation classes `internal` means the module's public surface is exactly its navigation entry point, so consumers cannot accidentally couple to internals.

**Follow-up:** *How does `:app` navigate into this feature without depending on its internals?*
> The feature exposes an extension on `NavGraphBuilder` (`fun NavGraphBuilder.checkoutGraph(onDone: () -> Unit)`) and a route type. `:app` calls that, supplying callbacks for anything that leaves the feature.

---

### Q28. A screen shows the wrong data after the user navigates away and back quickly. What are the likely causes? `[Senior]`

**Answer**
Most likely, in order:
1. **A shared ViewModel scoped too widely** (`activityViewModels`) retaining the previous screen's state.
2. **A `StateFlow` replaying a stale value** before the new load completes — the initial value is the old content rather than a loading state.
3. **A race between two loads**: the first request's response arrives after the second's. Fix with `flatMapLatest`, which cancels the in-flight request.
4. **A LazyColumn/RecyclerView without stable keys**, so remembered state attaches to the wrong rows.

**Follow-up:** *How does `flatMapLatest` prevent the race specifically?*
> When the upstream emits a new value, it cancels the coroutine collecting the previous inner flow before starting the new one. The stale response can never be emitted because its collection no longer exists.

---

### Q29. How do you decide between MVVM and MVI for a new project? `[Senior]`

**Answer**
Decide by screen complexity, not by ideology:
* **Simple screens** (a settings list, a static detail page) — a ViewModel exposing one `StateFlow` of a data class. MVI ceremony adds nothing.
* **Complex screens** (a multi-step form, a screen with many independent async sections, anything with a real state machine) — sealed state plus a single intent entry point pays for itself immediately.

A common and defensible answer: use a sealed `UiState` per screen and an `onEvent(event)` entry point everywhere, without a formal reducer — MVI's benefits with MVVM's ergonomics.

**Follow-up:** *What is the worst outcome?*
> Three patterns across one codebase. Inconsistency costs more than either pattern's weaknesses, because no one can predict where logic lives.

---

### Q30. Design the architecture for a chat app: real-time messages, offline send, read receipts, and pagination. `[Senior]`

**Answer**
**Data layer**
* Room as the single source of truth. `messages` table with a `status` column (`SENDING`, `SENT`, `DELIVERED`, `READ`, `FAILED`).
* WebSocket (or FCM) writes incoming messages into Room; the UI never reads the socket directly.
* **Outbox pattern** for sending: insert locally with `SENDING` and a client-generated UUID, enqueue a WorkManager job, update the row on success. The UI shows the message instantly and offline sending is free.
* Paging 3 with a `RemoteMediator` so history pages from the network into Room and reads back from Room.

**Domain layer**
* `SendMessageUseCase`, `MarkAsReadUseCase`, `ObserveConversationUseCase`.

**Presentation**
* `StateFlow<ConversationUiState>` combining the paged messages, connection status, and typing indicators.
* Read receipts debounced and batched — one API call per burst of visible messages, not one per message.

**Key decisions to state**
* Client-generated message IDs make the send idempotent, so a retry cannot duplicate.
* Room's `Flow` means an incoming socket message updates the UI with no extra plumbing.
* WorkManager, not a coroutine, for sending — it survives process death and retries with backoff.

**Follow-up:** *How do you handle the same message arriving from both the socket and a history page?*
> `@Insert(onConflict = REPLACE)` keyed on the server message ID, with the client UUID as a secondary unique key for reconciling the optimistic local row. The database deduplicates, so no layer above it needs to.

---

## Related

* [`../architecture_patterns.md`](../architecture_patterns.md) — full architecture reference
* [`05_coroutines_concurrency.md`](./05_coroutines_concurrency.md) — Flow operators, structured concurrency
* [`14_scenario_system_design.md`](./14_scenario_system_design.md) — more open-ended design scenarios
* [`00_INDEX.md`](./00_INDEX.md) — full index
