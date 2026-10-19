# Process

How to find what is wrong, how to say it, how to verify it, and how to keep it consistent past one screen.

## Heuristic evaluation, adapted for mobile

Experts compare the interface against a list of principles and record where it violates them. Cheap, fast, and it finds a large share of problems before a single user session. **Three to five evaluators is the established number** – one person misses too much, more than five finds almost nothing new.

The caveat from the research: **desktop-centric heuristic checklists do not adequately detect mobile-specific problems.** Nielsen's ten still apply, supplemented with the mobile set:

| Mobile heuristic | What a violation looks like |
|---|---|
| Reachability | Primary actions in the top corners on a phone |
| Target size | Anything under 44 pt / 48 dp, or targets closer than 8 dp |
| Interruptibility | An animation or flow that cannot be caught, canceled or resumed |
| Platform conventions | Android back unhandled, iOS swipe-back disabled, wrong control for the platform |
| Input economy | Wrong keyboard type, no autofill, no autocapitalization rules |
| Connectivity | No offline state, no retry, modal error over a recovering request |
| Permission etiquette | Any request at launch, or without a stated reason |
| Glanceability | Key information not readable in a two-second look |
| Text scaling | Layout breaks at the largest Dynamic Type size |
| Orientation and resize | State lost, layout broken, or rotation blocked without cause |
| Battery and heat | Continuous animation, polling, or inference with no cancel |
| Notification restraint | High frequency, low relevance, sensitive content in previews |

## Structured critique

Frameworks that hold up, and when to use each:

- **"I like / I wish / What if"** – early concepts, mixed-seniority rooms. Separates observation from prescription and gets non-designers participating.
- **The 3Cs: context, content, composition** – a single screen. Ask what the user arrives with, what the screen must say, and only then how it is arranged. **Most bad critique starts at composition and never reaches context.**
- **Nielsen's ten heuristics** – an audit of an existing product where the goal is a defensible list rather than a conversation.

**Feedback quality has a testable standard: specific, justified, actionable.**

> "The hierarchy is off" fails all three. "The price and the product name are the same weight, so the eye has no entry point; try dropping the name to the body style" passes all three.

**Match the critique depth to the design's maturity.** A rough concept gets critiqued on assumptions, flow and product risk. A high-fidelity mockup gets critiqued on clarity, states, accessibility and implementation detail. **Critiquing corner radii on a concept wastes the session; critiquing the premise on a build-ready mockup wastes the work.**

## Hierarchy on a small screen

The mechanics compress on a phone, and the compression is what makes hierarchy harder rather than easier.

- **One focal point per screen section.** Two only if they are far enough apart not to compete. A screen with four things shouting has no hierarchy, only noise.
- **Assign every element a tier** – primary, secondary, tertiary – before styling anything. If two land in the same tier next to each other, one of them is wrong.
- **Size and spacing carry more of the load than on desktop**, because there is no room for the layout-level moves that do the work on a large screen.
- **Reserve one accent color for the primary action.** A screen with three accent-colored controls has no primary action.
- **The bottom third is the strongest position**, not the top, because that is where the thumb is. This inverts the desktop instinct.
- **Contrast is the cheapest hierarchy tool on a small screen** – weight, size, color and containment, in that order of reliability.

## Cognitive load

**Hick's law:** decision time grows logarithmically with the number of choices. It has two honest limits: it applies to unfamiliar, unordered choices, and **grouping defeats it**. Twenty options in four labeled groups is not the same decision as twenty options in a list.

- **Progressive disclosure is the primary tool.** Reveal options as they become relevant.
- **Visual hierarchy is a partial substitute for removing choices.** If a primary action is clearly primary, the others cost less attention even though they are present.
- **Consistency lowers load for free.** Same control, same place, same behavior means the user decides once.
- **Defaults do more than any amount of explanation.** A well-chosen default converts a decision into a confirmation.

**The counterweight: reducing choices past the point of capability is not a win. The goal is fewer *decisions*, not fewer *features*.**

## First impressions

**The window is measured in milliseconds.** Research puts aesthetic judgment formation at **17–50 ms**, and finds those judgments are **sticky** – longer examination rarely overturns the first impression. Aesthetic treatment measurably changes the judged **credibility** of identical content, and roughly **94% of first impressions are attributed to design rather than content or function.**

For a native app this lands on three surfaces in order: **the icon** in search results, **the screenshots** on the store page, and **the first screen after launch**. Each is judged before anything the product does is experienced.

**The uncomfortable implication, stated honestly: visual appeal beats usability for first impressions.** That is not permission to build something pretty and broken – usability determines whether they stay – but it does mean an app with excellent interaction design and amateur visual craft loses users who never find out.

## The metrics a designer should hold

**Performance floors:**

