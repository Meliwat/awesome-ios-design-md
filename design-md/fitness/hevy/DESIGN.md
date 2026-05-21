# Design System Inspiration of Hevy (iOS)

## 1. Visual Theme & Atmosphere

Hevy's iOS app is a dark-first, gym-floor instrument. Open it and you are not greeted by marketing chrome — you are dropped into a near-black training canvas (`#0E1116`) engineered to never glare under fluorescent gym lights or wash out in direct sun. The product's entire reason to exist is the **live workout log**: a vertical stack of exercise cards, each containing a dense **set table** (Set / Previous / kg / Reps / ✓) with numbers in tabular figures so the layout never jitters while you type weights between sets with sweaty thumbs. Everything else — the home feed, the profile, the analytics — orbits this one screen. The signature moment of Hevy is **a workout in progress**: a running duration timer in Hevy Blue at the top, a glanceable rest-timer pill counting down, set rows that flip green as you tick them off, and a gold PR badge that fires the instant you beat a personal record.

The interface philosophy is "a clipboard that does math." There is no decorative photography, no gradients-for-mood, no illustration. Chrome is reduced to what a lifter needs mid-set: the exercise name (always in Hevy Blue, the tap target to swap or superset), a `Previous` column so you know what to beat, two editable number cells, and a checkmark. The rest of the app's surface area is reporting — volume charts, muscle-group splits, 1RM trend lines, a strava-like home feed of friends' workouts — but all of it is rendered in the same disciplined dark palette with the single blue accent.

The color system is monochrome-plus-one. The canvas and three surface tiers are all desaturated blue-greys (`#0E1116` → `#161B22` → `#1F2630` → `#283040`). **Hevy Blue (`#1E6FFF`)** is the *only* action color in the entire product — it is the Finish button, the active tab, every exercise title, every primary CTA, and the rest-timer ring. There is no secondary brand hue. Two semantic colors punctuate the log: a **set-done green (`#2FBF71`)** that washes a completed row at ~16% opacity, and **PR gold (`#F5B83D`)** for the personal-record badge — the app's small dopamine hit. Destructive actions use a muted red (`#F1545B`). This restraint is deliberate: a workout log is read in 2-second glances between sets, so the palette must let the *numbers* be the loudest thing on screen.

Typography is **Inter** throughout — a humanist sans with excellent small-size legibility and, critically, a tabular-figures feature that Hevy leans on hard for every weight, rep, set count, volume total, and timer. Hierarchy is functional: screen titles at 32pt/800, routine names at 26pt/700, exercise names at 18pt/700, metric read-outs at 17pt/700 with `tabular-nums`, body at 16pt/400, the set-table cells at 15pt/600, captions (the `Previous` hint) at 13pt, and column labels at 11pt uppercase. The type system never tries to be expressive — it tries to be unmissable at arm's length while you're under a barbell.

**Key Characteristics:**
- Near-black training canvas (`#0E1116`) dark-first — no light mode default; engineered for gym lighting
- The live **workout log** is the interface: stacked exercise cards, each a dense set table
- **Set table** — Set / Previous / kg / Reps / ✓ — all numerics in tabular figures so rows never shift
- **Hevy Blue (`#1E6FFF`)** is the single action color: Finish, active tab, exercise titles, all CTAs
- **Rest-timer pill** — a soft-blue countdown bar with an animated ring and a Skip affordance
- **PR badge** — gold (`#F5B83D`) "★ NEW PR" chip that fires when a set beats a record
- **Set-done state** — completed rows wash green (`#2FBF71` @ 16%) with a filled checkmark
- **Previous column** — last session's weight×reps shown inline so you always know the target
- Running **duration timer** in Hevy Blue at the top of every active workout
- Inter everywhere with aggressive use of the tabular-figures feature for all numbers
- Strava-style **home feed** of friends' completed workouts (cards, kudos, comments)
- Minimal chrome: 5-tab bottom bar (Home / Profile / Workout / History / Exercises)

## 2. Color Palette & Roles

