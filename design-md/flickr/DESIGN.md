# Design System Inspiration of Flickr (iOS)

## 1. Visual Theme & Atmosphere

Flickr's iOS app is a photography-first design system where the *image* is the entire interface and chrome is engineered to disappear. Open the app and you land in the **photostream** — a justified mosaic where photos are scaled to fill the screen edge-to-edge in rows of varying height, separated only by hairline 3pt gutters. There are no cards, no rounded corners on the photos, no drop shadows competing for attention. The canvas is near-black (`#0C0D0E` in dark, `#FFFFFF` in light) precisely so that full-color photography reads first and the UI recedes into negative space. This is the opposite of a typical content-card app: Flickr treats every screen as a gallery wall.

The two signature UI moments are the **justified grid** and the **photo detail page**. The justified grid is Flickr's heritage layout — a row-packing algorithm that preserves each photo's native aspect ratio while making every row the same height and full-bleed width, so a landscape, a square, and a portrait sit side-by-side with no cropping and no letterboxing. Tap any photo and you drop into the photo detail page: the image at full width, then the title in Proza Libre, the author row with avatar, the action bar (Fave / Comment / Share / Add to album), and — Flickr's truly distinctive surface — the **EXIF / camera detail table**: a clean key-value list of Camera, Lens, Exposure, Aperture, ISO, Focal length, and Date taken. No other mainstream photo app surfaces camera metadata this prominently; it is the core of Flickr's photographer-centric identity.

The brand is anchored by **two dots**: Flickr Pink (`#FF0084`) and Flickr Blue (`#0063DC`), drawn as overlapping circles in the logomark. Pink is the favorite/like color and the primary action color (Upload, Follow); Blue is the link and secondary-action color (Add to album, view links, hyperlinked tags). These two saturated hues are the *only* chromatic accents in an otherwise grayscale system — everything else is photography. A Pro gold star (`#FFB200`) appears only on Pro-account badges. The favorite "star" is Flickr's like primitive (not a heart) — a five-point star that fills Flickr Pink when active.

Typography pairs an editorial serif with a UI sans. **Proza Libre** (Google Fonts, an open serif designed for screen body text) carries titles, photo titles, and section headers — giving Flickr its "this is curated photography, not a social feed" magazine voice. **Inter** (or SF Pro fallback) handles all functional UI: tab labels, metadata, buttons, EXIF tables. EXIF values are set in a monospace face (`SF Mono` / `IBM Plex Mono`) so `f/2.8 · 1/250s · ISO 100 · 35mm` aligns like a camera readout. Hierarchy is gallery-like: a 32pt screen title, 18pt photo titles, 13pt photographer metadata, 11pt monospace EXIF.

**Key Characteristics:**
- Near-black gallery canvas (`#0C0D0E` dark / `#FFFFFF` light) — photography reads first, chrome recedes
- Justified photo mosaic — row-packed full-bleed grid preserving native aspect ratios, 3pt gutters
- Photo detail page — full-width image + title + author row + action bar + EXIF table
- EXIF / camera detail table — Camera / Lens / Exposure / Aperture / ISO / Focal length / Date taken key-value rows
- Twin brand dots — Flickr Pink (`#FF0084`) for favorites & primary, Flickr Blue (`#0063DC`) for links & secondary
- Five-point favorite star (not a heart) — fills `#FF0084` when active, with fave count overlay
- Proza Libre serif (titles, headers) + Inter sans (UI) + monospace EXIF readouts
- Pro gold star (`#FFB200`) — appears only on Pro-account badges
- Photos never dim across themes — only chrome, dividers, and EXIF tables invert
- Camera & lens metadata as tappable chips — link to "more from this camera/lens"
- Albums & galleries as curated collections — cover photo + count, magazine-styled
- Minimal navigation chrome — bottom tab strip (Feed / Search / Camera / Notify / You), center camera FAB-style tab

## 2. Color Palette & Roles

