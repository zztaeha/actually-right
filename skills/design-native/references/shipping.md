# Shipping

Getting in, and staying in. Store presence, first run, monetization, and the compliance floor around all of it.

# Store presence

## App icons

| | iOS | Android |
|---|---|---|
| Master | **1024 × 1024, fully opaque, no alpha** | **Adaptive: foreground + background, each 108 × 108 dp** |
| Mask | System applies a **squircle** | Manufacturer-dependent: circle, rounded square, squircle, other |
| Safe area | – | **Only the inner 66 dp is guaranteed visible** |

- **Design at 1024 px, then check legibility at ~40 px** – the size it is actually seen at in search.
- **The strongest-performing icons carry a single recognizable element on a clean background:** no clutter, no secondary symbols, no tiny text.
- Icon quality is reported as the top factor in tap-through from search, with a well-designed icon improving listing conversion **15–30%**. Vendor-sourced, directional.
- **On Android, 22% of your artwork can be cropped away by a manufacturer mask you do not control.**

**iOS 26 changed the pipeline.** The submitted asset is still a single opaque 1024 × 1024 square, but the system composites it as **layered glass with depth, reflection and light response**, and generates light, dark and tinted variants automatically. Icon Composer supplies the layers.

Two consequences: **a mark built from a few bold shapes survives the glass treatment and a detailed illustration does not**, and the icon can no longer be designed as a fixed picture – **it has to be designed as a subject that holds up under a material effect it does not control.**

Users can also tint, darken, enlarge and unlabel icons system-wide. See `foundations.md`.

## Screenshots

- **Three to five is the practical set.** The first three carry the install decision, because they show in search and on the product page before anyone taps through.
- **The 60/40 heuristic:** at least 60% of frames showing real app UI, no more than 40% lifestyle or marketing.
- **Platform split: iOS rewards lifestyle framing, Android rewards feature clarity.**
- Wrap real UI in a **current device frame**, keep type readable **at thumbnail size**, keep the set visually consistent.
- **Apple OCRs screenshot text and indexes it**, so the words on the images are searchable content, not decoration.
- Narrative order that recurs: **establish the outcome → show how it works → prove breadth or trust → give a reason to act.**
- Base sizes: **1320 × 2868 (6.9" iPhone)**, **2064 × 2752 (13" iPad)**. Apple downscales for smaller devices.

**Google Play feature graphic: 1024 × 500, JPEG or 24-bit PNG, no alpha, under ~2 MB.**

- **When a promo video exists, Google overlays a play control on the center.** Compose with the center left as the video's stage – product slightly off-center, headline in the upper third – so it reads correctly in both states.
- It is a **mood and personality asset**, not a feature demo, and it may be cropped, so nothing critical near the edges.

## What the stores reject

- **Placeholder "Coming Soon" screens, non-functional buttons and Lorem Ipsum** – one of the most common first-time rejection causes.
- **Metadata mismatch:** screenshots showing UI not present in the build. Every claim must match what the reviewer sees running.
- **Minimal functionality**, including web wrappers with no native value. **Apple removed over 42,000 apps in 2024 specifically for design violations.**
- 2026 tightening: data transparency, AI disclosures, user experience and clear pricing.

**Two design consequences.** The placeholder rule makes **"design all five states" a shipping requirement**, not a nicety – an empty state saying "Coming soon" is a rejection. And **screenshots are part of the product**, not marketing that can run ahead of the build.

## Store-page conversion

- **Users decide in under seven seconds**; the best assets communicate value in under two.
- Visual assets – icon, screenshots, preview video, feature graphic – are the primary drivers of conversion rate.
- A/B test via Store Listing Experiments or ad campaigns. **Copy first, then placement.**

## Launch screens

The two platforms disagree, and following the wrong one gets an app rejected.

**iOS:** the launch screen is a **static skeleton of the first screen**, not a brand moment. Mirror the first screen's layout without dynamic content, and avoid text, logos and taglines. Text cannot be localized on a launch screen, which is the stated reason. **A branded splash with an animated reveal is a rejection cause.** The intended effect is that the app *appears* rather than loads.