### Primary (Interactive)
- **Hevy Blue** (`#1E6FFF`): The single action color — Finish button, active tab, exercise titles, primary CTAs, rest-timer ring, links.
- **Hevy Blue Pressed** (`#1857CC`): Pressed state on primary buttons.
- **Hevy Blue Soft** (`#14233F`): 12–16% blue tint — rest-timer pill background, tonal button fill, selected-row glow.

### Canvas & Surfaces (Dark — default)
- **Training Canvas** (`#0E1116`): Primary app background — near-black, blue-cool, the launch surface.
- **Card Surface** (`#161B22`): Exercise cards, feed cards, list rows, sheets.
- **Input Surface** (`#1F2630`): Editable set cells (kg / reps), search field, segmented control track.
- **Surface 3** (`#283040`): Exercise thumbnails, raised chips, hovered/pressed rows.
- **Divider** (`#262C36`): Hairline dividers between cards and table rows.

### Canvas & Surfaces (Light — optional, opt-in)
- **Light Canvas** (`#F4F6FA`): Light-mode background (a minority setting).
- **Light Card** (`#FFFFFF`): Cards on light.
- **Light Input** (`#EEF1F6`): Editable cells on light.
- **Light Divider** (`#E2E6EE`): Hairlines on light.

### Text
- **Text Primary** (`#F2F4F8`): Headings, weights, reps, the numbers that matter — near-white.
- **Text Secondary** (`#9BA3B0`): Labels, captions, column headers, the `Previous` hint.
- **Text Tertiary** (`#5E6675`): Disabled, placeholder, the `⋯` glyph.
- **Text Primary (Light)** (`#11151C`): Primary text on light mode.
- **Text Secondary (Light)** (`#5E6675`): Secondary on light mode.

### Semantic
- **Set Done Green** (`#2FBF71`): Completed-set checkmark fill + row wash at 16% (`#2FBF7129`).
- **PR Gold** (`#F5B83D`): "★ NEW PR" badge background-tint + text; the record-broken signal.
- **Error / Destructive Red** (`#F1545B`): Discard workout, delete set, validation errors.
- **Warning Amber** (`#F5A623`): "Unsaved changes", deload suggestions.
- **Kudos Blue** (`#1E6FFF`): Feed like/kudos active state (same as primary).

### Color Roles Table

| Role | Dark Mode | Light Mode |
|------|-----------|-----------|
| Canvas | `#0E1116` | `#F4F6FA` |
| Card surface | `#161B22` | `#FFFFFF` |
| Input surface | `#1F2630` | `#EEF1F6` |
| Surface 3 / chips | `#283040` | `#E6EAF1` |
| Divider | `#262C36` | `#E2E6EE` |
| Action (Hevy Blue) | `#1E6FFF` | `#1E6FFF` |
| Action pressed | `#1857CC` | `#1857CC` |
| Blue soft fill | `#14233F` | `#E5EEFF` |
| Text primary | `#F2F4F8` | `#11151C` |
| Text secondary | `#9BA3B0` | `#5E6675` |
| Set done | `#2FBF71` | `#1E9E59` |
| PR gold | `#F5B83D` | `#C8881A` |
| Error | `#F1545B` | `#E0353C` |

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — humanist sans with superb small-size legibility and a first-class `tnum` (tabular figures) OpenType feature, which Hevy uses for every number on screen.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numeric rule**: every weight, rep count, set index, volume total, RPE, 1RM, percentage, and timer renders with **tabular figures** (`font-variant-numeric: tabular-nums`) so the set table and timers never reflow as digits change.
- **No alternate families**: unlike note apps, Hevy ships one face. Brand consistency over user choice.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.6pt | "Workout", "History" — large nav title |
| Routine Title | Inter | 26pt | 700 | 1.2 | -0.4pt | Routine / workout name at top of log |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.3pt | "This Week", chart section titles |
| Exercise Name | Inter | 18pt | 700 | 1.3 | -0.2pt | Exercise title — always in Hevy Blue |
| Metric / Timer | Inter | 17pt | 700 | 1.2 | 0pt | Duration, volume, 1RM — **tabular-nums** |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, feed text, settings |
| Set Cell | Inter | 15pt | 600 | 1.3 | 0pt | kg / reps values — **tabular-nums** |
| Card Title | Inter | 15pt | 600 | 1.3 | -0.1pt | Feed card title, list row title |
| Caption | Inter | 13pt | 500 | 1.4 | 0pt | `Previous` hint, timestamps, helper text |
| Column Label | Inter | 11pt | 600 | 1.1 | 0.5pt | SET / PREVIOUS / KG / REPS — uppercase |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary button label |
| Badge | Inter | 10pt | 800 | 1.0 | 0.3pt | PR / streak chips — uppercase |

