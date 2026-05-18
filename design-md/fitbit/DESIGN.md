# Design System Inspiration of Fitbit (iOS)

## 1. Visual Theme & Atmosphere

Fitbit's iOS app is a warm, encouraging health dashboard organized around a single screen: **Today**. The atmosphere is "supportive coach," not "clinical chart" — the app celebrates progress, nudges gently, and turns biometric data into friendly, glanceable cards. The dark theme is a deep teal-black (`#001017` → `#002A3A`) that carries the brand's cyan undertone rather than a neutral gray, so the whole product feels distinctly Fitbit even before any color appears. Content is laid out as a vertical scroll of generously rounded cards and tiles (16–24pt radii), each one a self-contained "stat object" with a big number, a metric icon, a short label, and a progress ring or trend chip.

The brand color is **Fitbit Teal** (`#00B0B9`) — it is the logo, the steps/activity color, the primary CTA, and the active tab. On dark backgrounds it brightens to a more luminous teal (`#21D9CE`) for text and links so it never muddies. The defining system characteristic is **per-metric color coding**: every health metric owns one fixed hue and keeps it everywhere it appears (tile icon, ring, chart line, detail screen). Steps/Activity = teal `#00B0B9`, Heart Rate = coral `#FF6B81`, Sleep = purple `#7C5CFF`, Daily Readiness = lime `#B8E986`, Active Zone Minutes = orange `#FF8A3D`, Calories = gold `#FFC233`. This means a user can recognize "that's my sleep data" purely by the purple, anywhere in the app — color is a navigational and mnemonic system, not decoration.

The signature visual moments are: the **steps ring hero** at the top of Today (a large circular progress ring with the step count centered and an encouraging side message), the grid of **metric tiles** (rounded squares, each in its metric color), the **Sleep score** card (a 0–100 score with a stage breakdown), and the **Daily Readiness** card (a 0–100 lime score with a plain-language recommendation). Progress rings are the recurring shape language — Fitbit thinks in rings: steps, sleep stages, zone minutes, hydration.

Typography is a rounded-friendly geometric sans. Fitbit uses a humanist face close to **DM Sans** for everything: huge numbers for hero metrics (40–44pt bold, tight tracking), warm 22pt greetings ("Good morning, Maya"), 18pt bold card titles, and approachable 16pt body. Numbers are the loudest element on every screen — the design philosophy is "make the number the hero, make the message kind."

**Key Characteristics:**
- Deep teal-black dark canvas (`#001017` → `#002A3A`) — cyan undertone, not neutral gray
- Fitbit Teal (`#00B0B9`) brand / steps / CTA; brightens to `#21D9CE` for text on dark
- **Per-metric color system**: each metric owns a fixed hue everywhere (steps teal, HR coral, sleep purple, readiness lime, zone orange, calories gold)
- Today dashboard: vertical scroll of generously rounded cards & tiles (16–24pt)
- Steps ring hero — big circular progress ring + centered count + encouraging side copy
- Metric tile grid — rounded squares, big number, metric icon chip, sub-line
- Sleep score (0–100) + stage breakdown; Daily Readiness score (0–100, lime)
- Progress rings everywhere — the recurring shape language
- Trend chips — "▲ 12% vs last week" pill in metric color
- Encouraging, plain-language coaching copy ("You're close!", "Good to go")
- Bottom tab bar (4): Today, Coach, Community, You
- Pill-shaped CTAs; big bold numbers as the visual hero of every card

## 2. Color Palette & Roles

### Brand / Primary (Interactive)
- **Fitbit Teal** (`#00B0B9`): Brand color, steps/activity metric, primary CTA background, active tab (light surfaces).
- **Teal Bright** (`#21D9CE`): On-dark variant for text, links, active tab label, and ring on the dark canvas (keeps legibility).
- **Teal Pressed** (`#008A91`): Pressed state of teal buttons.
- **Teal Tint** (`#00B0B926`): 15% teal wash behind selected rows / teal chips.

