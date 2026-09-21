# Octet-SCSS

**An 8-point vertical-grid SCSS framework. Spacing, type, and layout all step on eight** — by default. The rhythm is configurable: my own portfolio and the Northern Tool build both ended up on a 4-point grid.

I built this because I kept rebuilding it. Every project opened the same way — set the grid, size the type, wire the spacing, fix the reset — so I stopped starting over. Octet-SCSS is the foundation I've carried site to site for years, tightened a little each time. Sharing it was always the plan. It just took me a while to clean it up enough to admit I wrote it.

## Why: pixels and an 8-point grid

Designers think in pixels. Figma speaks pixels, Dev Mode says 24px, every handoff tool measures in pixels. Octet-SCSS keeps that unit end to end, so the number in the design file is the number in the code — no translation layer, no drift between what was drawn and what shipped. The math stays readable too: `gridx(4)` is 32px, not a decoded ratio.

The grid is the other half. One 8px base (halved to 4px if you want finer refinement) drives spacing and type together — so vertical rhythm lines up across blocks instead of by accident. Consistent spacing, a readable measure, type that scales: the defaults you'd set up anyway, already set up.

*One tradeoff, handled: px honors browser zoom — which satisfies WCAG 1.4.4, Resize Text — but not a user's default font-size preference. So body copy is the deliberate exception: reading text (p, lists, captions) is sized with `rem()` — still authored in px — so it honors that preference too. UI chrome and display type stay in px.*

## Features

- **8pt grid** — `$grid-base` (8px) and `gridx($n)` for on-grid vertical rhythm; spacing steps stay on the baseline
- **Responsive, scalable type** — `fluid-font-size()`, a self-bounding `clamp()`, with optional grid-snapped line-height via CSS `round()`
- **Reader-scalable body copy** — p, lists and captions use `rem()` (px-authored) so reading text honors the browser's font-size setting (WCAG 1.4.4)
- **Responsive layout mixins** — `from()` / `until()`, `display-grid`, `max-width`, `scaled-spacing`
- **Single-file spacing** — one breakpoint scale drives vertical rhythm, all in one place, for consistency (see more below)
- **Accessibility-ready (WCAG AA)** — focus-visible ring, `.sr-only`, skip link, reduced-motion reset, on by default
- **Motion tokens** — durations, easings, and a `transition()` mixin that self-disables under reduced-motion
- **Z-index scale** — named stacking layers, no magic numbers
- **Elevation** — layered shadows, nine graduated levels
- **Modern CSS reset** — box-sizing, zeroed margins, sensible element defaults
- **Max-readability measure** — `$layout-readable-width` (720px) for comfortable line length

## Requirements

**Dart Sass**, using the module system — `@use` / `@forward` (no `@import` in the source). Built against Dart Sass 1.99.

## Install

Not on npm yet — it's a GitHub repo. Two ways in:

```bash
# clone it
git clone https://github.com/ptoly/octet-scss.git

# …or add it as a submodule
git submodule add https://github.com/ptoly/octet-scss.git src/octet-scss
```

## Quick start

Drop the core into your styles, override a token or two, done:

```scss
@use "octet-scss/abstracts" as *;   // tokens, mixins, reset, a11y, motion

:root {
  --primary-500: rebeccapurple;   // re-brand a whole ramp…
  --link: #e8dda6;                // …or nudge one semantic token
}

.card {
  padding: gridx(4);              // 32px, on the grid
  border-radius: $radius-md;      // 4px
  background: var(--surface-card);
  @include elevation(2);
}
```

That's enough to build with. For a real site — app shell, nav, footer, your own palette — start from the **starter theme**: copy it out of the submodule so you can pull framework updates without your changes ever conflicting. The full walkthrough is in [`starter-theme/README.md`](https://claude.ai/chat/starter-theme/README.md).

## Token & mixin reference

### SCSS tokens — `abstracts/_variables.scss`

