# Design System Inspiration of Garmin Connect (iOS)

## 1. Visual Theme & Atmosphere

Garmin Connect's iOS app is a **data cockpit**. Where consumer fitness apps soften their numbers behind illustration and encouragement, Garmin treats the user as an athlete who came for the metrics — pace, VO₂ Max, Training Load, Body Battery, heart-rate zones — and arranges the entire interface as an instrument cluster around them. The app is dark-first: a **true-black canvas** (`#000000`) on which a single brand color, **Garmin Blue** (`#007CC3`), carries every interactive accent, route line, and progress arc. There is no second brand hue; color in Garmin Connect is overwhelmingly *functional* — the green→gold→orange→red heart-rate-zone ramp, the blue Body Battery gauge, the teal Intensity-Minutes bar — each color means a specific thing, and that meaning never changes across screens.

The signature surface is the **activity detail screen**: you tap a run, ride, or swim from the Activities list and land on a screen that opens with a glowing GPS route drawn on a dark slate map, followed by a tight grid of stat cells (Distance, Time, Avg Pace, Avg HR, Calories, Elevation Gain), then expandable cards for Splits, Heart Rate Zones, Training Effect, and weather. This is the moment the whole design system exists to serve: **maximum data density without clutter**, achieved through hairline dividers, condensed numerals, and ruthless label economy. The second pillar screen is **My Day** — a vertically scrolling dashboard of glanceable tiles (Steps, Body Battery, Sleep, Stress, Training Status, Intensity Minutes) that mirrors the watch's widget glances.

Typography does the heavy lifting that color refuses to. Garmin pairs **Roboto** (UI text, labels, body) with **Roboto Condensed** for every number that matters — the condensed face lets a value like `5:02 /km` or `1,248 m` sit on a single line inside a narrow stat cell without truncating or shrinking. Numerals are tabular so columns of splits align perfectly. Headings are heavy (700–900), labels are small uppercase eyebrows in muted gray, and the data values dominate the visual hierarchy: in a Garmin card the *number* is the hero and the label is a whisper above it.

Chrome is minimal and utilitarian. Navigation is a 4-tab bottom bar (My Day, Challenges, Calendar, More) with no tint pill — the active tab simply turns Garmin Blue. Cards are flat dark rectangles with 1px dividers, not floating shadowed objects; depth is communicated by surface lightness steps (`#000000` → `#121417` → `#1B1E22`), not by drop shadows. The aesthetic is closer to a Garmin watch face or a cockpit MFD than to a lifestyle app — precise, legible at a glance, and unapologetically dense.

**Key Characteristics:**
- True-black canvas (`#000000`) — dark-first, OLED-friendly, makes route lines and zone colors pop
- Single brand accent: Garmin Blue `#007CC3` (brightened to `#2A9FD6` on black for AA contrast)
- Functional color system — HR zones, Body Battery, Intensity Minutes each own a fixed hue with fixed meaning
- Roboto + Roboto Condensed pairing — condensed numerals keep dense metrics on one line
- Activity detail screen = glowing GPS route on dark map + tight stat grid + expandable analysis cards
- My Day dashboard = scrolling grid of glanceable watch-style tiles
- Hairline dividers (`#2A2E33`) and surface-lightness steps instead of drop shadows
- Tabular numerals so split tables and stat columns align
- Training Status / Body Battery / VO₂ Max as named, ring- or gauge-backed hero metrics
- 4-tab bottom bar, no tint pill — active tab is Garmin Blue glyph
- Uppercase micro-labels above big numbers — label whispers, value shouts

## 2. Color Palette & Roles

### Primary (Interactive)
- **Garmin Blue** (`#007CC3`): The single brand color — primary buttons, route lines, progress arcs, links, selected states. On true black, the literal `#007CC3` is used for fills (buttons, route stroke).
- **Garmin Blue (on dark)** (`#2A9FD6`): Brightened tint used for *text* and active tab glyphs on `#000000` — clears WCAG AA where `#007CC3` text would not.
- **Garmin Blue Pressed** (`#00689F`): Pressed state for the primary button.

