# Hierarchy and composition

## Visual weight – the actual variables

Perceived weight is what makes a composition balanced or not. The factors compose:

| Factor | Effect |
|---|---|
| **Size** | Larger is heavier. The most direct lever |
| **Saturation and brightness** | Saturated and bright carry more weight than muted |
| **Color temperature** | **Warm advances, cool recedes.** Red is the heaviest hue, yellow the lightest |
| **Vertical position** | Elements **higher** in the composition read as heavier |
| **Distance from center** | **The further from center, the more weight** – a small element at the edge can balance a large one in the middle. This is the mechanism of asymmetric balance |
| **Density** | A dense cluster carries the weight of its area × its optical density |
| **Texture** | Textured reads heavier than flat; it implies mass |

**When a layout feels lopsided and the sizes look right, the cause is usually temperature or density, not size.** A saturated warm block will outweigh a physically larger cool one.

## Scale is the cheapest drama

A scale jump is powerful, and **a single large shape among smaller neighbors becomes a natural focal point.**

**The failure mode is timid scale** – everything within 1.5× of everything else. That produces a layout that is technically correct and completely inert.

**Real hierarchy usually needs a jump, not a step:** 3×, 4×, sometimes more between the dominant element and the supporting ones.

This is also the first rung of the corpus emphasis ladder: **scale before imagery before surface before weight before color before chrome.** See `brand-corpus.md`.

## The three cheap objective checks

These turn self-critique from assertion into observation. All three cost nothing.

| Check | Question | Catches |
|---|---|---|
| **Squint / blur test** | What still stands out when text and detail disappear? | Hierarchy that exists element-by-element but not as a composition |
| **Grayscale test** | Are heading, body and caption still distinguishable? | Hierarchy carried by color alone |
| **400% zoom at 1280 px** | Does anything require scrolling in two directions? | Reflow failures. See `layout.md` |

**If what survives the squint is not what you intended, the hierarchy is wrong regardless of how correct the individual elements are.**

## The fold, honestly

People do scroll, and the fold is still real.

- **~57% of viewing time is spent above the fold**, ~43% below.
- In **57,453 eye-tracking fixations** there is a dramatic drop-off in attention at the fold. **The 100 px just *above* the fold were viewed 102% more than the 100 px just below.**
- ClickTale, ~100,000 pageviews: the scrollbar was used on **76%** of pages; **22%** were scrolled all the way to the bottom regardless of length.

**The correct conclusion is neither "everything must be above the fold" nor "the fold is dead". The first view sets whether the rest gets read.** When the hero communicates value quickly, people scroll. **The fold is a gate, not a container.**

**Do not cram; do earn the scroll.** And place a visible affordance that content continues – **a partially visible next section is more effective than an arrow.**

## Scan patterns

F-pattern and Z-pattern come from real eye-tracking, but they are **descriptions of behavior on particular content types, not layout templates.**

- **F-pattern shows up on text-heavy, unstructured content** – articles, news homepages. It is largely a symptom of *poor* formatting: **users F-scan when nothing helps them.**
- Z-pattern applies to sparse, visually structured pages.
- The 2026 shift: users increasingly arrive with a **pre-formed intent**, often via an AI agent, and **scan for anchors of intent** rather than sweeping the page. Hierarchy becomes less about what you want to show and more about **making findable what they already came for.**

**Do not lay out to an F or a Z. Build a strong hierarchy and clear anchors, and the scan pattern takes care of itself.** A well-structured page defeats the F-pattern rather than obeying it.

### People scan, they do not read

- **Nielsen 1997: 79% scan, 16% read word by word.** Consistently found since, and unchanged.
- **But** users switch from scanning to reading when the content helps them focus on sections of interest, and people read thoroughly when reading for pleasure.
- **So the design job is not to accept scanning, it is to make scanning productive enough that reading becomes worth starting.** Headings, front-loaded paragraphs, short blocks, meaningful subheads, visible structure.

