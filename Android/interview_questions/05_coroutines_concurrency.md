# 5. Coroutines, Flow & Concurrency — 30 Questions

> Coroutine internals, structured concurrency, dispatchers, cancellation, exceptions, Flow operators, JVM primitives.
> Reference material: [`../android.md`](../android.md) Module 5, [`../../Languages/Kotlin.md`](../../Languages/Kotlin.md).

---

### Q1. What is a coroutine and how is it different from a thread? `[Junior]`

**Answer**
A thread is an OS-scheduled execution context costing ~1 MB of stack. A coroutine is a **compiler-generated state machine** that runs *on* a thread and can suspend without blocking it — thousands can share a small pool.

Suspending releases the thread; blocking holds it.

**Follow-up:** *Can you have 100,000 coroutines but not 100,000 threads?*
> Yes. Each coroutine is a small heap object; 100,000 of them are trivially fine. 100,000 threads would need ~100 GB of stack and would spend all their time in context switches.

---

### Q2. How does the compiler implement `suspend`? `[Senior]`

**Answer**
Continuation-Passing Style. Every `suspend fun` gains a hidden `Continuation<T>` parameter, and its body is rewritten into a state machine with a `label` field marking the current suspension point.

```kotlin
suspend fun load(): User {
    val id = fetchId()          // suspension point 1
    return fetchUser(id)        // suspension point 2
}
// Conceptually becomes a class with label = 0, 1, 2; locals stored as fields.
// At a suspension point it saves state, returns COROUTINE_SUSPENDED, and frees the thread.
// The callback later calls resumeWith(), which re-enters at the saved label.
```

**Follow-up:** *Why does this mean a suspend function has almost no runtime cost when it does not actually suspend?*
> If the callee completes without suspending, it returns the value directly rather than `COROUTINE_SUSPENDED`, and the state machine falls straight through to the next label. There is no thread handoff and no allocation beyond the continuation object itself.

---

### Q3. `launch` vs `async`. `[Junior]`

**Answer**
* `launch` returns a `Job`, produces no value, and throws uncaught exceptions **immediately** to the parent / `CoroutineExceptionHandler`.
* `async` returns a `Deferred<T>`, produces a value via `await()`, and **stores** its exception until `await()` is called.

```kotlin
// Parallel decomposition: both start now, both awaited together
val page = coroutineScope {
    val user = async { repo.user() }
    val feed = async { repo.feed() }
    Page(user.await(), feed.await())
}
```

**Follow-up:** *What happens to an exception in an `async` whose result is never awaited?*
> It is silently swallowed (unless the scope is a plain `Job`, in which case it still cancels the parent). If you do not need the value, use `launch` — an unawaited `async` is a bug waiting to hide a failure.

---

### Q4. What is structured concurrency? `[Mid]`

**Answer**
Every coroutine has a parent. Cancelling the parent cancels all children; the parent does not complete until all children do; and by default a child's failure cancels the parent and its siblings.

The practical consequence: `viewModelScope` cancellation is total. No coroutine can outlive the screen that started it, so leaked background work becomes structurally impossible.

**Follow-up:** *What breaks structured concurrency?*
> `GlobalScope.launch` — it has no parent, so nothing ever cancels it. If you genuinely need app-lifetime work, inject a scope built from `SupervisorJob() + Dispatchers.Default` so it is at least owned and testable.

---

### Q5. `Job` vs `SupervisorJob`. `[Mid]`

**Answer**

| | `Job` | `SupervisorJob` |
|---|---|---|
| A child fails | Cancels the parent **and all siblings** | Only that child fails |
| Used by | `coroutineScope { }`, plain `launch` | `viewModelScope`, `lifecycleScope`, `supervisorScope { }` |
| Right for | All-or-nothing work | Independent tasks |

```kotlin
// All three must succeed → coroutineScope
coroutineScope { val a = async { x() }; val b = async { y() }; combine(a.await(), b.await()) }

// Independent sections → supervisorScope
supervisorScope {
    launch { runCatching { loadHeader() } }
    launch { runCatching { loadFeed() } }     // Header failing must not blank the feed
}
```

