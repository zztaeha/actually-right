# Accessibility

Not a phase. On both platforms it is a design decision made at layout time, and in the EU it is law.

## Which rules apply

**WCAG applies to native apps**, but there is no native-specific normative standard. W3C published **WCAG2Mobile** (*Guidance on Applying WCAG 2.2 to Mobile Applications*, first draft May 2025) to bridge the gap. In practice: WCAG 2.2 plus Apple's and Google's accessibility API documentation, recorded in a VPAT.

**The criteria that are specifically mobile-shaped:**

| Criterion | Requirement |
|---|---|
| **1.3.4 Orientation** | Support **portrait and landscape**. Do not lock unless essential |
| **1.4.10 Reflow** | No horizontal scrolling to read |
| **1.4.11 Non-text Contrast** | UI components at **3:1** |
| **2.5.1 Pointer Gestures** | **Single-tap alternative** to any multipoint or path-based gesture |
| **2.5.2 Pointer Cancellation** | Actions fire on **up**, not down, so a mistaken touch can be dragged away and canceled |
| **2.5.4 Motion Actuation** | Anything triggered by shake or tilt needs a **UI alternative** and must be disableable |
| **2.5.7 Dragging Movements** | **Single-tap alternative to any drag** |
| **2.5.8 Target Size** | 24 × 24 minimum (platform minimums are higher) |

**2.5.1, 2.5.7 and 2.5.4 have no real web equivalent, and they are exactly the gestures native design reaches for first.**

> Every swipe-to-delete needs a tappable delete. Every drag-to-reorder needs a move-up and move-down. Every shake-to-undo needs an undo button.

## Non-text contrast – the criterion native apps fail most

WCAG 1.4.11 requires **3:1 against adjacent colors** for UI components and meaningful graphics. It catches what a text-contrast checker misses.

What it covers on a native screen:

- **Component boundaries:** the edge of a text field, the track and thumb of a switch or slider, a checkbox outline, a card border if the border is what identifies it.
- **Icons that carry meaning on their own.** A meaningful icon needs 3:1; a decorative one, or one repeating adjacent text, does not. **The test is whether removing it loses information.**
- **Every interactive state.** Focused, pressed, selected and checked each need 3:1 individually, not just the resting state.
- **Focus indicators**, where custom-styled controls fail almost universally.

Exempt: **disabled components**, explicitly.

Where native apps break it in practice:

- A gray-on-white text field border. The single most common failure.
- A selected tab distinguished only by a tint under 3:1 against the unselected ones.
- A toggle whose "off" track is barely visible against the background.
- **Glass and translucent chrome**, where effective contrast depends on whatever is behind it – so it has to be measured against the real backdrop, not a flat approximation.

The scanners catch some of this and not all, because they cannot always tell a decorative icon from a meaningful one. It belongs on the manual list.

## The reading-order divergence

**iOS VoiceOver reads left-to-right, top-to-bottom by screen position. Android TalkBack and the web follow the view hierarchy.**

A genuine platform difference with design consequences:

- On iOS a **visually** correct layout is usually announced correctly, and an absolutely-positioned element in the wrong place is announced in the wrong place regardless of code order.
- On Android, view-hierarchy order rules – so the web problem of visual order diverging from source order **exists on Android and largely does not on iOS**.
- Both: the easiest way to avoid focus-order issues is to display elements in the same order they appear in the hierarchy.

Rules that hold on both:

- **Focus order follows visual reading order**, and changes are **announced**, not silent.
- **On navigating to a new screen, focus starts from the same position every time.** Consistency here is what makes an app navigable without sight.
- Move focus deliberately into a modal, and return it on dismiss.
- **Prefer the OS default reading order** unless there is a specific reason to override.

## The iOS toolkit

- **Labels, hints, traits.** Traits describe what an element *is*. For custom controls, combine the relevant traits with the superclass's via OR.
- **`accessibilityElement(children: .combine / .ignore)`** groups a composite into one focusable item spoken once. The equivalent of Compose's `mergeDescendants`.
- **`accessibilityAction(named:)`** exposes an action as a named custom action. **This is the mechanism that makes gesture-driven UI reachable** – a swipe action becomes a named action in the VoiceOver rotor.
- **`accessibilityRotor`** creates a custom navigation path through a specific collection of elements. The native equivalent of landmark navigation.
- Standard controls are accessible by default; custom-drawn ones are not.

**The rotor is what designers most often miss.** VoiceOver users navigate **by category** – headings, links, form controls, custom rotors – not by swiping through everything. **An app with no headings and no rotors can only be traversed linearly.**

