# PixiJS v8 reference

Source: https://github.com/pixijs/pixijs (MIT), `pixi.js@8.23.0`. Docs: https://pixijs.com/8.x/guides, API: https://pixijs.download/release/docs/index.html.
Verified in source: async `init()` requirement, `app.canvas`, `Ticker.deltaTime` / `deltaMS`, Graphics `rect` / `circle` / `roundRect` / `fill` / `stroke`, and the `particle-container` module. The rest is from the v8 guides and should be confirmed against the installed types.

## Top-level modules (from `src/index.ts`)

accessibility, advanced-blend-modes, app, assets, color, compressed-textures, culling, dom, environment, events, extensions, filters, maths, prepare, rendering, scene, spritesheet, ticker, utils.

## v7 to v8

| v7 | v8 |
| --- | --- |
| `new Application({ ... })` | `const app = new Application(); await app.init({ ... })` |
| `app.view` | `app.canvas` |
| `g.beginFill(c).drawRect(...).endFill()` | `g.rect(...).fill(c)` |
| `g.lineStyle(w, c)` | `g.rect(...).stroke({ width: w, color: c })` |
| `Texture.from(url)` for remote files | `await Assets.load(url)` |
| `ticker.add((delta) => ...)` | `ticker.add((ticker) => ... ticker.deltaTime)` |
| `ParticleContainer` of `Sprite` | `ParticleContainer` of `Particle` |
| `DisplayObject` | `Container` |

## Common calls

| Need | API |
| --- | --- |
| App | `new Application()`, `await app.init({ background, resizeTo, antialias, resolution })` |
| Scene | `app.stage.addChild(obj)`, `Container` for grouping |
| Images | `Assets.load`, `new Sprite(texture)`, `sprite.anchor.set(0.5)` |
| Shapes | `new Graphics().circle(0, 0, 20).fill(0xffffff)` |
| Text | `new Text({ text, style })` |
| Input | `obj.eventMode = 'static'; obj.on('pointerdown', fn)` |
| Loop | `app.ticker.add(fn)` |
| Cleanup | `app.destroy(true, { children: true })` |

## Common mistakes

- Using the app or its canvas before `await app.init()` finishes.
- v7 Graphics calls (`beginFill`, `drawRect`) which no longer exist.
- Treating `deltaTime` as milliseconds. Use `deltaMS` for that.
- Using full `Sprite`s for thousands of particles.
- Not destroying the app on unmount, leaking canvases and GPU memory.
- Two render loops (your own `requestAnimationFrame` plus the ticker) both drawing.
