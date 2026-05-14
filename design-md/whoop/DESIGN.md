# Design System Inspiration of WHOOP (iOS)

## 1. Visual Theme & Atmosphere

WHOOP's iOS app is a *performance instrument panel for the human body*. Where MyFitnessPal is a friendly ledger and Apple Fitness is a celebratory ring chase, WHOOP is the cockpit of a fighter jet — pitch-black canvas, neon-green strain, neon-red recovery, hard-edged tabular numerals, and the conviction that the data is the design. The app does not console you. It tells you that your recovery is 38% and your sleep was 6h 12m and that today's strain target is 13.2. The aesthetic is *clinical sports-tech*: think performance lab, not wellness retreat.

The canvas is **WHOOP Black** (`#0A0A0A`) — not pure black, but near enough that on an OLED screen the panel disappears into the device bezel. Every screen is built on this single foundation. The accents are deliberately neon — colors that vibrate against deep black: **Strain Green** (`#00FF7B`), the signature acid-electric green that has been WHOOP's brand color since 2012; **Recovery Green** (`#16EC06`) when recovery is high; **Recovery Yellow** (`#FFDE00`) when moderate; **Recovery Red** (`#FF0026`) when low; **Sleep Blue** (`#0093E7`) for the Sleep tab. These five accents do the heavy lifting; everything else is a six-step ramp of cool grays from `#1C1C1E` (surface 1) down to `#F5F5F7` (inverse text).

Typography is uniformly **DIN-style** — WHOOP licenses **DIN 2014** as the primary face, a flat, mechanically-engineered sans serif designed for instrumentation panels and modern wayfinding. The display weights are heavy: 30pt-50pt Bold for the strain number, 64pt+ for recovery percentage on the daily overview. **All-caps** is the dominant header treatment — "STRAIN", "RECOVERY", "SLEEP" — with wide letter-spacing (0.8pt+) and tight numerals. The numerals are *always* tabular and *always* monospaced — these are physiological metrics, not decoration.

**Key Characteristics:**
- Pitch-black canvas (`#0A0A0A`) on every screen — no light mode exists in the WHOOP app
- Strain Green (`#00FF7B`) as the signature accent — strain bar, primary CTA, active tab
- Recovery rings color-shift: red → yellow → green based on physiological score, in 1% increments
- Sleep blue (`#0093E7`) reserved for the Sleep tab and sleep-stage charts
- All-caps headers with wide tracking — STRAIN, RECOVERY, SLEEP, COACHING
- DIN 2014 typography — flat, mechanical, instrument-panel feel
- Tabular numerals everywhere — every metric, every number, monospaced
- The Recovery donut and Strain bar are signature components — 8pt-thick rings/bars with neon gradient fills
- Bottom tab bar with 4 tabs: Overview, Coaching, Community, Stats — minimal labels
- Subtle borders (1pt `#2A2A2A`) over shadows — WHOOP is flat and edge-defined, not elevated
- Negative space is sacred — large vertical gaps between cards let the neon accents breathe

## 2. Color Palette & Roles

### Primary
- **Strain Green** (`#00FF7B`): The signature accent — strain bar fill, primary CTA, active tab tint, link color.
- **Strain Green Bright** (`#67E26B`): Lighter strain green used on gradient stops in the strain bar.
- **Strain Green Dim** (`#005F2F`): Darker green for empty-state pulses and disabled states.

### Recovery Spectrum (color-shifts based on score)
- **Recovery Red** (`#FF0026`): Recovery score 0–33% (low recovery, body is fatigued).
- **Recovery Yellow** (`#FFDE00`): Recovery score 34–66% (moderate).
- **Recovery Green** (`#16EC06`): Recovery score 67–100% (high recovery).
- The ring interpolates between these three in 1% increments — the exact RGB lerps from red to yellow at 33%, and yellow to green at 67%.

### Sleep
- **Sleep Blue** (`#0093E7`): The Sleep tab accent — sleep-stage chart, sleep-need ring fill, time-in-bed metric.
- **Sleep Blue Bright** (`#4FB8FF`): Lighter sleep blue for gradient highlights and active hover.
- **REM Purple** (`#9C4DFF`): REM-stage segment in the sleep-stage stack chart.
- **Deep Sleep Indigo** (`#3D3DFF`): Deep-sleep segment.
- **Light Sleep Cyan** (`#3DD9FF`): Light-sleep segment.

