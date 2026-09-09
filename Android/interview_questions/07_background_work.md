# 7. Background Work & Scheduling — 20 Questions

> WorkManager, AlarmManager, JobScheduler, Doze, App Standby buckets, FCM, foreground services.
> Reference material: [`../android.md`](../android.md) Module 7.

---

### Q1. When do you use WorkManager vs a foreground service vs an exact alarm? `[Mid]`

**Answer**
The decision rule:
* Would the user notice it **not happening right now**? → **Foreground service** (music, navigation, active tracking).
* Would the user notice it **not happening at a specific wall-clock time**? → **Exact alarm** (alarm clock, medication reminder).
* Everything else → **WorkManager** (sync, upload, cleanup, log shipping).

**Follow-up:** *A user taps "upload photo" and backgrounds the app. Which one?*
> WorkManager with `setExpedited`. It survives process death, retries with backoff, and respects constraints. A foreground service would work but shows a permanent notification for something the user does not need to watch.

---

### Q2. How does WorkManager guarantee execution? `[Mid]`

**Answer**
It persists every `WorkRequest` in its own Room database. On device reboot, its `BOOT_COMPLETED` receiver reschedules pending work. Underneath it delegates to `JobScheduler` (API 23+) or `AlarmManager` + `BroadcastReceiver` on older devices.

Guaranteed means "will eventually run if constraints are met and the app is not uninstalled" — not "will run on time".

**Follow-up:** *What is the one thing WorkManager cannot guarantee?*
> Timing. A `PeriodicWorkRequest` has a 15-minute minimum interval and the OS may delay execution substantially based on Doze and the App Standby bucket. Never build a clock or a deadline on it.

---

### Q3. Why must a Worker be idempotent? `[Mid]`

**Answer**
WorkManager can re-run a worker after a process kill mid-execution, and `Result.retry()` re-runs it explicitly. If `doWork` charges a card or posts a comment, running it twice does it twice.

Make it idempotent with a client-generated operation ID the server deduplicates on, or by checking whether the work is already done before doing it.

**Follow-up:** *How do you make an upload idempotent?*
> Generate a UUID when enqueuing, pass it in the input `Data`, and send it as an idempotency key. The server returns the original result for a repeated key, so a retry after a lost response cannot create a duplicate.

---

### Q4. What are the WorkManager `Result` values? `[Junior]`

**Answer**
* `Result.success()` — done; chained work proceeds. Can carry output `Data`.
* `Result.failure()` — done, unrecoverably; chained work is cancelled.
* `Result.retry()` — reschedule according to the backoff policy.

**Follow-up:** *When should a network failure be `retry` and when `failure`?*
> `retry` for transient failures — timeout, 5xx, no connectivity. `failure` for permanent ones — 400, 401 after refresh failed, malformed input. Retrying a 400 forever burns battery and never succeeds.

---

### Q5. Explain `ExistingWorkPolicy` and `ExistingPeriodicWorkPolicy`. `[Mid]`

**Answer**
For unique work:
* `REPLACE` — cancel the existing and enqueue the new.
* `KEEP` — do nothing if one already exists.
* `APPEND` / `APPEND_OR_REPLACE` — chain after the existing work.

For periodic work:
* `KEEP` — preserve the existing schedule (so the interval is not reset on every app launch).
* `UPDATE` — apply the new request's configuration to the existing schedule.

**Follow-up:** *You enqueue periodic sync in `Application.onCreate` with `REPLACE` and it never runs. Why?*
> Every app launch cancels and re-enqueues it, restarting the interval from zero. If the user opens the app more often than the interval, it never fires. Use `KEEP`.

---

### Q6. What is expedited work and what are its limits? `[Mid]`

**Answer**
`setExpedited` requests immediate execution for user-initiated work. It is a **request**, not a guarantee — the OS grants a limited per-app quota that depends on the App Standby bucket.

You must supply an out-of-quota policy: `RUN_AS_NON_EXPEDITED_WORK_REQUEST` (fall back to normal) or `DROP_WORK_REQUEST`.

```kotlin
OneTimeWorkRequestBuilder<SendWorker>()
    .setExpedited(OutOfQuotaPolicy.RUN_AS_NON_EXPEDITED_WORK_REQUEST)
    .build()
```

