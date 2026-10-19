# Rust GUI

Slint in depth, then the rest of the landscape and how to choose. Cross-platform non-Rust toolkits are at the end.

## Choosing a toolkit

The axis that decides everything is **who draws the pixels**:

| Approach | Toolkits | You get | You owe |
|---|---|---|---|
| **Own canvas** | Slint, Flutter, Compose Multiplatform, egui, iced, GPUI | Pixel-identical brand everywhere | Accessibility, text rendering and platform conventions are yours to build |
| **Native widgets** | SwiftUI/UIKit, Compose, Qt with `QStyle` | Free accessibility, free text quality, free conventions | Per-platform work, and the brand bends to the platform |
| **System WebView** | Tauri, Capacitor | One frontend, smallest team | Gesture handling and transition interruptibility you cannot fully match |

For a Rust project in 2026:

- **Embedded, kiosk, appliance, instrument panel** → **Slint.** Not close.
- **One ambitious desktop app, performance is the product** → **GPUI**, accepting pre-1.0 churn.
- **A tool: debug UI, editor, internal utility, dense controls** → **egui.**
- **A desktop app where correctness and state discipline matter most** → **iced.**
- **React-shaped team, web plus native from one tree** → **Dioxus.**
- **Existing web frontend, desktop first** → **Tauri.**
- **Phone as the primary target** → not Rust yet. Native or Compose Multiplatform.

---

# Slint

## What it is, structurally

A **declarative DSL compiled to native code** – not a retained-mode widget library and not a WebView. One `.slint` source, bindings for Rust, C++, JavaScript and Python.

- **Runtime under 300 KiB of RAM.** 60 fps on low-resource hardware.
- Range: **Cortex-M0 to quad-core Cortex-A53 and desktop.** The same design is expected to run across that span, which pushes hard toward restraint.
- Current baseline: **1.17 (24 June 2026)**, framed as another step toward desktop-ready. Desktop polish is still catching up to embedded maturity.

## The reactivity model

A **dependency-tracking reactive graph**, not a diffing tree. When a property changes, the runtime finds every expression depending on it and re-evaluates only those. Closer to SolidJS or MobX than to React, and it is why Slint holds a frame budget where a virtual DOM would not.

- **Bindings are the default, callbacks are the exception.** `width: parent.width / 2;` re-evaluates automatically. Reaching for a callback to set it imperatively is fighting the engine.
- **Two-way binding is `<=>`.** The right tool for a widget's value and the model's value, and it removes the usual "who owns this state" problem in form code.
- **`changed` callbacks are a last resort.** Slint's own guidance: use one only when a binding cannot express it, because it forces the binding to evaluate even when nothing reads the property. It turns a lazy graph node eager.
- **Mutating a property inside its own change event is undefined behavior.** Property/callback loops are the classic way to hang a Slint UI.
- **`pure`** marks a callback or function side-effect free, which is what makes it callable from a binding. Inferred for private functions, declared for public ones.

## The Rust boundary

| Slint declaration | Generated Rust |
|---|---|
| `in property <string> name;` | `set_name(&self, v: SharedString)` |
| `out property <int> count;` | `get_count(&self) -> i32` |
| `in-out property <bool> on;` | both |
| `callback accepted(int);` | `on_accepted(impl FnMut(i32))` |
| `global Palette { ... }` | `global::<Palette>(&component)` |
| model-backed `for` | `ModelRc<T>` set from Rust |

**The architecture that holds up:** application state lives in Rust, exposed through `in` properties and a global for theme tokens; the UI emits `callback`s upward. `in-out` is for widget-local state only. That is unidirectional flow by construction – the same shape as iced's Elm loop, in different syntax.

Callbacks capture the component, so capturing a strong handle inside a callback on that same component leaks. Use `slint::Weak` and `upgrade_in_event_loop` from worker threads.

**The honest practitioner note:** the common pattern is globals in `globals.slint`, backend sets properties, UI fires callbacks – and it gets messy as the app grows, with form-heavy screens producing a proliferation of callbacks. Treat the global layer as a deliberately designed API surface, not a dumping ground.

