# Design System Inspiration of Sleep Cycle (iOS)

## 1. Visual Theme & Atmosphere

Sleep Cycle is a bedside app, and every design decision flows from that single fact: the screen is the last thing you look at before sleeping and the first thing you see at dawn. The entire UI is wrapped in a calm indigo→night gradient — a vertical sweep from `#2A2D5A` at the top through `#3B4371` mid, settling into a deep `#14152E` canvas at the bottom. There is no pure black and no bright white field anywhere in the core experience; the screen *glows softly* rather than glaring, so it never jolts a half-asleep user at 3am. The aesthetic is "night sky from inside a dark bedroom" — deep, low-glare, quietly luminous.

The signature screen is the **sleep-analysis graph**: a full-width hypnogram that plots the night as a soft cyan wave riding over color-coded sleep stages (Awake, Light, Deep, REM). The wave is the brand. It is smooth, organic, and slightly glowing — drawn with a 2.5pt aqua stroke (`#7FE3F0`) over an indigo gradient fill that fades to nothing at the baseline. Around it sits the **sleep-quality ring** (a circular progress arc with a big numeric score 0–100), the **time-asleep hero stat**, and the **smart-alarm card** showing the wake window. These four elements — wave, ring, hero stat, alarm — define a Sleep Cycle screen.

The accent strategy is deliberate restraint. Against the tranquil indigo field there is exactly **one** bright color: Aqua (`#4FD1E6` / soft `#7FE3F0`). It marks the active sleep wave, the primary score ring, and the active tab — nothing else competes with it. Sleep stages get their own muted-but-distinct hues (Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake/Rose `#FF8FB1`). A warm Gold (`#FFC56B`) appears only as tiny "star" accents and streak badges. The primary button is an indigo→deep-sleep gradient (`#6C7BFF → #3D4ABF`) with a soft glow shadow.

Typography is **Nunito** — a rounded humanist sans whose soft terminals feel gentle and unclinical, the opposite of a hard data dashboard. Numbers are heavy (800–900 weight) because the sleep score, hours slept, and alarm time are the emotional payload of the app; supporting copy is light (400) and secondary in a muted lavender (`#A6A9D4`). The hierarchy is stat-led: a huge hero number, a section label above it, soft metadata below.

**Key Characteristics:**
- Indigo→night gradient shell (`#2A2D5A → #3B4371 → #14152E`) — dark-first, never pure black, soft glow not glare
- Signature sleep-analysis graph — a smooth glowing cyan hypnogram wave over color-coded stages
- Sleep-quality ring — circular arc with a big 0–100 numeric score, single aqua accent
- Time-asleep hero stat — the emotional headline (e.g. "7h 36m"), heaviest type on screen
- Smart-alarm card — shows the wake window; "wakes you in your lightest sleep"
- One bright accent only — Aqua `#4FD1E6`; everything else is muted indigo/lavender
- Sleep-stage color system — Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake `#FF8FB1`
- Rounded soft cards (16–20pt radius) floating on the gradient with low-opacity shadows
- Nunito rounded sans — heavy numerals, light body, gentle and unclinical
- Auto-dimming — brightness drops further during an active tracking session
- Minimal chrome — 5-tab bottom bar (Sleep / Journal / Alarm / Sounds / Profile), no top nav clutter

## 2. Color Palette & Roles

### Primary (Interactive)
- **Accent Indigo** (`#6C7BFF`): Primary button gradient start, active controls, light-sleep stage.
- **Accent Pressed** (`#5563E6`): Pressed state for primary actions.
- **Deep Sleep Indigo** (`#3D4ABF`): Primary button gradient end, deep-sleep stage fill, icon tile gradients.
- **Aqua** (`#4FD1E6`): THE accent — active sleep wave node, primary score ring, active tab, links.
- **Aqua Soft** (`#7FE3F0`): The sleep-wave stroke itself (slightly brighter than fill for the glow).

### Canvas & Surfaces (Dark — default)
- **Night Top** (`#2A2D5A`): Top of the screen gradient.
- **Night Mid** (`#3B4371`): Mid gradient stop (the brand-anchor pair).
- **Canvas Deep** (`#14152E`): Bottom of gradient / scroll background — never `#000000`.
- **Surface 1** (`#1E2046`): Card backgrounds (graph card, alarm card, list rows).
- **Surface 2** (`#292C58`): Raised chips, segmented controls, ring track.
- **Divider** (`#353974`): Hairline borders and separators.

