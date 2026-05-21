# Design System Inspiration of Peloton (iOS)

## 1. Visual Theme & Atmosphere

Peloton's iOS app is a **boutique fitness studio in pure black**. The entire experience is engineered to feel like you walked into a dim, premium spin studio: the canvas is `#000000` (true black, never charcoal), the chrome recedes, and the content that pops is cinematic — full-bleed class thumbnails shot like film posters, an instructor's face mid-cue, the studio video feed when a class is playing. The single brand color, **Peloton Red** (`#DF1E2E`), is rationed deliberately: it marks the live `LIVE` badge, the primary "Take Class" button, the output ring, and *your* row in the leaderboard. Everything else is monochrome — white text, gray metadata, black surfaces — so the red and the photography do all the emotional work.

The app has two signature surfaces. The first is **class browse**: a vertically scrolling feed of class cards, each a 14pt-rounded tile with a 150pt cinematic thumbnail (live classes wear a pulsing red `● LIVE` badge top-left, on-demand classes a duration badge), then an uppercase discipline eyebrow in red, a heavy class title, the instructor name, and a meta line (rating, takes, work). Above the feed sits a horizontally scrolling row of filter pills — the selected pill inverts to a solid white fill with black text, Peloton's signature "selected = white chip" pattern. The second signature surface is the **in-class metrics screen**: while a ride/run plays, the bottom of the screen carries a large **output ring** (a thick red arc on a dark track with the total kJ in 900-weight numerals at its center), a column of live metrics (Cadence, Resistance, Output, Heart Rate — each in its own fixed color), and the **real-time leaderboard** — a scrolling list of riders with rank, avatar, name, and output, with *your* row highlighted in a translucent red wash and your output in red.

Typography is **Inter**, used heavily and confidently. Peloton sets numbers huge and black-weight (the output number can be 44pt/900), titles bold and tight (-0.6 to -1pt tracking), discipline eyebrows small uppercase red, and metadata in muted gray. Numerals are tabular so the leaderboard and metric column never jitter as values tick. The voice is energetic and editorial — this is a brand that sells motivation, and the type carries that intensity: tight, heavy, confident.

Chrome is minimal and dark. The bottom tab bar (Home, Classes, Schedule, Profile) has no tint pill — the active tab is simply white. Cards are flat dark tiles separated by spacing, with depth coming from the photography and surface-lightness steps rather than drop shadows. There is **no light mode** — Peloton is always studio-black, because the product *is* a dark room with a bright screen and a red brand mark.

**Key Characteristics:**
- Pure-black studio canvas (`#000000`) — never charcoal; cinematic content bleeds edge-to-edge
- Single brand color: Peloton Red `#DF1E2E` (brightened to `#FF4B57` for text/active on black)
- Cinematic class cards — 150pt film-poster thumbnail + pulsing red `● LIVE` badge / duration badge
- "Selected = white chip" — active filter pill inverts to solid white with black text
- In-class output ring — thick red arc on dark track, total kJ in 900-weight numerals at center
- Live metric column — Cadence / Resistance / Output / Heart Rate, each a fixed semantic color
- Real-time leaderboard — rank · avatar · name · output, *your* row washed translucent red
- Inter set heavy — huge black-weight numbers, tight bold titles, small uppercase red eyebrows
- Tabular numerals so leaderboard and metrics never jitter
- Pill-shaped primary buttons ("Take Class" red, "Start Ride" white)
- 4-tab bottom bar, no tint pill — active is white
- No light mode — always studio-black by design

## 2. Color Palette & Roles

### Primary (Interactive)
- **Peloton Red** (`#DF1E2E`): The single brand color — LIVE badge, primary CTA, output ring, your leaderboard row, selected accents. Used literally `#DF1E2E` for fills.
- **Peloton Red (on black)** (`#FF4B57`): Brightened tint for *text* and active glyphs on `#000000` — clears WCAG AA where `#DF1E2E` text would fail.
- **Peloton Red Pressed** (`#B81825`): Pressed state for the red primary button.
- **White Chip** (`#FFFFFF`): Selected filter pill / secondary CTA — solid white fill, black text.

