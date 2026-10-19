# Layout and responsive

**A design that works at one width was never finished.** Responsive is not a phase after the design; it is a property the design either has or does not.

## The spacing scale

The usual justification for the 8pt grid ("most screen sizes divide by 8") is weak. **The real reason is device pixel ratios:** 8 subdivides evenly into the 1x, 1.5x, 2x and 3x densities in use, so every value lands on whole pixels on every common device. 4pt half-steps are legitimate for tight UI; anything finer produces fractional pixels somewhere.

**The other real reason is not mathematical: a discrete scale forces deliberate spacing decisions.** Eyeballed values are what produce interfaces that feel subtly unpredictable. **The constraint is the point.**

Working scale: `4, 8, 12, 16, 24, 32, 48, 64, 96, 128`.

### The cardinal spacing rule

> **Internal padding ≤ external gap.**

When the space *inside* a group exceeds the space *between* groups, **the grouping inverts and the eye reads the wrong structure.** This one rule catches more real layout bugs than any other single check, and **it is mechanically verifiable from the CSS.**

### Naming the scale

- **Numeric beats t-shirt sizing at scale.** `xs, sm, md, lg, xl` collapses once the system grows – you reach `2xl`, `3xl`, and eventually need a value between `sm` and `md` with nowhere to put it. Numeric (`space-100…space-1000`) inserts cleanly.
- **Never mix conventions.** `spacing_md`, `spacing_4` and `spacing_8px` for the same concept is the failure that produces token chaos.
- Naming the token rather than using the raw value means **the scale can be re-tuned later without touching a component.**

### Section spacing at page level

- **Space before a heading should be roughly 2× the paragraph spacing**, up to 3×. Beyond that, sections visually fall apart from each other. Concretely: 20 px paragraph spacing means 40 px+ before a heading.
- **Large spacers separate sections, small spacers separate related items.** The internal ≤ external rule applied at page scale.
- Reference values: ~96 px clear between major sections; **the brand corpus clusters at 80–96 px**, stated as a token and applied uniformly.
- **Reduce top spacing before headings on mobile.** A large gap can leave the next heading below the fold, so the section reads as the end of the page.

## Gestalt, as engineering

Only two Gestalt principles do heavy lifting in interfaces:

- **Proximity** – elements close together read as one group. Free, costs no ink, and it is what spacing *is for*. **Most "this layout is confusing" problems are proximity problems.**
- **Common region** – a shared boundary groups elements even when spacing cannot. **This is the entire justification for the card**, and it is the tool when density prevents using space, which is most dashboards.

**When a layout is crowded, the choice is *add space* or *add a boundary*. Adding both is usually redundant.** A card with generous internal padding, a border, a shadow and a background tint is saying the same thing four times.

## White space

- Wichita State lab research: **white space improves reading comprehension, though it may reduce reading speed.** That is the honest finding – a trade, not a free win.
- The widely repeated "20% comprehension improvement" and the Crazy Egg "+20% white space lifted conversions 13%" are both secondhand and repeated without primary citation. **Directional only; do not quote as fact.**
- **Micro white space** (between lines, list items, nav links) governs readability. **Macro white space** (between major regions) governs hierarchy and calm. Different jobs.
- **The counter-argument that actually persuades a stakeholder is not aesthetic.** It is that density has a cost in comprehension and error rate, and **a denser page that is misread is not a more efficient page.**
- **Density is a legitimate design choice, not a failure.** Bloomberg Terminal, Linear and a flight-booking back office are all correctly dense. The rule is not "more space" – it is **space proportional to how carefully the content must be read.**

## Optical alignment

**Mathematical centering is not visual centering.** The computer knows width, height and position; it does not know where an object's visual weight sits.

| Case | What happens | Fix |
|---|---|---|
| Play triangle in a circular button | The mass is on the left, so mathematically centered looks left-shifted | Shift right until the sharp edges are equidistant from the circle |
| Any asymmetric icon in a container | Same failure | **Balance the mass, not the bounding box** |
| Text in a button with an icon | The icon's optical weight pulls the pair off-center | Adjust padding asymmetrically |
| Capital-only text in a container | Cap height without descenders sits visually high | Nudge down, or account for it in the padding |

**Method: draw a crosshair through the container center and shift the shape until its heaviest parts balance around it.**

One of the highest-leverage details – invisible when done, unmistakably amateur when not.

## Nested radius

> **`inner radius = outer radius − padding`**

The geometry: the outer corner arc is centered at (R, R); a child inset by p with radius r has its arc centered at (p+r, p+r). **They are concentric only when `p + r = R`.** When the curves share a center, the gap between the two shapes stays visually even the whole way around – **which is what "expensive" looks like at the corner of a card.**

