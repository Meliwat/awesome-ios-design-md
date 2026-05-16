# Design System Inspiration of Oura (iOS)

## 1. Visual Theme & Atmosphere

Oura's iOS app is a dark instrument panel for the body. The canvas is a deep, slightly cool charcoal (`#0B0B0F`) — near-black, but with the faintest blue cast so it reads as "night" rather than "off". Against it, the interface is organized around one recurring visual object: the **score ring**, a large circular gauge from 0 to 100 that fills clockwise. Three of these rings — Readiness (teal), Sleep (indigo), Activity (amber) — are the heartbeat of the product. Everything else (contributor lists, trend graphs, insight cards) exists to explain the rings. The app feels less like a feed and more like a cockpit: data-dense, calm, and precise.

The accent system is a tri-color domain language, not a single brand color. Each of the three health domains owns a hue and uses it consistently everywhere it appears: **Readiness teal** (`#4FD1C5`), **Sleep indigo** (`#7C6FF0`), **Activity amber** (`#F5A623`). A ring is teal; the contributors that feed it are teal; its trend line is teal. This color-as-meaning system lets the user parse a dense screen at a glance — color is information, never decoration. Outside the three domains everything is monochrome: white text, cool grey for secondary, and surface steps in charcoal. There is no fourth accent.

