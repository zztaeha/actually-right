# Components

**The catalog: how a thing is built.** What it does when something happens is in `states.md` – a form's structure lives here, its validation timing lives there.

## Buttons

- **One primary per view.** If everything is primary, nothing is. **The most commonly violated rule in real products.**
- **Primary = filled. Secondary = outlined. Tertiary = text or ghost.** The visual weight *is* the hierarchy.
- **Destructive is a color treatment layered on the hierarchy, not a level of it.** A destructive action that is the main step gets primary-danger; one of several options gets tertiary-danger.
- **Position matters as much as style:** put the destructive option away from the confirming one.

Proportions and sizing are in `hierarchy.md`.

### CTA buttons

- **Copy has the largest and most consistent effect. Test it first, before color or placement.** Verb-first, outcome-anchored, first-person.
- Sizes: **44 × 44 minimum**. Desktop primary around **200 × 48–56 px**, secondary ~160 × 40. **Mobile primary: full or near-full width, 90–95%.**
- **Multiple CTAs at natural decision points outperform a single CTA.** Repeat it down the page at the moments where the reader has just been given a reason.
- **One short reassurance line beneath** – "No card required", "Cancel anytime" – removes the last hesitation.
- **It should be the brightest thing on screen**, which only works if the rest of the page is disciplined. See `brand-corpus.md` on single voltage.
- Personalized CTAs reported up to **202%** better. Secondhand, directional.

## Forms

- **Labels above fields, always visible.** The label is the primary guide and it must not disappear.
- **Placeholder-as-label is measured harm.** NN/g found placeholder-only labels **increasing errors and completion time across every demographic tested.** The placeholder vanishes on first keystroke, so a distracted user cannot recover what the field was asking for. **Use placeholders for format examples only, or not at all.**
- **Single column.** Multi-column forms cause non-linear, zigzag reading and produce more errors and lower completion.
- **16 px minimum on inputs** – iOS zoom. See `layout.md`.
- **Mark optional fields rather than required ones** when most are required. Less visual noise.
- **Ask for the least possible.** Every field is a chance to abandon.

### Multi-step vs single-page

The numbers are unusually strong and consistent:

- **Multi-step converts ~86% higher than single-step** – one comparison reports 13.9% vs 4.5%.
- **Forms with five or fewer visible fields convert ~120% better** than longer ones.
- **Three to five steps is the optimum**; performance degrades past five.

Three real mechanisms: **reduced cognitive load** (discrete units beat one block), **mobile** (a long form is a visible wall of work), and **sunk cost** (once step one is done, step two is far more likely).

> **Decision rule: under five fields, single step. Five or more, multi-step in 3–5 chunks with visible progress.**

**Ethics note: the sunk-cost mechanism is the same lever a dark pattern pulls.** A multi-step form is legitimate when each step is **honest about what remains** – visible progress, step count – and lets the user go back. **It becomes manipulative when it hides the length to bait commitment.**

### Password fields

- **One field with a show/hide toggle**, not a confirm-password pair. The toggle does the verification job the second field was invented for.
- **Never block paste.** Paste is how people move a generated password out of a password manager; **blocking it forces weaker passwords.** The most damaging single mistake in the pattern.
- **Show the requirements while the field is focused**, before submission, and **make them match the server exactly.** Requirements revealed only on failure are a design failure.
- **Limit the requirements.** Complex composition rules produce weaker, more predictable passwords and more resets.
- **Strength meters do motivate stronger passwords** – pair them with copy that explains, and **never reject a long passphrase for an arbitrary maximum.**

### One-time codes

A screen with an unusually high bail rate, and the causes are almost all technical.

- **`<input type="text" inputmode="numeric">`** – **never `type="number"`**, which strips leading zeros and treats the code as a single number.
- **`autocomplete="one-time-code"`** enables SMS autofill on iOS and macOS.
- **Paste must distribute across the segmented boxes.** The code is nearly always already in the clipboard; **if pasting one 6-character string does not fill six boxes, the design has failed at the only thing it does.**
- Segmented boxes give clear structure and reduce transcription errors, but they need **size, contrast and spacing**.
- **Typography: never a face where 0/O or 1/l are indistinguishable.** Monospaced or a purpose-drawn numeric face.
- Visible focus and fill feedback per box. **Auto-submit on a valid-length code.**

### Phone and address

