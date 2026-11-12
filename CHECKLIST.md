# One-page release checklist

A condensed version of the guides for pull request and release reviews. Follow the links for the
reasoning, examples and testing methods behind each line.

## Core Web Vitals ([guide](docs/01-core-web-vitals.md))

- [ ] Field data reviewed for mobile and desktop at the 75th percentile.
- [ ] LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1.
- [ ] LCP image discoverable in HTML, not lazy-loaded, `fetchpriority="high"`.
- [ ] Space reserved for images, embeds and late content.

## Performance ([guide](docs/02-performance-optimisation.md))

- [ ] Text responses compressed; static assets on a CDN; HTTP/2 or HTTP/3.
- [ ] No render-blocking scripts; minimal critical CSS.
- [ ] Fonts in WOFF2, subset, `font-display` set, at most one or two preloaded.
- [ ] Third-party scripts justified and loaded late.
- [ ] Pages eligible for the back/forward cache.

## Accessibility ([guide](docs/03-accessibility.md))

- [ ] Landmarks, one `<h1>`, logical headings, `lang` and unique titles.
- [ ] Full keyboard support with visible, unobscured focus and a skip link.
- [ ] Contrast 4.5:1 for text (3:1 large text), 3:1 for components.
- [ ] Reflow at 320 CSS px; targets at least 24 × 24 CSS px.
- [ ] Labelled form fields with linked, specific error messages.
- [ ] Automated scan clean, plus keyboard and screen reader checks.

## Technical SEO ([guide](docs/04-technical-seo.md))

- [ ] Correct status codes and redirects.
- [ ] Unique titles and descriptions; canonical URLs; `hreflang` where relevant.
- [ ] `noindex` (not `robots.txt`) for pages that must stay out of results.
- [ ] Sitemap submitted; structured data valid and matching visible content.

## Images ([guide](docs/05-images.md))

- [ ] `width`/`height` or `aspect-ratio` on every image.
- [ ] `srcset`/`sizes`; AVIF or WebP with a fallback.
- [ ] Below-the-fold images lazy-loaded; meaningful `alt` text.

## JavaScript ([guide](docs/06-javascript.md))

- [ ] Route-based code splitting; on-demand loading for heavy features.
- [ ] No long tasks (over 50 ms) in key interactions; work split and yielded.
- [ ] Bundle size within budget; unused code removed.

## Caching ([guide](docs/07-caching.md))

- [ ] Hashed assets: `public, max-age=31536000, immutable`.
- [ ] HTML: `no-cache` with a validator.
- [ ] Private and sensitive responses: `private` or `no-store`.

## Responsive design ([guide](docs/08-responsive-design.md))

- [ ] Viewport meta tag present; zoom allowed.
- [ ] No horizontal scrolling at 320 CSS px; text in `rem`.
- [ ] Hover-only interactions have alternatives; user preferences respected.

## Browser compatibility ([guide](docs/09-browser-compatibility.md))

- [ ] Written support policy matches build targets.
- [ ] Newer features behind feature detection.
- [ ] Tested on Chromium, Firefox and WebKit, including iOS Safari.

## Deployment ([guide](docs/10-deployment-readiness.md))

- [ ] CI green: types, lint, tests, build, audits.
- [ ] No secrets in the repository or client bundle.
- [ ] HTTPS, security headers, caching and compression configured.
- [ ] Monitoring, smoke tests and a tested rollback procedure in place.

## Security ([guide](docs/11-security-fundamentals.md))

- [ ] HSTS, CSP, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, framing
      protection.
- [ ] Cookies `Secure`, `HttpOnly`, explicit `SameSite`; `__Host-` prefix for sensitive cookies.
- [ ] No untrusted HTML injection; user URLs validated; SRI for versioned CDN scripts.
- [ ] Dependencies audited; secret scanning enabled.
