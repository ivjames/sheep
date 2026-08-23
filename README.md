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
  | **Come by** | flank clockwise | circles the flock clockwise, swinging wide around strays |
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

## The dog

Not every collie is the same animal. Four ratings describe what *this* dog is
made of, and each drives one concrete lever in the sim:

| Rating | What it moves |
|---|---|
| 🎯 **Presence** | Every radius the dog works through — how far sheep flee from it, notice it, and feel its bark — and how far off the flock it stands to drive. |
| ⚡ **Speed** | Top speed and acceleration, in both drive modes and at every notch of pace. The charge a bark needs scales with it, so a slow dog can still bark. |
| 🫁 **Stamina** | How quickly it's ready to go *again*. Running is nearly free — a collie is built for that — but every **burst** costs: getting up to speed, and wrenching a hard turn at it. Spend the pool and the dog wants a breather before it can go properly again. |
| 🧘 **Patience** | Time between decisions — how often the dog stops to re-read the field. Bout lengths are unchanged; only how finely they're steered. A patient dog works in long deliberate legs, an impatient one re-decides constantly. Command and autopilot only. |

Ratings run 0–1 and **0.5 is the baseline** — the dog the game shipped with.
The two halves of each range map separately so a rating of exactly 0.5 lands on
a multiplier of exactly 1.00×, so that dog plays bit-for-bit the game it always
did and the ratings can be re-tuned without disturbing it.

Stamina is the only one that moves during a level, and what spends it is
**change of motion, not speed**. Holding a run costs a collie almost nothing —
that is what the breed is for — so the cost sits on bursts: the measure is how
much the dog's velocity changes, which is zero for cruising dead straight and
highest for a standing start or a hard turn at pace. Recovery needs a real
easing off, not just a straight line. The HUD bar shows what's left, and the
dog sweats when it's running short.

A dog at the baseline rating gets through an ordinary level without ever being
winded; it is the *unfit* dog that can't go again, which is where the rating
earns its keep.

## Raising a dog

Every dog starts **green** — all four ratings at 0.40, below the baseline — and
every dog has its own **ceilings** it can't train past. The ceilings are rolled
per dog and then held to a fixed total, so no dog can max all four: each one is
a specialist, and *which* specialist is what you find out by working it. They're
never shown as numbers. You read them off the notch on each bar in the kennel,
and off gains going soft as a rating nears its limit.

Two things move a rating, deliberately different in size:

- **Drift** — what the level itself taught. The dog's work is measured while you
  play (effort spent sprinting, ground covered, pressure actually landed on
  sheep, seconds spent holding a line) and at the gate each rating drifts by up
  to 0.02 toward whatever the dog actually *did*. A level of hard running builds
  stamina; a level worked patiently at range builds presence and patience.
- **Points** — one per level clear, worth 0.05, spent where you choose in the
  kennel. This is how you push a dog toward the job you want it for rather than
  the job it happened to get.

The clear card names what the level taught, and says when a rating is nearing
its limit. Open the **kennel** from the 🦴 HUD chip in game, or from the dog
line on the title screen, to spend points, switch dogs, or raise a new one.

Progress is a **career save** — your kennel, the level you reached, and the run
seed, so resuming replays the same fields you were working. `?career=new` starts
over. A save the build can't read is discarded rather than guessed at.

- Dev: `?dev=1` puts the raw sliders back on every kennel row, showing the
  rating, the multiplier it lands on and the dog's actual ceiling. That's the
  bench the ratings get balanced on.
- Ratings also ride in the URL as `?dog-presence=0.9&dog-stamina=0.2` (prefixed,
  because bare `?speed=` is already the sim-speed toggle). "Copy dog link" in the
  kennel builds one.

## Rules

- Levels are zoomed out — the whole field fits on screen. Each level has a
  **random-sized flock** (growing fast, with **no cap** — the flock keeps
  swelling as long as you keep clearing) and a fenced pen against one edge
  with a flagged gate that **narrows level by level** down to a two-sheep
  squeeze. Pen every sheep and the gate swings shut to clear the level. No
  fail state, just you, the flock, and the clock — which waits for your
  **Ready** click at the top of each level while the flock grazes.
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
  per-platform missing-glyph pitfalls. The start screen's how-to icons are
  inline SVG paths (see [Credits](#credits)), inherited `currentColor` so
  they pick up the panel and button text colours.
- Build stamp + self-update: deploys stamp the commit into `BUILD`, and open
  tabs poll their own URL and offer/auto-apply a refresh.
- Camera angles: the HUD 🎥 chip (or the `C` key) cycles **hillside →
  spectator → overhead**. The field is simulated flat and draped through a
  two-stage, exactly invertible camera — the hillside ridge lift, then a
  perspective tilt — so pointer input still lands on the right patch of
  grass in every angle. The choice is remembered in `localStorage`.
- Dev shortcut: the HUD level chip is a button — click it to jump between
  levels by tens (1, 10, 20 … 100) for stress-testing big flocks.
- Dev shortcut: sim speed — the `X` key cycles **1× → 2× → 4×** (or open with
  `?speed=2` / `?speed=4`). The frame loop takes that many whole simulation
  steps per frame at the real frame `dt` rather than stretching the step, so
  physics behaves identically, just faster; the level clock runs in sim time
  so recorded times stay honest.

## Credits

Start-screen icons (sitting dog, ranch gate, sheep, megaphone, whistle,
bullseye) are by **Delapouite** and **Skoll** from
[game-icons.net](https://game-icons.net/), used under
[CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). They're embedded
as inline SVG paths in `index.html` with the background square stripped and
the fill switched to `currentColor`.

## Roadmap ideas

- Sheepdog trial scoring: points for lines, penalties for scattering
- Split gates / multi-pen levels ("shed five into the left pen")
- Hazards: a stream with a bridge, mud that slows the flock
- Two-dog co-op (one drag, one commands)
- Lambs that follow a specific ewe
