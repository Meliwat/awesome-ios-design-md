# Design System Inspiration of TIDAL (iOS)

## 1. Visual Theme & Atmosphere

TIDAL's iOS app is the most austere player in music streaming, and that austerity is the brand. The canvas is pure black (`#000000`) — not the softened "not-quite-black" of competitors, but true OLED black that makes album art appear to float in a void. Surfaces step up only barely (`#0A0A0A`, `#1A1A1A`), so the interface reads as a single dark sheet with content punched into it. The visual philosophy is editorial and gallery-like: square album tiles, generous negative space, monochrome chrome, and a refusal to decorate. TIDAL positions itself as the audiophile's service, and the UI behaves like high-end audio hardware — matte black, minimal indicators, nothing superfluous.

The accent system is two-tone and deliberate. White (`#FFFFFF`) is the primary actionable color — the play button, primary CTAs, active states. The single chromatic accent is **HiFi cyan** (`#00FFFF`), reserved almost exclusively for quality-tier signaling: the "Master", "HiFi", and "Max" badges that indicate lossless and hi-res audio. When you see cyan in TIDAL, it means *fidelity* — it is the visual language of the product's core differentiator. A pressed cyan (`#00CCCC`) handles tap-down on the rare cyan controls. There is no third color. Secondary text sits in a cool gray (`#9A9A9A`).

Typography is a geometric sans — TIDAL's brand favors a clean, slightly mechanical grotesque (Space Grotesk is the closest free analog; the system font is the production fallback). Weights stay at 400 / 600 / 700. The type is confident and tightly tracked at display sizes, neutral at body. There are no playful flourishes. The most expressive typographic moment is the now-playing full-bleed screen, where the track title sits large (24-28pt) over a darkened crop of the album art with a quality badge pinned beside it.

**Key Characteristics:**
- Pure black canvas (`#000000`) — true OLED black, not softened
- White as the primary action color; **HiFi cyan** (`#00FFFF`) reserved for quality-tier badges
- Quality badges (Master / HiFi / Max) as the signature element — fidelity made visible
- Square album tiles in a gallery-like, high-negative-space grid
- Full-bleed now-playing screen with a darkened album-art backdrop
- Geometric sans typography (Space Grotesk analog), weights 400/600/700
- Mix rows and editorial shelves — "My Mix", "Suggested New Tracks"
- Dark-only — there is no light mode; the void is the brand

## 2. Color Palette & Roles

### Primary
- **TIDAL White** (`#FFFFFF`): Primary action color — play button, primary CTAs, active tab, active toggles, primary text.
- **HiFi Cyan** (`#00FFFF`): The single accent — Master / HiFi / Max quality badges, lossless emphasis, the rare highlighted control.
- **Cyan Pressed** (`#00CCCC`): Tap-down state for cyan controls.

### Canvas & Surfaces
- **Canvas Black** (`#000000`): Primary canvas — true OLED black, the entire app background.
- **Surface 1** (`#0A0A0A`): Cards, list rows, search field — barely lifted off black.
- **Surface 2** (`#1A1A1A`): Sheets, modals, pressed rows, elevated panels.
- **Divider** (`#262626`): Hairline dividers between list rows and sections.

### Text
- **Text Primary** (`#FFFFFF`): Track titles, screen headlines, primary UI text.
- **Text Secondary** (`#9A9A9A`): Artist names, durations, metadata, section subtitles.
- **Text Tertiary** (`#5C5C5C`): Disabled state, very low-emphasis labels.

### Semantic
- **Quality Cyan** (`#00FFFF`): Master / HiFi / Max badge text and outline.
- **Favorite White** (`#FFFFFF`): Heart filled when a track is favorited (white, not cyan — cyan is fidelity-only).
- **Now-Playing Backdrop**: A darkened, blurred crop of the current album art layered under `rgba(0,0,0,0.6)` so foreground text stays legible.
- **Error Red** (`#FF453A`): Playback errors, connection-lost states.

### Light Mode
TIDAL's iOS app is dark-only by design. There is no light variant — the pure-black void is the brand's identity and the optimal surface for album-art presentation and OLED power efficiency.

## 3. Typography Rules

