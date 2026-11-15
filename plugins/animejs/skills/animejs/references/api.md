# Anime.js v4 reference

Source: https://github.com/juliangarnier/anime (MIT). Verified against the v4.5.0 `src/index.js` exports. Docs: https://animejs.com/documentation.

## Public exports (from `animejs`)

timer, animation (`animate`), timeline (`createTimeline`), animatable, draggable (`createDraggable`), scope (`createScope`), events, engine, easings (also as `easings` namespace), layout, utils (also as `utils` namespace, includes `stagger`), svg (also `svg` namespace), text (also `text` namespace), waapi, `globals`.

Always confirm exact function names in the installed package's `types/` before relying on one not listed here.

## SVG (`animejs` or `svg` namespace)

- `createDrawable(selector)` - animate line drawing via the returned `draw` property.
- `createMotionPath(path)` - returns transform properties to move an element along a path.
- `morphTo(target)` - morph one shape's `d`/`points` into another.

## Text

- `splitText(target, options)` - split into chars/words/lines for staggered animation.
- Scramble effects live in the same module.

## v3 to v4 cheat sheet

| v3 | v4 |
| --- | --- |
| `import anime from 'animejs'` | `import { animate } from 'animejs'` |
| `anime({ targets, ... })` | `animate(targets, { ... })` |
| `anime.timeline()` | `createTimeline()` |
| `anime.stagger()` | `stagger()` |
| `easing: 'easeInOutQuad'` | `ease: 'inOutQuad'` |
| `direction: 'alternate'` | `alternate: true` |
| `direction: 'reverse'` | `reversed: true` |
| `translateX: 100` | `x: 100` |
| `value: [from, to]` | `[from, to]` or `{ from, to }` |
| `anime.remove(t)` | `animation.revert()` / engine API |

Official guide: https://github.com/juliangarnier/anime/wiki/Migrating-from-v3-to-v4

## Build outputs

ESM, UMD, CJS and IIFE bundles are published; browsers can use the ESM build via a CDN `<script type="module">`.
