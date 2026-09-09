# 10. Performance, Memory & Diagnostics — 25 Questions

> Memory leaks, GC, ANR, startup, jank, profiling tools, app size, battery.
> Reference material: [`../android.md`](../android.md) Module 9.

---

### Q1. What is a memory leak in Android and what are the classic sources? `[Junior]`

**Answer**
An object that is no longer needed but is still strongly reachable, so the GC cannot reclaim it. On Android the object is usually an Activity, and the leak retains its entire view hierarchy.

Classic sources:
1. A `static`/`object` field holding a Context or View.
2. An unregistered listener, `BroadcastReceiver`, or RxJava subscription.
3. A non-static inner class (including anonymous `Runnable`s and `Handler`s) holding an implicit outer reference.
4. A Fragment's `ViewBinding` not nulled in `onDestroyView`.
5. A `ViewModel` holding a View or Context.

**Follow-up:** *Why is a leaked Activity so much worse than a leaked plain object?*
> An Activity retains its entire view hierarchy, every bitmap those views reference, and its Context. A single leaked Activity is routinely tens of megabytes, and leaking one per rotation makes the app OOM quickly.

---

### Q2. How does LeakCanary work? `[Mid]`

**Answer**
It observes Activity and Fragment destruction, holds a `WeakReference` to each destroyed instance, and after a delay forces a GC. If the reference is still alive, it dumps the heap (`.hprof`), parses it, and computes the **shortest strong reference path** from a GC root to the leaked object — the leak trace.

**Follow-up:** *Why does it report the shortest path specifically?*
> Any path keeps the object alive, but the shortest one is almost always the one you introduced. Longer paths tend to run through framework internals that are consequences, not causes.

---

### Q3. What is a `Handler` leak and how do you fix it? `[Mid]`

**Answer**
A non-static `Handler` (or an anonymous `Runnable` posted to one) holds an implicit reference to its outer Activity. A message queued with a delay keeps that reference alive in the `MessageQueue` until it is processed.

```kotlin
class SafeHandler(activity: MainActivity) : Handler(Looper.getMainLooper()) {
    private val ref = WeakReference(activity)
    override fun handleMessage(msg: Message) { ref.get()?.update(msg) }
}
// And always: handler.removeCallbacksAndMessages(null) in onDestroy
```

**Follow-up:** *Is this still a common bug?*
> Much less so — coroutines with `lifecycleScope` are cancelled automatically, which removes the whole class of problem. It still appears in older code and in third-party SDKs.

---

### Q4. Explain ART's Concurrent Copying GC and why allocation still costs frames. `[Senior]`

**Answer**
CC GC relocates live objects into a fresh region while the app runs, using **read barriers** to redirect any read of a moved object. That removes the stop-the-world pause and compacts the heap, eliminating fragmentation.

Allocation still costs because: barriers add a small per-read cost; a high allocation rate forces more frequent collections; and exhausting the heap triggers a **blocking** allocation-failure GC. "Concurrent" means mostly pause-free, not free.

**Follow-up:** *Where does allocation hurt most?*
> In `onDraw`, `onBindViewHolder`, and any per-frame callback. Lint's `DrawAllocation` catches the first; the others need profiling.

---

### Q5. How do you find what is consuming memory? `[Mid]`

**Answer**
Memory Profiler → capture a heap dump → sort by **retained size**, not shallow size. Retained size is what would actually be freed. Then follow the reference chain to a GC root to see what is holding it.

```bash
adb shell am dumpheap com.example.app /data/local/tmp/heap.hprof
adb pull /data/local/tmp/heap.hprof
adb shell dumpsys meminfo com.example.app     # PSS breakdown by category
```

**Follow-up:** *`dumpsys meminfo` shows high "Graphics" but the Java heap is small. What is happening?*
> Bitmaps and GPU-backed surfaces. Since Android 8, bitmap pixel data lives in **native** memory, not the Java heap, so `Runtime.maxMemory()` looks healthy while the process is killed for total footprint. Look at image loading and cache sizes.

