# 8. Dependency Injection — 20 Questions

> Dagger, Hilt, Koin, scopes, components, assisted injection, multibinding, testing.
> Reference material: [`../android.md`](../android.md) Module 8.

---

### Q1. What is dependency injection and what problem does it solve? `[Junior]`

**Answer**
A class receives its collaborators from outside instead of constructing them. The problem it solves is testability and coupling: a ViewModel that builds its own Retrofit instance cannot be unit-tested without a real server, and cannot have its data source swapped.

```kotlin
// Not injectable: welded to Retrofit, untestable
class ProfileViewModel : ViewModel() {
    private val api = Retrofit.Builder().baseUrl(URL).build().create(UserApi::class.java)
}

// Injectable: the dependency is an interface supplied from outside
class ProfileViewModel @Inject constructor(private val repo: UserRepository) : ViewModel()
```

**Follow-up:** *Is a service locator dependency injection?*
> No. With a service locator the class **pulls** its dependencies (`ServiceLocator.get<Repo>()`), so the dependency is hidden inside the implementation and the class still cannot be constructed with alternatives. DI **pushes** them in through the constructor, making them visible in the signature.

---

### Q2. Dagger vs Hilt vs Koin. `[Mid]`

**Answer**

| | Dagger 2 | Hilt | Koin |
|---|---|---|---|
| Validation | Compile time | Compile time | Runtime |
| Codegen | Annotation processing | Annotation processing | None |
| Boilerplate | High — you define every component | Low — components are predefined | Very low |
| Android integration | Manual | `@AndroidEntryPoint` | Extension functions |
| Multiplatform | No | **No** | **Yes** |

**Follow-up:** *Why do KMP projects use Koin rather than Hilt?*
> Hilt is Android-only — it depends on Android component lifecycles and cannot compile for `commonMain` or iOS targets. Koin is pure Kotlin and works on every target.

---

### Q3. What does Hilt generate and what does `@AndroidEntryPoint` do? `[Mid]`

**Answer**
Hilt generates a predefined component hierarchy mirroring Android lifecycles and, for each `@AndroidEntryPoint` class, a base class that performs member injection at the right lifecycle moment. It rewrites the class's superclass via bytecode transformation so you do not have to extend a generated type manually.

`@HiltAndroidApp` on the `Application` generates the root `SingletonComponent`.

**Follow-up:** *Why does a Fragment need `@AndroidEntryPoint` even when its Activity has it?*
> Injection is per-class. The generated base class for the Fragment performs injection in `onAttach`, using the Activity's component as its parent — so the Activity being annotated is necessary but not sufficient.

---

### Q4. Name the Hilt components and their lifetimes. `[Mid]`

**Answer**

| Component | Scope | Created / Destroyed |
|---|---|---|
| `SingletonComponent` | `@Singleton` | `Application.onCreate` / process death |
| `ActivityRetainedComponent` | `@ActivityRetainedScoped` | `onCreate` / `onDestroy` — **survives config change** |
| `ViewModelComponent` | `@ViewModelScoped` | ViewModel created / `onCleared` |
| `ActivityComponent` | `@ActivityScoped` | `onCreate` / `onDestroy` — recreated on rotation |
| `FragmentComponent` | `@FragmentScoped` | `onAttach` / `onDestroy` |
| `ServiceComponent` | `@ServiceScoped` | `onCreate` / `onDestroy` |

**Follow-up:** *Which do you use for state that must survive rotation but not the whole app?*
> `@ActivityRetainedScoped`, or better, put it in a ViewModel. `@ActivityScoped` is recreated on rotation, which is the trap.

---

### Q5. What does it mean for a binding to be unscoped? `[Mid]`

**Answer**
An unscoped binding creates a **new instance at every injection point**. That is the default and usually correct — it is cheap and has no lifetime implications.

Scope only when the instance is expensive to build (an `OkHttpClient`, a `RoomDatabase`) or must be **shared** (an in-memory cache, a connection).

**Follow-up:** *What is the cost of scoping everything `@Singleton`?*
> Everything lives for the whole process. Anything holding an Activity context becomes a permanent leak, memory grows, and stateful objects retain state across screens that expected a fresh instance.

---

### Q6. `@Provides` vs `@Binds`. `[Mid]`

