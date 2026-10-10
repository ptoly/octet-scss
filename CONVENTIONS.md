# octet-scss conventions

Rules for writing styles in a project built on octet-scss, by a person or an
AI coding assistant. Written to be followed literally. The [README](README.md)
explains the reasoning; this file is the checklist.

**Using an AI assistant?** Add this line to your project's `CLAUDE.md`,
`AGENTS.md`, or equivalent:

```
Follow node_modules/octet-scss/CONVENTIONS.md for all styles.
```

## 1. Where things live

| Location | Owner | Rule |
| --- | --- | --- |
| `node_modules/octet-scss/` | the framework | Never edit. Update it with npm. |
| Your theme folder (copied from `starter-theme/`) | you | Edit freely. Setup steps: [`starter-theme/README.md`](starter-theme/README.md#quick-start). Copy the folder; don't `@use "octet-scss/starter-theme"`. |
| `<theme>/_theme-spacing.scss` | you | The only spacing file. See section 3. |
| `<theme>/_theme-layout.scss` | you | The only layout file. See section 4. |
| `<theme>/_brand.scss` | you | Palette overrides, as custom properties in `:root`. |
| One partial per BEM block | you | Component styles: color, type, borders. Spacing or layout only when it belongs to that component alone. |

**Spacing or layout? The property decides.**

| Property | File |
| --- | --- |
| `margin`, `padding`, `gap` (the distance between and around things) | `_theme-spacing.scss` |
| `width` / `height`, `grid` / `flex`, alignment, position (the size and arrangement of things) | `_theme-layout.scss` |

One component's styling can span both files: an input's height lives in
layout, its padding in spacing. That's intended. Consistency across the site
beats convenience on one component.

## 2. Load order

Your entry file is a list of `@use` lines, in this order:

1. `octet-scss/abstracts` as `*`: tokens, mixins, functions. Emits no CSS,
   so any partial may `@use` it.
2. `octet-scss/base`: reset, a11y, `:root` colors, document defaults.
3. `octet-scss/components` (optional), your brand, your theme, your
   component partials.
4. `octet-scss/utilities` (optional): **last**, so utility classes win.

## 3. Spacing

- Spacing means `margin`, `padding` and `gap`. Spacing that has to be
  consistent goes in `_theme-spacing.scss`, applied by selector:
  - rhythm **between** blocks (sections, cards, list items)
  - gutters and insets **shared** by several components (for example, one
    horizontal inset for a card header, a toolbar and table cells)
- Padding that belongs to **one** component alone stays in that component's
  partial.
- Space blocks with **`margin-bottom`**, mostly. Each block pushes the next
  one down.
- Vertical values: `gridx($n)` or `scaled-spacing()` (stepped, stays on the
  8pt baseline). Horizontal values: `fluid-gutter()` (fluid).
- **Don't** create a second spacing file, or a new layer of spacing mixins or
  variables. Extend `_theme-spacing.scss`.

```scss
// _theme-spacing.scss: yes
.card__header,
.table-toolbar,
.table__cell {
  @include fluid-gutter(padding-inline, 16px, 24px);
}

// _card.scss: no, this belongs in _theme-spacing.scss
@mixin card-gutter { … }
```

## 4. Layout

- Layout means `width` / `height`, `grid` / `flex`, alignment and position.
  Layout that has to be consistent goes in `_theme-layout.scss`, applied by
  selector:
  - page layout: the app shell, columns, reflow points
  - sizing and alignment **shared** by like components (for example, one
    control height for inputs, selects and buttons)
- Layout that belongs to **one** component alone stays in its partial.
- **Don't** create a second layout file. Extend `_theme-layout.scss`.

```scss
// _theme-layout.scss: yes
input:not([type="checkbox"], [type="radio"]),
select,
.btn {
  block-size: gridx(5);
}
```

## 5. Units and type

- Pixels for UI and display type. Body copy (p, lists, captions) uses
  `rem(<px>)`, so it honors the reader's font-size setting.
- Headlines and display type: `fluid-font-size()`. Body copy: fixed `rem()`,
  not fluid.

## 6. Color

- Components use semantic custom properties only: `var(--text-primary)`,
  `var(--surface-card)`, `var(--border-subtle)`, `var(--brand)`, and so on.
- **No hex, rgb or hsl values in component partials.** To change the
  palette, override custom properties in `_brand.scss`.
- Don't add color helper functions (no `gray()`).

## 7. Runtime vs. compile-time values

- CSS custom properties for anything that can change at runtime (colors,
  theme values).
- SCSS variables only for compile-time values, such as `$breakpoints`,
  because media queries can't read `var()`.

## 8. Responsive and interaction

- Media queries: `from($bp)` / `until($bp)` with the shared `$breakpoints`
  scale (360, 540, 720, 900, 1280, 1366, 1441px). Don't invent breakpoints
  in components.
- Hover styles go inside `@include hover-only`. Never gate hover on a width
  breakpoint. Use `touch-only` for touch-specific styles.
- Prefer `overflow: clip` to `overflow: hidden`.
- Transitions: `@include transition(...)` with the motion tokens.

## 9. Naming

- BEM: `.block`, `.block__element`, `.block--modifier`. Elements don't nest
  in the name (`.card__title`, never `.card__header__title`). A modifier is
  added alongside its base class.
- State lives on attributes (`[aria-expanded]`, `[aria-current]`,
  `[data-open]`). Use an `.is-*` class only when no attribute fits.
- Never style with IDs.
- Single-purpose helpers belong in utilities (`.center`, `.no-wrap`,
  `.max-width`), not in new one-off classes.

## 10. Before adding anything new

1. Check `abstracts` first. It likely has it: `gridx`, `rem`,
   `scaled-spacing`, `fluid-gutter`, `fluid-font-size`, `from` / `until`,
   `hover-only`, `elevation`, `transition`, `focus-visible`, `sr-only`,
   `$radius-*`, `$z-*`, `$duration-*`. Full list in the
   [README](README.md#mixins--functions).
2. Add a new token or mixin only when the same value repeats across several
   components, and ask the person you're working with first.

## 11. Done checklist

- [ ] No spacing outside `_theme-spacing.scss` except one-component padding
- [ ] Shared sizing and alignment in `_theme-layout.scss`; like controls share one height
- [ ] No raw color values in components
- [ ] Vertical sizes from `gridx()` / `scaled-spacing()`
- [ ] Hover inside `hover-only`
- [ ] BEM names; state on attributes; no ID selectors
- [ ] No new tokens, mixins, spacing or layout files without asking
