# Screen types by category

Each of these is a solved problem with a conventional shape. Deviating is allowed but must be a decision, not an oversight. Component-level behavior is in `patterns.md`.

## Video players

The control set is fixed by expectation: **play/pause, scrub bar, volume, fullscreen toggle, captions, playback speed.** Anything else is secondary and belongs behind a menu.

The mobile shift is that controls hide by default and gestures take over:

| Gesture | Action | Origin |
|---|---|---|
| Single tap anywhere | Toggle controls visibility | Universal |
| **Double tap left/right third** | **Seek back/forward 10 s** | YouTube, now expected everywhere |
| Rotate to landscape | Auto-enter fullscreen | System behavior |
| Swipe up on video | Enter fullscreen | YouTube |
| Swipe down in fullscreen | Exit fullscreen | YouTube |
| Vertical drag, left half | Brightness | MX Player lineage (Android) |
| Vertical drag, right half | Volume | MX Player lineage |

**The center third of a double-tap zone must stay neutral**, or a user tapping to show controls seeks by accident.

- Scrub bars need **chapter markers** when chapters exist, and a preview thumbnail on drag when bandwidth allows.
- **On a phone the scrub bar is the hardest target on the screen.** Give it a touch slop of at least 44 pt vertically even if the drawn track is 4 pt tall.
- **Auto-hide at 3 s of inactivity**, and **never while the user's finger is down.**

## Camera

Components, in the order they are looked for: viewfinder, shutter, mode selector, flash, front/rear switch, last-capture thumbnail.

- **The viewfinder is the interface.** Every chrome element competes with the subject, so chrome floats over the preview with a scrim only where text sits.
- **The shutter is bottom center, large, circular, high contrast.** It is the only control that never moves between modes.
- **Mode selection is a swipeable strip above the shutter, and swiping the viewfinder changes mode too.** Both, not one.
- **Aspect ratio changes letterbox the preview** rather than cropping silently, so the user sees what is being discarded.
- Grid overlay lines are thin and low opacity – visible without competing.
- **Tap to focus needs a visible reticle plus an exposure slider attached to it**, fading after about 2 s.
- **The capture confirmation is haptic plus a shutter animation, not a modal.** Latency from tap to haptic is the entire perceived quality of the app.
- **Zoom:** pinch works, but discrete 0.5x / 1x / 2x buttons are what people actually use, because they are repeatable.

## Maps

The map is the whole screen. Everything else is an overlay, and the dominant overlay is the **non-modal bottom sheet**.

Google Maps is the reference: a non-modal sheet shows place details and actions while the map stays pannable behind it. **Non-modal is correct here because the map is context for the sheet's content. A modal sheet over a map is almost always wrong.**

- Sheet detents typically run three-stop: **peek** (name plus primary action), **medium** (details), **full** (reviews, photos, hours). The peek stop must clear the home indicator and still leave the pin visible above it.
- **The recenter button floats above the sheet's top edge and moves with the sheet**, never under it.
- **The user's own location marker and the selected pin need distinguishable shapes, not just colors.**
- **Attribution and legal text are required by every tile provider** and must remain visible at the smallest detent.
- **Gesture conflict:** a vertical drag near the sheet edge is ambiguous between panning the map and resizing the sheet. Give the sheet a grabber and a hit area, and let map drags win everywhere else.

## Chat and messaging

Element list: input field, send button, bubbles with sender identity, timestamps, avatars or presence, typing indicator, read receipts, and a failed-send state with retry.

What actually decides quality:

- **The keyboard relationship.** The message list is anchored to the bottom and must translate with the keyboard **in the same frame it animates**, not after. Anything else reads as a stutter every time the user types.
- **Bubble contrast over long sessions.** A chat is read for minutes, not seconds. **Low-contrast gray-on-gray incoming bubbles are the single most common failure.**
- **Growing input.** The composer expands to a cap (commonly 4–6 lines) then scrolls internally. It never pushes the send button off-screen.
- **Failed messages stay in place with a retry affordance.** Silently dropping a message is worse than showing it failed.
- **Auto-scroll rule: scroll to bottom on a new message only if the user was already at the bottom.** Otherwise show a "new messages" pill. Yanking the view is the second most common failure.

For AI and chatbot surfaces, the four patterns in every working implementation: **structured formatting that chunks long responses, a typing or streaming indicator, quick-reply chips that scope an ambiguous query, and a graceful fallback when the system cannot answer.**

## Feeds