**Android:** the opposite. The Android 12+ Splash Screen API is a system-level splash with a centered app icon on a configurable background, and it is **the sanctioned brand moment**. Opting out and building a custom splash Activity is now the wrong pattern, because the system splash shows anyway and the app gets two.

Shared rules:

- **Under two seconds, and no artificial delay.** Abandonment is reported at 12% above two seconds; even discounting the figure, an added delay is indefensible.
- **Never animate a logo into a load.** If the app is ready in 400 ms, an 1,800 ms animation is 1,400 ms of deliberately wasted user time.
- Correct in both color schemes, at every size and orientation.
- If real loading is unavoidable, get past the launch screen into the app's own skeleton UI where progress can be shown honestly.

## App size

**Size is a conversion variable, not just an engineering metric.**

- **Google's own figure: every 6 MB of download size costs roughly 1% of install rate.**
- **Android App Bundles cut download size 15–20%.** Adobe reported a **5–7% install conversion increase** from bundles with dynamic delivery.
- **The effect is regional.** In markets with expensive data and low-storage devices, removing 10 MB is reported at around **2.5%** – roughly 2.5× the global average. If the product has growth ambitions in India, Brazil or Indonesia, **size is a growth lever rather than hygiene**.

Where the design decisions land:

- **Raster assets are almost always the largest removable chunk.** Vector where possible; one raster set rather than five when the platform can scale.
- **Fonts.** A custom family at four weights with Latin-Extended plus Cyrillic plus Greek is easily several megabytes. **Subset it.**
- **Onboarding videos and Lottie files** are worth their size only if they measurably help, and they are usually not measured.
- **On-demand resources** (iOS) and **dynamic feature modules** (Android) let large, rarely used assets download later.

## Alternative distribution in the EU

The DMA opened iOS to alternative marketplaces and web distribution in the EU. Terms have moved twice: a layered fee model in January 2026, replaced on **18 August 2026** by a single set of business terms for every EU developer, eliminating the Core Technology Fee, Initial Acquisition Fee and Store Services Fee. What a developer pays now depends on **where the payment happens** (Apple IAP, alternative in-app processing, web checkout) and **how the app is distributed** (App Store, alternative marketplace, own site).

The ecosystem is real but unsettled – alternative stores are shipping, and at least one shut down in February 2026 citing complex, still-evolving terms.

Design consequences, which outlast the fee schedules:

- **The install flow is no longer one flow.** A user arriving from the web, a marketplace, or the App Store lands differently and needs different framing.
- **Trust signals move onto the developer.** Outside the App Store the implied vetting is gone, so the product's own site and first run carry the credibility.
- **Payment choice becomes an interface decision.** Present one recommended path and keep the others available, rather than a menu of payment rails.
- **Updates are your problem** outside the App Store, which makes an in-app update prompt a designed screen rather than a system affordance.

# First run and growth

## Onboarding

**The goal is not to explain the app. It is to get the user to one meaningful action, once.**

- **The first session loses 70–90% of users; median day-1 retention is around 25%.** Everything about the first run fights that number.
- **Total flow 60–120 seconds**, roughly five stages. Longer loses people who have not yet committed. That is stricter than the web's two-minute time-to-first-value, because on native the user already paid the install cost and expects to have arrived.

Rules:

- **Value before signup.** Let the user see or do something before the account wall. Forced registration is the highest-drop-off screen in most apps.
- **Progressive disclosure over a tour.** Teach a feature when it becomes relevant, not in a launch carousel. Carousels are skipped and nothing is retained.
- **Learning by doing.** The step advances when the user performs the action, not when they tap Next.
- **Ask for the minimum.** Two or three segmentation questions at most; **five to seven only if the answers visibly shape the output.** Make the personalization visible, or the questions read as data collection.
- **Always offer skip.** A skip link costs a little onboarding completion and saves a much larger amount of abandonment.
- **Defer permissions.** Never at launch. See `surfaces.md`.
- **Passkey signup is the default expectation.** Apps lacking it are already behind on conversion benchmarks.
- **Platform-native auth matters.** A web-wrapped flow on either platform reads as low-effort and hurts trust from the first screen.
- **Instrument every step**, because each screen is a discrete drop-off point.