**Follow-up:** *Does `SupervisorJob` help if you pass it to a child `launch`?*
> No. Supervision is a property of the **scope**, determined by the parent's job. `launch(SupervisorJob())` creates a child whose parent is that new job, detaching it from the real scope — a common and subtle bug.

---

### Q6. Why is cancellation cooperative, and how do you cooperate? `[Mid]`

**Answer**
`cancel()` only sets `isActive = false` and arranges for `CancellationException` to be thrown at the **next suspension point**. A tight CPU loop with no suspension point ignores cancellation completely.

```kotlin
suspend fun compress(frames: List<Frame>) = withContext(Dispatchers.Default) {
    frames.map { frame ->
        ensureActive()          // Throws if cancelled — the cooperation point
        encode(frame)
    }
}
```
`yield()` also works and additionally gives other coroutines a turn.

**Follow-up:** *Your cleanup code in `finally` throws `CancellationException` and never runs. Why?*
> Any suspending call in a cancelled coroutine throws immediately. Wrap the cleanup in `withContext(NonCancellable) { }` so it is permitted to suspend.

---

### Q7. Why is catching `Exception` around a suspending call dangerous? `[Senior]`

**Answer**
`CancellationException` is an `Exception`. Swallowing it means the coroutine believes it is still running after being cancelled — it keeps working, may write to a dead UI, and can prevent its parent scope from completing.

```kotlin
try { repo.load() }
catch (e: CancellationException) { throw e }    // ALWAYS rethrow
catch (e: IOException) { showOffline() }
```
`runCatching` has the same problem — it catches `Throwable`.

**Follow-up:** *How do you use `runCatching` safely?*
> Rethrow explicitly: `runCatching { ... }.onFailure { if (it is CancellationException) throw it }`. Or avoid it in suspending code and catch specific types.

---

### Q8. Explain the dispatchers and when to use each. `[Junior]`

**Answer**

| Dispatcher | Backing | Use for |
|---|---|---|
| `Main` | Android main looper | UI updates |
| `Main.immediate` | Same, no re-dispatch if already on main | Avoiding an unnecessary post |
| `IO` | Elastic pool, 64 threads by default | Network, disk, database |
| `Default` | Pool sized to CPU cores | Parsing, sorting, image processing |
| `Unconfined` | Caller's thread until first suspension | Testing and advanced cases only |

**Follow-up:** *Why is `Dispatchers.IO` 64 threads while `Default` is core-count?*
> IO-bound work spends its time blocked, so more threads mean more concurrent in-flight operations. CPU-bound work gains nothing from more threads than cores — extra threads only add context switches.

---

### Q9. Do you need `withContext(Dispatchers.IO)` around a Retrofit or Room suspend call? `[Mid]`

**Answer**
No. Retrofit dispatches on its own executor and Room's suspend DAOs use their own query executor. Both are **main-safe** by contract. Adding `withContext(Dispatchers.IO)` costs an extra context switch for nothing.

The rule: a well-designed suspending API is main-safe. Add `withContext` only around code you wrote that blocks.

**Follow-up:** *What about a Room DAO that is not a suspend function?*
> A blocking DAO method genuinely blocks the calling thread and needs `withContext(Dispatchers.IO)`. Room throws by default if you call one on the main thread, unless `allowMainThreadQueries()` was enabled — which it should not be outside tests.

---

### Q10. `coroutineScope` vs `supervisorScope` vs `withContext`. `[Mid]`

**Answer**
* `withContext(dispatcher)` — switches context, suspends until the block completes, returns its value. Not for concurrency.
* `coroutineScope { }` — creates a child scope for concurrent children; waits for all; a failure cancels the rest.
* `supervisorScope { }` — same, but children fail independently.

