# Stamp Studio Pro: Zero-Effort Batch Watermarking Suite

A high-performance, 100% client-side batch watermarking web application designed for professional creators, photographers, and social media managers who need to brand dozens of images in seconds without bloated menus, cloud uploads, or subscriptions.

### User Review & Critical Decisions

> [!IMPORTANT]
> **Confirmed Choices from Clarification Phase:**
> - **Watermark Types**: Support both image logos (PNG/SVG/WebP) and customizable text copyright/creator handles (with live font, color, and badge background options).
> - **Positioning Engine**: 9-point instant snap grid combined with direct interactive freeform canvas drag-and-drop.
> - **Batch Export Engine**: Creator-grade export panel with format selection (Original, JPG, WebP, PNG), compression quality slider (10%–100%), and template-based custom file naming (e.g., `{name}_stamped`, `{name}_wm_{date}`).

---

### 1. Overview & Core Concept

- **What It Does**: Stamp Studio Pro provides an ultra-fast, local-first workspace where creators drag in batches of images, apply persistent brand watermarks (logo graphic or text handle), adjust scale/opacity/position with tactile snap or direct canvas dragging, and download the entire watermarked set as a ZIP archive or individual photos.
- **Target Audience / Persona**: Photographers, digital artists, content creators, and e-commerce merchants who find Canva too slow and cloud converters insecure or privacy-invasive.
- **Key Value**: 100% client-side privacy (zero server uploads or telemetry), persistent browser storage (remembers logo, handle, and placement across reloads), zero lag, and instant batch ZIP archiving.

---

### 2. User Experience & Visual Design

#### Key User Flows
1. **Drop & Brand in 3 Seconds**:
   - Creator drops 1 to 50+ images anywhere on the screen.
   - The stored logo or handle is instantly composited onto the first active image in the real-time canvas viewport.
   - Creator clicks any of the 9 snap anchors or drags the watermark freely across the preview canvas.
2. **Watermark Customization**:
   - Seamless toggle between **Logo Mode** (drag PNG/SVG, remembers in localStorage, instant invert/tint) and **Text Handle Mode** (type `@creator`, copyright symbol `©`, select typography, opacity, fill color, and optional pill backdrop).
   - Fast tactile sliders for scale (5%–50%), opacity (10%–100%), margin insets, and high-contrast drop shadow.
3. **Export & Queue Management**:
   - Thumbnails carousel with active selection indicator, single-image delete, and full batch clear.
   - Export drawer with one-click format choice (`JPG`, `WebP`, `PNG`), quality slider (`0.85` default for sharp, lightweight files), and custom naming prefix/suffix (`{name}_branded`).
   - Live progress indicator during ZIP creation (`Processing 14/32 images...`).

#### Visual Identity & Theme
- **Aesthetic Direction**: High-density creative tool aesthetic, dark slate workspace (`#090d16` canvas, `#0f172a` surfaces) with high-contrast neutral borders (`#1e293b`), crisp typography, and an electric indigo accent (`#6366f1`) for active states.
- **Typography**: Clean contemporary pairing with `Plus Jakarta Sans` for display headlines and interface controls, `DM Sans` for body copy, and tabular monospace (`font-mono tabular-nums`) for coordinates, dimensions, percentages, and batch counters.
- **Top Bar Contract**:
  - *Zone 1 (Brand)*: Clean text wordmark **Stamp Studio** with quiet subtitle tag `Local Batch Engine`.
  - *Zone 2 (Links / Privacy Indicators)*: Clean text indicator `100% Client-Side · Zero Server Uploads · Offline Ready`.
  - *Zone 3 (Actions)*: Support link and clear queue button.
- **Canvas Viewport**: Generous checkered transparency canvas with real-time responsive bounding box for active watermark, supporting both click-to-snap and mouse/touch drag manipulation.

---

### 3. Key Product Decisions & Trade-Offs

