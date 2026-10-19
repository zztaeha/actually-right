# Patterns and craft

The component-level decisions, plus lifecycle and offline. Screen types by product category are in `categories.md`.

## What "polished" actually means

> **A native app feels polished not because it looks fancy, but because nothing feels slightly off. Users rarely say "this app has abstraction overhead". They say "it feels clunky", then they leave.**

That is why cross-platform toolkits get judged harshly: the failure is never one big thing, it is **fifty small mismatches with platform expectation** – scroll physics, keyboard behavior, back semantics, haptic absence, transition timing – each individually deniable.

The 2026 list of what actually signals native craft: **Live Activities, App Intents, passkeys, on-device models, haptics, App Clips, Liquid Glass adoption.** Live Activities and interactive widgets buy back retention; App Intents buys distribution.

## Scroll physics – the invisible platform tell

**iOS tracks 1:1 with the finger and absorbs overscroll gracefully. Android stops more abruptly with an edge effect.** Custom scroll physics, or a web view's default momentum inside a native shell, is the clearest example of "nothing feels slightly off" that users cannot name.

**Use the platform scroll view untouched.** This is not a place to express brand.

## Sheets, modality and detents

The question is not "modal or not" but **how much of the user's context you can afford to take**.

| Need | Surface |
|---|---|
| A quick choice or short set of options | **Bottom sheet**, in the thumb zone, context visible behind |
| Supplementary detail about what is on screen | **Non-modal sheet** |
| A short focused task, 2–5 fields | **Medium-detent sheet**, escalating to full if the keyboard opens |
| A complex or multi-step task | **Full screen**, with a clear cancel and a clear commit |
| A destructive or irreversible confirmation | **Alert**, because it must block |
| A whole new context – onboarding, purchase | **Full-screen modal** |

- **System detents are `large` and `medium`; iOS 16+ supports custom detents.** Support `medium` for progressive disclosure – a share sheet shows the most relevant items without resizing. **Skip `medium` when the content needs full height**; Mail and Messages compose only at full height because the task needs the room.
- **Include a grabber on a resizable sheet.** It signals draggability **and cycles through detents on tap** – a discoverable affordance for a gesture.
- **A sheet leaves part of the parent visible, which is what preserves context.** That is the whole argument for a sheet over a pushed screen.
- **A centered dialog box is a desktop pattern.** On a phone the equivalents are the bottom sheet and the full-screen modal.
- **Escalate rather than guess.** Start as a sheet, let it expand when the user needs room. That is what detents are for.
- **Never interrupt an active task with a full-screen modal.** Let them finish. This applies to review prompts, upsells and rating flows.
- **Modality must be dismissible the platform's way** – swipe down on iOS sheets, system back on Android. Blocking either is a bug users feel immediately.
- Pair with origin-aware motion and interruptibility. **A sheet you can catch mid-dismiss and throw back is the single clearest "this feels native" signal there is.**

## The in-app message hierarchy

Four surfaces, and the mistake is almost always using a lighter one than the message deserves.

| Surface | Persists | Actionable | Use for |
|---|---|---|---|
| **Toast** | No, auto-dismisses | No | Passive confirmation: "Copied", "Saved" |
| **Snackbar** | No, but longer | **Yes, one action** | Confirmation with **undo** |
| **Banner / inline** | **Yes, until dismissed or resolved** | Optional | Ongoing state: offline, degraded service, expired payment |
| **Alert / dialog** | Yes, blocking | Required | A decision that must be made now |

- **Never put anything the user must see or act on in a toast.** It vanishes, it can be missed while looking away, and it cannot be recovered. The most common instance of this failure.
- **Banner for anything ongoing.** "You are offline" is a state, not an event.
- **Alert only for a real decision.** An alert used for information is an interruption with an OK button, and it teaches users to dismiss alerts without reading – which is what makes the next, genuinely important alert fail.
- **One at a time.** Queue them, and drop the ones no longer relevant by the time their turn comes.

## Swipe actions

