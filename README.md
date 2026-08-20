# Come By!

You are a border collie. A flock of sheep grazes somewhere on the pasture, and
there's one open gate into the pen. Sheep drift away from you — that pressure
is your only tool. Get behind them, push them where you want, and walk every
last one through the gate to clear the level.

Sparkle Butt's sibling: same one-file, zero-dependency engine, a very
different game. Where Sparkle Butt is chase-and-zap, Come By! is slow
pressure and positioning — a sheepdog trial in miniature.

## Play it

Live at **sheep.lab980.com** once provisioned (deployment notes in
[DEPLOY.md](DEPLOY.md)). Or locally — the whole game is one file with zero
dependencies:

```
open index.html
```

or serve it (needed for some browsers' autoplay/audio policies):

```
npx serve .
```

## Two ways to drive the dog

Pick one on the title screen, swap any time mid-game with the HUD toggle:

- **🕹️ Drag mode** — steer the dog yourself: WASD / arrow keys, drag anywhere
  on touch, or a Bluetooth gamepad stick. Same feel as Sparkle Butt's cat.
- **🗣️ Command mode** — the dog works the flock alone and you call real
  sheepdog-trial commands (keys 1–6, tap the buttons, or gamepad):

  | Command | Means | The dog… |
  |---|---|---|
  | **Come by** | flank clockwise | circles the flock clockwise |
  | **Away** (to me) | flank counter-clockwise | circles the other way |
  | **Walk on** | drive | pushes straight at the flock |
  | **Easy** | slow down | toggles a gentler working pace |
  | **Steady** | hold | stops and holds position |
  | **That'll do** | done | returns to the handler and lies down |

  Each command has its own synth whistle, like a real handler's.

**BARK** (SPACE / tap / Ⓐ) works in both modes: a burst of extra pressure in
a ring around the dog.

## Rules

- Levels are zoomed out — the whole field fits on screen. Each level has a
  **random-sized flock** (bigger as you go) and a fenced pen against one
  edge with a flagged gate. Pen every sheep to clear the level. No fail
  state, just you, the flock, and the clock.
- Sheep are boids: they bunch when scared, graze and drift when calm, and
  squirt around obstacles. Pressure from directly behind moves them where
  you want; pressure from the side scatters them.
- **Defiant sheep** (dark wool 🐏, from level 2): they don't attack — they're
  just hard to wrangle. They barely feel the dog, stray from the flock, and
  **plant their hooves** (😤) and refuse to move. A BARK — or the dog walking
  right up to them — startles them loose.
- Trees and rocks block movement. The hedge keeps everyone on the field.

## Tech

- Single `index.html` — vanilla JS, canvas rendering, WebAudio synth SFX
  (whistles, barks, bleats — no audio assets). No build step, no dependencies.
- Works on desktop and mobile browsers; DPR-aware, fit-to-screen world sized
  to the device aspect, virtual joystick, gamepad support,
  `prefers-reduced-motion` respected.
- Emoji sprites with per-glyph render probing and canvas-drawn vector
  fallbacks (add `?noemoji` to preview them) — same pipeline lessons as
  Sparkle Butt: glyphs are rasterized to cached offscreen canvases because
  iPad Safari silently drops mirrored emoji `fillText`.
- Build stamp + self-update: deploys stamp the commit into `BUILD`, and open
  tabs poll their own URL and offer/auto-apply a refresh.

## Roadmap ideas

- Sheepdog trial scoring: points for lines, penalties for scattering
- Split gates / multi-pen levels ("shed five into the left pen")
- Hazards: a stream with a bridge, mud that slows the flock
- Two-dog co-op (one drag, one commands)
- Lambs that follow a specific ewe