### Canvas & Surfaces (Dark — primary mode)
- **True Black Canvas** (`#000000`): App background. Garmin uses literal black, not charcoal.
- **Surface 1** (`#121417`): Cards, stat grids, list rows — the first lift off black.
- **Surface 2** (`#1B1E22`): Tiles, nested cards, pressed list rows.
- **Surface 3** (`#23272C`): Track backgrounds (progress bars, gauges), input fields.
- **Divider** (`#2A2E33`): 1px hairlines between stat cells, list rows, sections.

### Canvas & Surfaces (Light — optional)
- **Light Canvas** (`#FFFFFF`): Optional light theme background.
- **Light Surface** (`#F4F5F7`): Cards, grids.
- **Light Divider** (`#E2E5E9`): Hairlines.
- **Light Text Primary** (`#1A1D21`): Body text on white.

### Text
- **Text Primary** (`#FFFFFF`): Metric values, titles on dark.
- **Text Secondary** (`#A6ACB3`): Labels, metadata, secondary copy on dark.
- **Text Tertiary** (`#6B7178`): Units, captions, disabled, axis scale numbers.
- **Text Primary (light)** (`#1A1D21`): Primary text on white.
- **Text Secondary (light)** (`#5A6068`): Labels on white.

### Functional / Data-Viz Colors (fixed meaning, theme-invariant)
These colors are semantic — they encode a metric and do **not** restyle for dark/light.

| Role | Color | Meaning |
|------|-------|---------|
| Body Battery | `#2EA8E0` | Energy reserve gauge & sparkline |
| Intensity Minutes | `#00A8A8` | Weekly moderate/vigorous minutes |
| Steps / Move | `#C8B560` | Steps ring, step bars |
| HR Zone 1 (Warm Up) | `#9AA0A6` | Very light effort |
| HR Zone 2 (Easy) | `#4CAF50` | Aerobic base |
| HR Zone 3 (Aerobic) | `#C8B560` | Tempo |
| HR Zone 4 (Threshold) | `#F0A030` | Hard |
| HR Zone 5 (Maximum) | `#E5402A` | Max effort |
| Stress (low) | `#4CAF50` | Calm |
| Stress (high) | `#F0A030` | Elevated |
| Route Line | `#007CC3` | GPS track on map (with glow) |

### Semantic
- **Success / Goal Met** (`#4CAF50`): Goal-complete checkmarks, positive deltas.
- **Warning** (`#F0A030`): Elevated stress, low Body Battery alert.
- **Error / Max** (`#E5402A`): Errors, destructive actions, max HR zone.
- **Track / Empty** (`#23272C`): Unfilled portion of any bar, gauge, or ring on dark.

## 3. Typography Rules

### Font Family
- **UI / Body / Labels**: `Roboto` (by Christian Robertson, Apache 2.0) — neutral, screen-optimized grotesque; Garmin's app text face. On iOS, falls back to SF Pro Text when Roboto isn't bundled.
- **Numerals / Metrics**: `Roboto Condensed` (Apache 2.0) — the condensed face is used for *every* meaningful number (pace, distance, HR, elevation, durations) so long values fit a narrow stat cell on one line. Tabular figures enabled.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', 'Helvetica Neue', Arial, sans-serif`
- **Numeral fallback**: `'Roboto Condensed', 'SF Pro Display', 'Helvetica Neue Condensed', sans-serif` with `font-variant-numeric: tabular-nums`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Roboto | 32pt | 900 | 1.15 | -0.6pt | "My Day", large nav title |
| Hero Metric | Roboto Condensed | 40pt | 700 | 1.0 | -0.5pt | Lead value (distance, total) |
| Activity Name | Roboto Condensed | 26pt | 700 | 1.1 | -0.2pt | "Morning Run" on detail screen |
| Section Heading | Roboto | 20pt | 700 | 1.2 | -0.1pt | "Heart Rate Zones", "Splits" |
| Stat Value | Roboto Condensed | 22pt | 700 | 1.1 | 0pt | Value inside a stat cell |
| Stat Value (large) | Roboto Condensed | 28pt | 700 | 1.0 | -0.2pt | Tile value on My Day |
| Body | Roboto | 16pt | 500 | 1.5 | 0pt | Descriptive copy, list titles |
| Body (regular) | Roboto | 16pt | 400 | 1.5 | 0pt | Secondary descriptive copy |
| Meta | Roboto | 14pt | 400 | 1.4 | 0pt | Timestamp, location, subtitles |
| Eyebrow / Label | Roboto | 12pt | 700 | 1.2 | 0.5pt (UPPERCASE) | Micro-label above a value |
| Stat Unit | Roboto | 11pt | 500 | 1.2 | 0pt | "km", "bpm", "/km" suffix |
| Caption | Roboto | 11pt | 500 | 1.3 | 0pt | "Last synced 4 min ago" |
| Button | Roboto | 15pt | 700 | 1.0 | 0.5pt (UPPERCASE) | Primary / outline buttons |
| Tab Label | Roboto | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Axis / Scale | Roboto | 10pt | 500 | 1.0 | 0pt | Chart axis, gauge endpoints |

