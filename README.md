```
   _____ __  ______  ___________  _   _______ _    _____
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

**This is an instrument file (`.instr`), not a full firmware replacement.**
Your Nebulae stays intact. Drop it on USB. Load it. Go.

---

## Feature Overview

```
 ┌─────────────────────────────────────────────────────────────┐
 │                    SUPERNOVA v0.5                           │
 │                                                             │
 │  ✅ Absolute Start/End ──── Start and Size = exact points   │
 │  ✅ Gap Mode ───────────── Morphagene-style rhythmic gaps   │
 │  ✅ Killswitch ─────────── Speed button = performative mute │
 │  ✅ One-Shot Mode ──────── Trigger-to-play, no looping      │
 │  🔲 LPG Envelope ───────── Buchla 292-style on one-shots    │
 │  🔲 Crossfader ─────────── Output level control in loop mode│
 │  🔲 Filter Cycling ─────── LP/HP/BP/Comb/Resonant Body     │
 │  🔲 Chord Engine ───────── Harmonic pitch stacking          │
 │  🔲 Beat Slicing ───────── Rhythmic buffer chopping         │
 │                                                             │
 │  ✅ = implemented   🔲 = planned                            │
 └─────────────────────────────────────────────────────────────┘
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
              │  PHASE VOCODER │ │    GRANULAR     │
              │    (mincer)    │ │  (partikkel)    │
              │                │ │                 │
              │  pitch-shift   │ │  density        │
              │  time-stretch  │ │  overlap        │
              │                │ │  window shape   │
              └────────┬───────┘ └────────┬────────┘
                       │                  │
                       │    ┌─────────┐   │
                       └───►│  BLEND  │◄──┘
                            │         │
                            └────┬────┘
                                 │
                    ┌────────────▼────────────┐
                    │      ONE-SHOT GATE      │
                    │  (mute when not armed)  │
                    │                         │
                    │  🔲 LPG here when       │
                    │     implemented         │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │    GAP MODE GATE        │
                    │  (silence insertion)    │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │    KILLSWITCH GATE      │
                    │  (speed button mute)    │
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
1.  Format USB drive as FAT32
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
  │    Absolute start         SPEED ◉       Absolute end         │
  │    position in file     ← x4  x1  x4 →  position in file   │
  │                                                              │
  │    DENSITY ●                            ● OVERLAP            │
  │    Grain rate            PITCH ◉        Grain size           │
  │    0 → audio rate      -3oct  +2oct     + overlap amount     │
  │                                                              │
  │    BLEND ●                              ● WINDOW             │
  │    Vocoder ← → Grain    ───────────     Grain envelope       │
  │    (Live: Dry center)                   Gauss → Square       │
  │                                                              │
  └──────────────────────────────────────────────────────────────┘
```

| Control   | Range | Function                                       | SuperNova Change                   |
|-----------|-------|------------------------------------------------|------------------------------------|
| Start     | 0–1   | Absolute start position in file/buffer         | ✦ Now absolute, not offset         |
| Size      | 0–1   | Absolute end position in file/buffer           | ✦ Now absolute, not relative       |
| Speed     | 0–1   | Playback speed (±4x), center = 1x              | —                                  |
| Pitch     | 0–1   | Pitch shift, -3 to +2 octaves                  | —                                  |
| Density   | 0–1   | Grain trigger rate                             | —                                  |
| Overlap   | 0–1   | Grain size + overlap amount                    | —                                  |
| Blend     | 0–1   | Mix: phase vocoder ↔ granular                  | —                                  |
| Window    | 0–1   | Grain amplitude envelope shape                 | —                                  |

### Buttons

| Button  | Primary Function         | SuperNova Change                    |
|---------|--------------------------|-------------------------------------|
| Record  | Record to buffer         | —                                   |
| File    | Advance to next file     | —                                   |
| Source  | Toggle File/Live mode    | Hold for secondary menu             |
| Reset   | Reset to start position  | Also triggers one-shot playback     |
| Freeze  | ~~Freeze playback~~      | ✦ Momentary (nebconfig change)      |

### Speed Encoder Button

| Press              | Function                                      |
|--------------------|-----------------------------------------------|
| Click              | ✦ Killswitch — toggle output mute             |
| Hold (3 sec)       | Enter instrument selector (stock behavior)    |

### Secondary Controls (Hold Source)

| Control        | Stock Function       | SuperNova Change                         |
|----------------|----------------------|------------------------------------------|
| Source+Pitch   | Pitch fluctuation    | —                                        |
| Source+Start   | Grain position rand  | —                                        |
| Source+Size    | Grain panning        | —                                        |
| Source+Density | Grain displacement   | —                                        |
| Source+Overlap | Grain size rand      | 🔲 Crossfader (loop) / LPG Decay (1-shot)|
| Source+Window  | Grain muting         | —                                        |
| Source+Blend   | Granular FM          | —                                        |
| Source+Freeze  | ~~Grain trigger~~    | ✦ One-Shot mode toggle                   |
| Source+Reset   | (none)               | 🔲 Filter type cycling (planned)         |

---

## Feature Details

### Absolute Start / End

The stock Nebulae treats Start as an offset and Size as a relative window. This means
Size shrinks as Start increases — confusing in performance.

**SuperNova changes both to absolute positions:**

```
  Stock Behavior:                    SuperNova Behavior:
  ┌──────────────────────┐           ┌──────────────────────┐
  │ Start──►[===Size===] │           │ [Start]────────[Size] │
  │    ▲ offset    ▲ relative        │    ▲ absolute   ▲ absolute
  │    shrinks Size ──┘  │           │    independent ──┘    │
  └──────────────────────┘           └──────────────────────┘

  Start = 0.2, Size = 0.8:
  ├────[████████████████████████]────┤
  0.0  ▲                        ▲  1.0
       0.2 (Start)          0.8 (Size/End)
