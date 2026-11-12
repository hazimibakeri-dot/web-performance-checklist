# Technical SEO

Technical SEO makes sure search engines can crawl, render, understand and index your pages.
It does not replace useful content. This page focuses on Google Search, whose documentation is
public and detailed; most guidance applies to other search engines too.

## Checklist

### Crawling and indexing

- [ ] Important pages return HTTP 200; removed pages return 404 or 410; moved pages use 301.
- [ ] `robots.txt` does not block resources needed to render pages (CSS, JavaScript, images).
- [ ] Pages that must stay out of search results use a `noindex` robots meta tag or header, or
      password protection, not `robots.txt`.
- [ ] An XML sitemap lists canonical URLs and is referenced in `robots.txt` and submitted in
      Search Console.
- [ ] Internal links are real `<a href="…">` elements with descriptive text.
- [ ] Single-page apps render meaningful content and links in the HTML, or render reliably for
      crawlers; prefer server rendering or static generation for content pages.

### Page metadata

- [ ] Every page has a unique, descriptive `<title>`.
- [ ] Every page has a meta description that summarises the page (it may be shown as a snippet).
- [ ] Duplicate or near-duplicate URLs point to one canonical URL with `rel="canonical"`.
- [ ] Language and regional variants are linked with `hreflang`.
- [ ] Open Graph and similar tags are set for link previews on social platforms.

### Structured data

- [ ] Structured data uses JSON-LD, describes content visible on the page, and validates in the
      Rich Results Test.
- [ ] You only rely on rich result types that Google currently supports for your kind of site.
      Since August 2023, FAQ rich results are shown only for well-known, authoritative government
      and health sites, and HowTo rich results have been removed.

### Experience

- [ ] Pages are mobile-friendly and meet Core Web Vitals (see [Core Web Vitals](01-core-web-vitals.md)).
- [ ] The site uses HTTPS everywhere.
- [ ] Intrusive interstitials do not block the main content.

## Things that do not help

- The `meta keywords` tag. Google Search does not use it.
- Blocking a page in `robots.txt` to remove it from results. Google may still index the URL
  without crawling it if other pages link to it.

## Examples

Head metadata for a product page:

```html
<title>Trail Running Shoe X2 · Example Outfitters</title>
<meta name="description" content="Lightweight trail running shoe with a grippy outsole and a
  breathable mesh upper. Free returns within 30 days." />
<link rel="canonical" href="https://www.example.com/shoes/trail-x2" />
<link rel="alternate" hreflang="en-gb" href="https://www.example.com/uk/shoes/trail-x2" />
<link rel="alternate" hreflang="de-de" href="https://www.example.com/de/schuhe/trail-x2" />
<link rel="alternate" hreflang="x-default" href="https://www.example.com/shoes/trail-x2" />

<meta property="og:title" content="Trail Running Shoe X2" />
<meta property="og:type" content="product" />
<meta property="og:image" content="https://www.example.com/images/trail-x2-1200x630.jpg" />
```

Keeping a page out of search results:

```html
<meta name="robots" content="noindex" />
```

For non-HTML files, send the equivalent HTTP header: `X-Robots-Tag: noindex`.

`robots.txt` with a sitemap reference:

```text
User-agent: *
Disallow: /cart/
Disallow: /search?

Sitemap: https://www.example.com/sitemap.xml
```

JSON-LD for an organisation (all values must match what the page shows):

```html
<script type="application/ld+json">
  {
    "@context": "https://schema.org",
    "@type": "Organization",
    "name": "Example Outfitters",
    "url": "https://www.example.com/",
    "logo": "https://www.example.com/images/logo-512.png"
  }
</script>
```

## Testing methods

- **Search Console**: indexing status, sitemaps, Core Web Vitals and enhancement reports.
- **URL Inspection tool** (in Search Console): see the rendered HTML and whether a URL is
  indexable.
- **Rich Results Test**: validate structured data and preview eligible results.
- **Crawl your own site** with a crawler to find broken links, redirect chains, missing titles,
  duplicate content and `noindex` mistakes.
- **View the raw HTML** (not only the DOM in DevTools) to confirm key content and links do not
  depend on client-side JavaScript.

## References

- [Introduction to robots.txt](https://developers.google.com/search/docs/crawling-indexing/robots/intro)
- [Meta tags that Google supports](https://developers.google.com/search/docs/crawling-indexing/special-tags)
- [Block indexing with noindex](https://developers.google.com/search/docs/crawling-indexing/block-indexing)
- [Consolidate duplicate URLs](https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls)
- [Sitemaps overview](https://developers.google.com/search/docs/crawling-indexing/sitemaps/overview)
- [Localized versions of your pages](https://developers.google.com/search/docs/specialty/international/localized-versions)
- [Make your links crawlable](https://developers.google.com/search/docs/crawling-indexing/links-crawlable)
- [Introduction to structured data](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data)
- [Changes to HowTo and FAQ rich results](https://developers.google.com/search/blog/2023/08/howto-faq-changes)
- [Understanding page experience](https://developers.google.com/search/docs/appearance/page-experience)
- [JavaScript SEO basics](https://developers.google.com/search/docs/crawling-indexing/javascript/javascript-seo-basics)
- [Rich Results Test](https://search.google.com/test/rich-results)
- [The Open Graph protocol](https://ogp.me/)