### Principles
- **The number is the hero**: In any data card, the value uses the largest, heaviest type (Roboto Condensed 700); the label is a small uppercase gray eyebrow above it.
- **Condense the digits, not the words**: Numerals are Roboto Condensed so pace/distance/duration never wrap or auto-shrink; prose stays in regular Roboto.
- **Tabular figures everywhere numeric**: Split tables, stat columns, and HR-zone durations must align — always enable tabular numerals.
- **Uppercase micro-labels**: Field labels and eyebrows are 12pt 700 uppercase with +0.5pt tracking — they read as instrument labels.
- **Weight, not color, for hierarchy**: Garmin separates levels with 400 / 500 / 700 / 900 weight steps; color stays functional.
- **Dynamic Type on prose, fixed on the cluster**: Titles, body, and meta scale; stat values, units, tab labels, and axis text are layout-pinned.

## 4. Component Stylings

### Buttons

**Primary Button (Start / Save / Sync)**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#007CC3` (Garmin Blue)
- Text: `#FFFFFF`, Roboto 15pt weight 700, **UPPERCASE**, +0.5pt tracking
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#00689F` + scale 0.98
- Disabled: background `#23272C`, text `#6B7178`
- Full-width variant on forms: 100% width, 50pt tall

**Secondary / Outline Button**
- Background: transparent
- Border: 1.5pt `#2A9FD6`
- Text: `#2A9FD6`, Roboto 14pt weight 700, UPPERCASE, +0.5pt tracking
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 6pt
- Pressed: background `rgba(42,159,214,0.12)`

**Ghost / Tertiary Button**
- Background: `#1B1E22` (Surface 2)
- Border: 1pt `#2A2E33`
- Text: `#FFFFFF`, Roboto 14pt weight 500
- Corner radius: 6pt
- Pressed: background `#23272C`

**Text Button (inline action)**
- Background: none
- Text: `#2A9FD6`, Roboto 14pt weight 700, UPPERCASE, +0.5pt tracking
- Used for "View Splits", "See All", "Edit"

**Icon Button (nav action)**
- 24pt glyph in 44pt hit area
- Default `#FFFFFF`; pressed 60% opacity
- Stroke-style line icons, 2pt stroke

### Core Atoms

**Stat Cell** (the fundamental data unit)
- Background: `#121417`, separated from neighbors by 1px `#2A2E33` dividers (grid mortar)
- Label: Roboto 10pt 700 uppercase `#A6ACB3`, +0.5pt tracking
- Value: Roboto Condensed 22pt 700 `#FFFFFF`, tabular
- Unit: Roboto 11pt 500 `#6B7178`, inline after value
- Padding: 14pt vertical, 12pt horizontal
- Typically arranged 3-up in a grid; rows separated by the same hairline

**Glanceable Tile (My Day)**
- Background: `#121417`, 12pt corner radius, 1px `#2A2E33` border
- Leading: 18pt functional-color icon or mini ring
- Label: 10pt 700 uppercase `#A6ACB3`
- Value: Roboto Condensed 28pt 700 `#FFFFFF`
- Sub: 11pt 500 `#6B7178` ("goal 10,000")
- Tap → drill into the metric's detail screen

**Progress Bar / Gauge**
- Track: `#23272C`, 8pt tall, 4pt radius
- Fill: functional color (Body Battery gradient `#1E6FA8 → #2EA8E0`; goal bars solid)
- Endpoint labels: Roboto 10pt 500 `#6B7178`

