# Design System Inspiration of Runna (iOS)

## 1. Visual Theme & Atmosphere

Runna is a structured-training app for runners, and its design feels like a coach's plan come to life: confident, athletic, and energetic, but disciplined enough that you can read a whole training week at a glance. The app is **dark-first** — runners train at dawn and dusk and check the screen mid-run on a wrist or armband, so a low-glare dark canvas is the default and primary experience. That canvas is a deep blue-black `#0E0E16`, deliberately tinted toward the brand Indigo rather than neutral grey, so the whole UI feels cohesive with the brand even in the darkest surfaces. Cards lift to `#181826`.

Two signature elements define every Runna screen. First, the **training-plan calendar** — a horizontal week strip of seven day cells, each color-coded by run type and state: a completed run glows green, today is a solid Indigo cell with a Lime dot, a rest day is muted, and upcoming sessions carry their run-type color (Tempo amber, Intervals coral, Long-run indigo). The strip turns an 8-week plan into something you can scan in a second — "what did I do, what's today, what's coming". Second, the **guided run-session card** — a bold Indigo gradient card showing today's workout title ("6 × 800m @ 5K pace"), key stats (distance / duration / pace), and a Lime "Start guided run" button — paired with a **workout-structure breakdown**: a segmented bar (warm-up → main set → cooldown) plus per-step rows with pace targets.

The brand is a two-color system used with intent. **Runna Indigo** (`#4F46E5`) is the structural color — plan surfaces, the today cell, the long-run type, primary informational buttons, the app icon. **Runna Lime** (`#C2F94E`) is the energy/"go" color — it is reserved almost exclusively for action: the Start button, the today-marker dot, key CTAs, progress accents. The contrast between disciplined indigo and electric lime is the entire brand personality: structure plus drive. Run-type colors (Easy green `#34D399`, Tempo amber `#FBBF24`, Intervals coral `#FB7185`, Long indigo `#4F46E5`) form a fixed semantic palette so athletes learn to read effort by color.

Typography is **Sora** — a geometric sans with a slightly technical, athletic character (squared-off curves, even rhythm). It reads like performance gear: modern, confident, data-friendly. Numerals — pace, distance, splits — are heavy (700–800) because they're the substance of a training app; descriptive copy is regular (400). Lime always pairs with near-black text (`#1A1A1A`) for contrast; it is never used as a text color on dark.

**Key Characteristics:**
- Dark-first, Indigo-tinted blue-black canvas (`#0E0E16`) — low-glare for dawn/dusk training
- Training-plan week strip — seven color-coded day cells (done / today / rest / typed), the at-a-glance plan
- Guided run-session card — bold Indigo gradient, workout title + stats + Lime Start button
- Workout-structure breakdown — segmented warm-up → main set → cooldown bar + per-step pace rows
- Two-color brand system — Indigo `#4F46E5` (structure) + Lime `#C2F94E` (energy/"go")
- Lime is action-only — Start button, today dot, key CTAs; never a text color on dark
- Run-type color system — Easy `#34D399`, Tempo `#FBBF24`, Intervals `#FB7185`, Long `#4F46E5` (fixed)
- Pace targets everywhere — every step shows a `/km` (or `/mi`) target, heavy numerals
- Sora geometric sans — athletic, technical; heavy stats, regular prose
- Squared-soft cards (14–22pt radius) — disciplined, not bubbly
- Minimal chrome — 5-tab bottom bar: Plan / Run / Progress / Club / Profile

## 2. Color Palette & Roles

### Primary (Interactive)
- **Runna Lime** (`#C2F94E`): THE "go" color — Start-run button, today-marker dot, primary CTAs, progress accents. Always with near-black text.
- **Lime Pressed** (`#A3D93B`): Pressed state for Lime actions.
- **Runna Indigo** (`#4F46E5`): Structural color — plan surfaces, today cell, session-card gradient, secondary/informational buttons, long-run type.
- **Indigo Bright** (`#6366F1`): Indigo highlights, borders on the today cell, gradient highlight.
- **Indigo Pressed** (`#4338CA`): Pressed state for Indigo buttons.

