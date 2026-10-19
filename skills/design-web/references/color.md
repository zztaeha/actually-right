# Color

## Why OKLCH, precisely

**HSL's `L` is geometric, not perceptual.** It describes a position in a mathematical cylinder, not brightness as the eye reports it. Two colors at identical HSL lightness look mismatched because perceived brightness varies by hue – yellow at L=50% is far brighter than blue at L=50%.

OKLCH gives three axes where **equal numeric distance is equal perceived distance**.

| Task | HSL | OKLCH |
|---|---|---|
| Brighten a button on hover | Brightening desaturates noticeably | Raise `L` alone; chroma is preserved |
| Raise contrast by pushing saturation | Also shifts perceived brightness, unpredictably | Axes are independent |
| One lightness ramp across many hues | Steps look uneven between hues | Steps look even |
| Going out of sRGB | Naive per-channel clipping shifts hue **and** lightness | The browser gamut-maps by reducing chroma at constant L and h – the correct algorithm, done for you |

**That last row is the underrated one: CSS gamut-maps OKLCH correctly and automatically.** No media query, no script.

## Building a scale

- Pick the brand color, convert to OKLCH, **hold `H` constant and vary `L` in perceptually even steps.** A linear ramp from **L≈0.97 (near white) to L≈0.15 (near black)** across 11 steps (50–950) is a working start.
- **Chroma cannot be constant across the ramp.** It must fall at both ends, because near-white and near-black cannot hold chroma. A bell curve peaking in the middle is the standard shape.
- Tailwind, Radix and Material all use the 50–950 tonal-scale idea. **Radix's contribution is that each step has a defined job**, so a designer picks by role rather than by eye.
- ColorBox exists specifically because naive lightness adjustment is not perceptually even.

## The Radix 12-step scale – the best system to steal from

| Step | Job |
|---|---|
| 1 | App background |
| 2 | Subtle background |
| 3 | UI component background – **normal** |
| 4 | UI component background – **hover** |
| 5 | UI component background – **pressed / selected** |
| 6 | Subtle border on **non-interactive** components: sidebars, headers, cards, separators |
| 7 | Border on **interactive** components; also focus rings |
| 8 | Border on interactive components, **hover** |
| 9 | **Solid.** The chroma peak – purest step, least mixed with white or black. Designed for white foreground text |
| 10 | Solid, hover |
| 11 | **Low-contrast text** |
| 12 | **High-contrast text** |

Two details worth carrying:

- **Steps 11 and 12 are guaranteed Lc 60 and Lc 90 APCA against a step-2 background of the same scale.** A color system with contrast guarantees baked into the step definitions **makes the accessible choice the default choice** rather than a later audit.
- **Step 9 is defined as the chroma peak**, which is why it is the brand and solid step. That gives the bell-shaped chroma curve a purpose rather than an aesthetic justification.

## Contrast: WCAG 2 vs APCA

| | WCAG 2.x | APCA |
|---|---|---|
| Model | Simple relative-luminance ratio | Lightness contrast `Lc`, 0 to ~±106 |
| Inputs | Two colors | Two colors **plus font size, weight and polarity** |
| Directional | No – dark-on-light and light-on-dark score identically | **Yes**, polarity-aware, which matches how the eye works |
| Thresholds | 4.5:1 normal, 3:1 large or bold | **Lc 90+ body, 75+ normal, 60+ large, 45+ UI** |
| Legal status | **The current standard. What the law recognizes** | Candidate method, not adopted |

**WCAG 2's formula ignores spatial frequency** – how small and how thin the strokes are – which is a *primary* driver of human contrast perception. Tested against thousands of random color pairs, **WCAG 2 incorrectly passed 49%** of pairs too low-contrast to actually read.

Status: visual contrast was **moved out of the WCAG 3 Working Draft in July 2023** and the WCAG 3 algorithm is still undetermined.

> **Ship against WCAG 2.2 because that is what compliance means. Check APCA because that is what readability means. Audit against both. Where they disagree – WCAG 2 passing, APCA failing – real users will struggle. Fix it anyway.**

### In practice