### Primary (Interactive)
- **Flickr Pink** (`#FF0084`): Primary action color — Upload button, Follow button, active favorite star, primary CTAs.
- **Flickr Pink Pressed** (`#D60070`): Pressed state for pink controls.
- **Flickr Blue** (`#0063DC`): Secondary/link color — Add to album, hyperlinked tags, "View all" text links, in-text links.
- **Flickr Blue Pressed** (`#0052B8`): Pressed state for blue controls.

### Canvas & Surfaces (Dark Mode — default for gallery viewing)
- **Gallery Canvas** (`#0C0D0E`): Primary dark canvas — intentionally near-black so photos dominate.
- **Surface 1** (`#1A1B1D`): Cards, sheets, EXIF table container, settings rows.
- **Surface 2** (`#232427`): Chips, raised controls, search field background.
- **Divider** (`#2A2C30`): Hairline dividers between EXIF rows, list rows, sections.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F5F6F7`): Cards, EXIF container, grouped settings.
- **Surface Pressed** (`#EAECEF`): Pressed/selected rows.
- **Divider Light** (`#E2E4E8`): Hairline dividers.

### Text
- **Text Primary Dark** (`#FFFFFF`): Primary text on dark — photo titles, headers.
- **Text Secondary Dark** (`#B0B3B8`): Metadata, photographer names, captions.
- **Text Tertiary Dark** (`#6E7176`): EXIF labels, timestamps, disabled.
- **Text Primary Light** (`#1C1F23`): Primary text on light — near-black, not pure black.
- **Text Secondary Light** (`#6B7077`): Metadata on light.

### Accent & Semantic
- **Pro Gold Star** (`#FFB200`): Pro-account badge star — used *only* for Pro badging.
- **Success Green** (`#00C781`): Upload-complete toast, save confirmation.
- **Error Red** (`#FF3B5C`): Upload-failed, destructive confirmation.
- **Favorite Star Active** (`#FF0084`): The fave star fill (= Flickr Pink).
- **Photo Overlay Scrim** (`rgba(0,0,0,0.45)`): Bottom-up gradient behind fave-count text on grid tiles.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0C0D0E` |
| Surface 1 | `#F5F6F7` | `#1A1B1D` |
| Surface 2 | `#EAECEF` | `#232427` |
| Divider | `#E2E4E8` | `#2A2C30` |
| Text Primary | `#1C1F23` | `#FFFFFF` |
| Text Secondary | `#6B7077` | `#B0B3B8` |
| Flickr Pink | `#FF0084` | `#FF0084` |
| Flickr Blue | `#0063DC` | `#0063DC` |
| Pro Gold | `#FFB200` | `#FFB200` |

## 3. Typography Rules

### Font Family
- **Editorial Serif**: `Proza Libre` (by Jasper de Waard / Bureau Roffa, Google Fonts, SIL OFL) — a sturdy screen-optimized serif used for titles, photo titles, and section headers; gives Flickr its curated-photography editorial voice.
- **UI Sans**: `Inter` (by Rasmus Andersson, SIL OFL) — all functional UI: tab labels, metadata, buttons, settings, EXIF labels.
- **Monospace (EXIF readout)**: `IBM Plex Mono` / system `SF Mono` — EXIF values so `f/2.8 · 1/250s · ISO 100` aligns like a camera display.
- **Fallback Stack (serif)**: `'Proza Libre', Georgia, 'Times New Roman', serif`
- **Fallback Stack (sans)**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Proza Libre | 32pt | 700 | 1.2 | -0.4pt | "Explore", large nav title |
| Section Header | Proza Libre | 26pt | 700 | 1.25 | -0.3pt | "Recent activity", album section |
| Nav Title | Proza Libre | 22pt | 600 | 1.3 | -0.3pt | Compact nav bar title ("Photostream") |
| Photo Title | Proza Libre | 18pt | 600 | 1.3 | -0.2pt | Title on photo detail page |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Photo description, comment text |
| Card Title | Inter | 15pt | 600 | 1.35 | 0pt | Album / gallery card title |
| Button | Inter | 15pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | "by Sofia Marent · 312 faves · 2d" |
| Fave Count (overlay) | Inter | 11pt | 600 | 1.0 | 0pt | Grid tile fave count, on scrim |
| EXIF Label | Inter | 12pt | 400 | 1.4 | 0pt | EXIF row key ("Aperture") |
| EXIF Value | IBM Plex Mono | 11pt | 600 | 1.4 | 0.3pt | EXIF row value ("f/2.8") |
| Tag Chip | Inter | 12pt | 600 | 1.0 | 0pt | Camera / lens / keyword chips |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 400 | 1.4 | 0pt | Timestamps, small print |

