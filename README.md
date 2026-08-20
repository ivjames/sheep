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

- **🎯 Guide mode** — your finger (or mouse) is the dog's target: touch the
  field and it rubber-bands to the spot, easing to a stop when it arrives.
  WASD / arrows and a gamepad stick still direct-drive.
- **🗣️ Command mode** — the dog works the flock alone and you call real
  sheepdog-trial commands (keys 1–7, tap the buttons, or gamepad):

  | Command | Means | The dog… |
  |---|---|---|
  | **Come by** | flank clockwise | circles the flock clockwise |
  | **Steady** | halt | stops — pace drops to zero |
  | **Away** (to me) | flank counter-clockwise | circles the other way |
  | **Walk on** | drive | pushes at the flock, each tap adds pace |
  | **Easy** | slow down | each tap sheds pace |
  | **To me** | call off | comes off the sheep toward the gate post |
  | **That'll do** | done | returns to the post and lies down |

  Speed is a gradient: WALK ON taps push it up, EASY taps ease it down,
  STEADY halts. A flank keeps whatever pace you've set (from a halt the
  dog just creeps off) — calling the **same flank again** is the hurry-up
  that brings it back to working pace. Each command has its own synth
  whistle, like a real handler's.

**Barking** works in both modes and has no button: charge straight at a
sheep and the dog barks on its own as it closes in — a burst of extra
pressure in a ring, and the only thing that shifts a planted sheep.

## Rules

- Levels are zoomed out — the whole field fits on screen. Each level has a
  **random-sized flock** (growing fast, with **no cap** — the flock keeps
  swelling as long as you keep clearing) and a fenced pen against one edge
  with a flagged gate that **narrows level by level** down to a two-sheep
  squeeze. Pen every sheep and the gate swings shut to clear the level. No
  fail state, just you, the flock, and the clock.
- Sheep are boids: they bunch when scared, panic spreads between
  neighbours, strays and split groups drift back and merge with the mob,
  and they graze and wander when calm. Pressure from directly behind moves
  them where you want; pressure from the side scatters them.
- Every sheep rolls its own **temperament**: a gradient from timid (feels
  the dog from far out and bolts) to bold (makes you get close and barely
  budges), and another for how keenly it follows the flock's flow versus
  drifting its own way.
- Sheep near the gate mouth **know what it's for** and drift on in —
  strongest at early levels (training wheels) but never gone entirely.
  The stubborn end of the flock couldn't care less about your pen.
- **Defiant sheep** (dark wool, curled horns, from level 2) sit pinned at
  the bold, unflockable extreme: they barely feel the dog, stray from the
  flock, and **plant their hooves** (huffing steam) and refuse to move. A
  bark — the dog charging right up to them — startles them loose.
- Trees and rocks block movement. The hedge keeps everyone on the field.

## Tech

- Single `index.html` — vanilla JS, canvas rendering, WebAudio synth SFX
  (whistles, barks, bleats — no audio assets). No build step, no dependencies.
- Works on desktop and mobile browsers; DPR-aware, fit-to-screen world sized
  to the device aspect, tap-to-guide steering, gamepad support,
  `prefers-reduced-motion` respected.
- Everything on the field is canvas-drawn vector art. Sheep and dog are
  oblong bodies that rotate to a smoothed heading, with trotting feet and
  a wagging tail; trees, rocks, flowers, and the little status marks
  (steam huffs, sweat drops, music notes) are hand-drawn shapes too. The
  emoji sprite pipeline from Sparkle Butt — glyph probing, vector
  fallbacks, cached rasterization — is gone entirely, along with its
  per-platform missing-glyph pitfalls.
- Build stamp + self-update: deploys stamp the commit into `BUILD`, and open
  tabs poll their own URL and offer/auto-apply a refresh.

## Roadmap ideas

- Sheepdog trial scoring: points for lines, penalties for scattering
- Split gates / multi-pen levels ("shed five into the left pen")
- Hazards: a stream with a bridge, mud that slows the flock
- Two-dog co-op (one drag, one commands)
- Lambs that follow a specific ewe
