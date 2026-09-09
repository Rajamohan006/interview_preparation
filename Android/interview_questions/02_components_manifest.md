# 2. Core Components & Manifest — 30 Questions

> Activity, Service, BroadcastReceiver, ContentProvider, Intents, permissions, notifications, manifest configuration.
> Reference material: [`../android.md`](../android.md) Module 2.

---

### Q1. Name the four app components and what each is for. `[Junior]`

**Answer**
* **Activity** — a single screen with a UI; the entry point for user interaction.
* **Service** — long-running work with no UI (foreground, background, or bound).
* **BroadcastReceiver** — responds to system or app-wide announcements.
* **ContentProvider** — exposes structured data to other apps through a `content://` URI with permission control.

All four must be declared in `AndroidManifest.xml` (a `BroadcastReceiver` may instead be registered at runtime).

**Follow-up:** *Which of these can be started while your app is in the background on modern Android?*
> Practically only WorkManager-scheduled work and a foreground service started from an allowed context. Background services are blocked since Android 8, implicit broadcasts since Android 8, and foreground service starts from the background since Android 12.

---

### Q2. Walk through the Activity lifecycle for: A launches B, then the user presses back. `[Junior]`

**Answer**
Launching B: `A.onPause()` → `B.onCreate()` → `B.onStart()` → `B.onResume()` → `A.onStop()`.

Back from B: `B.onPause()` → `A.onRestart()` → `A.onStart()` → `A.onResume()` → `B.onStop()` → `B.onDestroy()`.

The key detail is that A's `onStop` runs **after** B is fully resumed — so `onPause` must be fast, and heavy release work belongs in `onStop`.

**Follow-up:** *Why should you not save data in `onPause`?*
> `onPause` runs on the critical path of the next Activity appearing; slow work there delays the transition visibly. It is also not the last guaranteed callback for persistence purposes — do lightweight state capture in `onSaveInstanceState` and durable persistence in a repository as changes happen.

---

### Q3. Explain the four launch modes. `[Mid]`

**Answer**

| Mode | Behavior |
|---|---|
| `standard` | New instance every time, in the caller's task |
| `singleTop` | Reuses the instance **if it is already on top**; delivers via `onNewIntent()` |
| `singleTask` | One instance per task; brought forward and everything above it cleared; `onNewIntent()` |
| `singleInstance` | Like `singleTask`, but alone in its own task — nothing else can be launched into it |
| `singleInstancePerTask` (API 31+) | Root of a task, but multiple tasks may each hold one |

**Follow-up:** *A deep link opens your `singleTop` MainActivity but the handler never runs. Why?*
> The link arrives in `onNewIntent()`, not `onCreate()`. Handle it in both, and call `setIntent(intent)` in `onNewIntent` so later `getIntent()` reads stay consistent.

---

### Q4. What is the difference between a foreground, background, and bound service? `[Mid]`

**Answer**
* **Foreground** — user-visible work with a mandatory persistent notification. Survives backgrounding. Requires `foregroundServiceType` and a matching permission on Android 14+.
* **Background (started)** — `startService()`, runs until `stopSelf()`. Effectively unusable since Android 8's background execution limits.
* **Bound** — `bindService()`, exposes an `IBinder` client interface, lives only while clients are bound.

**Follow-up:** *You need to upload a file the user just selected. Foreground service or WorkManager?*
> WorkManager with `setExpedited`. It survives process death, retries with backoff, and respects constraints. A foreground service is right only when the user is actively watching continuous work — playback, navigation, a live workout.

---

### Q5. What happens if you start a foreground service from the background on Android 14? `[Mid]`

**Answer**
`ForegroundServiceStartNotAllowedException`, unless the app falls into an exemption: it received a high-priority FCM message, the user interacted with a notification, it is a companion device manager, an exact alarm fired, a Bluetooth or geofence event occurred, or it is a device owner/system app.

On Android 14 you additionally must declare `android:foregroundServiceType` and hold the type's matching permission, or `startForeground` throws.

**Follow-up:** *How do you handle the case where you cannot start it?*
> Catch the exception and fall back to expedited WorkManager, and design so the feature degrades rather than crashing. Never let this exception reach the default handler — it is expected, not exceptional.

