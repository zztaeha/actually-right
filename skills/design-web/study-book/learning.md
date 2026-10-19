# Learning log – web

Raw capture for the `design-web` skill. Everything learned about frontend design goes here first, in full, with its source and its number. This file is allowed to be long, repetitive and messy at the edges. Consolidation into `SKILL.md` and the reference files happens later, from here.

## Capture rules

1. **A claim without a reason or a number does not go in.** Either it is spec-mandated (cite the spec), measured (cite the measurement), or reasoned from human perception/mechanism (state the mechanism). Taste with no argument behind it is noted as taste and labeled.
2. **Source every entry.** Spec section, study, article, docs page, or measured myself. `[unsourced]` if it came from memory and still needs verification.
3. **Prefer the shape `Pattern | Cost | Fix`.** That is the shape that survives into the skill. Write findings that way from the start where the material allows it.
4. **Contradictions are kept, not resolved on the spot.** Two sources disagreeing is a finding. Note both, mark it `CONFLICT`, resolve it when there is evidence.
5. **Received wisdom that turns out wrong is the highest-value entry.** Flag it `MYTH` with what people believe, what is actually true, and the evidence.
6. **Trade-offs get both sides.** Never record one direction of a fork without the cost of taking it.
7. **Verifiable beats assertable.** If a rule can be checked by a tool, a calculation or a screenshot, record how to check it.
8. **Responsive is not a section, it is a question asked of every entry.** Any rule recorded here must survive 320px, 375px, 768px, 1440px, 2560px, and 200% text zoom. If it does not, that is the finding. A design that works at one width was never finished.
9. **Motion carries emotion, and emotion is specifiable.** Record what a timing and curve *feel* like, not only what they are. The gap between "software" and "expensive" is measurable in milliseconds and easing coefficients.

## Status

| # | Area | Searches | State |
|---|---|---|---|
| 1 | Typography | – | complete |
| 2 | Color | – | complete |
| 3 | Space, scale and layout | – | complete |
| 4 | Responsive design | – | complete |
| 5 | Hierarchy and composition | – | complete |
| 6 | Motion and animation | – | complete |
| 7 | Interaction and state | – | complete |
| 8 | Accessibility | – | complete |
| 9 | Platform conventions | – | complete |
| 10 | Performance as a design constraint | – | complete |
| 11 | Modern CSS capability | – | complete |
| 12 | Design directions and styles | – | complete |
| 13 | Craft and micro-details | – | complete |
| 14 | Process, taste and ideation | – | complete |
| 15 | Brand corpus findings | – | complete |

**Status: 300 searches complete. Corpus read (theses, 449 prohibition rules, responsive strategies, iteration guides). Ready for consolidation.**

**Native split:** platform-specific material lives in `~/.claude/skills/design-native/study-book/learning.md`. Moved out of this file: §9.1 iOS Liquid Glass, §9.2 Material 3 Expressive, §9.3 iOS vs Android, §9.6 Mobile navigation, §9.9 PWA install, §6.18 Haptics – each leaves a pointer stub so existing §-references still resolve. Shared with the native file (present in both because the principle is platform-agnostic and the numbers are native): §4.4 touch targets, §4.8 safe areas, §4.12 thumb zone, §6.4 interruptibility, §6.25 bottom sheets, §6.26 gestures.

---

## 1. Typography

### 1.1 The scale

**A type scale is a closed set of sizes, and every size in it must have a job.** Two sizes doing the same job is the most common scale failure – it produces hierarchy that reads as noise. Ratio-generated scales (1.2 minor third, 1.25 major third, 1.333 perfect fourth, 1.5 perfect fifth) are a starting generator, not the answer: the generated values need rounding to something the grid and the font's metrics agree with, and the very top of a geometric scale grows faster than any layout can use.

| Pattern | Cost | Fix |
|---|---|---|
| Ratio scale used raw, unrounded | Sizes like 43.9453px that no other system agrees with | Generate, then round to the nearest usable value and lock the set |
| More than ~7 sizes in a system | Nobody can hold the hierarchy; two sizes end up meaning the same thing | Cut to the sizes that have distinct jobs |
| One ratio across the whole range | Big end explodes, small end compresses to indistinguishable steps | Use a tighter ratio for UI sizes and a wider one for display sizes, or hand-tune the ends |

### 1.2 Fluid type

`clamp(min, preferred, max)` is Baseline Widely Available, >97% global support as of 2026.

| Pattern | Cost | Fix |
|---|---|---|
| **Preferred value is pure `vw`** – `clamp(1.5rem, 4vw, 3rem)` | **Text stops responding to browser zoom.** The clamp bounds only engage at the extreme edges, so the entire middle range is zoom-dead. This is a WCAG 1.4.4 failure in practice | Always include a `rem` term: `clamp(1.5rem, 1rem + 2vw, 3rem)` |
| Fluid type applied everywhere by reflex | Fights any design that needs a deliberate discrete state (a weight change, a different face, a recomposed layout at a threshold) | Fixed values or a media query where the design genuinely changes state; fluid only where continuous scaling is what you actually want |
| A fluid formula more complex than the rules it replaces | Unmaintainable; nobody can predict the output | Two or three explicit sizes beats an unreadable formula |

Note for consolidation: fluid type solves *between* breakpoints. It does not remove the need for breakpoints where composition changes. `[source: moderncsstools.com fluid typography guide; thecrit.co CSS typography 2026]`

### 1.3 Measure (line length)

- **50–75 characters per line for body text; 66 CPL is the classic optimum.** Do not exceed ~70–75.
- Dyson & Haselgrove: **~55 CPL supports effective reading at both normal and fast reading speeds** – it is the length that does not degrade when the reader speeds up.
- Short lines (30–50 CPL) favour accuracy and careful reading. Right for technical docs, form labels, captions, and most mobile body text.
- Mechanism: line length governs the **return sweep** – the saccade from the end of one line to the start of the next. Too long and the eye loses the correct next line; too short and the sweep interrupts the reading rhythm too often. This is why the rule is in characters, not pixels: it is about eye movement per line, not about width.
- Emil Ruder (1967) landed on 50–60 characters by the same reasoning.

`CONFLICT / nuance:` measured **reading rate rises with longer lines** (a 7.3in line was the fastest measured; 4in the slowest), but **users prefer the shorter line**. Speed and preference point in opposite directions. Design implication: for content people *choose* to read, follow preference (65ch); for content people *must* process quickly under instruction, the longer line is not the error it is usually called. Record as a genuine trade-off, not a rule. `[source: Visible Language literature review; Baymard; Human Factors International]`

### 1.4 Leading

| Context | Value | Why |
|---|---|---|
| Body text | 1.4–1.6 (1.5 default) | WCAG 1.4.12 requires content to survive user-set line spacing of 1.5, so 1.5 is both the good default and the compliance floor |
| Display / headline | 1.0–1.2 | Large glyphs are taller, so the same ratio produces visually excessive space. Big type needs proportionally less |
| UI labels, captions | 1.3–1.45 | Short strings, no return sweep to support |

- **Leading and measure are coupled.** Longer lines need more leading so the return sweep lands on the right line. A wide column at 1.4 will read worse than a narrow one at 1.4.
- **A single global `line-height` is a design smell.** Headings, body and UI labels have different jobs and different metrics.
- Body copy in print terms is 120–145% of type size; the web's 1.5 default is on the generous side of that, correctly, because screens and viewing distance differ.

### 1.5 Tracking (letter-spacing)

| Case | Value | Reason |
|---|---|---|
| All-caps | **+0.05em to +0.1em** | Capitals were drawn to sit beside lowercase, where ascenders and descenders create rhythm. Remove the lowercase and the caps crowd. This is a mechanism, not a taste |
| Large display type | **−0.01em to −0.02em**, occasionally to −0.03em | Sidebearings that are correct at 16px are proportionally excessive at 64px. The type looks loose unless tightened |
| Body / lowercase text | **0** | Do not track body text. Almost every tracked body paragraph is worse than the untracked one |
| Small text (<14px) | slight positive, ~+0.01em | Counters close up at small sizes |

Always use `em`, never `px`, so tracking scales with the size it belongs to.

### 1.6 Optical sizing

- `opsz` is a registered OpenType design-variation axis. It adapts **glyph proportions, stem weights and detail** to the size the text is set at: thicker strokes and larger serifs when small, finer detail and more stroke contrast when large.
- `font-optical-sizing: auto` is **the default and already on** for any variable font carrying an `opsz` axis. The browser and the font negotiate it with no work from you.
- Typical text range on the axis is 10–16.
- The check that matters: a variable font is not automatically optically sized. Verify the font actually ships `opsz` before claiming the benefit.
- Prefer the registered axis properties (`font-weight`, `font-optical-sizing`) over raw `font-variation-settings`, which overrides them and breaks inheritance.

`[sources: MDN font-optical-sizing; OpenType spec dvaraxistag_opsz; Google Fonts Knowledge; Pixelambacht "Optical size, the hidden superpower of variable fonts"]`

### 1.7 Choosing a typeface for screen

- **X-height is the single most predictive metric for small-size legibility.** Large x-height reads better small; small x-height needs more size and more spacing to reach the same legibility. Sweet spot for body text is an **x-height 65–75% of cap height**. Inter sits around 0.53–0.55 of cap height by one measure – note the number is reported inconsistently across sources, so treat "generous x-height" as the reliable claim and the exact ratio as unverified. `CONFLICT`
- **Trade-off nobody states:** raising x-height at a fixed point size proportionally shortens ascenders and descenders, which **reduces letterform distinctiveness**. Very high x-height faces read well at 13px and get monotonous at 48px. This is why some faces are great for UI and wrong for display.
- Verdana and Georgia were drawn by Matthew Carter specifically for screen legibility at small sizes and still hold up. Inter, Roboto, Open Sans and Noto Sans were drawn with screen x-heights.
- **Glanceable reading is a different problem from sustained reading** (NN/g: "bigger is better" for glanceable text). Nav labels, KPI values, wayfinding and anything read in under a second want more size and weight than the same content in a paragraph.

### 1.8 Pairing

- The failure mode is **conflict, not contrast**: two faces that are *similar but not identical* read as a mistake. Either concordant (one family) or clearly contrasting. The dead zone in between is where amateur pairings live.
- The reliable structural test: **similar x-height and compatible proportions, different skeleton.** Shared DNA, different voice.
- **Superfamilies remove the risk entirely** – IBM Plex, Source, PT, Noto ship serif and sans members drawn to match, with x-heights and proportions pre-matched. Use when the project cannot afford a pairing experiment.
- A single family across two or three weights is a legitimate answer, not a cop-out. Most great UI is one face.

### 1.9 Hierarchy mechanics

Four levers: **size, weight, color/contrast, space.** Ranked by how they behave.

| Lever | Strength | Cost |
|---|---|---|
| Size | Most universally understood signal of rank | Consumes screen real estate; poor lever in dense UI |
| Weight | Creates rank **without spending space** – the best lever in dense interfaces | Needs real distance: 400 vs 500 is invisible on most screens, 400 vs 600/700 reads |
| Color / contrast | Excellent for de-emphasis (secondary text at lower contrast) | Fails for anyone with a color vision difference, and fails in bright sunlight. Never the sole signal |
| Space | Groups and separates without any type change at all; often the strongest and most overlooked | Costs vertical real estate |

- **A one- or two-step size difference does not create hierarchy** – it reads as an error. Steps must be unmistakable.
- **The test:** strip all color to grayscale. If heading, body and caption are still distinguishable, hierarchy is real. If not, color was carrying it and the hierarchy is weak.
- `CONSOLIDATE:` this grayscale test belongs in the verification register – it is checkable, not assertable.

### 1.10 Micro-typography

| Property | Use | Note |
|---|---|---|
| `text-wrap: balance` | Headings, subheadings, blockquotes, short strings | Balances line lengths so a heading does not leave one word alone. Browser-imposed line limit (typically ~6 lines) – it is designed for short text, not paragraphs |
| `text-wrap: pretty` | Body paragraphs | Targets the **widow** – the single word alone on the last line – by adjusting earlier lines so at least two words land on the final line |
| `font-variant-numeric: tabular-nums` | Any number that appears in a column, KPI, price, timer, table, or that updates in place | Digits get equal advance width so columns align and updating numbers do not jitter. Letters stay proportional. Fallback `font-feature-settings: 'tnum'` |
| Proportional (default) figures | Body copy | Tabular digits look mechanically spaced in running prose |

**The updating-number case is the one people miss:** a live counter or timer in proportional figures visibly twitches as digit widths change. `tabular-nums` fixes it. That is a "smooth" detail with a one-line fix.

`text-wrap: pretty` vs `balance` is not interchangeable – `balance` optimizes for even line lengths, `pretty` optimizes the last line only and is cheaper on long text.

### 1.11 Vertical rhythm and the baseline grid

`MYTH-adjacent.` Vertical rhythm is a **craft tradition producing calm, consistent pages – not a proven perceptual necessity.** Excellent typography breaks it deliberately and often.

- On the web the baseline grid fights back hard: element heights are content-dependent and responsive, headings rarely land on a multiple, captions and list items resist, and images have arbitrary heights.
- The defensible position: **aim for it in main body content, let peripheral elements break it.** Do not spend hours forcing every element onto the grid – the returns collapse fast.
- What actually delivers the perceived benefit is the cheaper thing: a **consistent spacing scale** and consistent leading, not literal baseline alignment.

### 1.12 Font loading

| Value | Block period | Swap | Use for |
|---|---|---|---|
| `swap` | ~100ms | infinite | Brand-critical headings, **with preload**. Font always eventually applies |
| `optional` | ~100ms | **zero** | Body text where stability beats brand. If not ready in ~100ms the fallback is used for the whole page view and the font is cached for next navigation. **Guarantees zero font-swap CLS** |
| `block` | up to 3s invisible | infinite | Icon fonts only |
| `fallback` | ~100ms | ~3s | Middle ground |

- **`size-adjust`, `ascent-override`, `descent-override` and `line-gap-override` on a `@font-face` fallback rule are the real fix for swap CLS** – they reshape the fallback's metrics to match the web font, so the swap causes no reflow. Combine `swap` + metric-matched fallback and you get brand type with no layout shift.
- Subsetting is where the size actually goes: Roboto Regular 168KB TTF → **12KB subsetted WOFF2 (93% reduction)**. Inter variable 303KB → **22KB** Latin-only.
- **`unicode-range` does not subset anything.** It only tells the browser which already-subset file to fetch. Physically subset first, then declare ranges.
- WOFF2 (Brotli) compresses ~30% better than WOFF. There is no reason to ship anything else in 2026.
- **Variable fonts break even around 3 weights.** Below that, two static subset WOFF2 files are smaller. Above it, one variable file wins. A subsetted variable WOFF2 runs ~40–60KB with full axis range.

### 1.13 System stack vs custom type

| | System stack | Custom face |
|---|---|---|
| Load cost | Zero. Renders instantly | Network latency, render-blocking risk, CLS risk |
| Rendering | Hinted and tuned by the OS vendor for that platform's screens | Whatever the foundry shipped |
| User settings | Respects OS-level font accommodations that some users depend on | Can override them |
| Familiarity | People read faster and more accurately in faces they meet daily | Costs a moment of adaptation |
| Brand | None | The point |

The honest middle: **system stack for body, one custom face for headings.** Brand where it is seen, speed where it is read. Ask the user – never decide this silently, and always ask for font choices.

### 1.14 Accessibility and reading difficulty

`MYTH:` **Dyslexia-specific fonts do not work.** Peer-reviewed studies find no reading improvement from Dyslexie or OpenDyslexic over Arial or Times New Roman. The evidence for them does not survive scrutiny.

What the research does support:

- **Larger font size.**
- **Sans-serif over serif; roman over italic; monospaced performs well.** Rello & Baeza-Yates measured sans-serif, monospaced and roman styles significantly outperforming serif, proportional and italic.
- **Increased letter spacing** has a measured positive effect on reading for dyslexic readers – one of the few places tracking body text is justified.
- **Increased line spacing.**
- Consistent section headings and structure.

Design implication: the accommodation is *ordinary good typography, turned up* – not a special font. And a user-controllable text-size and spacing setting beats any font choice you make for them.

`[sources: Rello & Baeza-Yates "Good Fonts for Dyslexia"; Edutopia; Nessy; NN/g glanceable fonts; web.dev font best practices; MDN font-variant-numeric; Smashing "CSS Baseline: The Good, The Bad And The Ugly"; Google Fonts Knowledge superfamilies]`

### 1.15 Long-form reading

- Reaffirms the measure rule: **50–75 CPL, ~8–12 English words per line.**
- *"A larger font size is inviting – it implies the user is free to lean back and settle in rather than angle forward toward the screen."* Reading posture is a design outcome. Long-form wants **18–21px**, not 16.
- **Serif vs sans matters far less than size, spacing and contrast.** Worth stating, because the serif/sans debate consumes attention that belongs on the three variables that actually move comprehension.
- Structure carries as much as type: digestible chunks, meaningful subheadings, generous white space, images breaking the column, a conclusion that points onward.
- *"A long-form article is less about designing an interface than designing text so that reading is relaxed and comfortable."*

### 1.16 Minimum sizes

- **16px is the floor for body text on the web**, and it is the browser default in every major browser plus Google's mobile-friendliness minimum.
- The grounding: at typical phone holding distance (**25–35cm**), 16px is at the **lower edge** of comfortable reading for normal vision. **14–15px is genuinely difficult** for anyone with reduced acuity that is not severe enough to warrant assistive tech – which is a very large population.
- **16–18px is the sweet spot for text-heavy pages**; line-height 1.5–1.6.
- iOS HIG: 11pt absolute minimum, **17pt recommended for body**.
- Cross-reference §4.9: 16px on inputs is also the iOS auto-zoom threshold. Same number, two independent reasons.

### 1.17 Font licensing – the practical trap

- Two pricing models: **flat fee per domain**, or **tiered by monthly pageviews**.
- The trap: *"a site that grows from 50,000 to 5 million pageviews can jump from a one-time $50 to a recurring four-figure annual bill."* A pageview-metered license is a liability that scales with success.
- **Buy a self-hosted web license with no pageview cap** where possible. Fontspring and several independent foundries (Klim, Grilli Type, Dinamo, Commercial Type) offer flat-fee, uncapped self-hosted licenses – usually the best terms.
- **A variable font is one file but not one license discount** – each deployed file still needs coverage; fewer files does not reduce obligation.
- Self-hosting gives privacy and performance control but puts the licensing burden on you, including not stripping font metadata.
- Google Fonts: free, open licenses, commercial use fine.

`CONSOLIDATE:` when the skill recommends a typeface it should say which licensing category it is in. Recommending a beautiful foundry face without mentioning a pageview meter is a real disservice.

### 1.18 Micro-typography details

| Item | Rule |
|---|---|
| **Quotes** | Curly/directional (“ ” ‘ ’) in prose, always. **Straight quotes only in code and data.** Straight quotes in body copy are the fastest visible tell of unconsidered typography |
| **En dash (–)** | Ranges and spans – "July 5th–9th", "10–20%". Unspaced |
| **Em dash (–)** | Parenthetical breaks. **Bringhurst recommends a spaced en dash instead**, because em dashes in many faces are wide enough to be visually disruptive. `[Project convention here uses the en dash for all prose dashes.]` |
| **`quotes` property** | Standardizes rendering of `<q>` and `open-quote`/`close-quote` content across platforms |
| **`font-feature-settings`** | Gateway to the rest: ligatures, alternates, stylistic sets. Note several corpus brands enable specific sets as brand signals – Raycast runs Inter `ss03` site-wide, Resend uses Domaine `ss01/ss04/ss11` |
| **Hyphenation** | `hyphens: auto` plus a `lang` attribute. Valuable in narrow columns; risky in headlines |

These are individually trivial and collectively the difference between typography that looks set and typography that looks typed.

### 1.19 UI type vs body type

**UI text is read in fragments – labels, values, statuses, actions – not in sustained paragraphs.** That single distinction drives everything:

| Role | Size | Weight |
|---|---|---|
| Display / H1 | 36px+ | Per direction (§15.3) – often 400–600, not 700 |
| Section heading | 20–28px | 600 |
| **Body** | **16–18px** | 400 |
| **Buttons, nav labels, sub-headers** | 14–16px | **500–600** – "stands out from body text without overpowering" |
| Caption / footnote | 12–14px | 400 |

- **Two or three weights per typeface. More reads as chaotic.**
- The reason UI labels take more weight than body text: they must be **recognized at a glance in the periphery**, not read in sequence. Weight buys recognition without buying space (§1.9).
- Leading differs too – UI labels want 1.3–1.45, not the 1.5 of body text (§1.4).

### 1.20 The one-family constraint

Working within a single family is a legitimate and often superior choice, not a compromise:

- **"Every member of the family is designed to work with every other member."** Harmony is guaranteed rather than negotiated.
- Weight, width and slope give real variation *"without breaking the system"* – all variants share design DNA.
- It streamlines every later decision and produces cohesion automatically.
- **The requirement: the family must be deep enough.** One weight is not a system. A family with 4–7 weights plus italics, and ideally a width axis or a mono companion, does everything a pairing would.

Confirmed hard by the corpus (§15.10) – a large share of the 74 brands run a single proprietary family across the entire system, with hierarchy carried by size and weight alone.

## 2. Color

### 2.1 Why OKLCH, precisely

HSL's `L` is **geometric, not perceptual** – it describes a position in a mathematical cylinder, not brightness as the eye reports it. Two colors at identical HSL lightness look mismatched because perceived brightness varies by hue (yellow at L=50% is far brighter than blue at L=50%). OKLCH (Ottosson, 2020, on top of OKLab) gives three axes where **equal numeric distance is equal perceived distance**.

Practical consequences that actually change the work:

| Task | HSL behavior | OKLCH behavior |
|---|---|---|
| Brighten a button on hover | Brightening desaturates noticeably | Raise `L` alone; chroma is preserved |
| Raise contrast by pushing saturation | Also shifts perceived brightness, unpredictably | Axes are independent |
| One lightness ramp across many hues | Steps look uneven between hues | Steps look even |
| Going out of sRGB | Naive per-channel clipping shifts hue *and* lightness | Browser gamut-maps by reducing chroma at constant L and h – the correct algorithm, done for you |

The last row is the underrated one: **CSS gamut-maps OKLCH correctly and automatically**, no media query, no script.

### 2.2 Building a scale

- Pick the brand color, convert to OKLCH, hold `H` constant, vary `L` in perceptually even steps. A linear ramp from **L≈0.97 (near white) to L≈0.15 (near black)** across 11 steps (50–950) is a working starting point.
- Chroma cannot be constant across the ramp – it must fall at both ends, because near-white and near-black cannot hold chroma. A bell curve peaking in the middle is the standard shape.
- Tailwind, Radix and Material all use the 50–950 tonal-scale idea. Radix's contribution is that **each step has a defined job** (backgrounds, subtle backgrounds, borders, solid fills, text) so a designer picks by role, not by eye. See §2.11 for the full step-by-step semantics.
- ColorBox (Lyft) exists specifically because naive lightness adjustment is *not* perceptually even.

### 2.3 Contrast: WCAG 2 vs APCA

| | WCAG 2.x | APCA |
|---|---|---|
| Model | Simple relative-luminance ratio | Lightness contrast `Lc`, 0 to ~±106 |
| Inputs | Two colors | Two colors **plus font size, weight and polarity** |
| Directional | No – dark-on-light and light-on-dark score identically | **Yes** – polarity-aware, which matches how the eye actually works |
| Thresholds | 4.5:1 normal, 3:1 large/bold | Lc 90+ body, 75+ normal, 60+ large, 45+ UI |
| Legal status | **The current standard. What the law recognizes** | Candidate method, not adopted |

`MYTH / important:` **WCAG 2's formula ignores spatial frequency** – how small and how thin the strokes are – which is a *primary* driver of human contrast perception. Tested against thousands of random color pairs, **WCAG 2 incorrectly passed 49%** of pairs that were too low-contrast to actually read.

Status as of 2026: visual contrast was **moved out of the WCAG 3 Working Draft in July 2023** and the WCAG 3 algorithm is still undetermined. So:

**The working position: ship against WCAG 2.2 because that is what compliance means, and check APCA because that is what readability means. Audit against both. Where they disagree, WCAG 2 passing but APCA failing means real users will struggle – fix it anyway.**

### 2.4 Dark mode

Not an inversion. A separate system.

| Rule | Value | Why |
|---|---|---|
| Never pure black `#000` | Base around `#121212` / L≈0.13–0.15 | Pure black maximizes contrast against everything, causing **halation** – light text appears to bloom and edges vibrate. Worst for light-sensitive users and for astigmatism |
| Never pure white text | ~`#E8E8E8`, L≈0.88–0.93 | Same reason, other direction |
| **Elevation by surface lightness, not shadow** | Each layer up = slightly lighter surface | Shadows are nearly invisible on dark ground. Lightness is the only depth cue that works |
| Desaturate brand colors | **Blues and cyans typically need 20–30% saturation reduction** | Saturated hues on dark ground produce optical vibration and eye strain |
| Recalibrate per color | Individually, not by formula | Each hue's vibration threshold differs |

The elevation rule is the one that separates a real dark theme from an inverted one: in light mode elevation reads as shadow, in dark mode elevation reads as **light**. Surfaces get lighter as they come forward, because in the physical metaphor they are closer to the light source.

### 2.5 Token architecture

Three tiers, and the **direction of reference is the whole rule: component → semantic → primitive.**

| Tier | Example | Rule |
|---|---|---|
| 1. Primitive / reference | `--blue-500: oklch(0.64 0.147 255)` | Raw values, no meaning. **Never referenced by a component** |
| 2. Semantic / alias | `--color-action-primary: var(--blue-500)`, `--color-surface-default` | Describes **intent**. This is what components consume |
| 3. Component | `--button-bg-primary: var(--color-action-primary)` | Per-component override slot for theming one component |

Retheme the product by changing tier 2. Retheme one component by changing tier 3. A component pointing straight at a primitive destroys both capabilities – that is the violation to look for in any audit.

### 2.6 Color vision deficiency

- **Blue, orange and dark blue are the safest hues across all major CVD types.** Blue is perceived reliably under deuteranomaly, protanomaly and most others.
- **Okabe-Ito (Wong) 8-color set** is the validated standard: blue `#0072B2`, orange `#E69F00`, vermillion `#D55E00`, reddish purple `#CC79A7`, bluish green `#009E73`. Distinguishable under protanopia, deuteranopia and tritanopia.
- **Kill combinations:** red/green, green/brown, blue/purple, green/blue, light green/yellow. Red and green both read as brownish-yellow under deutan and protan vision.
- **Use teal, not green, for success.** Teal carries enough blue to stay separable from red under deuteranopia. Plain green does not. This is the single highest-value swap in most product palettes.
- Safe hues and sufficient contrast are **two separate jobs**. Passing one does not imply the other.
- Where a risky pair is unavoidable: add an icon, a label, a pattern or a position difference. Color is never the sole carrier of meaning.

### 2.7 Wide gamut

- P3 holds ~50% more colors than sRGB. **The benefit is concentrated entirely in highly saturated colors** – a pastel, muted or near-neutral palette gains essentially nothing.
- Where it does pay: saturated brand accents, and **categorical data-viz colors**, where more perceptually distinct hues means easier category separation.
- Always ship an sRGB declaration first, then the wide-gamut one – older browsers take the first, capable ones take the second. `@media (color-gamut: p3)` detects hardware support.

### 2.8 Gradients

- **CSS gradients interpolate in sRGB by default, and sRGB interpolation produces a muddy, desaturated band through the middle.** This is the reason so many gradients look grayish in the center and nobody can explain why.
- Fix: `linear-gradient(in oklch, ...)`. Support ~93% (Chrome 111+, Firefox 113+, Safari 16.2+).
- **OKLab vs OKLCH for interpolation:** OKLab is cartesian and cuts a straight line between the two colors, losing chroma in the middle for distant hues. OKLCH is polar and rotates through hue, keeping chroma. **For similar hues they are nearly identical; for distant hues use OKLCH.**
- **Banding is a separate problem from interpolation.** Large gradients band on 8-bit displays regardless of color space. Fix with a subtle noise/grain overlay, or angle the gradient so the bands run diagonally and read as less regular.

### 2.9 Color psychology

`MYTH, with nuance.` "Blue means trust" is methodologically weak. Findings that matter:

- Cooler hues and **lower saturation** correlate with higher reported trust – saturation may matter more than hue.
- Context inverts it: **green tested as more trustworthy than blue on an outdoor-activity site.** The association is contextual, not intrinsic.
- Field-wide methodological problems: inadequate color specification, underpowered samples, oversimplified stimuli, and heavy moderation by culture, age, gender and personality.
- **Color supports trust; it cannot create it.** A badly designed product with a blue interface still feels unreliable.

Use in practice: treat color meaning as **culturally and contextually contingent**, decide from brand and differentiation rather than from a psychology chart, and never justify a palette to a user with a psychology claim you cannot source.

### 2.10 60-30-10

`MYTH-adjacent.` Useful as a **lesson in dominance hierarchy**, misleading as a formula. It has no research basis, and plenty of excellent work breaks it. What survives from it:

- One color should clearly dominate.
- The accent should be genuinely scarce – scarcity is what makes it read as an accent.
- Three colors that clash still clash at any ratio. The ratio does not create harmony.

Keep the principle (**one dominant, one supporting, one scarce accent**), drop the percentages.

`[sources: Adrian Roselli "WCAG3 Contrast as of April 2026"; Myndex WCAG2-vs-APCA gist; Evil Martians "OKLCH in CSS"; Smashing "Falling For Oklch"; WebKit display-p3; CSS Color Level 4; Okabe-Ito/Wong palette; ColorArchive token naming guide; "The Psychological Meaning of Color in Design: A Semantic Review"]`

### 2.11 The Radix 12-step scale – the best-designed color system to steal from

Every step has a **defined job**, so a designer picks by role rather than by eye. This is the single most useful color artifact found so far.

| Step | Job |
|---|---|
| 1 | App background |
| 2 | Subtle background |
| 3 | UI component background – **normal** |
| 4 | UI component background – **hover** |
| 5 | UI component background – **pressed / selected** |
| 6 | Subtle border on **non-interactive** components: sidebars, headers, cards, alerts, separators |
| 7 | Border on **interactive** components; also focus rings |
| 8 | Border on interactive components, **hover** |
| 9 | **Solid.** Highest chroma in the scale – the purest step, least mixed with white or black. Designed for white foreground text |
| 10 | Solid, hover |
| 11 | **Low-contrast text** |
| 12 | **High-contrast text** |

Two details worth carrying into the skill:

- **Steps 11 and 12 are guaranteed Lc 60 and Lc 90 APCA against a step-2 background of the same scale.** A color system with contrast guarantees baked into the step definitions is the right idea – it makes the accessible choice the default choice rather than a later audit.
- **Step 9 is defined as the chroma peak**, which is why it is the brand/solid step. This confirms the bell-shaped chroma curve from 2.2 and gives it a purpose rather than an aesthetic justification.

### 2.12 Deriving colors instead of storing them

Relative color syntax `oklch(from var(--c) L C H)` sits at ~89.6% support (Feb 2026); `color-mix()` is **Baseline Widely Available**.

The pattern that has aged best: **keep the reference layer flat (raw OKLCH steps) and derive variants in the semantic layer.** Token count stays small; hover, active, disabled and tinted states become infinite.

```css
--btn-hover:  oklch(from var(--btn-bg) calc(l - 0.1) c h);
--text-muted: oklch(from var(--text) l calc(c * 0.3) h);
--focus-ring: oklch(from var(--primary) 0.7 calc(c * 1.2) h / 0.5);
--dark-surface: oklch(from var(--surface) calc(1 - l) c h);
```

The caution: derived values are only as good as the base. A derivation chain three deep is unreadable and un-auditable. Derive one step from a token, not four.

### 2.13 Contrast in practice

- **Aim for Lc 90+ on columns of body text; Lc 75 is the practical minimum where readability matters.**
- Tools: the official APCA calculator (`apcacontrast.com`), the `apca-w3` library for automation, and **Bridge-PCA**, which reports both an Lc value and a backwards-compatible WCAG 2 ratio – the practical bridge while WCAG 3 remains undecided.
- The workflow that resolves §2.3: **keep meeting WCAG 2.x for compliance, apply APCA during design for real-world readability**, and *"document APCA findings as contrast guidance inside your design system"* so it becomes repeatable rather than a per-screen argument.

This is the answer to the two-standards problem: do not choose between them, **encode both into the token layer** the way Radix does (§2.11).

### 2.14 Choosing a brand color

The competitive-audit method, which is the only genuinely useful thing found in this area:

- **Audit direct competitors before finalizing.** *"If three or more use the same dominant color family as your proposed scheme, your palette is already a liability."* That is a hard, checkable test.
- Differentiate by shifting family (if the category is blue and gray, look at green or purple) or by shifting within family (mint instead of emerald).
- **The accent is where a conventional brand can break the category** – a finance brand keeping trust-blue as its base and using orange as its accent.
- Then apply the dominance hierarchy from §2.10: one dominant, one supporting, one scarce accent.