**Follow-up:** *Why is `withContext(Dispatchers.IO)` inside a loop a mistake?*
> Each call is a context switch. Wrap the loop, not the body: `withContext(IO) { items.forEach { process(it) } }`.

---

### Q11. Cold vs hot flows. `[Mid]`

**Answer**
A **cold** flow (`flow { }`) re-executes its builder for each collector, and does nothing with no collector. A **hot** flow (`StateFlow`, `SharedFlow`, `Channel`) exists independently and shares one emission stream across collectors.

**Follow-up:** *You call `repository.observeUsers()` from two composables and see two database queries. Why?*
> It is a cold flow, so each collector triggers its own upstream. Convert it once with `shareIn`/`stateIn` in the ViewModel so both collectors share one upstream.

---

### Q12. What does `flowOn` do, and why can you not use `withContext` inside `flow { }`? `[Senior]`

**Answer**
A flow must emit in the context in which it is collected — the **context preservation** invariant. `withContext` inside `flow { }` violates it and throws `IllegalStateException: Flow invariant is violated`.

`flowOn(dispatcher)` is the sanctioned mechanism: it changes the context of everything **upstream** of it, while downstream operators and the collector stay in the collector's context.

```kotlin
flow { emit(readFile()) }       // Runs on IO
    .map { parse(it) }          // Runs on IO — upstream of flowOn
    .flowOn(Dispatchers.IO)
    .collect { render(it) }     // Runs in the collector's context (Main)
```

**Follow-up:** *Why does this invariant exist at all?*
> So a collector can reason about where its code runs. Without it, an operator deep in a library's flow could silently move your `collect` block off the main thread.

---

### Q13. `flatMapLatest` vs `flatMapMerge` vs `flatMapConcat`. `[Mid]`

**Answer**
* `flatMapLatest` — cancels the previous inner flow when a new value arrives. **Search-as-you-type.**
* `flatMapMerge` — runs inner flows concurrently, interleaving results. Parallel independent fetches.
* `flatMapConcat` — runs them strictly in order, one after another. Sequential dependency.

**Follow-up:** *Why is `flatMapLatest` the fix for a stale-response race?*
> When the upstream emits, it cancels the coroutine collecting the previous inner flow before starting the new one. The old response's emission is therefore structurally impossible, not merely ignored.

---

### Q14. Write a search-as-you-type flow. `[Mid]`

**Answer**
```kotlin
val results: StateFlow<UiState> = query
    .debounce(300)                      // Wait for typing to settle
    .distinctUntilChanged()             // Ignore no-op re-emissions
    .flatMapLatest { q ->               // Cancel the in-flight request
        if (q.length < 2) flowOf(UiState.Idle)
        else repo.search(q)
            .map { UiState.Success(it) }
            .onStart { emit(UiState.Loading) }
            .catch { emit(UiState.Error(it.toUserMessage())) }
    }
    .flowOn(Dispatchers.IO)
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), UiState.Idle)
```

**Follow-up:** *Why is `catch` inside the `flatMapLatest` rather than at the end?*
> A `catch` at the end would terminate the **outer** flow on the first error, so the screen would never respond to further typing. Inside, it terminates only that one inner flow and the outer stream keeps accepting queries.

---

### Q15. What does `catch` actually catch? `[Mid]`

**Answer**
Only exceptions from **upstream** operators — everything before it in the chain. It does not catch exceptions thrown in the `collect` block, and it does not catch `CancellationException`.

**Follow-up:** *How do you handle an exception in the collector?*
> A plain `try`/`catch` around the `collect` call, or restructure so the collector cannot throw. Putting UI-rendering code that can throw inside `collect` is usually the real problem.

---

### Q16. `combine` vs `zip`. `[Mid]`

**Answer**
`combine` emits whenever **any** source emits, using each source's latest value — the right choice for deriving UI state from several inputs. `zip` pairs emissions one-to-one and waits for both.

The trap: `combine` waits until **every** source has emitted at least once before its first emission. A source with no initial value stalls the whole chain.

