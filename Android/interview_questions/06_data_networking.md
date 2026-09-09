# 6. Data Storage & Networking — 30 Questions

> Room, SQLite, DataStore, Retrofit, OkHttp, serialization, caching, image loading, error modelling.
> Reference material: [`../android.md`](../android.md) Module 6.

---

### Q1. What are Room's three components? `[Junior]`

**Answer**
* **`@Entity`** — a class mapping to a table.
* **`@Dao`** — an interface of query methods; Room validates the SQL at **compile time**.
* **`@Database`** — the holder that ties entities and DAOs together and manages the connection.

**Follow-up:** *What is Room's main advantage over raw SQLite?*
> Compile-time SQL verification. A typo in a column name is a build error rather than a runtime crash on a screen QA did not open. It also generates the boilerplate cursor-to-object mapping and integrates with Flow for observable queries.

---

### Q2. How do Room migrations work and what happens if you get one wrong? `[Mid]`

**Answer**
Increment the database version and supply a `Migration` with the SQL to transform the old schema into the new one. Room validates the resulting schema against the expected one at open time and throws `IllegalStateException` on mismatch.

```kotlin
val MIGRATION_1_2 = object : Migration(1, 2) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("ALTER TABLE user ADD COLUMN age INTEGER NOT NULL DEFAULT 0")
    }
}
Room.databaseBuilder(context, AppDatabase::class.java, "app.db")
    .addMigrations(MIGRATION_1_2)
    .build()
```

**Follow-up:** *Is `fallbackToDestructiveMigration()` ever acceptable?*
> Only for a cache that can be fully rebuilt from the network, and only with that decision documented. It **deletes all user data**. For anything the user created, a missing migration must be a crash in development, not silent data loss in production.

---

### Q3. How do you test a Room migration? `[Senior]`

**Answer**
Export schemas (`room.schemaLocation`) and use `MigrationTestHelper`, which creates the old schema, runs the migration, and validates the result.

```kotlin
@get:Rule val helper = MigrationTestHelper(
    InstrumentationRegistry.getInstrumentation(), AppDatabase::class.java
)

@Test fun migrate1To2() {
    helper.createDatabase(TEST_DB, 1).apply {
        execSQL("INSERT INTO user (id, name) VALUES (1, 'Ada')")
        close()
    }
    val db = helper.runMigrationsAndValidate(TEST_DB, 2, true, MIGRATION_1_2)
    db.query("SELECT age FROM user WHERE id = 1").use {
        it.moveToFirst()
        assertThat(it.getInt(0)).isEqualTo(0)     // The default was applied
    }
}
```

**Follow-up:** *Why must you insert data before migrating?*
> A migration that drops and recreates a table passes schema validation while destroying every row. Only asserting on real data after the migration catches that.

---

### Q4. Why should a DAO return `Flow<List<T>>` rather than `List<T>`? `[Mid]`

**Answer**
Room's `InvalidationTracker` watches the tables a query touches and re-runs the query whenever they change, emitting the new result. The UI therefore updates automatically after any write, with no manual refresh and no risk of the screen disagreeing with the database.

**Follow-up:** *Does the Flow re-emit when an unrelated table changes?*
> No — invalidation is per-table, derived from the query's referenced tables. But it does re-emit on **any** write to a table the query touches, even one that does not change the result, so add `distinctUntilChanged()` when re-rendering is expensive.

---

### Q5. SharedPreferences vs DataStore. `[Junior]`

**Answer**

| | SharedPreferences | DataStore |
|---|---|---|
| API | Synchronous, blocking | Coroutines + Flow |
| Main-thread safety | `commit()` blocks; `apply()` still does disk IO and can block at `onPause` | Fully async |
| Errors | Runtime exceptions | Catchable via the Flow |
| Transactions | No | Yes |
| Type safety | No | Yes with Proto DataStore |

**Follow-up:** *`apply()` is asynchronous — so why is SharedPreferences still a main-thread problem?*
> The first read loads and parses the whole XML file synchronously, and pending `apply()` writes are **force-flushed on the main thread** during `onPause`/`onStop` by `QueuedWork`. StrictMode surfaces both.

---

### Q6. Preferences DataStore vs Proto DataStore. `[Mid]`

