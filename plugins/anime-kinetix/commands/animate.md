---
description: Write or convert an Anime.js v4 animation from a description or v3 snippet
argument-hint: "<what should move, or paste v3 code>"
---

Use the `anime-kinetix:animejs` skill. Task: $ARGUMENTS

1. Detect the framework and the installed `animejs` version; install v4 only if the user agrees.
2. Write the animation with v4 APIs (`animate`, `createTimeline`, `stagger`), with cleanup via `revert()`/`createScope` where the framework needs it.
3. If given v3 code, convert it using the migration table in the skill and list what changed.
4. Respect `prefers-reduced-motion` for non-essential motion: check `window.matchMedia('(prefers-reduced-motion: reduce)').matches` and, when it is true, do not start the animation. Jump to the end state instead (`utils.set(targets, { ...finalProps })`) and never start `loop: true` animations. Do not use `duration: 0` as a substitute, because a zero-duration looping animation can spin. Keep essential feedback (for example a state change the user must see) as a short, non-looping fade.
