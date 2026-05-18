# Design System Inspiration of Strong (iOS)

## 1. Visual Theme & Atmosphere

Strong's iOS app is one of the most disciplined, content-first interfaces in fitness software. Open it and there is no onboarding fanfare, no hero imagery, no motivational gradient — just a flat, near-black canvas (`#1A1A1A`) and the **set-log table**. Strong's whole thesis is that a weightlifting app should feel like the world's best paper logbook: open to today's workout, see the exercises stacked vertically, each with a tight grid of sets (Set / Previous / kg / Reps / ✓), and log a set with two taps. The signature screen of Strong is **logging a workout**: a workout title and a row of stat pills (Time / Volume / Sets) at top, a slim rest-timer progress bar that auto-runs between sets, exercise cards with editable number cells, and rows that fill green the instant you check them off.

The design philosophy is "minimal chrome, maximal log." Strong predates and outlasts trend cycles by refusing decoration: there is no photography, no illustration, no skeuomorphic plate-loading animation. The only "interface" a lifter touches mid-set is the exercise name (in Strong Blue, the tap target to swap/replace), the `Previous` column (so you always know what you did last time), two number cells, and a checkmark. Everything around it — history calendar, body-measurement charts, the exercise library with muscle diagrams, plate calculator — is rendered in the same restrained dark palette so nothing competes with the numbers.

The color system is monochrome plus a single blue. Canvas and three surface tiers are flat neutral greys (`#1A1A1A` → `#242424` → `#2E2E2E` → `#383838`) — true grey, not blue-tinted, which gives Strong its characteristic neutral, "instrument panel" feel. **Strong Blue (`#2F80ED`)** is the lone accent: Finish button, active tab, exercise titles, links, the rest-timer fill, segmented selection. There is no secondary brand hue. Two semantic colors punctuate the log — a **set-done green (`#27AE60`)** that fills a completed row at ~18% opacity, and a **PR amber (`#F2C94C`)** flag for personal records. Destructive actions use a clear red (`#EB5757`). The restraint is functional: a set log is glanced at for two seconds between heavy sets, so the palette must keep the *numbers* the loudest thing on the screen.

Typography is **Inter** throughout — chosen for its excellent legibility at small sizes and, critically, its tabular-figures feature. Strong applies tabular figures to every weight, rep, set number, volume total, 1RM estimate, and timer so the set table's columns never shift width as a `9` becomes a `10` mid-workout. Hierarchy is utilitarian: screen titles at 32pt/800, the workout title at 26pt/800, section headers at 22pt/700, exercise names at 18pt/700, metric read-outs at 17pt/700 tabular, body at 16pt/400, set-table cells at 15pt/700 tabular, the `Previous` caption at 13pt, and column labels at 11pt uppercase. The type system never expresses brand personality — it expresses *the data*, legibly, at arm's length, under load.

**Key Characteristics:**
- Flat near-black canvas (`#1A1A1A`) dark-first — true neutral grey, "instrument panel" feel
- The **set-log table** is the interface: stacked exercise cards, each a dense Set/Previous/kg/Reps/✓ grid
- All numerics in **tabular figures** so the table never reflows as digits change
- **Strong Blue (`#2F80ED`)** is the single accent: Finish, active tab, exercise titles, rest-timer fill, links
- **`Previous` column** on every set — last session's weight×reps inline; the core value proposition
- **Slim rest-timer bar** — a thin progress bar that auto-runs between sets with ±15s controls
- **PR flag** — an amber (`#F2C94C`) "▲ PR" chip when a logged set sets an estimated-1RM/rep record
- **Set-done state** — completed rows fill green (`#27AE60` @ 18%) with a filled checkmark
- **Warm-up / drop / failure set tags** — `W` / `D` / `F` set-index markers (warm-up in amber)
- **Stat pills** — Time / Volume / Sets summary row at the top of the active workout
- Inter everywhere with aggressive tabular-figures usage; minimal chrome, no decoration
- 5-tab bottom bar (Profile / History / Workout / Exercises / Settings); Workout is the heart

## 2. Color Palette & Roles