### Canvas & Surfaces (Dark — default)
- **Canvas** (`#0E0E16`): Primary background — blue-black, tinted toward Indigo, NOT neutral.
- **Surface 1** (`#181826`): Card backgrounds (day cells, structure card, lists).
- **Surface 2** (`#222234`): Raised chips, week pill, segmented track, ghost buttons.
- **Divider** (`#2C2C42`): Hairline borders and separators.

### Canvas & Surfaces (Light — optional)
- **Light Canvas** (`#F6F6FB`): Pale indigo-white light background.
- **Light Surface** (`#FFFFFF`): Cards in light mode.
- **Light Divider** (`#E5E5F0`): Hairlines in light mode.

### Text
- **Text Primary Dark** (`#F3F3FB`): Headlines, pace numbers on dark.
- **Text Secondary Dark** (`#9C9CB8`): Metadata, descriptions on dark.
- **Text Tertiary Dark** (`#65657E`): Rest-day labels, axis, disabled.
- **Text On Lime** (`#1A1A1A`): Near-black — the ONLY text color used on Lime fills.
- **Text Primary Light** (`#15151F`): Primary text on light mode.

### Run-Type Color System (semantic — FIXED across themes)

| Type | Color | Meaning |
|------|-------|---------|
| Easy / Recovery | `#34D399` | Conversational pace; warm-up & cool-down also use this |
| Tempo / Threshold | `#FBBF24` | Sustained hard effort |
| Intervals / Speed | `#FB7185` | Hard repeats with recovery |
| Long Run | `#4F46E5` | Endurance — uses Runna Indigo |
| Completed | `#34D399` | A finished session (green check state) |
| Rest | `#65657E` | Rest / off day (muted) |

### Semantic
- **Success** (`#34D399`): Run completed, plan on track (same as Easy — intentional).
- **Error** (`#FB7185`): Sync failure, missed session (same hue as Intervals — intentional).
- **Warning** (`#FBBF24`): Behind-plan nudge (same as Tempo — intentional).
- **PR / Achievement** (`#C2F94E`): Personal-record badge, milestone (Lime = celebration).

## 3. Typography Rules

### Font Family
- **Primary**: `Sora` (by Jonathan Hill / Soulcave, Google Fonts, SIL OFL) — a geometric grotesque with a technical, athletic character; even rhythm and squared curves read like performance gear and keep dense pace data legible.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Sora 700–800 for all pace/distance/duration/split values. Use tabular figures on pace and the live run clock so digits don't jitter mid-run.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Sora | 32pt | 800 | 1.15 | -0.4pt | "Your plan", "Progress" |
| Plan Title | Sora | 26pt | 800 | 1.2 | -0.3pt | "Build week", "Race week" |
| Session Title | Sora | 21pt | 800 | 1.2 | -0.3pt | "6 × 800m @ 5K pace" |
| Section Title | Sora | 22pt | 700 | 1.25 | -0.2pt | "Workout structure" |
| Card Title | Sora | 18pt | 700 | 1.3 | -0.1pt | Step / list card titles |
| Body | Sora | 16pt | 400 | 1.5 | 0pt | Workout description copy |
| Step Item | Sora | 15pt | 600 | 1.4 | 0pt | "800m intervals · 90s recovery" |
| Meta | Sora | 14pt | 400 | 1.4 | 0pt | "3 runs · 11.2 km this week" |
| Stat Value | Sora | 18pt | 800 | 1.1 | -0.2pt | Distance/duration/pace on session card |
| Eyebrow | Sora | 11pt | 700 | 1.0 | 1.2pt | "5K PLAN · WEEK 4 OF 8" — uppercase, tracked, lime |
| Button | Sora | 16pt | 800 | 1.0 | 0.1pt | Primary CTA label |
| Pace / Tag | Sora | 12pt | 700 | 1.0 | 0.2pt | "4:35 /km", run-type chips |
| Day Cell Num | Sora | 14pt | 800 | 1.0 | 0pt | Date number in week strip |
| Tab Label | Sora | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Data-forward**: pace, distance, and splits are the substance — they're the heaviest type on screen (700–800), prose is regular (400).
- **Athletic geometry**: Sora's squared, technical curves are chosen because they read like performance equipment; do not swap for a soft rounded sans.
- **Eyebrows are lime + tracked**: the small uppercase plan label uses Lime with 1.2pt tracking — the one place Lime appears in type (on dark, only above an Indigo or dark surface where it has contrast).
- **Tabular numerals during runs**: the live clock, pace, and distance use tabular figures so the run screen doesn't shift.
- **Dynamic Type**: titles, plan/session titles, section, card title, body, step item scale; eyebrow, pace/tag, day-cell number, tab labels stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary "Go" Button (Start Guided Run)**
- Shape: Rounded rectangle, 14pt corner radius
- Background: `#C2F94E` (Runna Lime)
- Text: `#1A1A1A` (near-black), Sora 16pt weight 800; optional leading play glyph
- Padding: 13–15pt vertical, full-width on session card
- Pressed: `#A3D93B` + scale 0.98
- Disabled: `#222234` fill, `#65657E` text

