# 🎯 Android Interview Question Bank — 370 Questions

> Every question has an **Answer**, a **Follow-up** probe with its answer, and **code** where code clarifies.
> Difficulty is tagged inline: `[Junior]` · `[Mid]` · `[Senior]`.
> Reference material for each topic lives in the parent [`Android/`](../) directory.

---

## 📚 Files

| # | File | Topic | Questions |
|---|---|---|---|
| 01 | [System Internals](./01_system_internals.md) | Boot sequence, Zygote, ART/Dalvik, class loaders, Binder, DEX, R8, APK/AAB, signing | 30 |
| 02 | [Components & Manifest](./02_components_manifest.md) | Activity, Service, Receiver, Provider, Intents, deep links, permissions, notifications | 30 |
| 03 | [UI, Views & XML](./03_ui_views_xml.md) | Rendering pipeline, RecyclerView, custom views, touch dispatch, resources, drawables | 30 |
| 04 | [Jetpack Architecture](./04_jetpack_architecture.md) | ViewModel, Lifecycle, StateFlow, Navigation, MVVM/MVI, Clean Architecture, modularization | 30 |
| 05 | [Coroutines & Concurrency](./05_coroutines_concurrency.md) | Coroutine internals, structured concurrency, cancellation, Flow operators, JVM primitives | 30 |
| 06 | [Data & Networking](./06_data_networking.md) | Room, DataStore, Retrofit, OkHttp, serialization, caching, offline-first, error modelling | 30 |
| 07 | [Background Work](./07_background_work.md) | WorkManager, alarms, Doze, App Standby buckets, FCM, foreground services | 20 |
| 08 | [Dependency Injection](./08_dependency_injection.md) | Dagger, Hilt components & scopes, assisted injection, multibinding, Koin | 20 |
| 09 | [Jetpack Compose](./09_compose.md) | Recomposition, stability, effects, state, layout, animation, gestures, performance, testing | 40 |
| 10 | [Performance & Memory](./10_performance_memory.md) | Leaks, GC, ANR, startup, jank, profiling, app size, battery | 25 |
| 11 | [Testing & Security](./11_testing_security.md) | Testing pyramid, MockK/Turbine/Robolectric, Espresso, Keystore, TLS, OWASP MASVS | 25 |
| 12 | [KMP & CMP](./12_kmp_cmp.md) | expect/actual, Skiko rendering, Swift interop, memory model, adoption strategy | 20 |
| 13 | [Build & Release](./13_build_release_gradle.md) | Gradle lifecycle, variants, version catalogs, KSP, R8, signing, CI/CD | 20 |
| 14 | [Scenarios & System Design](./14_scenario_system_design.md) | Open-ended design, debugging, and judgment questions | 20 |
| | | **Total** | **370** |

---

## 🗓️ Study Plans

### One week before the interview
| Day | Focus |
|---|---|
| 1 | [04 Architecture](./04_jetpack_architecture.md) + [05 Coroutines](./05_coroutines_concurrency.md) — the two most-asked areas |
| 2 | [09 Compose](./09_compose.md) — or [03 Views](./03_ui_views_xml.md) if the role is View-based |
| 3 | [02 Components](./02_components_manifest.md) + [06 Data](./06_data_networking.md) |
| 4 | [10 Performance](./10_performance_memory.md) + [07 Background](./07_background_work.md) |
| 5 | [01 Internals](./01_system_internals.md) + [08 DI](./08_dependency_injection.md) |
| 6 | [11 Testing & Security](./11_testing_security.md) + [13 Build](./13_build_release_gradle.md) |
| 7 | [14 Scenarios](./14_scenario_system_design.md) — practice speaking answers out loud |

### One day before
Read only the **Follow-up** lines across every file. They are the second-order questions that separate a memorized answer from an understood one.

---

## 🎓 By Experience Level

**0–2 years** — focus on `[Junior]` and `[Mid]`:
[02 Components](./02_components_manifest.md) → [03 Views](./03_ui_views_xml.md) → [04 Architecture](./04_jetpack_architecture.md) → [05 Coroutines](./05_coroutines_concurrency.md) → [06 Data](./06_data_networking.md)

**2–5 years** — everything, with emphasis on:
[04 Architecture](./04_jetpack_architecture.md) · [05 Coroutines](./05_coroutines_concurrency.md) · [09 Compose](./09_compose.md) · [10 Performance](./10_performance_memory.md) · [14 Scenarios](./14_scenario_system_design.md)

**5+ years** — lead with `[Senior]` and the scenarios:
[14 Scenarios](./14_scenario_system_design.md) · [01 Internals](./01_system_internals.md) · [10 Performance](./10_performance_memory.md) · [13 Build](./13_build_release_gradle.md) · [12 KMP](./12_kmp_cmp.md)

---

## 💡 How to Use This Bank

1. **Answer out loud before reading the answer.** Recognizing an answer is not the same as producing one under pressure.
2. **Always read the follow-up.** Interviewers probe; the follow-up is where the real signal is.
3. **Name trade-offs, not verdicts.** "We used X because Y, accepting cost Z" beats "X is best practice" every time.
4. **Say "I don't know" and then reason.** Reasoning aloud from what you do know scores far better than a confident wrong answer.
5. **Bring your own examples.** Every answer here improves when you replace the generic case with something you actually shipped.

---

## 📖 Reference Guides

| Guide | Covers |
|---|---|
| [`../android.md`](../android.md) | Platform fundamentals, components, threading, data, DI, performance, media, Firebase, modern UI |
| [`../compose.md`](../compose.md) | Compose internals, state, effects, Material 3, animation, gestures, performance, testing |
| [`../xml.md`](../xml.md) | View system, layouts, resources, styles, drawables, MotionLayout, Data Binding |
| [`../architecture_patterns.md`](../architecture_patterns.md) | MVC/MVP/MVVM/MVI, Clean Architecture, modularization, SOLID, design patterns |
| [`../gradle_build.md`](../gradle_build.md) | Gradle lifecycle, variants, catalogs, KSP, R8, signing, build performance, CI/CD |
| [`../knowledge_points.md`](../knowledge_points.md) | Boot, Zygote, class loaders, JNI, AIDL, multi-process, APK anatomy, ANR triage |
| [`../testing_security.md`](../testing_security.md) | Testing stack, pentesting, Keystore, TLS, hardening, OWASP MASVS |
| [`../kmp_cmp.md`](../kmp_cmp.md) | Kotlin Multiplatform, Compose Multiplatform, Swift interop, adoption |
| [`../CD_Setup_Guide.md`](../CD_Setup_Guide.md) | Concrete CI/CD pipeline setup walkthrough |
| [`../../Languages/Kotlin.md`](../../Languages/Kotlin.md) | Kotlin language reference |
| [`../../Languages/java.md`](../../Languages/java.md) | Java and JVM reference |
