# HOLLOW PINES

A realistic first-person psychological horror game that runs in the browser at 60 FPS.

> **This entire game was created from a single prompt** using Claude Code —
> story, gameplay loop, stalking entity, two endings, fully procedural sound
> design and photoreal PBR graphics, all generated from one instruction with
> no hand-written code.

## Prompt:
```
Create a professional game a realistic first-person horror game that runs in the browser at 60 FPS.
The game takes place in a dark forest at night, with creepy atmosphere, immersive ambient sound design, and a clear objective required to finish the game.
Focus on realism, suspense, exploration, psychological horror, and performance optimization for web browsers.
Include the story, gameplay loop, mechanics, environment, audio design, technical stack, optimization strategy, roadmap, and ending.
```

## How to run

The game needs a **local server** (textures and the 3D model won't load over
`file://`) and an **internet connection** (Three.js is pulled from a CDN).

```powershell
# 1. open PowerShell in this folder (hollow-pines)
python -m http.server 8080

# 2. open in your browser
start http://localhost:8080
```

Stop the server with `Ctrl+C`. If you don't have Python, Node works too:

```powershell
npx serve -p 8080
```

Recommended browser: **Chrome or Edge**. Play with **headphones** — almost all
threat information is carried by spatial audio.

## Controls

| Key | Action |
|---|---|
| WASD | Move |
| Shift | Sprint (stamina-limited) |
| Mouse | Look |
| F | Flashlight on/off |
| E | Interact / pick up |
| P | FPS counter |
| Esc | Pause |

## Objective

Find **3 traces of Marek** (the amber tick on the compass points to the nearest
one), reach the source of a **mysterious transmission**, and **lead Marek back
to the trailhead** — he only walks inside your flashlight beam. Do all of it
**before your radio battery dies** (meter bottom-left). The radio drains 3×
faster when IT is near.

- Make it back with Marek in time → **FIRST LIGHT** ending
- The radio dies first → **HOLLOW** ending

Tips: when the crickets go silent, something is coming. When it charges you,
hold your flashlight beam on it for about a second to repel it.

## What's in this folder

```
hollow-pines/
├── index.html                ← the whole game (one file)
├── textures/                 ← scanned PBR textures (Polyhaven, CC0 license)
├── models/                   ← rigged character Soldier.glb (three.js examples)
├── GAME_DESIGN_DOCUMENT.md   ← full game design document
└── README.md                 ← this file
```

---

## Story

Marek Doubek, 34, went missing three days ago on the Hollow Pines loop. The official
search was suspended at sundown. You — Volunteer 6 — stayed. The story is told entirely
through radio messages from the ranger station and the things you find: his torn jacket,
his water bottle, and a camera whose last frames were taken *tonight* — of *you*, from
behind. The third discovery locks your direction finder onto a transmission coming from
a place that isn't on the map. What stalks you is never explained and never clearly seen.

## Gameplay loop

ORIENT (compass + direction-finder tick) → TRAVERSE (manage flashlight, stamina, noise)
→ DISCOVER (clue/battery/story beat) → DECIDE (light on and visible, or dark and slow?)
→ tension event → repeat. Three phases:

1. **FIND** — locate 3 traces of Marek. After the first, the forest starts stalking you.
2. **HOLLOW** — follow the locked bearing to a clearing that goes dead silent.
3. **ESCORT** — walk Marek out. He only moves inside your flashlight beam. The entity
   now hunts in waves: you hear it coming and must put your light on it before it
   reaches you. Getting caught costs 8% radio battery and dumps you backward.

## Mechanics

- **Flashlight** — the only real light you control. ~8 min of battery; 4 spares hidden
  in the forest. Flickers when the entity is close. Marek follows only while it's on.
- **Radio** — mission clock (≈16 min at base drain), narrative delivery, and Geiger
  counter: static swells as the entity approaches.
- **Compass + direction finder** — no minimap, no player marker. A bearing tick and a
  proximity ping are all the navigation you get.
- **Composure (hidden stat)** — drains in darkness and entity proximity. Never kills
  you; instead it narrows your FOV, darkens the vignette, and brings up breath and
  heartbeat. You stop trusting your own senses.
- **The entity** — an audio-first AI: it circles at 24–38 m (branch snaps, sub-bass
  rumble, dying cricket noise) and only renders as a silhouette. Shining your light
  directly on it for ~1 second repels it.

## Graphics

- Photoscanned forest-floor, pine-bark and boulder materials with normal maps
  (relit per-pixel by the flashlight)
- Procedurally painted needle-card pine canopies and grass/shrub cards
  (alpha-tested, with matching alpha-tested shadows)
- Rigged, animated human model for Marek (idle/walk, follows your beam)
- The entity is Marek's own silhouette — cloned, all black, stretched 30% too
  tall, frozen mid-stride while it stalks you

## Audio design (all procedural Web Audio)

- **Beds:** looped filtered noise for wind with LFO gusts; cricket chirps and owl calls
  scheduled as HRTF-panned 3D one-shots around the player.
- **Silence as alarm:** cricket density is a function of entity distance — the forest
  going quiet *is* the warning. The Hollow itself is nearly dead silent.
- **Threat layer:** 36/47 Hz sub-rumble and radio static both scale with entity
  proximity; branch snaps and approach footfalls are true 3D positioned sources.
- **Psyche layer:** breath and double-thump heartbeat fade in as composure falls.
- **Finale:** a barely-audible detuned drone (104.5/110/156.3 Hz) fades in during the
  escort — most players won't notice when it started.
- Everything routes through a compressor acting as a limiter so scares never clip.

## Technical stack

- **Three.js r160** (WebGL2) via CDN import map — no bundler, no install.
- **Web Audio API** — PannerNode HRTF spatialization, DynamicsCompressor limiter,
  all sounds synthesized from one shared noise buffer + oscillators.
- Vanilla JS module, seeded RNG (mulberry32) for a deterministic forest.
- DOM/canvas HUD (compass strip, meters, subtitles) — no UI framework.

## Optimization strategy (the 60 FPS budget)

- **One dynamic shadow-casting light** — the flashlight (1024² PCF map). Moon and
  ambient are non-shadowing fills. This is the single biggest win.
- **Instanced everything** — the whole forest is ~8 draw calls (trunks, needle cards,
  canopy cores, dead trunks, grass, shrubs, rocks, logs).
- **Fog as culling** — FogExp2 hides a 130 m camera far plane; nothing beyond
  visibility range is ever rasterized.
- **Zero per-frame allocations** in the game loop — reused vectors, spatial-hash
  tree collision (3×3 cell lookup, no physics engine).
- Pixel ratio capped at 1.5; entity AI is cheap state-machine math; audio scheduling
  uses the Web Audio clock, so sound stays accurate even if frames drop.

## Endings

- **FIRST LIGHT** — reach the trailhead with Marek while the radio still has charge.
  Dawn breaks; in the ranger truck, Marek quietly says your name — which you never
  told him and never said aloud all night.
- **HOLLOW** — the radio dies first, anywhere. Total darkness, total silence. Then the
  ranger station hears your own voice, calm: *"It's fine. I found him. We're staying."*

A stats screen (time, traces, batteries, times caught) ends both runs.