**Indigo / Informational Button (View Plan)**
- Background: `#4F46E5` (Runna Indigo)
- Text: `#FFFFFF`, Sora 16pt weight 800
- Corner radius: 14pt
- Padding: 15pt vertical, 30pt horizontal
- Pressed: `#4338CA` + scale 0.98

**Outline Button (Reschedule)**
- Background: transparent
- Text: `#F3F3FB`, Sora 14pt weight 700
- Border: 1.5pt `#2C2C42`
- Corner radius: 14pt
- Padding: 13pt vertical, 24pt horizontal
- Pressed: background `#181826`

**Text Button (Swap workout)**
- Font: Sora 14pt weight 800, color `#C2F94E`
- No background, no underline
- Pressed: opacity 0.6

### Core Atoms

**Card**
- Background: `#181826`
- Border: 1pt `#2C2C42`
- Corner radius: 14pt (step/list cards), 22pt (session gradient card)
- Padding: 16–18pt
- Shadow: minimal on dark — rely on surface lift + border; session card has a faint indigo glow

**Day Cell (week strip)**
- Aspect ~0.62 (tall), 14pt corner radius
- Default: `#181826` bg, `#2C2C42` border, weekday label 10pt weight 700 tertiary, date 14pt weight 800, a 7pt run-type dot
- Completed: `rgba(52,211,153,0.12)` bg, `rgba(52,211,153,0.35)` border, date in `#34D399`
- Today: solid `#4F46E5` bg, `#6366F1` border, white label/date, Lime dot
- Rest: muted — date in `#65657E`, neutral dot

**Run-Type Chip**
- Rounded rect, 10pt corner radius, Sora 13pt weight 800
- Fill = run-type color; text `#1A1A1A` on Easy/Tempo/Intervals, white on Long (Indigo)
- Padding: 8pt vertical, 14pt horizontal

**Workout-Structure Segmented Bar**
- Horizontal bar, ~30–34pt tall, 8pt corner radius, 3pt gaps between segments
- Each segment colored by phase (warm-up/cool = Easy green, main set = its run-type color)
- Segment widths proportional to phase duration; tiny uppercase label inside if it fits (9pt weight 800)

**Step Row**
- Leading 32pt rounded-square (9pt radius) phase icon, colored by phase, 2-letter code (WU / 6× / CD) in near-black weight 800
- Title (15pt weight 700) + description (11pt weight 500 secondary)
- Trailing pace value (13pt weight 800) with a small "/km" sublabel (10pt weight 600 tertiary)
- 10pt vertical padding, 1pt bottom divider