**Ring (Training Status / goal)**
- Track circle: `#23272C`, 6pt stroke
- Progress arc: Garmin Blue `#007CC3` (or functional color), 6pt stroke, round cap, starts at -90°
- Center: value in Roboto Condensed 700, or a status word

**Chip / Pill**
- Background: `#1B1E22`, 1px `#2A2E33`
- Text: Roboto 12pt 600 `#FFFFFF`
- Corner radius: 500pt (full pill)
- Selected: background `#007CC3`, text `#FFFFFF`

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(18,20,23,0.96)` with blur, 0.5pt top border `#2A2E33`
- Tabs (4): My Day, Challenges, Calendar, More
- Icon: 22pt; **no tint pill**
- Active: glyph filled `#2A9FD6`, label `#2A9FD6`
- Inactive: glyph stroked `#6B7178`, label `#6B7178`
- Labels: Roboto 10pt 500, always shown

**Top Nav (Activity Detail)**
- Height: 44pt + safe area
- Leading: back chevron + parent label ("Activities"), Roboto 15pt 500 `#FFFFFF`
- Trailing: settings gear + share node icon, 19pt stroke glyphs
- No large-title collapse; the activity name lives in the body, not the bar

**Section Header (in-page)**
- Roboto 20pt 700 `#FFFFFF`, 20pt top margin, 12pt bottom
- Optional trailing text button "See All" in `#2A9FD6`

### Input Fields

**Text Field**
- Height: 48pt
- Background: `#1B1E22`
- Border: 1px `#2A2E33`; focus border 1.5px `#2A9FD6`
- Corner radius: 6pt
- Text: Roboto 16pt 500 `#FFFFFF`; placeholder `#6B7178`
- Leading icon (optional): 18pt `#A6ACB3`

**Segmented Control (e.g., Day / Week / 4 Weeks)**
- Track: `#1B1E22`, 6pt radius, 1px `#2A2E33`
- Selected segment: `#007CC3` fill, white text
- Unselected: `#A6ACB3` text
- Font: Roboto 13pt 700 uppercase

**Stepper / Goal Picker**
- Inline `−` / `+` round buttons (`#1B1E22`, 1px border), value in Roboto Condensed 20pt 700 between them

### Distinctive Components

**GPS Route Map**
- Dark slate basemap (`#1A2329 → #0C1216` gradient with faint 28pt grid)
- Route polyline: `#007CC3`, 4pt stroke, round caps/joins, soft glow `drop-shadow(0 0 4px rgba(0,124,195,0.6))`
- Start pin: `#4CAF50` dot, 3pt white ring
- End pin: `#E5402A` dot, 3pt white ring
- Optional km-split markers as small white ticks along the line
- 12pt corner radius, full-bleed minus 16pt side insets

**Stat Grid (Activity Detail)**
- 3-column grid, cells `#121417`, 1px `#2A2E33` mortar, outer radius 12pt
- Each cell = one Stat Cell atom
- Order convention: Distance, Time, Avg Pace, Avg HR, Calories, Elev Gain

**Training Status Card**
- `#121417`, 12pt radius, 1px border
- Leading: 52pt progress ring (blue arc on `#23272C` track)
- Status word in `#2A9FD6` (Productive / Maintaining / Peaking / Detraining / Overreaching), Roboto 16pt 700
- Sub: "VO₂ Max 52 · Load 388" in `#A6ACB3` 12pt

**Body Battery Gauge**
- Card with name + big value (Roboto Condensed 22pt 700 `#2EA8E0`)
- Horizontal bar: track `#23272C`, fill gradient `#1E6FA8 → #2EA8E0`
- Scale row 0 — Charged — 100 in `#6B7178` 10pt
- Detail view shows a 24h area chart in the same blue

**Heart Rate Zones**
- Per-zone row: zone label (Z2…Z5) `#A6ACB3` 11pt, horizontal track `#23272C`, fill in the zone color (Z2 green → Z3 gold → Z4 orange → Z5 red), trailing duration in tabular `#A6ACB3` 11pt
- Zone color is fixed and semantic — never recolor for theme

