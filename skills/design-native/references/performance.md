# Performance as a design constraint

Everything here is a design decision with a measurable cost, not an engineering afterthought.

## The frame budget

| Refresh | Budget per frame |
|---|---|
| 60 Hz | **16.67 ms** |
| **120 Hz** (ProMotion, high-refresh Android) | **8.33 ms** |

**Jank is defined, not felt for.** Google Play counts a frame **slow** above 16.67 ms and **frozen** above 700 ms. Play Console **flags apps with slow frames in over 25% of sessions, or frozen frames above 0.1%**, and shows a store warning above a **0.47% user-perceived ANR rate**.

The sentence that matters most:

> **Users do not feel average fps. They feel the bad frames.** A list that runs at 120 fps most of the time but stutters for 200 ms every scroll feels worse than a steady 60 fps.

**Consistency beats speed**, and on Android there is store enforcement behind it.

Tools: `adb shell dumpsys gfxinfo` for Choreographer frame drops, `FrameMetricsAggregator`, Macrobenchmark for a bracketed user journey.

## Adaptive refresh, and the trap

ProMotion is **adaptive, not fixed**: **10–120 Hz on iPhone, 24–120 Hz on iPad Pro**, ramping up for scrolling and transitions and dropping to 24 Hz or lower when static. Input-to-paint latency falls from ~16.67 ms to **~8.33 ms**.

Apple's own guidance states the trap:

> If your animation requests 120 Hz but cannot keep up, it may render poorly – but the same animation may hold a steady cadence at a lower rate.

**Deliberately targeting 60 can be the better design decision than targeting 120 and missing.**

Also worth knowing: high refresh activates for scrolling and full-screen transitions, **not for every animation**, so a custom animation may run at a different effective rate than the scroll next to it. That inconsistency is visible.

**Design conclusion: hit the high frame rate during interaction and stop animating entirely at rest.** Constant idle motion is the worst combination available – it pins the refresh rate high, burns battery, and gains nothing, because smoothness only registers when the user is moving something.

## Input latency – the numbers behind "responsive"

Touch latency perception is far finer than design intuition assumes, and the thresholds differ by task:

| Task | Detection threshold |
|---|---|
| **Dragging** (finger down, content tracks it) | **~6 ms**, with reported detection as low as 2–10 ms |
| Drag task **performance** degrades | above **~25 ms** |
| **Tapping** (discrete, feedback after release) | **~24 ms** – below that, imperceptible |
| **Inking** with a stylus | **~50 ms** |
| Unacceptable in any task | above ~580 ms |

**The asymmetry is the useful part: anything the finger is touching has a latency budget roughly four times tighter than anything it merely triggers.** A button can respond in 20 ms and feel instant. A sheet being dragged cannot.

- **Direct manipulation is where the frame budget must be defended.** Drop a frame during a drag and it is felt; drop one during a fade and it is not.
- **Never gate a drag on work.** Layout, decode and network belong outside the gesture. If the content under the finger is not ready, drag a placeholder.
- **Touch-down feedback beats touch-up feedback.** Showing the press state on down – before deciding what happens – converts a 24 ms budget into a much easier one, because the user has already been answered.

## Cold start

| Target | Verdict |
|---|---|
| **< 1.5 s** | Excellent |
| **< 2 s** | Good |
| **> 3 s** | Hurting retention measurably |
| ~5 s | Serious problem |

Published thresholds:

| Platform | Threshold |
|---|---|
| Android cold | **5 s or more flagged as excessive** by Android Vitals |
| Android warm | 2 s or more |
| Android hot | 1.5 s or more |
| iOS first frame | **Under 400 ms** |
| Practical target | **Under 2 s cold on a mid-range device** |

The consequence numbers, which is why this is a design concern: roughly **half of users uninstall an app over performance problems**, and about **a third uninstall if it takes more than six seconds to load**. Directional, but the direction is not in doubt.

> Teams can grow cold-start time by 400 ms over a year by shipping useful-but-not-critical features, and that 400 ms costs measurable first-session retention.

