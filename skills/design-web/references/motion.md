# Motion

**Motion is not decoration on top of an interface. It is the interface explaining itself in time.** Done right it costs nothing and reads as quality; done wrong it reads as cheap, and users cannot say why.

## The asymmetry rule

**Entrances and exits must never use the same curve.** The single highest-value motion rule there is.

| Direction | Curve | Why |
|---|---|---|
| **Entering** | **`ease-out`** – fast start, slow stop | The fast start gets the object into position; the deceleration makes it **land** rather than stop. Matches how a real object arrives |
| **Exiting** | **`ease-in`** – slow start, fast finish | The slow start gives the eye a moment to register the departure; the fast finish makes it **decisive and permanent** |
| **Between states, same element** | `ease-in-out` | Both ends are anchored on screen, so both ends need to settle |

**Corollary on duration: entrances should be slightly longer than exits.** A popup at 300 ms in, 200–250 ms out. The user wants to see the arrival and does not want to wait for the departure.

Getting this pair right is the difference between motion that feels considered and motion that feels like a `transition: all 0.3s` reflex.

## Duration

| Range | Reads as | Use |
|---|---|---|
| **under 100 ms** | **Instant** – below the perception threshold for change | Hover tints, focus rings, toggles, anything high-frequency |
| 100–200 ms | Fast, barely perceived | Small elements, small distances, state changes |
| **200–300 ms** | **Fast but perceivable – the workhorse band** | Most UI transitions. Material caps mobile animation here |
| 300–400 ms | Deliberate, expressive | Large elements, large distances, moments meant to be noticed |
| 400–500 ms | Slow | Only for big movement across large screens |
| over 500 ms | Sluggish, blocking | Almost always wrong for UI. Reserve for cinematic brand moments where waiting is the point |

Anchors worth carrying:

- **~100 ms is perceived as instant.**
- **~230 ms is the average time to visually perceive something** (Model Human Processor). This is why sub-200 ms motion is **felt more than seen**.
- **1 second is the limit of uninterrupted flow of thought.**

**Distance and size scale duration.** A 40 px chip and a full-screen sheet should not share a duration. Bigger and farther means longer, **but sublinearly** – never proportionally, or large transitions feel broken.

Material 3 reference tokens: `short1` 50 ms, `short2` 100 ms, `medium1` 250 ms, `medium2` 300 ms. Easing splits into **Standard** `cubic-bezier(0.4, 0, 0.2, 1)` and **Emphasized**, each with accelerate and decelerate variants.

## Springs vs bezier

A cubic-bezier distributes a **fixed duration**. A spring has **mass, stiffness and damping**, and its duration is an output, not an input. The result is a decaying oscillation.

| | Cubic-bezier | Spring |
|---|---|---|
| Feel | Neutral, composed, controlled | Fluid, organic, alive |
| Bounce | Only fake, via a curve exceeding 1 | Real decaying oscillation |
| Interruptible | Poorly – restarting mid-flight jumps | **Natively**, because it is velocity-aware and can be redirected |
| Best for | Color, opacity, anything wanting a fixed duration | **Motion**: scale, position, offset. **Not color or opacity** |

- **Springs do a better job tricking our brains into thinking something is actually moving.** That is the mechanism behind the luxury feel: the eye recognizes physical plausibility.
- **When everything eases in and out, interfaces start to feel sluggish and overly polite.** Uniform `ease-in-out` on everything is a real failure mode and it is the default most people ship.
- **High-frequency interactions – typing, keyboard nav, fast toggles – feel slower and noisier with motion added.** The correct amount of animation there is often zero.

### Springs in pure CSS – `linear()`

`linear()` interpolates between a list of output values, and **values above 1 or below 0 overshoot** – real bounce and spring in CSS with no runtime JS.

**The technique: sample a real spring equation into dozens of points and hand them to `linear()`.** True spring motion, not a bezier approximation. ~87% global support.

Generators: `linear-easing-generator`, `kvin.me/css-springs` (perceptual duration and bounce inputs), presets for Spring, Bounce, Elastic and Material emphasized.

**This resolves the spring trade-off in the browser's favor: you no longer need a JS animation library to get spring *feel* – only to get spring *interruptibility*, which is the one thing `linear()` cannot do.**

## Interruptibility

> **An animation must stay grabbable and reversible at any instant, not just at rest.**

An interface is fluid when it behaves like the physical world: **responds instantly, moves continuously, carries momentum, resists at boundaries, and can be redirected mid-motion.**

