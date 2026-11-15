# Recipe: Pixi renders, Matter simulates

Matter owns positions and rotation. Pixi owns pixels. One display object per body, written after each physics step.

```js
import { Application, Graphics } from 'pixi.js';
import Matter from 'matter-js';
const { Engine, Runner, Bodies, Composite, Events } = Matter;

const app = new Application();
await app.init({ background: '#111', resizeTo: window });
document.body.appendChild(app.canvas);

const engine = Engine.create();
const T = 200; // thick static walls
const W = app.screen.width, H = app.screen.height;
Composite.add(engine.world, [
  Bodies.rectangle(W / 2, H + T / 2, W + 2 * T, T, { isStatic: true }),
  Bodies.rectangle(-T / 2, H / 2, T, H * 4, { isStatic: true }),
  Bodies.rectangle(W + T / 2, H / 2, T, H * 4, { isStatic: true }),
]);

const pairs = [];
for (let i = 0; i < 40; i++) {
  const r = 14 + Math.random() * 14;
  const body = Bodies.circle(Math.random() * W, -Math.random() * H, r, { restitution: 0.5 });
  const view = new Graphics().circle(0, 0, r).fill(0xffd166); // origin at center, like a body
  app.stage.addChild(view);
  Composite.add(engine.world, body);
  pairs.push({ body, view });
}

const runner = Runner.create();
Runner.run(runner, engine);
Events.on(engine, 'afterUpdate', () => {
  for (const { body, view } of pairs) {
    view.position.set(body.position.x, body.position.y);
    view.rotation = body.angle;
  }
});

// teardown
// Runner.stop(runner); Engine.clear(engine); app.destroy(true, { children: true });
```

Notes:
- Draw shapes centered on their origin so `position` and `rotation` map directly from the body.
- Use Matter's own loop (`Runner`) and write to Pixi in `afterUpdate`; do not also step the engine from `app.ticker`.
- Reduced motion: do not start the `Runner`; place bodies at rest, copy positions once, call `app.render()`.
- For thousands of bodies, use `ParticleContainer` and keep bodies simple (circles).