**Splits Table**
- Header row: Roboto 10pt 700 uppercase `#A6ACB3`
- Each split row: index, distance, pace bar (relative-length blue bar), elevation delta, HR — all tabular Roboto Condensed
- Fastest split's pace bar highlighted Garmin Blue; rows divided by 1px `#2A2E33`

**Goal Ring Cluster (My Day)**
- Stacked or side-by-side mini rings (Steps gold, Intensity Minutes teal, Floors), each on `#23272C` track, with the value Roboto Condensed centered

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Card internal padding: 16pt
- Stat-cell padding: 14pt vertical / 12pt horizontal
- Card-to-card gap: 18pt (on My Day) / hairline divider (within a grid)
- Screen side inset: 16pt

### Grid & Container
- iPhone: 16pt side insets; stat grids are 3-up edge-to-edge within the card
- Map: full content width, 168pt tall on detail screen, 12pt radius
- iPad: content max-width 760pt centered; My Day becomes a 2-column tile masonry
- Tiles: minimum 110pt wide; reflow 2-up (phone) / 3-4-up (iPad)

### Whitespace Philosophy
- **Dense but ruled**: Garmin packs many numbers per screen, but every group is bounded by a hairline or a card edge — density never becomes noise
- **Labels are quiet, values are loud**: vertical rhythm puts a tight 4pt gap between the uppercase label and its big value, then larger gaps between groups
- **Map breathes, data compresses**: the route map gets generous height; the stat grid below it is deliberately compact
- **No decorative whitespace**: spacing is structural, not stylistic — there are no large empty hero zones

### Border Radius Scale
- Square (0pt): full-bleed map edges (when edge-to-edge), dividers
- Subtle (4pt): progress-bar fills, track ends
- Standard (6pt): buttons, input fields, segmented control, chips-as-tags
- Card (12pt): cards, tiles, map container, stat-grid outer
- Comfortable (16pt): bottom sheets, modals
- Pill (500pt): filter chips, status pills
- Circle (50%): rings, gauges, profile avatar, map pins

## 6. Depth & Elevation

Garmin Connect is **flat and structural**. Depth comes from surface-lightness steps, not drop shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | `#000000`, no shadow | App canvas |
| Surface (Level 1) | `#121417`, no shadow, 1px `#2A2E33` border optional | Cards, stat grids, list rows |
| Raised (Level 2) | `#1B1E22`, no shadow | Tiles, nested cards, pressed rows |
| Floating (Level 3) | `#1B1E22` + `rgba(0,0,0,0.6) 0 8px 24px` | Bottom sheets, action menus, dialogs |
| Scrim | `rgba(0,0,0,0.6)` | Behind modals & sheets |

**Shadow Philosophy**: On true black, drop shadows are nearly invisible, so Garmin barely uses them — elevation is read as "this surface is lighter than the one behind it." Only genuinely floating layers (bottom sheets, popovers) get a soft `0 8px 24px` shadow plus the lighter surface tone. The GPS route line is the one element that uses a glow (`drop-shadow(0 0 4px rgba(0,124,195,0.6))`) — a deliberate signal that the track is "live" data.

### Motion
- **Activity open**: push transition 300ms iOS-native; the route line *draws on* over 700ms ease-out (stroke-dashoffset animation) once the map is visible
- **Stat reveal**: stat cells fade + 6pt rise, staggered 30ms each, 200ms ease-out, on first appearance
- **Ring / gauge fill**: arcs and bars animate from 0 to value over 600ms ease-out with a slight overshoot (spring, damping 0.85)
- **Card expand (Splits / HR Zones)**: chevron rotates 90° in 150ms; content height 0 → auto over 220ms ease-out
- **Tab switch**: cross-fade 150ms; active glyph swaps stroke→fill instantly, color tween 120ms
- **Pull to sync**: spinner; on success a brief `#4CAF50` "Synced" toast slides up 250ms then auto-dismisses
- **Number tick (live activity)**: live values count up with a 1-frame digit roll; tabular spacing prevents layout shift
- **Haptic**: light impact on tab change & goal-met; success notification haptic on sync complete; selection tick on segmented-control change
- **Reduce Motion**: route line appears instantly; rings/bars set final value with a 120ms crossfade; no rise/stagger

## 7. Do's and Don'ts

