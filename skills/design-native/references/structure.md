# Structure

Navigation, adaptive layout, and how the hand reaches the screen. Onboarding is in `shipping.md`.

## The back stack divergence nobody warns you about

> **Android's Navigation Component default is to clear the back stack and lose all UI state when switching between bottom tabs. iOS preserves a separate stack per tab.**

On Android, switching tab and returning shows only the root destination – everything the user had opened is gone. That is the **documented Material default**, and it is worse. Multiple back stacks must be implemented explicitly.

**This is the single highest-impact native design defect in this whole skill**, because it is invisible in a mockup, silent in review, and infuriating in use. **Specify per-tab stack preservation explicitly**, or the platform default will quietly ship.

Other back-stack rules:

- The start destination sits at the bottom; everything else pushes and pops on top.
- Users expect the complete back stack. Skipping past pages on Back confuses them.
- **Back is a history model; Up is a hierarchy model.** They are not the same target and a deep-linked screen needs both answered.

## Choosing the navigation model

| Pattern | Use |
|---|---|
| **Bottom tab bar** | **3–5 primary sections of comparable importance.** Both HIG and Material recommend it for primary navigation |
| **Navigation drawer** | Many destinations that do not all need visibility, or **one dominant task** with utilities behind the menu – Maps, Uber, Gmail |
| **Hub and spoke** | Distinct non-overlapping areas. Central screen, branch out, return |
| **Both** | Legitimate and common: tabs for primary, drawer for the long tail. Spotify and Gmail do this |

**The 2026 shift:** bottom-centric navigation is expanding beyond the tab bar. The primary interaction surface is below the screen's midpoint, and **bottom sheets have become the dominant container for secondary content**.

**The top of a phone screen is becoming display-only.** Anything actionable migrates down. That is thumb-zone research finally reshaping the whole layout convention rather than just the CTA.

### Bottom tab bars, specifically

- **3–5 destinations.** Beyond five the targets cramp and the labels truncate.
- **Pair every icon with a label.** Icon-only tab bars fail recognition for anything but the most conventional icons.
- Make the current tab visually distinct, and make **tapping the current tab scroll to top and reset that stack**. An expected behavior whose absence is felt immediately.
- Reserve the hamburger for secondary or rare items. It is a drawer for the long tail, not primary navigation.
- On iOS 26 the tab bar floats and minimizes on scroll; anything pinned above it becomes an accessory view. See `apple.md`.

## Deep links

**Universal Links (iOS) and App Links (Android)** are the standard. Real HTTPS URLs that open the app when installed and **fall back to the web page when not**, which is why they beat custom URL schemes.

The honest note practitioners give and vendors do not: *it rarely works smoothly.* Email providers break links with tracking wrappers, hosting platforms silently rewrite association files, and the OS decides a domain is untrusted without saying why.

- **The web fallback is not an edge case. Design it properly.** A meaningful share of taps land there.
- **Deferred deep linking** – install, then land on the intended content – is what stops an install-from-link flow feeling broken.
- **The destination must handle arriving with no history.** A deep-linked detail screen needs a sensible Up target, not a dead end. Both platforms can synthesize a parent stack; skipping it is the most common deep-link UX failure.
- **Cold start is the case that breaks.** The link arrives before the app has state, and the common bug is dropping it because the handler ran too early. Test cold, warm and backgrounded separately.
- **Authentication interrupts the link.** Store the destination, authenticate, then continue. Dropping the user on the home screen after login is the second most common failure.
- **Context by source is a real design choice.** A link from a marketing email, a shared link from a friend, and a link from a push notification arrive at the same screen and deserve different framing. A first-time user landing deep needs contextual onboarding there, not the standard first-run flow.
- Type-safe routes make all of this tractable, because the link parses into the value the navigation graph already uses.

## Adaptive layout

### The canonical layouts

Material 3 names three, and they cover most app structures:

| Layout | Structure | For |
|---|---|---|
| **List-detail** | List pane plus detail pane | Messaging, contacts, mail, files, settings – a collection with more behind each item |
| **Supporting pane** | Primary area (~⅔) plus a secondary pane | A main task with reference material, tools or context alongside |
| **Feed** | Multi-column grid of self-contained cards | News, photos, social – equal, independent items |

**How list-detail adapts is the model for all three.** Expanded width: both panes visible, selecting in the list updates the detail in place. Medium and compact: one or the other, selection navigates, back returns.

The pattern names the **responsive behavior**, not just the wide arrangement. That is better specified than most web equivalents.

**List-detail is the strongest of the three** because it uses width for context rather than decoration: the user moves through options without losing their place, which is the actual difference a big screen makes.

### The tablet failures, in order of frequency