Cold start is process spawn → runtime init → application object → first screen built from scratch.

**Design levers:**

- **Optimize time-to-initial-display first.** If the user sees nothing, the app is broken regardless of how fast the rest arrives.
- **Defer everything not needed for the first frame** – analytics SDKs, crash reporters, remote config, feature flags. Each is small and only shows up on a slow device.
- **Predictive preloading**: fetch the likely first destination while the launch screen is showing.
- **Do not start with a screen that needs the network.** A cached last-known state that appears instantly beats a correct state at 1.5 s; reconciling afterwards is invisible.
- **Skeleton over spinner on the first screen** – users tolerate skeleton states about 40% longer.
- **Never add a splash delay** to show a logo. See `shipping.md`.

## Skeletons versus spinners, with the conflicting evidence

One of the few places the research genuinely disagrees, so it belongs here honestly rather than as a rule.

- **Against skeletons:** a 2017 Viget study (n=136) found skeleton screens performed **worst** on every metric – longer task completion, more negative evaluation of the wait, longer estimated wait times – against spinners or a blank screen.
- **For skeletons:** other testing found skeletons scored higher than spinners on perceived speed and ease of navigation. Facebook and others report content perceived as loading up to 50% faster; other measurements say 10–15% or ~30%. Instagram, Facebook and LinkedIn all switched.

**Note the asymmetry in the sources: the positive numbers are largely vendor-published, the contrary study is independent.**

What survives both: **the duration decides.**

| Wait | Use |
|---|---|
| Under ~300 ms | Nothing. Any indicator flashes and reads as a glitch |
| ~300 ms to 1 s | An inline spinner on the control that was tapped |
| 1 s to a few seconds, content-shaped screen | Skeleton matching the real layout |
| Several seconds, known progress | Determinate progress with a real percentage |
| Long or unknown | Progress plus what is happening, and a cancel |

Details that make a skeleton work rather than annoy: it must **match the real layout** so nothing shifts when content arrives, the shimmer should move **left to right** to read as forward motion, and it should not sweep faster than about once per 1.5 s or it reads as agitation. **A skeleton that does not match what loads is worse than a spinner**, because it promised a shape and broke it.

**Optimistic UI** is the other lever: update as though it succeeded, roll back on error. For actions that rarely fail this creates a dramatically faster-feeling app with no engineering performance work at all. It requires a visible pending state to be honest – see `patterns.md`.

## Compose – stability is the lever

**Type instability is the named primary cause of Compose jank.** Google maintains a *Stability in Compose* guide as the central performance document.

- **Diagnosis:** Layout Inspector shows a **recomposition counter per composable**. A constantly rising counter on a static part of the UI is an immediate red flag. CPU Profiler with System Tracing shows the three phases – Composition, Layout, Draw.
- Reported case: recompositions per scroll frame **7.2× → 1.0×**; janky frames down **over 60% on median devices**.
- Toolkit: Strong Skipping Mode, stability annotations, Baseline Profiles, R8 full mode, composition tracing in production.
- Kotlin 2.2 improved stability inference, so fewer unnecessary recompositions without hand-annotating `@Stable`. It reduces the work rather than removing it.

**A Compose UI can be visually correct and still jank because of data-model shape.** The designer-visible symptom is scroll stutter on mid-range devices, and mid-range is where most users are.

## SwiftUI lists – the counterintuitive result

The instinct is that lazy containers are faster. Measured, for long lists:

| | `List` | `LazyVStack` |
|---|---|---|
| Scroll to bottom | **5.53 s** | **52.3 s** |
| Hangs | **4.6** | **78** |

**`List` recycles views. `LazyVStack` only defers creating them and then keeps them.** For large data sets `List` wins decisively on both memory and scrolling.

Also: **stable IDs in `ForEach`**, and conform the model to `Identifiable` **and** `Hashable`.

Use `LazyVStack` when you need layout freedom `List` cannot give – custom separators, non-uniform rows, nested scroll – and accept the cost. Not because it is assumed faster.