**Banner blindness is the same mechanism turned hostile.** People direct attention only to goal-related stimuli and have learned that ad-shaped things are void of information.

**The critical implication is not about ads: anything that *looks* like an ad gets ignored, including your own content.** A boxed, right-rail, brightly colored promo will be skipped even when it is the most useful thing on the page. **Ads measurably increase perceived workload, hinder visual search and are forgotten, and both animated and static banners slow visual search.**

**Make important content look like content** – in the reading column, in the page's own voice.

## Golden ratio and rule of thirds

- Fechner found 35% preference for golden rectangles, and a 2007 fMRI study found activation associated with phi proportions. **But comparable ratios of 1.5–1.75 perform similarly**, which undercuts any claim that phi specifically is special.
- **It is entirely possible to create a poor UX design that conforms to the golden ratio.**

**Keep phi as a generator for type scales and layout proportions, not a law.** Small UI elements do better on simpler ratios. **Never justify a decision by citing phi** – if it looks right it is right, and phi was a starting point.

## Hero structure

Four elements recur in nearly every high-converting hero:

1. **Headline** – carries the heaviest load. Speaks to **what the visitor wants, not what you sell.**
2. **Subheadline** – if the headline earns attention, the subheadline earns belief. Who it is for, what problem, what change.
3. **Visual** – usually the first impact, before anything is read.
4. **Primary CTA** – one, unmistakable.

The honest caveat: **there is no true blueprint.** Clarity and logical structure are the constants; the arrangement is not.

### Choosing the hero visual

| Visual | Use when |
|---|---|
| **Product screenshot or device mockup** | A digital product. Shows the actual thing, and the highest-credibility option because it cannot lie about what the product looks like |
| **Photography** | Physical product, people, place, or where mood carries the value. Show the outcome in the real world |
| **Illustration** | The value is abstract – infrastructure, security, a concept – and a literal image would be a stock-photo lie |
| **Nothing, type only** | Editorial, luxury, brutalist, or when the copy genuinely is the strongest asset |

**The corpus check is instructive: developer tools overwhelmingly show real product UI, luxury and automotive show photography, AI brands split between atmospheric imagery and type.** The choice is a genre signal, not just an asset decision.

## Cards, and when not to use them

Anatomy: **container** (the only required part), header, body, footer.

Rules: one task or idea per card; consistent action placement across cards; do not overload the body with competing actions; separate from the background with **either** a border **or** elevation, not both – and the corpus says usually the border.

**The anti-pattern is the important part:**

> **Never use a collection of cards when users need to scan a list or grid of homogeneous elements to compare one against another.** The card chrome adds space and visual noise; instead of linear top-to-bottom scanning, users have to **jump from card to card.**

**Cards are for heterogeneous, self-contained objects.** For homogeneous comparable rows – search results, pricing rows, data records, anything scanned to compare – **a list or table beats a card grid**, and the card grid actively harms the task.

**One of the most commonly violated rules in modern product design.**

## Page rhythm and pacing

Independently confirmed by the brand corpus and the editorial literature:

- **Alternate section types.** Text-heavy → visually rich → text-heavy. Alternating density maintains interest and prevents visual fatigue.
- **Image-text interlock** – text-left/image-right, then text-right/image-left – gives variety on one consistent grid.
- **Narrative pacing alternates high-impact visual sequences with restful sections**, so the reader absorbs between spectacles. **A page of continuous spectacle reads as noise.**

> **Rhythm is not about the gaps between content; it is about how the content prepares the reader for what comes next.**

**Rhythm is composition in time**, since a long page is consumed sequentially. The same skill as motion choreography, applied to scroll.

The corpus states it as law: **never repeat the same surface mode in two consecutive bands.** See `brand-corpus.md`.

## Full bleed

A **constrained content column with the ability for specific elements to break out** – to a wider band, or to the viewport edges.

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