### Canvas & Surfaces (Dark — the only mode)
- **Studio Black** (`#000000`): App background. True black, always.
- **Surface 1** (`#121212`): Cards, class tiles, metric cards — the first lift off black.
- **Surface 2** (`#1C1C1E`): Unselected pills, nested rows, inputs.
- **Surface 3** (`#262629`): Ring/bar tracks, pressed surfaces.
- **Divider** (`#2C2C2E`): 1px hairlines between leaderboard rows and metric rows.

### Text
- **Text Primary** (`#FFFFFF`): Titles, metric values, class names.
- **Text Secondary** (`#B5B5BA`): Instructor names, body, secondary copy.
- **Text Tertiary** (`#7A7A80`): Meta line, units, captions, disabled.

### Functional / In-Class Metric Colors (fixed meaning, theme-invariant)
Each live metric owns a color so a glance reads instantly. These never restyle.

| Role | Color | Meaning |
|------|-------|---------|
| Output / Power | `#DF1E2E` | Output ring, watts (the hero metric) |
| Cadence | `#E5402A` | RPM (cycling) / SPM (tread) |
| Resistance / Incline | `#F0A030` | Bike resistance, tread incline |
| Heart Rate | `#FF4B57` | Live BPM (red family) |
| Strive Score / HR Zones | `#3DB8E0` | Strive Score, HR-zone gauge (blue) |
| Personal Record | `#2ECC71` | PR celebration, goal hit |
| Streak / Milestone | `#F0A030` | Streak flame, badge earned |

### Semantic
- **Live** (`#DF1E2E`): The pulsing LIVE badge — Peloton Red, always.
- **Success / PR** (`#2ECC71`): Personal-record burst, goal complete.
- **Warning** (`#F0A030`): Low-battery sensor, paused class.
- **Error** (`#DF1E2E`): Errors, destructive actions (red doubles as error).
- **Track / Empty** (`#262629`): Unfilled portion of any ring or bar.

## 3. Typography Rules

### Font Family
- **All text**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist sans with excellent UI hinting and a deep weight range; Peloton sets it heavy and tight. On iOS, falls back to SF Pro when Inter isn't bundled.
- **Numerals**: Inter with **tabular figures** enabled (`tnum`) — leaderboard ranks/outputs and the live metric column must not jitter as values tick.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Output Number | Inter | 44pt | 900 | 1.0 | -1.0pt | The hero metric inside the output ring |
| Screen Title | Inter | 32pt | 900 | 1.1 | -0.6pt | "Cycling", large nav title |
| Hero Title | Inter | 26pt | 800 | 1.15 | -0.5pt | Class-detail title |
| Section | Inter | 22pt | 800 | 1.2 | -0.4pt | "Trending Classes", "Just Added" |
| Class Title | Inter | 17pt | 800 | 1.2 | -0.3pt | Card class name |
| Metric Value | Inter | 18pt | 800 | 1.1 | 0pt | In-class metric value (tabular) |
| Leaderboard Output | Inter | 14pt | 800 | 1.0 | 0pt | Output column (tabular) |
| Body | Inter | 15pt | 600 | 1.5 | 0pt | Descriptive copy, list titles |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "12,408 taken · 90s of work" |
| Eyebrow / Discipline | Inter | 12pt | 700 | 1.2 | 0.6pt (UPPERCASE) | Red discipline label above title |
| Metric Label | Inter | 11pt | 600 | 1.2 | 0.4pt (UPPERCASE) | "CADENCE", "RESISTANCE" |
| Caption | Inter | 11pt | 600 | 1.3 | 0pt | "Live in 4 minutes" |
| Button | Inter | 15pt | 700 | 1.0 | 0.2pt | Primary / white / outline buttons |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Badge | Inter | 10pt | 800 | 1.0 | 0.6pt (UPPERCASE) | LIVE / NEW / duration badges |

