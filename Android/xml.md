# 🎨 Android XML & Layout Architecture — Complete Interview Preparation Guide

> **Authoritative Technical Reference**
> Every topic follows the same structure — **Definition → Why It Is Used → How It Works Internally → Code Example → Common Pitfalls**.
>
> Covers the View system: layouts, resources, styles, themes, drawables, Data Binding, MotionLayout, and Navigation graphs.
>
> **30 View-system interview questions:** [Section 12](#12-ui-views--xml-interview-questions-30-questions)

---

## 📑 Table of Contents

| # | Module | Key Topics |
|---|---|---|
| 1 | [XML Role & Architecture](#1-xml-role--architecture-in-android) | Why XML, namespaces, core attributes |
| 2 | [Custom Attributes & Custom Views](#2-custom-xml-attributes--custom-views) | `declare-styleable`, `TypedArray`, custom view lifecycle |
| 3 | [Resources, Styles & Themes](#3-resource-management-styles-and-themes) | Resource organization, styles vs themes, drawable types |
| 4 | [Layout Inflation & Performance](#4-layout-inflation--performance-optimization) | `LayoutInflater` internals, `merge`, `ViewStub`, `include` |
| 5 | [Adaptive Layouts & Qualifiers](#5-adaptive-layouts--resource-qualifiers) | Screen qualifiers, weights, ConstraintLayout helpers |
| 6 | [Selectors & XML Animations](#6-interactive-selectors--xml-custom-animations) | State lists, animation sets, interpolators |
| 7 | [Data Binding](#7-advanced-data-binding-in-xml) | Binding expressions, two-way binding, binding adapters |
| 8 | [Accessibility & Localization](#8-accessibility--internationalization) | Content descriptions, RTL, string resources |
| 9 | [MotionLayout](#9-motionlayout) | MotionScene, keyframes, `OnSwipe`, transition listeners |
| 10 | [Navigation Graphs & Safe Args](#10-navigation-graphs-and-safe-args) | Destinations, actions, typed arguments, nested graphs |
| 11 | [Dark Theme & Qualifiers](#11-dark-theme-theme-overlays-and-resource-qualifiers) | `values-night`, theme overlays, qualifier precedence |
| 12 | [Interview Questions](#12-interview-questions) | Pointer to the question bank |

---

# 1. XML Role & Architecture in Android

## 1.1 Role of XML

### Definition
* **Simple:** XML (Extensible Markup Language) is a readable text format used in Android to build screen layouts, define color/string values, and configure application settings.
* **Advanced:** XML serves as the declarative configuration and presentation layer in Android. It separates the declarative view structure from the imperative controller logic (Java/Kotlin), enabling compile-time generation of layout resources and structured indexing via the `R.java` pointer file.

```mermaid
graph LR
    SourceXML[layout.xml] -->|Compiled by AAPT2| BinaryXML[Compiled Binary XML]
    BinaryXML -->|Included in APK| DEX[DEX Runtime]
    DEX -->|Inflater parses via XmlPullParser| ViewTree[RAM View Objects Tree]
```

### Why It Is Used
XML allows clean **Separation of Concerns**. UI designers and developers can write layout configurations independently of business logic. It also supports **multimodal resource loading**—the OS automatically selects different XML files at runtime depending on the device config (language, orientation, screen size) without changing code.

---

## 1.2 Structure of an Android XML & Namespaces

### Definition
* **XML namespace** — a prefix bound to a URI that tells the build tools which vocabulary an attribute belongs to, so identically-named attributes from different sources cannot collide.
* **`android:`** — the framework namespace (`http://schemas.android.com/apk/res/android`), covering every platform attribute.
* **`app:`** — the application/library namespace (`.../apk/res-auto`), used by AndroidX and by your own custom attributes.
* **`tools:`** — a **design-time-only** namespace. Its attributes are stripped from the built APK, so they configure the preview without affecting runtime.
* **Resource reference (`@`) vs attribute reference (`?`)** — `@color/red` points at a fixed resource; `?attr/colorPrimary` resolves through the **current theme**, which is what makes dark mode and theming work.

### Namespace Declarations
Namespaces tell the compiler how to interpret custom attributes inside the XML document.
* `xmlns:android="http://schemas.android.com/apk/res/android"`: Maps standard attributes provided by the Android OS framework.
* `xmlns:app="http://schemas.android.com/apk/res-auto"`: Maps attributes belonging to support libraries (Jetpack) or custom attributes defined in your own application modules.
* `xmlns:tools="http://schemas.android.com/tools"`: Provides layout editor design-time overrides that do not compile into the final production APK.

### Core Specifying Attributes
1. **`layout_width` and `layout_height`:** Dimensions of the view component. Can be fixed (e.g. `100dp`), `wrap_content` (expand to fit internal contents), or `match_parent` (expand to fill parent constraints).
2. **Margin vs. Padding:**
   * **Margin:** Specifies the empty spacing *outside* the boundaries of a view.
   * **Padding:** Specifies the spacing *inside* the view boundaries (between the view border and the view's content).
3. **Gravity vs. Layout Gravity:**
   * **`android:gravity`:** Aligns the *internal content* within the boundaries of the view itself.
   * **`android:layout_gravity`:** Aligns the *entire view* within the layout bounds of its parent ViewGroup.

---

# 2. Custom XML Attributes & Custom Views

## 2.1 Custom View Lifecycle & Attribute Extraction

### Definition
A **Custom View** is a user-created component that inherits from `View` (or widgets like `TextView`), using custom XML attributes to configure its styling directly in the layout file.

```mermaid
sequenceDiagram
    participant XML as Layout XML
    participant System as Context / Inflater
    participant View as CustomView Instance
    participant AT as TypedArray (Native Memory)

    XML->>System: References custom attributes (app:customColor)
    System->>View: Instantiates via Constructor(Context, AttributeSet)
    View->>System: Calls obtainStyledAttributes()
    System->>AT: Allocates native Attribute array
    AT->>View: Returns TypedArray data
    View->>View: Reads values (ta.getColor())
    View->>AT: Calls recycle() (Crucial: frees native memory)
    View->>View: Applies style (setTextColor)
```

### Why It Is Used
It groups styling configurations into reusable UI elements, allowing developers to configure custom view layouts without writing repetitive configuration code.

### Code Example: Creating a Custom View with Custom Attributes
1. **Define Attributes (`res/values/attrs.xml`):**
   ```xml
   <resources>
       <declare-styleable name="CustomBadgeView">
           <attr name="badgeColor" format="color" />
           <attr name="badgeCount" format="integer" />
       </declare-styleable>
   </resources>
   ```
2. **Extract Attributes in custom View Class (Kotlin):**
   ```kotlin
   class CustomBadgeView @JvmOverloads constructor(
       context: Context,
       attrs: AttributeSet? = null,
       defStyleAttr: Int = 0
   ) : TextView(context, attrs, defStyleAttr) {

       init {
           attrs?.let {
               // Extract values from TypedArray
               val typedArray = context.obtainStyledAttributes(it, R.styleable.CustomBadgeView)
               try {
                   val badgeColor = typedArray.getColor(
                       R.styleable.CustomBadgeView_badgeColor, 
                       Color.RED
                   )
                   val badgeCount = typedArray.getInt(
                       R.styleable.CustomBadgeView_badgeCount, 
                       0
                   )
                   
                   // Apply configurations to View
                   setBackgroundColor(badgeColor)
                   text = badgeCount.toString()
               } finally {
                   // CRITICAL: Always recycle TypedArray to prevent native memory leak
                   typedArray.recycle()
               }
           }
       }
   }
   ```
3. **Reference View in XML:**
   ```xml
   <com.example.app.CustomBadgeView
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       app:badgeColor="#FF00FF"
       app:badgeCount="99" />
   ```

---

# 3. Resource Management, Styles, and Themes

## 3.1 Resource Management

### Definition
* **Resource** — any non-code asset the app ships: strings, colours, dimensions, layouts, drawables, animations. Each lives in a typed directory under `res/`.
* **Why resources are not constants in code** — externalizing them is what allows the **same reference** to resolve differently per language, screen density, orientation, or theme.
* **Resource ID** — the generated integer in the `R` class that identifies a resource; `R.string.title` is compile-checked, so a typo is a build error.
* **Qualifier** — a suffix on a directory name (`values-night`, `layout-w600dp`) declaring the configuration it applies to.
* **Default resource** — the unqualified directory, which must always exist as the fallback when no qualified variant matches.

### File Separation Best Practices
* **`colors.xml`:** Centralizes all hex color values. Prevents hardcoded color constants across screens.
* **`dimens.xml`:** Centralizes dimensions. Use **`dp`** (density-independent pixels) for layouts to ensure consistent physical sizing across pixel-density layouts, and **`sp`** (scale-independent pixels) for text sizes to respect user-defined system font scale settings.
* **`strings.xml`:** Stores user-facing text strings, enabling localization and internationalization.
* **`styles.xml` / `themes.xml`:** Encapsulates style attributes to ensure design consistency.

---

## 3.2 Styles vs. Themes

### Definition
* **Style** — a named bundle of attributes applied to a **single view**, replacing repeated attributes on each widget.
* **Theme** — a style applied to an **Activity or a view subtree**, whose values are inherited by everything inside and can be referenced with `?attr/`.
* **The distinguishing property** — a style affects only the view it is set on; a theme's values propagate down the hierarchy.
* **Theme attribute** — a named slot (`colorPrimary`, `colorSurface`) that different themes fill with different values. Referencing the slot rather than a fixed color is what lets one file swap the whole app's appearance.
* **`ThemeOverlay`** — a partial theme that changes only a few attributes for one subtree, without redefining a complete theme.

### Comparison Table

| Feature | Style | Theme |
|---|---|---|
| **Scope** | Local (Applied to a single `View` instance) | Global (Applied to an `Activity` or the entire `<application>`) |
| **Declaration** | `style="@style/MyButtonStyle"` | `android:theme="@style/AppTheme"` |
| **Inheritance** | Single specific component hierarchy | Cascading (all children inherit properties) |
| **Usage** | Overrides layout dimensions, backgrounds, text | Configures status bar color, window background, accent color |

### Global Theme Example (`res/values/themes.xml`):
```xml
<resources>
    <style name="AppTheme" parent="Theme.Material3.DayNight.NoActionBar">
        <item name="colorPrimary">@color/blue_500</item>
        <item name="colorSecondary">@color/teal_200</item>
        <item name="android:statusBarColor">?attr/colorPrimary</item>
    </style>
</resources>
```

---

## 3.3 Drawable Types

### Definition
* **Drawable** — anything that knows how to draw itself into a `Canvas`. It is an abstraction over images, shapes, gradients, and state-dependent graphics.
* **Raster vs vector** — a raster (PNG, WebP) stores pixels and therefore needs one asset per density; a **vector** stores drawing instructions and scales to any density from a single file.
* **`ConstantState`** — the shared state object behind drawables loaded from the same resource. Because it is shared, changing one drawable's tint changes **every** user of that resource unless you call `mutate()` first.
* **Nine-patch** — a raster image with a 1-pixel border marking which regions may stretch and where content may sit.
* **Layer list** — several drawables stacked with individual insets, which frequently removes the need for an extra wrapper view.

### Why It Is Used
A single vector replaces five raster densities. A shape drawable replaces a nine-patch. A layer-list replaces an extra nested `FrameLayout`. Each substitution removes assets, view depth, or both.

### How It Works Internally

| Type | Root Tag | Best For | Notes |
|---|---|---|---|
| **Vector** | `<vector>` | Icons, simple illustrations | One asset for all densities; parsed and rasterized at load, cached afterward |
| **Shape** | `<shape>` | Backgrounds, dividers, chips | Rectangle/oval/line/ring with gradient, stroke, corners |
| **Layer list** | `<layer-list>` | Stacking without extra views | Each `<item>` is a layer with its own insets |
| **State list** | `<selector>` | Pressed/checked/disabled states | Order matters — first match wins |
| **Nine-patch** | `.9.png` | Stretchable raster (chat bubbles) | The 1 px border defines stretch and content regions |
| **Animated vector** | `<animated-vector>` | Icon morphs (play↔pause) | Binds an `<objectAnimator>` to a named vector path |
| **Ripple** | `<ripple>` | Touch feedback | Default on all clickable Material views |
| **Inset / clip / scale** | `<inset>` etc. | Adjusting an existing drawable | Wrappers around another drawable |

**Vector rendering cost.** Vectors are rasterized on first use and cached. A very complex path (a detailed illustration) is measurably slower to inflate than a PNG — use vectors for icons, WebP for photographic or complex art.

### Code Example
```xml
<!-- res/drawable/ic_favorite.xml — one asset, every density -->
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24dp"
    android:height="24dp"
    android:viewportWidth="24"
    android:viewportHeight="24"
    android:tint="?attr/colorControlNormal">   <!-- Theme attribute: adapts to dark mode -->
    <path
        android:fillColor="@android:color/white"
        android:pathData="M12,21.35l-1.45,-1.32C5.4,15.36 2,12.28 2,8.5 2,5.42 4.42,3 7.5,3c1.74,0 3.41,0.81 4.5,2.09C13.09,3.81 14.76,3 16.5,3 19.58,3 22,5.42 22,8.5c0,3.78 -3.4,6.86 -8.55,11.54L12,21.35z" />
</vector>
```

```xml
<!-- res/drawable/bg_card.xml — replaces a PNG and adapts to the theme -->
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <corners android:radius="12dp" />
    <solid android:color="?attr/colorSurface" />
    <stroke android:width="1dp" android:color="?attr/colorOutline" />
    <padding android:left="16dp" android:top="12dp" android:right="16dp" android:bottom="12dp" />
</shape>
```

```xml
<!-- res/drawable/bg_badge_shadow.xml — a fake shadow without an extra view -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:top="2dp">                         <!-- Offset layer = shadow -->
        <shape android:shape="oval">
            <solid android:color="#22000000" />
        </shape>
    </item>
    <item android:bottom="2dp">                      <!-- Foreground layer -->
        <shape android:shape="oval">
            <solid android:color="?attr/colorPrimary" />
        </shape>
    </item>
</layer-list>
```

```xml
<!-- res/drawable/ic_play_to_pause.xml — animated vector for an icon morph -->
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/ic_play">
    <target
        android:name="leftBar"
        android:animation="@animator/play_to_pause_left" />
</animated-vector>
```

```kotlin
// Starting the morph from code
val avd = AppCompatResources.getDrawable(context, R.drawable.ic_play_to_pause)
imageView.setImageDrawable(avd)
(avd as? AnimatedVectorDrawableCompat)?.start()

// Tinting programmatically without mutating the shared constant state
val tinted = AppCompatResources.getDrawable(context, R.drawable.ic_favorite)!!
    .mutate()                                        // MANDATORY: without it, every user of this
                                                     // drawable resource gets the tint too
    .apply { setTint(ContextCompat.getColor(context, R.color.accent)) }
```

### Common Pitfalls
* **Not calling `mutate()` before changing a drawable's state.** Drawables loaded from the same resource share a `ConstantState`; tinting one tints them all, app-wide, with no obvious cause.
* **Hardcoding colors instead of `?attr/` theme references.** The drawable then cannot follow dark mode.
* **Using vectors for complex illustrations.** Inflation cost grows with path complexity; profile before shipping a 200-path vector.
* **Shipping PNGs at five densities** when a `<shape>` or vector would do.

---
# 4. Layout Inflation & Performance Optimization

## 4.1 Layout Inflation Internals

### Definition
* **Simple:** Layout Inflation is the process of converting an XML layout file into visual Java/Kotlin view objects in RAM.
* **Advanced:** Inflation is triggered via `LayoutInflater.inflate()`. It reads compiled binary XML resources using an `XmlPullParser`, maps layout nodes to View classes using reflection (`Constructor.newInstance()`), and builds a nested tree of physical View objects in memory.

```mermaid
graph TD
    Inflate[LayoutInflater.inflate called] --> Parser[XmlPullParser reads node]
    Parser --> Reflection{Resolve ClassName?}
    Reflection -->|Finds Class| Instantiate[Reflection: instantiate View object]
    Instantiate --> Attaches[Add child view to parent ViewGroup]
    Attaches --> Loop[Loop for nested nodes]
```

### Inflation Optimization Tags
1. **`<include>`:** Injects a reusable XML layout into a parent layout. Helps reduce code duplication, but doesn't change performance on its own.
2. **`<merge>`:** Used as the root tag in layout files intended for inclusion. It merges child views directly into the parent view container, eliminating redundant nested layouts.
3. **`<ViewStub>`:** A lightweight, invisible view with zero dimensions. It serves as a placeholder that does not inflate its target layout until explicitly requested (`viewStub.inflate()`). Excellent for loading conditional screens (like errors or load pages) only when needed.

---

# 5. Adaptive Layouts & Resource Qualifiers

## 5.1 Screen Adaptation Qualifiers

### Definition
Android selects optimized layouts at runtime based on configuration qualifiers added as directory suffixes under the `res/` folder.

```mermaid
graph TD
    DeviceConfig[Device Config: Sw600dp, Landscape] --> Match{Qualifier Matcher}
    Match -->|res/layout-sw600dp-land| TabletLandscape[Load optimized tablet landscape layout]
    Match -->|res/layout-land| PhoneLandscape[Load phone landscape layout]
    Match -->|res/layout| DefaultPortrait[Load default portrait layout]
```

### Standard Directory Qualifiers
* **`layout/`:** Default layouts (usually portrait mobile layouts).
* **`layout-land/`:** Layouts optimized for landscape mode on all screen sizes.
* **`layout-sw600dp/`:** Smallest Width layouts. Applies only when the shortest screen dimension is at least 600dp (standard target for 7-inch tablets).
* **`layout-sw600dp-land/`:** Tablet layouts in landscape mode.

---

## 5.2 Dynamic View Spacing in XML

### Definition
* **The problem** — a layout must adapt to screen sizes and text lengths that were never anticipated, so spacing cannot be a set of fixed numbers.
* **`layout_weight`** — in a `LinearLayout`, the proportion of **leftover** space a child receives. Using it requires setting the corresponding dimension to `0dp` so the child claims none of the space up front.
* **Margin vs padding** — margin is space **outside** a view's boundary (between it and its neighbours); padding is space **inside**, between the boundary and the content.
* **`match_parent` / `wrap_content` / fixed `dp`** — fill the parent, size to the content, or commit to an exact size that will not adapt.
* **Why weighted nesting is costly** — a weighted `LinearLayout` measures its children **twice**, and nesting them multiplies that cost per level.

### Using `layout_weight`
In `LinearLayout`, `layout_weight` specifies how much remaining space should be distributed among sibling views.
* **Best Practice:** Set the size attribute corresponding to the layout orientation to `0dp` (e.g. `android:layout_width="0dp"` in horizontal orientation) so the compiler calculates dimensions using weight ratios, bypassing static measurements.

### ConstraintLayout Positioning
Provides a flat view hierarchy by aligning components via relative constraints. Uses bias (`layout_constraintHorizontal_bias`) to position views dynamically as percentages of the remaining parent layout size.

---

## 5.3 ConstraintLayout: Chains, Barriers, Guidelines, Groups, and Flow

### Definition
* **Constraint** — a declared relationship between an edge of one view and an edge of another (or of the parent). A view needs at least one horizontal and one vertical constraint to be positioned.
* **Why it replaces nesting** — the layout solves the whole screen as a system of equations in a **flat** hierarchy, removing the repeated measure passes that nested layouts cost.
* **Chain** — a run of views linked in both directions along an axis, distributed together (`spread`, `spread_inside`, `packed`).
* **Guideline** — an invisible line at a fixed `dp` or a percentage of the parent, used as a constraint target.
* **Barrier** — an invisible line that tracks the **extreme edge of several views**, so a column adapts to whichever neighbour is longest. This is what makes label/value layouts survive translation.
* **Group** — a handle that toggles the visibility of several views together.
* **Flow** — a virtual chain that **wraps** onto multiple lines, replacing a nested layout for tag rows.

### Why It Is Used
Every nesting level costs another measure pass, and nested weighted `LinearLayout`s measure their children twice per level. `ConstraintLayout` solves the whole layout with a constraint solver in a flat hierarchy, so removing nesting is the single biggest View-system layout win.

### How It Works Internally
`ConstraintLayout` builds a system of linear equations from the constraints and solves it with Cassowary, the same algorithm behind iOS Auto Layout. Guidelines, barriers, and groups are `View`s with `visibility="gone"` — they occupy no space and never draw, they only contribute constraints.

| Helper | Purpose |
|---|---|
| **Chain** | Distribute a run of views along an axis (`spread`, `spread_inside`, `packed`) |
| **Guideline** | A virtual line at a fixed dp or a percentage of the parent |
| **Barrier** | A virtual line at the extreme edge of several views — sizes to the longest |
| **Group** | Toggles the visibility of several views at once |
| **Flow** | A virtual chain that wraps, replacing a nested layout for tag lists |
| **Layer** | Transforms (rotate/scale) a set of views together |

### Code Example
```xml
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <!-- Guideline: content never crosses the 50% mark -->
    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/half"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.5" />

    <TextView
        android:id="@+id/label_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/name"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/label_email"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/email_address"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/label_name" />

    <!-- Barrier: the value column starts after the LONGEST label, in any language.
         This is what makes the layout survive translation without hardcoded widths. -->
    <androidx.constraintlayout.widget.Barrier
        android:id="@+id/label_barrier"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="end"
        app:constraint_referenced_ids="label_name,label_email" />

    <TextView
        android:id="@+id/value_name"
        android:layout_width="0dp"                       <!-- 0dp = "match constraints" -->
        android:layout_height="wrap_content"
        app:layout_constraintStart_toEndOf="@id/label_barrier"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="@id/label_name" />

    <!-- Group: one visibility toggle for an entire section -->
    <androidx.constraintlayout.widget.Group
        android:id="@+id/premium_section"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="gone"
        app:constraint_referenced_ids="badge,expiry_label,expiry_value" />

    <!-- Flow: a wrapping tag row without a nested layout or a RecyclerView -->
    <androidx.constraintlayout.helper.widget.Flow
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="tag1,tag2,tag3,tag4"
        app:flow_wrapMode="chain"
        app:flow_horizontalGap="8dp"
        app:flow_verticalGap="8dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="@id/value_name" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

```xml
<!-- A weighted chain: replaces a horizontal LinearLayout with layout_weight -->
<Button
    android:id="@+id/cancel"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    app:layout_constraintHorizontal_chainStyle="spread"
    app:layout_constraintHorizontal_weight="1"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toStartOf="@id/confirm" />
<Button
    android:id="@+id/confirm"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    app:layout_constraintHorizontal_weight="2"          <!-- Twice the width of cancel -->
    app:layout_constraintStart_toEndOf="@id/cancel"
    app:layout_constraintEnd_toEndOf="parent" />
```

```kotlin
// ConstraintSet: animate between two full layout states with one line
val collapsed = ConstraintSet().apply { clone(context, R.layout.header_collapsed) }
val expanded  = ConstraintSet().apply { clone(context, R.layout.header_expanded) }

fun toggle(expandedNow: Boolean) {
    TransitionManager.beginDelayedTransition(binding.root, AutoTransition())
    (if (expandedNow) expanded else collapsed).applyTo(binding.root)
}
```

### Common Pitfalls
* **`match_parent` inside a `ConstraintLayout`.** It is not supported and behaves unpredictably; use `0dp` (match constraints) with start and end constraints.
* **Missing a constraint on one axis.** The view silently jumps to (0,0) at runtime while looking fine in the editor. Enable "Missing constraints" lint.
* **Hardcoded widths for label columns.** They break in German and Arabic. Use a `Barrier`.
* **Setting each view's visibility individually.** Use a `Group`, or the states drift out of sync.
* **Nesting `ConstraintLayout` inside `ConstraintLayout`.** This defeats the entire point; flatten instead.

---
# 6. Interactive Selectors & XML Custom Animations

## 6.1 State-List Selectors

### Definition
* **State-list drawable (`<selector>`)** — a drawable that picks a different image or shape depending on the view's current **state**.
* **View state** — a boolean condition the framework reports: `state_pressed`, `state_focused`, `state_selected`, `state_checked`, `state_enabled`.
* **First-match-wins** — the rule that governs the whole file: the system uses the **first** `<item>` whose listed conditions all hold, so the unconditional default must be **last** or nothing after it is reachable.
* **Colour state list** — the same idea applied to colours rather than drawables, letting text colour change per state.

A **Selector** is a drawable resource defined in XML that updates a view's appearance based on state changes (e.g. focused, pressed, selected, disabled).

```xml
<!-- res/drawable/button_state_selector.xml -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Pressed state -->
    <item android:drawable="@color/blue_pressed" android:state_pressed="true" />
    <!-- Focused state -->
    <item android:drawable="@color/blue_focused" android:state_focused="true" />
    <!-- Default state -->
    <item android:drawable="@color/blue_default" />
</selector>
```

---

## 6.2 XML Custom Animations & Interpolators

### Definition
* **View animation (`<set>`, `<translate>`, `<alpha>`)** — the legacy system that changes only how a view is **drawn**; the view's actual position and its touch target do not move.
* **Property animation (`<objectAnimator>`)** — the modern system that changes real property values over time, so layout and touch handling follow.
* **Interpolator** — the curve mapping elapsed time to progress. It is what makes motion feel natural rather than mechanical: `accelerate`, `decelerate`, `overshoot`, `bounce`.
* **Duration and start offset** — how long a step takes, and how long it waits before beginning, which is what lets a `<set>` stagger its children.

Animations can be declared in XML files located in the `res/anim/` folder.

### Animation Set Tags
* **`<set>`:** Container that groups multiple animation operations together.
* **`<translate>`:** Moves a view horizontally or vertically.
* **`<alpha>`:** Animates the opacity (transparency) of a view.
* **`<scale>`:** Resizes a view along the X and Y axes.
* **`<rotate>`:** Spins a view around a specific pivot point.

### Interpolators
Modify the rate of animation progress over time.
* `LinearInterpolator`: Constant speed.
* `AccelerateInterpolator`: Starts slow and accelerates.
* `DecelerateInterpolator`: Starts fast and slows down.

---

# 7. Advanced Data Binding in XML

## 7.1 Data Binding

### Definition
* **Simple:** Data Binding links UI layout elements directly to data model variables, reducing boilerplate activity code.
* **Advanced:** Data Binding is a support library that auto-generates binding classes at compile time based on `<layout>` wrapped XML files. It replaces runtime `findViewById()` calls with direct binding references, resolving layout updates during compilation.

```mermaid
graph LR
    LayoutXML[wrap layout tag in layout.xml] -->|Compiler Generation| BindingClass[ActivityMainBinding class]
    BindingClass -->|Maps variables| LiveData[ViewModel LiveData]
    LiveData -->|Automatic updates| VisualUI[Device Screen Views]
```

### Double-Way Binding (`@={}` vs. `@{}`)
* **One-Way Binding (`@{variable}`):** Passes data from the model down to the UI view. If the variable updates, the view is updated automatically.
* **Two-Way Binding (`@={variable}`):** Synchronizes changes in both directions. If the user edits text inside an `EditText`, the bound variable update propagates back to the data class automatically.

```xml
<!-- Example Layout XML -->
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable
            name="viewmodel"
            type="com.example.app.UserViewModel" />
    </data>
    
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
        
        <!-- Two-Way Binding: changes sync to ViewModel automatically -->
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={viewmodel.inputUserName}" />
    </LinearLayout>
</layout>
```

---

# 8. Accessibility & Internationalization

## 8.1 Accessibility (a11y) in XML

### Definition
* **Accessibility** — making the screen usable by people navigating with a screen reader, a switch device, enlarged text, or high-contrast settings.
* **`android:contentDescription`** — the text a screen reader speaks for an element that has no visible label, such as an icon button. It should describe the **action**, not the picture.
* **`android:importantForAccessibility="no"`** — removes a purely decorative element from the accessibility tree, so the user does not stop on something meaningless.
* **`android:labelFor`** — links a label to the input it describes, so focusing the field announces its purpose.
* **Touch target** — the tappable area, required to be at least **48 × 48 dp** however small the icon inside it is drawn.
* **`sp` vs `dp`** — text sizes use `sp` so they follow the user's font-scale setting; everything else uses `dp`.

Accessibility ensures that users with visual, auditory, or motor impairments can interact with your application.

### Key Best Practices
1. **`contentDescription`:** Add clear descriptions to all non-text components (e.g. `ImageView` or `ImageButton`) so screen readers (like TalkBack) can read them aloud.
2. **`labelFor`:** Links a label (`TextView`) to a specific input field (`EditText`), so TalkBack reads the input purpose context correctly.
3. **Focus Navigation:** Use `android:focusable="true"` and `android:nextFocusDown="..."` to configure keyboard and D-pad navigation paths.
4. **Touch Targets:** Maintain a minimum size of **`48dp x 48dp`** for all interactive components to ensure comfortable touch accuracy.

---

## 8.2 Localization (l10n) in XML

### Definition
* **Internationalization (i18n)** — building the app so it *can* be translated: no hardcoded strings, no assumptions about word order, text direction, or number and date format.
* **Localization (l10n)** — supplying the actual translations and locale-specific resources.
* **String resource** — text extracted into `strings.xml` so it can be replaced per locale without touching code.
* **Positional argument (`%1$s`)** — a numbered placeholder, which lets a translator **reorder** the substitutions when the target language needs a different word order.
* **Plurals (`<plurals>`)** — a set of forms selected by quantity. It exists because languages have up to six plural categories, so an `if (n == 1)` check is wrong outside English.
* **RTL (right-to-left)** — languages such as Arabic and Hebrew, supported by using `start`/`end` instead of `left`/`right` throughout.

Android manages localization using resource qualifiers mapped to locale tags.

### Implementation Setup
* **`res/values/strings.xml` (Default English):**
  ```xml
  <resources>
      <string name="welcome_message">Welcome</string>
  </resources>
  ```
* **`res/values-es/strings.xml` (Spanish translation):**
  ```xml
  <resources>
      <string name="welcome_message">Bienvenido</string>
  </resources>
  ```
The system automatically detects the device's locale settings and selects the corresponding string translation folder.

---

# 9. MotionLayout

## 9.1 Declarative Motion in XML

### Definition
* **MotionLayout** — a `ConstraintLayout` subclass that animates **between two complete layout states** instead of animating individual properties.
* **`ConstraintSet`** — one complete set of constraints describing the layout at one end of the transition. MotionLayout interpolates every attribute that differs between the start and end sets.
* **MotionScene** — the separate XML file holding those sets and the transition between them, keeping motion out of the layout file.
* **Progress** — a value from 0.0 to 1.0 representing how far through the transition the layout is. Everything else is derived from it.
* **`<OnSwipe>`** — binds progress directly to a drag, so the animation follows the finger and can be reversed mid-gesture rather than merely playing.
* **Keyframe** — an intermediate state at a given progress point, used to bend the motion path or change a property partway through.

### Why It Is Used
Complex coordinated motion — a collapsing toolbar with a scaling avatar, a swipe-to-reveal panel — expressed imperatively becomes hundreds of lines of interdependent animator code. MotionLayout expresses the same thing as two end states plus rules, and gives you gesture-driven scrubbing for free.

### How It Works Internally
* A **MotionScene** declares a `<Transition>` between a `start` and an `end` `<ConstraintSet>`.
* Progress runs 0.0 → 1.0. MotionLayout interpolates every differing constraint between the two sets.
* `<OnSwipe>` binds progress to a drag, so the animation follows the finger and can be reversed mid-gesture.
* `<KeyFrameSet>` inserts intermediate states — a `KeyPosition` bends the motion path, a `KeyAttribute` changes a property (alpha, scale, rotation) at a given progress point.

### Code Example
```xml
<!-- res/layout/activity_profile.xml -->
<androidx.constraintlayout.motion.widget.MotionLayout
    android:id="@+id/motion_root"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutDescription="@xml/scene_profile">

    <ImageView
        android:id="@+id/avatar"
        android:layout_width="120dp"
        android:layout_height="120dp"
        android:src="@drawable/avatar" />

    <TextView
        android:id="@+id/name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/user_name" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/list"
        android:layout_width="match_parent"
        android:layout_height="0dp" />
</androidx.constraintlayout.motion.widget.MotionLayout>
```

```xml
<!-- res/xml/scene_profile.xml -->
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Transition
        app:constraintSetStart="@+id/expanded"
        app:constraintSetEnd="@+id/collapsed"
        app:duration="300">

        <!-- Binds progress to the RecyclerView's scroll: the header collapses as the list scrolls -->
        <OnSwipe
            app:touchAnchorId="@+id/list"
            app:touchAnchorSide="top"
            app:dragDirection="dragUp" />

        <KeyFrameSet>
            <!-- Fade the name out over the FIRST HALF of the transition only -->
            <KeyAttribute
                app:framePosition="50"
                app:motionTarget="@+id/name"
                android:alpha="0.0" />
            <!-- Bend the avatar's path so it arcs rather than moving in a straight line -->
            <KeyPosition
                app:framePosition="50"
                app:motionTarget="@+id/avatar"
                app:percentX="0.7"
                app:keyPositionType="parentRelative" />
        </KeyFrameSet>
    </Transition>

    <ConstraintSet android:id="@+id/expanded">
        <Constraint
            android:id="@+id/avatar"
            android:layout_width="120dp"
            android:layout_height="120dp"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />
    </ConstraintSet>

    <ConstraintSet android:id="@+id/collapsed">
        <Constraint
            android:id="@+id/avatar"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:layout_marginStart="16dp"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="parent" />
    </ConstraintSet>
</MotionScene>
```

```kotlin
// Driving and observing the transition from code
binding.motionRoot.transitionToEnd()

binding.motionRoot.setTransitionListener(object : MotionLayout.TransitionListener {
    override fun onTransitionChange(l: MotionLayout?, start: Int, end: Int, progress: Float) {
        // Sync something outside MotionLayout, e.g. the status bar icon colour
        window.statusBarColorForProgress(progress)
    }
    override fun onTransitionCompleted(l: MotionLayout?, currentId: Int) {}
    override fun onTransitionStarted(l: MotionLayout?, s: Int, e: Int) {}
    override fun onTransitionTrigger(l: MotionLayout?, id: Int, pos: Boolean, prog: Float) {}
})
```

### Common Pitfalls
* **Constraining children in the layout file.** MotionLayout takes constraints from the MotionScene; any constraint in the layout XML is overwritten. Declare only sizes and content there.
* **Views appearing at (0,0).** Every animated view must be constrained in **both** ConstraintSets.
* **Nesting MotionLayout inside a scrolling parent** without `app:motionInterpolator` and correct nested-scroll setup — the two fight over the gesture.
* **Reaching for MotionLayout in a Compose codebase.** Compose has no MotionLayout equivalent worth adopting; use `Transition` and `nestedScroll` instead.

---

# 10. Navigation Graphs and Safe Args

## 10.1 XML Navigation Graphs

### Definition
* **Navigation graph** — an XML resource declaring every destination in an app and the connections between them, so routing is described in one reviewable file.
* **Destination** — a screen in the graph: a fragment, an activity, or a dialog.
* **Action** — a named connection from one destination to another, carrying its own animations and back-stack behavior.
* **Argument** — a typed value a destination requires, declared in the graph so **Safe Args** can generate classes that make a missing or mistyped argument a build error.
* **Nested graph** — a sub-graph representing a self-contained flow. It gets its own `ViewModelStore`, so a ViewModel scoped to it is cleared when the flow is popped.
* **`popUpTo`** — the attribute controlling what is removed from the back stack when navigating, which is how you prevent Back returning into a completed login flow.

### Why It Is Used
It replaces scattered `FragmentTransaction` calls with one visual, reviewable description of the app's structure, and generates type-safe argument classes so a missing or mistyped argument is a build failure.

### How It Works Internally
The Safe Args Gradle plugin reads the graph and generates a `<Destination>Directions` class per source destination (one method per action) and a `<Destination>Args` class per destination with arguments. `NavController` executes the action, applies the pop behavior, and puts arguments into the destination's `Bundle` — which `SavedStateHandle` then exposes to the ViewModel.

### Code Example
```xml
<!-- res/navigation/nav_graph.xml -->
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/nav_graph"
    app:startDestination="@id/productListFragment">

    <fragment
        android:id="@+id/productListFragment"
        android:name="com.example.ui.ProductListFragment"
        android:label="@string/products">

        <action
            android:id="@+id/action_list_to_detail"
            app:destination="@id/productDetailFragment"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left" />
    </fragment>

    <fragment
        android:id="@+id/productDetailFragment"
        android:name="com.example.ui.ProductDetailFragment">

        <argument
            android:name="productId"
            app:argType="long" />                    <!-- Required: no defaultValue -->
        <argument
            android:name="referrer"
            app:argType="string"
            app:nullable="true"
            android:defaultValue="@null" />          <!-- Optional -->

        <!-- Deep link: an https URL routes straight to this destination -->
        <deepLink app:uri="https://example.com/product/{productId}" />
    </fragment>

    <!-- A nested graph gets its own ViewModel scope for the whole flow -->
    <navigation
        android:id="@+id/checkout_graph"
        app:startDestination="@id/cartFragment">
        <fragment android:id="@+id/cartFragment"    android:name="com.example.ui.CartFragment" />
        <fragment android:id="@+id/paymentFragment" android:name="com.example.ui.PaymentFragment" />
    </navigation>
</navigation>
```

```kotlin
// Navigating with generated, type-safe directions
val direction = ProductListFragmentDirections
    .actionListToDetail(productId = product.id, referrer = "list")
findNavController().navigate(direction)

// Reading arguments — generated, so a signature change breaks the build
class ProductDetailFragment : Fragment(R.layout.fragment_product_detail) {
    private val args: ProductDetailFragmentArgs by navArgs()

    // The same arguments reach the ViewModel through SavedStateHandle,
    // which is why they survive process death.
    private val viewModel: ProductDetailViewModel by viewModels()
}

@HiltViewModel
class ProductDetailViewModel @Inject constructor(
    savedState: SavedStateHandle
) : ViewModel() {
    private val productId: Long = checkNotNull(savedState["productId"])
}
```

```kotlin
// A ViewModel scoped to a nested graph: shared across the whole checkout flow,
// and cleared automatically when the flow is popped.
class PaymentFragment : Fragment() {
    private val checkoutViewModel: CheckoutViewModel by navGraphViewModels(R.id.checkout_graph) {
        defaultViewModelProviderFactory
    }
}
```

```gradle
plugins { id("androidx.navigation.safeargs.kotlin") }
```

### Common Pitfalls
* **Passing a `Parcelable` model as an argument.** The Bundle goes through a Binder transaction with a ~1 MB cap. Pass an ID.
* **Double navigation on a fast double tap.** Throws `IllegalArgumentException: navigation destination is unknown`. Guard by checking `currentDestination`.
* **Forgetting `app:popUpTo`.** Back from Home returns to the login screen.
* **Holding `NavController` in a ViewModel.** It leaks the Activity; emit navigation events instead.

---

# 11. Dark Theme, Theme Overlays, and Resource Qualifiers

## 11.1 Dark Theme

### Definition
* **Dark theme** — a second set of colours the system selects when the user prefers a dark interface, resolved through the `values-night` qualifier rather than through conditional code.
* **`DayNight` theme** — a parent theme that already defines both variants, so your theme only overrides what differs.
* **`AppCompatDelegate.setDefaultNightMode`** — the API selecting light, dark, or follow-system for the whole app, and where a user-chosen preference is applied.
* **The one rule that makes it work** — layouts and drawables must reference **theme attributes** (`?attr/colorSurface`), never fixed colour resources (`@color/white`), because only an attribute can resolve differently per theme.

### Why It Is Used
It is a strong user expectation, it reduces battery use on OLED, and it is a Play Store quality signal. Doing it through theme attributes rather than conditional code means one resource fork instead of branching everywhere.

### How It Works Internally
`AppCompatDelegate` sets the UI mode; `Resources` then resolves `values-night/` for colors and themes. The critical rule is that layouts and drawables reference **theme attributes** (`?attr/colorSurface`) rather than raw color resources (`@color/white`) — the attribute resolves differently per theme, the raw color does not.

### Code Example
```xml
<!-- res/values/themes.xml -->
<style name="Theme.App" parent="Theme.Material3.DayNight.NoActionBar">
    <item name="colorPrimary">@color/brand_purple</item>
    <item name="colorSurface">@color/surface_light</item>
    <item name="colorOnSurface">@color/on_surface_light</item>
    <!-- A custom attribute, declared in attrs.xml, for a value Material does not define -->
    <item name="chartGridColor">@color/grid_light</item>
</style>

<!-- res/values-night/themes.xml — only the values that DIFFER -->
<style name="Theme.App" parent="Theme.Material3.DayNight.NoActionBar">
    <item name="colorPrimary">@color/brand_purple_light</item>
    <item name="colorSurface">@color/surface_dark</item>
    <item name="colorOnSurface">@color/on_surface_dark</item>
    <item name="chartGridColor">@color/grid_dark</item>
</style>
```

```xml
<!-- res/values/attrs.xml -->
<resources>
    <attr name="chartGridColor" format="color" />
</resources>
```

```xml
<!-- Layouts must reference ATTRIBUTES, not colors -->
<TextView
    android:background="?attr/colorSurface"
    android:textColor="?attr/colorOnSurface" />

<!-- WRONG: fixed in both themes -->
<!-- <TextView android:textColor="@color/black" /> -->
```

```xml
<!-- ThemeOverlay: change part of the theme for one subtree without a whole new theme -->
<style name="ThemeOverlay.App.InverseHeader" parent="">
    <item name="colorSurface">?attr/colorPrimary</item>
    <item name="colorOnSurface">?attr/colorOnPrimary</item>
</style>

<LinearLayout
    android:theme="@style/ThemeOverlay.App.InverseHeader"
    android:background="?attr/colorSurface">
    <!-- Children inside resolve colorSurface to the PRIMARY colour -->
</LinearLayout>
```

```kotlin
// User-selectable theme, persisted, applied without recreating the Activity manually
enum class ThemeMode(val delegateValue: Int) {
    SYSTEM(AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM),
    LIGHT(AppCompatDelegate.MODE_NIGHT_NO),
    DARK(AppCompatDelegate.MODE_NIGHT_YES)
}

fun applyTheme(mode: ThemeMode) = AppCompatDelegate.setDefaultNightMode(mode.delegateValue)

// Apply the persisted value in Application.onCreate, before any Activity is created,
// or the app flashes the wrong theme on cold start.
```

## 11.2 Resource Qualifier Resolution

### Definition
* **Qualifier** — a suffix on a resource directory declaring the device or window configuration it applies to (`values-es`, `layout-land`, `drawable-xxhdpi`, `values-night`).
* **Resolution** — the process by which the system picks the best-matching directory for the current configuration at the moment the resource is requested.
* **Precedence** — qualifiers are evaluated in a **fixed order** (locale, then layout direction, then size, then orientation, then night mode, then density, then platform version). The first qualifier that eliminates candidates decides.
* **`sw` vs `w`** — `sw600dp` describes the **device's smallest dimension** and never changes at runtime; `w600dp` describes the **current window** and does change in split-screen and on foldables.
* **Default directory** — the unqualified one, which must always exist as the fallback.

### How It Works Internally
Qualifiers are evaluated in a defined priority: MCC/MNC → locale → layout direction → smallest width → available width/height → screen size → orientation → UI mode → night mode → density → touchscreen → keyboard → navigation → platform version. The first qualifier that eliminates candidates decides; ties fall through to the next.

| Qualifier | Directory | Selected When |
|---|---|---|
| Language + region | `values-es-rMX/` | Locale is Spanish (Mexico) |
| Layout direction | `layout-ldrtl/` | RTL locale |
| Smallest width | `layout-sw600dp/` | Smallest screen dimension ≥ 600 dp |
| Available width | `layout-w840dp/` | Current window width ≥ 840 dp — **reacts to split-screen** |
| Orientation | `layout-land/` | Landscape |
| Night mode | `values-night/` | Dark theme active |
| Density | `drawable-xxhdpi/` | ~480 dpi |
| API level | `values-v31/` | API 31 or higher |

**`sw600dp` vs `w600dp`:** `sw` describes the *device's* smallest dimension and never changes at runtime. `w` describes the *current window* and does change in split-screen and on foldables — which is why `w` qualifiers are the correct choice for adaptive layouts.

### Common Pitfalls
* **Hardcoding `@color/white` in layouts.** Dark mode then has no effect on those views.
* **Forking whole `values-night/themes.xml` files.** Redefine only what differs; duplicated values drift.
* **`sw600dp` for adaptive layouts.** It ignores split-screen. Use `w600dp`/`w840dp`.
* **Not calling `setDefaultNightMode` before the first Activity.** Produces a visible theme flash on launch.
* **Density-specific drawables for icons.** Use a single vector.

---

# 12. UI, Views & XML Interview Questions (30 Questions)

> Core topics: Rendering pipeline, RecyclerView internals & optimizations, custom views, touch dispatch, drawables, styles vs themes, MotionLayout, and ViewBinding.
> Difficulty: `[Junior]` `[Mid]` `[Senior]`

---

### Q1. Describe the three passes of the View rendering pipeline. `[Junior]`

**Answer**
1. **Measure** — each parent asks each child how big it wants to be, passing a `MeasureSpec` (mode + size). The child calls `setMeasuredDimension`.
2. **Layout** — each parent assigns final positions to children via `layout(l, t, r, b)`.
3. **Draw** — each view records drawing commands into a `DisplayList`, which `RenderThread` replays on the GPU.

**Follow-up:** *Why can a single layout pass measure a child more than once?*
> `RelativeLayout` measures twice by design (once to resolve dependencies, once with final constraints), and `LinearLayout` with `layout_weight` measures twice to distribute remaining space. Nesting these multiplies: two levels of weighted LinearLayout is four measure passes per child.

---

### Q2. Explain `MeasureSpec` modes. `[Mid]`

**Answer**
A `MeasureSpec` is a packed int: 2 bits of mode, 30 bits of size.

| Mode | Meaning | Produced by |
|---|---|---|
| `EXACTLY` | This dimension is fixed | `match_parent` or a fixed dp |
| `AT_MOST` | Anything up to this bound | `wrap_content` |
| `UNSPECIFIED` | No constraint | `ScrollView` measuring its child |

```kotlin
override fun onMeasure(widthSpec: Int, heightSpec: Int) {
    val desired = (96 * resources.displayMetrics.density).toInt()
    // resolveSize honors the parent's constraint against our preference
    setMeasuredDimension(resolveSize(desired, widthSpec), resolveSize(desired, heightSpec))
}
```

**Follow-up:** *What happens if you ignore the spec and call `setMeasuredDimension(500, 500)` unconditionally?*
> The view claims 500px regardless of what the parent allowed, so it gets clipped, overlaps siblings, or breaks the parent's layout. Lint does not catch it; it just looks broken on some screen sizes.

---

### Q3. `invalidate()` vs `requestLayout()`. `[Junior]`

**Answer**
`invalidate()` marks the view dirty and schedules a **draw** pass only — cheap. `requestLayout()` marks the view and every ancestor up to the root as needing **measure and layout**, then draw — expensive.

Change a color → `invalidate()`. Change a size or add a child → `requestLayout()`.

**Follow-up:** *Why does calling `requestLayout()` in `onDraw` cause a problem?*
> It schedules a layout pass from inside the draw pass, so the next frame re-measures the whole tree. Doing it every frame means the view hierarchy is never stable and the app drops frames continuously.

---

### Q4. How does RecyclerView recycle views? `[Mid]`

**Answer**
Four cache tiers, from cheapest to most expensive:
1. **Attached/Changed Scrap** — views detached during the current layout pass; reattached with no rebind.
2. **Cache (`mCachedViews`, default 2)** — recently scrolled-off views, reused **without** rebinding if the same position returns.
3. **ViewCacheExtension** — an optional custom tier.
4. **RecycledViewPool** — keyed by `viewType`; views from here **must** be rebound via `onBindViewHolder`.

**Follow-up:** *Why does increasing `setItemViewCacheSize` help a scroll-back-and-forth pattern but not a one-directional scroll?*
> The cache reuses views without rebinding only for the *same* position. Scrolling one direction always hits new positions, so views come from the pool and are rebound anyway; the larger cache just holds more memory for nothing.

---

### Q5. `RecyclerView.Adapter` vs `ListAdapter`. `[Mid]`

**Answer**
`RecyclerView.Adapter` requires manual `notify*` calls; `notifyDataSetChanged()` rebinds everything and loses animations. `ListAdapter` wraps `AsyncListDiffer`, computes a diff on a background thread with `DiffUtil`, and dispatches minimal granular updates.

```kotlin
class UserAdapter : ListAdapter<User, UserViewHolder>(Diff) {
    object Diff : DiffUtil.ItemCallback<User>() {
        // Identity: is this the same logical item?
        override fun areItemsTheSame(a: User, b: User) = a.id == b.id
        // Contents: does it need rebinding?
        override fun areContentsTheSame(a: User, b: User) = a == b
    }
}
```

**Follow-up:** *`areContentsTheSame` returns `true` but the row still shows stale data. What went wrong?*
> The model is not a `data class`, or it contains a mutable field not included in `equals`. `DiffUtil` trusts `equals` completely, so a model mutated in place looks unchanged.

---

### Q6. A RecyclerView scrolls with visible stutter. How do you diagnose and fix it? `[Senior]`

**Answer**
Diagnose first: capture a Perfetto trace and look at the `RV OnBindView` slices on the main thread. Common causes in order of frequency:
1. **Work in `onBindViewHolder`** — date formatting, string building, database or file access. Move it into the model, precomputed off the main thread.
2. **Nested layouts in the item** — flatten to `ConstraintLayout`.
3. **`notifyDataSetChanged()`** — switch to `ListAdapter`.
4. **Image decoding on the main thread** — use Coil/Glide, which downsample and decode off-thread.
5. **`wrap_content` on the RecyclerView itself** — forces it to measure all children. Give it a fixed size and `setHasFixedSize(true)`.

**Follow-up:** *What does `setHasFixedSize(true)` actually do?*
> It tells RecyclerView that adapter content changes cannot change the RecyclerView's own size, so an item insert or removal skips a full `requestLayout` up the tree. It is about the RecyclerView's dimensions, not the items'.

---

### Q7. Compare LinearLayout, RelativeLayout, FrameLayout, and ConstraintLayout. `[Junior]`

**Answer**

| Layout | Cost | Use for |
|---|---|---|
| `FrameLayout` | Very low | Single child, overlays, fragment containers |
| `LinearLayout` | Low, **high with weights** | Simple rows/columns |
| `RelativeLayout` | High — double measure | Legacy; superseded |
| `ConstraintLayout` | Low, flat hierarchy | Complex responsive UI |

**Follow-up:** *Is ConstraintLayout always faster than LinearLayout?*
> No. For a simple vertical stack of three views, `LinearLayout` is faster — the constraint solver has overhead. ConstraintLayout wins when it removes nesting, which is where the real cost lives.

---

### Q8. What are Barriers, Guidelines, Groups, and Flow in ConstraintLayout? `[Mid]`

**Answer**
All are zero-size `View`s that contribute constraints without drawing:
* **Guideline** — a virtual line at a fixed dp or percent of the parent.
* **Barrier** — a virtual line at the extreme edge of several referenced views; it moves to whichever is longest. This is how a label/value layout survives translation.
* **Group** — toggles visibility of several views at once.
* **Flow** — a virtual chain that wraps, replacing a nested layout for tag rows.

**Follow-up:** *Why is a Barrier better than a fixed-width label column?*
> A fixed width is tuned to the English string. German is often 30% longer and Arabic wraps differently, so the fixed column either clips or wastes space. A Barrier adapts per locale automatically.

---

### Q9. What does `0dp` mean in ConstraintLayout? `[Junior]`

**Answer**
`0dp` means "match constraints" — the view expands to fill the space between its start and end constraints. `match_parent` is not supported inside ConstraintLayout and behaves unpredictably.

**Follow-up:** *A view with `0dp` width collapses to nothing. Why?*
> It has a constraint on only one side. "Match constraints" needs both ends anchored; with one, there is no span to fill.

---

### Q10. Explain the custom view lifecycle and where to do what. `[Mid]`

**Answer**
* **Constructors** — read custom attributes via `obtainStyledAttributes`; recycle the `TypedArray`.
* `onAttachedToWindow` / `onDetachedFromWindow` — start/stop animations, register/unregister listeners.
* `onMeasure` — resolve size against the spec.
* `onSizeChanged` — recompute bounds-dependent geometry. This is where `RectF`s belong, not in `onDraw`.
* `onLayout` — position children (`ViewGroup` only).
* `onDraw` — issue drawing commands. **Allocate nothing here.**
* `onSaveInstanceState` / `onRestoreInstanceState` — persist your own properties.

**Follow-up:** *Why must you recycle a `TypedArray`?*
> It comes from a shared pool. Not recycling exhausts the pool over time and leaks the underlying data. Kotlin's `.use { }` extension handles it.

---

### Q11. Why must you never allocate in `onDraw`? `[Junior]`

**Answer**
`onDraw` runs up to 120 times per second per view. A `Paint`, `Path`, `Rect`, or even a string concatenation allocated there produces continuous garbage, triggering frequent GC and visible stutter. Lint flags it as `DrawAllocation`.

```kotlin
// Correct: allocate once as a field
private val paint = Paint(Paint.ANTI_ALIAS_FLAG)
private val bounds = RectF()

override fun onSizeChanged(w: Int, h: Int, ow: Int, oh: Int) { bounds.set(0f, 0f, w.toFloat(), h.toFloat()) }
override fun onDraw(canvas: Canvas) { canvas.drawOval(bounds, paint) }
```

**Follow-up:** *ART's GC is concurrent. Why does allocation still cost frames?*
> High allocation rates force more frequent collections, read barriers add per-access cost, and exhausting the heap triggers a blocking allocation-failure GC. Concurrent means mostly pause-free, not free.

---

### Q12. Explain touch event dispatch through a ViewGroup. `[Senior]`

**Answer**
`dispatchTouchEvent` → `onInterceptTouchEvent` → child's `dispatchTouchEvent` → child's `onTouchEvent`, bubbling back up if unconsumed.

Three rules explain nearly every dispatch bug:
1. **`ACTION_DOWN` decides ownership.** Whoever returns `true` from `onTouchEvent` for DOWN gets every subsequent MOVE and UP.
2. **Interception is one-way.** Once a parent intercepts mid-gesture, the child gets `ACTION_CANCEL` and is out.
3. **`requestDisallowInterceptTouchEvent(true)`** is the child's veto against ancestors for the rest of the gesture.

**Follow-up:** *A horizontal RecyclerView inside a ViewPager2 does not scroll horizontally. Fix?*
> On `ACTION_DOWN` in the inner list, call `parent.requestDisallowInterceptTouchEvent(true)` so the pager stops intercepting. ViewPager2 also offers a `NestedScrollableHost` wrapper that implements this correctly.

---

### Q13. Why should you never intercept `ACTION_DOWN`? `[Mid]`

**Answer**
Returning `true` from `onInterceptTouchEvent` for DOWN means children never receive the gesture at all — buttons stop responding entirely. Interception should begin at `ACTION_MOVE`, once the gesture's direction reveals whether the parent should claim it.

```kotlin
override fun onInterceptTouchEvent(ev: MotionEvent): Boolean = when (ev.actionMasked) {
    MotionEvent.ACTION_DOWN -> { downY = ev.y; false }               // Never intercept DOWN
    MotionEvent.ACTION_MOVE -> abs(ev.y - downY) > touchSlop         // Claim once clearly vertical
    else -> false
}
```

**Follow-up:** *Why use `ViewConfiguration.get(context).scaledTouchSlop` instead of a constant?*
> It is density-aware and respects accessibility settings. A hardcoded 20px threshold feels twitchy on a low-density screen and unresponsive on a high-density one.

---

### Q14. What is `ACTION_CANCEL` and why must you handle it? `[Mid]`

**Answer**
It is delivered to a view whose gesture was stolen by an ancestor. If you only handle `ACTION_UP` for cleanup, a cancelled gesture leaves the view in a stuck state — still pressed, still dragging, animation half-run.

**Follow-up:** *Where does `ACTION_CANCEL` most commonly appear in a normal app?*
> Any clickable item inside a scrolling container. Pressing a list row then scrolling delivers CANCEL to the row so it does not fire its click and does not stay highlighted.

---

### Q15. Explain `Choreographer` and the frame budget. `[Senior]`

**Answer**
`Choreographer` receives a VSYNC pulse from `SurfaceFlinger` and runs callbacks in a fixed order per frame: **INPUT → ANIMATION → TRAVERSAL (measure/layout/draw) → COMMIT**. The budget is 16.6 ms at 60 Hz, 8.3 ms at 120 Hz.

Missing the deadline means the previous frame is shown again — visible jank.

**Follow-up:** *How do you measure jank objectively?*
> `adb shell dumpsys gfxinfo <pkg> framestats` for per-frame timings, `JankStats` (androidx.metrics) in-app with contextual state attribution, and `FrameTimingMetric` in Macrobenchmark for CI regression detection.

---

### Q16. What is overdraw and how do you find it? `[Mid]`

**Answer**
Overdraw is painting the same pixel multiple times in one frame — a window background, under an opaque layout background, under an opaque card. GPU fill rate is finite, so heavy overdraw costs frames.

Find it with Developer Options → **Debug GPU Overdraw**: blue is 1×, green 2×, light red 3×, dark red 4×+. Aim for at most 2× on most of the screen.

**Follow-up:** *What is the single most common fix?*
> Removing the window background when the app's root layout already paints an opaque background: `<item name="android:windowBackground">@null</item>`, or `getWindow().setBackgroundDrawable(null)` after the first frame.

---

### Q17. `include`, `merge`, and `ViewStub` — what does each do? `[Mid]`

**Answer**
* **`<include>`** — inserts another layout file; the reused layout's root becomes a real view.
* **`<merge>`** — used as the root of an included layout to avoid adding a redundant wrapper view when the parent already provides one.
* **`<ViewStub>`** — a zero-size, invisible placeholder that inflates its layout only when made visible. Perfect for error states, empty states, and rarely-shown sections.

```xml
<ViewStub
    android:id="@+id/error_stub"
    android:layout="@layout/view_error"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```
```kotlin
// Inflates on first use only; the stub then removes itself from the hierarchy
binding.errorStub.inflate().findViewById<TextView>(R.id.message).text = error
```

**Follow-up:** *What is the gotcha with `ViewStub`?*
> It can be inflated only once, and after inflation the stub's ID no longer exists in the hierarchy — `findViewById(R.id.error_stub)` returns null. Keep the reference returned by `inflate()`.

---

### Q18. How does `LayoutInflater` work and why is inflation expensive? `[Mid]`

**Answer**
It parses the compiled binary XML, and for each tag **reflectively** instantiates the View class via its `(Context, AttributeSet)` constructor, then parses and applies every attribute. Reflection plus attribute resolution plus object allocation, per view, per inflation.

Mitigations: flatten the hierarchy, use `ViewStub` for conditional content, use `AsyncLayoutInflater` for very large layouts, and use a `RecyclerView` so inflation is amortized across scrolling.

**Follow-up:** *What does `attachToRoot` do in `inflate(resource, root, attachToRoot)`?*
> `true` adds the inflated view to `root` immediately and returns `root`. `false` returns the inflated view without attaching, but still uses `root` to resolve `layout_*` params. Passing `null` as root discards all `layout_*` attributes — the classic bug where a RecyclerView item's margins silently disappear.

---

### Q19. Styles vs themes. `[Junior]`

**Answer**
A **style** is a set of attributes applied to a **single view** (`style="@style/PrimaryButton"`). A **theme** is applied to an Activity or a view subtree and supplies values that anything inside can reference with `?attr/`.

A theme's values are inherited down the hierarchy; a style's are not.

**Follow-up:** *What is a `ThemeOverlay` for?*
> Changing part of a theme for one subtree without defining a whole new theme — for example making a header render on the primary color by remapping `colorSurface` to `colorPrimary` for that subtree only.

---

### Q20. How does dark theme work, and what is the one rule that makes it work? `[Junior]`

**Answer**
Provide `values-night/` overrides for colors and theme attributes; the system selects them based on `AppCompatDelegate.setDefaultNightMode` or the system setting.

The one rule: **reference theme attributes, not raw colors**, in layouts and drawables.
```xml
<TextView android:textColor="?attr/colorOnSurface" />   <!-- Adapts -->
<!-- <TextView android:textColor="@color/black" />          Never adapts -->
```

**Follow-up:** *The app flashes the light theme for a moment on cold start in dark mode. Fix?*
> Call `AppCompatDelegate.setDefaultNightMode` with the persisted value in `Application.onCreate`, before any Activity is created, and make sure `windowBackground` in the theme itself is night-aware.

---

### Q21. Explain resource qualifier precedence. `[Mid]`

**Answer**
Qualifiers are evaluated in a fixed priority order: MCC/MNC → locale → layout direction → smallest width → available width/height → screen size → orientation → UI mode → **night mode** → density → touchscreen → keyboard → navigation → platform version. The first qualifier that eliminates candidates decides.

**Follow-up:** *`sw600dp` vs `w600dp` — which one for adaptive layouts, and why?*
> `w600dp`. `sw` describes the **device's** smallest dimension and never changes at runtime, so it ignores split-screen and foldable resizing. `w` describes the **current window** and reacts correctly.

---

### Q22. What drawable types exist and when do you use each? `[Mid]`

**Answer**

| Type | Use |
|---|---|
| `<vector>` | Icons — one asset, all densities |
| `<shape>` | Backgrounds, dividers, chips |
| `<layer-list>` | Stacking without extra views |
| `<selector>` | Pressed/checked/disabled states — order matters, first match wins |
| `.9.png` | Stretchable raster (chat bubbles) |
| `<animated-vector>` | Icon morphs |
| `<ripple>` | Touch feedback |

**Follow-up:** *You tint a drawable and every other view using the same resource turns that color. Why?*
> Drawables loaded from the same resource share a `ConstantState`. Call `.mutate()` before changing state to get a private copy.

---

### Q23. What is a `<selector>` and what is the ordering trap? `[Junior]`

**Answer**
A state-list drawable picks a drawable based on view state. The system uses the **first** `<item>` whose conditions all match, so the default (no state qualifiers) must be **last**.

```xml
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="false" android:drawable="@drawable/btn_disabled" />
    <item android:state_pressed="true"  android:drawable="@drawable/btn_pressed" />
    <item android:drawable="@drawable/btn_normal" />   <!-- Default LAST -->
</selector>
```

**Follow-up:** *What happens if the default is first?*
> It matches unconditionally, so the pressed and disabled states are never reached and the button never changes appearance.

---

### Q24. `android:gravity` vs `android:layout_gravity`. `[Junior]`

**Answer**
`android:gravity` positions a view's **content** within itself (text inside a TextView). `android:layout_gravity` positions the **view itself** within its parent, and it only works in parents that support it (`LinearLayout`, `FrameLayout`).

**Follow-up:** *`layout_gravity="center"` has no effect inside a ConstraintLayout. Why?*
> ConstraintLayout does not honor `layout_gravity` — positioning is expressed entirely through constraints. Center a view by constraining it to both opposite sides of the parent.

---

### Q25. ViewBinding vs DataBinding vs findViewById. `[Junior]`

**Answer**

| | findViewById | ViewBinding | DataBinding |
|---|---|---|---|
| Null-safe | No | Yes | Yes |
| Type-safe | No | Yes | Yes |
| Build cost | None | Negligible | Significant (KSP/kapt) |
| Logic in XML | No | No | Yes |

ViewBinding is the current recommendation; DataBinding only for existing code.

**Follow-up:** *What is the one thing you must remember with ViewBinding in a Fragment?*
> Null the binding in `onDestroyView`. The Fragment instance survives on the back stack, and a retained binding keeps the whole destroyed view hierarchy alive.

---

### Q26. What is two-way data binding and what is the risk? `[Senior]`

**Answer**
`@={}` binds in both directions: the view updates when the data changes, and the data updates when the user edits the view. `@{}` is one-way.

```xml
<EditText android:text="@={viewModel.email}" />
```

The risk is an infinite loop — the view writes the data, which writes the view, which writes the data. The framework guards against the trivial case by comparing values, but a formatter or a validator in between can defeat that comparison and loop.

**Follow-up:** *Why do modern codebases avoid two-way binding?*
> It is bidirectional data flow, which is exactly what UDF exists to eliminate. It also puts logic in XML, which is untestable and invisible to code search. Compose's `TextFieldState` or an explicit `onValueChange` gives the same ergonomics with one-way flow.

---

### Q27. What is MotionLayout and when would you not use it? `[Mid]`

**Answer**
MotionLayout animates between two `ConstraintSet`s described in a MotionScene, with progress optionally driven directly by a touch gesture (`<OnSwipe>`). It replaces hundreds of lines of coordinated animator code for things like collapsing toolbars.

Do not use it if: the app is Compose-based (use `Transition` and `nestedScroll` instead), the animation is a single property (a plain `ValueAnimator` is simpler), or the views are inside a scrolling parent that will fight over the gesture.

**Follow-up:** *Views vanish to the top-left when the transition starts. Why?*
> Every animated view must be constrained in **both** ConstraintSets. Constraints in the layout XML are overwritten by the MotionScene, so a view missing from one set has no position there.

---

### Q28. Explain the Navigation Component's back-stack model. `[Mid]`

**Answer**
`NavController` owns a stack of `NavBackStackEntry`s. Each entry is itself a `LifecycleOwner`, `ViewModelStoreOwner`, and `SavedStateRegistryOwner` — which is why a graph-scoped ViewModel is cleared exactly when that entry is popped.

```kotlin
navController.navigate(Home) {
    popUpTo(Login) { inclusive = true }   // User cannot go back into the login flow
    launchSingleTop = true                // No duplicate destination on a double tap
}
```

**Follow-up:** *How do you return a result to the previous screen so it survives process death?*
> Write into `navController.previousBackStackEntry?.savedStateHandle` before popping, and observe it via `currentBackStackEntry?.savedStateHandle?.getStateFlow(...)`. Because it is a `SavedStateHandle`, the value survives process death.

---

### Q29. How do you support RTL languages properly? `[Mid]`

**Answer**
* Set `android:supportsRtl="true"` on `<application>`.
* Use `Start`/`End` instead of `Left`/`Right` for margins, padding, gravity, and drawables (`drawableStart`).
* Use `android:textAlignment="viewStart"` rather than `gravity="left"`.
* Never build sentences by concatenation — word order differs.
* Test with `adb shell settings put global debug.force_rtl 1` or a `@Preview(locale = "ar")`.

**Follow-up:** *What still breaks even after doing all of that?*
> Custom views drawing with hardcoded coordinates, and any layout with a fixed width tuned to the English string length. Check `layoutDirection == LAYOUT_DIRECTION_RTL` in custom drawing code.

---

### Q30. You are given a screen that scrolls badly, overdraws heavily, and takes 400 ms to inflate. Walk through your fix. `[Senior]`

**Answer**
Measure before changing anything:
1. **Perfetto trace** to see whether time goes to `inflate`, `measure`, or `draw`.
2. **Layout Inspector** for hierarchy depth; each nesting level multiplies measure cost, and weighted/relative layouts double it.
3. **GPU Overdraw** to see the fill-rate cost.

Then, in order of expected payoff:
* Flatten the hierarchy into one `ConstraintLayout` — usually the single biggest win for both inflation and measure.
* Remove redundant backgrounds; clear `windowBackground` if the root is opaque.
* Move rarely-shown sections into `ViewStub`s.
* Move any formatting or data work out of `onBindViewHolder` into precomputed model fields.
* Use `ListAdapter` so updates are granular.
* Verify with a Macrobenchmark `FrameTimingMetric` run, comparing before and after.

**Follow-up:** *Everything is fixed but the first launch is still slow. What is left?*
> JIT compilation of cold code paths. Add a Baseline Profile covering startup and the first scroll — typically 20–40% off cold start with no code change.

---

## 📚 Related Guides

| Guide | Covers |
|---|---|
| [`android.md`](./android.md) | Custom views, touch dispatch, rendering pipeline, ViewBinding, accessibility, i18n |
| [`compose.md`](./compose.md) | The declarative replacement for this entire system |
| [`architecture_patterns.md`](./architecture_patterns.md) | MVVM/MVI with the View system |
