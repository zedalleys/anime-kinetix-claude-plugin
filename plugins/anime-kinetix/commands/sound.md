---
description: Add Tone.js sound to a web page, optionally synced to animation or physics
argument-hint: "<what should make sound and when>"
---

Use the `anime-kinetix:tonejs` skill (plus `anime-kinetix:matterjs` for collision sounds, `anime-kinetix:animejs` or `anime-kinetix:pixijs` when syncing visuals). Task: $ARGUMENTS

1. Detect the framework and whether `tone` is installed (`npm ls tone`). Install only if the user agrees. If the project is React Native, say Tone.js needs the Web Audio API, which React Native does not provide. Offer the working paths: run it in a WebView / Expo DOM component / Capacitor, or use a native audio library the project already has. If the user wants Web Audio on native, mention that a Web Audio implementation for React Native exists (for example `react-native-audio-api`) but that Tone.js compatibility with it is unverified.
2. Gate all audio behind a user gesture and `await Tone.start()`. Never autoplay.
3. Schedule with the callback `time` and the Transport, never `setTimeout` / `setInterval`. Reuse instruments; do not create them per event.
4. For physics sounds, use the recipe in `skills/tonejs/references/motion-recipe.md` (speed threshold, rate limit, velocity from impact).
5. Add a mute control, a modest master volume, and teardown (`dispose()`, stop the Transport, remove event listeners).
