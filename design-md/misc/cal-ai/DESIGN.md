# Design System Inspiration of Cal AI (iOS)

## 1. Visual Theme & Atmosphere

Cal AI's iOS app is a dark, editorial calorie-tracker that treats your meals the way a photography portfolio treats its subjects — centered, well-lit, and uncluttered. The canvas is near-black (`#0A0A0A`), the text is crisp white, and the only other color in the interface comes from your own food photos and three softly-saturated macro accents. This is the anti-clinical calorie app: no red-orange warning states, no blocky blue dashboards, no kitschy illustrations of fruit. The app reads more like a minimal iOS health journal or an AI research product than a traditional fitness tracker.

The hero is the **AI scan flow**. You tap a bottom-center shutter, the camera fills the viewport with a dimmed viewfinder, a reticle animates across the food, and within a second the detected meal appears as an editable card — dish name, AI-generated ingredient breakdown, macros, calorie total in oversized numerals. The whole flow takes about 4 seconds and feels closer to Apple's Vision Pro capture UI than to a supermarket barcode scanner. The trust is built by making the AI's output feel surgical, not magical: every field is editable, every confidence score is visible.

Typography is a clean modern sans — close to **Inter** or **SF Pro Display** — deployed across two optical sizes. Large numerals (calorie totals, streak days, weight readings) are set at 48-72pt weight 700 with tight tracking. Body UI sits at 15-17pt weight 400-600. There are no rounded / friendly display faces here; the numbers look like data. This reinforces the product positioning: Cal AI is a tool, not a coach. The emotional tone comes from the photography, the animations, and the generous black space — not from cartoon mascots or encouraging prose.

**Key Characteristics:**
- Near-black canvas (`#0A0A0A`) — darker than most iOS apps, closer to OLED true-black
- High-contrast monochrome chrome with three macro accent colors as the only color story
- Oversized numerals (48-72pt, weight 700) for calorie totals and daily summaries
- AI-first capture flow: camera → reticle animation → editable result card, all in ~4 seconds
- Photo-centric meal cards — user's food photograph is the entire top 60% of each card
- Sharp, data-forward type (Inter / SF Pro) — no rounded display faces, no friendly curves
- Bottom tab bar with a large centered capture button (the "one-tap-to-log" promise)
- Subtle motion: 250-400ms springs, no bouncing mascot celebrations, no confetti

## 2. Color Palette & Roles

### Canvas & Surfaces
- **Canvas Black** (`#0A0A0A`): Primary dark canvas — nearly pure black, optimized for OLED.
- **Elevated Surface** (`#151515`): Meal cards, stat cards, grouped list rows.
- **Surface 2** (`#1E1E1E`): Sheet backgrounds, modals, input fills.
- **Surface 3** (`#2A2A2A`): Pressed / hover states, elevated controls.
- **Divider** (`#252525`): Hairline dividers between rows.
- **Canvas Light (rare)** (`#FFFFFF`): Light mode exists but is a secondary experience.

### Text
- **Text Primary** (`#FFFFFF`): Headlines, calorie numerals, primary body.
- **Text Secondary** (`#A1A1A1`): Meta text — "1 serving", "12:30 PM", macro labels.
- **Text Tertiary** (`#6E6E6E`): Placeholder, disabled, very low-emphasis labels.

### Macro Accents (the only color story)
- **Protein Blue** (`#4DA8FF`): Protein ring, chips, "P 42g" labels.
- **Carbs Amber** (`#FFB54C`): Carbs ring, chips, "C 48g" labels.
- **Fat Pink** (`#FF6E87`): Fat ring, chips, "F 14g" labels.
- Notes: These are intentionally desaturated pastels — not primary reds/blues/yellows. They should feel calm in the dark UI.