### Principles
- **Serif for curation, sans for function**: Proza Libre signals "this is photography worth reading about"; Inter handles every interactive/utility surface. Never set EXIF or tab labels in the serif.
- **EXIF is monospace**: camera readouts (`f/2.8`, `1/250s`, `ISO 100`, `35mm`) are always monospace so columns and decimals align like a camera display.
- **Photo titles breathe**: Proza Libre 18pt w600 with generous 16pt top margin above the author row — the title is the only large type on a photo page.
- **Metadata is quiet**: photographer names, fave counts, timestamps stay at 13pt secondary — they support the photo, never compete.
- **Dynamic Type first-class**: titles, body, metadata scale; EXIF values, tab labels, fave-count overlays stay fixed (layout-sensitive in the justified grid).

## 4. Component Stylings

### Buttons

**Primary Button (Upload / Follow)**
- Shape: Rounded rectangle, 6pt corner radius (Follow chip is pill 500pt)
- Background: `#FF0084` (Flickr Pink)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#D60070` + scale 0.98
- Disabled: `#FF0084` at 40% opacity

**Secondary Button (Add to Album / Share to)**
- Background: `#0063DC` (Flickr Blue)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal
- Corner radius: 6pt
- Pressed: background `#0052B8`

**Outline Button (Edit / Cancel)**
- Background: transparent
- Text: Text Primary (`#FFFFFF` dark / `#1C1F23` light)
- Border: 1pt Text Secondary
- Corner radius: 6pt
- Padding: 11pt vertical, 20pt horizontal
- Font: Inter 14pt weight 600

**Follow Pill**
- Background: `#FF0084`, fully rounded (500pt)
- Text: `#FFFFFF`, Inter 13pt weight 700
- Padding: 8pt vertical, 18pt horizontal
- Following state: outline style — transparent bg, 1pt `#B0B3B8` border, text `#B0B3B8`

**Text Link Button**
- Font: Inter 14pt weight 600
- Color: `#0063DC` (Flickr Blue)
- No underline; pressed: 70% opacity

### Core Atoms

**Favorite Star (the like primitive)**
- A five-point star, NOT a heart
- Inactive: outline only, `#FFFFFF` 1.8pt stroke at 90% (on photo) / `#B0B3B8` (on light surfaces)
- Active: filled `#FF0084` (Flickr Pink), no stroke
- Tap animation: scale 1.0 → 1.25 → 1.0 over 280ms spring + soft haptic; brief Flickr Pink particle burst
- Adjacent fave count: Inter 11pt w600

**Grid Tile (justified mosaic cell)**
- No corner radius (full-bleed), no border, no shadow
- Photo fills cell; 3pt gutter between cells and rows
- Bottom scrim: `linear-gradient(transparent, rgba(0,0,0,0.45))` lower 40% — only present if fave count shown
- Fave count overlay: bottom-left, white star + count, 11pt w600, text-shadow `0 1px 3px rgba(0,0,0,0.7)`
- Long-press: lifts a quick-action peek (Fave / Add to album / Share)

**Avatar**
- Circle, 30pt on photo detail (24pt in comments, 40pt on profile)
- Pro accounts: 2pt `#FFB200` ring
- Fallback: linear-gradient(135deg, `#FF0084`, `#0063DC`) with initials