### Navigation

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `rgba(14,14,22,0.94)` with `backdrop-filter: blur(20px) saturate(180%)`, 0.5pt top border `#2C2C42`
- Tabs (5): Plan · Run · Progress · Club · Profile
- Icon size: 22pt
- Active: `#C2F94E` (Lime — the active tab; icon fills solid)
- Inactive: `#65657E`
- Label: Sora 10pt weight 600, always shown
- No tint pill — color/fill change only

**Plan Header (no nav bar)**
- The Plan screen has no UINavigationBar — a header block: eyebrow ("5K PLAN · WEEK 4 OF 8" lime, tracked) + plan title (26pt weight 800) + meta line, with a trailing "Week 4 ▾" pill to switch weeks
- Detail screens (Run summary) use a sheet/push with a leading back chevron

### Input Fields

**Week Switcher Pill**
- Pill-ish, `#222234` bg, `#2C2C42` border, 999pt radius
- "Week 4 ▾" Sora 12pt weight 700, trailing chevron — taps open a week picker sheet

**Pace / Goal Stepper (plan setup)**
- Large Sora 24pt weight 800 numeral (target pace or race time)
- − / + circular buttons `#222234`, lime glyph
- Unit toggle (km / mi) as a small pill segment

**Segmented Control (This week / All / Calendar)**
- Container: `#222234`, 999pt radius, 4pt inset
- Selected segment: `#4F46E5` fill, white text; others transparent, `#9C9CB8`
- Slide animation 220ms ease-out

**Search (Club / find runners)**
- Pill field, `#222234`, 999pt radius, leading magnifier 18pt `#65657E`
- Placeholder "Search runners or clubs" 15pt `#65657E`
- Focus: 1.5pt `#6366F1` border

### Distinctive Components

**Training-Plan Week Strip — signature**
- Horizontal row of 7 day cells (see Day Cell above), full content width, 7pt gaps
- States: completed (green), today (solid Indigo + Lime dot), rest (muted), typed-upcoming (run-type dot)
- Tapping a cell opens that day's session detail; the strip scrolls horizontally for multi-week views

**Guided Run-Session Card — signature**
- Rounded card, 22pt radius, fill = linear gradient `#4F46E5 → #3A33B8` (~150°)
- Decorative translucent Lime circle bleeding off the bottom-right (`rgba(194,249,78,0.14)`)
- Lime pill tag "Today · Intervals" (near-black text, 10pt weight 800, uppercase)
- Session title white Sora 21pt weight 800
- Stats row: 3 stat blocks (Distance / Duration / Avg pace) — value 18pt weight 800 white, label 10pt weight 600 uppercase white@70%
- Full-width Lime CTA "Start guided run" with a play glyph, near-black text

**Workout-Structure Breakdown — signature**
- Header "Workout structure" 13pt weight 700
- Segmented phase bar (see above)
- Per-step rows (see Step Row): warm-up → main set → cool-down, each with a pace target

**Progress / Pace Trend Card**
- Line chart of pace or weekly volume; trend line in Lime over an Indigo-tinted fill
- PR markers as small Lime dots; goal pace as a dashed Indigo line

**Run Summary (post-run)**
- Big distance + time, splits list (per-km rows with pace bars colored by run-type), a map polyline route in Indigo with a Lime start dot
- "Great work" header and a PR badge (Lime) if a record was set

**Club Feed Row**
- Runner avatar + name + completed-session chip (run-type colored) + a kudos (Lime) button

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 7, 8, 10, 12, 14, 16, 18, 22, 24, 32, 40
- Screen horizontal inset: 18pt
- Card internal padding: 16–18pt
- Vertical gap between cards/sections: 14–16pt
- Week-strip cell gap: 7pt
- Step-row vertical padding: 10pt

### Grid & Container
- iPhone: single column, 18pt horizontal insets
- The Plan screen stacks: header → week strip → session card → structure breakdown
- The week strip is a 7-column equal-flex row spanning the full content width
- iPad: content max-width 640pt centered; week strip can show 2 weeks side-by-side