---

### Q6. Why does loading a large image cause OOM, and what is the fix? `[Junior]`

**Answer**
A 4000×3000 JPEG decodes to 4000 × 3000 × 4 bytes ≈ 48 MB in ARGB_8888, regardless of the view's size.

Fix by downsampling to the target size — which is exactly what Coil and Glide do automatically. Manually, compute `BitmapFactory.Options.inSampleSize` from the target dimensions.

**Follow-up:** *When is `RGB_565` worth using?*
> It halves memory but loses alpha and shows banding on gradients. Reasonable for full-screen opaque photo backgrounds on low-memory devices; a bad default for UI imagery.

---

### Q7. What are the ANR thresholds and how do you read an ANR trace? `[Mid]`

**Answer**
Input dispatch 5 s; broadcast 10 s foreground; service start 20 s foreground; `ContentProvider` 10 s.

Reading the trace, the main thread's state names the shape:
* `Blocked` with `held by thread N` → lock contention; **read thread N's stack**.
* `Runnable` in your code → slow main-thread work.
* `Waiting` on a latch → a background thread never signalled.

**Follow-up:** *Where do you get the trace?*
> `adb pull /data/anr/` on a device, or the Play Console's ANR cluster with a deobfuscated stack (requires the uploaded mapping file).

---

### Q8. Give the most common ANR cause and its fix. `[Mid]`

**Answer**
Main-thread IO — a SharedPreferences read, a synchronous database query, a file read, or a network call.

```kotlin
// StrictMode surfaces every instance of this in seconds
StrictMode.setThreadPolicy(
    StrictMode.ThreadPolicy.Builder()
        .detectDiskReads().detectDiskWrites().detectNetwork()
        .penaltyLog()
        .build()
)
```

**Follow-up:** *Why does `SharedPreferences.apply()` still cause ANRs despite being asynchronous?*
> Pending writes are force-flushed **on the main thread** by `QueuedWork` during `onPause`/`onStop`, and the very first read parses the whole XML synchronously. DataStore has neither problem.

---

### Q9. Explain cold, warm, and hot start. `[Junior]`

**Answer**
* **Cold** — no process exists. Zygote fork, `Application.onCreate`, ContentProvider initialization, then the first Activity. The slowest, and what users judge.
* **Warm** — the process exists but the Activity was destroyed; only the Activity is recreated.
* **Hot** — both exist; the Activity is brought forward.

**Follow-up:** *What is the Play Console threshold you must stay under?*
> Android Vitals flags cold starts over **5 seconds** as bad behavior, which affects store ranking. The percentile that matters is the 90th on low-end devices, not your flagship test device.

---

### Q10. What is TTID vs TTFD? `[Mid]`

**Answer**
* **TTID (Time To Initial Display)** — the first frame is drawn, possibly a skeleton. Reported by `adb shell am start -W`.
* **TTFD (Time To Full Display)** — the screen is genuinely usable. You must report it yourself with `reportFullyDrawn()`.

Optimizing TTID alone can produce a fast-looking empty screen; TTFD is what the user actually experiences.

**Follow-up:** *What happens if you never call `reportFullyDrawn()`?*
> Vitals and Macrobenchmark fall back to TTID, so your metrics look better than reality and you cannot detect regressions in data loading.

---

### Q11. What are the highest-leverage startup optimizations? `[Senior]`

**Answer**
1. **Baseline Profile** — 20–40% off cold start with no logic change. Nearly free.
2. **Move work out of `Application.onCreate`** — every millisecond there is on every cold start. Make initialization lazy or move it to a background dispatcher.
3. **Consolidate auto-initializing `ContentProvider`s** with App Startup — each library provider is instantiated before your first Activity line runs.
4. **Render a skeleton immediately**, fill it asynchronously — never block the first frame on network or database.
5. **Avoid heavy dependency-graph construction** at startup; scope expensive objects lazily.