**Follow-up:** *On API < 31, what does expedited work actually do?*
> It runs as a foreground service, so the worker must implement `getForegroundInfo()` and supply a notification. Omitting it throws `IllegalStateException` on those devices only — a classic bug found late.

---

### Q7. How do you chain work and pass data between stages? `[Mid]`

**Answer**
```kotlin
WorkManager.getInstance(context)
    .beginUniqueWork("upload", ExistingWorkPolicy.REPLACE, compress)
    .then(upload)          // Runs only if compress succeeded; gets its output as input
    .then(cleanup)
    .enqueue()

// Inside a worker
override suspend fun doWork(): Result {
    val path = inputData.getString("path") ?: return Result.failure()
    return Result.success(workDataOf("compressed_path" to compress(path)))
}
```

**Follow-up:** *What is the size limit on `Data`?*
> About 10 KB. It goes through a Binder transaction, so a larger payload throws. Pass a file path or a database ID and read the actual content inside the worker.

---

### Q8. What constraints can WorkManager apply and why do they matter? `[Junior]`

**Answer**
Network type (`CONNECTED`, `UNMETERED`, `NOT_ROAMING`), charging, battery not low, storage not low, and device idle.

They matter because they let the OS **batch** work across apps into maintenance windows, which is far cheaper for battery than each app waking the device independently.

```kotlin
Constraints.Builder()
    .setRequiredNetworkType(NetworkType.UNMETERED)   // Wi-Fi only
    .setRequiresBatteryNotLow(true)
    .build()
```

**Follow-up:** *Constraints are met but the work still does not run. What do you check?*
> The App Standby bucket. A `rare` or `restricted` app has a job quota of roughly one execution per day regardless of constraints. `adb shell am get-standby-bucket <pkg>` shows it.

---

### Q9. Explain Doze mode. `[Mid]`

**Answer**
When the device is stationary, unplugged, and screen-off for a while, the system enters Doze: network access is suspended, alarms are deferred, jobs and syncs are suspended, and wakelocks are ignored — except during periodic **maintenance windows**, which grow further apart the longer Doze persists.

Escapes: `setExactAndAllowWhileIdle` alarms, high-priority FCM messages, and foreground services already running.

**Follow-up:** *How do you test Doze?*
> ```
> adb shell dumpsys battery unplug
> adb shell dumpsys deviceidle force-idle
> ```
> Then `adb shell dumpsys deviceidle step` to advance stage by stage. Testing with the screen on and the device plugged in never enters Doze, which is why these bugs reach production.

---

### Q10. What are App Standby buckets? `[Mid]`

**Answer**
A per-app adaptive classification based on usage recency and frequency, determining job and alarm quotas.

| Bucket | Assigned when | Job quota |
|---|---|---|
| Active | In use now | Unlimited |
| Working set | Used regularly | ~every 2 h |
| Frequent | Used often | ~every 8 h |
| Rare | Used infrequently | ~every 24 h |
| Restricted | Heavy background use, or user-set | ~once/day |

**Follow-up:** *Can your app influence its own bucket?*
> Not directly. The only lever is genuine user engagement. Design so that degraded background frequency is acceptable — and surface to the user when a feature they enabled is being throttled, rather than failing silently.

---

### Q11. When is an exact alarm justified, and what does it require now? `[Mid]`

**Answer**
Only when the user set a specific time: an alarm clock, a calendar reminder, a medication reminder.

Android 12+ requires `SCHEDULE_EXACT_ALARM`, which on Android 13+ is **not** auto-granted for most apps — the user must enable it in Settings. `USE_EXACT_ALARM` (auto-granted) is restricted by Play policy to alarm clock and calendar apps.

```kotlin
if (Build.VERSION.SDK_INT >= S && !alarmManager.canScheduleExactAlarms()) {
    startActivity(Intent(Settings.ACTION_REQUEST_SCHEDULE_EXACT_ALARM))
    return
}
alarmManager.setExactAndAllowWhileIdle(RTC_WAKEUP, triggerAt, pendingIntent)
```

**Follow-up:** *Why `setExactAndAllowWhileIdle` rather than `setExact`?*
> `setExact` is deferred to the next maintenance window during Doze, so a 3 a.m. alarm can fire at 5 a.m. The `AllowWhileIdle` variant fires during Doze — rate-limited to roughly once every 9 minutes per app, which is fine for user-set alarms.