### Action & Semantic
- **Action White** (`#FFFFFF`): Primary CTA background ("Scan a meal", "Log now").
- **Action Black Text** (`#000000`): Primary CTA text — intentionally black on the white button.
- **Success Green** (`#42E17D`): Logged-confirmed checkmark, streak-met indicator.
- **Over-Budget Amber** (`#FFB54C`): "Over goal by 120 kcal" — soft amber, NOT red (no alarming feedback).
- **Destructive** (`#FF5A5A`): Delete action, "clear all" confirmations (only in destructive flows).
- **Info Indigo** (`#8B8DF5`): Info banners, AI-confidence indicators.

### AI Surface
- **AI Gradient Wash** (`linear-gradient(135deg, #1E1E1E 0%, #2A2A2A 100%)`): Subtle gradient used on AI-detected result cards to distinguish them from user-entered data.
- **AI Accent Line** (`linear-gradient(90deg, #4DA8FF, #8B8DF5, #FF6E87)`): 2pt animated gradient line that appears during AI processing.

### Light Mode (secondary)
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F5F5F5`)
- **Light Text** (`#0A0A0A`)
- Light mode preserves the same macro accents at identical hex values.

## 3. Typography Rules

### Font Family
- **Primary UI/Display**: `Inter` or system fallback `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text'`
- **Numerals Display**: Same family as UI but used at 32-72pt sizes — no separate display face
- **Tabular Numerals**: Inter's `tnum` OpenType feature or SF Pro's tabular variant enabled for ALL calorie/macro numbers (prevents layout jitter as numbers update)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Daily Total (Hero) | Inter | 72pt | 700 | 1.0 | -1.5pt | Today's calorie total on Home |
| Meal Card Calories | Inter | 40pt | 700 | 1.0 | -0.8pt | Big calorie number on each meal card |
| Streak Number | Inter | 48pt | 700 | 1.0 | -1pt | "14-day streak" hero |
| Screen Title (Large) | Inter | 28pt | 700 | 1.1 | -0.4pt | Today, Insights, History large-title |
| Section Title | Inter | 22pt | 700 | 1.2 | -0.3pt | "Recently logged", "This week" |
| Nav Title (Compact) | Inter | 17pt | 600 | 1.1 | -0.1pt | Default nav bar |
| Meal Name | Inter | 17pt | 600 | 1.3 | -0.15pt | "Grilled Chicken Bowl" |
| Body | Inter | 15pt | 400 | 1.4 | 0pt | Descriptions, instructional copy |
| Macro Chip | Inter | 12pt | 600 | 1.2 | 0pt | "P 42g" inline chip |
| Meta | Inter | 13pt | 500 | 1.3 | 0pt | Timestamps, serving info |
| Label (UPPER) | Inter | 11pt | 700 | 1.2 | 0.8pt | "PROTEIN", "CARBS", "FAT" under rings |
| Button (Primary) | Inter | 16pt | 600 | 1.0 | -0.1pt | "Scan a meal", "Save" |
| Button (Secondary) | Inter | 14pt | 600 | 1.0 | 0pt | "Edit", "Delete", text links |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.2pt | Tab bar labels |
| Timestamp Tabular | Inter | 13pt | 500 | 1.0 | 0pt | Feature-settings `tnum` enabled |

### Principles
- **Tabular numerals everywhere** — calorie totals, macro counts, weights, timestamps. Prevents "5 → 50" shifting layouts.
- **No light weights** — minimum weight is 400 (regular). 300 and lower feel anemic against the dark canvas.
- **Negative tracking on large numerals** — calorie totals get -0.8 to -1.5pt to feel dense and confident.
- **Dynamic Type support** — all body text scales across accessibility sizes. Display numerals (48-72pt) cap at 140% to preserve hero layouts.
- **Tracking for labels** — the 11pt uppercase macro labels get +0.8pt tracking to feel like machined metadata, not shouting.

## 4. Component Stylings

### Buttons

**Primary CTA (White Pill — "Scan a meal")**
- Background: `#FFFFFF`
- Text: `#000000` (intentional — black on white)
- Padding: 16pt vertical, 28pt horizontal
- Corner radius: 28pt (pill)
- Font: Inter, 16pt, weight 600
- Pressed: background dims to `#E8E8E8`, scale 0.97
- Disabled: `#2A2A2A` background, `#6E6E6E` text

