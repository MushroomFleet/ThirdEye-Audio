# ThirdEye

> See what you hear.

ThirdEye is an audio visualization environment for Windows. Point it at your
music — a file, your microphone, or whatever's playing through your speakers —
and the screen responds in real time. Spectrum bars breathe with the bass.
Particles burst on every snare hit. A fractal flythrough turns with the
tempo. Mercury cyan lines trace where the loudest frequencies live.

ThirdEye is built for people who want the visual equivalent of a **modular
synth** — every visualizer is small and sharp, and you combine three of them
into compositions no single visualizer could produce alone. You're composing,
not skinning.

---

## Download

The latest release lives at:

**[github.com/MushroomFleet/ThirdEye-Audio/releases/latest](https://github.com/MushroomFleet/ThirdEye-Audio/releases/latest)**

Grab `ThirdEye_<version>_x64_en-US.msi` and run it. Windows 11 (x64) is the
supported target.

### First install — the SmartScreen warning

Until ThirdEye is signed with a code-signing certificate, Windows SmartScreen
will warn you that "Windows protected your PC" on first install. This is the
expected behaviour for any unsigned installer.

To install anyway:

1. Click **More info** in the SmartScreen dialog
2. Click **Run anyway**
3. The installer proceeds normally

The installer drops ThirdEye in `Program Files\ThirdEye\`. Uninstall via the
standard Add/Remove Programs control panel.

---

## How to use it

### The viewport

The big dark rectangle in the centre is the **viewport** — three visual layers
composited on top of each other:

- **Slot A** sits at the back
- **Slot B** in the middle
- **Slot C** on top

Each slot has its own visualizer, blend mode (screen, multiply, lighten…),
and opacity. The tabs in the bottom-left corner of the viewport let you pick
which slot you're currently editing.

### Picking an audio source

The **Source** dropdown in the top-left cycles through the available inputs:

| Source | What it does |
|---|---|
| **Microphone** | Listens through any input device — works for live performance, room recording, voice |
| **System Loopback** | Captures whatever's playing through your speakers — Spotify, YouTube, your DAW, anything |
| **File** | Opens a music file from disk (`.wav`, `.mp3`, `.flac`, `.ogg`, `.m4a`, `.opus`…). Set a default music folder in Settings → Library so the dialog opens at the right place |
| **Demo Synth** | A bundled 124 BPM loop — useful for tuning visuals without needing music ready |

### Loading visualizers

The left rail lists 29 visualizers grouped by category — spectrum, scope,
particles, geometry, fractal, glitch, reactive typography, cymatics,
generative, video. Click one to load it into the currently-selected slot,
or drag it into the viewport to drop it into any slot.

Each visualizer has its own parameters in the right rail — knobs for things
like particle count, displacement, glow, rotation rate. Twist them while
music plays; you'll feel which parameter does what immediately.

### Mapping audio features to parameters

ThirdEye's strongest move is letting you bind any visualizer parameter to
the live audio. Click the **Mapping** button in the top-right, pick a
parameter (say `A.bassPulse`), drag a feature pill (say `bands.bass`) into
the expression box, and the parameter follows the music.

Expressions can be more than just direct mappings:

```
lerp(0.6, 1.6, bands.bass) + 0.1 * sin(barPhase * TAU)
```

That one says: scale the bass band from `0.6..1.6`, then add a small wobble
locked to the bar phase. The editor shows you the live value + a sparkline
of the last two seconds so you can tune the response by ear and eye.

If you have a MIDI controller plugged in, the **MIDI Learn** button captures
the next knob you turn and binds that CC to the same parameter — physical
control overrides the expression in real time.

### Post-FX

Below the viewport, five chips toggle the post-processing chain:

- **Bloom** — bright pixels glow softly
- **CA** (Chromatic Aberration) — channels split, like an old CRT
- **Grain** — film grain overlay
- **Vignette** — soft dark corners
- **Feedback** — phosphor trail; previous frames blend into the current

Toggle them freely. If your machine starts breathing hard, ThirdEye's
**Adaptive Quality** (Settings → Performance) auto-drops the heavier passes
to keep the framerate up, and restores them when it can.

### Saving and loading presets

A **preset** captures everything visible — which visualizers are in each
slot, their parameters, post-FX state, mappings, MIDI bindings. Open the
**Presets** overlay, name your loadout, click **Save current**. Presets
live in `%USERPROFILE%\ThirdEye\presets\` as readable JSON, easy to back
up or share.

Loading a saved preset doesn't snap — by default it **morphs** over a
configurable number of bars at the current tempo. Every numeric parameter
eases between its old and new value with a single canonical curve, so the
transition feels musical, not abrupt.

### Settings worth knowing about

- **Settings → Audio** — input device, sample rate, echo / AGC toggles (disable both for accurate meters)
- **Settings → Performance** — resolution scale, frame-budget HUD, adaptive quality, renderer preference, live frame-time graph
- **Settings → Library** — music folder (default folder the File picker opens at), preset path
- **Settings → MIDI** — current routings (each binding shows its target + CC number)
- **Settings → About** — version + the in-app update check (the update endpoint is being stood up for v1.x — for now, watch the GitHub releases)

---

## How this was built

ThirdEye is the output of a deliberate four-stage pipeline that uses Claude
as a **design partner** and Claude Code as the **implementer**, with
human-authored grounding documents at each end:

```
   Grounding.md
       │
       ▼
   Claude Design
   (conversational)
       │
       ▼
   TINS handoff + Design.zip
       │
       ▼
   TINS + Claude Code
   (autonomous implementation)
       │
       ▼
   Distribution (.MSI · .DMG)
```

- **Grounding.md** — a single human-authored brief that establishes the
  product's intent, its aesthetic non-negotiables, and the rules the design
  must respect. For ThirdEye: "astronomical-instrument console; one accent
  pulses to the beat; everything else grayscale; modular synth, not skinning."

- **Claude Design** — a conversation with Claude refines the grounding into
  a complete design system: tokens, type scale, motion law, primitives,
  layout surfaces, every overlay. The output is pixel-decided HTML/CSS
  artboards plus design notes — a full visual register before a line of app
  code exists.

- **TINS handoff + Design.zip** — the design crystallises into a TINS
  (*There Is No Source*) specification. The spec is the source of truth: a
  capable agent can read it end-to-end and produce the entire app from
  scratch. The design bundle ships alongside as the visual reference.

- **TINS + Claude Code** — Claude Code reads the TINS spec, executes its
  11-phase implementation plan, and ships working code. Every architectural
  decision that deviates from the spec is recorded as a small markdown note
  so the spec stays honest about what got built and why.

- **Distribution** — the result is a single installer. Download it, run it,
  point it at your music.

The pipeline matters because it's reproducible. The same grounding +
design + TINS spec can land on a different runtime (a mobile app, a web
build, a hardware visualiser) by changing only the final implementation
stage. ThirdEye is the first proof.

---

## License

Apache 2.0. See `LICENSE`.
