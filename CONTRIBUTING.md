# Contributing

Thank you for helping keep these checklists accurate and useful.

## What makes a good contribution

- **Corrections** to anything outdated or wrong, with a link to the source that shows it.
- **New checklist items** that are practical, testable and broadly applicable.
- **Better examples**: short, working code that a reader can copy and adapt.

## Rules for content

1. **Cite authoritative sources.** Prefer specifications (W3C, WHATWG, IETF), browser and platform
   documentation (MDN, web.dev, Chrome for Developers), Google Search Central and OWASP. Blog posts
   can supplement but not replace these.
2. **No invented data.** Do not add benchmark figures, percentages or "X% faster" claims unless
   they are quoted from, and linked to, a published source, and are clearly attributed.
3. **State uncertainty.** If support or behaviour differs between browsers, say so and link to a
   compatibility table instead of generalising.
4. **Keep items actionable.** Each checklist item should describe something a reader can do and
   verify.
5. **Write plainly.** Short sentences, consistent terminology, British or American spelling used
   consistently within a file.

## Checks

```bash
npm install
npm run lint          # markdownlint
npm run check:links   # link check (requires internet access)
```

Both run in CI. If an external site blocks automated link checks, mention it in your pull request
rather than removing the link.

## Dependency notes

`package.json` overrides `re2js` to 2.8.7, the release that fixes advisory
[GHSA-gg6h-894w-4m9c](https://github.com/advisories/GHSA-gg6h-894w-4m9c), because
`markdownlint` 0.42.0 pins the affected 2.8.6. Remove the override once `markdownlint-cli2` is
upgraded to a version whose `markdownlint` depends on a fixed `re2js`.

## Pull requests

- Keep each pull request focused on one topic.
- Describe what changed and link the sources you used.
- Update "Content last reviewed" in the README when you review a whole guide.

## License

By contributing, you agree that your contributions are licensed under the [MIT License](LICENSE).