```css
--outer-radius: 16px;
--padding: 8px;
--inner-radius: calc(var(--outer-radius) - var(--padding)); /* 8px */
```

**If padding ≥ outer radius, the inner element gets square corners**, not a negative radius. Wrong nested radii are one of the most common and most invisible causes of a UI feeling slightly off.

## Grids

**Not every site needs a 12-column grid.** It is a relic of the float-and-framework era, when that was the only thing the tooling could express.

**Intrinsic web design** is the current position:

- Fluid columns **and rows** – two-dimensional, not just horizontal.
- **Media queries optional, not structural.**
- **Nested formatting contexts** – flex inside grid inside flow, each solving what it is good at.
- **The layout is intrinsic to the content**, sized by what is in it, rather than content poured into a predetermined grid.

Reach for `grid-template-columns: repeat(auto-fit, minmax(min(280px, 100%), 1fr))` and `subgrid` before reaching for a column count. **Use an explicit 12-column grid when the design genuinely is a magazine-style layout that needs one, not by default.** See `modern-css.md`.

## Balance and tension

- **Symmetrical balance** reads as stable, formal, calm, institutional. Also the safe, slightly inert choice.
- **Asymmetrical balance** – different weights on each side, balanced by position and scale rather than mirroring – produces **movement and tension**. One large focal element on one side, several smaller counterweights on the other.
- **Tension comes from contrast held in balance:** a circle against straight lines, horizontal type against a vertical column, a heavy mass against a large void.
- Radial balance emphasizes a single center. Rare in UI; useful in a logo, a hero, a data viz.

**Symmetry when the message is trust and stability** – finance, institutional, checkout. **Asymmetry when the message is energy, editorial voice or modernity.** A layout that feels boring is very often symmetrical when it should not be.

## Breakpoints come from content

- **A breakpoint belongs where the layout starts to break**, found by dragging the window, not by copying a device list. **Real content-based breakpoints land on odd values** – 642, 860, 911 – because that is where the content actually fails.
- The device-width lists (768 / 1024 / 1280) are a legacy of a device landscape that no longer exists. A starting grid at best.
- **The most reliable content trigger is measure:** when a text column exceeds ~75 characters, that is a breakpoint. Cap the width, add padding, or go multi-column.
- **There is no "tablet breakpoint".** There is a continuum of widths and a set of input capabilities, and **they are independent.**

The corpus confirms this from the other direction: recurring explicit breakpoints are ≥1440 (11 mentions), ≥1280 (8), ≥1024 (6), ≤425 (4) – **plus a long tail of odd, content-derived values** (833, 1136, 1300, 1360, 1400, 1441, 1500).

**Apple's documented collapsing strategy is the model worth copying: each element declares what it does at each step.** Nav collapses to hamburger at 834 px, tiles 2-col to 1-col at 834 px, vertical padding 80 → 48 px on small phones, hero type 56 → 40 → 34 → 28 px. **Responsive behavior specified per component, not left to the grid.**

## Container queries

`@container (min-width: 400px)` – over 93% global support, production-safe without a polyfill.

**The dividing rule is clean:**

| Responsive behavior belongs to… | Use |
|---|---|
| A **component** – how a card lays out its own contents | **Container query** |
| The **page** – a sidebar layout collapsing to stacked | **Media query** |

**The payoff is the component that stops caring where it lives.** A card in a sidebar stacks; the same card in a wide region goes horizontal; no props, no variant, no breakpoint knowledge. **This is what makes dashboards tractable** – each widget is self-contained and does not know whether it sits in a 2-column or 6-column grid.

**Container queries do not replace media queries.** Global structure is still a viewport concern.

## Viewport units – the mobile `100vh` lie

`100vh` is computed against the **largest** viewport, with toolbars hidden – but on load the toolbars are visible. **So a `100vh` hero is taller than the screen on first paint and the bottom is cut off.** One of the most common real-world mobile bugs.

| Unit | Meaning | Use for |
|---|---|---|
| **`svh`** | **Small** – chrome fully visible, worst case | Full-screen sections that must fit on **initial load** without clipping |
| `lvh` | **Large** – chrome fully hidden | Rare; when you want the maximum |
| `dvh` | **Dynamic** – tracks the real visible area live | Anything that should track the actual viewport. Costs reflow as the bar animates |

Baseline Widely Available since June 2025, ~95% global support. **There is no longer any reason to use the JS `--vh` hack.**

**Practical default: `min-height: 100svh` for hero sections**, `dvh` where continuous accuracy matters more than layout stability.