### Primary (Interactive)
- **Strong Blue** (`#2F80ED`): The single accent — Finish button, active tab, exercise titles, links, rest-timer fill, segmented selection, PR-link.
- **Strong Blue Pressed** (`#2566C0`): Pressed state on primary buttons.
- **Strong Blue Soft** (`#16263D`): ~14–22% blue tint — rest-timer bar background/track, tonal button fill, selected-row glow.

### Canvas & Surfaces (Dark — default)
- **Pure Canvas** (`#1A1A1A`): Primary app background — flat near-black neutral grey, the launch surface.
- **Card Surface** (`#242424`): Exercise cards, list rows, sheets.
- **Input Surface** (`#2E2E2E`): Editable set cells (kg / reps), stat pills, search field, segmented track.
- **Surface 3** (`#383838`): Exercise thumbnails, raised chips, hovered/pressed rows.
- **Divider** (`#333333`): Hairline dividers between sections and table headers.

### Canvas & Surfaces (Light — optional, opt-in)
- **Light Canvas** (`#F2F2F2`): Light-mode background.
- **Light Card** (`#FFFFFF`): Cards on light.
- **Light Input** (`#EBEBEB`): Editable cells on light.
- **Light Divider** (`#DDDDDD`): Hairlines on light.

### Text
- **Text Primary** (`#F5F5F5`): Headings, weights, reps — the numbers that matter — near-white.
- **Text Secondary** (`#9A9A9A`): Labels, captions, column headers, stat-pill labels.
- **Text Tertiary** (`#636363`): Disabled, the `Previous` hint, the `⋯` glyph.
- **Text Primary (Light)** (`#1A1A1A`): Primary text on light mode.
- **Text Secondary (Light)** (`#6B6B6B`): Secondary on light mode.

### Semantic
- **Set Done Green** (`#27AE60`): Completed-set checkmark fill + row fill at 18% (`#27AE602E`).
- **PR Amber** (`#F2C94C`): "▲ PR" flag background-tint + text; also the warm-up set marker accent.
- **Error / Destructive Red** (`#EB5757`): Cancel workout, delete set, validation errors.
- **Warning Orange** (`#F2994A`): "Unsaved", deload prompts.
- **Warm-up Marker** (`#F2994A`): `W` set-index tag color (drop = neutral `D`, failure = `F`).

### Color Roles Table

| Role | Dark Mode | Light Mode |
|------|-----------|-----------|
| Canvas | `#1A1A1A` | `#F2F2F2` |
| Card surface | `#242424` | `#FFFFFF` |
| Input surface | `#2E2E2E` | `#EBEBEB` |
| Surface 3 / chips | `#383838` | `#E2E2E2` |
| Divider | `#333333` | `#DDDDDD` |
| Accent (Strong Blue) | `#2F80ED` | `#2F80ED` |
| Accent pressed | `#2566C0` | `#2566C0` |
| Blue soft fill | `#16263D` | `#E3EEFD` |
| Text primary | `#F5F5F5` | `#1A1A1A` |
| Text secondary | `#9A9A9A` | `#6B6B6B` |
| Set done | `#27AE60` | `#1E8E4E` |
| PR amber | `#F2C94C` | `#C99A12` |
| Error | `#EB5757` | `#D63D3D` |

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (by Rasmus Andersson, SIL OFL) — humanist sans with superb small-size legibility and a first-class `tnum` (tabular figures) OpenType feature, which Strong applies to every number on screen.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numeric rule**: every weight, rep count, set index, volume total, 1RM estimate, percentage, and timer renders with **tabular figures** (`font-variant-numeric: tabular-nums`) so the set table and timers never reflow.
- **No alternate families**: Strong ships one face — brand consistency over user choice.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.6pt | "History", "Exercises" — large nav title |
| Workout Title | Inter | 26pt | 800 | 1.2 | -0.4pt | "Workout" / routine name at top of log |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.3pt | "Personal Records", chart sections |
| Exercise Name | Inter | 18pt | 700 | 1.3 | -0.2pt | Exercise title — always in Strong Blue |
| Metric / Timer | Inter | 17pt | 700 | 1.2 | 0pt | Time / volume / 1RM — **tabular-nums** |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, settings, exercise notes |
| Set Cell | Inter | 15pt | 700 | 1.3 | 0pt | kg / reps values — **tabular-nums** |
| Card / Row Title | Inter | 15pt | 600 | 1.3 | -0.1pt | History row title, list row title |
| Caption | Inter | 13pt | 500 | 1.4 | 0pt | `Previous` hint, timestamps, exercise note |
| Column Label | Inter | 11pt | 700 | 1.1 | 0.4pt | SET / PREVIOUS / KG / REPS — uppercase |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary button label |
| Flag | Inter | 10pt | 800 | 1.0 | 0.3pt | PR flag chip — uppercase |

