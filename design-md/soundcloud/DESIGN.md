# Design System Inspiration of SoundCloud (iOS)

## 1. Visual Theme & Atmosphere

SoundCloud's iOS app is built around one idea that no other audio app has: the waveform *is* the interface. Every track — a bedroom demo, a DJ set, a podcast — renders as a horizontal amplitude graph in SoundCloud Orange (`#FF5500`), and that waveform is not decoration. It is the scrubber, the progress bar, the comment timeline, and the social surface all at once. The default canvas is a clean light white (`#FFFFFF`) with a soft gray surface (`#F2F2F2`) for cards and rows, letting the orange waveform and album thumbnails carry every bit of chromatic energy. A true dark mode (`#1A1A1A` canvas, `#262626` surface) mirrors the same structure for night listening.

The accent system is monomaniacal in the best way. SoundCloud Orange is the brand, the play affordance, the progress fill, the active tab, and the "now playing" heartbeat. A lighter orange (`#FF7700`) appears on the unplayed portion of waveforms and on hover-equivalent states; a pressed orange (`#E64A00`) handles tap-down. There is no secondary brand hue — text is near-black (`#333333`) on light, white on dark, and metadata sits in a calm gray (`#999999`). The restraint means the moment you see orange sweeping left-to-right across a waveform, you know audio is moving.

Typography is Interstate — the American highway-signage typeface SoundCloud has used since its rebrand — with Inter as the ubiquitous web/system fallback. It is a no-nonsense grotesque: legible at 11pt timestamps, confident at 22pt screen titles, never flashy. Weights stay at 400 / 500 / 700. There are no display-size headlines because the waveform and the cover art are the visual event; type is the label track underneath. The most expressive typographic moment is the inline comment — a tiny circular commenter avatar pinned to an exact point on the waveform, with the comment text appearing as the playhead sweeps past it.

**Key Characteristics:**
- Light canvas (`#FFFFFF`) / true dark (`#1A1A1A`) with SoundCloud Orange (`#FF5500`) as the only accent
- The waveform is the core UI primitive — scrubber, progress, and comment timeline fused into one element
- Inline commenter avatars pinned to exact timestamps on the waveform
- Orange progress sweep that fills the played portion of the waveform left-to-right
- Circular orange play FAB on the Now Playing screen (64pt)
- Track row with a miniature waveform preview to the right of the title
- "Up next" queue surfaced from the Now Playing screen
- Interstate typeface (Inter fallback), weights 400/500/700
- Repost / like / comment as first-class actions on every track

## 2. Color Palette & Roles

### Primary
- **SoundCloud Orange** (`#FF5500`): Primary brand, play FAB, played-waveform fill, active tab, repost-active, like-active, progress sweep.
- **Orange Light** (`#FF7700`): Unplayed waveform bars, hover/secondary emphasis, link text.
- **Orange Pressed** (`#E64A00`): Tap-down state for the play FAB and orange CTAs.

### Canvas & Surfaces — Light
- **Canvas** (`#FFFFFF`): Primary light canvas — feed, profiles, Now Playing background.
- **Surface** (`#F2F2F2`): Cards, track rows, search field, sheet backgrounds.
- **Divider** (`#E5E5E5`): Hairline dividers between feed items and list rows.

### Canvas & Surfaces — Dark
- **Canvas Dark** (`#1A1A1A`): Primary dark canvas — the signature "not-quite-black" night surface.
- **Surface Dark** (`#262626`): Elevated cards, rows, sheets in dark mode.
- **Divider Dark** (`#333333`): Hairline dividers in dark mode.

### Text
- **Text Primary** (`#333333` light / `#FFFFFF` dark): Track titles, screen headlines, primary UI text.
- **Text Secondary** (`#999999`): Artist names, play counts, durations, metadata (same in both themes).
- **Text Tertiary** (`#BFBFBF` light / `#666666` dark): Disabled state, very low-emphasis labels.

### Semantic
- **Like Orange** (`#FF5500`): Heart filled when a track is liked (same as primary).
- **Repost Orange** (`#FF5500`): Repost icon active state.
- **Waveform Played** (`#FF5500`): The amplitude bars left of the playhead.
- **Waveform Unplayed** (`#FF7700` at ~45% on light / `#FF7700` at ~35% on dark): Bars right of the playhead.
- **Error Red** (`#E5484D`): Upload failures, playback errors.

