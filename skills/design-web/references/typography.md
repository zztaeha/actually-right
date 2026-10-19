# Typography

## The scale

**A type scale is a closed set of sizes, and every size must have a job.** Two sizes doing the same job is the most common scale failure – it produces hierarchy that reads as noise.

Ratio generators (1.2 minor third, 1.25 major third, 1.333 perfect fourth, 1.5 perfect fifth) are a starting point, not the answer.

| Pattern | Cost | Fix |
|---|---|---|
| Ratio scale used raw, unrounded | Sizes like 43.9453 px that no other system agrees with | Generate, then round to a usable value and **lock the set** |
| More than ~7 sizes | Nobody can hold the hierarchy; two sizes end up meaning the same thing | Cut to the sizes with distinct jobs |
| One ratio across the whole range | The big end explodes, the small end compresses to indistinguishable steps | Tighter ratio for UI sizes, wider for display, or hand-tune the ends |

## Fluid type

`clamp(min, preferred, max)` is Baseline Widely Available, over 97% global support.

| Pattern | Cost | Fix |
|---|---|---|
| **Preferred value is pure `vw`** – `clamp(1.5rem, 4vw, 3rem)` | **Text stops responding to browser zoom.** The bounds only engage at the extremes, so the whole middle range is zoom-dead. **A WCAG 1.4.4 failure in practice** | Always include a `rem` term: `clamp(1.5rem, 1rem + 2vw, 3rem)` |
| Fluid type applied everywhere by reflex | Fights any design that needs a deliberate discrete state – a weight change, a different face, a recomposed layout | Fixed values or a media query where the design genuinely changes state |
| A fluid formula more complex than the rules it replaces | Unmaintainable; nobody can predict the output | Two or three explicit sizes beats an unreadable formula |

**Fluid type solves *between* breakpoints. It does not remove the need for breakpoints where composition changes.**

## Measure

- **50–75 characters per line for body; 66 CPL is the classic optimum.** Do not exceed ~70–75.
- Dyson & Haselgrove: **~55 CPL supports effective reading at both normal and fast speeds** – the length that does not degrade when the reader speeds up.
- **Short lines (30–50 CPL) favor accuracy and careful reading.** Right for technical docs, form labels, captions, and most mobile body text.
- Emil Ruder landed on 50–60 characters in 1967 by the same reasoning.

**The mechanism is the return sweep** – the saccade from the end of one line to the start of the next. Too long and the eye loses the next line; too short and the sweep interrupts the rhythm. **That is why the rule is in characters, not pixels:** it is about eye movement per line, not width.

**Conflict worth knowing:** measured **reading rate rises with longer lines** (7.3 in fastest, 4 in slowest), but **users prefer the shorter line**. Speed and preference point opposite ways. For content people *choose* to read, follow preference (~65ch). For content they *must* process quickly under instruction, a longer line is not the error it is usually called.

## Leading

| Context | Value | Why |
|---|---|---|
| Body | **1.4–1.6, 1.5 default** | WCAG 1.4.12 requires content to survive user-set 1.5 spacing, so 1.5 is both the good default and the compliance floor |
| Display / headline | **1.0–1.2** | Large glyphs are taller, so the same ratio produces visually excessive space |
| UI labels, captions | **1.3–1.45** | Short strings, no return sweep to support |

- **Leading and measure are coupled.** Longer lines need more leading so the return sweep lands correctly. A wide column at 1.4 reads worse than a narrow one at 1.4.
- **A single global `line-height` is a design smell.** Headings, body and UI labels have different jobs and different metrics.

## Tracking

| Case | Value | Reason |
|---|---|---|
| **All-caps** | **+0.05em to +0.1em** | Capitals were drawn to sit beside lowercase, where ascenders and descenders create rhythm. Remove the lowercase and the caps crowd. A mechanism, not a taste |
| **Large display** | **−0.01em to −0.02em**, occasionally −0.03em | Sidebearings correct at 16 px are proportionally excessive at 64 px |
| **Body / lowercase** | **0** | Almost every tracked body paragraph is worse than the untracked one |
| Small text under 14 px | ~+0.01em | Counters close up at small sizes |

**Always `em`, never `px`,** so tracking scales with the size it belongs to.

### Kerning and display type

**Large sizes make bad spacing obvious.** Errors invisible at 16 px are glaring at 72 px, which is why display type is where craft shows.