---

### Q6. Static vs dynamic BroadcastReceiver registration. `[Mid]`

**Answer**
* **Static** (`<receiver>` in the manifest): works when the app is not running, but since Android 8 implicit broadcasts are blocked for manifest-declared receivers except for a short allowlist (`BOOT_COMPLETED`, `LOCALE_CHANGED`, and similar).
* **Dynamic** (`registerReceiver`): works for any broadcast but only while the registering component is alive, and **must** be unregistered or it leaks the context.

On Android 14+, `registerReceiver` requires an explicit `RECEIVER_EXPORTED` or `RECEIVER_NOT_EXPORTED` flag.

```kotlin
override fun onStart() {
    super.onStart()
    registerReceiver(receiver, IntentFilter(ACTION), RECEIVER_NOT_EXPORTED)
}
override fun onStop() {
    super.onStop()
    unregisterReceiver(receiver)      // Skipping this leaks the Activity
}
```

**Follow-up:** *Why were implicit broadcasts restricted?*
> A single system broadcast could wake dozens of apps simultaneously, each forking a process and running code. The battery and memory cost was disproportionate to the value.

---

### Q7. Ordered vs normal broadcasts, and the security implication. `[Senior]`

**Answer**
A **normal** broadcast is delivered to all receivers asynchronously and in undefined order; it cannot be aborted. An **ordered** broadcast is delivered one receiver at a time by priority; each can modify the result data or call `abortBroadcast()`.

The security implication: a malicious app can register a high-priority receiver for an ordered broadcast, read the payload first, modify the result, and abort delivery before the legitimate receiver ever sees it.

**Follow-up:** *How do you send a broadcast only your own app can receive?*
> Use `LocalBroadcastManager`'s replacement — a shared `Flow` or event bus in a singleton — or send with a signature-level permission (`sendBroadcast(intent, "com.example.permission.INTERNAL")`) and declare that permission with `android:protectionLevel="signature"`.

---

### Q8. What is a ContentProvider and when do you actually need one? `[Mid]`

**Answer**
A ContentProvider exposes structured data through a `content://` URI with CRUD operations and per-URI permission grants. Internally it is a Binder service with a standardized interface.

You need one when: sharing data with **other apps**, integrating with system features that require it (search suggestions, sync adapters, the sharesheet via `FileProvider`), or sharing data across your own **processes**.

You do **not** need one to access your own database from your own process — use Room directly.

**Follow-up:** *What is `FileProvider` and what problem does it solve?*
> Passing a `file://` URI to another app throws `FileUriExposedException` since Android 7. `FileProvider` converts a file into a `content://` URI and, with `FLAG_GRANT_READ_URI_PERMISSION`, grants the recipient temporary read access without opening up your whole storage.

---

### Q9. Explicit vs implicit intents. `[Junior]`

**Answer**
An **explicit** intent names the target `ComponentName` directly; resolution skips the filter database. An **implicit** intent describes an action and lets the system find a handler.

Always use explicit intents for internal navigation. An implicit intent for internal navigation can be intercepted by another installed app that declares a matching filter.

```kotlin
startActivity(Intent(this, DetailActivity::class.java))          // Explicit
startActivity(Intent.createChooser(shareIntent, "Share via"))    // Implicit, guarded
```

**Follow-up:** *Why wrap an implicit intent in `createChooser`?*
> A bare `startActivity` on an implicit intent throws `ActivityNotFoundException` when nothing handles it. `createChooser` always resolves (to the chooser itself), and it also prevents a single installed app from becoming the silent default handler.

---

### Q10. Deep link vs App Link. `[Mid]`

**Answer**
Both use `<intent-filter>` with `ACTION_VIEW`. The difference is verification:
* A **deep link** (custom scheme or unverified https) shows a disambiguation dialog, and a custom scheme can be claimed by any app.
* An **Android App Link** adds `android:autoVerify="true"` and requires a `assetlinks.json` file at `https://yourdomain/.well-known/assetlinks.json` containing your signing certificate's SHA-256. Verified links open your app directly with no dialog.

**Follow-up:** *Verification is failing in production but works in debug. What is the most likely cause?*
> The fingerprint in `assetlinks.json` is the upload key's, not the Play **app signing key's**. With Play App Signing, the installed app is signed by Google's key, so that is the fingerprint that must be published. `adb shell pm get-app-links <pkg>` shows the verification state.

