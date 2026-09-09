# 9. Jetpack Compose — 40 Questions

> Recomposition, state, effects, stability, layout, animation, gestures, performance, testing, interop.
> Reference material: [`../compose.md`](../compose.md).

---

### Q1. What are the three phases of a Compose frame? `[Junior]`

**Answer**
1. **Composition** — run composables to build/update the UI tree.
2. **Layout** — measure and place each node.
3. **Drawing** — render into the canvas.

Compose can skip earlier phases: a state read only in the draw phase re-runs only drawing.

**Follow-up:** *Why does that make `Modifier.offset { }` cheaper than `Modifier.offset(x.dp)`?*
> The lambda overload reads the value during **layout**, so composition is skipped entirely. The value overload reads it during **composition**, so the whole composable recomposes on every change.

---

### Q2. What is recomposition and what triggers it? `[Junior]`

**Answer**
Re-running a composable to update the UI. It is triggered when a **snapshot state object that the composable read** changes. Compose records reads during composition, so only the composables that actually read the changed state are invalidated.

**Follow-up:** *Why must a composable be side-effect free?*
> It can run many times per frame, in any order, on any thread, and can be cancelled and restarted. Any side effect (a network call, a log, a counter increment) would execute an unpredictable number of times.

---

### Q3. What does the Compose compiler actually do to a composable function? `[Senior]`

**Answer**
It adds a `Composer` parameter and a `$changed` bitmask, and wraps the body in `startRestartGroup`/`endRestartGroup`. Inside, it compares each parameter against the value stored in the **slot table** from the previous composition, and if nothing changed and the composable is skippable, calls `skipToGroupEnd()` and does no work.

`endRestartGroup()?.updateScope { ... }` registers the lambda that re-invokes the function when an observed state invalidates it.

**Follow-up:** *What is the slot table?*
> A flat gap buffer of groups keyed by **call-site position**. It stores what `remember` saved and the structure of child groups. Because identity is positional, inserting an item at the top of an unkeyed list reassociates every item's remembered state with the wrong data.

---

### Q4. What is stability and why does it matter? `[Mid]`

**Answer**
The compiler classifies each parameter type as stable (reliable `equals`, no unnotified mutation) or unstable. A composable with any unstable parameter is **not skippable** and recomposes whenever its parent does.

`List`, `Map`, and `Set` interfaces are unstable, as is any class with a `var`, or any type from a module not compiled with the Compose compiler.

**Follow-up:** *How did strong skipping change this?*
> From Compose Compiler 2.0.20, composables with unstable parameters became skippable using **instance equality** for those parameters, and lambdas are auto-remembered. Most manual `@Immutable`/`ImmutableList` work is no longer needed — but a fresh `map {}` still produces a new reference every recomposition, so it still recomposes.

---

### Q5. `@Stable` vs `@Immutable`. `[Mid]`

**Answer**
* `@Immutable` — a promise that the object's public properties **never change** after construction.
* `@Stable` — the object may change, but it **notifies** composition when it does (through snapshot state), and its `equals` is consistent.

Both are promises the compiler trusts without verification. Lying about them produces stale UI that is extremely hard to debug.

**Follow-up:** *How do you mark a third-party type stable without owning it?*
> A stability configuration file passed via `composeCompiler { stabilityConfigurationFile = ... }`, listing types or package patterns (`java.time.LocalDate`, `com.thirdparty.model.*`).

---

### Q6. `remember` vs `rememberSaveable`. `[Junior]`

**Answer**
`remember` survives recomposition but dies with the composition — so it is lost on configuration change and process death. `rememberSaveable` additionally stores its value in the saved-state Bundle, surviving both.

`rememberSaveable` requires a `Saver` for non-primitive types (`@Parcelize`, `mapSaver`, or `listSaver`).

**Follow-up:** *What are its limits?*
> The Bundle goes through a Binder transaction, so it is bounded by the ~1 MB buffer. Store scroll positions and IDs; never a list of models.

---

### Q7. What does `remember(key)` do when the key changes? `[Junior]`

**Answer**
It discards the remembered value and recomputes it. With no key, the value is computed once and never again for that call site.

