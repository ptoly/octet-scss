# Changelog

All notable changes to octet-scss.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). While
the project is pre-1.0, a **minor** bump may carry breaking changes — pin to an
exact version if that matters to you.

## 0.4.1 — 2026-10-10

### Docs

- Corrected the claim that `transition()` "self-disables" under reduced motion.
  Since 0.4.0 the `prefers-reduced-motion` reset lives in `base`, so a project
  that loads only `abstracts` keeps its transitions for reduced-motion users.
  The README and source comments now say the reset in `base` turns them off.
  The mixin is unchanged: one global reset also covers hand-written
  transitions, animations and smooth scroll, which a per-mixin media query
  would miss. No change to emitted CSS.
- Clarified what `_theme-spacing.scss` is for: all spacing that has to stay
  consistent, meaning block rhythm *and* gutters or insets shared across
  components, applied by selector. Padding unique to one component still
  co-locates with it. The README and the file's header comment now also say
  not to start a second spacing file or a parallel layer of spacing mixins,
  which a downstream project did. No change to emitted CSS.
- Made the starter-theme setup harder to miss. The main README now has a
  "Starting a real project" section listing the five copy-and-customize steps
  from `starter-theme/README.md`, instead of a single pointer sentence. Also
  fixed the Theming paragraph, which read as "copy the starter theme to
  `_brand.scss`" when it meant `_theme-colors.scss`. No change to emitted CSS.
- Added `CONVENTIONS.md`, a terse, literal rulebook for writing styles in a
  project built on octet-scss: file ownership, load order, spacing, units,
  color, naming, and a done checklist. It's written for AI coding assistants
  as much as people, and it ships in the package so a project can point its
  `CLAUDE.md` / `AGENTS.md` at `node_modules/octet-scss/CONVENTIONS.md`. The
  README links to it. No change to emitted CSS.
- Relaxed the spacing rule from "always `margin-bottom`, never `margin-top`"
  to "mostly `margin-bottom`", and dropped the specific exceptions (a caption,
  page content below the header). The direction is a consistency default, not
  a law; edge cases are the developer's call. No change to emitted CSS.

## 0.4.0 — 2026-10-09

Split the framework into four layers: `abstracts` (no CSS), `base`,
`utilities` and `starter-theme`. Until now, `@use "octet-scss/abstracts"` was
both the toolbox and a stylesheet. Loading it to borrow one mixin also wrote
out the reset, the a11y rules, every `:root` color token and about 9.5 KB of
grid classes. Now abstracts is pure Sass: it defines things and outputs
nothing. The CSS comes from layers you choose to load. Code was moved, not
rewritten. The full `octet-scss` build has the same selectors and declarations
as before, though some rules appear in a different order.

### Breaking

- `octet-scss/abstracts` no longer emits any CSS. If you relied on it for the
  reset, focus ring, `.sr-only` / `.skip-link`, reduced-motion or the `:root`
  colors, add `@use "octet-scss/base";` after it.
- The grid classes (`.cols-*`, `.cg-cols-*`, `.cols-auto-fit`,
  `.cg-cols-auto`, `.css-grid-layout`) moved out of `abstracts/layout/_css-grid.scss`
  into `utilities/_grid.scss`. The `display-grid` mixin stays in abstracts.
  Add `@use "octet-scss/utilities";` if you use the classes. Their `%cg-cols-*`
  placeholders moved with them.
- `abstracts/_reset.scss` moved to `base/_reset.scss`. Direct imports of
  `octet-scss/abstracts/reset` need the new path.
- Document essentials moved out of the starter theme into `base/_document.scss`:
  `html` color and background, `html`/`body` min-height, `body` margin,
  font-family and line-height, and `a` color plus hover. A copied starter theme
  that still has these rules will just repeat them, so nothing breaks, but you
  can delete them from your copy.
- `starter-theme/_class-utilities.scss` moved to
  `utilities/_class-utilities.scss`, together with `html.is-locked`.
  `starter-theme/_base-theme.scss` is gone, since all of its rules moved.

### Changed

