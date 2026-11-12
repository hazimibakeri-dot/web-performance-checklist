# JavaScript optimisation

JavaScript costs more than its download size: it must be parsed, compiled and executed on the
main thread, which competes with responding to user input. Ship less of it, and run it in smaller
pieces.

## Checklist

### Ship less

- [ ] Bundles are split by route, and rarely used features load on demand with dynamic `import()`.
- [ ] Dead code is removed through tree shaking (ES modules, `sideEffects` declared in packages).
- [ ] Bundle contents are reviewed regularly with a bundle analyser; large dependencies are
      questioned.
- [ ] The build targets the browsers you actually support, so modern syntax is not transpiled and
      unnecessary polyfills are not shipped.
- [ ] Unused JavaScript is identified with the DevTools Coverage panel.

### Load it well

- [ ] Scripts use `defer`, `async` or `type="module"`; nothing blocks parsing without a reason.
- [ ] Third-party scripts are audited, loaded late, and removed when no longer used.
- [ ] Server rendering or static generation is used for content-heavy pages, so content does not
      wait for JavaScript.

### Run it efficiently

- [ ] No task on the main thread runs for more than 50 ms during load or interactions.
- [ ] Long work is split into chunks that yield to the browser between them.
- [ ] Event handlers do the minimum before the next paint; expensive work is deferred.
- [ ] Input handlers for typing, scrolling and resizing are debounced or throttled.
- [ ] CPU-heavy work (parsing large files, image processing) runs in a Web Worker.
- [ ] Code does not interleave layout reads and style writes in loops (layout thrashing).

## Examples

Loading a feature on demand:

```js
const button = document.querySelector('#open-editor');

button.addEventListener('click', async () => {
  const { openEditor } = await import('./editor.js'); // split into its own chunk by bundlers
  openEditor();
});
```

Yielding to the main thread between chunks of work:

```js
function yieldToMain() {
  // Use scheduler.yield() where the browser supports it; otherwise fall back to a macrotask.
  if (globalThis.scheduler?.yield) return scheduler.yield();
  return new Promise((resolve) => setTimeout(resolve, 0));
}

async function processItems(items, processItem) {
  let lastYield = performance.now();
  for (const item of items) {
    processItem(item);
    if (performance.now() - lastYield > 40) {
      await yieldToMain();
      lastYield = performance.now();
    }
  }
}
```

Updating the UI first, then doing non-urgent work:

```js
form.addEventListener('submit', (event) => {
  event.preventDefault();
  showSavingIndicator(); // visible feedback in the next frame

  // Let the browser paint, then continue with the slower work.
  requestAnimationFrame(() => {
    setTimeout(() => saveDraftAndSendAnalytics(new FormData(form)), 0);
  });
});
```

Avoiding layout thrashing by batching reads before writes:

```js
// Slow: each iteration forces a synchronous layout.
for (const card of cards) {
  card.style.height = `${card.scrollHeight}px`;
}

// Faster: read everything, then write everything.
const heights = cards.map((card) => card.scrollHeight);
cards.forEach((card, index) => {
  card.style.height = `${heights[index]}px`;
});
```

## Testing methods

- **Performance panel** in Chrome DevTools: record a page load and key interactions, then look for
  long tasks (marked in red) and what caused them.
- **Lighthouse**: Total Blocking Time, "Reduce JavaScript execution time" and "Reduce unused
  JavaScript".
- **Coverage panel**: how much of each script runs during a session.
- **Bundle analysis**: run your bundler's analyser in CI and fail the build when a budget is
  exceeded.
- **Field data**: INP from real users shows whether interactions are actually slow.

## Tools

- [Chrome DevTools Performance panel](https://developer.chrome.com/docs/devtools/performance)
- [Chrome DevTools Coverage panel](https://developer.chrome.com/docs/devtools/coverage)
- [source-map-explorer](https://github.com/danvk/source-map-explorer) and bundler-specific
  analysers

## References

- [Optimize long tasks](https://web.dev/articles/optimize-long-tasks)
- [Optimize Interaction to Next Paint](https://web.dev/articles/optimize-inp)
- [Reduce JavaScript payloads with code splitting](https://web.dev/articles/reduce-javascript-payloads-with-code-splitting)
- [`Scheduler.yield()`](https://developer.mozilla.org/en-US/docs/Web/API/Scheduler/yield) (MDN)
- [Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) (MDN)
- [Rendering on the web](https://web.dev/articles/rendering-on-the-web)