One container controls every width. Children land in the content column by default; a class promotes an element to full width. Extend to five columns with named lines when a third, intermediate width is needed – common for images and figures in editorial layouts.

**Full-bleed moments are the strongest rhythm device available in a long page.** Alternating a constrained text column with an edge-to-edge image or color band is what stops a long page reading as a document.

## Navigation structure

| | Sidebar | Top bar |
|---|---|---|
| Best for | Many sections, deep hierarchy – dashboards, admin, project tools | **3–6 primary areas** |
| Screen cost | **~20%+ of width** | **~6%** |
| Scanning | **Vertical lists are found with fewer eye fixations than horizontal ones** – a real, measured advantage | Fewer items, so it matters less |
| Scaling | Grows gracefully; supports trees and folders | Breaks past ~7 items |

**Most mature products use both:** top bar for global context (search, account, notifications), sidebar for primary navigation. **The vertical-scanning advantage is the underrated finding** – it is why sidebars persist in dense tools despite the space cost.

**Validate structure with tree testing and card sorting, not opinion.**

### App shell and sidebars

Three desktop modes: **icon-collapse (rail), off-canvas hide/show, fixed non-collapsible.**

- Roles: **primary** anchors the app, **secondary** supports the main content, **temporary** appears on demand and leaves. **Decide the role first** – width, collapse behavior, keyboard model and responsive treatment all follow from it.
- **The icon rail is the best collapse behavior**, because navigation stays visible and reachable at both widths, unlike off-canvas which hides it entirely.
- **⌘B / Ctrl+B** is the established toggle shortcut.
- Mobile: the sidebar becomes a sheet.
- Collapsed-state accessibility: **labels visually hidden but still in the accessible name**, tooltips on the icons, a persistent labeled collapse control, and **focus must not be lost when collapsing.**

## Header

- **Logo top-left, and it should be the first tappable element in DOM order** – both convention and screen reader order.
- **Two-state sticky header is the cleanest pattern:** 80–100 px initial with full logo and nav, shrinking to **50–60 px after ~100 px of scroll**, transition **150–200 ms**. Under 60 px on mobile, or the content area suffers.
- **Hide-on-scroll-down, reveal-on-scroll-up.** Scrolling up *is* the signal that the user wants navigation. Gives back vertical space without hiding the nav.
- The 2025–26 mainstream pattern is **a centered, rounded navigation pill floating slightly off the top edge**. Lighter than a full-width bar, and worth knowing that it is *becoming* a default – see `style.md`.
- **A sticky header must not obscure a focused element** (WCAG 2.4.11).

## Footer

- **The footer's real job is recovery** – where the user goes when they could not find something above.
- Expected contents: navigation groups, legal, support and contact, trust links, social, optional newsletter.
- **Bold category headers, subdued link text.** Grouping is what makes a large footer scannable rather than a wall.
- **Extend the visual identity into it.** It is the last brand impression on every page and usually the least designed region on the site.
- Performance trap: **embedded social widgets.** Link out or lazy-load; never embed.

## Breadcrumbs

- Justified only when the hierarchy is **three or more levels deep** and moving up is otherwise a hassle. In a flat product they are noise.
- **Location-based** (position in the IA) is the default and right for content sites, ecommerce, docs and knowledge bases. **Path-based** (actual history) confuses more than it helps. Attribute-based is a filter UI wearing breadcrumb clothes.
- **On mobile, do not render the full trail.** A single link back to the parent level is the clean solution; a wrapped trail crowds the title and is hard to tap.
- **Breadcrumbs describe structure; the back button describes history.** Not substitutes, and shipping breadcrumbs does not remove the need for sensible back behavior.

## Modal vs page vs inline

| Choose | When |
|---|---|
| **Modal** | High-stakes, irreversible, user-initiated, and **short**. Critical instruction that must be read |
| **Separate page** | The task takes **more than ~30 seconds**, involves multiple steps, or requires **comparing data from other sources** |
| **Inline / expand** | The user needs more context **without leaving the current task** – accordions, disclosure, editable fields |