- The color values in `abstracts/_colors.scss` are now Sass maps (`$primary`,
  `$secondary`, `$tertiary`, `$neutral`, `$semantic-colors`), all `!default`.
  `base/_colors.scss` emits them as the same `:root` custom properties. Runtime
  overrides in `:root` still work as before.
- The a11y rules (`:focus-visible`, `.sr-only`, `.visually-hidden`,
  `.sr-only-focusable`, `.skip-link`, reduced-motion) moved into
  `base/_a11y.scss`. The mixins stay in `abstracts/_a11y.scss`.
- `main.scss` loads the layers in this order: abstracts, base, components,
  starter-theme, sections, pages, utilities. Utilities load last on purpose:
  when a utility class and a component or theme class of the same specificity
  set the same property, the utility wins. So `class="card max-width"` gets the
  `max-width` you asked for.
- Emitted CSS: the selectors, declarations and values are the same. Base
  rules now come before components and the theme in the output, and utility
  rules come after everything else. Before, the grid classes came first and the
  class utilities sat in the middle of the theme. Wherever a utility class and a
  same-specificity class are on the same element, the utility now wins.
  `.unstyled` already beat `.btn`, and the grid classes now beat component
  `display` / `gap` values. The `a` rule is split in two: `color` and hover in
  base, `text-decoration` and `font-weight` in the theme.
- `package.json` `files` now includes `base/` and `utilities/`.

### Docs

- README: added `base` and `utilities` to the import table, and the quick start
  and theming examples now load `base`. A new "Load order" section explains the
  layer order and why utilities go last. A new "Utility classes" table lists
  what `utilities/` provides; the grid classes were not documented before.
  The theming section notes that the color maps can be configured at import.
- `starter-theme/README.md`: the `main.scss` example loads `base` and
  `utilities`, and the file table drops the files that moved.

### Upgrading

Change your entry point to:

```scss
@use "octet-scss/abstracts" as *;
@use "octet-scss/base";
// … components, your theme, your own styles …
@use "octet-scss/utilities";   // last, so utility classes win; if you use them
```

If you copied the starter theme, its `_base-theme.scss` and
`_class-utilities.scss` repeat what's now in `base` and `utilities`. Delete
them, or stop loading `utilities`.

## 0.3.1 — 2026-10-07

First release on npm. The framework is unchanged from 0.3.0. This release
changes how you get it, not what it emits. No change to emitted CSS.

### Changed

- Published as the `octet-scss` npm package. Until now the only ways in were
  cloning the repo or adding it as a git submodule. Submodules pin a commit
  rather than a version and are awkward to update, so they don't suit a shared
  framework. `npm install octet-scss` gives you a versioned dependency.
- `package.json` declares a `"sass"` entry and an `exports` map, so Dart Sass's
  Node package importer resolves `pkg:octet-scss/abstracts` and the other entry
  points. Plain `octet-scss/abstracts` also resolves when `node_modules` is on
  the load path.
- `sass >=1.71.0` is an optional peer dependency. 1.71 is the first release
  with `pkg:` imports. It's optional because load-path users can bring any
  compiler that supports the module system.
- The package ships `abstracts/`, `components/`, `starter-theme/`, the demo
  `sections/` and `pages/`, and `main.scss`. The README logo in `assets/` is
  left out.

### Docs

- README: rewrote the install section for npm. It covers `pkg:` versus the
  load path, with Vite, webpack/Gatsby and CLI setups, and a table of the
  importable entry points. The stated minimum is now Dart Sass 1.71.
- `starter-theme/README.md`: replaced the submodule wording with copying the
  theme out of `node_modules` and repointing its `../abstracts` imports to
  `octet-scss/abstracts`.

### Upgrading

Submodule users can keep the submodule, since nothing in the source moved. To
switch, remove the submodule, then `npm install octet-scss`. Next, point Sass
at `node_modules` (or use `pkg:`), and change imports like
`../octet-scss/abstracts` to `octet-scss/abstracts`.

## 0.3.0 — 2026-10-04

Framework tidy-up: dead code out, two silent bugs
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
