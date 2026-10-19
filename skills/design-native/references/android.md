# Android

Material 3 Expressive, Jetpack Compose, and the Android-specific conventions. Wear OS is in `surfaces.md`.

## Material 3 Expressive

Google's research-backed update: **46+ studies, 18,000+ participants**, finding that expressive elements improved **both usability and emotional connection** rather than trading one for the other. That is an unusually strong evidence base for a design system claim, and worth citing when someone calls expressiveness decoration.

The named fundamentals: **color, shape, size, motion and containment.**

**Containment is the one to notice.** Contrasted shades and rounded containers signify grouping. This is Gestalt common region promoted to a first-class system primitive, and it is stronger than proximity or similarity alone.

Direction of travel: sharp corners and rigid layouts toward rounded shapes and fluid forms, with softened edges on buttons, cards and nav bars.

### The concrete inventory

- **35 new shapes** for decorative use – image crops, avatars, containers – with a built-in **shape-morph animation** between them. A system primitive, not an effect to hand-build.
- **A spring-based motion system in two flavors.** *Spatial springs* for anything that moves in space; they mirror real object physics and are allowed to overshoot. *Effects springs* for color and opacity; **no overshoot, because a color that overshoots looks like a bug.** Using the wrong one is the most common M3E motion mistake.
- **Fifteen new or updated components.** New: button groups, FAB menu, loading indicators, split button, toolbars. Updated: app bars, carousel, common buttons, extended FAB, FABs, icon buttons, navigation bar, navigation rail, progress indicators.
- More configurable shape options and emphasized text styles throughout.

**Timeline, for expectation-setting.** Announced May 2025, shipped to eligible Pixel devices with Android 16 QPR1 in September 2025 (Pixel 6 and newer, plus Pixel Tablet), then rolling through OEM skins. A 2026 Android app designs for a mixed field where some users have M3E and some do not, which argues for **using the components rather than pinning exact visuals**.

Cross-platform note: iOS and Android both moved toward depth, softness and expressiveness in the same cycle, after a decade of flat minimalism. That is the biggest style signal of 2025–2026.

## Modifier order is the layout model

In Compose there is no separate style layer. **Modifiers chain, and each one wraps the previous. Order is semantic, not cosmetic.**

**The canonical order is `layout → draw → interaction`:** size, padding, offset, then background, clip, border, then clickable and gestures.

- `padding().background()` leaves the padding *outside* the background. `background().padding()` fills it. Different results, same modifiers.
- **Interaction last is the one that matters.** Putting `clickable` before `padding` means the padding is not tappable, which silently shrinks the touch target below the 48 dp minimum.
- Modifier order also changes render cost. Use the **lambda versions** when passing frequently changing state.

**The touch-target rule is enforced by modifier order, not by a size property.** This is the most common way a Compose UI fails an accessibility check while looking correct.

## Color – a generated system

**Five key colors produce five tonal palettes** (accent 1–3, neutral 1–2) **× 13 tones each = 65 colors.** Generate from a brand color with Material Theme Builder.

An M3 theme is three subsystems: **color scheme, typography, shapes.** Customizing them propagates through every M3 component automatically.

**Dynamic color (Material You, Android 12+)** derives the scheme from the user's wallpaper.

| Rule | Detail |
|---|---|
| Never rely on it alone | Fallback schemes must reflect the full M3 color role model, even if minimal |
| Accessibility | The tonal palette system is what makes a scheme accessible by default – contrast is designed into the tone steps |
| **Motion warning** | Color transitions tuned against a static theme can be jarring under dynamic color. An animation that looks right against your palette may be violent against someone's wallpaper |
| API | `dynamicLightColorScheme` / `dynamicDarkColorScheme` with static fallbacks |

**The tension worth naming:** dynamic color hands palette control to the user. A brand whose identity depends on a specific accent is in direct conflict with it. Decide deliberately – adopt dynamic color and accept a variable brand, or ship a fixed scheme and accept looking less Android. Do not drift into it.

## The type scale

**15 tokens: 5 roles × 3 scales.**

| Role | Large | Medium | Small | Use |
|---|---|---|---|---|
| Display | 57sp | 45sp | 36sp | Hero |
| Headline | 32sp | 28sp | 24sp | Section headers |
| Title | 22sp | 16sp | 14sp | Subsections, cards |
| Body | 16sp | 14sp | 12sp | Main content |
| Label | 14sp | 12sp | 11sp | Small text, captions |

Each token defines size, weight, line height and letter spacing. Roboto throughout, and **only Regular and Medium weights are used.**

That last fact is worth repeating to anyone reaching for bold: a system built by Google, covering display type at 57sp, **uses no bold weight at all.** Hierarchy comes from size and role, not from weight escalation.

**Units: `sp` scales with the user's font-size setting, `dp` does not.** Type in `dp` opts out of accessibility scaling entirely – the Android equivalent of hardcoding point sizes on iOS.

## Window size classes

| Class | Width | Typical |
|---|---|---|
| **Compact** | **< 600 dp** | Phone portrait |
| **Medium** | **600–840 dp** | Large phone landscape, small tablet |
| **Expanded** | **840 dp+** | Tablet, unfolded foldable, desktop window |
| Large / Extra-Large | New in Compose Adaptive Layouts 1.2 | Rich multi-pane UI on expansive screens |

