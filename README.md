```
   _____ __  ______  __________  _   _______ _   ______
  / ___// / / / __ \/ ____/ __ \/ | / / __ \ |  / /   |
  \__ \/ / / / /_/ / __/ / /_/ /  |/ / / / / | / / /| |
 ___/ / /_/ / ____/ /___/ _, _/ /|  / /_/ /| |/ / ___ |
/____/\____/_/   /_____/_/ |_/_/ |_/\____/ |___/_/  |_|

           ╔═══════════════════════════════════╗
           ║   Modified Nebulae v2 Firmware    ║
           ║    for live performance mangle    ║
           ╚═══════════════════════════════════╝
```

# SuperNova — Nebulae v2 Performance Firmware

> A modified `a_granularlooper.instr` for the Qu-Bit Nebulae v2, focused on
> **performability**, **audio mangling**, and **not being polite about it**.

Built on [TheTechnobear's enhanced firmware](https://github.com/TheTechnobear/QB_Nebulae_V2/tree/technobear) (v2.2.2).

---

## What Is This

SuperNova replaces the stock Nebulae v2 granular looper instrument with a version
designed for live performance destruction. It keeps everything that makes Nebulae
great — the phase vocoder, granular engine, recording — and bolts on controls
that actually make sense when you're performing.

Every secondary control has been repurposed. Grain muting, grain displacement,
grain panning, granular FM — gone. Replaced with chord grains, frequency shifting,
a five-mode filter bank, stereo widening, a Buchla LPG, and a performative killswitch.

**This is an instrument file (`.instr`), not a full firmware replacement.**
Your Nebulae stays intact. Drop it on USB. Load it. Go.

---

## Feature Overview

```
  ───────────────────────────────────────────────────────────────
                        SUPERNOVA v0.8                             
                                                        
    ✅ Killswitch ─────────── Freeze = momentary mute
    ✅ One-Shot Mode ──────── Trigger-to-play, no looping
    ✅ LPG Envelope ───────── Buchla 292t on one-shots
    ✅ Tail Dampener ──────── Transient-preserving crossfader
    ✅ Chord Grains ───────── Scale-based harmonic pitch stacking,
    ✅ Voice Detune ───────── Supersaw unison spread
    ✅ 5-Mode Filter ──────── LP / HP / Dual Peak / Crush / Comb
    ✅ Frequency Shifter ──── Hilbert-based bidirectional
    ✅ Stereo Widener ─────── Haas delay + allpass decorrelation
    ✅ Blend Reorder ──────── Live: Dry → Vocoder → Granular 
    🔲 Absolute Start/End ─── Replacement start/end concept     
    🔲 Gap Mode ───────────── Morphagene-style rhythmic gaps     
    🔲 Turntable Mode ─────── CDJ jog wheel scrub                
    🔲 Reverb ─────────────── Shimmer + saturation              
    🔲 Strum ──────────────── Grain timing distribution          
    🔲 Beat Slicing ───────── Rhythmic buffer chopping           
                                                                 
    ✅ = implemented   🔲 = planned
  ─────────────────────────────────────────────────────────────
```

---

## Signal Flow

```
                         ┌─────────────┐
                         │  USB FILES  │
                         │  or BUFFER  │
                         └──────┬──────┘
                                │
                    ┌───────────▼───────────┐
                    │       PHASOR          │
                    │  syncphasor @ kspeed  │
                    │  Start ──► End        │
                    └───┬───────────────┬───┘
                        │               │
              ┌─────────▼──────┐ ┌──────▼─────────┐
              │  PHASE VOCODER │ │    GRANULAR    │
              │    (mincer)    │ │  (partikkel)   │
              │                │ │                │
              │  pitch-shift   │ │  density       │
              │  time-stretch  │ │  overlap       │
              │                │ │  chord grains  │
              │                │ │  voice detune  │
              └────────┬───────┘ └────────┬───────┘
                       │                  │
                       │    ┌─────────┐   │
                       └───►│  BLEND  │◄──┘
                            │         │
                            └────┬────┘
                                 │
                    ┌────────────▼────────────┐
                    │    ONE-SHOT GATE        │
                    │  (mute when idle, or    │
                    │   bypass for LPG mode)  │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │  5-MODE FILTER CHAIN    │
                    │  LP│HP│Peak│Crush│Comb  │
                    │  Window = param         │
                    │  Window_alt = resonance │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │   FREQUENCY SHIFTER     │
                    │  Hilbert + ring mod     │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │    STEREO WIDENER       │
                    │  Haas delay + allpass   │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │  TAIL DAMPENER / LPG    │
                    │  Loop: transient xfader │
                    │  One-shot: 292t LPG     │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │      KILLSWITCH         │
                    │  (Freeze = mute)        │
                    └────────────┬────────────┘
                                 │
                            ┌────▼────┐
                            │  OUT L  │
                            │  OUT R  │
                            └─────────┘
```

---

## Installation

```
1.  Use a USB drive as FAT32
2.  Copy  a_granularlooper.instr  to USB root directory
3.  Insert USB into Nebulae v2
4.  Power on (or hold File + tap Source to hot-reload)
5.  The instrument loads as the default granular looper
```

> ⚠️ **Requires TheTechnobear firmware v2.2.2 or compatible fork.**
> Stock Qu-Bit firmware has not been tested.

> ⚠️ **The filename MUST be `a_granularlooper.instr`** for the UI layer
> (recording LEDs, file indicators) to function correctly.

---

## Controls Reference

### Primary Controls

```
  ┌──────────────────────────────────────────────────────────────┐
  │                                                              │
  │    START ●                              ● SIZE               │
  │    Loop start offset     SPEED ◉        Relative loop size   │
  │    (stock behavior)    ← x4  x1  x4 →  (stock behavior)      │
  │                                                              │
  │    DENSITY ●                            ● OVERLAP            │
  │    Grain rate            PITCH ◉        Grain size           │
  │    0 → audio rate      -3oct  +2oct     + overlap amount     │
  │                                                              │
  │    BLEND ●                              ● WINDOW             │
  │    Vocoder ← → Grain    ───────────     ✦ Filter parameter   │
  │    (Live: Dry center)                   (cutoff/morph/crush) │
  │                                                              │
  └──────────────────────────────────────────────────────────────┘
```

| Control   | Range | Function                                       | SuperNova Change                         |
|-----------|-------|------------------------------------------------|------------------------------------------|
| Start     | 0–1   | Loop start offset in file/buffer               | —                                        |
| Size      | 0–1   | Relative loop size from start point            | —                                        |
| Speed     | 0–1   | Playback speed (±4x), center = 1x              | —                                        |
| Pitch     | 0–1   | Pitch shift, -3 to +2 octaves                  | ✦ 2ms smoothing added                    |
| Density   | 0–1   | Grain trigger rate                             | ✦ Freeze_alt gate removed (always works) |
| Overlap   | 0–1   | Grain size + overlap amount                    | —                                        |
| Blend     | 0–1   | Mix: phase vocoder ↔ granular                  | ✦ Live mode reordered (Dry→Voc→Grain)    |
| Window    | 0–1   | ~~Grain window shape~~ → **Filter parameter**  | ✦ Grain window locked Gaussian           |

### Buttons

| Button  | Primary Function              | SuperNova Change                              |
|---------|-------------------------------|-----------------------------------------------|
| Record  | Record to buffer              | —                                             |
| File    | Advance to next file          | —                                             |
| Source  | Toggle File/Live mode         | Hold for secondary menu                       |
| Reset   | Reset to start position       | Also triggers one-shot playback               |
| Freeze  | ~~Spectral freeze (latch)~~   | ✦ Killswitch — momentary mute (hold to kill)  |

### Secondary Controls (Hold Source)

| Control        | Stock Function       | SuperNova Function                             |
|----------------|----------------------|------------------------------------------------|
| Source+Pitch   | Pitch fluctuation    | ✦ **Chord Grains** — Maj7 stacking             |
| Source+Start   | Grain position rand  | — (unchanged)                                  |
| Source+Size    | Grain panning        | ✦ **Stereo Widener** — Haas + allpass          |
| Source+Density | Grain displacement   | ✦ **Voice Detune** — ±50 cent drift            |
| Source+Overlap | Grain size rand      | ✦ **Tail Dampener** (loop) / **LPG** (one-shot)|
| Source+Window  | Grain muting         | ✦ **Filter Resonance / Feedback**              |
| Source+Blend   | Granular FM          | ✦ **Frequency Shifter** — Hilbert transform    |
| Source+Freeze  | Grain trigger mode   | ✦ **One-Shot Mode** toggle                     |
| Source+Reset   | (none)               | ✦ **Filter Type** cycling (5 modes)            |
| Source+Record  | Circular recording   | — (unchanged)                                  |
| Source+File    | File order           | — (unchanged)                                  |

---

## Feature Details

### Killswitch (Freeze Button)

Freeze is no longer a spectral freeze. It's a Buckethead-style performative mute.
Hold Freeze = silence. Release = audio. 0.3ms response time — fast enough for
16th note gate patterns at 140 BPM.

The nebconfig changes Freeze from latching to `momentary,state`, so `gkfreeze = 1`
while the button is physically held down.

### One-Shot Mode (Source+Freeze)

Toggle one-shot mode on/off with Source+Freeze. When active, the sample/buffer
plays once through (respecting Start/Size positions) and stops. Trigger again
with the Source gate input or Reset button/gate.

The phasor freezes when idle (no position drift). Re-triggering while playing
resets the playthrough.

### LPG Envelope (Source+Overlap in One-Shot Mode)

When in one-shot mode, Source+Overlap becomes a Buchla 292t-style Low Pass Gate.

The LPG opens instantly on trigger, then decays exponentially. The VCA and filter
track together — brightness fades with volume, darkening the tail naturally.
The squared filter tracking means cutoff drops faster than amplitude, giving the
characteristic 292t "plonk" that darkens before it silences.

```
  Source+Overlap (One-Shot Mode):
  CCW(0) = off — hard gate, stock one-shot behavior
  CW(1) = long decay — 5 second vactrol-style tail

  Decay time: 50ms (barely open) → 5s (sustaining wash)
  Squared knob curve for musical feel
```

When the one-shot finishes playing, the phasor freezes and the engines hold
their last audio frame. The LPG envelope shapes that frozen frame into a natural
decay tail — exactly how a vactrol LPG sustains a resonant pluck.

Re-triggering while the LPG is still decaying snaps it back open instantly.

### Tail Dampener (Source+Overlap in Loop Mode)

When looping normally, Source+Overlap becomes a transient-preserving crossfader.
It kills sustain tails while preserving attack transients.

Dual-envelope follower: fast envelope (10ms release) tracks signal tightly while
slow envelope (release scales with knob) holds recent peaks. The gain ratio
(fast / slow) passes attacks through (both high → ~1.0) while killing tails
(fast drops while slow holds → gain approaches 0).

```
  Source+Overlap (Loop Mode):
  CCW(0) = off — full natural decay
  CW(1) = maximum dampening — attacks punch, tails vanish
```

### Chord Grains (Source+Pitch)

Progressive harmonic pitch stacking across partikkel's four grain voices.
Replaces the stock random pitch fluctuation with deterministic chord intervals.

```
  Source+Pitch:
  CCW(0.0)    = unison — all 4 voices at root pitch
  0.0–0.5     = root + perfect 5th fading in
  0.5–0.75    = root + 5th + major 3rd fading in
  0.75–1.0    = root + 5th + 3rd + major 7th fading in (full Maj7)
```

Fixed chord quality: Major 7th (P5 = 7 semitones, M3 = 4 semitones, M7 = 11 semitones).
Volume stays constant — chord effect is purely pitch-based. When chord is off,
all 4 voices play at unison (identical to stock behavior). Max grains bumped to 20
(from stock 10) to support denser chord textures.

### Voice Detune (Source+Density)

Per-voice random pitch drift that stacks with chord intervals.

```
  Source+Density:
  CCW(0) = tight unison, no drift
  CW(1) = ±50 cents per voice, independent slow random drift
```

Each voice gets its own LFO rate (1.1, 1.7, 2.3 Hz) for organic movement.
With chord off: supersaw unison texture. With chord on: detuned chords.

### Multi-Type Filter (Source+Reset + Window + Window_alt)

Five filter types cycled forward by tapping Source+Reset (wraps 4→0).
Window knob controls the primary parameter. Window_alt (Source+Window)
controls resonance/feedback.

No bypass state — each filter has a transparent position in its knob range.
Switch to LP and park Window CW for effectively transparent operation.

```
  Type 0: LOWPASS — moogladder 4-pole
          Window: 300Hz (CCW) → 22kHz (CW, transparent)
          Resonance: clean → self-oscillation screaming

  Type 1: HIGHPASS — atonex 4-pole + resonant peak (resonx)
          Window: 20Hz (CCW, transparent) → 8kHz (CW)
          Resonance: clean → resonant peak at cutoff

  Type 2: DUAL PEAK — two reson filters at harmonic ratio (2.5x)
          Window: 100Hz (CCW) → 8kHz (CW)
          Resonance: dry (off) → two singing resonant peaks

  Type 3: BITCRUSHER — dual-axis destruction
          Window: clean 48kHz (CCW) → 200Hz sample rate (CW)
          Resonance: clean 24-bit (off) → ~2-bit quantization (CW)
          Post-crush saturation + warmth filter

  Type 4: COMB± — bipolar comb filter
          Window: low pitch (CCW) → high pitch (CW)
          Resonance: transparent (off) → positive feedback (0→0.5)
                     → negative feedback (0.5→1.0)
          LP in feedback path for warm tails. L/R stereo detune.
```

### Frequency Shifter (Source+Blend)

Bidirectional frequency shift via manual Hilbert transform (biquad allpass network).

```
  Source+Blend:
  CCW(0.0) = -1000 Hz (down shift, 2x range)
  Noon(0.5) = bypass (hard ±2Hz deadzone)
  CW(1.0)  = +500 Hz (up shift)
```

Default alt value is 0.5 (bypass). The asymmetric range gives more downshift
headroom — down-shifting is musically more useful and less likely to alias.

### Stereo Widener (Source+Size)

Haas delay + allpass decorrelation for genuine mono→stereo widening.

```
  Source+Size:
  CCW(0) = off (mono-compatible)
  CW(1) = max spread (25ms Haas + allpass phase scramble)
```

Slow LFO modulation (0.25 Hz) prevents static comb filtering. Gentle warmth
filter (tonex 12kHz) on the widened channel prevents harshness. Capped at
90% wet to maintain center image stability.

### Blend Reorder (Live Mode)

Live mode blend order changed for performance logic:

```
  Stock:     CCW = Vocoder    Noon = Dry      CW = Granular
  SuperNova: CCW = Dry        Noon = Vocoder  CW = Granular
```

Start with your clean input, then sweep CW into processed sound.
File mode blend is unchanged (stock constant-power crossfade).

---

## Future Roadmap

```
  Phase 1 — Core Engine ✅
  ├── Killswitch (momentary mute)
  ├── One-Shot Mode
  ├── LPG Envelope (292t-style)
  ├── Tail Dampener / Crossfader
  ├── Chord Grains + Voice Detune
  ├── 5-Mode Filter Chain
  ├── Frequency Shifter
  ├── Stereo Widener
  └── Live Blend Reorder

  Phase 2 — Playback Modes
  ├── 🔲 Absolute Start/End (independent positions)
  ├── 🔲 Gap Mode (Morphagene-style rhythmic gaps)
  └── 🔲 Turntable Mode (CDJ jog wheel scrub)

  Phase 3 — Effects & Synthesis
  ├── 🔲 Reverb (shimmer + saturation)
  ├── 🔲 Strum (grain timing distribution — tables defined, not wired)
  └── 🔲 Beat Slicing (rhythmic buffer chopping)
```

---

## Nebconfig

```
nebconfigbegin
ksmps,64
-B,2048
-b,512
sr,48000
freeze,momentary,state
reset,triggered,rising
source,latching,falling
file,incremental,falling
record,latching,rising
blend_alt,0.5
window_alt,0
overlap_alt,0
loopsize_alt,0
density_alt,0
nebconfigend
```

| Parameter    | Value           | Note                                         |
|--------------|-----------------|----------------------------------------------|
| ksmps        | 64              | Samples per k-cycle                          |
| -B           | 2048            | Buffer size                                  |
| -b           | 512             | Period size                                  |
| sr           | 48000           | Sample rate                                  |
| freeze       | momentary,state | ✦ Changed from latching (killswitch)         |
| blend_alt    | 0.5             | ✦ Freq shifter default = bypass (noon)       |
| window_alt   | 0               | ✦ Filter resonance default = off             |
| overlap_alt  | 0               | ✦ Tail dampener / LPG default = off          |
| loopsize_alt | 0               | ✦ Stereo widener default = off               |
| density_alt  | 0               | ✦ Voice detune default = off                 |

---

## Architecture Notes

### File Structure

SuperNova is a single `.instr` file that replaces the stock granular looper.
No Python or firmware modifications required.

```
  USB Drive (FAT32)
  └── a_granularlooper.instr    ← drop this here
```

### Engine Architecture

```
  Python UI Layer (ui.py, remotehandler.py)
       │
       │  control channels (gkpitch, gkspeed, gkloopstart, etc.)
       │
       ▼
  Csound Runtime
       │
       ├── instr 1  ─── Main DSP (runs forever)
       │   ├── Phasor (syncphasor)
       │   ├── Phase Vocoder (mincer)
       │   ├── Granular Engine (partikkel × 2, L+R with chord voices)
       │   ├── Mixer / Blend (Alex reorder for Live mode)
       │   ├── One-Shot Gate (hard mute or LPG bypass)
       │   ├── Filter Chain (5 types, Source+Reset cycling)
       │   ├── Frequency Shifter (HilbertIIR UDO + ring mod)
       │   ├── Stereo Widener (Haas delay + allpass)
       │   ├── Tail Dampener / LPG (dual-envelope or 292t)
       │   ├── Killswitch (Freeze momentary mute)
       │   └── Output (outs)
       │
       └── instr 2  ─── Buffer copy helper (short-lived)
```

### Repurposed Controls Summary

| Control        | Stock Function            | SuperNova Function                      |
|----------------|---------------------------|-----------------------------------------|
| Window knob    | Grain window shape        | Filter parameter (cutoff/morph/crush)   |
| Freeze button  | Spectral freeze (latch)   | Killswitch (momentary mute)             |
| Source+Pitch   | Pitch fluctuation         | Chord Grains (Maj7 stacking)            |
| Source+Size    | Grain panning             | Stereo Widener                          |
| Source+Density | Grain displacement        | Voice Detune (±50 cent drift)           |
| Source+Overlap | Grain size randomizer     | Tail Dampener (loop) / LPG (one-shot)   |
| Source+Window  | Grain muting              | Filter Resonance / Feedback             |
| Source+Blend   | Granular FM               | Frequency Shifter                       |
| Source+Freeze  | Grain trigger mode        | One-Shot Mode toggle                    |
| Source+Reset   | (none)                    | Filter type cycling                     |

### Custom UDOs

| UDO          | Purpose                         | Opcodes Used        |
|--------------|----------------------------------|---------------------|
| HilbertIIR   | Manual Hilbert transform         | biquad (6.05 safe)  |

### Key Technical Decisions

| Decision | Rationale |
|----------|-----------|
| `a()` wrapping for k-rate → a-rate | Prevents amplitude modulation artifacts |
| Freeze changed to momentary | Required for performative killswitch |
| Grain window locked to Gaussian | Window knob freed for filter control |
| Grain muting disabled (krandommask=0) | Window_alt freed for filter resonance |
| Grain FM disabled | Blend_alt freed for frequency shifter |
| imax_grains bumped to 20 | Supports dense chord grain textures |
| 2ms portk on pitch | Rejects Python control layer glitches |
| Exponential decay for LPG | Natural vactrol-like response curve |
| Squared LPG filter tracking | Filter closes faster than VCA (292t character) |
| -6.9078 decay constant | ln(0.001) — reaches -60dB floor precisely |
| Dual follow2 for tail dampener | Transient-preserving without phase artifacts |
| Formant + strum tables pre-allocated | Ready for future wiring, zero runtime cost |

---

## Development Notes

### Debugging on Nebulae

There are **no stack traces, no error logs, and no debugger.** If something
goes wrong, the module either boot-loops or produces silence. Development
is done surgically:

1. Change one thing at a time
2. Verify against factory behavior
3. Test both File and Live modes
4. Listen for: clicks, amplitude modulation, silence, winding sounds
5. If it boot-loops, your Csound syntax is wrong — check `if/endif` balance

### Common Pitfalls

```
  ✗ Forgetting a() cast on k-rate vars feeding audio-rate opcodes
  ✗ Mismatched if/endif nesting (instant boot loop)
  ✗ Using opcodes not available in Csound ~6.05
  ✗ Wrong filename (UI layer won't show recording LEDs)
  ✗ Modifying Python files (breaks everything, gains nothing)
  ✗ K-rate staircase into interp (produces bursts, not smooth audio)
```

### Building & Testing

```bash
# There is no build step. It's Csound interpreted at runtime.
# Copy to USB, insert, power on. That's it.

# To hot-reload without rebooting:
# Hold File button + tap Source button on the Nebulae
```

---

## Requirements

| Requirement            | Details                                     |
|------------------------|---------------------------------------------|
| Hardware               | Qu-Bit Nebulae v2                           |
| Recommended Firmware   | TheTechnobear v2.2.2 fork                   |
| USB Drive              | FAT32 formatted                             |
| Csound Version         | ~6.05 (ships with Nebulae firmware)         |
| Audio Format           | .wav, .flac, .aif — any sample rate/depth   |

---

## Credits & Lineage

```
  Qu-Bit Electronix ── Original Nebulae v2 hardware & firmware
       │                (Stephen Hensley, San Clemente CA, 2017)
       │
  TheTechnobear ────── Enhanced firmware fork (v2.2.2)
       │                WiFi, SuperCollider, QoL improvements
       │
  SuperNova ────────── This project
                        Performance modifications & extensions
```

**SuperNova contributors:**
- Andy (kuttor)

---

## License

This project modifies open-source firmware. Original Nebulae v2 firmware
by Qu-Bit Electronix. TheTechnobear fork used as base.

See upstream repositories for license details:
- https://github.com/TheTechnobear/QB_Nebulae_V2/tree/technobear
- https://github.com/Qu-Bit-Electronix/QB_Nebulae_V2

---

```
  "The magic comes from a combination of disparate processing
   techniques which coalesce into something more meaningful
   when experienced together."
                                    — Andrew Ikenberry, Qu-Bit
```