## Zoom and reflow – the requirement people skip

**WCAG 1.4.10 Reflow (AA):** content must work at **320 CSS px wide** with no loss of information or functionality, and **without scrolling in two dimensions**.

**320 px is exactly a 1280 px viewport at 400% zoom – the two requirements are the same requirement.**

- Vertical-scrolling content must work at 320 px wide. Horizontal-scrolling content at 256 px tall.
- **WCAG 1.4.4 Resize Text** additionally requires text to scale to **200%** without loss. **This is the criterion that pure-`vw` fluid type fails.** See `typography.md`.
- Exceptions: tables, maps and some visual media, where reflow genuinely destroys the information.
- **Test method: desktop browser at 1280 px, zoom to 400%, check nothing requires scrolling in two directions.** Manual – automated tools miss most of it.

A five-second check that catches real failures.

## Input capability is independent of screen size

**Never infer input from width.** A 13-inch laptop can be a touchscreen; a phone can have a stylus or a connected mouse; a desktop can have a touch monitor.

| Feature | Values | Meaning |
|---|---|---|
| `pointer` | `none` / `coarse` / `fine` | The **primary** pointing device's accuracy |
| `hover` | `none` / `hover` | Whether the primary device can hover |
| `any-pointer` | same | The **union** of all available devices |
| `any-hover` | same | Same, for hover |

- Hover-dependent UI: `@media (hover: hover) and (pointer: fine)`. **Both conditions**, because a stylus is fine but does not hover reliably.
- **`@media (pointer: coarse)` is the correct trigger for enlarging targets** – not a width breakpoint.
- **Anything only reachable by hover is unreachable on touch.** Hover reveals must have a tap or focus path.

### Touch-first consequences

- **No preview before commitment.** Hover lets a mouse user rehearse; touch commits immediately. **Everything a hover state was communicating has to be communicated statically or after the fact.**
- **≥8 px between touch targets reduces misclicks by over 40%.**
- Public and large touchscreens are a third context: no hover, imprecise touch, varying distance, casual exploration rather than task focus.
- **Never let critical functionality depend on hover.** Hover remains a legitimate desktop enhancement – it just cannot carry meaning.

## Touch targets

| Standard | Size | Spacing |
|---|---|---|
| Apple HIG | **44 × 44 pt** | ~10 px between |
| Material | **48 × 48 dp** | **8 dp between, as a separate minimum** |
| WCAG 2.5.8 (AA) | 24 × 24 CSS px | Sub-24 px passes if a 24 px circle centered on the target does not overlap a neighbor's |
| WCAG 2.5.5 (AAA) | 44 × 44 CSS px | – |

- MIT Touch Lab measured the adult index fingertip at **8–10 mm**, the finger pad at **10–14 mm**, thumb contact around **25 mm**. 44–48 px maps to roughly 9–11 mm.
- Correctly sized targets are reported to **cut mis-tap errors 60–80%** and improve selection speed 30–50%. Directional.
- **Spacing is a separate requirement from size.** A 48 dp target with zero gap still gets mis-hit.
- **Fitts' Law is not sufficient for fingers.** The 2013 FFitts correction adds a second source of spread tied to the finger's absolute precision, so small targets are worse than plain Fitts predicts.

### The thumb zone

Hoober's data: **49% of people hold a phone one-handed**, and 75% of interactions are thumb-driven.

**Three zones:** green (bottom-center, no strain), yellow (mid-screen sides, requires a stretch), red (top corners, awkward or impossible).

**The nuance most summaries omit: zone size varies enormously by thumb length.** In one study the large-thumb group reached **over 50%** of the screen while small and medium groups reached about **30%**, across all phone sizes. **Designing for your own reach is designing for the largest hands.**

- **Primary actions go bottom-center.** This is why bottom bars, bottom sheets and bottom-anchored CTAs win on mobile.
- **The green zone mirrors for left-handed users**, so never commit a critical control to one bottom *corner*.
- The top of the screen is for information, not action.

## Safe areas

