# Design System Inspiration of Miro (iOS)

## 1. Visual Theme & Atmosphere

Miro's iOS app is built around one radical idea: an **infinite canvas** in your pocket. Where most productivity apps are lists and forms, Miro is a boundless, pannable, zoomable whiteboard — you pinch to zoom, drag two fingers to pan, and the content (sticky notes, shapes, connectors, frames, images, text) lives in an unbounded 2D coordinate space, not a scrolling document. The board background is a subtle **dotted or square grid** on a near-white surface (`#F5F5F7` light, deep neutral `#202024` dark) that scales with zoom, giving spatial reference without competing with content. This canvas-first model is Miro's entire identity: the UI is minimal chrome floating *over* the board, never framing it.

The brand color is impossible to mistake: **Miro Yellow** (`#FFD02F`) — the warm, almost-gold yellow of the logo and of a default sticky note — paired with **Miro Ink** (`#050038`), a very dark desaturated indigo that's Miro's "black" for text, the logo, and dark buttons. Yellow is the primary action color (Create board, the active toolbar tool, the brand mark). When you select an object on the canvas, the selection bounding box, resize handles, and transform UI render in **Miro Blue** (`#4262FF`) — a distinct functional accent reserved for selection/interaction state, never confused with the brand yellow.

The signature interaction surfaces are: the **floating toolbar** (a rounded-rectangle bar of creation tools — select, sticky note, pen, shapes, text, frame, comment, more — that hovers at the bottom or side of the canvas; the active tool fills Miro Yellow with an ink glyph), the **sticky note** (a tight-4pt-radius colored square with a soft drop shadow and a bottom drag-grip; the most-created object, available in a fixed pastel palette), **connectors** (lines/arrows that snap object-to-object and reflow when objects move), **frames** (dashed-border named regions that group content and become presentation slides), and **live multiplayer cursors** (per-collaborator colored arrows with a name tag, moving in real time). Everything in Miro is created and arranged through these affordances on the canvas.