### Font Family
- **Primary**: TIDAL's brand geometric sans (a clean mechanical grotesque)
- **Web / System Fallback**: `Space Grotesk` — the closest free geometric grotesque
- **Production Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Tabular figures for durations and track numbers so scrubber digits don't shift

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Space Grotesk | 28pt | 700 | 1.15 | -0.4pt | "My Collection", "Search" large title |
| Now Playing Track | Space Grotesk | 24pt | 700 | 1.2 | -0.3pt | Current track on full-bleed player |
| Section Header | Space Grotesk | 22pt | 700 | 1.2 | -0.3pt | "My Mix", "Suggested New Tracks" |
| Album / Artist Title | Space Grotesk | 20pt | 700 | 1.2 | -0.2pt | Album detail / artist hero name |
| Track Title | Space Grotesk | 16pt | 600 | 1.3 | 0pt | Track row primary line |
| Card Title | Space Grotesk | 15pt | 600 | 1.3 | 0pt | Album / mix tile title |
| Artist / Subtitle | Space Grotesk | 14pt | 400 | 1.3 | 0pt | Artist name on rows |
| Body | Space Grotesk | 15pt | 400 | 1.45 | 0pt | Album notes, bios |
| Meta / Count | Space Grotesk | 12pt | 400 | 1.3 | 0pt | "14 tracks · 58 min" |
| Quality Badge | Space Grotesk | 10pt | 700 | 1.0 | 1.0pt | "MASTER" / "HIFI" / "MAX", all-caps cyan |
| Label (UPPER) | Space Grotesk | 11pt | 700 | 1.2 | 0.8pt | "EXCLUSIVE", section eyebrow |
| Button (Primary) | Space Grotesk | 15pt | 700 | 1.0 | 0.3pt | White "Play" / "Follow" pill |
| Button (Secondary) | Space Grotesk | 14pt | 600 | 1.0 | 0pt | Outline "Following" |
| Tab Label | Space Grotesk | 10pt | 600 | 1.0 | 0.3pt | Bottom tab bar labels |
| Timestamp | Space Grotesk | 11pt | 600 | 1.0 | 0pt | Scrubber 1:23 / -2:09, tabular |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular, semibold, bold — no thin, no black
- **Tight tracking on display type** (-0.2 to -0.4pt on titles); body sits at 0pt
- **Quality badges are uppercase with +1.0pt tracking** — the mechanical, technical voice
- **Tabular numerals on durations and track numbers**
- **Dynamic Type respected on titles, artists, body**; quality badges, timestamps, tab labels stay fixed

## 4. Component Stylings

### Buttons

**Primary Play Button (Now Playing)**
- Shape: Circle, diameter 64pt (56pt on compact headers)
- Background: `#FFFFFF`
- Icon: SF Symbol `play.fill` / `pause.fill` in `#000000`, 26pt
- Pressed: scale 0.93, background `#E5E5E5`, with `.impactOccurred(.medium)` haptic
- Shadow: none (TIDAL is flat — the white circle on black is contrast enough)

**Primary Pill (Play / Follow)**
- Background: `#FFFFFF`
- Text: `#000000`
- Padding: 11pt vertical, 32pt horizontal
- Corner radius: 500pt (full pill)
- Font: Space Grotesk 15pt weight 700, +0.3pt tracking
- Pressed: `#E5E5E5`, scale 0.97

**Outline Pill ("Following" / "In My Collection")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `#9A9A9A`
- Padding: 11pt vertical, 24pt horizontal
- Corner radius: 500pt
- Font: Space Grotesk 14pt weight 600
- Pressed: border `#FFFFFF`