**Answer**
Preferences DataStore is a typed key-value store with no schema — same shape as SharedPreferences but async and transactional. Proto DataStore uses a protobuf schema, giving a real typed object, compile-time field checking, and defined defaults.

Use Proto when the stored state is a structured object (user settings with nested types); Preferences for a handful of independent flags.

**Follow-up:** *How do you migrate from SharedPreferences to DataStore without losing data?*
> `SharedPreferencesMigration` passed to the DataStore builder. It runs once on first access, copies the values, and can delete the old file afterward.

---

### Q7. Explain Retrofit's relationship to OkHttp. `[Junior]`

**Answer**
Retrofit is a type-safe wrapper that turns an annotated Kotlin interface into HTTP calls. OkHttp is the engine underneath — sockets, connection pooling, HTTP/2, redirects, caching, retries.

Retrofit handles serialization and the call adapter (suspend functions, Flow, Call); OkHttp handles the network.

**Follow-up:** *Where do you add an auth header — a Retrofit annotation or an OkHttp interceptor?*
> An OkHttp interceptor, so every request gets it automatically and the token can be read fresh at call time. A `@Header` parameter on every method is repetitive and captures the token at the wrong moment.

---

### Q8. Application interceptor vs network interceptor. `[Mid]`

**Answer**

| | Application interceptor | Network interceptor |
|---|---|---|
| Called | Once per call, **even on a cache hit** | Once per network request, so multiple times on redirects and retries |
| Sees | The logical request/response | The actual wire request, including redirects and `Content-Encoding` |
| Use for | Auth headers, logging the logical call, retry policy | Inspecting redirects, cache headers, wire-level debugging |

**Follow-up:** *You add a logging interceptor and never see cached responses. Which one did you use?*
> A network interceptor — it is skipped entirely when the response is served from cache. Move it to an application interceptor to see every logical call.

---

### Q9. What is `Authenticator` in OkHttp and how is it different from an interceptor? `[Senior]`

**Answer**
An `Authenticator` is invoked specifically on a **401** response. It lets you refresh the token and return a new request, which OkHttp automatically retries — the standard token-refresh pattern.

```kotlin
class TokenAuthenticator(private val store: TokenStore) : Authenticator {
    override fun authenticate(route: Route?, response: Response): Request? {
        if (responseCount(response) >= 2) return null      // Give up rather than loop forever
        val fresh = synchronized(this) {                    // One refresh for concurrent 401s
            store.refreshBlocking() ?: return null
        }
        return response.request.newBuilder()
            .header("Authorization", "Bearer $fresh")
            .build()
    }
}
```

**Follow-up:** *Why is the synchronization important?*
> Ten concurrent requests can all get 401 simultaneously. Without a lock, all ten refresh, and nine of the resulting tokens are immediately invalidated by the tenth — often logging the user out.

---

### Q10. How does OkHttp's HTTP cache work? `[Mid]`

**Answer**
It is a disk cache keyed by URL, obeying standard HTTP semantics from response headers:

| Header | Effect |
|---|---|
| `Cache-Control: max-age=60` | Served from cache for 60 s with no network call |
| `ETag` / `If-None-Match` | Conditional request; a `304` saves the body transfer |
| `Cache-Control: no-store` | Never cached |
| `only-if-cached` | Cache-only; fails with `504` if absent — the offline path |

```kotlin
OkHttpClient.Builder()
    .cache(Cache(File(context.cacheDir, "http"), 20L * 1024 * 1024))
    .build()
```

**Follow-up:** *Your server sends no cache headers. Can you cache anyway?*
> Yes, by rewriting the response in a network interceptor to add `Cache-Control: max-age=...`. Do it deliberately — you are overriding the server's intent, and doing it for user-specific or rapidly-changing data serves stale content.

---

### Q11. kotlinx.serialization vs Moshi vs Gson. `[Mid]`

**Answer**

| | kotlinx.serialization | Moshi (codegen) | Gson |
|---|---|---|---|
| Adapters | Compiler plugin | KSP | Runtime reflection |
| Kotlin null safety | Enforced | Enforced | **Broken** |
| Default values | Honored | Honored | Ignored |
| Multiplatform | Yes | No | No |

