---
name: pixijs
description: Use when drawing or animating with PixiJS (the `pixi.js` npm package, pixijs/pixijs) - v8 Application setup, sprites, Graphics, text, filters, particles, ticker loops, asset loading, and rendering Matter.js physics bodies at 60fps. Also for migrating v7 code to v8.
---

# PixiJS v8

Fast 2D renderer for the web on WebGL and WebGPU (MIT). Checked against `pixi.js@8.23.0`. It renders; it does not simulate physics (use Matter.js) or tween (use Anime.js).

## Rules

1. Install with `npm i pixi.js` and import named exports: `import { Application, Assets, Sprite, Graphics } from 'pixi.js'`.
2. **v8 requires async init.** `const app = new Application(); await app.init({ ... });`. Passing options to the constructor is the v7 way and does nothing useful in v8.
3. The canvas is `app.canvas` (v7 used `app.view`). Append it yourself: `document.body.appendChild(app.canvas)`.
4. Draw shapes with the chained Graphics API, shape first, then fill/stroke: `new Graphics().rect(0, 0, 100, 50).fill(0xff0000)`. The v7 `beginFill` / `drawRect` / `endFill` calls are gone.
5. Ticker callbacks receive the ticker: `app.ticker.add((t) => { s.rotation += 0.1 * t.deltaTime; })`. `deltaTime` is a unitless scalar (about 1 at 60 fps); `deltaMS` is milliseconds.
6. Load textures with `await Assets.load(url)` before creating sprites. Do not construct `Sprite.from(url)` for unloaded URLs and expect it to be ready.
7. Positions are in canvas pixels, `y` grows downward. Set `sprite.anchor.set(0.5)` when you want the position to be the center, which is also what Matter.js bodies use.
8. Teardown: `app.destroy(true, { children: true })` (removes the canvas and destroys children). Do this on unmount; Pixi apps leak GPU memory otherwise.
9. Many identical moving things (hundreds to tens of thousands): use `ParticleContainer` with `Particle` objects instead of full `Sprite`s.
10. Verify names against the installed package types (`node_modules/pixi.js/lib/index.d.ts`) when unsure; v8 moved several APIs.

## Minimal example

```js
import { Application, Assets, Sprite } from 'pixi.js';

const app = new Application();
await app.init({ background: '#1099bb', resizeTo: window });
document.body.appendChild(app.canvas);

const texture = await Assets.load('https://pixijs.com/assets/bunny.png');
const bunny = new Sprite(texture);
bunny.anchor.set(0.5);
bunny.position.set(app.screen.width / 2, app.screen.height / 2);
app.stage.addChild(bunny);

app.ticker.add((t) => { bunny.rotation += 0.1 * t.deltaTime; });
```

## Frameworks

Create the `Application` in a mount effect, await `init`, guard against unmount happening before `init` resolves, then destroy in cleanup. React users may prefer `@pixi/react`; check its docs for the current API before writing against it.

## Combining

- Pixi + Matter.js: Matter steps bodies, Pixi draws one display object per body. See `references/matter-recipe.md`.
- Pixi + Anime.js: Anime.js can tween any numeric property of a Pixi object (`animate(sprite, { alpha: 0 })`, `animate(sprite.position, { x: 300 })`). Do not tween a property a physics body also drives.

## Reduced motion

If `prefers-reduced-motion: reduce` is set, render the final frame once (`app.render()` after `await app.init({ autoStart: false })`) and skip looping effects.

## Reference

`references/api.md` for the v7 to v8 table and common calls.