```kotlin
// Recomputed whenever userId changes; without the key it would go stale
val formatter = remember(locale) { NumberFormat.getCurrencyInstance(locale) }
```

**Follow-up:** *What is the most common `remember` bug?*
> Forgetting a key when the computation depends on a parameter, so the value goes stale silently. The UI shows data derived from an old parameter with no error.

---

### Q8. `derivedStateOf` — when does it earn its cost? `[Mid]`

**Answer**
When a **frequently-changing** input produces a **rarely-changing** output. It observes the inputs and only invalidates readers when the computed result actually changes.

```kotlin
// scrollState.firstVisibleItemIndex changes constantly; showButton flips rarely
val showButton by remember { derivedStateOf { listState.firstVisibleItemIndex > 5 } }
```

**Follow-up:** *When is it wrong to use?*
> When input and output change at the same rate. `derivedStateOf { user.name.uppercase() }` adds a snapshot-observation layer for no filtering benefit — just compute it inline.

---

### Q9. `LaunchedEffect` — what are its semantics? `[Mid]`

**Answer**
It launches a coroutine when it enters the composition, cancels it when it leaves, and **cancels and relaunches** when any key changes.

```kotlin
LaunchedEffect(userId) {          // Restarts when userId changes
    viewModel.load(userId)
}
LaunchedEffect(Unit) { }          // Runs once for the composable's lifetime
```

**Follow-up:** *You pass a lambda as a key and the effect restarts every recomposition. Why?*
> A lambda literal is a new instance each composition unless remembered. Key on stable values, and use `rememberUpdatedState` to read a changing lambda inside a long-running effect without restarting it.

---

### Q10. What is `rememberUpdatedState` for? `[Senior]`

**Answer**
Reading the **latest** value of something inside a long-running effect without restarting the effect.

```kotlin
@Composable
fun AutoDismiss(onTimeout: () -> Unit) {
    val currentOnTimeout by rememberUpdatedState(onTimeout)
    LaunchedEffect(Unit) {                 // Must NOT restart when onTimeout changes
        delay(5_000)
        currentOnTimeout()                 // Calls the newest lambda, not the captured one
    }
}
```

**Follow-up:** *What happens without it?*
> Either the effect keys on `onTimeout` and restarts the 5-second timer on every recomposition (so it never fires), or it captures the original lambda and calls a stale callback pointing at old state.

---

### Q11. `DisposableEffect` vs `LaunchedEffect`. `[Mid]`

**Answer**
`LaunchedEffect` runs a coroutine. `DisposableEffect` runs non-suspending setup and **requires** an `onDispose` block for cleanup — the right tool for registering and unregistering listeners.

```kotlin
DisposableEffect(lifecycleOwner) {
    val observer = LifecycleEventObserver { _, e -> if (e == ON_RESUME) refresh() }
    lifecycleOwner.lifecycle.addObserver(observer)
    onDispose { lifecycleOwner.lifecycle.removeObserver(observer) }
}
```

**Follow-up:** *What is `SideEffect` for?*
> Publishing Compose state to a non-Compose object **after every successful composition**. It is not cancelled or keyed — use it for things like updating an analytics screen name or a legacy view's property, never for launching work.

---

### Q12. What is `produceState`? `[Mid]`

**Answer**
It converts non-Compose state into Compose `State`, running a coroutine that sets `value` over time.

```kotlin
@Composable
fun userState(id: Long): State<Result<User>> = produceState<Result<User>>(Loading, id) {
    value = runCatching { repo.getUser(id) }.fold(::Success, ::Error)
}
```

**Follow-up:** *When would you use `collectAsStateWithLifecycle` instead?*
> Whenever the source is already a Flow — it handles lifecycle-aware collection correctly. `produceState` is for non-Flow sources, or where you need custom production logic.

---

### Q13. `collectAsState` vs `collectAsStateWithLifecycle`. `[Mid]`

**Answer**
`collectAsState` collects for the composition's lifetime, including while the app is in the background — wasting CPU, network, and battery. `collectAsStateWithLifecycle` applies `repeatOnLifecycle(STARTED)`, suspending collection when the app is not visible.

Always use the lifecycle-aware variant on Android.