### Per-Metric Hues (fixed across all themes & surfaces)
| Metric | Color | Use |
|--------|-------|-----|
| Steps / Activity | `#00B0B9` | Steps ring, distance, floors |
| Heart Rate | `#FF6B81` | HR tile, BPM chart, cardio zones |
| Sleep | `#7C5CFF` | Sleep score, stages, sleep ring |
| Daily Readiness | `#B8E986` | Readiness score ring & card |
| Active Zone Minutes | `#FF8A3D` | Zone minutes ring, weekly target |
| Calories | `#FFC233` | Calories burned tile & chart |
| SpO2 / Stress | `#4FC3F7` | Health Metrics secondary tiles |

### Canvas & Surfaces (Dark — default)
- **Canvas** (`#001017`): Primary background — deep teal-black.
- **Surface 1** (`#002A3A`): Cards, metric tiles, sheets.
- **Surface 2** (`#00384D`): Elevated/nested surfaces, selected tile.
- **Surface 3** (`#0A475E`): Progress-ring track, pressed rows, chip backgrounds.
- **Divider** (`#143E50`): 1pt hairlines.

### Canvas & Surfaces (Light — secondary theme)
- **Canvas Light** (`#FFFFFF`): Light background.
- **Surface 1 Light** (`#F2F7F8`): Cards, tiles.
- **Surface 3 Light** (`#E3EEF0`): Ring tracks, chips.
- **Divider Light** (`#DCE7E9`): Hairline dividers.

### Text
- **Text Primary Dark** (`#EAF6F9`): Primary text, hero numbers (near-white with a faint cyan cast).
- **Text Secondary Dark** (`#8AAEB8`): Labels, metadata, sub-lines.
- **Text Tertiary Dark** (`#5C808B`): Disabled, very low-emphasis.
- **Text Primary Light** (`#0B2A33`): Primary text on light.
- **Text Secondary Light** (`#577079`): Secondary text on light.

### Semantic (status)
- **Success / On-track** (`#3FC7A6`): Goal hit, positive trend (teal-leaning green).
- **Warning / Behind** (`#FF8A3D`): Behind target (reuses Zone orange).
- **Alert / High** (`#FF6B81`): Elevated HR alert, irregular reading (reuses HR coral).
- **Info** (`#21D9CE`): Informational tips (reuses Teal Bright).

### Color → Role Table

| Role | Dark | Light |
|------|------|-------|
| Canvas | `#001017` | `#FFFFFF` |
| Surface 1 | `#002A3A` | `#F2F7F8` |
| Surface 3 / ring track | `#0A475E` | `#E3EEF0` |
| Divider | `#143E50` | `#DCE7E9` |
| Brand / CTA | `#00B0B9` | `#00B0B9` |
| On-dark teal (text/link) | `#21D9CE` | `#008A91` |
| Text primary | `#EAF6F9` | `#0B2A33` |
| Text secondary | `#8AAEB8` | `#577079` |
| Heart Rate | `#FF6B81` | `#FF6B81` |
| Sleep | `#7C5CFF` | `#7C5CFF` |
| Readiness | `#B8E986` | `#B8E986` |

## 3. Typography Rules