---

### Q11. Explain the Context hierarchy and which context to use where. `[Mid]`

**Answer**
`Context` is abstract; `ContextImpl` does the real work; `ContextWrapper` subclasses (`Application`, `Service`, `ContextThemeWrapper` → `Activity`) delegate to it.

| Need | Context |
|---|---|
| Inflate a themed layout, show a dialog, start an activity normally | **Activity** |
| Anything stored longer than a screen (singleton, DI, WorkManager, Room) | **Application** |
| Start a service, access system services | Either |

**Follow-up:** *Why does inflating with the application context produce unstyled views?*
> `Application` is not a `ContextThemeWrapper`, so it carries the system default theme rather than your app theme. Attribute references like `?attr/colorPrimary` resolve to platform defaults, producing subtly wrong colors.

---

### Q12. What survives a configuration change, and what survives process death? `[Mid]`

**Answer**

| | Config change | Process death | Swipe from Recents |
|---|---|---|---|
| Activity instance | Recreated | Recreated | Gone |
| ViewModel | **Survives** | **Lost** | Lost |
| `SavedStateHandle` / `onSaveInstanceState` Bundle | Survives | **Survives** | **Lost** |
| Static / singleton | Survives | **Lost** | Lost |

**Follow-up:** *How do you actually test process death?*
> Background the app with the Home button (not swipe-away), run `adb shell am kill com.example.app`, then reopen from Recents. Rotation only exercises ViewModel retention, which is the easy half.

---

### Q13. When is `android:configChanges` the right answer? `[Senior]`

**Answer**
Rarely. It is legitimate when the Activity genuinely re-lays-out without needing recreation — a full-screen video player, a map, or a camera preview where recreating would visibly interrupt.

It is the wrong answer when used to "fix" a state-loss bug, because process death will still destroy that state, and because omitting a qualifier (locale, for instance) silently stops the Activity from picking up that configuration change at all.

**Follow-up:** *You add `android:configChanges="orientation|screenSize"` and dark mode stops working. Why?*
> `uiMode` is not in the list, so it still recreates — but if you *had* added `uiMode`, the Activity would no longer recreate and your theme-dependent resources would never be re-resolved. You would then have to handle it manually in `onConfigurationChanged`.

---

### Q14. Explain `SavedStateHandle`. `[Mid]`

**Answer**
A key-value map owned by the ViewModel and backed by the same `Bundle` mechanism as `onSaveInstanceState`, so its contents survive **both** configuration change and system-initiated process death. Navigation arguments are placed into it automatically.

```kotlin
@HiltViewModel
class SearchViewModel @Inject constructor(
    private val saved: SavedStateHandle
) : ViewModel() {
    val query: StateFlow<String> = saved.getStateFlow("query", "")
    fun onQueryChange(v: String) { saved["query"] = v }
}
```

**Follow-up:** *What are its limits?*
> It goes through a Binder transaction, so it is bounded by the ~1 MB buffer and must hold only `Bundle`-compatible types. Store IDs and small primitives; never lists of models or bitmaps.

---

### Q15. Explain the runtime permission flow including the "denied permanently" case. `[Mid]`

**Answer**
There are three states but only two APIs, so you must track the third:
1. **Never asked** — `checkSelfPermission` is denied and `shouldShowRequestPermissionRationale` is `false`.
2. **Denied once** — denied, and `shouldShowRequestPermissionRationale` is `true` → show rationale and re-ask.
3. **Denied permanently** — denied, and rationale is `false` **after** you have asked → only Settings can restore it.

```kotlin
val launcher = registerForActivityResult(RequestPermission()) { granted ->
    when {
        granted -> proceed()
        shouldShowRequestPermissionRationale(CAMERA) -> showRationale()
        else -> showSettingsRedirect()
    }
}
```

**Follow-up:** *Why can you not just cache the granted result?*
> The user can revoke it from Settings while your process is alive, and Android 11+ auto-revokes permissions for apps unused for a few months. Re-check before every use.

---

### Q16. What changed for storage permissions on Android 13? `[Mid]`