- **Lc 90+ on columns of body text; Lc 75 is the practical minimum** where readability matters.
- Tools: the official APCA calculator at `apcacontrast.com`, the `apca-w3` library for automation, and **Bridge-PCA**, which reports both an Lc value and a backwards-compatible WCAG 2 ratio – the practical bridge while WCAG 3 remains undecided.
- **Document APCA findings as contrast guidance inside the design system**, so it becomes repeatable rather than a per-screen argument.

**Do not choose between the two standards. Encode both into the token layer, the way Radix does.**

Non-text contrast (borders, states, focus rings, meaningful icons) is a separate 3:1 requirement. See `accessibility.md`.

## Dark mode

Not an inversion. A separate system.

| Rule | Value | Why |
|---|---|---|
| **Never pure black `#000`** | Base around `#121212`, L≈0.13–0.15 | Pure black maximizes contrast against everything, causing **halation** – light text blooms and edges vibrate. Worst for light-sensitive users and for astigmatism |
| **Never pure white text** | ~`#E8E8E8`, L≈0.88–0.93 | Same reason, other direction |
| **Elevation by surface lightness, not shadow** | Each layer up is a slightly lighter surface | Shadows are nearly invisible on dark ground. Lightness is the only depth cue that works |
| **Desaturate brand colors** | **Blues and cyans typically need 20–30% saturation reduction** | Saturated hues on dark ground produce optical vibration and eye strain |
| Recalibrate per color | Individually, not by formula | Each hue's vibration threshold differs |

**The elevation rule separates a real dark theme from an inverted one.** In light mode elevation reads as shadow; in dark mode elevation reads as **light**. Surfaces get lighter as they come forward, because in the physical metaphor they are closer to the light source.

**Dark mode is a genuinely split population** – a large benefit for light-sensitive users, harder for astigmatism, and variable for color vision differences. **The override must exist.** Toggle behavior is in `states.md`.

## Token architecture

Three tiers, and **the direction of reference is the whole rule: component → semantic → primitive.**

| Tier | Example | Rule |
|---|---|---|
| 1. Primitive | `--blue-500: oklch(0.64 0.147 255)` | Raw values, no meaning. **Never referenced by a component** |
| 2. Semantic | `--color-action-primary: var(--blue-500)` | Describes **intent**. This is what components consume |
| 3. Component | `--button-bg-primary: var(--color-action-primary)` | Per-component override slot |

Retheme the product by changing tier 2. Retheme one component by changing tier 3. **A component pointing straight at a primitive destroys both capabilities** – that is the violation to look for in any audit.

The corpus states the same rule as law: **use token references everywhere, never inline hex.** It is what makes a system re-themeable and auditable.

## Deriving colors instead of storing them

Relative color syntax `oklch(from var(--c) L C H)` sits around 89.6% support; `color-mix()` is Baseline Widely Available.

**The pattern that has aged best: keep the reference layer flat and derive variants in the semantic layer.** Token count stays small; hover, active, disabled and tinted states become infinite.

```css
--btn-hover:    oklch(from var(--btn-bg) calc(l - 0.1) c h);
--text-muted:   oklch(from var(--text) l calc(c * 0.3) h);
--focus-ring:   oklch(from var(--primary) 0.7 calc(c * 1.2) h / 0.5);
--dark-surface: oklch(from var(--surface) calc(1 - l) c h);
```

**The caution: derived values are only as good as the base, and a derivation chain three deep is unreadable and un-auditable.** Derive one step from a token, not four.

## Color vision deficiency

- **Blue, orange and dark blue are the safest hues across all major CVD types.** Blue is perceived reliably under deuteranomaly, protanomaly and most others.
- **The Okabe-Ito (Wong) 8-color set is the validated standard:** blue `#0072B2`, orange `#E69F00`, vermillion `#D55E00`, reddish purple `#CC79A7`, bluish green `#009E73`. Distinguishable under protanopia, deuteranopia and tritanopia.
- **Kill combinations:** red/green, green/brown, blue/purple, green/blue, light green/yellow. Red and green both read as brownish-yellow under deutan and protan vision.
- **Use teal, not green, for success.** Teal carries enough blue to stay separable from red under deuteranopia; plain green does not. **The single highest-value swap in most product palettes.**
- **Safe hues and sufficient contrast are two separate jobs.** Passing one does not imply the other.
- Where a risky pair is unavoidable, add an icon, a label, a pattern or a position difference. **Color is never the sole carrier of meaning.**

