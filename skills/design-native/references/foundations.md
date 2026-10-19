# Foundations

Typography, color, theming, tokens, icons, illustration, localization. The platform-specific systems are in `apple.md` and `android.md`.

## Use the system font unless brand overrides it

**SF Pro on iOS, Roboto on Android.** Both variable (SF Pro, Roboto Flex).

Three concrete reasons, not aesthetic ones:

1. **Zero latency.** Nothing is fetched.
2. **Accessibility scaling for free.** Dynamic Type and Android font scaling work with no work.
3. **It reads as native**, and SF Pro is optically adjusted for screen rendering at small sizes.

**The bar for a custom face on native is higher than on the web**, because you give up platform accessibility integration, not just add a download. If brand requires it, wire it to `UIFontMetrics` and Android's scaling explicitly – otherwise the app silently opts out of the most-used accessibility feature on both platforms.

## Android 14 changed font scaling

**Maximum font scale went from 130% to 200%, and scaling is now non-linear.** `FontScaleConverter` applies a deliberate curve:

| Size | Growth at the 2.0× setting |
|---|---|
| **8 sp** | **can exceed 200%** |
| **57 sp** (display) | **only ~112%** |

Small text scales aggressively while large display text barely grows, which prevents display type becoming unusable while keeping small text readable.

**API consequence: `fontScale` is now informational only.** Fonts are no longer scaled by a single scalar. **Any layout math that multiplies by `fontScale` is wrong on Android 14+.**

This is a genuinely elegant answer to a problem browser zoom has never solved – zoom scales everything uniformly, so headlines become absurd before small text becomes comfortable. **Android scales by need.**

**`sp` for text, `dp` for everything else.** Using `dp` for type to protect a layout is exactly the failure mode.

## Mobile measure is not web measure

| Context | Characters per line |
|---|---|
| Desktop / web body | 50–75 |
| **Mobile portrait** | **30–50**, commonly cited as 35–50 |

The reason is arithmetic, not preference: on most phones 60 characters extends beyond the screen, and fitting it requires type too small to read comfortably.

**The web rule does not port.** Chasing 65ch on a phone forces type below the 16 px floor. **Accept a shorter measure and keep the type size** – the opposite trade from the web.

Other values: minimum 14–15 px, line-height at least 1.3, 1.5 for body. And test on real devices; emulators do not replicate reading a phone held at arm's length.

## Variable fonts

- **SF Pro is optically tuned and the system switches between SF Pro Text and SF Pro Display by size automatically.** That switch is why Apple type looks right at both 11 pt and 34 pt without manual tracking, and why a static custom font at every size usually looks worse.
- **Roboto Flex exposes 12 axes**, including optical size (`opsz`) and grade (`GRAD`).

**The axis worth understanding is grade.** It changes apparent stroke weight **without changing character widths**, so text can be made optically heavier at small sizes, on dark backgrounds or in bright ambient light **with no reflow**. Weight cannot do that – 400 to 500 changes advance widths and the layout moves. **Grade is the tool for compensation; weight is the tool for hierarchy.**

Free variable fonts that actually include an optical-size axis are a short list: **Inter, Source Sans 3, DM Sans.** Most ship weight and width only.

- Ship **one variable file, not five statics**. Usually smaller, and intermediate weights come free.
- **Subset it.** A full-coverage variable font is a large asset, and app size is a conversion variable.
- On dark backgrounds text appears heavier. **Drop the grade, not the weight.**

## Custom fonts – licensing is a shipping blocker

- **Bundling a font in an app binary is distribution, and distribution triggers license obligations.**
- **A desktop license does not grant the right to embed a font in a mobile app.** Foundries sell app licenses separately.
- **OFL (Google Fonts and most open-source faces) permits bundling in commercial apps free of charge.** The safe default.
- Flutter and other own-canvas toolkits need the actual font files, because they draw their own text.

**Check the license category before choosing the typeface, not after.**

## Localization is a layout constraint