**Secondary CTA (Transparent with Border)**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `#2A2A2A`
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 24pt
- Font: Inter, 15pt, weight 600

**Capture FAB (Bottom Center)**
- Size: 68pt circular diameter
- Background: `#FFFFFF`
- Inner ring: 2pt `#0A0A0A` stroke inset 4pt from outer edge
- Center icon: camera glyph or none (pure white circle)
- Shadow: `rgba(255,255,255,0.15) 0 0 40px` (subtle white glow on dark canvas)
- Pressed: scale 0.92 with medium haptic
- Position: centered in the tab bar slot, raised 10pt above the bar

**Text Button (Edit / Delete / See More)**
- Background: transparent
- Text: `#FFFFFF` (primary) or `#A1A1A1` (secondary) or `#FF5A5A` (destructive)
- Font: Inter, 14pt, weight 600
- No underline, no background, 44pt hit area

**Chip (Filter / Time Range)**
- Background: `#151515` (inactive) / `#FFFFFF` (active)
- Text: `#FFFFFF` (inactive) / `#000000` (active)
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 16pt
- Font: Inter, 13pt, weight 600

### Cards & Containers

**Meal Card (Primary — Photo + Stats)**
- Background: `#151515`
- Corner radius: 20pt
- Padding: 0 (photo bleeds to edges; text has 16pt internal padding)
- Structure:
  - Top: 4:3 food photo with the top two corners rounded (radius 20pt on top corners only)
  - Overlay: subtle gradient from transparent top to `rgba(0,0,0,0.4)` bottom-1/3 for legibility
  - Text block (16pt padding): meal name 17pt w600 white, time + serving 13pt w500 `#A1A1A1`, bottom row: 3 macro chips + large right-aligned calorie number (40pt w700)
- No shadow on dark canvas — elevation via surface color contrast (`#151515` card on `#0A0A0A` canvas)
- Swipe-to-delete reveals `#FF5A5A` delete pill at trailing edge

**Today Hero Card (Daily Total)**
- Background: `#151515`
- Corner radius: 24pt
- Padding: 24pt
- Structure:
  - Label "TODAY" 11pt uppercase `#A1A1A1` with 0.8pt tracking
  - Huge number — remaining calories — 72pt w700 tabular numerals
  - Subtitle "kcal remaining of 2,200" 13pt w500 `#A1A1A1`
  - Progress bar at 6pt height, rounded, with a white fill proportional to intake
  - Row of three macro rings below (80pt each) with consumption / goal displayed inside

**Macro Ring**
- Outer size: 80pt (hero) / 64pt (compact)
- Track stroke: 6pt, color `#2A2A2A`
- Fill stroke: 6pt, color per macro (`#4DA8FF` / `#FFB54C` / `#FF6E87`)
- Cap: rounded
- Inner content:
  - Value (Inter 18pt w700 tabular, white)
  - Unit "g" in 12pt w500 `#A1A1A1`
  - Label below ring in 10pt w700 UPPERCASE `#A1A1A1`, 0.8pt tracking
- Animation: fill animates from 0° to current % over 600ms ease-out

**AI Detection Result Card**
- Background: linear gradient 135deg from `#1E1E1E` to `#2A2A2A`
- Corner radius: 20pt
- Top: AI confidence badge (SF Symbol `sparkles` + "AI detected" in 11pt uppercase `#8B8DF5`)
- Animated AI accent line (2pt, gradient `#4DA8FF → #8B8DF5 → #FF6E87`) at top edge during processing
- Detected items listed with per-item confidence percentages
- Editable quantity steppers inline
- CTA row at bottom: "Save to today" (white pill) + "Reject" (text link)

**Streak Card**
- Background: `#151515` with a subtle inner gradient (top `#181818` → bottom `#151515`)
- Corner radius: 20pt
- Padding: 20pt
- Large streak number — Inter 48pt w700 white tabular
- Flame icon (SF Symbol `flame.fill`) rendered in amber `#FFB54C`, 32pt, to the left of the number
- Subtitle 13pt w500 `#A1A1A1`