**Globals are not shared between windows.** A multi-window app cannot treat a global as application state. Also: hyphens in `.slint` become underscores in the host language.

## Layout

`VerticalLayout`, `HorizontalLayout`, `GridLayout` (with `row`, `col`, `rowspan`, `colspan`).

Every element carries a **minimum, maximum and preferred size**. `preferred-width` / `preferred-height` set it explicitly; unset, it derives from the largest preferred size among children **whose `x`/`y` are not set**. Setting `x` or `y` removes a child from the layout – the mechanism for overlays, and the accidental cause of "my layout ignores this element".

- **`horizontal-stretch` / `vertical-stretch`** distribute extra space. `0` means do not grow, but **when every sibling is 0 they all stretch equally** – a surprising default worth remembering.
- **Alignment defaults to `stretch`.** `start`, `end`, `center` size each item to its preferred size, clamped to min/max. Reaching for a `Rectangle` spacer usually means alignment was the answer.
- **`spacing`** on the layout, **`padding`** on its box. There is no margin concept, so do not look for one.

**There is no flexbox or CSS grid equivalent** – no `auto-fit`, no `minmax()`, no subgrid, no container queries. Layout is nesting plus stretch factors plus explicit min/preferred/max. Some layout ideas simply do not port.

## Styling and tokens

`std-widgets.slint` ships four styles: **`fluent`**, **`material`**, **`cupertino`**, and **`native`** / **`qt`** (real platform controls when Qt is installed). **The style is chosen at build time, not runtime.**

Two globals are the design-token layer, and custom components should be built against them rather than against literal colors:

- **`Palette`** – semantic colors: `background`, `foreground`, `alternate-background`, `accent-background`, `accent-foreground`, `border`, plus control variants.
- **`StyleMetrics`** – spacing and layout metrics for the current style.

**The limitation, stated plainly: `std-widgets` are not deeply themeable.** There is a long-standing open proposal for real styling capabilities, and the community answer to "how do I theme std-widgets" is still "write your own widgets". The internal architecture is tiered – a `Base` component holds logic, focus and accessibility, a styled component wraps it – but the base tier is not a public API.

**Consequence for a branded app: expect to build a component library.** Not as bad as it sounds, because the component syntax is compact, but it must be planned rather than discovered late. **Define a `global Theme` with color, spacing, radius and font tokens on day one** and build every component against it. A `.slint` file with literal numbers scattered through it is exactly as unmaintainable as a stylesheet with hardcoded pixels.

## Animation and states