**Answer**
`@Provides` is a function with a body that constructs the object — needed when you do not own the class or the construction is non-trivial. `@Binds` is an abstract function declaring "this interface is satisfied by this implementation", generating no factory body at all.

```kotlin
@Module @InstallIn(SingletonComponent::class)
abstract class RepoModule {
    @Binds @Singleton
    abstract fun bindUserRepository(impl: UserRepositoryImpl): UserRepository
}
```

**Follow-up:** *Why prefer `@Binds` for interface bindings?*
> It generates less code and is a pure graph edge rather than a factory invocation. Functionally equivalent, but cheaper at build time and at runtime.

---

### Q7. What is a qualifier and when do you need one? `[Mid]`

**Answer**
When two bindings share a type, a qualifier disambiguates them.

```kotlin
@Qualifier @Retention(BINARY) annotation class AuthedClient
@Qualifier @Retention(BINARY) annotation class PublicClient

@Provides @Singleton @AuthedClient
fun authed(i: AuthInterceptor): OkHttpClient = OkHttpClient.Builder().addInterceptor(i).build()

@Provides @Singleton @PublicClient
fun public(): OkHttpClient = OkHttpClient.Builder().build()
```

**Follow-up:** *What is the most valuable everyday use of qualifiers?*
> Injecting dispatchers. `@IoDispatcher CoroutineDispatcher` means a test can substitute a `TestDispatcher` without touching `Dispatchers.setMain`, making the class testable in isolation.

---

### Q8. How do you inject the application context? `[Junior]`

**Answer**
```kotlin
@Provides @Singleton
fun database(@ApplicationContext context: Context): AppDatabase =
    Room.databaseBuilder(context, AppDatabase::class.java, "app.db").build()
```
Hilt predefines `@ApplicationContext` and `@ActivityContext`.

**Follow-up:** *What happens if you inject `@ActivityContext` into a `@Singleton`?*
> Hilt catches it at compile time in most cases, because `ActivityContext` is not available in `SingletonComponent`. If it slips through some other route, it is a permanent Activity leak.

---

### Q9. What is assisted injection and when do you need it? `[Senior]`

**Answer**
When some constructor parameters come from the graph and one comes from the caller at runtime.

```kotlin
class ProductViewModel @AssistedInject constructor(
    private val repo: ProductRepository,     // From the graph
    @Assisted private val productId: Long    // From the caller
) : ViewModel() {
    @AssistedFactory interface Factory { fun create(productId: Long): ProductViewModel }
}
```

**Follow-up:** *For a ViewModel with a navigation argument, is assisted injection the right tool?*
> Usually not. Navigation arguments arrive in `SavedStateHandle`, so the ViewModel can read them directly — simpler, and it survives process death. Assisted injection is for values that genuinely cannot come from navigation.

---

### Q10. What is multibinding and what is it for? `[Senior]`

**Answer**
Contributing to a `Set` or `Map` from several modules without any one of them knowing all contributors — a plugin architecture.

```kotlin
@Module @InstallIn(SingletonComponent::class)
abstract class InitModule {
    @Binds @IntoSet abstract fun crashlytics(i: CrashlyticsInitializer): AppInitializer
    @Binds @IntoSet abstract fun analytics(i: AnalyticsInitializer): AppInitializer
}

@HiltAndroidApp
class App : Application() {
    @Inject lateinit var initializers: Set<@JvmSuppressWildcards AppInitializer>
    override fun onCreate() { super.onCreate(); initializers.forEach { it.initialize(this) } }
}
```

**Follow-up:** *Why `@JvmSuppressWildcards`?*
> Kotlin generates `Set<? extends AppInitializer>` for a generic parameter, and Dagger cannot match that against its `Set<AppInitializer>` binding. The annotation removes the wildcard.

---

### Q11. How do you inject into a class Hilt does not know about? `[Senior]`

**Answer**
An `@EntryPoint` — an interface Hilt implements, letting you reach into a component from a non-Hilt class such as a `ContentProvider` or a third-party-instantiated object.

```kotlin
@EntryPoint
@InstallIn(SingletonComponent::class)
interface AnalyticsEntryPoint { fun analytics(): Analytics }

// From a ContentProvider, which is created before Application.onCreate finishes
val analytics = EntryPointAccessors
    .fromApplication(context, AnalyticsEntryPoint::class.java)
    .analytics()
```