### Brand Constant
SoundCloud Orange (`#FF5500`) is identical across light and dark — the saturated orange reads cleanly on both `#FFFFFF` and `#1A1A1A`. Only the canvas, surface, divider, and primary text invert.

## 3. Typography Rules

### Font Family
- **Primary**: `Interstate` (Tobias Frere-Jones / Font Bureau) — SoundCloud's brand typeface
- **Web / System Fallback**: `Inter` — the closest free grotesque, used across web surfaces
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Tabular figures for timestamps and play counts so scrubber digits don't shift

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Interstate | 28pt | 700 | 1.15 | -0.3pt | "Library", "Search" large nav title |
| Now Playing Track | Interstate | 22pt | 700 | 1.2 | -0.2pt | Current track on Now Playing |
| Section Header | Interstate | 22pt | 700 | 1.2 | -0.2pt | "More of what you like", "Up next" |
| Profile Name | Interstate | 20pt | 700 | 1.2 | -0.1pt | Artist profile hero name |
| Track Title | Interstate | 16pt | 500 | 1.3 | 0pt | Track row primary line |
| Card Title | Interstate | 15pt | 500 | 1.3 | 0pt | Playlist / album tile title |
| Artist / Subtitle | Interstate | 14pt | 400 | 1.3 | 0pt | Uploader name on rows |
| Body | Interstate | 15pt | 400 | 1.45 | 0pt | Track descriptions, bios |
| Comment Text | Interstate | 14pt | 400 | 1.4 | 0pt | Inline waveform comment body |
| Meta / Count | Interstate | 12pt | 400 | 1.3 | 0pt | "12.3K plays", "284 comments" |
| Label (UPPER) | Interstate | 11pt | 700 | 1.2 | 0.6pt | "REPOSTED", "PROMOTED" tags |
| Button (Primary) | Interstate | 15pt | 700 | 1.0 | 0.2pt | Orange "Follow" / "Play" pill |
| Button (Secondary) | Interstate | 14pt | 500 | 1.0 | 0pt | Outline "Following" pill |
| Tab Label | Interstate | 10pt | 500 | 1.0 | 0.2pt | Bottom tab bar labels |
| Timestamp | Interstate | 11pt | 500 | 1.0 | 0pt | Waveform position 1:23 / -2:09, tabular |

### Principles
- **Weights concentrated at 400 / 500 / 700**: Regular, medium, bold — no thin, no black
- **Tabular numerals everywhere a number can change**: timestamps, play counts, comment counts
- **Negative tracking only on titles** (-0.1 to -0.3pt); body and meta sit at 0pt
- **All-caps reserved for system tags** ("REPOSTED", "PROMOTED") at 11pt 700 with +0.6pt tracking
- **Dynamic Type respected on titles, artists, body**; timestamps and tab labels stay fixed (layout-sensitive)

## 4. Component Stylings

### Buttons

**Primary Play FAB (Now Playing)**
- Shape: Circle, diameter 64pt (56pt on compact headers)
- Background: `#FF5500`
- Icon: SF Symbol `play.fill` / `pause.fill` in `#FFFFFF`, 26pt
- Pressed: scale 0.93, background `#E64A00`, with `.impactOccurred(.medium)` haptic
- Shadow: `rgba(255,85,0,0.32) 0 6px 20px`

**Primary Pill (Follow / Play)**
- Background: `#FF5500`
- Text: `#FFFFFF`
- Padding: 9pt vertical, 24pt horizontal
- Corner radius: 4pt (SoundCloud uses a *slightly* rounded rect, not a full pill)
- Font: Interstate 15pt weight 700, +0.2pt tracking
- Pressed: `#E64A00`, scale 0.97

**Outline Pill ("Following")**
- Background: transparent
- Text: `#333333` light / `#FFFFFF` dark
- Border: 1pt solid `#999999`
- Padding: 9pt vertical, 20pt horizontal
- Corner radius: 4pt
- Font: Interstate 14pt weight 500
- Pressed: border `#FF5500`, text `#FF5500`

**Icon Actions (Like, Repost, Comment, Share, More)**
- Size: 20pt glyph, 44pt hit area
- Default: `#999999`
- Active: `#FF5500` (like filled, repost active)
- Layout: a horizontal action bar directly under the waveform, evenly spaced
- Like / Repost show a count label (12pt 400 `#999999`) trailing the glyph

**Text Button ("See all", "More")**
- Font: Interstate 14pt weight 700
- Color: `#999999`
- No underline, 44pt hit area

