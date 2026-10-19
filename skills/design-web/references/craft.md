# Craft and micro-details

> **Craft is not about endlessly polishing every pixel – it is about building the judgment to know when details matter and when they do not.**

**Craft is allocation, not thoroughness.** Uniform polish is as much a failure as no polish: it spends the same effort on the thing nobody looks at as on the thing everyone does.

## Shadows – why most look fake

**One shadow is always wrong.** Real objects cast complex shadows whose softness varies with distance. **A single `box-shadow` cannot produce that falloff.**

**Layer three or more**, each with increasing offset and blur and **decreasing** opacity:

| Layer | Job |
|---|---|
| **Umbra** – tight, small offset, low blur | Defines the **contact edge**, where the object meets the surface |
| **Penumbra** – mid offset, mid blur | The main body of the shadow |
| **Ambient** – large offset, large blur, very low opacity | Scattered ambient light. The widest and faintest |

- **Offset and blur must stay in proportion.** A 40 px blur with a 2 px offset **looks physically impossible**, and this single mismatch is the most common cause of a shadow reading as fake.
- **One light source for the entire interface**, consistently. Any consistent choice works; inconsistency does not.
- **Never a neutral black shadow on a colored surface** – it goes muddy. **Match the shadow's hue to the environment.** A shadow on a warm surface is a warm shadow.
- **Elevation is a scale, not a set of one-offs:** 2–3 defined levels, each a defined layer stack, used consistently.
- **Dark mode: shadows barely read.** Use surface lightness for elevation. See `color.md`.

**The corpus goes further, and it is the more important finding: 54 of 449 prohibition rules concern shadow, overwhelmingly forbidding it.** Layered shadows are the right technique *when you use a shadow* – but **the best-executed systems mostly do not use one at all**, and "don't add drop shadow tiers" appears repeatedly as its own rule. See `brand-corpus.md`.

## Icons

- **One grid, usually 24 × 24 with 2 px padding** – a 20 × 20 live area. Everything is drawn inside it.
- **One stroke weight across the set.** 2 px for a bolder style, 1–1.5 px for a lighter one. **Mixed weights in one set is instantly visible and reads as careless.**
- **Optical size beats mathematical size:** a circle of 20 px diameter visually matches an 18 × 18 px square. **Circles and diagonals must be drawn larger than squares to look the same size.**
- **Pixel-grid alignment:** with 1 px strokes, **shift shapes by 0.5 px** so the stroke lands on the pixel grid instead of straddling it and blurring under anti-aliasing.
- Consistent corner radius across the set – commonly 2 px on a 24 px grid – and consistent terminal style.
- **Align strokes to the grid. Do not draw lines between gridlines.**

Labeling rules and the icon-only evidence are in `accessibility.md`.

## Microcopy

**The button-label rule, and it is the best single line in this area:**

> **Name the outcome, in the first person of the user's intent.**

| Weak | Strong |
|---|---|
| Submit | Start my free trial |
| Confirm | Send invite |
| OK | Delete 3 reports |
| Next | Create account |

A fintech onboarding step renaming "Next" to "Create account" plus a short hint about terms **increased completion 14%.** Secondhand. **The mechanism is that "Next" hides the consequence, and users hesitate at consequences they cannot see.**

### The three shapes

Most microcopy is one of three:

- **Button: verb + outcome** – "Send invite", "Delete 3 reports".
- **Error: what / why / next** – "This email is already registered. Would you like to sign in instead?"
- **Empty state: what / payoff / action** – "You haven't added any projects yet" → "Create your first project".

### Voice and tone

- **Voice is constant; tone varies with context.** Same personality throughout, but the tone shifts for an error, a celebration and a destructive confirmation.
- **Clarity beats personality**, and specifically in high-stakes and high-friction moments, **drop the voice and be plain.** Nobody wants a joke in a payment failure.
- **Tone at failure points – the error, the limit, the "you can't do that" – is what users remember.** A product that explains problems without blaming the person reading **earns forgiveness for the next one.**
- **Microcopy is design, not content written afterward.** Labels, hints, empty states, errors, confirmations, tooltips: all of it is part of the interface.

## Messaging

**Headline** (one attention-grabbing sentence) → **subheadline** (what you do, for whom, why it is useful) → **bullets** → **image**.