**Infinite scroll is right when content is homogeneous, discovery is the goal, and items are consumed rather than compared.** It is wrong when the user needs to find one specific thing, compare options, or reach a footer.

**The costs are documented in research, not opinion:** infinite scroll is repeatedly identified as a primary driver of mindless scrolling, with reported anxiety, fatigue and guilt across age groups. Regulators now treat some feed designs as dark patterns, and the EU is naming "addictive design" as a category. See `shipping.md`.

**The middle option that usually wins:** load a page automatically, then require a "Load More" tap. Keeps the speed and returns control plus a reachable footer.

Whichever is chosen:

- **Scroll position must be restored when returning from a detail view.** Losing position in a feed is the highest-severity feed bug there is.
- **Item height must be known or reserved before content loads**, or the feed jumps.
- **A feed needs a genuine end state.** "You are all caught up" is a feature.

## Finance and banking

The design constraint is trust, and trust here is a specific, buildable thing: **make security visible, make behavior predictable, remove ambiguity.**

- **Show transaction confirmations with the exact amount, recipient and account, on their own screen, before commit.** Never inside a toast.
- **Name the security state in plain language.** "Signed in with Face ID" beats a padlock glyph.
- **Balances need a hide toggle**, defaulting to respect shared contexts – screen sharing, recording, external display.
- **Destructive or irreversible money movement gets a confirmation that requires reading**, not a double tap in the same place.
- **Pending versus settled must be visually distinct.** The entire category of "where is my money" support tickets comes from that one distinction.

The 2026 direction is **passive security**: biometric and behavioral checks that stay invisible until risk rises, replacing constant interruption. Users should feel safe without being reminded that risk exists.

## Health and fitness

**One takeaway per chart.** Health data tempts multi-series overlays and they fail on a phone.

- Line for continuous signals (heart rate, weight), bar for discrete daily totals (steps, sessions), stacked bar or ring for composition. Pie only for 2–3 slices.
- **Every visualization should imply a next action.** A sleep chart that suggests nothing is decoration.
- **Timeframe switching (day/week/month/year) is the primary interaction** and belongs as a segmented control at the top, not buried.
- Dark backgrounds with one bright accent read well for glanceable metrics and are the category convention, but **do not force dark on a category people also use outdoors**.
- **Health data is sensitive:** same shared-context hiding as finance, and never in a notification preview by default.

## Charts on small screens

| Desktop habit | Mobile correction |
|---|---|
| Chart title above | The title is the screen title. Drop it |
| Full axis labels | First, last and one middle tick |
| Legend below | Direct labels on the series, or drop to one series |
| Hover tooltip | Tap and hold with a persistent readout |
| Multi-series overlay | Small multiples, or a series switcher |
| Truncated y-axis to show variation | Almost always misleading. Start at zero for bars |

**Adapt, do not shrink.** A desktop chart scaled down is the single most common mobile dataviz failure. Research comparing animation against small multiples for trend visualization on phones supports **splitting into small multiples over animating one chart through time**.

Interactions must be finger-sized: a data point needs padding well beyond the drawn dot, and **swiping between periods should work**, since it is the natural gesture on a time series.

## Dense data and tables

**A table scaled down is not a mobile table.** The desktop table has to be restructured, and there are only a few structures that work:

| Pattern | When | Cost |
|---|---|---|
| **List-detail** | The row has one identity and many attributes | Two taps to compare two rows |
| Card per row | 3–5 fields matter, the rest are secondary | Vertical space, no column scanning |
| Frozen first column, horizontal scroll | Genuine comparison across many columns | Discoverability of hidden columns |
| Column picker | Power users, variable needs | Extra configuration surface |
| Transposed, one row per screen | Very wide, few rows | Loses the overview entirely |

**List-detail is the default answer**, because it is an established mobile pattern rather than a table workaround.

- **Prioritize columns explicitly.** There is a primary field (the identity), one or two supporting fields, and everything else is detail. Cramming every field into the default view is what makes dense tables unreadable.
- **Right-align numbers, use tabular figures**, keep decimal places consistent down a column.
- **Status is a badge with a word, never a color alone.**
- Row targets are 44 pt minimum even when the text is small.
- **Inline editing needs an explicit commit or a visible saved state**, because a tap outside is ambiguous between commit and cancel.
- **Sort and filter are the two controls that actually get used.** Put them in reach; everything else behind a menu.
- Long tables need incremental loading, with the loaded-row count visible so the user knows the list is not the whole set.

## Commerce and checkout

