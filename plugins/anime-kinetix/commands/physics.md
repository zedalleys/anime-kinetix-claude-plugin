---
description: Add or debug Matter.js 2D physics, optionally combined with Anime.js or PixiJS rendering
argument-hint: "<what should fall, collide, or be dragged>"
---

Use the `anime-kinetix:matterjs` skill (plus `anime-kinetix:animejs` for scripted animation, and `anime-kinetix:pixijs` when many bodies or a polished render are needed). Task: $ARGUMENTS

1. Detect the framework and whether `matter-js` (and `pixi.js`) are installed (`npm ls matter-js pixi.js`). Install only if the user agrees. If the project is React Native, say Matter.js needs a DOM/canvas and propose a web or DOM-component approach instead of writing code that cannot run.
2. Decide who owns what: Matter for anything that collides, Anime.js for scripted motion, Pixi for drawing when there are more than a few dozen bodies. Do not animate one property from two owners.
3. Write the simulation with current APIs (`Composite.add`, `Runner.create` + `Runner.run`), thick static walls, and a render path (DOM transforms from `afterUpdate`, Matter `Render`, or Pixi following `skills/pixijs/references/matter-recipe.md`).
4. Add teardown: `Runner.stop`, `Render.stop`, `Events.off`, `Engine.clear`, and `app.destroy(true, { children: true })` for Pixi.
5. Respect `prefers-reduced-motion`: when set, do not start the simulation; render the bodies at rest in their final layout.
