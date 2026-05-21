# Design System Inspiration of Nike Run Club (iOS)

## 1. Visual Theme & Atmosphere

Nike Run Club is a stadium tunnel — black walls, white spotlights, a runner sprinting into them. The canvas is true black (`#000000`) on every screen, the typography is editorial and athletic, and every interaction is shouted in screaming capital letters. NRC is unapologetically loud. Where Strava is a newspaper, NRC is a poster. The brand is built for the moment just before a hard run: bold display weights, deep contrast, urgent action verbs ("LET'S DO THIS", "START RUN", "GO").

The accent is Nike Volt (`#CCFF00`) — the radioactive yellow-green that's been on Nike Air Max heels since the late 80s — backed by Nike Red (`#FA5400`) for fire moments and an austere Brake-Light Red (`#E50916`) for the workout-finish heart-rate-zone screens. Volt is the brand voice: progress rings on guided runs, accent strokes on stats, the "START" button on quick runs. Red is reserved for results, achievements, and "you crushed it" messaging. Everything else is monochromatic — true black canvas, white text, Nike Gray ladder (`#1A1A1A` to `#8E8E8E`) for substrate hierarchy.

Typography is Nike's house display face Trade Gothic (Heavy Condensed for the screaming hero numbers, Bold Condensed for section heads) backed by SF Pro for body and metadata. Trade Gothic Condensed is the secret sauce — it lets Nike pack "8.2 MILES" or "4.5K RUN" at hero-display size into a single line without losing impact, and the heavy condensed weight is the visual signature of Nike's stadium graphics, shoe campaigns, and apparel tags. Numbers are tabular (`tnum`) on every stat. Letter spacing is generous on display caps (0.5pt at 64pt down to 0.2pt at 16pt) — Nike-style headlines breathe.

The hero screen of the app is the Run Tracking screen during an active run: pure black canvas, a Volt-yellow progress ring filling clockwise around the elapsed time, three stats (DISTANCE / PACE / HEART RATE) stacked above it in giant condensed caps, and a pause/stop control at the bottom. This screen is what NRC is famous for — it's the closest a phone gets to feeling like a piece of equipment.

**Key Characteristics:**
- True black canvas (`#000000`) on every screen — sports broadcasting aesthetic
- Nike Volt (`#CCFF00`) — the radioactive accent on progress rings, primary CTAs, achievements
- Nike Red (`#FA5400`) — fire/intensity accent on workout results, "crushed it" moments
- Trade Gothic Heavy Condensed — the hero display face; everything screams in CONDENSED ALL CAPS
- Run Tracking screen: massive Volt progress ring + 3-up stat stack DISTANCE / PACE / HR
- Numbers everywhere — tabular, condensed, screaming sizes (88pt elapsed time during a run)
- Coach Bennett voice-over branding — friendly text labels paired with hard ALL CAPS verbs
- Workout cards with full-bleed photography of athletes (real Nike athletes, training gyms)
- "Achievement medals" — illustrated badges in Volt/Red gradients, animated reveal on completion
- Bottom tab bar: Home / Activity / Coach / Goal / Profile — black with Volt active state
- Heavy haptics on "Start Run" and milestones — `.heavy` impact, not `.medium`
- Action buttons are full-width pills with screaming labels: "LET'S DO THIS", "START RUN", "FINISH"

## 2. Color Palette & Roles

### Primary
- **Nike Volt** (`#CCFF00`): THE accent — progress rings, primary CTAs, active states, achievement gradients, voice-coach prompts.
- **Volt Pressed** (`#9FCC00`): Active/pressed state on Volt CTAs.
- **Volt Dim** (`#8AA800`): Used on light-mode (rare in NRC) for accessibility-compliant accent on white.

### Secondary (Intensity)
- **Nike Red** (`#FA5400`): "Crushed it" moments, achievement reveals, fire callouts, the brand's secondary signature.
- **Brake-Light Red** (`#E50916`): Heart rate zone 5 (max), pace-too-slow warning, error states.

### Canvas, Surfaces & Dividers
- **True Black** (`#000000`): Primary canvas everywhere — Nike's stadium void.
- **Nike Charcoal** (`#0A0A0A`): A whisper lighter than the canvas — section background variations, card fills inside the dark.
- **Nike Surface 1** (`#1A1A1A`): Card backgrounds on home feed, settings rows.
- **Nike Surface 2** (`#2B2B2B`): Pressed states, input field fill, chip backgrounds.
- **Nike Divider** (`#333333`): Hairline row dividers inside settings and result detail.