### Navigation

**Bottom Tab Bar (with centered Capture FAB)**
- Height: 76pt + safe area
- Background: `rgba(10,10,10,0.92)` with `.regularMaterial` blur
- Border top: 0.5pt `#252525`
- Five slots — Home, History, [Capture FAB], Insights, Profile
- Inactive icon: `#6E6E6E`
- Active icon: `#FFFFFF`
- Label: Inter 10pt weight 600, always visible
- Icon size: 22pt SF Symbols

**Top Nav (Large Title)**
- Height: 52pt + safe area (large title collapsed is 44pt)
- Background: `#0A0A0A` opaque (no blur on top nav)
- Large title "Today" 28pt w700 white, collapses to 17pt w600 inline on scroll
- Trailing action: profile avatar (28pt circular) with 1pt `#2A2A2A` border

**Bottom Sheet (Meal Detail / Settings)**
- Corner radius: 20pt top only
- Background: `#151515`
- Grabber: 36pt wide × 4pt tall pill, color `#3A3A3A`, 10pt from top
- Shadow: `rgba(0,0,0,0.4) 0 -8px 24px`
- Snap points: compact / medium / large

### Camera Capture Flow

**Viewfinder**
- Full-bleed camera preview
- 4-corner reticle (white, 20pt per corner with 2pt stroke) centered, animates in at 1.0 → 1.05 → 1.0 on capture prep
- Top bar: close (X) top-left 24pt, flash toggle top-right, subtle gradient scrim on top 80pt for legibility
- Bottom: capture button centered (68pt white circle), library shortcut left, barcode toggle right

**AI Processing Overlay**
- Viewfinder dims to 60% opacity
- Center label "Detecting..." + shimmering gradient bar (2pt tall, gradient `#4DA8FF → #8B8DF5 → #FF6E87`)
- 800-1200ms duration, then transitions to detection result card slide-up

### Input Fields

**Text Input**
- Background: `#1E1E1E`
- Text: `#FFFFFF`
- Placeholder: `#6E6E6E`
- Corner radius: 14pt
- Height: 52pt
- Padding: 16pt horizontal
- Focus: 2pt `#FFFFFF` border

**Quantity Stepper**
- Layout: `-` circle button (32pt) · big number (24pt w700 tabular) · `+` circle button (32pt)
- Button fill: `#1E1E1E`, icon in white
- Disabled: icon `#6E6E6E`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 68
- Card padding standard: 16pt (meal cards) / 24pt (hero)
- Section vertical rhythm: 12pt between meal cards, 28pt between major sections
- 16pt horizontal content margin

### Grid & Container
- Content width: device width with 16pt horizontal padding
- Meal cards span full width minus 16pt margins
- Single-column feed on iPhone
- iPad compact: 2-column masonry of meal cards

### Whitespace Philosophy
- **Dark canvas as breathing room**: The near-black background itself is the whitespace. Cards float on `#0A0A0A` with surface contrast providing visual separation without explicit gaps.
- **One hero per viewport**: The Today hero card is the single dominant element at the top of Home — everything below it supports it.
- **Meal photos bleed edge-to-edge within cards** — no photo margins inside the card, the food is the content.

### Border Radius Scale
- Subtle (8pt): Small badges, timestamp chips
- Input (14pt): Text inputs, small chips
- Card Thumbnail (16pt): Small meal photos in lists
- Card Standard (20pt): Meal cards, AI result cards
- Hero (24pt): Today total card, featured containers
- Pill (28pt): Primary CTAs
- Circle (50%): Profile avatars, macro rings, capture FAB

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, text blocks |
| Surface (Level 1) | `#151515` on `#0A0A0A` — surface contrast only, no shadow | Default meal cards, hero |
| Elevated (Level 2) | `#1E1E1E` + `rgba(0,0,0,0.4) 0 8px 24px` | AI result cards, sheets |
| Floating FAB (Level 3) | `#FFFFFF` + `rgba(255,255,255,0.15) 0 0 40px` subtle glow | Capture button (white on dark glows) |
| Sheet (Level 4) | `#151515` + `rgba(0,0,0,0.5) 0 -16px 48px` | Bottom sheets |
| Modal Overlay | `rgba(0,0,0,0.7)` + backdrop-blur 20 | Full-screen modals, AI processing dim |