**Follow-up:** *Why can a `ContentProvider` not just use `@AndroidEntryPoint`?*
> Providers are created **before** `Application.onCreate` completes, so the Hilt component does not exist yet. `EntryPointAccessors` forces component creation at that moment, which works but means the provider's initialization cost lands on every cold start.

---

### Q12. How do you replace a dependency in a test with Hilt? `[Mid]`

**Answer**
`@BindValue` for a single binding, or `@UninstallModules` plus a test module for a whole module.

```kotlin
@HiltAndroidTest
class LoginTest {
    @get:Rule(order = 0) val hilt = HiltAndroidRule(this)
    @get:Rule(order = 1) val compose = createAndroidComposeRule<MainActivity>()

    @BindValue @JvmField val repo: AuthRepository = FakeAuthRepository()
}
```

**Follow-up:** *Why does the rule order matter?*
> `HiltAndroidRule` must run first so the component is built before the Activity is launched. With the wrong order the Activity starts before injection is ready and fails with a missing-binding error at runtime.

---

### Q13. How is Koin's model different, and what is its main risk? `[Mid]`

**Answer**
Koin registers factory lambdas in a map keyed by type plus qualifier; resolution is a runtime lookup. There is no code generation, so no build-time cost — but a missing binding is a `NoBeanDefFoundException` **at first use**, in production, on the screen that needs it.

The mitigation is mandatory: a `checkModules()` test.

```kotlin
@Test fun `all dependencies resolve`() {
    koinApplication {
        androidContext(ApplicationProvider.getApplicationContext())
        modules(networkModule, dataModule, viewModelModule)
    }.checkModules()
}
```

**Follow-up:** *`single` vs `factory` vs `viewModel` in Koin?*
> `single` = one shared instance for the container's lifetime. `factory` = new instance per resolution. `viewModel` = resolved through `ViewModelProvider`, so it follows Android ViewModel lifetime. Using `single` for per-screen state shares it across screens — the most common Koin bug.

---

### Q14. How do you provide a `CoroutineScope` for app-lifetime work? `[Senior]`

**Answer**
```kotlin
@Provides @Singleton
fun appScope(): CoroutineScope = CoroutineScope(SupervisorJob() + Dispatchers.Default)
```

Injecting it replaces `GlobalScope`: it is owned, testable (substitutable with a `TestScope`), and `SupervisorJob` means one failing child does not tear down every other background task.

**Follow-up:** *When do you actually need one?*
> Work that must outlive the screen that started it — a fire-and-forget analytics flush, a cache warm, writing to the outbox. Most work belongs in `viewModelScope` or WorkManager; an app scope is the exception, not the default.

---

### Q15. Why should modules be `object` rather than `class` where possible? `[Mid]`

**Answer**
A Dagger module with only `@Provides` functions and no state should be an `object` (or the functions should be `@JvmStatic`), so Dagger can call them statically without instantiating the module. It is a small but free build-output and runtime saving.

Modules with `@Binds` must be `abstract class` or `interface`, since `@Binds` functions are abstract.

**Follow-up:** *Can you mix `@Binds` and `@Provides` in one module?*
> Only if the module is an abstract class and the `@Provides` functions are in a nested `companion object`. Cleaner to split them into two modules.

---

### Q16. What is a circular dependency and how do you break it? `[Senior]`

**Answer**
A depends on B, B depends on A. Dagger reports it at compile time as a dependency cycle.

Break it by:
1. **Extracting the shared piece** into a third class both depend on — almost always the right fix, because a cycle signals a design problem.
2. **`Lazy<T>` or `Provider<T>`**, which defers resolution until first use, breaking the construction cycle.

```kotlin
class A @Inject constructor(private val b: Lazy<B>) {
    fun doWork() = b.get().helper()      // Resolved on first call, not at construction
}
```

**Follow-up:** *Why is `Lazy` a workaround rather than a fix?*
> It resolves the construction order but leaves the mutual coupling. The classes still cannot be understood or tested independently. Use it when the cycle is genuinely unavoidable (a framework callback), not to avoid refactoring.

---

