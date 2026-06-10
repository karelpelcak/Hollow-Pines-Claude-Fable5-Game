# HOLLOW PINES
## Game Design Document — v1.0

**Genre:** First-person psychological horror / exploration
**Platform:** Web browser (desktop-first, WebGL2 with WebGPU upgrade path)
**Target performance:** 60 FPS on mid-range hardware (GTX 1060 / Apple M1 class)
**Session length:** 45–70 minutes, single playthrough with replay incentives
**Rating target:** 16+ (psychological dread, no gore)

---

## 1. High Concept

You are a volunteer search-and-rescue worker looking for a missing hiker in a national forest after dark. Your radio, your flashlight, and a paper map are all you have. The forest is real — wind, rain, distant wildlife — until, gradually, it isn't. The game never shows you a monster. It makes you certain one is there.

**Design pillars:**

1. **Plausible realism** — Everything that happens in the first 20 minutes could happen on a real night hike. Horror grows out of realism, never replaces it.
2. **Suspense over spectacle** — Tension is built through audio, light, and pacing. Jump scares are rare (max 3 in the whole game) and always earned.
3. **The player's imagination is the renderer** — Suggest, never show. The scariest asset in the game is a sound with no visible source.
4. **60 FPS is a feature** — Smooth framerate is non-negotiable for immersion. Every art and design decision is made inside the performance budget.

---

## 2. Story

### 2.1 Premise

October. Marek Doubek, 34, went missing three days ago on the Hollow Pines trail loop. Official search was called off at sundown. You — the player, an unnamed volunteer — stayed behind, convinced you saw a light moving off-trail. You have until your radio battery dies to find him.

### 2.2 Narrative structure (three acts, environmental storytelling only — no cutscenes)

**Act 1 — The Search (≈20 min):** Pure realism. Follow trail markers, find Marek's scattered belongings (water bottle, torn jacket, a disposable camera). The radio crackles with the ranger station telling you to come back. Wildlife sounds are real and identifiable. The player learns the mechanics in a safe — but increasingly uneasy — environment.

**Act 2 — The Turn (≈25 min):** Realism degrades in deniable increments. Trail markers point in directions that contradict the map. You pass a tree you marked with tape — twice. The ranger station's radio replies start arriving *before* you finish transmitting. Marek's camera, when developed at an abandoned fire-watch station (mid-game hub), shows photos of *you*, taken tonight, from behind. Something large moves parallel to you in the treeline — heard, never clearly seen.

**Act 3 — The Hollow (≈15 min):** The objective crystallizes: Marek's final radio message gives coordinates to "the place where the trees stop." The player must navigate to a clearing that does not exist on the map, using three landmarks discovered in Acts 1–2. The forest actively resists — paths loop, the flashlight gutters, the entity stalks openly (audio + peripheral glimpses only).

### 2.3 What is the entity?