- **Workflow order: tracking first, then kerning, then awkward pairs by hand.** Tracking is global, kerning is per-pair; the wrong order means redoing the kerning.
- **Problem letters: W, Y, V, T, L, P.** Diagonals and overhangs create holes next to straight-sided neighbors.
- **Manual kerning is worth it above ~24 pt** – headlines, logos, display lockups. Below that, trust the font's kerning table.
- The tension with all-caps: default uppercase kerning tightens AV and WA pairs, which *creates* dense spots across a tracked-out title. Tracked caps need the tracking **and** selective kerning.

## Optical sizing

- `opsz` is a registered OpenType axis. It adapts **glyph proportions, stem weights and detail** to the size the text is set at – thicker strokes and larger serifs when small, finer detail and more contrast when large.
- **`font-optical-sizing: auto` is the default and already on** for any variable font carrying an `opsz` axis. The browser and the font negotiate it with no work from you.
- Typical text range on the axis is 10–16.
- **A variable font is not automatically optically sized.** Verify the font actually ships `opsz` before claiming the benefit.
- **Prefer the registered properties** (`font-weight`, `font-optical-sizing`) over raw `font-variation-settings`, which overrides them and breaks inheritance.

## Choosing a typeface for screen

- **X-height is the single most predictive metric for small-size legibility.** Sweet spot for body is an **x-height 65–75% of cap height**.
- **The trade-off nobody states:** raising x-height at a fixed point size proportionally shortens ascenders and descenders, which **reduces letterform distinctiveness**. Very high x-height faces read well at 13 px and get monotonous at 48 px. That is why some faces are great for UI and wrong for display.
- Verdana and Georgia were drawn by Matthew Carter specifically for screen legibility at small sizes and still hold up. Inter, Roboto, Open Sans and Noto Sans were drawn with screen x-heights.
- **Glanceable reading is a different problem from sustained reading.** Nav labels, KPI values, wayfinding and anything read in under a second want more size and weight than the same content in a paragraph.

**On Inter specifically:** it is a genuinely excellent screen face with a generous x-height. It is also the single most recognizable AI-slop signature. **The failure is using it as a default rather than as a decision.** Exact x-height ratios are reported inconsistently across sources; treat "generous x-height" as reliable and the precise number as unverified.

## Pairing

- **The failure mode is conflict, not contrast.** Two faces that are *similar but not identical* read as a mistake. Either concordant (one family) or clearly contrasting. **The dead zone in between is where amateur pairings live.**
- The structural test: **similar x-height and compatible proportions, different skeleton.** Shared DNA, different voice.
- **Superfamilies remove the risk entirely** – IBM Plex, Source, PT, Noto ship serif and sans members drawn to match. Use when the project cannot afford a pairing experiment.

### The one-family constraint

Working within a single family is a legitimate and often superior choice, not a compromise.

- **Every member of the family is designed to work with every other member.** Harmony is guaranteed rather than negotiated.
- Weight, width and slope give real variation without breaking the system.
- **The requirement: the family must be deep enough.** One weight is not a system. Four to seven weights plus italics, ideally a width axis or a mono companion, does everything a pairing would.

Confirmed hard by the brand corpus – a large share of the 74 systems run a single proprietary family across everything, with hierarchy carried by size and weight alone. See `brand-corpus.md`.

## Hierarchy mechanics

Four levers: **size, weight, color/contrast, space.**

| Lever | Strength | Cost |
|---|---|---|
| **Size** | The most universally understood signal of rank | Consumes real estate; poor lever in dense UI |
| **Weight** | Creates rank **without spending space** – the best lever in dense interfaces | Needs real distance: 400 vs 500 is invisible, 400 vs 600/700 reads |
| **Color / contrast** | Excellent for de-emphasis | Fails for color vision differences and in sunlight. **Never the sole signal** |
| **Space** | Groups and separates with no type change at all; often the strongest and most overlooked | Costs vertical real estate |

- **A one- or two-step size difference does not create hierarchy** – it reads as an error. Steps must be unmistakable.
- **The check: strip to grayscale.** If heading, body and caption are still distinguishable, hierarchy is real. If not, color was carrying it.

Note the tension with the corpus law that hierarchy should come from **size before weight**. Both are true: weight is the efficient lever in dense UI, and reaching for weight on *display* type is what reads as template. See `brand-corpus.md`.