**Follow-up:** *Is there a case where `collectAsState` is correct?*
> On non-Android Compose targets (desktop, web) where there is no Android lifecycle, and for flows that must keep running regardless — rare, and usually a sign the collection belongs in the ViewModel instead.

---

### Q14. What is state hoisting? `[Junior]`

**Answer**
Moving state up to the lowest common ancestor that needs it, so the composable becomes stateless and takes `value` + `onValueChange`.

```kotlin
@Composable
fun Counter(count: Int, onIncrement: () -> Unit) {   // Stateless: testable, previewable, reusable
    Button(onClick = onIncrement) { Text("$count") }
}
```

**Follow-up:** *What is the cost of hoisting too far?*
> State at the top of the tree invalidates everything below it. Hoist to the lowest common ancestor that actually needs it — not to the root by default.

---

### Q15. Why does mutating a `List` inside `mutableStateOf` not recompose? `[Mid]`

**Answer**
The state holds a **reference**. Mutating the list in place does not change the reference, so the write observer never fires.

```kotlin
var items by remember { mutableStateOf(listOf<Item>()) }
items.toMutableList().add(x)      // Nothing happens
items = items + x                 // Correct: new reference

val items = remember { mutableStateListOf<Item>() }
items.add(x)                      // Also correct: observable collection
```

**Follow-up:** *When do you use `mutableStateListOf` over reassignment?*
> For frequently-mutated collections where copying is expensive. It also gives finer-grained invalidation. For small lists in a ViewModel, an immutable `StateFlow<List<T>>` is usually cleaner and matches UDF better.

---

### Q16. Explain the snapshot system. `[Senior]`

**Answer**
Compose state uses multiversion concurrency control. Each `MutableState` holds a chain of `StateRecord`s tagged with the snapshot that wrote them; a reader walks the chain for the newest record visible to its own snapshot.

Composition runs inside a snapshot with a **read observer** (building the dependency map) and writes go through a **write observer** (invalidating scopes that read that state). `Snapshot.apply()` merges records atomically.

**Follow-up:** *Why can you read Compose state from a background thread safely?*
> Because each snapshot sees a consistent, isolated view. A background reader never observes a half-applied set of writes — the same guarantee a database transaction gives.

---

### Q17. What does `snapshotFlow` do? `[Mid]`

**Answer**
It converts Compose state reads into a Flow, emitting when the **read values** change.

```kotlin
LaunchedEffect(listState) {
    snapshotFlow { listState.firstVisibleItemIndex }
        .distinctUntilChanged()
        .filter { it > 5 }
        .collect { analytics.trackDeepScroll(it) }
}
```

**Follow-up:** *Why not just read the state directly in the composable?*
> Reading it in the composable subscribes that composable to it, so it recomposes on every scroll pixel. `snapshotFlow` reads it inside an effect, so the reaction happens without any recomposition.

---

### Q18. Why does modifier order matter? `[Junior]`

**Answer**
Modifiers wrap each other in order, so each one operates on the result of the previous.

```kotlin
Modifier.padding(8.dp).background(Red)   // Red does NOT cover the padding
Modifier.background(Red).padding(8.dp)   // Red covers the padding
Modifier.clickable { }.padding(16.dp)    // Padding is OUTSIDE the touch target
Modifier.padding(16.dp).clickable { }    // Padding is INSIDE — larger touch target
```

**Follow-up:** *Which ordering do you want for a clickable row, and why?*
> `padding` before `clickable`, so the padding is part of the touch target. Otherwise the user tapping near the edge of a row hits nothing — a real accessibility problem given the 48 dp minimum target size.

---

### Q19. Why do `LazyColumn` items need keys? `[Mid]`

**Answer**
Without a key, the slot table identifies items by **position**. Inserting at the top shifts every position, so every item's remembered state — scroll offset, expansion, animation — attaches to the wrong data.

```kotlin
LazyColumn { items(users, key = { it.id }) { UserRow(it) } }
```

**Follow-up:** *What does `contentType` add?*
> It tells the reuse pool which compositions are interchangeable. Reusing a "header" composition for an "item" slot forces a full recomposition instead of a cheap update; declaring content types makes the pool match like-for-like.

