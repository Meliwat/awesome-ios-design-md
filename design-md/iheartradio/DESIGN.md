# Design System Inspiration of iHeartRadio (iOS)

## 1. Visual Theme & Atmosphere

iHeartRadio's iOS app is a live-radio-first audio platform whose identity rests on a warm maroon-black canvas (`#120A0E`) — a red-tinted dark, never pure black — across which the iHeart Red→Magenta system (`#C6002B` → `#E40A5D`, with coral `#F23A2F` reserved for "live" signals) is rationed deliberately. The warmth in the background means the brand red feels native to the same color world rather than pasted on top. Open the app and you land on a "For You" feed of recommended live stations, recently played, and podcast shelves, but the screen everything builds toward is the **Live Station Player**: a heart-logomark tile, a pulsing **LIVE** badge, the station's call sign and frequency, the currently-airing track, and — critically — an **indeterminate scanning bar instead of a scrubber**, because a live radio stream cannot be seeked.

The signature affordance is **live radio with no seek**. Unlike a music player, iHeartRadio's player has no progress bar and no scrubber knob — instead a coral light sweeps left-to-right across a thin track, communicating "streaming now" rather than "1:48 of 4:19." The transport is reduced accordingly: previous/next become **station-skip** and the center control is **stop** (a square), not pause, because you can't pause a live broadcast — you stop and rejoin. The second signature is the **heart logomark as artwork**: live stations don't have album art, so the station tile shows the iHeart heart-check mark on a warm radial-lit ground, badged LIVE, with the frequency in a glass chip. The third is the **pulsing LIVE badge** — a coral pill with a radiating white dot that pulses on a ~1.6s loop, the single most "alive" element on screen.

The color system is intentionally tight. There is one canvas, two raised surfaces, one divider, two text levels, and the brand expressed as a red→magenta system (with its endpoints usable solo, and coral `#F23A2F` strictly for live indicators). Station logos and podcast art remain full-color; UI chrome does not. There is no secondary brand palette. This restraint is the point: when the canvas is warm near-black and quiet, the red play button and the pulsing LIVE badge read instantly as "the broadcast is on."

