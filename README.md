# Sparkle Butt

You are a cat. You run around a meadow gobbling up sparkles, which fuel your
butt. When you've banked enough sparkle fuel, you chase down grumpy dogs and
fart sparkles at them, which makes them happy. Cheer up every dog to clear the
level.

An idea by a dad and his daughter, roughly fifteen years in the making.

## Play it

The whole game is one file with zero dependencies:

```
open index.html
```

or serve it (needed for some browsers' autoplay/audio policies):

```
npx serve .
```

**Controls:** WASD / arrow keys to move, SPACE to fart. On touch devices, drag
anywhere to scamper and tap the FART! button.

**Rules:** sparkles are worth 1 fuel each, a fart costs 3, and only grumpy dogs
caught inside the rainbow cloud convert. Dogs flee when you get close — corner
them against the hedge. Each level adds more, faster dogs.

## Tech

- Single `index.html` — vanilla JS, canvas rendering, WebAudio synth SFX
  (no audio assets), emoji sprites. No build step, no dependencies.
- Works on desktop and mobile browsers; DPR-aware rendering, virtual joystick,
  `prefers-reduced-motion` respected (no screen shake).

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

- Obstacles and yards to corner dogs in; dog breeds with behaviors (fast
  chihuahua, oblivious bulldog, pack leader that re-grumps others)
- Power-ups: chili flake (mega fart radius), catnip (speed), golden sparkle
- Real sprite art instead of emoji (the drawing layer is one function:
  `drawEmoji` — swap in sprite sheets there)
- Gamepad support, timed/score modes, local co-op (two cats, one meadow)
