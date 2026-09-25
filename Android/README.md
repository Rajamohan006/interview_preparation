# 🤖 Android Interview Preparation

A complete technical reference for Android developers preparing for interviews at the **2–5 years experience** level, plus a **370-question** interview bank.

Every topic in every guide follows the same structure:

> **Definition** (simple + advanced) → **Why It Is Used** → **How It Works Internally** → **Code Example** → **Common Pitfalls**

with comparison tables and Mermaid diagrams where they clarify the mechanism.

Updated for **Android 16 (API 36)**, **Kotlin 2.x**, **Jetpack Compose**, **Media3**, **Hilt/Koin**, and current Play policy.

---

## 📚 Reference Guides

| Guide | Modules | Covers | Integrated Questions |
|---|---|---|---|
| [**android.md**](./android.md) | 15 | Platform stack, ART, components, Intents, permissions, notifications, views, RecyclerView, coroutines, Flow, Room, DataStore, Retrofit, WorkManager, Hilt/Koin, leaks, ANR, startup, profiling, CameraX, Media3, edge-to-edge, accessibility, interop | **215 Questions** (8 core sections) |
| [**compose.md**](./compose.md) | 15 | Recomposition, compiler internals, slot table, stability, strong skipping, effect APIs, snapshot system, state hoisting, navigation, layouts, lazy lists, `TextFieldState`, UDF, semantics, Material 3, animation, gestures, Canvas, `Modifier.Node`, testing | **40 Questions** (§15) |
| [**xml.md**](./xml.md) | 12 | XML architecture, custom attributes, custom views, resources, styles vs themes, drawables, inflation, adaptive layouts, ConstraintLayout helpers, selectors, animations, Data Binding, accessibility, MotionLayout, Navigation graphs | **30 Questions** (§12) |
| [**architecture_patterns.md**](./architecture_patterns.md) | 9 | MVC/MVP/MVVM/MVI compared with code, UDF and state modelling, Clean Architecture, repository pattern, modularization, SOLID on Android, design patterns, choosing an architecture | **50 Questions** (§9, Architecture & System Design) |
| [**gradle_build.md**](./gradle_build.md) | 11 | Gradle lifecycle, AGP pipeline, build variants, version catalogs, KSP vs kapt, convention plugins, R8, signing and distribution, build performance, CI/CD | **20 Questions** (§11) |
| [**knowledge_points.md**](./knowledge_points.md) | 16 | Boot sequence, Zygote and CoW, class loaders, reference types, heap structure, serialization, Intent flags, NDK/JNI, licenses, ML Kit, MediaPipe/TFLite, AIDL, multi-process, APK anatomy and signing, ANR triage, Multi-Service Dashboard Architecture | **68 Questions** (§15 & §16) |
| [**testing_security.md**](./testing_security.md) | 12 | Testing pyramid, JUnit/MockK/Turbine, Robolectric, Espresso, UI Automator, coverage, CI, bug classification, pentesting, reverse engineering, BOLA, OAuth, Keystore, encrypted storage, TLS and pinning, app hardening, OWASP MASVS | **25 Questions** (§12) |
| [**kmp_cmp.md**](./kmp_cmp.md) | 9 | KMP fundamentals, source sets, `expect`/`actual`, Skiko/Skia rendering, Ktor, SQLDelight, Koin, serialization, DataStore, Swift interop, memory model, XCFrameworks, testing, adoption strategy | **20 Questions** (§9) |
| [**performance.md**](./performance.md) | 56 | Low-end hardware constraints (RAM/CPU/GPU/eMMC), ART Concurrent Copying GC, LMKD, memory leaks, bitmap management, ANR, frame budgets, overdraw, RecyclerView & Compose optimization, Studio Profiler vs Perfetto vs AGI, 4 scenario triages | **25 Questions** (§52) |
| [**CD_Setup_Guide.md**](./CD_Setup_Guide.md) | 25 sections | Complete CI/CD architecture, Git workflows, GitHub Actions, keystore signing, Firebase, SonarCloud, Play Console, secrets, rollback, and 110+ interview questions | **110+ Questions** (§24) |

---

## 🎯 Integrated Interview Question Bank (433 Questions)

Every question has an **Answer**, a **Follow-up** probe with its answer, and **code** where code clarifies.
Difficulty is tagged inline: `[Junior]` · `[Mid]` · `[Senior]`.

