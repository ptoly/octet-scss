# Changelog

All notable changes to octet-scss.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). While
the project is pre-1.0, a **minor** bump may carry breaking changes — pin to an
exact version if that matters to you.

## 0.3.0 — 2026-10-04

Framework tidy-up ahead of a public release: dead code out, two silent bugs
fixed, and the generic SVG helpers moved somewhere consumers can reach them.

### Breaking

- The flexbox grid is gone (see **Removed**). It was documented as unused, and
  `fb-responsive-grid` could never be called at all — it referenced `from()`
  without `@use`-ing the module that defines it, so the first invocation raised
  `Undefined mixin`.
- `elevation()` accepts levels **1–9** only. Out-of-range levels now `@error`
  instead of resolving to `null` on every map and silently emitting an empty
  rule.
- `scaled-spacing()` now `@error`s when the ramp would start negative. The base
  value is `$max - (($breaks - 1) * $step)`, so too many breaks for the
  `$max`/`$step` pair previously emitted invalid CSS. The check is
  property-aware: `gap`, `padding`, sizing, `border-radius` and `font-size`
  error, while negative `margin` and offsets still pass as legitimate overlap.
- The inline-SVG helpers moved modules (see **Changed**).

### Removed

- `abstracts/layout/_css-flexbox-grid.scss`, with the `grid` / `cell` /
  `fb-responsive-grid` mixins, the `grid-gap()` function, the
  `$default-columns` / `$default-gap` variables, and the emitted `.fb-grid`,
  `.fb-cell` and `.fb-cell.spanNof12` classes. Use the CSS grid helpers
  (`display-grid`, `.cg-cols-*`, `.cols-auto-fit`) instead; for a plain wrap
  container, `display: flex; flex-wrap: wrap` is all `.fb-grid` contributed.
  Drops ~61 lines of emitted CSS from every build.
- `elevation()` levels 10–24, across all three shadow maps. Compile-time only;
  no change to emitted CSS.

### Changed

- `str-replace`, `url-encode`, `inline-svg` and `prepare-icon` moved from
  `starter-theme/_icons.scss` to `abstracts/_icons.scss`, and are forwarded
  with the rest of the core. They are generic and emit no CSS, but living in
  the theme layer meant consumers couldn't `@use` them and had to copy the
  file. `starter-theme/_icons.scss` keeps only the arrow SVG and
  `link-arrow-right`. Emitted data-URIs are byte-identical.
- The example theme `starter-theme/_theme-colors.scss` is now generic. It was a
  drifted copy of the author's own olive brand — five of nine tones identical,
  four diverged, link and highlight hexes duplicated outright. Replaced with
  "ember", in a palette unlike the indigo defaults, and restructured to show
  both re-brand routes: swap a whole ramp, or retarget individual semantic
  tokens. Single-notation hex throughout.

### Fixed

- Invalid negative gaps in the column utilities. `.cg-cols-*` asked for a gap
  across 7 breakpoints to reach 32px, putting the ramp's base at `-16px` and
  emitting `gap: -16px` and `gap: -8px` on all 11 blocks — 22 invalid
  declarations that browsers were quietly discarding. Computed gap is unchanged
  at every breakpoint.
- `--link-hover` was never applied. The token was defined, re-pointed by the
  example theme and listed in the README, but no rule read it, so links had no
  hover state. `starter-theme/_typography.scss` now uses it.

### Docs

- Removed the flexbox grid's README entries alongside the module.
- Documented `inline-svg`, `prepare-icon`, `url-encode` and `str-replace` in
  the mixin reference, now that they're part of the core.
- Dropped a `$breakpoints` note crediting a `modular-spacing` mixin that does
  not exist.
- Corrected a `starter-theme/README.md` row citing a `_links.scss` that is not
  in the directory.

### Upgrading

If you worked around the old layering by **copying** the inline-SVG helpers
into your own partial — the only way to get them before this release — that
copy now collides:

```
Error: Two forwarded modules both define a function named str-replace.
```

Delete your copy and take them from `abstracts`. Keep your own icon constants
and treatments; only the four generic functions moved.

Emitted CSS is otherwise unchanged: the elevation trim is compile-time only,
and the gap fix removes invalid declarations without altering any computed
value.

## 0.2.x — 2026-09-21

Tagged `v0.2.x`.

### Changed

- Reduced the neutral ramp to nine tones, down from a larger set that was
  unwieldy to choose from.
- Adopted BEM naming for emitted classes.
- Headline sizing uses the CSS `round()` function so line-height snaps to the
  grid as type scales fluidly, rather than stepping at breakpoints.
- Body text blocks emit `rem`, so reading copy honours the reader's browser
  font-size (WCAG 1.4.4). UI chrome stays in px.

### Fixed

- Removed an orphaned `@use`.

### Docs

- Substantial inline and README documentation passes, plus consolidation and
  cruft removal.

## 0.1.0-beta — 2026-09-18

Initial tagged release.
