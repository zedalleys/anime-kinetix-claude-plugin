---
description: Add or debug Matter.js 2D physics, optionally combined with Anime.js
argument-hint: "<what should fall, collide, or be dragged>"
---

Use the `anime-kinetix:matterjs` skill (and `anime-kinetix:animejs` if scripted animation is involved). Task: $ARGUMENTS

1. Detect the framework and whether `matter-js` is installed (`npm ls matter-js`). Install it only if the user agrees. If the project is React Native, say Matter.js needs a DOM/canvas and propose a web or DOM-component approach instead of writing code that cannot run.
2. Decide who owns what: Matter for anything that collides, Anime.js for scripted motion. Do not animate one property from both.
3. Write the simulation with current APIs (`Composite.add`, `Runner.create` + `Runner.run`), thick static walls, and a render path (Matter `Render`, canvas, or DOM transforms from `afterUpdate`).
4. Add teardown: `Runner.stop`, `Render.stop`, `Events.off`, `Engine.clear`.
5. Respect `prefers-reduced-motion`: when set, do not start the simulation; render the bodies at rest in their final layout.
