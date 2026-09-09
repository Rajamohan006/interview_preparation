# 1. Android System Internals — 30 Questions

> Boot sequence, Zygote, ART, class loaders, Binder, DEX, R8, APK/AAB.
> Reference material: [`../knowledge_points.md`](../knowledge_points.md), [`../android.md`](../android.md) Module 1.
> Difficulty: `[Junior]` `[Mid]` `[Senior]`

---

### Q1. Describe the Android boot sequence from power-on to the launcher. `[Mid]`

**Answer**
1. **Boot ROM** loads the bootloader from a fixed address.
2. **Bootloader** initializes memory and loads the Linux kernel.
3. **Kernel** sets up drivers, memory management, and mounts the root filesystem, then starts `init` as PID 1.
4. **`init`** parses `init.rc`, mounts partitions, and starts native daemons (`servicemanager`, `lmkd`, `surfaceflinger`, `vold`).
5. **Zygote** starts, initializes a Dalvik/ART VM, preloads framework classes and resources, then listens on a socket.
6. **SystemServer** is forked from Zygote and starts ~100 system services — `ActivityManagerService`, `PackageManagerService`, `WindowManagerService`.
7. `ActivityManagerService` broadcasts `ACTION_BOOT_COMPLETED` and launches the **Launcher** via a `HOME` intent.

**Follow-up:** *Why is `servicemanager` started before Zygote?*
> Every Binder service registers itself with `servicemanager` and every client looks services up through it. It is the Binder name registry, so it must exist before any process that publishes or consumes a system service.

---

### Q2. What is Zygote and why does it exist? `[Mid]`

**Answer**
Zygote is a process started at boot that initializes a VM and preloads ~2,000 framework classes and shared resources. Every app process is `fork()`ed from it rather than started from scratch.

Two benefits:
* **Startup speed** — the framework is already loaded and, on modern ART, already JIT/AOT-warmed.
* **Memory** — forked processes share the preloaded pages via copy-on-write, so N apps do not each hold their own copy of the framework.

**Follow-up:** *What happens to those shared pages when an app writes to a preloaded object?*
> The kernel traps the write, copies just that 4 KB page into the child's private memory, and redirects the mapping. Only modified pages are duplicated, which is why the framework stays mostly shared across every running app.

---

### Q3. Explain Copy-on-Write and how the ART heap is laid out to maximize sharing. `[Senior]`

**Answer**
`fork()` gives the child an identical virtual address space where all pages are marked read-only and shared. A write triggers a page fault; the kernel copies that single page for the writer.

ART is designed around this: preloaded classes go into a **boot image** (`boot.art`/`boot.oat`) which is memory-mapped read-only and shared across every process on the device. The heap is also arranged so that write-heavy objects are separated from read-mostly ones, keeping more pages shareable.

**Follow-up:** *Why must you never modify a preloaded framework object's static state?*
> It dirties a shared page for that process, costing memory, and on some framework classes the mutation is visible only in your process, producing behavior that differs from every other app.

---

### Q4. Compare Dalvik and ART. `[Junior]`

**Answer**

| | Dalvik | ART |
|---|---|---|
| Compilation | JIT only | Hybrid AOT + JIT, profile-guided |
| Startup | Slower — compiles as it runs | Faster — runs pre-compiled native code |
| Install time | Fast | Slower originally; modern ART compiles during idle instead |
| Storage | Bytecode only | Bytecode + `.oat` native code |
| GC | Stop-the-world | Concurrent copying, mostly pause-free |

**Follow-up:** *ART originally compiled everything at install. Why did Google move away from that?*
> Install times became unacceptable (minutes for large apps), and most compiled code was never executed. Profile-guided compilation compiles only what the user actually runs, during idle-and-charging windows.

---

### Q5. What is a Baseline Profile and how does it improve startup? `[Mid]`

**Answer**
A Baseline Profile is a text file listing the classes and methods on the app's critical path. It ships in the APK/AAB, and at install time ART AOT-compiles exactly those methods. The first run therefore executes native code instead of interpreting and JIT-compiling.

Typical result: **20–40% faster cold start** and noticeably less jank on the first scroll.

```kotlin
@RunWith(AndroidJUnit4::class)
class BaselineProfileGenerator {
    @get:Rule val rule = BaselineProfileRule()

    @Test fun generate() = rule.collect(packageName = "com.example.app") {
        pressHome()
        startActivityAndWait()
        device.findObject(By.res("feed")).fling(Direction.DOWN)  // Cover the first scroll too
    }
}
```