### Canvas & Surfaces (Light — Dawn)
- **Dawn Canvas** (`#F4F5FC`): Pale lavender-white light background.
- **Dawn Surface** (`#FFFFFF`): Cards in light mode.
- **Dawn Divider** (`#E3E5F2`): Hairlines in light mode.

### Text
- **Text Primary Dark** (`#F2F3FB`): Headlines, stat numbers on dark.
- **Text Secondary Dark** (`#A6A9D4`): Muted lavender — metadata, body, captions on dark.
- **Text Tertiary Dark** (`#6C70A8`): Axis labels, disabled, very low emphasis.
- **Text Primary Light** (`#1B1D3A`): Primary text on Dawn light.
- **Text Secondary Light** (`#6C70A8`): Secondary text on Dawn light.

### Sleep Stage Colors (semantic — used on hypnogram, legend, chips, both modes)

| Stage | Color | Role |
|-------|-------|------|
| Awake | `#FF8FB1` | Brief wake events at the top of the graph |
| Light Sleep | `#6C7BFF` | The most common band — same as Accent Indigo |
| Deep Sleep | `#3D4ABF` | Slow-wave troughs at the bottom |
| REM | `#4FD1E6` | Dream sleep — same as Aqua accent |
| In Bed (idle) | `#6C70A8` | Time in bed not yet asleep |

### Semantic
- **Success** (`#4FD1A0`): Goal met, streak continued, good-night badge.
- **Error** (`#FF6B81`): Permission errors, missing-data state.
- **Gold** (`#FFC56B`): Star accents, streak flames, premium badges (sparingly).
- **Score Low** (`#FF8FB1`): Sleep-quality ring when score < 50.
- **Score Mid** (`#6C7BFF`): Sleep-quality ring 50–74.
- **Score High** (`#4FD1E6`): Sleep-quality ring 75–100.

## 3. Typography Rules

### Font Family
- **Primary**: `Nunito` (by Vernon Adams / Jacques Le Bailly, Google Fonts, SIL OFL) — a well-balanced rounded humanist sans. Its soft, rounded terminals make data feel gentle and human rather than clinical, which suits a wellness/sleep context.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Use Nunito at 800–900 weight for all hero numbers (sleep score, hours slept, alarm time). Tabular figures preferred for time displays so digits don't jitter.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Nunito | 32pt | 900 | 1.15 | -0.4pt | "Last night", "Statistics" |
| Hero Stat | Nunito | 34pt | 900 | 1.1 | -0.5pt | Sleep score / hours slept / alarm time |
| Section Title | Nunito | 22pt | 800 | 1.25 | -0.2pt | "Sleep stages", "Trends" |
| Card Title | Nunito | 18pt | 700 | 1.3 | -0.1pt | "Smart alarm", "Sleep aid" |
| Body | Nunito | 16pt | 400 | 1.5 | 0pt | Explanatory paragraph copy |
| List Item | Nunito | 15pt | 600 | 1.4 | 0pt | "Time to fall asleep · 31m" |
| Meta | Nunito | 14pt | 400 | 1.4 | 0pt | Date ranges, timestamps |
| Eyebrow | Nunito | 12pt | 700 | 1.0 | 1.4pt | "SLEEP ANALYSIS" — uppercase, tracked, aqua |
| Button | Nunito | 16pt | 800 | 1.0 | 0.2pt | Primary CTA label |
| Pill / Tag | Nunito | 11pt | 800 | 1.0 | 0.2pt | "Good night", stage chips |
| Axis Label | Nunito | 9pt | 700 | 1.0 | 0pt | Graph x/y tick labels (tertiary) |
| Tab Label | Nunito | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Stat-led hierarchy**: the biggest, heaviest thing on screen is always the emotional number (score, hours, wake time) — not a title bar.
- **Heavy numbers, light prose**: numerals at 800–900; explanatory body at 400 in muted lavender. The contrast is the point.
- **Rounded warmth over precision**: Nunito's soft terminals are chosen *because* they read as caring, not clinical. Don't substitute a geometric grotesque.
- **Eyebrows are aqua and tracked**: the small uppercase label above a title is the one place tracking and the aqua accent appear in type.
- **Dynamic Type**: titles, hero stats, body, list items scale; axis labels, tab labels, pill text stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Start Session / Save Alarm)**
- Shape: Rounded rectangle, 16pt corner radius
- Background: linear gradient `#6C7BFF → #3D4ABF` (135°)
- Text: `#FFFFFF`, Nunito 16pt weight 800
- Padding: 15pt vertical, 30pt horizontal
- Shadow: `rgba(108,123,255,0.35) 0 8px 20px` (soft indigo glow)
- Pressed: solid `#5563E6` + scale 0.98
- Disabled: `#292C58` fill, `#6C70A8` text