### Cards & Containers

**Track Row (Feed / Library)**
- Height: 72pt
- Layout: 56pt × 56pt rounded album thumbnail (4pt radius) → stacked uploader (14pt 400 `#999999`) + title (16pt 500) → trailing miniature waveform strip (compact, 28pt tall, last 40% of row width) + ellipsis
- Background: canvas; pressed: `#F2F2F2` (light) / `#262626` (dark)
- Currently-playing row: title turns `#FF5500`, the mini-waveform animates its progress sweep

**Playlist / Album Tile (Horizontal shelf)**
- Width: 150pt
- Aspect: 1:1 square art, 4pt corner radius
- Gap between tiles: 14pt
- Structure: art → title (15pt 500, 2-line clamp) → meta (12pt 400 `#999999`, "Playlist · 24 tracks")
- Tap: scale 0.97 with light haptic

**Now Playing (Full Screen)**
- Square cover art centered, ~300pt × 300pt, 6pt corner radius, soft shadow `rgba(0,0,0,0.18) 0 12px 32px`
- Track title (22pt 700) + uploader (14pt 400 `#999999`) below art
- **The orange waveform** spans full width below the title — interactive scrubber + comment timeline (see Distinctive)
- Transport row: shuffle · previous · 64pt orange Play FAB · next · repeat
- Action bar: like (count) · repost (count) · comment · share · more
- "Up next" handle at the bottom — drag up to reveal the queue list

**Comment Sheet**
- Bottom sheet, 12pt top corner radius, surface `#F2F2F2` / `#262626`
- Each comment: 28pt circular avatar + name (14pt 500) + timestamp chip (11pt 500 orange, e.g. "@1:24") + body (14pt 400)
- Tapping a timestamp chip seeks the waveform to that exact position

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(255,255,255,0.94)` light / `rgba(26,26,26,0.94)` dark, `.regularMaterial` blur
- Tabs: Home, Search, Library, Upload, Profile
- Icon size: 24pt (Upload is a 28pt prominent center glyph)
- Active color: `#FF5500`; inactive: `#999999`
- Labels: Interstate 10pt weight 500, shown always
- Active icon: filled SF Symbol variant; inactive: outlined

**Now Playing Mini Bar**
- Sits directly above the tab bar, full width, 52pt tall
- Background: `#F2F2F2` light / `#262626` dark
- A 2pt orange progress line is pinned to the very top edge of the mini bar (the only always-visible progress indicator)
- Left: 36pt square art (4pt radius); center: title 13pt 600 + uploader 11pt 400 `#999999`
- Right: `play.fill` / `pause.fill` 20pt + skip-next 20pt
- Tap: expands to full Now Playing with a shared-element transition

**Top Nav (Profile / Feed Entry)**
- Height: 44pt + safe area
- Avatar (28pt circular) top-left, screen title centered (22pt 700), search/notifications top-right
- Filter chips row below on Library (32pt height pills)

### Input Fields

**Search Bar**
- Background: `#F2F2F2` light / `#262626` dark
- Height: 40pt
- Corner radius: 4pt
- Leading SF Symbol `magnifyingglass`, 18pt, `#999999`
- Placeholder: "Search", 16pt 400 `#999999`
- Focus: 1pt `#FF5500` bottom border, cursor orange
- Text in field: `#333333` light / `#FFFFFF` dark

**Filter Chip**
- Background: `#F2F2F2` default / `#FF5500` selected (light); `#262626` default / `#FF5500` selected (dark)
- Text: `#333333` default / `#FFFFFF` selected
- Padding: 7pt vertical, 16pt horizontal
- Corner radius: 4pt
- Font: Interstate 14pt weight 500
- Examples: "All", "Tracks", "People", "Playlists"

### Distinctive Components

**The Commentable Waveform Scrubber**
- Full-width amplitude graph rendered as vertical bars (1.5pt bar width, 1pt gap)
- Played portion (left of playhead): solid `#FF5500`
- Unplayed portion (right): `#FF7700` at reduced opacity (~45% light / ~35% dark)
- Height: ~64pt on Now Playing, ~28pt on track rows
- Playhead: a 1pt vertical `#333333` line (light) / `#FFFFFF` (dark) at the current position
- Drag anywhere on the waveform to seek; tap to jump; haptic tick on scrub
- **Inline comment avatars**: 20pt circular avatars pinned along the baseline at the exact second a comment was left; as the playhead sweeps past one, the comment text fades in above the waveform for ~3s