**The deepest idea in motion.** Most web animation is fire-and-forget: it starts, it blocks, and grabbing it mid-flight either does nothing or snaps. **Springs are the tool that makes interruption natural, because they are velocity-aware.**

Apple's stated preference: **quick and precise** over long and elaborate. Brevity plus precision reads as lightweight and conveys information more effectively.

## Performance – what can actually be animated

**Tier S: `transform` and `opacity`.** They run on the compositor, skip layout and paint entirely, and **stay smooth even when the main thread is busy.** `filter` and `clip-path` are also compositor-capable in modern browsers.

**The number one cause of jank is animating properties that force layout or paint every frame** – `width`, `height`, `top`, `left`, `margin`, `padding`, `box-shadow`.

| Instead of | Animate |
|---|---|
| `width` / `height` | `transform: scale()` – watch text distortion, or animate a wrapper |
| `top` / `left` / `margin` | `transform: translate()` |
| `box-shadow` | Opacity of a pseudo-element carrying the shadow |
| `background-color` on a large area | Opacity of an overlay layer |

> **120 Hz makes mediocre animation look worse, because you have half the time per frame.**

As high-refresh displays become standard, sloppy animation gets *more* visible, not less. **That kills the argument that animation performance work is optional.**

### FLIP – animating the unanimatable

**First → Last → Invert → Play.** Record the position before, let the DOM change, apply an inverse transform so it *appears* not to have moved, then animate the transform to zero.

- It converts a **layout change into a compositor-only transform animation** – the element is already at its new layout position and only *looks* like it is traveling. 60 fps on low-end hardware.
- **The right tool whenever the animation involves a layout change:** list reordering, sortable lists, drag-and-drop, filtered views, expand and collapse, dashboard rearrangement.
- Powers Motion's `layoutId`, GSAP's Flip plugin, Vue's `<TransitionGroup>`.

**This is the answer to "I need to animate height or position and I was told not to."** You do not animate the layout property – you let the layout change instantly and animate the *difference*.

## Reduced motion, done properly

`prefers-reduced-motion` exists for **vestibular disorders**, where large scaling and panning can trigger genuine physical discomfort – nausea, dizziness, migraine. **It is not a taste setting.**

| Wrong | Right |
|---|---|
| `animation: none; transition: none;` | Set `animation-duration` / `transition-duration` to **`0.01ms`** |

**Killing animation outright can break interfaces that depend on an animation's completion event**, and it removes state-change feedback the user still needs. Making it instant preserves the logic and removes the motion.

- The preference must be honored for **all** animation, transitions, smooth scrolling and motion-driven UI – **including third-party plugins and legacy code that ignore it.** You override them.
- No autoplaying video, sliders or movement.
- **Not all motion is equal.** Opacity fades and color changes are generally safe; **large translations, scaling, parallax and rotation are the triggers.** The refined implementation reduces *transform* motion and keeps *opacity* transitions rather than flattening everything.
- **Offer an in-app toggle too.** The OS setting is not universally discoverable.

## Stagger and choreography

| Rule | Value | Why |
|---|---|---|
| Stagger delay per item | **50–100 ms** | Enough to read as a cascade, not enough to feel like waiting |
| Delay vs duration | Delay is **30–70% of the item's duration** | The next item starts before the previous finishes, so the sequence reads as one gesture rather than a queue |
| **First item: zero delay** | Always | A delay on the first item makes the user think the interaction failed |
| Maximum staggered items | **5–6** | 20 cards at 60 ms each is 1.2 s of waiting. Stagger the first 4–5, bring the rest in together |
| Property | Transforms and opacity only | Staggering layout properties across many elements is guaranteed jank |

**Stagger is a hierarchy tool as much as a decorative one: the order things arrive in tells the user what to read first.**

## Origin-aware motion

**`transform-origin` defaults to `center`, and center is wrong in most cases.**

A dropdown, popover, tooltip, menu or modal should **scale out from the thing that opened it.** Getting this right creates a causal link – "this came from that" – and **getting it wrong is one of the quietest reasons an interface feels generic.**

- Base UI and Radix expose CSS variables that set the correct origin automatically for popovers and menus.
- For a modal from a button, the View Transitions API can make the dialog appear to originate from the trigger.
- Same idea at larger scale: **a detail view opening from a card should grow from that card's position**, not fade in from the center of the screen.

## View Transitions

