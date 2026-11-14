# 🎨 一拍即玩 SnapPaint

**Paint-by-numbers, reinvented as a single HTML file.**  
Snap a photo or pick an infinite online level, then fill numbered cells until the original image reappears. Seven color-depth tiers, full-resolution photos, offline-safe levels, autosave, gallery, and a paint-studio dark UI — all with **zero dependencies**.

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-35D07F?style=for-the-badge)](https://s250038-hub.github.io/)
[![Single File](https://img.shields.io/badge/Single%20File-index.html-5EA0F2?style=for-the-badge)](#-quick-start)
[![No Dependencies](https://img.shields.io/badge/Dependencies-0-FFC53D?style=for-the-badge)](#-quick-start)
[![Mobile First](https://img.shields.io/badge/Mobile-First-B07CF7?style=for-the-badge)](#-browser-support)

---

## ✨ What is SnapPaint?

SnapPaint turns any image into a numbered paint-by-numbers puzzle:

1. **Choose a source** — your photo, or an infinite seeded online level.
2. **Pick a color depth** — from 64-color Easy to 16.7-million-color Extreme Demon.
3. **Paint by number** — tap or drag matching cells; use the bucket for connected regions.
4. **Complete the picture** — confetti, stats, autosave, download, or jump to the next level.

Everything runs locally in the browser. Photos are never uploaded.

---

## 🚀 Quick start

### Play instantly

Open the live version:

```text
https://s250038-hub.github.io/
```

### Run locally

No build step, no package manager, no bundler.

```bash
# Option 1: just open the file
open index.html

# Option 2: serve it locally (recommended for online levels)
python3 -m http.server 8080
# then visit http://localhost:8080
```

> The entire app — markup, styles, icons, sound synthesis, procedural art, and game engine — lives inside `index.html`.

---

## 🕹️ How to play

### Core loop

```text
Select a numbered color → find matching cells → paint → color completes →
auto-advance to next color → reach 100% → win → download / next level
```

### Tools

| Tool | Chinese UI | Behavior |
|---|---|---|
| Single-cell brush | 🖌️ 單格填色 | Paint one cell at a time; drag to paint continuously |
| Paint bucket | 🪣 油漆筒 | Flood-fill a connected region of the selected number |
| Locate | 🔍 找格 | Jump to a random unfilled cell of the selected color and zoom in |
| Number select | 編號 / 選擇 | Type a color number and press Enter or tap Select |
| Reset | ↺ 重置 | Two-step confirm: tap once to arm, tap again within 3 seconds |

### Gestures

| Action | Touch | Mouse / trackpad |
|---|---|---|
| Paint | One-finger tap / drag | Left-drag starting on a matching cell |
| Pan | Two-finger drag | Right-drag, middle-drag, or left-drag on empty / wrong cells |
| Zoom | Pinch | Scroll wheel / trackpad scroll |
| Fit to screen | Double-tap | Double-click |
| Wrong color | Red flash + soft buzz | Red flash + soft buzz |

---

## 🎚️ Difficulty tiers

Difficulty is controlled by **color depth** — how finely each RGB channel is quantized.

| Tier | UI label | Depth | Maximum colors | Character |
|---|---|---:|---:|---|
| Easy | 🙂 簡單 | 4 × 4 × 4 | 64 | Large readable regions, relaxing |
| Mid | 😌 普通 | 8 × 8 × 8 | 512 | Casual sessions |
| **Hard (default)** | 😠 困難 | 16 × 16 × 16 | **4,096** | The intended challenge |
| Insane | 😡 瘋狂 | 32 × 32 × 32 | 32,768 | Dense, many small regions |
| Easy Demon | 👿 初階惡魔 | 64 × 64 × 64 | 262,144 | Thousands of tiny color islands |
| Hard Demon | 👿 進階惡魔 | 128 × 128 × 128 | 2,097,152 | Brutal precision |
| Extreme Demon | 👿 極限惡魔 | 256 × 256 × 256 | 16,777,216 | Effectively lossless color |

> Actual palette size is the number of distinct quantized colors present in the image, bounded by the cell count. A 24 × 18 online level can never exceed 432 colors, even on Extreme Demon.

---

## 🌐 Infinite online levels

Online levels are seeded by level number, so every level has a stable identity.

### Level geometry

```text
longSide = min(24 + (level − 1) × 6, 240)
aspect cycle = [4:3, 1:1, 3:4, 16:10, 3:2, 4:5]
```

| Level | Grid | Cells |
|---:|---|---:|
| 1 | 24 × 18 | 432 |
| 2 | 30 × 30 | 900 |
| 3 | 27 × 36 | 972 |
| 4 | 42 × 26 | 1,092 |
| 5 | 48 × 32 | 1,536 |
| 10 | 78 × 49 | 3,822 |
| 37+ | up to 240 × 180 | up to 43,200 |

The level counter is unbounded; grid size caps at 240 on the long side to keep sessions playable.

### Image sources

The online screen includes a visible source selector:

| Source | Behavior |
|---|---|
| **Auto** | Tries the last successful network source first, then the other network source, then offline art |
| Unsplash | Curated landscape photos with seeded focal-point crops |
| Picsum | Seeded random photos (`snappaint-{level}`) |
| Offline | Deterministic procedural art generated in-browser |

### Network fallback chain

```text
Auto:
  last-good network source
    → alternate network source
      → procedural offline level

If navigator.onLine is false:
  skip network entirely → procedural offline level
```

- Per-source timeout: **7 seconds**
- After a failed network round: **2.5-second** timeouts for the next 60 seconds
- If all network sources fail, the game **still works** with generated art
- Offline art styles: mountain dawn, sunset sea, geometric mosaic, rolling hills, night city

> Saved games always resume exactly, because the quantized puzzle image is stored losslessly as PNG.

---

## 📷 Photo mode — full resolution

SnapPaint does **not** pre-shrink your photo to 1080px or 1920px.

| Resolution chip | Meaning |
|---|---|
| 1x 完整 | One cell per source pixel — full original resolution |
| 1/2x | Half width and height |
| 1/4x | Quarter width and height |
| 1/8x | One eighth |
| 1/16x | One sixteenth |
| 1/32x | One thirty-second |

### Smart defaults and guards

| Setting | Value | Purpose |
|---|---:|---|
| Auto-selected resolution | ≤ 150,000 cells | Keeps the first session playable |
| Soft warning | > 1,000,000 cells | Warns about slower load/save |
| Hard device guard | 16,000,000 cells | Prevents obvious out-of-memory attempts |

The source photo itself is never capped. You choose the grid density.

---

## 💾 Saves, gallery, and storage

### Autosave

Progress is saved automatically:

- ~1.2 seconds after painting stops
- Immediately when a stroke ends
- When leaving the game screen
- When the tab is hidden
- On `pagehide` / `beforeunload`

### Gallery features

- In-progress and completed works
- Resume any unfinished puzzle
- Full-screen viewer with pinch / wheel zoom
- Delete with confirmation **and undo**
- Download completed works
- Live storage meter

### Storage keys

| Key | Contents |
|---|---|
| `snapPaintWorksV2` | Saved works |
| `snapPaintSettingsV2` | Depth, mute, online level, completed levels, source mode, best level |
| `snapPaintMigratedV2` | One-time migration flag |
| `snapPaintWorks` | Legacy v1 key, read once during migration |

### Save format

Each work stores:

```json
{
  "id": 1730000000000,
  "date": "2026/11/14",
  "mode": "online",
  "level": 12,
  "depth": 16,
  "cols": 90,
  "rows": 68,
  "colors": 1234,
  "elapsed": 342,
  "time": "5:42",
  "completed": false,
  "puzzleImg": "data:image/png;base64,<lossless quantized puzzle>",
  "filled": "<base64 RLE filled mask>",
  "filledCount": 1200,
  "total": 6120,
  "preview": "data:image/jpeg;base64,<thumbnail>"
}
```

Completed works keep the lossless puzzle PNG and a preview, but drop the filled mask.

### Quota behavior

| Usage | Behavior |
|---:|---|
| < 85% | Normal |
| ≥ 85% | Warning toast |
| ≥ 95% | New games are blocked until space is freed |
| `setItem` fails | Graceful warning; the current session remains playable |

The app assumes a 10 MB localStorage budget and also handles real browser quota failures.

---

## 🧠 Architecture

### One file, no dependencies

```text
.
└── index.html
    ├── HTML screens & overlays
    ├── CSS design system
    └── JavaScript game engine
```

### Color quantization

For depth `L` (4, 8, 16, 32, 64, 128, or 256):

```text
q = round(channel × (L − 1) / 255)
key = (qR × L + qG) × L + qB
reconstructed = round(q × 255 / (L − 1))
```

The palette is sorted by frequency, then by color key. This makes large regions appear first and keeps numbering deterministic.

### Why resume is exact

The saved puzzle image is a **lossless PNG** of quantized colors. Re-quantizing those exact RGB values reproduces the same keys, totals, and palette order — so color numbers never drift after resume.

### Rendering pipeline

```text
source image
  → grid canvas (cols × rows)
  → quantize to L³ color keys
  → frequency-sorted palette
  → lossless target PNG for saves
  → grayscale work canvas
  → painted cells update dirty rectangles
  → main canvas: board + grid lines + pan/zoom
  → incremental number layer (6 ms/frame budget)
```

### Huge-palette support

The palette strip is virtualized with a **Fenwick tree**:

- `O(log n)` rank/select over unfinished colors
- Completed colors disappear from the strip
- Only visible palette items exist in the DOM
- Custom scrollbar avoids native scroll-width limits at millions of colors
- Drag, wheel, keyboard, and scrollbar thumb all navigate the strip

### Flood fill

The bucket uses an iterative scanline flood fill:

- No recursive stack overflow
- Fills only connected cells with the selected number
- Updates one dirty bounding box per fill
- Triggers color completion, autosave, and win detection correctly

---

## ⚡ Performance guards

| Guard | Value | Why |
|---|---:|---|
| Device pixel ratio | capped at 2 | Crisp without rendering every device pixel |
| Main canvas backing store | ≤ 9 MP | Bounds memory and fill cost |
| Number layer | 6 ms per frame | Pan/zoom stays responsive |
| Number visibility | cell ≥ 12 px | Avoids unreadable micro-text |
| Number outline | cell ≥ 18 px | Keeps small cells cheap |
| Dirty updates | union bounding box | Fewer `putImageData` calls |
| Palette DOM | visible items only | Scales to extreme palettes |
| Reduced motion | respected | Confetti and pulses are disabled when requested |

---

## 🔊 Sound and feedback

Sound is **on by default** and synthesized with Web Audio — no audio files.

| Event | Feedback |
|---|---|
| Paint a cell | Short pitched tick |
| Wrong color | Red flash + low buzz + haptic |
| Bucket fill | Rising sweep |
| Finish a color | Two-note chime + haptic |
| Complete puzzle | Arpeggio + palette-colored confetti |
| UI action | Tiny click |

A visible mute button appears on the menu and in every game/online/gallery HUD.  
Muting also disables haptics.

Browser autoplay rules mean audio unlocks on the first user interaction.

---

## ⌨️ Keyboard shortcuts

| Key | Location | Action |
|---|---|---|
| `Enter` | Color number input | Select that color |
| `Enter` | Level jump input | Go to level |
| `Esc` | Dialog / viewer | Close |
| `←` / `→` | Focused palette strip | Move one color |
| `PageUp` / `PageDown` | Focused palette strip | Move one page |
| `Home` / `End` | Focused palette strip | First / last unfinished color |

---

## 🔒 Privacy

- Photos are processed entirely in your browser.
- No photo is uploaded to any server.
- No account, no analytics, no cookies.
- Online levels fetch public photos from Unsplash / Picsum CDNs.
- Saves live in localStorage on your device.
- Clearing site data removes saved works.
- Export important works with the PNG download button.

---

## 📱 Browser support

Designed for modern evergreen browsers with:

- Canvas 2D
- Pointer Events
- localStorage
- ES2020 syntax
- CSS `aspect-ratio`
- Web Audio API (optional, for sound)
- Vibration API (optional, for haptics)

Targets desktop Chrome / Edge / Firefox / Safari and mobile Chrome / iOS Safari.  
The layout respects safe-area insets and reduced-motion preferences.

---

## 🛠️ Troubleshooting

| Symptom | Fix |
|---|---|
| Online preview fails | Auto mode falls back to offline art. You can also set Source to **Offline** or press Retry. |
| “儲存空間不足” | Open the gallery and delete old works. Deletion can be undone from the toast. |
| Huge photo fails to build | Lower the resolution chip or choose a smaller color depth. |
| No sound | Check the mute button. Sound unlocks after the first tap/click. |
| Progress missing after closing | Private browsing, cleared site data, or quota limits may prevent saving. Check the storage meter. |
| Palette has too many colors | Use **🔍 找格**, type a number, or drag the palette scrollbar. Completed colors vanish automatically. |
| Board is off-screen | Double-tap / double-click to fit the whole board. |

---

## 🗂️ Code map

The script is organized into numbered sections:

| Section | Responsibility |
|---|---|
| §1–§3 | Constants, settings, storage quota, screens, toast, confirm |
| §4–§5 | Synth audio, haptics, confetti |
| §6–§7 | Game state; Fenwick palette + virtual strip + custom scrollbar |
| §8–§9 | Quantization, palette build/sort, RLE codec |
| §10–§11 | Autosave persistence; canvas renderer + incremental number layer |
| §12–§13 | Painting, scanline flood fill, locate; pointer gestures |
| §14–§15 | Timer, progress, reset, win; photo resolution flow |
| §16 | Online sources, fallback chain, procedural offline art |
| §17–§18 | Resume rebuild; gallery, viewer, delete + undo |
| §19–§20 | Depth chips; bindings and boot |

---

## 🚢 Deploy

### GitHub Pages

1. Put `index.html` at the repository root.
2. Go to **Settings → Pages**.
3. Set **Source** to “Deploy from a branch”.
4. Choose your branch and `/ (root)`.
5. Save.

For a user-pages repo named `username.github.io`, the app will be available at:

```text
https://username.github.io/
```

The current live deployment:

```text
https://s250038-hub.github.io/
```

### Other static hosts

SnapPaint works on any static host:

- Netlify
- Vercel
- Cloudflare Pages
- GitHub Pages
- S3 + CloudFront
- A plain nginx / Apache directory

Use these settings:

```text
Build command: (none)
Output directory: /
Entry file: index.html
```

---

## 📜 Version history

### v2.1

- Multi-source online loading: Unsplash → Picsum → procedural offline art
- Visible source selector and source badge
- Guaranteed playable offline levels
- Custom palette scrollbar for extremely large palettes
- Depth chips shown as a responsive grid
- Better preview loading, placeholder, and error states

### v2.0

- Full rewrite as a clean single-file app
- Seven color-depth tiers, Easy through Extreme Demon
- Infinite seeded online levels
- Full-resolution photo support
- Virtualized palette with Fenwick tree
- Incremental number rendering layer
- Locate tool, bucket, reset, autosave, resume, gallery, viewer
- Synth sound effects, haptics, confetti
- Delete confirmation with undo
- One-time migration from v1 saves

### v1.0

- Original photo paint-by-numbers game

---

## 🙏 Credits

- Online photos: [Unsplash](https://unsplash.com/) and [Lorem Picsum](https://picsum.photos/)
- Procedural offline art: generated in-app with Canvas 2D
- Icons: native emoji and inline SVG
- Sound: synthesized with the Web Audio API
- No external JavaScript or CSS libraries

---

## 📄 License

No license file is currently included. Add a `LICENSE` file if you want to define reuse terms.

---

Made with one HTML file, Canvas 2D, and a lot of tiny numbered squares.