**Shadow Philosophy**: Dark canvas swallows conventional shadows, so elevation is expressed through **surface color contrast** (each surface level steps 1 shade lighter than the canvas). The one exception is the Capture FAB — a white circle on a dark canvas, which gets a soft white glow shadow to feel like an active light source.

### Motion
- **Capture tap**: FAB scale 0.92, paired with `.impactOccurred(.medium)` haptic
- **AI processing**: shimmering gradient sweeps across the bar at 60fps for 800-1200ms, then result slides up with a spring (response 0.4, damping 0.7)
- **Ring fill**: 600ms ease-out animation from 0° to target percentage
- **Log confirmation**: white flash pulse on card edge (150ms), success haptic
- **Swipe to delete**: elastic pull with red pill reveal at -80pt threshold
- **Streak increment**: amber flame icon scales 1.0 → 1.15 → 1.0 (600ms spring) with success haptic

## 7. Do's and Don'ts

### Do
- Use near-black `#0A0A0A` as the canvas — darker than most iOS system dark mode
- Use tabular numerals on every number — calories, weights, timestamps
- Use white on black for primary CTAs (not a saturated color) — it's the bold, confident choice
- Keep the macro accent trio desaturated — soft blue / soft amber / soft pink, NOT primary red/blue/yellow
- Let food photography be the content — bleed it edge-to-edge within cards
- Use surface contrast (color steps) for elevation, not drop shadows
- Apply subtle white glow to the Capture FAB — it's the one active-light element
- Use SF Pro / Inter at weights 400-700 only
- Pair meaningful actions with haptics (capture: medium, confirm: success, delete: warning)
- Animate AI processing with the tri-color gradient line — it's the signature "AI is thinking" moment

### Don't
- Don't use pure black `#000000` for the canvas — `#0A0A0A` reads less harsh on OLED
- Don't use alarm red for over-budget states — use soft amber (`#FFB54C`)
- Don't add cartoon mascots, confetti celebrations, or cheerleading copy — this app is a tool
- Don't render big numbers in a rounded display face — sharp geometric sans feels like data
- Don't use drop shadows on card edges — dark canvas makes them invisible or muddy
- Don't saturate the macro colors to system red/blue/yellow — the pastel palette is the calm choice
- Don't use thin weights (100-300) — they disappear on dark canvas
- Don't introduce new accent colors — three macros + white CTA + green success + amber warn
- Don't over-animate — springs are subtle (damping 0.7+), no 1.5x overshoots

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero calorie numeral reduces to 56pt; rings to 64pt |
| iPhone 13/14/15 | 390pt | Standard layout — 72pt hero, 80pt rings |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav |
| iPhone 15/16 Pro Max | 430pt | Hero numeral bumps to 80pt, rings to 88pt |
| iPad | 768pt+ | Two-column meal feed, fixed hero sidebar, capture FAB moves to floating top-right |

### Dynamic Type
- Body / title: full accessibility scale
- Hero calorie (72pt): caps at 96pt max
- Ring numerals: cap at 140% (prevents ring overflow)
- Tab labels: cap at 14pt

### Orientation
- All primary screens: **portrait-locked**
- Camera capture: **portrait-locked**
- Insights charts: **rotate to landscape** for wider trend visualization

### Touch Targets
- Primary CTAs: 52pt tall
- Capture FAB: 68pt — impossible to miss
- Chip buttons: 32pt min height
- Ring taps: full 80pt ring is tappable

