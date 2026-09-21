# starter-theme

A generic, ready-to-brand theme built on the octet-scss core (app shell, nav,
footer, base document styles, typography, block spacing, utilities, icon
helpers). It's meant to be **copied out of the framework** and customized, so you
can pull framework updates without your changes ever conflicting.

## Quick start

**1. Copy this folder out of the submodule** into your project, next to
`octet-scss` — e.g. `src/my-theme/`:

```
src/
  octet-scss/     ← the framework submodule (leave it alone; pull updates freely)
  my-theme/       ← your copy of starter-theme (yours to edit)
```

**2. Give it an entry point.** Add `src/my-theme/main.scss`:

```scss
@use "../octet-scss/abstracts" as *;   // tokens, mixins, a11y, motion, reset, layout
@use "brand";                          // your palette (see step 4)
@use "../octet-scss/components";        // buttons, lists  (optional)

@use "index";                          // this theme's chrome
// @use "sections";  @use "pages";     // add your own content layers
```

**3. Point your app at it.** Import `src/my-theme/main.scss` wherever you load
styles (e.g. a layout component), instead of `octet-scss/main.scss`.

**4. Brand it.** Copy `_theme-colors.scss` to `_brand.scss` and override the
ramps / semantic tokens in `:root`. No Sass required — it's all custom
properties:

```scss
:root {
  --primary-500: rebeccapurple;   // whole-ramp swap, or…
  --link: #e8dda6;                // …a single-token nudge
}
```

Because your `brand` loads **after** the framework's `:root`, your values win.

## What's inside

| File | Role |
|---|---|
| `_theme-layout.scss` | `.app-shell` — nav rail + scrolling main column |
| `_nav.scss` / `_footer.scss` | Generic nav + footer |
| `_base-theme.scss` | Document color/background, scroll-lock |
| `_typography.scss` | Body text + a decoupled heading level/size system |
| `_theme-spacing.scss` | Vertical rhythm between blocks (the one spacing file) |
| `_class-utilities.scss` | `.no-wrap`, `.center`, `.max-width`, `.readability-width`, `.unstyled` |
| `_links.scss` / `_icons.scss` | Theme mixins — `link-styling`, `link-arrow-right`, inline-SVG helpers |
| `_theme-colors.scss` | An example re-brand (olive). Copy → `_brand.scss` and edit. |
| `_index.scss` | Loads the chrome. |

## Customizing

- **Swap or extend the chrome** — edit `_nav`, `_footer`, `_theme-layout`, etc.
- **Add your own content** — create `sections/` and `pages/` folders and load
  them from `main.scss`. Use the framework mixins (`scaled-spacing`,
  `fluid-font-size`, `from()`/`until()`, `elevation`, `transition`) and tokens
  (`var(--brand)`, `$radius-md`, `gridx()`, `$breakpoints`).
- **Name classes with BEM** — block · `block__element` · `block--modifier`,
  state on attributes, IDs for hooks not styling. See
  [Naming conventions](../README.md#naming-conventions) in the core README; the
  shipped chrome here (`.nav`, `.footer`, `.app-shell`) are worked examples.
- **Accessibility primitives ship in the core** — focus rings, `.sr-only`,
  skip links, and reduced-motion are on by default. Wiring them into real,
  conformant pages (semantic markup, contrast, focus order) is your part.

The framework core is documented in `octet-scss` itself; this theme only depends
on `@use "../octet-scss/abstracts"` (and, optionally, `components`).
