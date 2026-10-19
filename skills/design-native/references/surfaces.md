# Surfaces beyond the app

Notifications, widgets and system capabilities, then desktop, watch, TV, car, spatial and embedded. Everything here is either outside the app or a different device entirely.

# Permissions and notifications

## Permission priming – the highest-leverage pattern here

**The system permission prompt is one-shot.** A "no" is effectively permanent, recoverable only by sending the user into Settings.

**The pattern: show your own screen first, and only fire the native prompt if they say yes to yours.**

- **Pre-prompts lift grant rates 2–4×.** Deferring a request until it is contextually relevant lifts grants by up to **28%**.
- If the user accepts your soft-ask, they have already decided; the native prompt is a formality and converts at very high rates.
- **The headline names the feature, not the permission.** *"Scan Documents Instantly"*, not *"Allow Camera Access"*. The system dialog already says the second thing; your screen exists to say the first.

> **The single most common push notification failure happens in the first thirty seconds, when an app opens and fires the system permission dialog before the user knows what the app does.**

**This is a design decision that permanently caps a channel.** It belongs in the first conversation about a native app, not in a growth review later.

| Permission | Guidance |
|---|---|
| **Camera / microphone** | Ask at the moment of first use – access appears as a document capture is about to begin. Clearest possible context |
| **Location** | **Over 60% of Android privacy breaches involve at least one location access point.** "Always" needs substantial justification and is heavily scrutinized by both stores. **Request while-in-use first**, escalate only with a demonstrated reason |
| **Contacts** | Ask inside the feature that needs it – invite, guest list – never at launch |
| **Notifications** | After a first success, not on launch |

**Privacy labels.** Apple's Nutrition Labels since December 2020, Google's Data Safety section mandatory since 20 July 2022. Every SDK's collection is your declaration, and session replay or heatmap tooling has to appear there.

## Push notifications

**Earn permission → send relevant content → time it well → land on the right screen → measure.**

- **Trigger on user actions, not on a schedule.** Send-time optimization from individual historical activity beats a scheduled blast, and it lifts open rates more reliably than inserting a first name.
- Anatomy: brand mark, title, one to three short sentences, a CTA.
- **Never disguise marketing as utility.** The clearest ethical line in this area.
- A poorly timed, irrelevant notification is ignored at best and provokes an uninstall at worst. **The downside is not a wasted impression, it is losing the user.**
- **The landing screen is part of the notification design.** A notification that opens the home screen instead of the referenced item has failed even if the copy was perfect.

**Copy rules:**

- **Specific beats promotional.** Numbers, names and concrete nouns outperform marketing language; all-caps reads as spam.
- **The title carries it.** Most are read on a lock screen where the body may truncate to one line, so the important word goes first.
- **Action buttons reduce friction measurably.** "View order", "Add to cart", "Book now" convert better than a tap-through.
- **Frequency is the retention risk, not the copy.** Around **46% opt out after 2–5 messages in one week** that do not feel relevant, and a revoked permission is permanent in practice.
- **Never put sensitive content in the preview.** The lock screen is a public surface.

**The test that catches most bad notifications: read it as though it arrived at 11 pm on a Sunday.** If it would not be welcome then, it needs a better trigger, not better wording.