**Trailing swipe (right-to-left) is conventionally destructive; leading swipe (left-to-right) is conventionally non-destructive.** Breaking that puts Delete where the user expects Archive.

- Use when the user can decide from the information already visible in the row – delete, archive, flag.
- **Do not use when the decision needs more context.** Then it belongs in the detail view or a menu.
- **One primary, visually prominent swipe action per row.**
- **Every swipe action needs a non-swipe path** – a tap-reachable menu or a named accessibility action. WCAG 2.5.7 and 2.5.1. Not optional.
- Watch the system gesture conflict at screen edges. See `structure.md`.

## Shared element transitions

Google Photos and the App Store use these heavily because they remove one of the most common causes of disorientation in deep navigation. **When a thumbnail expands into a detail screen, growing from its list position to fill the viewport, the user understands exactly what happened and how to return.**

- **iOS 18+: `navigationTransition`.** Compose: shared element transitions.
- Reported to reduce cognitive load and increase retention by up to 40%. Secondhand, directional.

**What it buys is continuity of identity** – the thumbnail *becomes* the hero, so the user never loses the thread. Both platforms ship it as a primitive, which removes the last excuse.

## Context menus

Long-press reveals **actions alongside a content preview** – better than a flat action list because preview and commands are visible together.

- **Prefer a graphical preview that clarifies the target of the commands.** Notes and Mail show a condensed version of the actual content.
- **The layout reconfigures by context:** iPhone portrait stacks preview above actions, landscape places them side by side, iPad adapts to available space.
- Supports child actions and dividers. It is a real menu.
- The preview is tappable to open and draggable to another target.

**The preview is the design opportunity most apps waste.** A context menu whose preview is just the row again teaches nothing; one showing the *content* answers "which item is this?" before the user commits.

## Keyboard handling

> **Keyboard handling belongs in product design, not inside a utility component.** When someone focuses a field, the software keyboard appears and primary actions can disappear below the fold.

- **Ensure the focused field scrolls into view.** Test it; do not assume.
- **Sticky submit above the keyboard**, and never put critical controls at the very bottom where the keyboard will cover them.
- **Trigger the correct keyboard per field** – numeric for phone and codes, email for email. Free, and it removes several taps.
- **3–5 fields maximum in a single step; past about 7, split into steps.**
- Single column. Avoid dropdowns where a better control exists.
- On a chat or composer screen, **the message list must translate with the keyboard in the same frame it animates**, not after. Anything else reads as a stutter every time the user types.

**This is a native-only failure mode with no web equivalent**, because the web keyboard does not resize the viewport the same way. It is also one of the most common reasons a form feels clunky.

## Forms

- **Autofill everything the platform offers.** `textContentType` on iOS, `autofillHints` on Android. Reported at roughly **−25% errors and +20% conversion**.
- `autocapitalize` set to words for names, off for email and URLs. Wrong capitalization on an email field is a small thing that reads as carelessness.
- **16 px minimum font size on inputs** in any embedded web view, or iOS zooms the page on focus.
- Mobile-optimized forms are reported at **86% completion against 34%** for non-optimized ones. Directional, but the gap is the argument for spending real design time here.

### Validation timing

**Validate on blur, not on every keystroke.** Validating while the user is still typing marks a half-typed email as wrong, which is both annoying and inaccurate.

- **After a field has failed once, switch that field to validating on input**, so the error clears the moment it is fixed.
- **Clear the error as soon as the input is valid.** The disappearance is the confirmation.
- **Errors sit directly below the field**, not above and not in a summary at the top – a summary is off-screen by the time the user is typing.
- **Say what is wrong and how to fix it.** "Password must be at least 8 characters" beats "Invalid input".
- **Never clear the user's input on error.** Losing typed data at the moment of failure is the most infuriating form behavior there is.
- **Reveal errors sequentially** rather than lighting up eight fields at once, which reads as failure rather than guidance.
- Animate the error in over **200–300 ms**.

### Date and time pickers

Pick the control from the task, not from habit:

