# Motion, haptics and sound

Frame budget and latency numbers are in `performance.md`. Reduce Motion policy is here; the wider accessibility floor is in `accessibility.md`.

## Interruptibility – the central principle

> **An animation must stay grabbable and reversible at any instant, not just at rest.**

An interface is fluid when it behaves like the physical world: **responds instantly, moves continuously, carries momentum, resists at boundaries, and can be redirected mid-motion.**

This is the deepest idea in native motion. Most web animation is fire-and-forget: it starts, it blocks, and grabbing it mid-flight either does nothing or snaps. Native-feeling interfaces let you catch a sheet halfway through its dismissal and throw it back. **Springs are the tool that makes this natural, because they are inherently velocity-aware.**

Apple's stated preference: **quick and precise** over long and elaborate. Brevity plus precision reads as lightweight and conveys information more effectively.

## Gesture-driven motion, mechanically

The three properties that make an interaction read as fluid, in order of importance:

1. **Interruptible.** Stoppable, redirectable or restartable at any instant, not only at rest.
2. **Velocity-preserving.** When a drag ends, the gesture recognizer's velocity in points per second becomes the spring's initial velocity. **It must be normalized by the remaining distance** before it is handed over, or fast flicks overshoot wildly.
3. **Rubber-banded at boundaries.** Movement past a valid limit is resisted, not blocked. The value slips instead of stopping, which communicates "there is nothing past here" without a wall.

**Rubber banding is a resistance curve, not a clamp.** The classic form is `offset = (1 - 1/(distance * c / dimension + 1)) * dimension` with `c` around 0.55, which is effectively what `UIScrollView` does. It never reaches a hard stop and it releases back with a spring.

**The failure this fixes:** an animation that ignores the finger's velocity feels like the interface is arguing with you. That is the precise mechanism behind "this app feels off" complaints users cannot articulate.

Platform support: SwiftUI springs are velocity-aware by construction. Compose has `DeferredTransition` APIs (1.12) for gesture-driven animation, and `PredictiveBackHandler` exposes back-gesture progress as a `Flow`.

## Spring parameters, with real numbers

SwiftUI `.spring()` defaults are **response 0.55, dampingFraction 0.825, blendDuration 0**. `.interactiveSpring()` is **response 0.15, dampingFraction 0.86, blendDuration 0.25** – use it for anything a finger is currently touching.

What the parameters do:

- **Response** is the time for one oscillation if damping were zero. Lower is faster and snappier. It is a duration in disguise, which is why it is easier to reason about than stiffness.
- **Damping fraction** controls how fast oscillation decays. 1.0 is critically damped, no bounce. Below 1.0 bounces; lower is bouncier.

| Intent | Response | Damping | Feel |
|---|---|---|---|
| Button press, toggle | 0.3 | 0.8 | Quick, slight bounce |
| Default UI transition | 0.55 | 0.825 | Balanced – the system default |
| Modal or sheet presentation | 0.9 | 0.9 | Slow, deliberate, almost no bounce |
| Playful confirmation | 0.4 | 0.5 | Obvious bounce. Use sparingly |
| **Anything under a finger** | **0.15** | **0.86** | Tracks input, settles instantly |

**Compose** expresses the same physics differently: `spring(dampingRatio, stiffness)` with mass fixed at 1, where `stiffness = k/m` and `dampingRatio = c / (2·√(k·m))`. `DampingRatioNoBouncy` is 1.0, `MediumBouncy` 0.5, `HighBouncy` 0.2; `StiffnessMedium` is 1500, `StiffnessLow` 200. `tween(durationMillis, easing)` covers the duration-based case.

**Material 3 Expressive splits springs by purpose:** *spatial* springs for things that move in space (overshoot allowed, because objects do), *effects* springs for color and opacity (**no overshoot – a color that overshoots looks like a bug**). Using the wrong one is the most common M3E motion mistake.

Slint has **no spring primitive** – `cubic-bezier` only, so overshoot must be hand-authored as a bezier exceeding 1. See `rust-gui.md`.

**Choosing the app's default spring is a brand decision.** SwiftUI's named presets make it a one-word choice: `.smooth` for calm precision, `.snappy` for sharp and rhythmic, `.bouncy` for playful.

## Motion tokens

Motion belongs in the token system alongside color and spacing. Material 3 publishes easing and duration as named tokens, and adjusts duration by distance traveled, element velocity, and whether a surface is entering or leaving.

| Token | Duration | Use |
|---|---|---|
| `motion.short` | 100–150 ms | State change in place: ripple, toggle, press |
| `motion.medium` | 200–300 ms | An element enters or leaves, small movement |
| `motion.long` | 400–500 ms | Full screen transition, large surface |
| `motion.emphasized` | 500 ms+ | A hero moment. Once per flow |