| Type | Support | Verdict |
|---|---|---|
| **Same-document** | Chrome 111+, Safari 18+, **Firefox 144+** | **Finished. Use freely** |
| **Cross-document (MPA)** | Chrome 126+, Safari 18.2+; still only animating in Chromium in practice | **Progressive enhancement only.** Real support gap |

- Cross-document requires an **explicit opt-in on both the source and destination page** via a CSS at-rule. No JS call – the link click triggers it.
- Watch the **four-second budget**. Treat it as a performance requirement, not an animation detail.
- **The point of a shared-element transition is continuity of identity:** the thumbnail *becomes* the hero image, so the user never loses the thread. That is the single most valuable thing view transitions buy.

## Scroll-driven animation

`scroll()` (scroll progress) and `view()` (element visibility progress) are pure CSS – **no scroll listeners, no IntersectionObserver, no main-thread work.**

- Support: Chrome/Edge 115+, Safari 18+, Firefox 132+. **~84% global.** Progressive enhancement, not a baseline.
- `view()` named ranges: `entry`, `exit`, `contain`, `cover`.
- **Gotcha with a real bite: declare `animation-timeline` *after* the `animation` shorthand.** The shorthand resets it to `auto`, silently killing it.
- Always guard with `@supports (animation-timeline: view())`.
- Because it runs on the compositor, transform and opacity scroll animations are **as smooth as the scroll itself** – a completely different quality bar from JS scroll handlers.
- Use for reading progress bars, parallax, reveal-on-enter, sticky headers that shrink. **Not for anything that must happen.**

## Feedback latency

| Threshold | Meaning |
|---|---|
| **~50–100 ms** | Feels **instantaneous**; the link between action and reaction stays intact |
| **~1 second** | Upper limit of uninterrupted flow of thought. Beyond this the user notices waiting |
| **~10 seconds** | Attention is lost unless explicit progress is shown |

> **The interface should feel as if it is listening to the user.** Give feedback on all their actions as soon as possible.

## Microinteraction structure

Four parts, and naming them makes them designable:

1. **Trigger** – what starts it. Explicit (a tap) or implicit (a state change, a threshold crossed).
2. **Rules** – what can and cannot happen. Make the rules as transparent as possible.
3. **Feedback** – what the system says back. Visual, motion, haptic, audio.
4. **Loops and modes** – what happens over time and on repeat. **What does the 50th time look like? Does it decay?**

**The loop question is the one that gets skipped, and it is where delight turns into irritation: a celebration that is charming once is grating on the tenth repeat.**

### Practical set

- **A subtle scale-down on button press makes an interface feel instantly more responsive.** One line, disproportionate effect.
- **Every action gets an acknowledgement.** Form submit shows a loading state; copy-to-clipboard shows a success state.
- Defaults that work: **`ease-out`**, custom cubic-beziers over stock keywords, spring for natural motion, **UI animation under ~300 ms**.

## Celebration and delight

**The FEAT test: if it is an ordinary thing, it does not deserve an extraordinary response.** Confetti is for something the user has been building toward – finishing onboarding, hitting a milestone, closing a deal. **Not for saving a form.**

**Escalation ladder, cheapest to most expensive:** inline state change → subtle checkmark → drawn checkmark with an easing that lands → glow or pulse → confetti. **Match the rung to the magnitude of the achievement.**

The failure mode has a name in the literature: **the over-confetti-ing of digital experiences.** Celebrating everything means celebrating nothing, and it makes a product feel like it is performing enthusiasm at you.

**This is a place to ask rather than decide:** "how big a moment is this, honestly?"

## Easing as emotional register

| Curve | Reads as |
|---|---|
| `ease-in` (slow start) | Cautious, hesitant, reluctant. **Wrong for entrances** |
| `ease-out` (fast start, soft landing) | **Satisfying, complete** – like finishing a task |
| Fast and bouncy, with overshoot | Energetic, playful, attention-grabbing |
| Slow and smooth | Elegant, serious, calm. Reduces cognitive load |
| `linear` | Mechanical, artificial. **Correct for progress and continuous rotation, wrong for almost everything else** |

**Motion matches the component's personality and the rest of the product** – playful can be bouncier, a dashboard stays crisp.

## Motion as brand

**Motion is where a brand stops being seen and starts being felt.** The vocabulary that makes it designable rather than vibes-based – **speed, rhythm, easing, transition style, amplitude** – is a system, and it should be written down like a color palette.