### Principles
- **Numbers are the loudest element**: weights, reps, and timers use 700 weight + tabular figures; everything supporting them is lighter and greyer.
- **Tabular figures everywhere numeric**: the set table must not reflow when a 9 becomes a 10 — `tnum` is mandatory on every digit.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium captions, semibold cells, bold exercise names + metrics, extrabold screen titles.
- **Hevy Blue carries hierarchy too**: an exercise name is bold *and* blue because it is both the heading and the tap-to-edit affordance.
- **Dynamic Type with floors**: titles, body, and cells scale; column labels, tab labels, and badges stay fixed (table layout is digit-sensitive).
- **Uppercase only for micro-labels**: column headers and badges; never body or titles.

## 4. Component Stylings

### Buttons

**Primary Button (Finish Workout / Save Routine)**
- Shape: rounded rectangle, 12pt corner radius
- Background: `#1E6FFF` (Hevy Blue) / text `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal; full-width variant spans content inset
- Font: Inter 16pt weight 700
- Pressed: background `#1857CC` + scale 0.98
- Disabled: `#1F2630` background, `#5E6675` text

**Secondary / Outline Button (Add Exercise)**
- Background: transparent
- Border: 1.5pt `#1E6FFF`
- Text: `#1E6FFF`, Inter 15pt weight 600
- Corner radius: 12pt; padding 11pt / 22pt
- Pressed: background `#14233F`

**Tonal Button (+ Add Set / inline actions)**
- Background: `#14233F` (Blue Soft) — or `#1F2630` for neutral inline actions
- Text: `#1E6FFF` (or `#9BA3B0` neutral), Inter 14pt weight 700
- Corner radius: 10pt; padding 11pt / 20pt
- Used as the full-width "+ Add Set" row inside an exercise card

**Destructive Text Button (Discard / Delete Set)**
- Background: transparent
- Text: `#F1545B`, Inter 14pt weight 600
- No border; confirmation sheet required before destructive commit

**Icon Button (exercise `⋯`, set swipe actions)**
- 24pt glyph in 44pt hit area
- Default `#5E6675`; active `#F2F4F8`
- Swipe-left on a set row reveals delete (`#F1545B`) and a warm-up toggle

### Core Atoms