**Secondary / Outline Button**
- Background: transparent
- Text: `#F2F3FB`, Nunito 14pt weight 700
- Border: 1.5pt `#353974`
- Padding: 13pt vertical, 22pt horizontal
- Corner radius: 14pt
- Pressed: background `#1E2046`

**Pill / Segment Button (This week / Month)**
- Background: `#292C58`
- Text: `#4FD1E6` (active) / `#A6A9D4` (inactive), Nunito 14pt weight 700
- Border: 1pt `#353974`
- Corner radius: 999pt (full pill)
- Padding: 11pt vertical, 20pt horizontal

**Text Button (View full night)**
- Font: Nunito 14pt weight 700
- Color: `#4FD1E6`
- No underline, no background
- Pressed: opacity 0.6

### Core Atoms

**Sleep-Quality Ring**
- Circular SVG, 92pt default (also 80pt compact)
- Track: 9pt stroke `#292C58`
- Progress arc: 9pt stroke, `stroke-linecap: round`, starts at -90° (top)
- Arc color is score-banded: `#FF8FB1` (<50), `#6C7BFF` (50–74), `#4FD1E6` (75–100)
- Center: big number Nunito 26pt weight 900 `#F2F3FB`; sublabel 9pt weight 700 `#A6A9D4` uppercase

**Card**
- Background: `#1E2046`
- Border: 1pt `#353974`
- Corner radius: 20pt (graph/hero cards), 18pt (list/alarm cards)
- Padding: 16pt
- Shadow: `rgba(0,0,0,0.35) 0 8px 24px` — soft, low, never harsh

**Icon Tile**
- 38pt rounded square, 12pt corner radius
- Background: gradient `#6C7BFF → #3D4ABF`
- Glyph: 20pt, 2pt stroke, `#FFFFFF`

**Sleep-Stage Chip**
- Pill, 12pt corner radius, Nunito 13pt weight 800
- Fill = stage color; text `#FFFFFF` on Deep/Light, dark ink on Awake (`#3A1430`) and REM (`#07303A`) for contrast
- Padding: 8pt vertical, 14pt horizontal

### Navigation

**Bottom Tab Bar**
- Height: 70pt + safe area
- Background: `rgba(20,21,46,0.92)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#353974`
- Tabs (5): Sleep · Journal · Alarm · Sounds · Profile
- Icon size: 22pt, 2pt stroke
- Active: `#4FD1E6` (icon fills solid, stroke removed)
- Inactive: `#6C70A8`
- Label: Nunito 10pt weight 700, always shown
- No tint pill — color change only

**Top Zone (no nav bar)**
- Sleep Cycle has no traditional UINavigationBar on the main screen
- Instead: an eyebrow + title block in the scroll content (eyebrow 12pt aqua tracked, title 32pt 900)
- Modal screens (Edit Alarm) use a sheet with a centered title, leading "Cancel", trailing "Save"

### Input Fields

**Time Picker (alarm)**
- Large wheel picker with Nunito 28pt weight 800 numerals
- Selected row: `#F2F3FB`; adjacent rows: `#6C70A8` dimmed
- Background: `#1E2046`, 20pt corner radius

**Segmented Control (week / month / year)**
- Container: `#292C58`, 999pt radius, 4pt inset
- Selected segment: `#6C7BFF` fill, white text; others: transparent, `#A6A9D4`
- Slide animation 220ms ease-out between segments

**Toggle Switch (smart alarm on/off)**
- Track on: `#6C7BFF`; off: `#353974`
- Thumb: `#F2F3FB`, 50% radius, soft shadow
- 200ms ease-out slide + soft haptic

### Distinctive Components

**Sleep-Analysis Graph (Hypnogram) — the signature**
- Full-width card, 20pt radius, `#1E2046`
- Plot area ~110pt tall
- Y-axis labels (left, 8–9pt `#6C70A8`): Awake / Light / Deep top→bottom
- The wave: a smooth bezier path, 2.5pt stroke `#7FE3F0`, `stroke-linecap: round`
- Fill below the wave: vertical gradient `#6C7BFF` 45% → 2% opacity, fades to baseline
- Stage event nodes: 3pt dots colored by stage (`#FF8FB1` awake, `#3D4ABF` deep)
- X-axis: hour ticks 00 / 02 / 04 / 06 / 07, 9pt weight 700 `#6C70A8`
- Legend row: four `leg` items — 9pt color swatch + stage name, 10pt weight 700

