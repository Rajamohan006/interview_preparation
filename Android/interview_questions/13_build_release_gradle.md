# 13. Build, Gradle & Release — 20 Questions

> Gradle lifecycle, AGP pipeline, variants, version catalogs, KSP, R8, signing, build performance, CI/CD.
> Reference material: [`../gradle_build.md`](../gradle_build.md), [`../CD_Setup_Guide.md`](../CD_Setup_Guide.md).

---

### Q1. What are Gradle's three phases? `[Mid]`

**Answer**
1. **Initialization** — evaluate `settings.gradle.kts`, decide which projects exist.
2. **Configuration** — execute **every** build script, building the task graph. Runs on every build, for every module.
3. **Execution** — run only the required tasks.

```kotlin
val sha = "git rev-parse HEAD".runCommand()   // CONFIGURATION: a process fork on every build
tasks.register("printSha") {
    doLast { println("git rev-parse HEAD".runCommand()) }   // EXECUTION: only when needed
}
```

**Follow-up:** *How do you measure how much time configuration costs?*
> `./gradlew help` times configuration essentially alone, since it runs almost no tasks. A build scan (`--scan`) breaks it down per project.

---

### Q2. `tasks.register` vs `tasks.create`. `[Mid]`

**Answer**
`register` is lazy — the task is configured only if it will actually run. `create` is eager and configures on every build regardless. In a large multi-module project, eager task creation is a measurable configuration-time cost.

**Follow-up:** *What else is eager and should be avoided?*
> `tasks.getByName` (use `tasks.named`), iterating a task collection with `forEach` (use `configureEach`), and `subprojects { }` in the root build file, which configures every module on every build and breaks the configuration cache.

---

### Q3. What is the configuration cache and why does it matter? `[Senior]`

**Answer**
It serializes the task graph after the configuration phase, so subsequent builds skip configuration entirely — often 10–30 seconds saved per build on a large project.

It requires that build logic not read mutable state at execution time and not hold references to `Project` in task actions, which is why `subprojects { }` and configuration-time `System.getenv` break it.

```properties
org.gradle.configuration-cache=true
org.gradle.configuration-cache.problems=warn   # Migrate incrementally
```

**Follow-up:** *What is the migration path for a project that fails it?*
> Set `problems=warn` to get a report without failing, then fix incrementally: replace `subprojects { }` with convention plugins, replace direct `Project` access in tasks with `Provider`/`ValueSource`, and declare environment reads as build inputs.

---

### Q4. Walk through the Android build pipeline. `[Mid]`

**Answer**
```
Kotlin/Java source → KSP/kapt codegen → kotlinc/javac → .class
res/ + manifest    → AAPT2 compile & link → R class + resources.arsc
.class + libraries → R8 (shrink, optimize, obfuscate, dex) → classes.dex
→ package (zip DEX + resources + assets + .so) → apksigner → APK/AAB
```

**Follow-up:** *How do you map a build error to a stage?*
> `Manifest merger failed` → manifest merge; `Duplicate class` → dependency resolution; `Cannot fit requested classes in a single dex file` → dexing; `Unresolved reference` in generated code → annotation processing. Each has a different fix, and identifying the stage is most of the diagnosis.

---

### Q5. How do you resolve a manifest merge conflict? `[Mid]`

**Answer**
Priority is build variant → main → libraries. Resolve explicitly:

```xml
<application
    android:allowBackup="false"
    tools:replace="android:allowBackup">
    <provider android:name="com.thirdparty.InitProvider"
        android:authorities="${applicationId}.init"
        tools:node="remove" />
</application>
```
The merger report at `app/build/outputs/logs/manifest-merger-debug-report.txt` lists every decision with its source.

**Follow-up:** *Why would you remove a library's `ContentProvider`?*
> Auto-initializing providers run before `Application.onCreate` finishes, on every cold start. Removing them and initializing the library lazily (or via App Startup) is a common startup optimization.

---

### Q6. Build type vs product flavor vs variant. `[Junior]`