### Principles
- **Numbers are the loudest element**: weights, reps, timers use 700 weight + tabular figures; everything supporting them is lighter and greyer.
- **Tabular figures everywhere numeric**: the set table must not reflow when a digit changes — `tnum` is mandatory on every figure.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: regular body, medium captions, semibold row titles, bold exercise names + cells + metrics, extrabold screen/workout titles.
- **Strong Blue carries hierarchy**: an exercise name is bold *and* blue because it's both the heading and the tap-to-replace affordance.
- **Dynamic Type with floors**: titles, body, and cells scale; column labels, tab labels, and flags stay fixed (table layout is digit-width-sensitive).
- **Uppercase only for micro-labels**: column headers and the PR flag; never body or titles.

## 4. Component Stylings

### Buttons

**Primary Button (Finish / Save Routine)**
- Shape: rounded rectangle, 8pt corner radius
- Background: `#2F80ED` (Strong Blue) / text `#FFFFFF`
- Padding: 14pt vertical, 28pt horizontal; full-width variant spans content inset
- Font: Inter 16pt weight 700
- Pressed: background `#2566C0` + scale 0.98
- Disabled: `#2E2E2E` background, `#636363` text

**Secondary / Outline Button (Add Exercise)**
- Background: transparent
- Border: 1.5pt `#2F80ED`
- Text: `#2F80ED`, Inter 15pt weight 600
- Corner radius: 8pt; padding 11pt / 22pt
- Pressed: background `#16263D`

**Tonal Button (+ Add Set / inline actions)**
- Background: `#16263D` (Blue Soft) — or `#2E2E2E` for neutral inline actions
- Text: `#2F80ED`, Inter 14pt weight 700
- Corner radius: 8pt; padding 11pt / 20pt
- Used as the full-width "+ Add Set" row inside an exercise card

**Destructive Text Button (Cancel Workout / Remove Exercise)**
- Background: transparent
- Text: `#EB5757`, Inter 14pt weight 600
- No border; confirmation alert required before destructive commit

**Icon Button (exercise `⋯`, swipe actions)**
- 24pt glyph in 44pt hit area
- Default `#636363`; active `#F5F5F5`
- Swipe-left on a set row reveals delete (`#EB5757`); swipe-right marks complete

### Core Atoms

