# The brand corpus

**74 analyzed brand design systems, 40,372 lines, 449 "Don't" rules.**

Each source file carries a design thesis, a full token set, a **prohibition list**, responsive behavior, and known gaps.

**That corpus of prohibitions is the highest-signal artifact in this whole skill.** It is what practitioners actually enforce, stated negatively – **which is where the real rules live** – and it repeatedly contradicts article-derived advice.

> **Reading 74 systems back to back, the thing that separates them from generic work is not richness – it is the number of things they refuse to do.** Every one of these brands is defined by a short list of materials used consistently and a long list of prohibitions. **449 "don'ts" against 74 "dos".**

**That is the inverse of how design is usually taught: a design system is a set of constraints with a reason attached to each one.**

## The single-voltage law

**25 of the 449 rules explicitly forbid introducing a second accent color.** The phrase "single brand voltage" / "single brand accent" / "single chromatic accent" recurs across at least 20 unrelated brands.

> "Don't introduce a second accent color; every 'click me' signal is Action Blue." – Apple
> "Don't introduce a second brand color. The system has exactly one accent and any expansion dilutes the brand identity." – Binance
> "Don't introduce a secondary brand color. Coinbase Blue is the only action color; trading green/red are semantic-only." – Coinbase
> "Don't introduce any accent color outside `{colors.link}`. Bugatti's brand discipline is total monochrome + photography." – Bugatti

**The most consistent finding in the entire corpus. One accent, used scarcely, on the actions that matter.**

**Semantic colors – success, error, price direction – are explicitly *not* accents.** They are a separate, non-brand vocabulary.

This contradicts the instinct to build a rich palette, and **it is what separates these systems from the generic multi-accent template.**

## The shadow finding

**54 rules mention shadow, and the overwhelming majority say do not use one.**

> "Don't add shadows to cards, buttons, or text – shadow is reserved for product imagery." – Apple
> "Don't add drop shadows to cards – depth comes from photo + color-block contrast." – BMW
> "Don't add drop shadows. Hairlines + ink-on-cream contrast carry the depth."
> "Don't add drop shadow tiers. Photography + brightness-step elevation carry the depth."
> "Don't apply heavy shadows on white cards; flat-with-borders is the documentation default."

**Elevation does not require shadow.** The best-executed systems get depth from, **in order of frequency: surface color contrast, hairline borders, brightness-step elevation, photography, and color-block juxtaposition.**

**Where a shadow exists at all it is usually one tier**, and often reserved for a single specific role – Apple restricts it to product photography.

**"Don't add drop shadow tiers" appears repeatedly as its own rule: having *multiple* elevation levels is itself treated as the error.**

Layered shadows are the right technique *when you use a shadow* – but **the more important finding is how rarely these systems use one at all.** See `craft.md`.

## Display weight is not bold

**21 rules govern weight, and the dominant direction is against bold.**

> "Don't bold display-weight type… going to 700 reads as **marketing-page-template**." – Airtable
> "Don't bold serif display weight. Copernicus at 700 reads as **bombastic**." – Claude
> "Don't bold any type. The system has no bold weight – every typeface stays at 400." – Bugatti
> "Don't bold body type. Body stays at 300 (Light)… bumping to 400 or 500 makes the page feel marketing-bombastic instead of **European-engineered**." – BMW M

**Observed display weights across the corpus: 300** (Elevenlabs, PlayStation, BMW, Shopify), **400** (Apple body, Coinbase, Cursor, Claude serif), **500** (Airbnb, Intercom, Linear, Webflow), **600** (Apple display, Expo), **700** (Binance, BMW corporate, Wise 900).

**Heavy display is the exception, not the default** – and where it appears it is a deliberate voice (Wise at 900, Vodafone at 800, Nike Futura caps). **The generic instinct to set headlines at 700 is exactly what these systems name as the tell of a template.**

Two related rules:

- **Deliberate gaps in the weight ladder.** Apple: 300 / 400 / 600 / 700, **with 500 deliberately absent.** **A weight ladder with a hole in it is a designed ladder.**
- **Hierarchy from size, not weight gradation.** And: "Don't introduce mid-gray text. Body hierarchy comes from weight, not from opacity."

## Surface rhythm – never two the same in a row

**Stated almost verbatim by at least six unrelated brands.**

> "**Don't repeat the same surface mode in two consecutive bands.** Two whites in a row read as a typography blog." – Airtable
> "Rhythm: photo band → spec table → photo band → magazine grid → photo band. Two text-only bands in a row read as a corporate site." – BMW M
> "light → dark hero → light → light feature → dark CTA → light footer rotation is required." – BMW
> "cream → cream-card → dark-mockup → cream → coral-callout → dark-footer" – Claude
> "Black canvas → dark feature card → yellow CTA card → black canvas → code-window card" – ClickHouse