Typography uses **iHeart Sans** (the brand's geometric sans) with **Inter** as the faithful fallback — both clean and confident at heavy weights. Hierarchy is bold: screen titles at 32pt weight 800, the station name at 26pt weight 800, sections at 22pt weight 700, body at 16pt weight 400. The LIVE tag and overlines are heavy 12pt weight 900 uppercase with wide tracking — broadcast-poster energy. The type leans into display weights because the warm-dark canvas is quiet and text needs presence.

**Key Characteristics:**
- Warm maroon-black canvas (`#120A0E`) — a red-tinted dark, never pure `#000000`
- iHeart Red→Magenta system (`#C6002B` → `#E40A5D`) rationed; coral `#F23A2F` reserved for "live"
- **Live Station Player** — heart logomark tile + LIVE badge + call sign + frequency chip
- **No scrubber** — an indeterminate coral scanning bar (live radio cannot seek)
- **Stop, not pause** — center transport is a square; prev/next are station-skip
- **Pulsing LIVE badge** — coral pill with a radiating white dot on a ~1.6s loop
- **Heart logomark as artwork** — the iHeart heart-check on a warm radial ground when no album art
- 68pt circular red play button — the single largest, brightest object on the player
- Heavy display typography — iHeart Sans / Inter at weights 700–900 for titles & LIVE tags
- Pill geometry — primary buttons and the LIVE badge are fully rounded (999pt)
- Monochrome chrome — chevrons, secondary transport, tab icons all `#B8A0A8`
- Bottom tab strip: For You / Radio / Podcasts / Search / Library, coral active tint

## 2. Color Palette & Roles

### Primary (Interactive)
- **iHeart Red** (`#C6002B`): Primary brand red; the circular play button, heart logomark fill, primary CTAs.
- **iHeart Coral** (`#F23A2F`): The "LIVE" signal color — LIVE badge, scanning bar, live tags, active tab tint.
- **iHeart Magenta** (`#E40A5D`): Gradient end / accent for shelf headers, "Create Station," highlights.
- **iHeart System Gradient**: `linear-gradient(135deg, #C6002B → #E40A5D)` — station logos, brand chips, hero cards.
- **Red Pressed** (`#9E0022`): Pressed state for red fills.
- **Coral Pressed** (`#CC2D24`): Pressed state for coral fills.

### Canvas & Surfaces (Dark — primary, the only real theme)
- **Canvas** (`#120A0E`): Primary app background — warm maroon-black, NOT pure black.
- **Surface 1** (`#1E1216`): Cards, sheets, raised rows, mini-player bar.
- **Surface 2** (`#2A171D`): Hovered/pressed rows, scanning-bar rail, chips.
- **Divider** (`#341C24`): Hairline separators between rows and sections.
- **Overlay Scrim** (`#120A0ED9`): 85% canvas behind modals and the expanded player.

### Canvas & Surfaces (Light — fallback only; iHeartRadio ships dark-first for the player)
A full light theme is not the player's identity; if a host OS forces light, invert minimally:

| Role | Dark Mode (primary) | Light Mode (fallback) |
|------|---------------------|------------------------|
| Canvas | `#120A0E` | `#FFFFFF` |
| Surface 1 | `#1E1216` | `#FBF2F4` |
| Surface 2 | `#2A171D` | `#F3E4E8` |
| Divider | `#341C24` | `#EAD9DE` |
| Text Primary | `#FFFFFF` | `#1A0E12` |
| Text Secondary | `#B8A0A8` | `#7C6168` |

### Text
- **Text Primary** (`#FFFFFF`): Station names, screen titles, primary metadata on dark.
- **Text Secondary** (`#B8A0A8`): "Now airing" sub-lines, captions, inactive icons.
- **Text Tertiary** (`#7C6168`): Disabled, very low-emphasis labels.
- **Text On Red** (`#FFFFFF`): Always white — used on the play button and primary CTA.

### Live / Status
- **LIVE Coral** (`#F23A2F`): The pulsing LIVE badge, scanning bar sweep, live row tags.
- **On-Air White** (`#FFFFFF`): The pulsing dot inside the LIVE badge.
- **Recorded Gray** (`#B8A0A8`): On-demand/podcast items (NOT live) — explicitly non-coral to contrast.

### Semantic
- **Success Green** (`#1ED760`): Download-complete, "added to library" confirm toast.
- **Error Red** (`#FF4D5E`): Stream failed, offline error, destructive confirm.
- **Warning Amber** (`#FFB02E`): Low-bandwidth / data-saver banner.

## 3. Typography Rules

### Font Family
- **Primary**: `iHeart Sans` — iHeartRadio's brand geometric humanist sans, confident at display weights.
- **Faithful Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — near-identical geometric humanist proportions; the recommended substitute.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for any timestamp/podcast duration (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | iHeart Sans | 32pt | 800 | 1.15 | -0.6pt | "For You", "Radio", page hero titles |
| Station Name | iHeart Sans | 26pt | 800 | 1.2 | -0.4pt | Call sign on the Live Station Player |
| Section Header | iHeart Sans | 22pt | 700 | 1.25 | -0.2pt | "Live Stations", "Recommended" |
| Subhead | iHeart Sans | 18pt | 700 | 1.3 | -0.1pt | Shelf group titles, sheet headers |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| Row Title | iHeart Sans | 15pt | 600 | 1.3 | 0pt | Station / podcast row primary line |
| Now Airing | Inter | 15pt | 500 | 1.35 | 0pt | "Now: Artist — Track" sub-line |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | Genre, market, secondary sub-labels |
| LIVE Tag | iHeart Sans | 12pt | 900 | 1.0 | 0.8pt | "● LIVE", overlines, UPPERCASE |
| Frequency | iHeart Sans | 12pt | 800 | 1.0 | 0.3pt | "103.5 FM" glass chip |
| Tab Label | iHeart Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | iHeart Sans | 16pt | 700 | 1.0 | 0pt | Primary / pill buttons |
| Chip | iHeart Sans | 13pt | 700 | 1.0 | 0.2pt | Genre/mood chips |

### Principles
- **Heavy by default**: Titles live at 700–800; LIVE tags at 900. On a warm-dark canvas, bold is how hierarchy reads — never thin/light for headings.
- **Display vs reading split**: iHeart Sans for titles, rows, chips, buttons, LIVE tags (brand voice); Inter at 400/500 for body and "now airing" metadata (reading rhythm).
- **Broadcast-poster overlines**: LIVE tags and the context overline use 900 weight + wide tracking — they should feel like a radio promo, not a UI caption.
- **Tabular numerals where time appears**: Podcast durations, episode counts — digits don't jitter.
- **Dynamic Type**: Titles, body, row text scale; tab labels, LIVE tags, frequency chip, overlines stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Red Button (Listen Live / Subscribe)**
- Shape: Full pill, corner radius 999pt
- Background: `#C6002B` (solid iHeart Red)
- Text: `#FFFFFF`, iHeart Sans 16pt weight 700, optional leading ▶ glyph
- Padding: 14pt vertical, 30pt horizontal
- Pressed: background `#9E0022` + scale 0.98
- Disabled: red at 35% opacity, text `#FFFFFF` at 50%

**Coral Button (Create Station / live action)**
- Background: `#F23A2F`
- Text: `#FFFFFF`, iHeart Sans 15pt weight 700
- Corner radius: 999pt
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#CC2D24` + scale 0.98

**Outline Button (Follow / Add)**
- Background: transparent
- Border: 1.5pt `#FFFFFF` at 40% opacity (`rgba(255,255,255,0.4)`)
- Text: `#FFFFFF`, iHeart Sans 14pt weight 600
- Corner radius: 999pt
- Padding: 12pt vertical, 22pt horizontal
- Pressed: border + text to 70% opacity, background `#FFFFFF` at 6%

**Text Button (See all / Skip)**
- Text: `#F23A2F`, iHeart Sans 14pt weight 700
- No background, no underline
- Pressed: opacity 0.7

**Circular Play Button (Live Station Player — signature)**
- Size: 68pt diameter
- Background: `#C6002B` (solid red — not a gradient on the player)
- Glyph: ▶ in `#FFFFFF`, 26pt, optically centered
- Shadow: `0 12px 28px -8px rgba(198,0,43,0.65)` — colored red glow
- Pressed: scale 0.94, 80ms; shadow tightens
- Note: this control is a play/stop toggle for a live stream — there is no pause state

### Core Atoms

**Heart Logomark Station Tile (no album art)**
- Aspect ratio: 1:1, corner radius 14pt
- Fill: `linear-gradient(150deg, #2A171D → #1E1216 → #120A0E)` + a warm red radial bloom centered (`rgba(198,0,43,0.35)`)
- Center: the iHeart heart-check logomark at ~44% of tile size, white check on red heart, with `drop-shadow(0 8px 22px rgba(198,0,43,0.55))`
- Shadow: `0 24px 48px -22px rgba(198,0,43,0.45)` — colored ambient lift

**Pulsing LIVE Badge**
- Position: top-left of station tile, 16pt inset
- Background: `#F23A2F` (coral)
- Content: a radiating white dot (7pt, `box-shadow` pulse ring on a ~1.6s ease-out loop) + "LIVE" in iHeart Sans 11pt weight 900, white, 0.8pt tracking
- Corner radius: 999pt; padding 6pt vertical, 12pt right / 10pt left

**Frequency Chip**
- Position: bottom-right of station tile, 16pt inset
- Background: `rgba(18,10,14,0.7)` with `backdrop-filter: blur(8px)`
- Content: "103.5 FM" in iHeart Sans 12pt weight 800, white, 0.3pt tracking
- Corner radius: 8pt; padding 6pt vertical, 12pt horizontal

**Scanning Bar (replaces the scrubber)**
- Track: 4pt height, `#2A171D`, corner radius 2pt, `overflow: hidden`
- Sweep: a `linear-gradient(90deg, transparent, #F23A2F, transparent)` band ~38% wide that animates left→right on a ~2.2s ease-in-out loop
- Below it: a "Streaming live" label — iHeart Sans 11pt weight 700 `#F23A2F` UPPERCASE with a leading 6pt coral dot. NO timestamps (live has no position).

**Live Station Row**
- Height: 68pt; 48pt station-logo tile (8pt radius) + name/sub stack + trailing play
- Name: iHeart Sans 15pt w600 `#FFFFFF`; sub: a coral LIVE tag (10pt w900, leading dot) + genre/market in `#B8A0A8`
- Trailing: an outlined circular play glyph in `#F23A2F`
- Pressed: row background `#2A171D`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(18,10,14,0.94)` with `backdrop-filter: saturate(180%) blur(20px)`, 0.5pt top border `#341C24`
- Tabs (5): For You, Radio, Podcasts, Search, Library
- Icon size: 22pt; active fills solid, inactive is stroked
- Active color: `#F23A2F` (coral) icon + label
- Inactive color: `#7C6168`
- Labels: iHeart Sans 10pt weight 600, always shown

**Live Station Player Top Bar**
- Leading: down-chevron (collapse player), 22pt `#FFFFFF`
- Center: two-line context — overline "LIVE STATION" (12pt w800 `#B8A0A8` uppercase) over "For You" (13pt w700 `#FFFFFF`)
- Trailing: ⋯ overflow menu, 22pt `#FFFFFF`

**Mini Player (collapsed)**
- Height: 56pt, sits above the tab bar, background `#1E1216`
- Leading 40pt station-logo thumb, name + "Now airing" stack, trailing play/stop + favorite
- A thin coral scanning sweep pinned to the very top edge of the bar (no progress fill — it's live)

### Input Fields

**Search Bar**
- Height: 44pt, background `#1E1216`, corner radius 12pt, no border
- Leading magnifier 18pt `#B8A0A8`; placeholder "Stations, podcasts, artists…" 15pt `#B8A0A8`
- Focus: 1.5pt `#F23A2F` border; clears to a recent-searches list

**Genre Chip**
- Height: 34pt, corner radius 999pt
- Default: background `#2A171D`, text `#FFFFFF` iHeart Sans 13pt w700
- Selected: background gradient `#C6002B → #E40A5D`, text `#FFFFFF`

### Distinctive Components

**Live Station Player (signature screen)**
- Warm `#120A0E` canvas; top bar → heart-logomark station tile (LIVE badge top-left, frequency chip bottom-right) → station call sign (26pt w800) + favorite heart (coral) → scanning bar + "Streaming live" label (no timestamps) → transport row (station-skip / STOP / play / settings) where only the 68pt circular red play button is brand-colored and every other control is monochrome

**Pulsing LIVE Badge**
- The single most "alive" element — a coral pill whose white dot radiates a pulsing ring. Mirrored as a small coral LIVE tag on every live station row.

**Scanning Bar (no scrubber)**
- The defining "this is live radio" cue: an indeterminate coral sweep instead of a seekable progress bar. There is no knob, no elapsed time, no remaining time.

**Heart Logomark as Artwork**
- When a live station has no album art, the iHeart heart-check mark on a warm radial-lit tile is the artwork — the brand is the cover.

**Stop-not-Pause Transport**
- The center control is a square (stop), and prev/next are station-skip — reinforcing that a broadcast can't be paused or seeked, only joined and left.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 22, 24, 28, 32, 40, 48, 64
- Screen horizontal inset: 24pt (generous — the warm-dark canvas wants air)
- Row vertical padding: 10pt; section gap: 28pt
- Station-tile-to-name gap on the player: 22pt

### Grid & Container
- iPhone: single column, 24pt side insets, full-width station tile
- iPad: content max-width 720pt centered; station tile capped at 480pt
- The Live Station Player is a vertical stack, never multi-column

### Whitespace Philosophy
- **Quiet warm canvas, loud focal point**: most of the screen is warm near-black so the red play button and pulsing LIVE badge dominate with no competition
- **Generous side insets (24pt)**: dark UIs feel cramped at tight gutters; iHeartRadio breathes
- **Station tile is the hero**: on the player the heart-logomark tile claims the largest single block
- **No card borders**: surfaces are distinguished by fill (`#1E1216` / `#2A171D`), never by stroke

### Border Radius Scale
- Square (0pt): full-bleed backgrounds, dividers
- Subtle (4pt): scanning-bar track
- Standard (8pt): station-row logo tiles, frequency chip, inline cards
- Comfortable (12pt): search bar, sheets, bottom-sheet headers
- Large (14pt): primary station tile on the player
- XL (16pt): hero shelf cards
- Pill (999pt): all buttons, chips, the LIVE badge
- Circle (50%): the play button, avatars, the pulsing LIVE dot

## 6. Depth & Elevation

iHeartRadio is mostly flat; the only "depth" is a colored red glow under the play button and the station tile. No gray Material shadows on the warm-dark canvas — they'd be invisible — so elevation is surface fill steps and colored ambient blooms.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; `#120A0E` | Page content, dividers, rows |
| Raised (Level 1) | Fill step to `#1E1216` | Cards, sheets, mini player |
| Pressed (Level 2) | Fill step to `#2A171D` | Hovered/pressed rows, scan rail |
| Glow (Level 3) | `0 24px 48px -22px rgba(198,0,43,0.45)` | Heart-logomark station tile |
| Accent Glow (Level 4) | `0 12px 28px -8px rgba(198,0,43,0.65)` | 68pt circular red play button |
| Scrim | `rgba(18,10,14,0.85)` | Behind modals & expanded player |

**Shadow Philosophy**: Shadows are *red*, never gray. The play button casts a red glow; the station tile casts a red glow; the LIVE dot radiates a white pulse ring. Elevation feels like broadcast light emitted by the brand rather than a drop-shadow trick — appropriate for a warm, live audio UI.

### Motion
- **LIVE badge pulse**: the white dot's ring expands `box-shadow 0 → 7pt` and fades over a ~1.6s ease-out loop — continuous while live
- **Scanning bar**: a coral band sweeps left→right across the track on a ~2.2s ease-in-out loop — continuous while streaming; stops when the stream stops
- **Player expand/collapse**: mini player → full Live Station Player in 320ms spring (damping 0.82); station tile scales up from thumb
- **Play/stop morph**: ▶ ↔ ■ glyph crossfades + button scales 0.94 → 1 in 120ms; soft haptic
- **Station skip**: station tile slides horizontally 220ms ease-out, new call sign + "now airing" crossfades
- **Live-row tag pulse**: the small coral LIVE dot on rows pulses subtly in sync (~1.6s)
- **Tab switch**: instant content swap; active icon fills + tints coral with a 120ms ease
- **Haptics**: soft impact on play/stop, favorite toggle, and station skip

## 7. Do's and Don'ts

### Do
- Use the warm maroon-black `#120A0E` as the canvas — never pure `#000000`
- Treat the red→magenta system as scarce — play button, heart logomark, LIVE badge, scanning bar
- Reserve coral `#F23A2F` for "live" signals only — LIVE badge, scan sweep, live tags, active tab
- Use a scanning bar, NOT a scrubber, on the live player — live radio cannot seek
- Make the center transport a STOP square (not pause) and prev/next station-skip
- Pulse the LIVE badge's white dot continuously while the stream is live (~1.6s loop)
- Use the heart logomark on a warm radial tile as artwork when a station has no album art
- Make the 68pt circular red play button the single brightest object on the player (red glow)
- Keep all chrome (chevrons, secondary transport, tab icons) monochrome `#B8A0A8`
- Use heavy display weights (700–800 titles, 900 LIVE tags) — the warm-dark canvas needs bold anchors
- Cast *red* glows under the play button and station tile — never gray drop shadows

### Don't
- Don't use pure black `#000000` — the maroon tint is the brand's warmth
- Don't add a scrubber, progress fill, or elapsed/remaining time to a live stream
- Don't show a pause button on the live player — it's play/STOP, you rejoin a broadcast
- Don't use coral `#F23A2F` for non-live UI — it must stay the unambiguous "live" signal
- Don't spray the red gradient across cards/backgrounds — it must stay scarce
- Don't color secondary transport controls — station-skip/settings stay `#B8A0A8`
- Don't ship a real light theme as the player's identity — light is OS-forced fallback only
- Don't use gray Material drop shadows on the warm-dark canvas — use red glows or fill steps
- Don't use thin/light font weights for titles — minimum 600, LIVE tags 900
- Don't put borders on cards — distinguish surfaces by fill (`#1E1216` / `#2A171D`)
- Don't stop the LIVE pulse or scanning sweep while the stream is actually live

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Station tile shrinks to fit; transport spacing tightens to 12pt |
| iPhone 13/14/15 | 390pt | Standard layout, 24pt insets |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; top bar respects it |
| iPhone 15/16 Pro Max | 430pt | Larger station tile; play button stays 68pt (fixed, iconic) |
| iPad (portrait) | 768pt | Content max-width 720pt centered; station tile capped 480pt |
| iPad (landscape) | 1024pt+ | Two-pane: station list left, Live Player right; tile max 520pt |

### Dynamic Type
- Screen titles, station name, section headers, body, row text: full scale
- Tab labels, LIVE tags, frequency chip, overlines, chip text: FIXED (layout-sensitive)
- Heart logomark, scanning bar, LIVE pulse are graphic, unaffected by type scale

### Orientation
- iPhone Live Station Player is portrait-locked (vertical stack)
- iPad supports landscape split (station list + player side-by-side)
- Station tile maintains 1:1 aspect in all orientations

### Touch Targets
- Circular play button: 68pt (well above minimum)
- Secondary transport (station-skip/stop/settings): 44pt hit, 26/22pt glyph
- Tab bar items: 22pt glyph, 48pt hit
- Station row: full 68pt row tappable; trailing play has 44pt hit
- Favorite heart: 44pt hit, 24pt glyph

### Safe Area Handling
- Top: Live Player top bar respects safe area + Dynamic Island
- Bottom: tab bar and mini player respect home indicator
- Scrim and expanded player extend edge-to-edge under status/home areas
- Sides: 24pt content inset on all devices

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#120A0E` (warm maroon-black — NOT pure black)
- Surface 1: `#1E1216`
- Surface 2: `#2A171D`
- Divider: `#341C24`
- Text primary: `#FFFFFF`
- Text secondary: `#B8A0A8`
- iHeart red (play button / primary): `#C6002B`
- iHeart coral (LIVE signal / active tab): `#F23A2F`
- iHeart magenta (gradient end / accents): `#E40A5D`
- iHeart system gradient: `linear-gradient(135deg, #C6002B → #E40A5D)`
- Red pressed: `#9E0022`
- Success: `#1ED760`
- Error: `#FF4D5E`
- Play-button glow: `0 12px 28px -8px rgba(198,0,43,0.65)`
- Station-tile glow: `0 24px 48px -22px rgba(198,0,43,0.45)`

### Example Component Prompts
- "Build the iHeartRadio Live Station Player in SwiftUI: warm `#120A0E` canvas. Top bar: leading down-chevron (`#FFFFFF` 22pt), center two-line context — overline 'LIVE STATION' (iHeart Sans/Inter 12pt w800 `#B8A0A8` UPPERCASE) over 'For You' (13pt w700 `#FFFFFF`), trailing ⋯. Then a 1:1 station tile (corner radius 14pt, `linear-gradient(150deg,#2A171D,#1E1216,#120A0E)` + a centered red radial bloom + glow `0 24px 48px -22px rgba(198,0,43,0.45)`) with the iHeart heart-check logomark centered at ~44%, a top-left pulsing LIVE badge (coral `#F23A2F` pill, radiating white dot + 'LIVE' 11pt w900), and a bottom-right frequency chip ('103.5 FM' blurred `rgba(18,10,14,0.7)`)."
- "Create the iHeartRadio circular play button: 68pt diameter, solid `#C6002B`, white ▶ glyph 26pt, shadow `0 12px 28px -8px rgba(198,0,43,0.65)`. It is a play/STOP toggle (no pause): pressed scales 0.94 over 120ms with a soft haptic; ▶/■ crossfade."
- "Render the iHeartRadio scanning bar (NO scrubber — live radio can't seek): 4pt track `#2A171D` radius 2pt, `overflow hidden`; a `linear-gradient(90deg,transparent,#F23A2F,transparent)` band ~38% wide sweeping left→right on a ~2.2s ease-in-out loop. Below it: a 'STREAMING LIVE' label, iHeart Sans 11pt w700 `#F23A2F` UPPERCASE with a leading 6pt coral dot. No timestamps."
- "Build an iHeartRadio live station row: 68pt tall, 48pt logo tile (radius 8pt, brand gradient), name iHeart Sans 15pt w600 `#FFFFFF`, sub = a coral LIVE tag (iHeart Sans 10pt w900 with a 5pt leading coral dot) + genre/market in `#B8A0A8`, trailing an outlined circular play glyph in `#F23A2F`. Pressed background `#2A171D`."
- "Create the iHeartRadio pulsing LIVE badge: coral `#F23A2F` pill, a 7pt white dot whose `box-shadow` ring expands 0 → 7pt and fades over a ~1.6s ease-out infinite loop, next to 'LIVE' in iHeart Sans 11pt w900 white 0.8pt tracking, 6pt vertical / 10–12pt horizontal padding, 999pt radius."
- "Create the iHeartRadio bottom tab bar: 52pt + safe area, `rgba(18,10,14,0.94)` blur, 0.5pt top border `#341C24`. Tabs: For You, Radio, Podcasts, Search, Library — 22pt icons, labels iHeart Sans 10pt w600. Active: coral `#F23A2F` filled icon + label; inactive: `#7C6168` stroked."

### Iteration Guide
1. Canvas is warm maroon-black `#120A0E` — never `#000000`, never an off-white light theme as the player's identity
2. The red→magenta system is SCARCE — play button, heart logomark, LIVE badge, scanning bar
3. Coral `#F23A2F` is the unambiguous "LIVE" color — LIVE badge, scan sweep, live tags, active tab only
4. There is NO scrubber — a coral scanning bar conveys "streaming live," with no timestamps
5. Transport is play/STOP (square, not pause) with station-skip prev/next — you rejoin a broadcast
6. The pulsing LIVE badge (radiating white dot, ~1.6s loop) is the most "alive" element on screen
7. The heart logomark on a warm radial tile IS the artwork when a station has no album art
8. The 68pt circular red play button is the single brightest object, with a red glow
9. All chrome (chevrons, secondary transport, tabs) is monochrome `#B8A0A8`
10. Titles are heavy (iHeart Sans / Inter 700–800), LIVE tags are 900; body/metadata is Inter 400/500; shadows are red glows, never gray
