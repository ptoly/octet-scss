# octet-scss

An 8pt vertical-grid SCSS framework, distributed on npm as
`octet-scss`. Dart Sass, module system only (`@use` / `@forward`, no
`@import`).
The README is the public API reference: tokens, mixin signatures, theming.
Read it before changing anything public.

## Layout
- `abstracts/`: the core. Tokens, mixins, functions, motion, layout. Emits
  **no** CSS (color values are Sass maps here). This is the main public API
- `base/`: the CSS every page needs. Reset, a11y rules, `:root` color tokens,
  document defaults (html/body, body font, link color)
- `utilities/`: grid classes, helper classes, `html.is-locked`
- `components/`: buttons, bullet lists (optional)
- `starter-theme/`: example theme, meant to be **copied** into the user's
  project, not imported. Its partials use `../abstracts`, and the copy step
  rewrites that to `octet-scss/abstracts`. Keep it that way, since `main.scss`
  compiles it in place
- `sections/`, `pages/`: demo content, pulled in by `main.scss` for the full
  showcase
- `assets/`: README logo only. Left out of the npm package

## Conventions (settled; follow them, don't re-argue them)
- CSS custom properties for runtime values. SCSS variables for compile-time
  values (breakpoints, because media queries can't read `var()`)
- Pixels for UI and display type. Body copy (p, lists, captions) uses the
  px-authored `rem()` so it honors the reader's font-size setting
- Hover: `(hover: hover) and (pointer: fine)` via `hover-only`, never a width
  breakpoint. `touch-only` for `(hover: none)`
- `overflow: clip` over `overflow: hidden`
- Vertical spacing is stepped (`scaled-spacing`), not clamped, so it stays on
  the 8pt baseline. Horizontal spacing is fluid (`fluid-gutter`)
- All block-level layout spacing lives in ONE file, on purpose (see
  `starter-theme/_theme-spacing.scss`). Always `margin-bottom`, never
  `margin-top`
- Group code by unit of work, not by kind of code
- One `$breakpoints` scale: 360, 540, 720, 900, 1280, 1366, 1441px. It drives
  vertical rhythm and is the list `from()` / `until()` pick from. Theme reflow
  points (rail width, etc.) belong to the theme, not the core
- Color: neutral + primary/secondary/tertiary ramps feeding semantic tokens
  (`var(--brand)`, `var(--text-primary)`, …). No `gray()` helpers
- BEM class names (see "Naming conventions" in the README)
- Configurable values are `!default`, set with `@use "octet-scss/abstracts" with (…)`

## Goal
A clean, modern, generic framework that's ready to share. That comes before
preserving any one site's look, including the user's portfolio, which was
built on it. Generic tokens and modern structure beat matching old output.

## Change discipline
- **Commit only when the user says to.** Make and verify the change, show the
  diff, suggest a message, and leave it in the working tree. When the user
  says to commit, run `git commit` yourself. They review every diff in Git
  Tower, so each commit should be easy to read there. Never push or tag
- One coherent change per commit; the revert trail matters. Commit subjects
  are past tense and plain ("Guarded scaled-spacing against a negative ramp")
- **The build must always compile cleanly.** A broken build isn't shareable.
  Byte-identical CSS checks aren't required; changes to the output are fine
  when they serve the goal
- Pre-1.0: a minor bump can break things. Every user-visible change gets a
  CHANGELOG entry (Keep a Changelog format, sections: Breaking / Removed /
  Changed / Fixed / Docs / Upgrading). Explain *why*, not just what. If the
  emitted CSS changes, say so. If it doesn't, say "no change to emitted CSS"
- Ask before deleting a public mixin, function or token. Someone downstream
  may use it
- The user publishes to npm and tags releases. Don't run `npm publish`

## Checking changes
There's no build script or test suite. Compile directly with Sass:

```bash
npx sass --quiet main.scss > /tmp/octet.css   # full build must compile
npm pack --dry-run                            # check what ships
```

To test the published import paths, symlink the repo into a scratch
`node_modules/octet-scss` and compile `@use "pkg:octet-scss/abstracts"` with
`--pkg-importer=node`, and `@use "octet-scss/abstracts"` with
`--load-path=node_modules`. Sass 1.71 places some `@extend`-merged rules in a
different order than 1.99. The declarations are the same.

Current `sass` needs Node 20+. The nvm default is Node 18 for other
projects, where `npm i sass@latest` fails with `ERR_REQUIRE_ESM`
(chokidar). This repo's `.nvmrc` pins Node 24 LTS (`lts/krypton`). Run
`nvm use` before working here.