**Sleep-Quality Ring + Hero Stat (paired)**
- Ring on the leading side (92pt), hero stat block trailing
- Stat block: big "7h 36m" 26pt weight 800, sub "Time asleep · 31m to fall asleep" 12pt `#A6A9D4`, then a status pill ("Good night", aqua fill, dark text)

**Smart-Alarm Card**
- Horizontal row: gradient icon tile (alarm-clock glyph) + title/subtitle + big time on the trailing edge
- Title "Smart alarm" 15pt weight 800; subtitle "Wakes you in your lightest sleep" 11pt `#A6A9D4`; time 19pt weight 900 `#F2F3FB`

**Sleep-Aid / Soundscape Player**
- Album-art-style rounded tile (16pt radius) with a gradient cover
- Play/pause circular button in aqua; scrubber line in `#6C7BFF` over `#353974` track
- Track title 15pt weight 700, duration 12pt `#A6A9D4`

**Trend Bar Chart (weekly)**
- Vertical bars, 8pt rounded tops, fill `#6C7BFF`, the best/selected bar in `#4FD1E6`
- Baseline grid line `#353974` dashed; goal line `#FFC56B` dashed

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 48
- Screen horizontal inset: 20pt
- Card internal padding: 16pt
- Vertical gap between cards: 14pt
- Gap between section title and its content: 12pt

### Grid & Container
- iPhone: single column, 20pt horizontal insets, full-bleed gradient behind
- The gradient is on the screen background; cards float over it (never edge-to-edge backgrounds inside cards)
- iPad: content max-width 600pt, centered; gradient still full-bleed
- Graph card spans the full content width minus insets

### Whitespace Philosophy
- **Calm, breathable, never dense**: this is a bedtime app — generous 14pt gaps, no cramped data tables
- **The wave gets room**: the hypnogram card is the visual focus; nothing crowds it
- **Stat-first**: the score ring + hero stat sit high with air around them so the number lands emotionally
- **Quiet edges**: cards have soft low shadows so they lift off the gradient without hard borders dominating

### Border Radius Scale
- Square (0pt): full-bleed gradient, graph plot baseline
- Soft (8pt): chart bar tops, small chips' inner detail
- Standard (12pt): stage chips, icon tiles
- Comfortable (16pt): primary buttons, sound tiles
- Card (20pt): graph card, hero card, time-picker, sheets
- Large (28pt): bottom-sheet top corners
- Pill (999pt): segmented controls, status pills, "this week" buttons
- Ring (50%): sleep-quality ring, avatar, play button

## 6. Depth & Elevation

Sleep Cycle uses soft, low-contrast elevation — shadows are present but quiet, because harsh shadows would feel jarring in a dark bedside context.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, sits on gradient | Eyebrow/title text, axis labels |
| Card (Level 1) | `rgba(0,0,0,0.35) 0 8px 24px` | Graph card, alarm card, list rows |
| Floating (Level 2) | `rgba(108,123,255,0.35) 0 8px 20px` | Primary button (colored glow, not gray) |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -8px 32px` | Bottom sheets (Edit alarm, sound picker) |
| Overlay | `rgba(8,9,24,0.55)` | Dim behind sheets and modals |

**Shadow Philosophy**: shadows are deep but soft-edged (large blur, low spread) so cards appear to *float over a night sky* rather than cast a hard drop shadow. The primary button uses a colored indigo glow instead of a black shadow — it should feel like it's emitting a soft light, fitting the "glow not glare" theme. On the Dawn light theme, shadows tighten and lighten to `rgba(43,29,58,0.10) 0 6px 18px`.

### Motion
- **Sleep wave draw-in**: on opening the analysis, the hypnogram path animates with a stroke-dashoffset reveal left→right over 900ms ease-out; the gradient fill fades in behind it over 600ms
- **Score ring fill**: the arc sweeps from 0 to its value over 800ms ease-out with a slight overshoot; the center number counts up in sync
- **Bar chart grow**: weekly bars rise from baseline over 500ms ease-out, staggered 40ms each
- **Tab switch**: cross-fade content 200ms; active icon color tweens 150ms
- **Segmented control**: selected pill slides between segments 220ms ease-out
- **Sheet present**: slides up 300ms ease-out; backdrop fades in 200ms
- **Toggle**: thumb slides 200ms ease-out + soft haptic
- **Session start**: a calming pulse/scale on the moon icon 1200ms ease-in-out, then the screen auto-dims brightness over 1.5s
- **Reduce Motion**: replace draw-in/sweep with a 200ms cross-fade; bars and ring appear at final value

## 7. Do's and Don'ts

### Do
- Wrap the app in the indigo→night gradient (`#2A2D5A → #3B4371 → #14152E`) — dark-first always
- Use a deep indigo canvas `#14152E` — NEVER pure black `#000000`
- Make the sleep-analysis graph the hero — a smooth glowing aqua wave over color-coded stages
- Keep exactly one bright accent: Aqua `#4FD1E6` — on the wave, the score ring, the active tab
- Make hero numbers huge and heavy (Nunito 800–900) — the score and hours are the emotional payload
- Use the sleep-stage color system consistently (Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake `#FF8FB1`)
- Float soft rounded cards (16–20pt radius) over the gradient with low, soft shadows
- Give the primary button a colored indigo glow (`rgba(108,123,255,0.35)`), not a gray drop shadow
- Use muted lavender `#A6A9D4` for all secondary copy — quiet, not loud
- Auto-dim brightness during an active tracking session
- Keep Nunito's rounded warmth — it's chosen because it feels caring, not clinical