`Note:` this is a much better basis for a color decision than a psychology chart (§2.9). The question is not "what does blue mean" but "what is everyone else in this category already using."

### 2.15 Color harmony schemes

The mechanism worth keeping: **when colors share a clear geometric relationship on the wheel, the eye reads them as a set rather than as a collection of accidents.** That is the whole value of harmony schemes – they buy coherence, not beauty.

| Scheme | Character | Best use |
|---|---|---|
| **Analogous** (3–5 adjacent) | Harmonious, low-tension, easily monotonous | Most web design. Pair with one complementary accent for contrast |
| **Complementary** (opposite) | Maximum contrast and visual tension | A single decisive accent against a base |
| **Triadic** (three equidistant) | Vibrant, balanced, three *equally weighted* accents | **Data visualization, multi-category dashboards, tags, status systems** – anywhere categories must be distinguishable and none should dominate |

The triadic note is the useful one: most harmony advice is aimed at brand palettes, but triadic's real strength is **categorical encoding**, where equal weight is a requirement rather than an aesthetic.

Convert the scheme to OKLCH before using it (§2.1) – wheel relationships defined in HSL will not have even perceived lightness.

## 3. Space, scale and layout

### 3.1 The 8pt grid, and the actual reason for it

The usual justification ("most screen sizes divide by 8") is weak. **The real reason is device pixel ratios:** 8 subdivides evenly into the 1x, 1.5x, 2x and 3x densities used by Android and high-DPI displays, so every value lands on whole pixels on every common device. 4pt half-steps are legitimate for tight UI; anything finer starts producing fractional pixels somewhere.

The other real reason is not mathematical: **a discrete scale forces deliberate spacing decisions.** Eyeballed values are what produce interfaces that feel subtly unpredictable. The constraint is the point.

Working scale: `4, 8, 12, 16, 24, 32, 48, 64, 96, 128`.

### 3.2 The cardinal spacing rule

**Internal padding ≤ external gap.** When the space *inside* a group exceeds the space *between* groups, the grouping inverts and the eye reads the wrong structure. This one rule catches more real layout bugs than any other single check, and it is mechanically verifiable from the CSS.

### 3.3 Naming the scale

- **Numeric beats t-shirt sizing at scale.** T-shirt names (`xs, sm, md, lg, xl`) collapse once the system grows – you end up at `2xl`, `3xl`, and eventually inserting a value between `sm` and `md` with nowhere to put it. Numeric (`space-100…space-1000`, or `space-1…space-10`) inserts cleanly.
- **Never mix conventions.** `spacing_md`, `spacing_4` and `spacing_8px` for the same concept is the failure that produces token chaos.
- Naming the token rather than using the raw value means the scale can be re-tuned later without touching a single component.

### 3.4 Gestalt, as engineering rather than trivia

Only two of the Gestalt principles do heavy lifting in interfaces:

- **Proximity** – elements close together are read as one group. Free, costs no ink, and it is what spacing *is for*. Most "this layout is confusing" problems are proximity problems.
- **Common region** – a shared boundary groups elements even when spacing cannot. This is the entire justification for the card. **Common region is the tool when density prevents you from using space** – which is most dashboards.

Practical consequence: when a layout is crowded, the choice is *add space* (proximity) or *add a boundary* (common region). Adding both is usually redundant. A card with generous internal padding, a border, a shadow and a background tint is saying the same thing four times.

### 3.5 White space

- Wichita State lab research: **white space improves reading comprehension, though it may reduce reading speed.** That is the honest finding – it is a trade, not a free win.
- Widely repeated: "adequate white space improves comprehension by 20%" and a Crazy Egg case study where **+20% white space around key elements lifted conversions 13%.** `[unverified – both are secondhand and repeated without primary citation. Treat as directional, do not quote as fact.]`
- **Micro white space** (between lines, list items, nav links) versus **macro white space** (between major regions and around the layout). They do different jobs: micro white space governs readability, macro white space governs hierarchy and calm.
- `MYTH:` "white space is wasted space." The counter-argument that actually persuades a stakeholder is not aesthetic – it is that **density has a cost in comprehension and error rate**, and that a denser page which is misread is not a more efficient page.
- **Density is a legitimate design choice, not a failure.** Bloomberg Terminal, Linear, and a flight-booking back office are all correctly dense. The rule is not "more space" – it is *space proportional to how carefully the content must be read.*

### 3.6 Optical alignment

**Mathematical centering is not visual centering.** The computer knows width, height and position; it does not know where an object's visual weight sits.

| Case | What happens | Fix |
|---|---|---|
| Play triangle in a circular button | Triangle's mass is on the left, so mathematically centered looks left-shifted | Shift right until the sharp edges are equidistant from the circle |
| Any asymmetric icon in a round or square container | Same failure | Balance the *mass*, not the bounding box |
| Text in a button with an icon | Icon's optical weight pulls the pair off-center | Adjust padding asymmetrically |
| Capital-letter-only text in a container | Cap height without descenders sits visually high | Nudge down, or use a container whose padding accounts for it |

Method: draw a crosshair through the container center and shift the shape until its **heaviest parts** balance around it.

This is one of the highest-leverage "the details compound" items – it is invisible when done and unmistakably amateur when not.

### 3.7 Nested radius

**`inner radius = outer radius − padding`** (R1 + D = R2).

The geometry: the outer corner arc is centered at (R, R); a child inset by p with radius r has its arc centered at (p+r, p+r). They are concentric only when `p + r = R`. When the curves share a center, the gap between the two shapes stays visually even the whole way around – which is what "expensive" looks like at the corner of a card.

```css
--outer-radius: 16px;
--padding: 8px;
--inner-radius: calc(var(--outer-radius) - var(--padding)); /* 8px */
```

If padding ≥ outer radius, the inner element gets **square corners**, not a negative radius. Wrong nested radii are one of the most common and most invisible causes of a UI feeling slightly off.

### 3.8 Grids

`MYTH:` **every site needs a 12-column grid.** Jen Simmons' central criticism: designers got stuck applying one grid to everything, because that is what the float-and-framework era could express.

**Intrinsic web design** is the current position:

- Fluid columns **and rows** – two-dimensional, not just horizontal.
- **Media queries optional**, not structural.
- **Nested formatting contexts** – flex inside grid inside flow, each solving what it is good at.
- The layout is **intrinsic to the content**, sized by what is in it, rather than content poured into a predetermined grid.

Practical translation: reach for `grid-template-columns: repeat(auto-fit, minmax(min(280px, 100%), 1fr))` and `subgrid` before reaching for a column count. Use an explicit 12-column grid when the design genuinely is a magazine-style layout that needs one, not by default.

### 3.9 Balance and tension

- **Symmetrical balance** reads as stable, formal, calm, institutional. It is also the safe, slightly inert choice.
- **Asymmetrical balance** – different weights on each side, balanced by position and scale rather than mirroring – produces **movement and tension**. One large focal element on one side, several smaller counterweights on the other.
- Tension comes from **contrast held in balance**: a circle against straight lines, horizontal type against a vertical column, a heavy mass against a large void.
- Radial balance emphasizes a single center. Rare in UI; useful in a logo, a hero, a data viz.

The design use: symmetry when the message is trust and stability (finance, institutional, checkout), asymmetry when the message is energy, editorial voice or modernity. A layout that feels boring is very often symmetrical when it should not be.

`[sources: Radix Colors "Understanding the scale"; theosoti.com CSS relative colors; NN/g "Proximity Principle in Visual Design"; Wichita State white space research via UX Myths #28; Cloud Four "The Math Behind Nesting Rounded Corners"; Jen Simmons "Designing Intrinsic Layouts"; Smashing "Compositional Balance, Symmetry and Asymmetry"; Marvel "Optical Adjustment"]`

## 4. Responsive design

The pillar. Fluid systems, breakpoints, container queries, intrinsic layout, touch versus pointer, text zoom, viewport units, orientation, foldables, safe areas, and what actually breaks in the wild.

**Position statement for the skill:** a design that works at one width was never finished. Responsive is not a phase after the design; it is a property the design either has or does not.

### 4.1 Breakpoints come from content, not from devices

- A breakpoint belongs **where the layout starts to break**, found by dragging the window, not by copying a device list. Real content-based breakpoints land on odd values – 642px, 860px, 911px – because that is where the content actually fails.
- The device-width lists (`768 / 1024 / 1280`) are a legacy of a device landscape that no longer exists. They are a starting grid at best.
- The most reliable content trigger is **measure**: when a text column exceeds ~75 characters, that is a breakpoint – cap the width, add padding, or go multi-column.
- `MYTH:` "tablet breakpoint", "mobile breakpoint". There is no tablet. There is a continuum of widths and a set of input capabilities, and they are independent.

### 4.2 Container queries change what "responsive" means

`@container (min-width: 400px)` – Chrome 105+, Firefox 110+, Safari 16+, **>93% global**, production-safe without polyfill.

**The dividing rule, and it is clean:**

| Responsive behavior belongs to… | Use |
|---|---|
| A **component** – how a card lays out its own contents | **Container query** |
| The **page** – sidebar layout collapsing to stacked | **Media query** |

The payoff is the component that stops caring where it lives: a card in a sidebar stacks; the same card in a wide region goes horizontal; no props, no variant, no breakpoint knowledge. **This is what makes dashboards tractable** – each widget is self-contained and does not know whether it is in a 2-column or 6-column grid.

Container queries do **not** replace media queries. Global structure is still a viewport concern.

### 4.3 Viewport units – the mobile 100vh lie

`100vh` is computed against the **largest** viewport (toolbars hidden), but on load the toolbars are visible. So a `100vh` hero is taller than the screen on first paint and the bottom is cut off. This is one of the most common real-world mobile bugs.

| Unit | Meaning | Use for |
|---|---|---|
| `svh` | **Small** – chrome fully visible, worst case | Full-screen sections that must fit on **initial load** without clipping |
| `lvh` | **Large** – chrome fully hidden | Rare; when you want the maximum |
| `dvh` | **Dynamic** – tracks the real visible area live | Anything that should track the actual viewport. Costs reflow as the bar animates |

**Baseline Widely Available since June 2025; ~95% global support in early 2026.** There is no longer a reason to use the JS `--vh` hack.

Practical default: `min-height: 100svh` for hero sections, `dvh` where continuous accuracy matters more than layout stability.

### 4.4 Touch targets

| Standard | Size | Spacing |
|---|---|---|
| Apple HIG | **44×44pt** | ~10px between |
| Material | **48×48dp** | **8dp between, as a separate minimum** |
| WCAG 2.5.8 (AA) | 24×24 CSS px | Sub-24px passes if a **24px circle centered on the target does not overlap a neighbor's circle** |
| WCAG 2.5.5 (AAA) | 44×44 CSS px | – |

- Physical grounding: MIT Touch Lab measured the adult index fingertip at **8–10mm**, the finger pad at **10–14mm**, thumb contact ~**25mm**. 44–48px maps to roughly 9–11mm.
- Reported effect: correctly sized targets **cut mis-tap errors 60–80% and improve selection speed 30–50%**. `[secondhand, treat as directional]`
- **Spacing is a separate requirement from size.** A 48dp target with zero gap around it still fails in practice – adjacent targets get hit.
- **Fitts' Law is not sufficient for fingers.** The 2013 FFitts correction adds a second source of spread tied to the finger's absolute precision – small targets are worse than plain Fitts predicts.
- Thumb reach matters as much as size: the bottom third of a phone screen is comfortable, the top corners are not. Primary actions belong low on mobile, which is the opposite of the desktop instinct.

### 4.5 Input capability is independent of screen size

**Never infer input from width.** A 13" laptop can be a touchscreen; a phone can have a stylus or a connected mouse; a desktop can have a touch monitor.

| Feature | Values | Meaning |
|---|---|---|
| `pointer` | `none` / `coarse` / `fine` | The **primary** pointing device's accuracy |
| `hover` | `none` / `hover` | Whether the primary device can hover |
| `any-pointer` | same | The **union** of all available devices – more than one value can match |
| `any-hover` | same | Same, for hover |

- Hover-dependent UI: `@media (hover: hover) and (pointer: fine)`. Both conditions, because a stylus is fine but does not hover reliably.
- `@media (pointer: coarse)` is the correct trigger for enlarging targets – not a width breakpoint.
- **Anything only reachable by hover is unreachable on touch.** Hover reveals must have a tap or focus path.

### 4.6 Zoom and reflow – the requirement people skip

**WCAG 1.4.10 Reflow (AA):** content must work at **320 CSS px wide** with no loss of information or functionality, and **without scrolling in two dimensions**. 320px is exactly a 1280px viewport at **400% zoom** – the two requirements are the same requirement.

- Vertical-scrolling content: must work at 320px wide. Horizontal-scrolling content: at 256px tall (1024px at 400%).
- **WCAG 1.4.4 Resize Text** additionally requires text to scale to 200% without loss. This is the criterion that **pure-`vw` fluid type fails** (see 1.2).
- Exceptions: tables, maps and some visual media, where reflow genuinely destroys the information.
- **Test method:** desktop browser at 1280px, zoom to 400%, check nothing requires scrolling in two directions. This is manual – automated tools miss most of it.

This belongs in the verification register. It is a five-second check that catches real failures.

### 4.7 Responsive images

- **`srcset` with `w` descriptors must be paired with `sizes`**, or the browser cannot choose correctly.
- **`<picture>` is for art direction and format negotiation** – a different *crop* for narrow screens, or AVIF → WebP → JPEG. `srcset` alone only varies resolution.
- **Generate variants by file-size steps, not device widths.** 400w / 800w / 1200w / 2400w covers almost everything.
- **2x DPR is enough** for content images. 3x is mostly wasted bytes.
- `width` and `height` attributes give the browser an aspect ratio before load – **this is the CLS fix**, and it is free.
- `fetchpriority="high"` on the LCP image; `loading="lazy"` on everything below the fold, never on the LCP image.
- Reported bandwidth saving: 40–70%.

### 4.8 Safe areas and device cutouts

