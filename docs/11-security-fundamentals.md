# Security fundamentals

Front-end security basics: transport security, response headers, cookies, third-party code and
handling untrusted input. This is a starting point, not a complete security programme; the OWASP
resources linked below go much further.

## Checklist

### Transport

- [ ] The whole site is served over HTTPS, and HTTP redirects to HTTPS.
- [ ] `Strict-Transport-Security` (HSTS) is set once HTTPS works everywhere, including subdomains
      if you use `includeSubDomains`.
- [ ] HSTS preloading is treated as a long-term commitment. The preload list requires a
      `max-age` of at least one year (31536000 seconds), `includeSubDomains` and `preload`, and
      removal takes a long time.

### Response headers

- [ ] A `Content-Security-Policy` restricts where scripts, styles, frames and connections can come
      from. Start with `Content-Security-Policy-Report-Only` to find violations before enforcing.
- [ ] Inline scripts are avoided, or allowed with nonces or hashes rather than `'unsafe-inline'`.
- [ ] Framing is controlled with CSP `frame-ancestors`; `X-Frame-Options` can also be sent for
      older browsers (its `ALLOW-FROM` value is obsolete).
- [ ] `X-Content-Type-Options: nosniff` is set.
- [ ] `Referrer-Policy` is set deliberately. Browsers default to `strict-origin-when-cross-origin`
      when it is missing.
- [ ] `Permissions-Policy` disables powerful features the site does not use (camera, microphone,
      geolocation and so on).
- [ ] `X-XSS-Protection` is not used. It is deprecated and can create vulnerabilities; if a
      scanner insists on it, send `X-XSS-Protection: 0`.

### Cookies

- [ ] Session cookies use `Secure`, `HttpOnly` and an explicit `SameSite` value.
- [ ] Sensitive cookies use the `__Host-` prefix, which requires `Secure`, `Path=/` and no
      `Domain` attribute.
- [ ] State-changing requests are protected against cross-site request forgery (CSRF).

### Code and dependencies

- [ ] Untrusted data is never inserted as HTML. Use text APIs (`textContent`, framework
      escaping) and sanitise HTML with a maintained library when rich text is unavoidable.
- [ ] URLs from users are validated before use in `href` or `src` (block `javascript:` URLs).
- [ ] Third-party scripts loaded from CDNs use Subresource Integrity (`integrity` attribute) when
      the file is versioned.
- [ ] Dependencies are kept up to date and audited; automated update pull requests are reviewed.
- [ ] Secrets are never committed or shipped to the browser; secret scanning is enabled.
- [ ] CORS allows only the origins that need access, and never combines a wildcard origin with
      credentials.
- [ ] Error pages and API errors do not reveal stack traces or internal details in production.

## Examples

A baseline set of security headers (adjust the CSP to your actual sources before enforcing it):

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self' data:; object-src 'none'; base-uri 'self'; frame-ancestors 'none'; form-action 'self'; upgrade-insecure-requests
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

A session cookie:

```http
Set-Cookie: __Host-session=abc123; Path=/; Secure; HttpOnly; SameSite=Lax
```

A third-party script pinned with Subresource Integrity (generate the hash from the exact file you
reviewed):

```html
<script
  src="https://cdn.example.com/library@4.2.1/library.min.js"
  integrity="sha384-REPLACE_WITH_THE_BASE64_HASH_OF_THE_FILE"
  crossorigin="anonymous"
></script>
```

Rendering user input safely:

```js
// Unsafe: interprets user input as HTML.
comment.innerHTML = userInput;

// Safe: treats it as text.
comment.textContent = userInput;

// Validate user-supplied links before using them.
function safeHref(value) {
  try {
    const url = new URL(value, location.origin);
    return ['http:', 'https:', 'mailto:'].includes(url.protocol) ? url.href : '#';
  } catch {
    return '#';
  }
}
```

## Testing methods

- Scan deployed sites with Mozilla's [HTTP Observatory](https://developer.mozilla.org/en-US/observatory).
- Deploy CSP in report-only mode, collect violation reports, then enforce.
- Run `npm audit` (or your platform's dependency scanning) in CI and review results.
- Enable secret scanning and push protection on your repositories.
- Inspect cookies in the DevTools **Application** panel to confirm their attributes.
- For applications handling sensitive data, commission a professional security review or
  penetration test.

## References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP HTTP Security Response Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
- [OWASP Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP) (MDN)
- [`Strict-Transport-Security`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security) (MDN)
- [HSTS preload list submission](https://hstspreload.org/)
- [`X-Frame-Options`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/X-Frame-Options) (MDN)
- [`X-Content-Type-Options`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/X-Content-Type-Options) (MDN)
- [`Referrer-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referrer-Policy) (MDN)
- [`Permissions-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Permissions-Policy) (MDN)
- [`X-XSS-Protection`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/X-XSS-Protection) (MDN)
- [`Set-Cookie`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie) (MDN)
- [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) (MDN)
- [About secret scanning](https://docs.github.com/en/code-security/secret-scanning/introduction/about-secret-scanning) (GitHub Docs)