Mobile web converts at roughly **1.53% against desktop 3.36%**, and mobile web cart abandonment is reported around **97%**. Dedicated apps are where the gap closes (5–8% for well-built DTC apps). Those numbers are the argument for an app, and also the argument that **checkout is where the app earns its existence**. Directional.

- **Wallet buttons (Apple Pay, Google Pay) at the top of checkout, not the bottom.** One tap plus biometric is the whole flow.
- **Guest checkout as the primary path.** Around **24% abandon at forced registration.** Invite account creation after the purchase.
- **One page, or at most two steps.** One-page checkout is reported to cut abandonment by roughly 20% by removing progress anxiety and back-button loss.
- **Autofill everything the platform offers.** Address autofill and card scanning are native capabilities that beat any custom form.
- **Payment method diversity is worth roughly 12–15%**, including buy-now-pay-later where the audience expects it.
- **Show the total including shipping and tax as early as the cart.** Surprise cost at the last step is the top stated abandonment reason across every survey in this space.

## Audio and now-playing

Audio is the category where most of the app runs outside the app.

- **Lock screen and Control Center / notification media controls are the primary interface**, not extras. iOS: `MPNowPlayingInfoCenter` plus `MPRemoteCommandCenter`. Android: a `MediaSession` with a media-style notification.
- **Populate artwork, title, artist, duration and elapsed time.** A media session without artwork looks broken on the lock screen and in Android's media carousel.
- **Support the remote commands the hardware sends:** play, pause, next, previous, seek, and skip intervals for spoken content – podcasts use 15/30 s, music uses track skip.
- **Audio focus and interruption handling is a design decision**, not just an API call: decide whether a phone call ducks or pauses, and whether playback resumes afterward. **Getting resume wrong is what makes an audio app feel unreliable.**
- **Background playback plus a persistent mini-player docked above the tab bar** is the expected in-app shape, expanding to the full now-playing screen with a shared-element transition. On iOS 26 the mini player should be a tab bar accessory view.
- Sleep timers, playback speed and skip-silence are podcast table stakes.

## Professional and creative apps

The tablet pro app – Procreate, Affinity Designer, Concepts, Logic – breaks several phone rules deliberately, and the reasons are worth knowing.

- **Chrome is allowed to be dense.** These are used for hours by users who invest in learning them. The 44 pt minimum still holds for anything hit under time pressure, but a tool palette can pack tighter because it is dwelt on, not tapped in passing.
- **The canvas is sacred.** Every panel is dismissible, and the gesture to dismiss everything (Procreate's four-finger tap) is a defining feature, not a shortcut.
- **Modifier gestures replace modifier keys.** Two-finger tap to undo, three-finger tap to redo, three-finger swipe for cut/copy/paste. These are iPadOS-wide conventions and inventing your own is a real cost.
- **Pencil and finger are different inputs.** Pencil draws, finger navigates. Pressure, tilt and azimuth are available, and hover on newer hardware allows a preview state phones do not have.
- **Continuity matters more than novelty.** A pro app is judged on whether a file opened tomorrow is exactly as it was left. Autosave, versioning and crash recovery are design features here.
- **Keyboard and trackpad support is not optional** on iPad. A pro app without shortcuts is a toy to its target user.

**The transferable lesson: density is earned by frequency of use.** The right question is not "is this too dense" but "how often does this user do this".

## Enterprise and field apps

A different user, a different context, and most consumer instincts are wrong here.

**Around 60% of field workers use mobile apps as their primary work interface**, and mobile accounts for over 60% of enterprise application access in construction, logistics, healthcare, retail and field services.

- **Offline is the default state, not the exception.** A field app that requires connectivity fails at the site. Full core functionality offline, visible sync state, a real conflict story.
- **Role-based interfaces.** The technician, the dispatcher and the supervisor see different screens from the same data. Showing everyone everything is the most common enterprise UI failure and why enterprise software gets its reputation.
- **Gloves, sunlight, noise and one hand.** Targets larger than the consumer minimum, contrast above the accessible minimum rather than at it, and no interaction requiring two hands or fine precision.
- **Speed over delight.** The user does this task forty times a shift. Every tap removed is worth more than every animation added. **This is the one category where a dense, information-heavy screen is the right answer.**
- **Scan, do not type.** Barcode, QR, NFC and camera capture beat any keyboard input in the field.
- **Battery is a shift-length constraint.** An app that drains a phone by 2 pm is a broken tool, which makes every background sync, GPS poll and animation an operational decision.
- **Training cost is a design metric.** Enterprise software is deployed to people who did not choose it, so learnability matters more than discoverability and consistency matters more than novelty.

Emerging and worth tracking: **AR overlays through the camera** are moving from experimental to practical in field service, warehousing and manufacturing, where the value is unambiguous – put the instruction on the equipment rather than next to it.

## AI features

New enough that conventions are still forming, but several are already firm.

### Mark AI involvement, consistently

Apple's visual grammar for Apple Intelligence is a **shimmer outline** on the affected region, **deference** (the AI surface does not take over the app's chrome), and **source attribution** when output derives from user content.

