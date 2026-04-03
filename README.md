# pinchpad 🤏🥁

webcam-powered drum sequencer you control by pinching your fingers. no clicks needed — just pinch over a cell to toggle it on/off and the beat loops.

![pinchpad](https://img.shields.io/badge/status-working-brightgreen) ![single file](https://img.shields.io/badge/single%20file-HTML-orange) ![zero deps](https://img.shields.io/badge/install-nothing-blue)

## what it does

- 8-step drum sequencer with 4 instruments (kick, snare, hi-hat, clap)
- pinch your thumb + index finger over a grid cell to toggle beats
- webcam feed renders as a grayscale background behind the UI
- all sounds are synthesized in-browser via Web Audio API — no samples needed
- lock mode prevents accidental pinch changes while performing

## how it works

**hand tracking** — MediaPipe Hands detects your thumb tip and index finger tip in real-time. when the distance between them drops below a threshold, that's a pinch.

**cell detection** — the pinch position is mapped to screen coordinates and hit-tested against actual grid cell bounding rects. no hardcoded pixel offsets, works at any screen size.

**audio** — each instrument is synthesized from scratch using oscillators and noise buffers routed through a warmth lowpass filter and compressor. kick uses a sine sweep + triangle transient, snare is bandpassed noise + body oscillator, hi-hat is highpassed short noise, clap uses staggered gain hits on bandpassed noise.

**sequencer** — uses the Web Audio API lookahead scheduler pattern for sample-accurate timing. schedules notes slightly ahead of the current time so there's no drift or jitter.

## controls

| control | action |
|---------|--------|
| **pinch** | toggle grid cells on/off |
| **click** | same as pinch, mouse fallback |
| **PLAY / STOP** | start/stop the loop |
| **CLEAR** | wipe all cells |
| **LOCK / UNLOCK** | freeze the grid so pinches don't change anything (only unlock responds to pinch when locked) |
| **BPM slider** | 60–200 BPM |

## run it

it's a single HTML file. no build step, no install, no server required.

**option 1** — open `index.html` directly in Chrome/Edge (needs to be served over localhost or HTTPS for webcam access)

**option 2** — use VS Code Live Server:
```
right-click index.html → Open with Live Server
```

**option 3** — python quick server:
```bash
python -m http.server 5500
# open http://localhost:5500
```

> needs webcam permission. if denied, falls back to click-only mode.

## tech

- [MediaPipe Hands](https://google.github.io/mediapipe/solutions/hands.html) — hand landmark detection
- [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) — synthesized drum sounds
- vanilla HTML/CSS/JS — no frameworks, no build tools

## the sounds

all four instruments are synthesized, no audio files:

- **kick** — sine oscillator sweeping 120→35Hz (sub) + triangle transient (click)
- **snare** — white noise through 600Hz HP + 7kHz LP (crack) + triangle oscillator 200→120Hz (body)
- **hi-hat** — short white noise burst through 5.5kHz HP + 12kHz LP
- **clap** — white noise through 1.8kHz bandpass with staggered gain envelope for that layered hand-clap feel

everything routes through a 9kHz lowpass warmth filter → dynamics compressor → speakers.