**Page rhythm is a designed alternation of surface modes.** This is the mechanism behind long-scroll pages that stay interesting **without decoration**, and **it is almost never discussed in the general literature.** It is a *pacing* device, closer to editorial than to UI.

## No decorative gradients

**29 rules concern gradients, most forbidding them as background decoration.**

> "Don't use gradients as decorative backgrounds; **atmosphere comes from photography**." – Apple
> "Don't add a gradient backdrop to the hero. Airtable's hero is white, full stop. **Mesh, aurora, spotlight gradients all read as 'another SaaS template'**." – Airtable
> "Don't add atmospheric gradients to the canvas. Binance trusts color-block contrast." – Binance
> "Don't put gradient backdrops behind hero type. The hero IS the photography." – BMW M

**Where gradients do appear they are a deliberate brand signature, not filler:** Stripe's mesh in the upper third, Vercel's multi-color hero mesh as the entire decorative system, Mistral's sunset stripe closing every page, Framer's oversized spotlight panels.

> **The distinction is signature vs wallpaper.**

**The blue-to-purple mesh gradient is *the* AI-slop signature, and this corpus independently confirms it** – the good systems either avoid it or make it unmistakably theirs.

## Radius is a grammar, not a value

**69 rules concern radius – the largest single category.** The pattern is not "use X px" but **use a consistent grammar and do not mix.**

> "Don't mix radii grammars – `{rounded.sm}` for compact utility, `{rounded.lg}` for utility cards, `{rounded.pill}` for pills, and **nothing in between**." – Apple
> "Don't use `{rounded.pill}` outside the pricing surface. It's a **sub-system signal**, not a general radius option." – Airtable
> "Don't use rounded buttons. The **rectangular silhouette IS the brand**. Rounded corners read as consumer-tech, not motorsport." – BMW M
> "Don't use rounded corners outside buttons. Cards, photos, inputs all stay at 0px. Rounded cards read as consumer-tech, not luxury-engineered." – Bugatti

**Radius carries genre meaning:**

| Radius | Reads as | Examples |
|---|---|---|
| **0 px** | Engineered, luxury, institutional | Bugatti, BMW M, IBM 0–4 px, NVIDIA 2 px |
| **8–16 px** | Modern software | Most SaaS |
| **Full pill** | Friendly, consumer | Airbnb, Uber 999 px, Ollama, Pinterest |

**Choosing a radius is choosing a genre, and it is one of the fastest ways to shift a design's register.**

## Canvas polarity is a decision, not a default

- **63 of 74 systems reference near-black or pure black surfaces.** **Dark is not a mode, it is a primary canvas for a large share of these brands.**
- **23 of 74 use a cream or warm-tinted canvas rather than pure white** – Cursor `#f7f7f4`, Claude cream, opencode `#fdfcfc`, Zapier `#fffefb`, PostHog, Replicate, Warp, Elevenlabs `#f5f5f5`. **Several state it as law:** "Don't use cool grays or pure white for canvas. Cream is the brand."
- Multiple brands run **dual-polarity systems** with an explicit split: Binance (dark marketing / light transactional), Shopify (near-black narrative / cream-mint transactional), MongoDB (dark hero / white docs), NVIDIA (black hero and footer / white body).

> **"White canvas" is a default, not a decision. Warm-tinting the canvas by a few percent is one of the cheapest ways to make an interface feel considered** – and nearly a third of this corpus does it.

## The emphasis escalation ladder

**Nearly every brand file ends its guidance with a "when in doubt about emphasis" rule, and they all say the same thing in different vocabularies.**

> "When in doubt about emphasis: **bigger type before bolder type**, signature surface card before solid accent."
> "When in doubt about emphasis: **bigger Cal Sans before bolder Cal Sans**."
> "When in doubt about emphasis: **bigger Copernicus serif before bolder weight**."
> "When in doubt about emphasis: **bigger Inter 700 before adding color**."
> "When in doubt about emphasis: **bigger photography before bigger type**."
> "When in doubt about emphasis: **alternate surface (light → dark tile) before adding chrome**."

**The ladder, in ascending order of cost:**

1. **Scale** – make it bigger.
2. **Imagery** – let the photograph carry it. In photography-led systems this comes *before* type.
3. **Surface change** – alternate the canvas rather than decorate the element.
4. **Weight** – go bolder.
5. **Color** – introduce the accent.
6. **Chrome** – add a border, shadow, badge, container.

> **Always climb from the top. Reaching for color or chrome when scale would have done it is the most common cause of a busy interface.**

**It also confirms the weight finding from the other direction: *bolder* is treated as more expensive than *bigger*, which is the opposite of most people's instinct.**

## The "reads as" vocabulary

**The corpus justifies rules with the construction "X reads as Y"** – naming the *genre signal* rather than asserting a preference.