**Follow-up:** *How do you prove a startup change actually helped?*
> Macrobenchmark with `StartupTimingMetric`, 10+ iterations, `StartupMode.COLD`, on a **release** build. Anything measured on a debuggable build is meaningless because AOT optimizations are disabled.

---

### Q12. What is the frame budget and what causes jank? `[Mid]`

**Answer**
16.6 ms at 60 Hz, 8.3 ms at 120 Hz. Missing it re-displays the previous frame — visible stutter.

| Symptom | Likely cause |
|---|---|
| Stutter while scrolling | Work in `onBindViewHolder` / unstable Compose params |
| Stutter on first display | Layout inflation or composition on the UI thread |
| Periodic hitches | GC from per-frame allocation |
| Uniformly slow | Overdraw |
| Only on first run | JIT compiling cold code — needs a Baseline Profile |

**Follow-up:** *Why is assuming 60 Hz a mistake?*
> Modern devices run 90/120/144 Hz, cutting the budget to 8.3 ms or less. Read `Display.getRefreshRate()`; code that fits comfortably at 60 Hz can jank badly at 120.

---

### Q13. How do you measure jank objectively? `[Mid]`

**Answer**
```bash
adb shell dumpsys gfxinfo com.example.app             # Janky-frame % and percentiles
adb shell dumpsys gfxinfo com.example.app framestats  # Per-frame phase breakdown
```
In-app, `JankStats` (androidx.metrics) reports dropped frames with contextual state, so you know *which screen* janked. In CI, Macrobenchmark's `FrameTimingMetric` catches regressions.

**Follow-up:** *Why attach state to `JankStats`?*
> `PerformanceMetricsState.putState("Screen", "ProductDetail")` turns "3% of frames janked" into "3% of frames janked, almost all on ProductDetail" — the difference between a number and an action.

---

### Q14. What is overdraw and how do you fix it? `[Mid]`

**Answer**
Painting the same pixel multiple times per frame. Find it with Developer Options → Debug GPU Overdraw (blue 1×, green 2×, light red 3×, dark red 4×+).

The most common fix is removing the window background when your root layout already paints an opaque background:
```xml
<item name="android:windowBackground">@null</item>
```

**Follow-up:** *Why does a translucent scrim cost more than an opaque one?*
> The GPU must read the existing pixels, blend, and write back, rather than simply writing. Large translucent areas are disproportionately expensive on low-end GPUs.

---

### Q15. What does StrictMode catch and how should you roll it out? `[Mid]`

**Answer**
Thread policy: disk reads/writes, network, and custom slow calls on the main thread. VM policy: leaked Activities, leaked `Closeable`s, leaked SQLite cursors, `file://` URI exposure.

Roll it out with `penaltyLog()` first, fix the existing violations, then tighten to `penaltyDeath()` in debug — starting with `penaltyDeath` on an existing app simply crashes it at launch.

**Follow-up:** *Should StrictMode ever be enabled in release?*
> Not with penalties. Some teams enable detection with a custom penalty that reports to Crashlytics at a low sample rate, which surfaces violations only occurring on real devices. Never `penaltyDeath` in release.

---

### Q16. What is Perfetto and what does it show you? `[Senior]`

**Answer**
A system-wide tracing tool giving a per-thread timeline with named slices, GC events, binder transactions, scheduling states, and frame lifecycle. It answers "which phase blew the budget" definitively.

```bash
adb shell perfetto -o /data/misc/perfetto-traces/t.pb -t 10s \
    sched freq idle am wm gfx view binder_driver dalvik
adb pull /data/misc/perfetto-traces/t.pb     # Open at ui.perfetto.dev
```
Add your own slices with `androidx.tracing`'s `trace("name") { }` so app code appears alongside framework slices.

**Follow-up:** *What does a thread in "Uninterruptible Sleep" (D state) tell you?*
> It is blocked on IO at the kernel level — usually disk. Seeing the main thread in D state is a direct pointer at main-thread file access.