The `animate` keyword animates a property whenever it changes: `delay`, `duration`, `iteration-count`, and CSS easing – `linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, `cubic-bezier(a,b,c,d)`. `animate x, y` covers several properties at once.

**`states` is the strongest idea in the language.** A state names a set of property values; `in` and `out` blocks attach animations to entering and leaving:

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

The states are declared in one place, so **the set of visual states a component has is readable at a glance**. This is the closest any toolkit comes to enforcing "name all the states" in the language itself. The `in`/`out` split also makes motion asymmetry natural: `ease-out` on entry, `ease-in` on exit.

Two gaps to design around:

- **No spring primitive.** `cubic-bezier` only, so overshoot must be hand-authored as a bezier exceeding 1. Spring guidance from `motion.md` has to be approximated.
- **No `prefers-reduced-motion` equivalent surfaced.** On a desktop target that is a real accessibility hole. Check whether the system setting is reachable from the host language and wire it manually.
- States and `animate` cannot be referenced from other elements, so a shared transition is duplicated or factored into a component.

## Text rendering – the sharpest gotcha

`font-family`, `font-size`, `font-weight`; `default-font-*` on `Window` cascades. Custom fonts are `.ttf`, `.ttc` or `.otf`, imported in the `.slint` file, which makes the family globally available. Under the hood: **`fontique`** for discovery, **`parley`** for complex layout, plus bitmap and SDF fonts for constrained targets.

**Text quality differs by renderer, and this is a design-level fact:**

| Renderer | Text output |
|---|---|
| **Software** (`fontdue`) | For low-end systems. On desktop it looks **visibly different** from CoreText / DirectWrite / FreeType |
| **FemtoVG** | Outlines to glyph textures **without hinting** – good at large sizes, **blurry at small sizes** |
| **Qt** | Delegates to the platform – identical to other native apps |

Working rules: **bias display and body sizes upward on FemtoVG**, because small text is where the missing hinting shows. **Verify the type scale on the renderer you actually ship**, not in whatever the preview defaults to. On MCU targets prefer SDF or bitmap fonts and accept that fine typographic craft does not survive.

## Images and assets

Reads SVG plus PNG, JPEG, WebP, AVIF, GIF, BMP, ICO, DDS, HDR.

- **SVG is rendered at the size it is used**, so it stays sharp across DPIs. Keep icons as SVG rather than exporting raster sets. Loading an SVG and scaling the element afterwards reintroduces blur.
- **Nine-slice scaling** via `@image-url("...", nine-slice(30 30 30 30))` – how a bitmap with decorative corners stretches without distorting them. SVG has no nine-slice concept, so this is the answer for skinned bitmap chrome.
- `image-fit` (`fill`, `contain`, `cover`, `preserve`) and `image-rendering` (`smooth`, `pixelated`). `pixelated` exists for deliberate pixel art and tiny embedded assets where smoothing turns a 16 px icon to mush.
- Embedded images are **scaled at compile time** to the size actually used. Set the scale factor rather than trusting the source asset.

## Rendering backends

| Backend | Acceleration | Use |
|---|---|---|
| Software | CPU only, partial rendering | MCUs, no-GPU targets |
| FemtoVG (OpenGL) | GPU | Standard desktop and embedded GPU |
| FemtoVG-wgpu | Metal, Vulkan, Direct3D | Modern desktop |
| Qt | Qt's own | Native look and native text |
| DMA2D, framebuffer, linebuffer | Per device | Embedded |

The software renderer has two modes, and the choice is a RAM decision, not a quality one:

- **`SoftwareRenderer::render()`** needs a full frame buffer. Embedded Linux, larger MCUs.
- **`SoftwareRenderer::render_by_line()`** needs one scanline. Small MCUs pushing pixels over SPI. **This is why Slint fits in tens of kilobytes**, and it means effects that sample the whole frame – real blur, backdrop translucency – are impractical.

**On constrained targets design with flat fills, gradients and sharp edges, and treat glass or blur as a desktop-only luxury.** Partial rendering rewards designs where only small regions change per frame, which argues directly against full-screen transitions and ambient background animation.

## The platform abstraction layer

A trait-based layer decouples the core runtime from windowing and rendering, so Slint runs on bare metal with no OS. Porting means implementing two things: feed input events in, push rendered pixels to a frame or line buffer.

## Threading and async

The UI runs on the main thread with a platform event loop (winit on desktop). **Do the minimum on the main thread and delegate real work**, or animations stall.

| Need | API |
|---|---|
| Run a future from a UI callback | `slint::spawn_local(future)` |
| Talk to the UI from a worker thread | `slint::invoke_from_event_loop(closure)` |
| Delay or repeat | `slint::Timer` |

- **A Tokio future will not just work** – it assumes a Tokio runtime, which the Slint loop is not. Run Tokio on its own thread and `await` a `tokio::spawn` handle inside the future passed to `spawn_local`.
- **Spawning from another thread** requires `invoke_from_event_loop` first, then starting the local task inside that closure.

**This is the machinery behind every loading, empty and error state.** Long work on the UI thread does not show a spinner slowly, it freezes, and a frozen UI cannot animate its way out. Wire the async pattern before designing the states.

## Focus and keyboard

`FocusScope` is the element that participates in keyboard focus. It takes focus on click (with `focus-on-click`) and via tab traversal, and exposes `key-pressed` / `key-released`.

- **`enabled: false` removes it from focus entirely** – not clickable, not tabbable, not focusable programmatically. That is the correct way to express a disabled control.
- **Key events bubble.** If the focused element's callback does not return `EventResult::accept`, the event walks up. That is how a screen-level shortcut handler is built.
- **Tab order follows declaration order.** No tab-index, which is a good default and a trap when a visually reordered layout no longer matches source order.
- Known gap: `FocusScope` does not distinguish keyboard focus from click focus. Until it does, **choose to show focus rings** rather than risk hiding them from keyboard users.

## Lists – a real constraint

`Model` provides data for `Repeater` and `ListView`. `ModelRc<T>` is the handle used in generated code; `StandardListView` is `ListView` with a default delegate over `StandardListViewItem`.

**Conflicting evidence, and it matters:** the docs state `ListView` virtualizes and gives stable performance with unlimited items. The issue tracker reports flickering when content exceeds one screen, redraw bugs on model change, and a visible-only rendering workaround **requiring uniform item heights**.

**Working position: treat long lists as a risk area and prototype early. Prefer uniform row heights until measured otherwise on the target renderer.** Do not design a mixed-height feed in Slint without proving it first. This constraint should shape the layout rather than be fought.

## Widgets, and what is missing

Provided: `Button`, `CheckBox`, `Switch`, `Slider`, `SpinBox`, `ComboBox`, `LineEdit`, `TextEdit`, `ListView`, `StandardListView`, `StandardTableView`, `TabWidget`, `ScrollView`, `GroupBox`, `ProgressIndicator`, `Spinner`, plus layouts.

`ComboBox` exposes `model`, `current-index` (**−1 when nothing is selected**) and `current-value`. The −1 convention matters: an unset combo box is a real state needing a placeholder, and forgetting it is how "select an option" ends up showing the first option as though chosen.

**Not in the box, and therefore design work:** date and time pickers, search with suggestions, snackbar or toast, sheet with detents, segmented control, navigation stack, carousel, chart, and any empty-state component. Budget for it – and note it is also why a Slint app can look like itself rather than like a default widget set.

## Text input and forms – the weak spot

`LineEdit` and `TextEdit`, with `has-focus`, `placeholder-text`, `enabled`, `read-only`, and `input-type` for passwords.

- **Input validation is an open issue.** With `input-type` set to number, non-numeric characters can still arrive through some input methods.
- **Text input widgets are still not exposed to accessibility.**

**Slint is currently a weaker choice for a form-heavy application than for a display- or control-heavy one.** Validation has to be built in Rust and reflected back. That is a genuine "choose a different tool" signal for a data-entry desktop app, and no obstacle at all for an instrument panel.

## Accessibility

**AccessKit integration** via the winit backend, **always enabled on desktop**. Accessibility frameworks can trigger custom actions such as incrementing a spinbox.

Two documented limitations: **text input widgets are not exposed** (a major missing feature), and there is a reported **performance impact on large list views**.

The principles from `accessibility.md` transfer; the verification methods do not. There is no axe, no Lighthouse, no browser accessibility tree. Verification means the platform's own tools – VoiceOver, Narrator, Orca – against a running binary.

## Multiple windows

Multi-window arrived in 1.7. Declare additional exported components and show them as separate windows. `show_modal()` takes `WindowModality::Window()` or `WindowModality::Application`. `PopupWindow` covers lighter cases. Remember globals are not shared between windows.

## Tooling – and why it matters here

- **Live Preview**: tweak colors, animation, geometry and text instantly; change component and global properties in real time without recompiling. **1.13 brought it into the Rust and C++ build cycle**, not just the VS Code extension, plus an interactive outline panel with drag-and-drop reordering and undo/redo.
- **LSP server** with autocomplete, go-to-definition and refactoring, bundled in the VS Code extension.
- **Figma integration**: select elements in Figma, view the corresponding Slint code, paste into Live Preview.
- **Remote viewer** (1.17): edit locally, see it update on target hardware, including phones. For embedded work this means evaluating a design on the actual display, at the actual size, under the actual renderer – which closes the most dangerous verification gap in the toolkit.

**Live Preview is what makes the three-variant protocol workable in Slint.** Three `.slint` files swapped live, rather than three rebuilds.

## Testing

`i-slint-backend-testing` provides UI-testing APIs, including for **CI with no display**.

- **Screenshot testing**: `.slint` files in `tests/screenshots/cases` rendered and compared against references. SoftwareRenderer only at present.
- **Animations use mocked time**, so motion is deterministic under test. A better animation-testing story than web or native offers.
- **The testing backend embeds an MCP server** that lets an agent inspect and drive a running Slint application – explore the UI tree, screenshot, click, drag, type.

That last point changes what this skill can do on a Slint project: there is a **programmatic path to driving and screenshotting the running UI**, which turns "verify rather than assert" into something executable.

## Performance

`SLINT_DEBUG_PERFORMANCE` reports frame rate, with an option to measure only on change. RenderDoc captures GPU commands. **Minimize commands per frame** – draw-call count is the lever.

The design-time levers: set the image scale factor, keep the reactive graph shallow (a binding depending on ten properties re-evaluates when any changes), avoid `changed` callbacks, use a real `Model` rather than a re-created array, and avoid frame-sampling effects.

## Platform reach and licensing

**Desktop (Windows, macOS, Linux) and embedded are the mature targets. Android and iOS support exists but is described by the project as initial.** Web via WASM. Bindings for Rust, C++, JavaScript and Python.

| License | Covers | Cost |
|---|---|---|
| **Royalty-free 2.0** | Proprietary **desktop, mobile and web** apps | Free, requires Slint attribution |
| **GPLv3** | Anything, including embedded | Free, copyleft |
| **Commercial** | Anything, including embedded, no attribution | Paid, includes support |

**Embedded is explicitly excluded from the royalty-free license.** A product shipping Slint on a device is either GPLv3 or paying. That is the single most important licensing fact for anyone evaluating Slint for hardware, and it belongs in the decision before any design work starts.

## Slint vs Qt/QML

Slint is a leaner QML-inspired declarative approach, compiled to native Rust or C++ rather than interpreted. Qt is 25+ years old with far more automotive acceptance and broader platform support.

Practitioner reports describe Slint replacing Qt QML HMIs with fewer bugs, better performance and faster UI design. **Treat pre-2026 assessments of Slint's desktop maturity as out of date** – 1.15 through 1.17 were an explicit desktop-readiness push that shipped drag and drop, tray icons, tooltips and multi-window.

---

# The rest of the Rust landscape

## iced – the Elm loop

**State → view → message → update → state.** Four pillars, no widget mutation, no imperative handles. A `struct` for state, an `enum Message` for everything that can happen, `update` to fold messages into state, `view` to render.

**Why this recurs in Rust specifically:** mutable aliasing of widget handles is exactly what the borrow checker refuses. A retained tree of `Rc<RefCell<Widget>>` fights the language. The Elm loop sidesteps it because the only mutation happens in one function owning `&mut self`.

**The design consequence is real:** every UI state must be nameable as a value. Loading, empty, error, partial and success stop being afterthoughts because they have to exist in the state type before `view` compiles. That is the best design-quality argument for Rust GUI, and worth saying to anyone asking why an unfamiliar toolkit is worth it.

0.14 added reactive rendering and time-travel debugging – the Elm architecture's classic payoff, because state is a value and messages are a log.

## egui – what immediate mode costs and buys

Lays out and paints the entire UI every frame. No callbacks, no click handlers, no synchronization between application state and widget state, because there is no widget state. For tools, debug overlays and highly interactive applications that removes a whole class of bug.

**The cost is layout, and it is structural.** egui is **single-pass**, and layout has a cyclical dependency between position, size and content:

- Sizing a container to dynamically sized children means measuring on one frame and using it on the next, which shows as a **one-frame flicker** when an element first appears.
- Centering or right-aligning a group of unknown width has the same problem. Atomic widgets (a button, a label) are fine because egui knows their size in advance.
- Genuinely responsive layout is where immediate mode fights hardest. The upstream layout tracking issue has been open for years.

Performance is fine if you design for it: full layout every frame means layout code must be cheap, so avoid enormous scroll areas and deep nesting.

**Right for dense, functional, tool-shaped UI where the layout is essentially a grid. Wrong for a marketing-quality, fluidly responsive product UI.** That is the immediate-mode trade, not a criticism.

egui was **the first pure-Rust GUI toolkit, and the first immediate-mode GUI in any language, to implement platform accessibility APIs** via AccessKit – enabled by default in `eframe`, currently Windows and macOS.

## GPUI – the Zed approach

**Hybrid immediate and retained mode**, GPU-accelerated, targeting **120 fps**. Views build a tree of elements styled with a **Tailwind-shaped API** (`.flex().gap_2().bg(...)`), then hand it to GPUI to rasterize.

- The Tailwind vocabulary means **web design knowledge transfers almost directly** – flex, gap, padding scale, color tokens.
- Elements have total control over their own layout and rendering, which is how Zed does efficient views into enormous lists and custom editor layout.

Pre-1.0 with frequent breaking changes, developed as part of Zed rather than as a product. Strong for one ambitious desktop app, risky for anything needing stability. `gpui-component` provides a component set.

## Dioxus – React shape, Rust body

0.7 is the reference version: sub-second hot reload including experimental Rust hot-patching, one component tree and a signals state model shipping to web, macOS, Linux, Windows, Android and iOS from one CLI.

- **A first-party component library modeled on Radix Primitives**: 28 foundational components, shipped **unstyled**, with keyboard shortcuts and ARIA built in. Unstyled primitives plus your own tokens is the model that made shadcn/ui work, and it is the most design-friendly starting point in Rust GUI.
- **Dioxus Native**, a WGPU-based HTML/CSS renderer, so the same markup runs without a WebView.

## Xilem and Masonry – the shared foundation

**Xilem** is a reactive layer inspired by React, SwiftUI and Elm, on top of **Masonry**, a widget toolkit developed alongside it. Application state is an ordinary `'static` Rust type. **Both are alpha.**