### Font Family
- **Primary**: Fitbit's brand sans — use **DM Sans** (by Colophon Foundry / Indian Type Foundry, SIL OFL) as the open stand-in. A geometric, slightly rounded humanist optimized for friendly numeric display.
- **Numerals**: DM Sans with tabular figures for any tabular data (charts, lists); proportional for big hero numbers.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Metric Hero | DM Sans | 44pt | 700 | 1.0 | -1.0pt | Big number inside steps ring / detail header |
| Screen Title | DM Sans | 32pt | 700 | 1.15 | -0.5pt | "Today", "Sleep" large nav title |
| Tile Value | DM Sans | 26pt | 700 | 1.0 | -0.5pt | The big number on each metric tile |
| Greeting | DM Sans | 22pt | 700 | 1.2 | -0.3pt | "Good morning, Maya" |
| Card Title | DM Sans | 18pt | 700 | 1.25 | -0.1pt | "Daily Readiness", "Sleep Score" |
| Body | DM Sans | 16pt | 400 | 1.5 | 0pt | Coaching copy, descriptions |
| Tile Value Inline | DM Sans | 15pt | 600 | 1.3 | 0pt | "68 bpm · Resting" |
| Label | DM Sans | 14pt | 400 | 1.4 | 0pt | "of 10,000 steps", metadata |
| Unit Suffix | DM Sans | 13pt | 600 | 1.0 | 0pt | "bpm", "min", "h" after a value |
| Caption / Chip | DM Sans | 12pt | 600 | 1.35 | 0.1pt | "Score 84 · Good", trend chips |
| Tab Label | DM Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | DM Sans | 15pt | 700 | 1.0 | 0pt | Primary / secondary |

### Principles
- **The number is the hero**: hero metrics are 40–44pt bold with tight negative tracking — the loudest element on the card. Labels and units are deliberately quieter.
- **Warm, plain language**: greetings and coaching copy are sentence case, friendly, never clinical ("You're close!" not "Step goal: 78% complete").
- **Weight concentrated at 400 / 600 / 700**: body 400, sub-values/units 600, all titles & numbers 700. No thin, no black.
- **Tight tracking on display, normal on body**: big numbers and titles get negative letter-spacing for a modern feel; body stays at 0.
- **Units are subordinate**: a unit ("bpm", "min") is rendered ~half the size and a step lighter than its value, often baseline-aligned.
- **Dynamic Type**: greeting, card titles, body, labels scale; hero/tile numbers scale one step then truncate; tab labels and unit suffixes stay FIXED.

## 4. Component Stylings

### Buttons

**Primary Button (Log Activity / Start / Continue)**
- Shape: Fully rounded pill (radius 500pt)
- Background: `#00B0B9` (Fitbit Teal)
- Text: `#001017` (deep ink on teal — high contrast, never white)
- Padding: 13pt vertical, 28pt horizontal; full-width on forms (height 52pt)
- Font: DM Sans 15pt weight 700
- Pressed: background `#008A91` + scale 0.98
- Disabled: background `#00B0B9` at 35% opacity, text `#001017` at 50%

**Secondary / Outline Button (View Trends)**
- Background: transparent; Text `#21D9CE` (on dark) / `#008A91` (on light)
- Border: 1.5pt `#21D9CE`
- Corner radius: pill; Padding: 11pt / 22pt
- Font: DM Sans 14pt weight 700
- Pressed: background `#00B0B926`

**Ghost Button (Skip / Dismiss)**
- Background: `#0A475E` (Surface 3); Text `#EAF6F9`
- Corner radius: pill; Padding: 12pt / 20pt
- Font: DM Sans 14pt weight 600
- Pressed: lightens ~10%

**Text Button (See all insights)**
- Background: transparent; Text `#21D9CE`; Font DM Sans 14pt weight 700; no underline
- Pressed: text dims to 70%

### Core Atoms

**Progress Ring** (the signature shape)
- Circular stroke, rounded line caps, stroke width ~9–11pt
- Track: `#0A475E` (Surface 3) on dark / `#E3EEF0` on light
- Progress: the metric's fixed color
- Centered content: big number + small label, or a score
- Fills clockwise from 12 o'clock; overshoot (>100%) wraps with a small overlap tick

**Metric Tile**
- Rounded square card, 20pt radius, `#002A3A` bg, 16pt padding
- Top row: metric-color icon chip (24pt, 7pt-radius rounded square, `#001017` glyph) + label DM Sans 12pt w600 `#8AAEB8`
- Big value DM Sans 26pt w700 `#EAF6F9` with subordinate unit
- Sub-line DM Sans 12pt `#5C808B`