- `env(safe-area-inset-top/right/bottom/left)` **only resolves to non-zero when `viewport-fit=cover` is set** in the viewport meta. Without it, every value is `0px` and the whole mechanism silently does nothing.
- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`
- Applies to notches, punch holes, Dynamic Island, rounded corners and the home indicator bar. A fixed bottom bar without `padding-bottom: env(safe-area-inset-bottom)` sits under the home indicator on every modern iPhone.
- Pattern: `padding-bottom: max(1rem, env(safe-area-inset-bottom));` so there is sensible padding on devices with no inset.

### 4.9 The failure list – what actually breaks in the wild

| Pattern | Cost | Fix |
|---|---|---|
| **Input `font-size` below 16px** | **iOS Safari auto-zooms the whole page on focus** and does not zoom back. Breaks the flow of every mobile form | 16px minimum on inputs, always |
| `100vh` on a mobile hero | Bottom clipped on load | `100svh` |
| Fixed units for layout and type | Rigid; fails at zoom and at unusual widths | `rem`, `%`, `fr`, `ch`, `clamp()` |
| Fixed header eating a short viewport | On a landscape phone the header can take a third of the screen | Cap header height in `vh` terms, or hide on scroll |
| Testing only in browser emulators | Emulators cannot reproduce touch behavior, real network, real GPU, real font rendering | Real devices for anything shipping |
| Hover-only affordance | Invisible and unreachable on touch | `@media (hover: hover)` and a tap path |
| Tables not wrapped | Horizontal page scroll, which is a two-dimension-scroll reflow failure | `overflow-x: auto` on a wrapper, or a card layout below a threshold |
| Long unbroken strings (URLs, IDs, code) | Blow the layout at 320px | `overflow-wrap: anywhere` / `min-width: 0` on flex and grid children |
| **`min-width: auto` default on flex/grid items** | Children refuse to shrink below content size, so the layout overflows with no obvious cause | `min-width: 0` – the single most common invisible cause of overflow |

### 4.10 Mobile-first vs desktop-first

`Trade-off, not a rule.` Mobile traffic >60% globally, 70–80% for retail, food, local services and travel.

- **The genuine argument for mobile-first is not traffic – it is that constraint forces prioritization.** Deciding what survives at 375px improves the desktop design too.
- Desktop-first is defensible for **dense, decision-heavy, comparison-heavy tools** where the desktop experience is the product: dashboards, admin, IDEs, trading, spreadsheets, back-office. Designing those mobile-first produces a weak desktop app.
- Honest heuristic: mobile majority ≥70% → mobile-first. Desktop majority ≥70% → desktop-first. Split → design both explicitly, neither as an afterthought.
- **Ask the user for the traffic split.** This is a question with a real answer that changes the work, which is exactly the kind worth asking.

`[sources: LogRocket "Container queries in 2026"; MDN env(); WCAG 1.4.10 / 1.4.4 / 2.5.5 / 2.5.8; MIT Touch Lab via Human Standards; FFitts Law (2013); Smashing "A Guide To Hover And Pointer Media Queries"; ishadeed.com "Designing better target sizes"; web.dev responsive images]`

### 4.11 Large screens

- **4K (3840×2160) is ~5–8% of desktop traffic; ultrawide 21:9 and 32:9 together ~2–3%** and growing. Small but not negligible.
- **A 4K panel usually exposes a much smaller CSS viewport** because of OS scaling and DPR. Design against CSS pixels, not panel resolution – *"media queries should be based on viewport width, not physical resolution."*
- **Content max-width is the answer, and the corpus agrees** (§15.15): lock at **1280–1440px** and let margins absorb the rest. For a 1920px screen, a **1140–1280px** container is the practical sweet spot.
- What *should* scale with a very wide viewport: side padding, the number of grid columns, and optional secondary panels. What should **not**: measure, type size, or the primary reading column.
- The failure mode at 2560px+ is a hero whose headline stretches to 200 characters and whose CTA sits marooned in the middle of an empty band.

### 4.12 The thumb zone, precisely

Hoober (2013) is still the foundational data: **49% of people hold a phone one-handed**, and Clark found **75% of interactions are thumb-driven.**

**Three zones:** green (bottom-center, no strain) · yellow (mid-screen sides, requires stretch) · red (top corners, awkward or impossible).

The nuance most summaries omit: **the zone size varies enormously by thumb length.** In one study the large-thumb group could reach **>50% of the screen**, while small and medium groups reached only **~30%**, across all phone sizes. Designing for your own reach is designing for the largest hands.

- Primary actions go **bottom-center**. This is why bottom tab bars, bottom sheets and bottom-anchored CTAs win on mobile (§7.35, §9.6, §6.25).
- **The green zone mirrors for left-handed users**, so avoid committing a critical control to one bottom *corner*.
- The top of the screen is for information, not action.

### 4.13 Density as a user setting

**Content density = information visible ÷ space available.** The important insight: it is a **preference split within one product's audience**, not a single correct value.

- Cloudscape ships **comfortable** (default; optimizes readability and cross-device) and **compact** (data-intensive views). Material ships **default / comfortable / compact**.
- *"Compact packs the most rows per screen. Power users living in the table all day almost always want it. Comfortable is the balanced default for mixed audiences."*
- *"If your users span both casual and power-user segments, treat a density toggle as near-mandatory past a certain product maturity."* Google Sheets, Notion and most enterprise admin tools ship one.
- Implement as a **spacing-token swap**, not per-component overrides – which is only possible if spacing is tokenized (§3.3).

This resolves the density argument in §3.5: **stop choosing for everyone and let the user choose**, when the audience genuinely splits.

## 5. Hierarchy and composition

### 5.1 Visual weight – the actual variables

Perceived weight ("optical weight", "dominance") is what makes a composition balanced or not. The factors, and they compose:

| Factor | Effect |
|---|---|
| **Size** | Larger = heavier. The most direct lever |
| **Color saturation / brightness** | Saturated and bright carry more weight than muted |
| **Color temperature** | **Warm advances, cool recedes.** Red is the heaviest hue, yellow the lightest |
| **Position (vertical)** | Elements **higher** in the composition read as heavier |
| **Position (from center)** | **The further from the center, the more weight** – a small element at the edge can balance a large one at the middle. This is the mechanism of asymmetric balance |
| **Density** | A dense cluster carries the weight of its area × its optical density |
| **Texture** | Textured reads heavier than flat – it implies mass |

Practical use: when a layout feels lopsided and the sizes look right, the cause is usually **temperature or density**, not size. A saturated warm block will outweigh a physically larger cool one.

### 5.2 Scale is the cheapest drama

*"A scale jump is powerful, and a single large shape among smaller neighbors becomes a natural focal point."*

The failure mode is **timid scale** – everything within 1.5× of everything else. That produces a layout that is technically correct and completely inert. Real hierarchy usually needs a **jump**, not a step: 3×, 4×, sometimes more between the dominant element and the supporting ones.

This is the composition-level version of the typographic rule in 1.9 (a one- or two-step size difference is not hierarchy, it is an error).

### 5.3 The fold, honestly

`MYTH-adjacent, both directions.` People do scroll – but the fold is still real.

- **NN/g: ~57% of viewing time is spent above the fold**, ~43% below.
- **In 57,453 eye-tracking fixations there is a dramatic drop-off in attention at the fold position.** The 100px just *above* the fold were viewed **102% more** than the 100px just below.
- ClickTale, ~100,000 pageviews: the scrollbar was used on **76%** of pages; **22%** were scrolled all the way to the bottom regardless of length.

The correct conclusion is neither "everything must be above the fold" nor "the fold is dead": **the first view sets whether the rest gets read.** When the hero communicates value quickly, people scroll. The fold is a *gate*, not a container.

Design consequence: do not cram; do earn the scroll. And place a visible affordance that content continues – a partially visible next section is more effective than an arrow.

### 5.4 Hero structure

The four elements that recur in nearly every high-converting hero:

1. **Headline** – carries the heaviest load. Speaks to **what the visitor wants, not what you sell.**
2. **Subheadline** – "if the headline earns attention, the sub-headline earns belief." Who it is for, what problem, what change.
3. **Visual** – usually the first impact, before anything is read.
4. **Primary CTA** – one, unmistakable.

The honest caveat from the sources: *"there is no true blueprint – there are fundamentals, and an open mind ready to test assumptions."* Clarity and logical structure are the constants; the arrangement is not.

### 5.5 Scan patterns

`MYTH, partially.` F-pattern and Z-pattern come from real eye-tracking, but they are **descriptions of behavior on particular content types, not layout templates.**

- F-pattern shows up on **text-heavy, unstructured** content – articles, news homepages. It is largely a symptom of *poor* formatting: users F-scan when nothing helps them.
- Z-pattern applies to **sparse, visually structured** pages.
- The 2026 challenge worth recording: **users increasingly arrive with a pre-formed intent** (often via an AI agent) and *"scan for anchors of intent"* rather than sweeping the page. Hierarchy becomes less about what you want to show and more about **making findable what they already came for.**

Working position: do not lay out to an F or a Z. **Build a strong hierarchy and clear anchors, and the scan pattern takes care of itself.** A well-structured page defeats the F-pattern rather than obeying it.

### 5.6 Golden ratio and rule of thirds

`MYTH, with a small kernel.`

- Fechner (1876): 35% preference for golden rectangles. A 2007 fMRI study found activation associated with phi proportions. **But comparable ratios (1.5–1.75) perform similarly** – which undercuts any claim that phi specifically is special.
- *"It is entirely possible to create a poor UX design that still conforms to the golden ratio."*
- Applied mechanically and out of context it produces nothing.

Keep: **phi is a generator for type scales and layout proportions, not a law.** Small UI elements do better on simpler ratios. Never justify a decision by citing phi – if it looks right it is right, and phi was a starting point.

### 5.7 Navigation structure

| | Sidebar | Top bar |
|---|---|---|
| Best for | Many sections, deep hierarchy – dashboards, admin, project tools | **3–6 primary areas** |
| Screen cost | **~20%+ of width** | **~6%** |
| Scanning | **Vertical lists are found with fewer eye fixations than horizontal ones** – a real, measured advantage | Fewer items, so it matters less |
| Scaling | Grows gracefully; supports trees and folders | Breaks past ~7 items |

**Most mature products use both:** top bar for global context (search, account, notifications), sidebar for primary navigation. The vertical-scanning advantage is the underrated finding – it is why sidebars persist in dense tools despite the space cost.

Validate structure with **tree testing and card sorting**, not opinion.

### 5.8 Cards – and when not to use them

Anatomy: **container** (the only required part), header, body (title, subtitle, supporting text, badges), footer (actions).

Rules: one task or idea per card; consistent action placement across cards; do not overload the body with multiple competing actions; separate from the background with **either** a border **or** elevation – not both (§15.2 says usually the border).

**The anti-pattern, and it is the important part:**

> "Never use a collection of cards when users need to **scan a list or grid of homogeneous elements to compare** one against another. The card chrome adds space and visual noise; instead of linear top-to-bottom scanning, users have to **jump from card to card**."

Cards are for **heterogeneous, self-contained objects**. For homogeneous comparable rows – search results, pricing rows, data records, anything the user is scanning to compare – **a list or table beats a card grid**, and the card grid actively harms the task. This is one of the most commonly violated rules in modern product design.

### 5.9 Page rhythm and pacing

Independently confirmed by the corpus (§15.4) and by the editorial literature:

- **Alternate section types.** Text-heavy → visually rich → text-heavy. Alternating density *"maintains reader interest and prevents visual fatigue."*
- **Image-text interlock** – text-left/image-right then text-right/image-left across sections – gives variety on one consistent grid.
- Narrative pacing alternates **high-impact visual sequences with restful sections**, so the reader can absorb between spectacles. A page of continuous spectacle reads as noise.
- The framing worth keeping: *"Rhythm isn't about the gaps between content; it's about how the content prepares the reader for what comes next."*

Rhythm is composition **in time**, since a long page is consumed sequentially. It is the same skill as motion choreography applied to scroll.

### 5.10 The squint test – put this in the verification register

Blur the design (squint, or apply a blur filter) until text and small detail disappear, leaving only masses of light and dark.

- **What still stands out is what users will see first.** If that is not what you intended, the hierarchy is wrong regardless of how correct the individual elements are.
- Costs nothing, needs no tools, works on any medium, and catches hierarchy failures that element-by-element review cannot.
- Pairs with the **grayscale test** (§1.9, is hierarchy carried by color alone?) and the **400% zoom test** (§4.6). Three cheap, objective checks that turn self-critique from assertion into observation.

`[sources: NN/g "Scrolling and Attention", "The Fold Manifesto", "Squint Test"; Smashing "Design Principles: Visual Weight And Direction"; LogRocket card UI design; Simple Thread "Card Components: Best Practices"; LION+MASON top vs side navigation; LogRocket "Using the golden ratio in UX design"; Fiveable "Rhythm and Flow in Editorial Design"; Polypane "Debug your visual hierarchy with the squint test"]`

### 5.11 Masonry

Native CSS masonry landed as **`display: grid-lanes`** (renamed from the masonry proposal). Safari 26 shipped it first; support is **experimental and inconsistent across engines in early 2026**.

- **`grid-lanes` preserves DOM order** – a real accessibility win over JS masonry libraries, which routinely reorder (§8.12).
- Guard with `@supports`.
- **The compatible fallback is better than most people realize:** `columns: 230px; break-inside: avoid;` is a **two-declaration Pinterest layout** that packs tighter than Masonry.js, with **zero JavaScript and no resize listeners.** Its limitation is that content flows down columns rather than across rows, so reading order is column-major.
- Use masonry only when item heights genuinely vary and equalizing them would crop or pad content badly. **A uniform card grid is almost always the better answer** – masonry's ragged bottom edge weakens the composition.

### 5.12 Modal vs page vs inline – the decision

| Choose | When |
|---|---|
| **Modal** | High-stakes, irreversible, user-initiated, and **short**. Critical instruction that must be read |
| **Separate page** | Task takes **more than ~30 seconds**, involves multiple steps, or requires **comparing data from other sources** |
| **Inline / expand** | The user needs more context **without leaving the current task** – accordions, disclosure, editable fields |

The decisive question: **does the user need to reference the background while acting?** If yes, a modal is actively working against them, and the answer is inline or a non-modal surface.

Corollary: a multi-step wizard in a modal is nearly always wrong – it is a page pretending to be an interruption.

### 5.13 Header

- **Logo top-left, and it should be the first tappable element in DOM order** – both convention and screen reader order.
- **Two-state sticky header** is the cleanest pattern: **80–100px initial** with full logo and nav, shrinking to **50–60px after ~100px of scroll**, transition **150–200ms**. Under 60px on mobile, or the content area suffers.
- **Hide-on-scroll-down, reveal-on-scroll-up** – scrolling up *is* the signal that the user wants navigation. Gives back the vertical space without hiding the nav.
- 2025–26 mainstream pattern: **a centered, rounded navigation pill floating slightly off the top edge** – lighter than a full-width bar; common on portfolio and SaaS sites. Worth knowing as a current convention, and worth knowing it is *becoming* a default (§12.1).
- Do not oversaturate with links.
- Cross-reference §8.1: a sticky header must not obscure a focused element (WCAG 2.4.11).

### 5.14 Footer

- The footer's real job: **recovery** – where the user goes when they could not find something above.
- Contents users expect: navigation groups, legal, support/contact, trust links, social, optional newsletter.
- **Bold category headers, subdued link text.** Grouping is what makes a large footer scannable rather than a wall.
- **Extend the visual identity into it.** It is the last brand impression on every page and it is usually the least designed region on the site.
- Performance trap: **embedded social widgets.** Link out or lazy-load; never embed.

### 5.15 Breadcrumbs

- Justified only when the hierarchy is **three or more levels deep** and moving up is otherwise a hassle. In a flat product they are noise.
- **Location-based** (position in the IA) is the default and the right choice for content sites, ecommerce, docs and knowledge bases. **Path-based** (actual history) confuses more than it helps. **Attribute-based** is a filter UI wearing breadcrumb clothes.
- **Mobile: do not render the full trail.** *"A single link back to the parent level"* is the clean solution; a wrapped breadcrumb trail crowds the title and is hard to tap.
- Breadcrumbs describe **structure**, the back button describes **history**. They are not substitutes and shipping breadcrumbs does not remove the need for a sensible back behavior.

### 5.16 Scroll-triggered reveals

- **Animate one thing at a time.** *"The most successful examples animated only the text or the images – where both faded in together, they competed for attention."*
- **Animate on first view only.** Re-animating on every scroll-past is the most irritating version of this pattern.
- **The illusion of completeness is the real danger** (NN/g "scroll fading"): if a section fades in only after it is reached and there is a lot of empty space between sections, users conclude the page has ended. Fixes: **reduce the gap between sections, and always leave part of the next section visible** – the Gestalt principle of closure pulls the scroll.
- Keep the distance small (8–16px) and the duration short. A reveal that travels far reads as a page still loading.
- Implement with CSS scroll-driven animations (§6.14) rather than IntersectionObserver where support allows – compositor-driven and no main-thread cost.
- Honor `prefers-reduced-motion` by rendering everything visible immediately.

### 5.17 Choosing the hero visual

| Visual | Use when |
|---|---|
| **Product screenshot / device mockup** | Digital product. Shows the actual thing; also the highest-credibility option because it cannot lie about what the product looks like |
| **Photography** | Physical product, people, place, or where mood carries the value. *"Show the product, service, desired outcome, or value in action, in the real world"* |
| **Illustration** | The value is abstract (infrastructure, security, a concept) and a literal image would be a stock-photo lie |
| **Nothing – type only** | Editorial, luxury, brutalist, or when the copy genuinely is the strongest asset. The corpus has several (opencode, Ollama, Bugatti-adjacent) |

The corpus check (§15.10) is instructive: **developer tools overwhelmingly show real product UI; luxury and automotive show photography; AI brands are split between atmospheric imagery and type.** The choice is a genre signal, not just an asset decision.

### 5.18 App shell and sidebars

Three desktop modes: **icon-collapse (rail), off-canvas hide/show, and fixed non-collapsible.**

- Roles: **primary** sidebar anchors the app; **secondary** supports the main content; **temporary** appears on demand and leaves. *"Width, collapse behavior, keyboard model and responsive treatment all follow from that role"* – decide the role first.
- **The icon rail is the best collapse behavior** – navigation stays visible and reachable at both widths, unlike off-canvas which hides it entirely.
- **⌘B / Ctrl+B** is the established toggle shortcut.
- Mobile: the sidebar becomes a **sheet**.
- Accessibility in the collapsed state: **labels visually hidden but still in the accessible name**, tooltips on the icons, a persistent labeled collapse control, and **focus must not be lost when collapsing**.
- Transitions fast and subtle; honor `prefers-reduced-motion`.

### 5.19 Page-load choreography

- **Sequence, do not stack.** *"Keep simultaneous animations to a minimum and run complex ones in sequence"* – hero first, then supporting elements, then chrome.
- The default entrance that works nearly everywhere: **a fade with a small upward translate**, subtle enough for cards, modals and page content alike.
- **Entrance animation delays the LCP element if you animate it.** Never fade in the largest contentful element from opacity 0 – it directly damages the metric and the perceived speed. Animate everything *around* it instead.
- Keep the whole opening sequence inside ~1 second total. A hero that takes two seconds to assemble reads as a slow site, not a considered one.
- Stagger rules from §6.11 apply: 50–100ms, first item zero delay, cap at 5–6 elements.

### 5.20 Full bleed

The pattern: a **constrained content column with the ability for specific elements to break out** – to a wider band, or all the way to the viewport edges.

The clean CSS Grid implementation:

```css
.content {
  display: grid;
  grid-template-columns:
    [full-start] minmax(1rem, 1fr)
    [content-start] min(65ch, 100%) [content-end]
    minmax(1rem, 1fr) [full-end];
}
.content > * { grid-column: content; }
.full-bleed { grid-column: full; }
```

- One container controls every width. Children land in the content column by default; a class promotes an element to full width.
- Extend to five columns with named lines when a third, intermediate width is needed (wider than text, not edge-to-edge) – common for images and figures in editorial layouts.
- The term is from print: something printed full-bleed runs to the edge of the paper.

**Why it matters for design:** full-bleed moments are the strongest rhythm device available in a long page (§5.9, §15.4). Alternating a constrained text column with an edge-to-edge image or color band is what stops a long page reading as a document.

### 5.21 Search-as-you-type

- **Debounce ~300ms.** *"If something visible happens within about a second, most users stay confident and keep typing."*
- **Show the search state**: an inline spinner in the results area or a brief "Searching…". If previous results stay on screen, **label them as stale** rather than leaving the user comparing against the wrong list.
- **Cache by query key** – repeated searches are common and caching is the cheapest route to "instant".
- The three failure modes to design against, all of which read as brokenness: **typing lag** (cursor stutter), **flicker** (list clears then refills – keep the old list until the new one is ready), and **stale results** (out-of-order responses overwriting newer ones – always discard responses for superseded queries).
- Never move focus out of the input while typing; never auto-select the first result in a way that hijacks Enter.

## 6. Motion and animation

Weighted equal to typography. Timing, easing, springs, choreography, staggering, interruption, shared-element transitions, success and celebration moments, loading and perceived speed, the physics of "smooth", and the emotional register a curve produces.

**Position statement:** motion is not decoration on top of an interface, it is the interface explaining itself in time. Done right it costs nothing and reads as quality; done wrong it reads as cheap, and users cannot say why.

### 6.1 The asymmetry rule – the single highest-value motion rule found so far

**Entrances and exits must never use the same curve.**

| Direction | Curve | Why |
|---|---|---|
| **Entering** | `ease-out` (fast start, slow stop) | The fast start gets the object into position; the deceleration makes it **land** rather than stop. Matches how a real object arrives |
| **Exiting** | `ease-in` (slow start, fast finish) | The slow start gives the eye a moment to register the departure; the fast finish makes it **decisive and permanent** |
| **Moving between states, same element** | `ease-in-out` / standard | Both ends are anchored on screen, so both ends need to settle |

Corollary on duration: **entrances should be slightly longer than exits.** A popup at 300ms in, 200–250ms out. The user wants to see the arrival and does not want to wait for the departure.

Getting this one pair right is the difference between motion that feels considered and motion that feels like a `transition: all 0.3s` reflex.

### 6.2 Duration

| Range | Reads as | Use |
|---|---|---|
| <100ms | **Instant** – below the perception threshold for change | Hover tints, focus rings, toggles, anything high-frequency |
| 100–200ms | Fast, barely perceived | Small elements, small distances, state changes |
| **200–300ms** | **"Fast, but perceivable"** – the workhorse band | Most UI transitions. Material caps mobile animation here |
| 300–400ms | Deliberate, expressive | Large elements, large screen distances, moments meant to be noticed |
| 400–500ms | Slow | Only for big movement across large screens |
| >500ms | Sluggish, blocking | Almost always wrong for UI. Reserve for cinematic/brand moments where waiting is the point |

Anchors worth carrying:

- **~100ms is perceived as instant** (NN/g).
- **~230ms is the average time to visually perceive something** (Model Human Processor). This is why sub-200ms motion is felt more than seen.
- **1 second is the limit of uninterrupted flow of thought** (NN/g).
- Optimal band across studies: 200–500ms, with UI concentrating at the low end.

**Distance and size scale duration.** A 40px chip and a full-screen sheet should not share a duration. Bigger and farther = longer, but sublinearly – never proportionally, or large transitions feel broken.

Material 3 tokens for reference: `short1` 50ms, `short2` 100ms, `medium1` 250ms, `medium2` 300ms; easing split into **Standard** (`cubic-bezier(0.4, 0, 0.2, 1)`, "practicality over naturalness") and **Emphasized** (recommended for most transitions), each with accelerate and decelerate variants.

### 6.3 Springs vs bezier – and what "expensive" actually is

A cubic-bezier distributes a **fixed duration**. A spring has **mass, stiffness and damping** and its duration is an output, not an input. The resulting motion is a decaying oscillation – several bounces, each smaller.

| | Cubic-bezier | Spring |
|---|---|---|
| Feel | Neutral, composed, controlled | Fluid, organic, alive |
| Bounce/overshoot | Only fake, via a curve that exceeds 1 | Real decaying oscillation |
| Interruptible | Poorly – restarting mid-flight jumps | **Natively** – it is velocity-aware and can be redirected |
| Best for | Color, opacity, anything where a fixed duration is desirable | **Motion**: scale, position, offset. Not color or opacity |

- **Springs "do a better job tricking our brains into thinking something is actually moving."** That is the mechanism behind the luxury feel: the eye recognizes physical plausibility.
- `WARNING:` "when everything eases in and out, interfaces start to feel sluggish and overly polite." Uniform `ease-in-out` on everything is a real failure mode and it is the default most people ship.
- **High-frequency interactions – typing, keyboard nav, fast toggles – feel slower and noisier with motion added.** The correct amount of animation there is often zero.
- CSS now has `linear()` for arbitrary curves, which is how spring physics is expressed in native CSS without a library.

### 6.4 Interruptibility – Apple's central principle

**"An animation must stay grabbable and reversible at any instant, not just at rest."**

An interface is fluid when it behaves like the physical world: **responds instantly, moves continuously, carries momentum, resists at boundaries, and can be redirected mid-motion.**

This is the deepest idea in the whole motion area. Most web animation is fire-and-forget: it starts, it blocks, and grabbing it mid-flight either does nothing or snaps. Native-feeling interfaces let you catch a sheet halfway through its dismissal and throw it back. **Springs are the tool that makes this natural, because they are inherently velocity-aware.**

Apple's stated preference: **quick and precise** over long and elaborate – brevity plus precision reads as lightweight and conveys information more effectively.

### 6.5 Microinteraction structure (Saffer)

Four parts, and naming them makes them designable:

1. **Trigger** – what starts it. Explicit (a tap) or implicit (a state change, arriving somewhere, a threshold crossed).
2. **Rules** – what can and cannot happen. *"Make the rules as transparent as possible"* so the user understands what is happening and why.
3. **Feedback** – what the system says back. Visual, motion, haptic, audio.
4. **Loops and modes** – what happens over time and on repeat. What does the 50th time look like? Does it decay?

*"The difference between a product you love and a product you tolerate is often the microinteractions you have with it."*

The loop question is the one that gets skipped, and it is where delight turns into irritation: **a celebration that is charming once is grating on the tenth repeat.**

### 6.6 Celebration and delight

**The FEAT test: if it is an ordinary thing, it does not deserve an extraordinary response.** Confetti is for something the user has been building toward – finishing an onboarding, hitting a milestone, closing a deal, completing a course. Not for saving a form.

- A green checkmark with subtle motion "feels rewarding" without spending the user's attention budget.
- Escalation ladder, cheapest to most expensive: inline state change → subtle checkmark → drawn checkmark with an easing that lands → glow/pulse → confetti. **Match the rung to the magnitude of the achievement.**
- The failure mode has a name in the literature: **"the over-confetti-ing of digital experiences."** Celebrating everything means celebrating nothing, and it makes a product feel like it is performing enthusiasm at you.
- `CONSOLIDATE:` this is a place where the skill should *ask* – "how big a moment is this, honestly?" – rather than decide.

### 6.7 Loading and perceived performance

`CONFLICT – genuine and unresolved.`

- **For:** skeleton screens measured 20–30% faster in *perceived* load time than spinners; academic work found higher scores on perceived speed and ease of navigation, and skeletons rated best emotionally – participants were happiest with skeleton loads.
- **Against:** a 2017 Viget study found the opposite – **skeletons performed worst on perceived duration**, and the spinner group evaluated their wait most positively.

Resolution attempt from the sources: skeletons work **in familiar interfaces and at short waits**; they are off-putting in unfamiliar ones and at long waits. The mechanism claimed is attentional – a spinner points at the waiting, a skeleton points at the content about to arrive.

Working position for the skill: **skeletons for short, predictable waits in a layout the user already knows** (feed refresh, tab switch, returning to a list). **Spinners or progress for unpredictable or long waits.** Determinate progress whenever the length is actually knowable. Do not present this as settled – the evidence is not.

### 6.8 View Transitions

| Type | Support (2026) | Verdict |
|---|---|---|
| **Same-document** | Chrome 111+, Safari 18+, **Firefox 144+** | **Finished. Use freely** |
| **Cross-document (MPA)** | Chrome 126+, Safari 18.2+; **still only animating in Chromium in practice** | Progressive enhancement only. Real support gap |

- Cross-document requires an **explicit opt-in on both the source and destination page** via a CSS at-rule. No JS call – the link click triggers it.
- Watch the **four-second budget** – treat it as a performance requirement, not an animation detail.
- The point of a shared-element transition is **continuity of identity**: the thumbnail *becomes* the hero image, so the user never loses the thread. This is the single most valuable thing view transitions buy, and it is worth building around.

### 6.9 Performance – what can actually be animated smoothly

**Tier S: `transform` and `opacity`.** These run on the compositor, skip layout and paint entirely, and stay smooth **even when the main thread is busy**. `filter` and `clip-path` are also compositor-capable in modern browsers.

**The #1 cause of jank: animating properties that force layout or paint every frame** – `width`, `height`, `top`, `left`, `margin`, `padding`, `box-shadow`.

Substitutions that matter in practice:

| Instead of | Animate |
|---|---|
| `width` / `height` | `transform: scale()` (watch text distortion; or animate a wrapper) |
| `top` / `left` / `margin` | `transform: translate()` |
| `box-shadow` | Opacity of a pseudo-element carrying the shadow |
| `background-color` on a large area | Opacity of an overlay layer |

**"120Hz makes mediocre animation look worse, because you have half the time per frame."** As high-refresh displays become standard, sloppy animation gets *more* visible, not less. This kills the argument that performance work on animation is optional.

WAAPI and CSS both hit the compositor path; a JS library only stays smooth if it drives compositor properties.

`[sources: Material Design 3 easing-and-duration tokens; NN/g "Executing UX Animations: Duration and Motion Characteristics"; Apple HIG Motion + WWDC18 "Designing Fluid Interfaces"; Josh Comeau "A Friendly Introduction to Spring Physics" and "Springs and Bounces in Native CSS"; Dan Saffer "Microinteractions" (2013); UX Collective "The over-confetti-ing of digital experiences"; Viget "A Bone to Pick with Skeleton Screens" (2017); "The effect of skeleton screens" (ResearchGate); Chrome View Transitions docs; Motion.dev "Web Animation Performance Tier List"; surma.dev "120fps and no jank"]`

### 6.10 Reduced motion – done properly

`prefers-reduced-motion` exists for **vestibular disorders**, where large scaling and panning motion can trigger genuine physical discomfort – nausea, dizziness, migraine. It is not a taste setting.

The correct implementation, and the common mistake:

| Wrong | Right |
|---|---|
| `animation: none; transition: none;` | Set `animation-duration` / `transition-duration` to a value short enough that the motion is no longer noticeable (`0.01ms`) |

Why it matters: killing animation outright can **break** interfaces that depend on an animation's completion event, and it removes state-change feedback the user still needs. Making it instant preserves the logic and removes the motion.

- The preference must be honored for **all** animation, transitions, smooth scrolling and motion-driven UI – **including third-party plugins and legacy code that ignore it.** You override them.
- No autoplaying video, sliders or movement.
- **Not all motion is equal.** Opacity fades and color changes are generally safe; large translations, scaling, parallax and rotation are the triggers. The refined implementation reduces *transform* motion and keeps *opacity* transitions, rather than flattening everything.
- Offer an in-app toggle too. The OS setting is not universally discoverable.

### 6.11 Stagger and choreography

| Rule | Value | Why |
|---|---|---|
| Stagger delay per item | **50–100ms** (0.05–0.15s) | Enough to read as a cascade, not enough to feel like waiting |
| Delay vs duration | Delay should be **30–70% of the item's duration** | The next item starts before the previous finishes, so the sequence reads as one gesture rather than a queue |
| **First item: zero delay** | Always | A delay on the first item makes the user think the interaction failed |
| Maximum staggered items | **5–6** | 20 cards at 60ms each is 1.2s of waiting. Stagger the first 4–5, bring the rest in together |
| Property | Transforms and opacity only | Staggering layout properties across many elements is a guaranteed jank source |

Stagger is a hierarchy tool as much as a decorative one: **the order things arrive in tells the user what to read first.**

### 6.12 Origin-aware motion

**`transform-origin` defaults to `center`, and center is wrong in most cases.**

A dropdown, popover, tooltip, menu or modal should **scale out from the thing that opened it**. Getting this right creates a causal link – "this came from that" – and getting it wrong is one of the quietest reasons an interface feels generic.

- Base UI and Radix expose CSS variables that set the correct origin automatically for popovers and menus.
- For a modal from a button, the View Transitions API can make the dialog appear to originate from the trigger, creating a visible relationship between them.
- Same idea at a larger scale: a detail view opening from a card should grow from that card's position, not fade in from the center of the screen.

### 6.13 Disney's principles that actually transfer

Three of the twelve carry real weight in UI:

- **Anticipation** – a small counter-movement before the main move. A button that dips slightly inward before springing out. It prepares the eye and makes the action read as intentional rather than abrupt.
- **Squash and stretch** – with volume conserved: stretch thinner, squash wider. Even rigid UI elements benefit from a subtle stretch on a fast move and a small squash on landing. This is a large part of what makes motion feel physical rather than computed.
- **Follow through / overlapping action** – not everything stops at once. Related elements moving at slightly different rates is how you **establish hierarchy in motion**: the primary element leads, secondary elements trail and settle.

The rest (arcs, secondary action, exaggeration, appeal) mostly apply to illustration and mascot work rather than interface chrome.

### 6.14 Scroll-driven animation

`scroll()` (scroll progress) and `view()` (element visibility progress) are pure CSS – **no scroll listeners, no IntersectionObserver, no main-thread work.**

- Support: Chrome/Edge 115+, Safari 18+, Firefox 132+ (partial/flagged in some builds). **~84% global mid-2026.** Progressive enhancement, not a baseline.
- `view()` named ranges: `entry`, `exit`, `contain`, `cover`.
- **Gotcha with a real bite:** declare `animation-timeline` **after** the `animation` shorthand. The shorthand resets `animation-timeline` to `auto`, silently killing it.
- Always guard with `@supports (animation-timeline: view())`.
- Because it runs on the compositor, transform/opacity scroll animations are **as smooth as the scroll itself** – a completely different quality bar from JS scroll handlers.
- Use for: reading progress bars, parallax, reveal-on-enter, sticky headers that shrink. Not for anything that must happen.

### 6.15 Feedback latency – the numbers that govern "smooth"

| Threshold | Meaning | Source |
|---|---|---|
| **~50–100ms** | Feels **instantaneous**; the link between action and reaction stays intact | Nielsen (1993), still the standard |
| **~1 second** | Upper limit of uninterrupted flow of thought. Beyond this the user notices waiting | Nielsen |
| **~10 seconds** | Attention is lost unless explicit progress is shown | Nielsen |

**Every click must produce a visible response within ~100ms** – a press state, a checkbox tick, an optimistic list update. A server round trip is 50ms at best and usually more, so **UI feedback cannot be coupled to the network.**

**Optimistic UI** is the structural answer: update locally first, sync in the background, reconcile if the server disagrees. It decouples perceived speed from network variability, which is the one variable you do not control.

This is where "smooth" actually comes from, and it is not an animation property – it is **never letting an interaction sit silent.**

### 6.16 Hover, and its limits

- **Subtle wins.** A slight background darkening, a small shadow lift, a border change. Dramatic color swaps and large motion on hover read as jarring and pull attention off the task.
- **Every `:hover` needs a matching `:focus-visible`.** A hover style with no focus equivalent means keyboard users get no feedback at all and the interaction is silently broken for them.
- Hover is a **progressive enhancement, never a requirement.** It does not exist on touch. On touch, `:active` and loading states carry the whole feedback load.
- Hover on a card that lifts: `translateY(-2px)` plus a shadow, 120–200ms, `ease-out`. More than that and it starts to feel like it is trying.

### 6.17 Drag and drop

- **The goal is that it feels like moving a physical object.** Users bring that expectation from the real world and notice immediately when it is not met.
- Lift the dragged item in **z** – shadow and slight scale – so it reads as picked up.
- **Magnetic snapping** into the drop target compensates for imprecise pointing. It is the single biggest usability improvement in a drag interaction.
- Animate the drop into the final position (~100ms). An item that teleports on release feels broken.
- The drop zone must **signal**: expand, change color, show a preview of where the item will land.
- A **drag handle** is the affordance. Without one, draggability is invisible.
- Always provide a non-drag path. Drag-and-drop is inaccessible to keyboard and to many motor-impaired users if it is the only route.

### 6.18 Haptics

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 6.19 Motion as brand

Motion is where a brand stops being seen and starts being felt. The vocabulary that makes this designable rather than vibes-based – **speed, rhythm, easing, transition style, amplitude** – is a system, and it should be written down like a color palette.

Four archetypes worth keeping:

| Personality | Motion signature |
|---|---|
| Calm precision | Short, tight, minimal overshoot, near-linear settle. **Apple** |
| Playful energy | Springy, overshoot, squash and stretch, character. **Duolingo** |
| Sharp and rhythmic | Fast, decisive, hard cuts, strong easing. **Nike** |
| Calm and minimal | Slow fades, tiny distances, almost no transform |

The design implication: **motion should be specified per project, in the spec block, alongside type and color.** A project's easing and duration set is part of its identity, not a default lifted from a framework.

`[sources: MDN prefers-reduced-motion; A11Y Project "A primer to vestibular disorders"; CSS-Tricks "No Motion Isn't Always prefers-reduced-motion"; Motion.dev stagger docs; Smashing "An Introduction To CSS Scroll-Driven Animations"; MDN scroll-driven animations; emilkowal.ski "Origin-aware animations" and "7 Practical Animation Tips"; IxDF "UI Animation – Disney's 12 Principles"; NN/g "Drag-and-Drop: How to Design for Ease of Use"; Android haptics design principles; Simon Hearne "Optimistic UI Patterns"; Nielsen response-time limits (1993)]`

### 6.20 Springs in pure CSS – `linear()`

`linear()` interpolates between a list of output values, and **values above 1 or below 0 overshoot** – which is how you get real bounce and spring in CSS with no runtime JS.

The technique: **sample a real spring equation (mass, stiffness, damping) into dozens of points and hand them to `linear()`.** That is true spring motion, not a bezier approximation. ~87% global support since 2024.

Generators: `linear-easing-generator`, `kvin.me/css-springs` (perceptual duration + bounce inputs), presets for Spring, Bounce, Elastic and Material emphasized.

This resolves the §6.3 trade-off in the browser's favor: **you no longer need a JS animation library to get spring feel** – only to get spring *interruptibility*, which is still the one thing `linear()` cannot do.

### 6.21 FLIP – animating the unanimatable

**First → Last → Invert → Play** (Paul Lewis). Record position before, let the DOM change, apply an inverse transform so it *appears* not to have moved, then animate the transform to zero.

- It converts a **layout change into a compositor-only transform animation** – the element is already at its new layout position and only *looks* like it is travelling. 60fps on low-end hardware.
- The right tool whenever the animation involves a **layout** change: list reordering, sortable lists, drag-and-drop, filtered views, expand/collapse, dashboard rearrangement.
- Powers Motion's `layoutId`, GSAP's Flip plugin, Vue's `<TransitionGroup>`.

This is the answer to "I need to animate height/position and I was told not to." You do not animate the layout property – you let the layout change instantly and animate the *difference*.

### 6.22 Emil Kowalski's practical set

Design engineer at Linear, previously Vercel; author of Sonner, Vaul and animations.dev. His material is the most practically useful animation writing found in the study.

- *"The interface should feel as if it's **listening to the user** – give feedback on all their actions as soon as possible."* This is the best one-line statement of the sub-100ms rule (§6.15).
- **A subtle scale-down on button press makes an interface feel instantly more responsive.** One line, disproportionate effect.
- Form submit shows a loading state; copy-to-clipboard shows a success state. **Every action gets an acknowledgement.**
- Defaults: **ease-out**, custom cubic-beziers over stock keywords, spring for natural motion, iOS-style drawer easing, **UI animation under ~300ms**.
- Origin-aware transforms (§6.12) come from the same source.

### 6.23 Easing as emotional register

The vocabulary that makes this specifiable rather than vague:

| Curve | Reads as |
|---|---|
| `ease-in` (slow start) | Cautious, hesitant, reluctant. Wrong for entrances |
| `ease-out` (fast start, soft landing) | **Satisfying, complete** – "like finishing a task" |
| Fast + bouncy / overshoot | Energetic, playful, attention-grabbing |
| Slow + smooth | Elegant, serious, calm. Reduces cognitive load |
| Linear | Mechanical, artificial. Correct for progress and continuous rotation, wrong for almost everything else |

**"Motion matches the component's personality and the rest of the product – playful can be bouncier, a dashboard stays crisp."** Duration and curve are the two dials; both are brand decisions (§6.19).

### 6.24 Progress indicators

| Wait | Use |
|---|---|
| **1–3s** | Spinner or skeleton |
| **3–10s** | **Determinate progress bar or percentage** |
| **10s+** | Progress **plus** time estimate, plus something to look at |

- **Determinate whenever the length is knowable.** It converts an unbounded wait into a bounded one, which is the whole psychological benefit.
- **A determinate bar fills 0→100 and never decreases.** A progress bar that goes backwards destroys trust in every progress bar you ship afterwards.
- Indeterminate says only *"something is happening"* – acceptable for quick or background work, poor for anything the user is waiting on.
- Research note: progress bars whose pace **accelerates toward the end** are perceived as faster than linear ones – the perception of a wait is shaped by its *profile*, not only its length. `[arXiv: "The Magic of Slow-to-Fast and Constant"]`

### 6.25 Bottom sheets and drawers

- **Modal** sheet blocks the rest of the UI; **non-modal / persistent** sheet lets the user keep working and can be expanded or collapsed but not fully dismissed.
- **Detents** are the snap points – fixed height, percentage of screen, or a mix. Multi-detent sheets (peek → half → full) are the pattern that makes a sheet feel native rather than like a modal that slid up.
- The real advantage over a dialog: a sheet **snaps to three of the four screen edges**, so it has far more usable space and lands in the thumb zone.
- Drawers are the same component from any edge.
- Pair with origin-aware motion (§6.12) and interruptibility (§6.4) – a sheet you can catch mid-dismiss and throw back is the single clearest "this feels native" signal on the web.

### 6.26 Gestures

- **Discoverability is the central problem: gestures are always hidden.** *"The more your UI relies on gestures over visible buttons, the greater the possibility for confusion."*
- **The best line found on this: "creative gestures look impressive in demos but rarely scale – gesture-driven design works best when it feels boring."** Use the conventions; do not invent.
- Layering rule: **primary functions get visible controls; secondary get standard gestures; tertiary get long-press.** A gesture is never the only path to a primary action.
- Always provide a visible alternative – gestures are inaccessible to many motor-impaired users and invisible to screen reader users.
- Cross-platform conflict to watch (§9.3): left-to-right edge swipe means *back* on iOS and *switch tab* on Android.

### 6.27 Scroll behavior – two things to refuse

**Scroll hijacking.** *"Users have memorized the way scrolling works on their device and intuitively expect consistency."* Overriding scroll speed, direction or locking it into steps:

- removes control, which is the core violation;
- is nearly impossible to implement consistently across devices and browsers, and produces **lag, CPU spikes and crashes**;
- **breaks the user's ability to control their own reading pace**, which is an accessibility failure, not just a preference one.

There is a site named `dontfuckwithscroll.com`. That is the strength of feeling. Treat scrolljacking as a refusal, not a trade-off.

**Parallax.** More nuanced but close:

- **Harmful to people with vestibular disorders** – differential scroll speeds are a direct motion-sickness trigger. Webflow's accessibility checklist says plainly: *avoid parallax effects.*
- Can confuse screen readers and keyboard navigation when the DOM order stops matching the visual flow.
- Where it legitimately works: **creative portfolios, brand storytelling, product demonstration** – places where immersion is the point and the content is not task-critical.
- Never on corporate or information-heavy sites; generally not on mobile.
- If used: honor `prefers-reduced-motion` completely (§6.10), keep the differential small, and never attach meaning to the effect.

### 6.28 Sound

- Use for **immediate confirmation that an action registered**, for information that has no visual channel, and to support multitasking where the user is not looking at the screen.
- **Short.** Interface audio must be immediate and brief or it disrupts the interaction flow.
- Design it as **ergonomics and branding together** – a UI sound is both a signal and a piece of identity.
- Genuine accessibility value: a second feedback channel helps users with cognitive and learning disabilities.
- Always mutable, never the sole carrier of meaning, and off by default on the web.

### 6.29 Choosing an animation approach

| Need | Use |
|---|---|
| Hover, fades, color transitions, single keyframe loops | **Native CSS** – zero bytes, everyone knows it |
| Spring feel without a library | **`linear()`** (§6.20) |
| Scroll-linked reveals, parallax, progress | **CSS scroll-driven animations** (§6.14), falling back to a library only where support requires |
| React UI transitions, presence/exit animation, layout animation | **Motion** (formerly Framer Motion) – `AnimatePresence` and layout animations are far less code than the equivalent elsewhere |
| Timeline-heavy sequences, complex scroll choreography, animating to runtime-computed values | **GSAP** – *"reach for it the moment you are fighting chained transition-delays"* |
| Lightweight JS control | Anime.js (~9kb) |

**The correct default is the platform.** Reach for a library at the point where you are fighting the platform, not before – which is a specific, checkable moment rather than a preference.

### 6.30 The entrance-animation trap

Do not animate the LCP element in. Fading in the largest contentful element from `opacity: 0` directly delays the metric and the perceived first paint. **Animate around it** – supporting elements, chrome, secondary content – and let the main thing simply be there.

### 6.31 List add and remove

The pattern that reads correctly: **on add, existing items slide out of the way while the new item fades in and scales up; on remove, the reverse.** The movement of the neighbors is what makes the change legible – an item that simply appears leaves the user unsure whether the list reordered.

- Use **FLIP** (§6.21) for the neighbors' movement, since it is a layout change.
- Feedback timings from a production system (GitLab): **~200ms for hover/focus state transitions, ~500ms for active/feedback states.**
- On removal, animating the height collapse is the part that makes it feel physical – but animate it via FLIP or `grid-template-rows: 0fr → 1fr`, not by animating `height`.
- **Always animate the removal *after* the item is gone from the data**, not before – an optimistic removal that animates and then fails needs a distinct restore animation, not a paused one.

### 6.32 Glows and colored shadows

- **A colored, zero-offset, high-blur shadow is a glow.** Modern use: CTAs, featured cards, and brand-color reinforcement without adding an element.
- `text-shadow` for text, `box-shadow` for boxes, **`filter: drop-shadow()` for icons and SVGs** (it follows the alpha shape rather than the bounding box – the reason SVG glows look wrong with `box-shadow`).
- **Performance:** animating `box-shadow` causes repaint-driven jank. **Paint a static glow on a `::after` pseudo-element and animate its `opacity` or `transform: scale()`.** Same trick as §6.9's shadow substitution.
- On dark UI specifically, **glows and borders replace shadows as the elevation cue** (§2.4) – "inner glows" and light-emitting strokes are the 2026 dark-mode convention for keeping elements visible and reading as interactive.
- Restraint applies: a glow is voltage. One or two per screen.

### 6.33 What "smooth" actually is – the synthesis

Pulling together every thread in this study, "smooth" is **five separate things**, and they can be worked on independently:

**1. Immediate acknowledgement.** *"A page that loads in 1.5s with no visual feedback feels slower than one that loads in 2s with an immediate skeleton."* Response within ~100ms (§6.15), optimistic UI (§6.15), a press state before any data moves (§6.22).

**2. Physically plausible motion.** Start fast, decelerate – *"because that matches how objects move in the physical world"* (§6.1). 150–300ms for most transitions; approaching a second feels sluggish.

**3. Consistent frame pacing.** 60fps target, 120Hz raising the bar (§6.9). And the finding that ties everything together:

> **"Variable performance destroys smooth perception even when averages are good."**

This is the same result as the streaming research (**>30% speed variation degrades perceived fluency**, §7.15) and the same as the animation-jank result. **Consistency beats speed, in every modality measured.** That is probably the single most transferable finding in the whole study.

**4. Responsiveness prioritized over capability.** *"The fastest-responding system that does less will often feel faster than a slower-responding system that does more."*

**5. Craft details the user feels without noticing.** Named explicitly in the sources: **concentric border radii on nested elements** (§3.7), **optical rather than geometric alignment** (§3.6), **layered transparent shadows instead of solid borders** (§13.2), **staggered enter animations** (§6.11), and **subtle texture** (§12.12). *"A sense of quality and craftsmanship that users feel rather than consciously notice."*

That list is an independent confirmation of the craft section, arrived at from the perception side rather than the aesthetics side.

## 7. Interaction and state

### 7.1 The five states, and the sixth everyone forgets

Every component needs: **default, hover, focus, active, disabled** – plus, for anything that fetches or mutates: **loading, empty, error, success, partial/stale.**

The rule that catches the most real bugs: **`:hover` without a matching `:focus-visible` means the interaction is invisible to keyboard users.** If a hover style is the only affordance, the component is broken for a whole class of users, and it will not show up in any visual review.

### 7.2 Empty states

An empty state is not a blank screen – it is **the screen with the most attention available and the least competition for it.**

| Type | Job |
|---|---|
| **First use** – nothing created yet | Teach the model and give one clear action. This is the best onboarding surface in the product |
| **No results** – search or filter returned nothing | Explain *why* (which filter, which term), and offer a way out: clear filters, broaden, suggest alternatives |
| **User-cleared** – inbox zero, all tasks done | Acknowledge it. This is a legitimate place for a small moment of delight |
| **Error** – it failed | Say what happened and offer recovery |

Rules: explain why it is empty in plain language; **never** ship "No data" or "Nothing here yet" with no context; use a friendly tone that does not blame the user; give **one** clear next action; keep it sparse – icon, title, one line, one CTA, max-width ~320px; and announce the change to screen readers, since a list going empty is a state change they otherwise miss.

### 7.3 Error messages

- **Place the error next to the thing that caused it.** A summary at the top of a form forces the user to scan the whole interface to find the problem. Adjacent errors are located instantly. (A top-of-form summary is still useful *in addition*, for screen readers and long forms – not instead.)
- **Say what happened, why, and what to do next**, in the user's vocabulary, not the system's. No stack traces, no error codes as the primary message, no jokes, no pop-culture references.
- **Preserve the user's input.** Losing typed data on an error is the most punishing thing a form can do.
- **Recovery must be specific to the error class:** network → check connection / retry; auth → reset password; payment → which method, what to change; validation → what format is expected, shown as an example.
- Include a **retry** for transient failures, and an escalation path for persistent ones.
- Measure it: recovery rate, abandonment, downstream impact. An error state nobody escapes from is a broken feature, not a message problem.

### 7.4 Validation timing – the evidence is clear

**On blur wins.** Across 80+ implementations tested, on-blur beat both alternatives more often than they beat it combined: real-time is too aggressive, on-submit is too late.

| Timing | Effect |
|---|---|
| **On focus / while empty** | **Premature.** Flags a field before the user has typed. Causes **error blindness** – users stop trusting the messages. Reported conversion drop of **9–16%** vs on-blur |
| **On every keystroke** | Flags `j` as an invalid email before `@` is typed. Interrupts flow, feels hostile |
| **On blur** | The default. The user has declared the field finished |
| **On blur + 300–500ms delay** for format-constrained fields (email, phone, card) | Best of both for fields where the format is checkable |
| **On submit only** | Too late – the user has to go back and re-navigate |

Once a field **has** errored, switch that field to live validation so the user sees it clear as they fix it. Error → strict; clean → lenient.

Reported: inline validation cuts error rates ~22% and completion time ~42%. `[secondhand]`

`WARNING:` real-time validation can itself be an accessibility problem – announcements firing on every keystroke flood screen reader users. Debounce, and use `aria-live="polite"`.

### 7.5 Disabled controls – an anti-pattern more often than not

The **contrast dilemma** is unsolvable as posed: raise contrast enough to be readable and it no longer looks disabled; keep it looking disabled and it fails contrast. That is the signal that the pattern itself is wrong.

Worse, a `disabled` button is **removed from the tab order**, so a keyboard or screen reader user may never discover it exists, and gets no explanation of why they cannot proceed.

| Instead of disabling | Do |
|---|---|
| Disabled submit until the form is valid | **Keep it enabled.** On click, validate and move focus to the first problem. The user learns what is wrong instead of guessing |
| Disabled because of state | `aria-disabled="true"` – stays focusable and announced, so the user can find it and hear why |
| Disabled "Out of stock" | Replace with a **useful** action: "Notify me", "See similar" |
| Disabled because of permissions | Show it with an explanation, or hide it entirely – see below |

**Hidden vs disabled:** hide when the user can never have it (wrong role, wrong plan – if it is not a sales moment); disable/explain when the user could have it under some condition they can reach. A disabled control with no explanation is the worst of both.

If disabling is unavoidable, **always pair it with the reason** – tooltip, adjacent text, or an error message on attempted use.

### 7.6 Destructive actions

**The governing rule: friction proportional to damage × irreversibility.**

- **Undo beats confirmation** wherever it is technically possible. It costs the user nothing in the 99% case and saves them in the 1%.
- **Confirmation is for the genuinely irreversible**: permanent deletion (not "move to trash"), account closure, anything that leaves your system.
- `MYTH:` "confirm destructive actions" as a blanket rule. **A blanket "Are you sure?" trains users to click through without reading**, so the one confirmation that mattered gets dismissed on autopilot. **Effectiveness depends on rarity.** Over-confirming actively makes the product less safe.
- **Never assign a destructive action to the primary button role.** People select primary buttons without reading the label. The primary should be the safe, non-destructive choice.
- **Separate consequential options from benign ones spatially**, and differentiate with redundant signals – color plus label plus position. Adjacent Save/Delete buttons is a design defect (NN/g).
- Type-to-confirm ("type the repo name") is the right level only for the most severe irreversible actions. Anywhere else it is theater.
- For infrequent tasks the user may not be watching closely, a confirmation can beat an easily-missed undo toast.

### 7.7 Button hierarchy

- **One primary per view.** If everything is primary, nothing is. This is the most commonly violated rule in real products.
- Primary = filled. Secondary = outlined. Tertiary = text/ghost. The visual weight *is* the hierarchy.
- **Destructive is a color treatment layered on the hierarchy, not a level of it.** A destructive action that is the main step in a flow gets primary-danger; a destructive action that is one of several options gets tertiary-danger.
- Position matters as much as style: put the destructive option away from the confirming one.

### 7.8 Toasts

| Property | Value |
|---|---|
| Duration | **2–3s** short confirmations, **5–6s** with an action. Formula: 5s + 1s per 120 words, 6s floor when in doubt |
| Placement | Top-right on desktop, **bottom-center on mobile** (thumb reach and it avoids the notch) |
| Info | `role="status"` + `aria-live="polite"` – waits for the screen reader to finish |
| Error | `role="alert"` (`aria-live="assertive"`) – interrupts, because it must |
| Dismissal | Keyboard-dismissible; hover pauses the timer |
| Contrast | 4.5:1, same as everything else |
| Motion | Respect `prefers-reduced-motion` |

**Never put anything critical or irreversible only in a toast** – it disappears, and a screen reader user or a distracted user will miss it. Toast is for non-blocking confirmation. Anything that must be acted on belongs inline.

### 7.9 Focus management

The complete modal pattern, all four parts required:

1. **Store** the element that had focus before opening.
2. **Move** focus into the dialog on open.
3. **Trap** Tab and Shift+Tab inside while open.
4. **Restore** focus to the trigger on close.

Escape must close and restore. `<dialog>` with `.showModal()` gives you the trap, Escape and the inert backdrop for free – it is the correct default and it eliminates most hand-rolled bugs. Focus restoration is still worth verifying.

Same principle for every transient surface: popovers, drawers, menus, comboboxes. **When it closes, focus returns to what opened it.** Focus that lands on `<body>` after a dialog closes drops a keyboard user at the top of the page.

### 7.10 Forms

- **Labels above fields, always visible.** The label is the primary guide and it must not disappear.
- `MYTH:` placeholder-as-label. **NN/g measured placeholder-only labels increasing errors and completion time across every demographic tested.** The placeholder vanishes on first keystroke, so a distracted user cannot recover what the field was asking for. Use placeholders for format examples only, or not at all.
- **Single column.** Baymard: multi-column forms cause non-linear, zigzag reading and produce more errors and lower completion.
- 16px minimum on inputs (iOS zoom, see 4.9).
- Mark **optional** fields rather than required ones when most are required – less visual noise.
- Ask for the least possible. Every field is a chance to abandon.

### 7.11 Progressive disclosure

Reveal essentials first, complexity on demand. Reported **30–50% faster initial completion** versus full exposure. `[secondhand]`

Three forms: **step-by-step** (a sequence), **conditional** (hidden until requested), **contextual** (surfaced by what the user is doing).

The trap: progressive disclosure that hides things the user needs every time is just extra clicks. Hide what is genuinely occasional; **expose what is frequent even if it looks busier.** The measure is frequency of need, not visual tidiness.

`[sources: Smashing "A Complete Guide To Live Validation UX"; UX Movement "Why Users Make More Errors with Instant Inline Validation"; a11yblog "Why real-time form validation can become an accessibility issue"; Smashing "Hidden vs. Disabled In UX"; CSS-Tricks "Making Disabled Buttons More Inclusive"; NN/g "Confirmation Dialogs Can Prevent User Errors" and "Dangerous UX: Consequential Options Close to Benign Options"; Carbon Design System button usage; Sheri Byrne-Haber "Designing Toast Messages for Accessibility"; Baymard form design; NN/g placeholder research; IxDF progressive disclosure]`

### 7.12 Dashboards

| Rule | Value |
|---|---|
| Metrics per screen | **5–9.** Dashboards exceeding **12 KPIs show a 40% engagement drop** `[secondhand]` |
| Primary KPI | **Top-left**, largest size, highest contrast |
| Space allocation | ~40% to the single most important metric, ~30% to 2–3 secondary, ~20% to trend context (sparklines, comparisons), ~10% to nav and filters |
| Visual language | **One** consistent language for color, scale and layout, so the viewer does not re-learn how to read each chart |
| 2026 addition | **Data-freshness transparency** – show the last-updated timestamp prominently. Trust in a dashboard is trust in its recency |

The framing that matters: **a dashboard needs a clear objective tied to a specific decision.** A dashboard that is not answering a decision is a report, and it will be ignored.

Note the tension with §5.5: dashboards *are* one place where F-pattern scanning is reported to hold, probably because they are dense grids of comparable units rather than prose.

### 7.13 Chart selection

| Question | Chart |
|---|---|
| Compare categories | **Bar.** Best for >7 categories or long category names. The safe default |
| Change over time, continuous | **Line** – it shows continuity, which bars cannot |
| Volume/magnitude over time, not just direction | **Area** |
| Relationship between two continuous variables | **Scatter**; add a third variable via bubble size |
| Categories over multiple periods | Stacked bar / stacked area |

The governing rule: *"Don't choose a chart because it looks familiar. Start with the story you want to tell and let that guide the format."*

### 7.14 AI and chat interfaces

Four properties a chat interface is judged on: **capability transparency, recovery patterns, confidence display, accessibility.**

- **Capability transparency** – the user can tell what the system can and cannot do **before they type.** An empty chat box with no signal is the most common failure.
- **Confidence display** – inline confidence indicators, clarifying questions when uncertain, **source citations on factual claims**, progressive disclosure of reasoning.
- **Recovery** – actionable quick-replies to pivot the query, and a context-preserving handoff path.
- **Message structure** – *"two short messages of 40 words beats one 80-word block"*, especially on mobile where most of this happens while the user is doing something else.
- Streaming implementation: buffer incoming tokens and render progressively, show a streaming indicator, **expose a stop button**.
- Contractions and human phrasing; microcopy that acknowledges the user's state.

### 7.15 Streaming – the strongest perceived-performance result found

**Users perceive streaming interfaces as ~40% faster than buffered ones even when total time is identical.** One reported deployment: first-token latency down ~90%, **perceived speed rating 4/10 → 9/10 with completion time barely changed**, and **drop-off after prompt fell from 22% to 7%.**

- The metric that matters is **TTFT (time to first token)** – that is what the user experiences as "the wait". **TPOT** (time per output token) governs how the rest feels.
- **Below ~12 tokens/s the stream falls behind reading speed** and reads as stalling.
- **Speed variation above ~30% degrades perceived fluency** – a stream that stutters feels worse than a slower steady one. *Consistency beats speed*, which is the same finding as animation frame pacing (§6.9).
- Engagement suffers past **~1.3s** before anything appears.

`CONSOLIDATE:` this is the clearest proof of the general principle in §6.15 – **showing progress beats being fast.** *"When people see something happening, they wait. When it's just a spinner, they bail."*

### 7.16 Onboarding

- **40–60% of SaaS signups use the product once and never return.** Onboarding is where that is won or lost.
- **Time to first value should be ~2 minutes.** *"A great onboarding flow removes everything between a new user and their first moment of value."*
- **User-triggered tours perform 2–3× better than auto-triggered ones.** Interrupting someone who did not ask is the default failure.
- **Cap tours at five steps** – completion drops by more than 50% beyond that.
- **Contextual banners at the moment the gap appears** – not on login, not by email. Relevance is timing.
- The empty state is the best onboarding surface in the product (§7.2), because it has full attention and no competition.

### 7.17 Pricing pages

- **Three tiers convert ~1.4× two tiers; four or more convert worse.**
- Anchoring: price the top tier **2.5–3× the middle** with features most buyers do not need, so the middle tier's value ratio reads as obviously better. Reported **15–30% lift in average purchase value** from strategic anchoring. `[secondhand, Journal of Marketing Research cited]`
- **Default the toggle to annual**, which anchors a lower perceived monthly number.
- Lead with **business outcomes over technical specs** – reported up to 25% higher perceived value.
- Average SaaS pricing page converts ~2.8%; top quartile 6.5–12%, and *"that gap comes almost entirely from page structure, tier count, anchoring layout, comparison clarity and CTA testing – not from charging different prices."*

`ETHICS NOTE:` anchoring is a legitimate framing device; the line into a dark pattern is crossed when the comparison is **false** – fake scarcity, hidden fees, a "most popular" label on the tier you want to sell rather than the one that is. Record the technique and the line together.

### 7.18 Search and filters

- **Filters** = broad categories; **facets** = granular, multi-dimensional, combinable.
- **Never auto-scroll on a single input. Never auto-collapse a filter group. Never freeze the UI on a single input.** These three are named as the most frustrating filter failures.
- **Avoid the zero-result dead end** – always offer a route out (relax a filter, broaden, suggest alternatives). A zero-result page with no exit is the worst empty state.
- Top-positioned filters only when there are a handful; vertical facet panel otherwise, switching to a sheet or popover on small screens.
- Communicate how AND/OR combinations affect the result set – silently intersecting facets produces confusing empties.
- Autocomplete is not a convenience, it is error correction and step elimination.

### 7.19 Settings

- Group by **type** or by **use case**, and keep groups to roughly **7 ± 2** items.
- Surface the most-used settings; push rare ones into subscreens.
- **The label that opens a group must match the subscreen title.** Small rule, constant violation, real disorientation when broken.
- White space, dividers and headings do the scanning work.
- iOS Settings is the reference for a tiered parent-child structure that grows gracefully.

### 7.20 Trust

- **First impression forms in ~0.05 seconds**, and it is primarily visual (Google research). **Stanford's web credibility work found people judge trustworthiness mainly on visual design and layout.**
- Consequence worth stating plainly to a stakeholder: **visual quality is not decoration, it is the first credibility signal, and it fires before a single word is read.**
- Concrete signals: consistent color/type/messaging, clear navigation, fast load, mobile-friendliness, real names and photos in testimonials rather than anonymous quotes, verified third-party reviews, visible contact information, current content.
- The negative version: *"if your site looks sloppy, people think you have a sloppy business."*

### 7.21 Dark patterns – now a legal matter

- The **FTC treats dark patterns as deceptive or unfair practice under Section 5** of the FTC Act. 2023 action against Amazon over Prime cancellation ("roach motel").
- **GDPR:** consent must be freely given and informed – obscured opt-outs or pre-selected consent **invalidate the consent.**
- **CCPA/CPRA** explicitly prohibits dark patterns that interfere with privacy choices.
- 2026 status: *"UX decisions are under legal scrutiny, especially when they manipulate users into giving consent or make it harder to refuse."*

The named patterns to refuse: roach motel (easy in, hard out), confirmshaming, pre-checked consent, disguised ads, fake scarcity/urgency, hidden costs revealed late, visual weight imbalance between accept and reject.

**Practical rule: the effort to decline must be comparable to the effort to accept.** That single test catches most consent dark patterns, and it is what regulators are actually measuring.

`[sources: ClearPoint / Improvado dashboard practice; Atlassian and UC Berkeley chart selection guides; UXPin chat UI design; Redis "Streaming LLM Responses"; TianPan "The Latency Perception Gap"; Appcues and Chameleon onboarding research; 925studios SaaS pricing page data; Smashing "Designing Filters That Work"; Material settings patterns; Stanford Web Credibility Project; FTC Section 5 / GDPR / CPRA guidance via Pandectes and Secure Privacy]`

### 7.22 Data tables

| Decision | Guidance |
|---|---|
| **Row height** | **44–48px** works for most desktop interfaces. Condensed shows more rows but **reduces readability and causes parsing errors**; relaxed is more comfortable for large data sets. Offer density as a user control rather than picking for everyone |
| **Zebra striping** | `MYTH-adjacent.` Enders (2008): striping **did not significantly improve reading speed**, slightly improved accuracy, and made tables *feel* easier. Another study found no significant accuracy gain and very little speed gain |
| When striping helps | **Dense tables with many columns.** In small tables it **misleads** – it implies grouping that is not there |
| Better alternative | **Row hover highlighting** – place-keeping without permanent visual noise. Often better than striping and it composes with it |
| Legibility | Adequate cell padding, taller rows, clear column separation. **White space does more for a table than any decoration** |

Combine with §1.10 (`tabular-nums` on every numeric column) and §8.9 (headers, scope, caption, and a focusable scroll region at 320px).

### 7.23 Multi-step vs single-page forms

The numbers are unusually strong and consistent:

- **Multi-step converts ~86% higher than single-step**; one comparison reports 13.9% vs 4.5%.
- **Forms with five or fewer visible fields convert ~120% better** than longer ones.
- **Three to five steps is the optimum**; performance degrades past five.

Mechanisms, all three real:

1. **Reduced cognitive load** – discrete manageable units beat one large block.
2. **Mobile** – long single-page forms mean long scrolling and a visible wall of work.
3. **Sunk cost** – once step one is done, completing step two is far more likely, and so on.

**Decision rule: under five fields, single step. Five or more, multi-step in 3–5 chunks with visible progress.**

`ETHICS NOTE:` the sunk-cost mechanism is the same lever a dark pattern pulls. The line: a multi-step form is legitimate when each step is honest about what remains (visible progress, step count) and lets the user go back. It becomes manipulative when it hides the length to bait commitment.

### 7.24 Tooltips

**A tooltip carries one small piece of context the user is missing right now** – small enough to read at a glance, specific enough to answer one question.

- **If the information is needed to complete the task, it must be visible.** A tooltip is supplemental by definition; putting required information behind hover is a design failure and an accessibility one.
- Under **150 characters**; 1–2 short sentences; **under 10 words for an icon label**.
- **Trigger on focus, not only hover.** Open on mouseover *or* focus, close on mouseout *or* blur.
- **WCAG 1.4.13 Content on Hover or Focus** requires: dismissible with **Escape**, **hoverable** (the pointer can move onto the tooltip without dismissing it), and **persistent** until dismissed or the trigger loses focus.
- `role="tooltip"` + `aria-describedby`. 4.5:1 contrast like any other text.
- **Touch has no hover** – design a tap-to-reveal path with an adequate target.
- Known harms: screen-magnification users get content obscured; users with limited pointer control trigger them accidentally or cannot keep them open; anyone who reads slowly loses the content when it vanishes. These are the reasons for the three 1.4.13 requirements.

### 7.25 Badges, chips, tags, avatars – the distinction that stops chaos

| Component | Interactive? | Job |
|---|---|---|
| **Badge** | **No.** Read-only, never clickable | Reports status or change: `draft`, `new`, `pending`, `−7%` |
| **Chip / tag** | **Yes** | Enter, filter, select, remove. Material types: assist, filter, input, suggestion |
| **Avatar** | Sometimes | Identity. **1:1 ratio – never distort it.** Initials fallback |

- **Name them explicitly in the system.** The single biggest source of inconsistency here is calling the same thing three names across a product and then giving it three behaviors.
- Uniform size, color and type across all instances – a badge is recognized by shape before it is read.
- Avatar groups: identical size, radius and content type, or the grouping stops reading as a group.
- 48×48 minimum touch target on mobile for anything interactive (§4.4).

`CONSOLIDATE:` the badge/chip split is a good example of a rule that is invisible in a mockup and structural in a system – a clickable badge teaches users that badges are clickable, and then every static one is a dead end.

### 7.26 Keyboard shortcuts

- **Echo existing muscle memory first** – ⌘F find, ⌘C copy, ⌘S save, ⌘K palette. Fence novel bindings to the context that needs them.
- **`?` opens the shortcuts window.** This is the established convention and it costs nothing.
- **"Go to" sequences** are the convention for navigation: `G` then the first letter of the destination (`G` `I` for Inbox). Highly memorable, no modifier collisions.
- Show the shortcut **next to the command** – in menus, in tooltips, in the command palette. Discoverability comes from surfacing it where the action already is, not from a help page.
- **OS-aware display** – show ⌘ on macOS and Ctrl on Windows/Linux everywhere the shortcut appears.
- Keep one consistent system and write it down; without a map the bindings drift into collisions.

### 7.27 Saving

| Pattern | When |
|---|---|
| **Autosave** | Document editors and anywhere the user expects the change to be instant and continuous. It protects work and removes a chore |
| **Explicit save** | Forms, settings, anywhere the user needs to **review before committing**. *"In most cases, the best practice is an explicit save action"* |

- **User expectation is the deciding factor, not efficiency.** People have learned that forms do not commit until Save is pressed, and that navigating away discards. Violating that is the failure, however "efficient" it is.
- **Keep the Save button even with autosave.** *"Users tend to panic when there is no Save and close button"* – it can do nothing but close, and it still provides the reassurance. This is a rare, genuinely useful case for a redundant control.
- **Never mix save patterns on one page or, worse, in one form.** Mixed models are how data gets lost.
- Autosave needs a **visible state**: saving / saved / failed, with a timestamp. Silent autosave is indistinguishable from silent failure.

### 7.28 Bulk actions

- **Checkbox on hover** hints that rows are actionable without permanently adding chrome to every row.
- **Floating contextual toolbar** on selection – above the rows or fixed at the bottom. Actions appear only when they are relevant.
- **Always show the selected count**, and distinguish "all on this page" from "all matching the filter, including rows you cannot see". Selecting 50 and destroying 5,000 is a classic disaster.
- Destructive bulk actions escalate the confirmation ladder (§7.6) – scale multiplies damage.
- For complex bulk edits, a **wizard** (select → choose fields → resolve conflicts → review → apply) beats a single dialog. Jira's bulk change is the reference.
- Fixed headers, pinned identifying columns and a scrollable body keep orientation in long selections.

### 7.29 Date input

- **Offer both**: type into a text field **and** pick from a calendar. Neither alone serves everyone.
- **Show the format** – `MM/DD/YYYY` – as a persistent hint, not just a placeholder, and auto-insert separators.
- **Separate day/month/year fields are easier for screen reader and keyboard users** than a single masked field.
- **Calendar pickers are for near dates.** For a birth date or anything more than a year away they require absurd navigation – use typed input.
- Always indicate **today** and **the current selection** distinctly, and disabled dates unambiguously.
- Spell month names out; `03/04` is ambiguous across locales in a way that causes real errors.
- 8px minimum gap between day cells; they are among the smallest touch targets in most products.

### 7.30 File upload

- Support **click-to-browse, drag-and-drop, and paste.** Drag-and-drop **always needs a clickable fallback** – it is inaccessible alone.
- **State the constraints inside the drop zone** – accepted types and size limit – *before* a failure can happen. Most upload errors are preventable this way.
- **Progress is the single biggest lever against mid-upload drop-off.** Show a bar, an estimate for large files, and distinct **uploading / processing / complete** states – processing is a separate wait users do not expect.
- Specific, actionable inline errors per file; never a generic "upload failed".
- **Resumable chunked upload** for large files – users expect a retry not to restart from zero.
- Multi-file needs per-file state, not one aggregate bar.

### 7.31 Notifications

- **Two surfaces, one system:** transient **toast** for what just happened (§7.8), persistent **inbox** for what happened while away.
- *"Designed for scanning, not reading"* – strict title / body / timestamp hierarchy, **one quiet unread signal**, and grouping.
- **Group by source or object** – Linear groups by issue and project, Notion by page and role. **No more than 3–4 categories.**
- Badge counts unread; **hide the badge at zero** rather than showing a 0.
- **Read state must sync everywhere.** Marking read in one tab and not another is the defect people notice most.
- The design question that decides everything: **what deserves to interrupt versus what deserves to accumulate.** Most products get this wrong by interrupting for things that could have waited.

### 7.32 Multiplayer and presence

- Cursors: **small, translucent, color-coded, name-tagged.**
- **Cursors stop helping past a handful of people** – beyond that the UI is too busy for the awareness to be worth it. Degrade to an avatar stack.
- **Make it configurable, and offer a focus mode** that hides all collaboration UI while still syncing in the background. Some people find presence supportive, others find it surveillance.
- Motion must be **smooth and interpolated**; ~100ms is the real-time threshold. Jumpy cursors read as broken.
- Avatar stack plus location, with jump-to and follow, is the scalable version of presence.

### 7.33 Permissions

- **A permission matrix** – roles across, capabilities down – is the transparent model, and its design problem is **density**: group capabilities into meaningful categories with collapsible sections, and use unambiguous allowed / not-allowed / partial states.
- The test: **an admin should be able to answer "does this role have this power?" at a glance.** *"A matrix that cannot be read at a glance provides the illusion of control without substance."*
- **The UI must match what the backend enforces.** A Viewer should see the Edit button disabled with an explanation (§7.5), not click it and receive a 403.
- Permissions should be **small, specific and composable.** "Manage everything" hides risk.
- RBAC works because it matches how organizations already think – responsibilities and who may do what.

### 7.34 Infinite scroll vs pagination vs load more

| Pattern | Best for | Cost |
|---|---|---|
| **Infinite scroll** | Exploratory browsing with no specific target – feeds, discovery | **Poor findability**, no sense of position or extent, unreachable footer, and it can prevent proper indexing |
| **Pagination** | The user is looking for **something specific**, or needs to return to a known position | Weak on mobile; extra interaction per page |
| **"Load more" + lazy loading** | **The usability-tested compromise** – found superior in ecommerce testing | Slightly more effort than infinite; slightly less orientation than pagination |

**Default to "Load more" unless there is a reason not to.** It preserves the footer, gives the user control over how much they take on, and keeps position stable – while still feeling continuous.

Whichever is chosen: **preserve scroll position on back-navigation.** Losing a user's place after they open and return from an item is the most-hated failure of all three patterns.

### 7.35 CTA buttons

- **Copy has the largest and most consistent effect** – test it first, before color or placement. Per §13.4: verb-first, outcome-anchored, first-person.
- Sizes: **44×44 minimum** (HIG) / 48 (Material). Desktop primary ~**200×48–56px**, secondary ~160×40. **Mobile primary: full or near-full width (90–95%).**
- **Multiple CTAs at natural decision points** outperform a single CTA – repeat it down the page at the moments where the reader has just been given a reason.
- One short reassurance line beneath ("No card required", "Cancel anytime") removes the last hesitation.
- It should be the brightest thing on screen – which only works if the rest of the page is disciplined (§15.1).
- Reported: personalized CTAs up to **202%** better (HubSpot). `[secondhand]`

### 7.36 Social proof

Placement beats volume, and the numbers are strong:

- **Social proof in the hero generates 2.3× the engagement of identical elements below the fold** (Baymard).
- A page with **testimonials + logos + ratings converts 35–50% better** than one with none.
- **Reviews can lift conversion up to 270%** (Northwestern Spiegel Research Center).
- Placing testimonials **near the top CTA**: +6% conversion, +10.3% unique purchases in one A/B test (Vegetology).
- **Testimonials outperformed logo displays by ~35%** in conversion impact – but CXL eye-tracking found **testimonials with photos and high-profile logos are the most memorable**. Use both; weight testimonials.
- **Relevance beats prestige:** *"a quote from a founder at a 15-person startup resonates more with other small founders than a quote from a VP at Google."* Match the proof to the visitor, not to your own vanity.

`[all secondhand CRO figures – directionally consistent, individually unverified.]`

### 7.37 Skeleton screens – implementation detail

- **Placeholder shapes must match the real content's dimensions.** If they do not, the swap causes a layout shift – the skeleton then *creates* the CLS it was meant to hide.
- Shapes correspond to content type: rounded rectangles for images, lines for text, with realistic line lengths (last line shorter).
- **Shimmer: a `linear-gradient` overlay animated left to right, 1.5–2s cycle, slow and gentle.** Faster reads as agitated.
- Respect `prefers-reduced-motion` – drop the shimmer, keep the shapes.
- Proper ARIA (`aria-busy`, a live region on completion) and a **defined error fallback** – a skeleton that never resolves is the worst loading state there is.
- See §6.7 for the unresolved evidence on whether skeletons help at all.

### 7.38 Dark mode – the toggle

**The three-tier precedence, in order: explicit user toggle → saved preference → OS setting.**

1. Detect with `prefers-color-scheme` and **default to the OS**. Apple and Microsoft both recommend mirroring the system setting.
2. Offer an in-app override.
3. **Persist it** (localStorage or account), and **make first paint correct** – a flash of the wrong theme is the classic implementation failure.

Lea Verou's position is worth recording as a counterpoint: **"the best dark mode toggle is probably none"** – respect the system setting and do not add UI. The counter-argument is that people's needs shift through the day and the OS setting is a blunt instrument.

The accessibility nuance that settles it: **dark mode is not universally better.** Users with astigmatism often find it *harder*; users with light sensitivity benefit enormously; color-vision-deficient users vary. **Because the population is genuinely split, the override should exist** – and it should never be a three-way "auto/light/dark" control hidden in a settings page nobody opens.

### 7.39 Print

Rarely designed, frequently used – invoices, receipts, recipes, tickets, documentation, anything anyone might file.

- `@media print` inline, or a dedicated `print.css` via `media="print"`.
- Hide nav, sidebars, ads, share widgets, cookie banners, interactive chrome.
- Larger, more legible type; dark text on white regardless of theme.
- **`@page` controls size, orientation and margins.**
- **Show link URLs** – `a[href]::after { content: " (" attr(href) ")" }` for external links; a printed page of invisible links is useless.
- Avoid breaking inside a table row, figure or heading – `break-inside: avoid`.
- This is also the PDF path, since "Save as PDF" uses the print stylesheet.

### 7.40 Password fields

- **One field with a show/hide toggle**, not a confirm-password pair. The toggle does the verification job the second field was invented for.
- **Never block paste.** Paste is how people move a generated password out of a password manager; blocking it forces weaker passwords. This is the most damaging single mistake in the pattern.
- **Show the requirements while the field is focused**, before submission, and make them match the server exactly. Requirements revealed only on failure are a design failure.
- **Limit the requirements.** Complex composition rules produce weaker, more predictable passwords and more resets. Princeton's password-policy study found most top sites fail their own best practices.
- **Strength meters do motivate stronger passwords** – but pair them with copy that explains, and **never reject a long passphrase for an arbitrary maximum.**

### 7.41 One-time codes

The screen with an unusually high bail rate, and the causes are almost all technical:

- **`<input type="text" inputmode="numeric">`** – never `type="number"`, which strips leading zeros and treats the code as a single number.
- **`autocomplete="one-time-code"`** enables SMS autofill on iOS/macOS.
- **Paste must distribute across the segmented boxes.** The code is nearly always already in the clipboard; if pasting one 6-character string does not fill six boxes, the design has failed at the only thing it does.
- Segmented boxes give clear structure and reduce transcription errors – but they need **size, contrast and spacing**; cramped digit boxes are hard to hit.
- **Typography: never a face where 0/O or 1/l are indistinguishable.** Monospaced or a purpose-drawn numeric face.
- Visible focus and fill feedback per box.
- **Auto-submit on a valid-length code.**

### 7.42 Phone and address

- **One phone field, not three.** Multiple fields raise cognitive load and slow completion, especially on mobile.
- **Country code alongside**, prefilled from geolocation, changeable. Masking and validation follow the selected country.
- **Keep the mask flexible** – overly strict masks break on legitimate international numbers, which is the most common failure.
- Addresses: **the form structure itself must change by country.** Three approaches – country-specific layouts, a layout that reshapes on country selection, or a generic multi-line format. Autocomplete-as-you-type plus country-driven restructuring is the strongest.
- Never assume State/ZIP; many countries have neither.

### 7.43 Comparison and pricing tables

- **Order rows by what the user needs to decide with, not by what you want to sell.** Seats and storage before add-ons.
- **Sticky first row** carrying plan name, price and CTA, so the identity of each column survives scrolling. **Repeat the CTA at the bottom of each column.**
- Check marks for present, **white space or a hyphen for absent** – never a red cross, which reads as an error.
- Monthly/annual toggle with the billing basis stated unambiguously.
- **Collapse the long tail behind "Show more"** – most visitors want the overview, not all 60 rows.
- Design for **skimming differences**, not reading everything: the eye is looking for where the columns diverge, so make divergence visually louder than similarity.

### 7.44 Accordions and FAQ

- **The whole header row is the hit target**, including padding. Making only the text or only the chevron clickable is the classic mistake.
- **Closed by default**, unless one section is overwhelmingly the likely target.
- Chevron rotation as the state indicator; hover and focus feedback on the header.
- **"Expand all / collapse all"** on long lists.
- Single-open (exclusive) vs multi-open is a real decision: exclusive keeps the page short, multi-open lets people compare answers.
- Native `<details name="group">` gives exclusive behavior for free.
- **SEO note: Google indexes accordion content that is present in the HTML source, but immediately visible content may carry slightly more weight.** So: keep the most important messaging *outside* collapsed sections. Do not hide your value proposition in an accordion.

## 8. Accessibility

**Framing for the skill:** accessibility is not a compliance chore bolted on at the end – it is a set of constraints that, taken seriously, make the design better for everyone. Almost every item below improves the interface for users with no disability at all.

### 8.1 WCAG 2.2 – what changed

Released October 2023. **9 new criteria, 1 retired (4.1.1 Parsing), 78 → 87 total.** The new ones lean toward **cognitive disabilities, motor disabilities and mobile**.

The ones that change design work:

| Criterion | Level | Requirement |
|---|---|---|
| **2.4.11 Focus Not Obscured (Minimum)** | AA | When an element receives focus, **at least part of it must remain visible**. Sticky headers, cookie banners and overlays must not cover the focused element |
| **2.5.8 Target Size (Minimum)** | **AA** | **24×24 CSS px** – or smaller, if a 24px circle centered on the target does not overlap a neighbor's circle. A relaxed, AA-level version of the AAA 44×44 |
| 2.4.13 Focus Appearance | AAA | Focus indicator sizing and contrast, below |

The sticky-header-obscuring-focus failure (2.4.11) is extremely common and almost never caught, because it only appears when tabbing.

### 8.2 Focus indicators

| Requirement | Value |
|---|---|
| Contrast against **adjacent unfocused colors** | **3:1** |
| Contrast against the **focused component itself** | **3:1** |
| Minimum area | 1 CSS px along the perimeter, **or** 4 CSS px along the shortest side |
| Practical floor | **≥2px thick**, covering a meaningful area |

Working default: `:focus-visible { outline: 2px solid var(--focus); outline-offset: 2px; }`

- **Never `outline: none` without a replacement.** This is the single most common accessibility defect in styled UI.
- `:focus-visible` rather than `:focus` – shows the ring for keyboard users, suppresses it for mouse users, which removes the usual excuse for deleting it.
- `outline` survives Windows High Contrast Mode; a `box-shadow` ring does not. Prefer outline.
- Test by tabbing the whole page. That is the entire test.

### 8.3 The first rule of ARIA

> If you can use a native HTML element or attribute with the semantics and behavior you require already built in, do that instead of repurposing an element and adding ARIA.

**"No ARIA is better than bad ARIA."** And the evidence is quantitative: the **2026 WebAIM Million report found pages using ARIA averaged 59.1 detectable errors versus 42 on pages without it.** More ARIA correlates with *worse* accessibility in the wild, because it is usually applied incorrectly.

Native elements bring keyboard behavior, roles, states and cross-AT support for free, across a far wider matrix of OS/browser/AT combinations than any hand-rolled widget.

ARIA is still essential where HTML has no equivalent: tabs, comboboxes, tree views, live regions. Build on semantics first; add ARIA only where the native layer genuinely cannot express the behavior.

### 8.4 Semantic structure

- **Landmarks:** `header`, `nav`, `main`, `aside`, `footer`. **Exactly one `main` per page.** All content should sit inside a landmark.
- **Headings form the page outline.** One `h1` describing the page, matching the title. **Never skip levels** (h2 → h4). Screen reader users navigate by heading far more than by tabbing – a broken outline is a broken navigation system.
- **Skip links** implement 2.4.1 Bypass Blocks. Technically a `main` landmark satisfies the criterion, but keyboard users who are *not* using a screen reader have no landmark navigation – they still tab through the whole nav. So: ship the skip link anyway.
- Lists as lists, buttons as buttons, links as links. A `div` with a click handler is invisible to keyboard and to AT.

### 8.5 Keyboard patterns

**Roving tabindex** is the APG pattern for composite widgets: the composite has **one tab stop**; arrow keys move within it.

- Active item `tabindex="0"`, all others `tabindex="-1"`; move the 0 as focus moves.
- Applies to: toolbars, tab lists, menus, listboxes, radio groups, grids, trees.
- Rationale: 40 tab stops in a toolbar is unusable. One stop in, arrows to navigate, Tab to leave.
- Coming: the **`focusgroup`** HTML attribute, a declarative replacement for hand-rolled roving-tabindex JS. Worth watching, not yet shippable.

### 8.6 Forced colors mode

Windows High Contrast / forced-colors **discards most of your CSS**: custom backgrounds vanish, shadows disappear, decorative borders are stripped, and a user-chosen palette is imposed.

- **This is not a theme to design for. It is strict usability, and the correct posture is small corrective tweaks, not a parallel design.**
- Check with `@media (forced-colors: active)`.
- **Text in images does not convert** – it keeps its original colors and can become unreadable. Another reason to use real text.
- **Focus rings: use `outline`.** Forced colors preserves outlines and drops box-shadows. `outline: 2px solid transparent` shows nothing normally and produces a real system-colored ring in forced-colors mode – the standard trick.
- Anything conveyed *only* by background color disappears entirely here. Same failure class as color-blindness: color must never be the sole signal.

### 8.7 Overlays – state this plainly when it comes up

Accessibility overlay widgets **do not work**, and there is hard evidence:

- **Over 600 accessibility professionals**, including contributors to the WCAG, ARIA and HTML specifications, signed the Overlay Fact Sheet stating overlays do not achieve compliance. Screen readers interact with the DOM, not with overlay scripts; an overlay cannot fix barriers in code it does not change.
- **Structural problems are unfixable from outside** – `div`s where headings belong, missing label associations. No JS layer can retroactively supply correct semantics.
- **25% of all US digital accessibility lawsuits (UsableNet 2024) targeted sites using overlays**; 22.6% in H1 2025. AudioEye's 2026 analysis: **38.5% of businesses sued already had an accessibility "solution" installed.**
- Courts have rejected them, and in some cases an overlay has been treated as **evidence the business knew its obligations and chose a workaround**.

If a user proposes one, say this once, plainly, and offer the real path.

### 8.8 Cognitive accessibility and plain language

- Target roughly **grade 9 / age 14** reading level for general content (WCAG 3.1.5 is AAA at lower secondary level).
- Plain language is not dumbing down – it is clear, concise, well-organized writing that reaches more people.
- **Front-load**: most important information first. Active voice with a clear actor. Short sentences, short paragraphs, chunked information.
- Kill jargon, and define any term the user could not reasonably know.
- ISO 24495-1:2023 is the international plain-language standard if a citation is needed.
- This applies to **every string in the interface** – button labels, error messages, empty states, tooltips, confirmations. UI copy is design.

### 8.9 Data tables

- Real `<table>`, never `div`s styled as one.
- `<caption>` first child, naming the data.
- `<th scope="col">` / `scope="row"`; `colgroup` / `rowgroup` for spans.
- **Complex/irregular tables need `headers`/`id` associations**, listed in the order a screen reader should read them. `scope` is only enough for straightforward grids.
- **Responsive:** at 320px either the table scrolls inside a **labeled, keyboard-focusable region**, or it reflows to cards **without losing header associations**. A scroll container that cannot receive focus is a keyboard trap in reverse – the content is unreachable.

### 8.10 How to actually test

- **Automated tools catch roughly 30–40% of WCAG violations.** They are a floor, not a verdict. This number is worth quoting whenever someone treats a passing axe scan as done.
- Best coverage for the effort: **NVDA + Firefox on Windows**, **VoiceOver + Safari on macOS**.
- Basic competency takes **2–4 hours of practice**; thorough testing, a few days.
- The test that matters: navigate a key flow **without looking at the screen** and see whether you can understand the page, find the content and complete the task from what is announced.
- Test at design validation, in development, in QA, and before any major release or redesign.

**The four checks that cost almost nothing and catch most of it:**

1. Tab through the whole page. Is focus always visible, always in a sensible order, never trapped, never hidden behind a sticky header?
2. Zoom to 400% at 1280px. Does anything require scrolling in two directions?
3. Grayscale the design. Is anything now ambiguous?
4. Turn the images off / read the headings outline. Does the page still make sense?

`[sources: W3C WCAG 2.2; W3C WAI-ARIA APG keyboard interface; WebAIM Million 2026; WebAIM "Creating Accessible Tables" and "Semantic Structure"; MDN forced-colors; Sarah Higley "Quick Tips for High Contrast Mode"; Overlay Fact Sheet; UsableNet 2024; AudioEye 2026; W3C Understanding SC 3.1.5; ISO 24495-1:2023]`

### 8.11 Text over images

**Contrast over an image is dynamic** – the same text can pass on one part of the photo and fail on another. Three techniques, in order of preference:

| Technique | Use |
|---|---|
| **Scrim** – a solid-to-transparent gradient behind the text (e.g. 40% black → transparent) | Text occupying part of the image. Fades smoothly so the image is not flattened. **Fails if text spans the whole image** – the far end loses the scrim |
| **Darken/lighten the whole image** | Text covering most of the frame. Less elegant, always works |
| **Solid or blurred panel behind the text** | Maximum reliability; loses the photographic effect |

- The "floor fade" – darkening gradient across the lower portion – works because the bottom of most photographs is the least informative region.
- A **soft-colored** gradient rather than black keeps the brand palette alive while doing the same job.
- Test against the **actual images**, including the worst-case one, and against the mobile crop where the composition shifts. Same warning as glassmorphism (§12.3) and video heroes (§12.8).

### 8.12 Visual order vs DOM order

**CSS `order` and `grid-auto-flow: dense` break the relationship between what is seen and what is read.**

- Tab order follows **DOM order** in most browsers – **but Firefox follows visual order**, so the experience differs by browser. There is no consistent behavior to design around.
- Screen reader order follows the DOM, so a visually reordered layout is announced in a different sequence than it appears.
- This is a WCAG 1.3.2 (Meaningful Sequence) failure when the visual order carries meaning.
- **The rule: visual order should match DOM order.** Reorder the DOM, or use grid placement that does not require `order`.
- The emerging fix is `reading-flow` (§11.8), which lets the tab and AT order follow the visual arrangement – but it is not yet a baseline.
- Highest-risk place: a responsive layout where `order` is used only at one breakpoint. It then breaks at that width and nowhere else, which is why it is rarely caught.

### 8.13 Older adults

NN/g's research base is substantial: **87 guidelines, 123 seniors across multiple countries, 93 testing 64 sites and apps.**

- **Vision loss is the most commonly reported disability among US adults, and roughly one in six people over 70 report vision impairment.** This is not a minority accommodation, it is a large and growing user group.
- What the research supports: **larger text, high contrast, larger targets**, and avoiding dropdowns and sliders, which are hard with declining motor control – especially on touch.
- Notable and counter to current fashion: older adults show a **preference for skeuomorphic design and icon-based menus** – stronger affordance cues. This lines up exactly with the flat-design usability ceiling in §12.2.
- **Minimize navigation sublevels; keep menus to a single function.**
- Semantic markup, no images of text, styling via stylesheets – so that **native OS accessibility settings work**, which is how many older users adapt interfaces rather than through assistive technology.

### 8.14 Annotating accessibility in handoff

Accessibility annotations specify **focus order, keyboard behavior, screen reader labels, contrast ratios, error handling and states** on the design itself.

- Tooling: the **A11y Annotation Kit**, **Include**, and Microsoft's **A11y Focus Order** plugin for Figma.
- The point is *shift-left*: decide accessibility at design time rather than discovering it in QA, where it becomes rework.
- **Translation for this skill:** since the output here is running HTML rather than a design file, the equivalent is **stating the accessibility decisions explicitly in the delivery** – focus order, keyboard model, announced labels, contrast figures – so they are reviewable rather than buried in markup.

### 8.15 Non-text contrast (1.4.11)

Everything that is not text needs **3:1**: the visual boundaries of controls, their states, focus indicators, and meaningful graphics.

- Applies to: button and input borders, checkbox and radio outlines, toggle tracks, card boundaries where the boundary is the only thing defining the control, custom control edges.
- **Only icons that carry meaning independently need 3:1.** Decorative icons and icons that merely repeat adjacent text are exempt.
- **Over a gradient or image, measure at the point of *lowest* contrast**, not the average.
- **Disabled components are explicitly exempt** – which is the technical loophole behind §7.5's contrast dilemma, and the reason `aria-disabled` plus a readable style is the better answer anyway.

This is the criterion most often missed, because visual review looks at text contrast and stops there. A 1px `#e5e5e5` border on white – the most popular card border in modern UI – is roughly **1.3:1** and fails whenever that border is the control's only boundary.