### Canvas, Surfaces & Dividers
- **WHOOP Black** (`#0A0A0A`): Primary canvas — every screen.
- **Surface 1** (`#1C1C1E`): Cards, sheets, tab bar background.
- **Surface 2** (`#2A2A2A`): Chip backgrounds, input fills, hover/press states.
- **Surface 3** (`#3A3A3A`): Highest elevation — slider tracks, popovers.
- **Divider** (`#252525`): Hairline dividers between rows.
- **Hairline** (`#1A1A1A`): Subtle border between cards on dark canvas.

### Text
- **Bright White** (`#FFFFFF`): The big numeric heroes — strain number, recovery %, sleep duration.
- **Soft White** (`#F5F5F7`): Body text, list items.
- **Gray 400** (`#A1A1AA`): Secondary text, captions, meta info.
- **Gray 600** (`#5F5F65`): Tertiary, disabled, placeholder.
- **Gray 700** (`#3F3F45`): Icon-only states on inactive elements.

### Semantic
- **Alert Red** (`#FF453A`): Critical alerts (low battery, missed measurement), distinct from Recovery Red.
- **Warning Amber** (`#FFAA00`): Caution states, "Strain trending high".
- **Info Blue** (`#0A84FF`): Informational toasts, learn-more links.
- **Success Green** (`#30D158`): Sync complete, goal hit (distinct from Strain Green).
- **Premium Gold** (`#D4AF37`): WHOOP Whole Body / Pro tier badges.

### No Light Mode
WHOOP has no light mode. The app is dark-canvas-only by design — physiological metrics read best on black, and the brand identity is inseparable from the pitch-black panel.

## 3. Typography Rules

### Font Family
- **Primary**: `DIN 2014` (licensed face by ParaType)
- **Weights available**: Light (300), Regular (400), Demi (500), Bold (700), Extra Bold (800)
- **Fallback stack**: `'DIN 2014', 'D-DIN', 'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Helvetica Neue', sans-serif`
- **Web/marketing Google Fonts substitute**: `Inter` or `Barlow` — both share DIN 2014's geometric, slightly condensed proportions
- **Numbers**: Tabular numerals are non-negotiable. DIN 2014 ships with tabular figures by default at all weights.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Recovery Hero | DIN 2014 | 76pt | 800 | 1.0 | -1.6pt | "73%" — the giant Recovery % on Overview |
| Strain Hero | DIN 2014 | 56pt | 800 | 1.0 | -1.0pt | "13.2" — the strain score |
| Sleep Hero | DIN 2014 | 48pt | 800 | 1.0 | -0.8pt | "6h 12m" — sleep duration |
| Section Header (caps) | DIN 2014 | 14pt | 700 | 1.0 | 1.6pt uppercase | "RECOVERY", "STRAIN", "SLEEP" |
| Large Nav Title | DIN 2014 | 24pt | 700 | 1.15 | -0.2pt | "Overview", "Coaching", "Stats" |
| Section Subhead | DIN 2014 | 18pt | 700 | 1.2 | 0pt | "Today's Activity" |
| Card Title | DIN 2014 | 15pt | 500 | 1.25 | -0.05pt | "Heart Rate Variability" |
| Body | DIN 2014 | 14pt | 400 | 1.4 | 0pt | Article copy, explanations |
| Meta (caps) | DIN 2014 | 11pt | 700 | 1.0 | 1.2pt uppercase | "TODAY", "PAST 7 DAYS" |
| Metric Value | DIN 2014 | 18pt | 700 tnum | 1.0 | 0pt | "57 ms" HRV, "62 bpm" RHR |
| Metric Label | DIN 2014 | 11pt | 500 | 1.0 | 0.6pt uppercase | "HRV", "RHR", "RESPIRATORY RATE" |
| Button (Primary) | DIN 2014 | 14pt | 700 | 1.0 | 1.0pt uppercase | "VIEW DETAILS", "LOG ACTIVITY" |
| Tab Label | DIN 2014 | 10pt | 700 | 1.0 | 0.8pt uppercase | "OVERVIEW", "COACHING" |
| Chart Axis | DIN 2014 | 10pt | 500 | 1.0 | 0.2pt tnum | Time labels, percentile ranges |