- **One phone field, not three.** Multiple fields raise cognitive load and slow completion, especially on mobile.
- **Country code alongside**, prefilled from geolocation, changeable. Masking and validation follow the selected country.
- **Keep the mask flexible.** Overly strict masks break on legitimate international numbers – the most common failure.
- **Addresses: the form structure itself must change by country.** Three approaches – country-specific layouts, a layout that reshapes on country selection, or a generic multi-line format. **Autocomplete-as-you-type plus country-driven restructuring is the strongest.**
- **Never assume State or ZIP.** Many countries have neither.

### Date input

- **Offer both:** type into a text field **and** pick from a calendar. Neither alone serves everyone.
- **Show the format** – `MM/DD/YYYY` – as a persistent hint, not just a placeholder, and auto-insert separators.
- **Separate day/month/year fields are easier for screen reader and keyboard users** than a single masked field.
- **Calendar pickers are for near dates.** For a birth date or anything more than a year away they require absurd navigation – use typed input.
- Indicate **today** and **the current selection** distinctly, and disabled dates unambiguously.
- **Spell month names out.** `03/04` is ambiguous across locales in a way that causes real errors.
- **8 px minimum gap between day cells.** They are among the smallest touch targets in most products.

### File upload

- Support **click-to-browse, drag-and-drop, and paste.** **Drag-and-drop always needs a clickable fallback** – it is inaccessible alone.
- **State the constraints inside the drop zone** – accepted types and size limit – *before* a failure can happen. **Most upload errors are preventable this way.**
- **Progress is the single biggest lever against mid-upload drop-off.** A bar, an estimate for large files, and distinct **uploading / processing / complete** states – **processing is a separate wait users do not expect.**
- Specific, actionable inline errors per file. Never a generic "upload failed".
- **Resumable chunked upload for large files.** Users expect a retry not to restart from zero.
- Multi-file needs per-file state, not one aggregate bar.

## Progressive disclosure

Reveal essentials first, complexity on demand. Reported **30–50% faster initial completion** versus full exposure. Secondhand.

Three forms: **step-by-step** (a sequence), **conditional** (hidden until requested), **contextual** (surfaced by what the user is doing).

**The trap: progressive disclosure that hides things the user needs every time is just extra clicks.** Hide what is genuinely occasional; **expose what is frequent even if it looks busier. The measure is frequency of need, not visual tidiness.**

## Accordions and FAQ

- **The whole header row is the hit target**, including padding. Making only the text or only the chevron clickable is the classic mistake.
- **Closed by default**, unless one section is overwhelmingly the likely target.
- Chevron rotation as the state indicator; hover and focus feedback on the header.
- **"Expand all / collapse all"** on long lists.
- **Single-open vs multi-open is a real decision:** exclusive keeps the page short, multi-open lets people compare answers. Native `<details name="group">` gives exclusive behavior for free.
- **SEO: Google indexes accordion content present in the HTML source, but immediately visible content may carry slightly more weight.** **Keep the most important messaging outside collapsed sections. Do not hide your value proposition in an accordion.**

## Tooltips

**A tooltip carries one small piece of context the user is missing right now** – small enough to read at a glance, specific enough to answer one question.

- **If the information is needed to complete the task, it must be visible.** A tooltip is supplemental by definition; **putting required information behind hover is a design failure and an accessibility one.**
- Under **150 characters**, 1–2 short sentences, **under 10 words for an icon label**.
- **Trigger on focus, not only hover.** Open on mouseover *or* focus, close on mouseout *or* blur.
- **WCAG 1.4.13 Content on Hover or Focus requires three things:** dismissible with **Escape**, **hoverable** (the pointer can move onto the tooltip without dismissing it), and **persistent** until dismissed or the trigger loses focus.
- `role="tooltip"` plus `aria-describedby`. 4.5:1 contrast like any other text.
- **Touch has no hover** – design a tap-to-reveal path with an adequate target.
- **Known harms, which are why 1.4.13 exists:** screen-magnification users get content obscured; users with limited pointer control trigger them accidentally or cannot keep them open; anyone who reads slowly loses the content when it vanishes.

## Badges, chips, tags, avatars

| Component | Interactive? | Job |
|---|---|---|
| **Badge** | **No.** Read-only, never clickable | Reports status or change: `draft`, `new`, `pending`, `−7%` |
| **Chip / tag** | **Yes** | Enter, filter, select, remove |
| **Avatar** | Sometimes | Identity. **1:1 ratio – never distort it.** Initials fallback |