**Repost Action**
- A circular-arrows glyph; tapping opens a confirmation ("Repost to your followers?")
- Active state: glyph + count turn `#FF5500`; the track gains a "REPOSTED" tag (11pt 700 upper) in the feed

**"Up Next" Queue**
- A draggable handle at the bottom of Now Playing
- Drag up: a sheet with the upcoming queue as 56pt rows (drag-handle to reorder)
- The currently-playing track is pinned at the top with its mini-waveform animating

**Waveform Progress Sweep (Motion)**
- The played-portion fill advances continuously left-to-right in sync with playback
- On the track-row mini-waveform, the same sweep runs at small scale
- If paused, the sweep freezes at the playhead

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 56, 64
- Standard margin: 16pt horizontal, 20pt between feed cards
- Track row internal padding: 8pt vertical, 16pt horizontal

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal shelves: 16pt left inset, last tile peeks at the right edge
- Playlist grid: 2-column square grid, 12pt gap

### Whitespace Philosophy
- **Dense, scannable feed**: 72pt rows pack many tracks per screen — SoundCloud is a discovery firehose
- **The waveform breathes**: full-width, generous vertical room on Now Playing — it is the hero
- **Comment avatars never crowd**: they sit on the waveform baseline, not over the bars

### Border Radius Scale
- Square (0pt): rarely used
- Soft (4pt): album thumbnails, cards, pills, chips, search field — SoundCloud's signature *slight* rounding
- Comfortable (6pt): Now Playing cover art
- Sheet (12pt): bottom sheets, comment sheet, queue sheet
- Circle (50%): play FAB, avatars, commenter dots, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, feed cards, canvas |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 4px` (light) / none (dark) | Subtle lift on tiles |
| Cover Art (Level 2) | `rgba(0,0,0,0.18) 0 12px 32px` | Now Playing centered cover |
| Play FAB (Level 2) | `rgba(255,85,0,0.32) 0 6px 20px` | The orange Play button glow |
| Sheet (Level 3) | `rgba(0,0,0,0.24) 0 -12px 40px` | Comment sheet, queue sheet |
| Tab Bar Material | `.regularMaterial` over 94%-opaque canvas | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On light, shadows are soft and shallow (0.06–0.18 opacity) — the white canvas only needs a hint of lift. The one expressive shadow is the orange glow under the Play FAB, which uses the brand hue itself at 32% to make the button feel energized. On dark, surface elevation is communicated by the `#262626` step rather than shadow.

### Motion
- **Waveform progress sweep**: continuous left-to-right fill tied to playback position (the signature motion)
- **Play FAB tap**: scale 0.93 → 1.0 spring (damping 0.7), `.impactOccurred(.medium)` haptic
- **Inline comment reveal**: comment text fades + slides up 6pt over 200ms as the playhead passes its avatar
- **Like tap**: heart fill + scale bounce 1.0 → 1.2 → 1.0 over 280ms
- **Mini-bar → Full player**: shared-element 0.3s spring, cover art is the anchor
- **Scrub**: light haptic tick every ~5% as you drag the waveform

## 7. Do's and Don'ts

### Do
- Make the waveform the interactive scrubber — it is the soul of SoundCloud
- Use SoundCloud Orange (`#FF5500`) as the only accent — play, progress, like, repost, active tab
- Fill the played portion of the waveform solid orange, fade the unplayed portion
- Pin inline comment avatars to exact timestamps on the waveform baseline
- Use a 2pt orange progress line on the mini-bar — the always-visible progress cue
- Keep corners at 4pt — SoundCloud's *slight* rounding, not full pills
- Use Interstate (Inter fallback) at weights 400/500/700 only
- Keep SoundCloud Orange identical in light and dark — only invert canvas/surface/text
- Use tabular numerals for timestamps and play counts

### Don't
- Don't render a generic linear progress bar — the waveform replaces it
- Don't add a second accent color — orange is the entire brand
- Don't use full-pill (500pt) buttons — SoundCloud buttons are 4pt rounded rects
- Don't hide the comment avatars — inline timestamped comments are the distinctive social layer
- Don't use heavy drop shadows on the light canvas — keep lift subtle (≤0.18)
- Don't use thin or black font weights — Interstate sits at 400/500/700
- Don't desaturate the orange in dark mode — `#FF5500` reads cleanly on `#1A1A1A`
- Don't drop the mini-bar during active playback
- Don't animate the waveform sweep with easing — it advances linearly with time

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 130pt shelf tiles; Now Playing cover 260pt |
| iPhone 13/14/15 | 390pt | Standard 150pt tiles; cover 300pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | 170pt tiles; Now Playing cover 340pt; taller waveform |
| iPad | 768pt+ | 3-column grid; waveform spans a max 720pt centered column |