Typography is **Inter** as the UI face (Miro's product UI uses a clean neutral grotesque very close to Inter; Inter is the faithful, freely-licensed stand-in), falling back to SF Pro on iOS. Because most "text" in Miro is *user content on sticky notes and shapes*, the type system is more about UI hierarchy than editorial rhythm: board title 28pt extrabold, section/heading 22pt bold, frame labels 18pt bold, sticky-note text 16pt semibold (auto-fit to note size), body/UI 16pt, toolbar/controls 15pt semibold, meta 14pt. The whole app feels energetic, collaborative, and tactile — bright yellow, soft-shadowed paper notes, springy object manipulation, and the constant subtle presence of other people's cursors.

**Key Characteristics:**
- **Infinite canvas** — pinch-zoom, two-finger pan; content lives in unbounded 2D space, not a scroll view
- **Dotted/square grid board** — `#F5F5F7` light / `#202024` dark; grid scales with zoom for spatial reference
- **Miro Yellow** (`#FFD02F`) + **Miro Ink** (`#050038`) — the unmistakable brand pair
- **Miro Blue** (`#4262FF`) — functional selection/interaction accent (bounding box, handles), distinct from brand
- **Floating toolbar** — rounded bar of creation tools; active tool fills yellow with ink glyph
- **Sticky notes** — tight 4pt-radius pastel paper squares with soft shadow + drag-grip; the core object
- **Connectors** — object-snapping lines/arrows that reflow when objects move
- **Frames** — dashed named regions that group content and double as presentation slides
- **Live multiplayer cursors** — per-user colored arrows + name tags moving in real time
- **Zoom control + minimap** — persistent zoom pill (+ / value / −); minimap for board navigation
- Minimal chrome: compact board top bar (back · board name · collaborators · share · ⋯) over the canvas

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Miro Yellow** (`#FFD02F`): Primary action — Create board, active toolbar tool fill, brand mark.
- **Miro Yellow Pressed** (`#E8B800`): Pressed state for yellow controls.
- **Miro Ink** (`#050038`): Brand "black" — logo, primary text on light, dark button background, glyph on yellow.
- **Miro Blue** (`#4262FF`): Functional selection accent — bounding box, resize handles, links, focus ring, active connector.
- **Miro Blue Pressed** (`#2F4AE0`): Pressed state for blue controls.

### Canvas & Surfaces (Light Mode)
- **Board Surface** (`#F5F5F7`): The infinite-canvas background.
- **Grid Dots/Lines** (`#D7D7DE`): Subtle grid on the board (scales with zoom).
- **Canvas White** (`#FFFFFF`): App chrome base, sheets, sticky-note inspector.
- **Surface Gray** (`#F1F1F4`): Toolbar/panel fills, secondary chrome.
- **Surface Pressed** (`#E6E6EB`): Pressed rows, hovered tools.
- **Divider** (`#E3E3E8`): Hairline dividers in chrome.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#1B1B1F`): App chrome base in dark.
- **Dark Board** (`#202024`): The infinite-canvas background in dark.
- **Dark Grid** (`#38383F`): Subtle grid on dark board.
- **Dark Surface 1** (`#26262B`): Toolbar, top bar, sheets.
- **Dark Surface 2** (`#313138`): Hovered tools, pressed rows.
- **Dark Divider** (`#38383F`): Hairline dividers.

### Text
- **Text Primary Light** (`#050038`): Primary chrome/UI text — Miro Ink.
- **Text Secondary Light** (`#6B6B7B`): Metadata, captions.
- **Text Tertiary Light** (`#9A9AA4`): Disabled, low-emphasis, connector default.
- **Text Primary Dark** (`#ECECEF`): Primary text on dark.
- **Text Secondary Dark** (`#9A9AA4`): Secondary on dark (shared tone).
- **Note Ink** (`#2A2A33`): Text rendered *on* colored sticky notes (dark, on all pastel fills).

### Sticky Note Palette (fixed pastels — paper, never goes dark)
These are the user-selectable sticky-note / shape-fill colors. They stay full-color in both themes.

| Role | Fill | On-Note Text |
|------|------|--------------|
| Yellow (default) | `#FEF3B6` | `#2A2A33` |
| Pink | `#F8C8D8` | `#2A2A33` |
| Green | `#C7E8C7` | `#2A2A33` |
| Blue | `#BCE0F5` | `#2A2A33` |
| Orange | `#FCD9B6` | `#2A2A33` |
| Violet | `#DCC9F0` | `#2A2A33` |
| Teal | `#BFE8E0` | `#2A2A33` |
| Gray | `#E2E2E6` | `#2A2A33` |

### Multiplayer Cursor Colors (per-collaborator)
Assigned round-robin to collaborators; identical across themes.

| Slot | Color |
|------|-------|
| 1 | `#4262FF` |
| 2 | `#F24822` |
| 3 | `#14AE5C` |
| 4 | `#9747FF` |
| 5 | `#F2A900` |
| 6 | `#E5489E` |

### Semantic
- **Error Red** (`#E5484D`): Delete confirmation, error toast, destructive actions.
- **Success Green** (`#2EA56A`): Save/sync-success toast, online indicator.
- **Warning Amber** (`#F0A92B`): Warning callouts, offline/limited-sync banner.
- **Selection Blue** (`#4262FF`): Bounding box, resize handles, marquee selection.
- **Marquee Fill** (`#4262FF1F`): ~12% blue fill of the drag-select rectangle.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — neutral humanist grotesque; faithful, freely-licensed stand-in for Miro's product UI sans, excellent at small UI sizes and on sticky notes.
- **Handwriting Option (sticky/marker)**: `Caveat` (Google Fonts, SIL OFL) — optional marker/handwriting style some users pick for sticky notes and pen-adjacent text.
- **Mono (code/embed)**: `JetBrains Mono` (SIL OFL) — used only inside code embeds.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **iOS behavior**: falls back to SF Pro Display (titles) / SF Pro Text (UI) when Inter isn't bundled.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Board Title | Inter | 28pt | 800 | 1.2 | -0.5pt | Top bar / board info sheet |
| Section / H1 | Inter | 22pt | 700 | 1.25 | -0.3pt | Sheet titles, frame headings |
| Frame Label | Inter | 18pt | 700 | 1.3 | -0.2pt | Dashed-frame name label |
| Sticky Note | Inter | 16pt | 600 | 1.3 | 0pt | Text on a note (auto-fits to note size) |
| Body / UI | Inter | 16pt | 400 | 1.5 | 0pt | Sheet body, descriptions |
| Toolbar / Control | Inter | 15pt | 600 | 1.0 | 0pt | Tool labels, segmented controls |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | Primary/secondary buttons |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "Edited by Dana · 4 collaborators" |
| Caption / Zoom | Inter | 12pt | 600 | 1.0 | 0.2pt | Zoom %, small labels |
| Connector Label | Inter | 13pt | 500 | 1.2 | 0pt | Optional label on a connector |
| List Row | Inter | 16pt | 500 | 1.3 | 0pt | Boards list, templates list |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels (boards home) |
| Avatar Initials | Inter | 11pt | 700 | 1.0 | 0.2pt | Collaborator avatar fallback |

### Principles
- **UI hierarchy over editorial rhythm**: most "text" is user content on notes/shapes — the type ramp serves chrome and labels, not long-form reading.
- **Sticky-note text auto-fits**: note text starts at 16pt w600 and scales down to fit the note's box (min ~9pt) as you type — never clips.
- **Extrabold reserved for the board title**: 800 is the single most expressive weight; toolbar/buttons top out at 700.
- **High legibility at zoom**: UI text stays fixed pixel size regardless of canvas zoom; only canvas *content* scales with zoom.
- **Dynamic Type for chrome**: sheet titles, body, list rows scale; toolbar labels, zoom %, avatar initials, connector labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Create board / Done)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: `#FFD02F` (Miro Yellow)
- Text: `#050038` (Miro Ink), Inter 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: `#E8B800` + scale 0.98 + soft haptic