**Answer**
* **Build type** — *how* it is built: debug vs release (debuggable, minified, signing).
* **Product flavor** — *what* is built: free vs paid, staging vs production.
* **Variant** — the cross product: `freeStagingDebug`.

Flavors belong to **dimensions**, and one flavor from each dimension combines with each build type.

**Follow-up:** *Two dimensions with three flavors each and three build types — how many variants, and what do you do about it?*
> 27. Prune the combinations nobody builds with `androidComponents { beforeVariants { it.enable = ... } }`, or the IDE and CI both pay for variants that never ship.

---

### Q7. How do source sets merge across variants? `[Mid]`

**Answer**
Priority: **variant** (`src/freeDebug/`) → **flavor** (`src/free/`) → **build type** (`src/debug/`) → **main**.

Kotlin/Java sources are **additive** — a class may exist in only one set. Resources and manifests are **overriding** — a more specific set replaces the same-named resource.

**Follow-up:** *Why does putting a flavor-specific class in `main/` cause a duplicate-class error?*
> Because sources are additive, not overriding. A class that varies by flavor must exist in **every** flavor of that dimension and **not** in `main`.

---

### Q8. What is a version catalog and what does it buy you? `[Mid]`

**Answer**
`gradle/libs.versions.toml` centralizes dependency coordinates and versions, exposed as type-safe accessors (`libs.androidx.core.ktx`).

Benefits: one place to upgrade, IDE autocomplete, typos become compile errors, and bundles group related dependencies. Combined with a BOM (`platform(libs.compose.bom)`), a whole library family stays version-consistent.

**Follow-up:** *Why is a BOM better than pinning each Compose artifact?*
> Compose artifacts must be version-compatible with each other. Pinning them individually eventually produces a combination that compiles but fails at runtime. A BOM makes that impossible.

---

### Q9. `implementation` vs `api` vs `compileOnly`. `[Mid]`

**Answer**
* `implementation` — not on consumers' compile classpath. Changing it does not recompile downstream modules. **The default.**
* `api` — exposed to consumers. Only when a type from that dependency appears in your public signatures.
* `compileOnly` — on your compile classpath, not packaged. Annotations, provided-at-runtime libraries.
* `runtimeOnly` — packaged, not on the compile classpath. Drivers.

**Follow-up:** *What is the practical cost of `api` everywhere?*
> Every dependency change invalidates the whole downstream module graph, so incremental builds degrade toward clean builds. It is one of the most common causes of a slow multi-module build.

---

### Q10. KSP vs kapt. `[Mid]`

**Answer**
kapt generates **Java stubs** for all Kotlin code before running Java annotation processors, then compiles for real — typically **2×** the processing time. KSP reads Kotlin symbols directly through a Kotlin-aware API, with no stub step.

Room, Hilt (Dagger 2.48+), Moshi, and Glide all support KSP.

**Follow-up:** *You migrate Room to KSP but the build is barely faster. Why?*
> Some other processor still uses kapt, and the stub-generation cost is paid as soon as **any** processor does. The saving only materializes when kapt is removed entirely.

---

### Q11. What is a convention plugin? `[Senior]`

**Answer**
A Gradle plugin in a `build-logic` included build that encapsulates shared module configuration, applied as one line per module.

```kotlin
class AndroidLibraryConventionPlugin : Plugin<Project> {
    override fun apply(target: Project) = with(target) {
        pluginManager.apply("com.android.library")
        extensions.configure<LibraryExtension> {
            compileSdk = 36
            defaultConfig { minSdk = 24 }
            compileOptions { sourceCompatibility = JavaVersion.VERSION_17 }
        }
    }
}
// Every module: plugins { id("myapp.android.library") }
```

**Follow-up:** *Why is this better than `subprojects { }`?*
> `subprojects` couples every module's configuration together, forces them all to be configured on every build, and breaks the configuration cache. Convention plugins are type-safe, testable, and applied only where wanted.

---

