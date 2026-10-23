# Recipe: Radix Primitives with octet

Radix Primitives supply behavior: keyboard handling, focus management, ARIA. Octet supplies every style. Do not use Radix Themes or any Radix CSS.

## Rules

1. **Wrap each primitive in a local component.** The rest of the app never imports Radix directly. Swapping to React Aria later touches one folder.
2. **Style with BEM classes and attribute selectors.** Pass `className` to each Radix part. Radix sets `data-state`, `data-disabled` and `data-orientation`, which fits octet's rule that state lives on attributes.
3. **Put each rule in the file its property belongs to.** Spacing in the spacing theme file, layout in the layout theme file, component rules in the component partial.
4. **Animate with Radix's CSS variables.** For example `--radix-accordion-content-height`. Wrap the animation in `prefers-reduced-motion`. Animating height this way is the one exception to the "no height rules" convention. Say so in the partial.
5. **Do not rely on DOM nesting.** Portaled parts (Dialog, Select, Popover) render outside your app root. Style them from global tokens and their own BEM classes, never from a parent selector.
6. **Keep the accessibility floors.** 16px type, 44px targets, 3:1 control borders, visible focus. Radix gives you behavior, not contrast or target size.

## Example: Accordion

```tsx
import * as Accordion from '@radix-ui/react-accordion';

export function Faq({ items }: { items: { q: string; a: string }[] }) {
  return (
    <Accordion.Root className="faq" type="single" collapsible>
      {items.map(({ q, a }) => (
        <Accordion.Item className="faq__item" value={q} key={q}>
          <Accordion.Header className="faq__heading">
            <Accordion.Trigger className="faq__trigger">{q}</Accordion.Trigger>
          </Accordion.Header>
          <Accordion.Content className="faq__content">{a}</Accordion.Content>
        </Accordion.Item>
      ))}
    </Accordion.Root>
  );
}
```

```scss
.faq__trigger {
  // padding and 44px target come from the theme spacing file
  border: 1px solid var(--color-border);

  &[data-state='open'] {
    // open styles here
  }
}

.faq__content {
  overflow: hidden;

  &[data-state='open'] {
    animation: faq-open 200ms ease-out;
  }

  &[data-state='closed'] {
    animation: faq-close 200ms ease-out;
  }

  @media (prefers-reduced-motion: reduce) {
    animation: none;
  }
}

@keyframes faq-open {
  from { height: 0; }
  to { height: var(--radix-accordion-content-height); }
}

@keyframes faq-close {
  from { height: var(--radix-accordion-content-height); }
  to { height: 0; }
}
```

Token names above are placeholders. Use the project's real ones.

## Status

Worked out in finspectcheck. Not yet shipped as an octet component. A Tailwind theme-file recipe (octet tokens mapped into a Tailwind theme, Preflight off, octet in a cascade layer) is the planned companion for 0.5.