### Q17. What is `@Reusable` in Dagger? `[Senior]`

**Answer**
A scope-like annotation meaning "you may cache this instance, but you need not". It is weaker than a real scope: Dagger may create more than one instance and does not bind it to a component's lifetime.

Use it for objects that are expensive to create but where sharing is an optimization, not a requirement — a stateless parser, a formatter.

**Follow-up:** *When is `@Reusable` wrong?*
> Whenever sharing is semantically required — a cache, a connection pool, anything holding state. `@Reusable` gives no guarantee of a single instance, so state can silently diverge.

---

### Q18. How does DI interact with modularization? `[Senior]`

**Answer**
Hilt's `@InstallIn` means a feature module can contribute bindings to `SingletonComponent` without `:app` knowing about them — the module is discovered at compile time by the aggregating processor. That keeps `:app` from having to reference every feature's internals.

The rule that keeps it clean: a feature module contributes **implementations**; the interfaces live in `:domain` or `:core`, so features stay decoupled.

**Follow-up:** *What is the build-time cost of Hilt in a large multi-module project?*
> Hilt's aggregating processor must see every module, so a change to any DI-annotated class can invalidate `:app`'s Hilt task. Mitigate with KSP instead of kapt, by keeping DI annotations out of frequently-edited classes, and by keeping the number of `@InstallIn(SingletonComponent::class)` modules modest.

---

### Q19. How do you decide what to inject and what to construct directly? `[Mid]`

**Answer**
Inject anything that:
* has a **non-trivial lifetime** (database, HTTP client, cache),
* is a **boundary** you will want to fake in tests (repositories, dispatchers, clocks, analytics),
* has **platform dependencies** (Context, system services).

Construct directly anything that is a pure value or a simple transformation — a mapper, a data class, a formatter with no dependencies. Injecting those adds indirection without benefit.

**Follow-up:** *Why inject a `Clock`?*
> Because `System.currentTimeMillis()` makes time-dependent logic untestable. An injected `Clock` lets a test fix "now", which is the difference between a reliable test and one that fails at midnight.

---

### Q20. Design the DI setup for an app with feature modules, two backends, and a Compose UI. `[Senior]`

**Answer**
**Structure**
* `:core:network` — `@Provides` for `OkHttpClient`, `Json`, and both Retrofit instances distinguished by `@MainApi` / `@PaymentsApi` qualifiers.
* `:core:database` — `RoomDatabase` and DAO providers, `@Singleton`.
* `:core:common` — dispatcher qualifiers (`@IoDispatcher`, `@DefaultDispatcher`), `Clock`, an app-scoped `CoroutineScope`.
* `:data` — `@Binds` mapping each `domain` repository interface to its implementation, `@Singleton` where an in-memory cache is shared, unscoped otherwise.
* `:feature:*` — `@HiltViewModel` classes only; no modules unless the feature has feature-local bindings, in which case they go in `ViewModelComponent`.
* `:app` — `@HiltAndroidApp`, plus any binding that genuinely needs to know about multiple features (a `Navigator` implementation).

**Scoping decisions to state**
* `@Singleton` for the database, the HTTP clients, and DataStore — expensive and genuinely shared.
* Repositories unscoped unless they hold a cache.
* `@ViewModelScoped` for anything a ViewModel and its use cases must share within one screen.

**Testing**
* Dispatchers injected, so tests substitute `TestDispatcher`.
* `@BindValue` for per-test fakes; `@UninstallModules` + a test module for suite-wide replacements.

**Follow-up:** *A feature module needs a binding that only makes sense for that feature. Where does it go?*
> A module inside the feature, `@InstallIn(ViewModelComponent::class)` with `@ViewModelScoped`. It stays invisible to the rest of the app, which is exactly the boundary modularization is meant to create — putting it in `SingletonComponent` would expose it app-wide and keep it alive forever.

---

## Related

* [`04_jetpack_architecture.md`](./04_jetpack_architecture.md) — ViewModel scoping, modularization
* [`11_testing_security.md`](./11_testing_security.md) — test doubles and Hilt test setup
* [`13_build_release_gradle.md`](./13_build_release_gradle.md) — KSP vs kapt build cost
* [`00_INDEX.md`](./00_INDEX.md) — full index