### Principles
- **All-caps with wide tracking on headers** — this is the defining typographic move of WHOOP. Section labels, tab labels, button labels, and meta tags are uppercase with 0.6–1.6pt letter-spacing.
- **Tabular numerals on every metric** — HRV, RHR, calories, time durations, heart-rate values — DIN 2014's tabular figures are part of why the app reads as instrumentation.
- **Weights cluster at 400 / 500 / 700 / 800** — Regular for body, Demi for card titles, Bold for headers, Extra Bold for the giant hero metrics.
- **Tight tracking at hero sizes** (-1.6pt on the 76pt Recovery number, -1.0pt at 56pt strain), neutral at body.
- **Mixed-unit numbers stay tabular** — "6h 12m" sleep duration, "57 ms" HRV; the unit letters share the same monospaced cadence.
- **Bright white is reserved for hero metrics**; body text sits at `#F5F5F7` and meta at `#A1A1AA`.
- **Dynamic Type respected on body and explanations**, fixed on hero metrics, tab labels, chart axes.

## 4. Component Stylings

### Buttons

**Primary CTA ("LOG ACTIVITY", "VIEW DETAILS", "GET STARTED")**
- Background: `#00FF7B` (Strain Green)
- Text: `#0A0A0A`, DIN 2014 14pt Bold, **uppercase**, letter-spacing 1.0pt
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 4pt — almost square, in keeping with the instrument-panel aesthetic (WHOOP buttons are *not* pills)
- Height: 48pt minimum
- Pressed: background `#67E26B`, scale 0.98
- Haptic: `.impactOccurred(.medium)` on tap

**Secondary Outline Button ("CANCEL", "LEARN MORE")**
- Background: transparent
- Border: 1.5pt `#00FF7B` (Strain Green)
- Text: `#00FF7B`, DIN 2014 14pt Bold, uppercase, letter-spacing 1.0pt
- Corner radius: 4pt
- Same height/padding as primary

**Tertiary Text Button**
- Text: `#00FF7B`, DIN 2014 14pt Demi, uppercase, letter-spacing 1.0pt
- No background, 44pt tap target
- Used in lists ("VIEW ALL", "EDIT", "SAVE")

**Critical/Destructive Button ("DELETE", "END WORKOUT")**
- Background: transparent
- Border: 1.5pt `#FF453A`
- Text: `#FF453A`, DIN 2014 14pt Bold, uppercase, letter-spacing 1.0pt

### Cards & Containers

**Recovery Ring (the hero of Overview)**
- Diameter: 240pt on Overview, 80pt as the dashboard summary
- Stroke width: 10pt (on the 240pt hero), 6pt (on summary)
- Background ring: `#2A2A2A`
- Filled ring: gradient that interpolates by recovery score
  - 0–33%: solid `#FF0026` (Recovery Red)
  - 34–66%: gradient red → yellow `#FFDE00`
  - 67–100%: gradient yellow → green `#16EC06`
- The score is a **2pt-wide gap** between the start and end of the stroke — the ring is "open" at the top
- Center stack: "RECOVERY" 14pt 700 uppercase 1.6pt tracking `#A1A1AA` → 76pt 800 tabular `#FFFFFF` "73" → small "%" 24pt 700 inline
- Below the ring: HRV / RHR / SpO₂ / Skin Temp metric strip — 4 columns, each with the value + caps label

**Strain Bar (Overview center)**
- Width: full content width minus 40pt margins
- Height: 12pt
- Background: `#2A2A2A`
- Filled fill: gradient `#005F2F` → `#00FF7B` → `#67E26B`, left to right
- Endpoints: tiny 4pt vertical tick marks at 8 (low), 10 (moderate), 14 (high), 18 (all-out) strain levels with caps labels above
- Current strain marker: a 16pt × 16pt white circle slides along the bar position; its center value matches the strain number
- Animation: marker slides smoothly via spring (response 0.5, damping 0.7)

**Sleep Stage Chart (Sleep tab)**
- Horizontal stacked bar, 32pt tall, full width
- Segments: REM `#9C4DFF`, Deep `#3D3DFF`, Light `#3DD9FF`, Awake `#5F5F65`
- Time-of-night axis below: 11pm – 7am with hourly tick marks in 10pt 500 `#A1A1AA`
- Tap a segment: pops a callout showing the segment's start/end time + duration in tabular numerals
- The chart is the centerpiece of the Sleep tab and gets a 32pt margin above and below