Deliberately unresolved. Evidence collected supports three mutually exclusive readings — a predator, a hallucination from hypothermia, or something the forest *does* to people. Collectible documents (ranger logs, Marek's notes, a 1973 newspaper clipping) are written so that no single theory wins. This is the engine of post-game discussion and replays.

### 2.4 The objective (clear win condition)

**Find Marek and lead him out of the forest before your radio battery reaches 0%.** The battery is the game's clock and tension meter — it drains slowly in Act 1, faster when the entity is near. The player always knows what they're doing (find the hiker) and how they're doing (battery %, landmarks found 0/3).

---

## 3. Core Gameplay Loop

```
ORIENT  →  TRAVERSE  →  DISCOVER  →  DECIDE  →  (tension event)  →  ORIENT
```

- **Orient (30–60 s):** Read the paper map (raises it physically, blocks view — vulnerability by design), check compass, pick a heading toward the current objective.
- **Traverse (2–4 min):** Move through forest. Manage flashlight (battery), stamina, and noise. Listen — audio carries 80% of the threat information.
- **Discover (1–2 min):** Find a story item, landmark, supply cache, or radio trigger. Each discovery updates the objective and tightens the screw.
- **Decide:** Risk/reward choices — take the dark shortcut or the lit long way? Use a flare (reveals area, reveals *you*)? Answer the radio (noise) or stay silent (miss information)?
- **Tension event:** Scripted or systemic scare/pressure beat, scaled by a pacing director (see 4.6).

The loop repeats ~10–12 times with rising stakes; the fire-watch station hub at the midpoint provides one deliberate safe-room exhale before Act 3 removes all safety.

---

## 4. Mechanics

### 4.1 Movement
- Walk 1.4 m/s, jog 3.0 m/s, sprint 5.0 m/s (stamina-limited to ~8 s, audible heavy breathing after).
- Realistic head-bob (subtle, toggleable for comfort), footstep audio varies by surface (soil, leaves, gravel, wooden boardwalk, shallow stream).
- No jumping. Crouch for moving under deadfall and reducing noise. Slow vault over fallen logs (contextual, animated hands).

### 4.2 Flashlight
- Primary tool and primary resource. Realistic beam with volumetric scattering in fog; battery lasts ~12 minutes of continuous use; spare batteries found in caches (6 total in world, player needs ~4).
- Shake-to-charge backup mode: 10 s of loud cranking buys 60 s of weak light. The noise attracts attention. This single mechanic generates most emergent dread.
- Light state machine: full → flicker (entity proximity) → brownout → dead.

### 4.3 Radio (objective + clock + horror delivery)
- Push-to-talk to the ranger station; responses drive the narrative. Battery % is always visible on the device model (diegetic UI — no HUD).
- In Act 2+, the radio becomes unreliable: static swells signal entity proximity (the game's "Geiger counter"), and wrong voices answer.

### 4.4 Map & compass (no GPS, no minimap, no objective markers)
- Physical paper map raised with [Tab]; player position is NOT shown. Navigation is by compass bearing + landmarks, teaching real orienteering in Act 1 so Act 2 can weaponize it when landmarks stop matching.

### 4.5 Sanity-adjacent system: "Composure" (hidden stat)
- No visible meter. Drains from darkness, entity proximity, and witnessed events; restores in light and at landmarks.
- Low composure effects are strictly audiovisual: breath audio, slight FOV narrowing, peripheral shadow movement, footsteps-behind-you false positives. It never takes control from the player and never causes death — it makes the player *distrust their own senses*, which is the psychological-horror core.

### 4.6 The Entity & the Pacing Director
- The entity is an **audio-first AI**: a point in space that stalks, circles, and retreats. It has no full-detail model — only a silhouette billboard/low-poly shape rated for peripheral vision and < 0.5 s exposures.
- A **director system** (à la Left 4 Dead) tracks player stress (recent events, light state, composure, time since last beat) and schedules pressure: stalking audio → branch snaps → radio static → flicker → (rarely) a sighting. After high stress, it enforces quiet recovery windows so tension can rebuild.
- **The player cannot die in Acts 1–2.** Unkillable-but-unknowable is scarier than respawning. In Act 3 only, being caught in the open during a "hunt" phase causes a blackout and relocation (lost progress toward the clearing, battery penalty) — pressure without checkpoint-grind frustration.

### 4.7 Interaction
- Single context-sensitive interact key. Physical inspection mode for items (rotate in hands, flashlight needed to read documents — reading costs light, another deliberate tension tax).

---

## 5. Environment Design

### 5.1 World structure
- ~1.2 km² wedge of forest, **hub-and-spoke around the fire-watch station**, bounded naturally (river, cliff face, impassable deadfall). Feels open; is actually a guided graph of 7 zones:
  1. Trailhead & parking lot (tutorial, last man-made safety)
  2. Marked trail loop (Act 1 spine)
  3. Creek crossing & ravine (verticality, sound of water masks footsteps — yours and *its*)
  4. Old logging camp (densest storytelling, first major scare)
  5. Fire-watch station (hub, save point, camera development, map table)
  6. The burn — old wildfire scar, open sightlines, moonlight (false relief; nowhere to hide)
  7. The Hollow (finale clearing; geometry subtly non-Euclidean — the treeline is always farther than it looks)

### 5.2 Atmosphere & lighting
- Single dynamic light source philosophy: the player's flashlight is the only real-time shadow-casting light. Moonlight is a baked/static directional fill (0.05 intensity, blue-grey). Fire-watch station has 2–3 baked warm lights — warmth = safety, taught visually.
- Ground fog (height-based, animated noise), light rain in Act 2 (audio + screen-space droplets, no expensive particles), wind gusts that move canopy and grass in synchronized audio-visual waves — wind is the game's breathing.
- Color script: Act 1 desaturated greens/browns → Act 2 blue-black with the flashlight's warm pool as the only comfort → Act 3 near-monochrome, moonlit silver in the burn, total black in the Hollow.

### 5.3 Realism details (the deniability budget)
- Real Central-European flora (pine, spruce, beech, fern, deadfall), correct nocturnal soundscape (tawny owl, foxes, deer), believable trail infrastructure (blazes, signposts, an emergency shelter). Every supernatural beat in Act 2 has a mundane near-explanation, so the player argues with themselves.

---

## 6. Audio Design (the real lead platform)

Audio carries the horror. Budget and effort split: **50% audio / 30% lighting / 20% everything else.**

### 6.1 Architecture
- **Web Audio API** graph, with **Howler.js** (or native AudioWorklet pipeline) for asset management.
- Binaural spatialization via `PannerNode` (HRTF mode) for all 3D sources; headphones strongly recommended at launch (calibration screen: "you should hear this owl behind your left shoulder").
- Convolution reverb zones (forest, ravine, station interior, the Hollow's unnatural *deadness* — the finale has almost no reverb, which reads as wrong).

### 6.2 Layered ambient system
Four continuous stem layers, crossfaded by zone, act, and director stress level:
1. **Bed:** wind in canopy (3 intensities), distant river, rain.
2. **Life:** randomized one-shot wildlife pool with realistic spacing; this pool *thins out* as the entity nears — silence as alarm. The absence of crickets is the game's best scare.
3. **Threat:** entity layer — sub-bass presence (< 60 Hz felt-not-heard rumble), branch snaps with true 3D positioning, a vocalization that is a pitched-down, reversed human breath.
4. **Psyche:** composure layer — player breath, heartbeat (only below 30% composure), and "false footstep" doubles of the player's own steps, delayed 300 ms, only on low composure.

### 6.3 Signature techniques
- **Sound that moves:** entity audio sources physically travel through the scene graph (HRTF panning sells a 3D creature with zero pixels rendered).
- **The radio as horror instrument:** real DSP — bandpass 300–3000 Hz, bit-crush, squelch tails. Wrong-voice replies are the player's own earlier transmissions, replayed with one word changed.
- **Dynamic mixing:** sidechain duck of ambience under any narrative audio; everything routed through a master limiter so scares never clip.
- **Diegetic-only music:** no score until the final 90 seconds, when a single sustained string cluster fades in so slowly players don't notice when it started.

---

## 7. Technical Stack

| Layer | Choice | Rationale |
|---|---|---|
| Renderer | **Three.js (r17x)** on WebGL2, WebGPURenderer behind a flag | Mature, huge ecosystem, proven 60 FPS forest scenes |
| Language | **TypeScript** + Vite | Fast iteration, strong typing for game systems |
| Physics | **Rapier (WASM)** — character controller + simple colliders only | Fast, deterministic; we need capsule-vs-static, nothing more |
| Audio | **Web Audio API** + Howler.js, custom director mixer | HRTF, convolution, DSP for radio |
| Assets | glTF 2.0 + **Draco** mesh compression + **KTX2/Basis** textures | 60–80% smaller downloads, GPU-native textures |
| State | Lightweight ECS-ish module structure (custom, no framework) | Avoid GC pressure from heavy frameworks |
| Save | localStorage / IndexedDB (single auto-save slot at station) | Zero backend |
| Hosting | Static CDN (Cloudflare Pages / itch.io) | No server logic needed |
| Tooling | Blender (assets), custom in-browser zone editor, Stats.js + Spector.js + Chrome tracing | Profiling discipline from day one |

---

## 8. Performance & Optimization Strategy (60 FPS budget)

**Frame budget at 60 FPS = 16.6 ms.** Target allocation: render ≤ 10 ms, game logic ≤ 2 ms, physics ≤ 1.5 ms, audio/JS misc ≤ 1 ms, 2 ms headroom.

### 8.1 Rendering
- **One dynamic shadow-casting light** (flashlight, 1024² shadow map, tight frustum). Moonlight = baked lightmaps + ambient probe. This single decision is worth more than every other optimization combined.
- **Instanced rendering for all vegetation** (`InstancedMesh`): thousands of trees/ferns/rocks in < 10 draw calls per type. Target: **< 150 draw calls total** per frame.
- **LOD chain** per tree species: full mesh (0–15 m) → simplified (15–40 m) → crossfaded billboard impostor (40 m+). Octahedral impostors for hero trees.
- **Aggressive culling:** zone-based portal culling on the 7-zone graph + frustum culling + camera far plane at 90 m hidden by fog — fog is an optimization disguised as atmosphere. Darkness itself culls: anything outside the flashlight cone renders with a cheap flat-dark shader.
- Post-processing: single combined pass (vignette, grain, color grade via 3D LUT). No SSAO/SSR — bake AO into lightmaps and vertex colors.
- Resolution: dynamic render scale (0.75–1.0) driven by a rolling frame-time average; fog and darkness hide upscaling artifacts.

### 8.2 Memory & loading
- Total download target **< 120 MB**, initial playable chunk < 25 MB (trailhead zone), remaining zones streamed in background during Act 1.
- Texture atlasing per zone; KTX2 keeps textures compressed on the GPU. Geometry budget ~300k triangles visible.
- **Zero per-frame allocations** policy in the game loop: object pools for vectors, raycasts, audio one-shots. GC pauses are the death of horror pacing — a 30 ms hitch during a scare ruins it.

### 8.3 CPU
- Entity AI + director tick at 10 Hz, not per-frame. Pathing on a precomputed nav-graph (no runtime navmesh queries).
- Audio scheduling via Web Audio's own clock (`AudioContext.currentTime`), not rAF, so sound stays sample-accurate even on frame drops.
- Physics: static trimesh world + one kinematic capsule. No dynamic rigid bodies except 2–3 scripted moments.

### 8.4 Verification discipline
- Automated performance harness: scripted camera flythrough of all 7 zones in CI, fails the build if p95 frame time > 16 ms on reference hardware.
- Test matrix: Chrome/Firefox/Safari × Windows/macOS; minimum spec defined and printed on the loading screen.

---

## 9. Production Roadmap (solo dev / tiny team, ~9 months)

| Phase | Duration | Milestone & exit criteria |
|---|---|---|
| **0. Tech proof** | 3 wks | Instanced forest + flashlight + fog at 60 FPS on min spec. **Go/no-go gate.** |
| **1. Core loop vertical slice** | 6 wks | Trailhead + trail loop zone, movement, flashlight, map/compass, one story item, footstep/ambience audio. Playable 10-min slice. |
| **2. Systems** | 8 wks | Radio, director, entity audio-AI, composure, save system, full audio mixer. Greybox all 7 zones. |
| **3. Content Act 1–2** | 8 wks | Full art pass zones 1–5, all Act 1–2 story items and radio script, camera-development hub sequence. |
| **4. Content Act 3 + endings** | 5 wks | The burn, the Hollow, hunt mechanic, both endings, final mix. |
| **5. Polish & optimization** | 5 wks | LOD/impostor tuning, dynamic resolution, full perf harness pass, accessibility (subtitles, photosensitivity, motion-comfort options). |
| **6. Beta & launch** | 4 wks | Closed playtest (20+ external testers, telemetry on quit points and scare reactions), balance battery economy, launch on itch.io + own domain. |

**Scope insurance (pre-agreed cuts if behind):** cut zone 6 (the burn) and merge into zone 7; reduce collectible documents from 14 to 8; ship Chrome/desktop-only at launch.

---

## 10. Ending

### 10.1 The finale
Act 3's three landmarks triangulate the Hollow. Crossing the treeline, all ambient layers cut to silence (the convolution-dead zone). Marek is there — sitting upright in the clearing's center, facing away, flashlight beside him still on. The entity's audio now comes from *every* direction at low level. The player's radio, unprompted, begins transmitting an open channel: the ranger station can hear everything but can't respond.

The player must physically walk Marek out: he follows only while inside your flashlight beam, and the walk back is the game's hardest sequence — the hunt is active, the path has changed, and the battery is in single digits.

### 10.2 Two endings (determined by play, not a menu choice)

- **"First Light"** — Reach the trailhead with Marek and >0% radio battery. Dawn breaks over the parking lot; the ranger truck arrives. Final shot: Marek in the truck looks back at the treeline and quietly says your character's name — which the player never entered and was never spoken aloud. Cut to black. (Resolution with a splinter — the horror followed you out as knowledge, not as a monster.)
- **"Hollow"** — Battery dies before you reach the trailhead. Total darkness, total silence for five real seconds. Then your own voice, on the radio, calling the ranger station: *"It's fine. I found him. We're staying."* Title card. (Players who manage resources poorly get the bleak ending — the battery economy was the moral system all along.)

### 10.3 Post-game
- Ending stinger teases the unresolved entity question (the 1973 clipping shown one final time, with one previously-redacted line now legible).
- Stats screen: time, batteries used, documents found (x/14), composure low-point — built for screenshots and "you got Hollow?? I got First Light" conversations, the replay and word-of-mouth engine for a browser game.

---

## 11. Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Browser audio autoplay restrictions | Click-to-start title screen initializes AudioContext; headphone calibration doubles as the unlock gesture |
| Safari WebGL2 quirks | Test from Phase 0; KTX2 transcoder fallbacks; Safari on the CI matrix |
| Horror fatigue / pacing failure | Director-enforced recovery windows; external playtests with quit-point telemetry from Phase 3 |
| Scope creep on realism | "Deniability budget" rule: an asset ships only if it serves loop, story, or scare |
| GC hitches breaking immersion | Zero-allocation loop policy + allocation tracking in CI perf harness |
