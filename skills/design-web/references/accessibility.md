# Accessibility

**Not a compliance chore bolted on at the end. A set of constraints that, taken seriously, make the design better for everyone.** Almost every item here improves the interface for users with no disability at all.

## WCAG 2.2 – what changed

Released October 2023. **9 new criteria, 1 retired (4.1.1 Parsing), 78 → 87 total.** The new ones lean toward **cognitive disabilities, motor disabilities and mobile**.

| Criterion | Level | Requirement |
|---|---|---|
| **2.4.11 Focus Not Obscured** | AA | When an element receives focus, **at least part of it must remain visible.** Sticky headers, cookie banners and overlays must not cover the focused element |
| **2.5.8 Target Size (Minimum)** | **AA** | **24 × 24 CSS px** – or smaller if a 24 px circle centered on the target does not overlap a neighbor's. A relaxed AA version of the AAA 44 × 44 |
| 2.4.13 Focus Appearance | AAA | Focus indicator sizing and contrast |

**The sticky-header-obscuring-focus failure is extremely common and almost never caught, because it only appears when tabbing.**

## Focus indicators

| Requirement | Value |
|---|---|
| Contrast against **adjacent unfocused colors** | **3:1** |
| Contrast against the **focused component itself** | **3:1** |
| Minimum area | 1 CSS px along the perimeter, **or** 4 CSS px along the shortest side |
| Practical floor | **≥2 px thick**, covering a meaningful area |

```css
:focus-visible { outline: 2px solid var(--focus); outline-offset: 2px; }
```

- **Never `outline: none` without a replacement.** The single most common accessibility defect in styled UI.
- **`:focus-visible` rather than `:focus`** – shows the ring for keyboard users, suppresses it for mouse users, which removes the usual excuse for deleting it.
- **`outline` survives Windows High Contrast Mode; a `box-shadow` ring does not.** Prefer outline.
- **Test by tabbing the whole page. That is the entire test.**

## The first rule of ARIA

> If you can use a native HTML element or attribute with the semantics and behavior you require already built in, do that instead of repurposing an element and adding ARIA.

**No ARIA is better than bad ARIA**, and the evidence is quantitative: the **2026 WebAIM Million found pages using ARIA averaged 59.1 detectable errors versus 42 on pages without it.** **More ARIA correlates with *worse* accessibility in the wild**, because it is usually applied incorrectly.

Native elements bring keyboard behavior, roles, states and cross-AT support for free, **across a far wider matrix of OS, browser and AT combinations than any hand-rolled widget.**

ARIA is still essential where HTML has no equivalent: tabs, comboboxes, tree views, live regions. **Build on semantics first; add ARIA only where the native layer genuinely cannot express the behavior.**

## Semantic structure

- **Landmarks:** `header`, `nav`, `main`, `aside`, `footer`. **Exactly one `main` per page.** All content should sit inside a landmark.
- **Headings form the page outline.** One `h1` describing the page, matching the title. **Never skip levels.** **Screen reader users navigate by heading far more than by tabbing – a broken outline is a broken navigation system.**
- **Skip links** implement 2.4.1 Bypass Blocks. A `main` landmark technically satisfies the criterion, but **keyboard users who are not using a screen reader have no landmark navigation** and still tab through the whole nav. **Ship the skip link anyway.**
- Lists as lists, buttons as buttons, links as links. **A `div` with a click handler is invisible to keyboard and to AT.**

## Keyboard patterns

**Roving tabindex** is the APG pattern for composite widgets: **the composite has one tab stop; arrow keys move within it.**

- Active item `tabindex="0"`, all others `tabindex="-1"`; move the 0 as focus moves.
- Applies to toolbars, tab lists, menus, listboxes, radio groups, grids, trees.
- **Rationale: 40 tab stops in a toolbar is unusable.** One stop in, arrows to navigate, Tab to leave.
- Coming: the **`focusgroup`** HTML attribute, a declarative replacement. Worth watching, not yet shippable.

## Visual order vs DOM order

**CSS `order` and `grid-auto-flow: dense` break the relationship between what is seen and what is read.**

