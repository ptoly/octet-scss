# octet-scss

**A small, brandable SCSS framework built on an 8-point grid.** The name is the
point: an *octet* is eight, and everything — spacing, type rhythm, layout —
steps on an 8pt grid (a 4px base unit, doubled for rhythm).

## Why it exists

To make a good typographic and spatial foundation the default: consistent 8pt
spacing, responsive/scalable type, and a responsive layout system, without
re-deriving them each project. It speaks in **pixels** (designers reason in
pixels), keeps runtime concerns as CSS custom properties and compile-time
concerns as SCSS.

## Features

- **8pt grid** — `$grid-base` (4px) + `gridx($n)`; `scaled-spacing` steps stay on the baseline
- **Responsive, scalable typography** — `fluid-font-size()` (a self-bounding `clamp()` with line-height snapped to the grid)
- **Responsive layout mixins** — `from()` / `until()`, `display-grid`, `max-width`, `scaled-spacing`
- **Stepped layout spacing** — one breakpoint scale drives vertical rhythm; the convention is to keep block spacing in a single file
- **Updated CSS reset** — box-sizing, zeroed margins, sensible media/element defaults
- **Max-readability measure** — `$layout-readable-width` (720px) for comfortable line length

Also included and wired: **AA-by-default accessibility** (global focus ring,
`.sr-only`, skip link, reduced-motion reset), **motion tokens + a `transition()`
mixin**, a **z-index scale**, and **elevation** (layered shadows, levels 1–24).

## Requirements

- **Dart Sass**, using the module system — `@use` / `@forward` (there is no
  `@import` in the source). Developed against Dart Sass **1.99**.

## Install

Not published to npm — it's a GitHub repo. Either:

```bash
# clone
git clone https://github.com/ptoly/octet-scss.git

# …or add as a submodule
git submodule add https://github.com/ptoly/octet-scss.git src/octet-scss
```

## Quick start

Import the core (tokens, mixins, reset, a11y), then override a token or two in
your own `:root` — it loads after the framework, so your values win:

```scss
@use "octet-scss/abstracts" as *;   // design tokens + mixins + reset + a11y

:root {
  --primary-500: rebeccapurple;   // re-brand a ramp…
  --link:        #e8dda6;         // …or a single semantic token
}

.card {
  padding: gridx(4);              // 16px, on the grid
  border-radius: $radius-md;      // 4px
  background: var(--surface-card);
  @include elevation(2);
}
```

## Token & mixin reference

### SCSS tokens — `abstracts/_variables.scss`, `_config.scss`

| Token | Value / notes |
|---|---|
| `$grid-base` | `4px` — the rhythm unit |
| `$font-family-sans` / `$font-family-serif` | `omnes-pro` / `gelica` |
| `$font-size-base` | `18px` |
| `$radius-sm` / `-md` / `-lg` / `-xl` | `2` / `4` / `8` / `16px` |
| `$breakpoints` | `360 540 720 900 1280 1366 1441px` — the one scale `from()`/`until()` draw from |
| `$layout-readable-width` | `720px` (max-readability measure) |
| `$layout-gutter` | `gridx(18)` — container horizontal offset |
| `$duration-fast` / `-base` / `-slow` | `120` / `240` / `400ms` |
| `$ease-standard` / `-out` / `-in` | `cubic-bezier(…)` easings |
| `$z-base … $z-toast` | `0, 10, 100, 200, 300, 400, 500` — named stacking layers |
| `$layout-max-width`, `$layout-sidebar-width`, `$layout-rail-collapse`, `$layout-single-col` | project config (`_config.scss`) |

> Defined but not wired (kept for future use, not documented as functional):
> `$layout-global-height`, `$font-family-narrow`, `$font-ratio-minor` / `-major`.

### Color tokens (`:root` custom properties) — `abstracts/_colors.scss`

- **Primitives** (numeric ramps): `--neutral-*`, `--primary-*`, `--secondary-*`, `--tertiary-*`
- **Semantic**: `--surface-page/-raised/-card/-elevated`, `--text-primary/-secondary/-muted/-on-brand`, `--border-subtle/-default/-strong`, `--brand/-hover/-active/-subtle`, `--accent`, `--highlight`, `--link/-hover`, `--focus-ring`, `--danger`

### Mixins & functions

| Signature | Purpose |
|---|---|
| `gridx($value)` | `$value × $grid-base` → on-grid length |
| `scaled-spacing($properties, $breaks: 4, $max: 32px, $step: 8)` | Stepped responsive spacing that grows to `$max` across the top breakpoints |
| `fluid-gutter($property, $min: 24px, $max: 80px, $min-bp: 360px, $max-bp: 1920px)` | Fluid (clamp) horizontal spacing |
| `fluid-font-size($min-size: 16px, $max-size: 24px, $min-bp: 360px, $max-bp: 1440px)` | Fluid font-size, line-height stepped to the grid |
| `from($device)` / `until($device)` | `min-width` / `max-width` media queries (exact complements) |
| `hover-only` / `touch-only` | `(hover: hover) and (pointer: fine)` / `(hover: none)` |
| `display-grid` | `display: grid` + a scaled gap |
| `max-width` | Centered container capped at `$layout-max-width` |
| `elevation($elevation)` | Layered box-shadow, levels 1–24 |
| `transition($properties: all, $duration: $duration-base, $easing: $ease-standard)` | Token-based transition (self-disables under reduced-motion) |
| `focus-visible($color: var(--focus-ring), $width: 2px, $offset: 2px)` | Custom focus ring |
| `sr-only` / `sr-only-focusable` / `skip-link` | Screen-reader / skip-link utilities |
| `roundto($value, $nearest)` | Round up to the nearest multiple |

Emitted as CSS by the core: a global `:focus-visible` ring, `.sr-only` /
`.visually-hidden` / `.sr-only-focusable` / `.skip-link`, and a
`prefers-reduced-motion` reset.

> Also present in `abstracts/layout/`: a flexbox grid (`grid`, `cell`,
> `fb-responsive-grid`, with `!default` `$default-columns` / `$default-gap`).
> **Currently unused** by the framework or starter-theme — included but not part
> of the documented, exercised API.

## Theming

The override model is **CSS custom properties**. Import the framework, then set
your values in `:root` *after* — later declarations win, so no `!important` or
Sass config needed:

```scss
@use "octet-scss/abstracts" as *;
:root { --brand: /* your brand */; --surface-page: /* your bg */; }
```

SCSS tokens (`$breakpoints`, `$radius-*`, grid, type, motion, z-index) are
**compile-time** and fixed — change them by editing your own copy of the theme,
not via `@use … with()` (they are not `!default`; the only `!default` vars are
the flexbox grid's `$default-columns` / `$default-gap`).

A generic example theme ships in **`starter-theme/`** (renamed from `themes/`) —
including `_theme-colors.scss`, an opt-in "olive" re-brand you can copy to
`_brand.scss` and edit. `starter-theme/README.md` documents the
copy-and-customize workflow.

## License

MIT