### 8.16 Icons and labels

The evidence, and it is one-sided:

- **Icon + label beats icon alone and label alone.** *"No research has ever shown labeled icons to be less scannable or recognizable than unlabeled ones."*
- Unlabeled icons are hard to memorize and *"often highly inefficient"*. The canonical case: Microsoft Outlook's icon-only toolbar had poor usability, and **adding text labels fixed it immediately**.
- Recognition of unlabeled icons varies enormously even in context, even for simple shapes.
- **The one advantage of icon-only: visual search is faster than reading labels once the icon is known, and the gap widens with more items.** That is why a toolbar of familiar icons in a professional tool is legitimate – for expert, high-frequency use.
- UIE's finding that **people remember a button's position more than its graphic** is the deeper truth: much of what looks like icon recognition is spatial memory. Which means moving an icon costs more than changing it.

**Rule: label unless the icon is universally understood (search, close, menu, play) or the audience is expert and high-frequency. When unlabeled, always provide an accessible name and a tooltip.**

### 8.17 `backdrop-filter` cost

- **`blur()` is the most expensive filter**, and `backdrop-filter: blur()` is worse because **it reads pixels from the layer below every frame.**
- **Never animate the blur radius** – it re-triggers GPU compositing every frame and *"will tank performance on anything below a flagship device."* Animate opacity or transform; keep blur static. This is the mechanism behind fde's existing "never animate blur" rule.
- Keep radii small on mobile; **avoid `backdrop-filter` on `position: fixed` elements on iOS.**
- Limit the number of simultaneous blur layers.
- Safari still wants `-webkit-backdrop-filter`.