- Tab order follows **DOM order** in most browsers – **but Firefox follows visual order**, so the experience differs by browser. **There is no consistent behavior to design around.**
- Screen reader order follows the DOM, so a visually reordered layout is **announced in a different sequence than it appears.**
- A **WCAG 1.3.2 (Meaningful Sequence) failure** when the visual order carries meaning.
- **The rule: visual order should match DOM order.** Reorder the DOM, or use grid placement that does not require `order`.
- The emerging fix is **`reading-flow`**, which lets tab and AT order follow the visual arrangement. Not yet a baseline. See `modern-css.md`.
- **Highest-risk place: a responsive layout where `order` is used only at one breakpoint.** It then breaks at that width and nowhere else, which is why it is rarely caught.

## Non-text contrast (1.4.11)

Everything that is not text needs **3:1**: the visual boundaries of controls, their states, focus indicators, and meaningful graphics.

- Applies to button and input borders, checkbox and radio outlines, toggle tracks, and **card boundaries where the boundary is the only thing defining the control.**
- **Only icons that carry meaning independently need 3:1.** Decorative icons and icons that repeat adjacent text are exempt.
- **Over a gradient or image, measure at the point of *lowest* contrast**, not the average.
- **Disabled components are explicitly exempt** – the technical loophole behind the disabled-contrast dilemma, and the reason `aria-disabled` plus a readable style is the better answer anyway. See `states.md`.

**The criterion most often missed**, because visual review looks at text contrast and stops. **A 1 px `#e5e5e5` border on white – the most popular card border in modern UI – is roughly 1.3:1** and fails whenever that border is the control's only boundary.

## Text over images

**Contrast over an image is dynamic** – the same text can pass on one part of the photo and fail on another.

| Technique | Use |
|---|---|
| **Scrim** – a solid-to-transparent gradient behind the text | Text occupying part of the image. Fades smoothly so the image is not flattened. **Fails if text spans the whole image** – the far end loses the scrim |
| **Darken or lighten the whole image** | Text covering most of the frame. Less elegant, always works |
| **Solid or blurred panel behind the text** | Maximum reliability; loses the photographic effect |

- **The "floor fade"** – darkening across the lower portion – works because **the bottom of most photographs is the least informative region.**
- A **soft-colored** gradient rather than black keeps the brand palette alive while doing the same job.
- **Test against the actual images, including the worst-case one, and against the mobile crop** where the composition shifts.

## Icons and labels

The evidence is one-sided.

- **Icon plus label beats icon alone and label alone.** **No research has ever shown labeled icons to be less scannable or recognizable than unlabeled ones.**
- Unlabeled icons are hard to memorize and often highly inefficient. **The canonical case: Microsoft Outlook's icon-only toolbar had poor usability, and adding text labels fixed it immediately.**
- **The one advantage of icon-only:** visual search is faster than reading labels **once the icon is known**, and the gap widens with more items. That is why a toolbar of familiar icons in a professional tool is legitimate – **for expert, high-frequency use.**
- **People remember a button's position more than its graphic.** Much of what looks like icon recognition is spatial memory, **which means moving an icon costs more than changing it.**

> **Label unless the icon is universally understood – search, close, menu, play – or the audience is expert and high-frequency. When unlabeled, always provide an accessible name and a tooltip.**

## Live regions

| Value | Behavior | Use for |
|---|---|---|
| `polite` | **Queues** – waits for the screen reader to finish | Almost everything: result counts, save confirmations, filter updates, status |
| `assertive` | **Interrupts** immediately | Only genuine urgency: blocking errors, session expiry, data loss |
| `off` | Not announced (default) | – |

- **Add the live region on page load, empty.** If it must be inserted dynamically, **wait ~2 seconds before injecting text** so the accessibility API has registered it – otherwise **nothing is announced at all.** This single detail breaks more live regions than anything else.
- **Content must be inserted into the DOM**, not merely revealed with CSS.
- Keep messages short. Test across JAWS, NVDA, VoiceOver and TalkBack – behavior genuinely differs.
- **The design question this raises: what actually deserves to be announced?** Announcing every filter keystroke is as bad as announcing nothing. **Announce the *result* ("24 results"), not the process.**