- Size classes are computed from the **window, not the device**, so they change on fold, unfold, rotation and multi-window resize. **The layout must handle that transition at runtime**, which is harder than a web breakpoint that only changes when a user drags a window.
- 1.2 adds **`reflow` and `levitate`** strategies for outer and inner display transitions on devices like the Pixel 10 Pro Fold and Galaxy Z Fold7 / Z Flip7.

Android gives named buckets rather than content-derived breakpoints. The spirit of "put the breakpoint where the content breaks" still applies: **choose which bucket triggers a genuine layout change** rather than adapting at every one. See `structure.md`.

## Accessibility – semantics as the model

The `semantics` modifier carries the meaning:

| Property | Purpose |
|---|---|
| `contentDescription` | What TalkBack reads on focus. Required for `Image` and `IconButton`; `Text` and `Button` use their own text |
| `role` | Identifies the element type |
| `mergeDescendants = true` | Makes a container a **single focusable entity** so TalkBack reads its children in order rather than stopping on each. This is the fix for a card that takes five swipes to traverse |
| `heading()` | Marks a composable as a heading, giving TalkBack users the structural navigation that headings give on the web |
| Target size | **48 × 48 dp** minimum |

**Compose renders its own canvas, so there are no native semantics to inherit. You must supply all of it.** There is no equivalent of "use the native element and get accessibility free". See `accessibility.md`.

## Material Symbols

**2,500+ icons in a single variable font**, three styles, four axes:

| Axis | Range | Rule |
|---|---|---|
| **Weight** | 100 to 700 | Match icon weight to the surrounding type weight |
| **Fill** | 0 to 1 | One icon renders unfilled and filled – the standard unselected/selected pair |
| **Optical size (`opsz`)** | **20 to 48 px**, default 24 | Use the value matching the rendered size |
| **Grade** | – | Finer than weight, small effect on apparent size. Optical correction on dark backgrounds |

Weight and `opsz` are handled automatically. **Fill and grade must be set via `font-variation-settings`.**

Both platforms make **fill the state distinction** rather than color, and both treat icons as type by giving them an optical-size axis. That is the correct model.

## Animation

**Compose uses spring as the default `AnimationSpec`** for most animations, including `animate*AsState` and `updateTransition`.

- Parameters are **`stiffness`** and **`dampingRatio`**, with named constants: `Spring.StiffnessLow` (200), `StiffnessMedium` (1500), `DampingRatioNoBouncy` (1.0), `MediumBouncy` (0.5), `HighBouncy` (0.2). Mass is fixed at 1.
- **Why it is the default:** a spring guarantees continuity of velocity when the target changes mid-animation, so it handles interruption more smoothly than a duration-based spec.
- `tween(durationMillis, easing)` covers the duration-based case. `AnimationSpec` is the common type, so both live in one API.

**On both platforms the correct instinct is to tune the spring, not reach for a duration curve.** Duration-based easing is the exception, for things that must take a known time. Parameter tables in `motion.md`.

## Navigation and predictive back

**Type-safe navigation** (Navigation Compose 2.8.0+): routes are `@Serializable` classes and objects rather than string patterns.

- For deep links the library **infers the URI pattern from the route class** – non-optional properties become path parameters, optional or defaulted ones become query parameters.
- Compile-time safety on arguments.
- Intent filter in the manifest on the `NavHost` activity; `navDeepLink` on the destination.

**Navigation 3** (stable since late 2025) goes further: the back stack is **a list you own**, and navigating means adding or removing entries. Its **Scenes** API can render multiple destinations at once, which is what finally makes list-detail on a large screen the same navigation graph rendered two ways. It also works in Compose Multiplatform. See `structure.md`.

**Predictive back** – the user swipes and **previews the destination before committing**.

- Enabled by default from **Android 15**; APIs available from Android 14.
- Design: a full-screen surface **scales down as the gesture progresses**, revealing the destination behind it.
- **Do not drive the preview from raw linear gesture progress.** Feed it through a `STANDARD_DECELERATE` token or `PathInterpolator`, or the motion feels mechanical.
- Implement with `PredictiveBackHandler` in Compose (a `Flow<BackEventCompat>` carrying progress), `OnBackPressedCallback`, or AndroidX Transitions' `controlDelayedTransition`.
- **Custom transitions must be seekable, not fire-and-forget.**

iOS has had an interactive, cancellable swipe-back since iOS 7. Android arriving at the same conclusion a decade later is a good argument that **interruptible previewing navigation is correct behavior, not a style choice**.

## Compose in 2026

Quarterly releases named by month. **August '26 is Compose 1.12** (BOM 2026.08.00, AGP 9.2.0+, compileSdk 37); April '26 was 1.11.

Design-relevant additions:

- **Mesh gradients** as a first-class API. This is the technique behind the current "aurora" and soft-blob backgrounds, and being native means it animates cheaply instead of shipping as a large image.
- **Wide color gamut** for P3 and HDR. See `foundations.md`.
- **Named areas in the experimental `Grid` layout** – `grid-template-areas` for Compose, which makes complex responsive layouts readable.
- **Rich-text formatting on `BasicTextField`** plus programmatic selection control. The foundation for any editor-shaped app.
- **`DeferredTransition` for gesture-driven animation** – interruptibility as a supported API rather than a manual build.
- **Credential Manager integration on text fields**, so passkeys and autofill work without custom wiring.

Performance: **stability inference improved with Kotlin 2.2**, so fewer unnecessary recompositions without hand-annotating `@Stable`. It reduces the stability work but does not remove it. See `performance.md`.
