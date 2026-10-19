# Apple platforms

iOS, iPadOS, macOS, and the SwiftUI/UIKit layer. Watch, TV and visionOS are in `surfaces.md`.

## Liquid Glass – the current design language

Announced WWDC June 2025, shipped across iOS 26, iPadOS 26, macOS Tahoe, tvOS 26, visionOS 26 and watchOS 26. Any Apple-platform work now has to reckon with it.

**The core idea is hierarchy through depth.** Importance is carried by transparency, refraction and visual weight rather than by color contrast or size. That is a real departure from a decade of flat design, and it is contested – legibility over refracting backdrops is the standing criticism.

**The law that governs adoption:**

> Liquid Glass belongs in the **functional layer** – controls, navigation, transient UI. **Never in the content layer.**

And the corollary that explains most bad adoptions: *Liquid Glass is a one-line API only when the app already respects the HIG layering rule.* An app that already separates chrome from content adopts it and feels native by default. An app that does not, does not.

### Adoption mechanics

An app gets the new appearance by **recompiling against the iOS 26 SDK**. There is no opt-in flag and no per-view adoption. `UITabBarController`, `UISplitViewController`, navigation bars and toolbars all pick it up automatically.

- **You do not implement Liquid Glass; you let the system do it.** A hand-rolled translucent capsule looks close today and wrong next release.
- **Recompiling is a design event.** An app shipping a custom tab bar now sits next to system chrome that changed underneath it, and the mismatch is what users see. Budget a design pass for the SDK bump, not just a build.

### The scroll behavior

Tab bars on iPhone float in an inset capsule above content and **minimize on scroll down, expanding on scroll up**. The tab bar is no longer a fixed reserved strip.

Anything previously pinned above it – a mini player, a cart summary, a status banner – should become a **tab bar accessory view**, which the system animates in sync. A separately positioned overlay will desynchronize.

### The API surface worth knowing

| API | What it does |
|---|---|
| `.glassEffect()` | Applies the material to a view |
| `GlassEffectContainer` | Groups glass elements so they blend and refract as **one** surface. Using the modifier without the container on a group of controls is the most common visible mistake |
| `.scrollEdgeEffectStyle()` | How content fades into a toolbar or tab bar: `.soft` (default blur), `.hard` (clean cutoff), `.automatic`. Dense UIs want `.hard` – a soft blur over dense content reads as smearing |
| Toolbar minimization | Toolbar collapses on scroll, expands on reverse, matching the tab bar |
| `ToolbarSpacer` | Splits toolbar items into separate glass capsules instead of one bar |
| `.labelIconToTitleSpacing()`, `.labelReservedIconWidth()` | Label internals; what makes mixed-width icons align down a list |

All of these are knobs for one question: **how does chrome sit over content.** That is the entire iOS 26 design problem.

**Contrast caveat:** glass puts the contrast floor at risk, and `GlassEffectContainer` merging makes the effective backdrop harder to predict. Measure against the real backdrop, and check with Reduce Transparency on. See `accessibility.md`.

## SwiftUI vs UIKit in 2026

**SwiftUI is the default for new work.** Production-ready since iOS 17, matches UIKit on performance, has the previously missing primitives (charts, navigation, search, complex layout), and is where Apple ships all new API. For visionOS and new Mac apps it is the only viable choice.

The realistic architecture is **SwiftUI-first with UIKit islands**. Both bridges are stable: `UIViewRepresentable` / `UIViewControllerRepresentable` to embed UIKit in SwiftUI, `UIHostingController` for the reverse.

Reach for UIKit for custom view-controller transitions needing precise control, deeply customized collection-view layouts, and UIKit-only SDKs. UIKit is the specialist you call for the hard parts.

## The layout model is inverted from the web

> SwiftUI's layout system is inverted from UIKit and from CSS: you describe what you want, and views decide their own sizes.

There is no box model to set. There is a negotiation: **the parent proposes a size, the child reports what it wants, the parent places it.** This is the single biggest conceptual shift coming from CSS, and fighting it produces most SwiftUI layout confusion.

| Concept | Behavior |
|---|---|
| `VStack` / `HStack` | Distribute by **intrinsic content size** unless told otherwise. Text takes its intrinsic width; `Spacer` absorbs the remainder |
| `layoutPriority` | Does **not** change a view's sizing behavior. It decides **which child the parent proposes to first**, so the higher priority is served before space runs out |
| `frame` | Prefer flexible frames (`minWidth` / `idealWidth` / `maxWidth`). Fixed sizes only for genuinely fixed things like icons |
| Size classes | **Prefer over `GeometryReader`.** Compact or regular per axis – four buckets, not a continuum |
| `GeometryReader` | The escape hatch. Reaching for it is usually a sign the layout is fighting the system |

**`layoutPriority` is the tool for the truncation problem.** When two labels compete for width, the one that must stay whole gets the priority. It is more expressive than `flex-shrink` and it is the right place to encode "this text is the differentiator".

**Size classes are coarse.** "Breakpoints come from the content" does not port cleanly – you get four buckets and design to them. See `structure.md`.