## UI type vs body type

**UI text is read in fragments – labels, values, statuses, actions – not in sustained paragraphs.** That distinction drives everything.

| Role | Size | Weight |
|---|---|---|
| Display / H1 | 36 px+ | Often **400–600**, not 700 |
| Section heading | 20–28 px | 600 |
| **Body** | **16–18 px** | 400 |
| **Buttons, nav labels, sub-headers** | 14–16 px | **500–600** |
| Caption / footnote | 12–14 px | 400 |

- **Two or three weights per typeface. More reads as chaotic.**
- UI labels take more weight than body text because they must be **recognized at a glance in the periphery**, not read in sequence. Weight buys recognition without buying space.
- Leading differs too: UI labels want 1.3–1.45, not body's 1.5.

## Minimum sizes

- **16 px is the floor for body text on the web** – the browser default everywhere and Google's mobile-friendliness minimum.
- At typical phone holding distance (**25–35 cm**), 16 px is at the **lower edge** of comfortable reading for normal vision. **14–15 px is genuinely difficult** for anyone with reduced acuity not severe enough to warrant assistive tech, which is a very large population.
- **16–18 px is the sweet spot for text-heavy pages**, line-height 1.5–1.6.
- **16 px on inputs is also the iOS auto-zoom threshold.** Same number, two independent reasons. See `layout.md`.

## Long-form reading

- **50–75 CPL, roughly 8–12 English words per line.**
- **Long-form wants 18–21 px, not 16.** A larger size is inviting – it implies the reader can lean back and settle in rather than angle forward. **Reading posture is a design outcome.**
- **Serif vs sans matters far less than size, spacing and contrast.** Worth saying, because that debate consumes attention belonging to the three variables that actually move comprehension.
- Structure carries as much as type: digestible chunks, meaningful subheads, generous white space, images breaking the column, a conclusion that points onward.

## Headline length

- Most practitioner sources say **6–10 words**; beyond ten, visitors skim.
- Some research finds longer headlines (12–17, even 21–28 words) achieve higher click-through.
- **The reconciling finding: higher headline *complexity* decreases engagement.** It is complexity, not length. **A long simple sentence can outperform a short dense one.**
- Simpler headlines have greater **stickiness** – better recall.
- **Subheads of 10–20 words convert 14% better than one-liners** and 22% better than long paragraphs.
- Unbounce reports a **−18.6% correlation** between word count and conversion overall.
- **The headline is the highest-variance element on a landing page** – tests on it produce significant winners about 24% of the time.

**Working position: aim for 6–10 words, optimize for clarity over brevity, and never let compression create ambiguity.**

## Micro-typography

| Property | Use | Note |
|---|---|---|
| `text-wrap: balance` | Headings, subheads, blockquotes, short strings | Balances line lengths so a heading does not leave one word alone. Browser line limit around 6 lines – designed for short text |
| `text-wrap: pretty` | Body paragraphs | Targets the **widow**, adjusting earlier lines so at least two words land on the last line. Cheaper on long text |
| **`font-variant-numeric: tabular-nums`** | **Any number in a column, KPI, price, timer, table, or that updates in place** | Equal digit advance width so columns align and updating numbers do not jitter. Fallback `font-feature-settings: 'tnum'` |
| Proportional figures (default) | Body copy | Tabular digits look mechanically spaced in running prose |

**The updating-number case is the one people miss:** a live counter or timer in proportional figures visibly twitches as digit widths change. A one-line fix that reads as smoothness.

| Item | Rule |
|---|---|
| **Quotes** | Curly and directional in prose, always. **Straight quotes only in code and data.** Straight quotes in body copy are the fastest visible tell of unconsidered typography |
| **En dash** | Ranges and spans: "July 5th–9th", "10–20%". Unspaced |
| **Parenthetical dashes** | Bringhurst recommends a **spaced en dash** over an em dash, because em dashes in many faces are wide enough to be visually disruptive |
| `quotes` property | Standardizes `<q>` and `open-quote` rendering across platforms |
| `font-feature-settings` | Ligatures, alternates, stylistic sets. Several corpus brands enable specific sets as brand signals – Raycast runs Inter `ss03` site-wide |
| **Hyphenation** | `hyphens: auto` plus a `lang` attribute. Valuable in narrow columns, risky in headlines |