### Text
- **Pure White** (`#FFFFFF`): Primary text — every hero stat, every button label, every screaming caps headline.
- **Nike Gray 1** (`#B3B3B3`): Secondary text — labels, "MIN/MI" units, metadata, "Hosted by Coach Bennett".
- **Nike Gray 2** (`#8E8E8E`): Tertiary — placeholder, disabled, "Tap to continue" prompts.
- **Nike Gray 3** (`#5C5C5C`): Quaternary — separator caption, footnotes, "as of 9:42 AM".

### Heart Rate Zones (used on the workout results "training effect" page)
- **HR Zone 1 (Easy)** (`#3D87F4`): Endurance blue.
- **HR Zone 2 (Steady)** (`#48C77E`): Steady green.
- **HR Zone 3 (Moderate)** (`#FFD600`): Tempo yellow.
- **HR Zone 4 (Hard)** (`#FA5400`): Threshold orange (Nike Red).
- **HR Zone 5 (Max)** (`#E50916`): Max red (Brake-Light Red).

### Semantic
- **Success Volt** (`#CCFF00`): Run uploaded, sync complete (Volt doubles as success).
- **Warning Amber** (`#FFC400`): GPS signal weak, low battery in session.
- **Error Red** (`#E50916`): Failed sync, payment failed, GPS lost.

### Light Mode
NRC does have a light mode (for accessibility) but Nike's brand is intentionally dark-first; light mode is rarely used in marketing.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F4F4F4`)
- **Light Text Primary** (`#0A0A0A`)
- **Volt on Light** (`#8AA800`): Volt darkened to pass WCAG AA on white.
- **Red on Light** (`#FA5400`): unchanged

## 3. Typography Rules

### Font Family
- **Display (hero stats, section heads, button labels)**: `Trade Gothic Next LT W04 Heavy Condensed` (proprietary licensed face by Linotype) — the screaming Nike voice
- **Display alt**: `Futura Condensed Extra Bold` is a sometimes-substitute on shoe drops; not used in NRC chrome
- **Body / metadata**: `SF Pro Text` — Apple-system fallback for body, comments, coach copy
- **Fallback stack**: `'Trade Gothic Next LT', 'Trade Gothic LT Std', 'Helvetica Neue Condensed Bold', 'Arial Narrow Bold', -apple-system, sans-serif`
- **Web/marketing Google Fonts substitute**: `Oswald` (close enough — geometric condensed with similar proportions); `Bebas Neue` for ultra-condensed all-caps moments
- **Numeric weight**: SF Pro `monospacedDigit()` on every running stat — pace, distance, elapsed, calories, HR

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Run Hero Stat | Trade Gothic Condensed | 88pt | 900 (Heavy) | 0.95 | 0.5pt | Elapsed time during active run: "1:14:23" |
| Pre/Post Run Hero | Trade Gothic Condensed | 64pt | 900 | 0.95 | 0.4pt | "8.2 MI" on the results screen |
| Large Nav Headline | Trade Gothic Condensed | 48pt | 900 | 0.95 | 0.3pt | "JUST DO IT", "LET'S DO THIS" — pre-run hype copy |
| Screaming Section Header | Trade Gothic Condensed | 32pt | 900 | 1.0 | 0.3pt | "TODAY'S RUN", "ACHIEVEMENTS" UPPERCASE |
| Sub Header | Trade Gothic Condensed | 22pt | 700 (Bold) | 1.1 | 0.3pt | "5K · 30 MIN · COACH BENNETT" UPPERCASE |
| Stat Label (under big number) | Trade Gothic Condensed | 13pt | 700 | 1.0 | 1.0pt | "MIN/MI", "BPM", "CAL" UPPERCASE — very wide tracking |
| Card Title | SF Pro Display | 17pt | 700 | 1.2 | -0.2pt | Run plan card title (mixed case allowed) |
| Body | SF Pro Text | 15pt | 400 (Regular) | 1.45 | -0.1pt | Coach Bennett's voice copy, training plan descriptions |
| Body Bold | SF Pro Text | 15pt | 600 | 1.45 | -0.1pt | Inline emphasis |
| Button Label | Trade Gothic Condensed | 17pt | 900 | 1.0 | 0.5pt | "START RUN", "FINISH", "PAUSE" UPPERCASE |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.3pt | Bottom tab — mixed case |
| Metadata | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "Aug 12 · 8:42 AM", "5 miles · Recovery" |
| Caption | SF Pro Text | 11pt | 500 | 1.3 | 0.1pt | "GPS connecting...", "Powered by NRC" |
| Achievement Number | Trade Gothic Condensed | 56pt | 900 | 0.95 | 0pt | "100" miles, "1,000" lifetime — on achievement medal reveal |