**Dark Button (Share / secondary primary)**
- Background: `#050038` (Miro Ink)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Same shape/padding; pressed: lighten 8% + scale 0.98

**Secondary / Outline Button**
- Background: transparent
- Text: `#050038` light / `#ECECEF` dark
- Border: 1pt `#E3E3E8` / `#38383F`
- Corner radius: 10pt
- Padding: 12pt vertical, 20pt horizontal
- Font: Inter 14pt weight 600
- Pressed: background `#E6E6EB` / `#313138`

**Ghost / Text Button (Invite / Cancel)**
- Background: transparent, no border
- Text: `#4262FF` (action) or `#6B6B7B` (Cancel)
- Font: Inter 14pt weight 600
- Pressed: 0.6 opacity

**Toolbar Tool Button**
- Size: 40pt tile, 11pt corner radius, inside the floating bar (8pt bar padding, 16pt bar radius)
- Default: glyph `#6B6B7B` / `#9A9AA4`, transparent fill
- Active (selected tool): fill `#FFD02F`, glyph `#050038`
- Pressed: scale 0.94 + soft haptic; long-press opens the tool's sub-palette (e.g. shape picker)

**Zoom Control Buttons (`+` / `−`)**
- Stacked pill, 38pt wide; glyph `#050038` / `#ECECEF` 18pt; center shows zoom % (Inter 12pt w600)
- Tap steps zoom by 25%; the % label tap resets to 100% / fit

### Canvas Objects (the core atoms)

**Sticky Note** — *signature*
- Shape: square (default ~96pt at 100% zoom), 4pt corner radius (tight — paper feel)
- Fill: one of the fixed pastel palette (default yellow `#FEF3B6`)
- Shadow: soft `0 8px 18px -8px rgba(0,0,0,0.18)` light / `…0.55` dark — notes "lift" off the board
- Text: Inter 16pt w600 `#2A2A33`, center-aligned, auto-fit to box
- Selected: 2pt `#4262FF` bounding box at 2pt offset + corner resize handles + a bottom drag-grip pill
- Double-tap to edit text; long-press to drag; pinch on a selected note to resize

**Shape (rectangle / ellipse / diamond / etc.)**
- Stroke: 2pt, default `#050038`; fill: transparent or a pastel
- Corner radius: shape-dependent (rounded-rect default 8pt)
- Selected: same `#4262FF` bounding box + handles

**Connector (line / arrow)**
- Stroke: 2pt `#4262FF` when selected, `#6B6B7B` default
- Endpoints snap to object anchor points; reflows automatically when either object moves
- Optional mid-label: Inter 13pt w500 on a small white/ink chip
- Style options: straight / elbow / curved; arrowhead on/off

