# Image optimisation

Images are often the largest bytes on a page and frequently the Largest Contentful Paint element.
Serve the right format, at the right size, at the right time.

## Checklist

- [ ] Every content image has `width` and `height` attributes (or CSS `aspect-ratio`) so space is
      reserved before it loads.
- [ ] Responsive images use `srcset` and `sizes` so small screens download small files.
- [ ] Modern formats (AVIF, WebP) are served with a fallback, using `<picture>` or content
      negotiation at the CDN.
- [ ] Images below the fold use `loading="lazy"`.
- [ ] The LCP image is **not** lazy-loaded and has `fetchpriority="high"`.
- [ ] A hero image set with CSS `background-image` is preloaded, or replaced with an `<img>`.
- [ ] Images are compressed; quality settings are checked visually, not left at maximum.
- [ ] Icons and simple illustrations use SVG (sanitised if it comes from untrusted sources).
- [ ] Animated GIFs are replaced by video (`<video autoplay muted loop playsinline>`).
- [ ] Every informative image has meaningful `alt` text; decorative images use `alt=""`.

## Formats

| Format | Good for | Notes |
| --- | --- | --- |
| AVIF | Photos and complex images | Usually the smallest files; slower to encode. Provide a fallback |
| WebP | Photos and images with transparency | Broad support; a common fallback for AVIF |
| JPEG | Photos | Universal fallback |
| PNG | Screenshots, images needing lossless quality | Often larger than WebP lossless |
| SVG | Logos, icons, diagrams | Scales without loss; can be styled with CSS |

MDN lists AVIF and WebP as supported by current versions of Chrome, Edge, Firefox, Opera and
Safari, but still recommends providing fallbacks for older browsers. Check the compatibility of
the specific features you use (for example animated AVIF or HDR) against your support policy.

## Examples

Responsive, modern-format image with fallback:

```html
<picture>
  <source
    type="image/avif"
    srcset="/img/team-480.avif 480w, /img/team-960.avif 960w, /img/team-1440.avif 1440w"
    sizes="(min-width: 64rem) 50vw, 100vw"
  />
  <source
    type="image/webp"
    srcset="/img/team-480.webp 480w, /img/team-960.webp 960w, /img/team-1440.webp 1440w"
    sizes="(min-width: 64rem) 50vw, 100vw"
  />
  <img
    src="/img/team-960.jpg"
    srcset="/img/team-480.jpg 480w, /img/team-960.jpg 960w, /img/team-1440.jpg 1440w"
    sizes="(min-width: 64rem) 50vw, 100vw"
    width="1440"
    height="960"
    alt="The support team at their weekly planning meeting"
    loading="lazy"
    decoding="async"
  />
</picture>
```

The LCP hero image, loaded as early and as fast as possible:

```html
<img
  src="/img/hero-1200.avif"
  srcset="/img/hero-800.avif 800w, /img/hero-1200.avif 1200w, /img/hero-2000.avif 2000w"
  sizes="100vw"
  width="2000"
  height="1000"
  alt="Mountain trail at sunrise"
  fetchpriority="high"
/>
```

Preloading a responsive background image that the browser would otherwise find late:

```html
<link
  rel="preload"
  as="image"
  imagesrcset="/img/hero-800.avif 800w, /img/hero-1200.avif 1200w"
  imagesizes="100vw"
  type="image/avif"
  fetchpriority="high"
/>
```

Keeping layout stable when the intrinsic size is unknown:

```css
.thumbnail {
  aspect-ratio: 16 / 9;
  width: 100%;
  height: auto;
  object-fit: cover;
}
```

## Testing methods

- Lighthouse flags oversized images, missing dimensions, offscreen images that are not deferred
  and a lazy-loaded LCP image.
- In the DevTools **Network** panel, filter by **Img** and compare the transferred size with the
  rendered size; check which `srcset` candidate was chosen at different viewport widths and pixel
  densities.
- The DevTools **Performance** panel shows which element is the LCP and when it painted.
- Compare compressed images side by side at 100% zoom before accepting a quality setting.

## Tools

- [Squoosh](https://squoosh.app/) for comparing formats and quality settings in the browser
- [sharp](https://sharp.pixelplumbing.com/) for automated image processing in build pipelines
- [SVGO](https://github.com/svg/svgo) for optimising SVG files

## References

- [Image file type and format guide](https://developer.mozilla.org/en-US/docs/Web/Media/Guides/Formats/Image_types) (MDN)
- [Responsive images](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Responsive_images) (MDN)
- [Browser-level image lazy loading](https://web.dev/articles/browser-level-image-lazy-loading)
- [Optimize resource loading with the Fetch Priority API](https://web.dev/articles/fetch-priority)
- [Optimize Largest Contentful Paint](https://web.dev/articles/optimize-lcp)
- [Learn Images](https://web.dev/learn/images) (web.dev course)