**Metric Card (Recovery / Sleep / Strain summary on Overview)**
- Background: `#1C1C1E` (Surface 1)
- Border: 1pt `#252525`
- Corner radius: 12pt
- Padding: 20pt all sides
- Layout:
  - Top row: small 6pt-stroke ring on the left (80pt diameter) → metric label + value on the right (caps label above, hero number below)
  - Bottom row: 3–4 supplementary metrics in a horizontal flexbox with caps labels
- No shadow — defined entirely by edge contrast with the canvas

**Activity Row (Activity / Workout list)**
- Height: 72pt
- Background: `#0A0A0A` with a 0.5pt `#252525` bottom divider
- Layout (leading to trailing):
  - 40pt circular activity-type icon (e.g., `figure.run` outlined Strain Green)
  - 12pt gap → activity name 15pt 500 white + strain value 18pt 700 tabular Strain Green underneath
  - flex spacer → duration "1h 32m" 14pt 500 white + start time 12pt 400 `#A1A1AA` underneath
  - 12pt trailing chevron `#5F5F65`
- Tap haptic: `.selectionChanged()`, opens the workout detail

**Coaching Card (Coaching tab)**
- Background: gradient `#1C1C1E` → `#0A0A0A` top-to-bottom
- Border: 1pt `#252525`
- Corner radius: 16pt
- Padding: 24pt
- Top: caps category label (e.g., "SLEEP COACHING") 11pt 700 `#A1A1AA` 1.2pt tracking
- Headline: "You're getting plenty of REM but light sleep is fragmented." DIN 2014 18pt 700 white
- Body: 14pt 400 `#F5F5F7` explanation
- Action button: tertiary text link in Strain Green at the bottom

**Heart-Rate Live Card**
- A wide card showing a real-time heart-rate trace
- Background: `#1C1C1E`
- Trace: 2pt Strain Green polyline that scrolls right-to-left at 60fps when live
- Big BPM number: 36pt 800 tabular `#FFFFFF` with a 16pt heart glyph in Strain Green pulsing on each beat (scale 1.0 → 1.15 → 1.0 over 600ms)
- Min/Max/Avg pills below the trace, each in `#2A2A2A` fill with caps labels

### Navigation

**Large-Title Nav Bar**
- Height: variable — 88pt with large title, collapses to 44pt inline
- Background: `#0A0A0A`, becomes `.systemThinMaterialDark` blur when content scrolls under
- Title: DIN 2014 24pt 700 `#FFFFFF`, left-aligned, 20pt leading inset
- Trailing: 24pt SF Symbol or DIN-icon (often `bell` or `gearshape`)
- Below title row (Overview only): tiny strain/recovery/sleep summary chip strip

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#1C1C1E` with 0.5pt `#252525` top divider; `.systemThinMaterialDark` blur when content scrolls beneath
- Tabs (4): Overview, Coaching, Community, Stats (all uppercase labels)
- Icon size: 22pt, custom WHOOP DIN-icons (not SF Symbols — WHOOP ships a proprietary icon set)
- Active tint: `#00FF7B` Strain Green (icon + label glow with a soft 4pt blur)
- Inactive: `#5F5F65` Gray 600
- Labels: DIN 2014 10pt 700 uppercase, 0.8pt tracking
- Tap haptic: `.selectionChanged()`

**Top Day Switcher (Overview)**
- A pinned strip below the large nav title: 7 day-of-week chips for the past week + today
- Selected day: filled circle `#00FF7B` Strain Green background with `#0A0A0A` day label inside
- Other days: 36pt × 36pt outline circle with `#A1A1AA` label
- Tap a day: spring scale + content fades

### Input Fields

**Search Field**
- Background: `#1C1C1E`
- Height: 44pt
- Corner radius: 8pt
- Leading 16pt `magnifyingglass` `#A1A1AA`
- Placeholder: "Search activities" DIN 2014 14pt 400 `#5F5F65`
- Focused border: 1pt `#00FF7B`

**Numeric Stepper (Stress / Recovery markers in Journal)**
- Plus/minus 32pt circles in `#1C1C1E`
- Value: 18pt 700 tabular `#FFFFFF`
- Tick marks at integer values; the slider rail is `#2A2A2A` with a Strain Green fill up to the current value