**Frame**
- Border: 2pt dashed `#9A9AA4`
- Corner radius: 6pt
- Label: top-left, Inter 18pt w700 `#6B6B7B`
- Acts as a content group; doubles as a presentation slide in present mode

**Text Object**
- Free-floating text on the canvas; Inter, size set by the user; default `#050038`
- Selected: dashed `#4262FF` box + handles

**Image / Embed Object**
- Dropped/pasted media; selected box + handles; corner radius 4pt

**Comment Pin**
- A circular `#FFD02F` pin (28pt) with an ink speech-glyph; tap opens the comment thread bubble

### Board Structure & Navigation

**Board Top Bar (over the canvas)**
- Height: 48pt + safe area, translucent over the board
- Leading: back chevron + board name + disclosure chevron (board menu)
- Trailing: collaborator avatar stack (26pt circles, overlapping) · share · ⋯ (board actions)
- No large title — the canvas is the content

**Floating Toolbar**
- Position: bottom-center (phone) or left edge (tablet), floating with `0 14px 32px -12px rgba(0,0,0,0.22)` shadow
- Background: `#FFFFFF` / `#26262B`, 1pt divider border, 16pt corner radius, 8pt padding
- Tools (typical order): Select, Sticky note, Pen, Shapes, Text, Frame, Comment, More (`+`)
- Active tool: yellow fill + ink glyph; tools are 40pt tiles, 11pt radius

**Zoom + Navigation**
- Persistent zoom pill (bottom-right) `+ / value / −`
- Minimap (toggle): a small board-overview thumbnail with a viewport rectangle for fast navigation
- Two-finger drag pans; pinch zooms; double-tap empty space zooms to fit

**Boards Home (outside a board)**
- A standard list/grid of board thumbnails; bottom tab bar: Boards, Templates, Activity, Profile
- List rows: thumbnail + board name (Inter 16pt w500) + meta (Inter 14pt) + ⋯
- Active tab: `#050038` / `#ECECEF` icon (no tint pill); inactive `#6B6B7B` / `#9A9AA4`

### Input & Panels

**Board Search / Find on Board**
- Height: 44pt; background `#F1F1F4` / `#313138`; no border; 10pt radius
- Leading `magnifyingglass` 16pt `#9A9AA4`; placeholder "Search board" 15pt `#9A9AA4`
- Focus: 2pt `#4262FF` ring

**Object Inspector (context bar)**
- A floating bar that appears above a selected object: color swatch, font, lock, duplicate, delete
- Background `#FFFFFF` / `#26262B`, 12pt radius, soft shadow; swatches are 24pt circles

**Bottom Sheet (Templates / Share / Board menu)**
- Corner radius 16pt (top); grabber handle; spring-presented; dim `rgba(5,0,56,0.32)` behind

### Distinctive Components

**Infinite Canvas** — the defining surface: an unbounded 2D coordinate space with a zoom-aware dotted/square grid. Pinch-zoom, two-finger pan, double-tap-to-fit. All objects live here in absolute coordinates.

**Floating Toolbar** — the creation hub: a rounded floating bar whose active tool fills Miro Yellow with an ink glyph. Long-press a tool to open its sub-palette (shapes, pen colors, sticky colors).

**Sticky Note** — the iconic object: a tight-radius pastel paper square with a soft lift shadow, auto-fitting text, a `#4262FF` selection box, and a bottom drag-grip. The default and most-created object.

**Connector** — smart object-to-object lines/arrows that snap to anchor points and reflow live as objects move; optional mid-label chip.

**Frame** — a dashed, named region that groups content and becomes a slide in present mode; its label sits top-left.

**Live Multiplayer Cursors** — per-collaborator colored arrows with name tags moving in real time across the board; the constant signal that Miro is multiplayer.

**Minimap & Zoom Pill** — persistent spatial-navigation aids: a viewport-rectangle minimap and a `+ / % / −` zoom pill, because an infinite canvas needs wayfinding.

**Comment Pin** — a yellow circular pin dropped on the canvas; tap to expand a threaded comment bubble; resolves with a check.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Toolbar tile size: 40pt; bar padding: 8pt
- Chrome content inset: 16pt horizontal
- Default sticky note: ~96pt square at 100% zoom; grid cell: 28pt at 100% zoom