**Follow-up:** *Your combined state never emits. What do you check?*
> Whether every source has produced a value. Convert cold sources with no natural first value using `onStart { emit(default) }`, or use `StateFlow`, which always has one.

---

### Q17. Explain `callbackFlow` and why `awaitClose` is mandatory. `[Mid]`

**Answer**
`callbackFlow` bridges a callback API into a flow. `awaitClose` suspends until the flow is cancelled and runs the unregistration — without it, the block would return immediately and the callback would leak. The builder throws at runtime if `awaitClose` is missing.

```kotlin
fun ConnectivityManager.status(): Flow<Boolean> = callbackFlow {
    val cb = object : NetworkCallback() {
        override fun onAvailable(n: Network) { trySend(true) }
        override fun onLost(n: Network) { trySend(false) }
    }
    registerDefaultNetworkCallback(cb)
    awaitClose { unregisterNetworkCallback(cb) }
}
```

**Follow-up:** *Why `trySend` rather than `send`?*
> The callback is not a suspending context, so `send` cannot be called there. `trySend` is non-suspending and returns a result you can inspect if the buffer is full.

---

### Q18. `StateFlow` vs `SharedFlow` — when do you need `SharedFlow`? `[Mid]`

**Answer**
`StateFlow` always has a current value, conflates, and drops emissions equal to the current one. Use it for state.

`SharedFlow` has configurable `replay` and buffering and does not conflate by default. Use it when every emission matters, or when you need replay of the last N values to new subscribers.

**Follow-up:** *For one-shot events, `SharedFlow(replay = 0)` or a `Channel`?*
> `Channel` + `receiveAsFlow()`. A `SharedFlow` with no replay drops events emitted while there is no collector — exactly what happens during a configuration change. A `Channel` buffers them.

---

### Q19. What is `suspendCancellableCoroutine` for? `[Senior]`

**Answer**
Wrapping a callback-based one-shot API as a suspending function, with cancellation support.

```kotlin
suspend fun LegacySdk.token(): String = suspendCancellableCoroutine { cont ->
    val call = requestToken(object : Callback {
        override fun onSuccess(t: String) = cont.resume(t)
        override fun onError(e: Throwable) = cont.resumeWithException(e)
    })
    cont.invokeOnCancellation { call.cancel() }   // Without this the SDK call keeps running
}
```

**Follow-up:** *What happens if you call `resume` twice?*
> `IllegalStateException: Already resumed`. Callback APIs that can fire twice (a retry callback, a listener invoked per attempt) need guarding — check `cont.isActive` before resuming.

---

### Q20. Explain `volatile` and what it does not guarantee. `[Mid]`

**Answer**
`@Volatile` guarantees **visibility** and **ordering** — a write is immediately visible to other threads, and reads/writes are not reordered around it. It does **not** guarantee **atomicity**.

```kotlin
@Volatile var count = 0
fun inc() { count++ }        // BUG: read-modify-write, still races

private val safe = AtomicInteger(0)
fun incSafe() { safe.incrementAndGet() }   // Single CAS instruction
```

**Follow-up:** *Why is `@Volatile` mandatory in the double-checked-locking singleton?*
> Without it, another thread can observe a non-null reference to a **partially constructed** object, because the constructor's field writes may be reordered after the reference assignment. The reader then sees an object with uninitialized fields.

---

### Q21. What does `synchronized` guarantee, and what is its Android-specific risk? `[Mid]`

**Answer**
Mutual exclusion plus the visibility and ordering guarantees of `volatile` for everything inside the block.

The Android-specific risk: if the main thread takes a lock held by a background thread doing slow work, the UI blocks and you get an ANR. This is one of the most common ANR shapes, and the trace shows the main thread `Blocked` with `held by thread N`.

**Follow-up:** *What is the rule that prevents it?*
> Never hold a lock across IO or any potentially slow operation, and never let the main thread contend for a lock a background thread can hold for long. Prefer immutable data and message passing over shared mutable state.

---

### Q22. How do you size a thread pool? `[Mid]`