---

### Q20. Why can you not nest a `LazyColumn` inside a `LazyColumn`? `[Mid]`

**Answer**
The inner one receives infinite height constraints and throws. Lazy layouts need a bounded constraint on their scroll axis to know what to compose.

Fix by flattening into one list with multiple `item`/`items` blocks, which is also better for performance since there is one reuse pool.

**Follow-up:** *What if you genuinely need a horizontal list inside a vertical one?*
> That is fine — the axes differ, so the inner `LazyRow` has a bounded height. Give it an explicit height and share a reuse pool across rows if there are many.

---

### Q21. What is `SubcomposeLayout` and what does it cost? `[Senior]`

**Answer**
It allows composing children **during** the layout phase, so a child's content can depend on the parent's measurement. `BoxWithConstraints`, `LazyColumn`, and `Scaffold` use it.

The cost is that composition happens inside layout, so it cannot be batched with the main composition pass and is measurably slower. Avoid it in frequently-measured or deeply nested places.

**Follow-up:** *What is the cheaper alternative to `BoxWithConstraints` for adaptive layouts?*
> `WindowSizeClass` from the Activity, or a custom `Layout` that reads constraints without subcomposing. `BoxWithConstraints` is convenient but pays the subcomposition cost on every measure.

---

### Q22. Explain the single-pass measurement guarantee. `[Senior]`

**Answer**
A Compose layout may measure each child **exactly once**. This makes layout O(n) rather than the O(n²) that nested double-measuring `RelativeLayout`s produce in the View system.

Where a parent genuinely needs to know a child's size before deciding, **intrinsics** (`minIntrinsicHeight`, `maxIntrinsicWidth`) provide a query that does not count as a measure.

**Follow-up:** *You need two columns to have the same height as the taller one. How?*
> `Modifier.height(IntrinsicSize.Min)` on the `Row`, which queries intrinsics rather than measuring twice. Without intrinsics you would need `SubcomposeLayout`, which is far more expensive.

---

### Q23. What is `CompositionLocal` and when should you use it? `[Mid]`

**Answer**
Implicit data passed down the tree without threading it through every parameter — used by `MaterialTheme`, `LocalContext`, `LocalDensity`.

Use it for genuinely ambient, tree-wide concerns: theme, density, layout direction. Do **not** use it for data a composable actually depends on — that makes the dependency invisible and the composable impossible to preview or test in isolation.

**Follow-up:** *`compositionLocalOf` vs `staticCompositionLocalOf`?*
> `compositionLocalOf` tracks reads, so changing it recomposes only readers. `staticCompositionLocalOf` does not track, so changing it recomposes the **entire subtree** — but reads are cheaper. Use static for values that effectively never change (a `Context`), dynamic for values that do (a theme toggle).

---

### Q24. How do you write a custom modifier correctly today? `[Senior]`

**Answer**
`Modifier.Node`, not `composed { }`. `composed` creates a composition per modifier instance, defeating modifier comparison and reuse.

```kotlin
private class ShimmerNode(var color: Color) : Modifier.Node(), DrawModifierNode {
    override fun ContentDrawScope.draw() { drawContent(); /* ... */ }
}

private data class ShimmerElement(val color: Color) : ModifierNodeElement<ShimmerNode>() {
    override fun create() = ShimmerNode(color)
    override fun update(node: ShimmerNode) { node.color = color }   // Update, do not recreate
}

fun Modifier.shimmer(color: Color) = this then ShimmerElement(color)
```

**Follow-up:** *Why must the element be a `data class`?*
> Compose compares the new element with the previous one using `equals` to decide whether to update or recreate the node. Without correct `equals`/`hashCode`, the node is recreated on every recomposition and any state it holds (a running animation) restarts.

---

### Q25. Which animation API for which job? `[Mid]`

**Answer**

| Need | API |
|---|---|
| One value to a new target | `animate*AsState` |
| Several values from one state change | `updateTransition` |
| Enter/exit of a composable | `AnimatedVisibility` |
| Swap between contents | `AnimatedContent` |
| Loading shimmer, pulse | `rememberInfiniteTransition` |
| Gesture-driven, interruptible | `Animatable` |
| Automatic size change | `Modifier.animateContentSize` |
| List insert/remove/reorder | `Modifier.animateItem` |

