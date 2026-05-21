# Design System Inspiration of VSCO (iOS)

## 1. Visual Theme & Atmosphere

VSCO's iOS app is a darkroom, not an app. The entire editing experience lives on a pure-black canvas (`#000000` — true black, not a charcoal) because VSCO's core belief is that the photograph is the only thing that should carry color. Open an image into the editor and you see exactly three things: the photo (full-bleed, centered, breathing with generous black margin), a hairline-thin adjustment slider, and a horizontally-scrolling carousel of film-emulation presets at the bottom. There are no cards, no shadows, no rounded panels, no tint. The chrome is a monochrome white-on-black skin so quiet that the eye never leaves the image.

The signature interface moment is the **filter/preset carousel** — a row of 56pt square thumbnails, each a tiny live preview of the photo under that film recipe (A6, C1, M5, AV4, KP9 — VSCO's famously cryptic two-character preset codes), with the active preset marked by a 2px solid-white ring (never a colored highlight). Tapping a preset cross-fades the photo into the graded look in ~250ms. The second signature is the **adjust tool tray** — a row of uppercase, wide-tracked tool labels (EXPOSURE, CONTRAST, SATURATION, GRAIN, SHARPEN, HSL…) that swap the carousel for a single **bipolar hairline slider**: a 1px track on `#3A3A3A`, a white fill from the zero-point, and an 18pt solid-white knob. The numeric value (`+2.4`, `−1.8`) sits above the slider in white. This slider is the most VSCO thing in the product — it is almost invisible, and that is the point.

The color system is radically restrained: black, white, and a 3-step gray ramp for dividers, tracks, and secondary text. There is no brand accent color — no "Spotify Green", no "Airbnb Coral". Selection, focus, and active states are all communicated through pure white (a ring, a fill, a label going from `#B3B3B3` to `#FFFFFF`). The only color that ever appears in the UI is incidental: the photograph itself, and the film-tinted preset thumbnails (warm ambers `#C8A86B`, cool steel `#7E8FA1`) which are previews of *content*, not chrome.

Typography is **Inter** (VSCO's brand face is the proprietary "VSCO Gothic"; Inter is the closest free Google substitute — a neutral grotesque). The signature typographic move is **uppercase labels with very wide letter-spacing** (`+1.6` for tool labels, `+1.0` for preset tags) — it reads like a film-stock data-back imprint or a darkroom enlarger dial. Editorial titles (the Discover and Studio screens) use sentence-case Inter at 24–32pt, weights 600–700, but the *editing chrome* is exclusively uppercase tracked caps. The type system is the only expressive flourish in an otherwise invisible UI.

**Key Characteristics:**
- Pure black canvas (`#000000`) — the editor is dark-native, there is NO light editing theme
- Monochrome UI — white, three grays, zero accent color; the photo is the only color
- Hairline bipolar sliders — 1px track `#3A3A3A`, white fill from zero, 18pt white knob
- Film-preset carousel — 56pt square live-preview thumbnails, 2px white ring on active
- Adjust tool tray — uppercase wide-tracked labels swap to the single slider
- Selection = white only — a 2px ring or a fill change, never a tint
- Uppercase wide-tracked Inter labels (`+1.6` tracking) — film-stock imprint feel
- No chrome — no cards, no shadows, no borders on the editing surface
- Studio = full-bleed photo grid — a tight gapless monochrome thumbnail grid
- Discover = editorial sentence-case feed, the only "designed" screen
- 2px corner radius everywhere — squares, not pills; film frames, not bubbles

## 2. Color Palette & Roles

### Primary (Interactive)
- **VSCO White** (`#FFFFFF`): The single interactive color — primary buttons, active labels, slider fill + knob, selection ring, "Next" action.
- **VSCO White Pressed** (`#DBDBDB`): Pressed state on the white primary button.
- **VSCO Black** (`#000000`): The canvas and the text-on-white color. Not a surface — the absence of one.

There is intentionally **no accent / brand-tint color**. VSCO communicates state with white alone.

### Canvas & Surfaces (Dark — the only mode for the editor)
- **VSCO Black** (`#000000`): Primary canvas — the editor stage, the photo margin, the tab bar.
- **Surface 1** (`#0E0E0E`): Feed / Studio / Discover screen background — a near-black lift.
- **Surface 2** (`#1A1A1A`): Sheets, menus, settings rows, contextual panels.
- **Surface 3** (`#242424`): Pressed rows, segmented-control selected fill, chips.
- **Divider** (`#2A2A2A`): Hairline separators between tool tray and stage, list rows.
- **Slider Track** (`#3A3A3A`): The unfilled portion of every adjustment slider.

### Light Mode (Studio / Discover browsing only)
VSCO offers a light theme for the *browsing* surfaces only (Studio grid, Discover feed, profile) — the editor never goes light. It is rarely used and deliberately plain.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#000000` |
| Surface 1 | `#F2F2F2` | `#0E0E0E` |
| Surface 2 | `#E8E8E8` | `#1A1A1A` |
| Divider | `#DCDCDC` | `#2A2A2A` |
| Text Primary | `#000000` | `#FFFFFF` |
| Text Secondary | `#767676` | `#B3B3B3` |

### Text
- **Text Primary Dark** (`#FFFFFF`): Active labels, values, titles, body on black.
- **Text Secondary Dark** (`#B3B3B3`): Inactive tool labels, metadata, captions.
- **Text Tertiary Dark** (`#767676`): Disabled, EXIF detail, inactive tab icons.
- **Text Primary Light** (`#000000`): Body on the light browsing surfaces.
- **Text Secondary Light** (`#767676`): Metadata on light.

### Incidental Film Tones (content previews — not chrome)
These appear only inside preset thumbnails as a *preview of the graded photo*. Never use them as UI fills.

| Role | Color | Where |
|------|-------|-------|
| Film Warm (amber) | `#C8A86B` | Warm film presets (C-series, KP) thumbnail tint |
| Film Cool (steel) | `#7E8FA1` | Cool film presets (M-series, AV) thumbnail tint |
| Film Neutral | `#9A9388` | Neutral/desaturated preset thumbnail tint |
| Film Shadow | `#3F382C` | Shadow region of a warm-graded preview |

### Semantic
- **Destructive Red** (`#E5484D`): Delete photo confirmation, "Discard edits" — the ONLY non-monochrome UI color, used sparingly in destructive sheets only.
- **Success** (`#FFFFFF`): "Saved to Camera Roll" toast — communicated in white, not green.
- **Disabled**: text drops to `#767676`, controls to 38% opacity.

## 3. Typography Rules

### Font Family
- **Brand Face**: `VSCO Gothic` (proprietary, a neutral humanist grotesque) — not publicly licensed.
- **Recommended Substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — neutral grotesque, excellent at small UI sizes and wide tracking.
- **Editorial Alternate** (Discover headlines, optional): `Inter` at 700 — VSCO occasionally sets editorial titles in a tighter display cut; Inter Bold covers it.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace** (EXIF / values, optional): `SF Mono` — used only for technical capture metadata when shown.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Case | Notes |
|------|------|------|--------|-------------|----------------|------|-------|
| Display | Inter | 32pt | 700 | 1.15 | -0.4pt | Sentence | "Studio", profile name hero |
| Title | Inter | 24pt | 600 | 1.2 | -0.2pt | Sentence | Discover section, screen title |
| Section | Inter | 19pt | 600 | 1.3 | -0.1pt | Sentence | "Recipes", "Recents" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Sentence | Editorial body, descriptions |
| Label | Inter | 14pt | 500 | 1.4 | 0pt | Sentence | Row labels, "Edited 2h ago" |
| Caption | Inter | 13pt | 400 | 1.4 | 0pt | Sentence | EXIF, file metadata |
| Value (live) | Inter | 14pt | 600 | 1.0 | 0pt | Numeric | `+2.4` slider value, white |
| Tool Label | Inter | 12pt | 600 | 1.0 | +1.6pt | UPPERCASE | EXPOSURE, CONTRAST, GRAIN |
| Preset Tag | Inter | 11pt | 600 | 1.0 | +1.0pt | UPPERCASE | A6, C1, M5 preset codes |
| Nav Action | Inter | 13pt | 600 | 1.0 | +1.4pt | UPPERCASE | NEXT, SAVE, CANCEL |
| Top Title | Inter | 13pt | 500 | 1.0 | +1.4pt | UPPERCASE | EDIT (editor title bar) |
| Tab Label | Inter | 10pt | 600 | 1.0 | +0.6pt | UPPERCASE | Bottom tab (when shown) |
| Button | Inter | 12pt | 600 | 1.0 | +1.4pt | UPPERCASE | Primary / outline buttons |

### Principles
- **Two registers only**: sentence-case editorial (titles/body, weight 400–700) for *content* screens; UPPERCASE wide-tracked (weight 600) for *editor chrome*. Never mix within one zone.
- **Wide tracking is the signature**: tool labels at `+1.6`, preset codes at `+1.0`, nav actions at `+1.4` — this film-data-back imprint is VSCO's strongest type identity.
- **Weight stays at 400 / 500 / 600 / 700**: no thin, no black. Editorial body is 400; all chrome is 600.
- **Values are white, labels are gray**: the live numeric value (`+2.4`) is always `#FFFFFF`; its label is `#B3B3B3` until active.
- **Dynamic Type on content, fixed on chrome**: editorial titles/body scale; tool labels, preset codes, slider values, tab labels stay fixed (layout-critical, single-line).

## 4. Component Stylings

### Buttons

**Primary Button ("Next" / "Save Recipe" / "Post")**
- Shape: Rectangle, 2pt corner radius (film-frame square, NOT pill)
- Background: `#FFFFFF` / text `#000000`
- Padding: 14pt vertical, 28pt horizontal
- Font: Inter 12pt weight 600, UPPERCASE, `+1.4` tracking
- Pressed: background `#DBDBDB` + scale 0.98
- Often rendered as a top-bar text action ("NEXT") rather than a filled button — same type spec, no fill

**Outline / Secondary Button ("Save Recipe", "Edit")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt `#FFFFFF`
- Padding: 13pt vertical, 26pt horizontal
- Corner radius: 2pt
- Pressed: background `#FFFFFF` 12% overlay

**Ghost / Cancel Button**
- Background: transparent
- Text: `#B3B3B3` (secondary)
- No border, no fill
- Padding: 13pt vertical, 16pt horizontal
- Pressed: text → `#FFFFFF`

**Text Action ("Reset all", "Show original")**
- Font: Inter 12pt weight 600, UPPERCASE, `+1.4` tracking
- Color: `#FFFFFF`
- No underline, no fill
- Pressed: 60% opacity

**Icon Button (tool-tray, top bar × / ✓)**
- Glyph: 22pt stroke icon, `stroke-width` 1.6
- Hit area: 44pt
- Default: `#B3B3B3`; active: `#FFFFFF`
- No background, no ring — color change only

### Adjust Slider (the core atom)

The single most important component. A bipolar hairline.
- **Track**: full-width, 1pt tall, `#3A3A3A`
- **Fill**: from the zero-point (often center) to the knob, 1pt tall, `#FFFFFF`
- **Knob**: 18pt circle, solid `#FFFFFF`, shadow `rgba(0,0,0,0.6) 0 1px 4px`, hit area 44pt
- **Label row** (above slider): tool name UPPERCASE 12pt `+1.6` `#B3B3B3` (left), live value 14pt w600 `#FFFFFF` (right)
- **Zero detent**: a subtle haptic + the fill collapsing to 0 when the knob crosses the neutral value
- **Double-tap**: resets to default (value animates back, soft haptic)
- No tint, no gradient, no shadow on the track — just two hairlines

### Film-Preset Carousel (the signature)

A horizontal scroll of live-preview tiles.
- **Tile**: 56pt square, 2pt corner radius, the actual photo rendered under that preset's grade
- **Spacing**: 14pt between tiles
- **Label**: preset code below tile, UPPERCASE 11pt w600 `+1.0` `#B3B3B3`
- **Active state**: 2pt solid `#FFFFFF` ring around the tile + label → `#FFFFFF`. NEVER a colored highlight.
- **Strength sub-slider**: tapping an already-active preset reveals a hairline slider (same spec as Adjust) to dial preset intensity 0–12
- **Scroll**: free, momentum, no snapping; ~80pt left/right peek

### Tool Tray

The mode switcher under the stage.
- **Row**: horizontal scroll, 84pt tall, `border-top` 0.5pt `#2A2A2A`
- **Tool**: 22pt stroke glyph + UPPERCASE 12pt `+1.6` label, stacked, 8pt gap
- **Default**: `#B3B3B3`; active: `#FFFFFF` (glyph + label both)
- **Spacing**: 30pt between tools
- Selecting a tool replaces the preset carousel with that tool's slider (or sub-UI: Crop grid, HSL wheel)

### Navigation

**Editor Top Bar**
- Height: 52pt + safe area
- Leading: `×` close glyph, 22pt, weight 300, `#FFFFFF`
- Center: screen title "EDIT" — UPPERCASE 13pt w500 `+1.4` `#B3B3B3`
- Trailing: "NEXT" action — UPPERCASE 13pt w600 `+1.4` `#FFFFFF`
- Background: `#000000`, no border

**Bottom Tab Bar** (Studio / Feed / Discover / Profile)
- Height: 48pt + safe area
- Background: `#000000`, `border-top` 0.5pt `#2A2A2A`
- 4 tabs: Studio (grid), Feed (up-arrow / for-you), Discover (camera/explore), Profile (avatar)
- Icon: 23pt stroke; active = filled `#FFFFFF`, inactive = stroke `#767676`
- Labels usually hidden (icon-only); if shown, UPPERCASE 10pt w600 `+0.6`
- No tint pill — active is white fill only

**Studio Top Bar**
- Height: 44pt + safe area
- Leading: title "Studio" Inter 24pt w600 sentence-case `#FFFFFF`
- Trailing: select-mode, import, settings — 22pt stroke icons `#FFFFFF`

### Input Fields

**Search (Discover / People)**
- Height: 40pt
- Background: `#1A1A1A`
- Border: none
- Corner radius: 2pt
- Leading icon: magnifier 16pt `#767676`
- Placeholder: "Search" Inter 14pt w400 `#767676`
- Text: `#FFFFFF`
- Focus: 1pt `#FFFFFF` bottom hairline (no full border, no glow)

**Caption / Bio Text Area**
- Background: transparent on `#000000`
- Text: Inter 16pt w400 `#FFFFFF`
- Placeholder: `#767676`
- Character count: bottom-right, Inter 12pt `#767676`
- No box, no border — bare text on black

### Distinctive Components

**Hairline Bipolar Slider**
- See Adjust Slider above — the defining VSCO control. Used for every numeric adjustment and for preset strength.

**Film-Preset Tile**
- A live-rendered photo preview, 56pt², 2pt radius, 2px white ring when active. The carousel of these IS VSCO's identity.

**Recipe Pill**
- A saved set of adjustments. Shown as a 2pt-radius `#1A1A1A` tile with a tiny preview swatch + recipe number. Tap to apply all, long-press to manage.

**HSL Color Wheel**
- A circular hue selector with 8 color stops; selecting a hue reveals 3 hairline sliders (Hue / Saturation / Lightness). Wheel is the only "round" component; the stops are the only saturated UI color, and only because they ARE color-pickers.

**Crop / Straighten Grid**
- A rule-of-thirds overlay on the photo: 0.5pt `rgba(255,255,255,0.4)` grid lines, draggable corner handles (white 1pt squares), a hairline rotation dial below with degree value in white.

**Before/After Press**
- Press-and-hold the photo to reveal the original (un-graded) image; release returns to edited. No UI — just the image swapping, optional "ORIGINAL" caption top-left UPPERCASE 11pt `+1.0`.

**Studio Grid**
- A gapless (1px hairline) grid of square photo thumbnails on `#0E0E0E`. No rounding, no shadow, no labels — pure contact-sheet. Multi-select adds a 2px white inset border + white check.

**Montage / Collage Canvas**
- A black artboard where photo/video/shape layers compose; layer handles are 1pt white squares; the toolbar is the same UPPERCASE tracked label row.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 28, 32, 40, 48
- Photo stage margin: 20pt horizontal, generous black breathing room
- Tool tray inter-item: 30pt
- Preset tile gap: 14pt
- Slider label → track: 10–12pt

### Grid & Container
- iPhone: editor stage is full-width minus 20pt; photo letterboxed to fit, centered
- Studio grid: 3-up (small), 4-up (large phones), gapless 1px hairline gutters
- Discover feed: single-column full-bleed editorial cards or 2-up image grid
- iPad: editor stage centered max ~900pt; tray + carousel pinned bottom; Studio grid up to 6-up
- Black margin around the photo is itself a layout element — never crop the breathing room away

### Whitespace Philosophy
- **The photo is sacred**: maximum black margin, photo always fully visible, never clipped by chrome
- **Chrome is a thin band**: controls live in a ≤140pt bottom band; the rest is black + image
- **No containers**: zero cards, panels, or grouped boxes on the editing surface — elements float on black
- **Contact-sheet density**: the Studio grid is intentionally gapless and dense — it reads like film negatives on a light table

### Border Radius Scale
- Square (0pt): Studio grid thumbnails, crop grid, full-bleed feed images
- Film frame (2pt): Preset tiles, buttons, recipe pills, search field — the default radius
- Subtle (4pt): Sheets, contextual menus
- Comfortable (8pt): Bottom sheets, modals
- Soft (12pt): Onboarding cards (rare)
- Pill (500pt): Almost never — only the membership/upsell "Try VSCO" CTA
- Circle (50%): Avatars, slider knob, HSL wheel

## 6. Depth & Elevation

VSCO is flatter than flat — it is *negative* depth, everything sits directly on black.

| Level | Treatment | Use |
|-------|-----------|-----|
| Stage (Level 0) | No shadow, no border | Photo, sliders, carousel, tool tray — everything on `#000000` |
| Knob (Level 0.5) | `rgba(0,0,0,0.6) 0 1px 4px` | The ONLY content shadow — under the white slider knob so it reads on a bright photo |
| Sheet (Level 1) | Slide-up `#1A1A1A` panel, no shadow, 4pt top radius | Export options, recipe manager, settings |
| Menu (Level 2) | `#1A1A1A`, 4pt radius, `rgba(0,0,0,0.5) 0 8px 24px` | Context menus, share sheet |
| Modal Scrim | `rgba(0,0,0,0.6)` | Behind sheets/modals |

**Shadow Philosophy**: VSCO uses essentially no shadow. The single exception is the slider knob, which gets a soft `0 1px 4px` so the white circle stays visible when it travels over a bright area of the photograph. Sheets and menus rely on a lighter `#1A1A1A` surface against pure black for separation, not on elevation shadow. On the (rare) light browsing theme, the same logic inverts — hairline `#DCDCDC` dividers do the separating, not shadows.

### Motion
- **Preset apply (cross-fade)**: photo cross-dissolves from current grade to new grade over 250ms ease-in-out; preset ring snaps instantly
- **Slider drag**: 1:1 finger tracking at 60fps; value text updates live; zero-detent gives a soft haptic tick as the knob crosses neutral
- **Slider double-tap reset**: value + knob spring back to default over 200ms ease-out + soft haptic
- **Tool switch**: carousel slides out / slider fades in over 180ms ease-out (horizontal 8pt offset)
- **Before/after press**: instant swap on press-down, instant return on release — no transition (the point is immediacy)
- **Tab switch**: cross-fade 200ms, no slide
- **Sheet present**: slide up 300ms ease-out, scrim fades in parallel
- **Studio multi-select**: white inset border + check scale-in 150ms ease-out per tile
- **Grain/film toggle**: applies live with a 200ms ease as the texture composites in
- **Haptics**: soft impact on zero-detent crossing, preset selection, double-tap reset, multi-select toggle
- **Reduce Motion**: cross-fades become instant cuts; slider reset jumps; tool switch is a plain crossfade

## 7. Do's and Don'ts

### Do
- Use pure black (`#000000`) for the editor — true black, dark-native, no light editing theme
- Keep the UI monochrome — white + three grays; the photograph is the only color
- Make sliders 1px hairlines on `#3A3A3A` with a solid white 18pt knob
- Communicate selection with a 2px white ring or a fill change — never a tint
- Set tool labels and preset codes in UPPERCASE Inter with wide tracking (`+1.6` / `+1.0`)
- Give the photo maximum black breathing room — never clip it with chrome
- Use 2pt corner radius as the default (film-frame square), 0pt for the Studio grid
- Render preset tiles as live previews of the actual photo under that grade
- Keep the live value text white and the label gray until active
- Make the Studio grid gapless and dense — a contact sheet of negatives
- Use the slider knob's `0 1px 4px` shadow — the only content shadow in the app

### Don't
- Don't introduce a brand accent color — VSCO has no "Spotify Green"; state is white
- Don't use a charcoal "dark mode" (`#191919` etc.) for the editor — it is true `#000000`
- Don't use rounded pills for buttons — buttons are 2pt-radius rectangles
- Don't add cards, panels, borders, or shadows to the editing surface — everything floats on black
- Don't tint sliders, knobs, or rings — they are white, always
- Don't sentence-case tool labels or preset codes — they are UPPERCASE wide-tracked
- Don't put gaps or rounding in the Studio grid — it is a gapless 1px-gutter contact sheet
- Don't animate aggressively — motion is a quiet 180–300ms cross-fade vocabulary
- Don't show a colored "active" highlight on presets — it's a 2px white ring only
- Don't crowd the photo — the black margin is a designed element, protect it
- Don't use color anywhere in chrome except the destructive red (`#E5484D`) in delete sheets

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Studio grid 3-up; tool tray scrolls; photo letterboxed tighter |
| iPhone 13/14/15 | 390pt | Standard layout; 4-up Studio grid |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the editor top bar |
| iPhone 15/16 Pro Max | 430pt | Larger photo stage; 4-up grid with slightly bigger tiles |
| iPad (portrait) | 768pt | Photo stage centered max ~760pt; Studio grid 5-up; tray pinned bottom |
| iPad (landscape) | 1024pt+ | Photo centered max ~900pt; tray + carousel as a bottom rail; Studio 6-up |

### Dynamic Type
- Editorial titles, section headers, body, captions: full scale
- Tool labels, preset codes, slider values, nav actions, tab labels: FIXED (single-line, layout-critical)
- Slider value never wraps — it is a fixed-width numeric

### Orientation
- Editor supports rotation; photo re-letterboxes to fit, tray + carousel stay bottom-pinned
- Studio grid reflows column count by width
- Crop/straighten tool fully orientation-aware

### Touch Targets
- Slider knob: 18pt glyph, 44pt hit
- Tool-tray item: 22pt glyph, 44pt hit
- Preset tile: 56pt (tap target is the full tile)
- Tab bar icon: 23pt glyph, 44pt hit
- Top-bar action: 13pt text, 44pt hit
- Studio thumbnail: full tile is the target; multi-select via long-press then tap

### Safe Area Handling
- Top: editor top bar respects safe area + Dynamic Island
- Bottom: tool tray + tab bar respect the home indicator
- The photo stage shrinks to fit between safe areas — it is never occluded
- Sides: 20pt photo margin honors the safe area on notched devices

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (editor): `#000000` (true black — no light editing theme)
- Surface 1 (Studio/Feed bg): `#0E0E0E`
- Surface 2 (sheets/menus): `#1A1A1A`
- Surface 3 (pressed/chips): `#242424`
- Divider: `#2A2A2A`
- Slider track: `#3A3A3A`
- Primary / interactive: `#FFFFFF`
- Primary pressed: `#DBDBDB`
- Text primary: `#FFFFFF` (dark) / `#000000` (light browse)
- Text secondary: `#B3B3B3` (dark) / `#767676` (light)
- Text tertiary: `#767676`
- Destructive (delete sheets only): `#E5484D`
- Film tint (warm preset preview, content): `#C8A86B`
- Film tint (cool preset preview, content): `#7E8FA1`
- Accent color: NONE — selection is white

### Example Component Prompts
- "Build the VSCO photo editor screen in SwiftUI: pure black (`#000000`) background. Top bar 52pt: leading `×` (SF Symbol `xmark`, 22pt, `#FFFFFF`), center title 'EDIT' (Inter 13pt weight 500, UPPERCASE, `+1.4` tracking, `#B3B3B3`), trailing 'NEXT' (Inter 13pt weight 600, UPPERCASE, `+1.4`, `#FFFFFF`). Center: the photo, aspect-fit, full-width minus 20pt, 2pt corner radius, centered with generous black margin. Below: a film-preset carousel of 56pt square tiles (live preview of the photo under each grade), 14pt gap, active tile has a 2pt `#FFFFFF` ring and its UPPERCASE 11pt `+1.0` code label turns `#FFFFFF`. Bottom: an 84pt tool tray with `border-top` 0.5pt `#2A2A2A`, UPPERCASE 12pt `+1.6` `#B3B3B3` tool labels with 22pt stroke glyphs, active = `#FFFFFF`."

- "Create the VSCO adjust hairline slider: a row with the tool name on the left (Inter 12pt weight 600, UPPERCASE, `+1.6` tracking, `#B3B3B3`) and the live value on the right (Inter 14pt weight 600, `#FFFFFF`, e.g. `+2.4`). Below, a 1pt track in `#3A3A3A` full-width; a 1pt `#FFFFFF` fill from the center zero-point to the knob; an 18pt solid `#FFFFFF` circular knob with shadow `rgba(0,0,0,0.6) 0 1px 4px` and a 44pt hit area. 1:1 finger tracking, a soft haptic when the knob crosses the neutral value, double-tap resets to default over 200ms ease-out."

- "Render the VSCO film-preset tile: 56pt square, 2pt corner radius, fill = the actual photo rendered under that preset's grade. Below it, the preset code in Inter 11pt weight 600 UPPERCASE `+1.0` tracking `#B3B3B3`. Selected state: a 2pt solid `#FFFFFF` ring around the tile and the label turns `#FFFFFF` — never a colored highlight. Tapping an already-selected tile reveals a strength hairline slider (same spec as the adjust slider) below the carousel."

- "Build the VSCO Studio grid: a gapless grid of square photo thumbnails (4 columns on a 390pt phone) with 1px hairline gutters in `#2A2A2A` on a `#0E0E0E` background. No corner radius, no shadow, no labels — a contact sheet. Multi-select: long-press a tile to enter select mode, then tap to add a 2px `#FFFFFF` inset border + a small `#FFFFFF` checkmark; selected count shows in the top bar."

- "Create the VSCO tool tray: a horizontally-scrolling 84pt row, `border-top` 0.5pt `#2A2A2A`, on `#000000`. Each tool is a 22pt stroke glyph (stroke-width 1.6) above an UPPERCASE Inter 12pt weight 600 `+1.6` tracking label, stacked with 8pt gap, 30pt between tools. Inactive `#B3B3B3`, active `#FFFFFF` (glyph + label). Selecting a tool swaps the preset carousel for that tool's hairline slider."

- "Build the VSCO primary action button: a 2pt-radius rectangle (NOT a pill), `#FFFFFF` background, `#000000` text, Inter 12pt weight 600 UPPERCASE with `+1.4` letter-spacing, 14pt vertical / 28pt horizontal padding. Pressed: background `#DBDBDB` + scale 0.98. Frequently it is rendered instead as a bare top-bar text action ('NEXT') with the same type spec and no fill."

### Iteration Guide
1. Editor canvas is true black `#000000` — dark-native, there is NO light editing theme; only browsing screens can go light
2. UI is monochrome: white + `#0E0E0E`/`#1A1A1A`/`#242424` surfaces + `#B3B3B3`/`#767676` text — zero brand accent
3. Sliders are 1px hairlines on `#3A3A3A` with a solid white 18pt knob (the knob's `0 1px 4px` shadow is the only content shadow)
4. Selection is communicated with a 2px white ring or a fill/label color change — NEVER a tint
5. Tool labels and preset codes are UPPERCASE Inter, wide-tracked (`+1.6` tools, `+1.0` presets) — the film-data-back signature
6. The film-preset carousel is the identity: 56pt live-preview tiles, 2px white active ring
7. Default corner radius is 2pt (film-frame square); the Studio grid is 0pt and gapless (contact sheet)
8. The photograph is sacred — maximum black margin, never clipped by chrome, never crowded
9. No cards, panels, borders, or shadows on the editing surface — every control floats on black
10. The only non-monochrome UI color is the destructive red `#E5484D`, used solely in delete/discard sheets