| Personality | Motion signature |
|---|---|
| **Calm precision** | Short, tight, minimal overshoot, near-linear settle. Apple |
| **Playful energy** | Springy, overshoot, squash and stretch, character. Duolingo |
| **Sharp and rhythmic** | Fast, decisive, hard cuts, strong easing. Nike |
| **Calm and minimal** | Slow fades, tiny distances, almost no transform |

**Motion should be specified per project, in the spec, alongside type and color.** A project's easing and duration set is part of its identity, **not a default lifted from a framework.**

## Disney's principles that transfer

Three of the twelve carry real weight in UI:

- **Anticipation** – a small counter-movement before the main move. A button that dips inward before springing out. It prepares the eye and makes the action read as intentional rather than abrupt.
- **Squash and stretch**, with volume conserved: stretch thinner, squash wider. Even rigid UI benefits from a subtle stretch on a fast move and a small squash on landing. **A large part of what makes motion feel physical rather than computed.**
- **Follow through and overlapping action** – not everything stops at once. Related elements moving at slightly different rates is how you **establish hierarchy in motion**: the primary element leads, secondary elements trail and settle.

The rest apply to illustration and mascot work rather than interface chrome.

## Loading and perceived performance

**A genuine, unresolved conflict.**

- **For skeletons:** measured 20–30% faster in *perceived* load time than spinners; academic work found higher scores on perceived speed and ease of navigation, and skeletons rated best emotionally.
- **Against:** a 2017 Viget study found the opposite – **skeletons performed worst on perceived duration**, and the spinner group evaluated their wait most positively.

The reconciliation the sources offer: **skeletons work in familiar interfaces and at short waits; they are off-putting in unfamiliar ones and at long waits.** The claimed mechanism is attentional – **a spinner points at the waiting, a skeleton points at the content about to arrive.**

**Working position: skeletons for short, predictable waits in a layout the user already knows** (feed refresh, tab switch, returning to a list). **Spinners or progress for unpredictable or long waits.** Determinate progress whenever the length is knowable. **Do not present this as settled – the evidence is not.**

### Progress indicators

| Wait | Use |
|---|---|
| **1–3 s** | Spinner or skeleton |
| **3–10 s** | **Determinate progress bar or percentage** |
| **10 s+** | Progress **plus** a time estimate, plus something to look at |

- **Determinate whenever the length is knowable.** It converts an unbounded wait into a bounded one, which is the whole psychological benefit.
- **A determinate bar fills 0 to 100 and never decreases.** A progress bar that goes backwards destroys trust in every progress bar you ship afterwards.
- Indeterminate says only "something is happening" – acceptable for background work, poor for anything the user is waiting on.
- **Progress bars whose pace accelerates toward the end are perceived as faster than linear ones.** The perception of a wait is shaped by its *profile*, not only its length.

### The entrance-animation trap

**Do not animate the LCP element in.** Fading in the largest contentful element from `opacity: 0` directly delays the metric and the perceived first paint. **Animate around it** and let the main thing simply be there.

## Hover, and its limits

- **Subtle wins.** A slight background darkening, a small shadow lift, a border change. **Dramatic color swaps and large motion on hover read as jarring** and pull attention off the task.
- **Every `:hover` needs a matching `:focus-visible`.** A hover style with no focus equivalent means keyboard users get no feedback and the interaction is silently broken for them.
- **Hover is a progressive enhancement, never a requirement.** It does not exist on touch, where `:active` and loading states carry the whole feedback load.
- A card that lifts on hover: `translateY(-2px)` plus a shadow, 120–200 ms, `ease-out`. More than that and it starts to feel like it is trying.

**The corpus goes further: never document hover. Document Default and Active/Pressed only.** Ten independent restatements across brand systems built mobile-first, where **press matters and hover does not exist.**

## Drag and drop

- **The goal is that it feels like moving a physical object.** Users bring that expectation from the real world and notice immediately when it is not met.
- **Lift the dragged item in z** – shadow and slight scale – so it reads as picked up.
- **Magnetic snapping into the drop target compensates for imprecise pointing.** The single biggest usability improvement in a drag interaction.
- **Animate the drop into the final position (~100 ms).** An item that teleports on release feels broken.
- **The drop zone must signal:** expand, change color, show a preview of where the item will land.
- **A drag handle is the affordance.** Without one, draggability is invisible.
- **Always provide a non-drag path.** Drag-and-drop is inaccessible to keyboard and to many motor-impaired users if it is the only route.