**EXIF Row**
- Full-width key-value row, 0.5pt `#2A2C30` bottom divider
- Key: Inter 12pt w400 `#B0B3B8`, left-aligned
- Value: IBM Plex Mono 11pt w600 `#FFFFFF`, right-aligned
- Row height: ~32pt (7pt vertical padding)
- Container: Surface 1, 12pt radius, 16pt inset

**Camera / Lens / Tag Chip**
- Background: Surface 2 (`#232427`), 1pt `#2A2C30` border
- Pill shape (500pt radius)
- Text: Inter 12pt w600 `#FFFFFF`; EXIF-value chips use IBM Plex Mono 11pt
- Padding: 7pt vertical, 14pt horizontal
- Tappable -> opens "more shot with this camera/lens" search

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(12,13,14,0.94)` with `saturate(180%) blur(20px)` backdrop, 0.5pt top border `#2A2C30`
- Tabs (5): Feed, Search, **Camera** (center, emphasized), Notify, You
- Icon size: 23pt (Camera 27pt, drawn in `#FF0084` outline as a quasi-FAB)
- Active: icon fills `#FFFFFF` (Camera always pink); Inactive: `#6E7176`
- Labels: Inter 10pt w500, shown always (Camera has no label)

**Top Nav (Photostream / Detail)**
- Height: 44pt + safe area
- Leading: large title that collapses to 22pt Proza Libre nav title on scroll
- Trailing: search glyph + camera glyph (21pt, `#FFFFFF` stroke)
- Photo detail: leading back chevron, trailing ... overflow (Report, Download, Copy link)

### Input Fields

**Search Bar**
- Height: 40pt
- Background: Surface 2 (`#232427`) / `#EAECEF` light
- Corner radius: 10pt
- Leading icon: magnifyingglass 16pt `#6E7176`
- Placeholder: "Search photos, people, groups" Inter 14pt `#6E7176`
- Focus: 1pt `#0063DC` border (Flickr Blue)

**Comment Composer**
- Height: 44pt min, grows to 5 lines
- Background: Surface 1, 22pt radius (capsule when single line)
- Trailing "Post" text button in `#0063DC` (disabled `#6E7176` when empty)

### Distinctive Components

**Justified Photo Mosaic**
- The signature layout. Row-packing: given a target row height (~110-130pt on phone) and screen width, scale each photo by its aspect ratio so the row exactly fills width; clamp final row.
- Gutter: 3pt between tiles and between rows
- No cropping — native aspect ratios preserved
- Infinite scroll; new rows packed as you scroll

**Photo Detail Page**
- Full-width image (native aspect, max 70% viewport height before scroll)
- Below: Proza Libre 18pt title -> author row (avatar + name + Follow pill) -> action bar (Fave / Comment / Share / Add) -> description body -> **EXIF table** -> tags chip-row -> comments
- Action bar icons: 18pt, label below, Fave active = `#FF0084` filled

**EXIF / Camera Detail Table**
- Card (Surface 1, 12pt radius) titled "CAMERA DETAILS" (10pt w700 tracked `#6E7176`)
- Rows: Camera, Lens, Exposure, Aperture, Focal length, ISO, Flash, Date taken
- Key Inter 12pt w400 `#B0B3B8` <-> Value IBM Plex Mono 11pt w600 `#FFFFFF`
- Tapping Camera/Lens rows -> search "shot with this gear"

**Album / Gallery Card**
- Cover photo (4:3) with no radius, then title (Inter 15pt w600) + "248 photos" count (13pt secondary)
- Galleries (curated collections) get a small `#0063DC` curator byline

**Pro Badge**
- Small `#FFB200` star + "PRO" wordmark, Inter 10pt w700 — appears next to Pro photographers' names only

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 3 (grid gutter only), 4, 8, 12, 16, 20, 24, 32, 40, 48
- Photo grid gutter: **3pt** (the signature tight mosaic spacing)
- Content inset (text surfaces): 16pt horizontal
- EXIF row vertical padding: 7pt