- **8 out of 10 people read only the headline.** That statistic should govern how much attention the headline gets relative to everything else on the page.
- Working pattern: **"We help [audience] get [outcome] without [pain]."**
- Or: **who you help, what problem you solve, why it is better** – under **25 words**, no jargon.
- Two or three short sentences maximum, and **every word must improve clarity or sharpen the differentiation.**
- **The headline speaks to what the visitor wants, not what you sell.**

**Treat headline and subhead as design elements you are responsible for, not placeholder text. A perfectly composed hero with a vague headline is a failed hero.**

Headline length and complexity findings are in `typography.md`.

## The amateur tells

The fastest read on why something looks wrong.

| Tell | Fix |
|---|---|
| Too many colors – everything competing | 2–4 core colors, each serving hierarchy rather than decoration |
| **More than three typefaces** | One or two. A third only with a reason |
| No visual hierarchy – everything the same weight | Establish a clear first, second and third read |
| Broken alignment and inconsistent spacing | **If something feels off, it is usually spacing or alignment. Check those first, always** |
| Default software effects – stock drop shadows, bevels | Custom, layered, intentional |
| Inconsistency across screens | **Consistency is the most visible marker of professional work** |
| Overused texture and pattern | Restraint |

The reassuring finding: **the answer is less about raw skill than most assume.** Plenty of people with modest technical ability produce work that looks polished and intentional. **Polish is systematic, not innate** – which is exactly why it can be encoded.

## The small details

Each is invisible when present and quietly cheapening when absent. **That is the definition of craft.**

- **Layered shadows** over a single shadow.
- **Immediate active state** – a color dip or slight scale-down **before** any data loads. The sub-100 ms feedback rule expressed as a detail.
- **`::selection`** styled to the brand instead of default browser blue. One line, and almost nobody does it.
- **`caret-color`** matched to the accent in inputs.
- **`scrollbar-width` and `scrollbar-color`** – standardized, replacing the never-standardized `::-webkit-scrollbar-*`. `thin` in dense UI; `none` hides the bar without disabling scrolling, **legitimate for internal carousels, never for a main scroll region.**
- **`text-box-trim`** to remove the font's built-in leading so optical padding matches declared padding.
- **`tabular-nums`** on anything numeric that updates or aligns.
- **Nested radius** by the `outer − padding` formula.
- **Optical alignment** of icons in containers.
- **`accent-color`** on native controls.
- Thumb-zone placement for anything frequently tapped.

## Art direction

### Photography

- The art director's job at selection is **not to choose the most beautiful image, but the one that best embodies the brief.**
- **Consistency over volume:** a smaller set of images that feels like one visual world beats twenty impressive but unrelated photographs. **The single most useful rule for a project without a shoot budget.**
- Consistent **color grading, lighting and depth of field** so the images read as a set. Check that lighting warmth and contrast match and that tones complement the palette.
- **Write it down:** crop rules, when full-bleed works, which treatments apply.

**The luxury and automotive brands in the corpus use photography as the entire voltage – no accent color at all.** That only works when the images are genuinely a set.

### Illustration

- An illustration system documents **line weight, corner treatment, fill style, and color use.** Four variables, and **locking them is what makes work by different people look like one hand.**
- The purpose is a shared visual language across mediums, not decoration.
- Same discipline as icons: one grid, one weight, one vocabulary.

### Inclusive imagery

Match the people in the imagery to the actual audience rather than to a stock idea of one, and check that the set does not accidentally encode a single age, ability or body type. It is the same "consistency as a set" discipline applied to who appears in it.

## Logos in a digital system

- **A responsive logo system: full lockup, simplified mark, icon-only.** **Not three sizes of one file – three *drawings* at different complexities.**
- The sophisticated version is called a **morph-mark** – a mark that alters its complexity by environment, detailed on a large display, reduced to a high-contrast geometric form for a watch notification or favicon.
- **Over 70% of brand interactions now happen on mobile**, so **the small-scale rendering is the primary case, not the fallback.**
- **The favicon test at 16 px is the same test.** A mark that fails there needs a simplified variant, not a smaller export.

## Assets that are always forgotten

### Favicon and app icons

Ship four files: `favicon.ico` (multi-size 16/32/48), `icon.svg`, `apple-touch-icon.png` (180 × 180), `icon-512.png`. Android additionally uses 192 × 192 and 512 × 512.