| Task | Control |
|---|---|
| Birth date, any date far from today | Wheel or masked text entry; a calendar makes you page through years |
| A date near today | Calendar with today visible and the next weeks reachable |
| A date range | Calendar with range selection, both ends visible |
| A time from a limited set (reservations) | **Tiles or a list of available times**, which also shows what is unavailable |
| An arbitrary time (alarm) | Wheel or dial, **plus manual text entry** |
| Start and end together | Dual-handle slider – a wheel holds one value |

Recurring failures: small day-cell targets, no way to jump years, no keyboard alternative to the dial, and month/year dropdowns that are miserable with a thumb.

Default to something sensible rather than an empty field, **disable rather than hide unavailable dates** so the constraint is visible, show the resulting selection in text near the control, and put the picker in a bottom sheet so it lands in the thumb zone.

### Authentication

The chain, best to worst: **passkey → biometric prompt → email magic link → email OTP → SMS OTP last.**

- **Auto-triggering biometric enrollment lifts adoption 30–50%.** Directional.
- **Jargon kills completion.** "FIDO2" and "WebAuthn credential" in enrollment copy measurably lower completion. Say "Face ID", "fingerprint", "your device".
- **The screen behind the system biometric prompt has to say what is about to happen.** Uncertainty during the prompt is a primary driver of canceled authentication attempts.
- Passkey signup is now the default expectation on both platforms, and Credential Manager on Android wires it into text fields.

## Empty and error states

Four types: **first use, user-cleared, error, no data.** Structure: concise headline, supporting line, relevant icon or illustration, one CTA, centered in the container.

> **A good empty state feels native to its function. The empty state of a calendar should not look like the one for a shopping cart.**

Generic reused empty states are the tell of a design system applied without thought.

**Error copy states the affected action, what remains intact, and what the user can do.** Factual, no blame.

**Error states exposing stack traces, database errors or API keys are a security risk**, not just a UX one. Error messages are written for users, not developers.

### Error recovery

1. **Name what happened** in the user's terms. "You are offline", not "Error 503".
2. **Say what still works.** "You can keep writing, we will save when you are back."
3. **Offer the action.** Retry, or a path to what does work.
4. **Retry automatically first**, with backoff, before showing anything. Most transient failures resolve in under a second.

**Non-modal connection status beats a modal error dialog**, because a modal blocks the recovery the app is already attempting. A modal is right only when the user must decide something.

Backoff discipline matters on mobile: exponential backoff with jitter, a retry cap, and centralized limiting rather than a retry at every layer. Uncoordinated retries turn a recovering backend into a failing one, and on a phone they drain battery.

## Undo versus confirm

| Action | Pattern |
|---|---|
| Frequent, low stakes, reversible (archive a mail, delete a note) | Do it, show undo |
| Rare, high stakes, irreversible (delete account, wipe data) | Confirm first, explicitly |
| Frequent and irreversible | **Redesign it so it is reversible** |

**A snackbar is not a sufficient safety net on its own.** It times out (roughly 4–10 s), it can be missed, and a scroll dismisses it. **Anything undoable that way must also be recoverable through a trash, archive or history for a real period of time.**

For genuine confirmation: red for the destructive option, **name the action in the button** ("Delete 3 photos", not "OK"), and never place the destructive button where the safe one usually sits.

## Search

The pattern vocabulary: auto-complete, explicit search, dynamic as-you-type, scoped, faceted, saved and recent, results, visual search.

- **Scoped search is the fastest and easiest pattern**, because results appear before the query is finished.
- **Faceted search is scoped search plus changing facets.** Facets change with each search; filters do not. Worth keeping the distinction.
- **The zero state does real work.** Before a character is typed: recent searches (the highest-value item for returning users), popular or trending queries, browsable categories. An empty search screen wastes the most attention the feature will ever get.

**Autocomplete rules:**

- **5–8 suggestions, no more.** Past about eight, users either ignore the list or spend too long reading.
- **Highlight the matched substring.**
- **Mix types deliberately but label them** – a suggestion that navigates and one that searches behave differently.
- **Apply a frequency threshold** before a query enters the popular list, or suggestions fill with typos.
- Suggestions must be tappable at 44 pt and not hidden behind the keyboard.

