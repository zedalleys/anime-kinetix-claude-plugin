# Tone.js reference

Source: https://github.com/Tonejs/Tone.js (MIT). npm `latest` is 15.1.22, `next` is 15.5.72. Docs: https://tonejs.github.io/docs/, examples: https://tonejs.github.io/examples/, time notation: https://github.com/Tonejs/Tone.js/wiki/Time.
Verified in the README: `import * as Tone`, `Tone.start()`, `Tone.now()`, `getTransport()`, `Loop`, `PolySynth`, `Player` + `loaded()`, `Sampler`, effects, signal `rampTo`. Verified in `Tone/index.ts`: `start`, `now`, `getTransport`, `getDestination`, `getDraw`, `getContext`, `loaded`. `dispose()` is from the API docs; confirm in the installed types.

## Common calls

| Need | API |
| --- | --- |
| Unlock audio | `await Tone.start()` inside a click/keydown handler |
| Current time | `Tone.now()` (seconds) |
| One note | `synth.triggerAttackRelease("C4", "8n", time?)` |
| Hold / release | `triggerAttack(note, time)` / `triggerRelease(time)` |
| Chords | `new Tone.PolySynth(Tone.Synth).toDestination()` |
| Sample | `new Tone.Player(url).toDestination(); await Tone.loaded(); player.start()` |
| Multisample | `new Tone.Sampler({ urls, baseUrl })` |
| Timeline | `Tone.getTransport().start()`, `.stop()`, `.bpm.rampTo(v, s)` |
| Repeating | `new Tone.Loop(cb, "4n").start(0)` |
| Effects | `source.connect(new Tone.Filter(400, "lowpass").toDestination())` |
| Volume | `Tone.getDestination().volume.value = -12` (dB) |
| Automation | `param.rampTo(value, seconds)` |

## Time notation

`"4n"` quarter, `"8n"` eighth, `"8t"` eighth triplet, `"1m"` measure, `"+0.5"` relative seconds, bare numbers are seconds.

## Common mistakes

- Playing before a user gesture and `Tone.start()`, so there is no sound.
- Scheduling with `setTimeout` / `setInterval` instead of the callback's `time`.
- Calling `Player.start()` before `await Tone.loaded()`.
- Using a monophonic `Synth` for overlapping hits (notes get cut or throw), instead of `PolySynth`.
- Creating new synths per event (memory and CPU) instead of reusing one.
- Forgetting the Transport: Loops and Parts do nothing until `getTransport().start()`.
- Not disposing nodes on unmount.