**Follow-up:** *Why does every animation API take a `label`?*
> The Animation Inspector in Android Studio identifies animations by label. Without one, a screen with several animations shows a list of indistinguishable entries.

---

### Q26. Why use `Animatable` rather than `animateFloatAsState` for a drag? `[Senior]`

**Answer**
`animateFloatAsState` animates toward a target and cannot be interrupted mid-flight to follow a finger. `Animatable` gives `snapTo` (instant, cancels any running animation) and `animateTo` (preserving velocity from the gesture), which is what makes a drag-and-fling feel natural.

```kotlin
val offsetY = remember { Animatable(0f) }
// During drag: scope.launch { offsetY.snapTo(offsetY.value + delta) }
// On release:  scope.launch { offsetY.animateTo(0f, spring()) }
```

**Follow-up:** *Why must `Animatable` be inside `remember`?*
> Otherwise a new instance is created on every recomposition, resetting the animation continuously.

---

### Q27. What is a shared element transition? `[Mid]`

**Answer**
An element that visually persists across a navigation, animating from its position on one screen to its position on the next. In Compose 1.7+, wrap the nav host in `SharedTransitionLayout` and mark matching elements with the same `rememberSharedContentState(key)`.

**Follow-up:** *What is the most common mistake?*
> Keys that are not unique per item. Using `"image"` rather than `"image-$id"` means every list item matches the same shared element, so the animation targets the wrong one in a list.

---

### Q28. How does `pointerInput` work and what is the key trap? `[Senior]`

**Answer**
`pointerInput(key)` gives a coroutine scope receiving pointer events; gesture detectors (`detectTapGestures`, `detectDragGestures`, `detectTransformGestures`) are built on it.

The trap: the block **restarts** when the key changes. `pointerInput(someChangingState)` cancels the gesture mid-drag. Use `Unit` and read changing values from state inside the block.

```kotlin
Modifier.pointerInput(Unit) {          // Installed once
    detectTransformGestures { _, pan, zoom, _ -> scale *= zoom; offset += pan }
}
```

**Follow-up:** *How does event consumption work?*
> `change.consume()` marks the event handled so ancestors skip it — Compose's replacement for `requestDisallowInterceptTouchEvent`. Events flow in three passes: Initial (parent first), Main (child first), Final (parent first).

---

### Q29. How do you implement a collapsing toolbar in Compose? `[Senior]`

**Answer**
A `NestedScrollConnection` that consumes scroll in `onPreScroll` before the list gets it.

```kotlin
val connection = remember {
    object : NestedScrollConnection {
        override fun onPreScroll(available: Offset, source: NestedScrollSource): Offset {
            val new = (headerHeight + available.y).coerceIn(MIN, MAX)
            val consumed = new - headerHeight
            headerHeight = new
            return Offset(0f, consumed)     // Report what we took; the list gets the rest
        }
    }
}
Box(Modifier.nestedScroll(connection)) { /* header + LazyColumn */ }
```

**Follow-up:** *Why `onPreScroll` rather than `onPostScroll`?*
> The header should collapse **before** the list scrolls, matching user expectation. `onPostScroll` receives only what the list did not consume, so the header would only move once the list hit its end.

---

### Q30. `Canvas` vs `drawBehind` vs `drawWithCache`. `[Mid]`

**Answer**
* `Canvas(modifier)` — the composable's entire content is custom drawing.
* `Modifier.drawBehind { }` — draws behind existing content.
* `Modifier.drawWithContent { }` — draws around content, calling `drawContent()` where you want it.
* `Modifier.drawWithCache { }` — a cached setup block plus a draw block; the setup re-runs only on size or key change.

**Follow-up:** *When do you need `drawWithCache`?*
> Whenever the drawing allocates — a `Brush`, a `Path`, an `ImageBitmap`. Allocating in the draw lambda happens every frame; `drawWithCache` builds it once per size change.

---

### Q31. How do you make Compose UI accessible? `[Mid]`