### Grid & Container
- Photostream: full-bleed justified mosaic, target row height 110-130pt (phone), 160-200pt (tablet)
- Text content (titles, EXIF, comments): 16pt horizontal insets
- Photo detail image: full-bleed width, native aspect
- iPad: justified mosaic with larger target row height; photo detail in centered 760pt column with image full-width-of-column

### Whitespace Philosophy
- **Photos own the space**: negative space is the 3pt gutter — minimal, so the wall of photography is continuous
- **Text surfaces breathe**: once you leave the grid (photo detail, EXIF, comments) spacing opens to 16pt insets and comfortable line height
- **Chrome is invisible**: nav and tab bars are translucent blurred; nothing frames the photos

### Border Radius Scale
- Square (0pt): photo grid tiles, full-bleed detail image, dividers
- Subtle (2pt): inline thumbnails
- Standard (4pt): small chips, badges
- Comfortable (6pt): buttons, primary controls
- Card (12pt): EXIF table card, album metadata sheets
- Sheet (16pt): bottom sheets, modals
- Pill (500pt): Follow pill, tag chips, search-result filter pills
- Circle (50%): avatars, fave-burst particles

## 6. Depth & Elevation

Flickr is photographically flat — shadows never compete with images. Elevation is used only for transient UI.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Grid tiles, photo detail image, EXIF rows, list rows |
| Raised (Level 1) | `rgba(0,0,0,0.3) 0 2px 8px` | Floating compose / camera buttons, chips on photos |
| Sheet (Level 2) | `rgba(0,0,0,0.4) 0 -4px 24px` | Bottom sheets (Add to album, Share, photo actions) |
| Modal Overlay | `rgba(0,0,0,0.55)` scrim | Behind modals, lightbox dim, full-screen photo viewer |

**Shadow Philosophy**: On the gallery wall, shadows would muddy the photography, so grid tiles and the detail image are perfectly flat. The only shadows are on controls that float *over* photos (chips, the camera FAB) using a soft dark `0 2px 8px`. In the full-screen lightbox the surrounding chrome dims to `rgba(0,0,0,0.55)` so the single photo is the only lit element. Photo scrims (the bottom-up `rgba(0,0,0,0.45)` gradient) are not elevation — they exist purely for fave-count legibility.

### Motion
- **Fave star tap**: scale 1.0 → 1.25 → 1.0 over 280ms spring (damping 0.6) + soft haptic + brief `#FF0084` particle burst
- **Grid → detail (shared element)**: tapped tile expands into the full-width detail image over 320ms ease-out (matched geometry transition); back collapses in 280ms
- **Photostream scroll**: infinite; new justified rows fade-in over 200ms as they pack
- **Lightbox open**: photo zooms from grid position to full-screen over 300ms ease-out, chrome dims to `rgba(0,0,0,0.55)`; pinch-to-zoom 1:1 finger tracking; swipe-down to dismiss with rubber-band
- **Tab change**: instant content swap, no cross-fade (gallery feel)
- **Photo detail scroll**: image parallaxes slightly (0.5x scroll) while EXIF/comments scroll at 1x
- **Upload progress**: determinate ring in `#FF0084`; success → green `#00C781` checkmark pop (200ms)
- **Pull to refresh**: Flickr Pink + Blue dual-dot spinner (the logomark animating)
- **Haptic feedback**: soft impact on fave, medium on add-to-album confirm, light tick on tab change

## 7. Do's and Don'ts