## List add and remove

**On add, existing items slide out of the way while the new item fades in and scales up; on remove, the reverse.** The movement of the neighbors is what makes the change legible – **an item that simply appears leaves the user unsure whether the list reordered.**

- **Use FLIP for the neighbors' movement**, since it is a layout change.
- Production timings: **~200 ms for hover and focus state transitions, ~500 ms for active and feedback states.**
- On removal, animating the height collapse is what makes it feel physical – but do it via FLIP or `grid-template-rows: 0fr → 1fr`, **not by animating `height`.**
- **Always animate the removal *after* the item is gone from the data**, not before. An optimistic removal that animates and then fails needs a distinct restore animation, not a paused one.

## Two things to refuse

**Scroll hijacking.** Users have memorized how scrolling works on their device and expect consistency. Overriding speed, direction or locking it into steps **removes control**, is nearly impossible to implement consistently across devices and browsers (producing lag, CPU spikes and crashes), and **breaks the user's ability to control their own reading pace – an accessibility failure, not a preference one.**

There is a site named `dontfuckwithscroll.com`. That is the strength of feeling. **Treat scrolljacking as a refusal, not a trade-off.**

**Parallax.** More nuanced but close.

- **Harmful to people with vestibular disorders** – differential scroll speeds are a direct motion-sickness trigger. Webflow's accessibility checklist says plainly: avoid parallax effects.
- Can confuse screen readers and keyboard navigation when DOM order stops matching visual flow.
- **Where it legitimately works:** creative portfolios, brand storytelling, product demonstration – where immersion is the point and the content is not task-critical.
- **Never on corporate or information-heavy sites; generally not on mobile.**
- If used: honor `prefers-reduced-motion` completely, keep the differential small, **and never attach meaning to the effect.**

## Sound

- Use for **immediate confirmation that an action registered**, for information with no visual channel, and to support multitasking where the user is not looking at the screen.
- **Short.** Interface audio must be immediate and brief or it disrupts the interaction flow.
- Design it as **ergonomics and branding together** – a UI sound is both a signal and a piece of identity.
- Genuine accessibility value: a second feedback channel helps users with cognitive and learning disabilities.
- **Always mutable, never the sole carrier of meaning, and off by default on the web.**

## Choosing an animation approach

| Need | Use |
|---|---|
| Hover, fades, color transitions, single keyframe loops | **Native CSS** – zero bytes, everyone knows it |
| Spring feel without a library | **`linear()`** |
| Scroll-linked reveals, parallax, progress | **CSS scroll-driven animations**, falling back to a library only where support requires |
| React UI transitions, presence and exit animation, layout animation | **Motion** – `AnimatePresence` and layout animations are far less code than the equivalent elsewhere |
| Timeline-heavy sequences, complex scroll choreography, animating to runtime-computed values | **GSAP** – reach for it the moment you are fighting chained transition-delays |
| Lightweight JS control | Anime.js (~9 kb) |

**The correct default is the platform.** Reach for a library at the point where you are fighting the platform, not before – **a specific, checkable moment rather than a preference.**

## What "smooth" actually is

"Smooth" is **five separate things**, and they can be worked on independently.

**1. Immediate acknowledgement.** A page that loads in 1.5 s with no visual feedback feels slower than one that loads in 2 s with an immediate skeleton. Response within ~100 ms, optimistic UI, a press state before any data moves.

**2. Physically plausible motion.** Start fast, decelerate, because that matches how objects move. 150–300 ms for most transitions; approaching a second feels sluggish.

**3. Consistent frame pacing.** 60 fps target, 120 Hz raising the bar. And the finding that ties everything together:

> **Variable performance destroys smooth perception even when averages are good.**

The same result as the streaming research (**over 30% speed variation degrades perceived fluency**) and the same as the animation-jank result. **Consistency beats speed, in every modality measured.** Probably the single most transferable finding in this whole skill.

**4. Responsiveness prioritized over capability.** The fastest-responding system that does less will often feel faster than a slower-responding system that does more.

**5. Craft details the user feels without noticing.** Named explicitly in the sources: **concentric border radii on nested elements, optical rather than geometric alignment, layered transparent shadows instead of solid borders, staggered enter animations, and subtle texture.** A sense of quality and craftsmanship that users feel rather than consciously notice.

That list is an independent confirmation of `craft.md`, arrived at from the perception side rather than the aesthetics side.