**German runs 30–50% longer than English. Design for 40% expansion.** Short strings expand worst – a 5-character English label can double.

Where it breaks first, in order: tab bar labels, fixed-width buttons, form labels in a two-column layout, modal titles.

- **Let containers size to content.** Never fix a width to fit the English string.
- Allow two lines on labels that can wrap; choose truncation deliberately for those that cannot.
- **Never concatenate sentences from fragments.** Word order changes between languages and fragments do not reorder.
- Leave space around icon-plus-label pairs; the label grows and the icon does not.
- Test with **German or Finnish** for length and **Japanese** for the opposite problem, where a layout tuned for expansion looks empty.

### RTL

**A layout-system decision made on day one.** Retrofitting Arabic or Hebrew into an LTR-only app typically means rebuilding the layout, which is why teams that do RTL well build locale-aware layout from the start.

| Mirrors | Does not mirror |
|---|---|
| Layout direction, leading and trailing edges | Clocks and time-of-day icons |
| Back and forward chevrons, arrows | Checkmarks |
| Progress bars and sliders (fill from the right) | Media transport controls – play still points right |
| List and grid reading order | Logos and brand marks |
| Transition direction – push comes from the left | Numbers themselves, in most locales |

**Use logical edges everywhere:** `leading` / `trailing` in SwiftUI, `start` / `end` in Compose and Android XML, `margin-inline-start` on the web. **Hardcoded `left` / `right` is the single mechanism by which RTL support breaks.**

Mechanical mirroring is the starting point, not the answer. It needs review by someone who reads the language, or you get a technically mirrored UI that reads as strange.

**The real trap is the interaction with text scaling: a long translation at the largest accessibility size is the worst case, and it is the case nobody tests.** That combination is where labels overlap, buttons clip and layouts collapse. Put it in the device matrix as an explicit configuration.

### Locale formatting

**Never hand-format a date, number or currency.** `DateFormatter` / `NumberFormatter` on iOS, `DateFormat` / `NumberFormat` on Android – locale-aware from the device locale.

Currency symbol position, decimal and thousands separators all vary. **Hardcoded date formats and number separators are among the most common i18n mistakes.**

Native date pickers automatically localize numerals, labels and layout direction, which is a strong argument for the platform picker over a custom one.

### Color and icons across cultures

Color meaning is not universal, and the cases that bite are specific:

| Signal | Western default | Divergence |
|---|---|---|
| Financial gain | Green up, red down | **Reversed in China and much of East Asia** – red is gain |
| Red | Danger, error, stop | Luck and celebration in China; mourning in parts of Africa |
| White | Clean, empty, default | Mourning in several Asian cultures |
| Purple | Premium, creative | Mourning in parts of Latin America |

**The financial one is the sharpest:** a trading app shipped to China with Western red/green semantics is not stylistically off, it is **backwards**, and users read losses as gains.

Very few icons are universal, and gestures are the worst offenders – a thumbs-up or open palm is offensive in some regions. Direction-bearing icons flip in RTL; clocks and storage do not.

Pick a default set, then have a native speaker or local designer review color semantics, icons and imagery per market. Cheap next to shipping a red-for-loss trading screen into a market that reads it the other way.

## Dark mode is the majority case

| Measure | Figure |
|---|---|
| Android users running dark mode | **~82%** permanently or scheduled |
| iOS adoption | 55–70% |
| Top App Store apps supporting it | 92% |
| Overall stated preference | ~68% |
| **Preference in the evening** | **87%** |
| **Preference at night** | **over 91%** |

Directional figures from survey aggregations; the pattern is solid even if the decimals are not.

**On mobile, dark mode is the default state for most users for most of the evening.** Designing light-first and adding dark afterwards inverts actual usage. Treat dark as a first-class citizen from the first frame – it costs far less than fixing it later.

## Tonal elevation

**Material 3 represents elevation primarily through tonal color overlays rather than shadows.** An elevated surface receives a **surface tint drawn from the primary color slot**; higher elevation means a more prominent tone. Background stays constant; **surface changes with elevation**.

