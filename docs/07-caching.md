# Caching

Good caching makes repeat visits fast and reduces server load. The usual strategy is simple:
cache versioned assets forever, and make HTML revalidate.

## Key `Cache-Control` directives

| Directive | Meaning |
| --- | --- |
| `max-age=N` | The response is fresh for N seconds |
| `s-maxage=N` | Like `max-age`, but only for shared caches such as CDNs |
| `no-cache` | The response **may be stored**, but must be revalidated with the server before each reuse |
| `no-store` | The response must **not be stored** in any cache |
| `immutable` | The response will not change while it is fresh, so the browser need not revalidate it |
| `private` | Only the user's browser may store it, not shared caches |
| `public` | Shared caches may store it, even if it would normally be private |
| `stale-while-revalidate=N` | After expiry, a stale copy may be served for N seconds while a fresh one is fetched in the background |

`no-cache` is a common source of confusion: it does not mean "do not cache".

## Checklist

- [ ] Build output uses content-hashed file names (for example `app.3f9c2a.js`).
- [ ] Hashed assets are served with `Cache-Control: public, max-age=31536000, immutable`.
- [ ] HTML documents use `Cache-Control: no-cache` (or a short `max-age`) with an `ETag` or
      `Last-Modified` validator, so new deployments are picked up immediately.
- [ ] Personalised or sensitive responses use `private` or `no-store`.
- [ ] API responses have explicit caching rules instead of relying on heuristics.
- [ ] The CDN cache key and `Vary` header match the response variations (for example
      `Vary: Accept-Encoding`).
- [ ] There is a way to purge the CDN cache after a bad deployment.
- [ ] A service worker, if used, has a clear update strategy and never serves stale HTML forever.
- [ ] Caching headers do not make pages ineligible for the back/forward cache unnecessarily.

## Examples

Response headers:

```http
# Versioned static asset (file name changes when content changes)
Cache-Control: public, max-age=31536000, immutable

# HTML: always revalidate, so deployments take effect immediately
Cache-Control: no-cache
ETag: "5f3a1c"

# Account page with personal data
Cache-Control: private, no-store

# Public API response that tolerates being slightly stale
Cache-Control: public, max-age=60, stale-while-revalidate=300
```

nginx configuration for a single-page app built with hashed assets:

```nginx
location /assets/ {
  add_header Cache-Control "public, max-age=31536000, immutable";
  try_files $uri =404;
}

location / {
  add_header Cache-Control "no-cache";
  try_files $uri /index.html;
}
```

Many static hosts use a headers file instead, for example (format varies by host):

```text
/assets/*
  Cache-Control: public, max-age=31536000, immutable

/*
  Cache-Control: no-cache
```

## Testing methods

- In the DevTools **Network** panel, reload twice and check the **Size** column ("memory cache",
  "disk cache" or a 304 response) and the response headers.
- Use `curl -I https://example.com/assets/app.3f9c2a.js` to inspect headers without a browser cache.
- After a deployment, confirm users get the new HTML and that the new HTML references new asset
  names.
- Lighthouse reports static assets with an inefficient cache policy.

## References

- [HTTP caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching) (MDN)
- [`Cache-Control`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cache-Control) (MDN)
- [`ETag`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag) (MDN)
- [`Vary`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Vary) (MDN)
- [RFC 9111: HTTP Caching](https://www.rfc-editor.org/rfc/rfc9111)
- [Back/forward cache](https://web.dev/articles/bfcache)