Compose's equivalents are `contentDescription`, `role`, `mergeDescendants` and `heading()`. See `android.md`.

## Motor accessibility

**Switch Control** lets someone operate the device with a single switch – an adaptive button, a head movement, a voiced or voiceless sound. Two modes: **item scanning** steps through elements, **point scanning** selects a screen position when item scanning cannot reach something.

The useful heuristic: *by making an app fully accessible to VoiceOver, it will likely already be great for Switch Control.* With one addition that matters:

> **Switch Control users cannot perform gestures at all**, so interactions must be exposed as named custom actions. **A swipe-only affordance is unreachable no matter how good the labels are.**

**Voice Control** operates the device entirely by speech and relies on elements having speakable names. **An unlabeled icon button is not just unreadable, it is unclickable.**

The rule covering both: **everything must be reachable without touch** – via switch, external keyboard, or voice.

## The display settings a design has to survive

| Setting | Effect on design |
|---|---|
| **Larger Text (Dynamic Type)** | 12 sizes including AX1–AX5 |
| **Bold Text** | **Re-weights every font on the device.** A hierarchy depending on the gap between regular and semibold can collapse |
| **Increase Contrast** | Raises separation; semantic colors adapt automatically, hardcoded custom ones do not |
| **Reduce Transparency** | **Removes blur and translucency.** Directly targets Liquid Glass, and there is documented user demand for exactly this. **Any hierarchy carried only by translucency disappears** |
| **Reduce Motion** | See `motion.md` |
| Color Filters, Smart Invert | Custom-drawn content and images can invert badly |

**The design test: turn on Bold Text, Increase Contrast and Reduce Transparency together and check whether the interface still has a hierarchy.** If depth was doing the work, it is now gone.

## Reduce Motion, and why it is not cosmetic

Every major OS ships it – **iOS since 2013**, macOS, Windows, Android, GNOME.

> Not respecting that setting creates inconveniences, but sometimes it is about real, life-affecting symptoms. For someone with a vestibular disorder, migraine with aura, or Ménière's disease, animations can trigger real nausea, dizziness or a headache.

**Decorative animation eliminated entirely; essential feedback animation reduced to instant or minimal motion** – not removed, because the feedback is still needed. Implementation detail in `motion.md`.

## Testing

**Automated first, manual second.**

| Platform | Tool | Covers |
|---|---|---|
| iOS | **Xcode Accessibility Inspector** | Inspect labels, traits, hints, frames. **Run Audit** scans for contrast, undersized targets, missing labels |
| iOS | `XCUITest` | Assert labels and traits in CI, so a missing label is a failing test rather than an audit finding |
| iOS | `UBKAccessibilityKit` | On-device audits without Xcode – useful for QA who do not build the app |
| Android | **Accessibility Scanner** | Labels, contrast, touch targets, clickable elements |
| Android | **Accessibility Insights for Android** | Goes further than the Scanner, produces a shareable report |
| Android | `UI Automator` | The automated side |
| Cross-platform | Appium-based checks, commercial scanners | Auditing a build with no source access |

**All of them are structural only.** A scanner cannot evaluate whether a label is contextually appropriate. **Automated tooling tops out around 30–40% of real issues.**

Then manual: **every core journey with VoiceOver and with TalkBack, on real devices, not emulators.**

**Put the automated checks in CI so mechanical failures never reach review, and spend the human time on the journeys.**

### The single highest-yield check

> **Testing at the worst case – smallest supported screen × largest font setting – catches more bugs than any other accessibility check, because it stresses the entire layout system at once.**

Add a long translation and RTL to that configuration and it becomes the worst case that actually ships. Nobody tests it, and it is where labels overlap, buttons clip and layouts collapse.

## The legal schedule

### European Accessibility Act

**Enforceable since 28 June 2025** for new digital products and services. Existing services have until **28 June 2028 – unless they undergo a major update after June 2025, in which case compliance is immediate.**

| | |
|---|---|
| **Standard** | **WCAG 2.1 Level AA**, audited against **EN 301 549 v3.2.1** |
| **Scope** | Websites, **mobile apps**, electronic documents, **self-service terminals**. **Applies to non-EU businesses serving EU users** |
| **Exemption** | Under **10 FTE** or under **€2M** annual turnover |
| **Enforcement** | **First lawsuits filed in France, November 2025.** Other member states active in 2026 |
| **Fines** | **€60,000 (Ireland) up to ~€900,000 (Sweden)** |