**Keyboard economy:** open the keyboard when search is entered deliberately, set the return key to "Search", disable autocorrect for names and codes, update results as the user types.

**Zero results is a designed screen, not a failure message.** Corrected spellings, related queries, popular searches, or a browse path. "No results for 'xyz'" alone is a dead end, and dead ends end sessions.

**Filters and scopes belong after the first result set**, not before it. Making a user configure a search before running one inverts the effort.

## Settings and information architecture

**iOS Settings is the reference implementation.** Roughly **7 items per level**, grouped, with the group header doing the explaining.

- **Two levels, not four.** A setting three taps deep is a setting nobody finds – which is what search in settings exists to patch, and patching is worse than structuring.
- **Label the door honestly.** "Advanced", "Show more", "More options" set an expectation. A chevron with no label does not.
- **Defaults must be right**, because most users never open the second level. Progressive disclosure hides complexity; it does not excuse it.
- **Never hide something that changes what the user is currently seeing.** If a toggle affects the visible result, it belongs next to the result.
- Use **card sorting to generate** the structure and **tree testing to evaluate** it.

## Pull to refresh

Still standard and still expected on feeds. Chrome augments it: pull-and-release refreshes, pull-and-swipe-right closes the tab, swipe-left opens a new one.

**It relies on fine touch input and excludes users with motor impairments.** A visible, tappable refresh gives everyone an alternative.

The governing rule: **gestures must always be shortcuts, never requirements.**

## Drag and drop

Earns its complexity only when the list is long enough that reordering by other means is tedious. **Moving an item ten places is a good case; moving it two is not** – there, move-up/move-down or a sort control wins.

- **The dragged item follows the finger with no lag and no offset**, and lifts visually – scale up slightly, raise the shadow, reduce opacity a little. **The lift is what says "this is now in your hand".**
- **Leave a gap or ghost where it came from**, or the user loses the reference for how far they have moved.
- **Neighbors move out of the way with a short animation** as the insertion point changes, rather than the list rearranging on drop. The list shows the result before committing.
- **Haptic on pick up, on each reorder crossing, and on drop.** The clearest case for haptics in the whole interface, because the interaction has no other physical feedback.
- **Auto-scroll near the edges**, speed proportional to proximity.
- **Long press to begin** on a scrollable list, because an immediate drag conflicts with scrolling. **A dedicated drag handle avoids the conflict entirely** and is better on a dense list.
- **Accessibility needs a non-drag path.** Both platforms provide one, and reordering that only works by dragging is unusable with a screen reader.

Cross-app drag on iPad and Android is a system capability, and supporting drop targets is a small amount of work that reads as polish out of proportion to its cost.

## Text selection

Mostly free if you use standard text components, and mostly broken if you do not. **A custom text renderer that cannot be selected, magnified or copied is a regression the team will not notice and every user will.**

Preserve: **long press starts selection** with grab handles; **a magnifier follows the finger** (Android's `Magnifier` widget since API 28, iOS's loupe forever) because precise placement is otherwise guesswork; **a floating toolbar** with Cut, Copy, Paste and More; double tap selects a word.

- **Make content text selectable and chrome text not.** A UI label should not offer to be copied; a message, code, address or error identifier should.
- **Never disable selection on anything a user might need to keep.** Order numbers, error codes, API keys and support references are the classic offenders, and blocking copy on them generates support tickets directly.
- **Offer explicit copy where selection is awkward.** A tap-to-copy affordance on a code, with confirmation, beats selecting six characters precisely.
- iOS's edit menu is extensible and underused – "Define", "Translate", "Add to note" belong there rather than in a separate flow.
- Test selection in RTL and mixed-direction text, where custom implementations break first.

## Carousels

Used far more than they work, and the failure is that content past the first slide is rarely seen.

Defensible for genuinely equivalent items (product photos, a small gallery), a short onboarding sequence, or a horizontally scrolling shelf inside a vertical feed. Not defensible for putting primary content behind a swipe, or rotating messages the user did not ask to rotate.

