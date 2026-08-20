# Sparkle Butt

You are a cat. You run around a meadow gobbling up sparkles, which fuel your
butt. When you've banked enough sparkle fuel, you chase down grumpy dogs and
fart sparkles at them, which makes them happy. Cheer up every dog to clear the
level.

An idea by a dad and his daughter, roughly fifteen years in the making.

## Play it

Live at **[sparkle.lab980.com](https://sparkle.lab980.com)** (deployment notes
in [DEPLOY.md](DEPLOY.md)). Or locally — the whole game is one file with zero
dependencies:

```
open index.html
```

or serve it (needed for some browsers' autoplay/audio policies):

```
npx serve .
```

**Controls:** WASD / arrow keys to move, SPACE to fart, E to drop a steak. On
touch devices, drag anywhere to scamper and tap the FART! / 🥩 buttons. Or
pair a Bluetooth gamepad (Xbox, PlayStation, MFi — works in iPad Safari too):
left stick or d-pad to move, A / R2 to fart, B / X to drop a steak, A or
Start on menus.

**Rules:** sparkles are worth 1 fuel each, a fart costs 3, and only grumpy dogs
caught inside the rainbow cloud convert. You start each level with 3 fuel.
Each level adds more, faster dogs.

**Dog tempers** — every grumpy dog has one:

- **Scaredy** (storm cloud ☁️): flees when you get close — corner it against
  the hedge.
- **Chill** (💤): too mopey to care. Wanders slowly. Easy pickings.
- **Aggro** (red aura, 😤): hunts you down from level 2 onward. A bite knocks
  up to 3 sparkles out of your butt — they scatter on the grass, so scramble
  and re-collect them. Get bitten with an empty butt and the grump takes over:
  you become a grumpy cat and retry the level.

**Steaks 🥩:** a couple spawn per level (carry up to 2). Press E (or tap 🥩)
to drop one — every grumpy dog nearby abandons what it's doing, mobs the
steak, and munches for ~6 seconds. Even aggro dogs. Herd them into a huddle,
then gas the whole crowd with one fart.

**Obstacles:** trees and rocks block movement (fart gas passes through).
Corner scaredy dogs against them, or break an aggro dog's chase around one.

## Tech

- Single `index.html` — vanilla JS, canvas rendering, WebAudio synth SFX
  (no audio assets). No build step, no dependencies.
- Works on desktop and mobile browsers; DPR-aware rendering, virtual joystick,
  `prefers-reduced-motion` respected (no screen shake).
- **Sprite pipeline:** characters render through a sprite-atlas layer with
  emoji fallbacks. Drop `assets/sprites.png` + `assets/sprites.json` next to
  `index.html` and real art replaces the emoji with zero code changes —
  see [ART-SPEC.md](ART-SPEC.md) for the full artist brief (sprite list,
  sizes, naming, JSON schema).

## Release paths

The zero-dependency single-file approach is deliberate — it keeps every
platform on the table:

| Platform | Path | Effort |
|---|---|---|
| **itch.io** | Zip `index.html`, upload as an HTML5 game. Done. | Minutes |
| **Web** | Host the file anywhere static (GitHub Pages, Netlify, your own nginx). | Minutes |
| **Steam** | Wrap in [Tauri](https://tauri.app) (tiny binaries) or Electron; add Steamworks via [steamworks.js](https://github.com/ceifa/steamworks.js). Needs the $100 Steam Direct fee, store assets, and achievements/cloud-save polish to feel at home there. | Days–weeks |
| **Mobile** | [Capacitor](https://capacitorjs.com) wraps it for App Store / Play Store; or ship as an installable PWA (add a manifest + service worker) with no store at all. | Days |

Suggested order: itch.io + web first (free, instant, real feedback), mobile PWA
second, Steam last — Steam buyers expect more content (more level variety,
progression, settings, gamepad support) than a prototype has.

## Roadmap ideas

- Dog breeds with behaviors (fast chihuahua, oblivious bulldog, pack leader
  that re-grumps others)
- More power-ups: chili flake (mega fart radius), catnip (speed), golden
  sparkle
- Timed/score modes, local co-op (two cats, one meadow)