### Principles
- **Heavy and tight**: Titles are 800–900 weight with negative tracking (-0.3 to -1pt) — Peloton's type is confident and energetic, never light.
- **The metric is the hero**: In-class numbers (output, cadence) are the largest, heaviest elements; their labels are small uppercase whispers.
- **Tabular for anything that ticks**: Leaderboard outputs, the live metric column, timers — always tabular figures so nothing jitters.
- **Discipline eyebrow in red**: The uppercase discipline label above a class title is the one place a small text element is brand-red (`#FF4B57` on black).
- **Color stays functional**: Hierarchy is built from weight and size; color is reserved for brand-red and the fixed metric semantics.
- **Dynamic Type on prose, fixed on the cluster**: Titles, body, meta scale; the output number, metric values/labels, leaderboard, tab labels, and badges are layout-pinned.

## 4. Component Stylings

### Buttons

**Primary Button (Take Class / Start)**
- Shape: Full pill, 500pt corner radius
- Background: `#DF1E2E` (Peloton Red)
- Text: `#FFFFFF`, Inter 15pt weight 700
- Padding: 15pt vertical, 30pt horizontal
- Pressed: background `#B81825` + scale 0.98
- Disabled: background `#262629`, text `#7A7A80`
- Full-width variant on detail screens: 100% width, 54pt tall

**White Button (Start Ride / secondary primary)**
- Background: `#FFFFFF`
- Text: `#000000`, Inter 15pt weight 700
- Pill, 500pt radius, same padding as primary
- Pressed: background `#E5E5E7` + scale 0.98

**Outline Button (Add to Stack)**
- Background: transparent
- Border: 1.5pt `rgba(255,255,255,0.4)`
- Text: `#FFFFFF`, Inter 14pt weight 700
- Pill, 500pt radius, 13pt × 24pt padding
- Pressed: background `rgba(255,255,255,0.08)`

**Text Button (See All / inline)**
- Background: none
- Text: `#FF4B57`, Inter 14pt weight 700

**Icon Button (header action)**
- 20pt glyph in 44pt hit area
- Default `#FFFFFF`; pressed 60% opacity
- Stroke-style line icons, 2pt stroke

### Core Atoms

**Class Card** (the browse unit)
- Background: `#121212`, 14pt corner radius, 1px `#2C2C2E` border
- Thumbnail: 150pt tall cinematic image (here gradient-simulated), full card width, top corners follow card radius
- Live badge: top-left, `#DF1E2E` fill, white text, 5pt radius, pulsing white dot + "LIVE" in Inter 10pt 800 uppercase
- Duration badge: top-right, `rgba(0,0,0,0.6)` fill, white text, "45 min"
- Body padding: 14pt × 16pt
- Discipline eyebrow: Inter 12pt 700 uppercase `#FF4B57`
- Class name: Inter 17pt 800 `#FFFFFF`
- Instructor: Inter 14pt 600 `#B5B5BA`
- Meta: Inter 12pt `#7A7A80` with a gold `#F0A030` star glyph

**Filter Pill**
- Unselected: `#1C1C1E` fill, 1px `#2C2C2E` border, `#B5B5BA` text
- Selected: `#FFFFFF` fill, no border, `#000000` text (the signature white-chip)
- Inter 13pt 600, 8pt × 16pt padding, 500pt radius

**Output Ring** (the in-class hero)
- Track: `#262629`, 9pt stroke
- Progress arc: Peloton Red `#DF1E2E`, 9pt stroke, round cap, starts at -90°
- Center: total kJ value in Inter 26–44pt 900 `#FFFFFF`, label "TOTAL kJ" in Inter 9pt 700 uppercase `#7A7A80`

**Metric Row (in-class)**
- Label: Inter 11pt 600 uppercase `#B5B5BA`, +0.4 tracking
- Value: Inter 18pt 800 tabular, in the metric's fixed color (Cadence `#E5402A`, Resistance `#F0A030`, Output `#FF4B57`, HR white)
- Optional unit suffix: Inter 11pt `#7A7A80`
- Rows divided by 1px `#2C2C2E`