**Metric Icon Chip**
- 24pt rounded square (radius 7pt) filled with the metric color; glyph in `#001017` (deep ink) for contrast

**Trend Chip**
- Pill, metric-color @15% bg, metric-bright text 11–12pt w700, leading ▲/▼ glyph — "▲ 12% vs last week"

**Score Badge**
- Circular outline (4pt stroke in the metric color) with the 0–100 number centered in the metric color, used for Sleep & Readiness

**Tag / Status Pill**
- "Good", "On track", "Behind" — pill, semantic color @15% bg, semantic text 12pt w700

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#001017` at 96% with 1pt top border `#143E50`
- Tabs (4): Today, Coach, Community, You
- Icon size: 22pt
- Active: `#21D9CE` (filled icon + bright-teal label)
- Inactive: `#5C808B` (outline icon + tertiary label)
- Labels: DM Sans 10pt w600, always shown
- No tint pill — just an icon fill + color change

**Top of Today (no nav bar)**
- Today opens directly into content with a greeting + avatar row (no chrome bar)
- On detail screens: 44pt + safe area nav, leading back chevron `#EAF6F9` 22pt, center title DM Sans 18pt w700, trailing share/⋯

**Segmented Range Control (Day / Week / Month)**
- Pill track `#0A475E`; selected thumb `#00B0B9` with `#001017` text; used on metric detail charts

### Input Fields

**Search Bar (Discover / Community)**
- Height: 40pt; Background: `#0A475E`; Border: none; Corner radius: 999pt (full pill)
- Leading `magnifyingglass` 16pt `#8AAEB8`
- Placeholder: "Search" DM Sans 14pt `#8AAEB8`
- Focus: 1.5pt `#21D9CE` border

**Goal / Value Input (set step goal, bedtime)**
- Large centered DM Sans value (32–40pt) `#EAF6F9`; stepper `−`/`+` 36pt circular `#0A475E` buttons flanking
- Or a wheel/picker themed in teal for time goals

**Log Entry Field (weight, water)**
- Height: 52pt; Background: `#002A3A`; Corner radius: 16pt; value DM Sans 18pt w600; trailing unit `#8AAEB8`
- Error: 1.5pt `#FF6B81` border + helper `#FF6B81` 12pt

### Distinctive Components

**Steps Ring Hero**
- Large card (24pt radius, `#002A3A`) with a ~116pt progress ring on the left
- Ring track `#0A475E`, progress `#00B0B9`, rounded caps
- Centered: step count DM Sans 26pt w700 + "of 10,000 steps" 11pt `#8AAEB8`
- Right side: encouraging headline DM Sans 15pt w700 + supporting line 13pt `#8AAEB8` + a trend chip
- Tapping opens the steps detail with a Day/Week/Month chart

**Metric Tile Grid**
- 2-column grid of metric tiles (Heart Rate, Sleep, Active Zone, Calories), each in its own color
- Each tile is tappable → metric detail screen

**Sleep Score Card**
- 18pt-radius card; left: score badge (0–100) in sleep purple `#7C5CFF`; right: "7h 24m · Good" + a horizontal stacked stage bar (Deep / Light / REM / Awake) using purple shades
- Tap → sleep stages timeline

**Daily Readiness Card**
- Distinct gradient card (deep green `#0E3A2E → #14564A`) with a lime score ring (`#B8E986`, 0–100) and a plain-language recommendation ("Good to go. Your body is ready for a moderate workout today.")

**Coaching Banner**
- Full-width rounded (20pt) card with a friendly headline + teal CTA pill — celebrates streaks, suggests workouts

**Active Zone Minutes Ring**
- Weekly ring in zone orange `#FF8A3D` with current/goal minutes centered; a small flame/lightning glyph

**Stat Chart (detail screens)**
- Bar or line chart in the metric color on a `#001017` plot; gridlines `#143E50`; rounded bar caps; selected bar shows a floating value bubble in the metric color; Day/Week/Month segmented control above