### Whitespace Philosophy
- **Disciplined, not bubbly**: cards are squared-soft (14–22pt), gaps are consistent 14–16pt — it should feel like a training plan, organized and scannable
- **The week strip earns top space**: it's the at-a-glance answer; it sits right under the header
- **The session card is the focal block**: the most saturated element, with the Lime Start button as the single clearest action
- **Pace data gets right-edge alignment**: step rows align pace values on the trailing edge so the eye can scan targets down a column

### Border Radius Scale
- Square (0pt): segmented-bar internal divisions, route polyline ends
- Soft (8pt): structure segmented bar, small chips
- Standard (10pt): run-type chips, phase step icons
- Comfortable (14pt): cards, buttons, day cells
- Card (18pt): larger list cards, sheets' inner content
- Session (22pt): the guided run-session gradient card, bottom-sheet tops
- Pill (999pt): week switcher, segmented controls, search
- Circle (50%): avatars, stepper buttons, today/PR dots, kudos button

## 6. Depth & Elevation

Runna is dark-first and mostly flat — depth comes from surface lift and the brand glow, not heavy shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, sits on canvas | Header text, dividers, day cells |
| Surface (Level 1) | Surface lift `#181826` + 1pt `#2C2C42` border | Cards, structure breakdown, lists |
| Session (Level 1.5) | Faint indigo glow `rgba(79,70,229,0.30) 0 10px 28px` | Guided run-session gradient card |
| Sheet (Level 2) | `rgba(0,0,0,0.5) 0 -8px 30px` | Bottom sheets (week picker, swap workout, run setup) |
| Overlay | `rgba(8,8,16,0.6)` | Dim behind sheets and modals |

**Shadow Philosophy**: on the dark canvas, drop shadows read poorly, so elevation is conveyed primarily by the `#181826` surface lift plus a crisp `#2C2C42` 1pt border. The only colored elevation is the session card's faint indigo glow, which makes the day's workout feel like the energized focal point. In the optional light theme, cards gain a soft indigo-tinted shadow `rgba(79,70,229,0.10) 0 6px 18px`.

### Motion
- **Week strip load**: day cells stagger-fade in left→right, 40ms each, 220ms ease-out; the "today" cell does a subtle 1.0→1.04→1.0 pulse on first appearance
- **Session card entrance**: scales 0.97 → 1.0 + fade over 300ms ease-out; the decorative lime circle drifts in
- **Structure bar grow**: segments expand from 0 width left→right over 500ms ease-out, in phase order
- **Start run press**: Lime button scales 0.98 + soft haptic, then a 350ms transition into the live run screen (slide-up)
- **Live run**: the pace number animates with a quick count when a new split is hit; a soft haptic marks each km
- **Step row complete (during run)**: row fills with a left→right Lime sweep + checkmark, 300ms ease-out
- **Tab switch**: cross-fade 200ms; active icon fills + Lime tint tweens 150ms
- **Segmented control**: selected pill slides 220ms ease-out
- **Sheet present**: slides up 300ms ease-out; backdrop fades 200ms
- **PR celebration**: Lime badge pops with a 400ms spring + confetti-light particles + success haptic
- **Reduce Motion**: replace stagger/scale/grow with 200ms cross-fades; bars and strip render at final state; no pulse

## 7. Do's and Don'ts

### Do
- Use the dark, Indigo-tinted blue-black canvas (`#0E0E16`) as the default — it's a training app for dawn/dusk
- Make the training-plan week strip the at-a-glance hero — color-coded day cells right under the header
- Reserve Lime `#C2F94E` for action and energy — Start button, today dot, key CTAs, PR badges
- Use Indigo `#4F46E5` as the structural color — plan surfaces, today cell, session card, info buttons
- Always pair Lime fills with near-black text `#1A1A1A` — never Lime as a text color on dark
- Keep the run-type color system fixed (Easy `#34D399`, Tempo `#FBBF24`, Intervals `#FB7185`, Long `#4F46E5`)
- Show a pace target on every workout step — pace is the substance; heavy numerals
- Use the segmented structure bar (warm-up → main → cool) so a session reads in one glance
- Use Sora's athletic geometry — it reads like performance gear; heavy stats, regular prose
- Right-align pace values in step rows so targets scan down a column
- Give the session card a faint indigo glow so it's the energized focal point