Combined with §12.3's contrast problem, glass effects carry **two** costs – legibility and GPU. Worth using, worth being deliberate about.

### 8.18 Live regions

| Value | Behavior | Use for |
|---|---|---|
| `polite` | **Queues** – waits for the screen reader to finish its current utterance | Almost everything: search result counts, save confirmations, filter updates, status |
| `assertive` | **Interrupts** immediately | Only genuine urgency: errors that block, session expiry, data loss |
| `off` | Not announced (default) | – |

- **Add the live region on page load, empty.** If it must be inserted dynamically, wait **~2 seconds** before injecting text so the accessibility API has registered it – otherwise nothing is announced at all. This single detail breaks more live regions than anything else.
- **Content must be inserted into the DOM**, not merely revealed with CSS.
- Keep messages short.
- Test across JAWS, NVDA, VoiceOver and TalkBack – behavior genuinely differs.
- The design question this raises: **what actually deserves to be announced?** Announcing every filter keystroke is as bad as announcing nothing. Announce the *result* ("24 results"), not the process.

### 8.19 Consent banners – the design is now case law

- **Accept and reject must have equal prominence on the same surface.** The French **CNIL** established this; **Austria's high court ruled in 2025 that a colored "Accept" button against a gray "Reject" link violates GDPR parity.** This is no longer a UX opinion – it is a design specification with enforcement behind it.
- **Withdrawal must take no more effort than granting** – GDPR Article 7(3), explicitly.
- **No non-essential cookies set before active consent.** No pre-selected non-essential categories.
- First layer: **Accept All / Reject All / Customize.** Second layer: per-category toggles.
- Explain what is collected, why, and for how long.
- The UX finding worth carrying: *"Most people click Reject All because the banner looks suspicious or takes too many steps. Making the consent experience quick, honest and visually comfortable improves opt-in numbers."* **Honesty outperforms manipulation on the actual metric** – which is the strongest possible argument against the dark pattern (§7.21).

## 9. Platform conventions

### 9.1 iOS – Liquid Glass (iOS 26, current)

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 9.2 Android – Material 3 Expressive

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 9.3 iOS vs Android – the differences that actually matter

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 9.4 Desktop

- **⌘K / Ctrl+K command palette is now an expected affordance** in any tool with power users. Originated in VS Code and Sublime, now standard in Figma, Notion, Linear, Raycast, Spotlight.
- A command palette is a text input plus a categorized action list. It is also the cheapest way to make a dense product feel fast – it makes discoverability and speed the same feature.
- Command labels: **brief, verb-led, no branded terms.** Limit modifier keys; follow platform conventions; handle the OS differences (⌘ vs Ctrl) rather than hardcoding one.
- Desktop earns: hover states, higher density, smaller targets (24px min, 32px comfortable), multi-column layouts, keyboard-first flows.

`[sources: Apple HIG / Liquid Glass; createwithswift.com "Liquid Glass: Redefining design through Hierarchy, Harmony and Consistency"; Google Design "Expressive Material Design" research; Material 3 Expressive; UXPin iOS vs Android; NN/g "UI Copy: Guidelines for Command Names and Keyboard Shortcuts"; Rob Dodson "Command palettes for the web"]`

### 9.5 Internationalization

**Use CSS logical properties by default, not physical ones.** `margin-inline-start` instead of `margin-left`, `padding-block` instead of `padding-top`/`bottom`, `inset-inline`, `border-inline-end`.

- The inline axis follows text flow; the block axis is perpendicular. One stylesheet then serves LTR and RTL with **no direction-specific overrides**, which is the entire maintenance argument.
- Set the `dir` attribute on `<html>` (`ltr` / `rtl` / `auto`).
- What does **not** mirror: logos, clocks, media playback controls, and most photographic content. Icons that imply direction (back/forward, next/previous) **do** mirror. Numbers do not.
- Practical rule for the skill: **write logical properties from the start.** It costs nothing when the project is English-only and saves a full re-layout if it is ever not.

**CJK typography** differs in ways that break Western assumptions:

- **Japanese has no spaces between words**, so a break could theoretically occur anywhere – but JIS X 4051 (Japanese) and GB/T 15834 (Chinese) define characters that **must not** start or end a line (kinsoku).
- **Korean has spaces** and allows two wrapping modes: syllable-based (common, especially justified) or whole-word (common with a ragged edge).
- `word-break: auto-phrase` (Chrome 119+) uses ML phrase parsing (BudouX) to break at phrase boundaries rather than mid-word – a real improvement for Japanese.
- **CJK wants more leading: ~1.7**, versus 1.5 for Latin, because character density per line is much higher.
- Chinese and Japanese can set **vertically**, with punctuation that rotates or swaps to vertical-specific glyphs.
- Latin type scales do not transfer – CJK glyphs occupy a full em box and read larger at the same nominal size.

### 9.6 Mobile navigation

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 9.7 Cross-browser and cross-platform reality

**The iOS constraint that changes decisions:** *"Safari's WebKit engine runs on every iOS device by policy – any WebKit rendering bug affects 100% of your iOS users regardless of which browser icon they tap."* Chrome on iPhone is Safari. There is no fallback browser to recommend.

Known Safari divergences: `position: sticky` still has unresolved issues; transitions that are smooth in Chromium can stutter; flexbox overflow, grid gap and print stylesheets differ subtly; `backdrop-filter` on `position: fixed` misbehaves (§8.17); IndexedDB is slower; ITP limits cross-site storage.

**Form controls are the most inconsistent components across browsers** – `select`, `date`, `range`, checkboxes and radios all render natively and differently. This is exactly why `accent-color` (§11.8) and `appearance: base-select` (§11.1) matter.

And the recurring one: **16px on inputs or iOS Safari zooms** (§4.9).

### 9.8 Font rendering by platform

Two different philosophies, and they produce visibly different type:

| | macOS | Windows |
|---|---|---|
| Engine | Quartz, **grayscale antialiasing**, largely **ignores hinting** | ClearType / DirectWrite, **aggressive hinting** to the pixel grid |
| Priority | **Preserve the typeface design**, accepting some blur | **Sharpness and pixel alignment**, accepting deviation from the design |
| Result | Text looks **bolder and heavier**, strokes faithful | Text looks **thinner and sharper** |

**Design consequences, and they are real:**

- **A weight that looks right on a Mac often looks thin on Windows.** A 300-weight display face that reads elegant on macOS can read weak and fragile on Windows. Check the light end of the ladder on Windows before committing.
- Very thin strokes and very low-contrast text are the most affected.
- If the audience is Windows-heavy, bias one step heavier and one step higher contrast than looks right on a Mac.
- This is a genuine hazard for this skill specifically, since design decisions get made and judged on one platform.

### 9.9 PWA install

`MOVED → design-native/study-book/learning.md.` Native-platform material; extracted so this study stays web-focused. Section numbering is preserved so every existing §-reference still resolves.

### 9.10 Low vision and magnification

- **WCAG 1.4.4: text resizable to 200%** without loss. **WCAG 1.4.10: reflow to a single column at 400%** with no two-dimensional scrolling (§4.6).
- **The experience that matters: at high magnification the user sees a small window onto the page** – they lose all spatial context. Anything that depends on seeing two things at once (a label far from its field, a fixed sidebar, a toolbar that scrolls away) becomes unusable.
- **Avoid fixed positioning that breaks at high zoom** – a fixed header at 400% can occupy most of the viewport.
- **Relative units throughout**; fixed px type does not respond to text-only zoom.
- **Images of text pixelate; real text does not.** Another reason (with forced-colors, §8.6, and translation, §14.29) never to set type in an image.
- Adequate spacing between interactive elements matters more at magnification, because a small visual window makes precise pointing harder.
- **Design for it from the start rather than retrofitting** – it is a layout property, not a style fix.

### 9.11 Sustainability

- **~0.8g CO₂ per page view** on average; the internet is estimated at **~3.7% of global carbon emissions.** Data centers projected at ~1,000 TWh annually by 2026.
- **Page weight is the single biggest factor.** Average UK page ~**4MB** (HTTP Archive 2025); a deliberately optimized build can run **under 200KB** – a 20× difference.
- Emerging: **carbon-aware computing** – sites detecting a high-carbon local grid and switching to a lite mode.
- Commercial angle worth knowing: **62% of consumers say they would choose one brand over another if the products were identical and one site was clearly greener** (Green Web Foundation). `[secondhand]`

`CONSOLIDATE:` sustainability and performance are the same work with two justifications (§10.9). Every reduction argument gets stronger when it can be made on both grounds, and page weight is the shared metric.

## 10. Performance as a design constraint

**Framing:** performance is not an engineering concern that happens after design. It is a design material. A design that cannot load fast is a design that most people never see.

### 10.1 Core Web Vitals 2026

| Metric | Good | Measures |
|---|---|---|
| **LCP** | **< 2.5s** | Loading – when the largest content element paints |
| **INP** | **< 200ms** | Responsiveness – **the worst interaction across the entire visit** |
| **CLS** | **< 0.1** | Visual stability |

- Google grades at the **75th percentile over a 28-day rolling window** from CrUX field data. You pass only when ≥75% of real page views hit "good". **Lab numbers are not the score.**
- **INP is the hardest and most-failed vital: 43% of sites fail it in 2026.** It reports the *worst* interaction, not the average, which is exactly the metric a designer should care about – one janky interaction ruins the page's rating and the user's impression.
- Sites passing all three see **24% lower bounce rates**. `[secondhand]`
- Only **42% of mobile sites pass all three**; 63% on desktop – while mobile is 62% of ecommerce traffic.

### 10.2 The business case, with numbers

Have these ready, because they are what moves a stakeholder who will not be moved by craft arguments:

- **Every 100ms of load time ≈ 1% of conversions.** A $10M/year ecommerce site recovers roughly **$500k** from a 500ms improvement.
- **A one-second delay reduces conversions ~7%.**
- Each additional second in the 0–5s range cuts conversions **~4.4%** on average; the best conversion rates cluster under **2 seconds**.
- **Bounce rate rises toward 90% as mobile load goes from 1s to 5s**; up to **53% of visits abandoned** past 3 seconds.
- Average 2026 load: **2.5s desktop, 8.6s mobile.** The mobile gap is where the money is.

`[all secondhand aggregations – directionally consistent across sources, but cite as "widely reported" rather than as a single study.]`

### 10.3 CLS – it is almost entirely a design problem

The complete cause list, and every one of them is a design or markup decision:

| Cause | Fix |
|---|---|
| Images / iframes with no dimensions | **`width` + `height` attributes** – the browser derives aspect-ratio and reserves the box. Free, and it is the single biggest win |
| Web font swap with different metrics | `font-display: swap` **plus a metric-matched fallback** (`size-adjust`, `ascent-override`) – or `font-display: optional` |
| Injected content: ads, cookie banners, promo bars | **Reserve the space with `min-height` before it arrives.** If you cannot know the size, do not inject above existing content |
| Animating `top`/`left`/`width`/`height` | `transform` |
| Late third-party scripts | Reserve space; load them where they cannot push content |

"Four fixes resolve the vast majority of CLS": image dimensions, reserved space for injected content, metric-matched font fallback, transform-based animation.

**Design implication:** a cookie banner or promo bar that pushes the page down is not a nuisance, it is a measurable ranking and revenue defect. Overlay it, or reserve its space.

### 10.4 Images

| Format | Support (2026) | Verdict |
|---|---|---|
| **AVIF** | **~93.4%** (Safari 16.4+) | **20–30% smaller than WebP at equal visual quality**; 15–40% fewer bytes in testing. Better gradients. Supports HDR and wide gamut. 5–10x slower to encode – a server-side cost only |
| **WebP** | **~97%** | Wider reach, fast encode. The fallback |
| JPEG/PNG | universal | Final fallback only |

**Ship AVIF with a WebP fallback via `<picture>`.** The remaining gap is legacy Android and iOS 15 and earlier.

### 10.5 Rendering – `content-visibility`

`content-visibility: auto` **skips layout and paint for offscreen elements entirely.** ~90%+ support in early 2026 (gap: Safari before 18).

**Always pair with `contain-intrinsic-size`.** Without it the browser estimates 0×0 for offscreen content, so scrollbars jump and CLS appears the moment it renders:

```css
.article-card { content-visibility: auto; contain-intrinsic-size: auto 500px; }
```

Apply only to **large, self-contained, repeating sections** – articles, feed items, long list rows. Applying it broadly is worse than not applying it.

### 10.6 Instant navigation – Speculation Rules

Prefetch (HTML only) or prerender (full page in a hidden tab, JS executed, ready to swap).

- **Prerendered navigations measured p75 LCP of 320ms vs 1,800ms – an 82% improvement.**
- The design insight: **a 200ms hover over a link is free time.** Predicting intent from hover, and using that window, is the cheapest large perceived-speed win available.
- Cost: prerendering executes the page. Analytics fire, resources load, work is done for navigations that may never happen. Use `moderate`/`conservative` eagerness for anything expensive, and never prerender something with side effects.

### 10.7 The four highest-impact LCP fixes

1. **Preload / prioritize the LCP image** (`fetchpriority="high"`; never `loading="lazy"` on it).
2. **Inline critical CSS.**
3. **Preload fonts, `font-display: swap`** – and per project convention, deliver preloads as HTTP `Link` headers for 103 Early Hints, not DOM `<link rel="preload">` tags.
4. **Server-side render.**

### 10.8 Perceived vs actual performance

The distinction the skill should hold: **actual performance is a number, perceived performance is a design outcome, and they can be improved independently.**

- Progressive loading – prioritize the order elements appear so the user can build a mental model and start interacting while the rest arrives.
- Optimistic UI (see 6.15) – decouple feedback from the network.
- Speculative loading – spend the user's decision time.
- Skeletons – contested (see 6.7), but the *ordering* advice holds regardless.

`[sources: web.dev Core Web Vitals; corewebvitals.io; MDN content-visibility / contain-intrinsic-size; MDN Speculative loading; DebugBear content-visibility; Sentry "Instant loads with Speculation Rules API"; 2026 page-speed aggregations (multiple secondary sources)]`

### 10.9 Performance budgets – how design decisions get made

A performance budget is **a limit set at the start of the project**, agreed between design and engineering, that every later decision is checked against.

The reason it belongs in a *design* skill is this framing, which is the best argument found for it:

> *"If you want a large hero image or video, you may need to cut down on the number of typefaces. You could design with one display face and keep a system font for body copy. Depending on your icon usage, consider SVGs over icon fonts."*

**The budget converts performance from a constraint imposed afterward into a currency the designer spends.** That is a far more productive relationship than "make it faster later", and it produces better design because it forces prioritization.

- Set it at the start, with designer and developer together, so there is input on every component.
- Benchmark against competitors – find one that performs well and beat it.
- A useful stretch target: **loads in 3 seconds on 2G.**
- **Every feature, asset, animation, font and dependency must justify its existence** and measurably improve the user's ability to complete a task.
- Load times over 3s are associated with ~40% higher bounce.

### 10.10 Low bandwidth and low-end devices

Worth designing for explicitly, not as charity but because it is a large real audience: intermittent connections, expensive data, old devices, restricted corporate networks, mobile-only access.

- **Reduction is the single biggest lever** – not optimization, removal.
- Modern formats (AVIF/WebP), lazy loading below the fold, **minimize DOM size**, CSS-based and minimal animation.
- Test under throttling in DevTools rather than guessing.
- A design that only works on a flagship phone on wifi is a design that excludes most of the world.

### 10.11 Designing for machines

The 2026 addition to the brief: a growing share of users never see the page – **an AI agent parses it on their behalf.**

- **Structured data is the base layer.** Organization schema (legal name, founding date, URL, logo, contact, social profiles) is *"how LLMs distinguish your brand from every other use of the same word."*
- **`llms.txt`** – structured Markdown, single H1 for the project or brand, blockquote summary immediately after. Published by Anthropic, Stripe, Vercel, Cloudflare and others. Reported to reduce hallucination 30–70% when tools bundle it into context.
- **Honest caveat:** **Google states it ignores `llms.txt`** and that such files *"will neither harm nor help your site's visibility"* in Google Search. The argument for it is signalling to non-Google AI systems and improving citation likelihood – **not** a Google ranking factor. Say this plainly rather than repeating the hype.
- Where design genuinely intersects: **semantic HTML, heading hierarchy and clear structure now serve three masters at once** – accessibility, search, and machine parsing. Same practice, three justifications, which makes it much easier to fund.
- Search behavior shifting from keywords to conversational prompts favors **natural-language FAQs and direct-answer sections placed higher in the page**.
- Reported: organic CTR can drop **over 61%** when an AI Overview is present. The strategic conclusion in the sources: *"the future of SEO is not ranking higher – it is becoming the answer."*

### 10.12 Image placeholders

Progressive loading with a low-quality placeholder, ordered by cost:

| Technique | Notes |
|---|---|
| **CSS-only LQIP** | A tiny blurred version encoded compactly; zero network requests, zero JS |
| **Base64 LQIP** | ~20×20px blurred image inlined as a data URI. Simple and effective; adds bytes to the HTML |
| **BlurHash** | DCT of the image, keeping key components, Base83-encoded. The original algorithm (Wolt). Very compact string |
| **ThumbHash** | Newer – **better color rendering and transparency support**. The current best-in-class |
| **SQIP** | SVG shape approximation. Distinctive look, more overhead |

- Combine with `IntersectionObserver` lazy loading, or native `loading="lazy"`.
- Next.js and Gatsby ship this behavior in their image components.
- **Reserve the correct aspect ratio regardless** (§10.3) – the placeholder must occupy exactly the final box or you have traded a blank for a layout shift.

The design value: the placeholder communicates *composition and color* before the image lands, so the page reads as complete earlier. It is the image equivalent of the skeleton, and it does not suffer the skeleton's evidence problem (§6.7) because it is showing real information about the actual image.

## 11. Modern CSS capability

**Why this section exists:** what is *possible* changes what is *worth designing*. A large amount of 2026 UI can be built with no JavaScript at all, which changes the cost of ambitious design – and removes the usual excuse for shipping a worse one.

### 11.1 Capability table – September 2026

| Feature | Support | Replaces |
|---|---|---|
| `:has()` | **~93%** (Safari 15.4+, Chrome 105+, FF 121+) | JS class-toggling for parent state |
| Container queries (`inline-size`) | **>93%** | Component-level breakpoints, variant props |
| **Anchor positioning** | **~91%**, Baseline 2026 (Chrome 125+, FF 132+, Safari 18.2+; `@position-try` needs 18.4+) | **Floating UI, Popper.js, Tippy** |
| **Popover API** | Baseline Widely Available **April 2025**, ~85–90% of real traffic | Dropdown/tooltip JS, light-dismiss logic |
| `@starting-style` + `transition-behavior: allow-discrete` | Chrome/Edge 117+, FF 124+, Safari 18.2+ (all majors since Dec 2024) | JS enter/exit animation orchestration |
| **Subgrid** | **Baseline Widely Available March 2026**, >90% | JS equal-height, magic-number card alignment |
| Scroll-driven animations | **~84%** | IntersectionObserver reveal, JS parallax |
| View transitions (same-doc) | Chrome 111+, Safari 18+, FF 144+ | Route-transition libraries |
| `@layer` | All major engines | Specificity wars, `!important` |
| `content-visibility` | **~90%+** (Safari 18+) | Virtualized-list libraries, for simple cases |
| `field-sizing: content` | Baseline **newly available June 2026** | Auto-growing textarea JS |
| `text-box-trim` / `text-box-edge` | Works across latest browsers **since Aug 2026** | Manual optical spacing hacks |
| `shape()` | Chrome, FF, Safari, Edge | SVG `path()` clip-paths that break on resize |
| `reading-flow` | Emerging | Fixing tab order broken by visual reordering |
| `appearance: base-select` | **Chrome/Edge 134+ only**; FF and Safari implementing | Custom dropdown component libraries |
| `::scroll-button()` / `::scroll-marker()` | Emerging (Chrome 150) | Carousel libraries |

### 11.2 Anchor positioning + Popover – the biggest practical change

Together these replace what used to be a JS library and several hundred lines of positioning logic:

- **Popover attribute** handles show/hide, top-layer, focus management and light dismiss.
- **Anchor positioning** handles *where* it appears, with `@position-try` giving automatic viewport-overflow flipping.

**For new work in 2026 there is no reason to install Floating UI, Popper or Tippy for basic anchored positioning.** Native is smaller, faster and more robust for the ~90% case.

### 11.3 Popover vs dialog – the decision rule

| Use | When |
|---|---|
| **`popover`** | The user should be able to **ignore it and keep working**. Tooltips, dropdowns, help panels, filter menus, autocomplete. Non-modal, background stays interactive, light-dismiss by default |
| **`<dialog>` + `.showModal()`** | Attention is **required**; background interaction must stop. Focus trap, inert backdrop, Escape |
| `<dialog popover>` | Dialog semantics with popover behavior |

`popover` is **not** a dialog replacement. If it needs a focus trap or must block the background, it is a dialog.

### 11.4 Entry and exit animation without JS

The four-part combination that finally solves animating to and from `display: none`:

```css
.panel {
  transition: opacity 200ms, transform 200ms, display 200ms allow-discrete;
}
@starting-style { .panel[open] { opacity: 0; transform: translateY(8px); } }
```

- `@starting-style` supplies the "before it existed" state.
- `transition-behavior: allow-discrete` lets discrete properties (`display`, `overlay`) participate.
- Display flips to visible at **0%** on entry and to `none` at **100%** on exit, so the element is visible for the whole animation in both directions.

This is what makes the asymmetry rule (6.1) implementable in pure CSS for modals, popovers and drawers.

### 11.5 Subgrid – the card-alignment fix

The classic failure: three pricing cards, one title wraps to two lines, one blurb is longer, and the three CTAs sit at three different heights. The row looks broken and no amount of padding fixes it.

`grid-template-rows: subgrid` makes the card inherit the parent's row tracks, so **every title, body and footer aligns across the row – no JS, no fixed heights, no magic numbers.**

Also excellent for form layouts (label column at `max-content`, inputs aligned) and nested sections sharing the page's column structure.

### 11.6 `:has()` patterns worth knowing