**Leaderboard Row**
- Rank: Inter 14pt 800 tabular `#B5B5BA` (your row: `#FF4B57`)
- Avatar: 30pt circle, gradient placeholder
- Name: Inter 14pt 600 `#FFFFFF`
- Output: Inter 14pt 800 tabular `#FFFFFF`
- Your row: background `rgba(223,30,46,0.12)`, rank + accents in red
- Rows divided by 1px `#2C2C2E`

**Chip / Tag**
- Background: `#1C1C1E`, 1px `#2C2C2E`
- Text: Inter 12pt 600 `#FFFFFF`
- 500pt radius

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(18,18,18,0.96)` with blur, 0.5pt top border `#2C2C2E`
- Tabs (4): Home, Classes, Schedule, Profile
- Icon: 22pt; **no tint pill**
- Active: glyph filled `#FFFFFF`, label `#FFFFFF`
- Inactive: glyph stroked `#7A7A80`, label `#7A7A80`
- Labels: Inter 10pt 600, always shown

**Top Header**
- Height: 44pt + safe area
- Leading: large screen title (Inter 22–32pt 800/900) or back chevron
- Trailing: search + filter glyphs, 20pt stroke

**Section Header (in-page)**
- Inter 22pt 800 `#FFFFFF`, 20pt top margin, 12pt bottom
- Optional trailing text button "See All" in `#FF4B57`

### Input Fields

**Search Bar**
- Height: 44pt
- Background: `#1C1C1E`
- Border: none; focus 1.5pt `#FF4B57`
- Corner radius: 500pt (pill)
- Leading magnifier 18pt `#7A7A80`
- Placeholder Inter 15pt `#7A7A80`

**Segmented Control (Live / On-Demand)**
- Track: `#1C1C1E`, 500pt radius
- Selected segment: `#FFFFFF` fill, black text (white-chip pattern)
- Unselected: `#B5B5BA` text
- Inter 13pt 700

**Class Filter Sheet**
- Bottom sheet, `#121212`, 20pt top radius
- Filter groups with white-chip pills; "Apply" red pill at bottom

### Distinctive Components

**Cinematic Class Thumbnail**
- 150pt tall, full card width, top corners 14pt
- Real product: instructor/studio photography with a darkened gradient overlay for text legibility
- A centered or bottom-right play triangle on hover/focus
- Live overlay: pulsing `● LIVE` red badge top-left

**Live LIVE Badge**
- `#DF1E2E` fill, white text "LIVE" Inter 10pt 800 uppercase, 5pt radius
- Leading 6pt white dot that pulses (opacity 1 → 0.4 → 1, 1.4s loop)
- Always Peloton Red — this is the brand's signature "now" signal

**Output Ring + Metric Column**
- Card `#121212`, 14pt radius, 1px border
- Left: 96pt output ring (red arc on `#262629` track, 900-weight kJ centered)
- Right: 4-row metric column (Cadence / Resistance / Output / Heart Rate), each value in its fixed color
- All values tabular, ticking live

**Real-Time Leaderboard**
- Header "Leaderboard · 1,284 riders" Inter 11pt 700 uppercase `#B5B5BA`
- Scrolling rows; your row pinned-highlighted with `rgba(223,30,46,0.12)` wash
- Rows reorder live with a 250ms ease-out position animation as outputs change

**Metric Ring (mini)**
- 84pt ring used for secondary live metrics (Output W, Cadence, HR)
- Track `#262629`, arc in the metric's fixed color, value 900-weight centered

**Personal Record Burst**
- On a PR, a `#2ECC71` ring pulse + confetti + "NEW PR!" Inter 22pt 900
- Brief, celebratory, 1.2s, with success haptic