| # | Topic | Questions | Integrated Inside Guide |
|---|---|---|---|
| 01 | System Internals & Multi-Service Architecture | 68 | [`knowledge_points.md#15`](./knowledge_points.md#15-android-system-internals-interview-questions-30-questions) & [`knowledge_points.md#16`](./knowledge_points.md#16-dashboard-with-multiple-services--interview-questions--definitions-38-questions) & [`android.md#151`](./android.md#151-system-internals--low-level-architecture-30-questions) |
| 02 | Components & Manifest | 30 | [`android.md#152`](./android.md#152-core-application-components--manifest-30-questions) |
| 03 | UI, Views & XML | 30 | [`xml.md#12`](./xml.md#12-ui-views--xml-interview-questions-30-questions) & [`android.md#153`](./android.md#153-ui-layouts-views-and-rendering-30-questions) |
| 04 | Jetpack Architecture | 30 | [`architecture_patterns.md#part-1`](./architecture_patterns.md#part-1-jetpack-architecture--state-management-30-questions) |
| 05 | Coroutines & Concurrency | 30 | [`android.md#154`](./android.md#154-threading-concurrency--reactive-streams-30-questions) |
| 06 | Data & Networking | 30 | [`android.md#155`](./android.md#155-data-storage--networking-30-questions) |
| 07 | Background Work | 20 | [`android.md#156`](./android.md#156-background-execution--scheduling-20-questions) |
| 08 | Dependency Injection | 20 | [`android.md#157`](./android.md#157-dependency-injection-20-questions) |
| 09 | Jetpack Compose | 40 | [`compose.md#15`](./compose.md#15-jetpack-compose-interview-questions-40-questions) |
| 10 | Performance & Memory | 25 | [`performance.md#52`](./performance.md#52-10-year-level-android-interview-questions) & [`android.md#158`](./android.md#158-performance-memory--diagnostics-25-questions) |
| 11 | Testing & Security | 25 | [`testing_security.md#12`](./testing_security.md#12-testing--security-interview-questions-25-questions) |
| 12 | KMP & CMP | 20 | [`kmp_cmp.md#9`](./kmp_cmp.md#9-kotlin-multiplatform--compose-multiplatform-interview-questions-20-questions) |
| 13 | Build, Release & Gradle | 20 | [`gradle_build.md#11`](./gradle_build.md#11-build-gradle--release-interview-questions-20-questions) |
| 14 | Scenarios & System Design | 20 | [`architecture_patterns.md#part-2`](./architecture_patterns.md#part-2-scenarios--system-design-20-questions) |
| | **Total Questions** | **433** | |

---

## 🗺️ Suggested Study Order

**If you are new to the material**
1. [`android.md`](./android.md) Modules 1–4 — platform, components, views, architecture
2. [`android.md`](./android.md) Module 5 — coroutines and Flow
3. [`compose.md`](./compose.md) Modules 1–3 — recomposition, effects, state
4. [`android.md`](./android.md) Modules 6–8 — data, background, DI
5. [`architecture_patterns.md`](./architecture_patterns.md) — tie it together
6. Test yourself on the integrated interview questions at the end of each guide

**If you are refreshing before an interview**
Read the **Follow-up** lines across each guide — they test deep understanding and trade-offs.

---

## 🧭 Finding a Topic

| Looking for | Go to |
|---|---|
| Activity lifecycle, launch modes | [`android.md`](./android.md) §2.1 |
| Coroutine internals, `suspend` | [`android.md`](./android.md) §5.1, [`§15.4`](./android.md#154-threading-concurrency--reactive-streams-30-questions) |
| Flow operators (`flatMapLatest`, `combine`) | [`android.md`](./android.md) §5.6 |
| Recomposition and stability | [`compose.md`](./compose.md) §1.5, §13 |
| Compose effect APIs | [`compose.md`](./compose.md) §2 |
| Memory leaks, GC | [`android.md`](./android.md) §9.1 |
| ANR diagnosis | [`knowledge_points.md`](./knowledge_points.md) §14 |
| App startup, Baseline Profiles | [`android.md`](./android.md) §9.4 |
| R8 keep rules | [`android.md`](./android.md) §1.4, [`gradle_build.md`](./gradle_build.md) §7 |
| MVVM vs MVI | [`architecture_patterns.md`](./architecture_patterns.md) §1 |
| Modularization | [`architecture_patterns.md`](./architecture_patterns.md) §5 |
| Room migrations | [`android.md`](./android.md) §6.1 |
| WorkManager vs alarms | [`android.md`](./android.md) §7.3 |
| Hilt scopes | [`android.md`](./android.md) §8.3 |
| Testing coroutines | [`testing_security.md`](./testing_security.md) §2.2 |
| Android Keystore | [`testing_security.md`](./testing_security.md) §8.1 |
| Edge-to-edge, insets | [`android.md`](./android.md) §12.1 |
| Gradle build speed | [`gradle_build.md`](./gradle_build.md) §9 |

---

## 🔗 Related Directories

| Directory | Covers |
|---|---|
| [`../Languages/`](../Languages/) | Kotlin and Java language references |
| [`../CyberSecurity/`](../CyberSecurity/) | Offensive and defensive security material |
| [`../ComputerNetworks/`](../ComputerNetworks/) | Networking fundamentals, protocols, routing |
| [`../Linux/`](../Linux/) | Linux fundamentals |
