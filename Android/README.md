# 🤖 Android Interview Preparation

A complete technical reference for Android developers preparing for interviews at the **2–5 years experience** level, plus a **370-question** interview bank.

Every topic in every guide follows the same structure:

> **Definition** (simple + advanced) → **Why It Is Used** → **How It Works Internally** → **Code Example** → **Common Pitfalls**

with comparison tables and Mermaid diagrams where they clarify the mechanism.

Updated for **Android 16 (API 36)**, **Kotlin 2.x**, **Jetpack Compose**, **Media3**, **Hilt/Koin**, and current Play policy.

---

## 📚 Reference Guides

| Guide | Modules | Covers |
|---|---|---|
| [**android.md**](./android.md) | 15 | Platform stack, ART, R8, APK/AAB, components, Intents, permissions, notifications, views, RecyclerView, custom views, touch dispatch, jank, ViewModel, Navigation, coroutines, Flow, Room, DataStore, Retrofit, serialization, offline-first, WorkManager, Doze, FCM, Hilt/Koin, leaks, ANR, startup, profiling, app size, CameraX, Media3, biometrics, Firebase, Play Integrity, edge-to-edge, predictive back, foldables, accessibility, i18n, interop |
| [**compose.md**](./compose.md) | 15 | Recomposition, compiler internals, slot table, stability, strong skipping, effect APIs, snapshot system, state hoisting, navigation, layouts, lazy lists, `TextFieldState`, UDF, semantics, Material 3, animation, gestures, Canvas, `Modifier.Node`, compiler metrics, testing |
| [**xml.md**](./xml.md) | 12 | XML architecture, custom attributes, custom views, resources, styles vs themes, drawables, inflation, adaptive layouts, ConstraintLayout helpers, selectors, animations, Data Binding, accessibility, MotionLayout, Navigation graphs, dark theme, qualifiers |
| [**architecture_patterns.md**](./architecture_patterns.md) | 9 | MVC/MVP/MVVM/MVI compared with code, UDF and state modelling, Clean Architecture, repository pattern, modularization, SOLID on Android, design patterns, choosing an architecture |
| [**gradle_build.md**](./gradle_build.md) | 11 | Gradle lifecycle, AGP pipeline, build variants, version catalogs, KSP vs kapt, convention plugins, R8, signing and distribution, build performance, CI/CD |
| [**knowledge_points.md**](./knowledge_points.md) | 15 | Boot sequence, Zygote and CoW, class loaders, reference types, heap structure, serialization, Intent flags, NDK/JNI, licenses, ML Kit, MediaPipe/TFLite, AIDL, multi-process, APK anatomy and signing, ANR triage |
| [**testing_security.md**](./testing_security.md) | 12 | Testing pyramid, JUnit/MockK/Turbine, Robolectric, Espresso, UI Automator, coverage, CI, bug classification, pentesting, reverse engineering, BOLA, OAuth, Keystore, encrypted storage, TLS and pinning, app hardening, OWASP MASVS |
| [**kmp_cmp.md**](./kmp_cmp.md) | 9 | KMP fundamentals, source sets, `expect`/`actual`, Skiko/Skia rendering, Ktor, SQLDelight, Koin, serialization, DataStore, Swift interop, memory model, XCFrameworks, testing, adoption strategy |
| [**CD_Setup_Guide.md**](./CD_Setup_Guide.md) | 10 steps | A concrete CI/CD pipeline walkthrough: keystore encoding, Firebase, SonarCloud, Play service account, secrets, branch protection, verification, rollback, troubleshooting |

---

## 🎯 Interview Question Bank

**[`interview_questions/`](./interview_questions/00_INDEX.md) — 370 questions**, each with an answer, a follow-up probe, and code where it clarifies.

| File | Topic | Q |
|---|---|---|
| [01](./interview_questions/01_system_internals.md) | System internals | 30 |
| [02](./interview_questions/02_components_manifest.md) | Components & manifest | 30 |
| [03](./interview_questions/03_ui_views_xml.md) | UI, views & XML | 30 |
| [04](./interview_questions/04_jetpack_architecture.md) | Jetpack architecture | 30 |
| [05](./interview_questions/05_coroutines_concurrency.md) | Coroutines & concurrency | 30 |
| [06](./interview_questions/06_data_networking.md) | Data & networking | 30 |
| [07](./interview_questions/07_background_work.md) | Background work | 20 |
| [08](./interview_questions/08_dependency_injection.md) | Dependency injection | 20 |
| [09](./interview_questions/09_compose.md) | Jetpack Compose | 40 |
| [10](./interview_questions/10_performance_memory.md) | Performance & memory | 25 |
| [11](./interview_questions/11_testing_security.md) | Testing & security | 25 |
| [12](./interview_questions/12_kmp_cmp.md) | KMP & CMP | 20 |
| [13](./interview_questions/13_build_release_gradle.md) | Build & release | 20 |
| [14](./interview_questions/14_scenario_system_design.md) | Scenarios & system design | 20 |

---

## 🗺️ Suggested Study Order

**If you are new to the material**
1. [`android.md`](./android.md) Modules 1–4 — platform, components, views, architecture
2. [`android.md`](./android.md) Module 5 — coroutines and Flow
3. [`compose.md`](./compose.md) Modules 1–3 — recomposition, effects, state
4. [`android.md`](./android.md) Modules 6–8 — data, background, DI
5. [`architecture_patterns.md`](./architecture_patterns.md) — tie it together
6. [`interview_questions/`](./interview_questions/00_INDEX.md) — test yourself

**If you are refreshing before an interview**
Follow the one-week plan in the [question bank index](./interview_questions/00_INDEX.md#-study-plans).

---

## 🧭 Finding a Topic

| Looking for | Go to |
|---|---|
| Activity lifecycle, launch modes | [`android.md`](./android.md) §2.1 |
| Coroutine internals, `suspend` | [`android.md`](./android.md) §5.1, [`Q05`](./interview_questions/05_coroutines_concurrency.md) |
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
