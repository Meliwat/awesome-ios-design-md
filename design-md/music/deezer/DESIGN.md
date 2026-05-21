# Design System Inspiration of Deezer (iOS)

## 1. Visual Theme & Atmosphere

Deezer's iOS app is a dark-native music player whose entire identity rests on one move: a violet-tinted near-black canvas (`#0F0D13`) across which a single purple→pink gradient (`#A238FF` → `#FF0092`) is rationed like a precious resource. The app never goes pure black and never offers a true light theme — the slight violet warmth in the background means the brand gradient feels native to the same color world rather than pasted on top. Open the app and you land on a "Home for you" feed of mixes and editorial cards, but the screen everything builds toward is **Flow Now Playing**: a full-bleed living-gradient artwork tile with the equalizer waveform embedded directly inside it, a 68pt circular gradient play button, a gradient scrubber, and song metadata in heavy Inter weights.

The signature affordance is **Flow** — Deezer's personalized infinite mix. It is not a playlist; it is a generative stream represented by an animated living-gradient cover (purples bleeding into magenta-pink) badged with a small lightning "FLOW" pill. The second signature is the **embedded equalizer**: instead of a static album image, Flow's artwork carries a row of luminous white bars that rise and fall with the music, so the artwork itself *is* the visualizer. The third is the **gradient-as-scarcity rule**: chrome (chevrons, secondary transport, tab labels, dividers) stays strictly monochrome `#A29CB0`, so the only colored thing on screen is the part that *is the music* — the play button, the scrubber fill, the Flow badge, and the now-playing equalizer marker on song rows.

The color system is intentionally tiny. There is one canvas, two raised surfaces, one divider, two text levels, and exactly one accent expressed as a gradient (with its two endpoint hues, purple `#A238FF` and pink `#FF0092`, usable solo for icons and small fills). Album and Flow artwork remains full-color and luminous — content is allowed to be vivid; UI chrome is not. There is no orange, no green-except-success, no secondary brand palette. This restraint is the whole point: when 95% of the screen is desaturated near-black, the gradient reads instantly as "alive."