## Data tables

- **Real `<table>`, never `div`s styled as one.**
- `<caption>` as the first child, naming the data.
- `<th scope="col">` / `scope="row"`; `colgroup` and `rowgroup` for spans.
- **Complex or irregular tables need `headers`/`id` associations**, listed in the order a screen reader should read them. `scope` is only enough for straightforward grids.
- **Responsive:** at 320 px either the table scrolls inside a **labeled, keyboard-focusable region**, or it reflows to cards **without losing header associations**. **A scroll container that cannot receive focus makes the content unreachable by keyboard.**

## Forced colors mode

Windows High Contrast **discards most of your CSS**: custom backgrounds vanish, shadows disappear, decorative borders are stripped, and a user-chosen palette is imposed.

- **This is not a theme to design for. It is strict usability, and the correct posture is small corrective tweaks, not a parallel design.**
- Check with `@media (forced-colors: active)`.
- **Text in images does not convert** – it keeps its original colors and can become unreadable. Another reason to use real text.
- **Focus rings: use `outline`.** Forced colors preserves outlines and drops box-shadows. **`outline: 2px solid transparent` shows nothing normally and produces a real system-colored ring in forced-colors mode** – the standard trick.
- **Anything conveyed only by background color disappears entirely here.** Same failure class as color blindness: **color must never be the sole signal.**

## Cognitive accessibility and plain language

- Target roughly **grade 9 / age 14** reading level for general content.
- **Plain language is not dumbing down** – it is clear, concise, well-organized writing that reaches more people.
- **Front-load:** most important information first. Active voice with a clear actor. Short sentences, short paragraphs, chunked information.
- Kill jargon, and define any term the user could not reasonably know.
- ISO 24495-1:2023 is the international plain-language standard if a citation is needed.
- **This applies to every string in the interface** – button labels, error messages, empty states, tooltips, confirmations. **UI copy is design.**

### Neurodivergent users

The framing that reorganizes the area: **a shift from designing only for task completion to designing for emotional comfort, mental energy conservation, and autonomy.**

Four named requirements: **predictability, sensory control, task segmentation, language clarity.**

- **Predictable navigation** – benefits autistic users and every first-time user.
- **All video and audio off by default.** Not autoplay-muted. Off.
- **Task segmentation** – multi-step over one wall of work.
- **User control over the sensory environment:** adjustable text size, contrast, background color, motion – **and easy to find, not buried.**

**Readable type and high contrast help dyslexic users and anyone in low light** – the recurring pattern where an accommodation is simply better design.

### Older adults

NN/g's research base is substantial: **87 guidelines, 123 seniors across multiple countries, 93 testing 64 sites and apps.**

- **Vision loss is the most commonly reported disability among US adults, and roughly one in six people over 70 report vision impairment.** **Not a minority accommodation – a large and growing user group.**
- What the research supports: **larger text, high contrast, larger targets**, and **avoiding dropdowns and sliders**, which are hard with declining motor control, especially on touch.
- **Counter to current fashion: older adults show a preference for skeuomorphic design and icon-based menus** – stronger affordance cues. This lines up exactly with the flat-design usability ceiling in `style.md`.
- **Minimize navigation sublevels; keep menus to a single function.**
- **Semantic markup, no images of text, styling via stylesheets**, so that native OS accessibility settings work – **which is how many older users adapt interfaces rather than through assistive technology.**

### Low vision and magnification

- **WCAG 1.4.4:** text resizable to **200%** without loss. **WCAG 1.4.10:** reflow to a single column at **400%** with no two-dimensional scrolling.
- **The experience that matters: at high magnification the user sees a small window onto the page and loses all spatial context.** Anything depending on seeing two things at once – a label far from its field, a fixed sidebar, a toolbar that scrolls away – becomes unusable.
- **Avoid fixed positioning that breaks at high zoom.** A fixed header at 400% can occupy most of the viewport.
- **Relative units throughout.** Fixed px type does not respond to text-only zoom.
- **Images of text pixelate; real text does not.**
- Adequate spacing between interactive elements matters more at magnification, because a small visual window makes precise pointing harder.
- **Design for it from the start rather than retrofitting** – it is a layout property, not a style fix.

