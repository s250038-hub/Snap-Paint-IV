# 🎨 一拍即玩 SnapPaint IV

**Paint-by-numbers from your photos, infinite online levels, or a text prompt.**  
One HTML file. Zero dependencies. Type `sunset`, `貓`, or `城市`, pick a result, and paint it cell by cell.

[![Live Demo](https://img.shields.io/badge/▶_Play_Live-SnapPaint_IV-5EA0F2?style=for-the-badge)](https://s250038-hub.github.io/Snap-Paint-IV/)
[![Version](https://img.shields.io/badge/version-3.3.1-B07CF7?style=for-the-badge)](#-version-history)
[![Single File](https://img.shields.io/badge/single_file-index.html-35D07F?style=for-the-badge)](#-quick-start)
[![No Dependencies](https://img.shields.io/badge/dependencies-0-FFC53D?style=for-the-badge)](#-quick-start)

> 單檔 HTML・零依賴・提示搜尋圖片・長按拖軌填色・短點單填・拖曳移動・填錯保留正確數字且不計進度・油漆桶只對正確編號啟動。

---

## 🆕 v3.3.1 — Color integrity fix

Fixed a palette display bug where switching puzzles — especially **prompt search → next image** — could show colors from the previous puzzle in the palette strip.

- Palette DOM cache is now cleared whenever a new puzzle is entered
- Transient board state (dirty rectangles, flashes, last painted cell) is reset between puzzles
- Palette dots, brush color, tool ring, and saved artwork all read from the same quantized palette

If you previously saw a mismatch between the palette dot and the painted cell in search mode, update to **v3.3.1**.

---

## ✨ At a glance

| | |
|---|---|
| **Format** | One `index.html` — HTML + CSS + Canvas 2D + vanilla JS |
| **Dependencies** | None. No build step, no bundler, no framework |
| **Sources** | Your photos, infinite seeded levels, or prompt search |
| **Difficulty** | 7 tiers: 64 → 16,777,216 colors |
| **Gestures** | Short tap = single fill · drag = pan · long-press = trail paint · pinch = zoom |
| **Wrong fills** | Optional; keep the correct number on top and never count as progress |
| **Bucket** | Only fires on the correct number |
| **Storage** | IndexedDB with Blob storage + real device quota |
| **Offline** | Procedural levels and themed search fallbacks keep the game playable |
| **Privacy** | Photos are processed locally and never uploaded |

---

## 🚀 Quick start

### Play instantly

```text
https://s250038-hub.github.io/Snap-Paint-IV/
```

### Run locally

```bash
# Option 1 — open directly
open index.html

# Option 2 — serve locally (recommended for online search and levels)
python3 -m http.server 8080
# visit http://localhost:8080
```

No `npm install`. No config. Drop `index.html` on any static host and it runs.

---

## 🕹️ Controls

### Gestures

| Action | Touch | Mouse |
|---|---|---|
| **Single-cell fill** | Short tap | Left click |
| **Pan the board** | Touch, then drag | Left-drag, right-drag, or middle-drag |
| **Trail paint** | Hold ~320 ms until the ring appears, then drag | Hold left button until the ring appears, then drag |
| **Zoom** | Two-finger pinch | Scroll wheel / trackpad |
| **Fit / reset view** | Tap **⛶** | Click **⛶** |

> Double-tap-to-reset was removed from the game canvas so it cannot interfere with rapid single fills. Use **⛶** instead.

### In-game tools

| Tool | UI | Behavior |
|---|---|---|
| Paint bucket | 🪣 油漆桶 | Flood-fills a connected region **only when the tapped number matches the selected color** |
| Locate | 🔍 找格 | Jumps to a random unfilled cell of the selected color and zooms in |
| Fit | ⛶ | Fits the whole board to the screen |
| Reset | ↺ 重置 | Two-step confirm: tap once to arm, tap again within 3 s |
| Number select | 編號 / 選擇 | Type a color number, then Enter or tap Select |

---

## 🔍 Prompt search

The online screen has two modes:

```text
♾️ 關卡  |  🔍 提示搜尋
```

### How it works

1. Switch to **🔍 提示搜尋**.
2. Type a prompt, for example:
   - `貓`
   - `sunset`
   - `城市`
   - `flower`
   - `mountain`
3. Press Enter or tap **🔍**.
4. Browse the thumbnail strip and tap a result.
5. Choose a grid size and start painting.

### Search backend

| Source | Behavior |
|---|---|
| **Wikimedia Commons** | Real keyword image search via the public MediaWiki API |
| **Offline themed art** | Deterministic procedural images derived from the prompt when search fails |

The Commons request uses:

```text
action=query
generator=search
gsrnamespace=6          (File namespace)
prop=imageinfo
iiprop=url|mime|size
iiurlwidth=640
origin=*                (anonymous CORS)
```

Only usable raster images are kept:

- `image/jpeg`, `image/png`, `image/webp`
- Minimum source size: 160 × 160
- Up to 18 results per search

### Search grid sizes

| Option | Long side | Use case |
|---|---:|---|
| 小 | 64 | Quick, relaxing sessions |
| 中 | 120 | Default balanced play |
| 大 | 200 | Detailed, longer sessions |

The grid preserves the source image aspect ratio, so searched pictures are never stretched.

### Offline fallback styles

When Commons is unavailable, the app generates prompt-aware art:

| Prompt keywords | Generated style |
|---|---|
| `city`, `urban`, `street`, `城市`, `街`, `夜景` | Night city |
| `mountain`, `山`, `峰` | Mountain dawn |
| `sea`, `ocean`, `beach`, `海`, `沙灘`, `浪` | Sunset sea |
| `flower`, `花`, `abstract`, `抽象`, `geometry`, `幾何` | Geometric mosaic |
| `hill`, `field`, `grass`, `草原`, `田`, `森林`, `forest`, `樹` | Rolling hills |
| Anything else | Seeded variation of the five styles |

Search results are saved as works with:

```text
mode: "search"
query: "sunset"
index: 3
```

After completion, the win screen offers **▶ 下一張「query」**.

---

## 🎨 Wrong-color rules

Controlled by **⚙️ 設定 → 允許填錯顏色**. Default: **OFF**.

| Situation | `allowWrong` OFF (default) | `allowWrong` ON |
|---|---|---|
| Tap a wrong number with brush | Nothing happens. No flash, no sound, no progress | Cell is painted with the selected color, **the correct number stays on top**, progress does not change |
| Tap a wrong number with bucket | Bucket does not activate | Bucket still does not activate |
| Later paint the same cell correctly | — | Wrong paint is replaced, the number disappears, and progress counts normally |
| Save / resume | — | Wrong fills are saved and restored with the work |

Wrong fills are stored separately from completed cells, so they can never inflate progress, finish a color, or trigger a win.

---

## 🎚️ Difficulty

Difficulty lives in **⚙️ 設定** and applies to the **next** puzzle.

| Tier | UI label | Depth | Max colors |
|---|---|---:|---:|
| Easy | 🙂 簡單 | 4 × 4 × 4 | 64 |
| Mid | 😌 普通 | 8 × 8 × 8 | 512 |
| **Hard (default)** | 😠 困難 | 16 × 16 × 16 | **4,096** |
| Insane | 😡 瘋狂 | 32 × 32 × 32 | 32,768 |
| Easy Demon | 👿 初階惡魔 | 64 × 64 × 64 | 262,144 |
| Hard Demon | 👿 進階惡魔 | 128 × 128 × 128 | 2,097,152 |
| Extreme Demon | 👿 極限惡魔 | 256 × 256 × 256 | 16,777,216 |

Actual palette size is the number of distinct quantized colors present in the image, bounded by the cell count.

---

## 📷 Photo mode

SnapPaint does **not** pre-shrink your photo to 1080 px or 1920 px.

| Chip | Meaning |
|---|---|
| 1x 完整 | One cell per source pixel — full original resolution |
| 1/2x | Half width and height |
| 1/4x | Quarter |
| 1/8x | One eighth |
| 1/16x | One sixteenth |
| 1/32x | One thirty-second |

| Guard | Value | Purpose |
|---|---:|---|
| Auto-selected resolution | ≤ 150,000 cells | Keeps the first session playable |
| Soft warning | > 1,000,000 cells | Warns about slower load/save |
| Hard device guard | 16,000,000 cells | Prevents obvious out-of-memory attempts |

The source photo itself is never capped. You choose the grid density.

---

## 🌐 Infinite online levels

Online levels are deterministic and unbounded.

```text
longSide = min(24 + (level − 1) × 6, 240)
aspect cycle = [4:3, 1:1, 3:4, 16:10, 3:2, 4:5]
```

| Level | Grid | Cells |
|---:|---|---:|
| 1 | 24 × 18 | 432 |
| 2 | 30 × 30 | 900 |
| 3 | 27 × 36 | 972 |
| 10 | 78 × 49 | 3,822 |
| 37+ | up to 240 × 180 | up to 43,200 |

### Image sources

| Source | Behavior |
|---|---|
| **Auto** | Last successful network source first, then the other source, then offline art |
| Unsplash | Curated photos with seeded focal-point crops |
| Picsum | Seeded random photos (`snappaint-{level}`) |
| Offline | Deterministic procedural art generated in-browser |

### Fallback chain

```text
Auto:
  last-good network source
    → alternate network source
      → procedural offline level

If navigator.onLine is false:
  skip network → procedural offline level
```

- Per-source timeout: **7 s**
- After a failed network round: **2.5 s** timeouts for the next 60 s
- Offline art styles: mountain dawn, sunset sea, geometric mosaic, rolling hills, night city

---

## 💾 Storage & saves

### Where things live

| Data | Store |
|---|---|
| Artwork, previews, progress masks | **IndexedDB** (`snapPaintDB / works`) as Blobs |
| Settings, level progress, preferences | localStorage (a few hundred bytes) |
| Legacy v1/v2 saves | Migrated once on first run, then removed from localStorage |

### Storage meter

- Counts **only artwork** — settings bytes are excluded
- Empty gallery = **0 B**
- Quota comes from `navigator.storage.estimate()` when available
- Fallback display quota: 512 MB
- `navigator.storage.persist()` is requested once after the first successful save

### Autosave triggers

- ~1.2 s after painting stops
- Immediately when a stroke ends
- When leaving the game screen
- When the tab is hidden
- On `pagehide` / `beforeunload`

### Save contents

Each work stores:

- Lossless quantized puzzle PNG (Blob)
- JPEG preview (Blob)
- Correct-fill RLE mask
- Optional wrong-fill map
- Grid size, depth, color count, elapsed time
- Mode, level, search query, and result index
- Completion state

Completed works keep the lossless puzzle PNG and preview, but drop the progress mask.

---

## 🏆 Gallery & viewer

- In-progress and completed works
- Resume any unfinished puzzle
- Full-screen viewer with pinch / wheel zoom
- Delete with confirmation **and undo**
- Download completed works as PNG
- Live artwork-only storage meter
- Badges for mode, level, search query, progress, completion, and difficulty

---

## ⚙️ Settings

| Setting | Default | Effect |
|---|---|---|
| 🔊 音效／震動 | ON | Paint ticks, bucket sweep, color chime, win arpeggio, haptics |
| 🎨 允許填錯顏色 | OFF | Wrong fills keep the correct number and do not count as progress |
| 🎚️ 色深難度 | Hard · 16³ | Applies to the next puzzle |

Settings are reachable from the menu, photo setup, and online screen.

---

## 🔊 Sound & feedback

Sound is **on by default** and synthesized with Web Audio — no audio files.

| Event | Feedback |
|---|---|
| Paint a cell | Short pitched tick |
| Bucket fill | Rising sweep |
| Finish a color | Two-note chime + haptic |
| Complete puzzle | Arpeggio + palette-colored confetti |
| UI action | Tiny click |
| Wrong number, `allowWrong` OFF | Silence — no flash |
| Wrong number, `allowWrong` ON | Paint tick; correct number remains visible |

A visible mute control appears on the menu, game HUD, online screen, gallery, and in Settings. Muting disables haptics too.

---

## ⌨️ Keyboard

| Key | Location | Action |
|---|---|---|
| `Enter` | Color number input | Select that color |
| `Enter` | Level jump input | Go to level |
| `Enter` | Search input | Run search |
| `Esc` | Dialog / settings / viewer | Close |
| `←` / `→` | Focused palette strip | Move one color |
| `PageUp` / `PageDown` | Focused palette strip | Move one page |
| `Home` / `End` | Focused palette strip | First / last unfinished color |

---

## 🧠 Architecture

### Color quantization

For depth `L`:

```text
q = round(channel × (L − 1) / 255)
key = (qR × L + qG) × L + qB
reconstructed = round(q × 255 / (L − 1))
```

The palette is sorted by frequency, then by color key, so large regions appear first and numbering is deterministic.

### Single source of truth for color

Every visible color in a puzzle comes from one palette:

```text
P.pal.keys[pi] + P.pal.CH  →  [r, g, b]
```

Used consistently by:

- Palette dots
- Brush and bucket paint
- Tool ring / cursor tint
- Target PNG export
- Confetti color sampling

The palette DOM cache is cleared on every new puzzle and on reset, so dots can never carry over colors from a previous image.

### Exact resume

The saved puzzle image is a **lossless PNG** of quantized colors. Re-quantizing those exact RGB values reproduces the same keys, totals, and palette order, so color numbers never drift after resume.

### Wrong-fill model

```text
P.filled[i] = 1   → correct fill; counts as progress; number hidden
P.wrong[i] = pi   → wrong fill; correct number shown; progress unchanged
```

Correct fills and wrong fills are separate. Painting a cell correctly later clears its wrong-fill entry.

### Virtualized palette

The palette strip uses a **Fenwick tree**:

- `O(log n)` rank/select over unfinished colors
- Completed colors disappear from the strip
- Only visible palette items exist in the DOM
- Custom scrollbar avoids native scroll-width limits at millions of colors
- Drag, wheel, keyboard, and scrollbar thumb all navigate the strip

### Rendering pipeline

```text
source image
  → grid canvas
  → quantize to L³ keys
  → frequency-sorted palette
  → lossless target PNG
  → grayscale work canvas
  → correct / wrong fills update dirty rectangles
  → main canvas: board + grid + pan/zoom
  → incremental number layer
```

Numbers are drawn for every unfilled cell — including wrong-filled cells — with contrast computed from the visible painted color, so the correct number stays readable on any background.

### Flood fill

The bucket uses an iterative scanline flood fill:

- No recursive stack overflow
- Fires only when the tapped cell matches the selected number
- Fills only connected cells of that number
- Updates one dirty bounding box per fill
- Clears any wrong-fill entries inside the filled region

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

## 🔒 Privacy

- Photos are processed entirely in your browser.
- No photo is uploaded to any server.
- No account, no analytics, no cookies.
- Online levels and prompt search fetch public images from Wikimedia Commons, Unsplash, and Picsum.
- Saves live in IndexedDB on your device.
- Clearing site data removes saved works.
- Export important works with the PNG download button.

---

## 📱 Browser support

Designed for modern evergreen browsers with:

- Canvas 2D
- Pointer Events
- IndexedDB
- localStorage
- ES2020 syntax
- CSS `aspect-ratio`
- Web Audio API (optional, for sound)
- Vibration API (optional, for haptics)

Targets desktop Chrome / Edge / Firefox / Safari and mobile Chrome / iOS Safari. Respects safe-area insets and reduced-motion preferences.

---

## 🛠️ Troubleshooting

| Symptom | Fix |
|---|---|
| Palette dot color does not match painted cell | Update to **v3.3.1**. The palette cache is now cleared between puzzles. |
| Search returns no usable images | The app falls back to offline themed art. Try a broader or English keyword. |
| Online preview fails | Auto mode falls back to offline art. You can also set Source to **Offline** or press Retry. |
| “儲存空間不足” | Open the gallery and delete old works. Deletion can be undone from the toast. |
| Huge photo fails to build | Lower the resolution chip or choose a smaller color depth. |
| No sound | Check the mute button. Sound unlocks after the first tap/click. |
| Progress missing after closing | Private browsing, cleared site data, or quota limits may prevent saving. Check the storage meter. |
| Palette has too many colors | Use **🔍 找格**, type a number, or drag the palette scrollbar. Completed colors vanish automatically. |
| Board is off-screen | Tap **⛶** to fit the whole board. |
| Wrong tap does nothing | Expected when **允許填錯顏色** is OFF. Enable it in Settings to paint wrong colors. |
| Bucket does nothing on a wrong number | Expected. The bucket only activates on the correct number. |

---

## 🚢 Deploy

### GitHub Pages

1. Put `index.html` at the repository root.
2. Go to **Settings → Pages**.
3. Set **Source** to “Deploy from a branch”.
4. Choose your branch and `/ (root)`.
5. Save.

Live deployment:

```text
https://s250038-hub.github.io/Snap-Paint-IV/
```

### Other static hosts

Works on Netlify, Vercel, Cloudflare Pages, S3 + CloudFront, or a plain nginx / Apache directory.

```text
Build command: (none)
Output directory: /
Entry file: index.html
```

---

## 🗂️ Code map

| Section | Responsibility |
|---|---|
| §1–§3 | Constants, settings, IndexedDB, quota, migration |
| §4–§6 | Screens, toast, confirm, storage UI, synth audio, confetti |
| §7–§8 | Game state; Fenwick palette + virtual strip + custom scrollbar |
| §9 | Quantization, palette build/sort, wrong-fill-aware assembly, RLE |
| §10–§11 | Autosave persistence; canvas renderer + incremental number layer |
| §12 | Brush, wrong fills, bucket, locate |
| §13 | Tap / drag / long-press / pinch gestures |
| §14–§15 | Timer, progress, reset, win; photo resolution flow |
| §16 | Online levels, prompt search, fallback chain, procedural art |
| §17–§18 | Resume rebuild; gallery, viewer, delete + undo |
| §19–§20 | Settings difficulty chips; bindings and boot |

---

## 📜 Version history

### v3.3.1
- Fixed palette color mismatch when switching puzzles, most visible in prompt-search mode
- Palette DOM cache is cleared on every new puzzle and on reset
- Transient board state is reset between puzzles

### v3.3
- Prompt image search via Wikimedia Commons
- Thumbnail result strip and selectable results
- Search grid size selector (64 / 120 / 200)
- Offline prompt-themed fallback art
- Search works saved with query and index
- “Next image” completion flow
- Search-query gallery badges

### v3.2
- Wrong fills keep the correct number visible and never count as progress
- Bucket only activates on the correct number
- Wrong fills are saved and restored separately
- Fixed an autosave re-queue loop caused by a function-name collision

### v3.1
- Long-press trail paint with ring indicator
- Short tap = single fill; drag = pan
- Removed double-tap reset; added **⛶** fit button
- Removed wrong-color flash
- Settings overlay: sound, allow-wrong, difficulty
- Difficulty moved out of photo/online setup into Settings

### v3.0
- IndexedDB + Blob artwork storage
- Real device quota via `navigator.storage.estimate()`
- Artwork-only storage meter; empty gallery = 0 B
- One-time migration from localStorage saves

### v2.1
- Multi-source online loading with offline procedural fallback
- Source selector and source badge
- Custom palette scrollbar for huge palettes
- Responsive difficulty grid

### v2.0
- Full single-file rewrite
- Seven color-depth tiers
- Infinite seeded online levels
- Full-resolution photo support
- Virtualized palette, locate tool, bucket, autosave, resume, gallery, viewer
- Synth sound, haptics, confetti, delete undo

### v1.0
- Original photo paint-by-numbers game

---

## 🙏 Credits

- Prompt search: [Wikimedia Commons](https://commons.wikimedia.org/) via the public MediaWiki API
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