### Dynamic Type
- Track titles, uploader names, body, comments: full scale
- Waveform timestamps: fixed 11pt (never scales — layout-sensitive)
- Play FAB: icon size fixed (26pt)
- Tab labels: fixed 10pt

### Orientation
- Home / Search / Library / Profile: **portrait-locked**
- Now Playing: **portrait-locked** (the waveform is tuned for portrait width)

### Touch Targets
- Play FAB: 64pt — unmissable
- Track rows: 72pt height, full-row tappable
- Waveform: full-width drag surface, ≥64pt tall on Now Playing
- Action icons: 20pt glyph, 44pt hit area
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: safe area honored on nav (Dynamic Island clears the title)
- Bottom: mini-bar floats above tab bar; both respect the home indicator
- Sides: 16pt content insets; waveform extends edge-to-edge within the 16pt margin

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Surface (light): `#F2F2F2`
- Divider (light): `#E5E5E5`
- Canvas (dark): `#1A1A1A`
- Surface (dark): `#262626`
- Divider (dark): `#333333`
- Text primary: `#333333` light / `#FFFFFF` dark
- Text secondary: `#999999`
- SoundCloud Orange (accent): `#FF5500`
- Orange light (unplayed waveform): `#FF7700`
- Orange pressed: `#E64A00`
- Error red: `#E5484D`

### Example Component Prompts
- "Create a SwiftUI SoundCloud track row: 72pt height, 56×56pt album art (4pt corner radius) leading, stacked uploader 'flume' in Interstate 14pt weight 400 #999999 above title 'Never Be Like You' in 16pt weight 500 #333333. Trailing: a compact orange waveform strip (28pt tall, ~40% of row width) with the played portion solid #FF5500 and unplayed #FF7700 at 45%, then a 20pt ellipsis. Pressed background #F2F2F2."
- "Build the SoundCloud Now Playing play FAB: 64pt circular button, #FF5500 background, SF Symbol 'play.fill' 26pt in #FFFFFF, shadow rgba(255,85,0,0.32) 0 6px 20px. Pressed: scale 0.93 with medium haptic, background #E64A00."
- "Design the SoundCloud commentable waveform scrubber: full-width amplitude graph of vertical bars (1.5pt wide, 1pt gap), 64pt tall. Played portion (left of playhead) solid #FF5500; unplayed #FF7700 at 45% opacity. 1pt #333333 playhead line. Pin 20pt circular commenter avatars along the baseline at their timestamps; when the playhead passes one, fade the comment text in above the waveform for 3s. Drag to seek with a light haptic tick every 5%."
- "Create the SoundCloud Now Playing mini bar: above the tab bar, 52pt tall, background #F2F2F2. A 2pt #FF5500 progress line pinned to the top edge. 36×36pt art (4pt radius) left, title 13pt weight 600 + uploader 11pt weight 400 #999999, trailing play/pause 20pt + skip-next 20pt. Tap expands to full Now Playing."
- "Build a SoundCloud comment sheet row: 28pt circular avatar, name 'maya' in Interstate 14pt weight 500, an orange timestamp chip '@1:24' (11pt weight 500 #FF5500), and body text in 14pt weight 400. Tapping the timestamp chip seeks the waveform to 1:24."

### Iteration Guide
1. The waveform replaces every progress bar — make it the scrubber and the comment timeline
2. SoundCloud Orange (`#FF5500`) is the only accent — play, progress, like, repost, active tab
3. Played waveform = solid orange; unplayed = `#FF7700` faded — the sweep advances linearly with time
4. Corners are 4pt — slight rounding, never full pills
5. Inline comment avatars pinned to timestamps are the distinctive social layer — don't omit them
6. Typography: Interstate (Inter fallback), weights 400/500/700, tabular numerals
7. Light and dark share the exact same orange — only canvas/surface/divider/text invert
8. Shadows are subtle on light (≤0.18); the one expressive shadow is the orange Play-FAB glow