Paired easing: **standard** (accelerate then decelerate) for movement within the screen, **decelerate** for entering elements, **accelerate** for exiting ones. Entering elements decelerate because they arrive and settle; exiting elements accelerate because they leave and do not need tracking.

**Asymmetry is the rule, not a refinement.** Entry and exit are different events and get different curves and often different durations.

**Tokenizing motion is what makes "the app feels slow" a one-line fix** instead of a sweep through every animation call site.

## Reduce Motion

A real user preference with real prevalence. Honoring it is not optional on either platform.

- **iOS:** Settings → Accessibility → Motion → Reduce Motion. A second, separate toggle sits underneath: **Prefer Cross-Fade Transitions** (iOS 14+), which replaces slide transitions with fades. SwiftUI exposes `\.accessibilityReduceMotion`; UIKit exposes `UIAccessibility.isReduceMotionEnabled` and `UIAccessibility.prefersCrossFadeTransitions`.
- **Android:** Settings → Accessibility → Remove animations, surfaced through `Settings.Global.ANIMATOR_DURATION_SCALE` being 0. **Compose does not read it automatically for custom animations.**

What to do when it is on, in order of preference:

1. **Cross-fade instead of slide, scale or parallax.** A fade still communicates the change and carries no motion.
2. **Keep the duration, drop the movement.** Removing the transition entirely makes state changes hard to follow – the animation was doing informational work.
3. **Never remove feedback.** A button that stops confirming a press is worse, not more accessible. Replace movement with a color or opacity change.

**The distinction: motion that conveys information stays, motion that conveys personality goes.** Parallax, spring bounce, zoom-through transitions, autoplaying background video and large-area movement are the specific things that trigger vestibular symptoms.

This is the same preference as the web's `prefers-reduced-motion`. Wire one internal flag from all sources so the rule is applied once.

## Motion tooling – Rive vs Lottie

Not competitors so much as different answers to "does this animation need to know anything".

| | Lottie | Rive |
|---|---|---|
| Nature | A **file format** (JSON) you export to | A **tool plus runtime** with a state machine |
| Authored in | After Effects, Cavalry, Rive, Lottie Creator | Rive's editor |
| Runtime behavior | Plays a timeline | Reacts to input and application state |
| Typical size | 5–20 KB for an icon | Comparable, plus the runtime |
| Targets | iOS, Android, Flutter, React Native, web | Same, plus game engines |
| Cost | MIT-licensed player | Editor is a product |

**Use Lottie** for anything that plays start to finish and never branches: an animated icon, an empty-state illustration, a success checkmark, a loading loop.

**Use Rive** when the animation has states: a loading indicator that morphs into success or failure, a toggle that tracks a drag, an icon whose animation depends on data. The state machine is the differentiator, because otherwise the branching logic ends up hand-written in the app and desynchronizes from the design.

**Use neither for standard UI transitions.** A sheet presentation, a list reorder or a button press should be the platform's own animation system. Shipping a Lottie for something the platform animates natively costs a dependency, a runtime, a file, and a result that will not match the rest of the OS.

The workflow that recurs: prototype in Figma, author production motion in Rive, hand the developer an asset **plus a state contract** rather than a video to reimplement.

## Haptics

### The three iOS generators, and their meanings

This is a vocabulary, not a set of buzz intensities. A `.success` haptic on a failure is as wrong as a green error message.

| Generator | Types | Meaning |
|---|---|---|
| `UINotificationFeedbackGenerator` | `.success`, `.warning`, `.error` | An outcome the user did not directly cause the timing of |
| `UIImpactFeedbackGenerator` | `.light`, `.medium`, `.heavy`, `.soft`, `.rigid` | A collision, a snap, a boundary, a landing |
| `UISelectionFeedbackGenerator` | – | A discrete value changed under the finger |

### The rules

- **Use OS defaults for standard flows.** Users already know the system success, failure and neutral patterns; inventing your own costs recognition and gains nothing.
- **Less is more.** Over-vibration is annoying, numbing, and drives users to disable haptics entirely – **losing the channel permanently**.
- **Consistency creates meaning.** A stable trigger-to-feedback mapping is what lets the pattern be learned.
- **Sync haptics with the visual and audio events.** Multimodal alignment is what makes it read as one event rather than three.
- **Always user-controllable** – accessibility, battery and preference.

### Core Haptics, when the standard patterns are not enough

Direct control of the Taptic Engine through two parameters, both 0 to 1:

- **Intensity** – how strong. **The intensity curve is logarithmic, not linear**, so 0.5 is not half of 1.0 and interpolating linearly gives the wrong result.
- **Sharpness** – how crisp. Low is a dull thud, high is a sharp tick. This carries most of the character.