**Instructor Header (class detail)**
- Full-bleed instructor photo with a black gradient foot
- Discipline eyebrow red, class title 26pt 900 white, instructor + duration meta
- Sticky red "Take Class" pill at bottom

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 20, 24, 32, 40, 48
- Card internal padding: 14pt vertical / 16pt horizontal
- Card-to-card gap: 18pt
- Screen side inset: 16pt
- Thumbnail height: 150pt (card) / full-bleed (detail hero)

### Grid & Container
- iPhone: 16pt side insets; class cards full content width
- iPad: content max-width 760pt centered; class browse becomes a 2-column grid
- In-class screen: output ring + metric column side-by-side; leaderboard scrolls below
- Filter pills: horizontal scroll, 8pt gap, no wrap

### Whitespace Philosophy
- **Cinematic content, quiet chrome**: thumbnails and the studio feed get maximum real estate; UI chrome is thin and dark so the imagery dominates
- **Tight type, generous tiles**: titles are visually dense (heavy, tight tracking) but cards are spaced 18pt apart so the feed breathes
- **The ring owns the screen**: in-class, the output ring is large and central — everything else orbits it
- **No decorative empty space**: spacing is structural; intensity comes from photography and red, not from emptiness

### Border Radius Scale
- Square (0pt): full-bleed studio video, dividers
- Small (5pt): LIVE/duration badges
- Standard (8pt): bottom-sheet inner chips, small tiles
- Card (14pt): class cards, metric cards, leaderboard card
- Comfortable (20pt): bottom sheets, modals
- Pill (500pt): all buttons, filter pills, search bar
- Circle (50%): rings, avatars, the LIVE dot

## 6. Depth & Elevation

Peloton is **flat with cinematic depth**. Perceived depth comes from photography and surface-lightness steps, not Material shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | `#000000`, no shadow | App canvas, studio video |
| Surface (Level 1) | `#121212`, no shadow, 1px `#2C2C2E` border | Class cards, metric cards |
| Raised (Level 2) | `#1C1C1E`, no shadow | Pills, nested rows, inputs |
| Floating (Level 3) | `#1C1C1E` + `rgba(0,0,0,0.7) 0 8px 28px` | Bottom sheets, dialogs, action menus |
| Scrim | `rgba(0,0,0,0.7)` | Behind sheets & modals; gradient foot on thumbnails for text legibility |

**Shadow Philosophy**: On pure black, drop shadows are invisible, so Peloton avoids them on content — depth is "this surface is lighter than black." The one heavily-used elevation tool is the **gradient scrim baked into every thumbnail**: a bottom-to-transparent black overlay that lifts white text off the photography. Only true floating layers (filter sheet, dialogs) get a soft `0 8px 28px` shadow plus the lighter `#1C1C1E` tone.