### Principles
- **Trade Gothic Condensed Heavy Condensed for every hero number and every button label** — the Nike sound
- **Tracking widens at small sizes**: 1.0pt at 13pt stat labels, 0.5pt at 17pt buttons, 0.3pt at 48pt headlines — uppercase Nike copy breathes
- **Line-height 0.95-1.0 at display**: Nike crops tight; condensed letterforms don't need vertical air
- **`monospacedDigit()` on every stat** — pace columns must align on the results page
- **Mixed case allowed in body copy only** — anything Trade Gothic is UPPERCASE; anything SF Pro can be mixed
- **Dynamic Type respected on body and metadata**; FIXED on hero numbers (the entire visual identity depends on them)

## 4. Component Stylings

### Run Tracking Screen (the hero component)

**Layout**
- Full-screen, black canvas, portrait orientation locked
- Top: GPS signal strength + battery + time (status bar, white)
- Top-third: 3-up stat row — DISTANCE / PACE / HR — each cell vertically stacked label-over-value
  - Label: Trade Gothic Condensed 13pt 700 white @ 70% with 1.0pt tracking UPPERCASE — "DISTANCE / MIN/MI / BPM"
  - Value: Trade Gothic Condensed 64pt Heavy white tabular — "1.4", "8:42", "152"
- Middle: Massive Volt progress ring — 280pt diameter, 6pt stroke, fill `#CCFF00`, track `rgba(255,255,255,0.1)`
  - Inside the ring: elapsed time at 88pt Trade Gothic Condensed Heavy white tabular — "12:34"
  - Below the time, inside the ring: "ELAPSED" label at 13pt UPPERCASE 1.0pt tracking white @ 70%
- Bottom: Pause button (large circle, white) and Lock screen affordance (smaller, side)
- Background: pure `#000000`, no other content