### Don't
- Don't use a neutral grey/black canvas — Runna's dark is tinted toward Indigo for brand cohesion
- Don't use Lime for large fills, backgrounds, or as text on dark — it's an action accent only, with near-black text
- Don't recolor the run-type system — athletes read effort by color; keep Easy/Tempo/Intervals/Long fixed
- Don't use a soft rounded/playful font — Sora's technical geometry is core to the athletic identity
- Don't make day cells round/bubbly — they're squared-soft (14pt), disciplined like a plan
- Don't bury the week strip below the session card — the strip is the at-a-glance answer, it sits up top
- Don't hide pace targets behind a tap — every step shows its `/km` (or `/mi`) target inline
- Don't bold descriptive copy by default — prose is 400; only pace/stats are heavy
- Don't use heavy drop shadows on dark — rely on surface lift + the `#2C2C42` border
- Don't add a second action color competing with Lime for "go"
- Don't use Material-style tab tint pills — active is a Lime color/fill change only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Day cells tighter (6pt gap); session title 19pt; 14pt insets |
| iPhone 13/14/15 | 390pt | Standard layout (reference) |
| iPhone 15/16 Pro | 393pt | Dynamic Island clearance in status zone |
| iPhone 15/16 Pro Max | 430pt | Larger day cells; session title 23pt; bigger stat values |
| iPad (portrait) | 768pt | Content max-width 640pt centered; week strip may show 14 days |
| iPad (landscape) | 1024pt+ | Two-column: week strip + session left, structure right, max 900pt |
| Apple Watch (companion) | — | Live run mirrors pace/clock; structure step + Lime progress; bigger tabular numerals |

### Dynamic Type
- Scales: screen title, plan/session title, section, card title, body, step item
- Fixed: eyebrow, pace/tag, day-cell number, stat labels, tab labels
- Session title caps at +25% so the gradient card doesn't overflow

### Orientation
- Phone: portrait-primary on Plan; the live run screen is portrait-locked
- iPad: supports landscape with the two-column split

### Touch Targets
- Tab bar icons: 22pt glyph, 44pt hit
- Day cell: full-cell tap, ≥ 44pt tall
- Lime Start button: ≥ 48pt tall, full-width on the card
- Segmented control segments: 36pt tall
- Week switcher pill: ≥ 36pt tall

### Safe Area Handling
- Top: header respects safe area + Dynamic Island
- Bottom: 68pt tab bar + home indicator; the live run controls float above the home indicator
- The session card sits inside the scroll content with top inset — never under the status bar
- Sheets respect bottom safe area; the run-setup sheet scrolls above the keyboard

## 9. Agent Prompt Guide

### Quick Color Reference
- Runna Indigo (structure): `#4F46E5` / bright `#6366F1` / pressed `#4338CA`
- Runna Lime (action/"go"): `#C2F94E` / pressed `#A3D93B`
- Canvas: `#0E0E16` (Indigo-tinted, NOT neutral) — light `#F6F6FB`
- Surface 1: `#181826` · Surface 2: `#222234` · Divider: `#2C2C42`
- Text primary: `#F3F3FB` / secondary `#9C9CB8` / tertiary `#65657E`
- Text on Lime (only): `#1A1A1A`
- Run types: Easy `#34D399`, Tempo `#FBBF24`, Intervals `#FB7185`, Long `#4F46E5`