- **Three to seven items.** Past that, use a list.
- **Show a slice of the next item.** Partial visibility is the strongest possible affordance that there is more, and it beats any indicator.
- **Never autoplay.** It removes control, interrupts reading, and is an accessibility problem. If it must, it pauses on interaction and offers a stop.
- **Dots communicate position and count and little else**, and they are poor touch targets. Prefer labels or thumbnails; treat dots as a readout rather than a control.
- **Swipe must work**, and every dot or arrow must also be a real labeled button.
- Do not loop infinitely without an indicator – the user gets no sense of having seen everything.

## Account and profile switching

Needed whenever one person holds multiple identities: personal and work, multiple teams, a parent managing children's profiles, an admin acting for an organization.

- **Make the active identity unmistakable at all times**, not only inside the switcher. An avatar in the header with the account's own color or image is the usual solution, and it prevents the real failure: acting in the wrong account.
- **Show which accounts are already authenticated** versus which need a login; the two behave differently on tap.
- **Put "Add account" in a fixed position**, so it does not move as accounts are added.
- **Preserve per-account state.** Switching and switching back returns to where the user was.
- **Confirm outbound or destructive actions with the identity named.** "Post as @workaccount" removes the entire class of wrong-account mistakes.

Placement: avatar in a top corner opening a sheet on phones; a persistent rail entry on tablets and desktop.

## Microcopy

Screen space is the constraint that makes native microcopy harder than web microcopy. There is no room for an explanatory paragraph, so every word does more work.

The four jobs microcopy does: **prevent mistakes** while they are cheap to fix; **explain consequences**, especially before anything irreversible; **build confidence** by confirming the input is on track; **help recovery** with a specific next step.

- **Button labels name the outcome.** "Submit", "OK" and "Continue" describe the mechanism, not the result. "Delete 3 photos", "Save draft", "Send to Anna" say what will happen.
- **Match the label to the heading.** A sheet titled "Discard changes?" with "Yes" and "No" forces a re-read. "Discard" and "Keep editing" do not.
- **Uncertainty, not grammar, is the friction.** Most microcopy work is writing down something the team already knows at the moment the user needs it: when the charge happens, whether this is reversible, what "sync" means here.
- **Write for the narrowest device and the longest translation.** A label perfect in English at 390 pt that clips in German at the largest text size is not finished.
- **Keep terminology stable across the app.** A thing called a "board" on one screen and a "project" on another is the quietest source of confusion, and it is free to fix.
- **Voice is consistent, tone varies.** Playful in a success state and plain in an error state is correct; playful in an error state is not.

## Image loading

The largest source of visible jank in a content app, and the fix is entirely a design decision about what shows before the pixels arrive.

| Approach | Problem |
|---|---|
| Nothing, then the image appears | Layout shift, flashing |
| Spinner per image | Busy, and a grid of spinners is noise |
| Solid gray box | No shift, but dead |
| Skeleton shimmer | Good; signals loading without noise |
| **BlurHash or a dominant color** | Best; the placeholder already resembles the image |

**BlurHash** encodes a blurred version in roughly 20–30 characters of text, small enough to ship inside the JSON that lists the images. The placeholder is available instantly with no second request, and the transition is a cross-fade from something that already has the right colors in the right places. A dominant-color swatch is the cheaper version and covers most of the benefit.

**Non-negotiable regardless of approach: reserve the space before loading.** The aspect ratio comes from the data, not from the decoded image, or every load shifts the layout.

- **Cache decoded bitmaps, not just bytes.** Decode is usually the expensive step in a fast-scrolling list.
- **Cancel in-flight requests for cells that scrolled off**, or the list degrades over time.
- **Request the size you will display.** A 4000 px image for a 120 px thumbnail is the most common mobile bandwidth waste there is.
- **Fade in over roughly 150–200 ms.** Instant reads as a flicker; longer feels slow.