**Answer**
* Describe the **action**, not the icon: `"Like, 42 likes"`, not `"heart icon"`.
* Never append the role — TalkBack already says "button".
* Merge composite rows with `semantics(mergeDescendants = true) {}` so a card is one swipe stop, not five.
* Minimum 48 dp touch targets.
* `contentDescription = null` for images already described by a parent.
* Use `sp` for text so it scales, `dp` for everything else.

**Follow-up:** *Why is writing accessible Compose the same activity as writing testable Compose?*
> Compose tests query the **semantics tree** — the same tree TalkBack reads. A screen with good semantics is automatically easy to test with `onNodeWithText` and `onNodeWithContentDescription`.

---

### Q32. How do you find the cause of excessive recomposition? `[Senior]`

**Answer**
1. **Layout Inspector** recomposition counts — live counts and skip counts per composable, the fastest way to find the hot spot.
2. **Compiler metrics** — enable `reportsDestination`, then read `<module>-composables.txt` for composables marked `restartable` without `skippable`, and which parameter is `unstable`.
3. **Macrobenchmark `FrameTimingMetric`** to confirm the fix in numbers.

```bash
./gradlew :app:assembleRelease
grep -v "skippable" app/build/compose_compiler/app_release-composables.txt
```

**Follow-up:** *What is the fix once you find an unstable parameter?*
> In order: use an immutable collection type; annotate your own type `@Immutable`; declare an external type stable in the configuration file; or narrow the parameter so the composable takes only the fields it reads.

---

### Q33. What is a deferred read and why is it the most effective performance fix? `[Senior]`

**Answer**
Reading state inside a lambda moves the read from the **composition** phase to the **layout** or **draw** phase, so only that phase re-runs.

```kotlin
// Recomposes every frame while scrolling
Box(Modifier.offset(x = offset.dp))

// Only re-lays-out — composition is skipped entirely
Box(Modifier.offset { IntOffset(offsetProvider().roundToInt(), 0) })

// Only redraws
Box(Modifier.drawBehind { drawRect(colorProvider()) })
```

**Follow-up:** *Why is `graphicsLayer` even cheaper than `offset {}`?*
> `graphicsLayer` applies the transform at draw time on the render node, so neither composition nor layout re-runs — the GPU just applies a different matrix to already-recorded content.

---

### Q34. How do you test a Compose screen? `[Mid]`

**Answer**
```kotlin
@get:Rule val compose = createAndroidComposeRule<MainActivity>()

@Test fun clicking_product_opens_detail() {
    compose.setContent { AppTheme { ProductList(sampleProducts, onClick = { clicked = it }) } }
    compose.onNodeWithText("Wireless Mouse").assertIsDisplayed().performClick()
    assertThat(clicked).isEqualTo(sampleProducts[0].id)
}
```
The rule synchronizes automatically — no `Thread.sleep` needed.

**Follow-up:** *A test with an infinite animation hangs. Why, and what do you do?*
> `waitForIdle` never returns because the composition is never idle. Set `compose.mainClock.autoAdvance = false` and drive time explicitly with `advanceTimeBy`, which also makes animation assertions deterministic.

---

### Q35. Can you run Compose tests on the JVM? `[Mid]`

**Answer**
Yes — Robolectric plus `createComposeRule()` runs them without an emulator, typically an order of magnitude faster.

```kotlin
@RunWith(RobolectricTestRunner::class)
@Config(sdk = [34])
class FastComposeTest {
    @get:Rule val compose = createComposeRule()
}
```
Screenshot testing (Paparazzi, Roborazzi) also runs on the JVM and catches visual regressions assertions cannot.

**Follow-up:** *What still requires a real device?*
> Anything touching real hardware or the system UI — permission dialogs, camera, biometric prompts — and final verification of GPU-dependent rendering. Keep those tests few.

---

### Q36. How do you host Compose inside a Fragment correctly? `[Senior]`

**Answer**
```kotlin
override fun onCreateView(...): View = ComposeView(requireContext()).apply {
    // The DEFAULT strategy disposes on window detach, which is WRONG for a back-stacked
    // fragment: the composition survives detached and leaks its state.
    setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed)
    setContent { AppTheme { ProfileScreen(hiltViewModel()) } }
}
```