## Spring animation – named, not hand-tuned

Three presets, and the naming is good design vocabulary:

| Preset | Physics | Use |
|---|---|---|
| `.smooth` | **Critically damped, no bounce** | Subtle transitions, natural deceleration |
| `.snappy` | Small bounce, crisp | Button taps, toggles |
| `.bouncy` | Noticeable bounce | Onboarding, celebration, playful moments |

Custom springs take **`duration`** (time to settle) and **`bounce`** (0 = no overshoot, 1 = maximum). Two parameters, not four, which makes them designable.

Raw defaults, when you need the numbers: `.spring()` is **response 0.55, dampingFraction 0.825, blendDuration 0**. `.interactiveSpring()` is **response 0.15, dampingFraction 0.86, blendDuration 0.25** – use it for anything a finger is currently touching. Full parameter table in `motion.md`.

Springs are **interruptible and velocity-aware by construction**, so Apple's central motion principle is the default here rather than something to engineer.

**Choosing the app's default spring is a brand decision expressible in one word.** Calm precision is `.smooth`; sharp and rhythmic is `.snappy`; playful energy is `.bouncy`.

## SF Symbols

**9 weights** (ultralight to black), each matching a San Francisco font weight so symbols align with adjacent text at any weight and size. **3 scales** (small, medium, large). **4 rendering modes**, with and without variable color. **216 configurations** of any single symbol.

Rendering-mode discipline, as Apple states it:

- **Variable color communicates *change*** – progress, signal strength, volume. Not depth.
- **Hierarchical communicates *depth*** – elevating layers, separating foreground from background.

Custom symbols use **variable templates**: draw three weights, and the system generates the other 24.

**The whole icon-consistency problem – one grid, one stroke weight, optical sizing, pixel alignment – is solved by the platform.** The correct instinct is to use the system symbol, and the bar for a custom icon is that no symbol expresses the concept.

## Dynamic Type

**12 sizes**: xSmall, Small, Medium, Large (default), xLarge, xxLarge, xxxLarge, plus **AX1–AX5** when Larger Accessibility Sizes is on.

- Built-in text styles get Dynamic Type for free. **Hardcoded point sizes opt out of it entirely.**
- Custom fonts scale through `UIFontMetrics`.
- The requirement: text stays legible at the largest sizes without truncating or overlapping, the layout adapts as text grows, hierarchy survives, and interactive elements stay reachable.

**Dynamic Type scales only text, not the whole interface.** That is the difference from browser zoom, and it is why layouts break differently here – a fixed-height row with growing text **clips** rather than reflows.

**The check:** set the largest AX size and walk the primary flows. Highest-yield accessibility test on iOS.

Verify that any cross-platform toolkit in use actually honors the AX sizes. Own-canvas renderers frequently do not.

## Navigation

**`NavigationStack`** (iOS 16+) is data-driven: a path array of route values, giving deep linking, programmatic control and type-safe routing that used to need third-party libraries.

- The common pattern is an **`enum` conforming to `Hashable` and `Codable`** as the route type. `Codable` specifically so paths can be persisted and restored.
- Deep links parse a URL into route values and assign the path; intercept with `.onOpenURL` at the app root. Cold-start handling in `shipping.md`.
- **`NavigationSplitView` as the root for universal apps** – multi-column on iPad and Mac, collapsing to a single-column stack on iPhone automatically.

The navigation model is a data structure, which means **the information architecture is expressible as a type**. The tree is literally in the code, which makes it reviewable.

## Spacing and layout constants

| Value | Meaning |
|---|---|
| **16–20 pt** | Standard content margin from screen edges |
| **16 pt** | Form sheet padding |
| **8 / 16 / 24 pt** | Spacing increments, on an 8 pt base |
| **44 pt** | Minimum interactive target **and** standard list row height |

**Safe areas** keep content clear of rounded corners, sensor housings and the home indicator. **Layout margins** add separation between content and the safe-area edge. Two different things, both needed.

## Color

**Use the semantic colors before creating custom ones:** `label`, `secondaryLabel`, `tertiaryLabel`, `quaternaryLabel`, `separator`, `systemBackground` and friends. They are defined by role, and the system resolves each to the right shade for the current appearance and accessibility settings.

The four-level label hierarchy is a better-designed default than most hand-built systems achieve.

Custom colors go in an **asset-catalog Color Set with Any / Light / Dark appearance slots**, from day one. Images too.

**Apple's recommended contrast for custom colors in dark mode is 7:1, especially for small text** – substantially stricter than WCAG AA's 4.5:1. Treat it as the platform convention.

## Building a design system in SwiftUI

Escalation order, cheapest first:

1. A `View` struct with parameters.
2. **`ViewModifier`** for styling – takes a view, applies changes, returns a modified view.
3. **`ViewBuilder`** for composable containers.
4. **Generics** for flexible containers.
5. **`Environment`** for global values.
6. **Protocols** for shared behavior.

**`Environment` is the token system.** It is the native equivalent of CSS custom properties cascading down a tree, and it is the right place to put a theme. See `foundations.md` for the token structure itself.
