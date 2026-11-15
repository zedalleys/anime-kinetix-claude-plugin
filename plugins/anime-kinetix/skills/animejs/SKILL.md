---
name: animejs
description: Use when writing, debugging or migrating animations with Anime.js (the `animejs` npm package, juliangarnier/anime). Covers v4 ESM API - animate, createTimeline, stagger, SVG drawing/morphing/motion paths, text splitting, draggable, scope, WAAPI - and v3 to v4 migration.
---

# Anime.js v4

Anime.js is a lightweight animation engine for CSS properties, SVG, DOM attributes and plain JS objects. Current major is **v4** (ES modules, named exports). Most code seen online is v3 and will not work.

## Rules

1. Install with `npm i animejs` and import named exports: `import { animate, createTimeline, stagger } from 'animejs'`. There is no default `anime()` function in v4.
2. Subpath imports are available for tree-shaking: `animejs/timeline`, `animejs/animation`, `animejs/draggable`, `animejs/scope`, `animejs/layout`, etc.
3. Durations and delays are in milliseconds.
4. Transform shorthands are first-class properties: `x`, `y`, `z`, `rotate`, `scale`, `skewX`... No `translateX` needed.
5. Check the installed version (`npm ls animejs`) before using an API; confirm unfamiliar options against https://animejs.com/documentation.
6. In frameworks (React, Vue, Svelte), create animations in a mount effect, scope them with `createScope`, and call `scope.revert()` on cleanup.

## Core pattern

```js
import { animate, stagger } from 'animejs';

animate('.square', {
  x: 320,
  rotate: { from: -180 },
  duration: 1250,
  delay: stagger(65, { from: 'center' }),
  ease: 'inOutQuint',
  loop: true,
  alternate: true,
});
```

Targets: CSS selector, DOM element(s), NodeList, or a JS object (`animate(obj, { value: 100, onUpdate })`). Object targets work without a DOM, so they can drive values in non-DOM environments (for example into a native renderer); DOM/CSS features need a browser.

## Timelines

```js
import { createTimeline } from 'animejs';

const tl = createTimeline({ defaults: { duration: 600, ease: 'outExpo' } });
tl.add('.a', { x: 200 })
  .add('.b', { y: 100 }, '-=200')   // relative position
  .label('end')
  .call(() => console.log('done'), 'end');
```

Methods: `add`, `set`, `call`, `label`, `sync`. Controls on any animation/timeline: `play`, `pause`, `restart`, `reverse`, `seek`, `revert`.

## Pick the right reference

- Full export list, v3 to v4 renames, easing names, SVG/text/draggable usage: `references/api.md`.
- Migration of existing v3 code: use the rename table in `references/api.md`, then run the project's build to catch leftovers.

## Common mistakes

- Using `anime({...})` or `anime.timeline()` (v3). Use `animate` / `createTimeline`.
- `easing: 'easeInOutQuad'` (v3). v4 uses `ease: 'inOutQuad'`.
- `direction: 'alternate'`. v4 uses `alternate: true`; `reversed: true` for reverse.
- `targets:` inside the params object. v4 passes targets as the first argument.
- Forgetting `revert()` on unmount, leaving inline styles behind.
