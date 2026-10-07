# octet-scss

An 8pt vertical-grid SCSS framework, being prepared for its first npm
publish. Dart Sass, module system only (`@use` / `@forward`, no `@import`).
The README is the public API reference: tokens, mixin signatures, theming.
Read it before changing anything public.

## Current work: npm publish (`nodepublish` branch)
- `package.json` is in place: `exports` / `"sass"` field, `files` whitelist,
  optional peer dependency `sass >=1.71.0` (the first version with `pkg:`
  imports)
- README install section and `starter-theme/README.md` are rewritten for
  npm (no more submodule wording)
- None of this is committed yet: the user commits it on `nodepublish`
- Still to do: test-install into a separate npm site, then `npm publish`.
  The user publishes and tags; don't run `npm publish` yourself
- Once it's published, the user's portfolio (`fct2023`, where this repo was
  a git submodule) switches to the package. That's out of scope here

## Layout
- `abstracts/`: the core. Tokens, mixins, functions, reset, a11y, motion,
  layout. Emits only a11y/reset CSS. This is the main public API
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
- **The user makes all commits.** Make and verify the change, show the diff,
  and leave it in the working tree. Don't run `git commit`, but do suggest a
  message. Reviewing each commit is how the user audits the work
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