- **SVG favicons support dark mode** via a `prefers-color-scheme` media query **inside the SVG**. This matters more than it sounds: **a black favicon disappears on a dark browser tab, and a white one disappears on a light tab. Almost every site gets this wrong.**
- SVG support is Chrome, Firefox and Edge, so **keep the ICO as fallback. SVG primary, ICO plus PNG fallback.**
- **Test at 16 × 16 and 32 × 32 before exporting anything.** Fine lines collapse. **If the mark is not legible at 16 px it is the wrong mark for a favicon** – simplify, or use a fragment of it.

### Open Graph and social preview

**1200 × 630, 1.91:1** covers Facebook, X large card, LinkedIn, Slack, Discord, WhatsApp and iMessage.

- Keep headline and faces inside a **center 1080 × 600 safe zone** – platforms crop differently.
- Under **1 MB**, and **under 300 KB for WhatsApp**. JPEG or PNG for compatibility.
- **No ranking effect, real click-through effect.** It is **the only design most people will ever see of a page they do not visit.**

### 404 and error pages

- **74% of users who hit a 404 with no recovery path leave and never return.** Secondhand. **That number reframes the 404 from a joke opportunity to a retention surface.**
- Required: a **clear message**, full **navigation**, **helpful links**, and ideally a **search field** – users who landed on a broken link often know what they are looking for.
- **Brand consistency.** It should feel like the same product, not a stripped system page.
- **Keep it short.** Concise, apologetic copy and a search input are more than enough.
- **Match the tone to the brand. A playful 404 on a serious product is worse than a plain one.**

## Design systems – the toolchain and where it breaks

**The stack has settled:** Figma Variables → Tokens Studio exporting **DTCG-format JSON** → **Style Dictionary** → CSS custom properties, Sass, JS constants, iOS, Android.

- **Theming is the payoff for the three-tier token architecture: if the semantic layer is right, theming is a token swap.**
- **The real failure is drift.** A component in Figma and the same component in React can drift apart fast if there is no shared language. **Tokens are the fix because they make translation mechanical rather than interpretive.**
- **Component API is a design artifact, not an engineering one.** A Figma variant property should map exactly to a React prop – `Type=Primary, State=Default, Theme=Light` is a variant **and** a props combination. **Normalizing that API before production is what prevents the two diverging.**
- The component API is a **contract** – entry props plus behavior – **designed jointly, not handed over.**

**When this skill outputs a design, name tokens the way the system will consume them, so the output is a starting design system rather than a one-off page.**

### Documentation

Per component: **visual examples of every state and variant, usage guidelines (when *and when not* to use), anatomy and specs, code snippets, accessibility requirements, responsive behavior, and rationale.**

- **Usage guidelines are where you stop showing what a component does and start telling people when and how to use it. The "when not to" is the part that gets skipped and the part that prevents misuse.**
- **Interactive demos are reported to drive a ~90% increase in adoption.** Secondhand. Documentation you can poke beats documentation you read.
- Organize by principles, components, patterns, guidelines.
- **The failure mode is single-team ownership.** When only designers or only one team owns the docs, engineering and product needs are missed, and the result is inconsistency, resistance and workarounds.

## Tooling positions worth holding

**Tailwind (v4).** ~37% of developers; the most-used CSS framework. v4's engine and CSS-first config removed the performance and tooling criticisms; **the philosophical objection is untouched.** The sharpened 2026 critique is that **native CSS grew up** – cascade layers, nesting, container queries, `:has()` and custom properties now cover much of what utility classes were compensating for.

> **Position: fine either way; match the project. Do not introduce it into a project that does not have it, and do not fight it in a project that does.**

**Component primitives.** **shadcn/ui is the default starting point for new React projects in 2026** – not a headless library itself, but the most common way teams consume Radix or Base UI primitives. **As of July 2026 shadcn/ui defaults to Base UI for new projects.**

The framing that matters: **components as malleable source code rather than rigid external dependencies** – UI ecosystems replacing UI kits. And: **the debate is not styled versus unstyled, it is how much control you want.**

**Headless primitives handle keyboard navigation, focus management, ARIA semantics, RTL and controlled/uncontrolled state** – exactly the work most hand-built components get wrong.

**Figma.** 70%+ share among product designers. The relevant point here is different: **the deliverable in this skill is running HTML, not a Figma file – which skips the handoff-drift problem entirely and is a genuine advantage of designing in the browser.**