- **Name them explicitly in the system.** The biggest source of inconsistency here is calling the same thing three names across a product and then giving it three behaviors.
- **Uniform size, color and type across all instances** – a badge is recognized by shape before it is read.
- Avatar groups: identical size, radius and content type, or the grouping stops reading as a group.
- 48 × 48 minimum touch target on mobile for anything interactive.

**The badge/chip split is invisible in a mockup and structural in a system:** a clickable badge teaches users that badges are clickable, and then every static one is a dead end.

## Data tables

| Decision | Guidance |
|---|---|
| **Row height** | **44–48 px** for most desktop interfaces. Condensed shows more rows but **reduces readability and causes parsing errors**; relaxed is more comfortable for large data sets. **Offer density as a user control** rather than picking for everyone |
| **Zebra striping** | Enders (2008): striping **did not significantly improve reading speed**, slightly improved accuracy, and made tables *feel* easier |
| When striping helps | **Dense tables with many columns.** In small tables it **misleads** – it implies grouping that is not there |
| **Better alternative** | **Row hover highlighting** – place-keeping without permanent visual noise. Often better than striping, and it composes with it |
| Legibility | Adequate cell padding, taller rows, clear column separation. **White space does more for a table than any decoration** |

Pair with `tabular-nums` on every numeric column, and a focusable scroll region at 320 px. See `typography.md` and `accessibility.md`.

### Bulk actions

- **Checkbox on hover** hints that rows are actionable without permanently adding chrome to every row.
- **Floating contextual toolbar** on selection – above the rows or fixed at the bottom. Actions appear only when relevant.
- **Always show the selected count**, and **distinguish "all on this page" from "all matching the filter, including rows you cannot see".** Selecting 50 and destroying 5,000 is a classic disaster.
- **Destructive bulk actions escalate the confirmation ladder** – scale multiplies damage.
- For complex bulk edits, a **wizard** (select → choose fields → resolve conflicts → review → apply) beats a single dialog.
- Fixed headers, pinned identifying columns and a scrollable body keep orientation in long selections.

## Search and filters

- **Filters are broad categories; facets are granular, multi-dimensional and combinable.**
- **Never auto-scroll on a single input. Never auto-collapse a filter group. Never freeze the UI on a single input.** The three most frustrating filter failures.
- **Avoid the zero-result dead end.** Always offer a route out – relax a filter, broaden, suggest alternatives. **A zero-result page with no exit is the worst empty state.**
- Top-positioned filters only when there are a handful; **vertical facet panel otherwise**, switching to a sheet or popover on small screens.
- **Communicate how AND/OR combinations affect the result set.** Silently intersecting facets produces confusing empties.
- **Autocomplete is not a convenience, it is error correction and step elimination.**

Search-as-you-type behavior is in `hierarchy.md`.

## Pagination, infinite scroll, load more

| Pattern | Best for | Cost |
|---|---|---|
| **Infinite scroll** | Exploratory browsing with no specific target – feeds, discovery | **Poor findability**, no sense of position or extent, unreachable footer, can prevent proper indexing |
| **Pagination** | The user is looking for **something specific**, or needs to return to a known position | Weak on mobile; extra interaction per page |
| **"Load more" plus lazy loading** | **The usability-tested compromise**, found superior in ecommerce testing | Slightly more effort than infinite; slightly less orientation than pagination |

**Default to "Load more" unless there is a reason not to.** It preserves the footer, gives the user control over how much they take on, and keeps position stable while still feeling continuous.

**Whichever is chosen: preserve scroll position on back-navigation.** Losing a user's place after they open and return from an item is the most-hated failure of all three patterns.

## Dashboards

| Rule | Value |
|---|---|
| Metrics per screen | **5–9.** Dashboards exceeding **12 KPIs show a 40% engagement drop.** Secondhand |
| Primary KPI | **Top-left**, largest size, highest contrast |
| Space allocation | ~40% to the single most important metric, ~30% to 2–3 secondary, ~20% to trend context, ~10% to nav and filters |
| Visual language | **One** consistent language for color, scale and layout, so the viewer does not re-learn how to read each chart |
| 2026 addition | **Data-freshness transparency.** Show the last-updated timestamp prominently. **Trust in a dashboard is trust in its recency** |

> **A dashboard needs a clear objective tied to a specific decision.** A dashboard that is not answering a decision is a report, and it will be ignored.

Note the tension with scan patterns: dashboards *are* one place where F-pattern scanning is reported to hold, probably because they are dense grids of comparable units rather than prose.