**Follow-up:** *Why exactly does Gson break Kotlin null safety?*
> It instantiates objects via `Unsafe.allocateInstance`, bypassing the Kotlin-generated constructor that performs null checks and applies defaults. A missing JSON field therefore leaves a non-null `String` holding `null`, and the NPE surfaces far from the parse site.

---

### Q12. What is the single most important `Json` configuration option and why? `[Mid]`

**Answer**
`ignoreUnknownKeys = true`. Without it, the first field the backend adds throws `SerializationException` in every already-installed client — an outage you cannot fix without a release.

```kotlin
val json = Json {
    ignoreUnknownKeys = true
    explicitNulls = false        // Omit nulls when writing
    coerceInputValues = true     // Use the default when the server sends null for a non-null field
}
```

**Follow-up:** *Why should DTOs not be used as domain models?*
> Every backend rename then ripples through the ViewModel and UI, and the domain layer becomes coupled to the wire format's optionality. Map at the repository boundary so wire changes stop there.

---

### Q13. How do you model a polymorphic API response? `[Senior]`

**Answer**
A sealed hierarchy with a class discriminator.

```kotlin
@Serializable
sealed interface Notification {
    @Serializable @SerialName("message")        data class Message(val body: String) : Notification
    @Serializable @SerialName("friend_request") data class FriendRequest(val userId: Long) : Notification
}
// Reads { "type": "message", "body": "hi" }; override the key with Json { classDiscriminator = "kind" }
```

**Follow-up:** *The server adds a new type your app does not know. What happens?*
> `SerializationException` on that element. Handle it with a `JsonContentPolymorphicSerializer` that falls back to an `Unknown` variant, so a new server type degrades gracefully instead of crashing the list.

---

### Q14. Design an offline-first data layer. `[Senior]`

**Answer**
The database is the single source of truth. The UI observes only the database; the network writes into it.

```kotlin
fun observeArticles(): Flow<List<Article>> =
    dao.observeAll().map { it.map(ArticleEntity::toDomain) }

suspend fun refresh(): Result<Unit> = runCatching {
    dao.upsertAll(api.fetch().map { it.toEntity() })   // The UI updates automatically
}
```

Writes use an **outbox**: insert locally with a `PENDING` status and a client-generated ID, enqueue a WorkManager job, mark `SYNCED` on success. The user sees the change instantly and offline writes are queued.

**Follow-up:** *How do you prevent duplicates when a sync job is retried?*
> Client-generated IDs make the operation idempotent — the server upserts on that key, so a retry after a lost response cannot create a second record.

---

### Q15. How do you show cached data and a refresh failure at the same time? `[Mid]`

**Answer**
Keep content and refresh state independent, and report the failure as a one-shot event rather than as state that replaces the content.

```kotlin
val uiState = combine(repo.observeArticles(), refreshing) { items, isRefreshing ->
    ArticleUiState(items, isRefreshing)
}.stateIn(viewModelScope, WhileSubscribed(5_000), ArticleUiState())

fun refresh() = viewModelScope.launch {
    refreshing.value = true
    repo.refresh().onFailure { _events.trySend(ShowSnackbar(it.toUserMessage())) }
    refreshing.value = false
}
```

**Follow-up:** *What is the common mistake?*
> Setting state to `Loading` on refresh, which blanks the list the user is reading. Loading is for a cold start with nothing to show.

---

### Q16. How should errors cross the layer boundary? `[Mid]`

**Answer**
Translate transport failures into a domain error type at the repository boundary, so the UI never sees an `IOException` or an HTTP code.

```kotlin
sealed interface AppError {
    data object Offline : AppError
    data object Unauthorized : AppError
    data class Server(val code: Int) : AppError
    data class Validation(val fields: Map<String, String>) : AppError
}

suspend fun <T> safeApiCall(block: suspend () -> T): DataResult<T> = try {
    DataResult.Success(block())
} catch (e: CancellationException) { throw e            // NEVER swallow
} catch (e: HttpException) { DataResult.Failure(e.toAppError())
} catch (e: IOException) { DataResult.Failure(AppError.Offline)   // Includes timeouts and DNS
}
```