### Grid & Container
- The board is **not** a fixed grid — it's an unbounded coordinate plane; the visible dotted grid is a zoom-scaled reference overlay
- Chrome (top bar, toolbar, sheets) is laid out in standard 16pt-inset device space, floating over the canvas
- iPhone: toolbar bottom-center; iPad: toolbar can dock to the left edge; sheets max-width 520pt centered on tablet
- Objects snap to the grid and to other objects' edges/centers (smart guides) when dragging

### Whitespace Philosophy
- **The board breathes infinitely**: there is no "page" — empty canvas is a feature; objects float with intentional spacing the user controls
- **Chrome floats, never frames**: toolbar, top bar, and inspector hover over the canvas with shadows; they never box it in
- **Notes lift off the board**: every sticky/shape gets a soft drop shadow so content reads as physical paper on a surface
- **Selection is loud, idle is quiet**: idle objects have no chrome; selecting one summons the `#4262FF` box, handles, and inspector

### Border Radius Scale
- Square (0pt): grid, marquee rectangle
- Paper (4pt): sticky notes, image objects, comment chips (tight — paper feel)
- Subtle (8pt): rounded-rect shapes
- Comfortable (10pt): buttons, search bar, inspector swatch row
- Standard (12pt): inspector bar, zoom pill, popovers
- Large (16pt): floating toolbar, bottom sheets
- Pill (500pt): drag-grip, segmented toggles, tags
- Circle (50%): collaborator avatars, comment pins

## 6. Depth & Elevation

Miro uses depth to separate *content on the board* from *chrome over the board*, and to make objects feel like physical paper.

| Level | Treatment | Use |
|-------|-----------|-----|
| Board (Level 0) | No shadow, grid overlay | The infinite canvas itself |
| Object (Level 1) | `0 8px 18px -8px rgba(0,0,0,0.18)` light / `…0.55` dark | Sticky notes, shapes, images on the board |
| Chrome (Level 2) | `0 8px 20px -10px rgba(0,0,0,0.16)` | Top bar, zoom pill, inspector bar |
| Floating (Level 3) | `0 14px 32px -12px rgba(0,0,0,0.22)` | Floating toolbar, popovers, tool sub-palettes |
| Sheet (Level 4) | `0 -10px 40px rgba(5,0,56,0.20)` | Bottom sheets (Templates, Share, Board menu) |
| Modal Overlay | `rgba(5,0,56,0.32)` | Dim behind sheets/modals (ink-tinted) |

**Shadow Philosophy**: object shadows make sticky notes read as paper lifted off a board (the tactile core of Miro); chrome shadows make floating UI clearly hover above the canvas. The dim behind modals is *ink-tinted* (`rgba(5,0,56,…)`) not pure black, reinforcing the brand. On dark mode, object/chrome shadows deepen and a 1pt divider border is added to floating panels as a secondary elevation cue.

### Motion
- **Pan**: 1:1 finger tracking with momentum/inertia decay (deceleration ≈ UIScrollView, ~0.5s glide)
- **Zoom (pinch)**: continuous, anchored at the pinch midpoint; double-tap-to-fit animates over 350ms ease-out
- **Object drag**: 60fps follow with a slight scale-up (1.03) + shadow bloom; smart-guide snap lines appear instantly; drop settles with spring (response 0.30, damping 0.82) + soft haptic
- **Object create (e.g. sticky)**: appears with a 200ms spring scale-in from 0.85 at the tap point + soft haptic
- **Connector draw**: rubber-band line follows the finger; snaps to a target anchor with a subtle magnet pull + tick haptic
- **Tool select**: active-tool yellow fill cross-fades in 120ms; tool sub-palette pops with 180ms spring
- **Selection box**: `#4262FF` bounding box + handles fade-in over 120ms when an object is selected
- **Multiplayer cursors**: remote cursors interpolate smoothly between network updates (~16–60ms tween) so they glide, not jump
- **Sheet present**: slides up 360ms spring (response 0.40, damping 0.82), ink dim fades in
- **Save/sync**: silent; a subtle "Saved"/"Syncing…" pill appears only on conflict or offline
- **Haptic feedback**: soft impact on object create, drag drop/snap, connector snap, tool select

## 7. Do's and Don'ts