```

- **Start < Size** → Normal playback between the two points
- **Start > Size** → Gap Mode (see below)
- Both respond to CV normally

---

### Gap Mode (Morphagene-Style)

When the Size knob is turned **below** the Start knob position, SuperNova
enters Gap Mode — a rhythmic slice-and-silence pattern inspired by
the Make Noise Morphagene.

```
  Start = 0.6,  Size = 0.3  →  GAP MODE

  Audio file:
  ├──────────────────────────────────────────────┤
  0.0                    ▲                      1.0
                         0.6 (Start)

  Playback pattern:
  ┌────┐          ┌────┐          ┌────┐
  │PLAY│          │PLAY│          │PLAY│
  │40ms│          │40ms│          │40ms│
  └────┘──────────└────┘──────────└────┘
        ◄─ gap ──►      ◄─ gap ──►

  Gap length grows as Size moves further below Start.
  At Size = Start: no gap (continuous 40ms slice)
  At Size = 0:     maximum gap length
```

**How it works internally:**
- A 40ms audio slice is clamped at the Start position
- Gap length scales quadratically: `0.5 * (distance²) / bufferlen`
- Grains read from the slice center (or randomly within slice bounds)
- Output hard-mutes during the gap portion of each cycle
- Both vocoder and grain outputs are gated

---

### Killswitch

A Buckethead-inspired performative audio kill. Press the Speed encoder
button to instantly mute all output. Press again to unmute.

```
  Speed encoder button:
  ┌───────┐     ┌───────┐     ┌───────┐
  │ PRESS │ ──► │ MUTED │ ──► │ PRESS │ ──► unmuted
  └───────┘     └───────┘     └───────┘

  When muted:
  - aoutl = 0, aoutr = 0
  - Engines keep running (no click on unmute)
  - Phasor keeps advancing (no position jump)
```

> **Note:** The killswitch operates at the final output stage.
> Recording and internal processing continue unaffected.

---

### One-Shot Mode

Activated via **Source + Freeze** (same button combo as the stock
grain trigger mode, which it replaces).

When enabled, the sample/buffer **does not loop**. It plays once
from Start to Size and stops. Retriggered via the **Source gate input**
or the **Reset button/gate**.

```
  LOOP MODE (default):              ONE-SHOT MODE (Source+Freeze):
  ┌────────────────────┐            ┌────────────────────┐
  │ ╔══════════╗       │            │ ╔══════════╗       │
  │ ║ playing  ║ ──┐   │            │ ║ playing  ║       │
  │ ╚══════════╝   │   │            │ ╚══════════╝       │
  │       ▲        │   │            │       ▲      ┌───┐ │
  │       └────────┘   │            │       └──────│TRG│ │
  │    (loops forever) │            │   (waits)    └───┘ │
  └────────────────────┘            └────────────────────┘

  Trigger sources:
  ├── Source gate jack (primary)
  ├── Reset button
  └── Reset gate jack
```

**Trigger → Play → Stop → Wait → Trigger → ...**

The one-shot respects all current knob positions: Start, Size, Speed,
Pitch, Blend, Density — everything. It plays exactly what you'd hear
in loop mode, just once.

---

## Planned Features

### Crossfader / LPG (Source+Overlap)

Source+Overlap will become a dual-behavior control depending on playback mode:

```
  ┌─────────────────────────────────────────────────┐
  │              SOURCE + OVERLAP                   │
  │                                                 │
  │  LOOP MODE:         ONE-SHOT MODE:              │
  │  ┌──────────────┐   ┌──────────────────────┐   │
  │  │  CROSSFADER   │   │    LPG ENVELOPE      │   │
  │  │  Output level │   │  Buchla 292-style    │   │
  │  │  fade in/out  │   │                      │   │
  │  │  of buffer    │   │  VCA ──┐             │   │
  │  │               │   │       ├─► coupled    │   │
  │  │  CCW = silent │   │  VCF ──┘  decay      │   │
  │  │  CW  = full   │   │                      │   │
  │  └──────────────┘   │  CCW = short pluck    │   │
  │                      │  CW  = long swell    │   │
  │                      └──────────────────────┘   │
  └─────────────────────────────────────────────────┘
