# Learning log – native

Raw capture for the `design-native` skill. Native, cross-platform and Rust-GUI design. Everything goes here first, in full, with its source and its number. Consolidation happens later, from here.

Companion: `~/.claude/skills/design-web/study-book/learning.md` – 3,602 lines. Almost all of its typography, color, space, hierarchy, motion, interaction, craft, process and corpus material applies here unchanged; only the implementation layer is web-specific. Cross-references to it are written `web §N.N`.

## Capture rules

1. **A claim without a reason or a number does not go in.** Spec-mandated (cite it), measured (cite it), or reasoned from mechanism (state it). Taste is labeled as taste.
2. **Source every entry.** `[unsourced]` if it came from memory and still needs verification.
3. **Prefer the shape `Pattern | Cost | Fix`.**
4. **Contradictions are kept, not resolved on the spot.** Mark `CONFLICT`.
5. **Received wisdom that turns out wrong is the highest-value entry.** Mark `MYTH`.
6. **Trade-offs get both sides.**
7. **Verifiable beats assertable.** Record how to check it.
8. **Platform divergence is the point.** Where iOS and Android differ, record both and the reason – never collapse to an average.
9. **Note what does not transfer from web.** Where a web habit is actively wrong on native, flag it.

## Status

| # | Area | State |
|---|---|---|
| 1 | Apple platforms and HIG | complete |
| 2 | Android and Material | complete |
| 3 | SwiftUI and UIKit | complete |
| 4 | Jetpack Compose and Android UI | complete |
| 5 | Slint (Rust) | complete |
| 6 | Cross-platform toolkits | complete |
| 7 | Navigation architecture | complete |
| 8 | Adaptive layout, tablets, foldables | complete |
| 9 | Touch, reach, input and gestures | complete |
| 10 | Motion and haptics | complete |
| 11 | Native accessibility | complete |
| 12 | Typography on native | complete |
| 13 | Color and theming | complete |
| 14 | Icons, symbols and illustration | complete |
| 15 | Lifecycle, state, offline and sync | complete |
| 16 | Notifications, widgets, ambient surfaces | complete |
| 17 | Native performance | complete |
| 18 | Desktop native | complete |
| 19 | Wearables, TV, auto, spatial, embedded | complete |
| 20 | Distribution and store presence | complete |
| 21 | Native craft and platform idioms | complete |
| 22 | App category patterns | complete |
| 23 | AI features in native interfaces | complete |
| 24 | Growth, monetization and lifecycle moments | complete |
| 25 | Regulated audiences and contexts | complete |
| 26 | Diagnosing a native UI | complete |

**300 of 300 searches done.** Registers filled and cross-references verified.

Numbering note: sections 1, 2, 9 and 10 were split out of the web study and keep their content, but their subsections were renumbered into this file's scheme. A reference written `web §N.N` points at `design-web/study-book/learning.md`; a bare `§N.N` points here.

---

## 1. Apple platforms and HIG

### 1.1 iOS – Liquid Glass (iOS 26, current)

Announced WWDC June 2025; shipped across iOS 26, iPadOS 26, macOS Tahoe, tvOS 26, visionOS 26, watchOS 26. This is **the current Apple design language** and any iOS-adjacent work in 2026 has to reckon with it.

- **Core idea: "hierarchy through depth."** Importance is communicated by **transparency, refraction and visual weight** rather than by color contrast or size. That is a genuine departure from a decade of flat design.
- **Liquid Glass is a functional layer for controls and navigation that floats above the content layer.** Tab bars and sidebars let content remain visible underneath while staying clearly distinct from it.
- Three concrete changes to an existing app: how content and controls are **layered**; how navigation **behaves on scroll**; how much of the visual language the **native components handle automatically**.
- The tab bar is now **inset from the edge, in a horizontal Liquid Glass capsule** rather than a full-width bar.
- `CONFLICT / open question:` the redesign is contested – legibility of text over refracting backgrounds is the recurring criticism. Note for the skill: **glass effects put a contrast floor at risk**, so anything using them needs the contrast check run against the *actual* backdrop, not against a flat color.

`OPPORTUNITY:` translating Liquid Glass ideas to the web (backdrop-filter, layered translucency, content visible under chrome) is a live design direction in 2026 – with the legibility caveat attached.

**Adoption mechanics, which are unusual and worth knowing.** An app gets the new appearance simply by **recompiling against the iOS 26 SDK**. There is no opt-in flag and no per-view adoption. `UITabBarController`, `UISplitViewController`, navigation bars and toolbars all pick up the Liquid Glass appearance automatically.

Two consequences follow:

- **You do not implement Liquid Glass; you let the system do it.** Hand-rolling a translucent capsule to imitate it produces something that looks close on the current OS and wrong on the next. The correct move is to use standard components and stop fighting them.
- **Recompiling is a design event.** An app that was shipping a custom tab bar or a custom navigation bar now sits next to system chrome that has changed, and the mismatch is what users see. Budget a design pass for the SDK bump, not just a build.

**The scroll behavior is new and it is the part designers should plan for.** Tab bars on iPhone float above content and **minimize on scroll down, expanding again on scroll up.** That means the tab bar is no longer a fixed reserved strip: content can occupy that space and the bar returns when the user reverses. Anything the app pinned above the tab bar (a mini player, a cart summary, a status banner) should now be a **tab bar accessory view**, which the system animates in sync, rather than a separately positioned overlay that will desynchronize.

## 2. Android and Material

### 2.1 Android – Material 3 Expressive

Google's research-backed update: **46+ studies, 18,000+ participants**, finding that expressive elements improved **both usability and emotional connection** – not one at the expense of the other. That is an unusually strong evidence base for a design system claim and worth citing.

- The named fundamentals: **color, shape, size, motion and containment.**
- **Containment** is called out explicitly: contrasted shades and rounded containers to signify grouping. This is Gestalt *common region* promoted to a first-class system primitive, and it is stronger than proximity or similarity alone.
- Direction of travel: **sharp corners and rigid layouts → rounded shapes and fluid forms**; softened edges on buttons, cards and nav bars.
- Dynamic color pushed further – colors adapt more vividly across backgrounds and components.
- More designer control over theme and motion behavior, for branding.

`Cross-platform note:` iOS and Android both moved toward depth, softness and expressiveness in the same cycle, after a decade of flat minimalism. That is the single biggest style signal of 2025–2026.

**The concrete inventory, for when "expressive" needs to become specifications:**

- **35 new shapes** for decorative use – image crops, avatars, containers – with a **built-in shape-morph animation** between them. This is a system primitive, not an effect to hand-build.
- **A spring-based motion system in two flavors:** *spatial springs* for anything that moves in space (they mirror real object physics and are allowed to overshoot), and *effects springs* for color and opacity changes (no overshoot, because a color that overshoots looks like a bug). Using the wrong one is the most common M3E motion mistake.
- **Fifteen new or updated components.** New: **button groups, FAB menu, loading indicators, split button, toolbars.** Updated: app bars, carousel, common buttons, extended FAB, FABs, icon buttons, navigation bar, navigation rail, progress indicators.
- More configurable shape options and emphasized text styles across the board.

**Timeline, for expectation-setting:** announced May 2025, shipped to eligible Pixel devices with the Android 16 QPR1 update in September 2025 (Pixel 6 and newer, plus Pixel Tablet), and rolling through OEM skins after that. In practice a 2026 Android app is designing for a mixed field where some users have M3E and some do not, which argues for using the components rather than pinning exact visuals.

### 2.2 iOS vs Android – the differences that actually matter

| Concern | iOS | Android |
|---|---|---|
| Back | **No system back.** Requires a UI back button (top-left) **and** edge-swipe support | System back button/gesture. No UI back button; handle the back stack correctly, including exiting the app |
| Left-to-right edge swipe | **Go back** | **Switch tabs** – the same gesture means different things. Conflicting gestures across platforms is a real source of feeling "wrong" |
| Tab bar | Bottom of screen, icons + text | Below the top nav bar – producing three stacked bars |
| Right-side actions | Often **text** ("Edit") | Consistently **icons** |
| Depth | Historically flat; **now Liquid Glass depth** | Elevation and shadow (Material) |
| Minimum target | 44×44 pt | 48×48 dp, 8dp gap |

The design lesson: **shipping one design to both platforms produces something that feels subtly wrong on both.** Share the logic and structure, split the navigation model and the gestures.

## 3. SwiftUI and UIKit

### 3.1 The position in 2026

**SwiftUI is the default for new work.** Production-ready since iOS 17, matches UIKit on performance, has caught up on the missing primitives (charts, navigation, search, complex layout), and **is where Apple ships all new API**. For visionOS or new Mac apps it is *"the only viable choice."*

The realistic architecture: **SwiftUI-first with UIKit islands.** Both bridges are stable and officially supported – `UIViewRepresentable` / `UIViewControllerRepresentable` to embed UIKit inside SwiftUI, `UIHostingController` to embed SwiftUI inside UIKit.

Reach for UIKit for: custom view-controller transitions needing precise control, deeply customized collection-view layouts, UIKit-only third-party SDKs. *"UIKit is the specialist you call for the hard parts."*

### 3.2 The layout model – inverted from the web

> **"SwiftUI's layout system is fundamentally inverted from UIKit – you describe what you want, and views decide their own sizes, rather than telling views where to be."**

This is the single biggest conceptual shift coming from CSS. There is no box model to set; there is a **negotiation** – the parent proposes a size, the child reports what it wants, the parent places it.

| Concept | Behavior |
|---|---|
| `VStack` / `HStack` | Distribute by **intrinsic content size** unless told otherwise. Text takes its intrinsic width; `Spacer` absorbs the remainder |
| `layoutPriority` | **Does not change a view's sizing behavior** – it decides **which child the parent proposes a size to first.** Higher priority gets served before the space runs out |
| `frame` | Use flexible frames (`minWidth`/`idealWidth`/`maxWidth`), not fixed sizes – *"avoid fixed frame sizes unless working with known fixed-size elements like icons"* |
| **Size classes** | **Prefer over `GeometryReader`** – *"instead of manually checking width with GeometryReader, use environment size classes, which aligns with Apple's adaptive layout philosophy"* |

`Design consequences:`
- **`layoutPriority` is the tool for the truncation problem** (web §14.29). When two labels compete, the one that must stay whole gets the priority. This is more expressive than CSS's `flex-shrink` and is the correct place to encode "this text is the differentiator."
- **Size classes are the native equivalent of breakpoints** – but they are *coarse* (compact / regular per axis), not a continuum. Web §4.1's "breakpoints come from content" does not port cleanly; you get four buckets and must design to them.
- `GeometryReader` is the `GAP:` escape hatch, and using it is usually a sign the layout is fighting the system.

### 3.3 Liquid Glass in SwiftUI – the rule that matters

APIs: **`.glassEffect()`**, **`GlassEffectContainer`** (shared sampling region; lets nearby elements **merge visually** as they approach), and glass button styles. Recompiling with Xcode 26 makes many system elements adopt it automatically.

**The design law, and it is unambiguous:**

> **Liquid Glass belongs in the functional layer – controls, navigation, transient UI. Never in the content layer.**

And the corollary that explains most bad adoptions:

> **"Liquid Glass is a one-line API only when the app already respects the HIG layering rule."** An app that already separates functional chrome from content adopts it and feels native by default. An app that does not, does not.

`This is the native restatement of web §12.2's test:` **does the depth carry information, or is it decoration?** Apple has drawn the line at the layer boundary. Cross-reference web §12.3 – the contrast risk over refracting backdrops is real, and `GlassEffectContainer` merging makes the effective backdrop harder to predict, not easier.

### 3.4 Spring animation – named, not hand-tuned

SwiftUI ships three presets, and the naming is genuinely good design vocabulary:

| Preset | Physics | Use |
|---|---|---|
| **`.smooth`** | **Critically damped – no bounce at all** | Subtle transitions, natural deceleration without overshoot |
| **`.snappy`** | Small bounce, crisp | **Button taps, toggles** – responsive feel |
| **`.bouncy`** | Noticeable bounce | Onboarding, celebration, playful moments |

Custom: **`duration`** (time to settle) and **`bounce`** (0 = no overshoot → 1 = maximum). Two parameters, not four – far more designable than mass/stiffness/damping.

`Direct mapping to web §6.19's motion archetypes:` calm precision → `.smooth`; sharp and rhythmic → `.snappy`; playful energy → `.bouncy`. **This is the cleanest expression of "motion is brand" found in any toolkit** – choosing a default spring for the app is a brand decision expressible in one word.

Also note: springs are **interruptible and velocity-aware by construction** (web §6.4), so Apple's central motion principle is the default here rather than something to engineer.

### 3.5 SF Symbols – the system that removes a whole category of work

**9 weights** (ultralight → black), each **matching a San Francisco font weight**, so symbols align with adjacent text at any weight and size. **3 scales** (small/medium/large). **4 rendering modes**, with and without variable color. **216 possible configurations** of any symbol.

**Rendering-mode discipline, stated by Apple:**
- **Variable color communicates *change*** – progress, signal strength, volume. **Not depth.**
- **Hierarchical communicates *depth*** – elevating layers, separating foreground from background.

Custom symbols: **variable templates** mean drawing **three** weights and letting the system generate the other 24.

`Design consequence:` web §13.3's entire icon-consistency problem – one grid, one stroke weight, optical sizing, pixel alignment – **is solved by the platform**. The correct native instinct is *use the system symbol*, and the bar for a custom icon is that no symbol expresses the concept. This is the opposite of the web, where you must build the discipline yourself.

### 3.6 Dynamic Type – the native equivalent of the whole zoom chapter

**12 sizes: xSmall, Small, Medium, Large (default), xLarge, xxLarge, xxxLarge – plus AX1–AX5** when Larger Accessibility Sizes is on.

- **Use built-in text styles and Dynamic Type is free.** Hardcoded point sizes opt out of it entirely.
- Custom fonts scale via **`UIFontMetrics`**.
- The design requirements: **text must stay legible at the largest sizes without truncating or overlapping, the layout must adapt as text grows, hierarchy must survive, and interactive elements must stay reachable.**

`This is web §4.6 (reflow at 400%) and web §9.10 (magnification) as a first-class platform feature.` The practical difference: on the web, zoom scales everything; **Dynamic Type scales only text, so layouts break differently** – a fixed-height row with growing text clips rather than reflows.

**The native check to run:** set the largest AX size and walk the primary flows. It is the single highest-yield accessibility test on iOS, and the direct analogue of the 400%-zoom check.

`GAP: worth verifying` whether the AX sizes are reachable in whatever cross-platform toolkit is in use – §5 Slint, §6 toolkits – because own-canvas renderers frequently do not honor them.

### 3.7 Navigation

**`NavigationStack`** (iOS 16+, standard since) is **data-driven**: a path array of route values, enabling deep linking, programmatic control and type-safe routing that previously required third-party libraries.

- Common pattern: an **`enum` conforming to `Hashable` and `Codable`** as the route type – `Codable` specifically so paths can be persisted and restored.
- Deep link: parse the URL into an array of route values and assign it to the path; intercept with **`.onOpenURL`** at the app root.
- **`NavigationSplitView` as the root for universal apps** – multi-column on iPad and Mac, **automatically collapsing to a single-column stack on iPhone.**

`Design consequence:` the navigation *model* is a data structure, which means **the information architecture is expressible as a type.** That is a real advantage over web routing, and it makes web §5.7's "validate structure with tree testing" easier to act on – the tree is literally in the code.

### 3.8 iOS spacing and layout constants

| Value | Meaning |
|---|---|
| **16–20pt** | Standard content margin from screen edges |
| **16pt** | Form sheet padding |
| **8 / 16 / 24pt** | Spacing increments – an **8pt base**, same as web §3.1 |
| **44pt** | Minimum interactive target **and** standard list row height |

**Safe areas** keep content clear of rounded corners, sensor housings and the home indicator; **layout margins** add separation between content and the safe-area edge. They are two different things and both are needed (web §4.8).

### 3.9 Color – semantic first

- **Use Apple's semantic colors before creating custom ones**: `label`, `secondaryLabel`, `tertiaryLabel`, `quaternaryLabel`, `separator`, `systemBackground` and friends. *"Colors defined by their role rather than a fixed value – the system resolves each to the right shade for the current appearance and accessibility settings."*
- Custom colors go in an **asset-catalog Color Set with Any / Light / Dark appearance slots**, used *"from day one"*. Images too.
- **`NOTE:` the recommended contrast for custom colors in dark mode is 7:1, especially for small text** – substantially stricter than WCAG AA's 4.5:1. Worth recording as a platform convention that exceeds the web baseline.

`This is web §2.5's three-tier token architecture already built into the platform`, with the semantic layer supplied. The four-level label hierarchy (primary → quaternary) is a better-designed default than most hand-built systems achieve.

### 3.10 Building a design system in SwiftUI

- **`ViewModifier`** extracts styling into a reusable unit – *"takes a view, applies changes, returns a new modified view."*
- **`ViewBuilder`** for composable container components.
- Escalation: extract a `View` struct with parameters → `ViewModifier` for styling → **generics** for flexible containers → **`Environment`** for global values → **protocols** for shared behavior.

`Environment is the token system.` It is the native equivalent of CSS custom properties cascading down a tree, and it is the right place to put a theme.

`[sources: Apple HIG (SF Symbols, Dark Mode, layout); developer.apple.com WWDC24 "Get started with Dynamic Type", WWDC22 "Adopt Variable Color in SF Symbols"; Swift by Sundell SwiftUI layout system; defagos "Understanding SwiftUI Layout Behaviors"; SwiftUI Liquid Glass guides (atelier-socle, blakecrosley, Dimillian/Skills); GetStream swiftui-spring-animations; SwiftLee "View Composition using ViewModifiers"; NavigationStack 2026 guides; ivomynttinen iOS design guidelines]`

### 3.11 The iOS 26 SwiftUI API surface worth knowing

The named APIs, because "adopt Liquid Glass" is not actionable and these are:

- **`.glassEffect()`** applies the material to a view. **`GlassEffectContainer`** groups several glass elements so they blend and refract as one surface rather than as separate panes stacked on each other. Using the modifier without the container on a group of controls is the most common visible mistake.
- **`.scrollEdgeEffectStyle()`** controls how content fades or blurs into a toolbar or tab bar: `.soft` (the default Liquid Glass blur), `.hard` (a clean cutoff), `.automatic`. Dense UIs with many floating elements – Apple cites Calendar – usually want `.hard`, because a soft blur over dense content reads as smearing.
- **Toolbar minimization APIs** let a toolbar collapse on scroll and expand on reverse, matching the tab bar behavior (§1.1).
- **`ToolbarSpacer`** groups toolbar items into separate glass capsules instead of one continuous bar, which is how related actions are visually separated in the new design.
- **`.labelIconToTitleSpacing()`** and **`.labelReservedIconWidth()`** control label internals, which is what makes a list of mixed-width icons align properly.

`The through-line:` these are all knobs for one question – how does chrome sit over content. That is the entire iOS 26 design problem, and an app that answers it deliberately looks designed while one that takes every default looks generic.

## 4. Jetpack Compose and Android UI

### 4.1 Modifier order is the layout model

In Compose there is no separate style layer – **modifiers chain, and each one wraps the previous.** Order is semantic, not cosmetic.

**The canonical order: `layout → draw → interaction`** (size, padding, offset → background, clip, border → clickable, gestures).

- `padding().background()` and `background().padding()` produce different results: the first leaves the padding *outside* the background, the second fills it.
- **Interaction last** is the important one – putting `clickable` before `padding` means the padding is not tappable, which silently shrinks the touch target below the 48dp minimum.
- Performance: *"the order of those modifiers can make the same UI cheaper or more expensive to render."* Use the **lambda versions** of modifiers when passing frequently changing state.

`Design consequence:` the touch-target rule (web §4.4) is enforced by **modifier order**, not by a size property. This is the most common way a Compose UI fails an accessibility check while looking correct.

### 4.2 Material 3 color – a generated system

**Five key colors → five tonal palettes** (accent 1–3, neutral 1–2) **× 13 tones each = 65 colors.** Generate from a brand color with **Material Theme Builder**.

An M3 theme is three subsystems: **color scheme, typography, shapes.** Customizing them propagates automatically through every M3 component.

**Dynamic color (Material You, Android 12+)** derives the scheme from the user's wallpaper.

| Rule | Detail |
|---|---|
| **Never rely on it alone** | *"Dynamic color should never be your only strategy. Your fallback schemes should reflect Material 3's full color role model, even if minimal."* |
| Accessibility | *"The system of tonal palettes is critical to making any color scheme accessible by default"* – contrast is designed into the tone steps |
| **Motion warning** | *"Color transitions that work with static themes can create jarring effects with dynamic colors, especially when colors change dramatically."* An animation tuned against your palette may be violent against someone's wallpaper |
| API | `dynamicLightColorScheme` / `dynamicDarkColorScheme` with static fallbacks |

`This is web §2.2's tonal scale, generated and enforced by the platform` – and the tone-step-guarantees-contrast idea is the same insight as Radix (web §2.11), shipped as a system rather than a convention.