### Do
- Treat the board as an **infinite 2D canvas** — pinch-zoom, two-finger pan, absolute object coordinates
- Render a **zoom-scaled dotted/square grid** as the board reference — `#D7D7DE` light / `#38383F` dark
- Use **Miro Yellow** (`#FFD02F`) + **Miro Ink** (`#050038`) for brand/primary actions; glyph on yellow is always ink
- Reserve **Miro Blue** (`#4262FF`) strictly for selection/interaction state (box, handles, marquee, links)
- Make sticky notes tight-radius (4pt) pastel paper squares with a soft lift shadow + drag-grip
- Float the toolbar over the canvas; fill the **active tool** yellow with an ink glyph
- Keep sticky-note text **auto-fitting** to the note box (16pt w600 down to ~9pt)
- Show **live multiplayer cursors** with per-user colors + name tags, interpolated smoothly
- Provide a persistent **zoom pill + minimap** — an infinite canvas needs wayfinding
- Keep UI text fixed pixel size; only **canvas content** scales with zoom
- Use object shadows so notes/shapes read as physical paper lifted off the board

### Don't
- Don't make the board a scroll view or a fixed-size page — it's an unbounded coordinate plane
- Don't tint the brand with blue or the selection with yellow — yellow = brand/action, blue = selection
- Don't use pure black for text or dim — Miro Ink `#050038` and ink-tinted dim are correct
- Don't give sticky notes large/rounded radii — 4pt tight paper corners are the signature
- Don't dock the toolbar as an opaque bottom bar that frames the canvas — it floats over it
- Don't desaturate sticky notes in dark mode — paper stays full-color; only chrome goes dark
- Don't scale UI chrome text with canvas zoom — only objects zoom
- Don't omit the zoom/minimap affordances — users get lost on an infinite canvas without them
- Don't make multiplayer cursors jump frame-to-frame — interpolate so they glide
- Don't animate object creation/drag without spring + haptic — the tactile feel is core
- Don't box the canvas in chrome borders — chrome floats with shadows, never frames

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Toolbar bottom-center, condensed (overflow into More); zoom pill smaller |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in board top bar |
| iPhone 15/16 Pro Max | 430pt | Full toolbar visible without overflow |
| iPad (portrait) | 768pt | Toolbar can dock to the left edge; inspector as a side panel |
| iPad (landscape) | 1024pt+ | Left toolbar + right properties panel; larger minimap |

### Dynamic Type
- Sheet titles, body, list rows, board title: full scale
- Toolbar labels, zoom %, avatar initials, connector labels, tab labels: FIXED (layout-sensitive)
- Sticky-note text: auto-fits to note box (independent of Dynamic Type — content, not chrome)

### Orientation
- All screens rotate; the canvas viewport recomputes but object world-coordinates are preserved
- iPad gains a docked left toolbar + right properties panel in landscape
- Pinch/pan gestures unaffected by rotation

### Touch Targets
- Toolbar tool: 40pt tile (≥44pt hit with padding)
- Zoom `+`/`−`: 38pt wide, 36pt tall (44pt hit area)
- Object resize handle: 12pt glyph, 44pt hit
- Avatar in stack: 26pt visible, 44pt hit
- Comment pin: 28pt, 44pt hit
- List rows / buttons: ≥44pt tall

### Safe Area Handling
- Top: board top bar respects safe area + Dynamic Island (translucent over canvas)
- Bottom: floating toolbar and zoom pill sit above the home indicator
- Keyboard: editing sticky-note text raises an inline editor above the keyboard; canvas re-centers the note
- Sides: chrome insets 16pt; the canvas itself bleeds edge-to-edge under the chrome

## 9. Agent Prompt Guide

### Quick Color Reference
- Board (light): `#F5F5F7` · Board (dark): `#202024`
- Grid: `#D7D7DE` light / `#38383F` dark
- Chrome surface: `#FFFFFF` light / `#26262B` dark
- Text primary: `#050038` (Miro Ink) light / `#ECECEF` dark
- Text secondary: `#6B6B7B` light / `#9A9AA4` dark
- Miro Yellow (brand/primary): `#FFD02F` (pressed `#E8B800`)
- Miro Ink (brand black / dark btn): `#050038`
- Miro Blue (selection/interaction): `#4262FF`
- Sticky yellow (default): `#FEF3B6` · pink `#F8C8D8` · green `#C7E8C7` · blue `#BCE0F5`
- On-note text: `#2A2A33`
- Error: `#E5484D` · Success: `#2EA56A` · Warning: `#F0A92B`
- Marquee fill: `#4262FF1F`

