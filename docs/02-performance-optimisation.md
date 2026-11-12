# Performance optimisation

General techniques for loading less, loading it sooner and doing less work in the browser. Measure
before and after each change: an optimisation that does not move a real metric is complexity
without benefit.

## Checklist

### Measure first

- [ ] Field data (CrUX or real user monitoring) shows which pages and metrics need work.
- [ ] A performance budget exists for key pages (for example JavaScript size, LCP, total requests).
- [ ] Lab tests run in CI so regressions are caught before release.

### Server and network

- [ ] Responses are compressed with Brotli or gzip (text assets: HTML, CSS, JS, JSON, SVG).
- [ ] Static assets are served from a CDN close to users.
- [ ] HTTP/2 or HTTP/3 is enabled.
- [ ] Redirect chains are avoided, especially on the first navigation.
- [ ] Caching headers are set correctly (see [Caching](07-caching.md)).

### Critical rendering path

- [ ] CSS needed for the first render is small; non-critical CSS is deferred or split by route.
- [ ] Scripts use `defer`, `async` or `type="module"` instead of blocking the parser.
- [ ] Connections to required third-party origins are warmed up with `preconnect`.
- [ ] Late-discovered critical resources (LCP image, key font) are preloaded.
- [ ] Preloading is used sparingly: every preload competes for bandwidth.

### Fonts

- [ ] Fonts are WOFF2 and subset to the characters you need.
- [ ] `font-display` is set (`swap` or `optional`).
- [ ] At most one or two critical fonts are preloaded, with `crossorigin`.
- [ ] Fallback fonts are tuned with `size-adjust`, `ascent-override` and related descriptors to
      limit layout shift.

### Third parties

- [ ] Every third-party script has an owner and a documented purpose.
- [ ] Non-essential third parties load after the page is interactive or on user interaction.
- [ ] Embeds (video, maps, social posts) use a lightweight placeholder until requested.

### Back/forward cache

- [ ] Pages are eligible for the back/forward cache: no `unload` handlers, and no
      `Cache-Control: no-store` on pages that do not need it.

## Examples

Resource hints in the document `<head>`:

```html
<!-- Start the connection early for a third-party origin you will definitely use -->
<link rel="preconnect" href="https://cdn.example.com" crossorigin />

<!-- Preload a critical font that CSS would otherwise reveal late -->
<link rel="preload" href="/fonts/inter-var.woff2" as="font" type="font/woff2" crossorigin />

<!-- Non-blocking scripts: defer keeps execution order, module scripts are deferred by default -->
<script src="/js/app.js" defer></script>
<script type="module" src="/js/main.js"></script>
```

Font loading with a metric-adjusted fallback:

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/inter-var.woff2') format('woff2');
  font-display: swap;
  font-weight: 100 900;
}

/* A local fallback scaled to occupy roughly the same space as the web font. Tune the values
   for your font pairing using a metrics tool, then verify visually. */
@font-face {
  font-family: 'Inter Fallback';
  src: local('Arial');
  size-adjust: 107%;
}

body {
  font-family: 'Inter', 'Inter Fallback', sans-serif;
}
```

Loading a third-party widget only when the user asks for it:

```html
<button type="button" id="load-chat">Chat with support</button>
<script type="module">
  document.getElementById('load-chat').addEventListener(
    'click',
    async () => {
      const { initChat } = await import('/js/chat-widget.js');
      initChat();
    },
    { once: true },
  );
</script>
```

## Testing methods

- Run Lighthouse on key templates and compare against your budget.
- Use the DevTools **Network** panel with throttling and a disabled cache to see the request
  waterfall, priorities and render-blocking resources.
- Use the DevTools **Performance** panel to find long tasks and slow rendering.
- Check the **Application → Back/forward cache** panel in Chrome DevTools to see why a page is
  not eligible.
- Track results over time; one lab run is not a trend.

## Tools

- [Lighthouse](https://developer.chrome.com/docs/lighthouse/overview) and
  [Lighthouse CI](https://github.com/GoogleChrome/lighthouse-ci)
- [Chrome DevTools Performance panel](https://developer.chrome.com/docs/devtools/performance)
- [WebPageTest](https://www.webpagetest.org/) for multi-location and connection testing

## References

- [Optimize Largest Contentful Paint](https://web.dev/articles/optimize-lcp)
- [Best practices for fonts](https://web.dev/articles/font-best-practices)
- [`font-display`](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display) (MDN)
- [`rel="preload"`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/preload) (MDN)
- [`Content-Encoding`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Content-Encoding) (MDN)
- [Back/forward cache](https://web.dev/articles/bfcache)
- [Rendering on the web](https://web.dev/articles/rendering-on-the-web)