The layers underneath matter far beyond Xilem:

| Layer | Crate | Also used by |
|---|---|---|
| 2D rendering | **Vello** (GPU compute-centric) | A growing set of Rust UI projects |
| Text layout | **Parley** | Multiple toolkits, including Slint |
| Accessibility | **AccessKit** | egui, Slint, FLTK |
| Windowing | **winit** | Almost everything |

**When evaluating any Rust toolkit, ask which of these it uses.** A project on Vello, Parley and AccessKit inherits work from a much larger pool than one with a bespoke stack. Track Xilem; do not ship on it yet.

## Text rendering in Rust GUI

`cosmic-text` is the common layer: shaping via HarfRust, rendering via **swash**, custom layout in safe Rust with bidirectional text. Font fallback reuses the static fallback lists from Chromium and Firefox, with full support on Linux, macOS and Windows.

**swash provides horizontal subpixel rendering and fractional positioning**, plus ligatures and color emoji. Those two are precisely what separates native-looking text from slightly-wrong text: without fractional positioning, glyph advances snap to whole pixels and word spacing becomes visibly uneven at small sizes.

- Compare against a native control **at 11–13 pt**, which is where differences show.
- **Complex scripts depend entirely on the shaping layer.** Verify Arabic, Devanagari and Thai early; a shaping gap is not something you can design around.
- Emoji rendering is a real gap in some stacks and shows up as tofu in user-generated content.