### Q12. Name the four things R8 does. `[Junior]`

**Answer**
1. **Shrinking** — deletes code unreachable from entry points.
2. **Optimization** — inlining, dead-code elimination, class merging.
3. **Obfuscation** — renames classes and members to short identifiers.
4. **Dexing** — produces `classes.dex`.

Resource shrinking is separate (`shrinkResources`), and does nothing without `minifyEnabled`.

**Follow-up:** *What is "full mode" and what does it break?*
> Default since AGP 8, it assumes classes without keep rules are not reflected on and optimizes more aggressively across the whole program. Libraries relying on reflection without shipping consumer rules break, typically as a `ClassNotFoundException` or a null field only in release.

---

### Q13. What needs a keep rule and why? `[Mid]`

**Answer**
Anything reached by **reflection**, which R8 cannot see:
* JSON model classes parsed reflectively.
* Custom views inflated from XML (the `(Context, AttributeSet)` constructor).
* `Parcelable.CREATOR` fields.
* `Enum.valueOf` / `values()`.
* Methods called from JNI.

```proguard
-keep class com.example.data.dto.** { *; }
-keepclasseswithmembers class * extends android.view.View {
    public <init>(android.content.Context, android.util.AttributeSet);
}
-keepclassmembers class * implements android.os.Parcelable {
    public static final ** CREATOR;
}
```

**Follow-up:** *Why is `-keep class com.example.** { *; }` a bad fix?*
> It disables shrinking and obfuscation for the entire app while looking like a targeted rule. Read `build/outputs/mapping/release/usage.txt` to find what was actually deleted, and write a rule for exactly that.

---

### Q14. Why must you upload `mapping.txt`, and what does `seeds.txt` tell you? `[Mid]`

**Answer**
`mapping.txt` is the obfuscation map. Without uploading it to Crashlytics or Play, every production stack trace reads `a.b.c(Unknown Source)` and is undiagnosable.

`seeds.txt` lists everything R8 **kept** and why — the fastest way to discover that an over-broad keep rule is preserving your whole app.

**Follow-up:** *How do you deobfuscate a trace you were given by hand?*
> `retrace mapping.txt stacktrace.txt`. Keep the mapping file for every released version — a trace from version 3.1 needs 3.1's mapping.

---

### Q15. Explain Play App Signing and the fingerprint trap. `[Mid]`

**Answer**
You sign the AAB with an **upload key**; Play verifies it, strips it, and re-signs the delivered APKs with the **app signing key** that Google holds.

The trap: certificate fingerprints for Google Sign-In, Maps, and App Links must be the **app signing key's** SHA-256 from the Play Console — not the upload key's. Using the upload key's fingerprint is the classic "works in debug, broken in production" bug.

**Follow-up:** *What if you lose the upload key?*
> Play support can reset it. Losing the **app signing key** without Play App Signing means the app can never be updated — which is precisely why Play App Signing exists.

---

### Q16. Which signature schemes should you enable? `[Mid]`

**Answer**
v2 and v3 always; v1 only if `minSdk < 24`. v1 (JAR signing) is slow to verify and does not protect ZIP metadata, so dropping it makes the APK smaller and installs faster.

v3 adds key-rotation lineage; v4 (a sidecar `.idsig`) enables ADB incremental install and is generated by the build tools when applicable.

**Follow-up:** *How do you check what an artifact is signed with?*
> `apksigner verify --print-certs --verbose app-release.apk`, which lists each scheme's presence and the signing certificate.

---

### Q17. What are the highest-impact build-speed settings? `[Senior]`

**Answer**
```properties
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.configuration-cache=true         # Usually the largest single win
android.nonTransitiveRClass=true            # Each module gets only its own R class
android.defaults.buildfeatures.buildconfig=false
kotlin.incremental=true
ksp.incremental=true
org.gradle.jvmargs=-Xmx6g -XX:MaxMetaspaceSize=1g
```
Plus: KSP instead of kapt, `implementation` instead of `api`, and a remote build cache populated by CI.

