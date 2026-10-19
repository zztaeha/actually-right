# Performance as a design constraint

**Performance is not an engineering concern that happens after design. It is a design material.** A design that cannot load fast is a design that most people never see.

## Core Web Vitals

| Metric | Good | Measures |
|---|---|---|
| **LCP** | **under 2.5 s** | Loading – when the largest content element paints |
| **INP** | **under 200 ms** | Responsiveness – **the worst interaction across the entire visit** |
| **CLS** | **under 0.1** | Visual stability |

- Google grades at the **75th percentile over a 28-day rolling window** from field data. You pass only when **at least 75% of real page views hit "good". Lab numbers are not the score.**
- **INP is the hardest and most-failed vital: 43% of sites fail it.** It reports the **worst** interaction, not the average – **exactly the metric a designer should care about, because one janky interaction ruins the page's rating and the user's impression.**
- Sites passing all three see **24% lower bounce rates.** Secondhand.
- Only **42% of mobile sites pass all three**; 63% on desktop – while **mobile is 62% of ecommerce traffic.**

## The business case, with numbers

Have these ready. They move a stakeholder who will not be moved by craft arguments.

- **Every 100 ms of load time is roughly 1% of conversions.** A $10M/year ecommerce site recovers roughly **$500k** from a 500 ms improvement.
- **A one-second delay reduces conversions ~7%.**
- Each additional second in the 0–5 s range cuts conversions **~4.4%**; the best conversion rates cluster **under 2 seconds**.
- **Bounce rate rises toward 90% as mobile load goes from 1 s to 5 s**; up to **53% of visits abandoned** past 3 seconds.
- Average 2026 load: **2.5 s desktop, 8.6 s mobile. The mobile gap is where the money is.**

All secondhand aggregations. Directionally consistent across sources – cite as "widely reported", not as a single study.

## CLS is almost entirely a design problem

**Every cause is a design or markup decision.**

| Cause | Fix |
|---|---|
| Images and iframes with no dimensions | **`width` + `height` attributes.** The browser derives aspect-ratio and reserves the box. **Free, and the single biggest win** |
| Web font swap with different metrics | `font-display: swap` **plus a metric-matched fallback** (`size-adjust`, `ascent-override`), or `font-display: optional` |
| Injected content: ads, cookie banners, promo bars | **Reserve the space with `min-height` before it arrives.** If you cannot know the size, **do not inject above existing content** |
| Animating `top` / `left` / `width` / `height` | `transform` |
| Late third-party scripts | Reserve space; load them where they cannot push content |

**Four fixes resolve the vast majority of CLS:** image dimensions, reserved space for injected content, metric-matched font fallback, transform-based animation.

**Design implication: a cookie banner or promo bar that pushes the page down is not a nuisance, it is a measurable ranking and revenue defect.** Overlay it, or reserve its space.

## The four highest-impact LCP fixes

1. **Preload and prioritize the LCP image** – `fetchpriority="high"`, and **never `loading="lazy"` on it**.
2. **Inline critical CSS.**
3. **Preload fonts, `font-display: swap`.** Deliver preloads as **HTTP `Link` headers for 103 Early Hints**, not DOM `<link rel="preload">` tags.
4. **Server-side render.**

And the trap from `motion.md`: **never animate the LCP element in.** Fading the largest contentful element from `opacity: 0` directly delays the metric.

## Images

| Format | Support | Verdict |
|---|---|---|
| **AVIF** | **~93.4%** | **20–30% smaller than WebP at equal visual quality**; 15–40% fewer bytes in testing. Better gradients, HDR and wide gamut. **5–10× slower to encode** – a server-side cost only |
| **WebP** | **~97%** | Wider reach, fast encode. The fallback |
| JPEG / PNG | Universal | Final fallback only |

**Ship AVIF with a WebP fallback via `<picture>`.** The remaining gap is legacy Android and iOS 15 and earlier.

Responsive image mechanics – `srcset`, `sizes`, art direction – are in `layout.md`.

### Image placeholders

Progressive loading with a low-quality placeholder, ordered by cost:

| Technique | Notes |
|---|---|
| **CSS-only LQIP** | A tiny blurred version encoded compactly. Zero network requests, zero JS |
| **Base64 LQIP** | ~20 × 20 px blurred image inlined as a data URI. Simple and effective; adds bytes to the HTML |
| **BlurHash** | DCT of the image keeping key components, Base83-encoded. Very compact string |
| **ThumbHash** | Newer – **better color rendering and transparency support. The current best-in-class** |
| **SQIP** | SVG shape approximation. Distinctive look, more overhead |

- Combine with `IntersectionObserver` lazy loading, or native `loading="lazy"`.
- **Reserve the correct aspect ratio regardless.** The placeholder must occupy exactly the final box, or you have traded a blank for a layout shift.

**The design value: the placeholder communicates composition and color before the image lands, so the page reads as complete earlier.** It is the image equivalent of the skeleton, and **it does not suffer the skeleton's evidence problem, because it is showing real information about the actual image.**