Typography is Inter (Oura's product face is a clean geometric sans in the same family), used with restraint at 400 / 600 / 700. The hero of every screen is a number — the score — set very large (the ring center shows a 48pt+ figure). Around it, labels are small and quiet: 13pt uppercase domain labels, 15pt contributor names, 13pt deltas. The type never competes with the rings; it annotates them. The single most expressive typographic moment is the score reveal: when a screen loads, the ring sweeps from 0 to its value while the central number counts up in sync, so the figure and the arc arrive together.

**Key Characteristics:**
- Deep cool-charcoal canvas (`#0B0B0F`) — night, with a faint blue cast, not pure black
- The score ring (0–100) as the central recurring object — fills clockwise
- Tri-domain color language — Readiness teal, Sleep indigo, Activity amber
- Color is information — a domain's hue follows it across ring, contributors, and trends
- Contributor bar list — horizontal mini-bars breaking a score into its inputs
- Ring-battery indicator — the physical ring's charge level, shown as a small arc
- Trend graphs — sparkline/area charts in the domain hue over 7/30/90 days
- Insight cards — a sentence of interpretation ("Your HRV is trending up")
- Bottom tabs (Today / Vitals / My Health / Explore) — quiet, glass-backed
- Inter typeface, 400/600/700; the score figure is the largest type on screen

## 2. Color Palette & Roles

### Domain Accents (Color = Meaning)
- **Readiness Teal** (`#4FD1C5`): The Readiness ring, its contributors, its trend line, the "Readiness" tab/section.
- **Readiness Teal Deep** (`#37B3A8`): Pressed/active state for teal controls; the unfilled-track tint never uses this.
- **Sleep Indigo** (`#7C6FF0`): The Sleep ring, sleep-stage bars, sleep contributors, the sleep trend line.
- **Sleep Indigo Deep** (`#6354D6`): Pressed state for indigo controls.
- **Activity Amber** (`#F5A623`): The Activity ring, move goal, step bars, activity contributors and trends.
- **Activity Amber Deep** (`#D98A12`): Pressed state for amber controls.

### Canvas & Surfaces
- **Canvas** (`#0B0B0F`): The primary dark canvas — cool near-black.
- **Surface 1** (`#16161C`): Card backgrounds, contributor list containers.
- **Surface 2** (`#1E1E26`): Raised cards, sheets, the expanded detail panels.
- **Surface 3** (`#27272F`): Pressed/hover state on dark surfaces, ring unfilled track.
- **Divider** (`#2A2A33`): Hairline separators between rows and sections.

### Text
- **Text Primary** (`#FFFFFF`): The score figure, card titles, primary metrics.
- **Text Secondary** (`#9A9AA5`): Contributor names, units, descriptions, axis labels.
- **Text Tertiary** (`#6A6A73`): Very low-emphasis metadata, disabled labels, gridline values.

### Semantic
- **Positive Delta** (`#4FD1C5`): An improving trend arrow / "+4 vs last week" (uses Readiness teal as the generic "good").
- **Negative Delta** (`#E0746B`): A declining trend — soft desaturated coral, never harsh red.
- **Neutral Delta** (`#9A9AA5`): Flat / no meaningful change.
- **Optimal Band** (`#4FD1C5` @ 16%): The shaded "optimal range" zone behind a trend graph.
- **Ring Battery** (`#9A9AA5`): The ring-charge arc (monochrome — it is hardware status, not a health domain).
- **Warning Amber** (`#F5A623`): "Battery low", "Sync needed" (reuses Activity amber, contextually).

### Light Mode
Oura's iOS app is dark-first by design — the dark instrument panel is the brand and is used at night and on waking. There is no fully shipped light variant. (A daytime reference would invert to Canvas `#F4F4F6`, Text `#0B0B0F`, with the same three domain accents, but it is not part of the product.)

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` — Oura's product face is a clean geometric sans in this family
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Tabular figures enabled**: `font-variant-numeric: tabular-nums` — scores and deltas must align in columns

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Ring Score (Hero) | Inter | 52pt | 700 | 1.0 | -1.0pt | The big number at the center of a score ring |
| Screen Title | Inter | 26pt | 700 | 1.2 | -0.4pt | "Today", "Sleep", large nav title |
| Section Header | Inter | 20pt | 700 | 1.25 | -0.2pt | "Contributors", "Trends" |
| Secondary Score | Inter | 34pt | 700 | 1.0 | -0.6pt | Smaller domain ring on the Today summary |
| Card Title | Inter | 17pt | 600 | 1.3 | -0.1pt | Insight card heading, metric card title |
| Metric Value | Inter | 22pt | 600 | 1.1 | -0.2pt | "62 ms" HRV, "7h 24m" sleep duration |
| Contributor Name | Inter | 15pt | 600 | 1.3 | 0pt | "Resting Heart Rate", "Sleep Balance" |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Insight sentences, explanatory copy |
| Subtitle / Unit | Inter | 13pt | 400 | 1.3 | 0pt | "ms", "vs last week", axis captions |
| Delta | Inter | 13pt | 600 | 1.2 | 0pt | "+4", "−2 bpm" trend deltas |
| Domain Label (UPPER) | Inter | 13pt | 700 | 1.2 | 0.8pt | "READINESS", "SLEEP", "ACTIVITY" |
| Button | Inter | 16pt | 600 | 1.0 | 0pt | "View details", "Add tag" |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab labels |
| Caption / Gridline | Inter | 11pt | 400 | 1.2 | 0pt | Chart axis values, timestamps |

### Principles
- **The number is the headline**: every screen leads with a large score or metric figure; titles are secondary to it.
- **Tabular figures always**: scores, deltas, and metric values use `tabular-nums` so columns and the counting-up animation never reflow.
- **Weight band 400 / 600 / 700**: regular for body, semibold for metrics/contributors, bold for scores/labels. No light, no black.
- **Domain labels are uppercase and tracked**: "READINESS" / "SLEEP" / "ACTIVITY" at 0.8pt tracking — they are categorical, not prose.
- **Color encodes domain, not emphasis**: never color body text for stress; the teal/indigo/amber are reserved for domain identity and rings.

## 4. Component Stylings

### Score Ring *(signature)*
- A circular gauge, diameter 200-240pt (hero) or 64-88pt (summary tile)
- Track: `#27272F`, stroke width 12pt (hero) / 6pt (tile), full circle
- Progress arc: the domain hue (teal/indigo/amber), same stroke, **rounded cap**, sweeps clockwise from the 12-o'clock position
- Center: the score figure (52pt w700 white, hero) above a domain label (13pt w700 uppercase, the domain hue)
- A small tick or gap at the top marks 0/100
- Reveal: arc animates 0 → value over ~900ms ease-out while the central number counts up in sync
- Optionally a thin secondary "yesterday" ghost arc at 30% opacity behind the live arc

### Contributor Bar List *(signature)*
- A vertical list inside a Surface 1 card; each row breaks the score into one input
- Row layout: contributor name (15pt w600 white, left) → a horizontal mini-bar → value/state (13pt w600 right)
- Mini-bar: track `#27272F` height 6pt radius 3pt; fill the domain hue, width = contributor's normalized score
- Row height: 44pt, 1pt `#2A2A33` divider between rows
- Tapping a row expands an inline explanation (Surface 2, body 15pt `#9A9AA5`)

### Buttons

**Primary Button**
- Shape: Pill, height 48pt
- Background: the contextual domain hue (or white on neutral screens)
- Text: `#0B0B0F` on a hued/white fill (dark text for contrast), Inter 16pt w600
- Padding: 14pt vertical, 28pt horizontal, corner radius 24pt
- Pressed: hue → its Deep variant, scale 0.97, soft haptic

**Glass Secondary Button**
- Background: `#1E1E26`, 1pt border `#2A2A33`
- Text: `#FFFFFF`, Inter 16pt w600
- Padding: 14pt vertical, 24pt horizontal, corner radius 24pt
- Pressed: background `#27272F`

**Icon Actions (Add tag, Share, Info)**
- Size: 20pt glyph, 44pt hit area
- Default: `#9A9AA5`; Pressed: `#FFFFFF`
- Domain-info icon picks up the domain hue when inside that domain's screen

**Segmented Range Control (7D / 30D / 90D)**
- Container: `#16161C`, 1pt `#2A2A33`, corner radius 20pt, height 36pt
- Active segment: background `#27272F`, text `#FFFFFF` w600
- Inactive: text `#9A9AA5`
- Selection slides 200ms ease

### Cards & Containers

**Insight Card**
- Background: `#16161C`, corner radius 16pt, padding 18pt
- Leading: a 24pt domain-hued glyph (e.g., HRV waveform)
- Title: Inter 17pt w600 white; Body: Inter 15pt w400 `#9A9AA5`
- Optional trailing chevron if it deep-links to a detail screen
- Pressed: background `#1E1E26`

**Metric Card (grid tile)**
- Background: `#16161C`, corner radius 16pt, padding 16pt
- Top: 13pt w700 uppercase label (`#9A9AA5`)
- Center: metric value 22pt w600 white + unit 13pt w400 `#9A9AA5`
- Bottom: a small sparkline in the domain hue + delta (13pt w600)
- 2-column grid, 12pt gap

**Trend Graph Card**
- Background: `#16161C`, corner radius 16pt, padding 18pt
- Area/line chart in the domain hue; fill is the hue at 16% opacity
- "Optimal band" shaded zone behind the line (`#4FD1C5` @ 16% for readiness-style)
- Axis labels 11pt `#6A6A73`; gridlines `#2A2A33` 0.5pt
- Range control (7D/30D/90D) docked at the bottom

**Today Summary (top of Today tab)**
- The three domain rings shown together: one large hero ring (today's primary focus) or a row of three medium rings
- Each ring tappable → pushes that domain's detail screen
- Below: a one-line insight in 15pt `#9A9AA5`

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(11,11,15,0.82)` with `.regularMaterial` blur
- Tabs: Today, Vitals, My Health, Explore
- Icon size: 24pt
- Active color: `#FFFFFF` (the tab indicator is neutral white — domain color lives in content, not chrome)
- Inactive: `#6A6A73`
- Labels: Inter 11pt w600, always shown
- A hairline `#2A2A33` top border

**Top Bar**
- Height: 44pt + safe area, background `#0B0B0F`
- Left: date selector ("Today" / "Wed, May 14") with a small chevron
- Right: ring-battery indicator (a tiny arc + %) then a profile/settings glyph 22pt
- A 0.5pt `#2A2A33` divider appears on scroll

**Domain Detail Screen**
- Hero score ring at top, domain label in the domain hue
- Scrolls into: contributor bar list → trend graph → insight cards
- The accent throughout the screen is that domain's single hue

### Input Fields

**Search / Filter (Explore)**
- Background: `#16161C`, height 44pt, corner radius 22pt
- Leading `magnifyingglass` 16pt `#9A9AA5`
- Placeholder: "Search programs & sessions", 15pt `#6A6A73`
- Focus: border `#2A2A33` → `#3A3A45`, no color shift

**Tag Chip (Day tagging)**
- Background: `#1E1E26` / domain hue when selected
- Text: `#FFFFFF`
- Padding: 8pt vertical, 16pt horizontal, corner radius 18pt
- Examples: "Caffeine", "Alcohol", "Late meal", "Sick"

### Distinctive Components

**Ring-Battery Indicator**
- A small (20pt) circular arc showing the physical Oura ring's charge
- Track `#27272F`, fill `#9A9AA5` (monochrome — hardware status, not a health domain)
- Turns Activity amber (`#F5A623`) below 20%
- Sits in the top bar with a "%"

**Sleep Stage Hypnogram**
- A horizontal banded chart of the night: Awake / REM / Light / Deep
- Each band a tint of Sleep indigo (`#7C6FF0`) at varying lightness
- Time axis 11pt `#6A6A73` below

**Tri-Ring Summary**
- The three rings (teal/indigo/amber) drawn concentrically or side-by-side on the Today tab
- A single glanceable object that encodes all three domains at once

**Score Reveal Animation**
- On screen load each ring sweeps 0 → value (~900ms ease-out) while its center figure counts up in lockstep — the arc and the number land together

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard horizontal margin: 16pt
- Section spacing: 24pt between major blocks (ring → contributors → trends)

### Grid & Container
- Single column, full device width with 16pt horizontal margins
- Metric tiles: 2-column grid, 12pt gap
- The hero ring is centered with generous vertical air above and below
- Contributor list and trend cards are full content width

### Whitespace Philosophy
- **The ring needs room**: the hero score ring is given large vertical breathing space — it is the focal point and must not feel crowded.
- **Dense where it explains**: contributor lists and metric grids are tighter (44pt rows) because they are reference data the user scans.
- **One domain per screen**: a detail screen commits to a single hue, which keeps a data-dense layout calm.

### Border Radius Scale
- Soft (6pt): mini-bar fills, small chart elements
- Standard (16pt): insight cards, metric tiles, trend cards
- Comfortable (20pt): sheets, segmented controls
- Pill (24pt): primary/secondary buttons, search field
- Chip (18pt): tag chips
- Circle (50%): score rings, avatars, ring-battery arc

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, the hero ring sits directly on canvas |
| Card (Level 1) | No shadow; `#16161C` fill | Insight cards, metric tiles, contributor lists |
| Raised (Level 2) | `#1E1E26` fill + `rgba(0,0,0,0.3) 0 8px 24px` | Expanded detail panels, the focused metric card |
| Sheet (Level 3) | `#1E1E26` + `rgba(0,0,0,0.5) 0 -16px 48px` | Tagging sheet, range pickers |
| Tab Bar Material | `rgba(11,11,15,0.82)` + `.regularMaterial` | Floats above scrolling content |
| Scrim | `rgba(0,0,0,0.6)` | Behind sheets |

**Shadow Philosophy**: On the cool near-black canvas, Oura communicates elevation mostly through surface value (`#0B0B0F` → `#16161C` → `#1E1E26`) rather than shadow. Cards are flat fills; only sheets and the actively-expanded panel get a soft, diffuse shadow (24-48pt blur, low opacity) to lift them. The score ring itself never casts a shadow — it is a flat, precise instrument, not a 3D object.

### Motion
- **Score reveal**: ring arc 0 → value over ~900ms ease-out; central number counts up in sync (same duration/easing)
- **Contributor bars**: each mini-bar grows from 0 to its width, staggered ~40ms per row, 500ms ease-out
- **Range switch (7D/30D/90D)**: the trend graph morphs between datasets over 350ms ease-in-out; segmented indicator slides 200ms
- **Card tap**: scale 0.98 → 1.0 over 200ms ease-out, soft haptic
- **Tab change**: cross-fade 250ms (no slide)
- **Pull-to-refresh (sync)**: a circular progress mimicking a ring filling, then a soft success haptic
- **Insight appear**: fade + 8pt rise over 300ms when scrolled into view

## 7. Do's and Don'ts

### Do
- Use the cool near-black canvas (`#0B0B0F`) — night with a faint blue cast
- Make the score ring the focal point of every domain screen — large, centered, with air
- Use the tri-domain color language consistently — teal=Readiness, indigo=Sleep, amber=Activity
- Treat color as information — a domain's hue follows it across ring, contributors, trends
- Lead each screen with the number — the score figure is the largest type on screen
- Use tabular figures for all scores, metrics, and deltas so columns never reflow
- Animate the score reveal — arc sweep and number count-up land together (~900ms)
- Keep the tab bar indicator neutral white — domain color lives in content, not chrome
- Use the soft desaturated coral (`#E0746B`) for negative deltas — never harsh red

### Don't
- Don't use pure black `#000000` — Oura's charcoal has a faint cool cast
- Don't introduce a fourth accent — only teal, indigo, amber carry domain meaning
- Don't color body text for emphasis — the three hues are reserved for domains/rings
- Don't square the ring's progress cap — it is rounded; the ring is a refined instrument
- Don't drop-shadow the score ring — it is a flat, precise gauge, not a 3D dial
- Don't reflow scores with proportional figures — always tabular-nums
- Don't put domain color on the tab bar — chrome stays neutral
- Don't animate the ring linearly — the reveal eases out so it decelerates into the value
- Don't crowd the hero ring — it needs vertical breathing room to read

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ring 200pt; metric grid stays 2-col |
| iPhone 13/14/15 | 390pt | Hero ring 220pt; standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the 44pt top bar |
| iPhone 15/16 Pro Max | 430pt | Hero ring 240pt; trend graphs taller |
| iPad | 768pt+ | Tri-ring summary side-by-side; 3-column metric grid; detail in a split layout |

### Dynamic Type
- Screen titles, contributor names, body, insights: full scale
- The ring score figure: scales modestly but capped (it must fit inside the ring) — typically max 60pt
- Domain labels, deltas, axis captions: fixed (layout-sensitive at 11-13pt)
- Tab labels: fixed 11pt

### Orientation
- Today / Vitals / My Health / Explore: **portrait-locked** (the ring + scroll layout is vertical)
- Detail screens: **portrait-locked**
- iPad: **rotates** — landscape uses a two-pane layout (ring + contributors beside trends)

### Touch Targets
- Score ring: the whole ring is a 44pt+ tap target → pushes detail
- Contributor rows: full 44pt height tappable to expand
- Tab icons: 24pt glyph, 44pt effective hit
- Range segments: full segment height (36pt)

### Safe Area Handling
- Top: 44pt bar respects the notch / Dynamic Island
- Bottom: tab bar floats above the home indicator
- Sides: 16pt content insets
- The hero ring centers within the safe content area, never under the bars

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0B0B0F`
- Surface 1 (cards): `#16161C`
- Surface 2 (raised/sheets): `#1E1E26`
- Ring track / pressed: `#27272F`
- Divider: `#2A2A33`
- Text primary: `#FFFFFF`
- Text secondary: `#9A9AA5`
- Readiness teal: `#4FD1C5`
- Sleep indigo: `#7C6FF0`
- Activity amber: `#F5A623`
- Negative delta (soft coral): `#E0746B`

### Example Component Prompts
- "Create a SwiftUI Oura score ring: 220pt diameter, track stroke #27272F 12pt full circle, progress arc in Readiness teal #4FD1C5 12pt with a rounded cap sweeping clockwise from 12 o'clock. Center: a 52pt weight 700 #FFFFFF tabular figure above a 13pt weight 700 uppercase 'READINESS' label in #4FD1C5. On appear, animate the arc 0 → 82 over 900ms ease-out while the number counts up in sync."
- "Build the contributor bar list: a #16161C card, corner radius 16pt. Each row 44pt tall: contributor name in Inter 15pt weight 600 #FFFFFF on the left, a horizontal mini-bar (track #27272F, 6pt tall, 3pt radius; fill Sleep indigo #7C6FF0 at the contributor's normalized width) in the center, and a state value in Inter 13pt weight 600 #FFFFFF on the right. 1pt #2A2A33 divider between rows; tapping a row expands a #1E1E26 explanation in 15pt #9A9AA5."
- "Design a metric tile: #16161C, 16pt radius, 16pt padding. Top: 'HRV' in Inter 13pt weight 700 uppercase #9A9AA5. Center: '62' 22pt weight 600 #FFFFFF with ' ms' 13pt #9A9AA5. Bottom: a small Readiness-teal sparkline plus '+4' delta in 13pt weight 600 #4FD1C5. 2-column grid, 12pt gap."
- "Build the trend graph card: #16161C, 16pt radius. An area chart in Sleep indigo #7C6FF0 with the fill at 16% opacity, an 'optimal band' shaded zone behind the line, axis labels 11pt #6A6A73, gridlines #2A2A33 0.5pt. A 7D/30D/90D segmented control docked at the bottom: container #16161C 1pt #2A2A33 20pt radius, active segment #27272F with #FFFFFF weight 600 text, selection slides 200ms."
- "Create the Oura bottom tab bar: 56pt + safe area, background rgba(11,11,15,0.82) with .regularMaterial blur, 1pt #2A2A33 top border. Tabs Today / Vitals / My Health / Explore, 24pt icons, active tint #FFFFFF (neutral — domain color is never on chrome), inactive #6A6A73, labels Inter 11pt weight 600."

### Iteration Guide
1. Canvas is cool near-black `#0B0B0F` — not pure black; it has a faint blue cast
2. The score ring is the recurring hero — large, centered, rounded cap, sweeps clockwise
3. Tri-domain color is a language: teal=Readiness, indigo=Sleep, amber=Activity — used everywhere that domain appears
4. Color encodes domain, never emphasis — body text stays white/grey
5. Lead with the number — the score figure is the largest type; use tabular figures everywhere
6. Animate the reveal — the arc sweep and the counting number land together (~900ms ease-out)
7. Elevation is surface value (`#0B0B0F` → `#16161C` → `#1E1E26`), not shadow; the ring is flat
8. The tab bar stays neutral white — domain color lives only in content
9. Oura is dark-first — do not surface a light theme