| Token                                      | Value / notes                                                |
| ------------------------------------------ | ------------------------------------------------------------ |
| `$grid-base`                               | `8px !default` — the rhythm unit; `gridx($n)` returns `$n × 8px` |
| `$rem-base`                                | `16px !default` — the document root font-size `rem()` divides by (leave at the browser default unless you set `html { font-size }`) |
| `$font-family-sans` / `$font-family-serif` | system stacks (override to taste)                            |
| `$radius-sm` / `-md` / `-lg` / `-xl`       | `2` / `4` / `8` / `16px`                                     |
| `$breakpoints`                             | `360 540 720 900 1280 1366 1441px` — see note below          |
| `$layout-readable-width`                   | `720px` (max-readability measure)                            |
| `$duration-fast` / `-base` / `-slow`       | `120` / `240` / `400ms`                                      |
| `$ease-standard` / `-out` / `-in`          | `cubic-bezier(…)` easings                                    |
| `$z-base … $z-toast`                       | `0, 10, 100, 200, 300, 400, 500` — named stacking layers     |
| `$layout-max-width`                        | `1640px !default` — content max width. (Rail width & reflow points are theme-owned — set in `starter-theme/_theme-layout.scss`.) |

### Color tokens (`:root` custom properties) — `abstracts/_colors.scss`

- **Primitives** (numeric ramps): `--neutral-*`, `--primary-*`, `--secondary-*`, `--tertiary-*`
- **Semantic**: `--surface-page/-raised/-card/-elevated`, `--text-primary/-secondary/-muted/-on-brand`, `--border-subtle/-default/-strong`, `--brand/-hover/-active/-subtle`, `--accent`, `--highlight`, `--link/-hover`, `--focus-ring`, `--danger`

### Mixins & functions

| Signature                                                    | Purpose                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `gridx($value)`                                              | `$value × $grid-base` → on-grid height / vertical length (width is fluid `clamp()`, not `gridx`) |
| `rem($px)`                                                   | px → rem against `$rem-base`; e.g. `rem(18px)` → `1.125rem`. For body copy that should honor the reader's font-size — not UI chrome |
| `scaled-spacing($properties, $breaks: 4, $max: 32px, $step: 8)` | Stepped responsive spacing that grows to `$max` across the top breakpoints |
| `fluid-gutter($property, $min: 24px, $max: 80px, $min-bp: 360px, $max-bp: 1920px)` | Fluid (clamp) horizontal spacing                             |
| `fluid-font-size($min-size: 16px, $max-size: 24px, $min-bp: 360px, $max-bp: 1440px, $line-height: null)` | Fluid `clamp()` font-size for **headline / display type**. Sets font-size only by default (leave line-height to the theme — e.g. `1` for tight display). Pass `$line-height` as a ratio to also keep line-height on `$grid-base` at every width via CSS `round()` (with a unitless fallback). Body copy (p, li, ol) stays fixed rem — not fluid. |
| `from($device)` / `until($device)`                           | `min-width` / `max-width` media queries (exact complements)  |
| `hover-only` / `touch-only`                                  | `(hover: hover) and (pointer: fine)` / `(hover: none)`       |
| `display-grid`                                               | `display: grid` + a scaled gap                               |
| `max-width`                                                  | Centered container capped at `$layout-max-width`             |
| `elevation($elevation)`                                      | Layered box-shadow, nine graduated levels                    |
| `transition($properties: all, $duration: $duration-base, $easing: $ease-standard)` | Token-based transition (self-disables under reduced-motion)  |
| `focus-visible($color: var(--focus-ring), $width: 2px, $offset: 2px)` | Custom focus ring                                            |
| `sr-only` / `sr-only-focusable` / `skip-link`                | Screen-reader / skip-link utilities                          |
| `roundto($value, $nearest)`                                  | Round up to the nearest multiple                             |

Emitted as CSS by the core: a global `:focus-visible` ring, `.sr-only` / `.visually-hidden` / `.sr-only-focusable` / `.skip-link`, and a `prefers-reduced-motion` reset.

> Also present in `abstracts/layout/`: a flexbox grid (`grid`, `cell`, `fb-responsive-grid`, with `!default` `$default-columns` / `$default-gap`). **Currently unused** by the framework or starter-theme — included but not part of the documented, exercised API.

## Layout spacing

