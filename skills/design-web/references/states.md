# States and feedback

**What a thing does when something happens.** The component catalog is in `components.md` – a form's structure lives there, its validation timing lives here.

## The five states, and the sixth everyone forgets

Every component needs **default, hover, focus, active, disabled** – plus, for anything that fetches or mutates, **loading, empty, error, success, partial or stale.**

**The rule that catches the most real bugs: `:hover` without a matching `:focus-visible` means the interaction is invisible to keyboard users.** If a hover style is the only affordance, the component is broken for a whole class of users, and **it will not show up in any visual review.**

## Empty states

**An empty state is not a blank screen. It is the screen with the most attention available and the least competition for it.**

| Type | Job |
|---|---|
| **First use** – nothing created yet | Teach the model and give one clear action. **The best onboarding surface in the product** |
| **No results** – search or filter returned nothing | Explain *why* – which filter, which term – and offer a way out: clear filters, broaden, suggest alternatives |
| **User-cleared** – inbox zero, all tasks done | Acknowledge it. A legitimate place for a small moment of delight |
| **Error** – it failed | Say what happened and offer recovery |

- Explain why it is empty in plain language. **Never ship "No data" or "Nothing here yet" with no context.**
- Friendly tone that does not blame the user.
- **One** clear next action.
- Keep it sparse: icon, title, one line, one CTA, max-width around 320 px.
- **Announce the change to screen readers.** A list going empty is a state change they otherwise miss.

## Error messages

- **Place the error next to the thing that caused it.** A summary at the top of a form forces the user to scan the whole interface to find the problem. **A top-of-form summary is still useful *in addition*, for screen readers and long forms – not instead.**
- **Say what happened, why, and what to do next**, in the user's vocabulary. No stack traces, no error codes as the primary message, no jokes, no pop-culture references.
- **Preserve the user's input.** Losing typed data on an error is the most punishing thing a form can do.
- **Recovery must be specific to the error class:** network → check connection and retry; auth → reset password; payment → which method, what to change; validation → what format is expected, shown as an example.
- Include a **retry** for transient failures and an escalation path for persistent ones.
- **Measure it:** recovery rate, abandonment, downstream impact. **An error state nobody escapes from is a broken feature, not a message problem.**

## Validation timing

**On blur wins.** Across 80+ implementations tested, on-blur beat both alternatives more often than they beat it combined.

| Timing | Effect |
|---|---|
| **On focus, or while empty** | **Premature.** Flags a field before the user has typed. Causes **error blindness** – users stop trusting the messages. Reported **9–16% conversion drop** vs on-blur |
| **On every keystroke** | Flags `j` as an invalid email before `@` is typed. Interrupts flow, feels hostile |
| **On blur** | **The default.** The user has declared the field finished |
| **On blur + 300–500 ms delay** for format-constrained fields | Best of both where the format is checkable – email, phone, card |
| **On submit only** | Too late. The user has to go back and re-navigate |

**Once a field has errored, switch that field to live validation** so the user sees it clear as they fix it. **Error → strict; clean → lenient.**

Inline validation is reported to cut error rates ~22% and completion time ~42%. Secondhand, directional.

**Real-time validation can itself be an accessibility problem** – announcements firing on every keystroke flood screen reader users. **Debounce, and use `aria-live="polite"`.**

## Disabled controls – an anti-pattern more often than not

**The contrast dilemma is unsolvable as posed:** raise contrast enough to be readable and it no longer looks disabled; keep it looking disabled and it fails contrast. **That is the signal that the pattern itself is wrong.**

Worse, a `disabled` button is **removed from the tab order**, so a keyboard or screen reader user may never discover it exists and gets no explanation of why they cannot proceed.

| Instead of disabling | Do |
|---|---|
| Disabled submit until the form is valid | **Keep it enabled.** On click, validate and move focus to the first problem. The user learns what is wrong instead of guessing |
| Disabled because of state | **`aria-disabled="true"`** – stays focusable and announced, so the user can find it and hear why |
| Disabled "Out of stock" | Replace with a **useful** action: "Notify me", "See similar" |
| Disabled because of permissions | Show it with an explanation, or hide it entirely |

**Hidden vs disabled:** hide when the user can never have it – wrong role, wrong plan, and it is not a sales moment. Disable and explain when the user could have it under some condition they can reach. **A disabled control with no explanation is the worst of both.**