**Streak / Badge**
- Circular badge with a metallic-ish fill and a number of days; celebratory confetti micro-animation on new milestone

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40
- Screen horizontal inset: 20pt
- Card internal padding: 16–22pt; inter-card vertical gap: 16pt
- Tile grid gap: 12pt
- Greeting block to first card: 14pt

### Grid & Container
- iPhone: 20pt horizontal insets; cards are inset (not full-bleed) so rounded corners read
- Metric tiles: 2-column responsive grid
- iPad: multi-column tile grid (3–4 across); Today becomes a dashboard mosaic
- Charts: full-width within their card, 16pt internal padding

### Whitespace Philosophy
- **Cards as breathing stat objects**: every metric lives in its own generously-padded rounded card with air around it — nothing is edge-to-edge or cramped
- **The number gets space**: hero/tile numbers have generous internal padding so the big figure dominates
- **Rounded everything**: 16–24pt card radii and pill buttons create a soft, encouraging, non-clinical feel
- **Color does the grouping**: metric color (not borders or heavy dividers) ties a metric's tile, ring, and chart together

### Border Radius Scale
- Square (0pt): chart plot baselines, full-bleed dividers
- Subtle (8pt): small chips, metric icon chip (~7pt)
- Standard (12pt): inputs, small inner elements
- Tile (16–20pt): metric tiles, content cards
- Hero Card (24pt): steps-ring hero, large feature cards
- Pill (500pt): all buttons, search bar, segmented controls, trend chips
- Ring (50%): progress rings, score badges, avatars

## 6. Depth & Elevation

Fitbit is soft and mostly flat — depth comes from rounded surface tiles on the teal-black canvas plus very gentle shadows. The vibe is "calm and tactile," never harsh.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; surface tile only | Most cards/tiles sit directly on canvas |
| Soft Card (Level 1) | `rgba(0,0,0,0.25) 0 4px 16px` | Feature cards, coaching banner |
| Floating (Level 2) | `rgba(0,0,0,0.35) 0 8px 24px` | Bottom sheets, log/edit modals |
| Sheet (Level 2) | `rgba(0,0,0,0.4) 0 -8px 28px` | Half-sheet pickers (set goal, log) |
| Modal Overlay | `rgba(0,4,8,0.6)` scrim | Behind modals & half-sheets |

**Shadow Philosophy**: Shadows are minimal and soft — they exist to lift sheets and the occasional feature card, never to outline every tile. On the teal-black canvas, color-coded tiles separate themselves by hue and the slightly lighter `#002A3A` surface, so shadows stay subtle. Floating sheets also get a 1pt `#143E50` border as a secondary cue.

### Motion
- **Ring fill**: on screen appear, the progress ring sweeps from 0 to its value over 900ms ease-out (a satisfying "draw"); on goal-hit it pulses once and triggers a success haptic + brief confetti
- **Number count-up**: hero/tile numbers count up from 0 to the value over ~700ms ease-out on first appearance
- **Card stagger-in**: Today cards fade + 8pt slide-up, staggered 40ms apart top-to-bottom
- **Tile tap**: tile scales 1.0 → 0.97 → 1.0 (120ms) then pushes to detail (300ms iOS push)
- **Trend chip**: appears with a tiny pop (scale 0.8 → 1.0, 180ms)
- **Sleep stage bar**: segments grow left-to-right in sequence on appear (600ms total)
- **Pull-to-refresh**: teal ring spinner; haptic on trigger; cards re-stagger
- **Goal celebration**: full-screen confetti + a centered ring "complete" animation + success haptic when a daily goal is reached
- **Range switch (Day/Week/Month)**: chart cross-fades + bars re-grow over 400ms
- **Haptics**: soft success on goal hit, selection tick on range/segment change, light impact on tile tap, celebratory notification haptic on milestone

## 7. Do's and Don'ts