Two things follow for design work:

1. **"Major update" triggers immediate compliance. A redesign is precisely a major update**, so any redesign brief for an EU-facing product is an accessibility brief whether the client framed it that way or not. **Say so early.**
2. **Self-service terminals are in scope**, which pulls kiosk and embedded HMI work into the same regime. See `surfaces.md`.

### Other deadlines

US **HHS requires WCAG 2.1 AA for healthcare apps by 11 May 2026.**

## Accessibility Nutrition Labels

App Store product pages can declare which accessibility features an app supports: VoiceOver, Voice Control, captions, larger text, sufficient contrast, reduced motion, differentiate without color. **Voluntary now, with Apple stating the intent to make it mandatory.**

**Why the labels matter more than the features:** a declaration on the store page turns accessibility into a **visible, comparable product attribute** rather than an internal quality bar. A competitor declaring VoiceOver support next to an app that cannot is a conversion difference. This is the mechanism that made privacy nutrition labels change behavior, and it is likely to move the industry faster than the EAA deadline did.

**The label is a list of claims that have to be true, so each becomes a testable requirement.** Build the list into the definition of done.

iOS 26 also added **Accessibility Reader** (a system-wide reading mode launchable from any app, with font, color and spacing controls) and **Braille Access**.

## Older adults

A large, growing and routinely ignored group. The systematic reviews converge on the same barriers and fixes.

**Barriers, in the order research finds them:** small type, complex navigation, unintuitive interaction models, cognitive overload, varying digital literacy.

- **Larger default type and larger targets** – designed for, not delegated to Dynamic Type. A layout that only works at the default size and breaks at 200% has moved the problem to the user's settings, not solved it.
- **Simplify navigation depth.** Flat beats nested. A path requiring the user to remember where they came from fails first.
- **Error tolerance over error prevention.** Confirmations before destructive actions, undo everywhere, forgiving input (accept a phone number in any format rather than rejecting it).
- **Voice interaction** is repeatedly identified as effective, both as input and for reading content back.
- **Reduce reliance on gesture-only paths.** This group is where "every gesture needs a visible alternative" stops being theoretical.
- **Stable layout.** Controls that move between screens cost this group more than any other.
- **Co-design.** Participatory methods measurably improve usability and satisfaction – the strongest finding in the literature and the one most teams skip.

**Almost every item here also helps users with motor impairments, users in a hurry, users on a bumpy train, and users unfamiliar with the product.** Designing for older adults is one of the highest-leverage accessibility investments there is, and it is rarely framed as accessibility at all.

## Designing for children

The legal frame moved recently, and it changes design rather than just the privacy policy.

- **COPPA** requires verifiable parental consent before collecting, using or disclosing personal information from children under 13.
- The **2025 rule update widened what counts as "directed to children"**: user reviews mentioning children, the age profile of similar apps, and marketing materials are now factors. **An app can be classified as child-directed because a competitor's similar app has children using it.**
- The **FTC policy statement of 25 February 2026** encourages age-verification technology and permits running an age check *before* parental consent, provided the data is used only for age determination, deleted promptly, disclosed clearly, secured, and reasonably accurate.
- A compliance deadline of **22 April 2026** applies to the updated rules.

Design consequences:

- **The age gate is a real screen with real requirements.** Neutral (a date-of-birth entry, not "are you over 13?" with an obvious right answer), not repeatable until the user gets the answer they want, and its result must persist.
- **Mixed-audience apps need a branch, not a compromise.** Under-13 users get the restricted experience: no behavioral advertising, no third-party analytics, no social features exposing personal information.
- **Consent flows are for parents, so write them for adults**, in plain language, on a screen a child is unlikely to complete by accident.

Interface guidance for children's apps, which differs from adult UI in specific ways:

- **Limit choices to roughly 5–7 per screen.** Children are overwhelmed by dense menus faster than adults.
- **Icons plus words**, with familiar imagery. Reading ability varies enormously across the age band an app targets.
- **Larger targets than the adult minimum.** Fine motor control is still developing; 60 pt is a reasonable floor for a primary control.
- **Keep layout consistent across screens.** Children learn positions rather than labels, so moving a control costs more here than with adults.
- **No dark patterns, at all.** Timed offers, streak pressure and confusing dismiss buttons are unacceptable in a children's product and are increasingly enforcement targets rather than ethics debates.
- **Parental gates on anything outbound** – purchases, external links, sharing. The standard implementation is a task a young child cannot complete, not a password.