### Do
- Use true black `#000000` as the canvas — it is Garmin's signature and powers OLED + data pop
- Keep Garmin Blue `#007CC3` as the *only* brand accent; brighten to `#2A9FD6` for text/active on black
- Treat HR-zone / Body Battery / Intensity colors as fixed semantics — same hue everywhere, theme-invariant
- Set every meaningful number in Roboto Condensed with tabular figures
- Make the value the visual hero and the label a small uppercase gray eyebrow above it
- Draw the GPS route in Garmin Blue with a soft glow and green start / red end pins
- Separate dense data with 1px `#2A2E33` hairlines and surface-lightness steps, not shadows
- Animate rings/bars filling from 0 and the route drawing on, on first appearance
- Keep the bottom tab bar pill-free — active is simply the blue glyph
- Right-align numeric columns and use tabular figures so splits stack perfectly

### Don't
- Don't use charcoal (`#1A1A1A`) as the base — Garmin is *true* black `#000000`
- Don't introduce a second brand color — there is only Garmin Blue
- Don't recolor HR zones or Body Battery per theme — their color *is* the data
- Don't set metric numerals in a proportional font — values will wrap or shrink
- Don't add a Material/iOS tint pill behind the active tab — Garmin has none
- Don't drop-shadow content cards — depth is surface lightness on black
- Don't let the label outweigh the value — labels are 10–12pt uppercase whispers
- Don't use bright saturated decorative gradients on cards — gradients are reserved for data gauges
- Don't center-align long split tables — numeric data is right-aligned and tabular
- Don't animate aggressively — motion is purposeful (route draw, ring fill), 150–700ms ease-out

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Stat grid stays 3-up but value drops to 20pt; map 150pt tall |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in top nav |
| iPhone 15/16 Pro Max | 430pt | Map 190pt tall; tiles can show inline sparkline |
| iPad (portrait) | 768pt | My Day → 3-column tile masonry; activity detail centered max 760pt |
| iPad (landscape) | 1024pt+ | Map + stats side-by-side (map left 60%, stat grid right 40%) |

### Dynamic Type
- Scales: screen titles, section headings, body, meta
- Fixed (layout-pinned): stat values, stat units, tab labels, chart axis/scale text, eyebrow labels — these live in tight cells and must not reflow
- Condensed numerals already shrink visually; do not additionally scale them

### Orientation
- All screens support rotation
- iPad landscape: activity detail splits into map pane + scrollable analysis pane
- Charts (HR, Body Battery 24h) expand to fill width on rotation

### Touch Targets
- Tab bar icon: 22pt glyph, 44pt hit
- Stat cell / tile: full-cell tap, ≥ 64pt tall
- Card expand chevron: 20pt glyph, 44pt hit
- Buttons: ≥ 44pt tall (primary 50pt full-width)
- Map: pannable/zoomable with two-finger gestures; tap a split marker for that split

### Safe Area Handling
- Top: nav respects safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; full-width primary button sits above the indicator
- Map: extends under nothing — clipped within its rounded container with side insets
- Sides: 16pt content inset throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#000000`
- Surface 1 / 2 / 3: `#121417` / `#1B1E22` / `#23272C`
- Divider: `#2A2E33`
- Garmin Blue (fill): `#007CC3`
- Garmin Blue (text/active on black): `#2A9FD6`
- Garmin Blue pressed: `#00689F`
- Text primary / secondary / tertiary: `#FFFFFF` / `#A6ACB3` / `#6B7178`
- Body Battery: `#2EA8E0`
- Intensity Minutes: `#00A8A8`
- Steps: `#C8B560`
- HR Zone 2 / 3 / 4 / 5: `#4CAF50` / `#C8B560` / `#F0A030` / `#E5402A`
- Success / Warning / Error: `#4CAF50` / `#F0A030` / `#E5402A`

### Example Component Prompts
- "Build the Garmin Connect activity detail header in SwiftUI: top nav 44pt with a back chevron + 'Activities' label (Roboto 15pt 500 white) leading, and a settings gear + share-node icon trailing (19pt stroke glyphs). Below, an eyebrow 'RUNNING' (Roboto 12pt 700 uppercase `#2A9FD6` +0.5 tracking), then 'Morning Run' in Roboto Condensed 26pt 700 white, then 'Today at 6:42 AM · Cedar Loop Trail' in Roboto 14pt 400 `#A6ACB3`. Canvas is true black `#000000`."