```css
.card:has(> img)            /* layout changes when there is media */
label:has(input:checked)    /* style the label from the input's state */
.field:has(input:invalid)   /* validation styling on the wrapper */
.field:has(input:focus)     /* focus-within with more precision */
form:has(:invalid) .submit  /* act on a descendant's aggregate state */
```

**Performance:** modern engines use Bloom filters and ancestor-invalidation, so a well-scoped `:has()` performs close to a normal descendant selector. The rule: **keep the anchor specific and constrain the argument with `>` or `+`.** The genuine risk is `html:has(...)` / `body:has(...)`, which is re-evaluated globally on every DOM change – fine for a theme toggle, bad if the argument is broad.

Specificity gotcha: `:has()` takes the specificity of its **most specific argument**.

### 11.7 `@layer`

2026 cascade order: **origin/importance → inline styles → cascade layers → specificity → source order.** Layers sit *above* specificity, which is why they end the specificity war architecturally rather than symptomatically.

- Declare the order once at the top: `@layer reset, base, components, utilities;`
- **Unlayered styles win over all layered styles** – the most common surprise.
- Tailwind v4 ships `@layer` internally (`theme, base, components, utilities`), so component styles always yield to utilities with no `!important`.

### 11.8 Small things with disproportionate value

- **`accent-color`** – one property themes checkboxes, radios, range and progress with the browser's own accessible-contrast logic, and it respects `color-scheme`. It replaces a pile of custom control CSS and is more accessible than most hand-built replacements.
- **`color-scheme: light dark`** + `light-dark()` – native control theming and two-mode color in one declaration.
- **`field-sizing: content`** – a textarea that grows with its content, no `scrollHeight` listener.
- **`text-box-trim` / `text-box-edge`** – trims the font's built-in leading above and below the text box, so a button's optical padding finally matches its declared padding. This is the fix for "why does this text sit 2px high in its container", which is one of the most common invisible craft failures.
- **`shape()`** – responsive clip paths in percentages and CSS units, instead of SVG `path()` coordinates that break on resize.
- **`reading-flow`** – aligns keyboard/AT order with the *visual* order in flex and grid, fixing the accessibility hole created by `order` and `grid-auto-flow: dense`.
- **`::scroll-marker()` / `::scroll-button()`** – browser-generated carousel dots and arrows that are focusable, keyboard-accessible and auto-disabled at the boundaries. Accessible by construction, which hand-built carousels almost never are.

### 11.9 The design consequence

Two things follow, and both belong in the skill:

1. **Ambitious interaction is now cheap.** Anchored popovers, animated modals, aligned card grids, scroll-linked reveals and accessible carousels cost CSS, not a dependency and a bundle. Design accordingly.
2. **The native versions are more accessible than the hand-built ones**, because focus management, keyboard behavior, top-layer stacking and light dismiss come with them. Reaching for a custom component is now usually a *downgrade* in accessibility, not an upgrade in control.

`[sources: State of CSS 2026; Chrome for Developers "What's new in web UI" (I/O 2026) and "Four new CSS features for smooth entry and exit animations"; MDN text-box-trim / anchor positioning / CSS carousels / reading-flow; web.dev "dialog and popover: Baseline layered UI patterns"; MDN @layer; modern-css.com 2026 roundups]`

## 12. Design directions and styles

### 12.1 The AI-slop signature – now measurable

This is the most important finding in this section, because it is the failure mode this skill exists to avoid, and it is **documented rather than asserted**.

**The signature:** Inter headlines · a purple→blue gradient (specifically around Tailwind `blue-600` → `purple-500`/`pink-500`) · three feature cards in a row · a gray 1px border on every card · large rounded corners · dark mode · centered hero with a floating screenshot.

**The scale:** roughly **three quarters of new commercial pages launched in Q1 2026 carry at least one strong AI-slop signature in their visual layer**, and about the same share in the textual layer. The blue→purple gradient appears in a striking majority of generator outputs and a clear majority of newly launched indie SaaS pages.

**Why it happens, mechanically:** models trained on similar datasets converge on the same patterns, and **the model picks the statistically safe average instead of committing to a direction.** That sentence is the whole problem. Averaging is the opposite of designing.

**The cost is measured, not aesthetic:** recognition is fast and the response is dismissal – **viewers who pattern-match a piece as AI-made assign it less effort and less credibility.** Looking generic is not a neutral outcome; it actively subtracts trust.

`CONSOLIDATE:` the skill's anti-slop section should be built from this, and framed as *"commit to a direction"* rather than as a banned-list. A ban list produces avoidance; a commitment produces design. fde's version bans Inter outright, which is wrong – **Inter is a good typeface being used as a default rather than as a decision.** The failure is the absence of a choice, not the typeface.

### 12.2 The pendulum – where style actually is in 2026

The clearest narrative across every source:

**Photorealistic skeuomorphism (early 2010s) → flat (mid 2010s) → the return of depth (2024–2026).**

- **Why flat failed:** it solved real problems – clutter, inconsistency, decorative excess – but **over-flattening stripped the affordance cues that tell users what is interactive.** After a decade, a "usability ceiling": people could not reliably tell a button from a label.
- **Why early skeuomorphism failed:** its depth was **fake decoration**. Neumorphism died faster because its depth **erased contrast**.
- **What survives:** the 2024–2026 return of depth is *selective* – texture and material metaphor **only where they communicate something.** Frosted/liquid glass survives because it **treats depth as information rather than ornament.**

That last phrase is the whole test, and it is the right one to carry into the skill: **does this depth carry information, or is it decoration?**

Both platform vendors moved the same way in the same cycle – Apple's Liquid Glass ("hierarchy through depth") and Material 3 Expressive (shape, containment, softened forms). That is not a coincidence, it is the pendulum.

### 12.3 Style directions, with honest costs

| Direction | Core move | Where it wins | Real cost |
|---|---|---|---|
| **Swiss / International** | Grid, mathematical alignment, one sans, flush left, objectivity | Dense information, documentation, tools, institutional trust | Can read as cold or generic; it is also the easiest style to do *adequately* and the hardest to do *memorably* |
| **Editorial** | Type-led, serif display, asymmetry, pacing, pull quotes, generous measure | Long-form, publishing, brand storytelling. **Serifs are genuinely back in 2026** – higher contrast, sharper detail, bolder shapes | Needs real content and real images. Fails immediately on thin content |
| **Luxury** | Restraint, 60%+ negative space, muted palette, one exceptional image over a gallery of adequate ones, subtle motion | Premium goods, hospitality, fashion, high-ticket services | Demands genuinely excellent assets. Slow to browse by design – wrong for utility |
| **Neo-brutalism** | Hard borders, offset shadows, zero radius, saturated primaries, exposed structure | Cutting through blandness; explicitly positioned as **the anti-AI-slop aesthetic** | **Harder than it looks – get it wrong and the site just looks broken.** A 2026 usability study found chaotic anti-design as low as **8–10% task success on information-heavy pages.** Use for expressive surfaces, never for dense task UI |
| **Glass / Liquid Glass** | Translucency, blur, layered depth, content visible beneath chrome | Current platform-native feel; overlay chrome above content | **Contrast is dynamic** – text can be legible over one part of a background and invisible over another. Requires a scrim, dynamic text color, or restrained blur. Avoid `blur(20px)+` |
| **Bento** | Modular asymmetric grid, mixed spans, structure carries hierarchy | Feature showcases (5–10 items), dashboards, spec presentation. Origin: Japanese bento box; popularized via Apple keynotes, arguably Microsoft Metro before that. **~67% of top-100 ProductHunt SaaS use it** | Now common enough to be its own kind of default. The largest cell must earn being largest |
| **Organic / soft** | Rounded forms, flowing lines, warm neutrals, soft gradients | The 2026 reaction against strict grids – *"humanity in technology"* | Slides into vagueness without a strong grid underneath |

### 12.4 Why bento works, mechanically

**"It matches how attention moves: to the largest element first, then to adjacent blocks."** The size hierarchy *is* the reading order. That is why it works for specs and features – you can rank content by cell size without writing a single number.

Corollary: a bento grid with **equal cells has no hierarchy** and is just a card grid with tighter gaps. The asymmetry is the feature.

### 12.5 Luxury, decoded

The recurring, useful formulation: **"Luxury design isn't about adding; it's about restraint. Every element must justify its presence."** And the operational version: **a single stunning image carries more impact than a gallery of mediocre photos.**

The signals, concretely: generous margins, comfortable leading, neutral palette, timeless type, large photography, subtle motion, and **no overexplaining** – premium brands assume the right customer will lean in rather than needing to be convinced by volume.

This is worth carrying because it is the clearest case where **the design decision is a subtraction**, and subtraction is the hardest thing to argue for in a review.

### 12.6 The 2026 structural shifts, beyond aesthetics

- **Agentic UX** – designing for systems that *do* things on the user's behalf, not just respond. Multi-step task completion changes what confirmation, progress, undo and trust look like.
- **Machine Experience (MX)** – a growing share of users arrive via conversational agents that **do not see the design at all; they parse semantic structure.** Semantic HTML and structured data become a *visibility* concern as well as an accessibility one. Two arguments for the same practice, which makes it much easier to fund.
- **Sustainability** – leaner code, optimized images, low-impact hosting entering the brief as a stated constraint.
- **Purpose as the maturity marker** – *"design is no longer about adding visual flair just because it looks good; every choice needs a reason."*

### 12.7 Design system vs brand expression

The tension stated well: **a strong brandbook with no design system looks polished outside and is slow and inconsistent inside; a strong design system with weak branding ships fast and fails to differentiate.**

- Off-the-shelf systems (Material, and by extension most component libraries) make products **look like each other** – a real cost in a competitive market.
- The resolution worth keeping: **"custom where it counts."** Use existing solutions for common patterns; invest deeply in the components that actually differentiate the experience.
- Brandbook defines the destination; the design system is the vehicle. They are complementary, not competing.

`CONSOLIDATE:` this maps directly onto how the skill should behave – ordinary components should be conventional and boring, and the two or three surfaces that carry the product's identity should be genuinely designed. Spending design energy uniformly is how you get something both expensive and forgettable.

`[sources: 925studios "AI Slop Web Design"; Sailop "AI Slop in 2026: State of the AI-Generated Web"; kompozy.io "The AI design aesthetic"; Codexical "The Skeuomorphism Revival"; Userology "Flat Design is Dead: Neo-Skeuomorphism"; Axess Lab "Glassmorphism Meets Accessibility"; brutalism.plus / oliverrevelo neo-brutalism 2026; SaaSFrame "Designing Bento Grids That Actually Work"; Smashing "Lessons From Swiss Style Graphic Design"; Figma / Designmodo / Fireart 2026 trend reports; Edana + Lollypop design-system-vs-custom]`

### 12.8 Heavy media – video and 3D

**Video backgrounds.**

- The hero is **almost always the LCP element**, so a video there is a direct Core Web Vitals liability.
- **2–5MB target, under 10MB hard ceiling. 10–30 second loop.**
- **Muted, `playsinline`, `loop`** – and always a **pause control**. Autoplaying motion is a photosensitive-epilepsy and vestibular risk, and `prefers-reduced-motion` must suppress it.
- **Always overlay a semi-transparent gradient scrim** and put the text in a container with `isolation: isolate` to protect the contrast boundary. Headline must clear 4.5:1 **against the actual video frames**, not against an average – video contrast is dynamic, exactly like glassmorphism (§12.3).
- Poster image first, video after; never block first paint on it.

**WebGL / 3D.** 97.54% browser support; 153,000+ sites use Three.js (ChatGPT, Stripe, Wix among them). *"3D became commercially viable in Q1 2026 for automotive, luxury, real estate and professional services."*

- **Optimize draw calls, not triangle count** – instancing, merging, atlases. Compression via Draco and KTX2, plus LODs.
- Tree-shaking takes Three.js from ~600KB to under 200KB.
- **Heavy 3D destroys Core Web Vitals if unmanaged** – LCP, INP and CLS all at risk.
- Mobile: feature detection, graceful degradation, simplified scenes, battery awareness, lazy-loaded assets.
- Three.js now ships production WebGPU alongside WebGL.

**The design position:** 3D and video are *voltage* – they should carry the same scarcity discipline as an accent color (§15.1). One moment, load-bearing, everything else quiet. A site with 3D everywhere has the same problem as a site with five accent colors.

### 12.9 Page archetypes – what each one is actually for

**Ecommerce product page.** Baymard 2026 benchmark: **52% of desktop, 62% of mobile and 64% of app product pages have "mediocre or worse" UX.** Average conversion 1.5–3%; top performers 4–8%.

- Above the fold, together: title, price, primary image, **average rating with review count link**, key variant selector, one primary CTA, one-line delivery/returns promise.
- **Products with 5+ reviews convert 270% better than those with none; over $100, the lift is 380%.**
- Trust elements alone can lift conversion **over 35%**.
- **Unexpected shipping cost at checkout is the leading cause of abandonment – 48% of US abandoners.** Surface it on the product page.
- Mobile: **sticky add-to-cart** that persists through description and reviews.
- **A page loading in 1s converts ~3× better than one loading in 5s.**

**SaaS marketing site.** Section order that recurs: **hero → problem → product → benefits → social proof → objections/FAQ → CTA.**

- The best framing found: *"the salesperson approach rather than a design-first methodology – structure content to answer prospects' questions in the exact order they arise during evaluation."* Design the page as a conversation, not as a layout.
- **Navigation that surfaces trials, demos and pricing outperforms navigation built purely for information architecture.** Nav is a conversion surface, not a sitemap.
- Trust: customer logos, testimonials, case studies, security badges, clear pricing, **real product images**.

**Developer documentation.** Persistent left nav; **copy-pasteable snippets adjacent to the explanation**; on-page TOC (mandatory as pages get longer); site search; breadcrumbs; versioning; audience-based paths; dark mode.

- Popular topics promoted at the entry point rather than buried in the tree.
- The three-column layout (sidebar / prose / TOC) recurs across every documentation system in the corpus (Mintlify, MiniMax, MongoDB).

**Blog / article.** Header metadata that earns its place: title, contextual image, topic, **reading time**, author (credibility), date.

- **Reading time is a commitment signal** – it tells a busy reader whether to start now or save it.
- TOC respects the reader's time and enables jumping.
- Every image gets a caption – *"captions give photos meaning and provide mini-learning experiences."* They are also among the most-read text on any page.
- Paragraph spacing must make section boundaries unambiguous.

**Portfolio.** *"Lead with the projects – big imagery, case studies – and keep the copy tight, letting the design get out of the way of the work."*

- A recruiter should understand strengths **within minutes**; skimmable role, process, result per project.
- Case studies: challenge → role → process → result, **with metrics**. Metrics are what make it credible.
- Short video (30–90s) showing the solution in action outperforms long written explanation.

### 12.10 Brand identity in 2026

A complete system is **logo, color, typography, imagery style, motion behavior, voice, and the rules governing how they flex across formats.**

Three shifts worth recording:

- **Typography as voice.** Custom and experimental type used as the primary differentiator in saturated markets – *"typography now acts as a voice with character."* Confirmed by the corpus, where a large share of the 74 brands run a proprietary typeface as the main brand signal.
- **Modular identity systems.** *"Not one rigid template but a constructor where elements connect by rules but allow variation"* – so the brand stays recognizable while expanding. This is the same idea as a token system, applied to identity.
- **Motion-first.** Kinetic logos and dynamic systems designed for digital first, static assets derived from them rather than the reverse. Directly supports §6.19: motion is part of identity and belongs in the spec.

Also noted: *"Humanised Hybrid Intelligence"* – generative tools for high-volume production with human oversight preserving emotional resonance and point of view. That is a fair description of what this skill should be.

### 12.11 What actually separates award-winning work

The single best line found in the entire study, and it is a **test**:

> **"Take away the animation, and the static frames still look intentional."**

If the design only works while it is moving, the motion is compensating for a composition that was never resolved. That is a check the skill can actually run.

The rest, from the same source:

- **"Award-winners aren't decorated templates. Every type choice, color, and layout grid serves a single idea."** Art direction – a single organizing idea – is the differentiator, not the effects.
- **"Directed motion, not animation for its own sake"** – transitions that carry meaning, scroll sequences that pace a story, micro-interactions that reward attention. *"The motion has a director, not just a library."*
- **Performance is a judging criterion, not a caveat.** *"A 3D hero that drops to 18fps on a mid-range Android, or a 9MB page taking six seconds to paint, will not win – jurors test on real devices."* Most "nice" sites fail here.
- Usability and readability are scored alongside design.

`CONSOLIDATE:` "single idea" + "static frames still look intentional" + "motion has a director" is the entire brief for ambitious work, and each is checkable.

### 12.12 Expressive techniques worth having in hand

**Kinetic typography.** Text that moves, splits, morphs or reveals in rhythm with scroll. *"Something happens that static text can never achieve: an emotional response."*

- Variable fonts are the enabler – **animate weight, width or slant continuously from one file.**
- Patterns: letter-by-letter reveal on scroll, weight shift on hover, phrase morphing.
- In production use by Apple (cinematic text reveals), Stripe (subtle headline animation guiding through features), Spotify (type mirroring playlist mood).
- **Three rules: only `transform` and `opacity`; respect `prefers-reduced-motion`; restraint.** And the timing rule that matters: *"it appears at a moment when the user has actually stopped to read."*

**Grain and noise.** SVG `feTurbulence` generating fractal noise, applied as a background or overlay.

- **`baseFrequency` controls grain size: 0.5–0.8 for fine grain; below 0.3 goes cloud-like.**
- **2–4% opacity** for subtle texture.
- Purpose: breaks up flat color and smooth gradients, adds depth, makes an interface feel tactile. **It is also the fix for gradient banding** (§2.8).
- In use by Apple, Linear and Vercel. Effectively the house texture of 2026.
- Performance is fine: a data-URI SVG background is a static image; `feTurbulence` is computed at render and cached.

**Duotone.** Grayscale the image, then map shadows to one brand color and highlights to another.

- Solves a real problem: **making a set of visually inconsistent photographs look like one system.** Apply the same treatment and any photo fits the brand.
- Reduces the image's color competition so type and CTAs read over it.
- Pure CSS: stack layers with `mix-blend-mode` – the image `darken` against a colored container, an overlay pseudo-element `lighten` to tint the highlights.

**Knockout / masked text.** Three routes: `background-clip: text` with a background image; `mix-blend-mode` on a text-image stack; `mask-image` with an SVG text mask. **`clip-path` for simple geometric cropping, `mask-image` for intricate masks and transparency.**

**Magnetic buttons.** The element leans toward the cursor and springs back.

- The mechanics: **lerp the cursor tracking per frame** so the pull feels physical rather than instantaneous, with **distance falloff** – weaker pull further from center, nothing outside the radius.
- *"The spring config is where the personality lives – snappy and responsive at low damping, heavy and elastic at higher values."* A tight radius reads premium; a wide one reads theatrical.
- **Disabled entirely under `prefers-reduced-motion` and on touch.**

**Custom cursors.** `WARNING` – the strongest accessibility caution in this whole area.

- Legitimate when: creative portfolios, immersive launches, interactive storytelling – *"when the site is already a destination experience, not a utility."*
- **Never** on dashboards, checkout, or anywhere speed-to-task beats delight.
- They **disorient users with motor impairments, break expected interaction patterns, and fail entirely on touch.** Eric Bailey's position is simply "don't".
- If used: enhancement layer only, never replacing the system cursor for those who need it, `prefers-reduced-motion` respected, click-target clarity preserved.

**Scrollytelling and horizontal sections.**

- `position: sticky` is the core primitive – pin an element while content moves past it. This is how NYT- and Bloomberg-class editorial works.
- Horizontal sections suit **chronological or linear content** – timelines, sequences, process steps – where the left-to-right axis carries meaning.
- **This is distinct from scrolljacking (§6.27):** sticky sections keep the user's scroll input mapped 1:1 to progress. Hijacking overrides the mapping. The first is legitimate; the second is not. The line is *does the user's scroll still do what they expect per unit of input.*

### 12.13 Breaking the grid

*"As AI-generated templates flood the internet with perfect layouts, human designers will pivot toward asymmetric storytelling – broken grids, overlapping elements, editorial layouts that feel more like a high-end magazine than a browser window."*

The governing rule, and it is the whole craft:

> **"The most effective broken-grid work keeps the grid as a foundation and selectively violates it – using the structure as a reference point that makes the deviation feel intentional rather than accidental."**

Techniques: split-screen heroes with a deliberately unequal split; elements set at angles against the horizontal-vertical axis; text and images overlapping and extending past grid boundaries; negative space used as an active element rather than leftover.

The failure mode is obvious once named: **a broken grid with no grid underneath is not asymmetry, it is mess.** The deviation only reads as a decision if the system it deviates from is visible.

## 13. Craft and micro-details

### 13.1 What craft actually means

The definition worth keeping: **"Craft is not about endlessly polishing every pixel – it is about building the judgment to know when details matter and when they don't."**

That reframes the whole area. Craft is not thoroughness, it is **allocation**. Uniform polish is as much a failure as no polish: it spends the same effort on the thing nobody looks at as on the thing everyone does.

### 13.2 Shadows – why most look fake

**One shadow is always wrong.** Real objects cast complex shadows whose softness varies with distance. A single `box-shadow` cannot produce that falloff.

**Layer three or more**, each with increasing offset and blur and *decreasing* opacity:

| Layer | Job |
|---|---|
| **Umbra** – tight, small offset, low blur | Defines the **contact edge** – where the object meets the surface |
| **Penumbra** – mid offset, mid blur | The main body of the shadow |
| **Ambient** – large offset, large blur, very low opacity | Scattered ambient light. The widest and faintest |

Rules that matter:

- **Offset and blur must stay in proportion.** *"A 40px blur with a 2px offset looks physically impossible."* This single mismatch is the most common cause of a shadow reading as fake.
- **One light source for the entire interface**, consistently. Material's is top-left; any consistent choice works, inconsistency does not.
- **Never a neutral black shadow on a colored surface** – it goes muddy. **Match the shadow's hue to the environment** and tune saturation and lightness. A shadow on a warm surface is a warm shadow.
- Elevation is a *scale*, not a set of one-offs: 2–3 defined levels, each a defined layer stack, used consistently.
- Dark mode: shadows barely read. Use surface lightness for elevation instead (2.4).

### 13.3 Icons

- **One grid, usually 24×24 with 2px padding** – a 20×20 live area. Everything is drawn inside it.
- **One stroke weight across the set.** 2px for a bolder style, 1–1.5px for a lighter one. Mixed weights in one set is instantly visible and reads as careless.
- **Optical size beats mathematical size:** *"a circle of 20px diameter visually matches an 18×18px square."* Circles and diagonals must be drawn larger than squares to look the same size. This is the same principle as optical alignment (3.6).
- **Pixel-grid alignment:** with 1px strokes, shift shapes by **0.5px** so the stroke lands on the pixel grid instead of straddling it and blurring under anti-aliasing.
- Consistent corner radius across the set (commonly 2px on a 24px grid) and consistent terminal style.
- Align strokes to the grid. Do not draw lines between gridlines.

### 13.4 Microcopy

**The button-label rule, and it is the best single line found in this area:** *"Name the outcome, in the first person of the user's intent."*

| Weak | Strong |
|---|---|
| Submit | Start my free trial |
| Confirm | Send invite |
| OK | Delete 3 reports |
| Next | Create account |

Reported case: a fintech onboarding step where renaming "Next" to "Create account" plus a short hint about terms **increased completion 14%.** `[secondhand]` The mechanism is that "Next" hides the consequence, and users hesitate at consequences they cannot see.

Other rules:

- **Voice is constant; tone varies with context.** Same personality throughout, but the tone shifts for an error, a celebration and a destructive confirmation.
- **Clarity beats personality** – and specifically in high-stakes and high-friction moments, drop the voice and be plain. Nobody wants a joke in a payment failure.
- Microcopy is design, not content that gets written afterward. Labels, hints, empty states, errors, confirmations, tooltips: all of it is part of the interface and should be designed with it.

### 13.5 The amateur tells – a usable diagnostic list

These are the fastest read on why something looks wrong:

| Tell | Fix |
|---|---|
| Too many colors – everything competing | 2–4 core colors, each serving hierarchy rather than decoration |
| **More than three typefaces** | One or two. A third only with a reason |
| No visual hierarchy – everything the same weight | Establish a clear first, second and third read |
| Broken alignment and inconsistent spacing | **"If something feels off, it is usually spacing or alignment"** – check those first, always |
| Default software effects – stock drop shadows, bevels | Custom, layered, intentional (13.2) |
| Inconsistency across screens | Consistency is the most visible marker of professional work |
| Overused texture and pattern | Restraint |

The reassuring finding: *"the answer is less about raw skill than most assume – plenty of people with modest technical ability produce work that looks polished and intentional."* **Polish is systematic, not innate.** Which is exactly why a skill can encode it.

### 13.6 The small details

- **Layered shadows** over a single shadow (13.2).
- **Immediate active state** – a color dip or a slight scale-down **before** any data loads. This is the sub-100ms feedback rule (6.15) expressed as a detail.
- **`::selection`** styled to the brand instead of default browser blue. One line, and almost nobody does it.
- **`caret-color`** matched to the accent in inputs.
- **`scrollbar-width` and `scrollbar-color`** – standardized (Chrome 121+), replacing `::-webkit-scrollbar-*`, which was never standardized. `scrollbar-width: thin` in dense UI; `none` hides the bar without disabling scrolling – legitimate for internal carousels, never for a main scroll region.
- **`text-box-trim`** (11.8) to remove the font's built-in leading so optical padding matches declared padding.
- **`tabular-nums`** on anything numeric that updates or aligns (1.10).
- **Nested radius** by the `outer − padding` formula (3.7).
- **Optical alignment** of icons in containers (3.6).
- **`accent-color`** on native controls (11.8).
- Thumb-zone placement for anything frequently tapped.

Each of these is invisible when present and quietly cheapening when absent. That is the definition of craft.

### 13.7 Design systems – the 2026 toolchain and where it breaks

**The stack has settled:** Figma Variables (design) → Tokens Studio plugin exporting **DTCG-format JSON** (W3C Design Tokens Community Group) → **Style Dictionary** (build) → CSS custom properties, Sass, JS constants, iOS, Android.

- Theming is the payoff for the three-tier architecture (§2.5): **if the semantic layer is right, theming is a token swap.** Figma Collections hold tokens; Modes carry light, dark and per-brand variants.
- **The real failure is drift.** *"A component in Figma and the same component in React can drift apart fast if there is no shared language."* Tokens are the fix because they make translation **mechanical rather than interpretive**.
- **Component API is a design artifact, not an engineering one.** A Figma variant property should map exactly to a React prop – `Type=Primary, State=Default, Theme=Light` is a variant *and* a props combination. Normalizing that API **before production** is what prevents the two from diverging.
- Nathan Curtis's framing: the component API is a **contract** – entry props plus behavior – and it should be designed jointly, not handed over.

`CONSOLIDATE:` when this skill outputs a design it should name tokens the way the system will consume them, so the output is a starting design system rather than a one-off page.

### 13.8 Art direction – images and illustration

**Photography:**

- The art director's job at selection is *"not to choose the most beautiful image, but the one that best embodies the brief."*
- **Consistency over volume:** *"a smaller set of images that feels like one visual world beats twenty impressive but unrelated photographs."* This is the single most useful rule for a project without a shoot budget.
- Consistent **color grading, lighting and depth of field** so the images read as a set. Check lighting warmth/contrast match and that tones complement the palette.
- Write it down: crop rules, when full-bleed works, which treatments apply.

Cross-reference §15.10: the luxury and automotive brands in the corpus use photography as **the entire voltage** – no accent color at all. That only works when the images are genuinely a set.

**Illustration:**

- An illustration system documents **line weight, corner treatment, fill style, and color use** – four variables, and locking them is what makes work by different people look like one hand.
- The purpose is a shared visual language across mediums, not decoration.
- Same discipline as icons (§13.3): one grid, one weight, one vocabulary.

### 13.9 Assets that are always forgotten

**Favicon / app icons.** Ship four files: `favicon.ico` (multi-size 16/32/48), `icon.svg`, `apple-touch-icon.png` (180×180), `icon-512.png` (PWA). Android additionally uses 192×192 and 512×512.

- **SVG favicons support dark mode** via a `prefers-color-scheme` media query *inside the SVG*. This matters more than it sounds: **a black favicon disappears on a dark browser tab, and a white one disappears on a light tab.** Almost every site gets this wrong.
- SVG support is Chrome/Firefox/Edge, so keep the ICO as fallback. 2026 best practice is **SVG primary, ICO + PNG fallback**.
- **Test at 16×16 and 32×32 before exporting anything.** Fine lines collapse. If the mark is not legible at 16px it is the wrong mark for a favicon – simplify or use a fragment of it.

**Open Graph / social preview.** **1200×630, 1.91:1** covers Facebook, X large card, LinkedIn, Slack, Discord, WhatsApp, iMessage.

- Keep headline and faces inside a **center 1080×600 safe zone** – platforms crop differently.
- Under **1MB**, and under **300KB for WhatsApp**. JPEG or PNG for compatibility.
- No ranking effect, real click-through effect. It is the only design most people will ever see of a page they do not visit.

### 13.10 Tooling positions worth holding

**Tailwind (v4).** ~37% of developers; the most-used CSS framework. v4's Oxide engine and CSS-first config removed the performance and tooling criticisms; **the philosophical objection is untouched.** The sharpened 2026 critique is that *"native CSS grew up"* – cascade layers, nesting, container queries, `:has()` and custom properties now cover much of what utility classes were compensating for. A meaningful subset of 2020–22 adopters have migrated back to plain CSS. **Position: fine either way; match the project. Do not introduce it into a project that does not have it, and do not fight it in a project that does.**

**Component primitives.** **shadcn/ui is the default starting point for new React projects in 2026** – not a headless library itself, but the most common way teams consume Radix or Base UI primitives. **As of July 2026 shadcn/ui defaults to Base UI for new projects** (Radix not deprecated, but Radix was acquired by WorkOS and some components have slowed; Base UI, maintained by MUI, is the more actively maintained primitive layer).

The framing that matters: *"components as malleable source code rather than rigid external dependencies"* – UI ecosystems replacing UI kits. And: *"the debate isn't styled vs unstyled, it's how much control you want."*

Headless primitives handle keyboard navigation, focus management, ARIA semantics, RTL and controlled/uncontrolled state. Per §11.9 that is exactly the work most hand-built components get wrong.

**Figma.** 70%+ share among product designers, though 28% of teams were evaluating alternatives in 2024 (up from 12% in 2022). For this skill the relevant point is different: **the deliverable here is running HTML, not a Figma file** – which skips the handoff-drift problem (§13.7) entirely and is a genuine advantage of designing in the browser.

## 14. Process, taste and ideation

### 14.1 Taste is trainable – and here is the method

The single most useful finding for this skill: **taste is not talent.** *"The ability to develop taste is trained through repetitions."* The described method is consistent across sources:

1. **Curate inputs deliberately.** *"Creative directors curate their inputs on purpose and study the best version of something, not the popular version."*
2. **Build a reference library in your head. Every later judgment gets checked against it.**
3. **Break down what works.** When something catches your eye, ask *why* – typography, layout, color, spacing, motion – and compare it against other examples.
4. **Collect, live with it, prune.** The pruning is the part that develops judgment.
5. **Articulate it.** *"Design critiques are training grounds where you learn to articulate taste."* Being forced to defend a decision is what converts intuition into knowledge.

`CONSOLIDATE:` this is the argument for the corpus in §15 and for the skill's critique behavior. A skill that can *name* why something works is doing the thing that builds taste – for itself and for the user.

### 14.2 Divergence before convergence

- **Divergent phase: quantity, deliberately including bold and unconventional options.** The purpose is explicitly *"to prevent designers from limiting themselves to one or few viable solutions too early."*
- **Convergent phase: rank, sort, combine, select.**
- Divergence measurably increases both **novelty and quality** of the final design – not just novelty.

`CONSOLIDATE:` this is the theoretical grounding for the **three-variant HTML demo** the user asked for. The variants are not a presentation device, they are the divergent phase made concrete – and the research says skipping it produces worse work, not just less interesting work.

The corollary matters as much: **the three variants must be genuinely different directions, not three color schemes of one layout.** Three near-identical options is convergence disguised as divergence, and it forfeits the entire benefit.

### 14.3 The discovery questions that actually earn their place

From the design-brief literature, the questions that change the work:

- **What does the business need this to achieve?** Not "what do you want" – what outcome.
- **Who is this for, and what do they already believe** when they arrive?
- **Show me work you admire.** And crucially: **show me work you think is bad, and say why.** The negative examples are more informative than the positive ones – they reveal the actual boundaries.
- **Is there an existing brand or design guideline?**
- Brand personality by indirection: *"if your brand were a newspaper, which one, and why?"* Clients who cannot speak design language can answer this.
- What is the constraint I do not know about yet? Framework, timeline, legal, a stakeholder with a veto.
- Where does traffic actually come from – mobile or desktop? (4.10)
- **Font choice** – per the user's standing instruction, always asked, never assumed.

The principle underneath: **ask questions whose answers change what you build.** A question with only one plausible answer is a wasted turn.

### 14.4 Critique