Typography uses **Deezer Sans** (the brand's proprietary geometric sans) with **Inter** as the faithful fallback — both are clean, slightly geometric, and carry weight beautifully. Hierarchy is heavy and confident: screen titles at 32pt weight 800, the now-playing track title at 26pt weight 800, sections at 22pt weight 700, body at 16pt weight 400. Overlines (the "FLOW · YOUR MIX" context label) are 12pt weight 700 uppercase with wide tracking. The type system leans into bold display weights because the canvas is so dark and quiet that text needs presence to anchor the layout.

**Key Characteristics:**
- Violet-tinted near-black canvas (`#0F0D13`) — dark-native, no true light theme, never pure black
- Purple→pink gradient (`#A238FF` → `#FF0092`) rationed as a scarce accent — only on "alive" elements
- **Flow** — personalized infinite mix with a living-gradient cover + lightning "FLOW" badge
- **Embedded equalizer** — luminous white bars *inside* the Flow artwork; the art is the visualizer
- 68pt circular gradient play button — the single largest, brightest object on Now Playing
- Gradient scrubber — track fill is the brand gradient; rest of UI is monochrome
- Now-playing-aware song rows — the playing track turns pink `#FF0092` with a mini equalizer
- Heavy display typography — Deezer Sans / Inter at weights 700–800 for titles
- Pill geometry — primary buttons and the Flow badge are fully rounded (999pt)
- Monochrome chrome — chevrons, secondary transport, tab icons all `#A29CB0`
- Bottom tab strip: Home / Search / Music / Profile, with purple active tint
- Full-color artwork, desaturated UI — content is vivid, chrome is quiet

## 2. Color Palette & Roles

### Primary (Interactive)
- **Deezer Purple** (`#A238FF`): Gradient start; solo accent for active tab, links, small icon fills.
- **Deezer Pink** (`#FF0092`): Gradient end; the "now playing" / favorited state color and equalizer color.
- **Flow Gradient**: `linear-gradient(135deg, #A238FF → #FF0092)` — the play button, scrubber fill, Flow badge.
- **Purple Deep** (`#7C28C4`): Pressed state for purple fills; deep tone inside artwork gradients.
- **Pink Pressed** (`#D60079`): Pressed state for pink fills.

### Canvas & Surfaces (Dark — primary, the only real theme)
- **Canvas** (`#0F0D13`): Primary app background — violet-tinted near-black, NOT pure black.
- **Surface 1** (`#19161F`): Cards, sheets, raised rows, mini-player bar.
- **Surface 2** (`#221E2B`): Hovered/pressed rows, inactive scrubber track, chips.
- **Divider** (`#2A2633`): Hairline separators between rows and sections.
- **Overlay Scrim** (`#0F0D13D9`): 85% canvas behind modals and the expanded player.

### Canvas & Surfaces (Light — fallback only; Deezer ships dark-first)
A light theme is not part of the real product; if a host OS forces light, invert minimally:

| Role | Dark Mode (primary) | Light Mode (fallback) |
|------|---------------------|------------------------|
| Canvas | `#0F0D13` | `#FFFFFF` |
| Surface 1 | `#19161F` | `#F4F2F7` |
| Surface 2 | `#221E2B` | `#EAE7F0` |
| Divider | `#2A2633` | `#E3E0EA` |
| Text Primary | `#FFFFFF` | `#15121C` |
| Text Secondary | `#A29CB0` | `#6E6880` |

### Text
- **Text Primary** (`#FFFFFF`): Track titles, screen titles, primary metadata on dark.
- **Text Secondary** (`#A29CB0`): Artist names, sub-labels, inactive icons, captions.
- **Text Tertiary** (`#6E6880`): Disabled, very low-emphasis timestamps.
- **Text On Gradient** (`#FFFFFF`): Always white — used on the play button and primary CTA.

### Artwork Gradient Tones (full-color, content layer)
The Flow / mix cover gradients use richer mid-tones than the UI accent:

| Role | Color |
|------|-------|
| Art Violet | `#A238FF` |
| Art Magenta Mid | `#C71F8E` |
| Art Pink | `#FF0092` |
| Art Deep Plum | `#7C28C4` |
| Art Highlight | `#FFFFFF38` (22% white radial bloom) |

### Semantic
- **Success Green** (`#1ED760`): Download-complete, "added to favorites" toast confirm.
- **Error Red** (`#FF4D5E`): Failed download, offline error, destructive confirm.
- **Warning Amber** (`#FFB02E`): Low-bitrate / data-saver warning banners.
- **Equalizer Pink** (`#FF0092`): Now-playing animated bars on rows and in artwork (white inside art on gradient).

## 3. Typography Rules

### Font Family
- **Primary**: `Deezer Sans` — Deezer's proprietary geometric humanist sans, clean and confident at display weights.
- **Faithful Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — near-identical geometric humanist proportions; the recommended substitute when Deezer Sans is unavailable.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for all timestamps and scrubber times (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Deezer Sans | 32pt | 800 | 1.15 | -0.6pt | "Flow", "Home", page hero titles |
| Now Playing Title | Deezer Sans | 26pt | 800 | 1.2 | -0.4pt | Track title on Now Playing screen |
| Section Header | Deezer Sans | 22pt | 700 | 1.25 | -0.2pt | "Made for you", "Your top mixes" |
| Subhead | Deezer Sans | 18pt | 700 | 1.3 | -0.1pt | Card group titles, sheet headers |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| Row Title | Deezer Sans | 15pt | 600 | 1.3 | 0pt | Song/playlist row primary line |
| Now Playing Artist | Inter | 15pt | 500 | 1.35 | 0pt | Artist · album sub-line |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | Artist names, secondary sub-labels |
| Overline | Deezer Sans | 12pt | 700 | 1.0 | 0.4pt | "FLOW · YOUR MIX" context label, UPPERCASE |
| Scrubber Time | Inter | 11pt | 500 | 1.0 | 0pt | Elapsed / remaining, tabular |
| Tab Label | Deezer Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Deezer Sans | 16pt | 700 | 1.0 | 0pt | Primary / pill buttons |
| Chip | Deezer Sans | 13pt | 700 | 1.0 | 0.2pt | Genre/mood chips, FLOW badge |

### Principles
- **Heavy by default**: Titles live at 700–800. On a near-black canvas, bold weights are how hierarchy reads — never use thin/light for headings.
- **Display vs reading split**: Deezer Sans for titles, rows, chips, buttons (brand voice); Inter at 400/500 for body and metadata (reading rhythm).
- **Tracked uppercase overlines**: The context label above artwork ("FLOW · YOUR MIX") is the one place wide letter-spacing appears — it frames the hero.
- **Tabular numerals everywhere time appears**: Scrubber, durations, track counts — so digits don't jitter while playing.
- **Dynamic Type**: Titles, body, row text scale; tab labels, scrubber time, overlines, the FLOW badge stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Gradient Button (Play Flow / Subscribe)**
- Shape: Full pill, corner radius 999pt
- Background: `linear-gradient(90deg, #A238FF → #FF0092)`
- Text: `#FFFFFF`, Deezer Sans 16pt weight 700, optional leading ▶ glyph
- Padding: 14pt vertical, 30pt horizontal
- Pressed: `filter: brightness(0.92)` + scale 0.98
- Disabled: gradient at 35% opacity, text `#FFFFFF` at 50%

**Solid Purple Button (Shuffle / secondary action)**
- Background: `#A238FF` (solid, no gradient)
- Text: `#FFFFFF`, Deezer Sans 15pt weight 700
- Corner radius: 999pt
- Padding: 13pt vertical, 26pt horizontal
- Pressed: background `#7C28C4` + scale 0.98

**Outline Button (Follow / Add)**
- Background: transparent
- Border: 1.5pt `#FFFFFF` at 40% opacity (`rgba(255,255,255,0.4)`)
- Text: `#FFFFFF`, Deezer Sans 14pt weight 600
- Corner radius: 999pt
- Padding: 12pt vertical, 22pt horizontal
- Pressed: border + text to 70% opacity, background `#FFFFFF` at 6%

**Text Button (See all / Skip)**
- Text: `#A238FF`, Deezer Sans 14pt weight 700
- No background, no underline
- Pressed: opacity 0.7

**Circular Play Button (Now Playing — signature)**
- Size: 68pt diameter
- Background: `linear-gradient(135deg, #A238FF → #FF0092)`
- Glyph: ▶ / ❚❚ in `#FFFFFF`, 26pt, optically centered (+2pt right nudge for ▶)
- Shadow: `0 12px 28px -8px rgba(255,0,146,0.6)` — colored glow, not gray
- Pressed: scale 0.94, 80ms; shadow tightens

### Core Atoms

**Living-Gradient Artwork Tile (Flow cover)**
- Aspect ratio: 1:1, corner radius 16pt
- Fill: `linear-gradient(135deg, #A238FF 0%, #C71F8E 45%, #FF0092 100%)` + a soft white radial bloom top-right (`rgba(255,255,255,0.22)`) and a deep-plum radial bottom-left (`rgba(124,40,196,0.55)`)
- Shadow: `0 24px 48px -20px rgba(162,56,255,0.55)` — colored ambient lift
- Optionally animates: gradient stops drift on a 12s loop while Flow plays

**FLOW Badge**
- Position: top-left of artwork, 16pt inset
- Background: `rgba(15,13,19,0.55)` with `backdrop-filter: blur(8px)`
- Content: lightning glyph (white, 14pt) + "FLOW" in Deezer Sans 12pt weight 800, white, 0.4pt tracking
- Corner radius: 999pt; padding 7pt vertical, 13pt right / 11pt left

**Embedded Equalizer (inside artwork)**
- Row of 10–14 bars pinned to the bottom of the artwork
- Bar color: `rgba(255,255,255,0.62)`; width flexes to fill; corner radius 3pt (top only)
- Heights animate independently (music-reactive); static design uses a varied 34–92% height set
- Bottom inset 22pt, side inset 18pt, 4pt gap between bars

**Gradient Scrubber**
- Track: 4pt height, `#221E2B`, corner radius 2pt
- Fill: `linear-gradient(90deg, #A238FF → #FF0092)`, width = progress
- Knob: 13pt white circle, `box-shadow: 0 2px 6px rgba(0,0,0,0.5)`; grows to 17pt while dragging
- Times: Inter 11pt weight 500 `#A29CB0`, tabular; elapsed left, remaining right (with leading `-`)

**Song Row (now-playing aware)**
- Height: 64pt; 44pt artwork thumb (6pt radius) + title/sub stack + trailing action
- Default: title `#FFFFFF` 15pt w600, sub `#A29CB0` 13pt; trailing ⋯ in `#A29CB0`
- Now playing: title turns `#FF0092`; trailing replaced by 4-bar mini equalizer in `#FF0092`
- Pressed: row background `#221E2B`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(15,13,19,0.94)` with `backdrop-filter: saturate(180%) blur(20px)`, 0.5pt top border `#2A2633`
- Tabs (4): Home, Search, Music, Profile
- Icon size: 22pt; active fills solid, inactive is stroked
- Active color: `#A238FF` (purple) icon + label
- Inactive color: `#6E6880`
- Labels: Deezer Sans 10pt weight 600, always shown

**Now Playing Top Bar**
- Leading: down-chevron (collapse player), 22pt `#FFFFFF`
- Center: two-line context — overline "FLOW · YOUR MIX" (12pt w700 `#A29CB0` uppercase) over title "Made for you" (13pt w700 `#FFFFFF`)
- Trailing: ⋯ overflow menu, 22pt `#FFFFFF`

**Mini Player (collapsed)**
- Height: 56pt, sits above the tab bar, background `#19161F`
- Leading 40pt artwork thumb, title + artist stack, trailing play/pause + favorite
- A 2pt gradient progress line pinned to the very top edge of the bar

### Input Fields

**Search Bar**
- Height: 44pt, background `#19161F`, corner radius 12pt, no border
- Leading magnifier 18pt `#A29CB0`; placeholder "Artists, tracks, podcasts…" 15pt `#A29CB0`
- Focus: 1.5pt `#A238FF` border; clears to a recent-searches list
- Voice icon trailing, `#A29CB0`

**Genre / Mood Chip**
- Height: 34pt, corner radius 999pt
- Default: background `#221E2B`, text `#FFFFFF` Deezer Sans 13pt w700
- Selected: background gradient `#A238FF → #FF0092`, text `#FFFFFF`

### Distinctive Components

**Flow Now Playing (signature screen)**
- Full-bleed `#0F0D13` canvas; top bar → living-gradient artwork (with FLOW badge + embedded equalizer) → track title (26pt w800) + favorite heart (pink) → gradient scrubber + tabular times → transport row (shuffle/prev/PLAY/next/repeat) where only the 68pt circular play button is gradient and every other control is monochrome

**Embedded Equalizer**
- The visualizer is not a separate panel — it lives inside the artwork as white bars, making the cover itself reactive. Mirrored as a 4-bar pink marker on the currently-playing song row.

**Living-Gradient Flow Cover**
- Generative purple→magenta→pink gradient with white bloom; animates on a slow loop while Flow plays; the visual signature of "personalized infinite mix."

**Gradient Scrubber**
- The only progress indicator that uses the brand gradient; reinforces "this colored thing is the music advancing."

**Now-Playing-Aware Song Rows**
- Rows are not static lists — the active track recolors to pink and swaps its trailing affordance for a live equalizer, tying every list back to the player.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 22, 24, 28, 32, 40, 48, 64
- Screen horizontal inset: 24pt (generous — the dark canvas wants air)
- Row vertical padding: 10pt; section gap: 28pt
- Artwork-to-title gap on Now Playing: 22pt

### Grid & Container
- iPhone: single column, 24pt side insets, full-width artwork tile
- iPad: content max-width 720pt centered; artwork capped at 480pt
- Now Playing is a vertical stack, never multi-column

### Whitespace Philosophy
- **Quiet canvas, loud focal point**: 95% of the screen is desaturated near-black so the gradient play button and living artwork dominate attention with zero competition
- **Generous side insets (24pt)**: dark UIs feel cramped at tight gutters; Deezer breathes
- **Artwork is the hero**: on Now Playing the cover claims the largest single block; everything else is supporting metadata
- **No card borders**: surfaces are distinguished by fill (`#19161F` / `#221E2B`), never by stroke

### Border Radius Scale
- Square (0pt): full-bleed backgrounds, dividers
- Subtle (4pt): scrubber track, mini-player progress line, equalizer bar tops
- Small (6pt): song-row artwork thumbnails
- Standard (8pt): inline cards, small media tiles
- Comfortable (12pt): search bar, sheets, bottom-sheet headers
- Large (16pt): primary artwork tile, hero mix cards
- Pill (999pt): all buttons, chips, the FLOW badge
- Circle (50%): the play button, avatars

## 6. Depth & Elevation

Deezer is mostly flat; the only "depth" is a colored glow under the gradient play button and the living artwork. There are no gray Material shadows on the dark canvas — they'd be invisible — so elevation is signaled by surface fill steps and colored ambient blooms.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; `#0F0D13` | Page content, dividers, rows |
| Raised (Level 1) | Fill step to `#19161F` | Cards, sheets, mini player |
| Pressed (Level 2) | Fill step to `#221E2B` | Hovered/pressed rows, inactive track |
| Glow (Level 3) | `0 24px 48px -20px rgba(162,56,255,0.55)` | Living artwork tile |
| Accent Glow (Level 4) | `0 12px 28px -8px rgba(255,0,146,0.6)` | 68pt circular play button |
| Scrim | `rgba(15,13,19,0.85)` | Behind modals & expanded player |

**Shadow Philosophy**: Shadows are *colored*, never gray. The play button casts a pink glow; the artwork casts a violet glow. This makes elevation feel like light emitted by the brand rather than a drop-shadow physics trick — appropriate for a dark, luminous music UI.

### Motion
- **Player expand/collapse**: mini player → full Now Playing in 320ms spring (damping 0.82); artwork scales up from thumb position
- **Play/pause morph**: ▶ ↔ ❚❚ glyph crossfades + button scales 0.94 → 1 in 120ms; soft haptic
- **Scrubber drag**: 1:1 finger tracking; knob grows 13 → 17pt over 100ms; time labels update live
- **Equalizer bars**: continuous music-reactive height animation while playing; freeze on pause (no animation when paused — Reduce Motion safe)
- **Living gradient**: artwork gradient stops drift on a 12s ease-in-out loop while Flow plays
- **Song-row activation**: title color animates `#FFFFFF` → `#FF0092` over 180ms; mini equalizer fades in
- **Tab switch**: instant content swap; active icon fills + tints purple with a 120ms ease
- **Skip / next**: artwork slides horizontally 220ms ease-out, new metadata crossfades
- **Haptics**: soft impact on play/pause, favorite toggle, and scrub release

## 7. Do's and Don'ts

### Do
- Use the violet-tinted near-black `#0F0D13` as the canvas — never pure `#000000`
- Treat the purple→pink gradient as scarce — only the play button, scrubber fill, FLOW badge, and now-playing equalizer
- Embed the equalizer *inside* the Flow artwork — the art is the visualizer
- Make the circular play button the single largest, brightest object on Now Playing (68pt, gradient, pink glow)
- Recolor the now-playing song row to pink `#FF0092` and swap its trailing icon for a live equalizer
- Keep all chrome (chevrons, secondary transport, tab icons) monochrome `#A29CB0`
- Use heavy display weights (700–800) for titles — the dark canvas needs bold anchors
- Use pill geometry (999pt) for buttons, chips, and the FLOW badge
- Cast *colored* glows (violet under artwork, pink under play) — never gray drop shadows
- Use tabular numerals for every timestamp and scrubber readout
- Let album/Flow artwork stay full-color and luminous — content is vivid

### Don't
- Don't use pure black `#000000` — the violet tint is the brand's warmth
- Don't spray the gradient across cards, headers, or backgrounds — it must stay scarce
- Don't render a separate visualizer panel — the equalizer belongs inside the artwork
- Don't color secondary transport controls — shuffle/prev/next/repeat stay `#A29CB0`
- Don't ship a real light theme — Deezer is dark-native; light is OS-forced fallback only
- Don't use gray Material drop shadows on the dark canvas — use colored glows or fill steps
- Don't use thin/light font weights for titles — minimum 600, headings 700–800
- Don't put borders on cards — distinguish surfaces by fill (`#19161F` / `#221E2B`)
- Don't tint the now-playing row purple — the "playing" state color is pink `#FF0092`
- Don't animate the equalizer while paused — bars freeze when playback stops
- Don't introduce a second accent hue (orange/teal/etc.) — the gradient is the only brand color

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Artwork tile shrinks to fit; transport row spacing tightens to 12pt |
| iPhone 13/14/15 | 390pt | Standard layout, 24pt insets |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; top bar respects it |
| iPhone 15/16 Pro Max | 430pt | Larger artwork; play button stays 68pt (fixed, iconic) |
| iPad (portrait) | 768pt | Content max-width 720pt centered; artwork capped 480pt |
| iPad (landscape) | 1024pt+ | Two-pane: library list left, Now Playing right; artwork max 520pt |

### Dynamic Type
- Screen titles, now-playing title, section headers, body, row text: full scale
- Tab labels, scrubber time, overlines, FLOW badge, chip text: FIXED (layout-sensitive)
- Equalizer and artwork are graphic, unaffected by type scale

### Orientation
- iPhone Now Playing is portrait-locked (vertical stack)
- iPad supports landscape split (library + player side-by-side)
- Artwork tile maintains 1:1 aspect in all orientations

### Touch Targets
- Circular play button: 68pt (well above minimum)
- Secondary transport (prev/next/shuffle/repeat): 44pt hit, 26/22pt glyph
- Scrubber knob: 13pt visual, 44pt vertical hit slop
- Tab bar items: 22pt glyph, 48pt hit
- Song row: full 64pt row tappable; trailing ⋯ has 44pt hit

### Safe Area Handling
- Top: Now Playing top bar respects safe area + Dynamic Island
- Bottom: tab bar and mini player respect home indicator
- Scrim and expanded player extend edge-to-edge under status/home areas
- Sides: 24pt content inset on all devices

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0F0D13` (violet-tinted near-black — NOT pure black)
- Surface 1: `#19161F`
- Surface 2: `#221E2B`
- Divider: `#2A2633`
- Text primary: `#FFFFFF`
- Text secondary: `#A29CB0`
- Deezer purple (gradient start / active tab): `#A238FF`
- Deezer pink (gradient end / now-playing): `#FF0092`
- Flow gradient: `linear-gradient(135deg, #A238FF → #FF0092)`
- Purple deep (pressed): `#7C28C4`
- Success: `#1ED760`
- Error: `#FF4D5E`
- Play-button glow: `0 12px 28px -8px rgba(255,0,146,0.6)`
- Artwork glow: `0 24px 48px -20px rgba(162,56,255,0.55)`

### Example Component Prompts
- "Build the Deezer Flow Now Playing screen in SwiftUI: full-bleed `#0F0D13` canvas. Top bar: leading down-chevron (`#FFFFFF` 22pt), center two-line context — overline 'FLOW · YOUR MIX' (Deezer Sans/Inter 12pt w700 `#A29CB0` UPPERCASE 0.4pt tracking) over 'Made for you' (13pt w700 `#FFFFFF`), trailing ⋯. Then a 1:1 living-gradient artwork tile (corner radius 16pt, `linear-gradient(135deg,#A238FF,#C71F8E,#FF0092)` + white radial bloom top-right + violet glow `0 24px 48px -20px rgba(162,56,255,0.55)`), with a top-left FLOW badge (blurred `rgba(15,13,19,0.55)` pill, lightning glyph + 'FLOW' 12pt w800 white) and a bottom row of ~12 white equalizer bars at 62% opacity."
- "Create the Deezer circular play button: 68pt diameter, `linear-gradient(135deg,#A238FF→#FF0092)`, white ▶ glyph 26pt nudged +2pt right, shadow `0 12px 28px -8px rgba(255,0,146,0.6)`. Pressed: scale 0.94 over 120ms with a soft haptic; ▶/❚❚ crossfade on toggle."
- "Render the Deezer gradient scrubber: 4pt track `#221E2B` radius 2pt; fill `linear-gradient(90deg,#A238FF→#FF0092)` to progress; 13pt white knob with `0 2px 6px rgba(0,0,0,0.5)` growing to 17pt while dragging; below it elapsed (left) and remaining (left `-` prefix, right) in Inter 11pt w500 `#A29CB0` tabular."
- "Build a Deezer now-playing-aware song row: 64pt tall, 44pt artwork thumb (radius 6pt), title Deezer Sans 15pt w600 `#FFFFFF`, sub Inter 13pt `#A29CB0`, trailing ⋯ `#A29CB0`. If this row is the playing track: title color `#FF0092` and replace ⋯ with a 4-bar animated equalizer in `#FF0092`. Pressed background `#221E2B`."
- "Create the Deezer bottom tab bar: 52pt + safe area, `rgba(15,13,19,0.94)` blur, 0.5pt top border `#2A2633`. Tabs: Home, Search, Music, Profile — 22pt icons, labels Deezer Sans 10pt w600. Active: purple `#A238FF` filled icon + label; inactive: `#6E6880` stroked."
- "Build the Deezer primary gradient button: full pill (999pt), `linear-gradient(90deg,#A238FF→#FF0092)`, white text Deezer Sans 16pt w700 with leading ▶, padding 14×30pt. Pressed: brightness 0.92 + scale 0.98. Disabled: gradient 35% opacity."

### Iteration Guide
1. Canvas is violet-tinted near-black `#0F0D13` — never `#000000`, never an off-white light theme
2. The purple→pink gradient is SCARCE — only the play button, scrubber fill, FLOW badge, now-playing equalizer
3. The equalizer lives INSIDE the Flow artwork — the cover is the visualizer (mirrored as a pink 4-bar marker on the active row)
4. The 68pt circular gradient play button is the single brightest object on Now Playing, with a pink glow
5. All chrome (chevrons, secondary transport, tabs) is monochrome `#A29CB0` — color belongs only to "the music"
6. Titles are heavy (Deezer Sans / Inter 700–800); body/metadata is Inter 400/500
7. Shadows are colored glows (violet under art, pink under play) — never gray Material drop shadows
8. The now-playing state color is PINK `#FF0092` — recolor the active row title and show a mini equalizer
9. Geometry is pill-first (999pt buttons/chips/badge) with a 16pt artwork tile and 6pt row thumbs
10. Tabular numerals on every timestamp; equalizer freezes on pause; living gradient drifts only while Flow plays
