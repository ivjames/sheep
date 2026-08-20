# Sparkle Butt — Sprite Art Spec (v1)

This is everything an artist needs to replace the placeholder emoji with real
sprites. The engine already supports sprite sheets: drop the two files below
into `assets/` and the art appears in-game with **zero code changes**. Any
sprite that isn't delivered yet falls back to its emoji placeholder, so partial
deliveries are playable immediately — send art in any order.

## Deliverables

1. `assets/sprites.png` — one PNG atlas, transparent background, **max
   2048×2048**. (If you'd rather deliver individual PNGs per frame, that's
   fine — we'll pack them; skip the JSON too.)
2. `assets/sprites.json` — frame coordinates and animations, in this schema:

```json
{
  "meta": { "image": "sprites.png" },
  "frames": {
    "cat_run_0": { "x": 0,   "y": 0, "w": 128, "h": 128 },
    "cat_run_1": { "x": 128, "y": 0, "w": 128, "h": 128 },
    "tree_0":    { "x": 0, "y": 512, "w": 256, "h": 256 }
  },
  "anims": {
    "cat_run": { "fps": 10, "frames": ["cat_run_0", "cat_run_1"] }
  }
}
```

TexturePacker or free-tool output can be converted to this trivially — or ask
and we'll adapt the loader to your tool's format.

## Ground rules

- **Anchor: center.** Every frame is drawn centered on the entity's position.
  Keep the character centered in the frame with consistent footing across
  frames of one animation (no bobbing baked in — the engine bobs/hops).
- **Facing: LEFT.** All characters face left in the art; the engine mirrors
  them for rightward movement. Avoid readable text/asymmetric props that look
  wrong mirrored.
- **Name frames `name_0`, `name_1`, …** zero-based, matching the table below.
- **Author at 2× the render size** listed below (the game renders on retina
  displays). E.g. the cat renders at ~52 px tall → author at 128×128.
- Bold, chunky silhouettes: everything must read at half the authored size, on
  a busy green field, on a phone. When in doubt, exaggerate.
- Style is yours to pitch, but the game's current world is a warm storybook
  meadow. Current palette for reference: grass `#7ec850`/`#6fb746`, hedge
  `#3e7a2a`, HUD plum `#31204a`, cream `#fff6e8`, sparkle gold `#ffd84d`,
  candy pink `#ff8ae0`, coral `#ff7b54`.

## Sprite list

### Tier 1 — makes the game fully sprite-based

| Name | Frames | Frame size | Renders at | Notes |
|---|---|---|---|---|
| `cat_idle` | 4 | 128×128 | ~52 px | Idle loop: tail swish, ear flick |
| `cat_run` | 6 | 128×128 | ~52 px | Full sprint cycle — this is 90% of gameplay |
| `cat_grumped` | 1 | 128×128 | ~48 px | Game-over cat: grumpy, storm-cloud vibes |
| `dog_scaredy` | 4 | 128×128 | ~46 px | Nervous trot, ears back, looking over shoulder |
| `dog_chill` | 2 | 128×128 | ~46 px | Mopey shuffle, half-lidded eyes |
| `dog_aggro` | 4 | 128×128 | ~50 px | Charging run, bared teeth — reads as THREAT |
| `dog_happy` | 4 | 128×128 | ~46 px | Bouncy hop cycle, tongue out, wagging |
| `dog_munch` | 2 | 128×128 | ~46 px | Head-down chomping (eating a dropped steak) |
| `sparkle` | 4 | 64×64 | ~26 px | Twinkle loop; must pop against grass |
| `steak` | 1 | 64×64 | ~32 px | Cartoon T-bone, appetizing |
| `tree_0`, `tree_1` | 1 each | 256×256 | ~116 px | Two variants; canopy sits high, base at bottom center |
| `rock_0`, `rock_1` | 1 each | 128×128 | ~60 px | Two boulder variants |

### Tier 2 — replaces the remaining emoji FX

| Name | Frames | Frame size | Notes |
|---|---|---|---|
| `cat_fart` | 4 | 128×128 | One-shot squat-and-release; engine will trigger on fart |
| `fx_heart`, `fx_note`, `fx_star` | 1–2 each | 64×64 | Floaters above happy dogs / on impacts |
| `fx_anger` | 2 | 64×64 | The 💢 mark, pulsing |
| `fx_cloud` | 2 | 64×64 | Grump storm cloud (scaredy dogs + grumped cat) |
| `fx_zzz` | 2 | 64×64 | Chill dog's 💤 |
| `fx_bite` | 1 | 64×64 | The 💥 impact star |
| `flower_0`–`flower_3` | 1 each | 64×64 | Ground decor (currently 🌼🌸🌷🍄) |

### Tier 3 — polish, when we get there

Title-screen key art, meadow ground tiles, UI skin (buttons, panels, fuel
pips), animated fart-cloud sheet, dog-breed variants per temper.

## Animation timing

Default playback is 8 fps; per-animation fps can be set in the JSON (we run
`cat_run` at ~10). Loops should cycle cleanly. One-shots (`cat_fart`) play
once — note them and we'll wire the trigger.

## Testing your art

Drop the two files into `assets/`, open `index.html` in a browser, play. Each
sprite replaces its emoji the moment it exists in the atlas; anything missing
stays emoji. No build step, no tooling.

## Sprite manager (recommended)

You don't have to build the atlas by hand: open `tools/sprite-manager.html` in
a browser. It shows every sprite above as a grid of frame slots — upload a PNG
per frame (or drag a batch of `name_i.png` files anywhere and they route
themselves), preview each animation live at its real fps, and track completion.
**Export** packs everything you've uploaded into a correctly formatted
`sprites.png` + `sprites.json` — partial exports work fine. Uploads autosave in
your browser between sessions.
