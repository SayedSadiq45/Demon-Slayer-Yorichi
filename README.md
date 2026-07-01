# Demon Slayer — Sun & Moon Reveal

An interactive hero banner built around Yoriichi Tsugikuni and his brother Kokushibo. Move your cursor across the screen and a comet-trail mask carves through one image to reveal the other, framed as a Sun Breathing vs. Moon Breathing showdown — complete with floating embers and ambient lightning.

Built with a single HTML5 `<canvas>`. No frameworks, no build step.

---

## Demo

Open `demon-slayer-sun-moon.html` in any modern browser and move your mouse across the screen. On touch devices, drag a finger across the image.

---

## Features

- **Comet-trail reveal** — a 60-point smoothed trail masks the hidden image into a tapering streak that follows the cursor.
- **Violet cursor glow** — a pulsing violet radial glow with inner/outer rings marks the reveal point.
- **Ember particles** — a full-screen drifting ember field, ambient red near the base image, shifting to violet near the reveal.
- **Thunder & lightning** — jagged, branching lightning bolts strike at frequent, semi-random intervals with a screen flash, in alternating purple/white tones.
- **Black overlay** — both layers are darkened with a configurable transparent overlay for a moodier scene.
- **Vertical kanji + wordmark** — 継国縁壱 (Tsugikuni Yoriichi) and 月ノ呼吸 (Moon Breathing) framed vertically beside a gradient "Sun & Moon" title.
- **Self-contained** — images are embedded as base64 data URIs, so the file runs offline with no external assets (aside from Google Fonts).

---

## Quick start

1. Download `demon-slayer-sun-moon.html`.
2. Double-click it, or open it in a browser.

No install step, no server. An internet connection is only needed the first time, to load the Google Fonts (Cinzel, Shippori Mincho, Cormorant Garamond, Bebas Neue).

---

## Using your own images

The shipped file embeds two images as base64. To use your own, replace the two `Image` sources near the top of the `<script>` block:

```js
const bottom = new Image(); // revealed on hover (Kokushibo / Moon)
const top    = new Image(); // shown by default (Yoriichi / Sun)

bottom.src = './images/kokushibo.jpg';
top.src    = './images/yoriichi.jpg';
```

The drawing loop waits for **both** images to load before starting:

```js
let loaded = 0;
const onLoad = () => { if (++loaded === 2) draw(); };
bottom.onload = onLoad;
top.onload = onLoad;
```

> `top` is what's visible at rest; `bottom` is revealed inside the cursor's trail.

---

## Tuning

All the knobs live at the top of the `<script>` block or inside `draw()`.

| What | Where | Default | Effect |
|------|-------|---------|--------|
| Trail length | `const TRAIL_LENGTH` | `64` | Longer = longer comet tail |
| Reveal size | `let HEAD_RADIUS` | auto-scaled (`~26%` of min dimension) | Radius of the revealed circle |
| Cursor smoothing | `smooth.x += (mouse.x - smooth.x) * 0.14` | `0.14` | Lower = more lag/drift |
| Darkness | `const OVERLAY` | `'rgba(0,0,0,0.30)'` | Higher alpha = darker scene |
| Glow color | radial gradient in the reveal-ring block | violet | The cursor-head glow + rings |
| Ember tint | ember loop `cr,cg,cb2` values | red ambient → violet near cursor | Ember particle color |
| Thunder frequency | `nextStrike = now + 450 + random*1100` | ~0.45–1.5s between strikes | Lower = more frequent storms |
| Bolt jaggedness | `disp` / `detail` args in `buildBolt()` | `w*0.22` / `6` | Higher `disp` = wilder bolts |

**Examples**

- Calmer storm: `nextStrike = performance.now() + 1400 + Math.random()*3200;`
- Darker mood: `const OVERLAY = 'rgba(0,0,0,0.5)';`
- Warmer (crimson) reveal instead of violet: swap the `rgba(200,150,255…)` / `rgba(140,70,240…)` values back toward `rgba(255,170,90…)` / `rgba(255,60,40…)`.

---

## How it works

1. The **top** image (Yoriichi) is drawn to the visible canvas, then darkened with the overlay.
2. Every frame, the smoothed cursor position is pushed to the front of a `trail` array (capped at `TRAIL_LENGTH`).
3. On an **offscreen canvas**, each trail point is drawn as a black circle that shrinks and fades toward the tail.
4. The **bottom** image (Kokushibo) is composited onto that offscreen canvas with `globalCompositeOperation = 'source-in'`, so it only appears where the trail circles are.
5. The offscreen result is drawn over the visible canvas — Kokushibo now shows only inside the comet, with Yoriichi everywhere else.
6. A `lighter`-blended violet radial gradient plus two rings render at the trail's head.
7. Independently, a lightning system fires jagged bolts (built via recursive midpoint displacement) with a screen flash, on its own timer.
8. Ember particles drift continuously across the whole canvas, tinted based on distance from the reveal point.

```
top image (Yoriichi)    ──► visible canvas (+ overlay)
trail circles            ──► offscreen canvas
bottom image (Kokushibo) ──► offscreen canvas (source-in mask)
offscreen                ──► visible canvas
violet glow + rings      ──► visible canvas (lighter blend)
lightning bolts + flash  ──► visible canvas (independent timer)
embers                   ──► visible canvas (lighter blend, every frame)
```

---

## Browser support

Works in all current versions of Chrome, Edge, Firefox, and Safari. Requires Canvas 2D and `requestAnimationFrame` (universally supported). Touch is supported via `touchmove`.

---

## Project structure (suggested)

If you split the single file into a project:

```
demon-slayer-reveal/
├── index.html
├── css/
│   └── style.css
├── js/
│   └── reveal.js
└── images/
    ├── yoriichi.jpg    # default (top)
    └── kokushibo.jpg   # revealed (bottom)
```

---

## Credits & licensing

- Code: free to use and modify for your own projects.
- **Artwork & "Demon Slayer" / characters**: Kimetsu no Yaiba is created by Koyoharu Gotouge; all character art and trademarks belong to their respective rights holders. The images used here are fan/demo assets — replace them with art you have the right to use before publishing.
- Kanji used: 継国縁壱 (Tsugikuni Yoriichi) and 月ノ呼吸 (Moon Breathing) — standard Japanese, not the stylized in-universe title cards. Verify rendering on your target devices, as CJK glyphs depend on the system/browser font stack.

---

*The sun and the moon, forever chasing each other across the sky.* ⚔️