| Metric | Bar |
|---|---|
| **Cold start** | **under 2.5 s at p90** |
| **Tap response** | **under 100 ms** |
| **Crash-free sessions** | **over 99.5%** |

> **One crash in a user's first session multiplies their churn rate by about 3×.**

**UX signals that can be detected automatically and are design problems, not engineering ones:**

- **Rage taps – 4+ taps in one second on the same element.** Almost always means an affordance is lying: something looks tappable and is not, or is tappable and gives no feedback.
- **UI freeze – no response to input for 2+ seconds.**
- Task completion rate by screen; time to first meaningful action; drop-off per onboarding step; conversion per paywall view.
- **Crash-free session rate and ANR rate are design metrics too:** an app that dies is an app whose design is never experienced.

**And the single strongest retention lever in the whole study:**

> **Reaching a real action inside 60 seconds roughly doubles day-7 retention versus a 3-minute onboarding.**

That is the argument to use when someone wants to add another onboarding screen.

**Measurement patience:** retention changes usually need **3–6 weeks** to show clearly, because you need cohort data over time. **A design change judged after a week has not been judged.**

## Finding out what is actually wrong

Heuristics and critique find problems from the inside. These find them from the outside.

**Behavioral, and cheap:**

- **Session replay and touch heatmaps.** Mobile-specific tools record sessions and aggregate taps per screen. What they reveal that analytics cannot: **rage taps**, **dead taps** on things that look tappable, scroll depth on long screens, and where a flow is abandoned mid-screen rather than between screens.
- **Funnel analytics per onboarding step**, because each screen is a discrete drop-off point.

**Attitudinal, and slower:**

- **Diary studies** are the method mobile most needs and gets least. Participants self-document over **one to four weeks**, in context, when the thought is fresh. The only practical way to see episodic behavior, how use changes with familiarity, and where the product fits in a day. Five phases: plan, recruit and onboard, monitor, debrief, analyze.
- **Remote moderated testing** by having the participant mirror or share their phone screen to a second device. Awkward, and still the fastest way to watch someone use the thing.
- **Unmoderated remote testing** for specific tasks at volume.
- **Tap testing / first-click testing** on a static screen, which answers "is the primary action findable" in minutes.

**The pairing that works: behavioral data tells you where the problem is, qualitative work tells you why.** A heatmap showing rage taps on a non-interactive element is the finding; five minutes watching someone do it explains what they expected instead.

**The trap:** session replay and heatmaps carry real privacy obligations on mobile. **Mask text input, exclude sensitive screens, and declare the tooling in the privacy label.** A design research tool that leaks user data is not a trade worth making.

## Experimentation is harder than on the web

Two structural constraints with no web equivalent:

- **Store approval delays.** A variant is not a deploy, it is a submission.
- **Offline users.** Assignment and exposure logging cannot assume connectivity.

Hence the 2026 shift to **feature-flag-first platforms** (Statsig, LaunchDarkly, Amplitude, Firebase) rather than classic web A/B tools, and **remote configuration for high-churn surfaces** – paywalls especially, where variants ship without resubmission.

**Anything you expect to iterate on should be remotely configurable from the start** – paywall, onboarding copy, empty-state CTAs, notification timing. Baking those into the binary means a two-week loop per change, which in practice means they never get iterated at all.

## The device matrix

- **8–12 device and OS combinations covers 90%+ of user traffic.**
- Minimum viable spread: **three Android manufacturers (Samsung, Xiaomi, Pixel), two recent iOS versions, two screen sizes per platform.**
- Build it **from your own analytics** – top devices, OS versions, regions – and **review it regularly**.
- **Hybrid strategy: emulators for speed, real devices for high-risk flows and release confidence.** Emulators cannot replicate thermal throttling, GPS drift, sensor latency or manufacturer skins.

**The design must be verified on a mid-range Android**, because that is where jank, font rendering and thermal behavior actually show. **A design signed off on the newest iPhone has been signed off on the best case.**

**Device farms** (BrowserStack, AWS Device Farm, Firebase Test Lab and similar) give access to thousands of real devices and, importantly, to what emulators cannot do: **biometrics, real GPU behavior, real thermal throttling, manufacturer skins.** They are how the device matrix becomes a CI job rather than a wish.

Add these as explicit *configurations*, not just devices: **largest text size, an RTL locale, a long-translation locale, dark mode, and Reduce Motion.**

## The tooling loop

**Previews are the fastest design surface on native.** SwiftUI's `#Preview` renders a view live in Xcode with dynamic refresh on code, asset and localization changes; Compose's `@Preview` does the same in Android Studio. Apple's own developer survey figure is **up to 50% faster interface iteration**.

**The part most teams miss: previews are a matrix, not a single render.** Declare previews for light and dark, the largest text size, an RTL locale, a long string and two device widths, and every one of those is checked continuously rather than at review time.