---

### Q12. Do alarms survive a reboot? `[Junior]`

**Answer**
No. All alarms are cleared on reboot. You must register a `BOOT_COMPLETED` receiver and reschedule them from persisted state.

```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<receiver android:name=".BootReceiver" android:exported="false">
    <intent-filter><action android:name="android.intent.action.BOOT_COMPLETED" /></intent-filter>
</receiver>
```

**Follow-up:** *Does WorkManager need the same treatment?*
> No — WorkManager persists its own state and reschedules automatically after boot. That persistence is precisely its value over raw `JobScheduler`.

---

### Q13. Explain the FCM notification-vs-data message distinction. `[Mid]`

**Answer**

| | `notification` payload | `data` payload |
|---|---|---|
| App foreground | `onMessageReceived` called | `onMessageReceived` called |
| App **background** | **Shown by the system tray; your code never runs** | `onMessageReceived` called |
| Customizable | Limited | Fully |

Use **data messages** for anything that needs logic, and build the notification yourself.

**Follow-up:** *Why do people get this wrong so often?*
> It works perfectly in development, because the app is in the foreground while you test. The bug only appears when the app is backgrounded — exactly the case push exists for.

---

### Q14. What is FCM message priority and when is `high` appropriate? `[Mid]`

**Answer**
`normal` priority is batched and can be delayed indefinitely in Doze. `high` wakes the device and delivers immediately, breaking through Doze.

`high` is for genuinely time-sensitive, user-visible messages — a chat message, an incoming call. Android tracks a per-app budget, and using `high` for background sync gets the app throttled, which then delays the messages that actually matter.

**Follow-up:** *What TTL should you set?*
> Match the message's usefulness. A chat message might use `ttl=3600s`; a "your ride is here" notification should have a short TTL so it is not delivered 20 minutes late when it is meaningless or misleading.

---

### Q15. How long does `onMessageReceived` have to run, and what if you need longer? `[Mid]`

**Answer**
Roughly 10–20 seconds, on a background thread. Beyond that the process may be killed.

For anything longer, enqueue WorkManager from `onMessageReceived` and return immediately.

```kotlin
override fun onMessageReceived(message: RemoteMessage) {
    when (message.data["type"]) {
        "chat" -> Notifier.show(this, message.data)            // Fast, do it inline
        "sync" -> WorkManager.getInstance(this)
            .enqueue(OneTimeWorkRequestBuilder<SyncWorker>().build())
    }
}
```

**Follow-up:** *Why is the FCM token not stable, and what must you do about it?*
> It rotates on app restore, clear-data, reinstall, and periodically. Re-upload it in `onNewToken` — through WorkManager, since that callback can fire with no connectivity — and also verify it on app start.

---

### Q16. Why does the same FCM message sometimes arrive twice? `[Mid]`

**Answer**
FCM guarantees **at-least-once** delivery. Network conditions and retries can produce duplicates.

Deduplicate on a server-supplied message ID: keep a small recent-ID set (or a Room table with a TTL) and ignore a repeat.

**Follow-up:** *Where do you deduplicate — in the service or in the repository?*
> In the repository or database, keyed on the server message ID with `onConflict = IGNORE`. Deduplicating in the service only covers the push path; the same message can also arrive from a history fetch.

---

### Q17. What are foreground service types and why did Android 14 require them? `[Mid]`

**Answer**
Every foreground service must declare `android:foregroundServiceType` (`location`, `mediaPlayback`, `dataSync`, `camera`, `microphone`, `connectedDevice`, …) and hold the matching permission. Calling `startForeground` without them throws.

The motivation: apps were using foreground services generically to avoid background limits. Typed services let the OS apply per-type rules — Android 15 added a timeout for `dataSync`, for example.

```xml
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_LOCATION" />
<service android:name=".TrackingService" android:foregroundServiceType="location" />
```

**Follow-up:** *What happens if the declared type does not match the actual work?*
> Play policy violation and possible rejection, and on some versions a runtime `SecurityException`. The type is a declaration the OS and Play both enforce.

---

