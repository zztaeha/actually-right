# Modern CSS capability

**What is *possible* changes what is *worth designing*.** A large amount of 2026 UI can be built with no JavaScript at all, which changes the cost of ambitious design – and removes the usual excuse for shipping a worse one.

## Capability table

| Feature | Support | Replaces |
|---|---|---|
| `:has()` | **~93%** | JS class-toggling for parent state |
| Container queries (`inline-size`) | **over 93%** | Component-level breakpoints, variant props |
| **Anchor positioning** | **~91%**, Baseline 2026 (`@position-try` needs Safari 18.4+) | **Floating UI, Popper.js, Tippy** |
| **Popover API** | Baseline Widely Available **April 2025**, ~85–90% of real traffic | Dropdown and tooltip JS, light-dismiss logic |
| `@starting-style` + `transition-behavior: allow-discrete` | All majors since Dec 2024 | JS enter and exit animation orchestration |
| **Subgrid** | **Baseline Widely Available March 2026**, over 90% | JS equal-height, magic-number card alignment |
| Scroll-driven animations | **~84%** | IntersectionObserver reveal, JS parallax |
| View transitions (same-document) | Chrome 111+, Safari 18+, FF 144+ | Route-transition libraries |
| `@layer` | All major engines | Specificity wars, `!important` |
| `content-visibility` | **~90%+** | Virtualized-list libraries, for simple cases |
| `field-sizing: content` | Baseline **newly available June 2026** | Auto-growing textarea JS |
| `text-box-trim` / `text-box-edge` | Across latest browsers **since Aug 2026** | Manual optical spacing hacks |
| `shape()` | Chrome, FF, Safari, Edge | SVG `path()` clip-paths that break on resize |
| `reading-flow` | Emerging | Fixing tab order broken by visual reordering |
| `appearance: base-select` | **Chrome/Edge 134+ only** | Custom dropdown component libraries |
| `::scroll-button()` / `::scroll-marker()` | Emerging | Carousel libraries |

**Guard anything below Baseline with `@supports`.**

## Anchor positioning plus Popover – the biggest practical change

Together these replace what used to be a JS library and several hundred lines of positioning logic.

- **The `popover` attribute** handles show and hide, top-layer, focus management and light dismiss.
- **Anchor positioning** handles *where* it appears, with **`@position-try` giving automatic viewport-overflow flipping.**

> **For new work in 2026 there is no reason to install Floating UI, Popper or Tippy for basic anchored positioning.** Native is smaller, faster and more robust for the ~90% case.

### Popover vs dialog – the decision rule

| Use | When |
|---|---|
| **`popover`** | The user should be able to **ignore it and keep working.** Tooltips, dropdowns, help panels, filter menus, autocomplete. **Non-modal, background stays interactive, light-dismiss by default** |
| **`<dialog>` + `.showModal()`** | **Attention is required**; background interaction must stop. Focus trap, inert backdrop, Escape |
| `<dialog popover>` | Dialog semantics with popover behavior |

**`popover` is not a dialog replacement. If it needs a focus trap or must block the background, it is a dialog.**

## Entry and exit animation without JS

The four-part combination that finally solves animating to and from `display: none`:

```css
.panel {
  transition: opacity 200ms, transform 200ms, display 200ms allow-discrete;
}
@starting-style { .panel[open] { opacity: 0; transform: translateY(8px); } }
```

- **`@starting-style`** supplies the "before it existed" state.
- **`transition-behavior: allow-discrete`** lets discrete properties (`display`, `overlay`) participate.
- Display flips to visible at **0%** on entry and to `none` at **100%** on exit, **so the element is visible for the whole animation in both directions.**

**This is what makes the entrance/exit asymmetry rule implementable in pure CSS** for modals, popovers and drawers. See `motion.md`.

## Subgrid – the card-alignment fix

**The classic failure:** three pricing cards, one title wraps to two lines, one blurb is longer, and the three CTAs sit at three different heights. **The row looks broken and no amount of padding fixes it.**

`grid-template-rows: subgrid` makes the card inherit the parent's row tracks, so **every title, body and footer aligns across the row – no JS, no fixed heights, no magic numbers.**

Also excellent for **form layouts** (label column at `max-content`, inputs aligned) and nested sections sharing the page's column structure.

## `:has()` patterns worth knowing

```css
.card:has(> img)            /* layout changes when there is media */
label:has(input:checked)    /* style the label from the input's state */
.field:has(input:invalid)   /* validation styling on the wrapper */
.field:has(input:focus)     /* focus-within with more precision */
form:has(:invalid) .submit  /* act on a descendant's aggregate state */
```

**Performance:** modern engines use Bloom filters and ancestor-invalidation, so **a well-scoped `:has()` performs close to a normal descendant selector.**

**The rule: keep the anchor specific and constrain the argument with `>` or `+`.** The genuine risk is `html:has(...)` or `body:has(...)`, **re-evaluated globally on every DOM change** – fine for a theme toggle, bad if the argument is broad.

**Specificity gotcha: `:has()` takes the specificity of its most specific argument.**

## `@layer`

2026 cascade order: **origin and importance → inline styles → cascade layers → specificity → source order.**

**Layers sit *above* specificity, which is why they end the specificity war architecturally rather than symptomatically.**

- Declare the order once at the top: `@layer reset, base, components, utilities;`
- **Unlayered styles win over all layered styles.** The most common surprise.
- Tailwind v4 ships `@layer` internally, so component styles always yield to utilities with no `!important`.

## Small things with disproportionate value

- **`accent-color`** – one property themes checkboxes, radios, range and progress **with the browser's own accessible-contrast logic**, and it respects `color-scheme`. **More accessible than most hand-built replacements.**
- **`color-scheme: light dark`** plus **`light-dark()`** – native control theming and two-mode color in one declaration.
- **`field-sizing: content`** – a textarea that grows with its content, no `scrollHeight` listener.
- **`text-box-trim` / `text-box-edge`** – trims the font's built-in leading above and below the text box, **so a button's optical padding finally matches its declared padding.** This is the fix for "why does this text sit 2 px high in its container", **one of the most common invisible craft failures.**
- **`shape()`** – responsive clip paths in percentages and CSS units, instead of SVG `path()` coordinates that break on resize.
- **`reading-flow`** – aligns keyboard and AT order with the *visual* order in flex and grid, **fixing the accessibility hole created by `order` and `grid-auto-flow: dense`.** See `accessibility.md`.
- **`::scroll-marker()` / `::scroll-button()`** – browser-generated carousel dots and arrows that are **focusable, keyboard-accessible and auto-disabled at the boundaries. Accessible by construction, which hand-built carousels almost never are.**

## The design consequence

Two things follow, and both matter.

1. **Ambitious interaction is now cheap.** Anchored popovers, animated modals, aligned card grids, scroll-linked reveals and accessible carousels cost CSS, not a dependency and a bundle. **Design accordingly.**
2. **The native versions are more accessible than the hand-built ones**, because focus management, keyboard behavior, top-layer stacking and light dismiss come with them. **Reaching for a custom component is now usually a *downgrade* in accessibility, not an upgrade in control.**