## Rendering – `content-visibility`

`content-visibility: auto` **skips layout and paint for offscreen elements entirely.** ~90%+ support.

**Always pair with `contain-intrinsic-size`.** Without it the browser estimates 0 × 0 for offscreen content, so **scrollbars jump and CLS appears the moment it renders:**

```css
.article-card { content-visibility: auto; contain-intrinsic-size: auto 500px; }
```

**Apply only to large, self-contained, repeating sections** – articles, feed items, long list rows. **Applying it broadly is worse than not applying it.**

## Instant navigation – Speculation Rules

Prefetch (HTML only) or prerender (full page in a hidden tab, JS executed, ready to swap).

- **Prerendered navigations measured p75 LCP of 320 ms against 1,800 ms – an 82% improvement.**
- **The design insight: a 200 ms hover over a link is free time.** Predicting intent from hover and using that window is **the cheapest large perceived-speed win available.**
- **Cost: prerendering executes the page.** Analytics fire, resources load, work is done for navigations that may never happen. Use `moderate` or `conservative` eagerness for anything expensive, and **never prerender something with side effects.**

## Perceived vs actual performance

**Actual performance is a number, perceived performance is a design outcome, and they can be improved independently.**

- **Progressive loading** – prioritize the order elements appear so the user can build a mental model and start interacting while the rest arrives.
- **Optimistic UI** – decouple feedback from the network. See `states.md`.
- **Speculative loading** – spend the user's decision time.
- **Skeletons** – contested, but the *ordering* advice holds regardless. See `motion.md`.

And the finding that governs all of it: **consistency beats speed.** Variable performance destroys smooth perception even when averages are good.

## Performance budgets – how design decisions get made

A performance budget is **a limit set at the start of the project**, agreed between design and engineering, that every later decision is checked against.

The reason it belongs in a design skill is this framing:

> If you want a large hero image or video, you may need to cut down on the number of typefaces. You could design with one display face and keep a system font for body copy. Depending on your icon usage, consider SVGs over icon fonts.

**The budget converts performance from a constraint imposed afterward into a currency the designer spends.** A far more productive relationship than "make it faster later", and **it produces better design because it forces prioritization.**

- **Set it at the start, with designer and developer together**, so there is input on every component.
- **Benchmark against competitors** – find one that performs well and beat it.
- A useful stretch target: **loads in 3 seconds on 2G.**
- **Every feature, asset, animation, font and dependency must justify its existence** and measurably improve the user's ability to complete a task.
- Load times over 3 s are associated with ~40% higher bounce.

## Low bandwidth and low-end devices

A large real audience, not a charity case: intermittent connections, expensive data, old devices, restricted corporate networks, mobile-only access.

- **Reduction is the single biggest lever – not optimization, removal.**
- Modern formats, lazy loading below the fold, **minimize DOM size**, CSS-based and minimal animation.
- **Test under throttling in DevTools rather than guessing.**
- **A design that only works on a flagship phone on wifi is a design that excludes most of the world.**

## Sustainability

Performance and sustainability are **the same work with two justifications**, and page weight is the shared metric. Every reduction argument gets stronger when it can be made on both grounds.

- **~0.8 g CO₂ per page view** on average; the internet is estimated at **~3.7% of global carbon emissions.**
- **Page weight is the single biggest factor.** Average UK page **~4 MB**; a deliberately optimized build can run **under 200 KB** – a 20× difference.
- Emerging: **carbon-aware computing** – sites detecting a high-carbon local grid and switching to a lite mode.
- **62% of consumers say they would choose one brand over another if the products were identical and one site was clearly greener.** Secondhand.

## Designing for machines

The 2026 addition to the brief: **a growing share of users never see the page – an AI agent parses it on their behalf.**

- **Structured data is the base layer.** Organization schema – legal name, founding date, URL, logo, contact, social profiles – is **how LLMs distinguish your brand from every other use of the same word.**
- **`llms.txt`** – structured Markdown, single H1 for the project or brand, blockquote summary immediately after. Published by Anthropic, Stripe, Vercel, Cloudflare and others. Reported to reduce hallucination 30–70% when tools bundle it into context.
- **The honest caveat: Google states it ignores `llms.txt`** and that such files will neither harm nor help visibility in Search. **The argument for it is signalling to non-Google AI systems and improving citation likelihood – not a Google ranking factor.** Say this plainly rather than repeating the hype.
- **Where design genuinely intersects: semantic HTML, heading hierarchy and clear structure now serve three masters at once** – accessibility, search, and machine parsing. **Same practice, three justifications, which makes it much easier to fund.**
- Search behavior shifting from keywords to conversational prompts favors **natural-language FAQs and direct-answer sections placed higher in the page.**
- Organic CTR can drop **over 61%** when an AI Overview is present. The strategic conclusion: **the future of SEO is not ranking higher, it is becoming the answer.**