**Follow-up:** *How is a Baseline Profile different from a Cloud Profile?*
> Baseline Profiles ship with the app and apply from the very first launch. Cloud Profiles are aggregated by Play from real users of previous versions and delivered later; they complement, not replace, a Baseline Profile — which is the only one available on day one of a release.

---

### Q6. Explain the class loader hierarchy in Android. `[Mid]`

**Answer**
Android uses the standard parent-delegation model with Android-specific loaders:
* **`BootClassLoader`** — framework classes from the boot image.
* **`PathClassLoader`** — your app's `classes*.dex` from the installed APK. Cannot load DEX from an arbitrary path.
* **`DexClassLoader`** — can load DEX/JAR/APK from any readable path, which is what makes plugin architectures and hot-fix frameworks possible.
* **`InMemoryDexClassLoader`** — loads DEX from a `ByteBuffer` with no file on disk.

Delegation: a loader asks its parent first, and only loads the class itself if the parent cannot.

**Follow-up:** *Why is parent delegation a security property, not just an organizational one?*
> It makes it impossible for app code to substitute its own `java.lang.String` or `android.app.Activity` — the boot loader always wins, so core classes cannot be shadowed by an attacker-supplied DEX.

---

### Q7. What is Binder and why does Android use it instead of standard Linux IPC? `[Senior]`

**Answer**
Binder is a kernel driver (`/dev/binder`) providing object-oriented RPC between processes.

Advantages over pipes/sockets/SysV IPC:
* **One copy instead of two.** Standard IPC copies user→kernel→user. Binder `mmap`s a buffer into the receiver's address space, so the kernel copies once.
* **Identity.** Every transaction carries the caller's verified UID and PID, so a service can authorize the caller (`Binder.getCallingUid()`) without trusting anything the caller claims.
* **Object references.** A Binder handle can be passed between processes and the kernel maintains the mapping, enabling capability-style access control.
* **Death notification.** `linkToDeath` tells a client when the remote process dies.

**Follow-up:** *What is the transaction size limit and why does it produce intermittent failures?*
> The buffer is **1 MB per process, shared across all in-flight transactions**. Whether a given 400 KB payload succeeds depends on what else is transacting at that instant, which is why `TransactionTooLargeException` reproduces inconsistently.

---

### Q8. What does the D8 compiler do, and how does R8 differ? `[Junior]`

**Answer**
**D8** converts Java bytecode (`.class`) into Dalvik bytecode (`.dex`). **R8** does the same job *plus* shrinking, optimization, and obfuscation in a single pass. R8 replaced ProGuard, which ran as a separate step before dexing.

**Follow-up:** *What is R8 "full mode" and what breaks in it?*
> Full mode (default since AGP 8) makes stronger assumptions — notably that classes without keep rules are not reflected on — enabling more aggressive optimization. Libraries relying on reflection without shipping consumer rules can break, showing up as `ClassNotFoundException` or a null field only in release builds.

---

### Q9. Name the four things R8 does and give an example of what each removes. `[Mid]`

**Answer**
1. **Shrinking (tree shaking)** — walks reachability from entry points (manifest components, keep rules) and deletes anything unreachable. Removes 80% of a large utility library you use one function from.
2. **Optimization** — inlines small methods, removes unused parameters, merges class hierarchies, and does dead-code elimination.
3. **Obfuscation** — renames `UserRepository.fetchProfile` to `a.b`, shrinking the string pool and hindering analysis.
4. **Resource shrinking** — removes unreferenced drawables and strings (requires `shrinkResources` alongside `minifyEnabled`).

**Follow-up:** *Which of these can break your app, and how do you find out before users do?*
> All except resource shrinking commonly break reflection-based code. Run the full instrumented test suite against a minified build, and read `build/outputs/mapping/release/usage.txt` to see exactly what was deleted.

---

### Q10. What is inside an APK? `[Junior]`

**Answer**
```
app.apk (a ZIP)
├── AndroidManifest.xml    # Binary XML, parsed by PackageParser
├── classes.dex            # Dalvik bytecode; classes2.dex... when multidexed
├── resources.arsc         # Flat resource table, stored uncompressed so it can be mmap'd
├── res/                   # Compiled layouts and drawables
├── assets/                # Raw files, read via AssetManager
├── lib/<abi>/*.so         # Native libraries per ABI
└── META-INF/              # v1 signature manifests and certificates
```