---

### Q17. What is Macrobenchmark and why can it not run on a debug build? `[Mid]`

**Answer**
Macrobenchmark launches your app as a separate process and measures startup and frame timing with statistical repetition. It requires a non-debuggable, release-like build because debug builds disable AOT compilation and enable extra checks, producing numbers that are 2–5× worse and directionally misleading.

The usual setup is a `benchmark` build type derived from `release` but signed with the debug key.

**Follow-up:** *What is `CompilationMode` for?*
> It controls how the app is compiled before measurement: `None()` (no AOT — worst case), `Partial()` (with Baseline Profiles — what users get), `Full()` (everything AOT). Comparing `None` with `Partial` quantifies exactly what your Baseline Profile buys.

---

### Q18. How do you reduce app size? `[Mid]`

**Answer**

| Technique | Typical saving |
|---|---|
| Ship an AAB | 15–35% |
| R8 full mode | 10–25% of DEX |
| `shrinkResources` (with `minifyEnabled`) | 5–15% |
| WebP/AVIF instead of PNG | 25–50% of images |
| Vector drawables for icons | Large — one asset replaces five densities |
| Removing unused dependencies | Varies, often large |

```bash
apkanalyzer apk file-size app-release.apk
apkanalyzer files list --exclude-dirs app-release.apk | sort -k2 -h -r | head -30
apkanalyzer dex packages --defined-only app-release.apk | sort -k2 -nr | head -20
```

**Follow-up:** *Why is measuring the APK size misleading?*
> After AAB splitting, the delivered size is much smaller and varies per device. Quote the Play Console download size, or `bundletool get-size total`.

---

### Q19. What causes battery drain and how do you detect it? `[Mid]`

**Answer**
Main causes: wakelocks held too long, frequent high-accuracy location, unnecessary network wakeups, unregistered sensor listeners, and excessive `AlarmManager` use.

```bash
adb shell dumpsys batterystats --charged com.example.app
# Then Battery Historian for a visual timeline of wakelocks, jobs, and radio state
```
Play Console's Android Vitals reports excessive wakeups and partial wakelocks against thresholds.

**Follow-up:** *Which single mistake causes the most drain in practice?*
> A registered `SensorEventListener` or location callback that is never unregistered. It effectively holds the CPU awake continuously, even with the screen off, and there is no crash or error to notice.

---

### Q20. What is `oom_score_adj` and what can you do about being killed? `[Senior]`

**Answer**
A per-process score from −1000 to 1000 that `lmkd` uses to choose kill victims under memory pressure. Foreground ≈ 0; cached/empty ≈ 900–1000 and killed first.

You cannot change your score. What you can do: reduce the retained heap so the app is cheaper to keep, and make restoration correct so being killed is invisible — persist via `SavedStateHandle` and the database, and test with `adb shell am kill`.

**Follow-up:** *How do you know how often your app is being killed?*
> `ActivityManager.getHistoricalProcessExitReasons()` (API 30+) reports why the process last died — `REASON_LOW_MEMORY`, `REASON_ANR`, `REASON_CRASH`, `REASON_USER_REQUESTED`. Log it on startup to get real data instead of guessing.

---

### Q21. What is the difference between shallow size, retained size, and PSS? `[Senior]`

**Answer**
* **Shallow size** — the object's own fields only.
* **Retained size** — everything that would be freed if this object were collected. The number that matters in a heap dump.
* **PSS (Proportional Set Size)** — the process's physical memory including native allocations and a proportional share of shared pages. This is what `lmkd` actually considers.

**Follow-up:** *Why can the Java heap be small while PSS is huge?*
> Bitmaps (native since Android 8), native libraries, GPU buffers, and mmap'd files all count toward PSS but not the Java heap. Optimizing `Runtime.totalMemory()` while ignoring PSS optimizes the wrong number.

---

### Q22. How do you decide whether a performance problem is worth fixing? `[Senior]`