**Answer**
`READ_EXTERNAL_STORAGE` is ignored. It is replaced by granular `READ_MEDIA_IMAGES`, `READ_MEDIA_VIDEO`, and `READ_MEDIA_AUDIO`. Android 14 adds partial access (`READ_MEDIA_VISUAL_USER_SELECTED`), where the user grants access to specific items only.

The better answer for most apps is the **Photo Picker**, which requires no permission at all.

```kotlin
val pick = registerForActivityResult(PickVisualMedia()) { uri -> uri?.let(::load) }
pick.launch(PickVisualMediaRequest(PickVisualMedia.ImageOnly))
```

**Follow-up:** *You need to save a photo to the gallery. What permission?*
> None on API 29+. Insert into `MediaStore.Images` with `RELATIVE_PATH` set; scoped storage grants write access to your own inserted entries without any permission.

---

### Q17. What is a notification channel and what can the app control? `[Junior]`

**Answer**
A channel is a user-facing category for notifications, mandatory since Android 8. The app sets importance, sound, and vibration **at creation**; after that only the user can change them. The app can never raise importance later.

To change defaults you must create a channel with a **new ID**, which resets the user's preference — user-hostile, so design channels carefully upfront.

**Follow-up:** *You call `notify()` and nothing appears, with no exception. Why?*
> Notifications are disabled — either the app-level toggle, the channel, or the missing `POST_NOTIFICATIONS` runtime permission on Android 13+. `notify()` silently no-ops. Check `NotificationManagerCompat.areNotificationsEnabled()`.

---

### Q18. Explain `PendingIntent` and its flags. `[Mid]`

**Answer**
A `PendingIntent` is a token granting another process (usually the system UI) permission to execute an Intent **as your app**, with your identity and permissions.

Its identity is `(requestCode, Intent action/data/component/categories)` — **extras are not part of the key**. Two PendingIntents differing only in extras collide.

| Flag | Effect |
|---|---|
| `FLAG_UPDATE_CURRENT` | Reuse the existing one but replace its extras |
| `FLAG_CANCEL_CURRENT` | Cancel the old one and create a new one |
| `FLAG_IMMUTABLE` | The recipient cannot modify the Intent — the safe default |
| `FLAG_MUTABLE` | Required for direct-reply actions and Bubbles |

Since API 31, one of `IMMUTABLE`/`MUTABLE` is mandatory or it throws.

**Follow-up:** *Two notifications open the same screen with different IDs and both show the first ID. Why?*
> Same request code and structurally identical Intents, so the second `getActivity` call returned the cached first PendingIntent with its original extras. Use a unique `requestCode` per item, plus `FLAG_UPDATE_CURRENT`.

---

### Q19. Why must `android:exported` be explicit since Android 12? `[Mid]`

**Answer**
Previously, a component with an `<intent-filter>` defaulted to `exported="true"`, so developers unknowingly exposed internal activities, services, and receivers to every app on the device. Targeting API 31+, omitting the attribute on a component with a filter is an **install-time failure**, forcing an explicit decision.

**Follow-up:** *What is the risk of an exported service with no permission?*
> Any app can bind to it and invoke its methods with your app's UID and permissions — a privilege escalation. Exported components must either be genuinely public or be protected with a `signature`-level permission.

---

### Q20. What is `taskAffinity` and how does it interact with `FLAG_ACTIVITY_NEW_TASK`? `[Senior]`

**Answer**
`taskAffinity` names the task an Activity prefers to belong to; it defaults to the application ID. With `FLAG_ACTIVITY_NEW_TASK`, the system looks for an existing task with a matching affinity and places the Activity there, or creates a new task if none exists.

Without `NEW_TASK`, affinity is mostly ignored for a `standard` Activity — it goes into the caller's task.

**Follow-up:** *What is task hijacking (StrandHogg)?*
> A malicious app declares the same `taskAffinity` as a target app plus `allowTaskReparenting`, so its Activity is placed into the victim's task and appears when the user opens the victim app — enabling convincing phishing. Mitigation: set `android:taskAffinity=""` on sensitive Activities and use `singleTask` for the launcher Activity.

---

### Q21. Explain `onSaveInstanceState` vs `onPause` vs `onStop` timing. `[Mid]`