### Safe Area Handling
- Top: large-title nav + status bar honor safe area
- Bottom: capture FAB raised 10pt above tab bar; tab bar respects home indicator
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0A0A0A` (dark) / `#FFFFFF` (light rare)
- Surface 1: `#151515`
- Surface 2: `#1E1E1E`
- Divider: `#252525`
- Text primary: `#FFFFFF` (dark) / `#0A0A0A` (light)
- Text secondary: `#A1A1A1`
- Text tertiary: `#6E6E6E`
- Protein blue: `#4DA8FF`
- Carbs amber: `#FFB54C`
- Fat pink: `#FF6E87`
- Primary CTA: `#FFFFFF` bg, `#000000` text
- Success: `#42E17D`
- Warning / Over-budget: `#FFB54C`
- Destructive: `#FF5A5A`
- AI accent: `#8B8DF5`

### Example Component Prompts
- "Create a SwiftUI Cal AI meal card: background #151515, corner radius 20pt. Full-width 4:3 food photo at the top with the top corners rounded to 20pt and a subtle bottom gradient overlay (transparent → rgba(0,0,0,0.4)) for legibility. Below photo, 16pt padding: meal name 'Grilled Chicken Bowl' in Inter 17pt weight 600 white, timestamp '12:30 PM · 1 serving' in Inter 13pt weight 500 #A1A1A1. Bottom row: three macro chips (P in #4DA8FF, C in #FFB54C, F in #FF6E87 — each chip is a soft-tinted pill at 12pt weight 600) on the left, large calorie total '487' in Inter 40pt weight 700 tabular numerals right-aligned."
- "Build a macro progress ring: 80pt diameter, 6pt stroke width with rounded line caps. Track #2A2A2A, fill per macro (#4DA8FF protein / #FFB54C carbs / #FF6E87 fat). Center content: numeric value '42' in Inter 18pt weight 700 tabular white, unit 'g' in 12pt weight 500 #A1A1A1 next to it. Below the ring: label 'PROTEIN' in Inter 10pt weight 700 uppercase #A1A1A1 with 0.8pt tracking. Fill animates 0° to current % over 600ms ease-out."
- "Design the Today hero card: background #151515, corner radius 24pt, 24pt padding. 'TODAY' label in Inter 11pt weight 700 uppercase #A1A1A1 with 0.8pt tracking. Below: hero number '1,353' (remaining calories) in Inter 72pt weight 700 tabular white, letter-spacing -1.5pt. Subtitle 'kcal remaining of 2,200' in Inter 13pt weight 500 #A1A1A1. Horizontal progress bar at 6pt height, rounded, white fill at 38%. Row of three 80pt macro rings below."
- "Create the Capture FAB: 68pt circular, white fill with a 2pt #0A0A0A inner ring inset 4pt. Centered in the bottom tab bar slot, raised 10pt above the tab bar. Shadow: rgba(255,255,255,0.15) blur 40pt (subtle white glow). Tap: scale 0.92 with medium haptic, opens camera capture sheet."
- "Build an AI detection result card: background linear gradient 135deg from #1E1E1E to #2A2A2A, corner radius 20pt. Top-left badge: SF Symbol 'sparkles' + 'AI DETECTED' in Inter 11pt weight 700 uppercase #8B8DF5. 2pt animated gradient line (90deg #4DA8FF → #8B8DF5 → #FF6E87) at the top edge during processing. Editable item list with confidence percentages in #A1A1A1. Bottom row: 'Save to today' white pill CTA + 'Reject' text button."

### Iteration Guide
1. Canvas is ALWAYS `#0A0A0A` (near-black), NOT true black, NOT `#191514`
2. The macro trio (protein blue, carbs amber, fat pink) is the ENTIRE chromatic palette — no new colors
3. Primary CTA is WHITE with BLACK text — not a saturated brand color
4. Every number uses tabular numerals — no layout shift on updates
5. Big numerals are Inter (or SF Pro) weight 700 — NOT a rounded display face
6. Elevation is surface-color contrast, not drop shadows — the dark canvas swallows shadows
7. The Capture FAB is the one light source — it gets a white glow shadow
8. Over-budget / warning uses soft amber (`#FFB54C`) — never alarm red
9. AI moments get the tri-color gradient sweep (blue → indigo → pink) at 2pt thickness
10. Haptics: capture medium, confirm success, streak-met success, delete warning