## Wide gamut

- P3 holds ~50% more colors than sRGB. **The benefit is concentrated entirely in highly saturated colors** – a pastel, muted or near-neutral palette gains essentially nothing.
- Where it pays: saturated brand accents, and **categorical data-viz colors**, where more perceptually distinct hues means easier category separation.
- **Always ship an sRGB declaration first, then the wide-gamut one.** Older browsers take the first, capable ones the second. `@media (color-gamut: p3)` detects hardware support.

## Gradients

- **CSS gradients interpolate in sRGB by default, and sRGB interpolation produces a muddy, desaturated band through the middle.** This is why so many gradients look grayish in the center and nobody can explain why.
- Fix: `linear-gradient(in oklch, ...)`. Support around 93%.
- **OKLab vs OKLCH for interpolation:** OKLab is cartesian and cuts a straight line, losing chroma in the middle for distant hues. OKLCH is polar and rotates through hue, keeping chroma. **For similar hues they are nearly identical; for distant hues use OKLCH.**
- **Banding is a separate problem from interpolation.** Large gradients band on 8-bit displays regardless of color space. Fix with a subtle noise or grain overlay, or angle the gradient so bands run diagonally and read as less regular.

**The corpus position on gradients is stricter than the technical one: 29 rules concern gradients, most forbidding them as background decoration.** Mesh, aurora and spotlight gradients behind a hero are named as "another SaaS template". A gradient is a **signature or it is wallpaper**. See `brand-corpus.md` and `style.md`.

## Choosing a brand color

**The competitive-audit method is the only genuinely useful thing in this area.**

- **Audit direct competitors before finalizing.** If **three or more use the same dominant color family** as your proposed scheme, **your palette is already a liability.** A hard, checkable test.
- Differentiate by shifting family (if the category is blue and gray, look at green or purple) or within family (mint instead of emerald).
- **The accent is where a conventional brand can break the category** – a finance brand keeping trust-blue as its base and using orange as its accent.
- Then apply dominance: one dominant, one supporting, one scarce accent.

**The question is not "what does blue mean" but "what is everyone else in this category already using".**

## Color psychology

"Blue means trust" is methodologically weak. What survives:

- **Cooler hues and lower saturation** correlate with higher reported trust – **saturation may matter more than hue**.
- **Context inverts it:** green tested as more trustworthy than blue on an outdoor-activity site. The association is contextual, not intrinsic.
- Field-wide methodological problems: inadequate color specification, underpowered samples, oversimplified stimuli, heavy moderation by culture, age, gender and personality.
- **Color supports trust; it cannot create it.** A badly designed product with a blue interface still feels unreliable.

**Treat color meaning as culturally and contextually contingent. Decide from brand and differentiation, and never justify a palette with a psychology claim you cannot source.**

## 60-30-10

Useful as a **lesson in dominance hierarchy**, misleading as a formula. It has no research basis and plenty of excellent work breaks it. What survives:

- One color should clearly dominate.
- **The accent should be genuinely scarce** – scarcity is what makes it read as an accent.
- **Three colors that clash still clash at any ratio.** The ratio does not create harmony.

**Keep the principle – one dominant, one supporting, one scarce accent – and drop the percentages.**

## Harmony schemes

The mechanism worth keeping: **when colors share a clear geometric relationship on the wheel, the eye reads them as a set rather than a collection of accidents.** Harmony buys coherence, not beauty.

| Scheme | Character | Best use |
|---|---|---|
| **Analogous** (3–5 adjacent) | Harmonious, low tension, easily monotonous | Most web design. Pair with one complementary accent for contrast |
| **Complementary** (opposite) | Maximum contrast and tension | A single decisive accent against a base |
| **Triadic** (three equidistant) | Vibrant, balanced, three **equally weighted** accents | **Data visualization, multi-category dashboards, tags, status systems** – where categories must be distinguishable and none should dominate |

**The triadic note is the useful one.** Most harmony advice targets brand palettes, but triadic's real strength is **categorical encoding**, where equal weight is a requirement rather than an aesthetic.

**Convert the scheme to OKLCH before using it.** Wheel relationships defined in HSL will not have even perceived lightness.
