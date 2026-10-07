# Octet-SCSS starter-theme

A generic, ready-to-brand theme built on the Octet-SCSS core (app shell, nav, footer, base document styles, typography, block spacing, utilities, icon helpers). It's meant to be **copied out of the framework** and customized, so you can update the framework without your changes ever conflicting.

## Quick start

**1. Copy this folder** out of the installed package into your project — e.g. `src/my-theme/`:

```bash
cp -R node_modules/octet-scss/starter-theme src/my-theme
```

```
node_modules/
  octet-scss/     ← the framework (leave it alone; update freely)
src/
  my-theme/       ← your copy of starter-theme (yours to edit)
```

**2. Repoint its imports.** Inside the package the partials reach the core with `../abstracts`; in your project they need the package path:

```bash
sed -i.bak 's#"\.\./abstracts#"octet-scss/abstracts#' src/my-theme/*.scss && rm src/my-theme/*.bak
```

(That assumes Sass resolves `node_modules` — see [Install](../README.md#install) in the core README. With the `pkg:` importer, use `"pkg:octet-scss/abstracts` as the replacement.)

**3. Give it an entry point.** Add `src/my-theme/main.scss`:

```scss
@use "octet-scss/abstracts" as *;      // tokens, mixins, a11y, motion, reset, layout
@use "brand";                          // your palette (see step 5)
@use "octet-scss/components";          // buttons, lists  (optional)

@use "index";                          // this theme's chrome
// @use "sections";  @use "pages";     // add your own content layers
```

**4. Point your app at it.** Import `src/my-theme/main.scss` wherever you load styles (e.g. a layout component), instead of `octet-scss`.

**5. Brand it.** Copy `_theme-colors.scss` to `_brand.scss` and override the ramps / semantic tokens in `:root`. No Sass required — it's all custom properties:

```scss
:root {
  --primary-500: rebeccapurple;   // whole-ramp swap, or…
  --link: #e8dda6;                // …a single-token nudge
}
```

Because your `brand` loads **after** the framework's `:root`, your values win.

## What's inside

| File                          | Role                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| `_theme-layout.scss`          | `.app-shell` — nav rail + scrolling main column              |
| `_nav.scss` / `_footer.scss`  | Generic nav + footer                                         |
| `_base-theme.scss`            | Document color/background, scroll-lock                       |
| `_typography.scss`            | Body text + a decoupled heading level/size system            |
| `_theme-spacing.scss`         | Vertical rhythm between blocks (the one spacing file)        |
| `_class-utilities.scss`       | `.no-wrap`, `.center`, `.max-width`, `.readability-width`, `.unstyled` |
| `_icons.scss`                 | The theme's own icon + treatment (`link-arrow-right`). The generic inline-SVG helpers live in `abstracts/_icons.scss` |
| `_theme-colors.scss`          | An example re-brand (ember). Copy → `_brand.scss` and edit.  |
| `_index.scss`                 | Loads the chrome.                                            |

## Customizing

- **Swap or extend the chrome** — edit `_nav`, `_footer`, `_theme-layout`, etc.
- **Add your own content** — create `sections/` and `pages/` folders and load them from `main.scss`. Use the framework mixins (`scaled-spacing`, `fluid-font-size`, `from()`/`until()`, `elevation`, `transition`) and tokens (`var(--brand)`, `$radius-md`, `gridx()`, `$breakpoints`).
- **Name classes with BEM** — block · `block__element` · `block--modifier`, state on attributes, IDs for hooks not styling. See [Naming conventions](../README.md#naming-conventions) in the core README; the shipped chrome here (`.nav`, `.footer`, `.app-shell`) are worked examples.
- **Accessibility primitives ship in the core** — focus rings, `.sr-only`, skip links, and reduced-motion are on by default. Wiring them into real, conformant pages (semantic markup, contrast, focus order) is your part.

The framework core is documented in `octet-scss` itself; this theme only depends on `@use "octet-scss/abstracts"` (and, optionally, `components`).