**If disabling is unavoidable, always pair it with the reason** – tooltip, adjacent text, or an error on attempted use.

## Destructive actions

> **Friction proportional to damage × irreversibility.**

- **Undo beats confirmation wherever it is technically possible.** It costs the user nothing in the 99% case and saves them in the 1%.
- **Confirmation is for the genuinely irreversible:** permanent deletion (not "move to trash"), account closure, anything that leaves your system.
- **A blanket "Are you sure?" trains users to click through without reading**, so the one confirmation that mattered gets dismissed on autopilot. **Effectiveness depends on rarity. Over-confirming actively makes the product less safe.**
- **Never assign a destructive action to the primary button role.** People select primary buttons without reading the label. **The primary should be the safe, non-destructive choice.**
- **Separate consequential options from benign ones spatially**, and differentiate with redundant signals – color plus label plus position. **Adjacent Save and Delete buttons is a design defect.**
- **Type-to-confirm** ("type the repo name") is the right level only for the most severe irreversible actions. Anywhere else it is theater.
- **For infrequent tasks the user may not be watching closely, a confirmation can beat an easily-missed undo toast.**

## Toasts

| Property | Value |
|---|---|
| Duration | **2–3 s** short confirmations, **5–6 s** with an action. Formula: 5 s + 1 s per 120 words, 6 s floor when in doubt |
| Placement | Top-right on desktop, **bottom-center on mobile** – thumb reach, and it avoids the notch |
| Info | `role="status"` + `aria-live="polite"` – waits for the screen reader to finish |
| Error | `role="alert"` (`aria-live="assertive"`) – interrupts, because it must |
| Dismissal | Keyboard-dismissible; **hover pauses the timer** |
| Contrast | 4.5:1, same as everything else |
| Motion | Respect `prefers-reduced-motion` |

**Never put anything critical or irreversible only in a toast.** It disappears, and a screen reader user or a distracted user will miss it. **Toast is for non-blocking confirmation. Anything that must be acted on belongs inline.**

## Focus management

The complete modal pattern, all four parts required:

1. **Store** the element that had focus before opening.
2. **Move** focus into the dialog on open.
3. **Trap** Tab and Shift+Tab inside while open.
4. **Restore** focus to the trigger on close.

**Escape must close and restore.** `<dialog>` with `.showModal()` gives the trap, Escape and the inert backdrop for free – **the correct default, and it eliminates most hand-rolled bugs.** Focus restoration is still worth verifying.

**The same principle applies to every transient surface** – popovers, drawers, menus, comboboxes. **When it closes, focus returns to what opened it.** Focus landing on `<body>` after a dialog closes drops a keyboard user at the top of the page.

## Loading

### Skeleton implementation

- **Placeholder shapes must match the real content's dimensions.** If they do not, the swap causes a layout shift – **the skeleton then creates the CLS it was meant to hide.**
- Shapes correspond to content type: rounded rectangles for images, lines for text, **with realistic line lengths – last line shorter.**
- **Shimmer: a `linear-gradient` overlay animated left to right, 1.5–2 s cycle, slow and gentle.** Faster reads as agitated.
- **Respect `prefers-reduced-motion`** – drop the shimmer, keep the shapes.
- Proper ARIA (`aria-busy`, a live region on completion) and **a defined error fallback. A skeleton that never resolves is the worst loading state there is.**

Whether skeletons help at all is genuinely unresolved. See `motion.md`.

### Streaming – the strongest perceived-performance result found

**Users perceive streaming interfaces as ~40% faster than buffered ones even when total time is identical.** One reported deployment: first-token latency down ~90%, **perceived speed rating 4/10 → 9/10 with completion time barely changed**, and **drop-off after prompt fell from 22% to 7%.**

- **TTFT (time to first token) is what the user experiences as "the wait".** TPOT (time per output token) governs how the rest feels.
- **Below ~12 tokens/s the stream falls behind reading speed** and reads as stalling.
- **Speed variation above ~30% degrades perceived fluency** – a stream that stutters feels worse than a slower steady one. **Consistency beats speed**, the same finding as animation frame pacing.
- Engagement suffers past **~1.3 s** before anything appears.

**The clearest proof of the general principle: showing progress beats being fast.** When people see something happening, they wait. When it is just a spinner, they bail.