| Move | Reads as |
|---|---|
| Display weight 700 where 400–500 belongs | *"marketing-page-template"*, *"bombastic"*, *"design-portfolio"* |
| Mesh, aurora or spotlight gradient behind a hero | *"another SaaS template"* |
| Rounded corners in an automotive or luxury system | *"consumer-tech, not motorsport"*, *"consumer-tech, not luxury-engineered"* |
| Squared buttons in a pill-based system | *"third-party widget"* |
| Two same-mode bands in a row | *"a typography blog"*, *"a corporate site"* |
| Body weight 400–500 where 300 belongs | *"marketing-bombastic instead of European-engineered"* |
| Tight interior and large gaps reversed | *"off-rhythm"* |
| Adding a second accent | *"off-brand"* |

> **This is the vocabulary a design partner needs.** It converts "I don't like it" into **"this reads as X, and we want Y"** – which is arguable, teachable and correctable.

**Critique in this register.** It is also the reason to ask, in the interview, what the product should **read as**: without a target Y there is nothing to argue from.

## Genre signatures

| Genre | Signature |
|---|---|
| **Luxury automotive** – Bugatti, Ferrari, BMW M, Lamborghini | Near-black canvas, **full-bleed photography as the only voltage**, uppercase letterspaced display, 0 px radius, **no accent color at all**, light type weights, 120 px rhythm |
| **Developer tools** – Linear, Vercel, Raycast, Warp, opencode, Supabase | Near-black or warm-cream canvas, **one saturated accent used scarcely**, hairline borders, monospace for code, **product-UI screenshots as the hero content, no shadows** |
| **Fintech** – Stripe, Coinbase, Wise, Revolut, Binance | Single strong voltage, **tabular figures where money appears**, dual light/dark tracks split by marketing vs transactional |
| **Consumer marketplace** – Airbnb, Pinterest, Uber | Pill radius everywhere, photography-led, one warm brand color, friendly modest weights |
| **AI brands** – Claude, Cursor, Mistral, Cohere, ElevenLabs, Replicate | **Warm and cream canvases and editorial type as deliberate counter-positioning.** Claude's file states it explicitly: *"deliberately warm and humanist where most AI brands use cool blue + slate"* |
| **Enterprise** – IBM, HP, NVIDIA, HashiCorp | Square or near-square corners, one assertive blue or green, light display weights, thin-bordered tiles, no shadow |

## Two universal maintenance rules

Appearing across 13+ and 10+ brand files respectively:

- **Use token references everywhere – never inline hex.** Not a style preference; **it is what makes the system re-themeable and auditable.**
- **Never document hover. Default and Active/Pressed states only.** Ten independent restatements, from systems built mobile-first **where press matters and hover does not exist.**

And one about structure: **add new variants as separate component entries** (`-pressed`, `-disabled`, `-focused`) – do not bury them inside prose. **States are first-class objects, not footnotes.**

## Hover is barely documented

**Only 5 rules mention hover**, and several say the same thing.

> "Don't add hover state styling beyond what the system already encodes. The system documents **Default and Active/Pressed only**." – Airtable
> "Never document hover. Default and Active/Pressed states only." – Apple iteration guide

**These are marketing surfaces built mobile-first, where press matters and hover does not exist.** It reinforces the general position: **hover is an enhancement layer, and the *press* state carries the feedback load.**

## Section rhythm and layout constants

- **Section rhythm clusters at 80 px and 96 px** vertical, **stated as a token and applied uniformly** – "every page in the set uses `{spacing…}`".
- Apple's content locks at **1440 px** with margins absorbing overflow; structural breakpoints at **1440 / 1068 / 833 / 734 / 640 / 480**.
- Apple's touch targets: primary button ~44 × 100, **icon button exactly 44 × 44**, and desktop-only nav utility links deliberately smaller (~32 × 80) **because they are replaced by a hamburger below 833 px.** **A documented, reasoned exception rather than a violation.**

## Breakpoint reality check

Recurring explicit breakpoints across the corpus: **≥1440 px (11 mentions), ≥1280 px (8), ≥1024 px (6), ≤425 px (4)** – **plus a long tail of odd, content-derived values** (833, 1136, 1300, 1360, 1400, 1441, 1500).

Two conclusions:

1. **The content-max-width lock is the most consistently used breakpoint of all** – 1440 px or 1280 px, with margins absorbing everything beyond. **Nobody lets a marketing layout grow indefinitely.**
2. **The long tail confirms that real breakpoints land on odd values because they come from where the content actually breaks**, not from a device list.

**Apple's documented collapsing strategy is the model worth copying: each element declares what it does at each step.** Nav collapses to hamburger at 834 px, tiles 2-col to 1-col at 834 px, vertical padding 80 → 48 px on small phones, hero type 56 → 40 → 34 → 28 px. **Responsive behavior specified per component, not left to the grid.**