**Answer**
* `onPause` — the Activity is losing focus but may still be visible (a dialog, split screen). Must be fast.
* `onStop` — no longer visible. The right place to release resources.
* `onSaveInstanceState` — called **before** the Activity may be killed. On API 28+, it runs *after* `onStop`; on earlier versions, before it.

Crucially, `onSaveInstanceState` is **not** called when the user explicitly finishes the Activity (back press, `finish()`), because there is no state to restore.

**Follow-up:** *So where does durable persistence belong?*
> In the repository, as changes happen — not in a lifecycle callback. Lifecycle callbacks handle transient UI state (scroll position, expanded rows); anything the user would be upset to lose belongs in a database or DataStore immediately.

---

### Q22. What is the Fragment lifecycle and what is the `viewLifecycleOwner` trap? `[Mid]`

**Answer**
A Fragment has two lifecycles: the **instance** (`onAttach` → `onDestroy`) and the **view** (`onCreateView` → `onDestroyView`). When a Fragment goes onto the back stack, the view is destroyed but the instance survives.

Collecting a Flow with `this` (the Fragment) as the lifecycle owner means the collection outlives the view, keeps a reference to the destroyed view hierarchy, and delivers updates to detached views.

```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(STARTED) { viewModel.state.collect(::render) }
}
override fun onDestroyView() { super.onDestroyView(); binding = null }
```

**Follow-up:** *Why does `binding = null` in `onDestroyView` matter if the Fragment is going to be destroyed anyway?*
> It is not going to be destroyed — that is the whole point. On the back stack the instance lives on, and a non-null binding keeps the entire destroyed view hierarchy in memory.

---

### Q23. `commit()` vs `commitNow()` vs `commitAllowingStateLoss()`. `[Senior]`

**Answer**
* `commit()` — schedules the transaction asynchronously on the main thread's queue. Throws `IllegalStateException` if the Activity's state was already saved.
* `commitNow()` — executes synchronously. Cannot be used with `addToBackStack`.
* `commitAllowingStateLoss()` — same as `commit` but silently drops the transaction rather than throwing after state save.

**Follow-up:** *Is `commitAllowingStateLoss` an acceptable fix for the `IllegalStateException`?*
> Only when losing the transaction is genuinely acceptable — a transient UI update. It is usually masking the real bug: performing a transaction from an asynchronous callback that arrives after `onSaveInstanceState`. The correct fix is to drive fragment transactions from lifecycle-aware state, so they only run while the Fragment is `STARTED`.

---

### Q24. How do you share data between two Fragments? `[Junior]`

**Answer**
Options in order of preference:
1. **A shared ViewModel** scoped to the Activity or a navigation graph (`by activityViewModels()` / `by navGraphViewModels(R.id.flow)`).
2. **The Fragment Result API** (`setFragmentResult` / `setFragmentResultListener`) for a one-shot result.
3. **`NavBackStackEntry.savedStateHandle`** for a result that must survive process death.

Never through an interface implemented by the Activity — that couples both fragments to the host.

**Follow-up:** *Why is a graph-scoped ViewModel better than an Activity-scoped one for a checkout flow?*
> It is cleared automatically when the flow is popped, so a cancelled checkout does not leave stale state that reappears when the user starts a new one.

---

### Q25. What is `RemoteCallbackList` and why not a plain list? `[Senior]`

**Answer**
When a service holds callbacks from other processes, those processes can die at any time. A plain `List<IBinder>` accumulates dead references and leaks; calling a dead one throws `DeadObjectException`.

`RemoteCallbackList` registers a death recipient for each entry and removes it automatically when the client process dies, and its `beginBroadcast`/`finishBroadcast` pair handles concurrent modification safely.

**Follow-up:** *You still get `DeadObjectException` inside the broadcast loop. Why?*
> A process can die between the death notification and your call. Wrap each individual invocation in a `try`/`runCatching`; the list cleans up but cannot prevent the race.

---

### Q26. What does `START_STICKY` vs `START_NOT_STICKY` vs `START_REDELIVER_INTENT` mean? `[Mid]`