## Saving

| Pattern | When |
|---|---|
| **Autosave** | Document editors and anywhere the user expects the change to be instant and continuous. It protects work and removes a chore |
| **Explicit save** | Forms, settings, anywhere the user needs to **review before committing**. In most cases the best practice is an explicit save action |

- **User expectation is the deciding factor, not efficiency.** People have learned that forms do not commit until Save is pressed, and that navigating away discards. **Violating that is the failure, however efficient it is.**
- **Keep the Save button even with autosave.** Users tend to panic when there is no Save and close. It can do nothing but close and still provide the reassurance – a rare, genuinely useful redundant control.
- **Never mix save patterns on one page, or worse, in one form.** Mixed models are how data gets lost.
- **Autosave needs a visible state:** saving / saved / failed, with a timestamp. **Silent autosave is indistinguishable from silent failure.**

## Optimistic UI

Update as though it succeeded, roll back on error. **For actions that rarely fail this creates a dramatically faster-feeling product with no engineering performance work at all.**

**The cost: rollback is a design problem, and a wrong optimistic state erodes trust permanently.** Design the rollback before shipping the optimism, and give the failure a distinct restore animation rather than a paused one.

## Notifications

- **Two surfaces, one system:** transient **toast** for what just happened, persistent **inbox** for what happened while away.
- **Designed for scanning, not reading** – strict title / body / timestamp hierarchy, **one quiet unread signal**, and grouping.
- **Group by source or object.** Linear groups by issue and project, Notion by page and role. **No more than 3–4 categories.**
- Badge counts unread; **hide the badge at zero** rather than showing a 0.
- **Read state must sync everywhere.** Marking read in one tab and not another is the defect people notice most.
- **The design question that decides everything: what deserves to interrupt versus what deserves to accumulate.** Most products get this wrong by interrupting for things that could have waited.

## Dark mode – the toggle

**Three-tier precedence, in order: explicit user toggle → saved preference → OS setting.**

1. Detect with `prefers-color-scheme` and **default to the OS**. Apple and Microsoft both recommend mirroring the system setting.
2. Offer an in-app override.
3. **Persist it**, and **make first paint correct** – a flash of the wrong theme is the classic implementation failure.

Lea Verou's counterpoint is worth recording: **the best dark mode toggle is probably none** – respect the system setting and add no UI. The counter-argument is that people's needs shift through the day and the OS setting is a blunt instrument.

**The accessibility nuance settles it: dark mode is not universally better.** Users with astigmatism often find it *harder*; users with light sensitivity benefit enormously; color-vision-deficient users vary. **Because the population is genuinely split, the override should exist** – and it should not be a three-way control hidden in a settings page nobody opens.

Palette construction for dark mode is in `color.md`.

## Trust

- **First impression forms in ~0.05 seconds, and it is primarily visual.** Stanford's web credibility work found people judge trustworthiness mainly on **visual design and layout**.
- **Say this plainly to a stakeholder: visual quality is not decoration, it is the first credibility signal, and it fires before a single word is read.**
- Concrete signals: consistent color, type and messaging; clear navigation; fast load; mobile-friendliness; **real names and photos in testimonials rather than anonymous quotes**; verified third-party reviews; visible contact information; current content.
- The negative version: **if your site looks sloppy, people think you have a sloppy business.**

## Dark patterns – now a legal matter

- The **FTC treats dark patterns as deceptive or unfair practice under Section 5** of the FTC Act. 2023 action against Amazon over Prime cancellation – the "roach motel".
- **GDPR:** consent must be freely given and informed. **Obscured opt-outs or pre-selected consent invalidate the consent.**
- **CCPA/CPRA** explicitly prohibits dark patterns that interfere with privacy choices.
- 2026 status: UX decisions are under legal scrutiny, especially where they manipulate users into giving consent or make it harder to refuse.

**The named patterns to refuse:** roach motel (easy in, hard out), confirmshaming, pre-checked consent, disguised ads, fake scarcity and urgency, hidden costs revealed late, **visual weight imbalance between accept and reject.**

> **The effort to decline must be comparable to the effort to accept.**

**That single test catches most consent dark patterns, and it is what regulators are actually measuring.**

Add it to critique: walk every flow asking where the easy path and the user's interest diverge, and whether the divergence is hidden.