**Follow-up:** *Why not use Kotlin's built-in `Result<T>`?*
> It carries only a `Throwable`, so the UI ends up matching on exception types and message strings. It also has restrictions as a return type in some suspending positions. A domain sealed type makes the `when` exhaustive and carries structured payloads.

---

### Q17. What does an image loader do that a naive `BitmapFactory.decodeStream` does not? `[Mid]`

**Answer**
* **Downsamples** to the target view's measured size — a 4000×3000 JPEG into a 300 dp view is 48 MB decoded, and reliably OOMs.
* **Two-level caching** — decoded bitmaps in an `LruCache`, compressed bytes on disk.
* **Cancels** in-flight requests when a RecyclerView row is recycled, preventing the wrong image flashing into a reused row.
* **Lifecycle awareness** — Coil ties requests to the resolved `LifecycleOwner`.

**Follow-up:** *Coil or Glide for a new project?*
> Coil 3 — Kotlin-first, coroutine-based, first-class Compose support (`AsyncImage`), multiplatform, and about a quarter of Glide's APK footprint. Glide remains excellent and is the right answer for an existing Java/View codebase already using it.

---

### Q18. Why is `contentDescription = null` on an image inside a described row correct? `[Mid]`

**Answer**
Because the parent already describes the whole row. Giving the image its own description makes TalkBack announce the content twice and adds an extra swipe stop.

`null` means "decorative, skip me". A meaningful standalone image must have a real description.

**Follow-up:** *What is the difference between `null` and `""`?*
> `null` removes the node from the accessibility tree entirely. `""` leaves a focusable node with no label, which is worse — the user lands on something and hears nothing.

---

### Q19. What is SQLCipher and when do you need it? `[Mid]`

**Answer**
SQLCipher transparently encrypts the entire SQLite file with AES. You need it when the app stores data that would be damaging if extracted from a rooted device, a device backup, or a stolen unencrypted image — health records, financial data, message contents.

```kotlin
val factory = SupportOpenHelperFactory(passphraseFromKeystore())
Room.databaseBuilder(context, AppDatabase::class.java, "secure.db")
    .openHelperFactory(factory)
    .build()
```

**Follow-up:** *Where does the passphrase come from?*
> Generated once per install and stored in the Android Keystore, so it never exists as extractable material. Storing it in SharedPreferences defeats the entire purpose — the weakest link defines the security.

---

### Q20. How do you handle a paginated API with Paging 3? `[Mid]`

**Answer**
```kotlin
class ArticlePagingSource(private val api: ArticleApi) : PagingSource<Int, Article>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Article> = try {
        val page = params.key ?: 1
        val response = api.articles(page, params.loadSize)
        LoadResult.Page(
            data = response.items,
            prevKey = if (page == 1) null else page - 1,
            nextKey = if (response.items.isEmpty()) null else page + 1
        )
    } catch (e: IOException) { LoadResult.Error(e) }

    // Used after invalidation, so refresh resumes near the user's scroll position
    override fun getRefreshKey(state: PagingState<Int, Article>): Int? =
        state.anchorPosition?.let { state.closestPageToPosition(it)?.nextKey?.minus(1) }
}
```

**Follow-up:** *How do you make it work offline?*
> A `RemoteMediator` that writes network pages into Room, with the `PagingSource` coming from the DAO. The UI then pages from the database, which is populated by the network.

---

### Q21. What is `cachedIn` and why is it necessary? `[Mid]`

**Answer**
`Flow<PagingData<T>>` is a stream of loading events, not a snapshot. Without `cachedIn(viewModelScope)`, a configuration change restarts collection and the list reloads from page one. `cachedIn` multicasts the stream and retains loaded pages for the scope's lifetime.

**Follow-up:** *Can you apply `map` to `PagingData` after `cachedIn`?*
> Yes, and you should — transformations after `cachedIn` re-run on each collection without refetching, so presentation-only mapping belongs there. Expensive transformations belong before it so they are cached.

---

### Q22. When do you use `@Transaction` in Room? `[Mid]`

**Answer**
Whenever several operations must be atomic, and — importantly — on any `@Query` returning a `@Relation`, because Room runs multiple queries to assemble the object graph and without a transaction they can see inconsistent intermediate states.