**Icon Actions (Favorite, Shuffle, Repeat, Add, Share, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#9A9A9A`
- Active: `#FFFFFF` (favorited, shuffle on, repeat on) — never cyan; cyan is fidelity-only
- Spacing: 28pt between icons in the now-playing action row

**Text Button ("See all", "More")**
- Font: Space Grotesk 14pt weight 700
- Color: `#9A9A9A`
- No underline, 44pt hit area

### Cards & Containers

**Album / Mix Tile (Horizontal shelf)**
- Width: 150pt
- Aspect: 1:1 square art, **0pt corner radius** (TIDAL keeps art square — no softening)
- Gap between tiles: 16pt
- Structure: art → quality badge overlay (top-left of art if Master/Max) → title (15pt 600, 2-line clamp) → subtitle (12pt 400 `#9A9A9A`)
- Tap: scale 0.97 with light haptic

**Track Row**
- Height: 60pt
- Layout: 44pt × 44pt square album thumbnail (0pt radius) → stacked title (16pt 600) + artist (14pt 400 `#9A9A9A`) → trailing quality badge (if hi-res) + duration (11pt 600 `#9A9A9A`, tabular) + 20pt ellipsis
- Background: canvas `#000000`; pressed: `#1A1A1A`
- Currently-playing row: title turns `#FFFFFF` bold, a small white equalizer animates left of the thumbnail

**Now Playing (Full Screen)**
- Full-bleed darkened crop of the album art as the backdrop, `rgba(0,0,0,0.6)` scrim
- Centered square album art ~320pt × 320pt, 0pt corner radius, no shadow (flat — the void provides separation)
- Below art: track title (24pt 700) + a cyan quality badge inline + artist (14pt 400 `#9A9A9A`)
- Scrubber: 2pt track `#5C5C5C`, `#FFFFFF` fill, 14pt circular white thumb
- Transport row: shuffle · previous · 64pt white Play · next · repeat
- Action bar: favorite · add to collection · credits · share · more

**Quality Detail Sheet**
- Bottom sheet, 14pt top corner radius, surface `#1A1A1A`
- Lists the streamed format ("FLAC 24-bit / 96 kHz") with a large cyan quality badge at the top
- Cyan is used here for the badge and the active "Max" selection only

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(0,0,0,0.96)` with `.regularMaterial` blur (reads near-opaque on black)
- Tabs: Home, Videos, Search, My Collection
- Icon size: 24pt
- Active color: `#FFFFFF`; inactive: `#9A9A9A`
- Labels: Space Grotesk 10pt weight 600, shown always
- Active icon: filled SF Symbol variant; inactive: outlined

**Now Playing Mini Bar**
- Sits directly above the tab bar, full width, 52pt tall
- Background: `#1A1A1A`
- Left: 40pt square art (0pt radius); center: title 14pt 600 + artist 11pt 400 `#9A9A9A`; a small cyan quality badge trails the title if hi-res
- Right: `play.fill` / `pause.fill` 20pt
- A 1pt `#FFFFFF` progress hairline pinned to the very top edge of the mini bar
- Tap: expands to full-bleed Now Playing with a shared-element transition

**Top Nav (Profile / Home Entry)**
- Height: 44pt + safe area
- Avatar (28pt circular) top-left, screen title centered (22pt 700), settings/cast top-right
- Filter chips row below on My Collection (32pt height pills)

### Input Fields

**Search Bar**
- Background: `#1A1A1A`
- Height: 40pt
- Corner radius: 500pt (full pill)
- Leading SF Symbol `magnifyingglass`, 18pt, `#9A9A9A`
- Placeholder: "Artists, tracks, playlists…", 16pt 400 `#9A9A9A`
- Focus: no border change, cursor white
- Text in field: `#FFFFFF`

**Filter Chip**
- Background: `#1A1A1A` default / `#FFFFFF` selected
- Text: `#FFFFFF` default / `#000000` selected
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 500pt (full pill)
- Font: Space Grotesk 14pt weight 600
- Examples: "All", "Albums", "Tracks", "Playlists", "Videos"

### Distinctive Components

**Quality-Tier Badge**
- A small pill or bracket-framed label: `MASTER`, `HIFI`, `MAX`, `DOLBY ATMOS`
- Text: `#00FFFF` (cyan), 10pt 700, +1.0pt tracking, uppercase
- Style: 1pt cyan hairline border, transparent fill, ~4pt corner radius, 4pt vertical / 8pt horizontal padding
- Appears: on track rows (trailing), on album tiles (overlaid top-left of art), inline beside the now-playing title, and in the quality detail sheet
- This is TIDAL's signature visual — fidelity is the product, so the badge is everywhere fidelity exists

**Full-Bleed Now Playing Backdrop**
- The current album art is cropped to fill, heavily darkened (`rgba(0,0,0,0.6)`) and slightly blurred behind the foreground
- The crisp square album art sits centered on top — a "picture within the picture"
- No color extraction (unlike competitors) — TIDAL keeps the backdrop monochrome-dark for editorial restraint

**My Mix Row (Editorial Shelf)**
- Section eyebrow (11pt 700 upper `#9A9A9A`) + title (22pt 700 white)
- Horizontal scroll of square tiles; Mix tiles often carry a subtle generated-gradient cover with the mix name overlaid
- Ends with a peek-edge tile to signal scroll

**Equalizer (Currently Playing)**
- 3-bar animated equalizer to the left of the playing track's thumbnail
- Bar width 3pt, gap 2pt, color `#FFFFFF` (white, not cyan)
- Freezes when paused

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56, 64
- Standard margin: 16pt horizontal, 24pt between shelves
- Track row internal padding: 8pt vertical, 16pt horizontal

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal shelves: 16pt left inset, last tile peeks at the right edge
- Collection grid: 2-column square grid, 16pt gap (more air than competitors)

### Whitespace Philosophy
- **Gallery-like negative space**: TIDAL deliberately gives content room — fewer items per screen than rivals, more black around each
- **Square integrity**: album art is never rounded — the square is part of the editorial, hardware-like aesthetic
- **The void separates**: with no shadows, the pure-black canvas itself is the separation between elements

### Border Radius Scale
- Square (0pt): ALL album art, tiles, thumbnails — TIDAL's signature
- Badge (4pt): quality badges
- Comfortable (14pt): bottom sheets, modals
- Full Pill (500pt): primary CTAs, filter chips, search bar
- Circle (50%): play button, avatars, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Everything — list rows, tiles, canvas, even Now Playing art |
| Surface Step (Level 1) | `#0A0A0A` (no shadow) | Cards/rows separated by a tonal step, not elevation |
| Sheet (Level 2) | `#1A1A1A` + `rgba(0,0,0,0.8) 0 -8px 32px` | Bottom sheets, quality detail sheet (the only real shadow) |
| Scrim | `rgba(0,0,0,0.6)` over blurred art | Now Playing backdrop legibility |
| Tab Bar Material | `.regularMaterial` over 96%-opaque black | Bottom tab bar over scrolling content |

**Shadow Philosophy**: TIDAL is radically flat. On a pure-black canvas, shadows are invisible anyway, so the design abandons them entirely and communicates hierarchy through tonal steps (`#000000` → `#0A0A0A` → `#1A1A1A`). The single exception is the bottom-sheet shadow, which uses an 80%-opacity dark blur to lift the sheet off the void. No element — not even the centered Now Playing album art — casts a shadow. The flatness *is* the premium, hardware-inspired aesthetic.

### Motion
- **Crossfade transitions**: screen and now-playing transitions use a minimal opacity crossfade (no slide, no bounce) — editorial restraint
- **Play button tap**: scale 0.93 → 1.0 spring (damping 0.75), `.impactOccurred(.medium)` haptic
- **Scrubber drag**: 1pt → 3pt track-height grow while scrubbing, light haptic tick
- **Equalizer bars**: continuous white vertical scale animation tied to playback
- **Mini-bar → Full player**: shared-element 0.3s crossfade, album art is the anchor (fades, does not spring)
- **Favorite tap**: heart fill + subtle scale 1.0 → 1.12 → 1.0 over 260ms

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — true OLED black, the brand identity
- Reserve HiFi cyan (`#00FFFF`) exclusively for quality-tier badges and lossless emphasis
- Make quality badges (Master / HiFi / Max) visible everywhere fidelity exists — it is the signature
- Keep album art perfectly square — 0pt corner radius, always
- Use white for all actions and active states — play, favorite, active tab
- Communicate elevation with tonal steps (`#0A0A0A`, `#1A1A1A`), not shadows
- Use a geometric sans (Space Grotesk fallback) at weights 400/600/700
- Use crossfade transitions — minimal, editorial, no spring or slide
- Give content gallery-like negative space — fewer items, more void

### Don't
- Don't soften the canvas to `#121212` — TIDAL is pure `#000000`
- Don't use cyan for play buttons, favorites, or active tabs — cyan means *fidelity only*
- Don't round album art — the square is the editorial signature
- Don't add drop shadows — the flat void is the premium aesthetic (sheets excepted)
- Don't extract color from album art for backdrops — keep the now-playing backdrop monochrome-dark
- Don't add a second accent color — white + cyan is the entire system
- Don't use thin or black font weights
- Don't animate with bounce or slide — crossfade only
- Don't crowd the grid — TIDAL's negative space is intentional

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 130pt shelf tiles; Now Playing art 280pt |
| iPhone 13/14/15 | 390pt | Standard 150pt tiles; art 320pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | 170pt tiles; Now Playing art 360pt |
| iPad | 768pt+ | 3-column grid; Now Playing art 480pt, controls centered |

### Dynamic Type
- Track titles, artist names, body, section headers: full scale
- Quality badges: fixed 10pt (never scales — the badge geometry is exact)
- Scrubber timestamps: fixed 11pt
- Play button: icon size fixed (26pt)
- Tab labels: fixed 10pt

### Orientation
- Home / Videos / Search / My Collection: **portrait-locked**
- Now Playing: **portrait-locked**
- Video content: **rotates to landscape** (TIDAL hosts music videos and live sets)

### Touch Targets
- Play button: 64pt — unmissable
- Track rows: 60pt height, full-row tappable
- Quality badge: visual 10pt, but tappable 44pt area (opens quality detail sheet)
- Action icons: 22pt glyph, 44pt hit area
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: safe area honored on nav (Dynamic Island clears the title)
- Bottom: mini-bar floats above tab bar; both respect the home indicator
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000` (pure black — not `#121212`)
- Surface 1 (cards): `#0A0A0A`
- Surface 2 (sheets): `#1A1A1A`
- Divider: `#262626`
- Text primary: `#FFFFFF`
- Text secondary: `#9A9A9A`
- Text tertiary: `#5C5C5C`
- TIDAL White (action): `#FFFFFF`
- HiFi Cyan (quality only): `#00FFFF`
- Cyan pressed: `#00CCCC`
- Error red: `#FF453A`

### Example Component Prompts
- "Create a SwiftUI TIDAL track row: 60pt height, 44×44pt square album art (0pt corner radius — never rounded) leading, stacked title 'Black Skinhead' in Space Grotesk 16pt weight 600 #FFFFFF + artist 'Kanye West' in 14pt weight 400 #9A9A9A. Trailing: a cyan 'MASTER' quality badge (10pt 700, 1pt #00FFFF border, transparent fill, +1pt tracking), then duration '3:08' in 11pt weight 600 #9A9A9A tabular, then a 20pt ellipsis. Pressed background #1A1A1A."
- "Build the TIDAL Now Playing play button: 64pt circular button, #FFFFFF background, SF Symbol 'play.fill' 26pt in #000000, NO shadow (TIDAL is flat). Pressed: scale 0.93 with medium haptic, background #E5E5E5."
- "Design the TIDAL full-bleed Now Playing screen: backdrop is the album art cropped to fill, blurred and darkened under rgba(0,0,0,0.6). A crisp 320×320pt square album art (0pt radius, no shadow) centered on top. Below: track title 24pt weight 700 #FFFFFF with an inline cyan 'MAX' quality badge, artist 14pt weight 400 #9A9A9A. Scrubber: 2pt #5C5C5C track, #FFFFFF fill, 14pt white thumb. Canvas pure #000000."
- "Create a TIDAL quality-tier badge: text 'HIFI' in Space Grotesk 10pt weight 700, color #00FFFF, +1pt letter-spacing, uppercase. 1pt solid #00FFFF border, transparent fill, 4pt corner radius, 4pt vertical / 8pt horizontal padding. Used wherever lossless audio exists."
- "Build the TIDAL Now Playing mini bar: above the tab bar, 52pt tall, background #1A1A1A, a 1pt #FFFFFF progress hairline pinned to the top edge. 40×40pt square art (0pt radius) left, title 14pt weight 600 + a small cyan 'MASTER' badge trailing it, artist 11pt weight 400 #9A9A9A, trailing play/pause 20pt. Tap expands to full-bleed Now Playing with a crossfade."

### Iteration Guide
1. Canvas is pure `#000000` — true OLED black, NOT softened to `#121212`
2. White is the action color; HiFi cyan (`#00FFFF`) is reserved exclusively for quality-tier badges
3. Quality badges (Master / HiFi / Max) are the signature — render them everywhere fidelity exists
4. Album art is always square — 0pt corner radius, no exceptions
5. The app is radically flat — no shadows; communicate elevation with tonal steps `#0A0A0A` / `#1A1A1A`
6. Typography: geometric sans (Space Grotesk fallback), weights 400/600/700, tabular numerals
7. Transitions are crossfades — minimal, editorial, never bounce or slide
8. Dark-only — there is no light mode; give content gallery-like negative space