One file owns the vertical rhythm — the spacing *between* blocks. I kept inheriting projects with margins and padding scattered across dozens of partials, no two sections spaced quite the same. Pulling every block-level margin into a single spacing file fixed that: the rhythm lives in one place, so it's consistent by construction and auditable at a glance. Spacing that's internal to a component (padding inside a card, say) still co-locates with its component — it's *layout* spacing that centralizes.

And it's always `margin-bottom`, never `margin-top`. Every block pushes the next one down, so spacing flows in one direction — top to bottom, the way you read it. One direction means margins never fight or double up, and "how far apart are these two blocks?" always has a single answer, on the block above. The rare exception is an element that genuinely belongs *below* another (a caption under its image); everything else pushes down.

Spacing steps on the 8pt grid through `scaled-spacing()` across `$breakpoints`, so vertical rhythm stays on the baseline as the viewport grows. `starter-theme/_theme-spacing.scss` is the worked example.

## Theming

The override model is **CSS custom properties**. Import the framework, then set your values in `:root` *after* — later declarations win, so no `!important` or Sass config needed:

```scss
@use "octet-scss/abstracts" as *;
:root { --brand: /* your brand */; --surface-page: /* your bg */; }
```

Most SCSS tokens (`$breakpoints`, `$radius-*`, motion, z-index, layout dimensions) are **compile-time and fixed** — change them by editing your own theme copy. Two are `!default` and configurable at import, since projects routinely re-base them:

```scss
@use "octet-scss/abstracts" as * with (
  $grid-base: 4px,   // default 8px — the 8pt rhythm unit
  $rem-base: 18px    // default 16px — the root size rem() divides by
);
```

If you re-base `$rem-base`, also set the matching document root so the two agree — and set it as a **percentage** (not a hard px) so body copy still honors the reader's font-size preference:

```scss
html { font-size: 112.5%; }   // 18px at the 16px browser default; scales with the reader
```

(The flexbox grid's `$default-columns` / `$default-gap` are `!default` too.)

A generic example theme ships in **`starter-theme/`** (renamed from `themes/`) — including `_theme-colors.scss`, an opt-in "olive" re-brand you can copy to `_brand.scss` and edit. `starter-theme/README.md` documents the copy-and-customize workflow.

## Naming conventions

Class names follow **BEM** — Block, Element, Modifier — so a selector tells you what a thing is and where it lives:

```html
<article class="card">            <!-- block: a standalone component -->
  <h3 class="card__title">…</h3>  <!-- element: a part of the block -->
  <p  class="card__body">…</p>
</article>
<a class="btn btn--primary">…</a> <!-- block + modifier: a variant -->
```

- **Block** — a standalone component: `.card`, `.nav`, `.hero`. Multi-word blocks use a single hyphen (`.card-grid`).
- **`block__element`** — a part that only means something inside its block: `.nav__link`, `.hero__actions`, `.page__body`. Two underscores. Elements don't nest in the name — it's `.card__title`, never `.card__header__title`.
- **`block--modifier`** — a variant of a block (or element): `.btn--primary`, `.btn--ghost`. Two dashes. A modifier is *added alongside* the base class (`class="btn btn--primary"`), never on its own.

Three rules keep it honest:

1. **State lives on attributes, not classes, when a semantic one exists.** Drive styling off `[aria-current]`, `[aria-expanded]`, `[aria-disabled]`, `[data-open]` — one source of truth, no class/ARIA drift. Reserve an `.is-*` class (e.g. `.is-locked`) for purely visual state with no attribute.
2. **IDs are for anchors and JS/ARIA hooks, never for styling.** An id is nearly impossible to override later; style with a class and keep the id for `href="#…"`, `aria-controls`, and `getElementById`.
3. **Utilities are a separate layer**, not BEM: single-purpose, hyphenated, context-free — `.center`, `.no-wrap`, `.max-width`, `.readability-width`, `.unstyled`. Reach for one on markup instead of a one-off rule; anything larger is a component.

Every shipped component (`.btn`, `.card`, `.hero`, `.page`, `.article`, `.nav`, `.footer`, `.cta`) already follows this — read one as a worked example.

## License

MIT