**The native-specific hazard is memory.** On the web an oversized image costs bandwidth; on mobile it costs RAM, and RAM pressure is what gets the app killed in the background. **Image sizing is a retention issue, not only a performance one.**

## Lifecycle

**iOS: not running → inactive → active → background → suspended.** Suspended means resident in memory but executing nothing, reached roughly five seconds after backgrounding.

**On entering background you have about five seconds to persist anything you need**, because the OS may terminate the process afterwards to free memory for the foreground app. When the user returns, it relaunches cold.

Android: the activity lifecycle, with `ViewModel` or `rememberSaveable` carrying UI state across recomposition and configuration change.

Design consequences the web does not have:

- **The app can be killed at any moment with no warning and no unload event.** Every in-progress form, filter, scroll position and draft has to survive that.
- **State restoration is a design requirement, not an engineering nicety.** A user who backgrounds mid-checkout and returns to an empty cart has been failed by design, not by the OS.
- **On mobile, persist continuously regardless of the save model**, because termination is normal rather than exceptional.

## Offline-first

Functionality unaffected by intermittent network, usually with sync across devices.

**The five states that must be visible: offline, pending, syncing, synced, failed.** The web has two. **The optimistic-UI pattern – write locally, mark pending, show it immediately – works only when "pending" is visible somewhere**, otherwise the user cannot tell a saved thing from a thing that will vanish.

Status surface requirements: an offline/online indicator, **badges on unsynced items per item rather than only globally**, and a retry affordance on failure. Good pending-changes UX is calm and predictable: it acknowledges that work is saved on the device and makes the next step obvious.

**Idempotency is a design concern.** Every change carries a unique action ID; a re-sent change returns the same result rather than creating a duplicate. Without it a flaky connection produces duplicate orders, which the user experiences as the app's fault.

### Sync conflicts

Easy until two devices edit the same thing. **The resolution strategy is a design decision with a visible consequence.**

| Strategy | When it is right | What the user sees |
|---|---|---|
| **Last write wins** | Preferences, toggles, single-user settings | Nothing – and silently losing an edit if that assumption is wrong |
| **Field-level merge** | Records with independent fields | Nothing, correctly. The best outcome |
| **CRDT / operational transform** | Collaborative text and lists | Nothing – both edits survive |
| **Ask the user** | Documents, notes, anything authored | A conflict UI |

**Last-write-wins is the default everywhere and it is the one that quietly destroys work.** Choose it deliberately or not at all. It is a bad choice for money, inventory, or anything audited.

When the user must resolve:

- **Show both versions with what differs**, not two opaque blobs labeled "local" and "server". A user cannot choose between two things they cannot compare.
- **Label by device and time in the user's words:** "This iPhone, 2 minutes ago" against "MacBook, yesterday at 4:12 pm".
- **Offer "keep both"** wherever the data model allows. It never loses anything and it is the option most apps forget.
- **Never resolve a conflict inside a toast.** It is a decision, so it gets a screen.
- **Do not surface conflicts the user did not cause.** A background conflict on untouched data is resolved by policy, not escalated.

## Protecting sensitive screens

A design decision, not just a security one, because it changes what the screen can show.

- **Android has a real mechanism:** `FLAG_SECURE` blocks screenshots, recording and casting at OS level, and the system tells the user the app blocked it.
- **iOS has no direct API.** The widely used technique layers content inside a secure-entry `UITextField`, which the system excludes from screenshots and recordings. It works and is stable, but it is undocumented. The documented paths are `UIScreen.isCaptured` and `sceneWillResignActive`.
- **Blur or mask on backgrounding is the one every app should do.** The system takes a snapshot for the app switcher, and a banking balance or a message thread sitting there is a real exposure. Cheap, and visible polish.

**Apply protection per screen, not per app.** Blocking screenshots globally is hostile – users legitimately screenshot receipts, confirmations and error messages, and blocking that generates support load and bad reviews. Protect the balance, the card number, the OTP and DRM content; leave the rest alone.

Related: mask card and account numbers by default with an explicit reveal, never put sensitive values in notification previews, and respect shared-context cases – external display, mirroring, active recording.