```kotlin
@Transaction
@Query("SELECT * FROM user")
fun observeUsersWithOrders(): Flow<List<UserWithOrders>>

@Transaction
suspend fun replaceAll(items: List<ArticleEntity>) {
    deleteAll(); insertAll(items)      // The UI never observes an empty list mid-write
}
```

**Follow-up:** *What happens without `@Transaction` on a relation query?*
> Room logs a warning, and you can get a user joined to orders that were deleted between the two queries — a rare, timing-dependent inconsistency that is very hard to reproduce.

---

### Q23. How do you handle a very large response without OOM? `[Senior]`

**Answer**
Stream it rather than materializing it.
* Retrofit: return `ResponseBody` and read the stream, or use `@Streaming` to prevent buffering the whole body into memory.
* Parse incrementally with a streaming JSON reader and insert into the database in batches.
* Never build the full `List<T>` in memory before writing.

```kotlin
@Streaming
@GET("export")
suspend fun export(): ResponseBody

suspend fun importAll() = api.export().byteStream().use { stream ->
    json.decodeToSequence<ItemDto>(stream)
        .chunked(500)
        .forEach { batch -> dao.insertAll(batch.map { it.toEntity() }) }
}
```

**Follow-up:** *Why `@Streaming` specifically?*
> Without it, Retrofit buffers the entire response body into memory before handing it to you — so the OOM happens before your streaming parser ever runs.

---

### Q24. What is certificate pinning and what is the operational risk? `[Mid]`

**Answer**
Pinning restricts accepted certificates to specific public-key hashes, defeating a proxy with a user-installed CA.

The operational risk is severe: when the certificate rotates and the new key is not pinned, **every installed client loses connectivity** and you cannot fix it server-side. Always pin a backup key, and set an expiry on the pin set.

```xml
<pin-set expiration="2027-01-01">
    <pin digest="SHA-256">primaryKeyHash=</pin>
    <pin digest="SHA-256">backupKeyHash=</pin>   <!-- Mandatory -->
</pin-set>
```

**Follow-up:** *Does pinning protect you from a determined attacker on a rooted device?*
> No. Frida patches it out in minutes. Pinning protects **users** from network attackers; it does not protect **you** from the device owner. Server-side verification is the only control that survives a hostile client.

---

### Q25. How do you correctly configure timeouts? `[Mid]`

**Answer**
```kotlin
OkHttpClient.Builder()
    .connectTimeout(15, TimeUnit.SECONDS)   // TCP + TLS handshake
    .readTimeout(30, TimeUnit.SECONDS)      // Between bytes, not total
    .writeTimeout(30, TimeUnit.SECONDS)
    .callTimeout(60, TimeUnit.SECONDS)      // TOTAL, including redirects and retries
    .build()
```

**Follow-up:** *Which one do people usually forget, and why does it matter?*
> `callTimeout`. Without it, a response that trickles one byte every 29 seconds never triggers the read timeout and the call can hang effectively forever. `callTimeout` is the only bound on total duration.

---

### Q26. What is `WorkManager` + `Retrofit` retry vs OkHttp retry — which handles what? `[Senior]`

**Answer**
* **OkHttp** retries transparently for connection-level failures (a stale pooled connection, a failed route) via `retryOnConnectionFailure`. It does **not** retry on HTTP error codes.
* **In-flow retry** (`retryWhen` with backoff) handles transient server errors within the current process and screen.
* **WorkManager** handles retries that must survive process death and the app being closed — and applies OS-level constraints and backoff.

**Follow-up:** *A user taps "save" offline. Which mechanism?*
> WorkManager. In-flow retry dies with the screen, and OkHttp will not retry an unreachable network for minutes. WorkManager persists the request, waits for connectivity, and retries with exponential backoff across process restarts.

---

### Q27. How do you test networking code? `[Mid]`

**Answer**
`MockWebServer` — a real HTTP server on localhost, so serialization, interceptors, and error mapping are all genuinely exercised.