Individually trivial, collectively the difference between typography that looks **set** and typography that looks **typed**.

## Vertical rhythm

Vertical rhythm is a **craft tradition producing calm, consistent pages – not a proven perceptual necessity.** Excellent typography breaks it deliberately.

On the web the baseline grid fights back: element heights are content-dependent and responsive, headings rarely land on a multiple, captions and list items resist, images have arbitrary heights.

**The defensible position: aim for it in main body content, let peripheral elements break it.** Do not spend hours forcing every element onto the grid – the returns collapse fast. **What actually delivers the perceived benefit is the cheaper thing: a consistent spacing scale and consistent leading**, not literal baseline alignment.

## Font loading

| Value | Block | Swap | Use for |
|---|---|---|---|
| `swap` | ~100 ms | infinite | Brand-critical headings, **with preload**. The font always eventually applies |
| **`optional`** | ~100 ms | **zero** | Body text where stability beats brand. **Guarantees zero font-swap CLS**; if not ready in ~100 ms the fallback is used for the whole page view and the font is cached for next navigation |
| `block` | up to 3 s invisible | infinite | Icon fonts only |
| `fallback` | ~100 ms | ~3 s | Middle ground |

- **`size-adjust`, `ascent-override`, `descent-override` and `line-gap-override` on a `@font-face` fallback rule are the real fix for swap CLS.** They reshape the fallback's metrics to match the web font, so the swap causes no reflow. **`swap` plus a metric-matched fallback gives brand type with no layout shift.**
- **Subsetting is where the size actually goes:** Roboto Regular 168 KB TTF → **12 KB subsetted WOFF2 (93% reduction)**. Inter variable 303 KB → **22 KB** Latin-only.
- **`unicode-range` does not subset anything.** It only tells the browser which already-subset file to fetch. Physically subset first, then declare ranges.
- WOFF2 compresses ~30% better than WOFF. No reason to ship anything else.
- **Variable fonts break even around 3 weights.** Below that, two static subset WOFF2 files are smaller. A subsetted variable WOFF2 runs ~40–60 KB with full axis range.

**Deliver preloads via HTTP `Link` headers, never a DOM `<link rel="preload">`.**

## System stack vs custom type

| | System stack | Custom face |
|---|---|---|
| Load cost | Zero, renders instantly | Latency, render-blocking risk, CLS risk |
| Rendering | Hinted and tuned by the OS vendor for that platform | Whatever the foundry shipped |
| User settings | Respects OS font accommodations some users depend on | Can override them |
| Familiarity | People read faster in faces they meet daily | Costs a moment of adaptation |
| Brand | None | The point |

**The honest middle: system stack for body, one custom face for headings.** Brand where it is seen, speed where it is read.

**Ask the user. Never decide this silently, and always ask for font choices.**

## Licensing – the practical trap

- Two models: **flat fee per domain**, or **tiered by monthly pageviews**.
- **The trap:** a site growing from 50,000 to 5 million pageviews can jump from a one-time $50 to a recurring four-figure annual bill. **A pageview-metered license is a liability that scales with success.**
- **Buy a self-hosted web license with no pageview cap** where possible. Fontspring and several independent foundries (Klim, Grilli Type, Dinamo, Commercial Type) offer flat-fee uncapped self-hosted licenses.
- **A variable font is one file but not one license discount.** Fewer files does not reduce obligation.
- Self-hosting gives privacy and performance control but puts the licensing burden on you, including not stripping font metadata.
- Google Fonts: free, open licenses, commercial use fine.

**When recommending a typeface, say which licensing category it is in.** Recommending a beautiful foundry face without mentioning a pageview meter is a real disservice.

## Reading difficulty

**Dyslexia-specific fonts do not work.** Peer-reviewed studies find no reading improvement from Dyslexie or OpenDyslexic over Arial or Times New Roman.

What the research does support:

- **Larger font size.**
- **Sans-serif over serif, roman over italic, monospaced performs well.** Rello & Baeza-Yates measured sans-serif, monospaced and roman styles significantly outperforming serif, proportional and italic.
- **Increased letter spacing** has a measured positive effect for dyslexic readers – one of the few places tracking body text is justified.
- **Increased line spacing.**
- Consistent section headings and structure.

**The accommodation is ordinary good typography, turned up – not a special font.** And a user-controllable text-size and spacing setting beats any font choice you make for them.