Category-shaped approaches: habit apps go goal-first (Duolingo); content apps personalize (Headspace); subscription apps run longer with social proof (Calm); complex tools use motion-guided walkthroughs (Notion).

**The measurable target is time-to-first-value, not onboarding completion.** An onboarding that completes but produces no first action has failed.

## Paywalls

Eight elements carry most of the measured variance: **headline, price display, default plan selection, trial length, CTA copy, benefit order, timing, visual hierarchy.**

- **Place the paywall at the end of onboarding**, after a quiz, a goal, or a preview. Small interactive steps before it increase emotional investment and reduce drop-off.
- **Highlight one plan.** Badge it, position it, contrast it. An undifferentiated grid is a decision the user will defer, and deferral is abandonment.
- **Use the smallest set of plans that represents meaningful choices.** One removes comparison, two separates flexibility from savings, three needs a real reason.
- **Comparison tables are near-universal** because a significant share of users at the paywall still do not understand what they are paying for.
- **Benefit-led CTA.** "Start my plan" beats "Subscribe"; wording changes alone are reported in the 10–20% range.
- **Show annual savings explicitly**, as a percentage or a per-month equivalent, next to the monthly option.
- **Progressive disclosure works.** Duolingo's shape – value screen, then feature comparison, then trial terms, then price – reduces overload versus leading with price.
- **Motion helps.** Animated paywalls are reported at roughly **2.9× static conversion**. One of the few places decorative animation has a defensible business case.
- **Message continuity.** Ad, onboarding and paywall should make the same promise in the same words.
- **Nine in ten subscriptions sell at full price.** The instinct to discount when conversion is low is usually wrong.
- Post-close welcome offers (a 24-hour discounted annual) are standard enough that not having one is unusual.
- **Remote paywall configuration** (Adapty, RevenueCat, Superwall) ships variants without resubmission – one of the clearest process advantages available to subscription teams.

All vendor-sourced and directional. **The ethical line:** highlighting a plan is persuasion; obscuring the cancel path or the real renewal price is a dark pattern and increasingly a regulatory problem.

**Store rules constrain the design too:** price, billing period, and what happens after the trial must all be visible on the paywall itself, and a restore-purchases path must exist. These are rejection causes, not suggestions.

## Cancellation, and the regulatory floor

**The standard is one sentence: cancelling must be at least as easy as subscribing.** Three taps to sign up means three taps to cancel.

The legal state as of 2026 is messy and worth knowing accurately:

- The FTC finalized its click-to-cancel amendment in **October 2024**; a federal appeals court **vacated it in 2025** on procedural grounds. The FTC opened a **new rulemaking in March 2026** to revive it.
- **Roughly 30 states have their own automatic-renewal laws**, several stricter than the vacated federal rule, and **New York City's own click-to-cancel rule takes effect 1 October 2026**.
- Civil penalties run to tens of thousands of dollars per violation.

The federal rule is in limbo and the requirement is effectively in force anyway through state law, city law and store policy. **Design to the stricter standard.**

- **Cancellation lives where the subscription lives**, in the app's own account settings, not only in a help article pointing at the App Store.
- **No retention maze.** One offer, declinable in one tap, is the outside limit. A sequence of "are you sure" screens, a survey and a chat handoff is the pattern regulators name explicitly.
- **State the terms plainly at the point of purchase.**
- **Send a renewal reminder** before charging for a long period. Required in several jurisdictions, and it prevents the chargeback and the one-star review regardless.
- **Confirm cancellation in writing**, with the date access ends.

## Review prompts

Both platforms cap and gate this, so the only design question is *when*.