**Answer**
The return value of `onStartCommand` tells the system what to do if the service is killed:
* `START_STICKY` — recreate the service with a **null** Intent. For services that manage ongoing state (a music player).
* `START_NOT_STICKY` — do not recreate. For work that is pointless to resume.
* `START_REDELIVER_INTENT` — recreate and redeliver the **last** Intent. For work that must complete.

**Follow-up:** *Does any of this still matter on modern Android?*
> Much less than it used to. Background services are already restricted, and WorkManager provides stronger guarantees with retry and constraints. These flags remain relevant mainly for long-running foreground services.

---

### Q27. Explain the manifest merger and how you resolve a conflict. `[Mid]`

**Answer**
Manifests merge with priority: **build variant → main → library manifests** (in dependency order). Conflicts occur when a library declares an attribute your app also declares with a different value.

```xml
<application
    android:allowBackup="false"
    tools:replace="android:allowBackup">   <!-- Explicitly win over the library -->

    <provider
        android:name="com.thirdparty.InitProvider"
        android:authorities="${applicationId}.thirdparty-init"
        tools:node="remove" />             <!-- Drop a component a library injected -->
</application>
```

**Follow-up:** *How do you find out which library injected an unwanted component?*
> `app/build/outputs/logs/manifest-merger-debug-report.txt` lists every merge decision with the source file, and the merged output is at `app/build/intermediates/merged_manifest/debug/AndroidManifest.xml`.

---

### Q28. What is `android:launchMode="singleInstancePerTask"` for? `[Senior]`

**Answer**
Added in API 31, it allows an Activity to be the root of a task while permitting **multiple** tasks each containing one instance. `singleTask` allows only one instance device-wide, which broke multi-window and multi-display use cases — a user could not open two documents from the same app side by side.

**Follow-up:** *Which scenario specifically motivated it?*
> Freeform/desktop windowing and foldables, where the same app should legitimately appear as several independent windows, each with its own task and back stack.

---

### Q29. What happens to a bound service when the last client unbinds? `[Mid]`

**Answer**
If the service was **only** bound (never `startService`d), it is destroyed: `onUnbind` → `onDestroy`. If it was also started, it keeps running until `stopSelf`/`stopService`.

A service that must both provide a client interface and survive unbinding uses both mechanisms — `startService` for lifetime and `bindService` for the interface.

**Follow-up:** *You bind with `BIND_AUTO_CREATE` from an Activity and the service dies on rotation. Why?*
> The Activity unbinds during destruction and rebinds in the new instance, and if no client is bound in the gap the service is destroyed and recreated. Bind from an Activity-retained scope, or use `startService` alongside binding, or move the work to WorkManager.

---

### Q30. Design a component structure for an app that must play audio, sync data hourly, and respond to a "device unlocked" event. `[Senior]`

**Answer**
* **Audio playback** → `MediaSessionService` (Media3) as a **foreground service** with `foregroundServiceType="mediaPlayback"`. It must be foreground because the user expects it to continue and to be controllable from the lock screen.
* **Hourly sync** → `PeriodicWorkRequest` (minimum 15 min interval) with `NetworkType.CONNECTED` and `requiresBatteryNotLow`. Never `AlarmManager` — the timing is not user-visible so it should be batched with other apps' work.
* **Device unlocked** → `ACTION_USER_PRESENT` is one of the broadcasts still allowed for a manifest receiver; register it statically, and have the receiver do nothing but enqueue work, since receivers have a 10-second budget.

The unifying rule: foreground service for continuous user-visible work, WorkManager for deferrable guaranteed work, receivers only as thin triggers.

**Follow-up:** *The sync sometimes does not run for days on certain devices. What do you check?*
> App Standby bucket (`adb shell am get-standby-bucket`) — an infrequently-opened app drops to `rare` or `restricted` with a ~24-hour job quota. Also check OEM battery managers (Xiaomi, Huawei, Oppo), which kill background work beyond AOSP rules.

---

## Related

* [`01_system_internals.md`](./01_system_internals.md) — Binder, IPC, process model
* [`07_background_work.md`](./07_background_work.md) — WorkManager, alarms, Doze, FCM
* [`04_jetpack_architecture.md`](./04_jetpack_architecture.md) — ViewModel, lifecycle, Navigation
* [`00_INDEX.md`](./00_INDEX.md) — full index