### Q18. Why does background work stop entirely on some Chinese OEM devices? `[Senior]`

**Answer**
Xiaomi, Huawei, Oppo, Vivo, and others add aggressive process management beyond AOSP: apps not on a user-managed "autostart" allowlist are killed on screen-off, and background work is terminated regardless of WorkManager's guarantees.

There is no API to detect or bypass it. The practical approach: detect the manufacturer, and for features that genuinely require background execution, guide the user to the OEM's autostart/battery settings with clear instructions.

**Follow-up:** *Is requesting battery-optimization exemption a fix?*
> Only partially, and Play restricts `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` to approved use cases. It also does not affect OEM-specific killers, which operate outside the AOSP battery-optimization framework.

---

### Q19. How do you observe and test WorkManager? `[Mid]`

**Answer**
Observe with `getWorkInfoByIdFlow` / `getWorkInfosForUniqueWorkLiveData`; test with `WorkManagerTestInitHelper`, which gives a synchronous executor and a driver for constraints and delays.

```kotlin
@Before fun setup() {
    val config = Configuration.Builder()
        .setExecutor(SynchronousExecutor())
        .setMinimumLoggingLevel(Log.DEBUG)
        .build()
    WorkManagerTestInitHelper.initializeTestWorkManager(context, config)
}

@Test fun `sync succeeds when constraints are met`() {
    val request = OneTimeWorkRequestBuilder<SyncWorker>()
        .setConstraints(Constraints.Builder().setRequiredNetworkType(CONNECTED).build())
        .build()
    val wm = WorkManager.getInstance(context)
    wm.enqueue(request).result.get()

    WorkManagerTestInitHelper.getTestDriver(context)!!.setAllConstraintsMet(request.id)

    assertThat(wm.getWorkInfoById(request.id).get().state).isEqualTo(WorkInfo.State.SUCCEEDED)
}
```

**Follow-up:** *How do you test the worker's logic without WorkManager at all?*
> Extract the logic into an injected class and unit-test that. `TestListenableWorkerBuilder` can construct a worker directly if you must test `doWork`, but a thin worker delegating to a testable class is better design.

---

### Q20. Design the background architecture for an app that syncs data hourly, uploads user photos, tracks a run in real time, and sends chat push. `[Senior]`

**Answer**
* **Hourly sync** → `PeriodicWorkRequest` (interval 1 h, real minimum 15 min) with `NetworkType.CONNECTED` and `requiresBatteryNotLow`, `ExistingPeriodicWorkPolicy.KEEP`. Idempotent, with a last-sync cursor so a delayed run still catches up.
* **Photo upload** → `OneTimeWorkRequest` per photo with `setExpedited`, an idempotency key, exponential backoff, and `NetworkType.UNMETERED` unless the user opted into cellular. Chained: compress → upload → cleanup.
* **Run tracking** → **foreground service** with `foregroundServiceType="location"` and `FOREGROUND_SERVICE_LOCATION`. This is genuinely continuous user-visible work; WorkManager cannot do it. Buffer points locally and flush via WorkManager so a network gap does not lose data.
* **Chat push** → FCM **data** messages, `high` priority with a short TTL, deduplicated on server message ID, with `onMessageReceived` posting the notification inline and enqueuing WorkManager for anything slower.

**Cross-cutting decisions to state**
* All work idempotent, because WorkManager re-runs.
* A single `SyncWorker` with a unique name, so concurrent triggers (push, manual pull, periodic) collapse rather than racing.
* Instrument success/failure rates — silent background failure is invisible without telemetry.

**Follow-up:** *Three things can trigger a sync — push, pull-to-refresh, and the periodic job. How do you prevent them racing?*
> `enqueueUniqueWork` with a single name and `ExistingWorkPolicy.KEEP`, so a sync already running or queued absorbs the new trigger. Combined with a last-sync cursor, the result is at-most-one-in-flight with no lost updates.

---

## Related

* [`02_components_manifest.md`](./02_components_manifest.md) — services, receivers, notifications
* [`06_data_networking.md`](./06_data_networking.md) — the sync and outbox patterns these jobs execute
* [`10_performance_memory.md`](./10_performance_memory.md) — battery and wakelock impact
* [`00_INDEX.md`](./00_INDEX.md) — full index