M2's dark-theme fix was a semi-transparent white overlay. **M3 replaced it with a tint from the brand's own primary color**, in both light and dark, so **depth and identity become the same mechanism**.

The general principle it confirms: the best systems get depth from **surface**, not shadow.

## Theming architecture

**Three tiers:** global (raw values) → semantic or alias (`color.action.primary`) → component (`button.background`).

Tokens live platform-agnostically in JSON or YAML and are transformed by **Style Dictionary** into CSS for web, XML for Android and Swift for iOS. The **W3C-track token format** backed by Adobe, Google, Meta and Figma means one file can drive Figma, CSS, iOS and Android without a hand-built translation layer.

The important native property: **tokens map to native OS APIs, which allows implementation across iOS and Android while preserving the native components, behaviors and interactions.**

**That is the resolution to the consistency-versus-idiom debate, expressed as architecture: share the token layer, keep the component layer native.** Brand travels; behavior stays platform-correct.

**The failure to watch for: tokens that are only primitives.** `blue600` in code is a renamed hex literal and buys nothing. The value is in the semantic tier, because that is the layer that can change per theme, per brand and per platform without touching a screen.

### Concretely, per platform

- **Compose:** the theme is a set of tokens exposed through `CompositionLocal`. `MaterialTheme` is built entirely on public APIs, so a custom system is built the same way rather than by fighting Material. The usual structure is `AppTheme` providing `colors`, `typography`, `shapes` and `spacing` – **`spacing` being the one Material does not give you.**
- **SwiftUI:** an `EnvironmentKey` carrying a theme struct, read as `@Environment(\.theme)`. Asset catalog color sets handle light, dark and high-contrast variants automatically, which is where SwiftUI is ahead: **one named color resolves per appearance with no branching in view code.**
- **Slint:** a `global Theme` with color, spacing, radius and font tokens. See `rust-gui.md`.
- **Flutter:** `ColorScheme.fromSeed` for the scheme, `ThemeExtension` for brand tokens Material has no slot for, and a spacing scale you define yourself.
- **React Native:** `StyleSheet.create` outside components, so styles become opaque references. Reusable theme constants plus a shared component library.

Every one of these is the same architecture: **a token layer the components consume, never values inline.**

### White-label and multi-brand

- **iOS:** Xcode targets with per-brand assets and `Info.plist`.
- **Android:** product flavors with their own `applicationId`, resources and build config.
- **Compose Multiplatform:** per-client app modules depending on a shared white-label library, theming injected.

**Theme radius, shape, spacing density and motion character too**, or every brand looks like the same app repainted.

## Semantic color first

Use the platform's semantic colors before creating custom ones: iOS `label` / `secondaryLabel` / `tertiaryLabel` / `quaternaryLabel` / `separator` / `systemBackground`; Material's role model. They resolve to the right shade for the current appearance and accessibility settings automatically.

Custom colors go in an asset catalog Color Set with **Any / Light / Dark** slots from day one, images too.

**Apple's recommended contrast for custom colors in dark mode is 7:1, especially for small text** – stricter than WCAG AA's 4.5:1.

### High-contrast settings

- **iOS: Increase Contrast.** System colors adapt automatically; custom colors need a **High Contrast variant** in the Color Set (`highContrastLight` / `highContrastDark`).
- **Android: High Contrast Text.**

The caveat, stated plainly:

> Supporting Increase Contrast **does not make your app WCAG compliant.**

**Design at adequate contrast by default and treat the accessibility setting as an enhancement, never as the compliance path.**

## Wide gamut – Display P3

**Display P3 covers roughly 25% more visible color than sRGB**, mostly in reds, oranges and green-cyans. Apple has shipped it since iPhone 7; Android added color management in 8.0 (API 26).

- **iOS:** asset catalog colors and images can be authored in Display P3; the system converts for non-P3 displays. No opt-in flag for UI color.
- **Android:** wide gamut is **opt-in per activity** via `android:colorMode="wideColorGamut"`. Without it, content is clipped to sRGB. Compose 1.12 added P3 and HDR support.
- **Rust toolkits:** generally sRGB unless the renderer is explicitly configured otherwise. Assume sRGB and verify.