### Chart selection

| Question | Chart |
|---|---|
| Compare categories | **Bar.** Best for more than 7 categories or long category names. The safe default |
| Change over time, continuous | **Line** – it shows continuity, which bars cannot |
| Volume or magnitude over time, not just direction | **Area** |
| Relationship between two continuous variables | **Scatter**; add a third variable via bubble size |
| Categories over multiple periods | Stacked bar or stacked area |

> **Do not choose a chart because it looks familiar. Start with the story you want to tell and let that guide the format.**

## AI and chat interfaces

Four properties a chat interface is judged on: **capability transparency, recovery patterns, confidence display, accessibility.**

- **Capability transparency** – the user can tell what the system can and cannot do **before they type. An empty chat box with no signal is the most common failure.**
- **Confidence display** – inline confidence indicators, clarifying questions when uncertain, **source citations on factual claims**, progressive disclosure of reasoning.
- **Recovery** – actionable quick-replies to pivot the query, and a context-preserving handoff path.
- **Message structure** – **two short messages of 40 words beats one 80-word block**, especially on mobile where most of this happens while the user is doing something else.
- Streaming: buffer incoming tokens and render progressively, show a streaming indicator, **expose a stop button.**
- Contractions and human phrasing; microcopy that acknowledges the user's state.

Streaming perception numbers are in `states.md`.

## Onboarding

- **40–60% of SaaS signups use the product once and never return.** Onboarding is where that is won or lost.
- **Time to first value should be ~2 minutes.** A great onboarding flow removes everything between a new user and their first moment of value.
- **User-triggered tours perform 2–3× better than auto-triggered ones.** Interrupting someone who did not ask is the default failure.
- **Cap tours at five steps** – completion drops by more than 50% beyond that.
- **Contextual banners at the moment the gap appears** – not on login, not by email. **Relevance is timing.**
- **The empty state is the best onboarding surface in the product**, because it has full attention and no competition. See `states.md`.

## Pricing pages

- **Three tiers convert ~1.4× two tiers; four or more convert worse.**
- **Anchoring:** price the top tier **2.5–3× the middle** with features most buyers do not need, so the middle tier's value ratio reads as obviously better. Reported **15–30% lift in average purchase value.** Secondhand.
- **Default the toggle to annual**, which anchors a lower perceived monthly number.
- **Lead with business outcomes over technical specs** – reported up to 25% higher perceived value.

### Comparison and pricing tables

- **Order rows by what the user needs to decide with, not by what you want to sell.** Seats and storage before add-ons.
- **Sticky first row** carrying plan name, price and CTA, so each column's identity survives scrolling. **Repeat the CTA at the bottom of each column.**
- **Check marks for present, white space or a hyphen for absent. Never a red cross**, which reads as an error.
- Monthly/annual toggle with the billing basis stated unambiguously.
- **Collapse the long tail behind "Show more".** Most visitors want the overview, not all 60 rows.
- **Design for skimming differences, not reading everything.** The eye is looking for where the columns diverge, so **make divergence visually louder than similarity.**

## Social proof

**Placement beats volume**, and the numbers are strong.

- **Social proof in the hero generates 2.3× the engagement of identical elements below the fold.**
- A page with **testimonials plus logos plus ratings converts 35–50% better** than one with none.
- **Reviews can lift conversion up to 270%.**
- Testimonials **near the top CTA**: +6% conversion, +10.3% unique purchases in one A/B test.
- **Testimonials outperformed logo displays by ~35%** – but eye-tracking found **testimonials with photos and high-profile logos are the most memorable.** Use both; weight testimonials.
- **Relevance beats prestige.** A quote from a founder at a 15-person startup resonates more with other small founders than a quote from a VP at Google. **Match the proof to the visitor, not to your own vanity.**

All secondhand CRO figures – directionally consistent, individually unverified.

## Settings

- Group by **type** or by **use case**, and keep groups to roughly **7 ± 2** items.
- **Surface the most-used settings; push rare ones into subscreens.**
- **The label that opens a group must match the subscreen title.** Small rule, constant violation, real disorientation when broken.
- White space, dividers and headings do the scanning work.
- **iOS Settings is the reference** for a tiered parent-child structure that grows gracefully.

## Permissions