`The design tension worth naming:` dynamic color hands palette control to the user. A brand whose identity depends on a specific accent (web §15.1's single-voltage law) is in direct conflict with it. **Decide deliberately: adopt dynamic color and accept a variable brand, or ship a fixed scheme and accept looking less "Android".**

### 4.3 The M3 type scale

**15 tokens: 5 roles × 3 scales.**

| Role | Large | Medium | Small | Use |
|---|---|---|---|---|
| **Display** | 57sp | 45sp | 36sp | Hero |
| **Headline** | 32sp | 28sp | 24sp | Section headers |
| **Title** | 22sp | 16sp | 14sp | Subsections, cards |
| **Body** | 16sp | 14sp | 12sp | Main content |
| **Label** | 14sp | 12sp | 11sp | Small text, captions |

Each token defines **size, weight, line height and letter spacing**. Roboto throughout, **and only Regular and Medium weights are used.**

`That last fact is a strong independent confirmation of web §15.3:` a system built by Google, covering display type at 57sp, **uses no bold weight at all.** Hierarchy comes from size and role, not from weight escalation.

`Note the units:` **`sp` scales with the user's font-size setting; `dp` does not.** Type in `dp` opts out of accessibility scaling – the Android equivalent of hardcoding point sizes on iOS (§3.6).

### 4.4 Adaptive layout – window size classes

| Class | Width | Typical |
|---|---|---|
| **Compact** | **< 600dp** | Phone portrait |
| **Medium** | **600–840dp** | Large phone landscape, small tablet |
| **Expanded** | **840dp+** | Tablet, unfolded foldable, desktop window |
| **Large / Extra-Large** | new in **Compose Adaptive Layouts 1.2** | Rich multi-pane UI on expansive screens |

- Size classes are computed from the **window**, not the device – so they change on **fold/unfold, rotation, and multi-window resize.** The layout must handle that transition at runtime, which is a harder problem than a web breakpoint that only changes when the user drags a window.
- 1.2 beta adds **`reflow` and `levitate`** layout strategies for the outer and inner displays of devices like the Pixel 10 Pro Fold and Galaxy Z Fold7 / Z Flip7.

`Compared with web §4.1:` Android gives you **five named buckets rather than content-derived breakpoints.** The web principle ("put the breakpoint where the content breaks") does not port – but its *spirit* does: choose which bucket triggers a genuine layout change rather than adapting at every one.

### 4.5 Accessibility – semantics as the model

The `semantics` modifier carries the meaning:

- **`contentDescription`** – TalkBack reads this on focus. Required for `Image` and `IconButton`; `Text` and `Button` use their own text by default.
- **`role`** – identifies the element type.
- **`mergeDescendants = true`** – makes a container a **single focusable entity** so TalkBack reads its children in order rather than stopping on each. This is the fix for the most common Compose a11y complaint: a card that takes five swipes to traverse.
- **`heading()`** – marks a composable as a heading, giving TalkBack users the same structural navigation web users get from `<h2>` (web §8.4).
- **48×48dp** minimum touch target.

`Direct mapping to web §8:` `contentDescription` ≈ accessible name, `role` ≈ ARIA role, `heading()` ≈ heading level, `mergeDescendants` ≈ grouping. **The first rule of ARIA (web §8.3) has no equivalent** – on Compose there are no native semantics to inherit, so **you must supply all of it.** Own-canvas rendering means own-canvas accessibility.

### 4.6 Material Symbols – four axes

**2,500+ icons in a single variable font**, three styles, four axes:

| Axis | Range | Rule |
|---|---|---|
| **Weight** | 100 (thin) → 700 (bold) | **Match icon weight to the surrounding type weight** |
| **Fill** | 0 → 1 | One icon renders both unfilled and filled – the standard selected/unselected pair |
| **Optical size (`opsz`)** | **20 → 48px**, default 24 | Use the value matching the rendered size |
| **Grade** | – | Finer than weight, small effect on apparent size. Used for optical correction on dark backgrounds |

Weight and `opsz` are handled automatically; **fill and grade must be set via `font-variation-settings`.**

`Same conclusion as SF Symbols (§3.5):` both platforms solve web §13.3's icon-consistency problem at the system level, and both make **fill the state distinction** rather than color. Note also that `opsz` here is exactly the typographic optical-sizing concept from web §1.6 applied to icons – the platforms treat icons as type, which is the correct model.

### 4.7 Animation – spring by default

**Compose uses spring as the default `AnimationSpec` for most animations**, including `animate*AsState` and `updateTransition`.

- Parameters: **`stiffness`** and **`dampingRatio`** – with named constants (`Spring.StiffnessMediumLow`, `Spring.DampingRatioMediumBouncy`).
- **The reason it is the default:** *"Spring can handle interruptions more smoothly than duration-based specs because it guarantees the continuity of velocity when the target value changes mid-animation."*

`That sentence is web §6.4's interruptibility principle stated as an engineering property.` **Both major native platforms now default to springs** (§3.4 SwiftUI, §4.7 Compose) – which means the web is the outlier, and web §6.20's `linear()` spring trick is catching up to a native default rather than inventing something.

`Design consequence:` on both platforms the correct instinct is **tune the spring, do not reach for a duration curve.** Duration-based easing is the exception, for things that must take a known time.

### 4.8 Navigation and predictive back

**Type-safe navigation (Navigation Compose 2.8.0+):** routes are **`@Serializable` classes and objects** rather than string patterns.

- For deep links the library **infers the URI pattern from the route class**: non-optional properties become **path parameters**, optional or defaulted properties become **query parameters.**
- Compile-time safety on arguments.
- Intent filter in the manifest on the `NavHost` activity; `navDeepLink` on the destination.

`Same idea as SwiftUI's Codable enum routes (§3.7)` – both platforms converged on **the navigation graph as a typed data structure.**

**Predictive back** – the user swipes back and **previews the destination before committing**, then decides whether to continue.

- Design: *"when a user performs a back gesture on a full-screen surface, the inner area should scale down as the gesture progresses."*
- **Do not drive the preview animation from the raw linear gesture progress** – *"feed the progress value with a `STANDARD_DECELERATE` token."*
- Implement via `OnBackPressedCallback` (AppCompat 1.6.0-alpha05+) or `OnBackInvokedCallback`.

`This is the clearest real-world instance of web §6.4's interruptibility` – a gesture that is continuously previewable and reversible until released. It is also the pattern Flutter had to adopt (`PopScope`) and React Native had to add support for, which is a good indicator of how strongly the platform expects it.

`[sources: developer.android.com – Compose modifiers, constraints and modifier order, Material 3 in Compose, dynamic colors, window size classes, adaptive apps, accessibility in Compose, animation quick guide, navigation type safety, predictive back design; Android Developers Blog "Unfold new possibilities with Compose Adaptive Layouts 1.2 beta"; m3.material.io "Introducing Material Symbols"; developers.google.com/fonts Material Symbols guide; composables.com M3 Typography]`

### 4.9 Compose in 2026 – what shipped

Version cadence is now quarterly-ish releases named by month. **August '26 is Compose 1.12** (BOM 2026.08.00, AGP 9.2.0+, compileSdk 37); April '26 was 1.11.

The design-relevant additions:

- **Mesh gradients.** Multi-point gradients as a first-class API. This is the technique behind most of the "aurora" and soft-blob backgrounds of the current visual moment, and having it native means it can animate cheaply instead of being a large image.
- **Wide color gamut support** for P3 and HDR color (§13.9).
- **Named areas in the experimental `Grid` layout**, which is CSS grid-template-areas for Compose and makes complex responsive layouts readable.
- **Rich-text formatting APIs on `BasicTextField`** plus programmatic selection control – the foundation for any editor-shaped app.
- **`DeferredTransition` APIs for gesture-driven animation**, which is §10.5's interruptibility becoming a supported API rather than a manual build.
- **Credential Manager integration on text fields**, so passkeys and autofill work without custom wiring (§21.15).

On performance: **stability inference improved with Kotlin 2.2**, so fewer unnecessary recompositions without hand-annotating `@Stable`. That reduces, but does not remove, the stability work described in §17.3.

## 5. Slint (Rust)

### 5.1 What Slint is, structurally

A **declarative DSL compiled to native code** – not a retained-mode widget library and not a WebView. Bindings for Rust, C++, JavaScript and Python from one `.slint` source.

- **Runtime fits in under 300KiB RAM**, reactive property system, written in Rust.
- **60fps on low-resource hardware.** The design consequence: Slint is the one toolkit in this study where *the same design* is expected to run on a Cortex-M0 and a desktop – which pushes hard toward restraint.
- The language is deliberately CSS/HTML-adjacent, separating presentation from business logic.
- Latest: **1.17, released 24 June 2026**, framed as "another big step toward desktop-ready" – note the implication that desktop polish is still catching up to embedded maturity.

### 5.2 The layout model

| Element | Behavior |
|---|---|
| `VerticalLayout` / `HorizontalLayout` | Children along one axis |
| `GridLayout` | Rows and columns |

- Sizing hints: **`preferred-width` / `preferred-height`**, plus **stretch factors** – *"when 0, the element won't be stretched unless all elements are 0."*
- Properties re-evaluate automatically when anything they read changes (reactive bindings).
- **Two-way binding with `<=>`.**
- Top-level non-private properties of a component are its public API, exposed to the host language as generated getters and setters.

`Design consequence:` there is **no CSS-grid or flexbox equivalent** – no `auto-fit`, no `minmax()`, no subgrid, no container queries. Layout is nesting plus stretch factors plus explicit min/preferred/max. **Every layout idea from the web study has to be re-expressed in that vocabulary**, and some of them (§web 5.11 masonry, §web 11.5 subgrid) simply do not port.

### 5.3 Styling and theming

Built-in styles: **`fluent`** (Fluent Design), **`material`** (Material 3), **`native`** (platform-resembling), and **`qt`** (uses `QStyle` for genuinely native widgets **when Qt is installed on the system**).

- **Built-in widgets adapt automatically to the system light/dark setting**, and the choice can be overridden programmatically.
- Custom styles are supported and documented; community custom styles exist.
- **`GAP:` custom widget creation from native code is planned, not yet public.** Everything custom happens in the `.slint` language.

`Design consequence:` picking `material` or `fluent` inherits an entire design language's decisions (web §9.2). Picking `native`/`qt` inherits the platform's. **Building a distinctive brand means building the style layer yourself in the DSL** – which is where the corpus discipline (web §15) becomes directly useful, because you are authoring the primitives rather than overriding a framework.

### 5.4 Animation – closer to CSS than to native

The `animate` keyword animates a property whenever it changes:

- Parameters: **`delay`, `duration`, `iteration-count`**.
- Easing: **`linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier(a,b,c,d)` – "as in CSS."**
- **`animate x, y`** applies one animation to several properties.
- **`states`** declares conditional states; **`transitions`** bind animations to state changes, with **`in`** and **`out`** keywords for entering and leaving.

`This is the most directly transferable area in the whole toolkit.` The entire web motion chapter applies with almost no translation:

- **The asymmetry rule (web §6.1) maps exactly onto `in` and `out` transitions** – `ease-out` on `in`, `ease-in` on `out`. Slint's syntax makes the correct pattern easier to express than CSS does.
- Duration bands (web §6.2) transfer unchanged.
- **`GAP:` no spring primitive.** `cubic-bezier` only – so §web 6.3's spring guidance has to be approximated, and §web 6.20's `linear()` trick has no equivalent. Overshoot must be hand-authored as a bezier that exceeds 1.
- **`GAP:` no `prefers-reduced-motion` equivalent surfaced.** On a platform that ships to desktop this is a real accessibility hole – flag it and check whether the system setting is reachable from the host language.

### 5.5 Typography and text rendering – the sharpest gotcha

Properties: `font-family`, `font-size`, `font-weight`; `default-font-*` on the `Window` element cascades. Custom fonts are **TrueType (.ttf), TrueType collection (.ttc) or OpenType (.otf)**, pulled in with an `import` in the `.slint` file, which makes the family globally available.

Under the hood: **`fontique` for font discovery, `parley` for complex text layout**, plus a manual layout engine and **bitmap and SDF fonts** for memory-constrained targets.

**Text rendering quality differs by renderer, and this is a design-level fact, not an implementation detail:**

| Renderer | Text output |
|---|---|
| **Software** (`fontdue`) | Targets low-end systems. **On desktop it looks visibly different from CoreText / DirectWrite / FreeType** |
| **FemtoVG** | Renders outlines to glyph textures **without hinting** – *"looks quite good at large glyph sizes, tends to be blurry at smaller sizes"* |
| **Qt** | Delegates to CoreText / DirectWrite / FreeType – **identical to other native apps** |

`Design consequence, and it is significant:` this is web §9.8's macOS/Windows rendering divergence **plus a third axis**. A type ladder tuned in the live preview on one renderer can look thin, blurry or mis-weighted on another.

**Working rules:**
- **Bias display and body sizes upward** if the target uses FemtoVG, because small text is where the missing hinting shows.
- Verify the type scale **on the renderer you actually ship**, not in whatever the preview defaults to.
- Prefer SDF or bitmap fonts on MCU targets and accept that fine typographic craft (web §1.5 tracking, web §1.18 micro-typography) largely does not survive there.

### 5.6 Accessibility – real, but with a named hole

- **AccessKit integration**, via the winit backend – *"a rich cross-platform library that provides an abstraction over operating system accessibility APIs."*
- **Always enabled on all desktop platforms.**
- Accessibility frameworks can trigger **custom actions** – incrementing a spinbox, setting a text input value.

**Two documented limitations:**

- **`GAP:` text input widgets are not exposed.** *"A major missing feature."* For any form-bearing application this is a serious constraint and needs verifying against the current release before committing.
- **Performance impact on large list views** is a reported issue.

`Design consequence:` the web accessibility chapter's *principles* transfer (web §8), but the *verification methods* do not – there is no axe, no Lighthouse, no browser accessibility tree inspector. Verification means the platform's own tools (VoiceOver, Narrator, Orca) against a running binary.

### 5.7 Rendering backends and where the design has to bend

| Backend | Acceleration | Use |
|---|---|---|
| **Software renderer** | None – CPU only, partial rendering supported | MCUs, no-GPU targets. Highly portable |
| **FemtoVG (OpenGL)** | GPU, OpenGL required | Standard desktop/embedded GPU |
| **FemtoVG-wgpu** | **Metal, Vulkan, Direct3D** | Modern desktop |
| **Qt** | Qt's own | Native look and native text |
| Embedded paths | **DMA2D, framebuffer, linebuffer** | Chosen per device |

Range: **Cortex-M0 up to quad-core Cortex-A53.** On MCU targets the developer supplies input events and a frame/line buffer.

`Design consequences on constrained targets:` no blur or backdrop-filter equivalent to lean on (web §12.3, web §8.17), effects budget is real, partial rendering rewards designs where **only small regions change per frame** – which argues directly against full-screen transitions and ambient background animation.

### 5.8 Tooling and workflow

- **Live-Preview**: tweak colors, animation, geometry and text and see it instantly; **change component and global properties in real time to test interactions and animations without recompiling.**
- **LSP server** with autocomplete, go-to-definition, refactoring, syntax highlighting; bundled in the **VS Code extension**. `Slint: Create New Project from Template` in the command palette.
- **Figma integration**: select elements in Figma, view the corresponding Slint code, paste it into Live-Preview.

`This is the closest analogue to the web study's core loop.` Live-Preview makes the **three-variant comparison** workable in Slint – edit properties live, compare directions, without a rebuild between them.

`[sources: slint.dev; docs.slint.dev (layouts, properties, animations, states, fonts, backends, embedded, VS Code); Slint 1.6 and 1.17 release notes; slint-ui/slint PR #2865 (AccessKit), issue #2895 (text input a11y), issue #3867 (listview a11y perf); DeepWiki slint-ui/slint (software renderer, text rendering and font system, platform support)]`

### 5.9 State architecture – globals and callbacks

**`global Name { ... }`** declares a singleton whose properties and callbacks are available project-wide, accessed as `Name.property`. Re-export it from the file that exports the main component to expose it to the host language.

**Two gotchas that shape architecture:**

- **Globals are not shared between windows.** *"You may need to initialize the global callbacks and properties for each window instance you create."* A multi-window Slint app cannot treat a global as application state.
- **Identifier translation:** hyphens in `.slint` become underscores in the host language, because most languages forbid hyphens.

The runtime is three layers converging on an event loop: **Component** (items as a tree) → **Window** (lifecycle, event dispatch, platform integration) → **Property** (reactive binding with dependency tracking and automatic change propagation).

`The honest practitioner note:` the common pattern is *"global singletons in `globals.slint` for application state, backend sets properties, UI fires callbacks"* – and *"this approach can become messy as the app grows,"* with form-heavy screens producing a proliferation of callbacks. **Rust stays the source of truth; the DSL is a projection of it.** Treat the global layer as an explicitly designed API surface, not a dumping ground – the same discipline as web §13.7's component API.

### 5.10 Slint 1.17 (24 June 2026)

New: **drag and drop (declarative), system tray icons, tooltips, two-way bindings on model rows**, and a **remote viewer** – edit the declarative portion locally and see it update on another machine, including phones.

`Two things worth noting for design work:`
- **Drag and drop and tooltips only arrived in mid-2026.** Anything from web §6.17 and web §7.24 assumed to be a primitive was, until recently, not one. **Check the version before designing around a component.**
- **The remote viewer extends the Live-Preview loop (§5.8) to real target hardware** – which for embedded work means evaluating a design on the actual display, at the actual size, under the actual renderer (§5.5). That closes the most dangerous verification gap in the whole toolkit.

### 5.11 Performance and the list problem

- **`SLINT_DEBUG_PERFORMANCE`** environment variable reports frame rate, with an option to measure only on change. **RenderDoc** captures the OpenGL/Vulkan commands.
- *"Minimise the number of commands per frame"* – draw-call count is the lever, matching web §12.8's WebGL guidance.
- Slint hardware-accelerates where available, but *"depending on UI complexity and GPU power you may hit limits."*

**`GAP` / known limitation:** **`ListView` performance is a documented pain point** – reported flickering when content extends beyond one screen, and the visible-only rendering workaround **requires all items to be the same height.**

`Design consequence, and it is a real constraint:` **variable-height list rows are expensive in Slint.** Where the web study reaches for a feed of mixed-height cards (web §5.11, §5.8) and native reaches for a recycling list (§17.4), Slint currently rewards **uniform row heights.** Design the list around that rather than discovering it at scale – it is the kind of constraint that should shape the layout, not be fought.

### 5.12 Models, repeaters and the list question

**`Model` provides data for `Repeater` and `ListView`.** `ModelRc<T>` is the reference-counted handle used in generated code and public APIs for array properties; `as_any()` downcasts to mutate the underlying source. `StandardListView` is `ListView` with a default delegate and a `model` property.

`CONFLICT – and it matters for design:`

- **The docs state ListView virtualizes:** *"elements are only instantiated if they are visible, which guarantees stable performance with a practically unlimited number of items."*
- **The issue tracker reports otherwise in practice** – flickering when content exceeds one screen, redraw bugs on model change, and a visible-only rendering workaround that **requires uniform item heights** (§5.11).

**Working position: treat long lists as a risk area to prototype early**, and prefer uniform row heights until measured otherwise on the target renderer. Do not design a mixed-height feed in Slint without proving it first.

### 5.13 Text input and forms

- **`LineEdit`** (single line) and **`TextEdit`** (multi-line). Properties: `has-focus`, `horizontal-alignment`, `font-size`, `placeholder-text`, `enabled`, `read-only`, and **`input-type`** for password fields.
- **`GAP:` input validation is an open issue** – with `input-type` set to number or decimal, non-numeric characters can still be entered via some input methods.
- **`GAP:` text input widgets are still not exposed to accessibility** (§5.6).

`Design consequence, and it is a real limitation:` **Slint is currently a weaker choice for a form-heavy application than for a display- or control-heavy one.** Validation has to be built in Rust and reflected back, and the accessibility gap is unresolved. That is a genuine "choose a different tool" signal for, say, a data-entry desktop app – and no obstacle at all for an instrument panel.

### 5.14 Multiple windows and modals

**Multi-window arrived in Slint 1.7.** Declare additional exported components (a `SettingsDialog` alongside `MainWindow`) and show them as separate windows.

- **`show_modal()`** with **`WindowModality::Window()`** (modal to one window) or **`WindowModality::Application`** (modal to the whole app).
- **`PopupWindow`** for lighter cases that do not need a real window.
- Remember §5.9: **globals are not shared between windows** – so a settings dialog does not automatically see the main window's global state.

### 5.15 Testing – and a detail directly relevant to this skill

**`i-slint-backend-testing`** provides UI-testing APIs, including for **CI environments with no display**.

- **Screenshot testing:** `.slint` files in `tests/screenshots/cases` are rendered and compared against references in `tests/screenshots/references`. **SoftwareRenderer only** at present.
- **Animations use mocked time, not real time** – so motion is deterministic under test. That is a better testing story than either web or native offers for animation.
- **The testing backend embeds an MCP server that lets an AI agent inspect and interact with a running Slint application in real time** – exploring the UI tree, taking screenshots, clicking, dragging, typing.

`That last point is significant for how this skill can work.` For web the deliverable is HTML the user opens; for Slint there is a **programmatic path to actually driving and screenshotting the running UI** – which turns web §14.13's "verify rather than assert" from a checklist into something executable. Worth building the skill's Slint workflow around.

### 5.16 Slint vs Qt/QML – the honest comparison

- Slint is *"a declarative approach inspired by QML but designed to be leaner"* – a DSL compiled to native Rust or C++, versus QML's interpreted/JIT engine.
- **Qt is 25+ years old with far more automotive acceptance and broader platform support** (desktop, mobile, web, embedded, RTOS).
- **`CONFLICT` / dated-source caution:** one 2025 comparison states *"Slint is mainly used for embedded devices, with support for desktop and web not yet production ready."* Slint's own 1.15–1.17 releases are explicitly a **desktop-readiness push** (§5.10), and 1.17 shipped drag-and-drop, tray icons, tooltips and multi-window. **Treat pre-2026 assessments of Slint's desktop maturity as out of date, and verify against the current release.**
- Practitioner reports: *"Slint is replacing Qt QML HMIs, reducing or eliminating bugs, improving performance, and making it much easier to rapidly design the UI."*

**The design read:** Slint is strongest where it started – **embedded and control-surface UI** – and is closing on desktop. For a Rust project targeting an instrument, kiosk, appliance or embedded HMI, it is the clear choice. For a form-heavy desktop application today, weigh §5.13's gaps honestly.

### 5.17 Rust accessibility – the state of it

**AccessKit is the shared accessibility layer for the Rust GUI ecosystem**, abstracting the OS accessibility APIs.

- **egui was the first pure-Rust GUI toolkit – and the first immediate-mode GUI of any language – to implement platform accessibility APIs**, via AccessKit. **Enabled by default in `eframe`**, currently covering **Windows and macOS**.
- Slint integrates AccessKit through the winit backend, always enabled on desktop (§5.6).
- FLTK integrated it in 2025 (two lines of code).

`The ecosystem position:` **accessibility in Rust GUI is real but young and uneven** – good on Windows and macOS, thinner on Linux, with named gaps (Slint's text inputs). **For any Rust GUI project with an accessibility requirement, verify the specific widget types you need against the current release rather than assuming toolkit-level support.**

### 5.18 The reactivity model, precisely

Slint's property system is a dependency-tracking reactive graph, not a diffing tree. When a property changes, the runtime finds every expression that depends on it and re-evaluates only those. This is closer to SolidJS or MobX than to React, and it is why Slint holds a frame budget on hardware where a virtual DOM would not.

Consequences a designer needs to internalize:

- **Bindings are the default, callbacks are the exception.** `width: parent.width / 2;` re-evaluates automatically. Reaching for a callback to imperatively set the width is fighting the engine.
- **Two-way binding is `<=>`.** Two properties linked with it always hold the same value. This is the right tool for a widget's value and the model's value, and it is what removes the usual "who owns this state" problem in form code.
- **`changed` callbacks are a last resort.** Slint's own guidance is explicit: use a `changed` callback only when a binding cannot express it, because a `changed` callback forces the property's binding to be evaluated even when nothing reads the property. It converts a lazy graph node into an eager one.
- **Mutating a property inside its own change event is undefined behavior.** Property/callback loops are the classic way to hang a Slint UI.
- **`pure`** marks a callback or function as side-effect free, which is what makes it callable from inside a property binding. Purity is inferred for private functions and must be declared for public ones.

### 5.19 The Rust side of the boundary

The generated Rust API is the design surface between the `.slint` file and the application:

| Slint declaration | Generated Rust |
|---|---|
| `in property <string> name;` | `set_name(&self, v: SharedString)` |
| `out property <int> count;` | `get_count(&self) -> i32` |
| `in-out property <bool> on;` | both |
| `callback accepted(int);` | `on_accepted(impl FnMut(i32))` |
| `global Palette { ... }` | `global::<Palette>(&component)` accessor |
| model-backed `for` loop | `ModelRc<T>` set from Rust |

The practical architecture that holds up: keep application state in Rust, expose it through `in` properties and a global for theme tokens, and let the UI emit `callback`s upward. `in-out` is for widget-local state only. This is unidirectional flow by construction, and it is the same shape as iced's Elm loop even though the syntax looks nothing like it.

Weak handles matter: callbacks capture the component, so capturing a strong handle inside a callback on that same component leaks. Use `slint::Weak` and `upgrade_in_event_loop` from worker threads.

### 5.20 The platform abstraction layer

Slint decouples the core runtime from windowing and rendering behind a trait-based platform layer. Introduced for MCU support in 0.3 and extended in 1.2, it means Slint runs on bare metal with no OS at all.

To port Slint to a new target you implement two things: feed input events (touch, keyboard) in, and push rendered pixels out to a frame buffer or line buffer.

The software renderer has two modes and the choice is a RAM decision, not a quality decision:

| Mode | Requirement | Use |
|---|---|---|
| `SoftwareRenderer::render()` | Enough RAM for a full frame buffer | Embedded Linux, larger MCUs |
| `SoftwareRenderer::render_by_line()` | One scanline of RAM | Small MCUs pushing pixels over SPI |

`render_by_line` is the reason Slint fits in tens of kilobytes. It also constrains design: effects that need to sample the whole frame (real blur, backdrop translucency) are impractical in line mode. On a constrained target, design with flat fills, gradients and sharp edges, and treat glass or blur effects as a desktop-only luxury.

### 5.21 Live preview, and why it matters for this skill

Slint 1.13 (3 September 2025) brought live preview into the Rust and C++ build cycle, not just the VS Code extension: enable it in the build, run the app, edit the `.slint` file, save, and the running application updates. 1.13 also added an interactive outline panel with drag-and-drop reordering onto the canvas, plus undo/redo for edits made in the preview UI.

For a design workflow this is the closest thing Rust GUI has to browser devtools. It is the reason a "three variants to choose from" flow is viable on Slint at all: the variants can be three `.slint` files swapped live, rather than three rebuilds.

Version note: 1.15 and 1.17 (24 June 2026) followed; treat 1.17 as the current baseline and check the changelog rather than trusting any single version claim.

### 5.22 Styling std-widgets, and the honest limitation

`std-widgets.slint` ships four styles: **fluent** (Fluent Design), **material** (Material Design), **cupertino** (Apple-like) and **native** (the platform's real controls). The style is selected at build time, not at runtime, and the widgets expose the current style's values through two globals:

- **`Palette`** – the semantic color set (`background`, `foreground`, `alternate-background`, `accent-background`, `accent-foreground`, `border`, and the control variants).
- **`StyleMetrics`** – spacing and layout metrics for the current style.

Those two globals are Slint's design-token layer, and the correct pattern is to build custom components against `Palette` and `StyleMetrics` rather than against literal colors, exactly as one would build against CSS custom properties on the web.

The limitation to state plainly: **`std-widgets` are not deeply themeable.** There is a long-standing open proposal (issue #5392) for real styling capabilities, and the community answer to "how do I theme std-widgets" is still, in practice, "write your own widgets." The internal architecture is tiered – a `Base` component holds logic, focus handling and accessibility, and a styled component wraps it – but the base tier is not a public API you can build on.

The consequence for a branded application: expect to build a component library. That is not as bad as it sounds, because Slint's component syntax is compact and a button plus a text field plus a list row covers most of an app, but it must be planned for rather than discovered late. Define a `global Theme` with color, spacing, radius and font tokens on day one, and build every component against it.

### 5.23 Images, scaling and resolution independence

Slint reads SVG plus the usual raster formats (PNG, JPEG, WebP, AVIF, GIF, BMP, ICO, DDS, HDR).

Two things matter for design quality:

- **SVG is rendered at the size it is used**, so it stays sharp across DPIs. This is the reason to keep icons as SVG in Slint rather than exporting raster sets per scale factor. Loading an SVG and then scaling the element afterwards is the mistake that reintroduces blur.
- **Nine-slice scaling is supported** via `@image-url("...", nine-slice(30 30 30 30))`. This is how a bitmap with decorative corners – a bordered panel, a chat bubble, a pressed-button skin – stretches without distorting its corners. SVG itself has no nine-slice concept, so this is Slint's answer for skinned bitmap chrome.

`image-fit` (`fill`, `contain`, `cover`, `preserve`) and `image-rendering` (`smooth`, `pixelated`) round out the set. `pixelated` exists for deliberate pixel art and for tiny embedded assets where smoothing turns a 16 px icon to mush.

### 5.24 Layout constraints, precisely

Every Slint element carries a **minimum, maximum and preferred size**. `preferred-width` and `preferred-height` set it explicitly; unset, it derives from the largest preferred size among children whose `x`/`y` are not set. Setting `x` or `y` on a child removes it from the layout, which is the mechanism for overlays and absolutely positioned decoration – and the accidental cause of "my layout ignores this element".

- **`horizontal-stretch` / `vertical-stretch`** distribute extra space along the layout axis. `0` means "do not grow". When every sibling is `0` they all stretch equally, which is a surprising default worth remembering.
- **Alignment** defaults to `stretch`: each item fills the cross axis. `start`, `end` and `center` instead size each item to its preferred size (clamped to min/max) and position it. Reaching for a `Rectangle` spacer to fake alignment usually means the alignment property was the answer.
- **`spacing`** on the layout, `padding` (plus the per-edge variants) on its box. Use these rather than margins on children, because Slint has no margin concept.
- `GridLayout` takes `row`, `col`, `rowspan`, `colspan`, and is the right tool for forms where labels and fields must align across rows.

`Design-token consequence:` define spacing and radius once in a `global Theme` and reference them from every layout's `spacing` and `padding`. A Slint file with literal numbers scattered through it is exactly as unmaintainable as a stylesheet with hardcoded pixels.

### 5.25 Threading, async and keeping the frame budget

The UI runs on the main thread with a platform event loop (winit on desktop). The rule Slint states directly: **do the minimum in the main thread and delegate real work elsewhere**, or animations stall.

The three tools:

| Need | API |
|---|---|
| Run a future from a UI callback | `slint::spawn_local(future)` |
| Talk to the UI from a worker thread | `slint::invoke_from_event_loop(closure)` |
| Delay or repeat | `slint::Timer` (single-shot or interval) |

Two gotchas that matter in practice:

- **A future tied to a specific runtime will not just work.** Tokio futures assume a Tokio runtime, which is not what the Slint event loop provides. The working pattern is to run the Tokio runtime on its own thread and `await` a `tokio::spawn` handle inside the future passed to `spawn_local`.
- **Spawning from another thread** requires wrapping: call `invoke_from_event_loop` and start the local task from inside that closure.

`Design relevance, and it is direct:` this is the machinery behind every loading, empty and error state in a Slint app. If long work runs on the UI thread the app does not show a spinner slowly, it freezes, and a frozen UI cannot animate its way out. Wire the async pattern before designing the states, or the states will not be reachable.

### 5.26 Platform reach and licensing – the practical constraints

**Platform status.** Desktop (Windows, macOS, Linux) and embedded (bare-metal MCU, embedded Linux) are the mature targets. **Android and iOS support exists but is described by the project as initial.** Web works via WASM. Language bindings: Rust, C++, JavaScript, Python.

`Design consequence:` Slint is a strong choice for a Rust desktop app or an embedded product, and a speculative one for a phone app in 2026. If the target is iOS or Android as a primary platform, the honest recommendation is a native toolkit or Compose Multiplatform, not Slint.

**Licensing, because it changes the answer by product type.** Three options:

| License | Covers | Cost |
|---|---|---|
| **Royalty-free 2.0** | Proprietary **desktop, mobile and web** apps | Free, requires showing Slint attribution |
| **GPLv3** | Anything, including embedded | Free, copyleft |
| **Commercial** | Anything, including embedded, no attribution | Paid, includes support |

**Embedded is explicitly excluded from the royalty-free license.** A product that ships Slint on a device is either GPLv3 or paying. That is the single most important licensing fact for anyone evaluating Slint for hardware, and it belongs in the decision before any design work starts.

### 5.27 States and transitions – the declarative state machine

Slint's `states` block is one of its strongest ideas and the reason animation in Slint stays readable as complexity grows. A state names a set of property values, and `in` / `out` blocks attach animations to entering and leaving it:

```slint
states [
    disabled when !root.enabled: {
        opacity: 0.4;
        out { animate opacity { duration: 800ms; } }
    }
    pressed when touch.pressed: {
        background: #c00;
        in { animate background { duration: 200ms; } }
    }
]
```

Why this matters beyond syntax: **the states are declared in one place, so the set of visual states a component has is readable at a glance.** The web equivalent is scattered across pseudo-classes, class toggles and JavaScript, and the native equivalent is scattered across view code. This is the closest thing in any toolkit to the "name all five states" discipline (web §7.1) being enforced by the language.

The `in`/`out` asymmetry is also the correct default. Entering a disabled state slowly and leaving it quickly is usually right, and having separate hooks makes that expressible without a conditional.

Known limitation: states and `animate` cannot be referenced from other elements, so a shared transition has to be duplicated or factored into a component. The upstream issue is open; design around it by making the state-bearing thing a component.

### 5.28 Performance and profiling

Slint's runtime fits in **under 300 KiB of RAM** and picks the best available rendering path per target: GPU acceleration, DMA2D on capable MCUs, a full framebuffer, or line-by-line (§5.20).

Profiling: Slint has internal stage profiling behind the `slint_debug_performance` cfg, which reports into `debug_log`. It is coarse compared to a platform profiler, and the practical technique when chasing a specific stall is still to wrap suspected calls with timing and bisect down the stack.

The optimizations that matter at design time rather than at fix time:

- **Embedded image scale factor.** Images are scaled at compile time to the size actually used, so shipping a 2048 px asset for a 64 px icon costs flash for nothing. Set the scale factor rather than trusting the source asset.
- **Keep the reactive graph shallow.** A property whose binding depends on ten other properties re-evaluates whenever any of them changes. Deep chains are the Slint equivalent of an over-broad recomposition scope in Compose (§17.3).
- **Avoid `changed` callbacks** (§5.18); each one forces eager evaluation of an otherwise lazy node.
- **Repeaters and models virtualize** (§5.12), but only if the model is a real `Model` implementation rather than a re-created array.
- **Effects that sample the frame are expensive on every backend and impossible on some.** Design chrome that does not need them (§19.6).

### 5.29 Focus and keyboard

`FocusScope` is the element that participates in keyboard focus. It takes focus on click (when `focus-on-click` is set) and via tab traversal, and it exposes `key-pressed` and `key-released` callbacks.

Mechanics worth knowing:

- **`enabled: false` removes it from focus entirely** – not clickable, not tabbable, not focusable programmatically. That is the correct way to express a disabled control, and it means disabled state is a focus decision, not just a visual one.
- **Key events bubble.** If the focused element's callback does not return `EventResult::accept`, the event walks up to the parent. That is how a screen-level shortcut handler is built: put a `FocusScope` around the screen and let unhandled keys reach it.
- **Tab order follows declaration order.** There is no explicit tab-index, so the order in the `.slint` file is the tab order – which is a good default and a trap when a visually reordered layout no longer matches the source order.
- Known gap: `FocusScope` does not distinguish keyboard focus from click focus (upstream issue #8387). That is the same distinction the web solved with `:focus-visible`, and until it lands, a Slint app either shows focus rings on mouse clicks or risks hiding them from keyboard users. Choose showing them.

`Why this belongs in a design study:` keyboard operability is an accessibility requirement on desktop, and a visible focus indicator is a WCAG criterion. On Slint both are the designer's responsibility rather than something the widget set guarantees.

### 5.30 The widget set, and what it does not include

`std-widgets.slint` provides the expected set: `Button`, `CheckBox`, `Switch`, `Slider`, `SpinBox`, `ComboBox`, `LineEdit`, `TextEdit`, `ListView`, `StandardListView`, `StandardTableView`, `TabWidget`, `ScrollView`, `GroupBox`, `ProgressIndicator`, `Spinner`, plus the layout primitives.

Two useful details:

- **`StandardListView` is `ListView` with a default delegate** and a model of `StandardListViewItem`. It is the fast path for a plain list, and the moment a row needs anything custom you drop to `ListView` and write the delegate yourself, which is the right boundary.
- **`ComboBox`** exposes `model`, `current-index` (−1 when nothing is selected) and `current-value`, and opens a popup on click. The −1 convention matters: an unset combo box is a real state that needs a placeholder, and forgetting it is how "select an option" ends up showing the first option as though it were chosen.

`What is not in the box, and therefore is design work:` date and time pickers, a search field with suggestions, a snackbar or toast, a sheet with detents, a segmented control, a navigation stack, a carousel, a chart, and any form of empty-state component. On Slint those are all built from primitives, which is a real cost to budget and also the reason a Slint app can look like itself rather than like a default widget set.

## 6. Cross-platform toolkits

### 6.1 The axis that actually decides everything

Every cross-platform toolkit sits somewhere on one line, and **the position on that line determines what design work is possible**:

| Approach | Toolkits | You get | You pay |
|---|---|---|---|
| **Own canvas** (draws every pixel itself) | Flutter, **Slint**, Compose Multiplatform (Skia on iOS), Avalonia (Skia), **egui**, **iced**, **GPUI** | **Pixel-perfect consistency across platforms.** Total control of the design language | You own *everything* – every control, every state, every accessibility affordance. Nothing is free, and nothing feels native unless you make it |
| **Native widgets** (delegates to platform controls) | React Native, .NET MAUI, Slint's `qt`/`native` styles | Platform-native feel, native text rendering, native a11y for free | **Minor inconsistencies across devices and OS versions**; you inherit the platform's design language whether you want it or not |
| **System WebView** | **Tauri**, Dioxus desktop, Electron | The whole web design toolkit – all of the web study applies directly | **Rendering divergence** between WKWebView / WebView2 / WebKitGTK, and a non-native feel unless carefully countered |

`This is the native equivalent of web §12.7's design-system-vs-brand tension`, and it is sharper here because it is decided at toolkit-selection time, before any design work happens.

### 6.2 Tauri – the numbers

Against Electron 43: **~96% smaller, ~3.7× faster start, ~75% less idle memory.** Bundle **3–10MB vs 120–200MB**; idle RAM **40–80MB vs 150–400MB**. Tauri 2.x adds **iOS and Android** alongside desktop.

- Architecture: **Rust backend at native speed, UI in HTML/CSS rendered by the OS webview** – WebView2 (Windows), WKWebView (macOS), WebKitGTK (Linux).
- **The design cost, stated plainly by the sources:** *"WKWebView, WebView2 and WebKitGTK render some CSS features slightly differently... it requires testing on all three platforms before shipping."*
- The honest framing: **"consistent rendering at a large size (Electron), or small size at the cost of rendering consistency (Tauri)"** – with Tauri the better default in 2026 for most tools and utilities.

`For this skill:` if the target is Tauri, **the entire web study applies unmodified** – with web §9.7's cross-browser caveats promoted from an annoyance to a shipping requirement, since WebKitGTK is a far weaker engine than desktop Safari.

### 6.3 The Rust GUI landscape, honestly

**Adoption reality check: egui has ~13M downloads – roughly 10× iced and Slint combined, and more than Tauri.** Popularity is not quality, but it tells you where the examples and community answers are.

**egui** – immediate mode. The UI is described and painted **every frame**; no callbacks, no click handlers, no state synchronization.

- Strength: *"the fastest to get started"*, excellent for tools, prototypes, debug UI, and games.
- **The design-relevant limitation, and it is structural:** immediate mode does **single-pass layout**, so anything requiring knowledge of a child's size before placing it needs a **frame-delay workaround** – *"which produces a frame delay for the correct layout and occasional flickering the first frame something shows up."* egui does exactly this for windows and grid layouts.
- *"When trying to involve dynamically sized elements and responsive layouts it becomes difficult, because it has to calculate everything in a single pass."*
- Styling via `Context::set_style` – *"not yet as powerful as CSS."*
- The most useful characterization found: **"it feels like a debug UI that grew up... custom styling is possible but not the point."**

`Design consequence:` **egui is the wrong tool for a design-led product** and the right tool for an instrument. Do not attempt the corpus-grade craft (web §15) in it – you will fight the framework for every pixel and lose the frame-delay argument.

**iced** – retained, **Elm architecture**: state, messages, update, view. Type-safe, functionally structured. *"Patterns from purely functional languages work well in Rust because they leverage immutability and referential transparency."* Widgets are reusable library building blocks; the application owns state, interactions, and how state dictates widgets.

**GPUI** – **hybrid immediate + retained, GPU-accelerated, targeting 120fps.** Built by the Zed team; still **pre-1.0 with frequent breaking changes**, currently inside the Zed repo, planned as a standalone crate. Community `gpui-component` kit exists.

`Note the 120fps target` – web §6.9's finding that *"120Hz makes mediocre animation look worse"* applies directly, and GPUI is explicitly built for that bar.

**Dioxus** – React-shaped: components, hooks, `rsx!`. Targets web, desktop, mobile and server from one codebase. **0.6 is production-usable; 0.7 added hot-patching and primitive UI components modeled on shadcn/ui.** For someone who already knows the web study's component vocabulary, this is the shortest path from web design thinking to a native binary.

**Slint** – §5. The declarative-DSL position: more design capability than egui, more structure than iced, and the only one in this list with a serious embedded story.

### 6.4 Choosing, for a Rust project

| If the work is… | Reach for |
|---|---|
| A design-led product UI, desktop or embedded, that must look **intentional** | **Slint** – the DSL is the only Rust option that treats styling and animation as first-class |
| A tool, instrument, debug panel, or game overlay | **egui** – fastest to working, and its "debug UI" register is honest rather than a failure |
| A web-shaped app where the whole web study applies | **Tauri** (or Dioxus web-target) |
| Maximum rendering performance and a bespoke design language | **GPUI**, accepting pre-1.0 churn |
| Functional-architecture preference, moderate design ambition | **iced** |

### 6.5 The other ecosystems, for reference

**Flutter** – own canvas, Material 3 on by default (`useMaterial3` true).

- The stated deviation discipline is excellent and worth stealing: **customize through component themes on `ThemeData`** (`segmentedButtonTheme`, `snackBarTheme`) rather than hand-building widgets, so **"deviation is explicit and greppable."**
- *"The M3 package ships the contract components are built from – including their measurements – so a control you build by hand lands on the same numbers as the built-in one."*
- `GAP:` Flutter does not yet have M3 **Expressive** widgets (web §9.2).

**React Native** – native widgets. New Architecture is **default from 0.76+ / Expo SDK 52+**: JSI, Fabric, TurboModules, Codegen replace the async bridge. Reported **60fps, ~40% faster startup, 20–30% less memory.** Still requires per-platform UI branching, which is the point rather than the flaw.

**Compose Multiplatform** – **Stable for Android, iOS and desktop.** Composables render on iOS through **Skia**, so it is an own-canvas approach wearing Android's design language. **80–90% UI code sharing** reported; near-zero learning curve for Android developers.

**Avalonia / .NET MAUI** – MAUI delegates to platform toolkits; **Avalonia renders to Skia** for pixel-perfect control and is desktop-first. Avalonia is now also shipping a **backend for MAUI** adding Linux and WebAssembly targets while keeping MAUI XAML and view models unchanged.

### 6.6 Consistency vs platform idiom – the resolution

The debate stated: *"whether to force brand style everywhere or adapt each platform to feel native."*

The 2026 position worth adopting, because it is specific rather than diplomatic:

> **Use native idioms where they carry meaning – navigation model, back behavior, system gestures, control placement. Keep brand color, iconography style and interaction feedback recognizably consistent.** *"The placement of a Back button may differ per OS, but the icon style and behavior should feel part of the same ecosystem."*

And the deeper correction, from the IA side: **do not ship the same hierarchy across genuinely different platforms** – the same navigation tree on web and voice *"results in dysfunction rather than consistency."* Real cross-platform consistency lives at the level of **model and vocabulary**, not layout.

`CONSOLIDATE:` this maps onto web §9.3 exactly. **Share the logic and the identity; split the navigation model and the gestures.**

`[sources: lib.rs/crates.io/GitHub for egui, iced, gpui, dioxus; book.iced.rs architecture; gpui.rs; Tritium "Thanks for All the Frames"; LogRocket "State of Rust GUI libraries"; Rustify Tauri v2 comparisons; docs.flutter.dev Material 3 migration; React Native New Architecture guides (0.76+/Expo 52+); kotlinlang.org Compose Multiplatform; avaloniaui.net MAUI comparison; SE Radio 713 (Héctor Ramón Jiménez on iced)]`

### 6.7 The Elm loop, and why it recurs in Rust

iced is the clearest statement of the pattern: **state → view → message → update → state**. Four pillars, no widget mutation, no imperative handles. Define a `struct` for state, an `enum Message` for everything that can happen, `update` to fold messages into state, `view` to render state as a widget tree.

Why this keeps winning in Rust specifically: mutable aliasing of widget handles is exactly what the borrow checker refuses. A retained-mode tree of `Rc<RefCell<Widget>>` fights the language. The Elm loop sidesteps it because the only mutation happens in one function that owns `&mut self`.

The design consequence is real, not just architectural: **every UI state must be namable as a value**. Loading, empty, error, partial and success stop being afterthoughts because they have to exist in the state type before `view` can compile. This is the single best argument for Rust GUI from a design-quality standpoint, and it is worth saying to a user who asks why an unfamiliar toolkit is worth it.

iced 0.14 added reactive rendering and time-travel debugging, which is the Elm architecture's classic payoff: because state is a value and messages are a log, you can replay them.

Slint reaches the same place by a different route (§5.19): state in Rust, `in` properties down, callbacks up.

### 6.8 Compose Multiplatform, as of 2026

Realistic sharing is **80–90% of UI code** across Android and iOS. Production-ready: core components, Material 3 theming, resources, state, animation, and navigation via Navigation 3 with type-safe arguments through Kotlin Serialization.

The adaptive story is the part that matters for design: navigation shape is driven by `WindowSizeClass`, never by hardcoded pixel widths. Bottom bar on compact, navigation rail on medium, navigation drawer on expanded and desktop. Material3 Adaptive supplies the components; the designer supplies the decision about what belongs at each size (§8).

The honest caveat: sharing UI is not the same as sharing design. An iOS user reading a Material bottom bar still reads it as an Android app. Compose Multiplatform is the right call when the product's identity is its own brand, and the wrong call when the goal is to disappear into each platform.

### 6.9 Flutter theming, as of 2026

Material 3 has been the default since Flutter 3.16 (`ThemeData.useMaterial3` defaults to true).

The rules that separate a well-themed Flutter app from a messy one:

- Build the scheme with `ColorScheme.fromSeed`, which generates the tonal palette. Never hardcode a color in a widget.
- Read typography from `Theme.of(context).textTheme`, not from inline `TextStyle` literals.
- Put brand tokens that Material has no slot for into a `ThemeExtension`. This is the sanctioned escape hatch and it keeps custom tokens theme-aware in light and dark rather than turning into constants.
- Define a spacing scale and use it. Flutter gives no spacing system, so the absence of one is the most common source of visual drift.
- Keep a gallery or storybook screen in the app that renders every component in isolation. It is the cheapest possible design-system regression test.

### 6.10 egui – what immediate mode costs and buys

egui lays out and paints the entire UI every frame. There are no callbacks, no click handlers, and no synchronization between application state and widget state, because there is no widget state to synchronize. For tools, debug overlays and highly interactive applications this removes an entire class of bug.

The cost is layout, and it is structural rather than a missing feature. egui is **single-pass**, and layout has a cyclical dependency between position, size and content. The consequences a designer hits immediately:

- Sizing a container to its dynamically sized children requires measuring on one frame and using the value on the next, which shows as a **one-frame flicker** the first time an element appears.
- Centering or right-aligning a group whose width is not known in advance has the same problem. Atomic widgets (a button, a label) are fine because egui knows their size before drawing them.
- Genuinely responsive layout – content that reflows based on measured content, not just available width – is where immediate mode fights back hardest. The upstream tracking issue for improved layout has been open for years.

Performance is fine if you design for it: full layout every frame means the layout code must be cheap, so avoid enormous scroll areas and deep nesting.

`Design consequence:` egui is the right choice for dense, functional, tool-shaped UI where the layout is essentially a grid and the value is in the interaction. It is the wrong choice for a marketing-quality, fluidly responsive product UI. That is not a criticism of egui; it is the immediate-mode trade.

### 6.11 GPUI – the Zed approach

GPUI is a **hybrid immediate and retained mode**, GPU-accelerated framework targeting **120 fps**. Views build a tree of elements and style them with a **Tailwind-style API** (`.flex().gap_2().bg(rgb(0x1e1e1e))`), then hand it to GPUI to rasterize.

Two things make it interesting for design work specifically:

- The Tailwind-shaped API means web design vocabulary transfers almost directly. Someone who thinks in flex, gap, padding scale and color tokens can work in GPUI without learning a new layout mental model.
- Elements have total control over their own layout and rendering, which is how Zed does efficient views into enormous lists and custom layout for the editor itself.

It is pre-1.0 with frequent breaking changes and is developed as part of Zed rather than as a general-purpose product, so treat it as a strong option for an ambitious desktop app and a risky one for anything that needs stability. `gpui-component` provides a component set built on it.

### 6.12 Dioxus – React shape, Rust body

Dioxus 0.7 is the reference version: sub-second hot reload including experimental Rust hot-patching, one component tree and a signals-based state model shipping to web, macOS, Linux, Windows, Android and iOS from the same CLI.

The design-relevant additions in 0.7:

- **A first-party component library modeled on Radix Primitives**: 28 foundational components, shipped **unstyled**, with keyboard shortcuts and ARIA built in. Unstyled primitives plus your own tokens is the same model that made shadcn/ui work on the web, and it is the most design-friendly starting point in the Rust GUI space.
- **Dioxus Native**, a WGPU-based HTML/CSS renderer, so the same markup can run without a WebView.

`Choosing between the three:` Dioxus if the team thinks in React and wants web plus native from one tree; GPUI if the target is one very fast desktop app; egui if the UI is a tool. Slint if the target includes embedded, or if a declarative DSL with real designer tooling matters (§5).

### 6.13 Text rendering in Rust GUI, and why it matters

Text quality is the first thing that betrays a non-native toolkit, so it is worth knowing what the Rust stack actually does.

`cosmic-text` is the common layer: shaping via HarfRust, rendering via **swash**, and a custom layout implementation in safe Rust with bidirectional text support. Font fallback is custom, reusing the static fallback lists from Chromium and Firefox, with full support on Linux, macOS and Windows.

**swash** provides horizontal subpixel rendering and fractional positioning, plus ligatures and color emoji. Those two features are precisely what separates text that looks native from text that looks slightly wrong: without fractional positioning, glyph advances snap to whole pixels and word spacing becomes visibly uneven at small sizes.

`Practical implications:`

- Do not assume a Rust toolkit hinted and rendered text the way the platform does. Compare against a native control at 11–13 pt, which is where differences show.
- Complex scripts (Arabic, Devanagari, Thai) depend entirely on the shaping layer. Verify them early rather than assuming, because a shaping gap is not a styling problem you can design around.
- Emoji rendering is a real gap in some stacks and shows up as tofu in user-generated content.

### 6.14 Window scale factors – a detail that produces blurry Rust apps

Any toolkit sitting on `winit` inherits its DPI model, and getting it wrong is the most common reason a Rust GUI looks subtly worse than a native app.

- **Physical pixels** are the actual screen pixels. **Logical pixels** are physical divided by the scale factor. Layout is authored in logical pixels; rendering happens in physical ones.
- `window.scale_factor()` gives the current value, and **`ScaleFactorChanged` fires when the window moves to a different monitor or the user changes their display settings.** Ignoring that event is what produces an app that is crisp on the laptop screen and blurry on the external monitor.
- **Scale factors are not always integers.** Wayland reports integers (usually 1 or 2), but X11 derives the factor from XRandR's millimeter dimensions and can produce values like 1.0833333333333333. macOS and Windows report fractional factors too.

`Design consequences:` never assume 1x or 2x; never bake a raster asset at a fixed pixel size when SVG is available (§5.23); snap borders and hairlines to physical pixels or a 1 px divider becomes a 1.08 px blur; and re-run any glyph atlas or cached raster when the scale factor changes.

### 6.15 The Linebender stack – Xilem and Masonry

Worth knowing because it is where a lot of Rust UI infrastructure actually comes from, even for people who never use Xilem directly.

**Xilem** is a reactive layer inspired by React, SwiftUI and Elm, sitting on top of **Masonry**, a widget toolkit developed alongside it. Application state is an ordinary `'static` Rust type; the view tree is lightweight and rebuilt on change. **Both are alpha.**

The layers underneath are the important part, because they are used far beyond Xilem:

| Layer | Crate | Also used by |
|---|---|---|
| 2D rendering | **Vello** (GPU compute-centric) | A growing set of Rust UI projects |
| Text layout | **Parley** | Multiple toolkits |
| Accessibility | **AccessKit** | egui, Slint and others (§5.17) |
| Windowing | **winit** | Almost everything (§6.14) |

`Why this matters for design work in Rust:` these shared layers are why Rust GUI text rendering and accessibility have improved across several unrelated toolkits at once. When evaluating a Rust toolkit, ask which of these it uses – a project on Vello, Parley and AccessKit inherits work from a much larger pool than one with its own bespoke stack.

Recommendation for 2026: track it, do not ship on it. Masonry is the more interesting piece for anyone building a toolkit; Xilem is the one to watch for applications.

### 6.16 React Native's new architecture, and what it changed

Stable and default since RN 0.76; the old bridge is **permanently disabled as of 0.82**. Three pieces: **JSI** replaces the JSON bridge with synchronous JavaScript-to-native calls, **Fabric** replaces the UI renderer, **TurboModules** replace the old native modules.

Reported effects, and vendor figures vary so treat the range as directional: cold start down around 43%, render speed up around 39%, memory down around 26%, cross-thread calls dramatically faster. More conservatively stated: typical apps see 10–30% UI-thread improvement, and apps leaning heavily on native modules see up to 3x on cross-thread call performance.

`The design consequence, which is the point:` the old architecture's asynchronous bridge is why React Native apps historically felt slightly off during gestures and scrolling – the animation could not respond to touch within a frame. Removing it makes **gesture-driven, interruptible animation** (§10.9) actually achievable in React Native, which was the main thing separating it from native feel. If a team's memory of React Native is "it never quite felt right", that memory is about an architecture that no longer exists.

Ecosystem baseline for the new architecture: React Navigation 7.2+, Reanimated 3.5.1+, Gesture Handler 2.16.2+, Expo SDK 52+.

### 6.17 Tauri v2 on mobile – the honest position

Tauri v2 ships desktop and mobile from one codebase: the frontend runs in the system WebView (WKWebView on iOS, the Android WebView), the Rust backend handles native capability through plugins – camera, notifications, geolocation, biometrics, haptics, sharing.

The honest framing, which the project's own community states: **Tauri is desktop-first with mobile reach, not a React Native or Flutter competitor.** A Tauri mobile app looks and feels like a WebView app unless it is designed deliberately not to.

`If it is the right call anyway – and for a Rust team with an existing web frontend it often is – the design work is explicit:`

- Rebuild the navigation for the platform: top nav becomes a bottom tab bar, hover states become sheets and long presses.
- Honor safe areas with `env(safe-area-inset-*)` and `viewport-fit=cover` (§9.3).
- Use the platform type scale and respect Dynamic Type through the WebView's text scaling.
- Implement momentum scrolling and overscroll behavior that matches the platform (§21.18), because default WebView scrolling is the clearest tell.
- Wire real haptics through the plugin rather than relying on the web Vibration API.
- Disable text selection and callouts on chrome, keep them on content (§21.29).

The remaining gap, which no amount of CSS closes: **gesture handling and transition interruptibility.** A WebView cannot participate in the platform's interactive back gesture the way a native view does. Accept it, or use a native toolkit for the parts where it shows.

## 7. Navigation architecture

### 7.1 The structural divergence nobody warns you about

> **Android's Navigation Component default is to clear the back stack and lose all UI state when switching between bottom tabs. iOS preserves a separate stack per tab.**

On Android, switching tab and returning shows only the root destination – everything the user had opened is gone. That is the **documented Material default**, and it is worse. Multiple back stacks must be explicitly implemented.

`This is the single highest-impact native design defect found in this study`, because it is invisible in a mockup, silent in review, and infuriating in use. **Specify per-tab stack preservation explicitly**, or the platform default will quietly ship.

Other back-stack rules:
- The start destination sits at the bottom of the stack; everything else pushes and pops on top.
- *"Users expect the complete back stack to be available. If your app skips past some pages when users tap Back, users may become confused."*
- Back is a **history** model; Up is a **hierarchy** model (web §5.15's breadcrumb/back distinction, restated natively).

### 7.2 Choosing the navigation model

| Pattern | Use |
|---|---|
| **Bottom tab bar** | **3–5 primary sections of comparable importance.** Both HIG and Material recommend it for primary navigation. Thumb-reachable (§9 / web §4.12) |
| **Navigation drawer** | Many destinations that do not all need to be visible; or **a single dominant primary task** with utilities behind the menu – Google Maps, Uber, Gmail |
| **Hub and spoke** | Distinct areas that do not overlap. Central screen, branch out, return |
| **Both** | Common and legitimate: tabs for primary, drawer for the long tail. Spotify and Gmail do this |

**The 2026 shift worth recording:** *"bottom-centric navigation is expanding beyond the tab bar – the primary interaction surface is below the screen's midpoint, and bottom sheets have become the dominant container for secondary content."*

`Design consequence:` the top of a phone screen is becoming **display-only**. Anything actionable migrates down. That is thumb-zone research (web §4.12) finally reshaping the whole layout convention rather than just the CTA.

- Limit primary items to **3–5**; **pair icons with short labels** (web §8.16).
- *"Fast access to frequent tasks, secondary actions one layer down, and clear cues so people always know where they are."*

### 7.3 Deep links

**Universal Links (iOS) + App Links (Android) are the 2026 standard.** Both are real HTTPS URLs that open the app when installed and **fall back to the website when not** – which is why they beat custom URL schemes.

The honest note, which practitioners state and vendors do not:

> *"Anyone who has implemented Universal Links or App Links knows it rarely works smoothly – sometimes the app opens, sometimes the browser opens, sometimes nothing happens. Email providers break links with tracking wrappers, hosting platforms silently rewrite files, and mobile OSes often decide a domain isn't trusted without telling you why."*

`Design consequences:`
- **The web fallback is not an edge case; design it properly.** A meaningful share of taps will land there.
- **Deferred deep linking** – install, then land on the intended content – is what makes an install-from-link flow not feel broken.
- The destination must handle **arriving with no history**: a deep-linked detail screen needs a sensible Up target, not a dead end.
- Type-safe routes (§3.7, §4.8) make this tractable, because the link parses into the same value the navigation graph already uses.

### 7.4 Predictive back – Android's most underrated design change

Enabled by default from **Android 15**, with the APIs available from Android 14. As the user drags from the screen edge, the current screen scales down and the destination is revealed behind it, so the gesture **previews where it will land** before it commits.

Why it matters beyond aesthetics: the single biggest complaint about gesture back was uncertainty – users did not know whether the swipe would go back one screen, leave the flow, or exit the app. Predictive back answers that question during the gesture, which removes an entire class of accidental exits.

What a designer needs to specify:

- **The scale-down of the outgoing surface.** Android's own guidance is that a full-screen surface scales down as the gesture progresses.
- **The cross-screen versus in-app distinction.** Leaving the app entirely shows the home screen or the previous app behind; going back within the app shows the previous screen. These look different and the difference is informative.
- **Never feed the raw gesture progress into the animation.** The linear progress value must be run through `STANDARD_DECELERATE` or a `PathInterpolator` so motion is pronounced at the start and decelerates for clarity. Raw linear progress feels mechanical.
- **Custom transitions must be seekable**, not fire-and-forget. `PredictiveBackHandler` in Compose exposes a `Flow<BackEventCompat>` carrying progress; AndroidX Transitions offers `controlDelayedTransition`. This is §10.9's interruptibility requirement showing up as a platform API.

`The cross-platform note:` iOS has had an interactive, cancellable swipe-back since iOS 7. Android arrived at the same conclusion a decade later, which is a good argument that interruptible, previewing navigation is not a style choice but the correct behavior.

### 7.5 Navigation 3 – the back stack as state

Android's navigation story changed materially. **Navigation 3** has been stable since late 2025 and is actively developed through 2026, in both the stable and alpha tracks.

The change that matters: **the back stack is just a list you own.** Navigating means adding or removing items from that list, and the UI follows. Nav2's `NavHost` displayed only the topmost destination filling the available space, which is exactly why adaptive multi-pane layouts were painful.

What Navigation 3 adds for design:

- **Scenes** – a layout API that can render **multiple destinations at once**, so list-detail on a large screen and single-pane on a phone are the same navigation graph rendered two ways. This is what finally makes §8.4b's canonical layouts a navigation decision rather than a layout hack.
- **State scoped to back stack entries**, with optional ViewModel support, so a destination's state survives correctly as the stack changes.
- **It works in Compose Multiplatform**, which makes one adaptive navigation model viable across Android, iOS and desktop (§6.8).

`Why a designer should care about a navigation library at all:` the reason so many Android tablet apps show a stretched phone layout is that the previous navigation model made two-pane genuinely hard. When the platform makes the right thing easy, the design constraint disappears – and it is worth knowing which constraints are real and which were just tooling.

### 7.6 Bottom tab bars, specifically

- **Bottom tab bar for 3–5 primary destinations of equal importance.** Beyond five the targets get cramped and the labels truncate.
- **Pair every icon with a label.** Icon-only tab bars fail recognition for anything but the most conventional icons.
- Make the current tab **visually distinct**, and make **tapping the current tab scroll to top / reset that stack** – an expected behavior whose absence is felt immediately.
- Reserve the hamburger for **secondary or rare** items. It is a drawer for the long tail, not a primary navigation pattern.
- Bottom placement is thumb-zone placement (§9.1) – that is the real reason it beats a top tab bar on phones.

## 8. Adaptive layout, tablets, foldables

### 8.1 The canonical layouts

Material 3 defines **three** adaptive layout patterns, and they cover most app structures:

| Layout | Structure | For |
|---|---|---|
| **List-detail** | Two side-by-side panes – list, detail | Messaging, contacts, mail, media browsers – anything that is a list of items with more information behind each |
| **Supporting pane** | Primary area (~⅔) plus a secondary supporting pane | A main task with reference material, tools, or context alongside |
| **Feed** | Grid composition for browsing | News, photos, social |

**How list-detail adapts is the model for all of them:**

- **Expanded width:** both panes visible; selecting in the list updates the detail in place.
- **Medium and compact:** one or the other. The list fills the screen, selection navigates to the detail, back returns.

`This is the native equivalent of web §5.7's navigation structure question`, and it is better specified: the pattern names the *responsive behavior*, not just the desktop arrangement. Worth stealing for web work.

### 8.2 Android's adaptive quality tiers – and the Play Store consequence

| Tier | Bar |
|---|---|
| **Tier 3 – Adaptive ready** | Runs full screen or full window in multi-window on all devices, **not letterboxed**; critical flows completable, layout may be suboptimal |
| **Tier 2 – Adaptive optimized** | Layout optimizations for all sizes and configurations; enhanced external-input support |
| **Tier 1 – Adaptive differentiated** | *"A user experience not possible on small screen devices"* – multitasking, drag and drop, foldable postures |

**Two enforcement facts that turn this from advice into a requirement:**

- **Google Play shows a notice on the store listing when an app is not optimized for large screens.**
- **Targeting API level 37 requires the app to adapt to a variety of display sizes.**

`Tier 1's definition is the useful one for design:` not "works on a tablet" but **"does something a phone could not."** That is the right bar for whether a large-screen layout is worth building at all.

### 8.3 Foldables

**Two half-opened postures**, determined by fold orientation:

- **Tabletop** – horizontal fold. Bottom half becomes a natural control surface, top half a display.
- **Book** – vertical fold. Two facing pages.

**Constraints:** *"UI controls near the fold can be difficult for users to access, and text overlaying the fold can be hard to read."* Treat the hinge as an obstruction for content and a **boundary** for layout.

**The reframing worth keeping:**

> **"The hinge is a design element with its own states, animations and UX patterns – not just a layout trigger."** And: **"posture as a layout trigger vs. angle as an animation input."**

`That second phrase is the interesting one.` Hinge angle is a **continuous** input – the same class of thing as scroll position or drag distance (web §6.14). An interface that responds continuously to the fold angle is doing something genuinely native that no other form factor allows.

Compose Adaptive Layouts 1.2 adds **`reflow` and `levitate`** strategies specifically for outer/inner display transitions (§4.4).

### 8.4 iPad multitasking

**Stage Manager: up to four overlapping, resizable windows**, organized into workspaces, floating over a blurred Home Screen.

Design detail worth noting: **iPad apps gain no new chrome under Stage Manager** – no title bar, no traffic lights. Rounded corners, the existing three-dot multitasking menu, and that is it.

`Design consequence:` **an iPad app must look correct at an arbitrary window size with no window chrome to frame it.** Combined with Split View and Slide Over, the app can be almost any size at any moment – which makes size classes (§3.2) load-bearing rather than a tablet nicety.

**iPadOS 26 update.** The four-window cap is gone. Apps now open in freely resizable, movable, overlapping windows, and a Stage holds as many as the user wants. There is a proper windowed mode alongside full screen and Stage Manager, plus external-display window management.

`This raises the stakes on the same design consequence:` there is no longer any window size an iPad app can assume, and no upper bound on how small or oddly proportioned a window can be. Two rules follow. First, **every layout decision keys off size classes and available width, never off device identity.** Second, **state must survive resize** – a window dragged from full width to a third of the screen must not lose scroll position, selection or in-progress text. That is a state-restoration requirement dressed up as a layout one, and it is where most iPad apps actually fail.

### 8.4b Tablet layout, stated as patterns

Three canonical large-screen layouts cover most apps, and Android's guidance names them directly:

| Layout | Shape | Use |
|---|---|---|
| **List-detail** | List pane plus detail pane | Mail, messages, files, settings, anything with a collection of items |
| **Feed** | Multi-column grid of self-contained cards | Content browsing where items are equal and independent |
| **Supporting pane** | Main content plus a secondary pane | A canvas plus tools, a document plus comments, a map plus a list |

List-detail is the strongest of the three because it **uses width for context rather than decoration**: the user moves through options without losing their place, which is the actual difference a big screen makes.

The common failures on tablets, in order of frequency:

1. **A stretched phone layout.** One column of content at 1,024 pt wide with 300 pt of empty gutter on each side. This is the single most common tablet failure and it reads as an app that was not designed for the device.
2. **Line length ignored.** A single column that does stretch produces 140-character lines. Cap the measure and use the extra width for a second pane, not for longer lines.
3. **Navigation that did not adapt.** A bottom tab bar on a 13-inch iPad is reachable only by moving the whole hand. Bottom bar on compact, rail on medium, drawer or persistent sidebar on expanded.
4. **State lost on rotation or resize.** Covered in §8.4; it is the failure users actually notice.

`The rule that ties it together:` decide what the extra space is *for* before deciding what to put in it. More content, more context, or fewer navigation steps are the three legitimate answers. "Bigger" is not one.

### 8.5 Native onboarding

- **The first session loses 70–90% of users; median day-1 retention is ~25%.** Everything about the first run is fighting that number.
- **Total flow 60–120 seconds**, five stages. Longer loses people who have not yet committed.
- The pattern set that recurs: **a meaningful first action, progressive disclosure, and an empty state that feels filled** (web §7.2, web §7.11, web §7.16).
- **Passkey signup is now the default expectation** – no password creation, works across both platforms. *"Apps lacking this support are already behind on conversion benchmarks."*
- **Platform-native auth matters**: Google sign-on and Material patterns on Android. *"Shipping a web-wrapped flow on either platform reads as low-effort and hurts trust from the first screen."*
- Category-shaped approaches: habit apps → goal-first (Duolingo); content apps → personalization (Headspace); subscription → longer flow with social proof (Calm); complex tools → motion-guided (Notion).

`Cross-reference web §7.16:` the web finding was *time-to-first-value ≈ 2 minutes*. Native says **60–120 seconds for the whole onboarding**, which is stricter – because on native the user has already paid the install cost and expects to have arrived.

### 8.6 Pointer and keyboard on iPad

An iPad with a Magic Keyboard is a laptop in every way that matters to the user, and an app that ignores that is the one they stop using on the couch.

**Pointer.** iPadOS adapts the cursor to context and offers three built-in effects for interactive elements: **highlight** (a soft rounded rect behind the element), **lift** (the element rises and the pointer disappears into it) and **hover** (the element responds without the pointer morphing). The pointer and the target visually merge, which is what makes the interaction feel precise on a device with no fine pointing.

The failure is stated plainly in Apple's own material and by practitioners: **missing hover effects make the app feel broken with a trackpad**, because the user cannot tell what is interactive without tapping it.

**Keyboard.** Provide shortcuts for every frequent action. An iPad app with no shortcuts forces the user to lift a hand from the keyboard to the screen for every operation, which is exactly the friction a keyboard was attached to remove. The Cmd-key-held shortcut overlay is a discovery surface: it is the iPad's version of a menu bar, and it should be complete.

Also expected once a keyboard is attached: arrow-key navigation through lists, Tab between fields with a visible focus ring, Escape to dismiss, Return to confirm, and full support for the system text-editing shortcuts.

`The general rule:` on iPad, touch is the baseline and pointer plus keyboard is an additional input mode that must be supported without degrading the touch experience. Targets stay at 44 pt even when a precise pointer is present, because the same app runs without one five minutes later.

### 8.7 Orientation

The default position: **support both orientations unless there is a real reason not to.** Locking to portrait is common and mostly unexamined, and it fails users with a mounted device, a keyboard case, or a physical limitation that makes rotating easier than reaching.

The four honest patterns for handling a rotation:

1. **Reflow.** Same content, new proportions. Correct for reading, lists and forms.
2. **Change density.** Landscape gets a second column, or a list-detail split. This is the tablet answer applied to a wide phone.
3. **Change the layout's intent.** A video player goes full-bleed in landscape; a camera moves its controls to the short edge. The content becomes the whole screen.
4. **Lock, deliberately.** A camera, a game, or a genuinely single-purpose tool can lock, and should say nothing about it – a "please rotate your device" screen is a failure message, not a design.

Rules that matter:

- **If it did not scroll in portrait, do not make it scroll in landscape.** That inconsistency is disorienting, and it usually means the layout reflowed rather than adapted.
- **State must survive rotation** (§8.4). Scroll position, selection, in-progress text, keyboard focus.
- **Android guidance now discourages locking orientation on large screens** even for apps that lock on phones, because a locked app in a resizable window is broken by definition.
- **Landscape phone is a short screen**, not just a wide one. Anything anchored to the bottom competes with the keyboard, which occupies proportionally far more of the screen.

## 9. Touch, reach, input and gestures

### 9.1 Touch targets

| Standard | Size | Spacing |
|---|---|---|
| Apple HIG | **44×44pt** | ~10px between |
| Material | **48×48dp** | **8dp between, as a separate minimum** |
| WCAG 2.5.8 (AA) | 24×24 CSS px | Sub-24px passes if a **24px circle centered on the target does not overlap a neighbor's circle** |
| WCAG 2.5.5 (AAA) | 44×44 CSS px | – |

- Physical grounding: MIT Touch Lab measured the adult index fingertip at **8–10mm**, the finger pad at **10–14mm**, thumb contact ~**25mm**. 44–48px maps to roughly 9–11mm.
- Reported effect: correctly sized targets **cut mis-tap errors 60–80% and improve selection speed 30–50%**. `[secondhand, treat as directional]`
- **Spacing is a separate requirement from size.** A 48dp target with zero gap around it still fails in practice – adjacent targets get hit.
- **Fitts' Law is not sufficient for fingers.** The 2013 FFitts correction adds a second source of spread tied to the finger's absolute precision – small targets are worse than plain Fitts predicts.
- Thumb reach matters as much as size: the bottom third of a phone screen is comfortable, the top corners are not. Primary actions belong low on mobile, which is the opposite of the desktop instinct.

### 9.2 The thumb zone, precisely

Hoober (2013) is still the foundational data: **49% of people hold a phone one-handed**, and Clark found **75% of interactions are thumb-driven.**

**Three zones:** green (bottom-center, no strain) · yellow (mid-screen sides, requires stretch) · red (top corners, awkward or impossible).

The nuance most summaries omit: **the zone size varies enormously by thumb length.** In one study the large-thumb group could reach **>50% of the screen**, while small and medium groups reached only **~30%**, across all phone sizes. Designing for your own reach is designing for the largest hands.

- Primary actions go **bottom-center**. This is why bottom tab bars, bottom sheets and bottom-anchored CTAs win on mobile (web §7.35, §9.6, §10.2).
- **The green zone mirrors for left-handed users**, so avoid committing a critical control to one bottom *corner*.
- The top of the screen is for information, not action.

### 9.3 Safe areas and device cutouts

- `env(safe-area-inset-top/right/bottom/left)` **only resolves to non-zero when `viewport-fit=cover` is set** in the viewport meta. Without it, every value is `0px` and the whole mechanism silently does nothing.
- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`
- Applies to notches, punch holes, Dynamic Island, rounded corners and the home indicator bar. A fixed bottom bar without `padding-bottom: env(safe-area-inset-bottom)` sits under the home indicator on every modern iPhone.
- Pattern: `padding-bottom: max(1rem, env(safe-area-inset-bottom));` so there is sensible padding on devices with no inset.

### 9.4 Gestures

- **Discoverability is the central problem: gestures are always hidden.** *"The more your UI relies on gestures over visible buttons, the greater the possibility for confusion."*
- **The best line found on this: "creative gestures look impressive in demos but rarely scale – gesture-driven design works best when it feels boring."** Use the conventions; do not invent.
- Layering rule: **primary functions get visible controls; secondary get standard gestures; tertiary get long-press.** A gesture is never the only path to a primary action.
- Always provide a visible alternative – gestures are inaccessible to many motor-impaired users and invisible to screen reader users.
- Cross-platform conflict to watch (§2.2): left-to-right edge swipe means *back* on iOS and *switch tab* on Android.

### 9.5 System gesture conflicts

**The system owns the screen edges.** Android's back gesture is an inward swipe from **either** edge; swipe-up from the bottom is home; bottom-corner swipes summon the assistant. iOS reserves the left edge for back and the bottom for home.

**The conflict list is predictable, and it is exactly the components that want edge swipes:**

- Navigation drawers (`DrawerLayout`)
- Carousels (`ViewPager`)
- Sliders (`SeekBar`)
- **Swipe actions on list rows** (§21.2)

*"When swiping on the edge starts the built-in navigation, the system's back gesture can kick in and cause the app to fall into an inconsistent state."*

**The escape hatch: `View.setSystemGestureExclusionRects()`** (Android 10+) declares regions that need the touch input instead. **Use it sparingly and deliberately** – the system exists to be predictable, and reclaiming the edge is a cost paid by every gesture the user makes afterward.

`Design consequence:` **any interaction that lives within ~20dp of a screen edge is contested territory.** The safer design keeps draggable and swipeable surfaces inset from the edge – which is also better for the thumb (web §4.12) and for foldable hinge proximity (§8.3).

### 9.6 One-handed use – the numbers

Hoober's observation of **1,333 people**:

| Grip | Share |
|---|---|
| **One-handed** | **49%** |
| Cradled | 36% |
| Two-handed | 15% |

**And the finding that most summaries omit: grip is not static.** *"The way people hold their phones is not in a static state – users change the way they hold the phone, sometimes every few seconds."*

`Design consequence:` you are not designing for "the one-handed user" as a segment. **You are designing for everyone, some of the time** – which is why thumb-zone placement (§9 / web §4.12) is a default rather than an accommodation.

- One-handed reduces the interaction model to **one-finger gestures** with the rest of the hand gripping.
- **Thumb length varies reachability enormously** (web §4.12): 30% of the screen for small thumbs, >50% for large.
- Platform escape hatches exist – iOS **Reachability**, Motorola's shrink-screen – but relying on them is designing a workaround into the primary path.
- **Reposition frequently-used controls to the bottom.** This is the same conclusion as §7.2's bottom-centric shift, arrived at from ergonomics rather than convention.

## 10. Motion and haptics

### 10.1 Interruptibility

**"An animation must stay grabbable and reversible at any instant, not just at rest."**

An interface is fluid when it behaves like the physical world: **responds instantly, moves continuously, carries momentum, resists at boundaries, and can be redirected mid-motion.**

This is the deepest idea in the whole motion area. Most web animation is fire-and-forget: it starts, it blocks, and grabbing it mid-flight either does nothing or snaps. Native-feeling interfaces let you catch a sheet halfway through its dismissal and throw it back. **Springs are the tool that makes this natural, because they are inherently velocity-aware.**

Apple's stated preference: **quick and precise** over long and elaborate – brevity plus precision reads as lightweight and conveys information more effectively.

### 10.2 Bottom sheets and drawers

- **Modal** sheet blocks the rest of the UI; **non-modal / persistent** sheet lets the user keep working and can be expanded or collapsed but not fully dismissed.
- **Detents** are the snap points – fixed height, percentage of screen, or a mix. Multi-detent sheets (peek → half → full) are the pattern that makes a sheet feel native rather than like a modal that slid up.
- The real advantage over a dialog: a sheet **snaps to three of the four screen edges**, so it has far more usable space and lands in the thumb zone.
- Drawers are the same component from any edge.
- Pair with origin-aware motion (web §6.12) and interruptibility (§10.1) – a sheet you can catch mid-dismiss and throw back is the single clearest "this feels native" signal on the web.

### 10.3 Spring parameters, with real numbers

SwiftUI's `.spring()` defaults are **response 0.55, dampingFraction 0.825, blendDuration 0**. `.interactiveSpring()` defaults to **response 0.15, dampingFraction 0.86, blendDuration 0.25**, which is the one to use for anything a finger is currently touching.

What the two parameters actually do:

- **Response** is the time for one oscillation if damping were zero. Lower is faster and snappier, higher is slower and more deliberate. It is a duration in disguise, which is why it is easier to reason about than stiffness.
- **Damping fraction** controls how fast oscillation decays. 1.0 is critically damped (no bounce). Below 1.0 bounces; the lower, the bouncier.

A usable table:

| Intent | Response | Damping | Feel |
|---|---|---|---|
| Button press, toggle | 0.3 | 0.8 | Quick, slight bounce |
| Default UI transition | 0.55 | 0.825 | Balanced (the system default) |
| Modal or sheet presentation | 0.9 | 0.9 | Slow, deliberate, almost no bounce |
| Playful confirmation | 0.4 | 0.5 | Obvious bounce, use sparingly |
| Anything under a finger | 0.15 | 0.86 | Tracks input, settles instantly |

Compose expresses the same physics differently: `spring(dampingRatio, stiffness)` with mass fixed at 1, where `stiffness = k/m` and `dampingRatio = c / (2·√(k·m))`. `Spring.DampingRatioNoBouncy` is 1.0, `MediumBouncy` 0.5, `HighBouncy` 0.2; `StiffnessMedium` is 1500, `StiffnessLow` 200. `tween(durationMillis, easing)` covers the duration-based case. `AnimationSpec` is the common type so both live in the same API.

The web equivalent is `linear()` easing generated from a spring (web §6). Same physics, three notations. Learn the mapping once.

### 10.4 Motion tokens

Motion belongs in the token system alongside color and spacing. Material 3 publishes easing and duration as named tokens rather than raw values, and adjusts duration by distance travelled, element velocity, and whether a surface is entering or leaving.

A minimal usable motion scale:

| Token | Duration | Use |
|---|---|---|
| `motion.short` | 100–150 ms | State change in place: ripple, toggle, hover |
| `motion.medium` | 200–300 ms | Element enters or leaves, small movement |
| `motion.long` | 400–500 ms | Full screen transition, large surface |
| `motion.emphasized` | 500 ms+ | Hero moment, use once per flow |

Paired with easing tokens: standard (accelerate then decelerate) for movement within the screen, decelerate for entering elements, accelerate for exiting elements. Entering elements decelerate because they arrive and settle; exiting elements accelerate because they leave and do not need to be tracked.

Tokenizing motion is what makes "the app feels slow" a one-line fix instead of a sweep through every animation call site.

### 10.5 Gesture-driven motion, mechanically

The three properties that make an interaction read as fluid, in the order they matter:

1. **Interruptible.** The animation can be stopped, redirected or restarted at any instant, not only at rest. Apple's WWDC material has said this since 2014 and it has not changed.
2. **Velocity-preserving.** When a drag ends, the gesture recognizer's velocity (in points per second) becomes the spring's initial velocity. It must be normalized by the remaining distance before it is handed to the animation, or fast flicks overshoot wildly.
3. **Rubber-banded at boundaries.** Movement past a valid limit is resisted rather than blocked. The value slips instead of stopping, which communicates "there is nothing past here" without a hard wall.

Rubber banding is a resistance curve, not a clamp. The classic form is `offset = (1 - 1/(distance * c / dimension + 1)) * dimension` with `c` around 0.55, which is what UIScrollView effectively does. The important part is that it never reaches a hard stop and it releases back with a spring.

The failure mode this fixes: an animation that ignores the finger's velocity feels like the interface is arguing with you. That is the precise mechanism behind "this app feels off" complaints that users cannot articulate.

### 10.6 Reduce Motion, precisely

The setting is a real user preference with a real prevalence, and honoring it is not optional on either platform.

- **iOS:** Settings → Accessibility → Motion → Reduce Motion. There is a second, separate toggle underneath: **Prefer Cross-Fade Transitions** (iOS 14+), which replaces slide transitions with fades. SwiftUI exposes `\.accessibilityReduceMotion`; UIKit exposes `UIAccessibility.isReduceMotionEnabled` and `UIAccessibility.prefersCrossFadeTransitions`.
- **Android:** Settings → Accessibility → Remove animations, surfaced through `Settings.Global.ANIMATOR_DURATION_SCALE` being 0. Compose does not read it automatically for custom animations.

What to do when it is on, in order of preference:

1. **Cross-fade instead of slide, scale or parallax.** A fade still communicates the change and carries no motion.
2. **Keep the duration, drop the movement.** Removing the transition entirely makes state changes hard to follow; the animation was doing informational work.
3. **Never remove feedback.** A button that stops confirming a press is worse, not more accessible. Replace movement with a color or opacity change.

The distinction that matters: **motion that conveys information stays, motion that conveys personality goes.** Parallax, spring bounce, zoom-through transitions, autoplaying background video and large-area movement are the specific things that trigger vestibular symptoms.

Cross-platform note: this is the same preference as the web's `prefers-reduced-motion` (web §6), and it is worth wiring one internal flag from all three sources so the rule is applied once.

### 10.7 Motion tooling – Rive vs Lottie

The two formats are not competitors so much as different answers to "does this animation need to know anything".

| | Lottie | Rive |
|---|---|---|
| Nature | A **file format** (JSON) you export to | A **tool plus runtime** with a state machine |
| Authored in | After Effects, Cavalry, Rive, Lottie Creator | Rive's own editor |
| Runtime behavior | Plays a timeline | Reacts to input and application state |
| Typical size | 5–20 KB for an icon | Comparable, plus the runtime |
| Native targets | iOS, Android, Flutter, React Native, web | iOS, Android, Flutter, React Native, web, game engines |
| Cost | MIT-licensed player | Editor is a product |

**Use Lottie** for anything that plays start to finish and never branches: an animated icon, an empty-state illustration, a success checkmark, a loading loop.

**Use Rive** when the animation has states: a loading indicator that morphs into success or failure, a toggle that tracks a drag, an icon whose animation depends on data. The state machine is the actual differentiator, because otherwise the branching logic ends up hand-written in the app and desynchronizes from the design.

**Use neither** for standard UI transitions. A sheet presentation, a list reorder or a button press should be the platform's own animation system (§10.3), not an imported asset. The mistake worth naming: shipping a Lottie for something the platform animates natively costs a dependency, a runtime, a file, and a result that will not match the rest of the OS.

The 2026 workflow shape that recurs: prototype in Figma, author production motion in Rive, and hand the developer an asset plus a state contract rather than a video to reimplement.

### 10.8 Haptics

- **Use OS defaults for standard flows.** Users already know the system success / failure / neutral patterns; inventing your own costs recognition and gains nothing.
- **Less is more.** Over-vibration is annoying, numbing, and drives users to disable haptics entirely – losing the channel permanently.
- **Consistency creates meaning.** A stable trigger→feedback mapping is what lets the pattern be learned.
- Sync haptics with the visual and audio events. Multimodal alignment is what makes it read as one event rather than three.
- Always user-controllable – accessibility, battery, and preference.

### 10.9 Haptics, at the API level

§21.3 covers the three iOS generators. This is the layer below, for when the standard patterns are not enough.

**iOS – Core Haptics (iOS 13+).** Direct control of the Taptic Engine through two parameters, both 0 to 1:

- **Intensity** – how strong. Note that the intensity curve is **logarithmic, not linear**, so 0.5 is not half of 1.0 and interpolating linearly gives the wrong result.
- **Sharpness** – how crisp. Low sharpness is a dull thud, high sharpness is a sharp tick. This is the parameter that carries most of the character.

Plus `attack`, `decay` and `release` for envelope shaping, and two event types: transient (a tap) and continuous (a sustained buzz). Patterns can be authored as **AHAP** files – JSON with an array of events at times relative to the pattern start – which means haptics can be designed as an asset and handed over like a Lottie file rather than written as code.

**Android – two layers, and the choice between them is the whole design decision:**

| API | Use |
|---|---|
| `HapticFeedbackConstants` | **Default choice.** Action-oriented constants (`CONFIRM`, `REJECT`, `LONG_PRESS`, `GESTURE_START`) that map consistently to system behavior across devices |
| `VibrationEffect` | Composition from primitives when the action has no matching constant |

Android's own guidance is explicit: **if an action is covered by a `HapticFeedbackConstants` value, use it.** That is what keeps an app's haptics consistent with the rest of the device, and it is stated as an accessibility consideration rather than a style one.

The `VibrationEffect` primitives, when needed: short ones are `CLICK`, `TICK`, `LOW_TICK`; the shaped ones are `SLOW_RISE`, `QUICK_RISE`, `QUICK_FALL`, `THUD`, `SPIN`. Newer devices support **envelope effects** for continuous control over intensity and frequency; check `areEnvelopeEffectsSupported` and design a fallback, because hardware varies enormously.

**Android's three stated design principles**, which generalize past Android: favor rich and clear haptics over buzzy ones, be consistent with both the system and the app, and scale the treatment by frequency of use and importance.

`The design rule underneath all of it:` custom haptics are worth authoring for the two or three moments that define the product – the capture, the send, the completion – and standard constants everywhere else. An app with fifteen bespoke haptic patterns has no haptic language, only noise.

### 10.10 Sound

Sound is the third feedback channel after visual and haptic, and it is the one most apps get wrong by using too much of it.

- **Earcons are short, distinct, and mapped consistently.** The camera shutter is the canonical example: it is short, unmistakable, and it means exactly one thing.
- **Keep them under roughly 400 ms.** A sound longer than the action it confirms makes the interface feel slower than it is.
- **Make sounds differ from each other more than seems necessary.** Phone speakers are small and the listening environment is usually noisy, so subtle timbre differences do not survive.
- **Sound is confirmation, not notification.** Use it where the user has just acted (send, capture, complete, error) and not to demand attention.
- **Respect the silent switch and the system volume**, always, and give an in-app toggle. On iOS this means choosing the right audio session category; a UI sound that plays through the ringer switch is a bug users notice immediately.
- **Align sound, haptic and visual to the same instant.** Misalignment by even 50 ms reads as three separate events instead of one, which is the same principle as §10.5's interruptibility.
- **Sound also serves accessibility**, giving confirmation to users who cannot see the visual change. That argues for having earcons at all, not for having many.

The general rule that survives every source: pick three to five sounds for the whole app, use them consistently, and default them off for anything that is not a direct response to a user action.

---

## Carried over from the web study

These apply to native work unchanged and are **not duplicated here** – read them in `design-web/study-book/learning.md`:

| Area | Why it transfers |
|---|---|
| §1 Typography | Scale, measure, leading, tracking, hierarchy mechanics, optical sizing – all platform-agnostic. Only font loading and licensing are web-specific |
| §2 Color | OKLCH, scale construction, dark mode, tokens, color vision deficiency, contrast standards |
| §3 Space, scale and layout | 8pt grid, internal ≤ external, Gestalt, optical alignment, nested radius, balance |
| §5 Hierarchy and composition | Visual weight, scale contrast, squint test, cards, page rhythm |
| §6 Motion (rest of section) | The asymmetry rule, duration bands, springs vs bezier, microinteraction structure, celebration, stagger, origin-aware motion, Disney principles, feedback latency, easing as emotion, progress indicators |
| §7 Interaction and state | The five states, empty states, errors, validation timing, disabled controls, destructive actions, button hierarchy, forms, progressive disclosure, dashboards, AI chat, onboarding, search, settings, dark patterns, tables, dates, uploads, notifications, permissions |
| §8 Accessibility | WCAG criteria, focus, semantics, cognitive accessibility, non-text contrast, icons and labels, older adults, live regions – **but native AT tooling is not covered** |
| §10 Performance | Perceived vs actual, budgets, low bandwidth, image placeholders. Core Web Vitals do not transfer |
| §12 Style directions | AI slop, the flat/depth pendulum, style directions with costs, art direction |
| §13 Craft | Layered shadows, icons, microcopy, amateur tells, the small-details list |
| §14 Process and taste | Norman's levels, delight, cognitive load, testing, critique, rationale, Nielsen's heuristics, component proportions, variable-length content, numbers and dates, inclusive imagery, rule-breaking, content-first, five-second test |
| §15 Brand corpus | All 449 prohibition rules, the emphasis escalation ladder, single-voltage law, the shadow finding, the "reads as" vocabulary |
| Registers | 26 myths, 8 conflicts, 23 trade-offs, 27 verification checks – the majority are platform-agnostic |

## 11. Native accessibility

### 11.1 Which rules actually apply

**WCAG applies to native apps** – but there is no native-specific normative standard. W3C published **WCAG2Mobile** (*Guidance on Applying WCAG 2.2 to Mobile Applications*, first draft note May 2025) to bridge the gap. In practice teams apply WCAG 2.2 plus Apple's and Google's accessibility API documentation, and record the result in a VPAT.

**The criteria that are specifically mobile-shaped:**

| Criterion | Requirement |
|---|---|
| **1.3.4 Orientation** | Support **portrait and landscape** – do not lock unless essential |
| **1.4.10 Reflow** | No horizontal scrolling to read |
| **1.4.11 Non-text Contrast** | UI components **3:1** (web §8.15) |
| **2.5.1 Pointer Gestures** | **Single-tap alternative** for any multipoint or path-based gesture |
| **2.5.2 Pointer Cancellation** | Actions fire on **up**, not down, so a mistaken touch can be dragged away and canceled |
| **2.5.4 Motion Actuation** | Anything triggered by shake or tilt must have a **UI alternative and be disableable** |
| **2.5.7 Dragging Movements** | **Single-tap alternative to any drag** |
| **2.5.8 Target Size** | 24×24 minimum |

`2.5.1, 2.5.7 and 2.5.4 are the ones with no real web equivalent`, and they are exactly the gestures native design reaches for first (web §6.26). **Every swipe-to-delete needs a tappable delete. Every drag-to-reorder needs a move-up/move-down. Every shake-to-undo needs an undo button.**

Deadlines in play: the **European Accessibility Act** applied from late June 2025; US **HHS requires WCAG 2.1 AA for healthcare apps by 11 May 2026.**

### 11.2 The reading-order divergence

**iOS VoiceOver reads left-to-right, top-to-bottom by screen position. Android TalkBack and the web follow the view hierarchy / DOM.**

That is a genuine platform difference with design consequences:

- On iOS a **visually** correct layout is usually announced correctly, and an absolutely-positioned element in the wrong place is announced in the wrong place regardless of code order.
- On Android, view-hierarchy order rules – so the web problem of visual order diverging from source order (web §8.12) **exists on Android and largely does not on iOS.**
- Both platforms: *"the easiest way to avoid focus-order issues is to make sure elements are displayed in the same order as they appear in the view hierarchy."*

Rules that hold on both:
- **Focus order follows visual reading order**, and changes are **announced**, not silent.
- **On navigating to a new screen, focus starts from the same position every time.** Consistency here is what makes an app navigable without sight.
- Move focus deliberately into a modal, and return it on dismiss (web §7.9).
- **Prefer the OS default reading order** unless there is a specific reason to override.

### 11.3 The iOS toolkit

- **Labels, hints, traits.** Traits describe what an element *is*; for custom controls, combine the relevant traits with the superclass's via OR.
- **`accessibilityElement(children: .combine / .ignore)`** groups a composite into one focusable item that is spoken once – the iOS equivalent of Compose's `mergeDescendants` (§4.5).
- **`accessibilityAction(named:)`** exposes an action as a named custom action. **This is the mechanism that makes gesture-driven UI reachable** – a swipe action becomes a named action in the VoiceOver rotor.
- **`accessibilityRotor`** (SwiftUI 3+) creates a **custom navigation path** through a specific collection of elements – the native equivalent of landmark navigation.
- Standard controls are accessible by default; custom-drawn ones are not.

The rotor is the part designers most often miss: VoiceOver users navigate **by category** – headings, links, form controls, custom rotors – not by swiping through everything. **An app with no headings and no rotors is an app that can only be traversed linearly**, which is the native version of a page with no heading structure (web §8.4).

### 11.4 Motor accessibility – Switch Control and Voice Control

**Switch Control** lets someone operate the device with a single switch: an adaptive button, a head movement, or a voiced/voiceless sound. Two modes: **item scanning** (steps through elements) and **point scanning** (select a screen position when item scanning cannot reach something).

The useful heuristic:

> **"By making an app 100% accessible to VoiceOver, it will likely already be great for Switch Control."**

But with one addition that matters: **Switch Control users cannot perform gestures at all**, so interactions must be exposed as **`accessibilityAction(named:)` custom actions**. A swipe-only affordance is unreachable no matter how good the labels are.

**Voice Control** operates the device entirely by speech. It relies on elements having names that can be spoken – so **an unlabeled icon button is not just unreadable, it is unclickable.**

The rule that covers both: **everything must be reachable without touch**, via switch, external keyboard, or voice.

### 11.5 The iOS display settings a design has to survive

| Setting | Effect on design |
|---|---|
| **Larger Text (Dynamic Type)** | §3.6 – 12 sizes including AX1–AX5 |
| **Bold Text** | **Re-weights every font on the device.** A design whose hierarchy depends on the gap between regular and semibold can collapse |
| **Increase Contrast** | Raises foreground/background separation; semantic colors adapt automatically, custom hardcoded ones do not |
| **Reduce Transparency** | **Removes blur and translucency.** Directly targets Liquid Glass (§3.3) – and there is documented user demand for exactly this. **Any hierarchy carried only by translucency disappears** |
| **Reduce Motion** | §11.6 |
| Color Filters / Smart Invert | Custom-drawn content and images can invert badly |

`The design test:` turn on **Bold Text + Increase Contrast + Reduce Transparency** together and check whether the interface still has a hierarchy. If depth was doing the work, it is now gone – which is the native version of web §8.6's forced-colors check.

### 11.6 Reduce Motion

Every major OS ships it – **iOS since 2013**, macOS, Windows, Android, GNOME. iOS: *Settings → Accessibility → Motion.* Android 9+: *Settings → Accessibility → Remove animations.*

The framing worth keeping, because it is the strongest version found anywhere:

> *"Not respecting that setting creates inconveniences, but sometimes it's about real, life-affecting symptoms. For someone with a vestibular disorder, migraine with aura, or Ménière's disease, animations can trigger real nausea, dizziness, or a headache."*

Implementation, same as web §6.10: **decorative animation eliminated entirely; essential feedback animation reduced to instant or minimal motion** – not removed, because the feedback is still needed.

### 11.7 Testing – tools and the one check that beats the rest

**Automated first, manual second.**

- **iOS: Xcode Accessibility Inspector** (Xcode → Open Developer Tool). Inspection mode shows any element's **label, traits, hints and frame**; **Run Audit** scans the current screen for **contrast issues, undersized touch targets and missing labels.**
- **Android: Accessibility Scanner** (Settings → Accessibility → Accessibility Scanner). Flags **labels, contrast, touch targets, clickable elements.**
- **Both are structural only.** *"The scanner cannot evaluate whether a label is contextually appropriate."* Same 30–40% ceiling as the web (web §8.10).

Then manual: **every core journey with VoiceOver and with TalkBack**, on **real devices, not emulators.**

**And the single highest-yield check found in the entire native study:**

> **"Testing at the worst case – smallest supported screen × largest font setting – catches more bugs than any other accessibility check, because it stresses the entire layout system at once."**

That is the native equivalent of the 400%-zoom reflow test (web §4.6), and it belongs at the top of the verification register.

`[sources: W3C WCAG2Mobile first draft note (May 2025); Allyant, accessibilitychecker.org, marconius.com WCAG 2.2 for native apps; Apple WWDC20 "App accessibility for Switch Control", WWDC21 "SwiftUI Accessibility: Beyond the basics"; Swift with Majid accessibility rotors; Orange digital accessibility guidelines (iOS); Deque "Intro to iOS Accessibility Inspector"; Android Accessibility Scanner docs; Eevis Panula "Android, Animations and Reduced Motion"; Apple Support display and text size settings; Maxability reading order]`

**The rest of the tool inventory, for when the built-in scanners are not enough:**

- **iOS:** `XCUITest` can assert accessibility labels and traits in CI, so a missing label becomes a failing test rather than an audit finding. `UBKAccessibilityKit` runs audits on-device without Xcode, which is useful for QA who do not build the app. Reveal inspects the element tree visually.
- **Android:** **Accessibility Insights for Android** (free, open source) goes further than the Scanner and produces a shareable report. `UI Automator` covers the automated side.
- **Cross-platform:** Appium-based checks, and commercial scanners that run on a build with no source access, which is the practical option for auditing a third-party or legacy app.

`The rule that does not change:` automated tooling tops out around 30–40% of real issues, and everything it cannot see – whether a label is *right*, whether the reading order makes sense, whether a flow is completable – needs a person with the screen reader on. Put the automated checks in CI so the mechanical failures never reach review, and spend the human time on the journeys.

### 11.8 The European Accessibility Act – the deadline that has already passed

**Enforceable since 28 June 2025** for new digital products and services. **Existing services have until 28 June 2028 – unless they undergo a major update after June 2025, in which case compliance is immediate.**

| | |
|---|---|
| **Standard** | **WCAG 2.1 Level AA**, audited against **EN 301 549 v3.2.1** |
| **Scope** | Websites, **mobile apps**, electronic documents, self-service terminals. **Applies to non-EU businesses serving EU users** |
| **Exemption** | Under **10 FTE** or **under €2M** annual turnover |
| **Enforcement** | **First lawsuits filed in France, November 2025.** Netherlands and other member states active in 2026 |
| **Fines** | **€60,000 (Ireland) up to ~€900,000 (Sweden)** |

`Two things follow for design work:`

1. **"Major update" triggers immediate compliance.** A redesign is precisely a major update – so **any redesign brief for an EU-facing product is an accessibility brief**, whether the client framed it that way or not. Say so early.
2. **Self-service terminals are in scope**, which pulls kiosk and embedded HMI work (§19.5) into the same regime.

Alongside the US HHS healthcare deadline of **11 May 2026** (§11.1), accessibility in 2026 is a legal schedule rather than a quality aspiration.

### 11.9 What iOS 26 added, and the one that changes store listings

- **Accessibility Nutrition Labels** on App Store product pages, declaring which accessibility features an app actually supports: VoiceOver, Voice Control, captions, larger text, sufficient contrast, reduced motion, differentiate without color. **Voluntary now, and Apple has stated the intent to make it mandatory.**
- **Accessibility Reader**, a system-wide reading mode launchable from any app, with font, color and spacing controls, built into the Magnifier app.
- **Braille Access**, turning the device into a braille note-taker.
- Live Listen with Live Captions integration.

`Why the labels matter more than the features:` an accessibility declaration on the store page turns accessibility into a **visible, comparable product attribute** rather than an internal quality bar. A competitor declaring VoiceOver support next to an app that cannot declare it is a conversion difference. This is the same mechanism that made privacy nutrition labels change behavior (§16.12), and it is likely to move the industry faster than the EAA deadline did.

The practical consequence for a design process: the label is a list of claims that have to be true, which means each one becomes a testable requirement rather than an aspiration. Build the list into the definition of done.

### 11.10 Older adults

A large, growing, and routinely ignored user group. The systematic reviews converge on the same barriers and the same fixes.

**The barriers, in the order research finds them:** small type, complex navigation, unintuitive interaction models, cognitive overload, and varying digital literacy.

**The design responses:**

- **Larger default type and larger targets**, and this means designing for it rather than relying on Dynamic Type. A layout that only works at the default size and breaks at 200% has not solved the problem, it has moved it to the user's settings.
- **Simplify navigation depth.** Flat is better than nested. A path that requires remembering where you came from is the one that fails first.
- **Error tolerance over error prevention.** Confirmations before destructive actions, undo everywhere, and forgiving input handling (accepting a phone number in any format rather than rejecting it).
- **Voice interaction** is repeatedly identified as effective, both as input and for reading content back.
- **Reduce reliance on gesture-only paths.** Every gesture needs a visible alternative (§9), and this group is where that rule stops being theoretical.
- **Stable layout.** Controls that move between screens or change with context cost this group more than any other.
- **Co-design.** Participatory methods measurably improve usability and satisfaction, which is the strongest finding in the literature and the one most teams skip.

`The overlap worth naming:` almost every item on this list also helps users with motor impairments, users in a hurry, users on a bumpy train, and users unfamiliar with the product. Designing for older adults is one of the highest-leverage accessibility investments there is, and it is rarely framed as accessibility at all.

### 11.11 Non-text contrast, which is the criterion native apps fail most

WCAG 1.4.11 requires **3:1 against adjacent colors** for user interface components and meaningful graphics. It is the criterion that catches the things a text-contrast checker misses.

What it covers on a native screen:

- **Component boundaries**: the edge of a text field, the track and thumb of a switch or slider, a checkbox outline, the border of a card if the border is what identifies it.
- **Icons that carry meaning on their own.** A meaningful icon needs 3:1; a decorative one, or one that merely repeats adjacent text, does not. The test is whether removing it loses information.
- **Every interactive state.** Focused, pressed, selected and checked each need 3:1 individually, not just the resting state.
- **Focus indicators**, which is where custom-styled controls fail almost universally.

Exempt: **disabled components**, explicitly.

`Where native apps break it in practice:`

- A gray-on-white text field border, which is the single most common failure.
- A selected tab distinguished only by a tint that is under 3:1 against the unselected ones.
- A toggle whose "off" track is barely visible against the background.
- Glass and translucent chrome (§1.1), where the effective contrast depends on whatever content is behind it – so it has to be measured against the real backdrop, not against a flat approximation.

The automated scanners (§11.7) catch some of this and not all of it, because they cannot always tell a decorative icon from a meaningful one. It belongs on the manual list.

## 12. Typography on native

### 12.1 Use the system font unless brand overrides it

**SF Pro on iOS, Roboto on Android.** Both are variable (SF Pro, Roboto Flex).

Three concrete reasons, not aesthetic ones:

1. **Zero latency** – nothing is fetched.
2. **Accessibility scaling for free** – Dynamic Type and Android font scaling work without any work.
3. **It reads as native**, and SF Pro specifically is *"optically adjusted for screen rendering at small sizes"* (web §1.6).

`The bar for a custom face on native is higher than on the web`, because you are giving up platform accessibility integration, not just adding a download. If brand requires it, wire it to `UIFontMetrics` (§3.6) and Android's scaling explicitly – otherwise the app silently opts out of the most-used accessibility feature on both platforms.

### 12.2 Android 14 changed font scaling, and it matters for layout

**Maximum font scale went from 130% to 200%, and scaling is now non-linear.**

`FontScaleConverter` applies a deliberate curve:

| Size | Growth at the 2.0× setting |
|---|---|
| **8sp** | **can exceed 200%** |
| **57sp** (display) | **only ~112%** |

*"Small text scales aggressively while large display text barely grows – this prevents display-size text from becoming unusably large while ensuring small text remains readable."*

**And the API consequence: `fontScale` is now informational only.** *"Fonts are no longer scaled with a single scalar value."* Any layout math that multiplies by `fontScale` is wrong on Android 14+.

`This is a genuinely elegant solution to the problem web §4.6 has never solved:` browser zoom scales everything uniformly, so large headlines become absurd before small text becomes comfortable. **Android scales by need rather than uniformly.** Worth recording as a design idea, not just an Android fact.

**`sp` for text, `dp` for everything else.** *"With sp you respect the user's preference; with dp you block scaling, which can impair accessibility even if it stabilizes the design."* The temptation to use `dp` for type to protect a layout is exactly the failure mode.

### 12.3 Mobile measure is not web measure

| Context | Characters per line |
|---|---|
| Desktop / web body | **50–75** (web §1.3) |
| **Mobile portrait** | **30–50**, commonly cited as **35–50** |

The reason is arithmetic rather than preference: *"on most mobile devices 60 characters extends beyond the boundaries of the screen. Lines with 60 characters on mobile screens usually require fonts that are too small to be easily readable."*

`So the web rule does not port.` Chasing 65ch on a phone forces type below the 16px floor. **Accept a shorter measure on mobile and keep the type size** – the opposite trade from the web.

Other native-specific values: minimum 14–15px, line-height ≥1.3, 1.5 for body. And *"test on real devices – emulators don't replicate the reading experience of a phone held at arm's length."*

### 12.4 Custom fonts – licensing is a shipping blocker

- **Bundling a font in an app binary is distribution, and distribution triggers license obligations.**
- **A desktop license does not grant the right to embed a font in a mobile app.** Foundries sell app licenses separately.
- **OFL (Google Fonts and most open-source faces) permits bundling in commercial apps free of charge.** It is the safe default.
- Flutter needs the actual font files because it draws its own text.

`Same warning as web §1.17, different mechanism:` on the web the trap is a pageview meter; on native it is a license category that simply does not cover the use. **Check the license category before choosing the typeface, not after.**

### 12.5 Localization is a layout constraint, not a string swap

The number to design against: **German runs 30–50% longer than English, and the working rule is to design for 40% expansion.** Short strings expand worst – a 5-character English label can double.

Where it breaks first, in order: tab bar labels, buttons with fixed width, form labels in a two-column layout, and modal titles.

The design responses:

- Let containers size to content, never fix a width to fit the English string.
- Allow two lines on labels that can wrap, and choose truncation deliberately for those that cannot.
- Avoid concatenating sentences from fragments. Word order changes between languages and the fragments will not reorder.
- Leave space around icon-plus-label pairs, because the label grows and the icon does not.
- Test with German or Finnish for length and with Japanese for the opposite problem (much shorter, so a layout tuned for expansion looks empty).

**RTL is a layout-system decision made on day one.** Retrofitting Arabic or Hebrew into an app built LTR-only typically means rebuilding the layout, which is why every team that does RTL well builds locale-aware layout from the start.

What mirrors and what does not:

| Mirrors | Does not mirror |
|---|---|
| Layout direction, leading/trailing edges | Clocks, and time-of-day icons |
| Back and forward chevrons, arrows | Checkmarks |
| Progress bars and sliders (fill from the right) | Media transport controls (play still points right) |
| List and grid reading order | Logos and brand marks |
| Transition direction (push comes from the left) | Numbers themselves, in most locales |

Use logical edges everywhere: `leading`/`trailing` in SwiftUI, `start`/`end` in Compose and Android XML, `margin-inline-start` on the web. Hardcoded `left`/`right` is the single mechanism by which RTL support breaks.

The interaction with Dynamic Type (§12.2) is the real trap: **a long translation at the largest accessibility text size is the worst case, and it is the case nobody tests.** That combination is where labels overlap, buttons clip and layouts collapse. It belongs in the device matrix (§21.11) as an explicit configuration.

### 12.6 Variable fonts on native

Both platforms ship variable system fonts and both use them for things a static font cannot do.

- **SF Pro** is optically tuned and the system switches between **SF Pro Text** and **SF Pro Display** by size automatically. That switch is why Apple type looks right at both 11 pt and 34 pt without manual tracking, and why substituting a static custom font at every size usually looks worse than the system font.
- **Roboto Flex** exposes **12 axes**, including optical size (`opsz`) and grade (`GRAD`).

The axis worth understanding is **grade**. It changes apparent stroke weight **without changing character widths**, so text can be made optically heavier at small sizes, on dark backgrounds, or in bright ambient light **with no reflow**. Weight cannot do that: going from 400 to 500 changes advance widths and the layout moves. Grade is the correct tool for compensation; weight is the tool for hierarchy.

For a custom family, the free variable fonts that actually include an optical-size axis are a short list: **Inter, Source Sans 3, DM Sans**. Most variable fonts ship weight and width only.

`Practical guidance:`

- **Use the system font unless brand genuinely requires otherwise** (§12.1). The optical tuning is the reason, not laziness.
- If shipping a variable font, ship **one variable file, not five statics**. It is usually smaller and it makes intermediate weights available.
- **Subset it.** A full-coverage variable font is a large asset and app size is a conversion variable (§20.8).
- On dark backgrounds, text appears heavier than on light – if the family has a grade axis, drop the grade slightly rather than dropping the weight.

## 13. Color and theming

### 13.1 Tonal elevation – Material's answer to dark-mode depth

**Material 3 represents elevation primarily through tonal color overlays rather than shadows.** An elevated surface receives a **surface tint drawn from the primary color slot**; higher elevation means a more prominent tone.

- In M2 the dark-theme fix was a semi-transparent **white** overlay. **M3 replaced it with a tint from the brand's own primary color**, in both light and dark.
- Background stays constant; **surface changes with elevation.**

`This is a better solution than either web practice or M2.` Web §2.4 says use surface lightness for dark-mode elevation; Material goes further and makes the elevation tint **carry brand color**, so depth and identity are the same mechanism. It also independently confirms web §15.2 – the best systems get depth from **surface**, not shadow.

### 13.2 Theming architecture

**Three tiers, same as web §2.5:** global (raw values) → alias/semantic (`color-primary`) → component (`button-background-color`).

- Tokens stored platform-agnostically (JSON/YAML), transformed by **Style Dictionary** into **CSS for web, XML for Android, Swift for iOS**. **Diez** is the native-ergonomics alternative with autocomplete and package-manager distribution.
- The important native property: *"design tokens map to the native OS APIs, which allows implementation across both iOS and Android **while preserving the native OS components, behaviors and interactions**."*

`That is the resolution to §6.6's consistency-vs-idiom debate, expressed as architecture:` **share the token layer, keep the component layer native.** Brand travels; behavior stays platform-correct.

**White-label / multi-brand mechanics:**
- **iOS: Xcode Targets** with per-brand assets and `Info.plist`.
- **Android: product flavors** with their own `applicationId`, resources and build config.
- **Compose Multiplatform:** per-client app modules depending on a shared white-label library, theming injected.
- Same caution as web §14.11: theme **radius, shape, spacing density and motion character** too, or every brand looks like the same app repainted.

### 13.3 High-contrast settings

- **iOS: Increase Contrast.** System colors adapt automatically; custom colors need a **High Contrast variant in the Color Set** (`highContrastLight` / `highContrastDark`).
- **Android: High Contrast Text.**

**The caveat that matters, stated plainly by Deque:**

> **"Even though it's great to support these settings, you cannot use them as a way to meet WCAG criteria. Supporting Increase Contrast does not make your app WCAG compliant."**

**Design at adequate contrast by default; treat the accessibility setting as an enhancement on top, never as the compliance path.** Exactly parallel to web §8.6 – forced-colors support does not excuse a low-contrast design.

### 13.4 RTL on native

**Mirror the whole interface, not the text alignment**: navigation bar, back chevron, progress indicators, directional icons, carousel scroll direction.

- **Use `leading` / `trailing` (iOS) and `start` / `end` (Android) instead of `left` / `right`** – the native equivalent of CSS logical properties (web §9.5). Costs nothing when the app is English-only.
- What does **not** mirror: logos, clocks, media playback controls, photographs, numbers.
- The caution worth carrying: *"'Mirroring' the layout is a common approach, but it might result in strange UIs for native speakers."* **Mechanical mirroring is the starting point, not the answer** – it needs review by someone who reads the language.

### 13.5 Locale formatting

**Never hand-format a date, number or currency.**

- **iOS: `DateFormatter`, `NumberFormatter`** – locale-aware by default from the device locale.
- **Android: `DateFormat`, `NumberFormat`**, with the locale's country code selecting the currency.
- Currency symbol position, decimal and thousands separators all vary (web §14.30).
- **Native date pickers automatically localize numerals, labels and layout direction** – a strong argument for the platform picker over a custom one (§web 7.29).

*"Hardcoded date formats and number separators are among the most common i18n mistakes."*

### 13.6 Dark mode is now the majority case on mobile

| Measure | Figure |
|---|---|
| Android users running dark mode | **~82%** (permanently or scheduled); ~10% alternate |
| iOS adoption | **55–70%** |
| Top App Store apps supporting dark mode | **92%** |
| Overall stated preference | **~68%** |
| **Preference in the evening** | **87%** |
| **Preference at night** | **>91%** |

`[secondhand survey aggregations – treat the exact figures as directional, the pattern as solid.]`

**Design consequence, and it is a real shift from the web:** on mobile, **dark mode is the default state for most users most of the evening.** Designing light-first and adding a dark theme afterwards inverts the actual usage. The Apple guidance from §3.9 – *"treat dark mode as a first-class citizen from the first Figma frame, it costs far less than fixing it later"* – is backed by these numbers.

Also note the **OLED battery argument** compounds it (§17.6): dark mode is not only preferred, it is cheaper to run.

### 13.7 Theming in the cross-platform toolkits

**Flutter – `ThemeExtension`.** Strongly-typed custom properties attached to `ThemeData`, participating in light/dark and context resolution like first-class theme data.

> *"A mature theme system separates design intention (tokens) from implementation (ThemeData) and consumption (widgets). Done well, design can evolve without refactoring UI code."*

The stated workflow is the correct one and generalizes: **define tokens → map them to `ThemeData` / `ColorScheme` / extensions → override only where the design specifically calls for it.** That last clause is §6.5's *"deviation is explicit and greppable"* restated.

**React Native – `StyleSheet.create`.** Still the recommended default in 2026: styles defined outside components become opaque references, which is the performance path. *"Establishing a design system early prevents inconsistencies and makes the app feel cohesive"* – reusable theme constants plus a shared component library.

`Both confirm the same architecture as §13.2 and web §2.5:` **a token layer the components consume, never values inline.**

### 13.8 Tokens in SwiftUI and Compose, concretely

The idea is the same on both: **named values, never raw numbers.** `AppSpacing.lg` instead of `16.dp`, `theme.colors.textPrimary` instead of a hex literal. A design system is a single source of truth where tokens define the language, components enforce it, and screens only consume it.

**Compose:** the theme is a set of tokens exposed through `CompositionLocal` so any composable in the tree can read them. `MaterialTheme` is built entirely on public APIs, which means a custom design system is built the same way rather than by fighting Material. The usual structure is `AppTheme` providing `AppTheme.colors`, `AppTheme.typography`, `AppTheme.shapes` and `AppTheme.spacing`, with `spacing` being the one Material does not give you.

**SwiftUI:** the equivalent is an `EnvironmentKey` carrying a theme struct, read as `@Environment(\.theme)`. Asset catalog color sets handle light/dark/high-contrast variants automatically, which is the one place SwiftUI is ahead: a single named color resolves per appearance with no branching in view code.

**Slint:** a `global Theme` with color, spacing and radius properties, referenced from every component (§5.22).

The layer that makes it hold across platforms is **Style Dictionary** or an equivalent, generating the Swift, Kotlin and Slint files from one JSON or YAML source. That is what stops the three platforms drifting, and it is the reason the W3C token format mattering (§24.6) is a practical fact rather than a standards footnote.

`The failure to watch for:` tokens that are only primitives. `blue600` in code is a renamed hex literal and buys nothing. The value comes from the semantic tier – `color.action.primary` – because that is the layer that can change per theme, per brand and per platform without touching a screen.

### 13.9 Wide gamut – Display P3

**Display P3 covers roughly 25% more visible color than sRGB**, mostly in reds, oranges and green-cyans. Apple has shipped it since the iPhone 7; Android added color management in 8.0 (API 26).

What it means in practice:

- **iOS:** asset catalog colors and images can be authored in Display P3, and the system handles conversion for non-P3 displays. There is no opt-in flag for UI color.
- **Android:** wide gamut is **opt-in per activity** via `android:colorMode="wideColorGamut"` in the manifest, and the OpenGL/Vulkan path is where it actually matters. Without the flag, content is clipped to sRGB.
- **Slint and other Rust toolkits:** generally sRGB unless the renderer is explicitly configured otherwise. Assume sRGB and verify.

`The design decisions:`

- **A saturated brand color can be reproduced more accurately in P3**, and a color picked in sRGB will look duller on a P3 display than the designer intended when the pipeline clips.
- **The failure mode is inconsistency**, not dullness: the same brand red rendering differently in the app, the widget and the store screenshot because three pipelines handled the profile differently. Fix the pipeline before chasing the color.
- **Photo and video apps are where P3 pays.** For a mostly-typographic UI it is a refinement, and sRGB with correct tagging beats P3 with a broken pipeline every time.
- **Contrast ratios do not change.** Wider gamut does not relax any accessibility requirement, and a very saturated P3 color can be harder to read against, not easier.

### 13.10 Color and icons across cultures

Color meaning is not universal, and the cases that bite are specific rather than vague:

| Signal | Western default | Divergence |
|---|---|---|
| Financial gain | Green up, red down | **Reversed in China and much of East Asia** – red is gain |
| Red | Danger, error, stop | Luck and celebration in China; mourning in parts of Africa |
| White | Clean, empty, default | Mourning in several Asian cultures |
| Purple | Premium, creative | Mourning in parts of Latin America |

The financial one is the sharpest: a trading app shipped to China with Western red/green semantics is not stylistically off, it is **backwards**, and users will read losses as gains.

Icons carry the same problem. Very few symbols are genuinely universal, and gestures are the worst offenders – a thumbs-up or an open palm is offensive in some regions. Direction-bearing icons flip in RTL (§12.5); non-directional ones like clocks and storage do not.

`The practical process, and it is not "avoid all culture":` pick a default set, then have a native speaker or local designer review the color semantics, the icons and the imagery per market. Focus groups and A/B tests in the target market catch what a checklist cannot. The cost is small compared to shipping a red-for-loss trading screen into a market that reads it the other way.

## 14. Icons, symbols and illustration

### 14.1 Tab bar icons – the filled/outline convention

**Unselected = outline. Selected = filled.** Both platforms.

- **iOS: use the outline SF Symbol and the system selects the filled variant automatically** in the right context. Do not ship two hand-drawn variants.
- **Android: a state list drawable** per icon, listing the image per UI state.
- **The active item must be distinguished by more than hue.** *"Users with colorblindness might have difficulty identifying the active tab if color is the only distinguishing factor."* Fill **plus** color, not color alone – web §2.6's rule, in the most-used component on the screen.

`This is why fill is a symbol-system axis on both platforms` (§3.5, §4.6): the selected/unselected pair is the single most common icon state in mobile UI, so both design systems built it into the font.

### 14.2 Custom SF Symbols

The workflow, and it is much cheaper than it looks:

1. **Export a template from an existing symbol that resembles yours** – `File → Export Template…` (⌘E). The reference symbol matters; pick a close one.
2. **Choose the "Variable" template.** You then **draw only three weights**, and SF Symbols generates the other 24.
3. **Preserve the folder hierarchy** – each symbol in a named folder containing `Symbols`, `Guides`, `Notes`. Import fails without it.
4. Export as SVG and run **Validate Templates**.

`Three drawings for 27 variants` is the reason a custom symbol set is viable at all – and the reason a custom icon on iOS should be built as a **symbol**, not as an image asset. An image asset does not participate in weight matching, optical scaling, rendering modes or Dynamic Type alignment.

### 14.3 Material Symbols on Android

**`material-icons-extended` is deprecated.** *"The Material Icon library is no longer maintained or recommended – it contains an older look and feel and can significantly increase build time."*

The structural reason matters: **the old Compose icon libraries were generated from vector drawables, not from the icon font – so weight, grade and boldness cannot be changed.** Variable-axis control requires the actual variable font.

Current options:
- **Download the XML from Google Fonts (Android tab)** for a current Material Symbols icon.
- **`compose-material-symbols`** (Compose Multiplatform) – built on the variable fonts, so **weight, style and size are adjustable**.
- Variable fonts are instantiated with **`FontVariation.Settings`** in Compose.

`Practical consequence:` an Android project still importing `material-icons-extended` has icons that **cannot match the type weight** (§4.6's rule) and is paying a build-time cost for the privilege. Worth flagging in any audit.

### 14.4 Lottie

- **5–20KB** per animation, versus hundreds of KB or MB for the equivalent GIF or video. **Vector, so crisp from a 16px icon to a 2000px hero.**
- JSON describing keyframes, paths and timing curves; exported from After Effects via Bodymovin.
- Good uses: **animated tab icons, state-morphing buttons, loading and empty-state illustrations, micro-interaction confirmations (heart, checkmark, bell), onboarding illustrations** (2–4s, loop or one-shot).
- **Avoid Alpha Matte and Alpha Inverted Matte** – *"unpredictable results, difficult to troubleshoot."* Use masks instead.
- **Fewer keyframes = smaller file.**

`The honest position:` Lottie makes bespoke motion cheap, which means it also makes over-animation cheap. Everything in web §14.7 applies – **the loop question especially.** A Lottie tab icon that plays on every tap is charming for a day.

### 14.5 The shared/split line for a mobile design system

The clearest statement found of what travels across platforms and what must not:

> **"Colors, typography, spacing, design tokens, icons and illustrations stay unified – while navigation patterns, gestures, elevation and component behaviors flex between iOS and Android, because users instantly feel when something is off."**

`That is the operational form of §6.6.` **Identity is shared; behavior is native.** And the last clause is the reason: platform mismatch is *felt* before it is noticed, which is exactly §21.1's definition of unpolished.

### 14.6 Skeleton implementation on native

- **Match component sizes exactly** – *"video thumbnails and the text under them are spot-on, which prevents the screen from jumping during loading."* Same rule as web §7.37: a mis-sized skeleton creates the layout shift it was meant to prevent.
- **Shimmer/wave is the default style** because it *"creates a sense of progress"*.
- Platform note: **Android generally implements shimmer fully custom; iOS leverages native mechanisms** with a custom shimmer on top.
- Honor Reduce Motion (§11.6) – keep the shapes, drop the shimmer.

### 14.7 The user now themes your icon

iOS 26 gave users system-wide control over icon appearance: **tint the whole home screen a chosen color, switch to a dark variant, make icons translucent glass, resize them, and remove labels entirely.** Android has had icon theming through Material You and third-party launchers for longer, and Android's launcher replacement goes further still.

`Design consequences, and they are real:`

- **The icon must survive being tinted.** A mark that reads only through its own colors disappears when the system flattens it to one hue. Contrast within the mark – shape, negative space, weight – is what survives.
- **Supply the dark and tinted variants** rather than letting the system derive them. iOS accepts a dark and a tinted asset alongside the light one; letting it guess produces muddy results on detailed icons.
- **Labels may be off.** A user with labels hidden identifies the app by silhouette alone. If the mark is not distinctive at 40 px with no text, it is not finished (§20.1).
- **Do not fight it.** An icon designed to look wrong when tinted does not force the user to stop tinting; it just looks wrong.

`The broader point:` personalization moved from a niche to a default surface, and the app's mark is now one input into a look the user is composing. That is a real loss of control and the correct response is a simpler, stronger mark, not a more elaborate one.

### 14.8 Illustration

Empty states, onboarding and error screens are where illustration lives on native, and the failure is almost always inconsistency rather than quality.

**Pick one primary style for the whole product**, and at most one secondary style reserved for a specific repeated job. The pairing that works: flat illustration throughout, line illustration for empty states. What does not work is three styles because three screens were designed by three people at three times.

The styles, and what each is actually for:

| Style | Reads as | Best for |
|---|---|---|
| **Flat** | Neutral, modern, recolorable | The default for product UI; solid shapes stay clear at any size |
| **Line** | Light, unobtrusive, technical | Dense or data-heavy screens, and empty states where the illustration must not dominate |
| **Micro-illustration** (48–64 px) | Compact, narrative | Empty states, feature cards, list decoration |
| **Soft 3D** | Premium, playful, heavy | Marketing surfaces and onboarding; expensive to keep consistent |

`Rules that hold regardless of style:`

- **Recolor from tokens.** An illustration with hardcoded brand colors breaks in dark mode and breaks again on a theme change. Build them to accept two or three token colors.
- **They must work at the size they will actually be shown**, which is usually smaller than the canvas they were drawn on.
- **Never carry meaning in the illustration alone.** It is decoration next to the message, not the message. An empty state still needs a heading and an action (§21.9).
- **Ship them as vectors** (SVG on Android and Slint, PDF or SVG in an iOS asset catalog) so there is one asset rather than three raster scales, and so size stays small (§20.8).
- **Check them for cultural readings** (§13.10) before shipping globally – hands, gestures and food are the usual offenders.

## 15. Lifecycle, state, offline and sync

### 15.1 Lifecycle – the states a design has to survive

**iOS: not running → inactive → active → background → suspended.** *"Suspended"* means resident in memory but executing nothing, reached roughly five seconds after backgrounding.

**The hard constraint: on entering background you have about five seconds to persist anything you need**, because the OS may terminate the process afterwards to free memory for the foreground app. When the user returns, it relaunches cold.

Android: activity lifecycle, with **`ViewModel` or `rememberSaveable`** carrying UI state across recomposition and configuration change.

`Design consequences the web does not have:`

- **The app can be killed at any moment with no warning and no unload event.** Every in-progress form, filter, scroll position and draft has to survive that.
- **State restoration is a design requirement, not an engineering nicety** – *"users must be able to return to their exact previous context."* A user who backgrounds mid-checkout and returns to an empty cart has been failed by design, not by the OS.
- The web's autosave-vs-explicit-save question (web §7.27) is settled differently here: **on mobile, persist continuously regardless of the save model**, because termination is normal rather than exceptional.

### 15.2 Offline-first

The paradigm: *"functionality is unaffected by intermittent lack of network"*, usually with sync across devices.

**Conflict resolution, by data type:**

| Strategy | Use |
|---|---|
| **Last-write-wins** | *"Fine for low-stakes fields where overwrites do not harm anyone"* – **a bad choice for money, inventory, or anything audited** |
| **Field-level merge** | When records have independent fields. *"If the server can merge non-overlapping changes, conflicts drop without involving the user"* – the best outcome, because the user never sees it |
| **User-assisted** | Only when the first two cannot resolve it |

**The user-assisted UX pattern, and it is well-formed:** show **"your version vs latest version"** with a clear three-way choice – **keep mine / use theirs / merge** – in **non-technical language.**

**Idempotency is a design concern too:** every change carries a **unique action ID (UUID)**; a re-sent change returns the same result rather than creating a duplicate. Without it, a flaky connection produces duplicate orders – which the user experiences as the app's fault.

**Status surface requirements:**

- Offline / online indicator.
- **Badges or icons on unsynced items** – per item, not just globally.
- Retry affordance on failure.
- *"Good pending-changes UX is calm and predictable: it acknowledges that work is saved on the device, and it makes the next step obvious."*

`This is a whole design surface with no web equivalent.` Web §7.27's save states assume connectivity; native has to express **saved-locally, syncing, synced, conflicted, failed** – five states where the web has two.

### 15.2b Sync conflicts – the part offline-first usually skips

Offline-first is easy until two devices edit the same thing. The resolution strategy is a design decision with a visible consequence, not an implementation detail.

| Strategy | When it is right | What the user sees |
|---|---|---|
| **Last write wins** | Preferences, toggles, single-user settings | Nothing – and silently losing an edit if that assumption is wrong |
| **Field-level merge** | Records with independent fields (a contact, a profile) | Nothing, correctly |
| **CRDT / operational transform** | Collaborative text and lists | Nothing – both edits survive |
| **Ask the user** | Documents, notes, anything with authored content | A conflict UI |

Last-write-wins is the default everywhere and it is the one that quietly destroys work. Choose it deliberately or not at all.

When the user does have to resolve a conflict, the UI rules:

- **Show both versions with what differs**, not two opaque blobs labeled "local" and "server". A user cannot choose between two things they cannot compare.
- **Label by device and time**, in the user's words: "This iPhone, 2 minutes ago" against "MacBook, yesterday at 4:12 pm".
- **Offer "keep both"** wherever the data model allows it. It is the answer that never loses anything and it is the one most apps forget to offer.
- **Never resolve a conflict inside a toast.** It is a decision, so it gets a screen.
- **Do not surface conflicts the user did not cause.** A background sync conflict on data the user has not touched should be resolved by policy, not escalated.

The states that must be visible throughout, and this is the part that actually builds trust: **offline, pending, syncing, synced, failed.** The optimistic-UI pattern (write locally, mark pending, show it immediately) works only when "pending" is visible somewhere; otherwise the user cannot tell a saved thing from a thing that will vanish.

### 15.3 Images on native

Same progressive model as web §10.12, with the same rule about size:

- **Blur-up / low-quality placeholder**, then the full image. **BlurHash** encodes a preview as a compact string.
- **The placeholder must occupy the final image's exact box** – *"if your placeholder is too small it'll still create that annoying layout shift when the real image loads."*
- Caching plus runtime eviction; resize before caching rather than holding full-resolution bitmaps.

`Note the native-specific hazard:` memory. On the web an oversized image costs bandwidth; on mobile it costs **RAM**, and RAM pressure is what gets the app killed in the background (§15.1). Image sizing is therefore a **retention** issue, not only a performance one.

## 16. Notifications, widgets, ambient surfaces

### 16.1 Permission priming – the highest-leverage pattern here

**The system permission prompt is one-shot.** A "no" is effectively permanent, recoverable only by sending the user into Settings.

**The pattern: show your own screen first, and only fire the native prompt if they say yes to yours.**

- **Pre-prompts lift grant rates 2–4×.**
- *"If the user accepts your soft-ask, they have already decided; the native prompt is a formality and converts at very high rates."*
- **Ask in context, immediately after the user does something that makes the value obvious** – not on launch.

> **"The single most common push notification UX failure happens in the first thirty seconds, when an app opens and instantly fires the system permission dialog before the user knows what the app even does."**

`This is a design decision that permanently caps a channel.` It belongs in the first conversation about a native app, not in a growth review later. The same pattern applies to location, camera, contacts and health.

### 16.2 Push notifications

- **Earn permission → send relevant content → time it well → land on the right screen → measure.** The fundamentals have not changed.
- **Trigger on user actions, not on a schedule.** Send-time optimization from individual historical activity beats a scheduled blast.
- Anatomy: brand mark, title, one to three short sentences, a CTA.
- **"Never disguise marketing as utility."** The single clearest ethical line in this area, and it maps directly onto web §7.21's dark-pattern test.
- *"A poorly timed, irrelevant notification is ignored at best, and provokes an app uninstall at worst."* The downside is not a wasted impression – it is losing the user entirely.
- **Landing screen is part of the notification design.** A notification that opens the home screen instead of the referenced item has failed even if the copy was perfect.

### 16.3 Rich notifications

**Two states, both of which must work:**

- **Collapsed** – one or two lines. *"The compressed view needs to work as a standalone message."*
- **Expanded** – images, long text, progress, action buttons. Android: `BigPictureStyle`, `BigTextStyle`, `RemoteViews`. iOS requires a **Notification Service Extension** to fetch and attach media before display.

**Media at 2:1 ratio** works on both platforms and is required in some cases.

Inline replies, quick actions and deep links let the user act **without opening the app** – which is often the best possible outcome and is under-designed almost everywhere.

### 16.4 Android notification channels

Every notification belongs to a channel (Android 8+), and **the user controls each channel independently.**

**Group by two things at once: subject matter *and* the importance level it deserves.** Because a channel has one importance level, everything in it must genuinely warrant that level.

> *"When an unimportant notification is disguised as urgent, it can produce unnecessary alarm."*

- Time-critical messages in one channel; account tips and marketing in another.
- Channel **groups** organize the list when there are many.
- **Design consequence:** the channel taxonomy is a design artifact. A single "Notifications" channel forces the user to choose between everything and nothing – and they will choose nothing.

`This is the best notification-control model on any platform`, and it has no iOS or web equivalent. Where the app ships on both, design the taxonomy anyway – it clarifies what deserves to interrupt (web §7.31).

### 16.5 Push vs in-app

> **"Push notifications are an external tap on the shoulder designed to bring users back. In-app messaging is an internal conversation that happens once they've arrived."**

- **Push** – reaches users who are *not* in the app. Re-engagement, urgency, time-critical events.
- **In-app** – only while the app is open. Behavior-targeted, precise, better for onboarding, feature discovery and conversion.
- **The layered strategy: push to start the session, in-app to guide what happens in it.** Treating them as interchangeable is the common failure.

### 16.6 Widgets – the glance constraint

**The standard, and it is brutal:**

> **"Users discard any widget that takes longer than 0.5 seconds to interpret."**

That has *"eliminated dense, multi-layered designs in favor of single-metric, high-contrast layouts."*

**iOS (WidgetKit):** small / medium / large / extra-large (iPad, macOS) on the Home Screen; **inline, circular and rectangular on the Lock Screen.**

- **Glanceable, relevant, personalized.** Summarize one core feature.
- **Tasks completable in a single tap** where interactivity exists at all.
- **Cache locally** – *"people shouldn't need to wait for content to load"*; always show recent data while updating.
- **Do not customize the widget background.** *"The light, blurred widget background provided by the system is designed for consistency and legibility. Use it whenever possible."*

**Android (Glance):** Compose-style API – `Box`, `Row`, `Column`, `Text`, `Button`, `LazyColumn`, `Image`, `Spacer`.

- **Layout must be orientation- and device-independent**, stretching and contracting.
- **Design explicit compact and expanded states**, set an honest minimum usable size, declare a sensible resize envelope.

`The design lesson generalizes:` a widget is the most extreme version of web §5.10's squint test – **if the single most important value is not legible at a glance, the widget has no reason to exist.** Widgets are where hierarchy discipline pays off most, because there is room for exactly one first read.

### 16.6b Widget families, exactly

**Home Screen:** `systemSmall` (a 2×2 grid slot), `systemMedium` (4×2), `systemLarge` (4×4), and `systemExtraLarge` (iPad, and iPhone from iOS 15).

**Lock Screen (iOS 16+):** `accessoryInline` (a single line of text beside the time), `accessoryCircular` (a gauge or glyph), `accessoryRectangular` (a small block of text or a tiny chart). These render **monochrome and are tinted by the system**, so a design that depends on color does not survive here.

**StandBy** (iPhone on charge, landscape) reuses the Lock Screen families at a larger scale and in a dim red night mode, which is another reason not to encode meaning in color.

`supportedFamilies` declares which ones the app offers, and the design decision is which to support at all.

`The rules that make a widget good rather than present:`

- **Each size is a different design, not a scaled one.** Small holds one number or one action. Medium holds a small set or one item with context. Large can hold a list. Designing large and shrinking produces three bad widgets.
- **Support small.** It is the size people actually place, because home screen space is contested.
- **The widget is a glance, not a screen** (§16.6). If it needs to be read rather than seen, it is too dense.
- **Every widget needs a deep link target** that lands on the matching content, not the home screen (§20.7).
- **Refresh budgets are real.** WidgetKit limits reload frequency, so design content that stays meaningful when it is a few minutes stale, and never show a time-critical number that could be silently wrong.

### 16.7 Live Activities and Dynamic Island

**Design order matters, and Apple states it:**

> **"Design from the smallest surface first: compact/minimal Dynamic Island, then small activity family, then expanded, then Lock Screen."**

`That is progressive enhancement applied to ambient surfaces` – and it is the opposite of how most teams work (design the big one, shrink it).

Constraints:

- **8 hours active + 4 hours stale = 12 hours maximum.** The system ends anything longer.
- **Minimal presentation image ≤ 45 × 36.67 points.**
- Multiple concurrent activities are allowed, but **the Dynamic Island shows one at a time.**
- Content must be **balanced around the camera cutout** in compact and expanded presentations.
- The same activity can surface on the **Lock Screen, StandBy, Apple Watch Smart Stack, Mac menu bar and CarPlay Dashboard** – so it is one design shipping to six surfaces.
- Users can disable Live Activities globally or per app.

### 16.8 Watch complications

- Families (circular small, graphic bezel, and others) define templates; **all watch faces show at least one, some show four or more.**
- **Line widths of 2 points or greater** – *"thinner lines can be difficult to see at a glance, especially when the wearer is in motion."* **Motion is a legibility variable**, which is a constraint no other surface has.
- Data-rich and glanceable: *"identify essential content people might appreciate viewing on the watch face."*
- **Support all families**, and where you cannot show useful data, still supply an app-representative image so the complication remains a launch point.

`[sources: Apple HIG – Widgets, watchOS Complications, Live Activities / ActivityKit; developer.android.com – app widgets overview, Glance, notification channels, notifications design; WWDC 2026 WidgetKit foundations; onesignal, braze, reteno push best practices 2026; appcues "3 strategies for successful mobile permission priming"; pushengage iOS push permission priming; pushwoosh rich push notifications; moengage push vs in-app]`

### 16.9 Interactive widgets and controls

**iOS 17+ widgets support buttons and toggles**, executing code via **App Intents** and refreshing inline without launching the app. **iOS 18 extends the same App Intents to Control Center controls**, the Lock Screen and the Action button.

> **"One App Intent, many places."**

- Reported: **interactive widgets get ~3× the engagement of static ones.** `[secondhand]`
- The design implication is structural: **an App Intent is a unit of *capability*, and the surfaces are just places it appears.** Designing the intent well – a clear name, a single outcome, sensible parameters – is what makes it usable from a widget, a control, Siri, Shortcuts and Spotlight simultaneously.

`This is the strongest argument in the native study for designing capabilities rather than screens.` The web study's closest analogue is web §10.11's machine-readable web – but this is further along, because the OS actively surfaces your capabilities in places you did not design.

### 16.10 App Clips and Instant Apps

- **App Clips ≤ 10MB; Android Instant Apps ≤ 15MB.**
- *"A lightweight version of your app that lets people perform everyday tasks quickly, without downloading and installing."*
- The design discipline: **carefully select the smallest set of features that completes one task.** Apple's own example ships an App Clip that *only* orders donuts.
- Use when the app *"provides an in-the-moment experience that helps people perform a task over a finite amount of time"* – ordering, paying, booking, checking in.

`The size limit is the design brief.` 10MB forces exactly the prioritization web §14.15 argues constraints produce – and an App Clip is a useful thought experiment even when you are not shipping one: **what is the one task, and what is the minimum interface that completes it?**

### 16.11 Permissions beyond notifications

The same priming pattern (§16.1) applies to every permission, with numbers:

- **Deferring a request until it is contextually relevant lifts grant rates by up to 28%.**
- Pre-permission overlays explaining *why* led to **"nearly every user granting access"** in the contacts case.
- **The headline names the feature, not the permission.** *"Scan Documents Instantly"* – not *"Allow Camera Access."* The system dialog already says the second thing; your screen exists to say the first.

**Per-permission notes:**

| Permission | Guidance |
|---|---|
| **Camera / microphone** | Ask at the moment of first use – *"camera access appears at the moment a document capture is about to begin."* Clearest possible context |
| **Location** | **Over 60% of Android privacy breaches involve at least one location access point.** *"Always"* requires substantial justification and is heavily scrutinized by both stores. Request **while-in-use** first and escalate only with a demonstrated reason |
| **Contacts** | Ask inside the feature that needs it (invite, guest list), never at launch |

**And the ethical floor:** *"avoid dark patterns, and enable users to reverse their decision."* A priming screen that makes declining hard is the consent dark pattern from web §8.19 wearing a different hat.

### 16.12 Privacy labels

- **Apple Privacy Nutrition Labels** (App Store Connect, since Dec 2020): what data is collected, whether it is linked to the user, whether it is used to track, and for what purposes.
- **Google Play Data Safety** (mandatory since 20 July 2022): the same plus **encryption behavior, Families Policy compliance, whether safety has been independently verified, and whether collection is optional or mandatory.**

`Design consequence:` the label is a **public, comparable, pre-install disclosure of every data decision the product makes.** Which means data collection is now a design decision with a visible cost – an optional field that triggers a data-type disclosure may not be worth the label entry. Worth raising when someone proposes collecting something "in case it's useful later."

### 16.13 App Intents – the app's surface outside the app

App Intents (iOS 16+) replaced SiriKit, and Apple gave SiriKit a formal deprecation notice at WWDC 2026: App Intents is now the only route for Siri to call into a third-party app for new development.

What an intent buys, all from one declaration: **Spotlight, the Shortcuts app, Siri, the Action Button, Control Center controls, widgets, and Apple Intelligence.** These are seven discovery surfaces the app does not have to design separately.

The design framing that matters: **an app that exposes no intents is invisible in an intent-driven OS.** As assistants and system search become the entry point, the question shifts from "what does my home screen look like" to "what are the ten things a user can ask for by name". Those ten things are a design deliverable, and naming them well is copywriting, not engineering:

- The phrase must be what a person would actually say, not the internal feature name.
- Parameters need natural values ("morning", not "profile ID 3").
- Every intent should be completable without opening the app when that is possible, and should return a compact result view when it is not.

iOS 27's App Intents 2.0 adds richer entity types, streaming responses for long-running actions, multi-turn follow-ups, and a View Annotations API that lets a user refer to on-screen elements in a voice command.

Android's parallel is App Actions and shortcuts via `shortcuts.xml` plus the Assistant's built-in intents, which is narrower in scope but the same idea: declare the capability, let the system route to it.

### 16.14 Glance – widgets as Compose

Jetpack Glance builds Android app widgets with a Compose-style Kotlin DSL on top of the existing `AppWidget` system. Logic that took 100+ lines of XML and `RemoteViews` boilerplate lands in 30–40 lines.

The composables available are deliberately small: `Box`, `Row`, `Column`, `Text`, `Button`, `Image`, `Spacer`, `LazyColumn`. That is the entire toolkit, and the constraint is the point – a widget that needs more than these is trying to be an app.

Design-relevant details:

- **`SizeMode`** decides how the widget responds to resizing: `Single` (one layout), `Exact` (recompose per exact size) or `Responsive` (declare a set of supported sizes and let the system pick). `Responsive` is the correct default, because users resize widgets freely and a single layout stretched to a 4×1 grid looks broken.
- **Material You dynamic color works**, so a widget can pick up the user's wallpaper palette and sit naturally on the home screen. This is the main reason an Android widget can look more at home than its iOS counterpart.
- **Glance is not interoperable with regular Compose UI.** They share a syntax and nothing else, so component code cannot be reused between app and widget. Plan the widget as its own small design, not as a shrunken screen.

### 16.15 Notification copy

The notification is the only part of the app a user sees without opening it, so it is judged more harshly than anything inside.

- **Specific beats promotional.** Numbers, names and concrete nouns outperform generic marketing language, and all-caps reads as spam.
- **The title carries it.** Most notifications are read on a lock screen where the body may be truncated to one line, so the important word goes first.
- **Action buttons reduce friction measurably.** "View order", "Add to cart", "Book now" as notification actions convert better than a tap-through to the app, because they shorten the path.
- **Frequency is the retention risk, not the copy.** Around **46% of users will opt out after 2–5 messages in one week** that do not feel relevant, and a revoked permission is permanent in practice (§16.11).
- **Send-time personalization is worth more than copy personalization.** Predicting when an individual user is actually looking at their phone lifts open rates more reliably than inserting their first name.
- **Never put sensitive content in the preview** (§21.22). The lock screen is a public surface.

The test that catches most bad notifications: read it as though it arrived at 11 pm on a Sunday. If it would not be welcome then, it needs a better trigger, not better wording.

### 16.16 Focus, summaries and notification ranking

The delivery layer is no longer neutral, and that is a design constraint rather than an infrastructure detail.

- **Apple Intelligence ranks incoming notifications by inferred importance.** Context-tied messages – a delivery arriving, a meeting starting, a direct reply – get promoted. **Generic marketing is demoted or folded into the Notification Summary**, where it is read hours later or not at all.
- **Focus modes filter by app and by category**, and 2026 extended this with expanded Focus Filters that limit **in-app content**, not just alerts. An app can be asked to show a work-only view.
- **Time Sensitive is a real category that breaks through Focus and the Scheduled Summary** – but only for genuinely time-critical content: a ride arriving, a medication reminder, a delivery at the door.

`What this changes:`

- **Marking everything Time Sensitive is self-defeating.** The user can revoke the app's ability to use it, and the system's own ranking learns. Reserve it and it keeps working.
- **Relevance is now upstream of visibility.** A notification the system considers generic may never be seen, so the open rate is no longer just an engagement metric, it is a deliverability signal.
- **Design for the summary as a surface.** A notification that lands in a batch three hours later needs to still make sense out of sequence, and it needs to not read as urgent when it is not.
- **Support Focus filters where the app has modes.** A work-and-personal app that respects the user's Focus is doing the same job as account switching (§21.27) with zero interaction cost.

## 17. Native performance

### 17.1 The frame budget, and why it is the design constraint

| Refresh | Budget per frame |
|---|---|
| 60Hz | **16.67ms** |
| **120Hz** (ProMotion, high-refresh Android) | **8.33ms** |

**Jank is defined, not felt-for:** Google Play counts a frame **slow** above **16.67ms** and **frozen** above **700ms**. Play Console **flags apps with slow frames in >25% of sessions, or frozen frames >0.1%**, and surfaces a store warning above a **0.47% user-perceived ANR rate.**

And the sentence that matters most:

> **"Users don't feel 'average fps', they feel the bad frames. A list that runs at 120fps most of the time but stutters for 200ms every scroll feels worse than a steady 60fps."**

`This is web §6.33's central finding – consistency beats speed – restated with a store-enforcement mechanism behind it.` Third independent confirmation, after streaming fluency (web §7.15) and animation pacing.

Tools: `adb shell dumpsys gfxinfo` (Choreographer frame drops), `FrameMetricsAggregator`, Macrobenchmark for a bracketed user journey.

### 17.2 ProMotion and adaptive refresh – a real design decision

ProMotion is **adaptive, not fixed**: **10–120Hz on iPhone, 24–120Hz on iPad Pro**, ramping up for scrolling and transitions and dropping to 24Hz or lower when static. Input-to-paint latency falls from ~16.67ms to **~8.33ms**.

**The trap, stated by Apple's own guidance:**

> **"If your animation requests 120Hz but can't keep up, it may render poorly – but the same animation may be able to maintain a steady cadence at a lower rate."**

**Deliberately targeting 60 can be the better design decision than targeting 120 and missing.** That is a genuine design choice, not an engineering afterthought.

Also noted: high refresh is *"activated only for scrolling and full-screen transitions, not for all other animations, which can cause a very inconsistent user experience"* – so a custom animation may run at a different effective rate than the scroll it sits next to.

### 17.3 Compose performance – stability is the lever

**Type instability is the named primary cause of Compose jank.** Google maintains a *Stability in Compose* guide as the central performance document (updated Jan/Feb 2026).

- **Diagnosis:** Android Studio **Layout Inspector shows a recomposition counter per composable** – *"a constantly rising counter on a static part of the UI is an immediate red flag."* CPU Profiler with System Tracing shows the three Compose phases (Composition, Layout, Draw).
- Reported case study: recompositions per scroll frame **7.2× → 1.0×**; janky frames (>16ms) **down over 60% on median devices.**
- Toolkit: Strong Skipping Mode, stability annotations, Baseline Profiles (AOT), R8 full mode, composition tracing in production.

`Design consequence:` **a Compose UI can be visually correct and still jank because of data-model shape.** The designer-visible symptom is scroll stutter on mid-range devices – and mid-range is where most users are.

### 17.4 SwiftUI lists – the counterintuitive result

`MYTH-adjacent.` The instinct is "lazy containers are faster". Measured comparison says otherwise for long lists:

| | `List` | `LazyVStack` |
|---|---|---|
| Scroll to bottom | **5.53s** | **52.3s** |
| Hangs | **4.6** | **78** |

**`List` recycles views; `LazyVStack` only defers creating them and then keeps them.** For large data sets `List` wins decisively on both memory and scrolling.

Other rules: **stable IDs in `ForEach`**; conform the model to `Identifiable` **and** `Hashable`.

`Use LazyVStack when you need layout freedom a List cannot give (custom separators, non-uniform rows, nested scroll), and accept the cost – not because it is assumed faster.`

### 17.5 Cold start

**The first impression, and it is measured:**

| Target | Verdict |
|---|---|
| **< 1.5s** | Excellent |
| **< 2s** | Good |
| **> 3s** | *"Hurting your retention measurably"* |
| ~5s | Serious problem |

> *"Teams can grow their cold-start time by 400ms over a year by shipping useful-but-not-critical features, and that 400ms can cost measurable first-session retention."*

Cold start is **process spawn → runtime init → application object → first screen built from scratch.**

Design levers:
- **Predictive preloading** – fetch the likely first destination *while the splash screen is showing.*
- **Skeleton over spinner on the first screen** – *"users tolerate skeleton states 40% longer."*
- Do not put anything on the launch path that is not needed for the first interactive screen.

### 17.5b Startup budgets, with the published numbers

| Platform | Threshold | Source |
|---|---|---|
| Android – cold | **5 s or more is flagged as excessive** by Android Vitals | Google |
| Android – warm | 2 s or more | Google |
| Android – hot | 1.5 s or more | Google |
| iOS – first frame | **Render in under 400 ms** | Apple |
| Practical target | **Under 2 s cold on a mid-range device** | Industry practice |

The consequence numbers, which is why this is a design concern and not only an engineering one: roughly **half of users uninstall an app over performance problems**, and about **a third uninstall if it takes more than six seconds to load.**

`The design levers:`

- **Optimize time-to-initial-display first.** If the user sees nothing, the app is broken regardless of how fast the rest arrives. That is what the launch screen and the skeleton exist for (§20.6, §17.10).
- **Defer everything not needed for the first frame** – analytics SDKs, crash reporters, remote config, feature flags. Each is a small delay that only shows up on a slow device.
- **Do not start with a screen that needs the network.** A cached last-known state that appears instantly beats a correct state that appears in 1.5 s, and reconciling afterward is invisible.
- **Never add a splash delay** to show a logo (§20.6).

### 17.6 Battery – an argument for restraint with numbers

- **Reducing animation lowers battery consumption by 15–25%.**
- **Background animations can run at 30fps with no noticeable quality loss**; 60fps is for direct interaction; **120fps consumes exponentially more.**
- Background-processing improvements yield **15–20%** savings.
- Complex and simultaneous animations are the named drains.

`This gives web §6.7 and web §12.8's "restraint" argument a hard number on native.` **Ambient animation that runs continuously is a battery cost the user pays for the whole session** – which is a stronger argument than aesthetics for keeping it rare, or dropping it to 30fps when it is decorative.

Pause animations when backgrounded. Always.

### 17.7 Perceived performance

- **Skeletons: Facebook and others report content perceived as loading up to 50% faster; other measurements say 10–15% or ~30%.** Instagram, Facebook and LinkedIn all switched.
- **Optimistic UI**: update as though it succeeded, roll back on error. *"For actions that rarely fail, this creates a dramatically faster-feeling app with no engineering performance work at all."*

`CONFLICT – carried over from web §6.7:` the skeleton evidence is strongly positive here and contested there (Viget 2017 found skeletons *worst* on perceived duration). **The native sources are largely vendor-published; the contrary study is independent.** Working position unchanged: **skeletons for short, predictable waits in a familiar layout** – and note that native cold start is exactly that case, which may be why native evidence is more favorable.

`[sources: developer.android.com – app startup time, slow rendering, measuring performance, Compose performance; Apple "Optimizing iPhone and iPad apps to support ProMotion displays"; drizz.dev "Animation Testing on Mobile: Frame Budgets, Jank"; Medium/Santiago Mattiauda "Compose Performance 2026: Stability as the Key Lever Against Jank"; STRV and fatbobman "List vs LazyVStack"; digia.tech app startup guide; uxcam mobile performance metrics]`

### 17.8 Dark mode and battery, with the real numbers

Worth having exact figures, because this is claimed loosely in both directions.

- **OLED only.** Each pixel is its own light source and black pixels draw essentially no power. On an LCD the backlight is always on, so dark mode saves nothing.
- Purdue's per-frame OLED power profiler measured the display at **44–73% of total phone power in light mode at maximum brightness**, and switching to dark mode cut total phone power **by roughly 42% at 100% brightness**.
- **The catch is brightness.** At the 30–50% brightness most people actually use, the saving falls to a few percent. Dark mode is a large win only for users who run bright screens, and a rounding error for everyone else.

`Design conclusion:` ship dark mode because a large majority of users prefer it and because it is a system-level expectation (§13), not because of battery. And when it is shipped, **use true dark surfaces rather than dark gray if the saving is being claimed at all** – Material's recommended `#121212` surface is a legibility choice, not a power one, and the two goals genuinely pull against each other.

### 17.9 Overdraw – the design decision that costs frames

Overdraw is drawing the same pixel more than once in a frame. It is invisible in a mockup and it is where a beautiful design turns into a janky one on mid-range hardware, because fill rate is the first thing that runs out on a weak GPU.

The design choices that cause it, in order of cost:

- **Stacked opaque backgrounds.** A window background, a container background, a card background and a row background at the same place is 4x overdraw for one visible color. Set the background once, at the layer that actually needs it.
- **Alpha on large surfaces.** A translucent overlay covering the screen forces everything under it to be drawn and blended. Translucency is expensive in exact proportion to its area, which is the technical reason Liquid Glass and Material's scrims belong on chrome rather than on content.
- **Deep nesting** with a background on each level.
- **Shadows on many small elements**, since each is drawn outside its own bounds.

Tooling: **Debug GPU Overdraw** color-codes how many times each pixel was drawn (blue 1x, green 2x, pink 3x, red 4x+), and **Profile GPU Rendering** shows a per-frame histogram against the 16.67 ms line. Both are on-device developer options, which means a designer can run them without a build.

Target: **no more than 2x on most of the screen, and no red anywhere.** One reported case cut a product grid from 4x to 1.5x and measured roughly 38% less scroll jank.

`The design-level rule:` every layer of translucency, blur, shadow and stacked background is a frame-budget expense, and the budget is spent on the cheapest device in the support matrix, not the reviewer's phone.

### 17.10 Skeletons versus spinners, with the conflicting evidence

This is one of the few places where the research genuinely disagrees, so it belongs in the study honestly rather than as a rule.

- **Against skeletons:** a 2017 Viget study (136 participants) found skeleton screens performed **worst** on every metric – longer task completion, more negative evaluation of the wait, and longer estimated wait times – compared to spinners or a blank screen.
- **For skeletons:** other testing found skeletons scored higher than spinners on both perceived speed and ease of navigation, and the theoretical argument is that a spinner puts the user in active waiting while a skeleton lets the brain pre-place the incoming content.

What survives both: **the duration decides.**

| Wait | Use |
|---|---|
| Under ~300 ms | Nothing. Any indicator flashes and reads as a glitch |
| ~300 ms to 1 s | An inline spinner on the control that was tapped |
| 1 s to a few seconds, content-shaped screen | Skeleton matching the real layout |
| Several seconds, known progress | A determinate progress indicator with a real percentage |
| Long or unknown | Progress plus what is happening, and a cancel |

The details that make a skeleton work rather than annoy: it must **match the real layout** so nothing shifts when content arrives, the shimmer should move **left to right** to read as forward motion, and it should not animate faster than about one sweep per 1.5 s or it reads as agitation. A skeleton that does not match what loads is worse than a spinner, because it promised a shape and then broke it.

### 17.11 The low-end device, which is most devices

**Android Go** targets devices with **512 MB to 1 GB of RAM** and as little as **8 GB of storage**, aimed at three specific conditions Google names directly: slow, unreliable or expensive data; cheaper hardware; and limited opportunity to recharge. Samsung, Nokia, Motorola, Tecno, Infinix, Xiaomi and itel all ship Go models, so this is a large real population rather than a rounding error.

Typical low-end Android hardware more broadly: **1–2 GB RAM, a quad-core chip around 1.3 GHz, 8–16 GB storage.**

`What this changes about design, not just engineering:`

- **Animations are the first thing to degrade.** A transition that runs at 60 fps on a flagship runs at 20 on a Go device, and a janky animation is worse than none. Either keep motion cheap (opacity and small transforms) or gate the expensive treatments behind a device-capability check.
- **Every megabyte is a barrier twice** – once at install (§20.8) and again on a device that is already full.
- **Memory pressure means the app gets killed in the background more often**, which makes state restoration (§15.1) a core requirement rather than a nicety. On a flagship a killed app is invisible; on a Go device it is the normal path.
- **Images dominate memory.** Decode at display size and cache aggressively but bounded (§21.23), because on 1 GB of RAM an oversized bitmap cache is the reason the app gets killed.
- **Data cost is a design constraint.** Autoplaying video, prefetching a feed and high-resolution image defaults all spend money that belongs to the user.

`The rule:` the design target is the slowest device in the support matrix, and that decision is made once, at the start, because retrofitting a heavy design onto weak hardware means removing the parts that were the point.

### 17.12 Input latency – the numbers behind "it feels responsive"

Touch latency perception is far finer than most design intuition assumes, and the thresholds differ by task:

| Task | Detection threshold |
|---|---|
| **Dragging** (finger stays down, content tracks it) | **~6 ms**, with reported detection as low as 2–10 ms |
| Drag task **performance** degrades | above **~25 ms** |
| **Tapping** (discrete, feedback after release) | **~24 ms** – below that, imperceptible |
| **Inking** with a stylus | **~50 ms** |
| Unacceptable in any task | above ~580 ms |

The asymmetry is the useful part: **anything the finger is touching has a latency budget roughly four times tighter than anything it merely triggers.** A button can respond in 20 ms and feel instant. A sheet being dragged cannot.

`Design consequences:`

- **Direct manipulation is where the frame budget must be defended** (§17.1). Drop a frame during a drag and it is felt; drop one during a fade and it is not.
- **Never gate a drag on work.** Layout, decode and network all belong outside the gesture. If the content under the finger is not ready, drag a placeholder.
- **Touch-down feedback beats touch-up feedback.** Showing the press state on down – even before deciding what happens – converts a 24 ms budget into a much easier one, because the user has already been answered.

### 17.13 What 120 Hz actually buys

A 60 Hz display puts **16.7 ms** between frames; 120 Hz puts **8.3 ms**. That halving is the single most perceptible hardware difference on a phone, and it is felt in exactly the place §17.12 predicts: content under a moving finger.

- Scrolling at 120 Hz reads as *continuous* rather than as a fast sequence of steps, and users often perceive it as **faster**, not just smoother, even though the content moves at the same speed.
- ProMotion and its Android equivalents are **adaptive** – 10 Hz to 120 Hz depending on content – which is why an app that keeps something animating continuously prevents the display from dropping down and costs real battery (§17.6).

`The design conclusion:` design the app to hit the high frame rate during interaction and to stop animating entirely at rest. Constant idle motion is the worst combination available: it burns battery by pinning the refresh rate high and it gains nothing, because the smoothness only registers when the user is moving something.

## 18. Desktop native

### 18.1 macOS

*"Mac apps serve power users who expect deep keyboard control, persistent menu bars, resizable multi-window layouts, and tight system integration."* That sentence is the whole brief.

- **Every Mac app must have a menu bar** – it is *"the primary discovery mechanism for commands."* Minimum: **App, File, Edit, View, Window, Help** (File may be omitted if not document-based).
- **Sidebar on the leading edge** as the primary navigation surface, collapsible from the toolbar or a shortcut.
- **Toolbar items carry both an SF Symbol and a text label**; icons-only in compact mode. *"Prefer labeled icons for discoverability"* (web §8.16 again).
- **Search field in the trailing toolbar area.**

`The menu bar is the important structural difference from mobile.` It means **every command in the app must be reachable and nameable**, which is a design discipline in itself – if a feature cannot be given a menu name, it probably is not a coherent feature. It also gives keyboard shortcuts a natural home (web §7.26).

**The menu bar, in more detail, because it is the part web-shaped developers skip.**

Every Mac app has a menu bar and **it is the primary discovery mechanism for commands.** A user who cannot find a feature looks there before anywhere else, which makes it a design surface, not a compatibility checkbox.

- **The required minimum: App, File, Edit, View, Window, Help.** Shipping an app whose menu bar is only the auto-generated defaults is a visible tell that the app is a port.
- **One-word titles** in the bar itself. They scan fast and take little space.
- **Standard items keep their standard shortcuts.** Cmd-C, Cmd-V, Cmd-S, Cmd-P, Cmd-W, Cmd-Q. Rebinding any of these is hostile.
- **Custom shortcut tiers:** Cmd+letter for primary actions, Cmd+Shift for secondary, Cmd+Option or Cmd+Ctrl for tertiary. Define custom shortcuts only where the action is frequent.
- **Everything reachable by mouse needs a keyboard equivalent.** Mac users lean on the keyboard more than users of any other platform, and a Mac app that cannot be driven from it reads as unfinished.
- The Help menu's search field searches the menu bar itself, which is a real discovery path and a reason to name menu items in the words users would search for.

### 18.2 Windows 11 – Mica and Acrylic, with a strict rule

| Material | Nature | Use |
|---|---|---|
| **Mica** | **Opaque**, subtly tinted by the user's desktop background, **very performant** | **Long-lived surfaces** – title bars, app backgrounds. Communicates active/inactive state |
| **Acrylic** | **Semi-transparent**, frosted glass; brighter and more translucent in Windows 11 | **Only transient, light-dismiss surfaces** – flyouts, context menus |

**That is the same layering discipline as Liquid Glass (§3.3) and it is worth stating as a cross-platform law:**

> **Translucency belongs to transient, functional chrome. Opaque materials belong to persistent surfaces and content.**

Apple and Microsoft arrived at the same rule independently. WinUI now allows **per-element Acrylic** rather than whole-window backdrops only – which makes it easier to misuse, so the rule matters more.

### 18.3 Linux – GNOME and KDE

Concrete constants worth having:

| | GNOME (Adwaita, GTK4 + libadwaita) | KDE (Breeze) |
|---|---|---|
| Base spacing | **8px** | **12px** |
| Focus ring | **2px** | **10px** |
| System font | **Cantarell, 16px** | – |
| Shadows | **12% opacity** | Variable gradients |

GNOME's HIG covers design principles, text and icon conventions, and patterns for containers, navigation, feedback and controls. KDE published a **new HIG** (led by Nate Graham) aimed at being *"100% actionable"* and easier to contribute to.

`Design consequence for a Rust/Slint app targeting Linux (§5.3):` there is **no single Linux look.** GNOME and KDE disagree on spacing, focus and shadow. Either pick the `native`/`qt` style and inherit whichever is present, or **commit to your own coherent style and stop pretending to be native** – the half-way position looks wrong in both environments.

### 18.4 Desktop windows and state

- Treat desktop windows as **independent containers with their own input model and lifecycle**, not one mobile screen repeated.
- **Users expect state restoration.** Snapshot open windows and tabs; restore on next launch.
- **Save state incrementally as the user works, not at shutdown** – *"reduces the risk of data loss and distributes the I/O cost."* Same conclusion as mobile lifecycle (§15.1), different reason: on desktop the risk is a crash, on mobile it is termination.
- Coordinate shared state across windows via a database, IPC or a local server rather than duplicating caches per window.
- Restore **position and size**, especially on multi-monitor setups.

### 18.5 Shortcut conventions across desktops

- **Modifier naming differs:** Windows **Alt** = macOS **Option**; Windows **Win** = macOS **Command**. Shift and Ctrl match.
- The primary modifier differs too – **⌘ on macOS, Ctrl on Windows/Linux** – so shortcuts must be declared per platform, not hardcoded (web §7.26).
- Attach accelerators to **menu items**, which makes them discoverable rather than secret.

## 19. Wearables, TV, auto, spatial, embedded

### 19.1 TV – the focus model replaces the pointer

**tvOS Focus Engine:** the system moves focus between elements based on **geometry first** – nearest eligible element in the direction pressed, by alignment and proximity.

`This is a genuinely different interaction model.` There is no cursor and no touch point: **the layout *is* the navigation graph.** Two elements that look adjacent but are misaligned may not be reachable from one another.

Requirements:

- **Every interactive element needs an unmistakable focus state** – **scale 1.05–1.1×**, elevation/shadow, brightness change, or border. Subtlety is a failure here; the user is 3 metres away.
- **Minimum card target ~250×150pt.**
- **Large readable text, high contrast, clean layouts** – 10-foot viewing at roughly **3 metres**.
- Android TV: **d-pad navigation**, and *"TV UI must provide instant and distinct feedback when buttons are pressed."* **Compose for TV** is the recommended framework.
- Leanback's model – content in rows, `ImageCardView` – remains the structural convention.

`Design consequence:` the focus state is the single most important visual in the whole interface, which inverts the usual priority. And web §8.2's focus-ring rules become the *primary* design surface rather than an accessibility obligation.

**tvOS 26 update.** Apple TV picked up the Liquid Glass redesign, with the stated intent of keeping focus on what is playing.

The focus engine is the whole interaction model and it has hard requirements:

- **Every interactive element needs an unmistakable focus state.** The conventions are a scale of roughly **1.05–1.1x**, an elevation shadow, a brightness lift, or a border. Subtlety is wrong here: the user is 10 feet away and not holding the device.
- **Focus movement must be spatially predictable.** A swipe right moves to the thing that is visually to the right. Grids where focus jumps unexpectedly are the defining tvOS usability failure.
- **Design for the 10-foot distance**: larger type, generous safe areas, fewer items per screen, and no information that requires reading rather than recognizing.
- **Moderate parallax and motion.** The layered parallax effect on focused artwork is a platform signature and it becomes nauseating when overused.
- **Top Shelf content** is the app's presence on the home screen when it is in the top row, and it is a real acquisition surface rather than an afterthought.

### 19.2 Automotive

- **CarPlay allows only strictly approved app categories** – navigation, audio, podcasts, messaging, EV charging – each safety-verified. *"Apple's design philosophy prioritizes minimal distraction over maximum customization."*
- **Android Auto has widened categories** (passenger video, media, casual games) but gates them on vehicle state.
- **Video functions only when parked**, with motion detection disabling it and falling back to audio.
- Android Automotive apps must follow **Driver Distraction Guidelines** to run while the vehicle is moving.

`The design position:` this is the only platform where **the user's attention is legally and physically constrained**, and where the correct answer to "can we add this?" is usually no. Everything in web §14.7's restraint argument applies at maximum strength.

**The template model, which is the whole design story for car apps.**

Neither platform lets an app draw whatever it wants. **CarPlay and the Android for Cars App Library both give you a fixed set of templates** – list, grid, map with pane, message, navigation, search, sign-in – and the app supplies content into them. The system owns layout, type size, contrast, and how many items may appear.

This is unusual and it is deliberate: the templates are the compliance mechanism. They are road-tested against government and industry driver-distraction guidelines, so an app built from them inherits that compliance rather than having to argue for it.

What it means in practice:

- **The design work is content strategy, not layout.** Which six items appear in the list, in what order, with what labels. That is the entire surface.
- **Item counts are capped** and the cap varies by vehicle. Design for the smallest allowance.
- **Voice is a first-class path**, not an accessibility afterthought. Any task that cannot be completed by voice is a task that should not be attempted while driving.
- **Category restrictions are real.** Both platforms only admit apps in approved categories (navigation, audio, messaging, EV charging, parking, driving-task adjacent). A general-purpose app cannot ship a car mode.
- **The glanceability budget is stricter than a watch's.** Every element exists to be understood in a fraction of a look while attention is elsewhere and legally required to be elsewhere.

`The transferable lesson:` a constrained template system produced apps that are more consistent, more accessible and faster to build than free-form design would have. That is worth remembering the next time a design system's constraints feel restrictive.

### 19.3 Spatial – visionOS

**The organizing principle, and it is the sharpest statement of depth found anywhere:**

> **"Depth is information – use z-axis positioning to communicate hierarchy, not decoration."** And: *"windows float, backgrounds don't exist, and depth is literal, not metaphorical."*

- **Windows are planes in space** with familiar controls; users reposition them, and **dynamic scaling keeps content legible near or far.**
- **Ornaments** are floating toolbars outside the main window, keeping content clean while providing persistent controls. **They overlap the window's bottom edge by 20 points** to anchor visually, and sit slightly in front of the window to establish depth hierarchy.
- The glass material **lets light and color from the user's actual room bleed through** *"making the app feel like it belongs there."*

`Cross-reference §3.3 and §12.2 of the web study:` Liquid Glass, Windows Mica/Acrylic and visionOS glass are the same idea at three scales, and all three carry the same rule – **depth carries information, or it does not belong.** visionOS is where that stops being a metaphor.

### 19.4 Wearables

**The five-second rule governs everything.** *"Most smartwatch interactions last under five seconds, so a glanceable user experience is essential."* Wear OS tiles are explicitly *"designed around the five-second interaction rule."*

**Wear OS:**
- **Watch Face Format (declarative XML, built with Samsung) is required for installing watch faces on all Wear OS devices as of January 2026.** A real deadline, not a recommendation.
- **Up to eight complications** per watch face.
- Complication types map to shape: **`RANGED_VALUE` and `GOAL_PROGRESS` on edge Arc complications; `SHORT_TEXT` and `SMALL_IMAGE` on circular ones.**

**watchOS** (§16.8): families define templates; **line widths ≥2pt** because *"the wearer is in motion"*; support all families and fall back to an app-representative image.

`The wearable constraint set is the extreme case of everything else in this study:` one piece of information, maximum contrast, no reading, motion tolerance, and a five-second budget. **It is the best possible test of whether you know what the product's single most important value actually is** – the same question as the widget glance test (§16.6) and the squint test (web §5.10), with no room left to hide.

### 19.5 Embedded HMI, kiosks and the regulatory reversal

**The most consequential single fact in this section:**

> **From 2026, Euro NCAP penalizes vehicles for over-reliance on touchscreens for essential functions, requiring physical controls with tactile feedback for core functions.**

`A safety regulator has formally reversed the touchscreen-everything trend.` For any automotive or safety-adjacent interface, **the physical control is now the compliant design**, and a touchscreen for a core function is a scored defect. This is the clearest available evidence that "everything is a screen" was a fashion rather than a conclusion.

**Industrial HMI principles:** clarity, responsiveness, safety, usability. *"Strong HMI design centers on situational awareness – helping users instantly understand what the system is doing and what's normal."*

- The 2025–26 direction: **consumer UX principles imported into industrial HMI**, replacing dense data-heavy screens with cleaner layouts that emphasize *actionable* information – improving learnability and reducing operator training time.
- Automotive interfaces trending toward **context adaptation**: surfacing navigation in heavy traffic, simplifying under demanding driving conditions.

**Designing for constrained displays** (directly relevant to Slint on MCU, §5.7):

- *"Every visual element, animation and font consumes resources. The goal is maximum clarity with minimum overhead – prioritize essential information and remove anything that does not directly serve a user's goal."*
- **Use shapes and contrast rather than color gradients.** Avoid complex graphics and fine detail, which pixelate at low resolution.
- Compress icons, images and fonts; load only what is needed; release promptly.
- Where there is no touchscreen, organize for **button or rotary-encoder navigation** – which is the same focus-graph problem as tvOS (§19.1).

`This is the environment where web §15's whole restraint argument is not aesthetic preference but a hard budget.` Everything the corpus recommends – one accent, no decorative gradients, no shadow tiers, hierarchy from scale and surface – is also what fits in 300KB of RAM.

**The memory budget, stated in numbers, because it is what actually constrains embedded design.**

An MCU GUI framework adds a fixed base footprint of **tens of kilobytes**, and a smooth 25 fps UI is achievable on a 32-bit MCU with **a few hundred kilobytes of RAM**. Everything above that base is assets, and assets are where designs die.

| Asset | Cost | Lever |
|---|---|---|
| Full CJK font | **10–30 MB of flash** | **Selective glyph compilation**: scan every string in every locale and compile only the referenced glyphs. A typical Japanese HMI drops to **300–800 KB** |
| Glyph cache | ~4 KB, configurable | Eliminates repeated rasterization for live numeric readouts, which is a known hotspot in instrument clusters |
| Full-frame buffer | width × height × bytes per pixel | Line-by-line rendering (§5.20) removes it entirely at the cost of full-frame effects |
| Compressed images | Smaller flash, higher CPU and a temporary RAM buffer | A genuine trade, decided per product |

`The design consequences, which are unlike any other platform in this study:`

- **Every glyph you use costs flash.** A design with three type families and six weights is not a style choice on an MCU, it is a bill of materials. Restraint here is not minimalism, it is engineering.
- **Localization changes the hardware requirement.** Adding Japanese to a product that shipped in English can exceed the flash budget outright, which is why locale coverage belongs in the design brief.
- **Animations are cheap only if they redraw a small region.** A full-screen transition on an MCU is often simply impossible, so motion is designed as local state change rather than screen movement.
- **Effects that sample the frame – blur, translucency, drop shadows over content – are largely unavailable.** Flat fills, gradients, sharp edges and careful typography are the vocabulary, which is why good embedded UI often looks cleaner than its desktop equivalent.

### 19.6 Kiosks and self-service terminals

**In scope for the EAA (§11.8)**, so this is a compliance surface, not just a usability one.

- **Reach: controls and screens no more than ~48 inches (1220mm) from the ground.**
- **A touchscreen alone cannot serve everyone.** Required alternatives: **tactile keypad with a consistent layout, raised keys and Braille**, audio guidance, and voice input where possible.
- High-contrast visuals, large type, generously sized and spaced targets for limited dexterity.
- Simple, direct, jargon-free instructions; familiar icons; step-by-step guidance.
- Adjustable screen height where the enclosure allows.

`Note how much of this is web §8.13's older-adult guidance and web §4.4's target sizing, made physical.` The kiosk is the case where "it works on my phone" has no meaning at all – the user did not bring their own device, their own settings, or their own assistive technology.

### 19.7 Physical viewing conditions – the constraints screens do not have indoors

**Gloved operation** (industrial, automotive assembly, cold-chain, field service):

- **Minimum target 15–20mm for gloved use** – roughly 3–4× the 44–48px consumer minimum (web §4.4).
- Generous spacing and **clear visual feedback**, because tactile confirmation through a glove is poor.
- Touch technology matters at design time: **resistive and infrared work through gloves inherently; PCAP needs controller tuning** and may not.

**Sunlight and high ambient light** (kiosks, outdoor terminals, automotive, marine, field devices):

| Condition | Requirement |
|---|---|
| "Sunlight readable" baseline | **>1000 nits**, contrast **800:1** |
| Shaded / semi-outdoor kiosk | 1000–1500 nits |
| **Direct sunlight** | **2500–4000 nits** |

**The number that reframes the whole problem:**

> **A standard 200-nit LCD measuring 300:1 contrast in a dark room falls below 2:1 in direct sunlight** – because surface reflection masks the emitted light.

- **≥5:1 ambient contrast ratio is required for daylight readability.**
- Anti-glare coating plus optical bonding to bring surface reflectance **below 1%**.
- Measured effect: **raising ambient contrast ratio from 3:1 to 8:1 shortened visual confirmation time by 45%** at outdoor self-service terminals.

`This is web §2.3's contrast argument with the ambient term restored.` **WCAG contrast is measured on the rendered colors; ambient contrast is what the eye actually receives.** A design that passes 4.5:1 in the design tool can be functionally invisible outdoors. For any outdoor or vehicle surface: **maximize luminance contrast, avoid mid-tones, prefer pure black-on-white or white-on-black, and never rely on subtle tonal separation** – the whole low-contrast-secondary-text convention (web §1.9) fails here.

**Night and low-light** (automotive, marine, aviation, medical):

- **Red is the only color that does not impair night vision.** Long-wavelength light is *"easily visible to daylight photoreceptors (cones) but almost invisible to night vision photoreceptors (rods)"* – so a red night mode preserves dark adaptation.
- Night mode targets **under 50 cd/m²**, switched automatically from an ambient sensor or manually.
- Hues chosen for **mesopic vision** at dusk and dawn, not just for aesthetics.

`This is the one place where a color choice is a physiological engineering decision`, and it is worth knowing precisely because it contradicts everything else in the study: in a vehicle at night, **red is not an error color – it is the readable color.**

### 19.8 Wear OS 6 and Android XR – the two 2026 updates

**Wear OS 6** brought Material 3 Expressive to the watch, fully optimized for round displays: new scrolling animations and transitions, dynamic color theming that matches the watch face, and redesigned buttons, progress indicators and scroll indicators shaped for a circle rather than adapted from a rectangle. It also claims up to 10% better battery life than Wear OS 5.

The concrete design change is **the three-slot tile layout: title, main content, bottom slot.** That is the whole canvas. A tile that needs a fourth region is a tile that should be an app screen, and the constraint is what keeps tiles glanceable (§19.4).

**Android XR** is Google's spatial platform, with published design guidance covering three device categories: XR headsets, wired XR glasses, and AI glasses.

The numbers worth carrying:

- **Spatial panels are the building block** – a 2D layout floating in space – and they **scale automatically with distance**, holding apparent size between roughly 0.75 m and 1.75 m from the user.
- Maximum panel size in Full Space is **2560 × 1800 dp**; there is no minimum.
- The conversion is **0.868 dp to dmm**, and because content sits farther away than a phone, **everything must be larger**.
- **Minimum touch target 48 × 48 dp, optimal 56 × 56 dp or more.**
- Depth and elevation are available on standard UI components, plus 3D entities (glTF/USDZ), stereoscopic video and 360-degree environments.

`The shared conclusion across visionOS (§19.3) and Android XR:` the same rule holds on both – **depth carries information or it does not belong**, targets grow with distance, and a flat panel floating in space is still a flat panel that has to obey every layout rule in this study.

## 20. Distribution and store presence

### 20.1 App icons

| | iOS | Android |
|---|---|---|
| Master | **1024×1024, fully opaque, no alpha channel** | **Adaptive: foreground + background PNG, each 108×108dp** |
| Mask | System applies a **squircle** (superellipse) | Manufacturer-dependent: circle, rounded square, squircle, other |
| Safe area | – | **Only the inner 66dp is guaranteed visible** |

- **Design at 1024px, then check legibility at ~40px** – the size it is actually seen at in search results.
- *"The strongest-performing icons feature a single recognizable element on a clean background: no visual clutter, no secondary symbols, no tiny text."*
- **Icon quality is reported as the #1 factor in tap-through rate from search, with a well-designed icon improving listing conversion 15–30%.** `[secondhand, ASO-vendor sourced]`

`Exactly web §13.9's favicon test at higher stakes`: if the mark is not legible small, it is the wrong mark – and on Android, **22% of your artwork can be cropped away by a manufacturer mask you do not control.**

**iOS 26 changed the icon pipeline.** The submitted asset is still a single opaque 1024×1024 square, but the system now composites it as layered glass with depth, reflection and light response, and generates the light, dark and tinted variants automatically. Icon Composer is the tool for supplying the layers. Two practical consequences: a mark built from a few bold shapes survives the glass treatment and a detailed illustration does not, and the icon can no longer be designed as a fixed picture – it has to be designed as a subject that holds up under a material effect it does not control.

### 20.2 Screenshots

- **Three to five is the practical set.** *"The first three carry the install decision because they show in search and on the product page before anyone taps through."*
- **The 60/40 heuristic: at least 60% of frames showing real app UI, no more than 40% lifestyle or marketing.**
- **Platform split: iOS rewards lifestyle framing, Android rewards feature clarity.**
- Wrap real UI in a **current device frame**; keep type readable **at thumbnail size**; keep the set visually consistent.
- **Apple OCRs screenshot text and indexes it** – so the words on the images are searchable content, not decoration.
- Narrative order that recurs: **establish the outcome → show how it works → prove breadth or trust → give a reason to act.**
- Base sizes: **1320×2868 (6.9" iPhone)**, **2064×2752 (13" iPad)**; Apple downscales for smaller devices.

**Google Play feature graphic: 1024×500, JPEG or 24-bit PNG, no alpha, keep under ~2MB.**

- **When a promo video exists, Google overlays a play control on the center.** So compose with the **center left as the video's stage** – product slightly off-center, headline in the upper third – and it reads correctly in both states.
- It is a **mood and personality asset**, not a feature demo, and it may be resized or cropped, so nothing critical near the edges.

`This is web §13.9's OG-image problem` – safe zones, dual states, and an asset most people see without visiting the product.

### 20.3 What the stores reject

- **Placeholder "Coming Soon" screens, non-functional buttons, and Lorem Ipsum text** – *"one of the most common reasons for first-time rejections."*
- **Metadata mismatch:** *"Your screenshots show UI not present in the build."* Every claim must match what the reviewer sees running the app.
- **Minimal functionality / web-wrapper with no native value.** **Apple removed over 42,000 apps in 2024 specifically for design violations**, including copycats and minimal-functionality apps.
- 2026 tightening: **data transparency, AI disclosures, user experience and clear pricing.**

`Two design consequences:` the **placeholder rule makes the "all five states" discipline (web §7.1) a shipping requirement**, not a nicety – an empty state that says "Coming soon" is a rejection. And **screenshots are part of the product**, not marketing that can run ahead of the build.

### 20.4 Store-page conversion timing

- **Users decide in under seven seconds**; the best assets *"communicate value in under two seconds."*
- Visual assets – icon, screenshots, preview video, feature graphic – are the **primary drivers of conversion rate.**
- A/B test via **Store Listing Experiments** or ad campaigns. Copy first, then placement (web §7.35).

### 20.5 Paywalls

- **Place the paywall at the end of onboarding**, after a quiz, a goal, or a preview. *"Small interactive steps before the paywall increase emotional investment and reduce drop-off."*
- **Comparison tables are now near-universal** because *"a significant percentage of users at the paywall still don't fully understand what they're paying for."* (web §7.43.)
- **Use the smallest set of plans that represents meaningful choices** – one removes comparison, two separates flexibility from savings, three needs a real reason. Same discipline as web §7.17's three-tier finding.
- **"Nine in ten subscriptions sell at full price. The instinct to discount when seeing low conversion is usually wrong."**
- Post-close welcome offers (a 24-hour discounted annual) are now standard enough that **not having one is the unusual choice.**
- **Remote paywall configuration** (Adapty, RevenueCat Paywalls, Superwall) lets variants ship without App Store resubmission – *"one of the clearest process advantages available to subscription app teams in 2026."*

`ETHICS NOTE (web §7.23):` the onboarding-investment-then-paywall pattern is the sunk-cost mechanism again. It is legitimate when the steps deliver real value or personalization and the price is stated plainly; it becomes a dark pattern when the steps exist only to manufacture commitment before a price the user has not been shown.

### 20.6 Launch screens

The two platforms disagree, and following the wrong one gets an app rejected.

**iOS:** the launch screen is a static skeleton of the first screen, not a brand moment. Apple's guidance is to mirror the first screen's layout without dynamic content, and to avoid text, logos and taglines. Text cannot be localized on a launch screen, which is the stated reason. **A launch screen that presents as a branded splash with an animated reveal is a rejection cause.** The intended effect is that the app appears rather than loads.

**Android:** the opposite. The Android 12+ Splash Screen API is a system-level splash with a centered app icon on a configurable background, and it is the sanctioned brand moment. Opting out of it and building a custom splash Activity is now the wrong pattern, because the system splash shows anyway and the app gets two.

Shared rules:

- **Under two seconds, and no artificial delay.** One source puts abandonment at 12% for splash screens over two seconds; even discounting the exact figure, an added delay is indefensible.
- Never animate a logo into a load. If the app is ready in 400 ms, an 1,800 ms animation is 1,400 ms of deliberately wasted user time.
- The launch screen must look correct in both color schemes and at every screen size and orientation.
- If real loading is unavoidable, get past the launch screen into the app's own skeleton UI (§21.9), where progress can be shown honestly.

### 20.7 Deep links

Universal Links (iOS) and App Links (Android) are HTTPS URLs that open the app when installed and the web page when not. Custom URL schemes still exist but are unverified and should not be the primary path.

The design problems, which are all about state rather than routing:

- **Cold start is the case that breaks.** When the app is not running, the link arrives before the app has state. The router has to wait for initialization and then navigate, and the common bug is dropping the link because the handler ran too early. On iOS the link comes through `application(_:continue:restorationHandler:)`; anything that reads it before the session is restored loses it.
- **The back stack has to be synthesized.** A user who lands three levels deep from a link needs a way up that is not "exit the app". Both platforms support building a synthetic parent stack, and skipping it is the most common deep-link UX failure.
- **Context by source is a real design choice, not a nicety.** A link from a marketing email, a shared link from a friend, and a link from a push notification arrive at the same screen but deserve different framing. A first-time user arriving deep in the app needs contextual onboarding at that screen, not the standard first-run flow.
- **Authentication interrupts the link.** Store the destination, authenticate, then continue to it. Dropping the user on the home screen after login is the second most common failure.
- **Test warm start, background and cold start separately.** They take different code paths and only cold start is usually broken.

### 20.8 App size

Size is a conversion variable, not just an engineering metric.

- **Google's own figure: every 6 MB of download size costs roughly 1% of install rate.**
- **Android App Bundles cut download size 15–20%** by serving only the code and resources a given device needs. Adobe reported a **5–7% install conversion increase** from adopting bundles with dynamic delivery.
- **The effect is regional.** In markets with expensive data and low-storage devices, removing 10 MB is reported at around **2.5%** install conversion – roughly 2.5x the global average. If the product has growth ambitions in India, Brazil or Indonesia, size is a growth lever rather than hygiene.

`Where the design decisions land:`

- **Raster assets are almost always the largest removable chunk.** Vector where possible (SF Symbols, Material Symbols, SVG), and one raster set rather than five when the platform can scale.
- **Fonts.** A custom family at four weights, each with a full Latin-Extended plus Cyrillic plus Greek coverage, is easily several megabytes. Subset it.
- **Onboarding videos and Lottie files** are worth their size only if they measurably help. They usually are not measured.
- **On-demand resources** (iOS) and **dynamic feature modules** (Android) let large, rarely used assets download later. A tutorial video, an alternate icon pack or a rarely used mode does not belong in the initial download.

The related number worth pairing with it: iOS still warns users before a cellular download above a threshold, and crossing it turns an install into "later", which usually means never.

### 20.9 Alternative distribution in the EU

The DMA opened iOS to alternative marketplaces and web distribution in the EU, and the terms have moved twice: a layered fee model in January 2026, replaced on **18 August 2026** by a single set of business terms for every EU developer, eliminating the Core Technology Fee, the Initial Acquisition Fee and the Store Services Fee. What a developer now pays depends on **where the payment happens** (Apple IAP, alternative in-app processing, or web checkout) and **how the app is distributed** (App Store, alternative marketplace, or the developer's own site).

The ecosystem is real but unsettled – alternative stores exist and are shipping, and at least one (MacPaw's Setapp store) shut down in February 2026 citing complex, still-evolving terms.

`The design consequences, which are the part that outlasts the fee schedules:`

- **The install flow is no longer one flow.** A user arriving from the web, from a marketplace, or from the App Store lands differently and needs different framing (§20.7).
- **Trust signals move onto the developer.** Outside the App Store, the store page's implied vetting is gone, so the product's own site and first-run experience carry the credibility.
- **Payment choice becomes an interface decision.** If in-app purchase, alternative processing and web checkout all exist, the user sees a choice they did not ask for. Present one recommended path and keep the others available rather than presenting a menu of payment rails.
- **Updates are your problem** outside the App Store, which makes an in-app update prompt a designed screen rather than a system affordance.

## 21. Native craft and platform idioms

### 21.1 What "polished" means on native

The best statement of it found anywhere in this study:

> **"A native app feels polished not because it looks fancy, but because nothing feels slightly off. Users rarely say 'this app has abstraction overhead'. They say 'it feels clunky', then they leave."**

`That is web §6.33's "smooth" synthesis restated for native`, and it explains why cross-platform toolkits are judged harshly: the failure is never one big thing, it is fifty small mismatches with platform expectation – scroll physics, keyboard behavior, back semantics, haptic absence, transition timing – each individually deniable.

The 2026 list of what actually signals native craft: **Live Activities, App Intents, passkeys, on-device models, haptics, App Clips, Liquid Glass adoption.** *"Live Activities and interactive widgets buy back retention. App Intents buys distribution."*

### 21.2 Swipe actions – the convention has meaning

**Trailing swipe (right-to-left) is conventionally destructive; leading swipe (left-to-right) is conventionally non-destructive.** Breaking that puts Delete where the user expects Archive.

- Use when *"users can make decisions from the limited information shown in the row"* – quick, frequent actions like delete, archive, flag.
- **Do not use when the decision needs more context.** Then the action belongs in the detail view, a menu, or on the card.
- **One primary (visually prominent) swipe action per row.**
- **And per WCAG 2.5.7 / 2.5.1 (§11.1), every swipe action needs a non-swipe path** – a tap-reachable menu or an `accessibilityAction(named:)` (§11.3). This is not optional.

### 21.3 Haptics – three generators, distinct meanings

| Generator | Meaning | Use |
|---|---|---|
| **`UIImpactFeedbackGenerator`** | A physical impact occurred. Light / medium / heavy, plus custom intensity | *"A physical metaphor that complements the visual experience"* – something landed, snapped, hit a boundary |
| **`UISelectionFeedbackGenerator`** | **A selection changed** | Pickers, segmented controls, moving through options |
| **`UINotificationFeedbackGenerator`** | Outcome: **`.success` / `.warning` / `.error`** | Result of an operation |

**Apple's own caution:** *"Using haptics inappropriately may confuse users, particularly those reliant on haptic feedback. Avoid using the wrong haptic for a given situation."*

`This is a vocabulary, not a set of buzz intensities` – and it maps exactly onto web §6.18's "use OS defaults for standard flows". A `.success` haptic on a failure is as wrong as a green error message.

### 21.4 Shared element transitions

Google Photos and the iOS App Store use this heavily because *"it removes one of the most common causes of disorientation in deep navigation flows. When a thumbnail expands into a full detail screen, with the image growing from its list position to fill the viewport, users understand exactly what happened and how to return."*

- **iOS 18+: `navigationTransition`.** Android: shared element transitions in Compose.
- Reported: **reduces cognitive load and increases retention by up to 40%** (attributed to a Google UX report). `[secondhand]`

`Same finding as web §6.8:` **continuity of identity** is what a shared-element transition buys – the thumbnail *becomes* the hero, so the user never loses the thread. Both platforms now ship it as a primitive, which removes the last excuse.

### 21.5 Context menus

Long-press reveals **actions alongside a content preview** – an improvement on Peek & Pop specifically because the preview and the commands are visible together.

- *"Prefer a graphical preview that clarifies the target of the menu's commands"* – Notes and Mail show a condensed version of the actual content.
- **The layout reconfigures by context:** iPhone portrait stacks preview above actions; iPhone landscape places them side by side; iPad reconfigures by available space.
- Supports **child actions and dividers** – it is a real menu, not a flat action list.
- The preview is tappable to open and draggable to another target.

`Design consequence:` the preview is the design opportunity most apps waste. A context menu whose preview is just the row again teaches nothing; one showing the *content* answers "which item is this?" before the user commits.

### 21.6 Sheets and detents

- **System detents: `large`** (full height) and **`medium`** (~half). **iOS 16+ supports custom detents.**
- **Support `medium` for progressive disclosure** – a share sheet shows the most relevant items without resizing.
- **Skip `medium` when the content needs full height** – Mail and Messages compose sheets present only at full height because the task needs the room.
- **Include a grabber on a resizable sheet.** It signals draggability **and cycles through detents on tap** – a discoverable affordance for a gesture (web §6.26).
- **Modal sheet** for a single task needing no parent interaction; **non-modal** when both must stay live.
- *"A sheet allows some of the parent view to remain visible, helping people retain their original context"* – which is exactly web §5.12's modal-vs-page test, resolved in the sheet's favor.

### 21.7 Keyboard handling is a design problem

> **"Keyboard handling belongs in product design, not just inside a utility component – when someone focuses a field, the software keyboard appears and primary actions can disappear below the fold."**

- **Ensure the focused field scrolls into view.** Test it; do not assume.
- **Sticky submit above the keyboard**, and **never put critical controls at the very bottom** where the keyboard will cover them.
- **Trigger the correct keyboard per field** – numeric for phone and codes, email for email. Free, and it removes several taps per field.
- **3–5 fields maximum in a single step; past ~7, split into steps** (web §7.23).
- Single column; avoid dropdowns where a better control exists.

`This is the native-only failure mode with no web equivalent`, because the web keyboard does not resize the viewport the same way. It is also one of the most common reasons a form "feels clunky" (§21.1).

### 21.8 Pull to refresh

- Still standard, still expected on feeds. Chrome augments it – pull-and-release refreshes, pull-and-swipe-right closes the tab, swipe-left opens a new one.
- **It relies on fine touch input and excludes users with motor impairments** – *"providing a visible, tappable refresh button gives everyone an alternative."*
- The governing rule for all of this: **"gestures must always be shortcuts, never requirements"** (web §6.26, §11.1).

### 21.9 Empty and error states, natively

- Four types, same as web §7.2: **first use, user-cleared, error, no data.**
- Structure: concise headline, supporting line, relevant icon or illustration, one CTA, centered in the container.
- **The rule worth carrying:** *"A good empty state feels native to its function. The empty state of a calendar should not look like the one for a shopping cart."* Generic reused empty states are the tell of a design system applied without thought.
- **Error copy: state the affected action, what remains intact, and what the user can do.** Factual, no blame.
- **`SECURITY:` error states that expose stack traces, database errors or API keys are a security risk**, not just a UX one. *"Error messages should be written for users, not for developers."*

### 21.10 Mobile search patterns

The pattern vocabulary: **auto-complete, explicit search, dynamic (as-you-type), scoped, faceted, saved and recent, search forms, results, visual search.**

- **Scoped search is *"the fastest and easiest search pattern"*** because results appear before the query is finished.
- **Faceted search is scoped search plus changing facets** – facets change with each search, filters do not. Worth keeping the distinction (web §7.18).
- **Recent searches** serve as both shortcut and reminder; trending and category suggestions fill the pre-query empty state – which is otherwise a wasted screen.

### 21.11 The device matrix

- **8–12 device and OS combinations covers 90%+ of user traffic.**
- Minimum viable spread: **three Android manufacturers (Samsung, Xiaomi, Pixel), two recent iOS versions, two screen sizes per platform.**
- Build it **from your own analytics** – top devices, OS versions, regions – and **review it regularly** as devices and markets shift.
- **Hybrid strategy: emulators for speed and fast feedback, real devices for high-risk flows and release confidence.** *"Emulators cannot replicate hardware-specific issues like thermal throttling, GPS drift, or sensor latency."*

`Design-relevant translation:` **the design must be verified on a mid-range Android**, because that is where jank (§17.1), font rendering, and thermal behavior actually show. A design signed off on the newest iPhone has been signed off on the best case.

### 21.12 The metrics a designer should hold

**Performance floors:**

| Metric | Bar |
|---|---|
| **Cold start** | **< 2.5s at p90** (§17.5) |
| **Tap response** | **< 100ms** (web §6.15) |
| **Crash-free sessions** | **> 99.5%** |

> **"One crash in a user's first session multiplies their churn rate by about 3×."**

**UX signals that can be detected automatically and are design problems, not engineering ones:**

- **Rage taps – 4+ taps in one second on the same element.** Almost always means an affordance is lying: something looks tappable and is not, or is tappable and gives no feedback (§21.1).
- **UI freeze – no response to input for 2+ seconds.**
- Task completion rate by screen; time to first meaningful action; drop-off per onboarding step; conversion per paywall view.

**And the single strongest retention lever in the native study:**

> **"Reaching a real action inside 60 seconds roughly doubles day-7 retention versus a 3-minute onboarding."**

`That confirms §8.5 with a number attached`, and it is the argument to use when someone wants to add another onboarding screen.

**Note on measurement patience:** *"Retention changes usually need 3–6 weeks to show clearly, because you need cohort data over time."* A design change judged after a week has not been judged.

### 21.13 Experimentation on native is harder than web

Two structural constraints with no web equivalent:

- **App store approval delays** – a variant is not a deploy, it is a submission.
- **Offline users** – assignment and exposure logging cannot assume connectivity.

Hence the 2026 shift: **feature-flag-first platforms** (Statsig, LaunchDarkly, Amplitude Feature Experimentation, Firebase) rather than classic web A/B tools, and **remote configuration for high-churn surfaces** – paywalls especially (§20.5), where variants ship without resubmission.

`Design consequence:` **anything you expect to iterate on should be remotely configurable from the start** – paywall, onboarding copy, empty-state CTAs, notification timing. Baking those into the binary means a two-week loop per change, which in practice means they never get iterated at all.

### 21.14 Handoff

The declarative frameworks map unusually cleanly from a well-structured design file: **sections → views, rows → stacks, cards → reusable components, tokens → colors and modifiers.**

But the honest boundary: *"Figma screens can become SwiftUI view structure, styling, text and assets – **engineers still need to add state, navigation, data, accessibility and testing**."*

`That list is the point.` The generated part is the cheap part. **State, navigation, accessibility and testing are where a native app is actually designed** – and none of them are visible in a mockup, which is why the native equivalent of web §8.14's accessibility annotations matters even more here: specify focus order, announced labels, Dynamic Type behavior, back-stack semantics and the five loading/error/empty states explicitly, because the file cannot carry them.

### 21.15 Authentication

**The chain that works, in order:** **passkey (biometric or PIN) → biometric app push → email magic link → email OTP → SMS OTP last.**

- **Auto-triggering biometric enrollment on mobile increases adoption 30–50% versus manual opt-in.**
- **Jargon kills it.** *"Users who encounter 'FIDO2' or 'WebAuthn credential' in enrollment copy have significantly lower completion rates."* Say "Face ID", "fingerprint", "your device" – never the protocol name (web §8.8).
- **"Uncertainty during the biometric prompt is a primary driver of canceled authentication attempts."** The screen *behind* the system prompt has to make clear what is about to happen and why – the OS dialog is terse by design and cannot do that job.
- Reported: passkeys plus biometrics → **45% faster login**, less churn, higher store ratings.

`Cross-reference §8.5:` passkey signup is now the **default expectation** for new apps. A password field on a new consumer app in 2026 reads as dated before the user has typed anything.

### 21.16 Form input on native

- **Use the right input type per field** – email, tel, number, date. Native pickers eliminate typing errors; the email keyboard surfaces `@`.
- **`autocomplete` is the highest-leverage single attribute:** 50+ values including **`one-time-code`** for SMS verification. Reported **–25% form errors, +20% mobile conversion.**
- **`autocapitalize="words"` for names, off for email and URL** – otherwise the system "corrects" a valid entry into an invalid one, which the user then has to notice and fix.
- **16px minimum on inputs** (§4.9 / web §4.9).
- Every field is friction; ask only what is needed (web §7.10).

### 21.17 Onboarding personalization

- **Two to three questions.** *"Beyond that, they start to feel like homework and users begin dropping off."*
- **5–7 is justified only when each answer visibly shapes what comes next** – a fitness app's body-metrics quiz earns it because the plan visibly changes.
- **"Make the personalization visible. If a user's answers never appear to influence anything, they notice."** A quiz whose output is invisible is pure friction dressed as care – and it is the most common way this pattern fails.
- Apps with strong onboarding report up to **5× engagement and 80%+ completion.** `[secondhand]`
- Emerging: adjusting screen order and copy in real time from signup source, device and early taps – personalization as a continuous flow rather than a fixed branch.

`Note the tension with §20.5:` the same quiz that personalizes is the same quiz that manufactures sunk cost before a paywall. **The honest test is whether the answers change the product or only the pitch.**

### 21.18 Scroll physics – the invisible platform tell

- **iOS:** content moves **1:1 with the finger**, can overscroll past bounds, and **bounces back with the velocity change gracefully absorbed** – *"no noticeable discrete change in velocity."* Momentum is derived from pixels travelled ÷ time.
- **Android:** *"content simply stops, which can feel abrupt."*

`This is the single clearest example of §21.1's "nothing feels slightly off."` Scroll physics is the most-used interaction in any app and the one users have the most calibrated expectation for. **A cross-platform toolkit that gets scroll wrong will feel non-native no matter how correct the visual design is** – and it is worth testing explicitly, by hand, on both platforms, early.

`For own-canvas toolkits (§6.1) – Flutter, Compose Multiplatform, Slint, egui – scroll physics is something you inherit or implement, not something you get.` Check it before committing.

### 21.19 Settings and information architecture

- **iOS Settings is the reference implementation**: a clear parent-child hierarchy where categories contain subcategories (Privacy → Location Services, Camera, Microphone).
- **~7 items per level**, most important first, the rest grouped and pushed down (web §7.19).
- **Progressive disclosure** – *"a settings screen doesn't need every option visible at once."*
- Contextual help plus reliable **search** is what makes a genuinely complex settings tree usable.

**Validating the structure:** **card sorting to generate** the architecture, **tree testing to evaluate** it – *"card sorting helps you organize information; tree testing helps you understand if the organization makes sense to the user."* Tree testing belongs **after** the IA is drafted and **before** a high-fidelity prototype, which is exactly the point where native teams usually skip it.

### 21.20 Drag and drop

Drag and drop earns its complexity only when the list is long enough that reordering by other means is tedious. Moving an item ten places is a good case; moving it two is not, and a simple "move up / move down" or a sort control beats a drag interaction there.

The mechanics that make it feel right:

- **The dragged item follows the finger with no lag and no offset**, and lifts visually – scale up slightly, raise the shadow, reduce opacity a little. The lift is what says "this is now in your hand".
- **Leave a gap or a ghost where it came from.** Without it the user loses the reference point for how far they have moved.
- **Neighbors move out of the way with a short animation** as the insertion point changes, rather than the list rearranging on drop. The list is showing the result before committing to it.
- **Haptic on pick up, on each reorder crossing, and on drop.** This is the clearest case for haptics in the whole interface, because the interaction has no other physical feedback.
- **Auto-scroll near the edges** when the list is longer than the screen, with speed proportional to how close the finger is to the edge.
- **Long press to begin** on a scrollable list, because an immediate drag conflicts with scrolling. A dedicated drag handle avoids the conflict entirely and is the better choice on a dense list.
- **Accessibility needs a non-drag path.** Both platforms provide one (custom accessibility actions on iOS, a similar mechanism on Android), and reordering that only works by dragging is unusable with a screen reader and hard with motor impairments.

Cross-app drag on iPad and Android is a system capability, and supporting drop targets in a tablet app is a small amount of work that reads as polish out of proportion to its cost.

### 21.21 Visual regression testing

Snapshot testing captures a reference image of a component or screen and compares later renders against it. On mobile the standard tools are **swift-snapshot-testing** (and Uber's `iOSSnapshotTestCase`) on iOS and **Paparazzi** or **Shot** on Android, both with Compose support.

What makes it worth doing for design rather than only for engineering: render **every notable state across every environment** – light and dark, the largest Dynamic Type size, an RTL locale, a long translation, and two or three device widths. That matrix is exactly the set of configurations a designer cannot check by hand and that break silently.

The practical caveat every source raises: **pixel-exact comparison produces too many false positives** across device configurations and OS versions. Use a perceptual diff with a tolerance, run on a fixed simulator or a rendering library rather than real devices, and treat the baselines as reviewed artifacts – a changed baseline should be looked at by a person, not rubber-stamped.

This is also the mechanism that makes a design system enforceable. A token change that alters 40 components shows up as 40 image diffs, which is either confirmation or a caught mistake.

### 21.22 Protecting sensitive screens

A design decision, not just a security one, because it changes what the screen can show.

- **Android has a real mechanism:** `FLAG_SECURE` on the window blocks screenshots, screen recording and casting at the OS level, and the system tells the user the app blocked the capture.
- **iOS has no direct API.** The widely used technique is layering content inside a `UITextField` configured for secure entry, which the system excludes from screenshots and recordings. It works and is stable, but it is undocumented behavior. The documented paths are `UIScreen.isCaptured` (react to recording in progress) and `sceneWillResignActive` (blur before the app switcher snapshot).
- **Blur or mask on backgrounding is the one every app should do.** The system takes a snapshot for the app switcher, and a banking balance or a message thread sitting in the app switcher is a real exposure. This is cheap and it is visible polish.

Apply protection **per screen, not per app.** Blocking screenshots globally is hostile: users legitimately screenshot receipts, confirmations and error messages, and blocking that generates support load and bad reviews. Protect the balance, the card number, the OTP and the DRM content; leave the rest alone.

The related design rules: mask card numbers and account numbers by default with an explicit reveal, never put sensitive values in notification previews (§16.2), and respect the shared-context cases – external display, screen mirroring, an active recording.

### 21.23 Image loading

Images are the largest source of visible jank in a content app, and the fix is entirely a design decision about what shows before the pixels arrive.

The ladder, worst to best:

| Approach | Problem |
|---|---|
| Nothing, then the image appears | Layout shift, flashing |
| Spinner per image | Busy, and a grid of spinners is noise |
| Solid gray box | No shift, but dead |
| Skeleton shimmer | Good; signals loading without noise |
| **BlurHash or a dominant color** | Best; the placeholder already resembles the image |

**BlurHash** encodes a blurred version of an image in roughly 20–30 characters of text, small enough to ship inside the JSON payload that lists the images. The placeholder is therefore available instantly, with no second request, and the transition to the real image is a cross-fade from something that already has the right colors in the right places. A dominant-color swatch is the cheaper version of the same idea and covers most of the benefit.

Non-negotiable regardless of approach: **reserve the space before loading.** The aspect ratio must come from the data, not from the decoded image, or every load shifts the layout. This is CLS (web §14) with the same cause and the same fix on native.

Other rules that hold:

- Cache decoded bitmaps, not just the bytes. Decode is usually the expensive step in a fast-scrolling list.
- Cancel in-flight requests for cells that scrolled off. A list that keeps loading images the user has passed is why scrolling degrades over time.
- Request the size you will display. Downloading a 4000 px image for a 120 px thumbnail is the most common mobile bandwidth waste there is.
- Fade in over roughly 150–200 ms. An instant swap reads as a flicker; anything longer feels slow.

### 21.24 Validation timing

The timing rule, and it is well established: **validate on blur, not on every keystroke.** Validating while the user is still typing marks a half-typed email as wrong, which is both annoying and inaccurate.

The refinements:

- **After a field has failed once, switch that field to validating on input**, so the error clears the moment it is fixed. Waiting for another blur to confirm the fix feels unresponsive.
- **Clear the error as soon as the input is valid.** The disappearance is the confirmation.
- **Errors sit directly below the field**, not above it and not in a summary at the top, because horizontal space is scarce on a phone and the summary is off-screen by the time the user is typing.
- **Say what is wrong and how to fix it.** "Password must be at least 8 characters" beats "Invalid input" every time.
- **Never clear the user's input on error.** Losing typed data at the moment of failure is the single most infuriating form behavior.
- **Reveal errors sequentially** rather than lighting up eight fields at once, which reads as failure rather than guidance.
- Animate the error in over **200–300 ms**, enough to notice and not enough to wait for.
- **16 px minimum font size on inputs** on iOS, or Safari and web views zoom on focus. This applies to any embedded web content in a native app.

Mobile-optimized forms are reported at 86% completion against 34% for non-optimized ones. Treat that gap as the argument for spending real design time here.

### 21.25 Date and time pickers

Pick the control from the task, not from habit:

| Task | Control |
|---|---|
| Birth date, any date far from today | Wheel or text entry with a mask; a calendar makes you page through years |
| A date near today (booking, reminder) | Calendar with today visible and the next weeks reachable |
| A date range | Calendar with range selection, both ends visible at once |
| A time from a limited set (reservations, appointments) | **Tiles or a list of available times**, which also shows what is unavailable |
| An arbitrary time (alarm) | Wheel or dial, plus manual text entry |
| Start and end together | A dual-handle slider, since a wheel can only hold one value |

The recurring usability failures: small tap targets on calendar day cells, no way to jump years, no keyboard entry alternative to the dial, and dropdowns for month and year that are miserable with a thumb.

Rules: default to something sensible rather than an empty field, disable rather than hide unavailable dates so the constraint is visible, show the resulting selection in text near the control, and put the picker in a bottom sheet so it lands in the thumb zone.

### 21.26 Microcopy on native

Screen space is the constraint that makes native microcopy harder than web microcopy: there is no room for an explanatory paragraph, so every word does more work.

The four jobs microcopy actually does:

1. **Prevent mistakes**, while they are still cheap to fix.
2. **Explain consequences**, especially before anything irreversible.
3. **Build confidence**, by confirming the input is on the right track.
4. **Help recovery**, with a specific next step rather than a restatement of the failure.

The rules that follow:

- **Button labels name the outcome.** "Submit", "OK" and "Continue" are the worst labels available because they describe the mechanism, not the result. "Delete 3 photos", "Save draft", "Send to Anna" all tell the user what is about to happen.
- **Match the label to the heading.** A sheet titled "Discard changes?" with buttons "Yes" and "No" forces the user to re-read the question. "Discard" and "Keep editing" do not.
- **Uncertainty, not grammar, is the friction.** Most microcopy work is writing down something the team already knows, at the moment the user needs it: when the charge happens, whether this is reversible, what "sync" means here.
- **Write for the narrowest device and the longest translation** (§12.5). A label that is perfect in English at 390 pt and clips in German at the largest text size is not finished.
- **Keep terminology stable across the app.** A thing called a "board" in one screen and a "project" in another is the most common source of quiet confusion, and it is free to fix.
- **Voice is consistent, tone varies.** Playful in a success state and plain in an error state is correct; playful in an error state is not.

### 21.27 Account and profile switching

Needed whenever one person holds multiple identities in the product: personal and work, multiple teams, a parent managing children's profiles, or an admin acting on behalf of an organization.

What a good switcher does:

- **Makes the active identity unmistakable at all times**, not only inside the switcher. An avatar in the header with the account's own color or image is the usual solution, and it is what prevents the real failure mode: acting in the wrong account.
- **Shows which accounts are already authenticated** versus which need a login, because the two behave differently on tap.
- **Puts "Add account" in a fixed position**, usually at the bottom of the list, so it does not move as accounts are added.
- **Preserves per-account state.** Switching and switching back should return to where the user was, not to a reset home screen.
- **Confirms destructive or outbound actions with the identity named.** "Post as @workaccount" removes the entire class of wrong-account mistakes.

Placement conventions: the avatar in the top-leading or top-trailing corner opening a sheet on phones, a persistent rail entry on tablets and desktop. Google's list-with-active-highlight and Slack's always-visible rail are the two reference implementations.

### 21.28 Search, in detail

§21.10 covers where search lives; this is what it does once opened.

**The zero state does real work.** Before a character is typed, show recent searches (the highest-value item for returning users), popular or trending queries, and any browsable categories. An empty search screen wastes the most attention the feature will ever get.

**Autocomplete rules that measure:**

- **5–8 suggestions, no more.** Past roughly eight, users either ignore the list or spend too long reading it.
- **Highlight the matched substring**, so the relationship between what was typed and what is offered is visible.
- **Mix types deliberately** – query completions, direct results, and categories – but label them, because a suggestion that navigates and a suggestion that searches behave differently.
- **Apply a frequency threshold** before a query enters the popular list, or the suggestions fill with typos and one-off searches.
- **Suggestions must be tappable at 44 pt** and the list must not be hidden behind the keyboard.

**Keyboard economy:** open the keyboard automatically when search is entered deliberately, set the return key to "Search", disable autocorrect for anything that is a name or a code, and keep results updating as the user types rather than requiring a submit.

**Zero results is a designed screen, not a failure message.** Offer corrected spellings, related queries, popular searches, or a browse path. "No results for 'xyz'" alone is a dead end, and dead ends are where sessions end.

**Filters and scopes** belong after the first result set, not before it. Making a user configure a search before running one inverts the effort.

### 21.29 Text selection

Mostly free if you use standard text components, and mostly broken if you do not. That is the whole reason to care: a custom text renderer that cannot be selected, magnified or copied is a regression the team will not notice and every user will.

The system behavior to preserve:

- **Long press starts selection**, with grab handles at both ends.
- **A magnifier follows the finger** so the user can see what is under it. Android has provided the `Magnifier` widget since API 28; iOS has had the loupe since the beginning. Without it, precise placement on a phone is guesswork.
- **A floating toolbar** appears above the selection with Cut, Copy, Paste and More. On iOS this is the edit menu, which is extensible with app-specific actions and is an underused surface: "Define", "Translate", "Add to note" all belong there rather than in a separate flow.
- **Double tap selects a word, triple tap or a drag selects more.**

Design decisions worth making deliberately:

- **Make content text selectable and chrome text not.** A label that is part of the interface should not offer to be copied; a message, a code, an address or an error identifier should.
- **Never disable selection on anything a user might need to keep.** Order numbers, error codes, API keys and support references are the classic offenders, and blocking copy on them generates support tickets directly.
- **Offer copy explicitly where selection is awkward.** A tap-to-copy affordance on a code, with a confirmation, beats making the user select six characters precisely.
- **Test selection in RTL and in mixed-direction text**, which is where custom implementations break first.

### 21.30 Sonic identity

Sound is a brand surface most apps leave empty, and the reason to care is speed: **humans process sound in roughly 0.05 s**, faster than an image resolves.

- **A sonic logo runs 2–5 s.** Long enough to have character, short enough to be recognized. That is a launch, an unlock, a payment confirmation – once per session at most.
- **Everything else is functional sound** (§10.10), and it should be built from the same tonal material as the logo so the app sounds like one thing.
- **Design for the emotion, not the melody.** The useful brief is texture and feeling, not a jingle.
- **The recognizable examples are mechanical, not musical:** the camera shutter, the Apple Pay confirmation, the payment terminal beep. Each is a single event with a single meaning, repeated identically forever, which is precisely why it is recognized.

`The honest scoping note:` most apps should not commission a sonic identity. Most apps should pick three functional sounds, use them consistently, and default them off outside direct user actions. The full sonic branding exercise pays for products with an audio-first surface – voice, media, payments, automotive – and is decoration everywhere else.

### 21.31 The in-app message hierarchy

Four surfaces, and the mistake is almost always using a lighter one than the message deserves.

| Surface | Persists | Actionable | Use for |
|---|---|---|---|
| **Toast** | No, auto-dismisses | No | Passive confirmation: "Copied", "Saved" |
| **Snackbar** | No, but longer | **Yes, one action** | Confirmation with **undo** (§24.5) |
| **Banner / inline** | **Yes, until dismissed or resolved** | Optional | Ongoing state: offline, degraded service, expired payment |
| **Alert / dialog** | Yes, blocking | Required | A decision that must be made now |

The rules that follow:

- **Never put anything the user must see or act on in a toast.** It vanishes, it can be missed while looking away, and it cannot be recovered. This is the single most common instance of the failure.
- **Snackbar over toast when the app is in the foreground** and there is any recoverable action, because the snackbar can carry it.
- **Banner for anything ongoing.** "You are offline" is a state, not an event, so it belongs on a persistent surface until it is no longer true.
- **Alert only for a real decision.** An alert used for information is an interruption with an OK button, and it teaches users to dismiss alerts without reading them – which is what makes the next, genuinely important alert fail.
- **One at a time.** Stacked toasts and a banner and a dialog is a system with no hierarchy. Queue them, and drop the ones that are no longer relevant by the time their turn comes.

### 21.32 Choosing modality

The question is not "modal or not" but "how much of the user's context can I afford to take".

| Need | Surface |
|---|---|
| A quick choice or a short set of options | **Bottom sheet**, in the thumb zone, context visible behind |
| Supplementary detail about what is on screen | **Non-modal sheet** (§22.3) |
| A short focused task (a form of 2–5 fields) | **Medium-detent sheet**, escalating to full if the keyboard opens |
| A complex or multi-step task | **Full screen**, with a clear cancel and a clear commit |
| A destructive or irreversible confirmation | **Alert**, because it must block |
| A whole new context (onboarding, a purchase flow) | **Full screen modal** |

Mobile-specific rules:

- **A centered dialog box is a desktop pattern.** On a phone the equivalents are the bottom sheet and the full-screen modal; a small centered card wastes the thumb zone and reads as a web page.
- **Escalate rather than guess.** Start as a sheet and let it expand to full screen when the user needs more room. That is what detents are for.
- **Never interrupt an active task with a full-screen modal.** Let them finish. This applies to review prompts (§24.3), upsells, and "rate us" flows.
- **Modality has to be dismissible in the platform's way**: swipe down on iOS sheets, system back on Android. Blocking either is a bug users feel immediately.

### 21.33 Carousels

Carousels are used far more than they work, and the failure mode is that content past the first slide is rarely seen.

When they are defensible: a set of genuinely equivalent items (product photos, a small gallery), a short onboarding sequence, or a horizontally scrolling shelf inside a vertical feed. When they are not: putting primary content behind a swipe, or rotating messages the user did not ask to rotate.

The mechanics:

- **Three to seven items.** Past that, use a list.
- **Show a slice of the next item.** Partial visibility is the strongest possible affordance that there is more, and it beats any indicator.
- **Never autoplay.** It removes control, it interrupts reading, and it is an accessibility problem. If it must autoplay, it must pause on interaction and offer a stop.
- **Dots communicate position and count, and little else.** They are also poor touch targets. Prefer labels or thumbnails where the items are distinguishable, and treat dots as a status readout rather than a control.
- **Swipe must work**, and every dot or arrow must also be a real button with a label ("Go to slide 2") for screen readers and keyboards.
- **Do not loop infinitely without an indicator.** A carousel with no beginning or end gives the user no sense of having seen everything.

### 21.34 Progressive disclosure in settings

The pattern that lets one screen serve both a first-time user and a power user: defer advanced or rare options to a second level, and reveal information when it becomes relevant.

- **Label the door honestly.** "Advanced", "Show more", "More options" work because they set an expectation. A chevron with no label does not.
- **Two levels, not four.** A setting three taps deep is a setting nobody finds; that is what search in settings exists to patch, and patching is worse than structuring.
- **Defaults must be right**, because most users never open the second level. Progressive disclosure hides complexity, it does not excuse it.
- **The measured benefit runs both ways:** novices avoid mistakes and save time, and experts save time too because they are not scanning past options they never use.
- **Never hide something that changes what the user is currently seeing.** If a toggle affects the visible result, it belongs next to the result.

### 21.35 The benchmark numbers

Useful for calibrating expectations and for arguing about targets. Treat all of them as directional; they are aggregates across very different products.

**Retention.** More than **90% of users are gone before day 30.** iOS day-1 averages around **25.4%** falling to **5.3%** by day 30; Android day-1 around **20.2%** falling to **3.8%**. The median app across categories lands near **4% at day 30**; strong performers (75th percentile) sit at **5–8%**.

By category at day 30, roughly:

| Category | Day 30 (strong performers) |
|---|---|
| Social | 15–20% |
| Productivity | 10–18% |
| Fintech | 10–15% |
| Gaming | 4–8% |
| Ecommerce | 3–6% |
| Education | ~2% |

And a structural one worth citing: **subscription apps retain around 14% at day 30 against 5.4% for ad-monetized apps.** Business model shapes retention more than most design changes do.

**Push opt-in**, which decides whether the notification design matters at all:

- **iOS around 44–54%**, with the higher figure following Apple's revised permission prompts in iOS 18.2 – the first measurable iOS improvement in years.
- **Android nominally 91–97%**, but that number is inflated by legacy installs from Android 12 and earlier where the permission was granted automatically. On Android 13+ the app must ask, like iOS.

`How to use these:` as a sanity check, not a target. A product whose day-30 retention is 2% in a category where 12% is normal has a problem worth finding. A product at 6% in ecommerce is doing well, and chasing social-app numbers there is chasing a different business.

---

## 22. App category patterns

Each of these is a solved problem with a conventional shape. Deviating is allowed but must be a decision, not an oversight. Web equivalents are in web §12 where they overlap.

### 22.1 Video players

The control set is fixed by expectation: play/pause, scrub bar, volume, fullscreen toggle, captions, playback speed. Anything else is secondary and belongs behind a menu.

The mobile shift is that controls hide by default and gestures take over. The established gesture vocabulary:

| Gesture | Action | Origin |
|---|---|---|
| Single tap anywhere | Toggle controls visibility | Universal |
| Double tap left/right third | Seek back/forward 10 s | YouTube, now expected everywhere |
| Rotate to landscape | Auto-enter fullscreen | iOS/Android system behavior |
| Swipe up on video | Enter fullscreen | YouTube |
| Swipe down in fullscreen | Exit fullscreen | YouTube |
| Vertical drag left half | Brightness (Android convention) | MX Player lineage |
| Vertical drag right half | Volume | MX Player lineage |

The center third of a double-tap zone must stay neutral, otherwise a user tapping to show controls seeks by accident.

Scrub bars need chapter markers when chapters exist, and a preview thumbnail on drag when bandwidth allows. On a phone the scrub bar is the hardest target on the screen: give it a touch slop of at least 44 pt vertically even if the drawn track is 4 pt tall.

Auto-hide timing: 3 s of inactivity is the common value. Never auto-hide while the user's finger is down.

### 22.2 Camera UI

Components, in the order they are looked for: viewfinder, shutter, mode selector, flash, front/rear switch, last-capture thumbnail.

The rules that separate a competent camera from a bad one:

- The viewfinder is the interface. Every chrome element competes with the subject, so chrome floats over the preview with a scrim only where text sits.
- The shutter is bottom center, large, circular, high contrast. It is the only control that never moves between modes.
- Mode selection is a horizontally swipeable strip directly above the shutter, and swiping the viewfinder itself changes mode too. Both, not one.
- Aspect ratio changes (4:3, 16:9, 1:1) letterbox the preview rather than cropping silently, so the user sees what is being discarded.
- Grid overlay lines are thin and low opacity, visible without competing.
- Tap to focus needs a visible reticle plus an exposure slider that appears attached to it and fades after ~2 s.
- The capture confirmation is haptic plus a shutter animation, not a modal. Latency from tap to haptic is the entire perceived quality of the app.

Zoom: pinch works, but discrete 0.5x/1x/2x buttons are what people actually use, because they are repeatable.

### 22.3 Maps

The map is the whole screen. Everything else is an overlay, and the dominant overlay pattern is the non-modal bottom sheet (§21.6 for detents).

Google Maps is the reference implementation: a non-modal sheet shows place details and actions while the map stays pannable and zoomable behind it. Non-modal is the correct choice here because the map is context for the sheet's content. A modal sheet over a map is almost always wrong.

Sheet detents for maps typically run three-stop: peek (name plus primary action), medium (details), full (reviews, photos, hours). The peek stop must clear the home indicator and still leave the pin visible above it.

Other map-specific rules:

- Recenter button floats above the sheet's top edge and moves with the sheet, never under it.
- The user's own location marker and the selected pin need distinguishable shapes, not just colors.
- Attribution and legal text are required by every tile provider and must remain visible at the smallest sheet detent.
- Gesture conflict: a vertical drag near the sheet edge is ambiguous between panning the map and resizing the sheet. Resolve by giving the sheet a grabber and a hit area, and letting map drags win everywhere else.

### 22.4 Chat and messaging

The element list is long and every item is load-bearing: input field, send button, bubbles with sender identity, timestamps, avatars or presence, typing indicator, read receipts, and a failed-send state with retry.

What actually decides quality:

- **The keyboard relationship.** The message list is anchored to the bottom and must translate with the keyboard in the same frame it animates, not after. Anything else reads as a stutter every time the user types. See §21.7.
- **Bubble contrast over long sessions.** High contrast in both bubble directions, because a chat is read for minutes, not seconds. Low-contrast gray-on-gray incoming bubbles are the single most common failure.
- **Growing input.** The composer expands to a cap (commonly 4–6 lines) then scrolls internally. It never pushes the send button off-screen.
- **Failed messages** stay in place with a retry affordance, they are not removed and re-added. Silently dropping a message is worse than showing it failed.
- **Auto-scroll rule.** Scroll to bottom on new message only if the user was already at the bottom. Otherwise show a "new messages" pill. Yanking the view is the second most common failure.

For AI/chatbot surfaces specifically the four patterns that appear in every working implementation are structured formatting that chunks long responses, a typing or streaming indicator, quick-reply chips that scope an ambiguous query, and a graceful fallback state when the system cannot answer. Reported engagement effects of well-formed bubbles run high in vendor material (up to +72%) and should be treated as directional, not as a number to quote.

### 22.5 Feeds

Infinite scroll is right when content is homogeneous, discovery is the goal, and items are consumed rather than compared. It is wrong when the user needs to find one specific thing, compare options, or reach a footer.

Known costs, and they are real enough to be documented in research rather than opinion: infinite scroll is repeatedly identified as a primary driver of mindless scrolling, and studies report anxiety, fatigue and guilt as outcomes across age groups, not only teenagers. The EU and several regulators now treat some feed designs as dark patterns.

The middle option that usually wins: load a page automatically, then require a "Load More" tap. It keeps the speed of infinite scroll and returns control plus a reachable footer.

Practical requirements whichever is chosen:

- Scroll position must be restored when returning from a detail view. Losing position in a feed is the highest-severity feed bug there is.
- Item height must be known or reserved before content loads, or the feed jumps.
- A feed needs a genuine end state. "You are all caught up" is a feature.

### 22.6 Finance and banking

The design constraint is trust, and trust here is a specific, buildable thing: make security visible, make behavior predictable, remove ambiguity.

What that means concretely:

- Show transaction confirmations with the exact amount, recipient and account, on their own screen, before commit. Never inside a toast.
- Name the security state in plain language. "Signed in with Face ID" beats a padlock glyph.
- Balances need a hide toggle, and the default should respect whether the device is in a shared context (screen sharing, screen recording, external display).
- Destructive or irreversible money movement gets a confirmation that requires reading, not a double tap in the same place.
- Pending versus settled must be visually distinct, because the whole category of "where is my money" support tickets comes from that one distinction.

The 2026 direction is passive security: biometric and behavioral checks that stay invisible until risk rises, replacing constant interruption. Users should feel safe without being reminded that risk exists.

### 22.7 Health and fitness

One takeaway per chart. Health data tempts multi-series overlays and they fail on a phone.

- Line for continuous signals (heart rate, weight), bar for discrete daily totals (steps, sessions), stacked bar or ring for composition. Pie only for 2–3 slices.
- Every visualization should imply a next action. A sleep chart that does not suggest anything is decoration.
- Timeframe switching (day/week/month/year) is the primary interaction and belongs as a segmented control at the top, not buried.
- Dark backgrounds with one bright accent read well for glanceable metrics and are the category convention, but do not force dark on a category people also use outdoors.
- Health data is sensitive: it needs the same shared-context hiding as finance, and it must never appear in a notification preview by default.

### 22.8 Charts on small screens

The general rules, which apply beyond health:

| Desktop habit | Mobile correction |
|---|---|
| Chart title above | Title is the screen title, drop it |
| Full axis labels | First, last and one middle tick |
| Legend below | Direct labels on the series, or drop to one series |
| Hover tooltip | Tap and hold with a persistent readout |
| Multi-series overlay | Small multiples, or a series switcher |
| Truncated y-axis to show variation | Almost always misleading; start at zero for bars |

Adapt, do not shrink. A desktop chart scaled down is the single most common mobile dataviz failure. Research comparing animation against small multiples for trend visualization on phones supports splitting into small multiples over animating one chart through time.

Interactions must be finger-sized: a tap target on a data point needs padding well beyond the drawn dot, and swiping between periods should be supported since it is the natural gesture on a time series.

### 22.9 Commerce and checkout

Mobile web converts at roughly 1.53% against desktop 3.36%, and mobile web cart abandonment is reported around 97%; dedicated apps are where the gap closes (5–8% for well-built DTC apps). Those numbers are the argument for an app, and also the argument that checkout is where the app earns its existence.

The pattern that works:

- Wallet buttons (Apple Pay, Google Pay) at the top of checkout, not the bottom. One tap plus biometric is the whole flow.
- Guest checkout as the primary path. Around 24% abandon at forced registration. Invite account creation after the purchase.
- One page, or at most two steps. One-page checkout is reported to cut abandonment by roughly 20% by removing progress anxiety and back-button loss.
- Autofill everything the platform offers (§21.16). Address autofill and card scanning are both native capabilities that beat any custom form.
- Payment method diversity is worth roughly 12–15% conversion, including buy-now-pay-later where the audience expects it.
- Show the total including shipping and tax as early as the cart. Surprise cost at the last step is the top stated abandonment reason across every survey in this space.

### 22.10 Audio and now-playing

Audio is the category where most of the app runs outside the app.

- Lock screen and Control Center / notification media controls are not optional extras, they are the primary interface for a music or podcast app. On iOS this is `MPNowPlayingInfoCenter` plus `MPRemoteCommandCenter`; on Android it is a `MediaSession` with a media-style notification.
- Populate artwork, title, artist, duration and elapsed time. A media session without artwork looks broken on the lock screen and in Android's media carousel.
- Support the remote commands the hardware sends: play, pause, next, previous, seek, and skip-forward/back intervals for spoken content (podcasts use 15/30 s, music uses track skip).
- Audio focus and interruption handling is a design decision, not just an API call: decide whether a phone call ducks or pauses, and whether playback resumes afterward. Getting resume wrong is what makes an audio app feel unreliable.
- Background playback plus a persistent mini-player docked above the tab bar is the expected in-app shape. The mini-player expands to the full now-playing screen with a shared-element transition (§21.4).
- Sleep timers, playback speed and skip-silence are podcast-category table stakes.

### 22.11 Dense data and tables on a phone

A table scaled down is not a mobile table. The desktop table has to be restructured, and there are only a few structures that work:

| Pattern | When | Cost |
|---|---|---|
| List-detail | The row has one identity and many attributes | Two taps to compare two rows |
| Card per row | 3–5 fields matter, the rest are secondary | Vertical space, no column scanning |
| Frozen first column, horizontal scroll | Genuine comparison across many columns | Discoverability of the hidden columns |
| Column picker | Power users, variable needs | Extra configuration surface |
| Transposed (one row per screen) | Very wide, few rows | Loses the overview entirely |

List-detail is the default answer and the one to reach for first, because it is an established mobile pattern rather than a table workaround.

Rules that apply to all of them:

- Prioritize columns explicitly. There is a primary field (the identity), one or two supporting fields, and everything else is detail. Cramming every field into the default view is what makes dense tables unreadable.
- Right-align numbers, use tabular figures, and keep the decimal places consistent down a column (see web §5 on numerals).
- Status is a badge with a word, never a color alone.
- Row targets are 44 pt minimum even when the text is small.
- Inline editing in cells works and is expected in spreadsheet-like apps, but it needs an explicit commit or a visible saved state, because a tap outside is ambiguous between commit and cancel.
- Sort and filter are the two controls that actually get used. Put them in reach; everything else can live behind a menu.
- Long tables need incremental loading, and the loaded-row count should be visible so the user knows the list is not the whole set.

### 22.12 Professional and creative apps

The tablet pro app (Procreate, Affinity Designer, Concepts, Logic) breaks several phone rules deliberately, and the reasons are worth knowing.

- **Chrome is allowed to be dense.** These apps are used for hours by users who invest in learning them. The 44 pt minimum still holds for anything hit under time pressure, but a tool palette can pack tighter because it is dwelt on, not tapped in passing.
- **The canvas is sacred.** Every panel is dismissible, and the gesture to dismiss everything (Procreate's four-finger tap) is a defining feature, not a shortcut.
- **Modifier gestures replace modifier keys.** Two-finger tap to undo, three-finger tap to redo, three-finger swipe for the cut/copy/paste menu. These are iPadOS-wide conventions and inventing your own is a real cost.
- **Pencil versus finger are different inputs.** Pencil draws, finger navigates, is the convention that works. Pressure, tilt and azimuth are available and hover is available on newer hardware, which allows a preview state that phones do not have.
- **Continuity matters more than novelty.** A pro app is judged on whether a file opened tomorrow is exactly as it was left. Autosave, versioning and crash recovery are design features in this category.
- **Keyboard and trackpad support is not optional** on iPad any more. A pro app without shortcuts is a toy to its target user.

The transferable lesson for phone apps: density is earned by frequency of use, and the right question is not "is this too dense" but "how often does this user do this".

### 22.13 Enterprise and field apps

A different user, a different context, and most consumer design instincts are wrong here.

Who they are: **around 60% of field workers use mobile apps as their primary work interface**, and mobile accounts for over 60% of enterprise application access in construction, logistics, healthcare, retail and field services.

What changes:

- **Offline is the default state, not the exception.** A field app that requires connectivity is a field app that fails at the site. Full core functionality offline, with a visible sync state and a real conflict story (§15.2b).
- **Role-based interfaces.** The technician, the dispatcher and the supervisor see different screens from the same data. Showing everyone everything is the most common enterprise UI failure, and it is why enterprise apps get a reputation for density.
- **Gloves, sunlight, noise and one hand.** Targets larger than the consumer minimum (§19.7), contrast above the accessible minimum rather than at it, and no interaction that requires two hands or fine precision.
- **Speed over delight.** The user does this task forty times a shift. Every tap removed is worth more than every animation added. This is the one category where a dense, information-heavy screen is the right answer.
- **Scan, don't type.** Barcode, QR, NFC and camera capture beat any keyboard input in the field, and the camera is already in their hand.
- **Battery is a shift-length constraint.** An app that drains a phone by 2 pm is a broken tool, which makes every background sync, GPS poll and animation an operational decision.
- **Training cost is a design metric.** Enterprise software is deployed to people who did not choose it, so learnability matters more than discoverability, and consistency matters more than novelty.

The emerging one worth tracking: AR overlays through the camera are moving from experimental to practical in field service, warehousing and manufacturing, where the value is unambiguous – put the instruction on the equipment rather than next to it.

---

## 23. AI features in native interfaces

New enough that conventions are still forming, but several are already firm.

### 23.1 Mark AI involvement, consistently

Apple's visual grammar for Apple Intelligence is a shimmer outline on the affected region, deference (the AI surface does not take over the app's own chrome), and source attribution when output derives from user content. The rule that matters: **never use Apple's shimmer for a non-Apple AI integration.** Build a distinct signal instead. Borrowing the system's AI treatment for your own model implies a provenance you do not have.

Design three states explicitly, because they are three different screens:

| State | What it shows |
|---|---|
| Pre-AI | The entry point, and what it will do |
| AI-active | Progress, streaming output, and a cancel |
| AI-resolved | Result, source attribution, and accept/reject/regenerate |

The resolved state is the one most implementations skip, and it is where trust is won or lost. Generated content that cannot be rejected in one tap reads as an imposition.

### 23.2 System integration beats a bolted-on assistant

On iOS, standard text controls get Writing Tools (proofread, rewrite in friendly/professional/concise, summarize, list-ify, table-ify) for free. Custom text controls need to adopt the API to get it. A custom text view that silently lacks Writing Tools while every other app has them is a visible regression to the user, and it is invisible to the team that built it.

The generalization: on native, the best AI feature is usually the one the OS already provides, surfaced correctly, rather than a chat window added to the corner of the app.

### 23.3 Generative and adaptive UI, with the caveat

The 2026 direction is interfaces assembled at runtime from a modular component system rather than fixed layouts, with the arrangement chosen per user and per intent. Intent-based navigation surfaces the tools needed for the current task instead of a fixed menu.

Two design consequences follow directly:

- The design deliverable shifts from screens to **components plus assembly rules**. Every component must be legible in any position, at any size, next to anything else. That is a much harder constraint than designing a fixed screen.
- **Predictability is now a feature you have to deliberately preserve.** An interface that rearranges itself destroys learned muscle memory, which is the thing that makes an app feel fast to a returning user. Adaptive placement is safest for content and most dangerous for controls.

The visual convention that has settled around AI surfaces in 2026: near-black bases (roughly #0A0A0A to #1A1A2E) with translucent frosted panels for model output. It is a convention, not a requirement, and following it makes an app look like every other AI app.

### 23.4 On-device inference changes the UX budget

Running locally removes the network round trip, so latency becomes device-dependent rather than connection-dependent. That flips the design problem: instead of "handle offline", it becomes "handle a three-year-old phone".

- Measure on the oldest supported device, not the newest. Model latency varies far more across hardware than network latency varies across connections.
- Below roughly 100 ms, no indicator. Up to about a second, an inline indicator. Beyond that, stream partial output, because streaming converts waiting into reading.
- Always offer a cancel. Local inference holds the CPU or NPU, and an uncancellable job on a phone is a thermal and battery problem the user can feel.
- Say when processing is local. "Processed on your device" is one of the few privacy claims users understand immediately, and it is a genuine differentiator.
- Production telemetry catches what lab testing does not: rage taps, UI freezes and unresponsive gestures around inference are the signals to watch.

### 23.5 The compliance floor

Store review for AI-enabled apps now expects privacy disclosure for any data processing, safeguards around hallucinated user-facing text, and clear labeling of AI-generated content. Treat labeling as a design requirement with a reserved place in the layout, not a badge added at the end.

### 23.6 Multimodal – voice with a screen, not instead of it

Voice-only is essentially gone as a design target. In 2026 nearly every voice product pairs speech with a screen: a phone, a display, a dashboard, a watch.

**The division of labor is the design principle.** Speech is linear and cannot deliver density; the screen exists to carry what speech cannot – maps, lists, comparisons, tables. The screen's job is **not** to transcribe what was just said.

The method that works: **design the voice flow as if there were no screen, and the visual layer as its support, at the same time.** The flow has to complete in both modes, because the user will switch mid-task – asking a question hands-free and then reaching for the screen to choose from the answer.

Practical rules:

- **Always offer a touch equivalent.** Voice fails in noise, in public, with accents the model handles poorly, and for users who cannot speak clearly. It is an additional mode, never the only one.
- **Show the interpretation, not just the result.** Displaying what the system heard is the cheapest possible error-recovery mechanism, and it lets the user correct one word instead of restarting.
- **Non-verbal feedback carries state**: a listening indicator, a haptic on capture, a short earcon on completion (§10.10). These are what make a voice interaction feel like it is working during the seconds when nothing is displayed.
- **Voice input on a phone is often just dictation into a field** (§21.16), and that is a legitimate and underused use. It does not need a conversational design.
- **Never lose the user's input to a failed transcription.** Keep the audio or the partial text and let them edit.

---

## 24. Growth, monetization and lifecycle moments

The screens that are not the product but decide whether the product gets used.

### 24.1 Onboarding

The goal is not to explain the app. It is to get the user to one meaningful action, once.

- **Value before signup.** Let the user see or do something before the account wall. Forced registration is the highest-drop-off screen in most apps.
- **Progressive disclosure over a tour.** Teach a feature at the moment it becomes relevant, not in a carousel at launch. Carousels are skipped and nothing is retained.
- **Learning by doing.** The onboarding step advances when the user performs the action, not when they tap Next.
- **Ask for the minimum.** One or two segmentation questions at most (§21.17), and collect the rest over time from behavior or in-app surveys.
- **Always offer skip.** A skip link costs a small amount of onboarding completion and saves a much larger amount of abandonment.
- **Defer permissions.** Never ask at launch (§16.11). Ask when the feature needs it, with the reason stated in terms of the feature.
- **Instrument every step.** Onboarding is where step-by-step funnel analytics earn their cost, because each screen is a discrete drop-off point.

The measurable target is time-to-first-value, not onboarding completion. An onboarding that is completed but produces no first action has failed.

### 24.2 Paywalls

Eight elements carry most of the measured variance: headline, price display, default plan selection, trial length, CTA copy, benefit order, timing, and visual hierarchy.

What holds up across sources:

- **Highlight one plan.** Badge it, position it, contrast it. An undifferentiated grid of plans is a decision the user will defer, and deferral is abandonment.
- **Benefit-led CTA.** "Start my plan" beats "Subscribe"; wording changes alone are reported in the 10–20% range.
- **Show annual savings explicitly** as a percentage or a per-month equivalent, next to the monthly option.
- **Progressive disclosure works.** Duolingo's shape (value screen, then feature comparison, then trial terms, then price) reduces overload versus leading with price.
- **Motion helps.** Animated paywalls are reported at roughly 2.9x static conversion. This is one of the few places where decorative animation has a defensible business case.
- **Message continuity.** Ad, onboarding and paywall should make the same promise in the same words.
- **Charm pricing still measures.** Prices ending in .99 are reported at 8–10% higher purchase rates.

Treat all of these numbers as directional vendor data, not as guarantees, and note the ethical line: highlighting a plan is persuasion, obscuring the cancel path or the real renewal price is a dark pattern and increasingly a regulatory problem.

Store rules constrain the design too: the price, billing period, and what happens after the trial must all be visible on the paywall itself, and a restore-purchases path must exist. These are review-rejection causes, not suggestions.

### 24.3 Review prompts

Both platforms cap and gate this, so the design question is only "when".

- Apple allows at most **three prompts per 365 days**, and Apple, not the app, decides whether the prompt actually appears. The same is true of Google's in-app review API.
- Ask after a **completed success**, never on launch and never mid-task. This is the peak-end rule applied directly: the rating reflects the moment of asking, not the average experience.
- A reasonable qualification gate: installed 7+ days ago, 2–3+ sessions, last session crash-free, not prompted in the last 90 days.
- Never gate the prompt behind a "do you like the app?" pre-question that routes unhappy users to a private form. Both stores prohibit it.

### 24.4 Error recovery

Generic failure text is the most common mobile UX failure there is. The pattern that works:

1. **Name what happened** in the user's terms. "You are offline" rather than "Error 503".
2. **Say what still works.** "You can keep writing, we will save when you are back."
3. **Offer the action.** Retry, or a path to the thing that does work.
4. **Retry automatically first,** with backoff, before showing anything at all. Most transient failures resolve in under a second.

Apple's guidance and general practice both favor **non-modal** connection status indicators over modal dialogs, because a modal blocks the recovery the app is already attempting. A modal error is appropriate only when the user must decide something.

Backoff discipline matters on mobile specifically: exponential backoff with jitter, a retry cap, and centralized limiting rather than a retry at every layer. Uncoordinated retries across layers turn a recovering backend into a failing one, and on a phone they also drain battery.

Offline-first is the structural answer (§15): cache aggressively, queue writes, and show three distinct states – offline, syncing, synced. A user who can see which state they are in stops worrying about their data.

### 24.5 Undo versus confirm

The decision rule:

| Action | Pattern |
|---|---|
| Frequent, low stakes, reversible (archive a mail, delete a note) | Do it, show undo |
| Rare, high stakes, irreversible (delete account, wipe data) | Confirm first, typed or explicit |
| Frequent and irreversible | Redesign it so it is reversible |

Undo lives in a snackbar or toast on Android and typically a toast or shake-to-undo on iOS. The critical constraint: **a snackbar is not a sufficient safety net on its own.** It times out (roughly 4–10 s), it can be missed, and it can be dismissed by a scroll. Anything undoable via snackbar should also be recoverable through a trash, an archive or a history for a real period of time.

For genuine confirmation: red for the destructive option, name the action in the button ("Delete 3 photos", not "OK"), and never place the destructive button where the safe one usually sits.

### 24.6 Design systems across native platforms

Token adoption is now the default rather than the exception, and the W3C-track token format backed by Adobe, Google, Meta and Figma means one token file can drive Figma, CSS, iOS and Android without a hand-built translation layer.

The structure that survives contact with multiple platforms:

| Tier | Example | Platform-specific? |
|---|---|---|
| Primitive | `blue-600: #1B62E8` | No |
| Semantic | `color.action.primary` → `blue-600` | No |
| Component | `button.primary.background` | Rarely |
| Platform override | elevation on Android, no shadow on iOS | Yes, deliberately |

The override tier is the part teams get wrong. A cross-platform design system should encode **where platforms are allowed to diverge**, not pretend they do not. Navigation shape, motion curves, elevation treatment, control appearance and typography scale are the usual legitimate divergences; color, spacing, iconography and copy are usually not.

The honest observation from every source on this: naming, structure, governance and adoption are the hard parts, not the tooling. A token system nobody adopts is worse than no system, because it splits the truth in two.

### 24.7 Cancellation, and the regulatory floor

The standard is one sentence: **cancelling must be at least as easy as subscribing.** If signup is three taps, cancellation is three taps.

The legal state as of 2026 is messy and worth knowing accurately rather than approximately:

- The FTC finalized its click-to-cancel amendment to the Negative Option Rule in **October 2024**; a federal appeals court **vacated it in 2025** before enforcement, on procedural grounds (a skipped economic-impact analysis). The FTC opened a new rulemaking in **March 2026** to revive it.
- **Roughly 30 states have their own automatic-renewal laws**, several stricter than the vacated federal rule, and **New York City's own click-to-cancel rule takes effect 1 October 2026.**
- Civil penalties run to tens of thousands of dollars per violation.

So the federal rule is in limbo and the requirement is effectively in force anyway through state law, city law and store policy. Designing to the stricter standard is both simpler and safer.

`What that looks like in the interface:`

- **Cancellation lives where the subscription lives**, in the app's own account settings, not only in a help article that points at the App Store.
- **No retention maze.** One offer, declinable in one tap, is the outside limit. A sequence of "are you sure" screens, a survey, and a chat handoff is the pattern regulators name explicitly.
- **State the terms plainly at the point of purchase** (§24.2): price, billing period, renewal date, and what happens after a trial.
- **Send a renewal reminder** before charging for a long period. It is required in several jurisdictions and it prevents the chargeback and the one-star review regardless.
- **Confirm cancellation in writing**, with the date access ends.

### 24.8 Gamification, and where it turns

Streaks work because they weaponize loss aversion. That is also the reason they cause harm: research on streak mechanics reports guilt, anxiety and compulsive checking as measured outcomes, not as speculation.

The line between engagement and manipulation is not fuzzy in practice:

| Legitimate | Manipulative |
|---|---|
| Progress toward a goal the user set | Progress toward a goal the app set for its own metrics |
| Streak freezes and planned breaks | Losing months of progress for one missed day |
| Rewards for real accomplishment | Rewards for time spent |
| Notifications the user can tune | Escalating notifications engineered around loss aversion |
| Visible, honest counters | Counters that reset in ways the user cannot predict |

Duolingo is the usual cited example of the defensible version, and the specific mechanism is the **streak freeze**: it acknowledges that life interrupts, which converts the streak from a threat into a record.

`The business case for restraint, since ethics arguments rarely move a roadmap:` gamification done well lifts 30-day retention; done badly it draws FTC attention, refunds, and a trust problem that outlasts the retention bump. And the user who quit because a streak made them anxious does not come back.

### 24.9 Dark patterns are now law, not opinion

The design-ethics conversation moved into statute, and the framing that matters is that **regulators now reason about specific interface mechanics**, not about intent.

- **EU Digital Services Act, Article 25** prohibits three categories of autonomy violation: **deception, manipulation, and distortion or impairment** of a user's ability to make free choices. It has applied to very large platforms since 2023 and more broadly since **17 February 2024**, with enforcement continuing through 2026.
- Recent academic work maps **59 known dark patterns onto those three categories** and identifies eight design factors that determine when a pattern actually violates autonomy – which gives regulators a systematic tool rather than a judgment call.
- The European Commission has announced a **Digital Fairness Act for Q4 2026**, intended to cover dark patterns, **addictive design**, misleading influencer marketing and unfair personalization, and to put a single agreed definition of dark patterns into EU law.
- On the US side, the FTC's activity on negative-option and cancellation flows (§24.7) is the same idea arriving through a different door.

`What a designer should take from this:`

- **"Addictive design" is being named as a regulated category**, which reaches directly into streak mechanics, infinite scroll and notification pressure (§22.5, §24.8).
- **The unit of enforcement is the pattern, not the outcome.** A confirmshaming button, a pre-checked consent box, an obscured decline, a countdown that resets – each is identifiable on a screenshot, which is why compliance here is a design review rather than a legal one.
- **Build the review into critique** (§26.2). Add one question to every flow: is there any point where the easy path and the user's interest diverge, and is the divergence hidden?

### 24.10 Documenting and versioning the system

A design system that nobody can find, read or safely upgrade is a shared folder.

- **Semantic versioning** (major.minor.patch) is the convention: major means a breaking change, minor and patch mean additions and fixes. It gives a team a way to say "we are on 3.x" and have it mean something.
- **The failure is not the version number, it is the migration.** A major release with no migration path is a delayed incident. Every breaking change ships with a note saying **what changed, who has to act, what can wait**, and before-and-after examples for any rename or pattern shift.
- **Write for both audiences.** Documentation that speaks only to designers or only to engineers loses adoption immediately, and on mobile that is three audiences: designers, iOS, and Android.
- **Document the decision, not just the component.** "Use a snackbar when the action is undoable, an alert when a decision is required" is the part that gets reused; the prop list is the part the IDE already provides.
- **Communication is a system function.** The teams that succeed are proactive about announcing changes and collecting feedback, and treat adoption as a metric rather than an assumption.

`The mobile-specific piece:` the system has to state **where platforms are allowed to diverge** (§24.6) and document both variants side by side. A component page showing only the iOS rendering is how an Android implementation quietly drifts.

---

## 25. Regulated audiences and contexts

### 25.1 Designing for children

The legal frame moved recently and it changes design, not just privacy policy.

- **COPPA** requires verifiable parental consent before collecting, using or disclosing personal information from children under 13.
- The **2025 rule update widened what counts as "directed to children"**: user reviews mentioning children, the age profile of similar apps, and marketing materials are now factors. An app can be classified as child-directed because a competitor's similar app has children using it.
- The **FTC policy statement of 25 February 2026** encourages age-verification technology, and permits running an age check *before* parental consent provided the data is used only for age determination, deleted promptly, disclosed clearly to parents and children, secured, and reasonably accurate.
- A new compliance deadline of **22 April 2026** applies to the updated rules.

The design consequences:

- **The age gate is a real screen with real requirements.** It must be neutral (a date-of-birth entry, not "are you over 13?" with an obvious right answer), it must not be repeatable until the user gets the answer they want, and its result must persist.
- **Mixed-audience apps need a branch, not a compromise.** Under-13 users get the restricted experience: no behavioral advertising, no third-party analytics, no social features that expose personal information.
- **Consent flows are for parents, so write them for adults**, in plain language, on a screen a child is unlikely to complete by accident.

Interface guidance for children's apps themselves, which is different from adult UI in specific ways:

- **Limit choices to roughly 5–7 per screen.** Children are overwhelmed by dense menus faster than adults, and the number matches the general IA guidance in §21.19 for a different reason.
- **Icons plus words**, with familiar imagery. Reading ability varies enormously across the age band an app targets.
- **Larger targets than the adult minimum.** Fine motor control is still developing; 60 pt is a reasonable floor for a primary control.
- **Keep layout consistent across screens.** Children learn positions rather than labels, so moving a control between screens costs more here than with adults.
- **No dark patterns, at all.** Timed offers, streak pressure, and confusing dismiss buttons are unacceptable in a children's product and are increasingly enforcement targets rather than ethics debates.
- **Parental gates on anything outbound** – purchases, external links, sharing. The standard implementation is a task a young child cannot complete, not a password.

---

## 26. Diagnosing a native UI

The part of the study that turns into method. This is how to look at an app and find what is wrong with it.

### 26.1 Heuristic evaluation, adapted for mobile

The classic inspection method: experts compare the interface against a list of principles and record where it violates them. Cheap, fast, and it finds a large share of problems before a single user session. **Three to five evaluators is the established number** – one person misses too much, more than five finds almost nothing new.

The important caveat from the research: **desktop-centric heuristic checklists do not adequately detect mobile-specific problems.** Nielsen's ten heuristics still apply, but they have to be supplemented with the mobile-specific set, which is roughly:

| Mobile heuristic | What a violation looks like |
|---|---|
| Reachability | Primary actions in the top corners on a phone |
| Target size | Anything under 44 pt / 48 dp, or targets closer than 8 dp |
| Interruptibility | An animation or flow that cannot be caught, canceled or resumed |
| Platform conventions | Android back not handled, iOS swipe-back disabled, wrong control for the platform |
| Input economy | Wrong keyboard type, no autofill, no autocapitalization rules |
| Connectivity | No offline state, no retry, modal error over a recovering request |
| Permission etiquette | Any request at launch, or without stated reason |
| Glanceability | Key information not readable in a two-second look |
| Text scaling | Layout breaks at the largest Dynamic Type size |
| Orientation and resize | State lost, layout broken, or rotation blocked without cause |
| Battery and heat | Continuous animation, polling, or inference with no cancel |
| Notification restraint | High frequency, low relevance, sensitive content in previews |

### 26.2 A structured critique

Critique frameworks that hold up, and when to use each:

- **"I like / I wish / What if"** – for early concepts and mixed-seniority rooms. It separates observation from prescription and gets non-designers participating.
- **The 3Cs: context, content, composition** – for a single screen. Ask what the user arrives with, what the screen must say, and only then how it is arranged. Most bad critique starts at composition and never reaches context.
- **Nielsen's ten heuristics** – for an audit of an existing product where the goal is a defensible list rather than a conversation.

Feedback quality has a testable standard: **specific, justified, actionable.** "The hierarchy is off" fails all three. "The price and the product name are the same weight, so the eye has no entry point; try dropping the name to the body style" passes all three.

**Match the critique depth to the design's maturity.** A rough concept gets critiqued on assumptions, flow and product risk. A high-fidelity mockup gets critiqued on clarity, states, accessibility and implementation detail. Critiquing corner radii on a concept wastes the session; critiquing the premise on a build-ready mockup wastes the work.

### 26.3 Hierarchy on a small screen

The mechanics compress on a phone, and the compression is what makes hierarchy harder rather than easier.

- **One focal point per screen section.** Two only if they are far enough apart not to compete. A screen with four things shouting has no hierarchy at all, only noise.
- **Assign every element a tier** – primary, secondary, tertiary – before styling anything. If two elements land in the same tier and sit next to each other, one of them is wrong.
- **Size and spacing carry more of the load than on desktop**, because there is no room for the layout-level moves (asymmetric columns, generous whitespace) that do the work on a large screen.
- **Reserve one accent color for the primary action.** A screen with three accent-colored controls has no primary action.
- **The bottom third is the strongest position**, not the top, because it is where the thumb is. This inverts the desktop instinct to put the important thing at the top.
- **Contrast is the cheapest hierarchy tool available on a small screen** – weight, size, color and containment, in that order of reliability.

### 26.4 Cognitive load and choice

**Hick's law:** decision time grows logarithmically with the number of choices. It is the formal justification for most of the advice in this study, and it has two honest limits: it applies to unfamiliar, unordered choices, and grouping defeats it. Twenty options in four labeled groups is not the same decision as twenty options in a list.

Applied:

- **Progressive disclosure** is the primary tool. Reveal options as they become relevant rather than all at once.
- **Visual hierarchy is a partial substitute for removing choices.** If a primary action is clearly primary, the others cost less attention even though they are still present.
- **Consistency lowers load for free.** Same control in the same place doing the same thing across screens means the user decides once, not every time.
- **Defaults do more than any amount of explanation.** A well-chosen default converts a decision into a confirmation.

The counterweight worth stating: reducing choices past the point of capability is not a win. The goal is fewer *decisions*, not fewer *features*.

### 26.5 Microinteractions and where delight actually lives

A microinteraction is one trigger, one rule, one feedback, one loop or mode. On native the ones that matter are unglamorous: the press state, the pull-to-refresh spring, the checkmark, the toggle, the keyboard haptic.

What the good examples have in common:

- **They are multimodal and simultaneous.** Apple Pay's success is a haptic, a visual and a sound landing together (§10.10). Any one of the three alone is noticeably weaker.
- **They are attached to a real event**, not sprinkled on decoration. A celebration for completing a real task is delightful; a celebration for opening a screen is noise.
- **They are fast.** 100–200 ms. A delightful animation the user waits for stops being delightful on the fifth repetition.
- **They scale with frequency.** The interaction a user performs fifty times a day gets the subtlest treatment; the one they perform once a month can afford a moment.

The 2026 direction worth noting and treating carefully: microinteractions that adapt to the user, becoming quicker for power users and more pronounced for new ones. It is a real idea, and it collides with the predictability argument in §23.3. Adapting speed is defensible; adapting whether feedback exists at all is not.

### 26.6 Finding out what is actually wrong

Heuristics and critique (§26.1, §26.2) find problems from the inside. These find them from the outside.

**Behavioral, and cheap:**

- **Session replay and touch heatmaps.** Mobile-specific tools (UXCam, FullSession and similar) record sessions and aggregate taps per screen. What they reveal that analytics cannot: **rage taps** (repeated taps on something unresponsive or non-interactive), **dead taps** on things that look tappable, scroll depth on long screens, and where a flow is abandoned mid-screen rather than between screens.
- **Funnel analytics per onboarding step** (§24.1), because each screen is a discrete drop-off point.
- **Crash-free session rate and ANR rate**, which are design metrics as much as engineering ones: an app that dies is an app whose design is never experienced.

**Attitudinal, and slower:**

- **Diary studies** are the method mobile most needs and gets least. Participants self-document over **one to four weeks**, in context, when the thought is fresh. This is the only practical way to see episodic behavior, how use changes with familiarity, and where the product fits in a day. Five phases: plan, recruit and onboard, monitor, debrief, analyze.
- **Remote moderated testing** by having the participant mirror or share their phone screen to a second device. Awkward, and still the fastest way to watch someone use the thing.
- **Unmoderated remote testing** for specific tasks at volume.
- **Tap testing / first-click testing** on a static screen, which answers "is the primary action findable" in minutes.

`The pairing that works:` behavioral data tells you where the problem is, qualitative work tells you why. A heatmap showing rage taps on a non-interactive element is the finding; a five-minute session watching someone do it explains what they expected instead.

The trap worth naming: session replay and heatmaps carry real privacy obligations on mobile. Mask text input, exclude the sensitive screens (§21.22), and declare it in the privacy label (§16.12). A design research tool that leaks user data is not a trade worth making.

### 26.7 The tooling loop

**Previews are the fastest design surface available on native.** SwiftUI's `#Preview` macro renders a view live in Xcode, with dynamic refresh on code, asset and localization changes; Compose's `@Preview` does the same in Android Studio. Apple's own developer survey figure is up to **50% faster interface iteration.**

The part most teams miss: **previews are a matrix, not a single render.** Declare previews for light and dark, the largest Dynamic Type size, an RTL locale, a long string and two device widths, and every one of those configurations is checked continuously rather than at review time. That is the same matrix as the snapshot test (§21.21), and it should be the same list.

**Design-to-code tools** in 2026 are real but partial. Figma Make generates web-shaped prototypes; tools like Locofy target React, React Native and Flutter; a few generate native iOS directly. The consistent caveat across every source is the same: **output needs human review for accessibility, semantics and production readiness.** They compress the first draft, not the last mile.

**Device farms** (BrowserStack, AWS Device Farm, Firebase Test Lab and similar) give access to thousands of real devices and, importantly, to the things emulators cannot do: **biometrics, real GPU behavior, real thermal throttling, and manufacturer skins.** They are how the device matrix (§21.11) becomes a CI job instead of a wish.

`The loop that actually works:` prototype in previews, snapshot-test the matrix in CI, run the real-device pass on the shortlist before release, and use session replay (§26.6) to find what all three missed.

### 26.8 First impressions, and consistency at scale

**The window is measured in milliseconds.** Research puts aesthetic judgment formation at **17–50 ms**, and finds that those judgments are **sticky** – longer examination rarely overturns the first impression. Aesthetic treatment measurably changes judged **credibility** of identical content, and roughly **94% of first impressions are attributed to design rather than content or function.**

For a native app this lands on three surfaces in order: the **icon** in search results (§20.1), the **screenshots** on the store page (§20.2), and the **first screen after launch** (§20.6). Each is judged before anything the product actually does is experienced.

`The uncomfortable implication, stated honestly:` visual appeal beats usability for first impressions. That is not permission to build something pretty and broken – usability determines whether they stay – but it does mean an app with excellent interaction design and amateur visual craft loses users who never find out.

**Consistency at scale** is what keeps the impression from degrading across a hundred screens.

- **Tokens and components are the mechanism** (§13.8, §24.6): change one value and it updates everywhere, which is the only way consistency survives a growing team.
- **Decisions, not just components, need to be shared** (§24.10). Two designers with the same button library will still produce different screens if they disagree about when to use a sheet.
- **The scaling failures are predictable:** spacing drift (because spacing is the token teams skip), one-off components created under deadline, screens that never got a dark-mode pass, and the fifth engineer's interpretation of a pattern documented only in a Figma file.
- **Visual regression across the whole component set** (§21.21) is what catches drift mechanically, and it is the only method that scales past what a person can review.

`The check that works at any size:` open ten screens in sequence and look only at spacing, type sizes and corner radii. If they vary, the system is not being used, regardless of what the documentation says.

---

## MYTH register

| Belief | Reality | Evidence |
|---|---|---|
| Dark mode saves meaningful battery | Only on OLED, and only at high brightness. ~42% total phone power at 100% brightness, a few percent at the 30–50% people actually use; nothing at all on LCD | Purdue per-frame OLED power profiler (§17.8) |
| Shipping one design to both platforms is efficient | It produces something that feels subtly wrong on both. Share logic and identity, split navigation and gestures | §2.2 |
| Liquid Glass is something you implement | It arrives automatically on recompile against the iOS 26 SDK. Hand-rolling it produces something that will be wrong next release | §2.1 adoption mechanics, §3.3 |
| A splash screen is a branding opportunity on iOS | Apple rejects launch screens with text, logos, taglines or animation. Android is the opposite and gives you a sanctioned brand moment | §20.6 |
| SwiftUI `List` is slower than `LazyVStack` | `List` is faster for long lists; `LazyVStack` inside a `ScrollView` does not release views the same way | §17.4 |
| Skeleton screens always beat spinners | A 2017 Viget study (n=136) found skeletons worst on every metric; other testing found the opposite. The duration decides, not the pattern | §17.10 |
| Infinite scroll is a neutral engagement pattern | Research identifies it as a primary driver of mindless scrolling, with measured anxiety, fatigue and guilt across age groups. Now inside the EU's regulated "addictive design" category | §22.5, §24.9 |
| Users can only perceive latency down to ~100 ms | Dragging latency is detectable at ~6 ms and degrades performance above ~25 ms. Tapping is imperceptible below ~24 ms | §17.12 |
| Automated accessibility scanners cover accessibility | 30–40% ceiling. They cannot judge whether a label is contextually right, whether reading order makes sense, or whether a flow completes | §11.7 |
| Streaks are harmless engagement | They weaponize loss aversion, and the measured outcomes include guilt, anxiety and compulsive checking. Streak freezes are what make the pattern defensible | §24.8 |
| Cancellation belongs in the store, not the app | The standard is "as easy as subscribing", enforced by ~30 US state laws, NYC from 1 Oct 2026, and store policy regardless of the vacated FTC rule | §24.7 |
| A tablet layout is a phone layout with more room | Stretched phone layouts are the most common tablet failure. Extra width buys context, more content, or fewer steps – "bigger" is not one of the three | §8.4b |
| Gestures make an interface feel modern | Gestures are always hidden. Primary actions get visible controls; gestures are shortcuts, never requirements | §9.4 |
| P3 makes colors look better | It makes them reproduce more accurately. The common failure is an inconsistent pipeline, not a dull gamut, and contrast requirements do not relax | §13.9 |
| Immediate-mode GUI is simply simpler | Single-pass layout cannot resolve content-dependent sizing in one frame, so dynamic layout costs a frame of lag or a workaround | §6.10 |
| Slint is free for any product | Royalty-free covers desktop, mobile and web with attribution. **Embedded is excluded** – that is GPLv3 or a paid license | §5.26 |
| React Native cannot feel native | That was the bridge. JSI, Fabric and TurboModules removed it, and gesture-driven interruptible animation is now achievable | §6.16 |
| Custom haptics show craft | Android's own guidance is to use `HapticFeedbackConstants` wherever one matches, for consistency and accessibility. Fifteen bespoke patterns is noise, not a language | §10.9 |
| More onboarding steps means better activation | Time-to-first-value is the metric. An onboarding that completes but produces no first action has failed | §24.1 |
| An undo snackbar is an adequate safety net | It times out, it can be missed, and a scroll dismisses it. Anything undoable that way also needs a trash, archive or history | §24.5 |
| Locking to portrait is a safe default | It fails mounted devices, keyboard cases and users for whom rotating is easier than reaching. Android now discourages locking on large screens outright | §8.7 |
| Wide targets fix mis-taps | Spacing is a separate requirement. A 48 dp target with no gap around it still gets mis-hit | §9.1 |
| Design for the average device | Design for the slowest device in the matrix. Animation degrades first, and retrofitting a heavy design onto weak hardware removes the parts that were the point | §17.11 |
| App size is an engineering concern | Every 6 MB of download costs roughly 1% of install rate, and about 2.5% per 10 MB in low-storage markets | §20.8 |
| Voice interfaces replace touch | Voice-only is effectively gone. Speech cannot deliver density; the screen carries lists, maps and comparisons, and both paths must complete alone | §23.6 |

## CONFLICT register

| Question | Source A | Source B | Status |
|---|---|---|---|
| Skeletons or spinners? | Viget (2017, n=136): skeletons performed worst on every measured metric | Other user testing: skeletons scored higher on perceived speed and ease of navigation | Unresolved on the pattern; resolved on duration. Under 300 ms show nothing, 300 ms–1 s a spinner, 1 s+ a skeleton that matches the real layout (§17.10) |
| Is Liquid Glass good design? | Apple: hierarchy through depth, refraction and visual weight | Widespread criticism of text legibility over refracting backdrops, plus documented demand for Reduce Transparency | Live. Adopt the system components, and measure contrast against the real backdrop rather than a flat approximation (§1.1, §11.11) |
| Share UI across platforms or split it? | Compose Multiplatform shares 80–90% of UI code; Flutter shares more | An iOS user reading a Material bottom bar reads it as an Android app | Depends on whether the product's identity is its own brand or the platform's (§6.8) |
| Does dark mode save battery? | 42% reduction in total phone power at 100% brightness on OLED | A few percent at realistic brightness, zero on LCD | Both true. Ship dark mode for preference, not for battery (§17.8) |
| Adaptive/generative UI or predictable UI? | AI assembles the interface per user and per intent, surfacing only what the task needs | Muscle memory is what makes an app feel fast to a returning user, and rearranging controls destroys it | Split by element type: adapt content freely, adapt controls almost never (§23.3) |
| Is Android's push opt-in really 91–97%? | Airship benchmark reports 97% | The figure is inflated by legacy installs from Android 12 and earlier, where permission was automatic | Treat Android 13+ as comparable to iOS in kind, better in degree (§21.35) |
| Does the tab bar own its strip? | Historic iOS: a fixed reserved bar at the bottom | iOS 26: the tab bar floats and minimizes on scroll, so content occupies that space | Resolved by the platform. Anything pinned above it becomes a tab bar accessory view (§2.1) |
| Gamification: engagement or manipulation? | Streaks measurably lift 30-day retention | Streak mechanics produce documented guilt, anxiety and compulsive checking, and the EU is naming "addictive design" as a regulated category | Line is drawable: whose goal, and can the user survive a missed day (§24.8, §24.9) |
| Is Rust GUI ready for product work? | Slint ships in production on desktop and embedded; iced, GPUI and Dioxus all have real applications | Slint's mobile support is initial, GPUI is pre-1.0, Xilem is alpha, and std-widgets are not deeply themeable | Ready for desktop and embedded, speculative for phones (§5.26, §6.4) |
| Does a design system need platform variants? | Consistency across platforms is the point of a system | Navigation, motion, elevation, controls and type scale legitimately diverge | Encode where divergence is allowed rather than pretending it is not (§24.6) |

## Trade-off register

| Fork | Buys | Costs |
|---|---|---|
| Native per platform vs one shared UI | Correct idioms, full API access, best feel | Two teams, two codebases, drift between them |
| Own-canvas toolkit (Flutter, Slint, Compose MP) vs native widgets | Pixel-identical brand across platforms | Accessibility and text rendering are the toolkit's, not the OS's; platform conventions must be rebuilt |
| System WebView (Tauri, Capacitor) vs compiled UI | One frontend, smallest team | Gesture handling and transition interruptibility cannot fully match native (§6.17) |
| Immediate mode (egui) vs retained/reactive | No state synchronization bugs, ideal for tools | Single-pass layout costs a frame on content-dependent sizing (§6.10) |
| Liquid Glass / translucency vs opaque chrome | Depth, current platform identity, content visible under chrome | Contrast risk, fill-rate cost, and it disappears under Reduce Transparency |
| Custom font vs the system font | Brand voice | Loses SF Pro's optical size switching and Roboto Flex's axes, adds app size, adds a licensing question |
| Dark mode as the only mode | Coherent identity, matches category convention | Fails outdoor use, and forces users who prefer light into a preference the OS already knows |
| Infinite scroll vs load-more | Session length, discovery | Lost footer, lost scroll position, and a pattern regulators are now looking at |
| Adaptive AI-assembled UI vs fixed layout | Relevance, fewer irrelevant controls | Destroys learned position; every component must work anywhere |
| On-device inference vs server | Privacy, no network dependency, no round trip | Latency becomes device-dependent, thermal and battery cost is visible, model ships in the binary |
| Streaks and gamification vs neutral progress | Measurable retention | Guilt and anxiety in users, and regulatory exposure under "addictive design" |
| Undo (do it, offer reversal) vs confirm (ask first) | Speed, fewer interruptions | Missed snackbars lose data unless a trash or history also exists |
| Snapshot testing the whole matrix vs spot checks | Catches theme, locale and text-size regressions mechanically | Baseline churn, false positives, review load |
| Widgets, Live Activities and App Intents vs app-only | Retention and distribution outside the app | Each is a separate design with its own constraints and its own refresh budget |
| Alternative EU distribution vs App Store only | Lower fees, direct relationship | You own trust signals, updates and the install flow (§20.9) |
| Slint royalty-free vs commercial license | Free for desktop, mobile and web | Attribution required, and embedded is excluded entirely |
| Wide gamut (P3) vs sRGB | More accurate saturated color on capable displays | A pipeline that must be right end to end, or the brand color drifts between surfaces |
| Voice as a first-class input vs touch only | Hands-free, faster for some tasks, accessibility gain | Two flows to design and keep in sync, and both must complete alone |

## Verification register

| Claim | How to check |
|---|---|
| The layout survives real conditions | **Smallest supported screen × largest Dynamic Type / font scale.** The single highest-yield check in the whole native study (§11.7) |
| ...and survives translation | Repeat the above in German or Finnish, and again in Arabic for RTL (§12.5) |
| Contrast holds | Xcode Accessibility Inspector Run Audit and Android Accessibility Scanner, then manually for meaningful icons, control borders and every interactive state (§11.11) |
| Glass and translucent chrome are legible | Measure against the actual backdrop content, not a flat approximation, and check with Reduce Transparency on |
| The app is usable without sight | Every core journey end to end with VoiceOver and with TalkBack, on real devices |
| Motion respects the user | Toggle Reduce Motion (and Prefer Cross-Fade Transitions) and confirm information-bearing motion survives while decorative motion goes (§10.6) |
| Touch targets pass | 44×44 pt iOS, 48×48 dp Android, **with spacing checked separately** (§9.1) |
| Drag interactions feel right | Profile during the gesture, not at rest. Dragging latency is detectable at ~6 ms; a dropped frame under a finger is felt (§17.12) |
| The frame budget holds | Debug GPU Overdraw: no red anywhere, at most 2x over most of the screen (§17.9) |
| Startup is acceptable | Cold start under 2 s on a mid-range device; iOS first frame under 400 ms; Android Vitals flags 5 s (§17.5b) |
| State survives the lifecycle | Rotate, resize the window, background for 30 minutes, force-kill and relaunch. Scroll position, selection and in-progress text must all return (§8.4, §15.1) |
| Deep links work | Test cold start, warm start and backgrounded separately; they take different code paths and only cold start is usually broken (§20.7) |
| Offline is real | Airplane mode through a full core journey: offline, pending, syncing, synced and failed must all be visible and recoverable (§15.2b) |
| Permissions are asked correctly | No request at launch; every request preceded by an in-context reason naming the feature (§16.11) |
| Notifications are defensible | Read each one as though it arrived at 11 pm on a Sunday, and check nothing sensitive appears in a lock-screen preview (§16.15) |
| The icon works | Render at ~40 px, in light, dark and tinted, and with labels hidden (§20.1, §14.7) |
| Sensitive screens are protected | Background the app and inspect the app-switcher snapshot (§21.22) |
| The design system is actually used | Open ten screens in sequence and compare only spacing, type sizes and corner radii (§26.8) |
| Nothing coerces | Walk every flow asking where the easy path and the user's interest diverge, and whether the divergence is hidden (§24.9) |
| It works on the worst device | Run the core journey on the oldest supported hardware, on a device farm if not in hand (§17.11, §26.7) |

## Does not transfer from web

| Web habit | Why it is wrong on native |
|---|---|
| Hover states | There is no hover on touch. On iPad with a pointer, hover is a separate additional mode that must not become the only affordance (§8.6) |
| Modifier order does not matter | In Compose it *is* the layout model: `padding` before `clickable` shrinks the touch target, after it does not (§4.1) |
| Layout: parent constrains, child fills | SwiftUI inverts it – the parent proposes, the child chooses, the parent places. Fighting this is the source of most SwiftUI layout confusion (§3.2) |
| Zoom scales everything | Dynamic Type scales only text, so a fixed-height row clips instead of reflowing (§3.6) |
| A back button in the UI | iOS needs one and Android must not have one; Android's system back must be handled instead (§2.2) |
| Left-to-right edge swipe means "back" | It means "switch tab" on Android. The same gesture, two meanings (§9.4) |
| Any element can own the screen edge | The system owns the edges on both platforms. Edge-adjacent gestures are contested territory (§9.5) |
| Centered dialog boxes | On a phone the equivalents are the bottom sheet and the full-screen modal; a centered card wastes the thumb zone (§21.32) |
| Fire-and-forget transitions | Native transitions must be catchable, reversible and velocity-aware mid-flight (§10.1, §10.5) |
| `<link rel="preload">`, Core Web Vitals, LCP/INP/CLS | No equivalent. The native metrics are cold start, frame time, jank rate and battery (§17) |
| Toast for anything important | Native toasts and snackbars time out and can be dismissed by a scroll; anything actionable belongs on a persistent surface (§21.31) |
| The browser handles text selection, magnifier and callouts | Only if you use standard text components. A custom renderer loses all of it silently (§21.29) |
| A single scroll physics | iOS tracks 1:1 with graceful bounce absorption; Android stops abruptly. Getting it wrong is the clearest "feels off" tell (§21.18) |
| Notifications are yours to send | The OS ranks, batches and filters them. Generic content is demoted into a summary and may never be seen (§16.16) |
| Permissions are free to request | Each denial is effectively permanent, and asking at launch is how you lose the channel forever (§16.11) |
| Ship it and it is live | The store is a review gate, a metadata contract and a rejection surface. Placeholder states are a documented rejection cause (§20.3) |
| Assets can be any size | On MCU targets a CJK font is 10–30 MB of flash, and every glyph is a bill-of-materials item (§19.6) |
| Blur and backdrop effects are cheap enough | On embedded and line-by-line renderers they are impractical or impossible, and on any GPU they are an area-proportional fill-rate cost (§17.9, §5.20) |

## Sources

| # | Source | Type | Used for |
|---|---|---|---|
| 1 | Apple Human Interface Guidelines | Primary spec | Liquid Glass, layout, SF Symbols, Dynamic Type, modality, menus, watchOS, tvOS, visionOS |
| 2 | Apple Developer documentation and WWDC sessions | Primary spec | SwiftUI and UIKit APIs, App Intents, WidgetKit, Core Haptics, StoreKit, accessibility |
| 3 | Material Design 3 and M3 Expressive | Primary spec | Color system, type scale, motion tokens, components, shapes, adaptive layout |
| 4 | Android Developers documentation | Primary spec | Compose, Glance, Navigation 3, predictive back, haptics, large screens, wide gamut, app size, startup |
| 5 | Android Open Source Project – haptics and driver distraction | Primary spec | Haptic constants and UX foundation, automotive guidelines |
| 6 | Slint documentation, blog and GitHub | Primary spec | Language, reactivity, layouts, platform layer, software renderer, widgets, licensing, testing |
| 7 | iced, egui, GPUI, Dioxus, Xilem/Masonry, Tauri docs and repos | Primary spec | Rust GUI landscape, architectures, trade-offs |
| 8 | winit, cosmic-text, swash, Vello, Parley, AccessKit | Primary spec | DPI handling, text shaping and rendering, Rust accessibility |
| 9 | W3C WCAG 2.2, WCAG2Mobile draft, EN 301 549 | Standard | Native accessibility criteria, non-text contrast, target size |
| 10 | European Accessibility Act, EU Digital Services Act, announced Digital Fairness Act | Regulation | Compliance deadlines, dark patterns, addictive design |
| 11 | FTC (COPPA rules and policy statement, Negative Option Rule) and US state auto-renewal laws | Regulation | Children's design, age assurance, cancellation flows |
| 12 | EU Digital Markets Act developer portal and Apple EU business terms | Regulation | Alternative distribution and its design consequences |
| 13 | Nielsen Norman Group | Research | Bottom sheets, progressive disclosure, heuristics, critique |
| 14 | Purdue OLED per-frame power profiler | Research | Dark mode battery figures |
| 15 | Hoober (2013) grip study, MIT Touch Lab, FFitts | Research | One-handed use, thumb zones, target sizing |
| 16 | Touchscreen latency perception literature | Research | 6 ms drag, 24 ms tap, 25 ms performance thresholds |
| 17 | Viget skeleton-screen study and counter-studies | Research | The skeleton versus spinner conflict |
| 18 | Systematic reviews of mobile design for older adults (JMIR, Springer) | Research | Age-friendly design guidelines |
| 19 | ACM CHI / DIS work on dark patterns and the DSA | Research | Mapping 59 dark patterns to autonomy violations |
| 20 | Localization and RTL practice guides (Crowdin, SimpleLocalize, POEditor) | Practitioner | Text expansion, mirroring rules, cultural color |
| 21 | Airship, RevenueCat, Adapty, Appcues, UXCam benchmark reports | Vendor data | Retention, opt-in, paywall and onboarding figures – directional only |
| 22 | Baymard Institute | Research | Autocomplete and search patterns |
| 23 | Mobbin, Smashing Magazine, LogRocket, UX Collective | Practitioner | Pattern catalogs, component behavior, critique method |
| 24 | ASO and store-listing practitioner sources | Vendor data | Icon, screenshot and store conversion figures – directional only |
| 25 | Emerge Tools, Paparazzi, swift-snapshot-testing, device farm vendors | Tooling | Snapshot and visual regression practice |
| 26 | `design-md` brand corpus | Primary corpus | Carried over via the web study; prohibition rules and emphasis discipline |

*Vendor-sourced conversion and retention numbers are marked directional throughout. They calibrate expectations; they are not targets and they are not evidence for a specific product.*