**Answer**
* **CPU-bound:** `Runtime.getRuntime().availableProcessors()`. More threads only add context switches.
* **IO-bound:** much larger, because threads spend their time blocked. `Dispatchers.IO` uses 64 by default.
* **Ordering required:** `newSingleThreadExecutor()` gives sequential execution and implicit mutual exclusion with no locks.

**Follow-up:** *What is wrong with `Thread { }` per request?*
> Each thread costs ~1 MB of stack and creation is not free. Under load it exhausts memory and thrashes the scheduler. Always use a bounded pool, or coroutines on a bounded dispatcher.

---

### Q23. What is a deadlock and how do you avoid one? `[Mid]`

**Answer**
Two threads each hold a lock the other needs. Classic shape:
```
Thread A: lock(X) then lock(Y)
Thread B: lock(Y) then lock(X)
```
Avoid it with a **global lock ordering** — always acquire X before Y everywhere — or, better, by not holding two locks at once.

**Follow-up:** *Can coroutines deadlock?*
> Yes. A `Mutex` used in the wrong order deadlocks identically, and `runBlocking` on a dispatcher whose only thread is needed to complete the awaited work deadlocks immediately. `runBlocking` in production code is almost always a bug.

---

### Q24. `Channel` vs `SharedFlow` — when do you use each? `[Senior]`

**Answer**
A `Channel` is a **hot, single-consumer queue** with backpressure; each element goes to exactly one receiver. A `SharedFlow` broadcasts every emission to **all** collectors.

Use a `Channel` for one-shot UI events (exactly-once delivery, buffered while no collector) and for producer/consumer pipelines. Use `SharedFlow` when several independent parts of the app must all see the same event.

**Follow-up:** *What are the Channel buffer options and which for UI events?*
> `RENDEZVOUS` (default, suspends until received), `BUFFERED` (64), `UNLIMITED`, `CONFLATED` (keeps only the latest). For UI events, `BUFFERED` — the events survive a configuration-change gap without the producer suspending.

---

### Q25. Explain `CoroutineExceptionHandler` and why it sometimes does nothing. `[Senior]`

**Answer**
It handles exceptions that reach a **root** coroutine. Installing it on a child is a no-op, because the exception has already propagated to the parent, which handles it according to its own job.

```kotlin
private val handler = CoroutineExceptionHandler { _, e -> _state.update { it.copy(error = e) } }
fun load() = viewModelScope.launch(handler) { ... }   // Root of this launch: works
// viewModelScope.launch { launch(handler) { ... } }  // Child: never fires
```

Also: it never fires for `async` — that exception lives in the `Deferred` until `await()`.

**Follow-up:** *What handles the exception if no handler is installed?*
> It propagates to the thread's default uncaught exception handler, which on Android crashes the app. That is usually the right default in debug and the wrong one in production, which is why a handler plus a Crashlytics `recordException` is the common pattern.

---

### Q26. What is `runTest` and how does virtual time work? `[Mid]`

**Answer**
`runTest` runs the body on a `TestScope` with a `TestCoroutineScheduler` that fast-forwards `delay`. A `delay(30_000)` completes instantly when the scheduler advances past it, so testing a 30-second timeout takes microseconds.

* `advanceTimeBy(n)` — advance virtual time by n ms.
* `advanceUntilIdle()` — run everything queued.
* `runCurrent()` — run only what is due now.

**Follow-up:** *`StandardTestDispatcher` vs `UnconfinedTestDispatcher`?*
> `Standard` queues coroutines and requires explicit advancement — deterministic, and it surfaces ordering bugs. `Unconfined` runs eagerly, which is convenient but hides those bugs. Prefer `Standard` and reach for `Unconfined` only when eager execution is genuinely what you are testing.

---

### Q27. Why does a ViewModel test fail with "Module with the Main dispatcher had failed to initialize"? `[Mid]`

**Answer**
`viewModelScope` uses `Dispatchers.Main`, which on the JVM has no Android main looper. Replace it:

