# Platform reality

Desktop conventions, internationalization, and the cross-browser and cross-platform facts that change design decisions. Native app design is the `design-native` skill.

## Desktop conventions

- **⌘K / Ctrl+K command palette is now an expected affordance** in any tool with power users. It originated in VS Code and Sublime and is standard in Figma, Notion, Linear, Raycast and Spotlight.
- A command palette is a text input plus a categorized action list. **It is also the cheapest way to make a dense product feel fast, because it makes discoverability and speed the same feature.**
- Command labels: **brief, verb-led, no branded terms.** Limit modifier keys, follow platform conventions, and **handle the OS difference (⌘ vs Ctrl) rather than hardcoding one.**
- **Desktop earns:** hover states, higher density, smaller targets (24 px minimum, 32 px comfortable), multi-column layouts, keyboard-first flows.

Keyboard shortcut conventions are in `components.md`.

## Cross-browser reality

**The iOS constraint that changes decisions:**

> **Safari's WebKit engine runs on every iOS device by policy.** Any WebKit rendering bug affects **100% of your iOS users regardless of which browser icon they tap.**

**Chrome on iPhone is Safari. There is no fallback browser to recommend.**

Known Safari divergences worth checking:

- `position: sticky` still has unresolved issues.
- Transitions that are smooth in Chromium can stutter.
- Flexbox overflow, grid gap and print stylesheets differ subtly.
- **`backdrop-filter` on `position: fixed` misbehaves.** See `accessibility.md`.
- IndexedDB is slower; ITP limits cross-site storage.

**Form controls are the most inconsistent components across browsers** – `select`, `date`, `range`, checkboxes and radios all render natively and differently. **This is exactly why `accent-color` and `appearance: base-select` matter.** See `modern-css.md`.

And the recurring one: **16 px on inputs, or iOS Safari zooms the page on focus.**

## Font rendering by platform

Two different philosophies, producing visibly different type.

| | macOS | Windows |
|---|---|---|
| Engine | Quartz, **grayscale antialiasing**, largely **ignores hinting** | ClearType / DirectWrite, **aggressive hinting** to the pixel grid |
| Priority | **Preserve the typeface design**, accepting some blur | **Sharpness and pixel alignment**, accepting deviation from the design |
| Result | Text looks **bolder and heavier**, strokes faithful | Text looks **thinner and sharper** |

**The consequences are real:**

- **A weight that looks right on a Mac often looks thin on Windows.** A 300-weight display face that reads elegant on macOS can read **weak and fragile** on Windows. **Check the light end of the ladder on Windows before committing.**
- **Very thin strokes and very low-contrast text are the most affected.**
- **If the audience is Windows-heavy, bias one step heavier and one step higher contrast** than looks right on a Mac.
- **This is a genuine hazard, because design decisions get made and judged on one platform.**

## Internationalization

**Use CSS logical properties by default, not physical ones.** `margin-inline-start` instead of `margin-left`, `padding-block` instead of `padding-top`/`bottom`, `inset-inline`, `border-inline-end`.

- **The inline axis follows text flow; the block axis is perpendicular.** One stylesheet then serves LTR and RTL **with no direction-specific overrides**, which is the entire maintenance argument.
- Set the `dir` attribute on `<html>` – `ltr`, `rtl`, or `auto`.
- **What does not mirror:** logos, clocks, media playback controls, most photographic content, and numbers. **Icons that imply direction – back and forward, next and previous – do mirror.**

> **Write logical properties from the start.** It costs nothing when the project is English-only and saves a full re-layout if it is ever not.

Text expansion figures and truncation rules are in `components.md`.

### CJK typography

Differs in ways that break Western assumptions.

- **Japanese has no spaces between words**, so a break could theoretically occur anywhere – but **JIS X 4051 (Japanese) and GB/T 15834 (Chinese) define characters that must not start or end a line** (kinsoku).
- **Korean has spaces** and allows two wrapping modes: syllable-based (common, especially justified) or whole-word (common with a ragged edge).
- **`word-break: auto-phrase`** uses ML phrase parsing to break at phrase boundaries rather than mid-word – a real improvement for Japanese.
- **CJK wants more leading: ~1.7**, against 1.5 for Latin, because character density per line is much higher.
- Chinese and Japanese can set **vertically**, with punctuation that rotates or swaps to vertical-specific glyphs.
- **Latin type scales do not transfer.** CJK glyphs occupy a full em box and read larger at the same nominal size.

## What lives in `design-native` instead

Platform-specific material was split out and is not duplicated here. Use the `design-native` skill for:

- **iOS Liquid Glass**, SwiftUI and UIKit, SF Symbols, Dynamic Type.
- **Android Material 3 Expressive**, Jetpack Compose, Material Symbols.
- **The iOS versus Android convention split** – back behavior, gestures, tab bars, target sizes.
- **Mobile app navigation**, PWA install and app-store presence.
- **Haptics.**
- Rust GUI toolkits, desktop-native, watch, TV, automotive, spatial and embedded.