### Example Component Prompts
- "Build the Runna training-plan week strip in SwiftUI: a horizontal row of 7 equal-flex day cells with 7pt gaps. Each cell is ~0.62 aspect, 14pt corner radius, `#181826` bg, 1pt `#2C2C42` border; inside, a weekday label (Sora 10pt weight 700 `#65657E`), a date number (Sora 14pt weight 800), and a 7pt run-type dot. State variants: Completed = `rgba(52,211,153,0.12)` bg + `rgba(52,211,153,0.35)` border + date `#34D399`; Today = solid `#4F46E5` bg + `#6366F1` border + white text + a `#C2F94E` dot; Rest = date `#65657E` + neutral dot. On load, stagger-fade cells left→right 40ms each over 220ms ease-out, and pulse the Today cell 1.0→1.04→1.0 once."

- "Build the Runna guided run-session card: a 22pt-radius card filled with a linear gradient `#4F46E5 → #3A33B8` at ~150°, with a decorative `rgba(194,249,78,0.14)` circle clipped off the bottom-right. Inside: a Lime pill tag 'Today · Intervals' (Sora 10pt weight 800, near-black `#1A1A1A` text, uppercase); the session title 'Today's intervals' in Sora 21pt weight 800 white; a stats row of three blocks (Distance 8.4 km / Duration ~48 min / Avg pace 4:52) — values Sora 18pt weight 800 white, labels Sora 10pt weight 600 uppercase white@70%; and a full-width Lime button 'Start guided run' (14pt radius, near-black text, leading play glyph). Add a faint indigo glow `rgba(79,70,229,0.30) 0 10px 28px`. On appear scale 0.97→1.0 + fade 300ms ease-out."

- "Build the Runna workout-structure breakdown: a header 'Workout structure' Sora 13pt weight 700; a 30pt-tall segmented bar (8pt radius, 3pt gaps) with phase segments — warm-up & cool-down in Easy green `#34D399`, the main set in its run-type color (e.g. Intervals `#FB7185`), widths proportional to phase duration; then per-step rows: a 32pt rounded-square (9pt radius) phase icon in the phase color with a 2-letter code (WU/6×/CD) in near-black weight 800, a title (Sora 15pt weight 700) + description (11pt weight 500 `#9C9CB8`), and a trailing pace value (Sora 13pt weight 800) with a '/km' sublabel (10pt weight 600 `#65657E`), 10pt vertical padding, 1pt `#2C2C42` divider. Grow the bar segments left→right over 500ms ease-out."

- "Build the Runna primary 'go' button: a 14pt-radius rectangle, `#C2F94E` fill, near-black `#1A1A1A` Sora 16pt weight 800 text with an optional leading play glyph. Pressed: `#A3D93B` + scale 0.98. The Indigo informational variant is `#4F46E5` fill with white text."

- "Render the Runna bottom tab bar: 68pt + safe area, 5 tabs — Plan, Run, Progress, Club, Profile — 22pt icons, Sora 10pt weight 600 labels. Active tab `#C2F94E` (Lime) with the icon filled solid; inactive `#65657E`. No tint pill — color/fill change only."

### Iteration Guide
1. Dark-first: canvas is an Indigo-tinted blue-black `#0E0E16` (NOT neutral grey/black) — low-glare for dawn/dusk training
2. The training-plan week strip is the at-a-glance hero — 7 color-coded day cells (done/today/rest/typed) under the header
3. Two-color brand: Indigo `#4F46E5` is structure; Lime `#C2F94E` is action/"go" — Start button, today dot, CTAs only
4. Lime fills always use near-black text `#1A1A1A` — Lime is never a text color on dark
5. The run-type color system is fixed (Easy `#34D399`, Tempo `#FBBF24`, Intervals `#FB7185`, Long `#4F46E5`) — athletes read effort by color
6. Every workout step shows a pace target inline (`/km` or `/mi`) — pace is the substance, heavy numerals
7. The guided run-session card is the focal block — Indigo gradient + faint indigo glow + the single Lime Start button
8. The structure segmented bar (warm-up → main → cool) makes a session readable in one glance
9. Sora's athletic, technical geometry is core — heavy stats (700–800), regular prose (400); cards are squared-soft, not bubbly
10. Minimal chrome — 5-tab bar, no opaque nav bar; the plan header lives in scroll content with a week-switcher pill
