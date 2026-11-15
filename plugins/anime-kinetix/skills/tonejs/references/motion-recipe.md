# Recipe: impact sounds for Matter.js collisions

```js
import * as Tone from "tone";
import Matter from "matter-js";
const { Events } = Matter;

let ready = false;
const synth = new Tone.PolySynth(Tone.Synth, {
  oscillator: { type: "triangle" },
  envelope: { attack: 0.001, decay: 0.12, sustain: 0, release: 0.05 },
}).toDestination();
Tone.getDestination().volume.value = -14;

// unlock audio from a real user gesture
document.querySelector("#sound-toggle").addEventListener("click", async () => {
  await Tone.start();
  ready = !ready;
});

let last = 0;
const notes = ["C4", "D4", "E4", "G4", "A4", "C5"];

Events.on(engine, "collisionStart", (e) => {
  if (!ready) return;
  const now = performance.now();
  if (now - last < 40) return;               // rate-limit
  for (const { bodyA, bodyB } of e.pairs) {
    const speed = Math.hypot(
      bodyA.velocity.x - bodyB.velocity.x,
      bodyA.velocity.y - bodyB.velocity.y,
    );
    if (speed < 1.5) continue;               // ignore resting contact
    const note = notes[Math.floor(Math.random() * notes.length)];
    synth.triggerAttackRelease(note, "32n", Tone.now(), Math.min(1, speed / 12));
    last = now;
    break;
  }
});

// teardown
// Events.off(engine, "collisionStart", handler); synth.dispose();
```

Notes:
- Store the handler in a variable so `Events.off` can remove it.
- The third `triggerAttackRelease` argument is the time, the fourth is velocity (0 to 1).
- Resting stacks fire constant tiny collisions; the speed threshold and rate limit are what keep it from sounding like static.
- Offer the mute toggle; do not start audio without a click.