### Distinctive Components

**Strain Coach Wheel**
- A radial gauge with the recommended strain target (e.g., "13.2") highlighted as a green arc on a `#2A2A2A` track
- The current strain is a `#00FF7B` filled arc within the target zone
- Numerals at 0 (low), 7 (moderate), 14 (high), 21 (all-out) around the wheel
- Tap haptic: `.selectionChanged()` when scrubbing the target

**Live Workout Banner**
- A persistent thin bar at the top of the screen during a recorded activity
- Background: `#00FF7B` Strain Green
- Text: "LIVE — 32:14 · STRAIN 11.8" in DIN 2014 12pt 700 `#0A0A0A` uppercase 0.6pt tracking
- A pulsing white dot leads the text — opacity 0.4 → 1.0 every 1200ms

**Battery / Sync Status Pill**
- A small pill in the nav bar trailing position showing strap battery + last sync
- 28pt height, `#2A2A2A` background, 14pt corner radius
- Battery icon + percent + sync clock — all DIN 2014 11pt 700 uppercase

**Journal Quick-Log Tile**
- A 64pt × 64pt rounded square (12pt radius) with an emoji or icon centered and a caps label below
- Selected state: 1.5pt Strain Green border, scaled 1.04
- Unselected: `#1C1C1E` background
- Used for: alcohol, caffeine, stress, soreness, illness — daily journal toggles

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80, 120
- Standard horizontal margin: 20pt on Overview, 16pt on lists
- Section vertical gap: 48pt between major sections — large negative space is part of the look
- Negative space around the Recovery ring: at least 24pt on all sides

### Grid & Container
- Content width: full device width minus 16–20pt margins
- Recovery ring: centered in a full-width hero container, 24pt top and 32pt bottom padding
- Activity list: full-width rows, no internal margins
- Coaching cards: stacked vertically with 16pt gaps

### Whitespace Philosophy
- **Black breathes** — the canvas is a presence, not an absence. Large vertical gaps between cards (32pt+) let the neon accents "vibrate" against the pitch-black panel.
- **Numbers anchor the screen** — the 76pt Recovery % is the gravity well; everything else flows around it.
- **Hard edges over soft elevations** — WHOOP uses 1pt hairline borders to separate cards on the canvas; shadows are nearly invisible because the canvas is already as dark as a shadow.

### Border Radius Scale
- Square (0pt): primary CTAs frequently — WHOOP's instrument-panel buttons are 4pt at most
- Small (4pt): primary buttons, badges, ribbon tags
- Medium (8pt): search bar, input fields
- Standard (12pt): metric cards, coaching cards
- Comfortable (16pt): hero containers, top-of-sheet rounded corners
- Soft (24pt): bottom-sheet top corners
- Full Pill (500pt): rare — only used on chip pills (battery status, alert toasts)
- Circle (50%): day-switcher dots, activity-type icons, stepper buttons, recovery ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, single 1pt `#252525` border | Most cards — WHOOP is edge-defined, not elevated |
| Hairline (Level 1) | 1pt `#252525` border, no shadow | All metric/activity/coaching cards |
| Soft Glow (Level 2) | `rgba(0,255,123,0.18) 0 0 12px` | Active tab icon, focused button border, live indicator |
| Heavy Glow (Level 3) | `rgba(0,255,123,0.32) 0 0 24px` | Live workout banner, strain marker on bar |
| Sheet (Level 4) | `rgba(0,0,0,0.6) 0 -12px 32px` | Bottom sheets, modals, photo lightbox |
| Blur Material | `.systemThinMaterialDark` over `#0A0A0A` at 92% | Sticky nav bar on scroll, tab bar |

**Shadow Philosophy**: WHOOP rarely uses neutral shadows — the canvas is too dark for them to register. Instead, *neon glows* serve as elevation cues. The active tab icon emits a soft 12pt green halo. The live workout banner pulses with a heavier 24pt green halo. This is sci-fi UI elevation — the brightness is the elevation. Hairline 1pt `#252525` borders separate cards on the canvas.