1. **A stretched phone layout.** One column at 1,024 pt with 300 pt of dead gutter each side. The single most common tablet failure, and it reads as an app that was not designed for the device.
2. **Line length ignored.** A single column that does stretch produces 140-character lines. Cap the measure and spend the width on a second pane.
3. **Navigation that did not adapt.** A bottom tab bar on a 13-inch iPad needs a whole-hand move. Bottom bar on compact, rail on medium, drawer or persistent sidebar on expanded.
4. **State lost on rotation or resize.** The failure users actually notice.

**Decide what the extra space is *for* before deciding what to put in it.** More content, more context, or fewer navigation steps are the three legitimate answers. "Bigger" is not one.

### Android's adaptive quality tiers

| Tier | Bar |
|---|---|
| **Tier 3 – Adaptive ready** | Runs full screen or full window in multi-window on all devices, **not letterboxed**; critical flows completable |
| **Tier 2 – Adaptive optimized** | Layout optimizations for all sizes and configurations; enhanced external-input support |
| **Tier 1 – Adaptive differentiated** | **A user experience not possible on a small screen** – multitasking, drag and drop, foldable postures |

Two facts that turn this from advice into a requirement:

- **Google Play shows a notice on the store listing when an app is not optimized for large screens.**
- **Targeting API level 37 requires the app to adapt to a variety of display sizes.**

**Tier 1's definition is the useful one for design:** not "works on a tablet" but "does something a phone could not". That is the right bar for whether a large-screen layout is worth building at all.

### Foldables

**Two half-opened postures**, set by fold orientation:

- **Tabletop** – horizontal fold. Bottom half becomes a control surface, top half a display.
- **Book** – vertical fold. Two facing pages.

Controls near the fold are hard to reach and text over it is hard to read. **Treat the hinge as an obstruction for content and a boundary for layout.**

> **The hinge is a design element with its own states, animations and UX patterns, not just a layout trigger.** Posture is a layout trigger; **angle is an animation input.**

Hinge angle is a **continuous** input – the same class of thing as scroll position or drag distance. An interface that responds continuously to fold angle is doing something genuinely native that no other form factor allows.

Compose Adaptive Layouts 1.2 adds `reflow` and `levitate` for outer/inner display transitions.

### iPad multitasking

Stage Manager gives resizable, movable, overlapping windows in workspaces, floating over a blurred Home Screen. **iPadOS 26 removed the four-window cap** and added a proper windowed mode alongside full screen and Stage Manager, plus external-display window management.

**iPad apps gain no new chrome under any of it** – no title bar, no traffic lights. Rounded corners and the three-dot multitasking menu, and that is all.

Two rules follow, and the second is where most iPad apps actually fail:

1. **Every layout decision keys off size classes and available width, never off device identity.** There is no window size an iPad app can assume and no lower bound on how small or odd the proportions get.
2. **State must survive resize.** A window dragged from full width to a third must not lose scroll position, selection or in-progress text. That is a state-restoration requirement wearing a layout costume.

### Orientation

**Support both unless there is a real reason not to.** Locking to portrait is common and mostly unexamined, and it fails users with a mounted device, a keyboard case, or a physical limitation that makes rotating easier than reaching.

The four honest patterns:

1. **Reflow.** Same content, new proportions. Right for reading, lists and forms.
2. **Change density.** Landscape gets a second column or a list-detail split.
3. **Change the layout's intent.** A video player goes full-bleed; a camera moves controls to the short edge.
4. **Lock deliberately**, and say nothing about it. A "please rotate your device" screen is a failure message, not a design.

- **If it did not scroll in portrait, do not make it scroll in landscape.** That inconsistency is disorienting and usually means the layout reflowed rather than adapted.
- **State must survive rotation.**
- **Android now discourages locking orientation on large screens** even for apps that lock on phones, because a locked app in a resizable window is broken by definition.
- **Landscape phone is a short screen**, not just a wide one. Anything anchored to the bottom competes with a keyboard that now occupies proportionally far more of it.

## Touch targets

| Standard | Size | Spacing |
|---|---|---|
| Apple HIG | **44 × 44 pt** | ~10 px between |
| Material | **48 × 48 dp** | **8 dp between, as a separate minimum** |
| WCAG 2.5.8 (AA) | 24 × 24 CSS px | Sub-24 px passes if a 24 px circle centered on the target does not overlap a neighbor's |
| WCAG 2.5.5 (AAA) | 44 × 44 CSS px | – |