**The failure mode is inconsistency, not dullness** – the same brand red rendering differently in the app, the widget and the store screenshot because three pipelines handled the profile differently. **Fix the pipeline before chasing the color.**

Photo and video apps are where P3 pays. For a mostly-typographic UI it is a refinement, and **sRGB with correct tagging beats P3 with a broken pipeline every time**. Contrast ratios do not change, and a very saturated P3 color can be harder to read against.

## Icons

Both platforms solve the icon-consistency problem at system level, and both make **fill the state distinction** rather than color. Details in `apple.md` (SF Symbols) and `android.md` (Material Symbols).

### The tab bar convention

**Filled for the selected tab, outlined for the unselected ones.** It is the strongest and most universally understood state signal in mobile navigation, and it survives color-vision deficiency, monochrome rendering and small sizes because the difference is shape, not hue.

Both symbol systems provide the pair from one glyph – SF Symbols with a `.fill` variant, Material Symbols with the fill axis. **Do not draw two icons.**

### Custom symbols

- **iOS:** export a variable template, draw **three** weights, and the system generates the other 24.
- **Android:** match the Material Symbols grid, weight and optical size, or the custom icon reads as foreign next to system ones.
- The bar for a custom icon on either platform is that **no system symbol expresses the concept**.

### Animated icons

**Lottie** is the format: 5–20 KB of JSON for a typical icon, MIT-licensed player, every platform. Use it for icons that play a fixed animation. Use **Rive** when the animation depends on state. Use neither for standard transitions. See `motion.md`.

### The user now themes your icon

iOS 26 gave users system-wide control over app icon appearance: **tint the whole home screen a chosen color, switch to a dark variant, make icons translucent glass, resize them, and remove labels entirely.** Android has had icon theming through Material You and third-party launchers for longer.

- **The icon must survive being tinted.** A mark that reads only through its own colors disappears when the system flattens it to one hue. Contrast within the mark – shape, negative space, weight – is what survives.
- **Supply the dark and tinted variants** rather than letting the system derive them. Letting it guess produces muddy results on detailed icons.
- **Labels may be off.** A user with labels hidden identifies the app by silhouette alone.
- **Do not fight it.** An icon designed to look wrong when tinted does not stop the user tinting; it just looks wrong.

**Personalization moved from a niche to a default surface. The correct response is a simpler, stronger mark, not a more elaborate one.** Store-facing icon requirements are in `shipping.md`.

## Illustration

Empty states, onboarding and error screens are where illustration lives on native, and the failure is almost always inconsistency rather than quality.

**Pick one primary style for the whole product**, and at most one secondary reserved for a specific repeated job. The pairing that works: flat throughout, line illustration for empty states. What does not work is three styles because three screens were designed by three people at three times.

| Style | Reads as | Best for |
|---|---|---|
| **Flat** | Neutral, modern, recolorable | The default for product UI; solid shapes stay clear at any size |
| **Line** | Light, unobtrusive, technical | Dense screens, and empty states where the illustration must not dominate |
| **Micro-illustration** (48–64 px) | Compact, narrative | Empty states, feature cards, list decoration |
| **Soft 3D** | Premium, playful, heavy | Marketing surfaces and onboarding; expensive to keep consistent |

Rules regardless of style:

- **Recolor from tokens.** Hardcoded brand colors break in dark mode and again on a theme change. Build them to accept two or three token colors.
- **They must work at the size they will actually be shown**, which is usually smaller than the canvas they were drawn on.
- **Never carry meaning in the illustration alone.** It is decoration next to the message. An empty state still needs a heading and an action.
- **Ship them as vectors** – SVG on Android and Slint, PDF or SVG in an iOS asset catalog – so there is one asset rather than three raster scales.
- **Check them for cultural readings** before shipping globally. Hands, gestures and food are the usual offenders.