**Giving:**
- Ask whether feedback is wanted before giving it.
- **Match the feedback to the stage.** "Change direction entirely" is useful in ideation and destructive at the end. Say what stage you are in and what kind of feedback you want.
- Be specific and cite the actual element. *"I like it"* is not feedback; *"the eyebrow spacing sets up the heading well"* is.
- Critique the work, never the person.

**Receiving:**
- Take notes; ask clarifying questions; **repeat back what you heard** to confirm you understood.
- Ask for a specific example when feedback is vague.
- **Close the loop:** say what you addressed, what you did not, and why. This is the step almost everyone skips and it is what makes the next critique better.

`CONSOLIDATE:` the skill should state up front what kind of feedback it wants at each stage – "this is a direction check, not a pixel review" – and it should close the loop when the user gives feedback, saying what it changed and what it deliberately did not.

### 14.5 The self-critique problem

`OPEN:` fde's Phase 4 is a self-graded checklist, which is the weakest possible form of critique – the same mind that made the decision is grading it, with no external reference.

Better mechanisms, from this section's evidence:

- **Check against the reference library** rather than against a rule (14.1). "Does this hold up next to the best example of this kind of thing?" is a harder and more useful question than "does this pass the checklist?"
- **Make it verifiable.** Contrast ratios, 320px reflow, grayscale hierarchy, tab order – these can be *run*, not asserted.
- **Name the trade-off you made and its cost.** A design decision with no stated cost has not been examined.
- **State what you would change with more time.** This forces an honest ranking of the remaining weaknesses.

`[sources: Slack Design "Craft in design"; Josh W. Comeau "Designing Beautiful Shadows in CSS"; CSS-Tricks "Getting Deep Into Shadows"; designsystems.com iconography guide; Adobe Design "How to design effective icons"; NN/g and parallelhq UX writing; Designlab "How To Give And Receive Design Critique Well"; readymag "How to train your taste"; Product Design Psychology "Taste Isn't Talent"; Double Diamond / divergent-convergent literature; 99designs "20 questions to ask your client"; MDN CSS scrollbars styling]`

### 14.6 Norman's three levels – the frame that organizes everything

| Level | What it is | Governed by |
|---|---|---|
| **Visceral** | Immediate, pre-conscious, automatic reaction to appearance. Largely outside the user's control | Color, type, imagery, composition, craft. **Fires in ~0.05s (§7.20)** |
| **Behavioral** | Usability, function, performance, effectiveness in use | Hierarchy, states, feedback latency, error handling, responsive behavior |
| **Reflective** | Meaning, self-image, the story the user tells afterward, whether they recommend it | Brand, voice, values, the memory of the experience |

**A good design addresses all three, and they fail independently.** A beautiful product that is hard to use fails behaviorally. A usable product with no character fails reflectively – which is exactly the AI-slop failure (§12.1): behaviorally fine, visceral neutral, reflectively forgettable.

`CONSOLIDATE:` this is the cleanest framework found for **why craft is not decoration.** The visceral level is not the shallow one – it is the one that fires first, sets trust, and colors everything after it.

### 14.7 Delight – the honest version

- **The paradox:** *"A good user experience flows so effortlessly it doesn't stand out in memory – it works exactly as expected and is remarkable because it's forgettable."* And alongside it: *"world-class UX should be frictionlessly forgettable, but defining brand moments should be delightfully indelible."*
- **The resolution: most of the product should be invisible; a small number of moments should be unmistakable.** Spreading delight evenly produces neither.
- **Repetition is what turns delight into friction.** *"When animations are experienced dozens of times per day, any delay is perceived as friction."* This is the loop question from §6.5 with evidence attached.
- Reported bounds: click-triggered micro-interactions **under ~500ms**; hover-driven ambient animation can run longer (1500–3000ms) because it is not blocking anything.
- **Delight is subjective** – what charms one person irritates another. Novelty for its own sake reliably annoys.
- Emotion is qualitative; it needs qualitative methods to find, not analytics.

### 14.8 Cognitive load

- **Miller: ~7 items held at once.** Beyond that, tasks get harder, details are missed, and people feel overwhelmed. This is the source of the 7±2 rules in settings (§7.19) and nav (§5.7).
- **Hick: decision time grows with the number and complexity of choices.**
- The three practical levers: **chunking** (group related items), **progressive disclosure** (§7.11), and **visual hierarchy** (size, color, contrast, spacing) so the user can prioritize without reading everything.
- Together they are why a dense screen with good grouping outperforms a sparse screen with none. **Load is about structure, not quantity.**

### 14.9 Testing – the cheap version that actually happens

- **Five users find ~85% of usability issues** (Nielsen; Google concurs). **Krug argues three is enough.** *"Quantity is less of the concern – regular testing is."*
- **Guerrilla testing:** one clear goal, two or three simple tasks, **5–10 minute sessions**, real people in a public setting. Insights within hours. Best in early design stages.
- Automated accessibility tooling catches **30–40%** (§8.10) – the same lesson: cheap and frequent beats thorough and rare.

`CONSOLIDATE:` the skill cannot run a usability test, but it can **name the three tasks worth testing** and tell the user that five people and an afternoon will answer the question better than another round of opinion. Knowing when to stop designing and go ask someone is part of the judgment.

### 14.10 Dieter Rams, and why not to quote him uncritically

The ten principles translate unusually well to software – Jony Ive credited them as the starting point for Apple's aesthetic – and *"less, but better"* is a genuinely useful corrective.

But the critiques are real and worth holding:

- **They presume you know in advance how the design will be used.** Digital products get used in ways nobody designed for; that assumption is weaker in software than in a radio.
- **It is a specifically Western, 20th-century minimalism.** *"To some designers Rams' products are sleek and elegant; to others they appear bland and forgettable."* There is a 2025 paper in *Design and Culture* explicitly calling to move beyond the ten principles toward plural, evolving definitions of good design.
- Applied dogmatically, they produce exactly the restrained, tasteful, unmemorable work that the AI-slop critique also lands on (§12.1) – arriving at sameness from the opposite direction.

`CONSOLIDATE:` use Rams as **one available position, named as such** – excellent for tools, instruments, dashboards and anything instrumental; actively wrong for brands whose job is warmth, joy, spectacle or cultural specificity. The skill should be able to argue *for* maximalism when the brief calls for it, and know that this is not a failure of discipline.

### 14.11 White-label and multi-brand

*"One shared codebase, many differentiated behaviors."*

- Theming is a **token swap** – if the semantic layer is right (§2.5, §13.7).
- The thing most systems get wrong: **theming only color and type.** A brand's identity also lives in **corner radius, outline style, shadow use, spacing density and motion character** (confirmed hard by §15.6 – radius carries genre). A multi-brand system that varies only color produces brands that look like the same product wearing different paint.
- Layering: one set of base components matched to the primary brand, then additional decision layers for sub-brands.
- Honest note from practitioners: *"building a genuinely flexible white-label product is one of the more challenging design problems"* – the flexibility fights the coherence, always.

### 14.12 Messaging – the part of design that is words

The formula, and it holds up:

**Headline** (one attention-grabbing sentence) → **subheadline** (what you do, for whom, why it is useful) → **bullets** → **image**.

- **"8 out of 10 people read only the headline."** That single statistic should govern how much attention the headline gets relative to everything else on the page.
- Working pattern: **"We help [audience] get [outcome] without [pain]."**
- Or: **who you help + what problem you solve + why it is better**, under **25 words**, no jargon.
- Two or three short sentences maximum, and **every word must improve clarity or sharpen the differentiation.**
- Cross-reference §5.4: the headline speaks to **what the visitor wants**, not what you sell.

`CONSOLIDATE:` the skill should treat headline and subhead as **design elements it is responsible for**, not placeholder text. A perfectly composed hero with a vague headline is a failed hero.

### 14.13 Design QA – what to check before shipping

Design QA asks *"does it look and feel right?"* where traditional QA asks *"does it work?"* Systematic areas:

- **Visual alignment and spacing** – across all breakpoints, not just the design width.
- **Typography hierarchy** – sizes and weights actually resolving to the scale.
- **Color contrast** – measured, not eyeballed.
- **Interaction states** – all of them, on every interactive element (§7.1).
- **Brand consistency** – tokens used, no inline values (§15.14).
- **Spacing between paragraphs, images and interactive elements** – called out specifically as where consistency shows.

**And the process point that matters more than the list: QA belongs throughout, not at the end** – checkpoints after wireframe, during visual design, before handoff. A polish pass at the end cannot fix a structural problem.

### 14.14 Redesigns

- **"A case study that doesn't document the baseline cannot make a meaningful claim about what changed."** Capture traffic, conversion, bounce and revenue-per-visit *before* touching anything. This applies to any redesign the skill is asked to do.
- **"The strongest redesigns do not rely on one dramatic fix. They improve a series of smaller decisions that remove hesitation and make the next step obvious."**
- Metrics that matter: bounce rate, conversion rate, page load speed, session duration, revenue impact.
- Reported representative results: organic traffic +35%, bounce −20%, session duration +25%, conversion roughly doubled on key landing pages, load 6s → under 3s. `[secondhand, single-agency case studies – directional only]`

`CONSOLIDATE:` when asked to redesign, **ask what the current numbers are first.** If the user does not have them, say that the redesign will be unmeasurable and offer to capture a baseline. That is what a senior person does before touching a working page.

### 14.15 Constraints produce better work – and this is now research-backed

The relationship between constraint and creativity is **curvilinear**: *"a certain level of constrainedness is a prerequisite to creativity."* Not zero, not maximum.

- *"When there are no constraints, complacency sets in and people follow the path of least resistance."* Unlimited freedom produces the *average* solution – which is precisely the mechanism behind AI slop (§12.1).
- Constraints direct cognitive focus, reduce overload, and force connections between unrelated sources.
- Measured effects in creativity-support systems: **more radical creativity, more adaptation and activity, less cognitive overload, less feature fatigue, less topic drift.**
- *"Constraints transform vague aspirations into actionable challenges."*

`CONSOLIDATE:` this is the evidence for the fde principle "constraints produce character", and it justifies the skill **imposing** constraints rather than offering unlimited options. A spec block that says *three colors, two typefaces, one motion character, an 8pt grid* is not a limitation on the design – it is the thing that makes the design possible.

### 14.16 Cross-cultural design

Localization is *"not simply translating interfaces – it is a deep dive into the cultural fabric that shapes behaviors, expectations and values."*

Concrete differences worth holding:

- **The hamburger icon is not universal** – it is misunderstood in markets with different UX conventions.
- **White space reads differently.** *"Whitespace, an essential staple of good design in the West, may be seen as a waste of space in the East."* Dense East-Asian commerce interfaces are not badly designed; they are correctly designed for a different expectation. This is the most important correction in the whole area, because Western designers routinely read density as failure.
- **Color symbolism does not transfer** – reinforcing §2.9's conclusion that color meaning is contextual and not a design input.
- Individualist cultures respond to personalization and autonomy; collectivist cultures to community and shared experience.
- Hofstede's cultural dimensions are the usual framework; local competitor analysis and testing with local users are the practical methods.

### 14.17 What is overused right now

The 2026 cliché list, useful as a "check yourself" pass:

- **AI-generated imagery** – *"instantly recognizable, most notably a specific overused yellow hue."*
- Generic stock photography.
- **Flashy gradients, glowing elements, heavy effects applied without context.**
- **Parallax and blinking/attention-seeking effects.**
- **Mega menus with dozens of options.**
- **Overloaded heroes** – excessive text, multiple CTAs, animation and video all at once.
- **Minimalism itself is now called overused**, with the correction being *"intentionality, usefulness, emotion and human clarity"* rather than more or less ornament.
- And the root cause, stated well: *"design inspiration from competitor sites, showcase platforms and galleries has led to a generic look where every site has the same colors, fonts and layouts."*

`CONSOLIDATE:` this and §12.1 are the same finding from two directions – **the failure mode of 2026 design is convergence.** Every mechanism in the pipeline, human and machine, pulls toward the average. The value a design partner adds is a *committed* direction, and the discipline to keep it.

### 14.18 Section spacing at page level

- **Space before a heading should be roughly 2× the paragraph spacing** – up to 3×, but beyond that sections *"visually fall apart from each other."* Concrete: 20px paragraph spacing → 40px+ before a heading.
- **Large spacers separate sections; small spacers separate related items.** This is the internal ≤ external rule (§3.2) applied at page scale.
- Reference values: ~96px clear space between major sections (ASU standard); the corpus clusters at **80–96px** (§15.9).
- **Reduce top spacing before headings on mobile** – a large gap can leave the next heading below the fold, so the section reads as the end of the page. Same failure as scroll-reveal's illusion of completeness (§5.16).
- Tie spacing values to the line-height unit so vertical rhythm survives (§1.11).

### 14.19 Presenting design work

- **Start with the problem, the user and the goal** – *"give stakeholders a clear lens for evaluating"* before showing anything. Without a lens, every review becomes a preference argument.
- **Show that alternatives were considered and say why this one won.** Presenting one option reads as an assertion; presenting the rejected paths reads as a decision. *"Offering alternatives shows you explored various solutions, and discussing the pros and cons leads stakeholders to appreciate the thought process."*
- **Frame around "why", not "what"** – *"this shifts the mood of the meeting from judgment to collaboration."*
- Direct attention deliberately: *"a clear presentation directs attention to layout, hierarchy, navigation and user flow instead of unfinished visual details."* Say what kind of feedback you want (§14.4).
- Connect to business goals and use language the audience already has.

`CONSOLIDATE:` this is exactly the three-variants interaction. Present **three genuinely different directions with the reasoning attached**, name the trade-off each one makes, and state a recommendation. That is a design presentation, not a menu.

### 14.20 Design rationale

*"A design rationale forces designers to be explicit about what they are doing and why."*

Records the **reasoning, constraints and trade-offs** behind decisions. Benefits: better team communication, reuse of past reasoning, more careful deliberation, explicit trade-off management, and accountability.

`CONSOLIDATE:` the skill should ship rationale with the design, in the delivery. Not a paragraph of justification – **the specific decision, the alternative rejected, and the cost accepted.** Three lines per significant decision. That is what makes a design reviewable rather than merely likeable, and it is the difference between a partner and a generator.

### 14.21 Logos in a digital system

- **A responsive logo system: full lockup, simplified mark, icon-only.** Not three sizes of one file – three *drawings* at different complexities.
- The current term for the sophisticated version is **"morph-marks"** – marks that alter their complexity by environment, detailed on a large display, reduced to a high-contrast geometric form for a watch notification or favicon.
- **>70% of brand interactions now happen on mobile**, so the small-scale rendering is the primary case, not the fallback.
- Direct link to §13.9: the favicon test at 16px is the same test. A mark that fails there needs a simplified variant, not a smaller export.

### 14.22 Design system documentation

Per component: **visual examples of every state and variant, usage guidelines (when *and when not* to use), anatomy and specs, code snippets, accessibility requirements, responsive behavior, and rationale.**

- *"Usage guidelines are where you stop showing what a component does and start telling people when and how to use it."* The "when not to" is the part that gets skipped and the part that prevents misuse.
- **Interactive demos reported to drive a ~90% increase in adoption.** `[secondhand]` Documentation you can poke beats documentation you read.
- Organize by principles / components / patterns / guidelines.
- **The failure mode: single-team ownership.** When only designers or only one team owns the docs, engineering and product needs are missed, and the result is inconsistency, resistance and workarounds.

### 14.23 Kerning and display type

- **"Large sizes make bad spacing obvious."** Spacing errors that are invisible at 16px are glaring at 72px, which is why display type is where craft shows.
- **Workflow order: set tracking first, then kern, then fix awkward pairs by hand.** Tracking is global, kerning is per-pair; doing them in the wrong order means redoing the kerning.
- **Problem letters: W, Y, V, T, L, P.** Diagonals and overhangs create holes next to straight-sided neighbors.
- All caps needs positive tracking (§1.5); oversized headlines need it pulled back in.
- **Manual kerning is worth it above ~24pt** – headlines, logos, display lockups. Below that, leave it to the font's own kerning table.
- Note the tension with all-caps: default uppercase kerning pairs tighten AV/WA combinations, which *creates* dense spots across a whole all-caps title. This is why tracked-out caps need the tracking *and* selective kerning.

### 14.24 Microcopy shapes

The compression worth keeping – **most microcopy is one of three shapes:**

- **Button: verb + outcome** ("Send invite", "Delete 3 reports")
- **Error: what / why / next** ("This email is already registered. Would you like to sign in instead?")
- **Empty state: what / payoff / action** ("You haven't added any projects yet" → "Create your first project")

And the principle underneath: **"Tone at failure points – the error, the limit, the 'you can't do that' – is what users remember. A product that explains problems without blaming the person reading earns forgiveness for the next one."**

Calibrate tone to emotional context: a failed payment carries anxiety and urgency; a validation hint carries none. The same voice, a different tone (§13.4).

### 14.25 Starting from a blank page

- **"Design projects begin with a question, not a sketch."** Start with the problem, not the canvas.
- **Mind map first, on paper.** *"Mind mapping and sketching help a designer process ideas, and this does not happen the same way if the design process starts immediately on a screen."*
- **Generate quickly and without judgment** – the point of the first pages is to *get every bad idea out* to make room for the good ones. This is the divergent phase (§14.2) in practice.
- **"A blank page feels infinite and overwhelming; an outline and a wireframe limit possibilities, which makes starting feel doable."** The constraint is what unblocks – the same finding as §14.15.

`CONSOLIDATE:` the skill's equivalent of the blank page is the first response to an open brief. The right first move is **not** to start generating a layout – it is to establish the problem, then impose a constraint set (direction, palette, type, motion character), and only then build. That order is the entire difference between designing and producing.

### 14.26 What is genuinely new in 2026

Separating real change from recycled trend:

**Genuinely new:**
- **3D has become practical** – WebGL/Three.js matured, browser performance improved, quality asset libraries available. It was a trend-in-waiting for years and is now shippable (§12.8).
- **Human-crafted work as deliberate differentiation** – hand-drawn illustration, custom photography, visible texture, asymmetry, used specifically because AI-generated visuals are now recognizable. Reported: **94% of designers and marketers are reviewing and refining AI output specifically to restore human quality.**
- **Accessibility moved to the front of the process** rather than a compliance pass at the end.
- **AI in the core production loop**, not as a side tool.

**Recycled:**
- Saturated palettes, Y2K nostalgia, dopamine design, '80s/'90s maximalism.
- Kinetic and oversized typography as storytelling – a maximalism idea returning.
- Broken grids.

`CONSOLIDATE:` the second item is the most important thing in this study for positioning the skill. **The differentiator in 2026 is visible human intent.** Which means the skill's job is not to produce competent output faster – it is to help the user commit to something specific enough that no generator would have arrived at it.

### 14.27 Nielsen's 10 heuristics – still the evaluation spine

**Unchanged since 1994, refined only in wording.** Still the most widely applied evaluation framework in UX practice.

1. **Visibility of system status** – see §6.15 (feedback latency), §7.31 (notifications), §7.27 (save state).
2. **Match between system and the real world** – speak the user's language (§8.8, §13.4).
3. **User control and freedom** – undo (§7.6), escape hatches, no scrolljacking (§6.27).
4. **Consistency and standards** – conventions over invention (§6.26 gestures, §7.26 shortcuts).
5. **Error prevention** – better than error messages (§7.4 validation, §7.30 stating constraints up front).
6. **Recognition rather than recall** – labels over memorized icons (§8.16), visible shortcuts (§7.26).
7. **Flexibility and efficiency of use** – shortcuts and accelerators for experts without penalizing novices.
8. **Aesthetic and minimalist design** – every element competes with the important ones.
9. **Help users recognize, diagnose and recover from errors** – what / why / next (§14.24).
10. **Help and documentation** – findable, task-focused.

*"Their strength is their generality: they don't say 'put the login button top right', they say 'speak the user's language', 'give immediate feedback', 'be consistent'."*

`CONSOLIDATE:` these are the right backbone for the skill's **diagnostic** pass – they name *what class of thing is wrong*, and the rest of the study supplies the specific fix. A heuristic without a fix is a complaint; a fix without a heuristic is a preference.

### 14.28 Component proportions

**Buttons:**
- **Horizontal padding ≈ 2× vertical padding.** Minimum ~16px horizontal.
- The classic optical check: **at minimum, an uppercase "W" should fit between the label and each edge.**
- **Height 44–72px** covers touch-friendly and visually balanced. 40px is the common desktop compact size.
- **Size should follow from padding and type, not be set directly** – a height that comes from content is one that survives translation and text scaling.
- Carbon ships seven sizes; most systems need three.

**Inputs:**
- **Match the button height** – commonly 40px – so a field and its adjacent button align. Mismatched input/button heights are one of the most visible "off" details in a form.
- Anatomy: container, optional icons, label, placeholder, helper/error text. Small/medium/large.
- Padding must keep text away from the edges; cramped fields measurably reduce completion.
- **`field-sizing: content` with min/max constraints** (§11.8) is the modern approach for content-driven sizing.

### 14.29 Variable-length content

**The rule underneath all of this: design for the longest realistic string, not the one in the mockup.**

- **Text expansion in translation: German averages 20–35%, and can exceed that.** Plan for **25–30% for European languages and 35%+ for German.** Fixed-width buttons and precise spacing break first.
- **Remove strict minimum widths; use flexible containers with min and max bounds** so layouts adapt rather than clip.
- **Truncation:** `text-overflow: ellipsis` requires `white-space: nowrap` and `overflow: hidden`. Multi-line via `-webkit-line-clamp` (non-standard but universally supported).
- **Truncation rules:** at least **four characters** of real content before the ellipsis, and **always a title/tooltip carrying the full string.**
- The important caveat: **truncation is a design smell.** Appropriate where the text is a preview (email list, notification body); a failure where the truncated content is the thing the user needs to distinguish items by. If every row truncates at the same point, the list is useless.

### 14.30 Numbers and dates

**Numbers:**
- **Abbreviate large numbers** – 1.2K, 3.4M, 1.1B – in dashboards and stat tiles; **never in a financial or legal context** where the exact figure is the content.
- Currency: most use two decimals (some, like the Tunisian dinar, use three). **No space between symbol and amount** in en-US/en-GB; this varies by locale. **Minus goes before the currency symbol.**
- **Separators are locale-dependent** – French uses a comma decimal and a space for thousands. Never hardcode; use `Intl.NumberFormat`.
- `tabular-nums` on anything aligned or updating (§1.10).

**Dates and times:**
- **Relative for recency, absolute for reference.** *"Accuracy isn't important with relative timestamps, but immediacy is."*
- The working ladder: **< 1 min → "Just now"; < 1 day → "x minutes/hours ago"; 1–2 days → "Yesterday at h:mm"; beyond → "MMM d, YYYY at h:mm TZ".**
- **"52 weeks ago" is useless** – fall back to a real date past a threshold.
- **Always absolute for anything actionable or consequential** – expiry, deadlines, certificates, keys, legal dates. If the user must act before a time, show the time.
- Best of both: relative text with the absolute date in the `title` attribute (and in a `<time datetime>` element).

### 14.31 Verification tooling

- **Responsive:** Chrome DevTools device emulation for the loop; **Responsively App** to see many viewports simultaneously; real devices via BrowserStack before launch. *"Emulators cannot reproduce touch, real network, real GPU or real font rendering"* (§4.9).
- **Visual regression:** Percy (CI screenshot diffing), Chromatic (Storybook components), Applitools Eyes, Playwright's built-in visual comparison (free), BackstopJS (free). The 2026 shift is **from pixel diffing to semantic/AI comparison**, which cuts the false-positive noise that killed adoption of the pixel-based generation.
- **Contrast:** APCA calculator, `apca-w3`, Bridge-PCA (§2.13).
- **Accessibility:** axe/Lighthouse as a floor (**30–40% coverage**, §8.10), then manual keyboard, zoom and screen reader passes.

### 14.32 An honest read on AI design tools

Worth holding, because this skill *is* one:

**What they do well:** fast exploration, polished raster output from short prompts, plausible states, near-instant iteration, and covering the boring surface area (empty and error states, variants) that gets skipped by hand.

**What they do badly, per practitioners:**
- **"Generated output ignores your design system until you clean it."**
- No structural knowledge – it can show the wall removed without knowing whether it was load-bearing.
- Colors are approximations, not guarantees.
- Typography generation lacks the precision of real typesetting.
- And the systemic one from §12.1: **they converge on the statistical average.**

**"The most effective setup isn't one AI tool at all – it's a small stack of specialized tools connected to the workflow the team already uses."**

`CONSOLIDATE – this is the design brief for the skill itself:`

1. **Work inside the user's design system**, not around it. Read the tokens that exist before proposing new ones.
2. **Commit to a direction** rather than averaging (§12.1, §14.15, §14.26).
3. **Verify rather than assert** – contrast measured, 320px checked, tab order walked, squint test run (§5.10).
4. **Say what was traded** (§14.20).
5. **Cover the boring surface area properly** – all five states, every breakpoint, the error copy – because that is genuinely where machine thoroughness beats human patience.

### 14.33 Neurodivergent users

The framing that reorganizes the whole area: **"a shift from designing only for task completion to designing for emotional comfort, mental energy conservation, and autonomy."**

Four requirements named by the research: **predictability, sensory control, task segmentation, language clarity.**

- **Predictable navigation** – benefits autistic users and every first-time user.
- **All video and audio off by default.** Not autoplay-muted – off.
- **Task segmentation** – multi-step over one wall of work (§7.23).
- **Language clarity** (§8.8).
- **User control over the sensory environment**: adjustable text size, contrast, background color, motion – and *easy to find*, not buried.
- Readable type and high contrast help dyslexic users **and** anyone in low light – the recurring pattern where an accommodation is simply better design.

### 14.34 People scan; they do not read

- **Nielsen 1997: 79% scan, 16% read word by word.** *"Consistently found since 1997 – a fundamental truth of online information-seeking that has not changed."*
- **But**: *"users switch from scanning to reading when the content helps them focus on sections of interest"*, and people read thoroughly when reading for pleasure.
- So the design job is not to accept scanning, it is to **make scanning productive enough that reading becomes worth starting.** Headings, front-loaded paragraphs, short blocks, meaningful subheads, visible structure.

**Banner blindness** is the same mechanism turned hostile: *"people direct attention only to stimuli related to their goals"* and have learned that ad-shaped things are void of information.

- The critical design implication is not about ads: **anything that *looks* like an ad gets ignored**, including your own content. A boxed, right-rail, brightly colored promo will be skipped even when it is the most useful thing on the page.
- Measured cost: **ads increase perceived workload, hinder visual search, and are forgotten** – and *both animated and static banners slow visual search.* Ad-shaped chrome damages the page around it.
- The fix is to make important content **look like content**, in the reading column, in the page's own voice.

### 14.35 Hero headline length

`CONFLICT, resolvable.`

- Most practitioner sources: **6–10 words**; *"beyond 10, visitors skim and miss the point."*
- Some research finds longer headlines (12–17, even 21–28 words) achieve higher click-through.
- The reconciling finding: **"higher headline complexity decreases engagement"** – so it is **complexity, not length**, that does the damage. A long simple sentence can outperform a short dense one.
- Simpler headlines have greater **stickiness** – better recall and retention.
- **Subheads of 10–20 words convert 14% better than one-liners and 22% better than long paragraphs.**
- Unbounce: **−18.6% correlation between word count and conversion** overall.
- **The headline is the highest-variance element on a landing page** – tests on it produce significant winners ~24% of the time.

Working position: **aim for 6–10 words, optimize for clarity over brevity, and never let compression create ambiguity.** *"Clarity is not boring – clarity converts."*

### 14.36 Designing AI products

**Uncertainty is a design problem, not a data science one.** *"The confidence signal already exists inside the model; the work is deciding how, when, and to whom it gets communicated."*

- **Language matters enormously:** *"'AI is unsure' reads as a failure. 'Limited data available for this recommendation' reads as useful context that keeps the user in control."* Same information, opposite effect on trust.
- **Disclosure builds trust rather than costing it:** *"an upfront 'created with AI' note framed as craft reads as honesty."* Users want to know when they are talking to AI, how data is used, and how to reach a human.
- Trust measurably affects loyalty, conversion, complaint rates and willingness to engage at all.

**Agentic UI – when to interrupt the user.** A principled interruption policy weighs four things:

| Factor | Rule |
|---|---|
| **Reversibility** | Irreversible actions **always** warrant approval |
| **Scope** | Actions affecting external systems warrant more scrutiny |
| **Confidence** | Low confidence → ask for clarification |
| **Cost** | High-cost actions warrant approval |

This is §7.6's friction-proportional-to-damage rule generalized, and it is the correct model.

Three approval patterns: **pre-execution approval** (pause before each consequential action), **post-execution review** (act, then surface for inspection), **escalation triggers** (autonomous until a risk signal). Plus **plan-and-execute** – show the proposed plan first and let the user approve, modify or remove steps. The design goal: *"meaningful human oversight without creating a bottleneck that negates the efficiency benefit."*

**Generative UI.** Gartner: **30% of new apps will use AI-driven adaptive interfaces by 2026**, up from under 5% two years earlier. Three levels: **static** (the agent picks from predefined components), **declarative** (the agent returns a UI spec the frontend renders), **open-ended** (the agent returns a full surface).

And the line that matters most here:

> **"GenUI without a strong design system is chaos. The AI needs constraints – what components exist, how they compose, what states they support, and what 'on-brand' means – so every generated screen feels like it belongs to the same product."**

`CONSOLIDATE:` that is the argument for everything in this document. A constrained system is what lets generation produce something coherent. **The spec block is not paperwork before the design – it is the thing that makes generated work good.**

### 14.37 404 and error pages

- **74% of users who hit a 404 with no recovery path leave and never return.** `[secondhand]` That number reframes the 404 from a joke opportunity to a retention surface.
- Required: a **clear message**, full **navigation**, **helpful links**, and ideally a **search field** – *"users who landed on a broken link often know what they're looking for."*
- **Brand consistency** – it should feel like the same product, not a stripped system page.
- Keep it short. *"Concise, apologetic copy and a search input are more than enough."*
- Match the tone to the brand (§13.4). A playful 404 on a serious product is worse than a plain one.

### 14.38 Inclusive imagery

- **Take a portfolio view.** *"It is unrealistic to expect a single photo to show the diversity of your audience."* Coherence and representation are both properties of the **set**, which is the same finding as art direction (§13.8).
- **Vary multiple characteristics independently.** *"When you show Black people, don't always show the same skin tone, hair texture and body size."* Repeating one combination is a stereotype with extra steps.
- **Show marginalized people in active, strong roles** – not defaulting to those who historically held power.
- **The question to ask: who is missing or excluded?** Then check whether that absence is a pattern.
- Involve people with different perspectives in choosing and editing the images.
- The stake, stated plainly: **"When people don't see themselves in images, they feel invisible – and may even perform worse on tasks."**

### 14.39 Breaking the rules

The condition, and it is the whole thing:

> **"Rule-breaking only works when it is deliberate. Random inconsistency reads as an accident, not confidence."**

- **Know the rules first.** A broken grid needs a grid (§12.13); a violated scale needs a scale.
- Break for a **named gain** – *"better flow, more character, a space that feels human rather than well-behaved."* If you cannot name what the break buys, it is a mistake.
- Legitimate reasons: memorability, brand expression, cutting through convention, communicating something the convention cannot.
- **The tell of an accident vs a decision is consistency of the deviation.** One element off the grid reads as intent; five elements off by different amounts reads as no grid at all.

`CONSOLIDATE:` this is the counterweight the skill needs so it does not become a rule-enforcement machine. Every rule in this document has a condition under which breaking it is correct – and the skill should be able to say *which rule it is breaking, why, and what it buys.* That sentence is the difference between a design and a template.

### 14.40 Content first, and real content only

- **Lorem ipsum divorces layout from meaning.** *"Designers create layouts using placeholder text that look beautiful in presentations, but real content doesn't cooperate."*
- Real headlines are **longer** and real copy is **messier** than placeholder. Retrofitting real content into a layout designed around fake content causes late-stage rework.
- **Content-first costs more early collaboration and usually shortens total time-to-launch**, because the big disconnects never happen.
- With placeholder text, clients and users *"can't see much past the layout"* – so reviews become about aesthetics rather than communication.

**Design with real data, including the ugly cases:**

- Extremely long names, names with diacritics and non-Latin scripts, single-character names, missing values, zero states, enormous numbers, negative numbers, very old and very future dates.
- **Analyze logs and real records to find the actual edge cases** rather than inventing them.
- Character limits should allow reasonable variation, and every field should support diverse character sets.

`CONSOLIDATE:` this is directly actionable for this skill. **Every demo it builds should use plausible real content and include at least one deliberately awkward record** – the longest name, the empty field, the six-figure number. A design that only works with the tidy example is not finished, and building with tidy examples is exactly how AI-generated UI ends up brittle.

### 14.41 Five-second test

- **Opinion forms in ~50ms; a decision to stay takes ~5–7 seconds.**
- Method: show the design briefly, then ask what they remember and what they think it is for.
- **What it exposes: gaps in clarity, messaging problems, unclear hierarchy, and elements that get overlooked entirely.**
- 15–20 participants for statistical usefulness – more than the 5 needed for usability issues (§14.9), because first impressions vary more.