- **Decision 1: Dual Positioning (9-Point Snap + Freeform Canvas Dragging)**
  - *Chosen Approach*: Maintain the 3x3 anchor grid for instant one-click corner snapping, while adding mouse/touch event listeners on the canvas overlay to permit freeform percentage-based coordinate dragging.
  - *Why*: Gives creators maximum speed (1 click for corner watermarks) without sacrificing precision when a logo needs to sit over a specific graphic element.
- **Decision 2: In-Browser Text & Logo Engine without External Dependencies**
  - *Chosen Approach*: Pure HTML5 Canvas 2D rendering pipeline using native FontMetrics and OffscreenCanvas/standard Canvas for export blobs.
  - *Why*: Eliminates bundle bloat, ensures 100% offline functionality, and allows the single-file HTML deliverable to be hosted directly on GitHub Pages.
- **Decision 3: Local-First Preset & Asset Persistence**
  - *Chosen Approach*: Store logo DataURL, text handle string, position anchor, custom coordinates, and export preferences in `localStorage`.
  - *Why*: Creators never have to re-upload their watermark or configure sliders every time they open the tool.

---

### 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────────────────────┐
│                          Stamp Studio Pro                              │
├────────────────────────────────────────────────────────────────────────┤
│  Top Bar: Wordmark ── Privacy Badge ── Preset Reset ── Support         │
├──────────────────────────┬─────────────────────────────────────────────┤
│  Toolbox Sidebar (320px) │  Interactive Canvas & Batch Drawer          │
│                          │                                             │
│  1. Watermark Type:      │  ┌───────────────────────────────────────┐  │
│     [Logo] / [Text]      │  │        Live Preview Canvas            │  │
│     • Drop Logo / Input  │  │  ┌──────────┐                         │  │
│     • Invert / Tint      │  │  │Watermark │ (Interactive Drag Box)  │  │
│                          │  │  └──────────┘                         │  │
│  2. Position & Snap:     │  │  Coordinates: (X: 85%, Y: 90%)        │  │
│     • 9-Anchor Snap Grid │  └───────────────────────────────────────┘  │
│     • Margin & Padding   │                                             │
│                          │  Batch Photo Queue:                         │
│  3. Appearance:          │  • Thumbnail Carousel [1][2][3]...          │
│     • Scale (5% - 50%)   │  • Add More Photos (+ Dropzone)             │
│     • Opacity (10-100%)  │                                             │
│     • High-Contrast Shdw │  Export Panel:                              │
│                          │  • Format: [JPG] [WebP] [PNG]               │
│  4. LocalStorage Sync    │  • Quality: 90% | Name: {name}_stamped      │
│     • Auto-save presets  │  • [⚡ Process & Download ZIP]              │
└──────────────────────────┴─────────────────────────────────────────────┘
```

#### Core State Model (`window.state`)
- `photos`: Array of `{ file: File, url: string, name: string, width: number, height: number }`
- `activePhotoIndex`: number
- `watermarkType`: `'logo' | 'text'`
- `logo`: `{ image: HTMLImageElement | null, dataUrl: string | null, invert: boolean }`
- `text`: `{ content: string, font: string, color: string, badgeBg: string, hasBadge: boolean }`
- `transform`: `{ anchor: string, customX: number | null, customY: number | null, scale: number, opacity: number, margin: number, dropShadow: boolean }`
- `export`: `{ format: 'image/jpeg' | 'image/webp' | 'image/png', quality: number, namePattern: string }`

#### Rendering Pipeline
1. **Interactive Preview**: Calculates aspect ratios, scales the current photo to fit the canvas viewport container while maintaining crisp device pixel ratio, draws image, calculates watermark coordinates (either snapped or custom percentage), and renders watermark with shadow and opacity.
2. **Canvas Drag Hit-Test**: Detects pointer clicks inside the watermark bounding box, tracks delta movement in normalized percentages `[0.0 - 1.0]`, updates `customX` and `customY`, and re-renders at 60fps.
3. **Batch Export**: Sequential canvas rendering with chunked `requestAnimationFrame` yielding to keep UI responsive, feeding into `JSZip` and triggering immediate client-side `.zip` download.