**Follow-up:** *What about a `ComposeView` inside a RecyclerView item?*
> Each recycled row leaks a composition without a disposal strategy. Use `DisposeOnViewTreeLifecycleDestroyed`, or better, use a `LazyColumn` instead of a RecyclerView hosting ComposeViews.

---

### Q37. How do you host a View inside Compose? `[Mid]`

**Answer**
```kotlin
AndroidView(
    factory = { ctx -> MapView(ctx).apply { onCreate(null) } },   // Runs ONCE
    update = { view -> view.moveCamera(location) },               // Every relevant recomposition
    onRelease = { view -> view.onDestroy() },                     // MANDATORY for native resources
    modifier = modifier
)
```

**Follow-up:** *What goes wrong if you skip `onRelease`?*
> Views holding a `Surface`, a `MediaCodec`, or a map session leak permanently — and devices support only a handful of codec instances, so eventually all video playback fails.

---

### Q38. How do you handle edge-to-edge and the keyboard in Compose? `[Mid]`

**Answer**
```kotlin
enableEdgeToEdge()                    // In the Activity, before setContent

Column(
    Modifier.fillMaxSize()
        .windowInsetsPadding(WindowInsets.safeDrawing)   // Bars + cutout
        .imePadding()                                     // Rises with the keyboard
)

// For a scrolling list, insets go in contentPadding so items scroll UNDER the bars
LazyColumn(contentPadding = WindowInsets.safeDrawing.asPaddingValues())
```

**Follow-up:** *Why is `windowInsetsPadding` wrong on a `LazyColumn`?*
> It pads the whole list, so the list stops short of the bars instead of scrolling under them — visually wrong for edge-to-edge, which is enforced by default on Android 15.

---

### Q39. How does `BackHandler` relate to predictive back? `[Mid]`

**Answer**
`BackHandler(enabled) { }` registers an `OnBackPressedCallback`. The `enabled` flag is what makes predictive back possible: the system needs to know **before** the gesture starts whether the app will intercept it, so it can animate a preview.

For an animated predictive back, `PredictiveBackHandler` exposes the in-progress gesture as a `Flow<BackEventCompat>`.

**Follow-up:** *What is the most common bug?*
> A permanently-enabled `BackHandler`, so back never exits the screen and users get stuck. Drive `enabled` from state — for example, only intercept when there are unsaved changes.

---

### Q40. A Compose list scrolls poorly. Walk through your diagnosis. `[Senior]`

**Answer**
Measure first, on a **release** build — debug Compose is dramatically slower and directionally misleading.

1. **Layout Inspector recomposition counts** while scrolling. High counts on item composables point at stability or unkeyed items.
2. **Compiler metrics** — find item composables that are `restartable` but not `skippable`, and which parameter is unstable.
3. **Perfetto / Macrobenchmark `FrameTimingMetric`** to see whether time is in composition, layout, or draw.

Then, in order of expected payoff:
* Add `key` and `contentType` to `items`.
* Fix unstable parameters, or narrow them to the fields actually read.
* Move animated/scroll-derived reads into lambdas (`offset {}`, `graphicsLayer`, `drawBehind`).
* Replace per-item `derivedStateOf`/heavy computation with values precomputed in the ViewModel.
* Ensure images are loaded through Coil with correct sizing, not decoded inline.
* Add a **Baseline Profile** covering the first scroll — often the largest single win, and it requires no code change.

**Follow-up:** *Everything is skippable and the profile is in place, but the first scroll still hitches. What is left?*
> Item content that is genuinely expensive to compose the first time — deeply nested layouts, `SubcomposeLayout` per item, or synchronous image decoding. Flatten the item, avoid `BoxWithConstraints` per row, and make sure the image loader has a memory cache hit path.

---

## Related

* [`../compose.md`](../compose.md) — full Compose reference
* [`04_jetpack_architecture.md`](./04_jetpack_architecture.md) — state holders and UDF
* [`10_performance_memory.md`](./10_performance_memory.md) — profiling and frame timing
* [`00_INDEX.md`](./00_INDEX.md) — full index