**Set Row** (the core atom)
- A table row inside an exercise card: `[set-index chip] [previous] [kg cell] [reps cell] [✓]`
- Set-index chip: 28pt rounded square, `#1F2630` bg, `#9BA3B0` 13pt/700; warm-up sets show `W`, drop sets `D`, failure `F`
- `Previous` cell: `#5E6675` 13pt, format `60 × 10` (last session's matching set)
- Editable cells (kg / reps): `#1F2630` bg, 8pt radius, `#F2F4F8` 15pt/600, tabular figures; focused → 1.5pt `#1E6FFF` border
- Checkmark: 26pt rounded-square; empty = 1.5pt `#262C36` border; checked = filled `#2FBF71` + white tick, and the entire row washes `#2FBF7129`
- Row height: 40pt; 7pt vertical cell padding

**Exercise Card**
- Background `#161B22`, 1pt `#262C36` border, 16pt corner radius, 14pt padding, 14pt gap between cards
- Header: 36pt exercise thumbnail (`#283040`, blue glyph), exercise name (`#1E6FFF` 18pt/700), trailing `⋯`
- Body: the set table
- Footer: full-width "+ Add Set" tonal row (34pt, 9pt radius, `#1F2630`, `#9BA3B0` 13pt/700)

**Rest-Timer Pill**
- 44pt tall, full-width minus 18pt inset, 12pt radius
- Background `#14233F`, 1pt `rgba(30,111,255,0.35)` border
- Leading: 22pt progress ring (3pt stroke, track `rgba(30,111,255,0.3)`, fill `#1E6FFF`, sweeping as time elapses)
- Center: `Rest · 1:24` in `#1E6FFF` 14pt/600 tabular
- Trailing: `Skip` in `#9BA3B0` 13pt/600; tap adds/subtracts 15s with ± controls on long-press

**PR Badge**
- Inline chip: background `#F5B83D` at 18% (`#F5B83D2E`), text `#F5B83D`, Inter 10pt/800 uppercase
- Content: `★ NEW PR · 1RM 87.5 kg` (also fires for best-set-volume and best-reps records)
- Appears under the set table the moment a logged set beats a stored record; a brief gold pulse + soft haptic

**Metric Stat (timer / volume / sets)**
- Stacked: 11pt `#9BA3B0` label over 17pt/700 `#F2F4F8` value (tabular)
- Live duration value tints `#1E6FFF`
- Used in the workout header row and on the post-workout summary

**Feed Card (Strava-like home)**
- `#161B22` card, 16pt radius; header avatar + name + relative time
- Workout title (`#F2F4F8` 15pt/600), stat strip (Duration / Volume / PRs), muscle-split bar
- Footer: kudos (filled `#1E6FFF` when liked) + comment count

### Navigation

**Bottom Tab Bar**
- Height: 68pt incl. home indicator; background `rgba(14,17,22,0.94)` + blur, 0.5pt `#262C36` top border
- 5 tabs: Home, Profile, **Workout** (center), History, Exercises
- Icon 22pt; active color `#1E6FFF` with a soft `rgba(30,111,255,0.16)` icon fill; inactive `#5E6675`
- Labels: Inter 10pt/600, always shown
- No tint pill — active state is icon color + soft fill only

**Top Bar (in-workout)**
- Routine name (26pt/700) leading; trailing `Finish` button (Hevy Blue, 10pt radius, 13pt/700)
- Below: the metric stat row (Duration / Volume / Sets) then the rest-timer pill
- On scroll, a compact title + Finish stay pinned

**Large-Title Screens (Home / History / Exercises)**
- iOS large-title pattern: 32pt/800 title that collapses to a 17pt centered nav title on scroll
- Trailing: contextual icon (filter, add, search)

### Input Fields

**Set Cell Input (kg / reps)**
- 36pt tall, `#1F2630` bg, 8pt radius, centered `#F2F4F8` 15pt/600 tabular
- Numeric keypad; focus → 1.5pt `#1E6FFF` border, value selected for fast overwrite
- Empty placeholder shows the `Previous` value greyed at `#5E6675`

**Search Field (Exercises / Add Exercise)**
- 40pt tall, `#1F2630` bg, 12pt radius, leading 16pt magnifier `#9BA3B0`
- Placeholder "Search exercises" `#5E6675` 15pt
- Focus: 1.5pt `#1E6FFF` border

**Segmented Control (chart range: 1W / 1M / 3M / 1Y)**
- Track `#1F2630`, 10pt radius; selected segment `#283040` with `#F2F4F8` text; unselected `#9BA3B0`
- 32pt tall, equal segments

### Distinctive Components

**The Set Table**
- The signature Hevy component: a per-exercise grid with columns SET / PREVIOUS / KG / REPS / ✓
- Tabular figures lock column widths; the `Previous` column is the killer feature (always know the target)
- Checking a set washes it green and auto-starts the rest timer

**Rest-Timer Pill with Sweeping Ring**
- Auto-starts on set completion; the ring depletes; ±15s controls; soft haptic + sound at 0
- Persists as a Live Activity / Dynamic Island countdown when the app backgrounds

**Gold PR Badge**
- Fires for 1RM, best set volume, or best reps; gold pulse + haptic; collected on the profile's "Records" page

**Post-Workout Summary**
- Full-screen card: duration, total volume, set count, list of PRs, muscle-split donut, share-image button
- The single celebratory moment — confetti-free, just numbers and PR gold

**Muscle-Split Bar / Donut**
- Horizontal stacked bar (or donut) of volume per muscle group; segments use a calm desaturated ramp, never the brand blue

**Friends Feed**
- Strava-style chronological feed of friends' completed workouts with kudos and comments

**1RM / Volume Trend Chart**
- Line chart on `#0E1116`; line in `#1E6FFF`, gridlines `#262C36`, axis labels `#5E6675`; PR points marked in `#F5B83D`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 48, 64
- Content inset: 16–18pt horizontal
- Card padding: 14pt; card-to-card gap: 14pt
- Set-row vertical padding: 7pt; row height ~40pt
- Section gap: 24pt

### Grid & Container
- iPhone: single column, 16–18pt insets, cards full-width
- Set table: 5 columns — index (28pt) / previous (flex) / kg (flex) / reps (flex) / check (26pt)
- iPad: content max-width ~720pt centered; optional 2-column for feed + summary
- Charts: full-bleed minus inset, fixed aspect 16:9

### Whitespace Philosophy
- **Density where it counts**: the set table is intentionally tight so a full exercise fits on screen mid-set
- **Breathing room between exercises**: 14pt gaps + card borders separate exercises clearly
- **Numbers get air**: metric stats and the timer get generous vertical padding so they read at a glance
- **No decorative whitespace**: every pixel serves logging or reading; the app is a tool, not a magazine

### Border Radius Scale
- Square (0pt): chart plot area, full-bleed media
- Subtle (4pt): rarely used micro-elements
- Small (8pt): set cells, set-index chip
- Standard (10pt): tonal buttons, segmented control, Finish button
- Comfortable (12pt): primary/outline buttons, rest pill, search field
- Card (16pt): exercise cards, feed cards, sheets
- Large (20pt): bottom sheets, modals
- Pill (500pt): filter chips, kudos pill
- Circle (50%): avatars, the rest-timer ring

## 6. Depth & Elevation

Hevy is mostly flat — depth is conveyed by the surface-tier ladder (`#0E1116` → `#161B22` → `#1F2630` → `#283040`) and hairline borders, not by drop shadows. Shadows appear only on truly floating UI.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | Canvas `#0E1116`, no shadow | App background |
| Card (Level 1) | `#161B22` + 1pt `#262C36` border | Exercise cards, feed cards, list rows |
| Raised (Level 2) | `#1F2630` / `#283040`, no shadow | Set cells, chips, segmented control |
| Floating (Level 3) | `rgba(0,0,0,0.5) 0 8px 24px` | Sheets, dropdowns, the add-exercise picker |
| Sheet (Level 4) | `rgba(0,0,0,0.6) 0 -10px 32px` + 20pt top radius | Bottom sheets (set-type, rest settings) |
| Overlay | `rgba(0,0,0,0.55)` scrim | Behind modals and the post-workout summary |

**Shadow Philosophy**: on a near-black canvas, drop shadows barely register, so Hevy uses the surface ladder + 1pt borders as the primary depth cue and reserves real shadows for genuinely floating layers. Floating panels also get a subtle border so they read as detached even when the shadow is faint.

### Motion
- **Set check**: tapping ✓ fills the box (120ms ease-out), washes the row green (150ms), triggers a soft success haptic, and auto-starts the rest timer
- **Rest-timer ring**: continuous linear sweep over the rest duration; ±15s buttons nudge with a 200ms spring; soft + sound at 0
- **PR badge**: scales 0.9→1.0 with a 220ms spring + brief gold opacity pulse + a celebratory soft-impact haptic
- **Add set**: new row slides down (height 0→auto, 200ms ease-out) and the cell focuses
- **Finish workout**: primary button → post-workout summary pushes up as a full-screen sheet (320ms ease-out)
- **Tab switch**: instant; no cross-fade (a gym tool should feel immediate)
- **Pull-to-refresh (feed)**: standard iOS spinner; cards fade-in on load
- **Live Activity**: rest countdown mirrors to Dynamic Island / Lock Screen with the ring + remaining time
- **Haptics**: soft impact on set check, medium on PR, light tick on rest ± and timer end

## 7. Do's and Don'ts

### Do
- Default to dark — launch into the near-black training canvas (`#0E1116`); it's engineered for gyms
- Use Hevy Blue (`#1E6FFF`) as the *only* action color — Finish, tabs, exercise titles, CTAs
- Render every number with tabular figures so the set table and timers never reflow
- Show the `Previous` column on every set — knowing the target is the core value proposition
- Wash completed set rows green (`#2FBF7129`) with a filled `#2FBF71` checkmark
- Fire a gold (`#F5B83D`) PR badge the moment a record breaks — it's the app's dopamine hit
- Auto-start the rest timer on set completion and surface it as a glanceable pill
- Keep the set table dense — a full exercise should fit on screen mid-set
- Make the exercise name both bold *and* blue (it's heading + edit affordance)
- Use the surface ladder + 1pt borders for depth, not heavy shadows
- Mirror the rest timer to Dynamic Island / Live Activity when backgrounded

### Don't
- Don't add a second brand accent — Hevy Blue is the entire action vocabulary
- Don't use proportional figures anywhere numeric — the table will jitter as digits change
- Don't hide the `Previous` column to "clean up" — it's the feature
- Don't decorate with photography, gradients-for-mood, or illustration — numbers are the content
- Don't use pure black (`#000000`) for the canvas — `#0E1116` (blue-cool near-black) is correct
- Don't make PRs loud with confetti — a gold badge + pulse + haptic is the whole celebration
- Don't add tab-bar tint pills — active is icon color + soft fill only
- Don't space the set table loosely — density is intentional for mid-workout speed
- Don't animate aggressively — a gym tool must feel instant; motion is 120–320ms, functional
- Don't tint charts with the brand blue's hue for non-data chrome — keep gridlines neutral grey
- Don't block logging behind confirmation dialogs — only destructive actions confirm

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Set table tightens to 6pt cell padding; metric row wraps to 2+1 |
| iPhone 13 / 14 / 15 | 390pt | Standard layout |
| iPhone 15 / 16 Pro | 393pt | Dynamic Island hosts the rest-timer Live Activity |
| iPhone 15 / 16 Pro Max | 430pt | Larger set cells; metric values at 18pt |
| iPad (portrait) | 768pt | Content max-width 720pt centered; feed + summary can go 2-column |
| iPad (landscape) | 1024pt+ | Optional split: exercise list rail + active set table |

### Dynamic Type
- Scales: screen titles, routine titles, exercise names, body, set-cell values
- Fixed: column labels, tab labels, PR/streak badges, set-index chip (table layout is digit-width-sensitive)
- Numeric metrics scale but always retain tabular figures

### Orientation
- iPhone: portrait-locked for the workout log (one-handed mid-set use)
- iPad: supports landscape with the split set-table layout
- Charts rotate and re-fit

### Touch Targets
- Set-cell input: 36pt tall, full-cell tap
- Checkmark: 26pt glyph, 44pt hit
- Tab icons: 22pt glyph, 49pt hit
- `+ Add Set` row: 34pt tall, full-width tap
- Rest ± controls: 32pt hit minimum

### Safe Area Handling
- Top: large title / workout header respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; the rest pill floats above the keyboard when a cell is focused
- Keyboard: numeric keypad pushes the active exercise card into view; rest pill stays pinned above it
- Sides: 16–18pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0E1116`
- Card surface: `#161B22`
- Input surface: `#1F2630`
- Surface 3 / chips: `#283040`
- Divider: `#262C36`
- Hevy Blue (action): `#1E6FFF`
- Blue pressed: `#1857CC`
- Blue soft fill: `#14233F`
- Text primary: `#F2F4F8`
- Text secondary: `#9BA3B0`
- Text tertiary: `#5E6675`
- Set done green: `#2FBF71` (row wash `#2FBF7129`)
- PR gold: `#F5B83D` (badge bg `#F5B83D2E`)
- Error red: `#F1545B`

### Example Component Prompts
- "Build the Hevy active-workout header in SwiftUI: leading routine name in Inter 26pt weight 700 `#F2F4F8`; trailing `Finish` button — `#1E6FFF` bg, white Inter 13pt/700, 10pt radius, 7×16pt padding. Below: a 3-up metric row (Duration / Volume / Sets), each a VStack of an 11pt `#9BA3B0` label over a 17pt/700 value with `.monospacedDigit()`; the Duration value tints `#1E6FFF`. Background `#0E1116`."

- "Create a Hevy set row: an HStack — 28pt rounded-square index chip (`#1F2630` bg, `#9BA3B0` 13pt/700), a `Previous` label `#5E6675` 13pt formatted `60 × 10`, two 36pt editable cells (`#1F2630` bg, 8pt radius, `#F2F4F8` 15pt/600, monospaced digits, 1.5pt `#1E6FFF` border on focus), and a 26pt checkmark (empty: 1.5pt `#262C36` border; checked: filled `#2FBF71` + white tick). On check, wash the whole row `#2FBF71` at 16% alpha and start the rest timer."

- "Render the Hevy rest-timer pill: 44pt tall, full-width minus 18pt, 12pt radius, `#14233F` bg, 1pt `rgba(30,111,255,0.35)` border. Leading 22pt circular progress ring (3pt stroke, track `rgba(30,111,255,0.3)`, fill `#1E6FFF`) sweeping over the rest duration. Center text `Rest · 1:24` in `#1E6FFF` 14pt/600 monospaced. Trailing `Skip` in `#9BA3B0` 13pt/600. Soft haptic + sound at 0; mirror to a Live Activity."

- "Build the Hevy PR badge: an inline chip with `#F5B83D` at 18% alpha background, `#F5B83D` text, Inter 10pt weight 800 uppercase, content `★ NEW PR · 1RM 87.5 kg`. On appear, spring-scale 0.9→1.0 over 220ms with a brief gold opacity pulse and a medium-impact haptic."

- "Create a Hevy exercise card: `#161B22` bg, 1pt `#262C36` border, 16pt radius, 14pt padding. Header: 36pt thumbnail (`#283040`, blue glyph), exercise name in `#1E6FFF` Inter 18pt/700, trailing `⋯` `#5E6675`. Body: the set table (columns SET / PREVIOUS / KG / REPS / ✓, 11pt uppercase `#9BA3B0` headers). Footer: a full-width `+ Add Set` tonal row, 34pt, 9pt radius, `#1F2630` bg, `#9BA3B0` 13pt/700."

- "Render the Hevy bottom tab bar: 68pt incl. home indicator, `rgba(14,17,22,0.94)` + blur, 0.5pt `#262C36` top border. Tabs: Home, Profile, Workout (center), History, Exercises. 22pt icons; active `#1E6FFF` with a soft `rgba(30,111,255,0.16)` fill; inactive `#5E6675`; labels Inter 10pt/600 always shown. No tint pill."

### Iteration Guide
1. Canvas is near-black blue-cool `#0E1116` — NOT pure black, NOT a warm dark
2. Hevy Blue `#1E6FFF` is the ONLY action color — Finish, tabs, exercise titles, every CTA
3. Every number uses tabular figures — the set table must never reflow as digits change
4. The `Previous` column is mandatory on every set — it is the product's core value
5. Completed set rows wash green `#2FBF71` @ 16% with a filled checkmark; rest timer auto-starts
6. PR badge is gold `#F5B83D` and fires on record-break — a pulse + haptic, never confetti
7. Surface ladder (`#0E1116`→`#161B22`→`#1F2630`→`#283040`) + 1pt borders carry depth, not shadows
8. The set table is intentionally dense so a full exercise fits on screen mid-set
9. Exercise names are bold AND blue (heading + tap-to-edit affordance)
10. Motion is functional and fast (120–320ms) — a gym tool must feel instant
