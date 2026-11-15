---
name: tonejs
description: Use when adding sound to a web page with Tone.js (the `tone` npm package, Tonejs/Tone.js) - synths, samples, effects, scheduling with the Transport, browser audio-unlock rules, and syncing audio to animation or Matter.js collisions.
---

# Tone.js

Web Audio framework for interactive music and sound (MIT). Stable on npm: `tone@15.1.22` (`tone@next` is 15.5.72 and tracks the dev branch). The checks below come from the repo README and `Tone/index.ts`.

## Rules

1. Install with `npm i tone` and `import * as Tone from "tone"`. It is one namespace.
2. **Browsers block audio until a user gesture.** Call `await Tone.start()` inside a click/keydown handler before any sound. Scheduling or playing before the context is running gives silence or wrong timing. Never start audio on page load.
3. Time is in seconds on the AudioContext clock. `Tone.now()` gives the current time. Methods accept seconds or tempo strings: `"8n"` (eighth note), `"4n"`, `"8t"` (triplet), `"1m"` (measure).
4. **Schedule with the `time` argument, not with JavaScript timers.** Callbacks run at imprecise moments but receive the exact audio time: `new Tone.Loop((time) => synth.triggerAttackRelease("C2", "8n", time), "4n").start(0)`. Never use `setInterval` for rhythm.
5. The main timekeeper is `Tone.getTransport()` (start, stop, loop, `bpm.rampTo`). Loops and Parts only run after `Tone.getTransport().start()`.
6. Instruments: `Synth`, `FMSynth`, `AMSynth`, `NoiseSynth` are monophonic. Use `new Tone.PolySynth(Tone.Synth)` for chords or overlapping hits. `Tone.Sampler` is already polyphonic.
7. Samples: `new Tone.Player(url).toDestination(); await Tone.loaded();` before `start()`.
8. Route with `source.connect(effect)` and `.toDestination()`. Effects like `Tone.Filter`, `Tone.Distortion`, `Tone.FeedbackDelay` can be chained or run in parallel.
9. Automate parameters with signals: `osc.frequency.rampTo("C2", 2)`.
10. Dispose what you create when the view unmounts (`node.dispose()`, and stop the Transport) so nodes and loops do not pile up. Confirm the method names in the installed types.

## Minimal example

```js
import * as Tone from "tone";

const synth = new Tone.Synth().toDestination();

document.querySelector("button").addEventListener("click", async () => {
  await Tone.start();                       // required, from a user gesture
  synth.triggerAttackRelease("C4", "8n");
});
```

## Sound for motion

Short hits for physics or UI, not music:

- Create one `PolySynth` or a few `Player`s up front; do not construct nodes per event.
- Trigger at `Tone.now()` (immediate) from an event handler such as Matter's `collisionStart`.
- Map impact strength to velocity/pitch, and rate-limit (for example, at most one hit per 40 ms) so a pile of colliding bodies does not become noise.
- Keep a mute control and a modest master volume (`Tone.getDestination().volume.value = -12`).
- The recipe is in `references/motion-recipe.md`.

## Combining

- **Matter.js:** play a hit on `collisionStart`, scaled by relative speed.
- **Anime.js / Pixi:** drive visuals from the same moment you schedule audio. For beat-synced visuals, schedule the visual change at the audio `time` rather than at the callback's wall-clock time (`Tone.getDraw()` exists for this; check its API in the installed types).
- Audio has no equivalent of `prefers-reduced-motion`, so offer a mute toggle and never autoplay.

## Reference

`references/api.md` for classes, common mistakes and the time notation.