```kotlin
@Test fun `unknown fields do not break parsing`() = runTest {
    server.enqueue(MockResponse().setBody("""{"id":1,"full_name":"Ada","new_field":"x"}"""))
    assertThat(api.getUser(1).fullName).isEqualTo("Ada")
}

@Test fun `500 maps to a server error`() = runTest {
    server.enqueue(MockResponse().setResponseCode(500))
    assertThat(safeApiCall { api.getUser(1) }).isEqualTo(DataResult.Failure(AppError.Server(500)))
}
```

**Follow-up:** *Why is `MockWebServer` better than mocking the Retrofit interface?*
> Mocking the interface skips serialization, interceptors, and error mapping — exactly the layers where the bugs are. `MockWebServer` tests the real stack with a controlled server.

---

### Q28. What is the N+1 query problem in Room and how do you avoid it? `[Senior]`

**Answer**
Loading a list of users and then querying each user's orders individually is N+1 queries. Room's `@Relation` solves it with two queries total: one for users, one `IN`-query for all their orders.

```kotlin
data class UserWithOrders(
    @Embedded val user: UserEntity,
    @Relation(parentColumn = "id", entityColumn = "user_id") val orders: List<OrderEntity>
)

@Transaction
@Query("SELECT * FROM user")
fun observeAll(): Flow<List<UserWithOrders>>
```

**Follow-up:** *When is `@Relation` the wrong tool?*
> When the relation is large — a user with 10,000 orders loads all of them into memory. For that, page the child collection separately or query only an aggregate (a count, the latest N).

---

### Q29. How would you implement a token refresh that is correct under concurrency? `[Senior]`

**Answer**
Use an OkHttp `Authenticator` with a mutex, so concurrent 401s trigger exactly one refresh, and a retry counter so a persistently-failing refresh cannot loop.

```kotlin
class TokenAuthenticator(private val store: TokenStore) : Authenticator {
    private val lock = Any()

    override fun authenticate(route: Route?, response: Response): Request? {
        val failedToken = response.request.header("Authorization")

        synchronized(lock) {
            val current = store.accessToken()
            // Another thread already refreshed while we waited — just use the new token
            if (current != null && "Bearer $current" != failedToken) return retry(response, current)

            if (responseCount(response) >= 2) { store.clear(); return null }  // Give up: log out

            val fresh = store.refreshBlocking() ?: run { store.clear(); return null }
            return retry(response, fresh)
        }
    }
}
```

**Follow-up:** *Why check whether the token already changed before refreshing?*
> Ten concurrent requests hit 401 together. The first refreshes; the other nine reach the lock afterwards. Without that check they each refresh again, invalidating each other's tokens and often logging the user out.

---

### Q30. Design the data layer for a news app with feeds, offline reading, bookmarks, and search. `[Senior]`

**Answer**
**Storage**
* Room as the single source of truth: `articles`, `bookmarks`, `feeds`, `pending_actions`.
* FTS4/FTS5 virtual table for search — orders of magnitude faster than `LIKE '%term%'` and it works offline.
* Downloaded article bodies in app-private files, referenced by path from the article row.

**Reads**
* Feed: Paging 3 with a `RemoteMediator` (network → Room, UI pages from Room).
* Search: query the FTS table, debounced, `flatMapLatest` to cancel superseded queries.

**Writes**
* Bookmarks are optimistic: write to Room immediately, enqueue a `pending_action`, sync via WorkManager. The UI is instant and offline bookmarking works.

**Refresh policy**
* Time-based TTL on the feed (5 min), plus a manual pull-to-refresh, plus a periodic WorkManager sync on unmetered networks for offline reading.

**Retention**
* Delete articles older than N days on each successful sync, and cap the offline body cache by total size — otherwise storage grows without bound.

**Follow-up:** *How do you keep the offline body cache from growing forever?*
> An LRU policy keyed on last-read timestamp, enforced in the same WorkManager job that syncs: delete the least-recently-read bodies until the directory is under budget, and never delete anything the user explicitly saved for offline.

---

## Related

* [`05_coroutines_concurrency.md`](./05_coroutines_concurrency.md) — Flow operators used throughout
* [`07_background_work.md`](./07_background_work.md) — WorkManager sync jobs
* [`11_testing_security.md`](./11_testing_security.md) — encrypted storage, TLS, pinning
* [`00_INDEX.md`](./00_INDEX.md) — full index
