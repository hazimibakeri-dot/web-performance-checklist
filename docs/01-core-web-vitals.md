# Core Web Vitals

Core Web Vitals are three user-centred metrics that Google recommends every site measure: how
quickly the main content appears, how quickly the page responds to input, and how visually stable
it is while loading.

## Thresholds

| Metric | Measures | Good | Needs improvement | Poor |
| --- | --- | --- | --- | --- |
| **LCP**, Largest Contentful Paint | Loading: when the largest image or text block in the viewport is rendered | ≤ 2.5 s | 2.5–4 s | > 4 s |
| **INP**, Interaction to Next Paint | Responsiveness: delay between user input and the next frame | ≤ 200 ms | 200–500 ms | > 500 ms |
| **CLS**, Cumulative Layout Shift | Visual stability: unexpected movement of visible content | ≤ 0.1 | 0.1–0.25 | > 0.25 |

A page meets a threshold when the **75th percentile** of page loads, measured separately for
mobile and desktop, is in the "good" range. INP replaced First Input Delay (FID) as a Core Web
Vital on 12 March 2024.

Two supporting metrics help diagnose problems but are not Core Web Vitals themselves:
**Time to First Byte** (TTFB, roughly 0.8 s or less is good) and **First Contentful Paint** (FCP,
1.8 s or less is good).

## Checklist

- [ ] Measure Core Web Vitals from real users (field data), not only in lab tools.
- [ ] Review mobile and desktop results separately.
- [ ] LCP is 2.5 s or less at the 75th percentile.
- [ ] INP is 200 ms or less at the 75th percentile.
- [ ] CLS is 0.1 or less at the 75th percentile.
- [ ] The LCP element is known for key templates, and its resource is discoverable in the HTML.
- [ ] The LCP image is not lazy-loaded.
- [ ] Images, videos, ads and embeds reserve space (width and height, or `aspect-ratio`).
- [ ] No content is inserted above existing content unless it responds to a user action.
- [ ] Long tasks on the main thread are broken up, especially in input handlers.
- [ ] Regressions are caught in CI with lab tests and monitored in production with field data.

## Largest Contentful Paint (LCP)

LCP time can be split into four parts. Find the largest one before optimising:

1. **Time to first byte**: server response time, redirects and connection setup.
2. **Resource load delay**: time between TTFB and the browser starting to load the LCP resource.
3. **Resource load duration**: time spent downloading the LCP resource.
4. **Element render delay**: time between the resource finishing and the element being painted.

Common fixes:

- Make the LCP image discoverable in the initial HTML. Images set with CSS `background-image` or
  injected by JavaScript are found late; preload them if you cannot change the markup.
- Mark the LCP image as high priority and never lazy-load it:

  ```html
  <img src="/hero-1200.avif" width="1200" height="630" alt="…" fetchpriority="high" />
  ```

- Serve correctly sized, modern image formats (see [Images](05-images.md)).
- Reduce server response time with caching and a CDN (see [Caching](07-caching.md)).
- Remove render-blocking CSS and JavaScript that delay the first render.
- Avoid hiding the main content until JavaScript runs (for example with an opacity animation or
  client-only rendering).

## Interaction to Next Paint (INP)

INP observes the latency of clicks, taps and key presses during the whole visit and reports one of
the longest. Each interaction's latency has three parts: **input delay** (the main thread is busy),
**processing time** (your event handlers) and **presentation delay** (rendering the next frame).

Common fixes:

- Break up long tasks (anything over 50 ms) so the browser can respond to input
  (see [JavaScript](06-javascript.md)).
- Do the minimum in event handlers: update the UI first, then defer non-urgent work.
- Debounce expensive work triggered by typing, scrolling or resizing.
- Reduce DOM size and avoid layout thrashing (reading layout after writing styles in a loop).
- Audit third-party scripts, which often run long tasks.

## Cumulative Layout Shift (CLS)

CLS groups layout shifts into **session windows**: shifts less than 1 second apart, with each
window capped at 5 seconds. The score is the largest window. Shifts within 500 ms of a discrete
user input (a tap, click or key press) are excluded; scrolling is not treated as such input.

Common fixes:

- Give images and videos `width` and `height` attributes, or use CSS `aspect-ratio`.
- Reserve space for ads, embeds and late-loading components with a minimum height.
- Avoid inserting banners above existing content after load; overlay them instead.
- Reduce font-swap shifts with `font-display`, preloading key fonts and fallback font metric
  overrides such as `size-adjust`.
- Animate with `transform` and `opacity` rather than properties that change layout.

## Measuring in the field

Field data reflects real devices, networks and behaviour. Sources:

- **Chrome User Experience Report (CrUX)**: data from opted-in Chrome users, available through
  PageSpeed Insights, Search Console and the CrUX API. It only covers Chrome and only sites with
  enough traffic.
- **Real user monitoring (RUM)** with the `web-vitals` library:

```js
import { onCLS, onINP, onLCP } from 'web-vitals';

function sendToAnalytics(metric) {
  const body = JSON.stringify({
    name: metric.name, // "CLS", "INP" or "LCP"
    value: metric.value,
    rating: metric.rating, // "good", "needs-improvement" or "poor"
    id: metric.id,
  });
  // sendBeacon survives page unloads; fall back to fetch with keepalive.
  if (!navigator.sendBeacon?.('/analytics', body)) {
    fetch('/analytics', { body, method: 'POST', keepalive: true });
  }
}

onCLS(sendToAnalytics);
onINP(sendToAnalytics);
onLCP(sendToAnalytics);
```

Respect privacy rules and consent requirements for any analytics you collect.

## Testing methods

| Method | Use it for |
| --- | --- |
| PageSpeed Insights | Field data (when available) and a Lighthouse lab run for one URL |
| Search Console Core Web Vitals report | Groups of URLs failing in the field |
| Lighthouse (DevTools or CI) | Repeatable lab tests. It cannot measure INP; Total Blocking Time is the closest lab signal |
| Chrome DevTools Performance panel | Finding the LCP element, long tasks, layout shifts and slow interactions |
| `web-vitals` library | Real user monitoring in your own analytics |

Lab results vary with hardware and network throttling. Compare runs on the same machine and
settings, and treat field data as the source of truth.

## References

- [Web Vitals](https://web.dev/articles/vitals) (web.dev)
- [Largest Contentful Paint](https://web.dev/articles/lcp) and [Optimize LCP](https://web.dev/articles/optimize-lcp)
- [Interaction to Next Paint](https://web.dev/articles/inp) and [Optimize INP](https://web.dev/articles/optimize-inp)
- [Cumulative Layout Shift](https://web.dev/articles/cls) and [Optimize CLS](https://web.dev/articles/optimize-cls)
- [INP becomes a Core Web Vital on March 12](https://web.dev/blog/inp-cwv-march-12)
- [Time to First Byte](https://web.dev/articles/ttfb) and [First Contentful Paint](https://web.dev/articles/fcp)
- [web-vitals library](https://github.com/GoogleChrome/web-vitals)
- [Chrome UX Report documentation](https://developer.chrome.com/docs/crux)
- [PageSpeed Insights](https://pagespeed.web.dev/)