**Visual regression testing** captures a reference image and compares later renders. On mobile the standard tools are **swift-snapshot-testing** and `iOSSnapshotTestCase` on iOS, **Paparazzi** or **Shot** on Android, both with Compose support.

What makes it worth doing for design rather than only engineering: **render every notable state across every environment** – the same matrix as the previews. That set is exactly what a designer cannot check by hand and what breaks silently.

The caveat every source raises: **pixel-exact comparison produces too many false positives** across device configurations and OS versions. Use a perceptual diff with a tolerance, run on a fixed simulator or a rendering library rather than real devices, and **treat baselines as reviewed artifacts** – a changed baseline gets looked at by a person, not rubber-stamped.

**This is also what makes a design system enforceable.** A token change that alters 40 components shows up as 40 image diffs, which is either confirmation or a caught mistake.

**Design-to-code tools** in 2026 are real but partial. Figma Make generates web-shaped prototypes; tools like Locofy target React, React Native and Flutter; a few generate native iOS directly. The consistent caveat across every source: **output needs human review for accessibility, semantics and production readiness.** They compress the first draft, not the last mile.

**The loop that works:** prototype in previews, snapshot-test the matrix in CI, run the real-device pass on the shortlist before release, and use session replay to find what all three missed.

## Handoff

The declarative frameworks map unusually cleanly from a well-structured design file: **sections → views, rows → stacks, cards → reusable components, tokens → colors and modifiers.**

The honest boundary: Figma screens can become SwiftUI view structure, styling, text and assets – **engineers still need to add state, navigation, data, accessibility and testing.**

**That list is the point. The generated part is the cheap part.** State, navigation, accessibility and testing are where a native app is actually designed, and none of them are visible in a mockup.

**So specify them explicitly, because the file cannot carry them:**

- Focus order and announced labels for every screen.
- Dynamic Type behavior – what wraps, what truncates, what reflows.
- Back-stack semantics, including per-tab stack preservation.
- All five loading, empty, error, partial and success states, plus the offline set where relevant.
- Motion: which spring, which duration token, what is interruptible.
- Which values are tokens and which are one-off deviations, and why.

## Design systems at scale

Token adoption is now the default rather than the exception, and the **W3C-track token format** backed by Adobe, Google, Meta and Figma means one token file can drive Figma, CSS, iOS and Android without a hand-built translation layer.

The structure that survives contact with multiple platforms:

| Tier | Example | Platform-specific? |
|---|---|---|
| Primitive | `blue-600: #1B62E8` | No |
| Semantic | `color.action.primary` → `blue-600` | No |
| Component | `button.primary.background` | Rarely |
| **Platform override** | Elevation on Android, no shadow on iOS | **Yes, deliberately** |

**The override tier is the part teams get wrong.** A cross-platform design system should **encode where platforms are allowed to diverge**, not pretend they do not. Navigation shape, motion curves, elevation treatment, control appearance and typography scale are the usual legitimate divergences; color, spacing, iconography and copy are usually not.

**The honest observation from every source: naming, structure, governance and adoption are the hard parts, not the tooling.** A token system nobody adopts is worse than no system, because it splits the truth in two.

### Documenting and versioning

- **Semantic versioning** (major.minor.patch): major means a breaking change, minor and patch mean additions and fixes.
- **The failure is not the version number, it is the migration.** A major release with no migration path is a delayed incident. Every breaking change ships with a note saying **what changed, who has to act, what can wait**, and before-and-after examples for any rename or pattern shift.
- **Write for all three audiences** – designers, iOS, Android. Documentation speaking to only one loses adoption immediately.
- **Document the decision, not just the component.** "Use a snackbar when the action is undoable, an alert when a decision is required" is the part that gets reused; the prop list is what the IDE already provides.
- **Communication is a system function.** Announce changes proactively, collect feedback, and treat adoption as a metric rather than an assumption.
- **Document both platform variants side by side.** A component page showing only the iOS rendering is how an Android implementation quietly drifts.

## Consistency at scale

- **Tokens and components are the mechanism.** Change one value and it updates everywhere, which is the only way consistency survives a growing team.
- **Decisions, not just components, need to be shared.** Two designers with the same button library will still produce different screens if they disagree about when to use a sheet.
- **The scaling failures are predictable:** spacing drift (because spacing is the token teams skip), one-off components created under deadline, screens that never got a dark-mode pass, and the fifth engineer's interpretation of a pattern documented only in a Figma file.
- **Visual regression across the whole component set** is what catches drift mechanically, and the only method that scales past what a person can review.

**The check that works at any size: open ten screens in sequence and look only at spacing, type sizes and corner radii.** If they vary, the system is not being used, regardless of what the documentation says.