### Motion
- **Recovery ring fill**: when the score updates (after a sleep recording), the ring's stroke-dashoffset animates over 1200ms ease-out with a slight overshoot
- **Strain marker slide**: as you log activity, the white circle on the strain bar springs to the new position (response 0.5, damping 0.7)
- **Heart-rate pulse**: the heart glyph next to the live BPM scales 1.0 → 1.15 → 1.0 over 600ms, syncing with the beat
- **Live banner pulse**: leading dot opacity 0.4 → 1.0 → 0.4 every 1200ms
- **Day switcher tap**: selected day scales 1.0 → 1.08 → 1.0 over 200ms spring with `.selectionChanged()` haptic
- **Primary CTA tap**: scale 0.98 over 100ms, then `.impactOccurred(.medium)` haptic
- **Sleep stage segment tap**: callout slides up 200ms ease-out, opacity 0 → 1; tap haptic `.impactOccurred(.light)`
- **Sync complete**: green checkmark icon pulses scale 1.0 → 1.3 → 1.0 once with `.notificationOccurred(.success)` haptic

## 7. Do's and Don'ts

### Do
- Use `#0A0A0A` as the canvas everywhere — WHOOP is dark-mode-only
- Use Strain Green `#00FF7B` as the primary action color — CTAs, active tabs, links
- Let the Recovery ring interpolate red → yellow → green by score; never override the color
- Use DIN 2014 — license it, or substitute Inter / Barlow when DIN 2014 isn't available
- Set tabular numerals on every metric — HRV, RHR, strain, sleep duration, percentages
- Use ALL CAPS with wide letter-spacing for headers, button labels, tab labels, meta tags
- Use 1pt `#252525` hairline borders instead of shadows for card definition
- Use neon glows (12-24pt blur, ~20% opacity) as elevation cues for live/active elements
- Reserve Sleep Blue `#0093E7` for the Sleep tab and sleep-stage visualizations
- Keep the strain bar gradient `#005F2F` → `#00FF7B` → `#67E26B` left to right
- Render activity-type icons as 40pt outlined Strain Green glyphs

### Don't
- Don't add a light mode — WHOOP is dark-canvas-only by design
- Don't use Strain Green for non-strain metrics — green means strain or "go", recovery has its own ramp
- Don't use neutral drop shadows — the canvas is already too dark; use glows instead
- Don't break the all-caps treatment on headers and buttons — it's the most recognizable typographic move
- Don't compress the Recovery ring smaller than 80pt — below that, the giant number stops being legible
- Don't use full pill corner radii on primary buttons — 4pt is the instrument-panel radius
- Don't replace DIN 2014 with a humanist sans (Inter is the only acceptable fallback because it shares the mechanical proportions)
- Don't use the Recovery red `#FF0026` for general alerts — that's `#FF453A` Alert Red; the two are intentionally different
- Don't add a 5th tab to the bottom nav — Overview, Coaching, Community, Stats are the canonical four
- Don't break tabular numerals on any metric value — the cockpit aesthetic depends on aligned digits

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Recovery ring shrinks to 200pt; activity rows compress slightly |
| iPhone 13/14/15 | 390pt | Standard 240pt ring; 20pt horizontal margins |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; large nav title nudges down 8pt |
| iPhone 15/16 Pro Max | 430pt | Recovery ring up to 260pt; day switcher fits 10 chips |
| iPad | 768pt+ | 2-column dashboard (Recovery / Strain side by side); coaching becomes a 3-col grid |

### Dynamic Type
- Body, coaching headlines, activity names, large nav title: scale up to XXL
- **Fixed**: Recovery hero number, Strain hero, Sleep hero, tab labels, chart axes, metric labels

### Orientation
- Portrait-locked on iPhone for all primary screens
- Strain trends and HRV history support landscape on Pro Max (full-width chart)
- iPad supports both orientations natively

### Touch Targets
- Day switcher chips: 36pt visual, 44pt hit slop
- Primary CTA: 48pt height, full width minus 40pt margins
- Tab bar icons: 22pt icon, 56pt row height
- Sleep stage segment: full segment height (32pt), minimum 24pt hit-width

### Safe Area Handling
- Top: large nav title starts at safe-area top + 16pt
- Bottom: tab bar honors home indicator
- Live workout banner sits *above* the safe-area top, replacing the status bar background

## 9. Quick Reference Cheat Sheet