### Don't
- Don't use pure black or pure white as the field — the screen must glow softly, never glare at night
- Don't introduce a second bright accent — aqua is alone for a reason; extra brights break the calm
- Don't use a hard-edged geometric/grotesque font — it makes sleep data feel clinical
- Don't draw the hypnogram as hard bars or steps — it's a smooth organic bezier wave
- Don't crowd the graph — it's the focus; give it space, never wrap dense tables around it
- Don't use harsh black drop shadows — soft, large-blur, low-spread only
- Don't color the sleep score ring a single fixed hue — it's score-banded (rose / indigo / aqua)
- Don't make body text bold by default — body is 400; only numbers are heavy
- Don't add bright modal flashes or aggressive transitions — motion is slow, calming, 200–900ms ease-out
- Don't use Material-style tint pills on the tab bar — active state is a color change only
- Don't put a heavy opaque nav bar on the main screen — the title lives in the scroll content

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Score ring 80pt; graph height 96pt; tighter 16pt insets |
| iPhone 13/14/15 | 390pt | Standard layout (reference) |
| iPhone 15/16 Pro | 393pt | Dynamic Island clearance in status zone |
| iPhone 15/16 Pro Max | 430pt | Score ring 100pt; graph height 124pt; larger hero stat |
| iPad (portrait) | 768pt | Content max-width 600pt centered; gradient full-bleed |
| iPad (landscape) | 1024pt+ | Two-column: graph + stats side-by-side, max 900pt centered |

### Dynamic Type
- Scales: screen title, hero stat, section title, card title, body, list item
- Fixed: graph axis labels, legend text, tab labels, stage-chip text, ring sublabel
- Hero stat caps at +30% to avoid breaking the ring/stat row layout