**Follow-up:** *You made these changes and the build is not faster. What do you do?*
> Run `--scan` and read the actual timeline. It reports which tasks were cache misses and why, whether the configuration cache was reused, and where the wall-clock time went. Guessing at build performance almost always optimizes the wrong thing.

---

### Q18. What makes a task cacheable, and what commonly breaks caching? `[Senior]`

**Answer**
A task is cacheable when its inputs and outputs are fully declared and deterministic. Gradle hashes the inputs to form a cache key.

Common breakers: reading a timestamp or git SHA at configuration time, absolute paths in inputs, non-reproducible generated files, and undeclared environment variable reads.

**Follow-up:** *How do you inject a build number without breaking the cache?*
> Declare it as a `Provider` input from a `ValueSource`, so Gradle knows it is an input and invalidates only the tasks that consume it — rather than reading it into a `val` at configuration time, which makes the whole build non-reproducible.

---

### Q19. How should a release pipeline be structured? `[Mid]`

**Answer**
```yaml
static-analysis:  lint + detekt              # Every push
unit-tests:       testDebugUnitTest          # Every push
instrumented:     connectedAndroidTest       # Pull requests only
release:          bundleRelease + Play upload  # Tags only
```
With: a Gradle cache action, a concurrency group cancelling superseded runs, animations disabled on emulators, the keystore decoded from a base64 secret at build time, and the mapping file uploaded with the release.

**Follow-up:** *What is the release safety net?*
> Staged rollout — start at 10% with `status: inProgress`, monitor Vitals for crash and ANR rate, and halt the rollout if it regresses. A 100% first-day rollout has no recovery path short of a new release.

---

### Q20. Design the build setup for a 40-module app with three environments and two tiers. `[Senior]`

**Answer**
**Configuration**
* `gradle/libs.versions.toml` for every dependency, with BOMs for Compose and Firebase.
* `build-logic` convention plugins: `android.application`, `android.library`, `android.feature`, `android.compose`, `android.hilt`, `android.test`. Each module applies one or two lines.
* `settings.gradle.kts` with `FAIL_ON_PROJECT_REPOS` so repository configuration is centralized.

**Variants**
* Dimensions `environment` (dev/staging/production) and `tier` (free/pro); build types debug/release/benchmark.
* Prune with `beforeVariants` so only the ~6 combinations anyone builds are configured.
* `benchmark` derived from `release` but debug-signed, for Macrobenchmark.

**Speed**
* Configuration cache, build cache (remote, CI-push/dev-read), parallel, non-transitive R classes.
* KSP throughout; kapt eliminated.
* `implementation` by default; `api` only where a type is genuinely public.
* `buildconfig` disabled except in the modules that need it.

**Release**
* Play App Signing; keystore in CI secrets, base64-decoded at build time; a task that fails `bundleRelease` if signing is unconfigured.
* `versionCode` from the CI run number, declared as a `ValueSource` so caching still works.
* Mapping and native symbols uploaded automatically.
* Staged rollout starting at 10%.

**Guardrails**
* APK size budget checked in CI, failing on unexpected growth.
* Macrobenchmark startup regression check against a threshold.
* Baseline Profile regenerated each release.
* Dependency vulnerability scan on PRs.

**Follow-up:** *One team complains their module takes four minutes to build. How do you investigate?*
> `--scan` on their specific task, and look at three things: whether the configuration cache is being reused, which upstream module invalidated them (usually an `api` dependency or a `:core:common` everyone depends on), and whether kapt is still present anywhere in their dependency chain.

---

## Related

* [`../gradle_build.md`](../gradle_build.md) — full build system reference
* [`../CD_Setup_Guide.md`](../CD_Setup_Guide.md) — concrete pipeline setup walkthrough
* [`01_system_internals.md`](./01_system_internals.md) — DEX, R8, APK anatomy, signing schemes
* [`00_INDEX.md`](./00_INDEX.md) — full index