- "Create the Garmin GPS route map: a 168pt-tall rounded (12pt) container, basemap a dark gradient `#1A2329 → #0C1216` with a faint 28pt grid overlay at 4% white. Draw the route as a `#007CC3` polyline, 4pt stroke, round caps, with a soft glow `drop-shadow(0 0 4px rgba(0,124,195,0.6))`. Place a green `#4CAF50` start pin and a red `#E5402A` end pin, each a dot with a 3pt white ring. Animate the route drawing on over 700ms ease-out via stroke-dashoffset."

- "Build the Garmin stat grid: a 3-column grid, each cell `#121417` separated by 1px `#2A2E33` mortar, outer radius 12pt. Cell = uppercase label (Roboto 10pt 700 `#A6ACB3` +0.5 tracking) above a value in Roboto Condensed 22pt 700 white with tabular figures, and an inline unit in Roboto 11pt 500 `#6B7178`. Populate: Distance 8.42 km, Time 42:18, Avg Pace 5:02 /km, Avg HR 156 bpm, Calories 612 C, Elev Gain 124 m. Stagger-fade the cells in with a 6pt rise on first appearance."

- "Build the Garmin Training Status card: `#121417`, 12pt radius, 1px `#2A2E33` border. Leading a 52pt ring — `#23272C` track at 6pt stroke, a `#007CC3` progress arc at 6pt stroke, round cap, starting -90°, ~75% filled, animating from 0 over 600ms ease-out. Right of it: 'TRAINING STATUS' eyebrow `#A6ACB3` 10pt 700 uppercase, 'Productive' in `#2A9FD6` Roboto 16pt 700, and 'VO₂ Max 52 · Load 388' in `#A6ACB3` 12pt."

- "Build the Garmin Body Battery card: name 'Body Battery' (Roboto 13pt 600 white) on the left, value '72' (Roboto Condensed 22pt 700 `#2EA8E0`) on the right baseline-aligned. Below, an 8pt-tall track `#23272C` (4pt radius) with a 72%-width fill using gradient `#1E6FA8 → #2EA8E0`. Under it a 3-point scale row '0 — Charged — 100' in Roboto 10pt 500 `#6B7178`. Bar fills from 0 over 600ms ease-out."

- "Render the Garmin heart-rate-zones card: for each zone a row with a fixed-width zone label (Z2…Z5, Roboto 11pt `#A6ACB3`), a flexible 10pt track `#23272C` (3pt radius) with a fill in the zone's fixed color (Z2 `#4CAF50`, Z3 `#C8B560`, Z4 `#F0A030`, Z5 `#E5402A`) sized by time-in-zone, and a trailing duration in tabular Roboto 11pt `#A6ACB3`. Zone colors are semantic — never recolor them for theme."

### Iteration Guide
1. Canvas is **true black `#000000`** — not charcoal; this is the Garmin signature and powers OLED + data legibility
2. Garmin Blue `#007CC3` is the **only** brand accent — fills use `#007CC3`, text/active on black uses brightened `#2A9FD6`
3. Every meaningful number is **Roboto Condensed, tabular** — values never wrap or shrink; prose stays in Roboto
4. The **value is the hero**; the label is a 10–12pt uppercase gray eyebrow above it
5. Data-viz colors (HR zones, Body Battery `#2EA8E0`, Intensity `#00A8A8`, Steps `#C8B560`) are **fixed semantics** — identical across themes
6. The **GPS route** is Garmin Blue with a soft glow; green `#4CAF50` start pin, red `#E5402A` end pin
7. Depth is **surface-lightness steps** (`#000000 → #121417 → #1B1E22`) + 1px `#2A2E33` hairlines — almost no shadows
8. The bottom tab bar has **no tint pill** — active is just the blue glyph + blue label
9. Density is **ruled**: every data group bounded by a card edge or hairline; right-align + tabular for split tables
10. Motion is purposeful — **route draws on (700ms)**, rings/bars **fill from 0 (600ms)**, stat cells stagger-rise; quiet otherwise