```

**LPG Character:**

```
  Trigger arrives:

  VCA ───────────╮
                  │    ┌─────────────────────────────────┐
  Amplitude:      │    │  ╱╲                              │
                  ├───►│ ╱  ╲                             │
                  │    │╱    ╲╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╌╲         │
  VCF ───────────╮│    │      exponential decay   ╲____   │
                  │    └─────────────────────────────────┘
  Cutoff:         │         ▲ attack    decay ▲
                  │         │           │
                  │    Source+Overlap controls both
                  │    (short pluck ◄──► long swell)
                  │
                  ▼
             ┌─────────┐
             │  OUTPUT  │
             └─────────┘
```

| Parameter       | Control          | Min (CCW)           | Max (CW)            |
|-----------------|------------------|---------------------|---------------------|
| Attack time     | Source+Overlap   | ~1ms (sharp pluck)  | ~50ms (soft swell)  |
| Decay time      | Source+Overlap   | ~30ms (tight plonk) | ~3s (long sustain)  |
| Filter tracking | Coupled to VCA   | 200 Hz (closed)     | 18 kHz (open)       |

- VCA opens slightly faster than filter → gives the "thonk" quality
- Exponential decay curves → natural, not synthy
- Filter and amplitude coupled but not identical → organic, woody
- CCW: percussive plinks, sample chops
- CW: full sustain, gentle fade

---

### Filter Cycling (Source+Reset)

Pressing Source+Reset will cycle through filter modes applied to the
main output:

```
  Source+Reset cycles through:

  ┌──────┐    ┌──────┐    ┌──────┐    ┌──────┐    ┌──────────┐
  │BYPASS│───►│  LP  │───►│  HP  │───►│  BP  │───►│ RESONANT │──┐
  │      │    │      │    │      │    │      │    │   BODY   │  │
  └──────┘    └──────┘    └──────┘    └──────┘    └──────────┘  │
      ▲                                                         │
      └─────────────────────────────────────────────────────────┘
```

| Mode          | Opcode(s)            | Character                           |
|---------------|----------------------|-------------------------------------|
| Bypass        | —                    | Clean signal                        |
| Lowpass       | moogladder           | Warm, analog, classic               |
| Highpass      | atonex + resonx      | Thin, cutting                       |
| Bandpass      | resonx               | Vocal, nasal, telephone             |
| Resonant Body | alpass + vcomb       | Tuned room / comb → reverb spectrum |

**Resonant Body** is a reverb-derived filter that combines allpass
diffusion with a tuned comb resonator. At low diffusion it acts as
a metallic comb filter; at high diffusion it becomes a resonant
room/reverb character. One mode, full spectrum from pluck to wash.

---

### Future Roadmap

```
  Phase 1 — Core Playback ✅
  ├── Absolute Start/End
  ├── Gap Mode
  ├── Killswitch
  └── One-Shot Mode

  Phase 2 — Dynamics & Filtering
  ├── 🔲 LPG Envelope (one-shot mode)
  ├── 🔲 Crossfader (loop mode)
  └── 🔲 Filter Type Cycling (Source+Reset)

  Phase 3 — Extended Synthesis
  ├── 🔲 Chord Engine (harmonic pitch stacking)
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
nebconfigend
```

| Parameter | Value           | Note                                        |
|-----------|-----------------|---------------------------------------------|
| ksmps     | 64              | Samples per k-cycle                         |
| -B        | 2048            | Buffer size                                 |
| -b        | 512             | Period size                                 |
| sr        | 48000           | Sample rate                                 |
| freeze    | momentary,state | ✦ Changed from latching (for killswitch)    |

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
       │   ├── Granular Engine (partikkel × 2, L+R)
       │   ├── Mixer / Blend
       │   ├── One-Shot Gate
       │   ├── Gap Mode Gate
       │   ├── Killswitch Gate
       │   └── Output (outs)
       │
       └── instr 2  ─── Buffer copy helper (short-lived)
```

### Repurposed Controls Summary

| Control        | Stock Function            | SuperNova Function                      |
|----------------|---------------------------|-----------------------------------------|
| Start knob     | Loop offset               | Absolute start position                 |
| Size knob      | Relative loop size        | Absolute end position                   |
| Freeze button  | Spectral freeze (latch)   | Momentary (nebconfig change)            |
| Speed button   | Reset speed / reverse     | Killswitch toggle                       |
| Source+Freeze  | Grain trigger mode        | One-Shot mode toggle                    |
| Source+Overlap | Grain size randomizer     | 🔲 Crossfader / LPG Decay              |
| Source+Reset   | (none)                    | 🔲 Filter type cycling                  |

### Key Technical Decisions

| Decision | Rationale |
|----------|-----------|
| `a()` wrapping for gap mode grain positions | Prevents k-rate → a-rate amplitude modulation artifacts |
| Freeze changed to momentary | Required for killswitch toggle behavior via speed button |
| 40ms clamp window | Below audible gap threshold, long enough for grain content |
| Quadratic gap scaling | Natural feel — small knob movements = small gaps |
| Output-stage muting only | Engines keep running, no clicks on gap boundaries |
| Phasor keeps running when muted | No position jumps on killswitch unmute |
| Phasor freeze in one-shot idle | No position drift while waiting for trigger |

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
- Andy (kuttor) — Direction, testing, feature design
- Amyl — Lead development, DSP implementation

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