`CONSOLIDATE:` this is the human version of the squint test (§5.10). The skill can run the squint test itself; it should **name the five-second test as the thing to do next** when the question is "does this communicate", because that is a question no amount of self-review answers.

### 14.42 Mobile-first as a CSS discipline

Distinct from mobile-first as a *design* strategy (§4.10):

- **Base styles outside any media query target the smallest screen; `min-width` queries add complexity upward.**
- The benefit is not philosophical: **mobile devices download and apply only what they need**, and **if the CSS partially fails to load, the small-screen layout is the fallback** – which is the correct fallback.
- Breakpoints from content, not devices (§4.1).
- Even when the *design* is desktop-first, the *CSS* should usually still be mobile-first. They are separate decisions.

**Progressive enhancement** is the same idea at system scale, and it remains current: content and core functionality first, enhancements layered for capable browsers. It aligns with performance practice (critical rendering path, lazy loading, async JS) and with accessibility. This is what `@supports` guards (§11.1) implement in practice.

### 14.43 Touch-first consequences

- **No preview before commitment.** Hover lets a mouse user rehearse; touch commits immediately. Everything a hover state was communicating has to be communicated statically or after the fact.
- **≥8px between touch targets reduces misclicks by over 40%** – spacing, again, as a separate requirement from size (§4.4).
- Public/large touchscreens are a third context: no hover, imprecise touch, varying distance, casual exploration rather than task focus.
- **Never let critical functionality depend on hover.** Hover remains a legitimate desktop enhancement – it just cannot carry meaning.

### 14.44 Brand vs conversion

The tension stated honestly: *"The CTA color that converts best might not match your brand palette; the form layout that lifts leads might go against your design system."*

- Brand builds loyalty, emotional connection and differentiation – slow, compounding, hard to measure.
- CRO is immediate and quantifiable – and optimizes toward local maxima, which is how everything converges on the same page (§12.1).
- **The resolution worth keeping: "start CRO with UX optimization, because it is least likely to conflict with the brand."** Clarity, speed, hierarchy, error handling and friction removal lift conversion *without* touching identity. Only when those are exhausted does the trade become real.
- Align design and marketing from day one rather than arbitrating later.

`CONSOLIDATE:` when the skill is asked to "make it convert better", the first pass should be **UX debt, not brand compromise** – and it should say so.

### 14.45 Onboarding checklists

- **3–5 tasks** leading to the activation moment.
- **The endowed progress effect: pre-check item one.** *"Users who see '1 of 5 complete' are more likely to continue than users who see '0 of 5'."* Free, and one of the most reliably replicated effects in this area.
- **Zeigarnik effect**: a visible progress bar creates the pull to finish an incomplete task.
- Reported: gamified checklists drive **~50% higher completion**; one platform saw **+40% activation**, another **+47% activation with feature engagement +83%**. `[secondhand, vendor-published – directional only]`
- The caution the sources also raise: gamification **backfires in B2B when it feels condescending.** Progress and clarity travel well; badges, streaks and confetti do not always (§6.6).

## 15. Brand corpus findings

Per-brand and cross-brand patterns from the 74 analyzed sites. What the best actually ship, where they contradict the articles.

Source: 74 analyzed brand systems, 40,372 lines, at `~/Downloads/design-md`. Each file carries a design thesis, full token set (colors, typography, radius, spacing, components), a **"Don't" rule list**, responsive behavior, and known gaps.

**449 "Don't" rules across 74 brands.** That corpus of prohibitions is the highest-signal artifact in the whole study – it is what practitioners actually enforce, stated negatively, which is where the real rules live.

### 15.1 The single-voltage law

**25 of the 449 rules explicitly forbid introducing a second accent color.** The phrase "single brand voltage" / "single brand accent" / "single chromatic accent" recurs across at least 20 unrelated brands.

> "Don't introduce a second accent color; every 'click me' signal is Action Blue." – Apple
> "Don't introduce a second brand color. The system has exactly one accent and any expansion dilutes the brand identity." – Binance
> "Don't introduce a secondary brand color. Coinbase Blue is the only action color; trading green/red are semantic-only." – Coinbase
> "Don't introduce any accent color outside `{colors.link}`. Bugatti's brand discipline is total monochrome + photography." – Bugatti

**This is the most consistent finding in the entire corpus.** One accent, used scarcely, on the actions that matter. Semantic colors (success/error/price-direction) are explicitly *not* accents – they are a separate, non-brand vocabulary.

`CONSOLIDATE:` this belongs near the top of the skill. It contradicts the instinct to build a rich palette, and it is what separates these systems from the generic multi-accent template.

### 15.2 The shadow finding – genuinely counterintuitive

**54 rules mention shadow, and the overwhelming majority say do not use one.**

> "Don't add shadows to cards, buttons, or text – shadow is reserved for product imagery." – Apple
> "Don't add drop shadows to cards – depth comes from photo + color-block contrast." – BMW
> "Don't add drop shadows. Hairlines + ink-on-cream contrast carry the depth."
> "Don't add drop shadow tiers. Photography + brightness-step elevation carry the depth."
> "Don't add drop shadows on dark; surface lift carries elevation."
> "Don't apply heavy shadows on white cards; flat-with-borders is the documentation default."

`MYTH:` elevation requires shadow. **The best-executed systems get depth from, in order of frequency: surface color contrast, hairline borders, brightness-step elevation, photography, and color-block juxtaposition.** Where a shadow exists at all it is usually **one tier**, and often reserved for a single specific role (Apple: product photography only).

This reframes §13.2 – layered shadows are the right technique *when you use a shadow*, but the more important finding is **how rarely these systems use one at all.** "Don't add drop shadow tiers" appears repeatedly as its own rule: having *multiple* elevation levels is itself treated as the error.

### 15.3 Display weight is not bold

21 rules govern weight, and they run in both directions – but the dominant direction is **against bold**:

> "Don't bold display-weight type… going to 700 reads as **marketing-page-template**." – Airtable
> "Don't bold serif display weight. Copernicus at 700 reads as **bombastic**." – Claude
> "Don't bold display weight beyond 500. Plain Black at 700 reads as bombastic." – Clay
> "Don't bold any type. The system has no bold weight – every typeface stays at 400." – Bugatti
> "Don't bold body type. Body stays at 300 (Light)… bumping to 400 or 500 makes the page feel marketing-bombastic instead of **European-engineered**." – BMW M

Observed display weights across the corpus: **300 (Elevenlabs Waldenburg Light, PlayStation SST, BMW Light, Shopify Neue Haas thin), 400 (Apple body, Coinbase display, Cursor display, Claude serif), 500 (Airbnb, Intercom Saans, Linear, Webflow), 600 (Apple display, Expo), 700 (Binance, BMW corporate, Wise 900).**

**Heavy display is the exception, not the default** – and where it appears it is a deliberate voice (Wise at weight 900, Vodafone at 800, Nike Futura caps). The generic instinct to set headlines at 700 is exactly what these systems name as the tell of a template.

Two related rules worth keeping:

- **Deliberate gaps in the weight ladder.** Apple: "300 / 400 / 600 / 700, with **500 deliberately absent**." A weight ladder with a hole in it is a designed ladder.
- **Hierarchy from size, not weight gradation** – "the hierarchy depends on size, not on weight gradation" (Figma), and "Don't introduce mid-gray text. Body hierarchy comes from weight, not from opacity."

### 15.4 Surface rhythm – never two the same in a row

A rule stated almost verbatim by at least six unrelated brands:

> "**Don't repeat the same surface mode in two consecutive bands.** Two whites in a row read as a typography blog." – Airtable
> "Rhythm: photo band → spec table → photo band → magazine grid → photo band. Two text-only bands in a row read as a corporate site." – BMW M
> "light → dark hero → light → light feature → dark CTA → light footer rotation is required." – BMW
> "cream → cream-card → dark-mockup → cream → coral-callout → dark-footer" – Claude
> "Black canvas → dark feature card → yellow CTA card → black canvas → code-window card" – ClickHouse

**Page rhythm is a designed alternation of surface modes.** This is the mechanism behind long-scroll pages that stay interesting without decoration, and it is almost never discussed in the general literature. It is a *pacing* device, closer to editorial than to UI.

### 15.5 No decorative gradients

29 rules concern gradients, most forbidding them as background decoration:

> "Don't use gradients as decorative backgrounds; **atmosphere comes from photography**." – Apple
> "Don't add a gradient backdrop to the hero. Airtable's hero is white, full stop. **Mesh, aurora, spotlight gradients all read as 'another SaaS template'**." – Airtable
> "Don't add atmospheric gradients to the canvas (mesh, aurora, glow effects). Binance trusts color-block contrast." – Binance
> "Don't put gradient backdrops behind hero type. The hero IS the photography." – BMW M

Where gradients *do* appear they are a deliberate brand signature, not filler: Stripe's mesh in the upper third, Vercel's multi-color hero mesh as "the entire decorative system", Mistral's sunset stripe closing every page, Framer's oversized spotlight panels. **The distinction is signature vs. wallpaper.**

Cross-reference with §12.1: the blue→purple mesh gradient is *the* AI-slop signature. This corpus independently confirms it – the good systems either avoid it or make it unmistakably theirs.

### 15.6 Radius is a grammar, not a value

**69 rules concern radius** – the largest single category. The pattern is not "use X px" but **"use a consistent grammar and do not mix":**

> "Don't mix radii grammars – `{rounded.sm}` for compact utility, `{rounded.lg}` for utility cards, `{rounded.pill}` for pills, and **nothing in between**." – Apple
> "Don't use `{rounded.pill}` outside the pricing surface. It's a **sub-system signal**, not a general radius option." – Airtable
> "Don't use rounded buttons. The **rectangular silhouette IS the brand**. Rounded corners read as consumer-tech, not motorsport." – BMW M
> "Don't use rounded corners outside buttons. Cards, photos, inputs all stay at 0px. Rounded cards read as consumer-tech, not luxury-engineered." – Bugatti

Radius carries genre meaning: **0px reads engineered/luxury/institutional (Bugatti, BMW M, IBM 0–4px, NVIDIA 2px); 8–16px reads modern software; full pill reads friendly/consumer (Airbnb, Uber 999px, Ollama, Pinterest).** Choosing a radius is choosing a genre, and it is one of the fastest ways to shift a design's register.

### 15.7 Canvas polarity is a brand decision, and cream is common

- **63 of 74 systems reference near-black or pure black surfaces.** Dark is not a mode, it is a primary canvas for a large share of these brands.
- **23 of 74 use a cream or warm-tinted canvas rather than pure white** – Cursor `#f7f7f4`, Claude cream, opencode `#fdfcfc`, Zapier `#fffefb`, PostHog, Replicate, Warp, Elevenlabs `#f5f5f5`, Shopify's cream-mint transactional track. Several state it as law: **"Don't use cool grays or pure white for canvas. Cream is the brand."**
- Multiple brands run **dual-polarity systems** with an explicit split: Binance (dark marketing / light transactional), Shopify (near-black narrative / cream-mint transactional), MongoDB (dark hero / white docs), NVIDIA (black hero and footer / white body), Sentry, Mintlify.

`CONSOLIDATE:` "white canvas" is a default, not a decision. **Warm-tinting the canvas by a few percent is one of the cheapest ways to make an interface feel considered** – and nearly a third of this corpus does it.

### 15.8 Hover is barely documented

Only 5 rules mention hover, and several say the same thing:

> "Don't add hover state styling beyond what the system already encodes. The system documents **Default and Active/Pressed only**." – Airtable
> "Never document hover. Default and Active/Pressed states only." – Apple iteration guide

These are marketing surfaces built mobile-first, where **press matters and hover does not exist.** It reinforces §6.16: hover is an enhancement layer, and the *press* state is the one carrying the feedback load.

### 15.9 Section rhythm and layout constants

- **Section rhythm clusters at 80px and 96px** vertical, stated as a token and applied uniformly ("every page in the set uses `{spacing...}`").
- Apple's content locks at **1440px** with margins absorbing overflow; structural breakpoints at 1440 / 1068 / 833 / 734 / 640 / 480.
- Apple's touch targets: primary button ~44×100, icon button exactly 44×44, and desktop-only nav utility links deliberately smaller (~32×80) **because they are replaced by a hamburger below 833px.** A documented, reasoned exception rather than a violation.

### 15.10 Cross-brand genre signatures

| Genre | Signature |
|---|---|
| **Luxury automotive** (Bugatti, Ferrari, BMW M, Lamborghini) | Near-black canvas, full-bleed photography as the only voltage, uppercase letterspaced display, 0px radius, no accent color at all, light type weights, 120px rhythm |
| **Developer tools** (Linear, Vercel, Raycast, Warp, opencode, Voltagent, Supabase) | Near-black or warm-cream canvas, one saturated accent used scarcely, hairline borders, monospace for code, product-UI screenshots as the hero content, no shadows |
| **Fintech** (Stripe, Coinbase, Wise, Revolut, Binance) | Single strong voltage, tabular figures where money appears, dual light/dark tracks split by marketing vs transactional |
| **Consumer marketplace** (Airbnb, Pinterest, Uber) | Pill radius everywhere, photography-led, one warm brand color, friendly modest weights |
| **AI brands** (Claude, Cursor, Mistral, Cohere, ElevenLabs, Replicate) | **Warm/cream canvases and editorial type as deliberate counter-positioning** – Claude's file states it explicitly: *"deliberately warm and humanist where most AI brands use cool blue + slate"* |
| **Enterprise** (IBM, HP, NVIDIA, HashiCorp) | Square or near-square corners, one assertive blue or green, light display weights, thin-bordered tiles, no shadow |

### 15.11 The meta-lesson

Reading 74 systems back to back, the thing that separates them from generic work is not richness – it is **the number of things they refuse to do.** Every one of these brands is defined by a short list of materials used consistently and a long list of prohibitions. 449 "don'ts" against 74 "dos".

That is the inverse of how design is usually taught, and it is the single most important structural idea to carry into the skill: **a design system is a set of constraints with a reason attached to each one.** The reasons in this corpus are almost always of the form *"X reads as Y"* – which is exactly the vocabulary a design partner needs in order to explain a decision rather than assert it.

`[source: ~/Downloads/design-md – 74 brand design analyses, 40,372 lines, read in full for theses and prohibition rules]`

### 15.12 The emphasis escalation ladder – a genuine cross-brand law

Nearly every brand file ends its guidance with a "when in doubt about emphasis" rule, and they all say **the same thing in different vocabularies**:

> "When in doubt about emphasis: **bigger type before bolder type**, signature surface card before solid accent."
> "When in doubt about emphasis: **bigger Cal Sans before bolder Cal Sans**."
> "When in doubt about emphasis: **bigger Copernicus serif before bolder weight**."
> "When in doubt about emphasis: **bigger Inter 700 before adding color**."
> "When in doubt about emphasis: **bigger photography before bigger type**."
> "When in doubt about emphasis: **alternate surface (light → dark tile) before adding chrome**."

**The ladder, in ascending order of cost:**

1. **Scale** – make it bigger.
2. **Imagery** – let the photograph carry it (in photography-led systems, this comes *before* type).
3. **Surface change** – alternate the canvas rather than decorate the element.
4. **Weight** – go bolder.
5. **Color** – introduce the accent.
6. **Chrome** – add a border, shadow, badge, container.

**Always climb from the top.** Reaching for color or chrome when scale would have done it is the most common cause of a busy interface. This is a directly actionable rule and it belongs in the skill verbatim.

It also confirms §15.3 from the other direction: *bolder* is treated as more expensive than *bigger*, which is the opposite of most people's instinct.

### 15.13 The "reads as" vocabulary

The corpus consistently justifies rules with the construction **"X reads as Y"** – naming the *genre signal* rather than asserting a preference. Collected vocabulary:

| Move | Reads as |
|---|---|
| Display weight 700 where 400–500 belongs | *"marketing-page-template"*, *"bombastic"*, *"design-portfolio"* |
| Mesh / aurora / spotlight gradient behind a hero | *"another SaaS template"* |
| Rounded corners in an automotive or luxury system | *"consumer-tech, not motorsport"*, *"consumer-tech, not luxury-engineered"* |
| Squared buttons in a pill-based system | *"third-party widget"* |
| Two same-mode bands in a row | *"a typography blog"*, *"a corporate site"* |
| Body weight 400–500 where 300 belongs | *"marketing-bombastic instead of European-engineered"* |
| Tight interior + large gaps reversed | *"off-rhythm"* |
| Adding a second accent | *"off-brand"* |

**This is the vocabulary a design partner needs.** It converts "I don't like it" into "this reads as X, and we want Y" – which is arguable, teachable, and correctable. The skill should critique in this register.

### 15.14 Two more universal maintenance rules

Appearing across 13+ and 10+ brand files respectively:

- **"Use token references everywhere – never inline hex."** Not a style preference; it is what makes the system re-themeable and auditable (§2.5, §14.11).
- **"Never document hover. Default and Active/Pressed states only."** (§15.8) Ten independent restatements.

And one about structure: **"Add new variants as separate component entries (`-pressed`, `-disabled`, `-focused`) – do not bury them inside prose."** States are first-class objects, not footnotes.

### 15.15 Breakpoint reality check

Across the corpus the recurring explicit breakpoints are **≥1440px (11 mentions), ≥1280px (8), ≥1024px (6), ≤425px (4)** – plus a long tail of odd, content-derived values (833, 1136, 1300, 1360, 1400, 1441, 1500).

Two conclusions:

1. **The content-max-width lock is the most consistently used breakpoint of all** – 1440px or 1280px, with margins absorbing everything beyond. Nobody lets a marketing layout grow indefinitely.
2. **The long tail confirms §4.1** – real breakpoints land on odd values because they come from where the content actually breaks, not from a device list.

Apple's documented collapsing strategy is the model worth copying: **each element declares what it does at each step** – nav collapses to hamburger at 834px, tiles 2-col → 1-col at 834px, vertical padding 80px → 48px at small phone, hero type 56 → 40 → 34 → 28px. Responsive behavior specified per component, not left to the grid.

---

## MYTH register

Received wisdom that is wrong. Belief, reality, evidence.

| Belief | Reality | Evidence |
|---|---|---|
| Dyslexia-specific fonts help dyslexic readers | **They do not.** Ordinary good typography, turned up, is what helps | Peer-reviewed studies find no improvement from Dyslexie/OpenDyslexic over Arial or Times. Rello & Baeza-Yates; §1.14 |
| WCAG 2 contrast ratios reflect readability | The formula **ignores spatial frequency**, a primary driver of contrast perception. Tested on random color pairs it **wrongly passed 49%** | Myndex / Adrian Roselli; §2.3 |
| Elevation requires shadow | The best-executed systems mostly **do not use shadows at all** – depth comes from surface contrast, hairlines, brightness steps and photography | 54 of 449 corpus rules concern shadow, overwhelmingly forbidding it; §15.2 |
| Bold display type reads as confident | It reads as **"marketing-page-template"** and **"bombastic"**. Observed display weights cluster 300–500 | 21 corpus weight rules; §15.3 |
| Elements should be mathematically centered | Mathematical centering is not **visual** centering. Balance the mass, not the bounding box | §3.6 |
| Every site needs a 12-column grid | A relic of the float/framework era. Intrinsic layout sizes to content in two dimensions | Jen Simmons; §3.8 |
| "White space is wasted space" | White space improves comprehension (though it may reduce reading *speed*) – an honest trade, not a free win | Wichita State; §3.5 |
| The 60-30-10 rule produces good palettes | No research basis. What survives is **dominance hierarchy**; three clashing colors clash at any ratio | §2.10 |
| Blue means trust | Methodologically weak. **Saturation may matter more than hue**, and context inverts it (green beat blue on an outdoor site) | §2.9 |
| F-pattern is how people read, so lay out to it | The F-pattern is a **symptom of poor formatting** – people F-scan when nothing helps them. Strong hierarchy defeats it | §5.5 |
| Golden ratio produces better proportions | Ratios of 1.5–1.75 test comparably. *"It is entirely possible to create a poor UX design that conforms to the golden ratio"* | §5.6 |
| Confirm every destructive action | A blanket "Are you sure?" **trains users to click through**, so the one that mattered gets dismissed. Effectiveness depends on rarity | NN/g; §7.6 |
| Placeholders can replace labels | **Increased errors and completion time across every demographic tested** | NN/g; §7.10 |
| More ARIA means more accessible | Pages using ARIA averaged **59.1 errors vs 42 without**. "No ARIA is better than bad ARIA" | WebAIM Million 2026; §8.3 |
| Accessibility overlays provide protection | They do not work and **increase** legal exposure – 25% of US accessibility lawsuits targeted sites using them | Overlay Fact Sheet; UsableNet 2024; §8.7 |
| Automated tools verify accessibility | They catch roughly **30–40%** of violations | §8.10 |
| The fold is dead, people scroll | ~57% of viewing time is above the fold and attention **drops sharply** at the fold. It is a gate, not a container | NN/g; §5.3 |
| Cards are a safe default for lists | For **homogeneous, comparable** items a card grid actively harms scanning – users jump card to card instead of reading down | §5.8 |
| Skeleton screens improve perceived speed | **Contested.** One study found them *worst* on perceived duration | Viget 2017 vs. multiple pro findings; §6.7 |
| `opt`-style minimalism is universally good | Rams-style minimalism is a specific Western 20th-century position; a 2025 *Design and Culture* paper argues for moving beyond it | §14.10 |
| Icon-only UI is cleaner | *"No research has ever shown labeled icons to be less scannable than unlabeled ones."* Outlook's icon-only toolbar was fixed by adding labels | §8.16 |
| Zebra striping improves table readability | No significant speed gain; slight accuracy gain; **misleads in small tables**. Row hover often better | Enders 2008; §7.22 |
| Real-time validation is more helpful | Premature validation causes **error blindness** and a reported **9–16% conversion drop** vs on-blur | §7.4 |
| `llms.txt` improves Google visibility | **Google states it ignores it** and that it "will neither harm nor help" visibility in Search | §10.11 |
| Minimalism is the safe modern choice | Minimalism is now itself listed among the overused; the correction is *intentionality*, not less ornament | §14.17 |
| Fluid `clamp()` type is always the right default | A **pure-`vw`** preferred value **breaks browser zoom** – a WCAG 1.4.4 failure. Always include a `rem` term | §1.2 |

## CONFLICT register

Open disagreements between sources, unresolved.

| Question | Source A | Source B | Status |
|---|---|---|---|
| Do skeletons beat spinners? | 20–30% faster perceived; best emotional rating | Viget 2017: skeletons **worst** on perceived duration | **Unresolved.** Working position: skeletons for short, predictable waits in familiar layouts; progress for long or unpredictable ones (§6.7) |
| Long or short line length? | Reading **rate rises** with longer lines (7.3in fastest measured) | Users **prefer** ~4in; 50–75 CPL is the standard | Speed and preference diverge. Follow preference for chosen reading, accept longer lines for instructed processing (§1.3) |
| Hero headline length? | 6–10 words; beyond that people skim | Some research: 12–17 or even 21–28 words get higher CTR | Resolved: **complexity, not length**, is what costs engagement (§14.35) |
| Is Inter acceptable? | fde bans it as slop; it is an AI-slop signature | It is a genuinely excellent screen face with a large x-height | Resolved: **the failure is using it as a default rather than as a decision** (§12.1) |
| Is dark mode better? | Large benefit for light-sensitive users | **Harder for astigmatism**; CVD users vary | Genuinely split population → the override must exist (§7.38) |
| Inter's x-height ratio | Reported ~0.53–0.55 of cap height | Also reported as "one of the highest x-heights" | Numbers inconsistent across sources; treat "generous x-height" as reliable, the exact ratio as unverified (§1.7) |
| Is Liquid Glass good design? | Apple: hierarchy through depth, platform-native | Widely criticized for text legibility over refracting backdrops | Open. Note the contrast risk and test against real backdrops (§9.1) |
| Baseline grid on the web? | Produces calm, professional pages | Fights responsive content; returns collapse fast | Aim for it in body content, let peripheral elements break it (§1.11) |

## Trade-off register

Forks with no universally correct answer. Both costs stated.

| Fork | Buys | Costs |
|---|---|---|
| **Density vs comfort** | More visible at once; fewer navigations for power users | Comprehension, error rate, calm. **Resolution: ship a density toggle** when the audience splits (§4.13) |
| **Mobile-first vs desktop-first** | Forced prioritization; matches majority traffic | A weak desktop experience for dense, comparison-heavy tools (§4.10) |
| **System fonts vs custom type** | Zero load, OS-tuned rendering, respects user font settings | No brand. Middle path: system body + one custom display face (§1.13) |
| **Variable font vs static subsets** | One file, full axis range | Only wins **above ~3 weights**; below that two static subsets are smaller (§1.12) |
| **`font-display: swap` vs `optional`** | Brand type always eventually applies | `optional` guarantees **zero CLS** but may never show your typeface that visit (§1.12) |
| **Animation vs restraint** | Meaning, continuity, delight, perceived quality | Every animation is a delay on repeat use; *"experienced dozens of times a day, any delay is friction"* (§14.7) |
| **Springs vs bezier** | Physical plausibility, interruptibility, life | Unpredictable duration; wrong for color and opacity (§6.3) |
| **Undo vs confirmation** | No friction in the 99% case | Requires reversibility; and infrequent tasks may need the prompt (§7.6) |
| **Optimistic UI vs honest pending** | Instant perceived response | Rollback is a design problem, and a wrong optimistic state erodes trust (§6.15) |
| **Modal vs page** | Focus, context preserved, cheap | Blocks reference to the background – fatal if the user needs it (§5.12) |
| **Infinite scroll vs pagination** | Engagement, exploration | Findability, position, footer access, indexing. **"Load more" is the tested compromise** (§7.34) |
| **AVIF vs WebP** | 20–30% smaller at equal quality; HDR and wide gamut | 5–10× slower encode; ~93% vs ~97% support (§10.4) |
| **Wide gamut (P3)** | Real vibrancy in saturated colors and categorical data | Nothing at all for muted or pastel palettes (§2.7) |
| **3D / video / heavy media** | Depth, spectacle, differentiation | LCP, INP, CLS, battery, and a mobile experience that may not survive it (§12.8) |
| **Glass / blur** | Depth, current platform feel, content visible beneath chrome | **Dynamic contrast risk** plus the most expensive filter to render (§12.3, §8.17) |
| **Brand expression vs conversion** | Differentiation, loyalty, memory | Local conversion maxima. Start CRO with UX debt, which conflicts least (§14.44) |
| **Design system vs bespoke** | Consistency, speed, coherence at scale | Sameness. **"Custom where it counts"** (§12.7) |
| **Utility CSS (Tailwind) vs authored CSS** | Speed, enforced scale, no naming, small bundle | Readability in review; and native CSS now covers much of the original need (§13.10) |
| **Gestures vs visible controls** | Speed and screen space for experts | Discoverability, and total inaccessibility to some users (§6.26) |
| **Multi-step vs single-page forms** | ~86% higher conversion above ~5 fields | Uses the sunk-cost mechanism – honest only with visible progress (§7.23) |
| **Truncation vs wrapping** | Predictable layout, scannable rows | Hides the distinguishing information. A design smell where the text is the differentiator (§14.29) |
| **Custom cursor / magnetic UI** | Distinctive, tactile, memorable | Disorients motor-impaired users, breaks expectations, dead on touch (§12.12) |
| **Autosave vs explicit save** | No lost work, no chore | Violates the expectation that forms do not commit until Save (§7.27) |

## Verification register

Things that can be proven rather than asserted, and how.

| Claim | How to check |
|---|---|
| Hierarchy is real | **Squint / blur test.** What still stands out is what users see first. If it is not what you intended, hierarchy is wrong (§5.10) |
| Hierarchy is not carried by color alone | **Grayscale the design.** Heading, body and caption must remain distinguishable (§1.9) |
| Reflow works | **1280px viewport at 400% zoom.** Nothing may require scrolling in two directions (WCAG 1.4.10, §4.6) |
| Text scales | **Zoom text to 200%** with no loss of content or function (WCAG 1.4.4). Catches pure-`vw` fluid type (§1.2) |
| Focus is usable | **Tab the entire page.** Always visible, sensible order, never trapped, never hidden behind a sticky header (WCAG 2.4.11, §8.1) |
| Text contrast passes | Measure, do not eyeball. 4.5:1 body / 3:1 large. Then check APCA: **Lc 90 body, 75 minimum** (§2.13) |
| Non-text contrast passes | **3:1 for control boundaries, states, focus rings and meaningful icons** at the lowest-contrast point (§8.15) |
| Grouping reads correctly | **Internal padding ≤ external gap** for every group. Mechanically checkable from the CSS (§3.2) |
| Nested corners are right | `inner radius = outer radius − padding` (§3.7) |
| Icons are optically centered | Crosshair through the container; the icon's **mass** balanced, not its box (§3.6) |
| Numbers do not jitter | `font-variant-numeric: tabular-nums` on every aligned or updating figure (§1.10) |
| Motion respects preference | Toggle `prefers-reduced-motion` – durations go to `0.01ms`, not `none` (§6.10) |
| Animation is compositor-only | Only `transform`, `opacity`, `filter`, `clip-path`. Anything else is jank (§6.9) |
| The LCP element is not animated in | Check that the largest element is not fading from `opacity: 0` (§6.30) |
| Layout is stable | Every image, iframe, ad slot and embed has reserved dimensions; fonts metric-matched (§10.3) |
| Long strings do not break it | Test the longest realistic name, a 6-figure number, an empty value, and a non-Latin script (§14.29, §14.40) |
| It survives translation | Assume **+30–35%** text length. Fixed-width buttons break first (§14.29) |
| Visual order matches DOM order | Tab through a layout that uses `order` or dense grid placement (§8.12) |
| Hover has a focus equivalent | Every `:hover` rule has a matching `:focus-visible` (§7.1) |
| Touch targets pass | ≥24px (WCAG 2.5.8) / 44–48px recommended, **and ≥8px apart** (§4.4) |
| Inputs do not trigger iOS zoom | Every input ≥16px (§4.9) |
| Flex/grid children can shrink | `min-width: 0` where overflow appears (§4.9) |
| Static frames hold up | **Disable all animation. Does the composition still look intentional?** (§12.11) |
| It is not slop | Check against the signature list: Inter default, blue→purple gradient, three feature cards, 1px gray borders, centered hero + floating screenshot (§12.1) |
| Palette is differentiated | Audit direct competitors. **If three or more share your dominant color family, the palette is a liability** (§2.14) |
| It communicates | **Five-second test**, 15–20 people. The one thing self-review cannot answer (§14.41) |
| It is actually usable | Five users find ~85% of usability issues; three is enough to start (§14.9) |

## Open questions for consolidation

| Question | Status |
|---|---|
| Explicit activation only (`use skill: design`) or auto-trip on clear UI design tasks? | open, settle at consolidation |

## Sources

| # | Source | Type | Used for |
|---|---|---|---|
| – | **300 web searches**, September 2026 | Primary method | All sections; individual sources cited inline at the end of each |
| – | **`~/Downloads/design-md`** – 74 brand design analyses, 40,372 lines | Corpus | §15 in full; cross-checks throughout |
| 1 | W3C – WCAG 2.2, WAI-ARIA APG, CSS specs, i18n drafts | Spec | §4, §8, §9.5 |
| 2 | MDN | Reference | §6, §8, §10, §11 |
| 3 | Nielsen Norman Group | Research | §5, §6, §7, §14 |
| 4 | Baymard Institute | Research | §7.10, §7.36, §12.9 |
| 5 | WebAIM Million 2026 | Data | §8.3 |
| 6 | Apple HIG + WWDC "Designing Fluid Interfaces" | Platform | §6.4, §9.1 |
| 7 | Material Design 3 / M3 Expressive + Google Design research | Platform | §6.2, §9.2 |
| 8 | Radix Colors | System | §2.11 |
| 9 | Josh W. Comeau | Practitioner | §6.3, §6.20, §6.21, §13.2 |
| 10 | Emil Kowalski / animations.dev | Practitioner | §6.12, §6.22 |
| 11 | Smashing Magazine | Practitioner | §1.11, §3.9, §7.4, §7.5, §8.11 |
| 12 | Chrome for Developers / web.dev | Platform | §6.8, §10, §11 |
| 13 | Evil Martians, Adrian Roselli, Myndex, Sarah Higley, Eric Bailey, Lea Verou, Nathan Curtis, Jen Simmons | Practitioner | §2, §8, §12, §13 |
| 14 | Rello & Baeza-Yates, Wichita State, Stanford Web Credibility, MIT Touch Lab, Hoober (2013) | Academic | §1.14, §3.5, §4.4, §4.12, §7.20 |
| 15 | Dan Saffer, *Microinteractions* (2013) | Book | §6.5 |
| 16 | Overlay Fact Sheet, UsableNet, AudioEye, FTC/GDPR/CPRA guidance, CNIL | Legal/advocacy | §7.21, §8.7, §8.19 |
| 17 | 925studios / Sailop / kompozy – AI slop analyses | Analysis | §12.1 |
| 18 | State of CSS 2026, Can I Use / Baseline | Data | §11.1 |