### Do
- Use the deep teal-black canvas `#001017`/`#002A3A` — cyan undertone, not neutral gray
- Make Fitbit Teal `#00B0B9` the brand/steps/CTA; brighten to `#21D9CE` for text/links on dark
- Give every metric its fixed hue and keep it everywhere (tile, ring, chart, detail) — color is a mnemonic
- Make the big number the visual hero of every card (40–44pt bold, tight tracking)
- Use progress rings as the recurring shape language for goals
- Use generously rounded cards (16–24pt) and pill buttons — soft, encouraging
- Write warm, plain-language coaching copy ("You're close!", "Good to go")
- Animate the ring sweep + number count-up on appear; celebrate goal hits with confetti + haptic
- Put deep ink (`#001017`) on teal buttons and metric-color chips — never white
- Use a trend chip in the metric color to show week-over-week change

### Don't
- Don't use a neutral-gray dark theme — the canvas must carry the teal cast
- Don't let metrics share or swap colors — sleep is always purple, HR always coral
- Don't make labels/units compete with the hero number — keep them quieter and smaller
- Don't use clinical phrasing ("78% goal completion") — Fitbit speaks like a kind coach
- Don't use sharp or small radii — cards are 16–24pt, buttons are full pills
- Don't put white text on the teal button or on metric-color chips (use deep ink)
- Don't draw heavy borders/shadows around every tile — hue + surface step does the separation
- Don't skip the ring-sweep / count-up animation — the "draw" is core to the feel
- Don't invert or recolor metric hues in light mode — they're identical across themes
- Don't bury the encouraging message — the kind line is as important as the number
- Don't use rectangular progress bars where Fitbit uses rings (steps, sleep, zone, readiness)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Steps ring ~100pt; tiles stay 2-up but tighter padding |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; greeting starts below it |
| iPhone 15/16 Pro Max | 430pt | Steps ring ~130pt; larger hero numbers |
| iPad (portrait) | 768pt | 3-column tile mosaic; steps ring + readiness side-by-side |
| iPad (landscape) | 1024pt+ | 4-column dashboard; charts expand to full width |

### Dynamic Type
- Greeting, card titles, body, labels: scale with Dynamic Type
- Hero number & tile value: scale one step, then shrink-to-fit / truncate (layout-bounded)
- Unit suffixes, tab labels, trend-chip text: FIXED
- Ring center text: scales within the ring; ring geometry fixed

### Orientation
- Portrait default
- Metric detail / chart screens support landscape (wider chart)
- Today and tab screens lock portrait

### Touch Targets
- Metric tile: full-tile tap (≥120pt tall)
- Tab icons: 22pt glyph, 44pt+ hit
- Pill buttons: ≥48pt tall
- Stepper `−`/`+`: 36pt circle, 44pt hit
- Segmented control segments: ≥44pt tall

### Safe Area Handling
- Top: greeting/avatar row starts below safe area + Dynamic Island
- Bottom: tab bar + home indicator respected
- Keyboard: log/goal inputs scroll above keyboard; the save pill stays visible
- Sides: 20pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#001017`; Surface 1: `#002A3A`; Surface 3 / ring track: `#0A475E`; Divider: `#143E50`
- Brand / CTA: `#00B0B9` (pressed `#008A91`); on-dark text/link: `#21D9CE`
- Steps/Activity: `#00B0B9` · Heart Rate: `#FF6B81` · Sleep: `#7C5CFF` · Readiness: `#B8E986` · Active Zone: `#FF8A3D` · Calories: `#FFC233`
- Text primary: `#EAF6F9` / secondary `#8AAEB8` / tertiary `#5C808B`
- Canvas (light): `#FFFFFF`; text primary light `#0B2A33`; on-light teal `#008A91`

