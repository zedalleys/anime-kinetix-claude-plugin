# Matter.js reference

Source: https://github.com/liabru/matter-js (MIT), checked against `src/module/main.js` at `0.20.0`. Docs: https://brm.io/matter-js/docs/, wiki: https://github.com/liabru/matter-js/wiki.

## Modules on the `Matter` object

Axes, Bodies, Body, Bounds, Collision, Common, Composite, Composites, Constraint, Contact, Detector, Engine, Events, Grid, Mouse, MouseConstraint, Pair, Pairs, Plugin, Query, Render, Resolver, Runner, SAT, Sleeping, Svg, Vector, Vertices, World (deprecated).

## Use

| Need | API |
| --- | --- |
| Create world | `Engine.create({ gravity: { x: 0, y: 1 } })` |
| Shapes | `Bodies.rectangle(x, y, w, h, opts)`, `circle`, `polygon`, `trapezoid`, `fromVertices` |
| Add / remove | `Composite.add(engine.world, bodies)`, `Composite.remove(...)` |
| Move a body | `Body.setPosition`, `Body.setVelocity`, `Body.applyForce`, `Body.setAngle`, `Body.setStatic` |
| Grids / stacks | `Composites.stack(x, y, cols, rows, gapX, gapY, factory)` |
| Joints, springs | `Constraint.create({ bodyA, bodyB, stiffness, length })` |
| Drag with pointer | `MouseConstraint.create(engine, { mouse: Mouse.create(el) })` |
| Collisions | `Events.on(engine, 'collisionStart', e => e.pairs...)` (also `collisionActive`, `collisionEnd`) |
| Frame hooks | `Events.on(engine, 'beforeUpdate' / 'afterUpdate', fn)` |
| Hit tests | `Query.point(bodies, point)`, `Query.ray`, `Query.region` |
| Run loop | `Runner.create()`, `Runner.run(runner, engine)`, `Runner.stop(runner)`, `Runner.tick` for your own loop |
| Single step | `Engine.update(engine, delta)` (fixed delta, e.g. `1000 / 60`) |
| Debug view | `Render.create({ engine, element, options })`, `Render.run`, `Render.stop` |

Common body options: `isStatic`, `restitution` (bounce), `friction`, `frictionAir`, `density`, `isSensor`, `collisionFilter: { category, mask, group }`, `render: { fillStyle }`.

## Deprecated or changed

| Old | Use |
| --- | --- |
| `World.add(world, b)` | `Composite.add(world, b)` |
| `Engine.run(engine)` | `Runner.run(Runner.create(), engine)` |
| `engine.world` events on World | `Events.on(engine, ...)` |

## Common mistakes

- Treating `position` as top-left. It is the center.
- Thin static walls that fast bodies tunnel through. Make them thick.
- Scaling a body with `Body.scale` every frame, which compounds. Scale once.
- Forgetting to stop `Runner` / `Render` and remove `Events` listeners on unmount, leaking loops.
- Setting `velocity` directly on bodies each frame and wondering why collisions feel wrong; use forces or `Body.setVelocity` deliberately.
- Running two loops (own `requestAnimationFrame` plus `Runner`) that both step the engine.