## Window scale factors

Any toolkit on `winit` inherits its DPI model, and getting it wrong is the most common reason a Rust GUI looks subtly worse than a native app.

- **Physical pixels** are screen pixels; **logical pixels** are physical divided by the scale factor. Author layout in logical, render in physical.
- `window.scale_factor()` gives the current value, and **`ScaleFactorChanged` fires when the window moves to a different monitor or display settings change.** Ignoring it produces an app that is crisp on the laptop and blurry on the external monitor.
- **Scale factors are not always integers.** Wayland reports integers; X11 derives from XRandR millimeters and produces values like 1.0833333333333333. macOS and Windows report fractional factors too.

Never assume 1x or 2x. Never bake a raster asset at a fixed pixel size when SVG is available. **Snap borders and hairlines to physical pixels** or a 1 px divider becomes a 1.08 px blur. Re-run any glyph atlas or cached raster when the scale factor changes.

---

# Non-Rust cross-platform

## Compose Multiplatform

Realistic sharing is **80–90% of UI code** across Android and iOS. Production-ready: core components, Material 3 theming, resources, state, animation, and navigation via Navigation 3 with type-safe arguments.

Navigation shape is driven by `WindowSizeClass`, never hardcoded widths: bottom bar on compact, rail on medium, drawer on expanded and desktop.