**Progress Ring**
- Stroke: 6pt rounded cap
- Track: `rgba(255,255,255,0.1)`
- Fill: Volt `#CCFF00`, starts at -90° (12 o'clock) and progresses clockwise
- Rotates only when there's a "Coach Bennett interval" — otherwise it just fills toward the run's goal
- Animation: discrete 1s linear ticks for each progress unit (mile, kilometer, time interval)

**Pause / Stop / Lock Buttons**
- Pause: 72pt × 72pt white circle with 28pt black `pause.fill` glyph, centered horizontally near the bottom
- After pause, Pause becomes Resume (Volt circle with black `play.fill`) + Stop button appears to the left (red circle `#E50916`)
- Lock affordance: 48pt × 48pt black circle with white `lock` glyph, top-right inset 16pt — tap to disable accidental taps mid-run

### Pre-Run / Post-Run Hero Cards

**Pre-Run "LET'S DO THIS" Screen**
- Full-bleed photography of a runner mid-stride (Nike athlete or stock photography)
- 60% gradient overlay from transparent (top) to `rgba(0,0,0,0.85)` (bottom)
- Bottom-aligned content stack:
  - Coach attribution: SF Pro Text 13pt Medium white @ 70% — "COACH BENNETT · 30 MIN · RECOVERY"
  - Headline: Trade Gothic Condensed 48pt Heavy white UPPERCASE — "EASY RECOVERY RUN"
  - Body: SF Pro Text 15pt Regular white — Coach's intro paragraph
  - "LET'S DO THIS" full-width pill button below

**Post-Run Results "YOU CRUSHED IT" Screen**
- Black canvas
- Hero: Trade Gothic Condensed 64pt Heavy Volt `#CCFF00` — "YOU CRUSHED IT"
- Below: 3-up stat row in same massive Trade Gothic numbers (white)
- Map snapshot below stats: route polyline in Volt `#CCFF00` with `rgba(204,255,0,0.3)` halo
- Coach Bennett quote card: dark surface, Coach's photo + quote in body italic
- "ACHIEVEMENTS UNLOCKED" section if any — animated medal reveal

### Achievement Medal (the celebration moment)

**Design**
- Hexagonal medal silhouette, 200pt diameter, gradient fill Volt `#CCFF00` → Nike Red `#FA5400` for "100 miles" tier, or solid Volt for milestone tiers
- Centered icon: Trade Gothic Condensed Heavy 56pt — the milestone number ("100", "500", "1K")
- Below the medal: medal name in Trade Gothic 22pt UPPERCASE "100 MILES" + description in SF Pro Text 15pt Regular
- Reveal animation: 800ms — medal fades in at 0.5x scale, springs to 1.0x at damping 0.6, simultaneously a 8-particle Volt confetti burst behind the medal
- Tap: bouncing scale 1.0 → 1.05 → 1.0, haptic `.notificationOccurred(.success)`

### Run Plan Card (Home Feed)

**Structure**
- Full-width minus 16pt margins, 320pt tall
- Background: full-bleed photography with `rgba(0,0,0,0.6)` overlay gradient bottom-up
- Bottom-aligned content (16pt inset):
  - Eyebrow: "GUIDED RUN · 25 MIN" in SF Pro Text 11pt Medium UPPERCASE white @ 70% with 0.5pt tracking
  - Title: "RECOVERY MILE" in Trade Gothic Condensed 32pt Heavy white UPPERCASE
  - Description: "Coach Bennett · Easy effort" in SF Pro Text 13pt Regular white @ 80%
- Corner radius: 16pt
- No shadow (the canvas is already black; shadow doesn't help)
- Tap: scale 0.98, haptic `.impactOccurred(.medium)`, push to plan detail

### Buttons

**Primary Action Button ("START RUN", "LET'S DO THIS", "FINISH")**
- Full-width minus 16pt margins
- Background: `#FFFFFF` (white on black canvas — the inversion of the canvas)
- Text: `#000000`, Trade Gothic Condensed 17pt Heavy 900 UPPERCASE, 0.5pt tracking
- Corner radius: 32pt (full pill, very rounded)
- Height: 56pt
- Pressed: scale 0.98, no color change
- Haptic on tap: `.impactOccurred(.heavy)` — Nike's CTA tap is the heaviest in the system

**Volt Highlight Button (special celebratory CTAs)**
- Background: `#CCFF00` Volt
- Text: `#000000`, Trade Gothic Condensed 17pt Heavy UPPERCASE
- Used for "CLAIM YOUR MEDAL", "JOIN CHALLENGE"
- Same shape and shadow as primary

**Outline Button (secondary)**
- Background: transparent
- Border: 2pt `#FFFFFF`
- Text: `#FFFFFF`, Trade Gothic Condensed 17pt Heavy UPPERCASE
- Same shape

**Pause / Stop Circular Buttons (in-run)**
- Pause: 72pt × 72pt white circle with black `pause.fill` 28pt
- Stop: 72pt × 72pt `#E50916` red circle with white `stop.fill` 28pt
- Tap: scale 0.95, haptic `.impactOccurred(.heavy)`

**Text Link**
- "VIEW ALL" or "SEE DETAILS" in Trade Gothic Condensed 14pt Bold UPPERCASE white, 0.5pt tracking
- No underline, 44pt hit area

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#000000` with 0.5pt `#333333` top hairline
- Tabs (5): Home / Activity / Coach / Goal / Profile
- Icon: 24pt outlined SF Symbol, `#5C5C5C` inactive / `#FFFFFF` active
- Active state: white icon + 4pt Volt `#CCFF00` underline dot below the icon, centered
- Labels: SF Pro Text 10pt Semibold mixed case, `#8E8E8E` inactive / `#FFFFFF` active
- Tap haptic: `.selectionChanged()`

**Top App Bar**
- Height: 56pt + safe area
- Background: `#000000`
- Title: Trade Gothic Condensed 32pt Heavy UPPERCASE white — "HOME", "ACTIVITY", "GOAL"
- Right: action glyphs at 22pt white

### Input Fields

**Text Field**
- Background: `#2B2B2B`
- Border: 1pt `rgba(255,255,255,0.1)` default, 2pt Volt focused
- Corner radius: 8pt
- Padding: 14pt vertical, 14pt horizontal
- Text: SF Pro Text 15pt Regular white
- Placeholder: `#8E8E8E`

### Distinctive Components

**Heart Rate Zone Bar (on the workout results page)**
- A horizontal stacked bar — 5 segments, each colored to its zone (`#3D87F4` blue → `#48C77E` green → `#FFD600` yellow → `#FA5400` orange → `#E50916` red), proportional to time spent in each zone
- Height: 8pt
- Corner radius: 4pt
- Below the bar: 5 zone labels in Trade Gothic Condensed 11pt 700 UPPERCASE white with zone color dots, plus zone time in tabular SF Pro

**Pace Ladder (workout intervals)**
- Vertical list of intervals
- Each row: interval number (Trade Gothic 22pt Heavy white) + target pace (Trade Gothic 17pt 700 Volt) + actual pace (SF Pro 13pt Regular Nike Gray 1) + rest interval bar

**Coach Bennett Quote Card**
- Background: `#1A1A1A`
- Coach's circular photo, 48pt diameter, top-left
- Coach name: SF Pro Text 13pt Semibold white "COACH BENNETT" UPPERCASE
- Quote: SF Pro Text 17pt Regular Italic white — Coach's voice line ("So beautiful, what a run.")
- Corner radius: 16pt
- Padding: 16pt

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 16pt on most screens; 24pt on Run Tracking and post-run results
- Section vertical gap: 32pt between major sections; 48pt above hero CTA buttons

### Grid & Container
- Content width: full minus 16pt margins
- Plan cards: full-width, stacked vertically (no grid)
- Activity history: chronological vertical list with date separators
- Profile achievements: 3-col 1:1 hexagonal medal grid

### Whitespace Philosophy
- **Black is the breathing room**: the canvas absorbs visual fatigue, the Volt and the Trade Gothic command attention
- **Hero numbers crop tight**: line-height 0.95 means the elapsed time touches the progress ring's inner edge — that's intentional
- **Stack tightly, but space hero CTAs**: 48pt gap above a "START RUN" button so it reads as committed

### Border Radius Scale
- Square (0pt): map polyline halo, stat-row dividers
- Standard (8pt): text fields, input fills
- Medium (16pt): plan cards, quote cards
- Pill (32pt): primary action button (very rounded but not full pill)
- Full Pill (500pt): chip pills, tag pills
- Circle (50%): avatars, pause/stop buttons, profile photos

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Almost everything — Nike is flat on black |
| Card (Level 1) | None (the photo's gradient overlay creates the depth) | Plan cards |
| Pop (Level 2) | `rgba(204,255,0,0.4) 0 6px 16px` Volt glow | The "START RUN" button or active progress ring |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -8px 32px` | Bottom sheets for Pause confirmation, Lock screen |

**Shadow Philosophy**: Nike is flat on black. The only elevation is the Volt glow under "START RUN" (a colored shadow that says GO) and the natural gradient overlays on photography. Cards never cast neutral shadows — that's a Material/Google idiom, not a Nike idiom.

### Motion
- **Progress ring fill**: 1s linear tick per progress unit (mile / km / interval); no spring, just deterministic
- **"START RUN" tap**: scale 0.98 instant + `.impactOccurred(.heavy)` haptic, then transition to Run Tracking screen via 400ms `.easeOut` fade (no slide)
- **Pause tap**: button scales to 0.95 + haptic `.impactOccurred(.heavy)`, transforms to Resume (Volt circle) + Stop (red circle) over 250ms spring
- **Achievement medal reveal**: 800ms — fade in at 0.5x scale, spring to 1.0x at `response: 0.4 dampingFraction: 0.6`, simultaneously 8-particle Volt confetti burst with `delay: 0.2`
- **Coach Bennett voice cue indicator**: subtle Volt dot blinks at 1Hz when Coach is speaking via headphones
- **Tab switch**: `.selectionChanged()` haptic, icon fades from outlined to filled in 200ms, Volt underline dot slides via `matchedGeometryEffect`
- **Confetti burst on milestone (PR mile pace, fastest 5K)**: bigger burst — 16 Volt particles + heavy haptic

## 7. Do's and Don'ts

### Do
- Use true black `#000000` for the canvas on every screen — Nike's brand is dark-first
- Use Volt `#CCFF00` as the primary accent — progress rings, active states, primary CTAs (when not white)
- Use Trade Gothic Condensed Heavy for every hero number and every button label — CONDENSED ALL CAPS is the Nike voice
- Apply 0.5pt-1.0pt letter spacing on uppercase Trade Gothic — Nike copy breathes
- Use `monospacedDigit()` on every running stat — pace columns must align
- Render the run-tracking progress ring at exactly 280pt diameter with 6pt stroke and rounded caps
- Use heavy haptics on "START RUN" and "PAUSE" — Nike's CTAs are committed, not casual
- Use 88pt Trade Gothic for the elapsed time inside the progress ring — it's the visual identity
- Show Coach Bennett's photo + name on every guided run plan card
- Use white as the primary CTA color (white pill on black canvas) — the inversion of the canvas is the brand
- Use Nike Red `#FA5400` for results/achievements, Volt for in-run/progress — they're different verbs
- Use the hexagonal medal silhouette for achievements (not a circle, not a star — Nike's pattern is hex)

### Don't
- Don't use a light canvas as the default — NRC is dark-first; light mode exists only for accessibility
- Don't use mixed case in Trade Gothic — it's UPPERCASE only; only SF Pro body copy uses mixed case
- Don't render the progress ring with sharp caps — it must be rounded
- Don't use system blue or any cool accent — Nike's palette is monochromatic black/white with Volt + Red
- Don't use proportional digits in stats — `monospacedDigit()` is mandatory
- Don't put a drop shadow under cards — Nike is flat on black
- Don't replace Trade Gothic with a system condensed face — `SF Compressed` is close but Trade Gothic's letterforms (especially the lowercase 'a' on the rare title) are part of the brand
- Don't use the heavy haptic on tab switches — that's reserved for "START RUN", "PAUSE", and achievement reveals
- Don't use the medal hexagon as an avatar — hex is exclusively for achievements
- Don't use any Volt larger than 20% surface area on a screen — it's an accent, not a color field; Volt fields are exhausting

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Progress ring shrinks to 240pt; hero number 72pt |
| iPhone 13/14/15 | 390pt | Standard 280pt ring, 88pt hero number |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected — top app bar sits below it |
| iPhone 15/16 Pro Max | 430pt | Larger 320pt ring, 96pt hero number |
| Apple Watch (companion) | 184-220pt | Run tracking simplified to elapsed + pace stacked, no ring |
| iPad | 768pt+ | Run tracking unchanged (single-column); home feed uses 2-col plan grid |

### Dynamic Type
- Body and metadata: full scale
- Run hero stats (Trade Gothic 88pt): FIXED — the visual identity depends on them
- Button labels (Trade Gothic 17pt): FIXED
- Plan card titles: scale up to xLarge then clamp

### Orientation
- Portrait-locked on Run Tracking
- Map view on activity detail can rotate
- Plan detail and Coach screens are portrait-locked

### Touch Targets
- Pause button: 72pt visual, 80pt tap area
- Tab icons: 24pt visual, 56pt tap row
- Primary action button: 56pt height full-width

### Safe Area Handling
- Top: safe area honored — top app bar sits below Dynamic Island / notch
- Bottom: tab bar respects home indicator; Run Tracking pause button respects safe area inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#000000`
- Surface 1: `#1A1A1A`
- Surface 2: `#2B2B2B`
- Divider: `#333333`
- Nike Volt: `#CCFF00`
- Volt Pressed: `#9FCC00`
- Nike Red: `#FA5400`
- Brake Red: `#E50916`
- White: `#FFFFFF`
- Gray 1: `#B3B3B3`
- Gray 2: `#8E8E8E`
- HR Zone 1 (Blue): `#3D87F4`
- HR Zone 2 (Green): `#48C77E`
- HR Zone 3 (Yellow): `#FFD600`
- HR Zone 4 (Orange): `#FA5400`
- HR Zone 5 (Red): `#E50916`

### Example Component Prompts
- "Build the Nike Run Club tracking screen: pure `#000000` canvas, locked to portrait. Top third has a 3-up horizontal row of stats — 'DISTANCE 1.4', 'PACE 8:42', 'BPM 152'. Labels in Trade Gothic Condensed 13pt Heavy white @ 70% with 1.0pt tracking UPPERCASE; values in Trade Gothic Condensed 64pt Heavy white tabular. Below: a 280pt diameter Volt `#CCFF00` progress ring with 6pt rounded stroke and `rgba(255,255,255,0.1)` track. Inside the ring: elapsed time '12:34' in Trade Gothic Condensed 88pt Heavy white tabular + 'ELAPSED' label below it in Trade Gothic 13pt with 1.0pt tracking UPPERCASE white @ 70%. Bottom: 72pt white pause circle button with a 28pt black `pause.fill` glyph. On the right inset 16pt: a 48pt black circle with white lock glyph."
- "Render the Nike pre-run hero card: full-bleed photo of a runner at golden hour, with a `linear-gradient(180deg, transparent 0%, rgba(0,0,0,0.85) 100%)` overlay. Bottom-aligned content stack: 'COACH BENNETT · 30 MIN · RECOVERY' in SF Pro Text 13pt Medium UPPERCASE white @ 70% with 0.5pt tracking. Below: 'EASY RECOVERY RUN' headline in Trade Gothic Condensed 48pt Heavy white UPPERCASE with 0.3pt tracking. Below: 2-line body paragraph in SF Pro Text 15pt Regular white. Below: full-width 'LET'S DO THIS' primary CTA — 56pt tall white pill (32pt corner radius), label in Trade Gothic Condensed 17pt Heavy `#000000` UPPERCASE with 0.5pt tracking. Haptic on tap: `.impactOccurred(.heavy)`."
- "Build a Nike achievement medal reveal: 200pt hexagonal silhouette, gradient Volt `#CCFF00` → Nike Red `#FA5400` fill. Centered milestone number '100' in Trade Gothic Condensed 56pt Heavy black tabular. Below the medal: '100 MILES' in Trade Gothic 22pt Heavy white UPPERCASE + 'Lifetime distance' in SF Pro Text 15pt Regular Nike Gray. Animation: medal fades in at 0.5x scale and springs to 1.0x with `.spring(response: 0.4, dampingFraction: 0.6)`, simultaneously emit 8 Volt particles in a 200pt radius burst over 600ms. Haptic on reveal: `.notificationOccurred(.success)`."
- "Design the bottom tab bar: 56pt + safe area, `#000000` background with a 0.5pt `#333333` top hairline. Five tabs — Home / Activity / Coach / Goal / Profile — each with a 24pt outlined SF Symbol. Inactive icon `#5C5C5C`, active icon `#FFFFFF`. Below the active icon: a 4pt Volt `#CCFF00` circle dot, slides between tabs via `matchedGeometryEffect`. Labels in SF Pro Text 10pt Semibold, mixed case, `#8E8E8E` inactive / `#FFFFFF` active. Tap haptic: `.selectionChanged()`."
- "Render the heart-rate zone bar from a workout results page: 8pt horizontal stacked bar with 4pt rounded corners. Five segments proportional to time spent — blue `#3D87F4`, green `#48C77E`, yellow `#FFD600`, orange `#FA5400`, red `#E50916`. Below the bar: 5 zone labels in Trade Gothic Condensed 11pt Heavy white UPPERCASE 'Z1 EASY · Z2 STEADY · Z3 MOD · Z4 HARD · Z5 MAX' each prefixed by an 8pt color dot, plus zone time '12:34' in SF Pro Text 11pt Regular Nike Gray 1 monospaced."

### Iteration Guide
1. Canvas is `#000000` true black on every screen — Nike's stadium void
2. Volt `#CCFF00` is the primary accent — progress rings, active states, achievements
3. Nike Red `#FA5400` for results/celebration, Brake Red `#E50916` for warnings/HR max
4. Trade Gothic Condensed Heavy on every hero number and every button label — UPPERCASE
5. SF Pro Text for body and metadata — mixed case allowed
6. Run tracking ring is exactly 280pt × 6pt stroke with rounded caps and a `rgba(255,255,255,0.1)` track
7. Elapsed time inside the ring is 88pt Trade Gothic Heavy tabular — the visual identity
8. Primary CTA is a white pill on black canvas (`#FFFFFF` background, `#000000` Trade Gothic label) — 56pt, 32pt corner radius
9. Heavy haptics on "START RUN" and "PAUSE" — Nike's CTAs are committed
10. Achievements use hexagonal medals — never circles, never stars — with Volt or Volt→Red gradients