**Opt-in benchmarks:** iOS around **44–54%** (the higher figure follows Apple's revised prompts in iOS 18.2). Android nominally **91–97%**, but that is inflated by legacy installs from Android 12 and earlier where permission was automatic. On Android 13+ the app must ask, like iOS.

## Rich notifications

**Two states, both of which must work:**

- **Collapsed** – one or two lines. **The compressed view has to work as a standalone message.**
- **Expanded** – images, long text, progress, action buttons. Android: `BigPictureStyle`, `BigTextStyle`, `RemoteViews`. iOS requires a **Notification Service Extension** to fetch and attach media before display.

**Media at 2:1** works on both platforms.

**Inline replies, quick actions and deep links let the user act without opening the app** – often the best possible outcome, and under-designed almost everywhere.

## Android notification channels

Every notification belongs to a channel (Android 8+), and **the user controls each channel independently.**

**Group by two things at once: subject matter *and* the importance level it deserves.** A channel has one importance level, so everything in it must genuinely warrant that level.

> When an unimportant notification is disguised as urgent, it can produce unnecessary alarm.

**The channel taxonomy is a design artifact.** A single "Notifications" channel forces the user to choose between everything and nothing, and they will choose nothing.

**This is the best notification-control model on any platform**, with no iOS or web equivalent. Where the app ships on both, design the taxonomy anyway – it clarifies what deserves to interrupt.

## Push versus in-app

> **Push is an external tap on the shoulder designed to bring users back. In-app messaging is an internal conversation that happens once they have arrived.**

- **Push** reaches users who are not in the app: re-engagement, urgency, time-critical events.
- **In-app** only while the app is open: behavior-targeted, better for onboarding, feature discovery and conversion.
- **The layered strategy is push to start the session, in-app to guide what happens in it.** Treating them as interchangeable is the common failure.

## Focus, summaries and ranking

The delivery layer is no longer neutral, and that is a design constraint.

- **Apple Intelligence ranks incoming notifications by inferred importance.** Context-tied messages – a delivery arriving, a meeting starting, a direct reply – get promoted. **Generic marketing is demoted or folded into the Notification Summary**, where it is read hours later or not at all.
- **Focus modes filter by app and category**, and 2026 extended this with Focus Filters that limit **in-app content**, not just alerts. An app can be asked to show a work-only view.
- **Time Sensitive breaks through Focus and the Scheduled Summary** – but only for genuinely time-critical content: a ride arriving, a medication reminder, a delivery at the door.

- **Marking everything Time Sensitive is self-defeating.** The user can revoke it and the system's ranking learns. Reserve it and it keeps working.
- **Relevance is now upstream of visibility.** Open rate is no longer just an engagement metric, it is a deliverability signal.
- **Design for the summary as a surface.** A notification landing in a batch three hours later must still make sense out of sequence and must not read as urgent when it is not.
- **Support Focus filters where the app has modes.** A work-and-personal app that respects Focus is doing account switching's job at zero interaction cost.

# Widgets and ambient surfaces

## The glance constraint

> **Users discard any widget that takes longer than 0.5 seconds to interpret.**

That has eliminated dense, multi-layered designs in favor of single-metric, high-contrast layouts.

**A widget is the most extreme version of the squint test: if the single most important value is not legible at a glance, the widget has no reason to exist.**

- **Glanceable, relevant, personalized.** Summarize one core feature.
- **Tasks completable in a single tap** where interactivity exists.
- **Cache locally.** People should not wait for content to load; always show recent data while updating.
- **Do not customize the widget background on iOS.** The system's light, blurred background is designed for consistency and legibility.

## iOS widget families

**Home Screen:** `systemSmall` (a 2×2 grid slot), `systemMedium` (4×2), `systemLarge` (4×4), `systemExtraLarge` (iPad, and iPhone from iOS 15).

**Lock Screen (iOS 16+):** `accessoryInline` (one line beside the time), `accessoryCircular` (a gauge or glyph), `accessoryRectangular` (a small block of text or a tiny chart). **These render monochrome and are tinted by the system**, so a design depending on color does not survive here.

**StandBy** (on charge, landscape) reuses the Lock Screen families at larger scale and in a dim red night mode – another reason not to encode meaning in color.

Rules that make a widget good rather than present:

- **Each size is a different design, not a scaled one.** Small holds one number or one action. Medium holds a small set, or one item with context. Large can hold a list. **Designing large and shrinking produces three bad widgets.**
- **Support small.** It is the size people actually place, because home screen space is contested.
- **Every widget needs a deep link target** that lands on the matching content, not the home screen.
- **Refresh budgets are real.** WidgetKit limits reload frequency, so design content that stays meaningful when a few minutes stale, and **never show a time-critical number that could be silently wrong.**

## Android widgets and Glance

**Jetpack Glance** builds widgets with a Compose-style Kotlin DSL over the existing `AppWidget` system. Logic that took 100+ lines of XML and `RemoteViews` lands in 30–40.

The composables available are deliberately small: `Box`, `Row`, `Column`, `Text`, `Button`, `Image`, `Spacer`, `LazyColumn`. **A widget needing more than these is trying to be an app.**

- **`SizeMode`** decides resize behavior: `Single` (one layout), `Exact` (recompose per exact size), `Responsive` (declare supported sizes and let the system pick). **`Responsive` is the correct default**, because users resize freely and a single layout stretched to a 4×1 grid looks broken.
- **Material You dynamic color works**, so a widget can pick up the wallpaper palette. The main reason an Android widget can look more at home than its iOS counterpart.
- **Glance is not interoperable with regular Compose UI.** They share a syntax and nothing else, so component code cannot be reused. **Plan the widget as its own small design, not a shrunken screen.**

## Live Activities and Dynamic Island

**Design order matters, and Apple states it:**

> **Design from the smallest surface first:** compact and minimal Dynamic Island, then the small activity family, then expanded, then Lock Screen.

**That is progressive enhancement applied to ambient surfaces**, and it is the opposite of how most teams work.

Constraints:

- **8 hours active plus 4 hours stale = 12 hours maximum.** The system ends anything longer.
- **Minimal presentation image ≤ 45 × 36.67 pt.**
- Multiple concurrent activities are allowed, but **the Dynamic Island shows one at a time.**
- Content must be **balanced around the camera cutout**.
- The same activity can surface on the **Lock Screen, StandBy, Apple Watch Smart Stack, Mac menu bar and CarPlay Dashboard** – **one design shipping to six surfaces**.
- Users can disable Live Activities globally or per app.

## Interactive widgets and controls

**iOS 17+ widgets support buttons and toggles**, executing code via **App Intents** and refreshing inline without launching the app. **iOS 18 extends the same intents to Control Center, the Lock Screen and the Action button.**

> **One App Intent, many places.**

Interactive widgets are reported at roughly **3× the engagement** of static ones. Directional.

**The implication is structural: an App Intent is a unit of *capability*, and the surfaces are just places it appears.**

## App Intents – the app's surface outside the app

App Intents (iOS 16+) replaced SiriKit, and **Apple gave SiriKit a formal deprecation notice at WWDC 2026**: App Intents is now the only route for Siri to call into a third-party app for new development.

One declaration buys **Spotlight, the Shortcuts app, Siri, the Action Button, Control Center controls, widgets, and Apple Intelligence** – seven discovery surfaces you do not have to design separately.

**An app that exposes no intents is invisible in an intent-driven OS.** As assistants and system search become the entry point, the question shifts from "what does my home screen look like" to **"what are the ten things a user can ask for by name"**. Those ten things are a design deliverable, and naming them is copywriting:

- The phrase must be what a person would actually say, not the internal feature name.
- Parameters need natural values ("morning", not "profile ID 3").
- **Every intent should be completable without opening the app** where possible, and return a compact result view where not.

iOS 27's App Intents 2.0 adds richer entity types, streaming responses for long-running actions, multi-turn follow-ups, and a View Annotations API letting users refer to on-screen elements by voice.

Android's parallel is **App Actions and `shortcuts.xml`** plus the Assistant's built-in intents – narrower, same idea.

**This is the strongest argument in the whole skill for designing capabilities rather than screens**, because the OS actively surfaces your capabilities in places you did not design.

## App Clips and Instant Apps

**App Clips ≤ 10 MB; Android Instant Apps ≤ 15 MB.** A lightweight version letting people perform an everyday task without installing.

**The design discipline: carefully select the smallest set of features that completes one task.** Apple's own example ships an App Clip that *only* orders donuts.

Use when the app provides an in-the-moment experience over a finite time – ordering, paying, booking, checking in.

**The size limit is the design brief**, and it is a useful thought experiment even when you are not shipping one: what is the one task, and what is the minimum interface that completes it?

# Desktop

## macOS

> Mac apps serve power users who expect deep keyboard control, persistent menu bars, resizable multi-window layouts, and tight system integration.

**Every Mac app must have a menu bar, and it is the primary discovery mechanism for commands.** A user who cannot find a feature looks there first, which makes it a design surface, not a compatibility checkbox.

- **Required minimum: App, File, Edit, View, Window, Help.** (File may be omitted if not document-based.) **Shipping only the auto-generated defaults is a visible tell that the app is a port.**
- **One-word titles** in the bar itself.
- **Standard items keep their standard shortcuts.** Cmd-C, Cmd-V, Cmd-S, Cmd-P, Cmd-W, Cmd-Q. Rebinding any of these is hostile.
- **Custom shortcut tiers:** Cmd+letter primary, Cmd+Shift secondary, Cmd+Option or Cmd+Ctrl tertiary. Define custom shortcuts only where the action is frequent.
- **Everything reachable by mouse needs a keyboard equivalent.** Mac users lean on the keyboard more than any other platform's, and an app that cannot be driven from it reads as unfinished.
- The Help menu searches the menu bar itself, which is a real discovery path and a reason to name items in the words users would search for.

**If a feature cannot be given a menu name, it probably is not a coherent feature.**

Also: **sidebar on the leading edge** as primary navigation, collapsible. **Toolbar items carry both an SF Symbol and a text label**, icons-only in compact mode. Search field in the trailing toolbar area.

## Windows 11 – Mica and Acrylic

| Material | Nature | Use |
|---|---|---|
| **Mica** | **Opaque**, subtly tinted by the desktop background, **very performant** | **Long-lived surfaces** – title bars, app backgrounds. Communicates active and inactive state |
| **Acrylic** | **Semi-transparent** frosted glass | **Only transient, light-dismiss surfaces** – flyouts, context menus |

**The cross-platform law, which Apple and Microsoft arrived at independently:**

> **Translucency belongs to transient, functional chrome. Opaque materials belong to persistent surfaces and content.**

WinUI now allows per-element Acrylic rather than whole-window backdrops, which makes it easier to misuse, so the rule matters more.

## Linux – GNOME and KDE

| | GNOME (Adwaita, GTK4 + libadwaita) | KDE (Breeze) |
|---|---|---|
| Base spacing | **8 px** | **12 px** |
| Focus ring | **2 px** | **10 px** |
| System font | Cantarell, 16 px | – |
| Shadows | 12% opacity | Variable gradients |

**There is no single Linux look.** GNOME and KDE disagree on spacing, focus and shadow. Either pick a `native`/`qt` style and inherit whichever is present, or **commit to your own coherent style and stop pretending to be native** – the half-way position looks wrong in both environments.

## Desktop windows and state

- Treat windows as **independent containers with their own input model and lifecycle**, not one mobile screen repeated.
- **Users expect state restoration.** Snapshot open windows and tabs; restore on next launch. **Restore position and size**, especially on multi-monitor setups.
- **Save state incrementally as the user works, not at shutdown.** Reduces data-loss risk and distributes I/O cost. Same conclusion as mobile lifecycle, different reason: crash rather than termination.
- Coordinate shared state across windows via a database, IPC or a local server rather than duplicating caches.

**Shortcut conventions:** Windows **Alt** = macOS **Option**; Windows **Win** = macOS **Command**; Shift and Ctrl match. The primary modifier differs – **⌘ on macOS, Ctrl on Windows and Linux** – so shortcuts are declared per platform, never hardcoded. **Attach accelerators to menu items**, which makes them discoverable rather than secret.

# Other form factors

## TV

**The focus engine replaces the pointer.** The system moves focus between elements based on **geometry first** – nearest eligible element in the direction pressed, by alignment and proximity.

**The layout *is* the navigation graph.** Two elements that look adjacent but are misaligned may not be reachable from one another.

- **Every interactive element needs an unmistakable focus state:** scale **1.05–1.1×**, elevation shadow, brightness lift, or border. **Subtlety is a failure here** – the user is roughly 3 metres away and not holding the device.
- **Focus movement must be spatially predictable.** A press right moves to what is visually right. **Grids where focus jumps unexpectedly are the defining tvOS usability failure.**
- **Minimum card target ~250 × 150 pt.** Large readable text, high contrast, clean layouts, fewer items per screen, and nothing requiring reading rather than recognizing.
- **Moderate parallax and motion.** The layered parallax on focused artwork is a platform signature and becomes nauseating when overused.
- **Top Shelf content** is a real acquisition surface, not an afterthought.
- Android TV: d-pad navigation, **instant and distinct feedback on press**, Compose for TV, and Leanback's content-in-rows structure.

**The focus state is the single most important visual in the whole interface**, which inverts the usual priority.

tvOS 26 picked up Liquid Glass, with the stated intent of keeping focus on what is playing.

## Automotive

**The only platform where the user's attention is legally and physically constrained**, and where the correct answer to "can we add this?" is usually no.

- **CarPlay allows only strictly approved categories** – navigation, audio, podcasts, messaging, EV charging – each safety-verified. Apple's philosophy prioritizes minimal distraction over customization.
- **Android Auto has widened categories** (passenger video, media, casual games) but gates them on vehicle state. **Video functions only when parked**, with motion detection disabling it and falling back to audio.
- Android Automotive apps must follow **Driver Distraction Guidelines** to run while moving.

**The template model is the whole design story.** Neither platform lets an app draw what it wants. **CarPlay and the Android for Cars App Library both provide a fixed set of templates** – list, grid, map with pane, message, navigation, search, sign-in – and the app supplies content. The system owns layout, type size, contrast and item counts.

**The templates are the compliance mechanism**, road-tested against government and industry distraction guidelines, so an app built from them inherits compliance rather than arguing for it.

- **The design work is content strategy, not layout.** Which six items appear, in what order, with what labels. That is the entire surface.
- **Item counts are capped and vary by vehicle.** Design for the smallest allowance.
- **Voice is a first-class path**, not an accessibility afterthought. Any task that cannot be completed by voice should not be attempted while driving.
- **The glanceability budget is stricter than a watch's.**

**The transferable lesson: a constrained template system produced apps that are more consistent, more accessible and faster to build than free-form design would have.** Worth remembering the next time a design system's constraints feel restrictive.

## Spatial – visionOS and Android XR

> **Depth is information. Use z-axis positioning to communicate hierarchy, not decoration.** Windows float, backgrounds do not exist, and depth is literal rather than metaphorical.

**visionOS:**

- **Windows are planes in space** with familiar controls; users reposition them, and **dynamic scaling keeps content legible near or far.**
- **Ornaments** are floating toolbars outside the main window. They **overlap the window's bottom edge by 20 pt** to anchor visually, and sit slightly in front to establish depth hierarchy.
- The glass material lets light and color from the user's actual room bleed through, making the app feel like it belongs there.
- **Glass is earned.** Translucent materials only work when the content behind them adds context.
- **Eyes are the cursor.** Size, spacing and interactive zones must account for gaze precision.

**Android XR**, covering headsets, wired XR glasses and AI glasses:

- **Spatial panels are the building block** – a 2D layout floating in space – and they **scale automatically with distance**, holding apparent size between roughly **0.75 m and 1.75 m**.
- Maximum panel size in Full Space is **2560 × 1800 dp**; no minimum.
- The conversion is **0.868 dp to dmm**, and because content sits farther away than a phone, **everything must be larger**.
- **Minimum touch target 48 × 48 dp, optimal 56 × 56 dp or more.**
- Depth and elevation on standard components, plus 3D entities (glTF/USDZ), stereoscopic video and 360-degree environments.

**Liquid Glass, Windows Mica/Acrylic and spatial glass are the same idea at three scales, and all three carry the same rule: depth carries information, or it does not belong.** Spatial is where that stops being a metaphor.

## Wearables

**The five-second rule governs everything.** Most smartwatch interactions last under five seconds.

**Wear OS:**

- **Watch Face Format (declarative XML) is required for installing watch faces on all Wear OS devices as of January 2026.** A real deadline.
- **Up to eight complications** per watch face.
- Complication types map to shape: `RANGED_VALUE` and `GOAL_PROGRESS` on edge Arc complications; `SHORT_TEXT` and `SMALL_IMAGE` on circular ones.
- **Wear OS 6 brought Material 3 Expressive to the watch**, optimized for round displays, with dynamic theming matching the watch face and components shaped for a circle rather than adapted from a rectangle. Up to 10% better battery than Wear OS 5.
- **The tile layout is three slots: title, main content, bottom slot.** That is the whole canvas. A tile needing a fourth region should be an app screen.

**watchOS:** complication families define templates; **line widths of 2 pt or greater**, because the wearer is in motion. **Motion is a legibility variable**, which is a constraint no other surface has. Support all families, and where you cannot show useful data still supply an app-representative image so the complication remains a launch point.

**The wearable constraint set is the extreme case of everything else here:** one piece of information, maximum contrast, no reading, motion tolerance, five seconds. **It is the best possible test of whether you know what the product's single most important value actually is.**

## Embedded HMI and kiosks

**The most consequential single fact in this section:**

> **From 2026, Euro NCAP penalizes vehicles for over-reliance on touchscreens for essential functions, requiring physical controls with tactile feedback for core functions.**

**A safety regulator has formally reversed the touchscreen-everything trend.** For any automotive or safety-adjacent interface, **the physical control is now the compliant design**, and a touchscreen for a core function is a scored defect. Clearest available evidence that "everything is a screen" was a fashion rather than a conclusion.

Industrial HMI principles: clarity, responsiveness, safety, usability. **Strong HMI design centers on situational awareness** – helping users instantly understand what the system is doing and what is normal.

The 2025–26 direction is consumer UX principles imported into industrial HMI, replacing dense data-heavy screens with cleaner layouts emphasizing *actionable* information, improving learnability and reducing operator training time.

### Constrained displays

- **Every visual element, animation and font consumes resources.** Maximum clarity with minimum overhead.
- **Use shapes and contrast rather than color gradients.** Avoid complex graphics and fine detail, which pixelate at low resolution.
- Compress icons, images and fonts; load only what is needed; release promptly.
- Where there is no touchscreen, organize for **button or rotary-encoder navigation** – the same focus-graph problem as TV.

**The memory budget, in numbers:**

An MCU GUI framework adds a base footprint of **tens of kilobytes**, and a smooth 25 fps UI is achievable on a 32-bit MCU with **a few hundred kilobytes of RAM**. Everything above that is assets, and assets are where designs die.

| Asset | Cost | Lever |
|---|---|---|
| Full CJK font | **10–30 MB of flash** | **Selective glyph compilation** – compile only referenced glyphs. A typical Japanese HMI drops to **300–800 KB** |
| Glyph cache | ~4 KB, configurable | Removes repeated rasterization for live numeric readouts, a known instrument-cluster hotspot |
| Full-frame buffer | width × height × bytes per pixel | Line-by-line rendering removes it, at the cost of full-frame effects |
| Compressed images | Smaller flash, higher CPU, a temporary RAM buffer | A genuine trade, decided per product |

- **Every glyph costs flash.** Three type families and six weights is not a style choice on an MCU, it is a bill of materials. **Restraint here is engineering, not minimalism.**
- **Localization changes the hardware requirement.** Adding Japanese to a product that shipped in English can exceed the flash budget outright, so locale coverage belongs in the design brief.
- **Animations are cheap only if they redraw a small region.** A full-screen transition on an MCU is often impossible, so motion is designed as local state change rather than screen movement.
- **Frame-sampling effects – blur, translucency, drop shadows over content – are largely unavailable.** Flat fills, gradients, sharp edges and careful typography are the vocabulary, which is why good embedded UI often looks cleaner than its desktop equivalent.

**Kiosks and self-service terminals are in EAA scope.** See `accessibility.md`.

**Physical viewing conditions** that indoor screens never face: sunlight readability needs **2500–4000 nits** and a **5:1 ambient contrast ratio**; gloved operation needs **15–20 mm targets**, roughly 3–4× the consumer minimum; automotive night mode uses red wavelengths to preserve night vision.