- **A permission matrix** – roles across, capabilities down – is the transparent model, and its design problem is **density.** Group capabilities into meaningful categories with collapsible sections, and use unambiguous allowed / not-allowed / partial states.
- **The test: an admin should be able to answer "does this role have this power?" at a glance.** A matrix that cannot be read at a glance **provides the illusion of control without substance.**
- **The UI must match what the backend enforces.** A Viewer should see the Edit button explained, not click it and receive a 403.
- **Permissions should be small, specific and composable.** "Manage everything" hides risk.
- RBAC works because it matches how organizations already think.

## Multiplayer and presence

- Cursors: **small, translucent, color-coded, name-tagged.**
- **Cursors stop helping past a handful of people.** Beyond that the UI is too busy for the awareness to be worth it – **degrade to an avatar stack.**
- **Make it configurable, and offer a focus mode** that hides all collaboration UI while still syncing in the background. **Some people find presence supportive, others find it surveillance.**
- Motion must be **smooth and interpolated**; ~100 ms is the real-time threshold. **Jumpy cursors read as broken.**
- Avatar stack plus location, with jump-to and follow, is the scalable version of presence.

## Keyboard shortcuts

- **Echo existing muscle memory first** – ⌘F find, ⌘C copy, ⌘S save, ⌘K palette. Fence novel bindings to the context that needs them.
- **`?` opens the shortcuts window.** The established convention, and it costs nothing.
- **"Go to" sequences are the convention for navigation:** `G` then the first letter of the destination. Highly memorable, no modifier collisions.
- **Show the shortcut next to the command** – in menus, tooltips, the command palette. **Discoverability comes from surfacing it where the action already is, not from a help page.**
- **OS-aware display:** ⌘ on macOS, Ctrl on Windows and Linux, everywhere the shortcut appears.
- Keep one consistent system and **write it down**; without a map the bindings drift into collisions.

## Print

Rarely designed, frequently used – invoices, receipts, recipes, tickets, documentation, anything anyone might file.

- `@media print` inline, or a dedicated `print.css` via `media="print"`.
- **Hide nav, sidebars, ads, share widgets, cookie banners, interactive chrome.**
- **Larger, more legible type; dark text on white regardless of theme.**
- **`@page`** controls size, orientation and margins.
- **Show link URLs:** `a[href]::after { content: " (" attr(href) ")" }` for external links. A printed page of invisible links is useless.
- **Avoid breaking inside a table row, figure or heading** – `break-inside: avoid`.
- **This is also the PDF path**, since "Save as PDF" uses the print stylesheet.

## Variable-length content

> **Design for the longest realistic string, not the one in the mockup.**

- **Translation expansion: German averages 20–35% and can exceed it.** Plan for **25–30% for European languages and 35%+ for German.** **Fixed-width buttons and precise spacing break first.**
- **Remove strict minimum widths; use flexible containers with min and max bounds** so layouts adapt rather than clip.
- **Truncation:** `text-overflow: ellipsis` requires `white-space: nowrap` and `overflow: hidden`. Multi-line via `-webkit-line-clamp`.
- **Truncation rules:** at least **four characters** of real content before the ellipsis, and **always a title or tooltip carrying the full string.**
- **Truncation is a design smell.** Appropriate where the text is a preview – an email list, a notification body. **A failure where the truncated content is the thing the user needs to distinguish items by. If every row truncates at the same point, the list is useless.**

## Numbers and dates

**Numbers:**

- **Abbreviate large numbers** – 1.2K, 3.4M, 1.1B – in dashboards and stat tiles. **Never in a financial or legal context** where the exact figure is the content.
- Currency: most use two decimals, some three. **No space between symbol and amount** in en-US and en-GB; this varies by locale. **Minus goes before the currency symbol.**
- **Separators are locale-dependent.** French uses a comma decimal and a space for thousands. **Never hardcode; use `Intl.NumberFormat`.**
- `tabular-nums` on anything aligned or updating.

**Dates and times:**

- **Relative for recency, absolute for reference.** Accuracy is not important with relative timestamps; immediacy is.
- The working ladder: **under 1 min → "Just now"; under 1 day → "x minutes/hours ago"; 1–2 days → "Yesterday at h:mm"; beyond → "MMM d, YYYY at h:mm TZ".**
- **"52 weeks ago" is useless.** Fall back to a real date past a threshold.
- **Always absolute for anything actionable or consequential** – expiry, deadlines, certificates, keys, legal dates. **If the user must act before a time, show the time.**
- Best of both: relative text with the absolute date in the `title` attribute and in a `<time datetime>` element.