## Overdraw

Drawing the same pixel more than once in a frame. Invisible in a mockup, and where a beautiful design turns janky on mid-range hardware, because **fill rate is the first thing that runs out on a weak GPU**.

The design choices that cause it, in order of cost:

- **Stacked opaque backgrounds.** A window background, a container background, a card background and a row background in the same place is 4x overdraw for one visible color. Set the background once, at the layer that needs it.
- **Alpha on large surfaces.** A translucent overlay forces everything under it to be drawn and blended. **Translucency costs in exact proportion to its area**, which is the technical reason Liquid Glass and Material's scrims belong on chrome rather than content.
- **Deep nesting** with a background at each level.
- **Shadows on many small elements**, since each draws outside its own bounds.

Tooling, both available as on-device developer options so a designer can run them without a build: **Debug GPU Overdraw** color-codes how many times each pixel was drawn (blue 1x, green 2x, pink 3x, red 4x+), and **Profile GPU Rendering** shows a per-frame histogram against the 16.67 ms line.

**Target: no more than 2x over most of the screen, and no red anywhere.** One reported case cut a product grid from 4x to 1.5x and measured roughly 38% less scroll jank.

**Every layer of translucency, blur, shadow and stacked background is a frame-budget expense, and the budget is spent on the cheapest device in the support matrix, not the reviewer's phone.**

## Battery

- **Reducing animation lowers battery consumption by 15–25%.**
- **Background animations can run at 30 fps with no noticeable quality loss.** 60 fps is for direct interaction; **120 fps consumes exponentially more**.
- Background-processing improvements yield 15–20%.
- Complex and simultaneous animations are the named drains.

**Ambient animation that runs continuously is a battery cost the user pays for the whole session.** That is a stronger argument than aesthetics for keeping it rare, or dropping it to 30 fps when it is decorative.

**Pause animations when backgrounded. Always.**

### Dark mode and battery, with the real numbers

Claimed loosely in both directions, so here are the figures.

- **OLED only.** Each pixel is its own light source and black pixels draw essentially no power. On LCD the backlight is always on, so dark mode saves nothing.
- Purdue's per-frame OLED power profiler measured the display at **44–73% of total phone power in light mode at maximum brightness**, and switching to dark mode cut total phone power by roughly **42% at 100% brightness**.
- **The catch is brightness.** At the 30–50% most people actually use, the saving falls to a few percent.

**Ship dark mode because users prefer it and expect it, not because of battery.** And note that true-black surfaces and legibility pull against each other – Material's `#121212` is a legibility choice, not a power one.

## The low-end device, which is most devices

**Android Go** targets devices with **512 MB to 1 GB of RAM** and as little as **8 GB of storage**, for three named conditions: slow, unreliable or expensive data; cheaper hardware; and limited opportunity to recharge. Samsung, Nokia, Motorola, Tecno, Infinix, Xiaomi and itel all ship Go models, so this is a large real population.

Typical low-end Android hardware more broadly: **1–2 GB RAM, a quad-core chip around 1.3 GHz, 8–16 GB storage.**

What this changes about design, not just engineering:

- **Animations degrade first.** A transition that runs at 60 fps on a flagship runs at 20 on a Go device, and a janky animation is worse than none. Keep motion cheap – opacity and small transforms – or gate expensive treatments behind a capability check.
- **Every megabyte is a barrier twice**: once at install, again on a device that is already full. See `shipping.md`.
- **Memory pressure means the app is killed in the background more often**, which makes state restoration a core requirement rather than a nicety. On a flagship a killed app is invisible; on a Go device it is the normal path.
- **Images dominate memory.** Decode at display size and cache bounded – on 1 GB of RAM an oversized bitmap cache is why the app gets killed.
- **Data cost is a design constraint.** Autoplaying video, prefetching a feed and high-resolution image defaults all spend the user's money.

**The design target is the slowest device in the support matrix, and that decision is made once, at the start** – retrofitting a heavy design onto weak hardware means removing the parts that were the point.