**Answer**
By data, at the percentile that matters:
* **Vitals** for field data — the p90 on real devices, not your test device.
* **Device distribution** — a 200 ms regression on a device class holding 40% of your users outweighs a 2 s regression on 0.5%.
* **User impact** — startup and scroll are perceived directly; a 50 ms improvement in a rarely-opened settings screen is not.
* **Cost** — a Baseline Profile is hours of work for 20–40%; a rewrite is months for less.

**Follow-up:** *What is the most common misallocation of performance effort?*
> Micro-optimizing code that is not on the critical path, guided by intuition rather than a trace — while shipping without a Baseline Profile, which is the largest available win and requires almost no work.

---

### Q23. A user reports the app "feels slow" but you cannot reproduce it. What do you do? `[Senior]`

**Answer**
1. **Check Vitals** for their device model and Android version — the problem is often device-class-specific.
2. **Look at `getHistoricalProcessExitReasons`** telemetry for low-memory kills.
3. **Add JankStats reporting** with screen attribution so field data tells you where.
4. **Test on a genuinely low-end device**, not an emulator — storage IO and thermal throttling are the usual differences.
5. **Check data scale** — a user with 10,000 rows exercises code paths your test data never does.
6. **Check network conditions** — throttle to 3G with high latency; code that assumes a fast response often shows a spinner forever on a slow one.

**Follow-up:** *Which of those is most often the answer?*
> Data scale and low-end hardware. Both are trivially testable and both are routinely skipped, so they account for a disproportionate share of "cannot reproduce" reports.

---

### Q24. What is thermal throttling and how does it affect measurement? `[Senior]`

**Answer**
Sustained load raises device temperature, and the OS reduces CPU/GPU frequency to compensate. A benchmark run immediately after another can be 30%+ slower for reasons unrelated to the code.

Macrobenchmark checks `PowerManager.getCurrentThermalStatus()` and warns. For manual measurement, let the device cool and repeat runs.

**Follow-up:** *How do you make benchmarks reproducible in CI?*
> Lock CPU clocks where the device permits it (`./gradlew lockClocks` on rooted devices), run enough iterations for a stable median, compare medians rather than single runs, and treat only sustained changes across runs as real.

---

### Q25. Design a performance monitoring strategy for a production app. `[Senior]`

**Answer**
**In the field**
* **Android Vitals** — cold start, ANR rate, crash rate, excessive wakeups. The baseline, free, and it is what affects store ranking.
* **Firebase Performance or a custom trace** — TTFD per screen, key network call durations, with device class and country as dimensions.
* **JankStats** with screen attribution, sampled.
* **`getHistoricalProcessExitReasons`** logged on startup, so low-memory kills are visible.
* **Crashlytics** with custom keys (device tier, data-set size, feature flags) so crashes are diagnosable.

**In CI**
* **Macrobenchmark** startup and scroll metrics on a fixed device, failing the build on regression beyond a threshold.
* **APK size check** with a budget, failing on unexpected growth.
* **Baseline Profile regenerated** on each release.

**Process**
* Track p90 rather than mean — the mean hides the users having a bad time.
* Segment by device tier; a single global number averages away the population that suffers most.
* Set explicit budgets (cold start < 1.5 s p90, jank < 1%) so a regression is a defined failure rather than a judgment call.

**Follow-up:** *What is the single most valuable metric if you can only have one?*
> Cold start at p90, segmented by device tier. It correlates with retention, it is the metric Play penalizes, and it is sensitive to a broad class of regressions — a new library, an added `ContentProvider`, a heavier dependency graph all show up there first.

---

## Related

* [`01_system_internals.md`](./01_system_internals.md) — GC internals, LMK, ANR mechanics
* [`09_compose.md`](./09_compose.md) — recomposition performance
* [`13_build_release_gradle.md`](./13_build_release_gradle.md) — R8, app size, Baseline Profiles in the build
* [`00_INDEX.md`](./00_INDEX.md) — full index
