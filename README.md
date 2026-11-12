# Web Performance Checklist

Practical, referenced checklists for shipping fast, accessible, discoverable and secure websites.
Each guide explains why an item matters, shows working code, describes how to test it and links
to authoritative documentation from web.dev, MDN, the W3C, Google Search Central and OWASP.

> **No invented numbers.** Thresholds and requirements come from the cited sources. The guides do
> not quote benchmark results, because real gains depend on your site; measure before and after
> every change.

## Guides

| # | Guide | Covers |
| --- | --- | --- |
| 1 | [Core Web Vitals](docs/01-core-web-vitals.md) | LCP, INP and CLS thresholds, diagnosis, field measurement |
| 2 | [Performance optimisation](docs/02-performance-optimisation.md) | Critical rendering path, fonts, resource hints, third parties, back/forward cache |
| 3 | [Accessibility](docs/03-accessibility.md) | WCAG 2.2 AA essentials, keyboard and focus, forms, testing with assistive technology |
| 4 | [Technical SEO](docs/04-technical-seo.md) | Crawling, indexing, canonical URLs, sitemaps, structured data |
| 5 | [Image optimisation](docs/05-images.md) | Formats, responsive images, lazy loading, LCP images |
| 6 | [JavaScript optimisation](docs/06-javascript.md) | Code splitting, long tasks, yielding, Web Workers |
| 7 | [Caching](docs/07-caching.md) | `Cache-Control`, versioned assets, HTML revalidation, CDNs |
| 8 | [Responsive design](docs/08-responsive-design.md) | Fluid layouts, container queries, touch targets, user preferences |
| 9 | [Browser compatibility](docs/09-browser-compatibility.md) | Baseline, support policies, feature detection, cross-engine testing |
| 10 | [Deployment readiness](docs/10-deployment-readiness.md) | CI, secrets, hosting configuration, monitoring, rollback |
| 11 | [Security fundamentals](docs/11-security-fundamentals.md) | HTTPS and HSTS, CSP and other headers, cookies, dependencies |

For a one-page version to use during reviews, see [CHECKLIST.md](CHECKLIST.md).

## Quick reference: Core Web Vitals

| Metric | Good | Poor |
| --- | --- | --- |
| Largest Contentful Paint (LCP) | ≤ 2.5 s | > 4 s |
| Interaction to Next Paint (INP) | ≤ 200 ms | > 500 ms |
| Cumulative Layout Shift (CLS) | ≤ 0.1 | > 0.25 |

Assessed at the 75th percentile of page loads, separately for mobile and desktop. Source:
[web.dev/articles/vitals](https://web.dev/articles/vitals).

## How to use these checklists

1. **Measure first.** Use field data (Chrome UX Report or your own real user monitoring) to find
   the pages and metrics that actually need work.
2. **Pick a guide** for the problem area and work through its checklist.
3. **Verify** each change with the testing methods described in the guide.
4. **Automate** what you can (linting, accessibility checks, performance budgets) so regressions
   are caught in CI.

Not every item applies to every project. Treat the lists as prompts for a decision, not as rules.

## Keeping it accurate

Web platform guidance changes. Each guide links to its sources so claims can be checked, and the
repository includes tooling to keep the Markdown consistent and the links working:

```bash
npm install
npm run lint          # markdownlint
npm run check:links   # verify every link (needs internet access)
```

Content last reviewed: November 2026.

## Contributing

Corrections and improvements are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md); every
technical claim needs an authoritative source.

## License

[MIT](LICENSE)