**The honest caveat: sharing UI is not sharing design.** An iOS user reading a Material bottom bar still reads it as an Android app. Right when the product's identity is its own brand; wrong when the goal is to disappear into each platform.

## Flutter

Material 3 has been the default since Flutter 3.16.

- Build the scheme with `ColorScheme.fromSeed`. **Never hardcode a color in a widget.**
- Read typography from `Theme.of(context).textTheme`, not inline `TextStyle` literals.
- Put brand tokens Material has no slot for into a **`ThemeExtension`** – the sanctioned escape hatch that keeps custom tokens theme-aware in light and dark.
- **Define a spacing scale and use it.** Flutter gives no spacing system, so its absence is the most common source of visual drift.
- Keep a gallery screen rendering every component in isolation. Cheapest possible design-system regression test.

Flutter does not yet have M3 Expressive widgets.

## React Native

Stable and default since 0.76; the old bridge is **permanently disabled as of 0.82**. **JSI** replaces the JSON bridge with synchronous calls, **Fabric** replaces the renderer, **TurboModules** replace native modules.

Reported effects vary by source – treat as directional: cold start down ~43%, render speed up ~39%, memory down ~26%. More conservatively, 10–30% UI-thread improvement, up to 3x on cross-thread calls for native-module-heavy apps.