### Example Component Prompts
- "Build the Miro infinite canvas in SwiftUI: a zoom-scaled dotted grid (dots `#D7D7DE` light / `#38383F` dark) on a `#F5F5F7` / `#202024` board. Support `MagnificationGesture` for pinch-zoom anchored at the pinch midpoint, two-finger `DragGesture` for pan with inertia, and double-tap to zoom-to-fit over 350ms. Objects are positioned in absolute world coordinates and transformed by the current zoom/offset."

- "Create a Miro sticky note: a ~96pt square with 4pt corner radius, fill `#FEF3B6` (default yellow), soft shadow `0 8px 18px -8px rgba(0,0,0,0.18)`. Auto-fit text in Inter 16pt w600 `#2A2A33`, center-aligned, scaling down to ~9pt to fit. When selected, draw a 2pt `#4262FF` bounding box at 2pt offset with corner resize handles and a bottom drag-grip pill. Double-tap edits text; created with a 200ms spring scale-in from 0.85 + soft haptic."

- "Render the Miro floating toolbar: a `#FFFFFF` / `#26262B` rounded bar, 16pt corner radius, 8pt padding, 1pt divider border, shadow `0 14px 32px -12px rgba(0,0,0,0.22)`, bottom-center over the canvas. Tools (Select, Sticky, Pen, Shapes, Text, Frame, Comment, More) are 40pt tiles at 11pt radius; default glyph `#6B6B7B`. The active tool fills `#FFD02F` with a `#050038` glyph. Long-press a tool opens its sub-palette with a 180ms spring."

- "Build the Miro primary button: `#FFD02F` background, 10pt corner radius, Inter 15pt w700 `#050038` label, 13pt vertical / 26pt horizontal padding. Pressed: `#E8B800` + scale 0.98 + soft haptic. Variant: dark button with `#050038` bg and `#FFFFFF` label."

- "Create a Miro connector: a 2pt line whose endpoints snap to two objects' anchor points and reflow whenever either object moves. Default stroke `#6B6B7B`, `#4262FF` when selected, optional arrowhead, optional mid-label chip in Inter 13pt w500. Drawing rubber-bands from the source and magnet-snaps to the target with a tick haptic."

- "Render Miro live multiplayer cursors: per-collaborator colored arrow (colors round-robin from `#4262FF`, `#F24822`, `#14AE5C`, `#9747FF`, `#F2A900`, `#E5489E`) with a name-tag pill in that color (Inter 10pt w700 white). Interpolate position smoothly between network updates so cursors glide, never jump."

- "Build the Miro zoom pill: a stacked `#FFFFFF` / `#26262B` pill (12pt radius, soft shadow) bottom-right with `+` (top), the zoom % in Inter 12pt w600 (middle), and `−` (bottom). Tap `+`/`−` steps zoom 25%; tapping the % resets to fit."

### Iteration Guide
1. The board is an **infinite 2D canvas** — pinch-zoom, two-finger pan with inertia, absolute world coordinates; NOT a scroll view
2. Render a **zoom-scaled dotted/square grid** as the board (`#D7D7DE` light / `#38383F` dark) — it scales with zoom
3. **Miro Yellow `#FFD02F` + Miro Ink `#050038`** = brand/primary; glyph on yellow is always ink
4. **Miro Blue `#4262FF`** is reserved strictly for selection/interaction (bounding box, handles, marquee, links)
5. Sticky notes are tight-4pt-radius pastel paper squares with a soft lift shadow + drag-grip; text auto-fits
6. The toolbar **floats** over the canvas; the active tool fills yellow with an ink glyph
7. Provide a persistent **zoom pill + minimap** — an infinite canvas needs wayfinding
8. Show **live multiplayer cursors** (per-user colors, name tags) interpolated smoothly
9. UI chrome text is **fixed size**; only canvas *content* scales with zoom
10. Motion is springy + haptic on create/drag/snap; remote cursors glide; dim behind modals is ink-tinted `rgba(5,0,56,0.32)`
