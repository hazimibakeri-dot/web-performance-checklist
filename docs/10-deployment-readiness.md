# Deployment readiness

A release checklist for front-end projects. Adapt it to your stack and keep it in version control
next to the code.

## Checklist

### Build and quality

- [ ] CI runs type checks, linting, tests and the production build on every change.
- [ ] The production build is reproducible from a clean checkout with a lockfile (`npm ci`).
- [ ] Dependencies have been checked for known vulnerabilities (`npm audit` or an equivalent
      service), and findings are triaged.
- [ ] Performance budgets and accessibility checks run in CI for key pages.
- [ ] A decision has been made about source maps: not public, uploaded privately to an error
      monitoring service, or public on purpose.

### Configuration and secrets

- [ ] No secrets are committed to the repository or embedded in client bundles. Anything sent to
      the browser (for example `VITE_*` or `NEXT_PUBLIC_*` variables) is public.
- [ ] Environment-specific configuration comes from the environment, not from code changes.
- [ ] Secret scanning is enabled for the repository.

### Hosting

- [ ] HTTPS is enforced and HTTP redirects to HTTPS.
- [ ] Security headers are configured (see [Security fundamentals](11-security-fundamentals.md)).
- [ ] Caching headers are configured (see [Caching](07-caching.md)).
- [ ] Text responses are compressed (Brotli or gzip).
- [ ] Single-page apps have a fallback to `index.html` for client-side routes, while real 404s
      still return a 404 status where possible.
- [ ] Redirects for moved URLs are in place.

### Content and metadata

- [ ] Every page has a title, description and canonical URL; `robots.txt` and the sitemap point
      to the production domain (see [Technical SEO](04-technical-seo.md)).
- [ ] Staging and preview environments are kept out of search results.
- [ ] Favicon, web app manifest (if used) and social preview images are present.
- [ ] A useful 404 page exists.
- [ ] Analytics, cookies and third-party services comply with the privacy rules that apply to
      your users, including consent requirements.

### Operations

- [ ] Error monitoring and uptime monitoring are active for production.
- [ ] A smoke test runs against the deployed site (key pages load, main journeys work).
- [ ] There is a documented rollback procedure, and it has been tried.
- [ ] Someone is responsible for watching the release.

## Examples

A minimal CI workflow (GitHub Actions) with read-only permissions:

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7
      - uses: actions/setup-node@v7
        with:
          node-version-file: .nvmrc
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

A post-deployment smoke test with Playwright:

```js
import { expect, test } from '@playwright/test';

const BASE_URL = process.env.BASE_URL ?? 'https://www.example.com';

test('home page loads and links to the main journey', async ({ page }) => {
  const response = await page.goto(BASE_URL);
  expect(response?.status()).toBe(200);
  await expect(page.getByRole('heading', { level: 1 })).toBeVisible();
  await page.getByRole('link', { name: 'Pricing' }).click();
  await expect(page).toHaveURL(/\/pricing/);
});
```

Keeping preview deployments out of search engines (response header on non-production hosts):

```http
X-Robots-Tag: noindex
```

## Testing methods

- Run the full checklist against a staging environment that mirrors production.
- After deployment, run the smoke tests, check response headers with `curl -I`, and scan the
  site with Mozilla's HTTP Observatory.
- Review error monitoring and Core Web Vitals in the days after a release.

## References

- [`npm ci`](https://docs.npmjs.com/cli/commands/npm-ci) and [`npm audit`](https://docs.npmjs.com/cli/commands/npm-audit)
- [The Twelve-Factor App: Config](https://12factor.net/config)
- [Lighthouse CI](https://github.com/GoogleChrome/lighthouse-ci)
- [Playwright](https://playwright.dev/)
- [HTTP Observatory](https://developer.mozilla.org/en-US/observatory) (MDN)
- [Block indexing with noindex](https://developers.google.com/search/docs/crawling-indexing/block-indexing)
