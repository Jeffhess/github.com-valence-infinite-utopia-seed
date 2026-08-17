# Orbivore

A single-file 2D growth game for iPhone. Touch and drag anywhere — your character
swims toward your finger, nose first. Eat glowing orbs to get bigger; the camera
gradually pulls back to match your new size so you can always see the space around
you and hunt for more. The goal is simply to grow as big as you can.

Everything is in `index.html` — no build step, no dependencies, no network access.

## Get it onto your iPhone

The game is designed to be installed to your Home Screen, where it runs fullscreen
with its own icon and no Safari chrome.

**Via GitHub Pages (recommended)**

1. On GitHub, go to this repo's **Settings → Pages**.
2. Under *Build and deployment*, set **Source** to `Deploy from a branch`, pick
   branch `claude/ios-growth-orb-game-xppyzs` and folder `/ (root)`, then **Save**.
3. Wait a minute, then open this on your iPhone:
   `https://jeffhess.github.io/github.com-valence-infinite-utopia-seed/growth-orb-game/`
4. Tap the **Share** button → **Add to Home Screen** → **Add**.
5. Launch it from the Home Screen icon.

**Without GitHub Pages**

AirDrop or email `index.html` to yourself, open it from the Files app (it opens in
Safari), and play. Add to Home Screen works best from a real `https://` URL, so use
Pages if you want the standalone fullscreen experience.

## Using your own character picture

The built-in character is a placeholder. To use your own:

- **On the phone (easiest):** tap the 🖼️ button in the game and pick an image. It is
  downscaled to 512px, stored on-device, and reused every time you play. Nothing is
  uploaded anywhere.
- **In the code:** drop your file next to `index.html` and set `CUSTOM_SPRITE` near
  the top of the sprite section, e.g. `var CUSTOM_SPRITE = 'player.png';`

**The top edge of your image is the front of the character.** Point your character
"north" in the source image and it will rotate correctly toward wherever you drag.
PNG with a transparent background looks best. Non-square images keep their aspect
ratio.

## Controls

| | |
|---|---|
| Touch and drag | Swim toward your finger. Farther from the character = faster. |
| Release | Coast to a stop. |
| 🖼️ | Choose your own character picture. |
| 🔊 | Sound on/off. |
| ↺ | Start over (your best size is kept). |
| Arrow keys / WASD | Desktop testing. |

Switching apps pauses the game; tap **RESUME** to continue.

## How the growth and zoom work

Growth is by **area**, not radius: eating an orb adds its area to yours, so each orb
is worth a roughly constant *percentage* of your current size. That keeps progress
feeling steady forever instead of stalling once you get big.

The camera scale is derived from your radius every frame and eased toward its target
(`ZOOM_RATE`), which is what produces the gradual zoom-out after each pickup rather
than a jarring snap. Your on-screen size grows only slightly (`GROW_EXP = 0.12`) —
most of your growth is expressed by the world shrinking around you — and `MIN_SPAN`
guarantees you always see at least ~4.6 body-radii of space in every direction, so
there are always orbs in view to chase.

Orbs are spawned relative to your current size in a ring just off-screen and culled
once far behind you, so the field stays evenly stocked (~15 on screen) at every
scale. Because everything scales together, the game is genuinely endless.

## Tuning

The constants at the top of the script are the whole design:

| Constant | Effect |
|---|---|
| `VIEW_SPAN` | How much space you see at size ×1. Higher = more zoomed out. |
| `MIN_SPAN` | Floor on visible space when huge. Higher = never feel cramped. |
| `GROW_EXP` | How much growth shows as on-screen bulk vs. camera pull-back. |
| `ZOOM_RATE` | How gradually the camera follows your size. Lower = slower. |
| `BASE_SPEED` / `SPEED_EXP` | Movement speed, and how it changes as you grow. |
| `ORB_TARGET` | Orb density. |
| `ORB_MIN` / `ORB_MAX` | Orb size as a fraction of you — i.e. growth per pickup. |
| `BIG_CHANCE` | Frequency of the gold bonus orbs. |
| `MILESTONES` | The ×2, ×3, ×5 … banners and the progress bar targets. |

## Notes

- Tested in Chromium at iPhone 15 dimensions (393×852 @3x). Renders at up to 2.5×
  device pixel ratio.
- Orb glow is drawn from pre-baked textures rather than `ctx.shadowBlur`, which is
  far too slow to do ~130 times per frame.
- Your best size, sound preference and character picture are kept in `localStorage`
  (wrapped so Private Browsing can't break the game).