## Overlays – state this plainly when it comes up

**Accessibility overlay widgets do not work, and there is hard evidence.**

- **Over 600 accessibility professionals**, including contributors to the WCAG, ARIA and HTML specifications, signed the **Overlay Fact Sheet** stating overlays do not achieve compliance. **Screen readers interact with the DOM, not with overlay scripts; an overlay cannot fix barriers in code it does not change.**
- **Structural problems are unfixable from outside** – `div`s where headings belong, missing label associations. **No JS layer can retroactively supply correct semantics.**
- **25% of all US digital accessibility lawsuits (UsableNet 2024) targeted sites using overlays**; 22.6% in H1 2025. AudioEye's 2026 analysis: **38.5% of businesses sued already had an accessibility "solution" installed.**
- Courts have rejected them, and in some cases **an overlay has been treated as evidence the business knew its obligations and chose a workaround.**

**If a user proposes one, say this once, plainly, and offer the real path.**

## Consent banners – the design is now case law

- **Accept and reject must have equal prominence on the same surface.** The French **CNIL** established this; **Austria's high court ruled in 2025 that a colored "Accept" button against a gray "Reject" link violates GDPR parity.** **No longer a UX opinion – a design specification with enforcement behind it.**
- **Withdrawal must take no more effort than granting** – GDPR Article 7(3), explicitly.
- **No non-essential cookies set before active consent.** No pre-selected non-essential categories.
- First layer: **Accept All / Reject All / Customize.** Second layer: per-category toggles.
- Explain what is collected, why, and for how long.

**The UX finding worth carrying:** most people click Reject All because the banner looks suspicious or takes too many steps. **Making the consent experience quick, honest and visually comfortable improves opt-in numbers.** **Honesty outperforms manipulation on the actual metric** – the strongest possible argument against the dark pattern.

## `backdrop-filter` cost

- **`blur()` is the most expensive filter**, and `backdrop-filter: blur()` is worse because **it reads pixels from the layer below every frame.**
- **Never animate the blur radius.** It re-triggers GPU compositing every frame and will tank performance on anything below a flagship device. **Animate opacity or transform; keep blur static.**
- Keep radii small on mobile; **avoid `backdrop-filter` on `position: fixed` elements on iOS.**
- Limit the number of simultaneous blur layers. Safari still wants `-webkit-backdrop-filter`.

**Combined with the contrast problem, glass effects carry two costs – legibility and GPU.** Worth using, worth being deliberate about. See `style.md`.

## How to actually test

- **Automated tools catch roughly 30–40% of WCAG violations.** They are a floor, not a verdict. **Quote this number whenever someone treats a passing axe scan as done.**
- Best coverage for the effort: **NVDA + Firefox on Windows**, **VoiceOver + Safari on macOS**.
- Basic competency takes **2–4 hours of practice**; thorough testing, a few days.
- **The test that matters: navigate a key flow without looking at the screen** and see whether you can understand the page, find the content and complete the task from what is announced.
- Test at design validation, in development, in QA, and before any major release or redesign.

### The four checks that cost almost nothing and catch most of it

1. **Tab through the whole page.** Is focus always visible, always in a sensible order, never trapped, never hidden behind a sticky header?
2. **Zoom to 400% at 1280 px.** Does anything require scrolling in two directions?
3. **Grayscale the design.** Is anything now ambiguous?
4. **Turn images off, or read the headings outline.** Does the page still make sense?

## Annotating accessibility in handoff

Accessibility annotations specify **focus order, keyboard behavior, screen reader labels, contrast ratios, error handling and states** on the design itself.

Tooling: the **A11y Annotation Kit**, **Include**, Microsoft's **A11y Focus Order** plugin.

**The point is shift-left:** decide accessibility at design time rather than discovering it in QA, where it becomes rework.

**When the output is running HTML rather than a design file, the equivalent is stating the accessibility decisions explicitly in the delivery** – focus order, keyboard model, announced labels, contrast figures – **so they are reviewable rather than buried in markup.**
