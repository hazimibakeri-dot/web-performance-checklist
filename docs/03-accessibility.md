# Accessibility

The target for most sites is **WCAG 2.2 Level AA**. WCAG 2.2 is a W3C Recommendation; the current
version is dated 12 December 2024. It adds nine success criteria to WCAG 2.1 and removes 4.1.1
Parsing as obsolete.

Automated tools find only part of all accessibility problems. Use them early and often, and
combine them with keyboard and screen reader testing.

## Checklist

### Structure and semantics

- [ ] The page has a `lang` attribute and a descriptive, unique `<title>`.
- [ ] Landmarks are used: `<header>`, `<nav>`, `<main>`, `<footer>`; one `<main>` per page.
- [ ] Headings form a logical outline, with one `<h1>`.
- [ ] Native elements are used before ARIA: `<button>` for actions, `<a href>` for navigation.
- [ ] Lists, tables and forms use their semantic elements.

### Text alternatives and media

- [ ] Informative images have meaningful `alt` text; decorative images use `alt=""`.
- [ ] Icon-only buttons have an accessible name.
- [ ] Videos have captions; audio has a transcript.

### Keyboard and focus

- [ ] Everything works with the keyboard alone, in a logical order, with no keyboard traps.
- [ ] Focus is always visible (do not remove outlines without a clear replacement).
- [ ] Focused elements are not hidden behind sticky headers or banners (2.4.11, AA).
- [ ] A skip link lets users bypass repeated navigation.
- [ ] Dialogs move focus inside on open, trap it while open and return it on close.

### Visual design

- [ ] Text contrast is at least 4.5:1, or 3:1 for large text (1.4.3, AA).
- [ ] Interface components and meaningful graphics have at least 3:1 contrast (1.4.11, AA).
- [ ] Colour is never the only way information is conveyed.
- [ ] Content reflows without horizontal scrolling at a width of 320 CSS pixels (1.4.10, AA).
- [ ] Text can be resized to 200% without loss of content or function.
- [ ] Pointer targets are at least 24 × 24 CSS pixels, or have enough spacing (2.5.8, AA).
- [ ] Animation respects `prefers-reduced-motion`; nothing flashes more than three times per second.

### Forms

- [ ] Every input has a visible, programmatically associated label.
- [ ] Errors are described in text, linked to the field, and say how to fix them.
- [ ] Required fields are indicated in text, not by colour alone.
- [ ] Users are not asked to re-enter information they already provided in the same process (3.3.7, A).
- [ ] Sign-in does not depend on a cognitive test such as remembering or transcribing a code,
      unless an alternative or assistance (such as paste or a password manager) is available (3.3.8, AA).
- [ ] Actions that use dragging have a single-pointer alternative (2.5.7, AA).

### Dynamic content

- [ ] Status messages that appear without a focus change are announced (`role="status"` or
      `aria-live`).
- [ ] Single-page app navigations update the title and move focus or announce the new content.
- [ ] Help mechanisms (contact details, chat) appear in a consistent place across pages (3.2.6, A).

## Examples

Accessible field with an error:

```html
<label for="email">Email address</label>
<input
  id="email"
  type="email"
  autocomplete="email"
  aria-invalid="true"
  aria-describedby="email-error"
/>
<p id="email-error">Enter an email address in the format name@example.com.</p>
```

Visible focus that survives custom styling, and reduced motion:

```css
:focus-visible {
  outline: 2px solid #1d4ed8;
  outline-offset: 2px;
}

/* Keep sticky headers from covering focused elements and anchor targets. */
html {
  scroll-padding-top: 5rem;
}

@media (prefers-reduced-motion: reduce) {
  *,
  ::before,
  ::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Skip link:

```html
<a class="skip-link" href="#main">Skip to main content</a>
…
<main id="main" tabindex="-1">…</main>
```

## Testing methods

1. **Automated scan** of each template with axe DevTools, Lighthouse or WAVE.
2. **Keyboard pass**: Tab, Shift+Tab, Enter, Space, Escape and arrow keys through every
   interactive element. Check that focus is visible and the order makes sense.
3. **Zoom and reflow**: 200% text zoom, and a 320 CSS pixel wide viewport.
4. **Screen readers**: at least one desktop pairing (NVDA with Firefox or Chrome on Windows,
   VoiceOver with Safari on macOS) and one mobile pairing (VoiceOver on iOS, TalkBack on Android).
5. **Contrast**: check text and component colours, including hover, focus and disabled states.
6. **Content review**: link text, headings, alternative text and error messages make sense out of
   context.

Automate what you can in CI (for example axe-core in component or end-to-end tests), but do not
treat a clean automated report as proof of conformance.

## Tools

- [axe DevTools](https://www.deque.com/axe/devtools/) and the open-source
  [axe-core](https://github.com/dequelabs/axe-core) engine
- [WAVE](https://wave.webaim.org/)
- [Lighthouse accessibility audits](https://developer.chrome.com/docs/lighthouse/accessibility/scoring)
- [NVDA](https://www.nvaccess.org/) screen reader (Windows, free)

## References

- [Web Content Accessibility Guidelines (WCAG) 2.2](https://www.w3.org/TR/WCAG22/)
- [How to Meet WCAG (Quick Reference)](https://www.w3.org/WAI/WCAG22/quickref/)
- [What's new in WCAG 2.2](https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/)
- [ARIA Authoring Practices Guide](https://www.w3.org/WAI/ARIA/apg/)
- [Evaluating web accessibility](https://www.w3.org/WAI/test-evaluate/)
- [`prefers-reduced-motion`](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion) (MDN)