### Do
- Use a near-black canvas (`#0C0D0E` dark / `#FFFFFF` light) so photography reads first
- Build the photostream as a justified mosaic — row-packed, native aspect ratios, 3pt gutters
- Surface the EXIF / camera detail table prominently on photo detail — it's Flickr's identity
- Use the five-point star (not a heart) as the favorite primitive — fills `#FF0084`
- Use Flickr Pink (`#FF0084`) for favorites + primary actions, Flickr Blue (`#0063DC`) for links + secondary
- Set EXIF values in monospace so `f/2.8 · 1/250s · ISO 100` aligns like a camera readout
- Use Proza Libre (serif) for titles/headers, Inter (sans) for all functional UI
- Keep photo grid tiles square-cornered, borderless, shadowless — the wall is continuous
- Make camera/lens metadata tappable chips that link to "more shot with this gear"
- Reserve the gold star (`#FFB200`) for Pro badges only
- Keep nav/tab chrome translucent and blurred so it floats invisibly over photos

### Don't
- Don't crop photos in the grid — the justified algorithm preserves native aspect ratios
- Don't round the corners of grid tiles or the detail image — full-bleed, square
- Don't use a heart for favorites — Flickr's like is a five-point star
- Don't add drop shadows to photos — shadows muddy the gallery wall
- Don't use the serif (Proza Libre) for EXIF, tab labels, or buttons — that's Inter's job
- Don't set EXIF values in a proportional font — they must be monospace and column-aligned
- Don't lighten the dark canvas toward gray — near-black `#0C0D0E` is intentional
- Don't introduce accent colors beyond Pink/Blue (and the Pro gold) — everything else is photography
- Don't dim photos in dark mode — only chrome and EXIF tables recede
- Don't widen the grid gutter beyond 3pt — the tight mosaic is the signature
- Don't bury camera metadata in a secondary screen — the EXIF table is core, not optional

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Justified grid target row ~105pt; EXIF table single column |
| iPhone 13/14/15 | 390pt | Standard layout, target row ~115pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in translucent top nav |
| iPhone 15/16 Pro Max | 430pt | Target row ~130pt; larger photo titles |
| iPad (portrait) | 768pt | Target row ~170pt; photo detail in centered 760pt column |
| iPad (landscape) | 1024pt+ | Target row ~200pt; 2-pane (grid + detail) split view |

### Dynamic Type
- Photo titles, body, comments, metadata: full scale
- EXIF values, fave-count overlays, tab labels: FIXED (layout-sensitive in the justified grid and on photo scrims)
- Camera/lens chips: scale to L then truncate

### Orientation
- Photostream re-packs the justified grid on rotation (recomputes row heights for new width)
- Photo detail rotates; landscape uses full-screen lightbox by default
- iPad landscape: persistent two-pane (mosaic left, detail right)

### Touch Targets
- Grid tile: full tile tappable (min effective 44pt via row height)
- Fave star: 24pt glyph, 44pt hit area
- Tab bar icons: 23pt glyph (Camera 27pt), 48pt hit
- EXIF row: full-row tappable for Camera/Lens rows, 44pt min
- Buttons: 44pt tall minimum

### Safe Area Handling
- Top: translucent nav respects safe area + Dynamic Island
- Bottom: translucent tab bar respects home indicator; lightbox extends edge-to-edge under safe areas
- Sides: photo grid is full-bleed (ignores side insets); text content keeps 16pt insets
- Keyboard: comment composer docks above keyboard; photo detail scrolls clear

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0C0D0E`
- Canvas (light): `#FFFFFF`
- Surface 1: `#1A1B1D` dark / `#F5F6F7` light
- Surface 2: `#232427` dark / `#EAECEF` light
- Divider: `#2A2C30` dark / `#E2E4E8` light
- Text primary: `#FFFFFF` dark / `#1C1F23` light
- Text secondary: `#B0B3B8` dark / `#6B7077` light
- Flickr Pink (favorite / primary): `#FF0084` (pressed `#D60070`)
- Flickr Blue (link / secondary): `#0063DC` (pressed `#0052B8`)
- Pro gold star: `#FFB200`
- Success: `#00C781` · Error: `#FF3B5C`
- Photo scrim: `rgba(0,0,0,0.45)`