**Follow-up:** *Why are `classes.dex` and `resources.arsc` stored uncompressed?*
> So the runtime can memory-map them directly out of the APK instead of extracting and decompressing them. That is why they must also be page-aligned (`zipalign`), and why an APK's on-disk size is larger than a fully-compressed ZIP would be.

---

### Q11. APK vs AAB — what changes for the developer and for the user? `[Junior]`

**Answer**
An **APK** is installable. An **AAB** is a publishing format: Play uses it to generate device-specific APKs containing only the ABI, density, and language that device needs.

* **User:** typically 15–35% smaller download.
* **Developer:** Play holds the app signing key (Play App Signing), and you can no longer directly produce the exact artifact a user installs — use `bundletool` to reproduce it locally.

**Follow-up:** *You have a crash reported only on one device model. How do you get the exact APK it received?*
> `bundletool build-apks --bundle=app.aab --output=app.apks --device-spec=device.json`, where `device.json` comes from `bundletool get-device-spec` on that device (or is hand-written from the model's ABI/density/locale).

---

### Q12. Explain the 64K method limit. `[Mid]`

**Answer**
The DEX format addresses methods with a 16-bit `method_id` index, capping a single DEX at 65,536 **referenced** methods — which includes every framework and library method your code calls, not just methods you wrote.

* **minSdk ≥ 21:** ART loads `classes2.dex`, `classes3.dex`… natively with no cost or configuration.
* **minSdk < 21:** legacy multidex extracts and optimizes secondary DEX files at first launch, adding seconds to cold start.

**Follow-up:** *Your build just crossed the limit. What is the first thing you do?*
> Enable R8, not multidex. Shrinking usually drops the count well below the limit and makes the app smaller and faster, whereas multidex just accommodates the bloat.

---

### Q13. Compare the four APK signature schemes. `[Mid]`

**Answer**

| Scheme | Since | Signs | Key property |
|---|---|---|---|
| v1 (JAR) | Always | Individual files via `META-INF` | Slow; ZIP metadata unprotected |
| v2 | API 24 | The whole APK byte-for-byte | Fast; detects any modification |
| v3 | API 28 | v2 + key-rotation lineage | Rotate the signing key without losing update rights |
| v4 | API 30 | Merkle tree in a sidecar `.idsig` | Enables ADB incremental install |

**Follow-up:** *You lost your app signing key and are not on Play App Signing. What are your options?*
> None that preserve the app. Android refuses an update signed by a different key. You must publish under a new package name and migrate users manually — which is exactly the failure mode Play App Signing exists to prevent.

---

### Q14. What is `oom_score_adj` and how does the Low Memory Killer use it? `[Senior]`

**Answer**
Every process has an `oom_score_adj` from −1000 (never kill) to 1000 (kill first). `lmkd` (the userspace low-memory-killer daemon) watches memory pressure via PSI and kills the highest-scoring processes first.

| Process state | Approx. score | Killed |
|---|---|---|
| Foreground (visible Activity) | 0 | Last |
| Visible (behind a dialog) | 100–200 | Rarely |
| Service (running background service) | 500 | Under pressure |
| Cached / empty | 900–1000 | First |

**Follow-up:** *Your app is killed within seconds of backgrounding on a low-end device. What can you actually do?*
> Nothing to change the score directly — you make the app cheap to kill and cheap to restore: shrink the retained heap, persist state via `SavedStateHandle` and a database, and test with `adb shell am kill` so restoration is correct rather than fighting the kill.

---

### Q15. Explain ART's Concurrent Copying garbage collector. `[Senior]`

**Answer**
CC GC relocates live objects into a fresh region while the app keeps running:
* **Read barriers** — every object-reference read goes through a barrier that redirects to the new location if the object has been moved. This is what removes the stop-the-world pause.
* **Generational** — new allocations go into a young region collected frequently and cheaply; survivors are promoted.
* **Compaction** — moving objects into contiguous regions eliminates fragmentation, so a large allocation is less likely to fail even when total free memory is sufficient.

**Follow-up:** *If GC is concurrent, why do allocations still cause jank?*
> The barriers have a small per-read cost, allocation itself can trigger a blocking allocation-failure GC when the heap is exhausted, and high allocation rates force more frequent collections. Allocating in `onDraw` or `onBindViewHolder` still costs frames.

---

### Q16. Strong, soft, weak, and phantom references — when do you use each? `[Mid]`

**Answer**

| Type | Collected when | Android use |
|---|---|---|
| **Strong** | Never while reachable | Normal references |
| **Soft** | Only under memory pressure | Memory caches (but prefer `LruCache`) |
| **Weak** | At the next GC | Breaking a leak: a listener or a `Handler` holding an Activity |
| **Phantom** | After finalization, for cleanup notification | Native resource cleanup; rarely used in apps |

```kotlin
// A static Handler leaks the Activity through its implicit outer reference.
// A WeakReference breaks the chain.
class SafeHandler(activity: MainActivity) : Handler(Looper.getMainLooper()) {
    private val ref = WeakReference(activity)
    override fun handleMessage(msg: Message) {
        ref.get()?.updateUi(msg)      // Null if the Activity was collected
    }
}
```

**Follow-up:** *Why is `SoftReference` discouraged for caching on Android?*
> ART's collector treats soft references aggressively and unpredictably, so the cache can be cleared long before memory is actually tight. `LruCache` with an explicit size budget is predictable and is what the framework recommends.

---

### Q17. `Serializable` vs `Parcelable` — and does the answer change with `@Parcelize`? `[Mid]`

**Answer**
`Serializable` is a JVM marker interface; the runtime uses **reflection** to walk fields, which is slow and allocates heavily. `Parcelable` requires you to write explicit marshalling, which is roughly an order of magnitude faster and is what the framework uses for IPC.

`@Parcelize` (kotlin-parcelize) generates the `writeToParcel`/`CREATOR` boilerplate at compile time, so you get `Parcelable` performance with `Serializable`-level ergonomics. There is no longer a good reason to choose `Serializable` for Android IPC.

```kotlin
@Parcelize
data class User(val id: Long, val name: String, val tags: List<String>) : Parcelable
```

**Follow-up:** *Is a `Parcel` a valid format for persisting data to disk?*
> No. `Parcel` is explicitly not a stable serialization format — its layout can change between Android versions, so data written by one release may be unreadable by the next. Use JSON, Proto, or a database.

---

### Q18. What is JNI and what does it cost? `[Mid]`

**Answer**
JNI is the bridge between managed (Kotlin/Java) and native (C/C++) code. Costs:
* **Transition overhead** — each call crosses a managed/native boundary; a call in a tight loop is dominated by that cost.
* **Manual reference management** — local references must be freed or the local reference table overflows; global references leak if not deleted.
* **No GC visibility** — native memory is invisible to ART, so heap profiling misses it entirely.
* **Crash severity** — a native crash is a SIGSEGV that kills the process with no catchable exception.

**Follow-up:** *When is the NDK actually worth it?*
> Compute-heavy work where the boundary is crossed rarely and a lot happens inside: image and video processing, audio DSP, cryptography, physics, ML inference, and reusing an existing large C/C++ library. Not for business logic.

---

### Q19. Explain the Handler / Looper / MessageQueue mechanism. `[Mid]`

**Answer**
* **`Looper`** — an infinite loop bound to a thread via `ThreadLocal`, pulling messages off a queue. The main thread's is created by `ActivityThread`.
* **`MessageQueue`** — a time-ordered linked list of `Message`s. When empty it blocks on `epoll_wait` on a pipe FD, so an idle thread uses zero CPU.
* **`Handler`** — posts into a queue and processes the messages it posted.

```kotlin
val mainHandler = Handler(Looper.getMainLooper())
mainHandler.post { textView.text = "done" }
```

**Follow-up:** *What is a synchronization barrier?*
> A message with a `null` target inserted into the queue. While it is present, synchronous messages are blocked but **asynchronous** ones still run. `Choreographer` uses this so that measure/layout/draw traversals jump ahead of queued background work, protecting frame timing.

---

### Q20. Why is `Looper.loop()` an infinite loop that does not ANR? `[Senior]`

**Answer**
An ANR is triggered by an input event or a broadcast not being *processed* within a timeout — not by the main thread being busy in general. `Looper.loop()` spends its idle time blocked in `epoll_wait`, which is a sleeping state consuming no CPU; the kernel wakes it when a message arrives. The loop is therefore always available to process the next message immediately.

The ANR happens when a *single* message handler takes too long, blocking the loop from reaching the next message.

**Follow-up:** *So what exactly does the 5-second input-dispatch ANR measure?*
> The time from an input event being delivered to the app's window until the app finishes processing it. If the main thread is stuck inside a previous message's handler, the input event sits unprocessed and the timer expires.

---

### Q21. What are the ANR thresholds and how do you diagnose one from a trace? `[Mid]`

**Answer**
* Input dispatch: **5 s**
* Broadcast receiver: **10 s** (foreground), 60 s (background)
* Service start: **20 s** (foreground), 200 s (background)
* `ContentProvider`: **10 s**

Diagnosis: the main thread's state names the shape of the problem.
* `Blocked` with `held by thread N` → lock contention; read **thread N's** stack.
* `Runnable` in your own code → slow main-thread work; the stack is the answer.
* `Waiting` on a latch → a background thread never signalled.

**Follow-up:** *The main thread shows `Blocked` waiting on a lock held by a thread doing disk IO. What is the fix?*
> Stop holding the lock across the IO. Compute or load outside the critical section and only take the lock to publish the result — or restructure so the main thread never waits on that lock at all.

---

### Q22. Explain `TransactionTooLargeException` and how to prevent it. `[Mid]`

**Answer**
It is thrown when a Binder transaction exceeds the available portion of the 1 MB per-process buffer. Common causes: a big `Bundle` in `onSaveInstanceState`, a large list in an Intent extra, or a `ContentProvider` returning too many rows.

Prevention: save **identity**, not data.
```kotlin
override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    // outState.putParcelableArrayList("photos", photos)   // Megabytes: throws
    outState.putLong("album_id", albumId)                  // Rebuild from the DB instead
    outState.putInt("scroll", layoutManager.findFirstVisibleItemPosition())
}
```

**Follow-up:** *Why does it only crash sometimes?*
> The 1 MB buffer is shared across all concurrent transactions in the process. Whether a given payload fits depends on what else is in flight at that moment, which makes it look non-deterministic.

---

### Q23. `PathClassLoader` vs `DexClassLoader` — and what is each used for? `[Senior]`

**Answer**
`PathClassLoader` loads DEX only from the installed APK path; it is what the framework creates for your app. `DexClassLoader` accepts an arbitrary readable path, which allows loading code that was not part of the installed package.

`DexClassLoader` powers plugin frameworks, hot-fix systems, and dynamic feature loading — and is also the mechanism malware uses to fetch a payload after passing store review.

**Follow-up:** *What is the security concern with `DexClassLoader`?*
> If the DEX comes from a writable location or an unauthenticated network source, an attacker who can write there achieves arbitrary code execution inside your app's UID. Any dynamically loaded code must be signature-verified and read from app-private storage.

---

### Q24. What is AIDL and on which thread do AIDL methods execute? `[Senior]`

**Answer**
AIDL declares a cross-process interface; the compiler generates a client `Proxy` (marshals into a `Parcel`, calls `transact`) and a server `Stub` (`onTransact`, unmarshals, dispatches).

AIDL methods execute on a thread from the **Binder thread pool** — never the main thread. Touching UI from one crashes; blocking in one occupies a finite pool thread (16 by default).

**Follow-up:** *When would you use `Messenger` instead?*
> When you want requests serialized. `Messenger` funnels every call onto a single `Handler`, so you get ordering and implicit mutual exclusion with no locks — at the cost of throughput. AIDL is for concurrent callers.

---

### Q25. What happens when you run an app in a separate process with `android:process`? `[Senior]`

**Answer**
It becomes a distinct OS process, forked separately from Zygote. Consequences:
* `Application.onCreate` runs **once per process**.
* Every singleton, static field, and in-memory cache exists **independently** per process.
* Crashes are isolated — a native crash in `:web` does not kill the main process.
* Communication requires IPC; static fields do not cross the boundary.

```kotlin
override fun onCreate() {
    super.onCreate()
    when (getProcessName()) {
        packageName -> initUiStack()        // Main process only
        "$packageName:web" -> Unit          // Keep it minimal
    }
}
```

**Follow-up:** *You use Room from two processes and updates in one are invisible to the other. Why?*
> Room's invalidation tracker uses in-process observers. Enable `enableMultiInstanceInvalidation()` on the builder, which uses a `ContentProvider`-based mechanism to propagate invalidation across processes.

---

### Q26. What is the Android security sandbox? `[Junior]`

**Answer**
Each app is assigned a unique Linux **UID** at install. Its files are owned by that UID with no world access, and its process runs as that UID. Standard Linux DAC therefore prevents one app from reading another's data or memory. SELinux (enforcing since Android 5) adds mandatory access control on top, restricting even root-owned processes to their declared domain.

**Follow-up:** *How can two apps share a UID, and why is it discouraged now?*
> `android:sharedUserId` with identical signing keys. It is deprecated because it is irreversible — you cannot later split apps that share a UID without data loss — and it collapses the security boundary between them.

---

### Q27. How does `PackageManagerService` resolve an implicit intent? `[Mid]`

**Answer**
It matches the intent against every registered `<intent-filter>`. A filter matches only if **all three** hold:
1. The **action** matches one listed in the filter.
2. **Every** category in the intent is present in the filter (`DEFAULT` is added automatically for `startActivity`).
3. The **data** matches: scheme, host, port, path, and MIME type as declared.

One match launches directly; several show the chooser; zero throws `ActivityNotFoundException`.

**Follow-up:** *Why does Android 14 block some implicit intents that previously worked?*
> Targeting API 34, an implicit intent can no longer be delivered to a **non-exported** component of your own app. Internal navigation that relied on implicit resolution must be made explicit.

---

### Q28. What is a `WindowToken` and why does `Application` context fail to show a dialog? `[Mid]`

**Answer**
A `WindowToken` is a Binder token issued by `WindowManagerService` authorizing a client to add a window layer at a given position in the z-order. An `Activity` has one; an `Application` does not, because it corresponds to no on-screen window.

Showing a dialog with the application context therefore throws `WindowManager$BadTokenException: Unable to add window — token null is not valid`.

**Follow-up:** *What legitimately uses the application context then?*
> Anything that must outlive an Activity: singletons, WorkManager, Room, DI-provided clients, and `registerReceiver` for process-lifetime listeners. The rule of thumb is that anything drawing UI needs the Activity context; anything else should take the application context to avoid leaks.

---

### Q29. Walk through what happens between `startActivity()` and `onCreate()`. `[Senior]`

**Answer**
1. `Activity.startActivity` → `Instrumentation.execStartActivity` → Binder call to **`ActivityTaskManagerService`** in `system_server`.
2. ATMS resolves the intent via `PackageManagerService`, checks permissions, and determines the target task and launch mode.
3. If the target process does not exist, ATMS asks Zygote to fork it; the new process's `ActivityThread.main()` runs and attaches back to ATMS.
4. ATMS sends a launch transaction back over Binder to the app's `ApplicationThread`.
5. `ActivityThread` posts it to the main `Handler`; `performLaunchActivity` instantiates the Activity, attaches the `ContextImpl`, and calls `onCreate`.
6. `onStart`, `onResume`, then `WindowManager.addView` makes the window visible.

**Follow-up:** *Where in that sequence does the "cold start" cost actually go?*
> Step 3 dominates: process fork plus `Application.onCreate` plus every auto-initializing `ContentProvider` runs before your first Activity line executes. That is why moving work out of `Application.onCreate` is the highest-leverage startup fix.

---

### Q30. What is `mmap` and where does Android rely on it? `[Senior]`

**Answer**
`mmap` maps a file (or anonymous memory) into a process's virtual address space, so the file is accessed as memory with the kernel paging it in on demand, avoiding an explicit read-into-buffer copy.

Android uses it for:
* **DEX and `.oat` loading** — pages are shared across processes and loaded lazily, which is why DEX is stored uncompressed.
* **`resources.arsc`** — mapped directly rather than parsed into the heap.
* **Binder** — the receiver's buffer is mapped into the kernel, enabling the single-copy transfer.
* **The ART boot image** — one physical copy shared by every app on the device.

**Follow-up:** *Why does an mmap'd file not count toward your app's Java heap limit?*
> It is not on the managed heap at all; it lives in the process's virtual address space and is backed by the page cache. This is why `Runtime.maxMemory()` can look healthy while the process is still killed for exceeding its total memory footprint (PSS).

---

## Related

* [`02_components_manifest.md`](./02_components_manifest.md) — components, IPC in practice, manifest
* [`10_performance_memory.md`](./10_performance_memory.md) — GC, leaks, ANR triage, startup
* [`13_build_release_gradle.md`](./13_build_release_gradle.md) — R8 rules, signing, DEX limits in the build
* [`00_INDEX.md`](./00_INDEX.md) — full index