### Example Component Prompts
- "Build the Fitbit steps-ring hero in SwiftUI: a 24pt-radius card `#002A3A`, 22pt padding, HStack. Left: a 116pt `ZStack` ring — a `Circle().stroke(Color(hex:0x0A475E), lineWidth: 11)` track and a `Circle().trim(from: 0, to: progress).stroke(Color(hex:0x00B0B9), style: StrokeStyle(lineWidth: 11, lineCap: .round)).rotationEffect(.degrees(-90))`; centered VStack with step count in DM Sans 26pt w700 `#EAF6F9` and 'of 10,000 steps' in 11pt `#8AAEB8`. Right: VStack — 'You're close!' DM Sans 15pt w700, '2,158 steps to hit your daily goal.' 13pt `#8AAEB8`, and a trend chip pill (`#00B0B9` @15% bg, `#21D9CE` text, '▲ 12% vs last week'). Sweep the ring 0→progress over 900ms ease-out on appear."

- "Create a Fitbit metric tile: 20pt-radius card `#002A3A`, 16pt padding. Top row: a 24pt rounded-7 chip filled `#FF6B81` (Heart Rate color) with a heart glyph in `#001017`, then 'Heart Rate' in DM Sans 12pt w600 `#8AAEB8`. Big value `68` in DM Sans 26pt w700 `#EAF6F9` with a subordinate `bpm` (13pt w600 `#8AAEB8`). Sub-line 'Resting · 52–148 today' in DM Sans 12pt `#5C808B`. Number counts up from 0 over 700ms on appear. Whole tile is tappable to a detail screen."

- "Build the Fitbit Daily Readiness card: a 20pt-radius card with a `LinearGradient([#0E3A2E, #14564A], topLeading→bottomTrailing)`. Left: a 60pt circle with a 4pt `#B8E986` stroke and the score '78' centered in `#B8E986` DM Sans 22pt w700. Right: 'Daily Readiness' DM Sans 15pt w700 `#EAF6F9` and 'Good to go. Your body is ready for a moderate workout today.' DM Sans 12pt `#A9D9C8`."

- "Render the Fitbit Sleep Score card: 18pt-radius `#002A3A`. Left: a circular 0–100 score badge in sleep purple `#7C5CFF` (4pt stroke, number centered in purple). Right: '7h 24m' DM Sans 18pt w700 `#EAF6F9`, 'Score 84 · Good' 12pt w600 `#8AAEB8`, and a horizontal stacked stage bar (Deep/Light/REM/Awake) in shades of `#7C5CFF`. Segments grow left-to-right on appear."

- "Create the Fitbit bottom tab bar: 4 tabs (Today, Coach, Community, You) on a `#001017`@96% bar with a 1pt `#143E50` top border, 56pt + safe area. Active icon + label `#21D9CE` (filled icon), inactive `#5C808B` (outline icon). Labels DM Sans 10pt w600. No tint pill."

- "Build the Fitbit primary CTA: a `Capsule()`, `#00B0B9` background, deep-ink `#001017` DM Sans 15pt w700 text, 13pt vertical / 28pt horizontal padding, full-width 52pt on forms. Pressed: `#008A91` + scale 0.98."

### Iteration Guide
1. Canvas is deep teal-black `#001017`/`#002A3A` — cyan undertone, never neutral gray
2. Fitbit Teal `#00B0B9` is brand/steps/CTA; on dark, text & links use the brighter `#21D9CE`
3. Every metric owns a fixed hue everywhere: steps teal, HR coral `#FF6B81`, sleep purple `#7C5CFF`, readiness lime `#B8E986`, zone orange `#FF8A3D`, calories gold `#FFC233`
4. The big number is the hero of every card — 40–44pt bold, tight negative tracking
5. Progress rings are the recurring shape language (steps, sleep, zone, readiness)
6. Cards are generously rounded (16–24pt), buttons are full pills — soft & encouraging
7. Coaching copy is warm and plain ("You're close!"), never clinical percentages
8. Sweep the ring + count up the number on appear; celebrate goal hits with confetti + a success haptic
9. Deep ink `#001017` goes on teal buttons and metric chips — never white
10. Metric hues are identical in light mode; the light theme only swaps canvas (`#FFFFFF`) and text (`#0B2A33`)