**Set Row** (the core atom)
- A table row inside an exercise card: `[set-index] [previous] [kg cell] [reps cell] [✓]`
- Set-index: plain `#9A9A9A` 14pt/700 numeral; warm-up shows `W` in `#F2994A`, drop `D`, failure `F`
- `Previous` cell: `#636363` 12pt, format `95 × 5` (last session's matching set; em-dash if none)
- Editable cells (kg / reps): `#2E2E2E` bg, 7pt radius, `#F5F5F5` 15pt/700, tabular figures; focused → 1.5pt `#2F80ED` inset border
- Checkmark: 24pt rounded-square; empty = 1.5pt `#333333` border; checked = filled `#27AE60` + white tick, and the row's cells fill `#27AE602E`
- Row height: ~38pt; 6pt vertical cell padding

**Exercise Card**
- Background `#242424`, no border (flat), 14pt corner radius, 14pt padding, 12pt gap between cards
- Header: 34pt exercise thumbnail (`#383838`, blue glyph), exercise name (`#2F80ED` 18pt/700), trailing `⋯`
- Optional exercise note line: `#9A9A9A` 12pt
- Body: the set table
- Footer: full-width "+ Add Set" tonal row (34pt, 8pt radius, `#2E2E2E`, `#2F80ED` 13pt/700)

**Rest-Timer Bar**
- 40pt tall, full-width minus 18pt inset, 8pt radius
- Background `#16263D`; an inner fill (`rgba(47,128,237,0.22)`) animates from full → empty over the rest duration (a thin progress bar, not a ring)
- Leading: 16pt clock glyph `#2F80ED`
- Center: `Rest Timer · 1:18` in `#2F80ED` 13pt/700 tabular
- Trailing: `−15  +15` adjusters in `#9A9A9A` 12pt/700
- Auto-runs on set completion; soft haptic + chime at 0; tap to expand/edit default

**Stat Pill (Time / Volume / Sets)**
- Background `#2E2E2E`, 7pt radius, 5×10pt padding
- Label `#9A9A9A` 12pt/600 + value in `#2F80ED` 12pt/700 tabular
- Row of three under the workout title; live Time value updates each second

**PR Flag**
- Inline chip: background `#F2C94C` at 18% (`#F2C94C2E`), text `#F2C94C`, Inter 10pt/800 uppercase
- Content: `▲ PR · Est. 1RM 117 kg` (also fires for best-reps and best-weight records)
- Appears under the set table the moment a logged set beats a stored record; brief amber pulse + soft haptic

**Metric (workout title row)**
- The three stat pills double as the workout's live metric read-out
- Post-workout summary repeats them larger with the PR list

**History Row**
- `#242424` card, 14pt radius; workout name (`#F5F5F5` 15pt/600), date + duration (`#9A9A9A` 13pt)
- Mini stat strip: total volume, set count, PR count; tap → full workout detail

### Navigation

**Bottom Tab Bar**
- Height: 66pt incl. home indicator; background `rgba(26,26,26,0.94)` + blur, 0.5pt `#333333` top border
- 5 tabs: Profile, History, **Workout** (center), Exercises, Settings
- Icon 22pt; active color `#2F80ED` with a soft `rgba(47,128,237,0.18)` icon fill; inactive `#636363`
- Labels: Inter 10pt/600, always shown
- No tint pill — active state is icon color + soft fill only

**Top Bar (in-workout)**
- Workout title (26pt/800) leading; trailing `Finish` button (Strong Blue, 8pt radius, 13pt/700)
- Below: the three stat pills, then the rest-timer bar
- On scroll, a compact title + Finish stay pinned; a hairline divider separates header from log

**Large-Title Screens (History / Exercises / Profile)**
- iOS large-title pattern: 32pt/800 title that collapses to a 17pt centered nav title on scroll
- Trailing: contextual icon (calendar, add, search/filter)

### Input Fields

**Set Cell Input (kg / reps)**
- ~34pt tall, `#2E2E2E` bg, 7pt radius, centered `#F5F5F5` 15pt/700 tabular
- Numeric keypad; focus → 1.5pt `#2F80ED` inset border, value selected for fast overwrite
- Empty placeholder shows the `Previous` value greyed at `#636363`

**Search Field (Exercises / Add Exercise)**
- 38pt tall, `#2E2E2E` bg, 8pt radius, leading 16pt magnifier `#9A9A9A`
- Placeholder "Search exercises" `#636363` 15pt
- Focus: 1.5pt `#2F80ED` border

**Segmented Control (chart range: W / M / 6M / Y)**
- Track `#2E2E2E`, 8pt radius; selected segment `#383838` with `#F5F5F5` text; unselected `#9A9A9A`
- 32pt tall, equal segments

### Distinctive Components

**The Set-Log Table**
- The signature Strong component: a per-exercise grid with columns SET / PREVIOUS / KG / REPS / ✓
- Tabular figures lock column widths; the `Previous` column is the killer feature
- Checking a set fills its cells green and auto-runs the rest timer

**Slim Rest-Timer Bar**
- A thin horizontal progress bar (not a ring) that depletes over the rest duration; ±15s; chime + soft haptic at 0
- Persists as a Live Activity / Dynamic Island countdown when backgrounded

**Amber PR Flag**
- Fires for estimated-1RM, best weight, or best reps; amber pulse + haptic; aggregated on the profile's "Records"

**Post-Workout Summary**
- Full-screen card: duration, total volume, set count, PR list, muscle-split, share-image button
- The single celebratory moment — restrained, just numbers and the PR amber

**Plate Calculator**
- A utility sheet: given a target weight + bar weight, renders the per-side plate stack as labeled discs
- Pure utility styling — neutral discs, blue accents, tabular weight labels

**Exercise Library with Muscle Map**
- Searchable list; each exercise detail shows a primary/secondary muscle diagram (calm desaturated fills, never the brand blue)

**1RM / Volume Trend Chart**
- Line chart on `#1A1A1A`; line in `#2F80ED`, gridlines `#333333`, axis labels `#636363`; PR points marked in `#F2C94C`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 14, 18, 24, 32, 40, 56
- Content inset: 14–18pt horizontal
- Card padding: 14pt; card-to-card gap: 12pt
- Set-row vertical padding: 6pt; row height ~38pt
- Section gap: 24pt

### Grid & Container
- iPhone: single column, 14–18pt insets, cards full-width
- Set table: 5 columns — index (36pt) / previous (flex) / kg (flex) / reps (flex) / check (24pt)
- iPad: content max-width ~700pt centered; optional 2-column for history + summary
- Charts: full-bleed minus inset, fixed aspect 16:9

### Whitespace Philosophy
- **Density where it counts**: the set table is intentionally tight so a full exercise fits on screen mid-set
- **Quiet separation**: 12pt gaps + flat `#242424` cards (no borders) separate exercises without visual noise
- **Numbers get air**: stat pills and the timer bar get clear vertical room so they read at a glance
- **Zero decorative whitespace**: every pixel serves logging or reading; Strong is an instrument, not a magazine

### Border Radius Scale
- Square (0pt): chart plot area, full-bleed media
- Subtle (4pt): rarely used micro-elements
- Small (7pt): set cells, stat pills
- Standard (8pt): buttons, segmented control, rest bar, search field, Finish
- Card (14pt): exercise cards, history rows, sheets
- Large (20pt): bottom sheets, modals, plate calculator
- Pill (500pt): filter chips
- Circle (50%): avatars, plate discs

## 6. Depth & Elevation

Strong is intentionally flat — depth comes from the surface-tier ladder (`#1A1A1A` → `#242424` → `#2E2E2E` → `#383838`), not drop shadows. Exercise cards have *no* border (unlike many dark UIs); the subtle lift from canvas to `#242424` is enough. Shadows appear only on truly floating UI.

| Level | Treatment | Use |
|-------|-----------|-----|
| Base (Level 0) | Canvas `#1A1A1A`, no shadow | App background |
| Card (Level 1) | `#242424`, no border, no shadow | Exercise cards, history rows |
| Raised (Level 2) | `#2E2E2E` / `#383838`, no shadow | Set cells, stat pills, segmented control |
| Floating (Level 3) | `rgba(0,0,0,0.55) 0 8px 24px` | Sheets, dropdowns, the add-exercise picker |
| Sheet (Level 4) | `rgba(0,0,0,0.6) 0 -10px 32px` + 20pt top radius | Bottom sheets (plate calc, set-type) |
| Overlay | `rgba(0,0,0,0.6)` scrim | Behind modals and the post-workout summary |

**Shadow Philosophy**: on a flat near-black canvas, drop shadows are nearly invisible and visually noisy, so Strong uses *only* the surface ladder to convey depth on content. Real shadows are reserved for genuinely floating layers; those panels also get a faint border so they read as detached even when the shadow is imperceptible.

### Motion
- **Set check**: tapping ✓ fills the box (120ms ease-out), fills the row's cells green (140ms), soft success haptic, auto-runs the rest timer
- **Rest-timer bar**: inner fill depletes linearly over the rest duration; ±15s nudges with a 180ms ease; soft + chime at 0
- **PR flag**: fades + scales 0.92→1.0 over 200ms with a brief amber opacity pulse + a soft-impact haptic
- **Add set**: new row slides down (height 0→auto, 200ms ease-out) and the kg cell focuses
- **Finish workout**: primary button → post-workout summary pushes up as a full-screen sheet (300ms ease-out)
- **Tab switch**: instant; no cross-fade (the app should feel immediate)
- **Pull-to-refresh (history)**: standard iOS spinner; rows fade-in on load
- **Live Activity**: rest countdown mirrors to Dynamic Island / Lock Screen as a slim progress bar + remaining time
- **Haptics**: soft impact on set check, light tick on rest ± and timer end, soft impact on PR

## 7. Do's and Don'ts

### Do
- Default to dark — launch into the flat near-black canvas (`#1A1A1A`); it's the brand
- Use Strong Blue (`#2F80ED`) as the *only* accent — Finish, tabs, exercise titles, rest-timer fill
- Render every number with tabular figures so the set table and timers never reflow
- Show the `Previous` column on every set — knowing last session's numbers is the core value
- Fill completed set rows green (`#27AE602E`) with a filled `#27AE60` checkmark
- Auto-run the slim rest-timer bar on set completion with ±15s adjusters
- Show an amber (`#F2C94C`) PR flag when a set sets a record — a pulse + haptic, restrained
- Keep exercise cards borderless and flat — the `#242424` lift from canvas is enough
- Make the exercise name both bold *and* blue (heading + tap-to-replace affordance)
- Keep the set table dense — a full exercise should fit on screen mid-set
- Use true neutral greys (`#1A1A1A`/`#242424`/`#2E2E2E`) — not blue-tinted darks

### Don't
- Don't add a second brand accent — Strong Blue is the entire accent vocabulary
- Don't use proportional figures anywhere numeric — the table will jitter as digits change
- Don't hide the `Previous` column to "simplify" — it's the feature
- Don't decorate with photography, gradients, plate-loading skeuomorphism, or illustration
- Don't tint the canvas blue — Strong's neutral grey is deliberate and characteristic
- Don't make PRs loud with confetti — an amber flag + pulse + haptic is the whole celebration
- Don't add tab-bar tint pills — active is icon color + soft fill only
- Don't put borders on exercise cards — flat surface lift only (this is a signature)
- Don't animate aggressively — the app must feel instant; motion is 120–300ms, functional
- Don't tint charts/muscle-maps with the brand blue's hue for non-data chrome — keep them neutral
- Don't block logging behind dialogs — only destructive actions confirm

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Set table tightens to 5pt cell padding; stat pills wrap 2+1 |
| iPhone 13 / 14 / 15 | 390pt | Standard layout |
| iPhone 15 / 16 Pro | 393pt | Dynamic Island hosts the rest-timer Live Activity |
| iPhone 15 / 16 Pro Max | 430pt | Larger set cells; metric values at 18pt |
| iPad (portrait) | 768pt | Content max-width 700pt centered; history + summary 2-column |
| iPad (landscape) | 1024pt+ | Optional split: exercise list rail + active set table |

### Dynamic Type
- Scales: screen titles, workout title, section headers, exercise names, body, set-cell values
- Fixed: column labels, tab labels, PR flag, set-index marker (table layout is digit-width-sensitive)
- Numeric metrics scale but always retain tabular figures

### Orientation
- iPhone: portrait-locked for the workout log (one-handed mid-set use)
- iPad: supports landscape with the split set-table layout
- Charts rotate and re-fit

### Touch Targets
- Set-cell input: ~34pt tall, full-cell tap
- Checkmark: 24pt glyph, 44pt hit
- Tab icons: 22pt glyph, 49pt hit
- `+ Add Set` row: 34pt tall, full-width tap
- Rest ±15s adjusters: 32pt hit minimum

### Safe Area Handling
- Top: large title / workout header respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; the rest bar floats above the keyboard when a cell is focused
- Keyboard: numeric keypad pushes the active exercise card into view; rest bar stays pinned above it
- Sides: 14–18pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#1A1A1A`
- Card surface: `#242424`
- Input surface: `#2E2E2E`
- Surface 3 / chips: `#383838`
- Divider: `#333333`
- Strong Blue (accent): `#2F80ED`
- Blue pressed: `#2566C0`
- Blue soft fill: `#16263D`
- Text primary: `#F5F5F5`
- Text secondary: `#9A9A9A`
- Text tertiary: `#636363`
- Set done green: `#27AE60` (row fill `#27AE602E`)
- PR amber: `#F2C94C` (flag bg `#F2C94C2E`)
- Error red: `#EB5757`

### Example Component Prompts
- "Build the Strong active-workout header in SwiftUI: leading workout title in Inter 26pt weight 800 `#F5F5F5`; trailing `Finish` button — `#2F80ED` bg, white Inter 13pt/700, 8pt radius, 8×16pt padding. Below: a horizontal row of three stat pills (Time / Volume / Sets), each `#2E2E2E` bg, 7pt radius, 5×10pt padding, a `#9A9A9A` 12pt/600 label + `#2F80ED` 12pt/700 monospaced value. Background `#1A1A1A`, hairline `#333333` divider below."

- "Create a Strong set row: an HStack — set-index numeral `#9A9A9A` 14pt/700 (warm-up shows `W` in `#F2994A`), a `Previous` label `#636363` 12pt formatted `95 × 5`, two ~34pt editable cells (`#2E2E2E` bg, 7pt radius, `#F5F5F5` 15pt/700, monospaced digits, 1.5pt `#2F80ED` inset border on focus), and a 24pt checkmark (empty: 1.5pt `#333333` border; checked: filled `#27AE60` + white tick). On check, fill the row's cells `#27AE60` at 18% alpha and auto-run the rest timer."

- "Render the Strong rest-timer bar: 40pt tall, full-width minus 18pt, 8pt radius, `#16263D` bg. An inner fill (`rgba(47,128,237,0.22)`) animates from full width to zero linearly over the rest duration. Leading 16pt clock glyph `#2F80ED`. Center `Rest Timer · 1:18` in `#2F80ED` 13pt/700 monospaced. Trailing `−15  +15` adjusters in `#9A9A9A` 12pt/700. Soft haptic + chime at 0; mirror to a Live Activity."

- "Build the Strong PR flag: an inline chip with `#F2C94C` at 18% alpha background, `#F2C94C` text, Inter 10pt weight 800 uppercase, content `▲ PR · Est. 1RM 117 kg`. On appear, fade + spring-scale 0.92→1.0 over 200ms with a brief amber opacity pulse and a soft-impact haptic."

- "Create a Strong exercise card: `#242424` bg, NO border (flat), 14pt radius, 14pt padding. Header: 34pt thumbnail (`#383838`, blue glyph), exercise name in `#2F80ED` Inter 18pt/700, trailing `⋯` `#636363`. Optional note line `#9A9A9A` 12pt. Body: the set table (columns SET / PREVIOUS / KG / REPS / ✓, 11pt uppercase `#9A9A9A` headers). Footer: a full-width `+ Add Set` tonal row, 34pt, 8pt radius, `#2E2E2E` bg, `#2F80ED` 13pt/700."

- "Render the Strong bottom tab bar: 66pt incl. home indicator, `rgba(26,26,26,0.94)` + blur, 0.5pt `#333333` top border. Tabs: Profile, History, Workout (center), Exercises, Settings. 22pt icons; active `#2F80ED` with a soft `rgba(47,128,237,0.18)` fill; inactive `#636363`; labels Inter 10pt/600 always shown. No tint pill."

### Iteration Guide
1. Canvas is flat near-black NEUTRAL grey `#1A1A1A` — NOT blue-tinted, NOT pure black
2. Strong Blue `#2F80ED` is the ONLY accent — Finish, tabs, exercise titles, rest-timer fill, links
3. Every number uses tabular figures — the set table must never reflow as digits change
4. The `Previous` column is mandatory on every set — it is the product's core value
5. Logged set rows fill green `#27AE60` @ 18% with a filled checkmark; rest timer auto-runs
6. PR flag is amber `#F2C94C` and fires on record-break — a pulse + haptic, never confetti
7. Exercise cards are FLAT and BORDERLESS — the `#242424` lift from canvas is the only depth
8. The set table is intentionally dense so a full exercise fits on screen mid-set
9. Exercise names are bold AND blue (heading + tap-to-replace affordance)
10. Motion is functional and fast (120–300ms) — the app must feel instant