- `env(safe-area-inset-*)` **only resolves to non-zero when `viewport-fit=cover` is set** in the viewport meta. Without it every value is `0px` and the whole mechanism silently does nothing.
- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`
- Applies to notches, punch holes, Dynamic Island, rounded corners and the home indicator.
- Pattern: `padding-bottom: max(1rem, env(safe-area-inset-bottom));` so there is sensible padding on devices with no inset.

## Responsive images

- **`srcset` with `w` descriptors must be paired with `sizes`**, or the browser cannot choose correctly.
- **`<picture>` is for art direction and format negotiation** – a different *crop* for narrow screens, or AVIF → WebP → JPEG. `srcset` alone only varies resolution.
- **Generate variants by file-size steps, not device widths.** 400w / 800w / 1200w / 2400w covers almost everything.
- **2x DPR is enough** for content images. 3x is mostly wasted bytes.
- **`width` and `height` attributes give the browser an aspect ratio before load. This is the CLS fix, and it is free.**
- `fetchpriority="high"` on the LCP image; `loading="lazy"` on everything below the fold, **never on the LCP image**.

## Large screens

- **4K is ~5–8% of desktop traffic; ultrawide 21:9 and 32:9 together ~2–3%** and growing. Small but not negligible.
- **A 4K panel usually exposes a much smaller CSS viewport** because of OS scaling and DPR. **Design against CSS pixels, not panel resolution.**
- **Content max-width is the answer, and the corpus agrees:** lock at **1280–1440 px** and let margins absorb the rest. For a 1920 px screen, a 1140–1280 px container is the practical sweet spot.
- **What should scale with a very wide viewport:** side padding, number of grid columns, optional secondary panels. **What should not:** measure, type size, the primary reading column.
- The failure mode at 2560 px+ is a hero whose headline stretches to 200 characters and whose CTA sits marooned in an empty band.

## The failure list – what actually breaks

| Pattern | Cost | Fix |
|---|---|---|
| **Input `font-size` below 16 px** | **iOS Safari auto-zooms the whole page on focus and does not zoom back.** Breaks the flow of every mobile form | 16 px minimum on inputs, always |
| `100vh` on a mobile hero | Bottom clipped on load | `100svh` |
| Fixed units for layout and type | Rigid; fails at zoom and at unusual widths | `rem`, `%`, `fr`, `ch`, `clamp()` |
| Fixed header eating a short viewport | On a landscape phone the header can take a third of the screen | Cap header height, or hide on scroll |
| Testing only in browser emulators | Cannot reproduce touch behavior, real network, real GPU, real font rendering | Real devices for anything shipping |
| Hover-only affordance | Invisible and unreachable on touch | `@media (hover: hover)` plus a tap path |
| Tables not wrapped | Horizontal page scroll, which is a two-dimension reflow failure | `overflow-x: auto` on a wrapper, or a card layout below a threshold |
| Long unbroken strings – URLs, IDs, code | Blow the layout at 320 px | `overflow-wrap: anywhere` |
| **`min-width: auto` default on flex and grid items** | Children refuse to shrink below content size, so the layout overflows **with no obvious cause** | **`min-width: 0`** – the single most common invisible cause of overflow |

## Mobile-first vs desktop-first

A trade-off, not a rule. Mobile traffic is over 60% globally, 70–80% for retail, food, local services and travel.

- **The genuine argument for mobile-first is not traffic – it is that constraint forces prioritization.** Deciding what survives at 375 px improves the desktop design too.
- **Desktop-first is defensible for dense, decision-heavy, comparison-heavy tools** where the desktop experience is the product: dashboards, admin, IDEs, trading, spreadsheets, back-office. Designing those mobile-first produces a weak desktop app.
- Honest heuristic: mobile ≥70% → mobile-first. Desktop ≥70% → desktop-first. Split → **design both explicitly, neither as an afterthought.**
- **Ask for the traffic split.** A question with a real answer that changes the work.

### Mobile-first as a CSS discipline

Distinct from mobile-first as a design strategy:

- **Base styles outside any media query target the smallest screen; `min-width` queries add complexity upward.**
- The benefit is not philosophical: mobile devices download and apply only what they need, and **if the CSS partially fails to load, the small-screen layout is the fallback** – which is the correct fallback.
- **Even when the design is desktop-first, the CSS should usually still be mobile-first.** They are separate decisions.

**Progressive enhancement** is the same idea at system scale: content and core functionality first, enhancements layered for capable browsers. It aligns with performance practice and with accessibility, and `@supports` guards implement it.

## Density as a user setting

**Content density is information visible divided by space available.** The important insight: **it is a preference split within one product's audience, not a single correct value.**

- Cloudscape ships **comfortable** (default) and **compact** (data-intensive views). Material ships default / comfortable / compact.
- Compact packs the most rows per screen, and **power users living in a table all day almost always want it.** Comfortable is the balanced default for mixed audiences.
- **If your users span casual and power-user segments, treat a density toggle as near-mandatory past a certain product maturity.** Google Sheets, Notion and most enterprise admin tools ship one.
- **Implement as a spacing-token swap**, not per-component overrides – which is only possible if spacing is tokenized.

**Stop choosing for everyone and let the user choose, when the audience genuinely splits.**