- Physical grounding: MIT Touch Lab measured the adult index fingertip at **8–10 mm**, the finger pad at **10–14 mm**, thumb contact around **25 mm**. 44–48 px maps to roughly 9–11 mm.
- Correct sizing is reported to **cut mis-tap errors 60–80% and improve selection speed 30–50%**. Directional.
- **Spacing is a separate requirement from size.** A 48 dp target with zero gap still gets mis-hit.
- **Fitts' Law is not sufficient for fingers.** The 2013 FFitts correction adds a second source of spread tied to the finger's absolute precision, so small targets are worse than plain Fitts predicts.
- Grow the **hit area**, not necessarily the drawn control.

## The thumb zone

Hoober's observation of **1,333 people**:

| Grip | Share |
|---|---|
| **One-handed** | **49%** |
| Cradled | 36% |
| Two-handed | 15% |

**Three zones:** green (bottom-center, no strain), yellow (mid-screen sides, requires a stretch), red (top corners, awkward or impossible).

**The nuance most summaries omit: zone size varies enormously by thumb length.** In one study the large-thumb group reached **over 50%** of the screen while small and medium groups reached about **30%**, across all phone sizes. Designing for your own reach is designing for the largest hands.

**And grip is not static.** Users change how they hold the phone, sometimes every few seconds. So you are not designing for "the one-handed user" as a segment – **you are designing for everyone, some of the time**, which is why thumb-zone placement is a default rather than an accommodation.

- Primary actions go **bottom-center**. This is why bottom tab bars, bottom sheets and bottom-anchored CTAs win on phones.
- **The green zone mirrors for left-handed users**, so never commit a critical control to one bottom *corner*.
- The top of the screen is for information, not action.
- Platform escape hatches exist – iOS Reachability, Motorola's shrink-screen – but relying on them is designing a workaround into the primary path.

## Safe areas and cutouts

Safe areas keep content clear of rounded corners, sensor housings, the Dynamic Island and the home indicator. Layout margins add separation between content and the safe-area edge. **Both are needed.**

In an embedded web view, `env(safe-area-inset-*)` **only resolves to non-zero when `viewport-fit=cover` is set** in the viewport meta. Without it every value is `0px` and the whole mechanism silently does nothing. Use `padding-bottom: max(1rem, env(safe-area-inset-bottom))` so there is sensible padding on devices with no inset.

A fixed bottom bar without the inset sits under the home indicator on every modern iPhone.

## Gestures

- **Discoverability is the central problem: gestures are always hidden.** The more a UI relies on gestures over visible buttons, the greater the possibility for confusion.
- **The best line found on this: creative gestures look impressive in demos but rarely scale. Gesture-driven design works best when it feels boring.** Use the conventions; do not invent.
- **Layering rule:** primary functions get visible controls, secondary get standard gestures, tertiary get long-press. **A gesture is never the only path to a primary action.**
- Always provide a visible alternative. Gestures are inaccessible to many motor-impaired users and invisible to screen reader users.
- **The cross-platform conflict to watch:** left-to-right edge swipe means *back* on iOS and *switch tab* on Android.

### System gesture conflicts

**The system owns the screen edges.** Android's back gesture is an inward swipe from **either** edge; swipe-up from the bottom is home; bottom-corner swipes summon the assistant. iOS reserves the left edge for back and the bottom for home.

The conflict list is predictable, and it is exactly the components that want edge swipes: navigation drawers, carousels, sliders, and **swipe actions on list rows**.

When swiping on the edge starts built-in navigation, the system's back gesture can fire and leave the app in an inconsistent state.

**The escape hatch is `View.setSystemGestureExclusionRects()`** (Android 10+), declaring regions that need the touch instead. **Use it sparingly and deliberately** – the system exists to be predictable, and reclaiming an edge is a cost paid by every gesture the user makes afterwards.

**Any interaction within ~20 dp of a screen edge is contested territory.** The safer design keeps draggable and swipeable surfaces inset, which is also better for the thumb and for hinge proximity on foldables.

## Pointer and keyboard on iPad

An iPad with a Magic Keyboard is a laptop in every way that matters to the user.

**Pointer.** iPadOS adapts the cursor to context and offers three effects for interactive elements: **highlight** (a soft rounded rect behind it), **lift** (the element rises and the pointer disappears into it), and **hover** (the element responds without the pointer morphing). Pointer and target visually merge, which is what makes the interaction feel precise on a device with no fine pointing.

**Missing hover effects make the app feel broken with a trackpad**, because the user cannot tell what is interactive without tapping it.

**Keyboard.** Shortcuts for every frequent action. An app without them forces a hand off the keyboard for every operation, which is the exact friction the keyboard was attached to remove. The Cmd-held shortcut overlay is the iPad's version of a menu bar and should be complete.

Also expected: arrow-key navigation through lists, Tab between fields with a **visible focus ring**, Escape to dismiss, Return to confirm, and the system text-editing shortcuts.

**Touch stays the baseline.** Targets remain 44 pt even with a precise pointer attached, because the same app runs without one five minutes later.