### Orientation
- Phone: portrait-locked on the main sleep screen (it's a bedside view)
- iPad: supports landscape with the side-by-side graph/stats split

### Touch Targets
- Tab bar icons: 22pt glyph, 44pt hit area
- Primary button: 50pt tall
- Toggle switch: 51×31pt standard
- Time-picker rows: 44pt tall
- Segmented control segments: 36pt tall minimum

### Safe Area Handling
- Top: status/eyebrow zone respects safe area + Dynamic Island
- Bottom: tab bar respects home indicator (70pt + safe area)
- The gradient extends edge-to-edge including under the status bar and home indicator
- Sheets respect bottom safe area; content scrolls above the keyboard in journal notes

## 9. Agent Prompt Guide

### Quick Color Reference
- Gradient top: `#2A2D5A`
- Gradient mid: `#3B4371`
- Canvas deep: `#14152E` (NOT black)
- Surface 1 (cards): `#1E2046`
- Surface 2 (chips/track): `#292C58`
- Divider: `#353974`
- Accent indigo: `#6C7BFF`
- Aqua (the accent): `#4FD1E6` / soft `#7FE3F0`
- Deep sleep: `#3D4ABF`
- Awake / rose: `#FF8FB1`
- Gold (stars): `#FFC56B`
- Text primary: `#F2F3FB` / secondary `#A6A9D4` / tertiary `#6C70A8`
- Stage colors: Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake `#FF8FB1`

### Example Component Prompts
- "Build the Sleep Cycle sleep-analysis graph card in SwiftUI: a `#1E2046` card with 20pt corner radius and a soft `rgba(0,0,0,0.35) 0 8px 24px` shadow. Inside, a ~110pt-tall plot. Draw a smooth bezier hypnogram path with a 2.5pt stroke in `#7FE3F0`, `lineCap: .round`, over a vertical gradient fill (`#6C7BFF` 45% → 2% opacity) that drops to the baseline. Left y-axis labels Awake/Light/Deep in 9pt weight 700 `#6C70A8`. Bottom x-axis hour ticks 00/02/04/06/07. On appear, animate the path with a left-to-right stroke reveal over 900ms ease-out and fade the fill in over 600ms. Below, a legend row with 9pt color swatches for Awake `#FF8FB1`, Light `#6C7BFF`, Deep `#3D4ABF`, REM `#4FD1E6`."

- "Create the Sleep Cycle sleep-quality ring: a 92pt circular SVG/Canvas. Track is a 9pt stroke `#292C58`. Progress arc is a 9pt round-cap stroke starting at -90°, colored by score band — `#FF8FB1` if <50, `#6C7BFF` if 50–74, `#4FD1E6` if 75–100. Center: the score number in Nunito 26pt weight 900 `#F2F3FB`, with a 9pt weight 700 `#A6A9D4` uppercase sublabel 'SLEEP QUALITY'. Animate the arc sweeping 0→value over 800ms ease-out while the center number counts up."

- "Build the Sleep Cycle smart-alarm card: a horizontal `#1E2046` row, 18pt radius, 14pt padding. Leading: a 38pt rounded-square (12pt radius) icon tile with a `#6C7BFF → #3D4ABF` gradient and a white alarm-clock glyph. Center: title 'Smart alarm' Nunito 15pt weight 800 `#F2F3FB`, subtitle 'Wakes you in your lightest sleep' 11pt weight 600 `#A6A9D4`. Trailing: the wake time '07:30' Nunito 19pt weight 900 `#F2F3FB`."

- "Build the Sleep Cycle primary button: rounded rectangle 16pt radius, gradient `#6C7BFF → #3D4ABF` at 135°, white Nunito 16pt weight 800 text, 15pt/30pt padding, and a colored glow shadow `rgba(108,123,255,0.35) 0 8px 20px` (NOT a gray drop shadow). Pressed: solid `#5563E6` + scale 0.98."

- "Render the Sleep Cycle bottom tab bar: 70pt + safe area, background `rgba(20,21,46,0.92)` with a 20px blur and 0.5pt top border `#353974`. Five tabs — Sleep, Journal, Alarm, Sounds, Profile — 22pt 2pt-stroke icons, label Nunito 10pt weight 700. Active tab is `#4FD1E6` with the icon filled solid; inactive is `#6C70A8`. No tint pill — color change only."

- "Build the Sleep Cycle screen header: an eyebrow 'SLEEP ANALYSIS' in Nunito 12pt weight 700 `#4FD1E6` with 1.4pt tracking uppercase, then a title 'Last night' Nunito 32pt weight 900 `#F2F3FB`, then a date line 'Mon, May 12 · 23:42 – 07:18' Nunito 14pt weight 400 `#A6A9D4`. No nav bar — this lives in the scroll content over the gradient."

### Iteration Guide
1. The whole screen is the indigo→night gradient (`#2A2D5A → #3B4371 → #14152E`) — dark-first, soft glow, NEVER pure black
2. The sleep-analysis graph is the hero — a smooth glowing aqua bezier wave (`#7FE3F0`) over color-coded stages, not bars
3. Exactly one bright accent: Aqua `#4FD1E6` — wave, score ring, active tab; nothing else competes
4. Hero numbers are huge and heavy (Nunito 900); body is light 400 in muted lavender `#A6A9D4`
5. Sleep-stage colors are fixed and semantic: Deep `#3D4ABF`, Light `#6C7BFF`, REM `#4FD1E6`, Awake `#FF8FB1`
6. The score ring is score-banded (rose <50 / indigo 50–74 / aqua 75–100) — never one fixed hue
7. Cards float over the gradient with soft low shadows; primary button has a colored indigo glow
8. Nunito's roundness is intentional — it makes sleep data feel caring, not clinical
9. Motion is slow and calming — wave draw-in 900ms, ring sweep 800ms, all ease-out; auto-dim on session start
10. Minimal chrome — 5-tab bar, no opaque nav bar; the title lives in scroll content