### Color Quick Reference
- Canvas: `#0A0A0A`
- Surface 1: `#1C1C1E`
- Surface 2: `#2A2A2A`
- Divider: `#252525`
- Strain Green: `#00FF7B`
- Strain Green Bright: `#67E26B`
- Recovery Red: `#FF0026`
- Recovery Yellow: `#FFDE00`
- Recovery Green: `#16EC06`
- Sleep Blue: `#0093E7`
- REM Purple: `#9C4DFF`
- Deep Sleep Indigo: `#3D3DFF`
- Light Sleep Cyan: `#3DD9FF`
- Bright White: `#FFFFFF`
- Soft White: `#F5F5F7`
- Gray 400: `#A1A1AA`
- Gray 600: `#5F5F65`
- Alert Red: `#FF453A`
- Premium Gold: `#D4AF37`

### Example Component Prompts
- "Create a SwiftUI WHOOP Recovery ring: 240pt diameter, 10pt stroke, background ring `#2A2A2A`. The fill color interpolates by score: red `#FF0026` for 0–33%, gradient to yellow `#FFDE00` at 33–66%, gradient to green `#16EC06` at 67–100%. The ring is *open* at the top — a 2pt gap. Center stack: 'RECOVERY' caps label 14pt 700 1.6pt tracking `#A1A1AA`, then '73' DIN 2014 76pt 800 tabular white with a small '%' 24pt inline. Animate the fill via stroke-dashoffset over 1200ms ease-out."
- "Build the WHOOP strain bar: full-width minus 40pt margins, 12pt tall, background `#2A2A2A`. Filled with a left-to-right gradient `#005F2F` → `#00FF7B` → `#67E26B`. A 16pt white circle slides along the bar to mark the current strain. Above the bar: four tick marks at strain values 8 (low), 10 (moderate), 14 (high), 18 (all-out) with caps labels in DIN 2014 10pt 700 `#A1A1AA` 0.8pt tracking. Spring the marker to position on update (response 0.5, damping 0.7)."
- "Design the WHOOP primary CTA: 4pt corner radius (almost square), Strain Green `#00FF7B` fill, dark `#0A0A0A` text in DIN 2014 14pt Bold uppercase with 1.0pt letter-spacing — 'LOG ACTIVITY'. Padding 14pt vertical and 28pt horizontal, 48pt min height. Pressed state: bg shifts to `#67E26B`, scale 0.98. Haptic `.impactOccurred(.medium)` on tap."
- "Build the WHOOP sleep stage chart: a 32pt-tall horizontal stacked bar full-width. Segments in this order, left to right by time: Awake `#5F5F65`, Light `#3DD9FF`, Deep `#3D3DFF`, REM `#9C4DFF`. Time-of-night axis below with hourly labels from 11pm to 7am in DIN 2014 10pt 500 tabular `#A1A1AA`. Tap a segment: pop a callout in 14pt 700 white showing the segment's start/end + duration. Haptic `.impactOccurred(.light)`."
- "Create the WHOOP activity row: 72pt tall, `#0A0A0A` background with a 0.5pt `#252525` bottom divider. Leading: 40pt outlined Strain Green activity icon (e.g., `figure.run`). Next: activity name 'Running' DIN 2014 15pt 500 white, with strain value '11.8' 18pt 700 tabular Strain Green underneath. Trailing: duration '1h 32m' 14pt 500 white, start time '6:42 AM' 12pt 400 `#A1A1AA` underneath, 12pt chevron `#5F5F65` far right."

### Iteration Guide
1. Canvas is `#0A0A0A` — pitch black on every screen; no light mode exists
2. Primary action is Strain Green `#00FF7B`; the Recovery ring uses its own ramp (red → yellow → green)
3. Use DIN 2014 — Inter or Barlow are the only acceptable fallbacks
4. ALL CAPS with 0.8–1.6pt letter-spacing on headers, button labels, tab labels, meta tags
5. Tabular numerals on every metric — HRV, RHR, strain, sleep duration, percentages
6. 4pt corner radius on primary buttons — instrument-panel, not pill
7. 1pt `#252525` hairline borders instead of shadows for card definition
8. Neon glows (12–24pt blur, ~20% opacity) as elevation cues for live elements
9. Recovery ring is open at the top — a 2pt gap between the start and end of the stroke
10. Tab bar is 4 tabs (Overview, Coaching, Community, Stats); active tint is Strain Green with a soft glow