```kotlin
class MainDispatcherRule(
    private val dispatcher: TestDispatcher = StandardTestDispatcher()
) : TestWatcher() {
    override fun starting(d: Description) = Dispatchers.setMain(dispatcher)
    override fun finished(d: Description) = Dispatchers.resetMain()
}
```

**Follow-up:** *Why is `resetMain` in `finished` important?*
> `setMain` is global state. Leaving it set leaks the test dispatcher into subsequent tests, causing failures that depend on test execution order — the worst kind to debug.

---

### Q28. Explain backpressure in Flow and how `buffer`, `conflate`, and `collectLatest` differ. `[Senior]`

**Answer**
By default a flow is sequential: the emitter waits for the collector. That is backpressure by suspension.

* `buffer(n)` — the emitter continues into a buffer while the collector works; both run concurrently.
* `conflate()` — a buffer of size 1 that drops intermediate values; the collector always gets the latest.
* `collectLatest { }` — **cancels** the collector's block when a new value arrives, then restarts it.

**Follow-up:** *A UI collector renders every value from a fast sensor and drops frames. Which operator?*
> `conflate()`. The UI only needs the latest state; intermediate values are wasted work. `collectLatest` also works but cancels mid-render, which can leave partial UI updates.

---

### Q29. What is `Mutex` in coroutines and how does it differ from `synchronized`? `[Senior]`

**Answer**
`Mutex.withLock { }` **suspends** rather than blocking, so a waiting coroutine releases its thread. `synchronized` blocks the thread, which on `Dispatchers.IO` wastes a pool thread and on `Main` risks an ANR.

`Mutex` is also not reentrant — a coroutine that takes the same mutex twice deadlocks, unlike `synchronized`.

```kotlin
private val mutex = Mutex()
suspend fun update(block: suspend () -> Unit) = mutex.withLock { block() }
```

**Follow-up:** *When do you need a `Mutex` at all if coroutines on the same dispatcher run one at a time?*
> They do not — `Dispatchers.Default` and `IO` are multi-threaded, so two coroutines genuinely run in parallel. A `Mutex` (or confining the state to a single-threaded dispatcher, or using immutable data) is required for shared mutable state.

---

### Q30. You need to load a user's profile, their orders, and their recommendations. Profile is required; the other two are optional and independent. Write it. `[Senior]`

**Answer**
```kotlin
fun load() = viewModelScope.launch(exceptionHandler) {
    // Required: a failure here must fail the whole screen
    val profile = repo.profile()

    _state.update { it.copy(profile = profile, loading = false) }

    // Optional and independent: one failing must not affect the other or the profile
    supervisorScope {
        launch {
            runCatching { repo.orders() }
                .onSuccess { o -> _state.update { it.copy(orders = o) } }
                .onFailure { e ->
                    if (e is CancellationException) throw e
                    _state.update { it.copy(ordersError = true) }
                }
        }
        launch {
            runCatching { repo.recommendations() }
                .onSuccess { r -> _state.update { it.copy(recommendations = r) } }
                .onFailure { if (it is CancellationException) throw it }   // Silently omit
        }
    }
}
```

The reasoning to state: `supervisorScope` because the two optional loads are independent; `runCatching` with an explicit `CancellationException` rethrow; and separate error fields in state so each section can render its own failure without blanking the others.

**Follow-up:** *Why not `async` for all three and `awaitAll`?*
> `awaitAll` is all-or-nothing — one failure cancels the others and fails the screen. That is correct only when all three are genuinely required. Here two are optional, so their failures must be isolated.

---

## Related

* [`04_jetpack_architecture.md`](./04_jetpack_architecture.md) — ViewModel scope, state exposure
* [`06_data_networking.md`](./06_data_networking.md) — Flow from Room and Retrofit
* [`11_testing_security.md`](./11_testing_security.md) — testing coroutines and flows
* [`00_INDEX.md`](./00_INDEX.md) — full index