**The design consequence is the point.** The old asynchronous bridge is why React Native apps felt slightly off during gestures and scrolling – the animation could not respond to touch within a frame. Removing it makes **gesture-driven interruptible animation** achievable. If a team's memory of React Native is "it never quite felt right", that memory is about an architecture that no longer exists.

Ecosystem baseline: React Navigation 7.2+, Reanimated 3.5.1+, Gesture Handler 2.16.2+, Expo SDK 52+.

## Tauri v2 on mobile

Desktop and mobile from one codebase: the frontend runs in the system WebView (WKWebView, Android WebView), Rust handles native capability through plugins – camera, notifications, geolocation, biometrics, haptics, sharing.

**The honest framing, from the project's own community: Tauri is desktop-first with mobile reach, not a React Native or Flutter competitor.** A Tauri mobile app looks and feels like a WebView app unless designed deliberately not to.

If it is the right call anyway – and for a Rust team with an existing web frontend it often is – the design work is explicit: rebuild navigation for the platform (top nav becomes a bottom tab bar, hover becomes sheets and long presses), honor safe areas with `env(safe-area-inset-*)` and `viewport-fit=cover`, use the platform type scale, match platform scroll physics, wire real haptics through the plugin rather than the web Vibration API, and disable selection and callouts on chrome while keeping them on content.

**The remaining gap no CSS closes: gesture handling and transition interruptibility.** A WebView cannot participate in the platform's interactive back gesture the way a native view does. Accept it, or use a native toolkit for the parts where it shows.
