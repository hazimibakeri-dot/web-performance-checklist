# Responsive design

A responsive page adapts to any screen size, input method and user preference without losing
content or functionality.

## Checklist

### Layout

- [ ] The viewport meta tag is present: `<meta name="viewport" content="width=device-width, initial-scale=1">`.
- [ ] Zooming is not disabled (no `user-scalable=no` or `maximum-scale=1`).
- [ ] There is no horizontal scrolling at 320 CSS pixels wide, except for content that needs two
      dimensions, such as data tables, maps and diagrams.
- [ ] Layouts use flexible units and CSS Grid or Flexbox rather than fixed pixel widths.
- [ ] Components that appear in different contexts adapt with container queries where useful.
- [ ] Long words, URLs and code blocks do not break the layout (`overflow-wrap`, scrollable
      `<pre>`).

### Typography and spacing

- [ ] Font sizes use `rem` so they follow the user's browser setting.
- [ ] Body text is comfortable to read on small screens (the browser default of 16 px is a common
      baseline), with a moderate line length and generous line height.
- [ ] Fluid type uses `clamp()` with a minimum that stays readable.

### Input and preferences

- [ ] Touch targets are at least 24 × 24 CSS pixels (WCAG 2.5.8); a larger size is more
      comfortable for primary actions.
- [ ] Hover-only interactions have a tap or keyboard equivalent; use `@media (hover: hover)` for
      hover enhancements.
- [ ] Inputs use the right `type`, `inputmode` and `autocomplete` values so mobile keyboards help.
- [ ] Dark mode (`prefers-color-scheme`) and reduced motion (`prefers-reduced-motion`) are
      respected where the design supports them.
- [ ] Full-height layouts account for mobile browser toolbars (`dvh`/`svh` units).

## Examples

```css
/* Fluid type with readable limits */
h1 {
  font-size: clamp(2rem, 1.2rem + 3vw, 3.5rem);
}

/* Intrinsic grid: as many columns as fit, never narrower than 16rem */
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(16rem, 100%), 1fr));
  gap: 1.5rem;
}

/* Container query: the card changes layout based on its own width, not the viewport */
.card-wrapper {
  container-type: inline-size;
}

@container (min-width: 32rem) {
  .card {
    display: grid;
    grid-template-columns: 12rem 1fr;
  }
}

/* Hover effects only for devices that can hover */
@media (hover: hover) {
  .card:hover {
    transform: translateY(-2px);
  }
}

/* Full-height section that respects mobile browser UI */
.hero {
  min-height: 100svh;
}

/* Prevent long URLs from overflowing */
.prose {
  overflow-wrap: anywhere;
}
```

## Testing methods

- Use device emulation in browser DevTools, and drag the viewport through every width, not only
  common breakpoints.
- Test on at least one real phone and one tablet; emulators do not reproduce touch, performance
  or browser toolbars exactly.
- Set the browser's default font size to a larger value and zoom to 200%.
- Test landscape orientation and split-screen or narrow desktop windows.
- Run an automated check for horizontal overflow at 320 px wide (for example with Playwright).

## References

- [Learn Responsive Design](https://web.dev/learn/design) (web.dev course)
- [Viewport meta tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Viewport_meta_element) (MDN)
- [Container size and style queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_size_and_style_queries) (MDN)
- [`clamp()`](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp) (MDN)
- [Using media queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_media_queries/Using_media_queries) (MDN)
- [Understanding SC 1.4.10 Reflow](https://www.w3.org/WAI/WCAG22/Understanding/reflow.html)
- [Understanding SC 2.5.8 Target Size (Minimum)](https://www.w3.org/WAI/WCAG22/Understanding/target-size-minimum.html)