Plus `attack`, `decay` and `release` for envelope shaping, and two event types: transient (a tap) and continuous (a sustained buzz).

Patterns can be authored as **AHAP** files – JSON with events at times relative to the pattern start – which means haptics can be designed as an asset and handed over like a Lottie file rather than written as code.

### Android

Two layers, and the choice between them is the whole design decision:

| API | Use |
|---|---|
| **`HapticFeedbackConstants`** | **Default choice.** Action-oriented constants (`CONFIRM`, `REJECT`, `LONG_PRESS`, `GESTURE_START`) that map consistently across devices |
| `VibrationEffect` | Composition from primitives when no constant matches |

Android's own guidance is explicit: **if an action is covered by a `HapticFeedbackConstants` value, use it.** That keeps haptics consistent with the rest of the device, and it is stated as an accessibility consideration rather than a style one.

`VibrationEffect` primitives when needed: short ones are `CLICK`, `TICK`, `LOW_TICK`; shaped ones are `SLOW_RISE`, `QUICK_RISE`, `QUICK_FALL`, `THUD`, `SPIN`. Newer devices support **envelope effects** for continuous intensity and frequency control – check `areEnvelopeEffectsSupported` and design a fallback, because hardware varies enormously.

Android's three stated principles generalize past Android: **favor rich and clear haptics over buzzy ones, be consistent with both the system and the app, and scale the treatment by frequency of use and importance.**

**The rule underneath all of it:** author custom haptics for the two or three moments that define the product – the capture, the send, the completion – and use standard constants everywhere else. An app with fifteen bespoke haptic patterns has no haptic language, only noise.

## Sound

The third feedback channel, and the one most apps get wrong by using too much of it.

- **Earcons are short, distinct, and mapped consistently.** The camera shutter is canonical: short, unmistakable, and it means exactly one thing.
- **Keep them under roughly 400 ms.** A sound longer than the action it confirms makes the interface feel slower than it is.
- **Make sounds differ from each other more than seems necessary.** Phone speakers are small and the environment is noisy, so subtle timbre differences do not survive.
- **Sound is confirmation, not notification.** Use it where the user has just acted – send, capture, complete, error – not to demand attention.
- **Respect the silent switch and system volume**, always, and give an in-app toggle. On iOS that means choosing the right audio session category; a UI sound that plays through the ringer switch is a bug users notice immediately.
- **Align sound, haptic and visual to the same instant.** Misalignment by even 50 ms reads as three separate events instead of one.
- **Sound also serves accessibility**, giving confirmation to users who cannot see the visual change. That argues for having earcons at all, not for having many.

**Pick three to five sounds for the whole app, use them consistently, and default them off for anything that is not a direct response to a user action.**

### Sonic identity

Worth knowing because sound is a brand surface most apps leave empty, and **humans process sound in roughly 0.05 s** – faster than an image resolves.

- **A sonic logo runs 2–5 s.** Long enough to have character, short enough to be recognized. Once per session at most: a launch, an unlock, a payment confirmation.
- **Everything else is functional sound**, built from the same tonal material so the app sounds like one thing.
- **Design for the emotion, not the melody.** The useful brief is texture and feeling, not a jingle.
- **The recognizable examples are mechanical, not musical:** the camera shutter, the Apple Pay confirmation, the payment terminal beep. Each is a single event with a single meaning, repeated identically forever, which is exactly why it is recognized.

**Scoping note:** most apps should not commission a sonic identity. Most apps should pick three functional sounds and default them off outside direct actions. The full exercise pays for products with an audio-first surface – voice, media, payments, automotive – and is decoration everywhere else.

## Microinteractions

One trigger, one rule, one feedback, one loop or mode. On native the ones that matter are unglamorous: the press state, the pull-to-refresh spring, the checkmark, the toggle, the keyboard haptic.

What the good ones have in common:

- **Multimodal and simultaneous.** Apple Pay's success is a haptic, a visual and a sound landing together. Any one alone is noticeably weaker.
- **Attached to a real event**, not sprinkled on decoration. A celebration for completing a real task is delightful; a celebration for opening a screen is noise.
- **Fast: 100–200 ms.** A delightful animation the user waits for stops being delightful on the fifth repetition.
- **Scaled to frequency.** The interaction performed fifty times a day gets the subtlest treatment; the one performed monthly can afford a moment.

The 2026 direction to treat carefully: microinteractions that adapt to the user, becoming quicker for power users and more pronounced for new ones. Real idea, and it collides with predictability. **Adapting speed is defensible; adapting whether feedback exists at all is not.**
