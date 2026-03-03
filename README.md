# ⚡ REACTION GAME

A sleek reaction time trainer built with vanilla HTML, CSS, and JavaScript — works great on both desktop and mobile. Test how fast your reflexes are, track your session stats, and compete against yourself.

---

## 🎮 How to Play

1. **Start a round** — tap the game box, press the **START** button, or hit **Spacebar**
2. **Wait** — the gem pulses and dims. The delay is random (1.5–4 seconds)
3. **React** — the moment the gem flashes **green**, click or tap as fast as you can
4. **See your result** — your reaction time in milliseconds is shown instantly
5. **Don't click early** — clicking before the green flash registers as *Too Soon* and resets the round

> Press **Escape** or tap the grid icon (⊞) at any time to toggle the instructions overlay open and closed.

---

## ⚡ Reaction Time Scale

| Tier | Range | Color |
|---|---|---|
| 🤖 Superhuman | < 150ms | Lime |
| 🎯 Amazing | 150–200ms | Lime |
| 🔥 Very Good | 200–250ms | Cyan |
| 👊 Good | 250–300ms | Cyan |
| 😐 Average | 300–380ms | White |
| 🐢 Below Average | 380–500ms | Gray |
| ☕ Needs Coffee | > 500ms | Red |

---

## 🗂 Project Structure

```
/
├── index.html       # Entire app — HTML, CSS, and JS in one file
└── favicon.png      # App icon (referenced from root)
```

The project is intentionally **single-file** — no build tools, no dependencies beyond fonts and Three.js which are loaded via CDN links already embedded in the `<head>`.

---

## 🧱 Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | Vanilla CSS (custom properties, `100dvh`, CSS Grid, keyframe animations) |
| Logic | Vanilla JavaScript (ES6+) |
| 3D visuals | [Three.js r128](https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js) |
| Audio | Web Audio API — all sounds synthesized in code, no audio files |
| Fonts | Google Fonts — Bebas Neue (display) + DM Sans (body) |

---

## 🏗 Architecture Overview

### Layout

The app uses a **vertical flexbox column** that fills `100dvh` (dynamic viewport height — handles mobile browser chrome correctly). Sections from top to bottom:

```
#topbar        — Logo + instructions toggle button
#stats-strip   — Live result block + session metrics grid + score bar
#center        — Game box (flex-grows to fill remaining space)
#bottombar     — START and RESET buttons
```

### Game Box (`#click-box`)

A square container that resizes to fill available height. It holds three layers stacked with `position: absolute`:

- **`#box-canvas`** — Three.js WebGL renderer for the 3D crystal gem
- **`#intro-overlay`** — Scrollable instructions panel, visible on first load only, lives inside the game box itself
- **`#box-overlay`** — State label and hint text (GET READY / NOW! / RESULT / TOO SOON)

### Game State Machine

The variable `gs` tracks the current state. All transitions run through `act()`:

| State | Description |
|---|---|
| `'intro'` | Instructions overlay is open |
| `'idle'` | Waiting for player to start |
| `'waiting'` | Round active, waiting for random delay to expire |
| `'go'` | Green flash — player must react immediately |
| `'early'` | Player clicked before the green flash |
| `'result'` | Reaction time measured and displayed |

```
intro → idle → waiting → go → result → waiting → ...
                    ↓
                  early → waiting → ...
```

---

## 🔮 3D Scenes (Three.js)

Two separate WebGL renderers run simultaneously:

**Background scene** (`#bg-canvas`, fixed behind everything)
- Ambient rotating wireframe polyhedra: icosahedron, octahedron, dodecahedron
- 100 floating particles
- Slow camera drift

**Gem scene** (`#box-canvas`, inside the game box)
- Custom diamond geometry built from scratch: crown facets, girdle ring, pavilion cone
- Three layers: outer semi-transparent glass mesh, inner backface mesh, wireframe overlay
- Three energy orbit rings (tori) at different angles
- 220 orbiting halo particles with individual radii, speeds, and tilts
- Four dynamic point lights

The gem's visual state is controlled by `setBS(state)` — on every animation frame, colors, intensities, rotation speed, and particle behavior all lerp smoothly toward their target values, giving fluid transitions between game states.

---

## 🔊 Audio Engine (Web Audio API)

All sounds are synthesized at runtime with no audio files:

| Function | Trigger | Character |
|---|---|---|
| `sStart()` | Round begins | Two descending sine tones (E4 → B3), calm gallery chime |
| `sGo()` | Green flash | Bright crystal bell (C5 + soft G5 harmonic shimmer) |
| `sResult(ms)` | Result recorded | Fast C4→E4→G4 arpeggio if < 300ms; soft A3→C4 resolution if slower |
| `sEarly()` | Too-soon click | Short frequency-swept thud (180Hz → 90Hz), very quiet and non-irritating |

Each note uses a 12ms linear attack and an exponential release envelope. `sStart`, `sGo`, and `sResult` also add a quiet reverb echo (22% volume, 60ms delay) for a sense of space.

---

## 📊 Stats Panel

The stats strip below the header displays:

**Left — Live result block**
- Current millisecond reading in large display font
- "Milliseconds" label (becomes "🏆 Personal Best!" when applicable)
- Rating badge (e.g. *🔥 Very Good*)
- Colored left-edge accent stripe that transitions: lime (fast) / cyan (good) / red (slow)

**Right — Session metrics**
- Three card tiles: **Avg**, **Best**, **Runs**

**Score bar**
- Full-width gradient spectrum: red → gray → white → cyan → lime
- Animated diamond marker (◆) slides to your exact position after each result
- Marker shows tier label above, colored glow, and a tick line pointing down to the bar
- Slides in with a spring animation (`cubic-bezier(.34, 1.56, .64, 1)`)

---

---

## 📱 Cross-Platform Support

Works on desktop (mouse + keyboard) and mobile (touch) without any code changes.

- `viewport` meta includes `maximum-scale=1, user-scalable=no` to prevent accidental pinch-zoom during gameplay on touch screens
- `touch-action: manipulation` removes the 300ms tap delay on mobile browsers
- `-webkit-tap-highlight-color: transparent` suppresses the default tap highlight on the game box
- `100dvh` correctly accounts for the collapsing mobile browser address bar
- All tap targets are sized for comfortable touch use

---

## ⌨️ Keyboard Controls

| Key | Action |
|---|---|
| `Space` | Start round / react / advance through states |
| `Escape` | Toggle instructions overlay open / closed |

---