**The decisive question: does the user need to reference the background while acting?** If yes, a modal is actively working against them.

**A multi-step wizard in a modal is nearly always wrong** – it is a page pretending to be an interruption.

## Component proportions

**Buttons:**

- **Horizontal padding ≈ 2× vertical padding.** Minimum ~16 px horizontal.
- The classic optical check: **at minimum, an uppercase "W" should fit between the label and each edge.**
- **Height 44–72 px** covers touch-friendly and visually balanced; 40 px is the common desktop compact size.
- **Size should follow from padding and type, not be set directly.** A height that comes from content survives translation and text scaling.
- Carbon ships seven sizes; most systems need three.

**Inputs:**

- **Match the button height** – commonly 40 px – so a field and its adjacent button align. **Mismatched input and button heights are one of the most visible "off" details in a form.**
- Padding must keep text away from the edges; cramped fields measurably reduce completion.
- **`field-sizing: content`** with min and max constraints is the modern approach for content-driven sizing. See `modern-css.md`.

## Masonry

Native CSS masonry landed as **`display: grid-lanes`**. Safari shipped it first; support is **experimental and inconsistent across engines in early 2026**. Guard with `@supports`.

- **`grid-lanes` preserves DOM order** – a real accessibility win over JS masonry libraries, which routinely reorder.
- **The compatible fallback is better than most people realize:** `columns: 230px; break-inside: avoid;` is a **two-declaration Pinterest layout** that packs tighter than Masonry.js, with **zero JavaScript and no resize listeners.** Its limitation is column-major reading order.
- **Use masonry only when item heights genuinely vary** and equalizing them would crop or pad content badly. **A uniform card grid is almost always the better answer** – masonry's ragged bottom edge weakens the composition.

## Page-load choreography

- **Sequence, do not stack.** Keep simultaneous animations to a minimum and run complex ones in sequence: hero first, then supporting elements, then chrome.
- The default entrance that works nearly everywhere: **a fade with a small upward translate**, subtle enough for cards, modals and page content alike.
- **Never fade in the largest contentful element from `opacity: 0`.** It directly damages the LCP metric and the perceived speed. **Animate everything around it instead.**
- **Keep the whole opening sequence inside ~1 second.** A hero that takes two seconds to assemble reads as a slow site, not a considered one.
- Stagger: 50–100 ms, first item zero delay, cap at 5–6 elements. See `motion.md`.

## Scroll-triggered reveals

- **Animate one thing at a time.** The most successful examples animate only the text or only the images – where both fade in together they compete for attention.
- **Animate on first view only.** Re-animating on every scroll-past is the most irritating version of this pattern.
- **The illusion of completeness is the real danger.** If a section fades in only after it is reached and there is a lot of empty space between sections, **users conclude the page has ended.** Fixes: **reduce the gap between sections, and always leave part of the next section visible** – closure pulls the scroll.
- **Keep the distance small (8–16 px) and the duration short.** A reveal that travels far reads as a page still loading.
- Implement with CSS scroll-driven animations rather than IntersectionObserver where support allows – compositor-driven, no main-thread cost.
- **Honor `prefers-reduced-motion` by rendering everything visible immediately.**

## Search-as-you-type

- **Debounce ~300 ms.** If something visible happens within about a second, most users stay confident and keep typing.
- **Show the search state** – an inline spinner in the results area or a brief "Searching…". **If previous results stay on screen, label them as stale** rather than leaving the user comparing against the wrong list.
- **Cache by query key.** Repeated searches are common and caching is the cheapest route to "instant".
- **Three failure modes, all of which read as brokenness:** typing lag (cursor stutter), **flicker** (list clears then refills – keep the old list until the new one is ready), and **stale results** (out-of-order responses overwriting newer ones – always discard responses for superseded queries).
- **Never move focus out of the input while typing**, and never auto-select the first result in a way that hijacks Enter.
