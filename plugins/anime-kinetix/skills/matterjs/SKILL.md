---
name: matterjs
description: Use when adding 2D rigid-body physics to a web page or Node script with Matter.js (the `matter-js` npm package, liabru/matter-js) - gravity, collisions, stacks, constraints, mouse dragging, syncing physics bodies to DOM or canvas, and combining physics with Anime.js animation.
---

# Matter.js

2D rigid-body physics engine (MIT). Latest npm release when this plugin was written: `matter-js@0.20.0` (types: `@types/matter-js`). It is a CommonJS/UMD bundle with one namespace, not an ES-module library of named exports.

## Rules

1. Install with `npm i matter-js` and `import Matter from 'matter-js'`, then destructure: `const { Engine, Bodies, Composite, Runner } = Matter`. Browsers can also load `build/matter.js` with a script tag.
2. `Matter.World` is deprecated. Use `Composite.add(engine.world, bodies)`.
3. `Engine.run` is deprecated. Create a runner: `const runner = Runner.create(); Runner.run(runner, engine);`.
4. Units are pixels, positions are body centers (not top-left), and `y` grows downward. Default gravity is `engine.gravity.y = 1`.
5. The engine itself is plain JavaScript and runs headless (Node, React Native, workers): call `Engine.update(engine, 1000 / 60)` yourself. `Render`, `Mouse` and `MouseConstraint` need a browser DOM. Bodies are not DOM nodes. Matter only simulates; you render with `Matter.Render`, a canvas, or by writing body positions to DOM elements each frame.
6. Static bodies (`isStatic: true`) never move. Make walls and floors static, and give them real thickness so fast bodies do not tunnel through.
7. Never create or remove bodies inside a collision event handler without care; collect them and apply after the update. Remove event listeners (`Events.off`) and the runner (`Runner.stop`) on teardown, and `Engine.clear(engine)` / `Composite.clear(engine.world)` to free the world.
8. In frameworks, run the engine in a mount effect and stop it on unmount. Dev-mode bundler settings can slow it badly (see the project README on performance).

## Minimal example

```js
import Matter from 'matter-js';
const { Engine, Render, Runner, Bodies, Composite, Mouse, MouseConstraint } = Matter;

const engine = Engine.create();
const render = Render.create({
  element: document.body,
  engine,
  options: { width: 800, height: 600, wireframes: false },
});

const ground = Bodies.rectangle(400, 610, 810, 60, { isStatic: true });
const box = Bodies.rectangle(400, 100, 80, 80, { restitution: 0.6 });
const mouse = MouseConstraint.create(engine, { mouse: Mouse.create(render.canvas) });

Composite.add(engine.world, [ground, box, mouse]);
Render.run(render);
const runner = Runner.create();
Runner.run(runner, engine);

// teardown
// Render.stop(render); Runner.stop(runner); Engine.clear(engine);
```

## Syncing physics to the DOM

```js
Events.on(engine, 'afterUpdate', () => {
  for (const { body, el } of pairs) {
    el.style.transform =
      `translate(${body.position.x - w / 2}px, ${body.position.y - h / 2}px) rotate(${body.angle}rad)`;
  }
});
```

Subtract half the element size because Matter positions are centers.

## Using with Anime.js

Let Matter own anything that collides and Anime.js own everything scripted (enter/exit, color, opacity, UI). Do not animate the same property from both. To animate a body from Anime.js, set it with `Body.setPosition` / `Body.setAngle` inside `onUpdate`, and make it static or set its velocity to zero while scripted.

## Reference

Module list, common options, events, and common mistakes: `references/api.md`.