### Example Component Prompts
- "Build the Flickr justified photo mosaic in SwiftUI: a row-packing layout where each photo keeps its native aspect ratio and rows are scaled to fill screen width at a target height of ~115pt. 3pt gutter between tiles and rows. No corner radius, no border, no shadow. Each tile that has favorites shows a bottom-left overlay: a white five-point star + count in Inter 11pt w600 over a `linear-gradient(transparent, rgba(0,0,0,0.45))` bottom scrim. Long-press lifts a peek menu (Fave / Add to album / Share)."

- "Create the Flickr photo detail page: full-bleed image at native aspect (max 70% viewport then scroll). Below at 16pt insets: title in Proza Libre 18pt w600 `#FFFFFF`; author row with 30pt circular avatar + name (Inter 13pt w600) + Follow pill (`#FF0084`, 500pt radius, white 13pt w700); action bar (Fave / Comment / Share / Add to album — 18pt icons, label below, Fave active fills `#FF0084`); description body Inter 16pt w400; then the EXIF table."

- "Render the Flickr EXIF / camera detail card: a Surface 1 (`#1A1B1D`) card, 12pt corner radius, 16pt inset, titled 'CAMERA DETAILS' in Inter 10pt w700 tracked `#6E7176`. Rows for Camera, Lens, Exposure, Aperture, Focal length, ISO, Date taken — each a full-width row with a 0.5pt `#2A2C30` bottom divider, key in Inter 12pt w400 `#B0B3B8` left, value in IBM Plex Mono 11pt w600 `#FFFFFF` right. Camera and Lens rows are tappable (open 'shot with this gear' search)."

- "Build the Flickr favorite star: a five-point star (NOT a heart). Inactive = outline only, white 1.8pt stroke at 90% opacity. Active = filled `#FF0084` no stroke. On tap: scale 1.0 → 1.25 → 1.0 over 280ms spring (damping 0.6), soft haptic, brief `#FF0084` particle burst, and increment the adjacent fave count (Inter 11pt w600)."

- "Create the Flickr bottom tab bar: 56pt + safe area, background `rgba(12,13,14,0.94)` with `saturate(180%) blur(20px)`, 0.5pt top border `#2A2C30`. Five tabs: Feed, Search, Camera (center, emphasized — 27pt camera glyph drawn in `#FF0084` outline, no label), Notify, You. Active icon fills `#FFFFFF`; inactive `#6E7176`. Labels Inter 10pt w500."

- "Render a Flickr camera/lens chip row: pill chips (500pt radius), background `#232427`, 1pt `#2A2C30` border, text Inter 12pt w600 `#FFFFFF`; EXIF-value chips (`f/2.8`, `1/250s`) use IBM Plex Mono 11pt. 7pt vertical / 14pt horizontal padding, 10pt gap. Each chip tappable -> opens 'more photos shot with this' search."

### Iteration Guide
1. Canvas is near-black `#0C0D0E` in dark, pure `#FFFFFF` in light — darker than typical apps so photos dominate
2. The photostream is a justified mosaic — row-packed, native aspect ratios preserved, 3pt gutters, no cropping
3. Favorites use a five-point STAR filling `#FF0084` — never a heart
4. Two brand colors only: Flickr Pink `#FF0084` (favorites/primary), Flickr Blue `#0063DC` (links/secondary)
5. The EXIF / camera detail table is the signature surface — Camera/Lens/Exposure/Aperture/ISO key-value rows, values in monospace
6. Proza Libre (serif) = titles/headers; Inter (sans) = all functional UI; never mix them up
7. Grid tiles and the detail image are square-cornered, borderless, shadowless — the gallery wall is continuous
8. Camera & lens metadata are tappable chips linking to "more shot with this gear"
9. The Pro gold star `#FFB200` is for Pro badges only — not a general accent
10. Chrome (nav, tabs) is translucent + blurred so it floats invisibly over the photography