- Apple allows at most **three prompts per 365 days**, and **Apple, not the app, decides whether the prompt appears.** Same for Google's in-app review API.
- **Ask after a completed success**, never on launch and never mid-task. This is the peak-end rule applied directly: the rating reflects the moment of asking, not the average experience.
- A reasonable qualification gate: installed 7+ days ago, 2–3+ sessions, last session crash-free, not prompted in the last 90 days.
- **Never gate the prompt behind a "do you like the app?" pre-question** that routes unhappy users to a private form. Both stores prohibit it.

## Gamification, and where it turns

Streaks work because they weaponize loss aversion. That is also why they cause harm: research on streak mechanics reports **guilt, anxiety and compulsive checking as measured outcomes**, not speculation.

| Legitimate | Manipulative |
|---|---|
| Progress toward a goal the user set | Progress toward a goal the app set for its own metrics |
| Streak freezes and planned breaks | Losing months of progress for one missed day |
| Rewards for real accomplishment | Rewards for time spent |
| Notifications the user can tune | Escalating notifications engineered around loss aversion |
| Visible, honest counters | Counters that reset in ways the user cannot predict |

Duolingo is the usual cited example of the defensible version, and the specific mechanism is the **streak freeze**: it acknowledges that life interrupts, which converts the streak from a threat into a record.

**The business case for restraint:** gamification done well lifts 30-day retention; done badly it draws FTC attention, refunds, and a trust problem that outlasts the retention bump. And the user who quit because a streak made them anxious does not come back.

## Dark patterns are now law

The design-ethics conversation moved into statute, and **regulators now reason about specific interface mechanics**, not about intent.

- **EU Digital Services Act, Article 25** prohibits three categories of autonomy violation: **deception, manipulation, and distortion or impairment** of a user's ability to choose freely. Applied to very large platforms since 2023 and more broadly since **17 February 2024**.
- Academic work maps **59 known dark patterns onto those three categories** and identifies eight design factors that determine when a pattern violates autonomy – a systematic tool rather than a judgment call.
- The European Commission has announced a **Digital Fairness Act for Q4 2026**, intended to cover dark patterns, **addictive design**, misleading influencer marketing and unfair personalization, and to put a single agreed definition into EU law.
- FTC activity on negative-option and cancellation flows is the same idea through a different door.

What to take from it:

- **"Addictive design" is being named as a regulated category**, which reaches directly into streak mechanics, infinite scroll and notification pressure.
- **The unit of enforcement is the pattern, not the outcome.** A confirmshaming button, a pre-checked consent box, an obscured decline, a countdown that resets – each is identifiable on a screenshot, which makes compliance a design review rather than a legal one.
- **Build the review into critique.** Add one question to every flow: is there any point where the easy path and the user's interest diverge, and is the divergence hidden?

# Benchmarks

Useful for calibrating expectations and arguing about targets. **All directional** – aggregates across very different products.

**Retention.** More than **90% of users are gone before day 30.** iOS day-1 averages around **25.4%** falling to **5.3%** by day 30; Android **20.2%** falling to **3.8%**. Median across categories is near **4% at day 30**; strong performers (75th percentile) sit at **5–8%**.

| Category | Day 30, strong performers |
|---|---|
| Social | 15–20% |
| Productivity | 10–18% |
| Fintech | 10–15% |
| Gaming | 4–8% |
| Ecommerce | 3–6% |
| Education | ~2% |

And a structural one worth citing: **subscription apps retain around 14% at day 30 against 5.4% for ad-monetized apps.** Business model shapes retention more than most design changes do.

**Push opt-in:** iOS around **44–54%**; Android nominally 91–97% but inflated by legacy installs. See `surfaces.md`.

**Cold start, jank and app size** thresholds are in `performance.md`.

**How to use these:** as a sanity check, not a target. A product at 2% day-30 in a category where 12% is normal has a problem worth finding. A product at 6% in ecommerce is doing well, and chasing social-app numbers there is chasing a different business.
