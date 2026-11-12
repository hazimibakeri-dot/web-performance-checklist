# Browser compatibility

Decide which browsers you support, write that down, and let the decision drive your build
targets, feature choices and testing.

## Baseline

[Baseline](https://web.dev/baseline) is a shared way to describe whether a web platform feature is
safe to use. It tracks a core set of browsers: Chrome and Chrome for Android, Edge, Firefox and
Firefox for Android, and Safari on macOS and iOS.

- **Newly available**: the feature works in the latest version of every core browser.
- **Widely available**: 30 months have passed since the feature became newly available, so it
  also works in most older versions still in use.

Baseline badges appear on MDN reference pages and in other tools, which makes them a convenient
input for a support policy.

## Checklist

- [ ] A written support policy names the browsers and versions you test, for example "Baseline
      widely available, plus the latest two versions of each core browser".
- [ ] Build tools (transpiler, CSS processing, polyfills) target the same browser list as the
      policy.
- [ ] Features newer than the policy are used only as progressive enhancements, guarded by
      feature detection (`@supports` in CSS, property checks in JavaScript).
- [ ] Polyfills are added deliberately and only for features you use.
- [ ] Every core browser engine (Chromium, Gecko and WebKit) is tested before release, including
      Safari on iOS.
- [ ] Critical user journeys still work, perhaps with less polish, when an enhancement is
      unavailable.

## Examples

A browser list shared by build tools such as Autoprefixer, Babel and many bundler plugins
(`package.json`):

```json
{
  "browserslist": ["defaults"]
}
```

Run `npx browserslist` to see which browsers a query includes, and review it periodically.

Feature detection in CSS:

```css
/* Works everywhere: a neutral border on every form row. */
.form-row {
  border-inline-start: 4px solid transparent;
}

/* Enhancement: highlight rows that contain an invalid field, where :has() is supported. */
@supports selector(:has(*)) {
  .form-row:has(:user-invalid) {
    border-inline-start-color: #b91c1c;
  }
}
```

Feature detection in JavaScript:

```js
if ('share' in navigator) {
  shareButton.hidden = false;
  shareButton.addEventListener('click', () => {
    navigator.share({ title: document.title, url: location.href });
  });
}
```

## Testing methods

- Automate end-to-end tests across the three engines with a tool such as Playwright, which can
  run Chromium, Firefox and WebKit.
- Test on real iOS and Android devices for touch, viewport and keyboard behaviour; a cloud device
  service can help cover models you do not own.
- Check MDN compatibility tables or Can I use before adopting a new feature.
- Watch error monitoring for browser-specific exceptions after each release.

## Tools

- [Can I use](https://caniuse.com/)
- [Browserslist](https://github.com/browserslist/browserslist)
- [Playwright](https://playwright.dev/)

## References

- [Baseline](https://web.dev/baseline) (web.dev)
- [`@supports`](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports) (MDN)
- [Implementing feature detection](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Testing/Feature_detection) (MDN)
- [Progressive enhancement](https://developer.mozilla.org/en-US/docs/Glossary/Progressive_Enhancement) (MDN glossary)