**Never use Apple's shimmer for a non-Apple AI integration.** Build a distinct signal. Borrowing the system's AI treatment for your own model implies a provenance you do not have.

**Design three states explicitly, because they are three different screens:**

| State | What it shows |
|---|---|
| Pre-AI | The entry point, and what it will do |
| AI-active | Progress, streaming output, and a cancel |
| AI-resolved | Result, source attribution, and accept / reject / regenerate |

**The resolved state is the one most implementations skip, and it is where trust is won or lost.** Generated content that cannot be rejected in one tap reads as an imposition.

### System integration beats a bolted-on assistant

On iOS, standard text controls get **Writing Tools** (proofread, rewrite friendly/professional/concise, summarize, list-ify, table-ify) for free. Custom text controls must adopt the API. **A custom text view silently lacking Writing Tools while every other app has them is a visible regression to the user and invisible to the team that built it.**

The generalization: **on native, the best AI feature is usually the one the OS already provides, surfaced correctly**, rather than a chat window added to the corner of the app. See `surfaces.md` for App Intents.

### Generative and adaptive UI, with the caveat

The 2026 direction is interfaces assembled at runtime from a modular component system, arranged per user and per intent, with intent-based navigation surfacing the tools the current task needs instead of a fixed menu.

Two consequences follow:

- **The design deliverable shifts from screens to components plus assembly rules.** Every component must be legible in any position, at any size, next to anything else. A much harder constraint than a fixed screen.
- **Predictability is now a feature you have to deliberately preserve.** An interface that rearranges itself destroys the muscle memory that makes an app feel fast to a returning user. **Adaptive placement is safest for content and most dangerous for controls.**

The settled visual convention around AI surfaces in 2026: near-black bases (roughly `#0A0A0A` to `#1A1A2E`) with translucent frosted panels for model output. **It is a convention, not a requirement, and following it makes an app look like every other AI app.**

### On-device inference changes the UX budget

Running locally removes the network round trip, so **latency becomes device-dependent rather than connection-dependent**. That flips the problem: instead of "handle offline" it becomes "handle a three-year-old phone".

- **Measure on the oldest supported device**, not the newest. Model latency varies far more across hardware than network latency varies across connections.
- **Below ~100 ms, no indicator. Up to about a second, an inline indicator. Beyond that, stream partial output**, because streaming converts waiting into reading.
- **Always offer a cancel.** Local inference holds the CPU or NPU, and an uncancellable job on a phone is a thermal and battery problem the user can feel.
- **Say when processing is local.** "Processed on your device" is one of the few privacy claims users understand immediately, and a genuine differentiator.
- Production telemetry catches what lab testing does not: rage taps, freezes and unresponsive gestures around inference.

### The compliance floor

Store review for AI-enabled apps expects **privacy disclosure for any data processing, safeguards around hallucinated user-facing text, and clear labeling of AI-generated content.** Treat labeling as a design requirement with a reserved place in the layout, not a badge added at the end.

## Multimodal – voice with a screen

Voice-only is essentially gone as a design target. Nearly every voice product pairs speech with a screen.

**The division of labor is the principle.** Speech is linear and cannot deliver density; **the screen carries what speech cannot** – maps, lists, comparisons, tables. The screen's job is **not** to transcribe what was just said.

The method: **design the voice flow as if there were no screen, and the visual layer as its support, at the same time.** The flow must complete in both modes, because the user will switch mid-task.

- **Always offer a touch equivalent.** Voice fails in noise, in public, with accents the model handles poorly, and for users who cannot speak clearly.
- **Show the interpretation, not just the result.** Displaying what the system heard is the cheapest error-recovery mechanism there is, and it lets the user correct one word instead of restarting.
- **Non-verbal feedback carries state**: a listening indicator, a haptic on capture, a short earcon on completion.
- **Voice input on a phone is often just dictation into a field**, which is legitimate and underused. It does not need a conversational design.
- **Never lose the user's input to a failed transcription.** Keep the audio or partial text and let them edit.