### Motion
- **Class card tap**: thumbnail scales 1.0 → 0.98 in 120ms, then push-transition to detail (300ms iOS-native)
- **LIVE dot pulse**: white dot opacity 1 → 0.4 → 1 on a 1.4s ease-in-out loop (the brand's heartbeat)
- **Output ring fill**: arc animates from 0 to value over 600ms ease-out at class start; thereafter follows live output with a 300ms ease-out tween
- **Live metric tick**: values count with tabular spacing (no layout shift); a brief color flash on a new segment cue
- **Leaderboard reorder**: rows shift position over 250ms ease-out as outputs change; your row stays visually anchored
- **PR burst**: `#2ECC71` ring pulse + confetti + "NEW PR!" scale-in (1.2s), success haptic
- **Pill select**: white fill cross-fades in over 150ms; text color swaps instantly
- **Filter sheet**: bottom sheet slides up 300ms ease-out; scrim fades in
- **Tab switch**: cross-fade 150ms; active glyph swaps stroke→fill instantly
- **Haptic**: light impact on pill select & tab change; medium impact on "Take Class"; success notification on PR / class complete
- **Reduce Motion**: LIVE dot stays solid (no pulse); ring sets final value with a 150ms crossfade; no card scale; leaderboard reorders instantly

## 7. Do's and Don'ts

### Do
- Use pure black `#000000` as the canvas — Peloton is a dark studio, always
- Keep Peloton Red `#DF1E2E` as the *only* brand color; brighten to `#FF4B57` for text/active on black
- Treat in-class metric colors (Cadence `#E5402A`, Resistance `#F0A030`, Strive/HR `#3DB8E0`) as fixed semantics — same hue every time
- Make class thumbnails cinematic and full-bleed with a baked gradient scrim for text legibility
- Pulse the white dot in the red LIVE badge — it's the brand heartbeat
- Use the white-chip pattern for selected pills (solid white fill, black text)
- Set numbers heavy and tabular — output 900-weight, leaderboard/metrics never jitter
- Highlight *your* leaderboard row with a translucent red wash and red accents
- Use pill-shaped buttons (red primary, white secondary)
- Keep the bottom tab bar pill-free — active is simply white

### Don't
- Don't use charcoal (`#121212` is a card, not the canvas) — the base is *true* black `#000000`
- Don't introduce a second brand color — there is only Peloton Red
- Don't recolor in-class metrics per theme — their color *is* the data
- Don't ship a light mode — Peloton is studio-black by design
- Don't set titles light or wide — Peloton type is 800–900 with negative tracking
- Don't drop-shadow content cards — depth is surface lightness + thumbnail scrim
- Don't add a Material/iOS tint pill behind the active tab — Peloton has none
- Don't make the LIVE badge any color but Peloton Red
- Don't use proportional figures for the leaderboard/metrics — they must be tabular
- Don't over-animate — motion is the LIVE pulse, ring fill, and leaderboard reorder; quiet elsewhere

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Thumbnail 130pt; output ring 84pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in header |
| iPhone 15/16 Pro Max | 430pt | Thumbnail 168pt; larger output ring |
| iPad (portrait) | 768pt | Class browse → 2-column grid; in-class metrics in a sidebar beside the video |
| iPad (landscape) | 1024pt+ | Studio video full-frame with metric ring + leaderboard overlaid right |

### Dynamic Type
- Scales: screen titles, hero/section titles, body, meta
- Fixed (layout-pinned): output number, in-class metric values/labels, leaderboard, tab labels, badges — these live in tight live-updating layouts
- The output number already dominates; do not additionally scale it with Dynamic Type

### Orientation
- Browse is portrait-first
- In-class supports landscape: video fills the frame, ring + leaderboard overlay the right edge
- iPad landscape mirrors the in-studio screen layout

### Touch Targets
- Tab bar icon: 22pt glyph, 44pt hit
- Class card: full-card tap, ≥ 200pt tall
- Filter pill: ≥ 36pt tall, full-pill tap
- Buttons: ≥ 48pt tall (primary 54pt full-width)
- Leaderboard row: full-row tap (opens that rider), ≥ 50pt tall

### Safe Area Handling
- Top: header respects safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; sticky "Take Class" pill sits above the indicator
- In-class: metric overlay respects safe area on all edges over the video
- Sides: 16pt content inset; thumbnails full-bleed within the card

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Surface 1 / 2 / 3: `#121212` / `#1C1C1E` / `#262629`
- Divider: `#2C2C2E`
- Peloton Red (fill): `#DF1E2E`
- Peloton Red (text/active on black): `#FF4B57`
- Peloton Red pressed: `#B81825`
- White chip / secondary CTA: `#FFFFFF` (black text)
- Text primary / secondary / tertiary: `#FFFFFF` / `#B5B5BA` / `#7A7A80`
- Cadence: `#E5402A`
- Resistance / Incline / Streak: `#F0A030`
- Strive / HR-zone: `#3DB8E0`
- PR / Success: `#2ECC71`
- Track / empty: `#262629`

### Example Component Prompts
- "Build a Peloton class card in SwiftUI: `#121212`, 14pt corner radius, 1px `#2C2C2E` border. Top a 150pt cinematic thumbnail (use a dark gradient placeholder) with a top-left red `● LIVE` badge (`#DF1E2E` fill, white 'LIVE' Inter 10pt 800 uppercase, 5pt radius, pulsing 6pt white dot) and a top-right `rgba(0,0,0,0.6)` duration badge '45 min'. Body padded 14×16: discipline eyebrow 'POWER ZONE RIDE' (Inter 12pt 700 uppercase `#FF4B57`), class name '45 min Power Zone Max' (Inter 17pt 800 white), instructor 'Olivia Amato' (Inter 14pt 600 `#B5B5BA`), and a meta line with a gold `#F0A030` star, '4.9 · 12,408 taken'."

- "Create the Peloton in-class output ring: a 96pt ring — `#262629` track at 9pt stroke, a `#DF1E2E` progress arc at 9pt stroke, round cap, starting -90°, ~70% filled, animating from 0 over 600ms ease-out at class start. Centered: total kJ '312' in Inter 26pt 900 white over 'TOTAL kJ' in Inter 9pt 700 uppercase `#7A7A80`."

- "Build the Peloton in-class metric column: four rows divided by 1px `#2C2C2E`. Each row: an uppercase label (Inter 11pt 600 `#B5B5BA` +0.4 tracking) on the left, a tabular value (Inter 18pt 800) on the right in the metric's fixed color — Cadence `#E5402A` (94), Resistance `#F0A030` (52), Output `#FF4B57` (218 W), Heart Rate white (148 bpm). Values tick live without layout shift."

- "Render the Peloton real-time leaderboard: header 'Leaderboard · 1,284 riders' (Inter 11pt 700 uppercase `#B5B5BA`). Each row, divided by 1px `#2C2C2E`: rank (Inter 14pt 800 tabular `#B5B5BA`), 30pt gradient avatar circle, name (Inter 14pt 600 white), output (Inter 14pt 800 tabular white) right-aligned. Highlight the 'You' row with a `rgba(223,30,46,0.12)` background and rank/accents in `#FF4B57`. Rows reorder over 250ms ease-out as outputs change."

- "Build the Peloton filter pill row: a horizontal scroll of pills, 8pt gap. Unselected pill: `#1C1C1E` fill, 1px `#2C2C2E` border, `#B5B5BA` Inter 13pt 600 text, 8×16 padding, 500pt radius. Selected pill: solid `#FFFFFF` fill, no border, `#000000` text — the signature white-chip. Selecting cross-fades the white fill in over 150ms."

- "Build the Peloton primary button: a full pill (500pt radius), `#DF1E2E` background, white Inter 15pt 700 text, 15pt × 30pt padding. Pressed: `#B81825` + scale 0.98. Provide a white variant (`#FFFFFF` bg, `#000000` text) for 'Start Ride', and an outline variant (transparent, 1.5pt `rgba(255,255,255,0.4)` border, white text) for 'Add to Stack'."

### Iteration Guide
1. Canvas is **pure black `#000000`** — always; Peloton is a dark studio and there is no light mode
2. Peloton Red `#DF1E2E` is the **only** brand color — fills use `#DF1E2E`, text/active on black uses brightened `#FF4B57`
3. Class thumbnails are **cinematic and full-bleed** with a baked gradient scrim; live classes wear a pulsing red `● LIVE` badge
4. Selected pills use the **white-chip** pattern — solid white fill, black text
5. In-class metric colors (Cadence `#E5402A`, Resistance `#F0A030`, Strive/HR `#3DB8E0`) are **fixed semantics** — never recolor
6. The **output ring** is the in-class hero — thick red arc, 900-weight kJ centered, fills from 0 on start
7. Numbers are **heavy and tabular** — output up to 44pt/900; leaderboard + metrics never jitter
8. Highlight *your* leaderboard row with a **translucent red wash** (`rgba(223,30,46,0.12)`) and red accents
9. Depth is **surface lightness + thumbnail scrim**, not drop shadows; buttons are **pills**
10. Motion is the **LIVE dot pulse (1.4s)**, **ring fill (600ms)**, and **leaderboard reorder (250ms)**; quiet otherwise
