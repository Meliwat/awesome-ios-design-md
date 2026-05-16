# Design System Inspiration of Flo (iOS)

## 1. Visual Theme & Atmosphere

Flo's iOS app is a soft, reassuring light space built around one circular object: the **cycle wheel**. The canvas is pure white (`#FFFFFF`), and the dominant supporting surface is a barely-there blush (`#FFF0F3`) — the color of the inside of a shell. The whole app is rounded, low-contrast, and gentle: nothing has a hard edge, nothing shouts. This is deliberate — Flo handles intimate, sometimes anxious subject matter (cycles, fertility, symptoms), and the visual language is engineered to feel calm, private, and unclinical. It looks less like a medical chart and more like a friendly daily companion.

The accent system is led by **Flo Coral** (`#FF6B81`) — a warm, soft pink-red that carries the brand. It is used for the primary action, the active tab, the "today" marker on the wheel, and the prediction highlight. A secondary **lavender** (`#C5B3E6`) supports it for the fertile-window and ovulation phase, giving the cycle wheel a two-color phase story (coral for period, lavender for fertile). Beyond these two, everything is monochrome warm-grey: near-black ink (`#1A1A2E`) for text, a muted violet-grey (`#6E6A82`) for secondary copy. The restraint keeps the emotionally loaded content from feeling alarming — color is soft and used to *reassure*, never to warn.

Typography is Inter (Flo's product face is a rounded geometric sans in this family — Gilroy-like in feel), used warmly at 400 / 600 / 700. Headlines are friendly and medium-sized rather than bold and large — a 28pt greeting, a 22pt section header. The most prominent number is at the center of the cycle wheel: the cycle day or the countdown to the next period ("Period in 5 days"). Around the wheel, labels are small and supportive. The single most expressive moment is the wheel itself animating: when the day advances or the user logs, the phase ring smoothly rotates and the phase colors morph, so the user *sees* where they are in their cycle.

**Key Characteristics:**
- Pure-white canvas (`#FFFFFF`) with a barely-there blush surface (`#FFF0F3`)
- Soft, rounded, low-contrast — engineered to feel calm and unclinical
- Flo Coral (`#FF6B81`) as the lead accent — primary action, active tab, "today" marker
- Lavender (`#C5B3E6`) as the phase partner — fertile window / ovulation on the wheel
- The cycle wheel — a circular calendar with a colored phase ring (signature)
- Prediction card — a reassuring sentence ("Period in 5 days", "Ovulation today")
- Symptom-log chip grid — soft tappable chips for daily logging
- Soft, generously rounded cards (16-20pt radius) with gentle shadows
- Bottom tabs (Today / Calendar / Insights / Partner / More)
- Inter typeface, 400/600/700 — friendly and warm, never clinical

## 2. Color Palette & Roles

### Primary
- **Flo Coral** (`#FF6B81`): The lead accent — primary CTA, active tab, the "today" dot on the wheel, prediction highlight, period phase on the cycle ring.
- **Coral Pressed** (`#E85870`): Pressed/active state for coral controls.
- **Coral Soft** (`#FFD9DF`): Coral at low intensity — selected-chip background, subtle fills, period-day calendar cells.

### Phase Partner
- **Lavender** (`#C5B3E6`): The fertile-window / ovulation phase on the cycle wheel, fertile calendar cells, fertility insight accents.
- **Lavender Deep** (`#A893D6`): Pressed state for lavender controls; the ovulation-day marker.
- **Lavender Soft** (`#EDE6F7`): Lavender at low intensity — fertile-day cell fills.

### Canvas & Surfaces
- **Canvas White** (`#FFFFFF`): The primary canvas.
- **Surface Blush** (`#FFF0F3`): The barely-there blush card/section surface — the app's signature soft background.
- **Surface Raised** (`#FFFFFF`): White cards that lift off the blush with a soft shadow.
- **Surface Sunken** (`#FBE9ED`): Pressed state on blush surfaces, subtle input fills.
- **Divider** (`#F3DDE3`): Hairline separators — a soft pink-tinted grey, never harsh.

### Text
- **Text Primary** (`#1A1A2E`): Headlines, the cycle-day number, primary copy — a soft near-black with a violet cast (not pure black).
- **Text Secondary** (`#6E6A82`): Supporting copy, dates, descriptions, labels (muted violet-grey).
- **Text Tertiary** (`#A09CB0`): Very low-emphasis metadata, placeholder text, disabled labels.

### Semantic (Soft, Reassuring)
- **Predicted Period** (`#FF6B81`): Predicted period days on the calendar (coral, often outlined rather than filled to read as "forecast").
- **Logged Period** (`#E85870`): Confirmed/logged period days (the deeper coral — solid fill).
- **Fertile Window** (`#C5B3E6`): Fertile days (lavender fill).
- **Ovulation** (`#A893D6`): The single ovulation day (deep lavender, a ring or star marker).
- **Gentle Positive** (`#7DC9A8`): A soft sage for "Cycle logged" / positive confirmations — never a loud green.
- **Gentle Alert** (`#F2B36B`): A soft amber for "Late period" / attention — warm and calm, never red.

### Light Mode
Flo's iOS app is light-first by design — the soft white space is intrinsic to the calm, unclinical brand. A dark mode exists for OS parity (Canvas `#15131C`, Surface `#221E2C`, Text `#F2EFF7`, with Coral and Lavender preserved), but the light experience is primary and is what this spec describes.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` — Flo's product face is a rounded geometric sans in this family (Gilroy-like warmth)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`
- **Numerals**: tabular figures on the wheel center and calendar (`font-variant-numeric: tabular-nums`) so day numbers align

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Wheel Center (Hero) | Inter | 30pt | 700 | 1.1 | -0.4pt | "Day 14" or "Period in 5 days" at the wheel center |
| Greeting | Inter | 28pt | 700 | 1.2 | -0.3pt | "Good morning, Mia" on Today |
| Screen Title | Inter | 24pt | 700 | 1.25 | -0.3pt | "Calendar", "Insights" large title |
| Section Header | Inter | 22pt | 700 | 1.3 | -0.2pt | "My cycle", "Symptoms today" |
| Card Title | Inter | 18pt | 600 | 1.3 | -0.1pt | Insight / prediction card heading |
| Prediction Emphasis | Inter | 20pt | 700 | 1.25 | -0.2pt | The reassuring forecast line, coral |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Insight body, explanatory copy |
| List / Chip Label | Inter | 15pt | 600 | 1.3 | 0pt | Symptom chip text, list-row title |
| Subtitle / Date | Inter | 14pt | 400 | 1.4 | 0pt | "May 14 · Cycle day 14", supporting meta |
| Button (Primary) | Inter | 16pt | 600 | 1.0 | 0.1pt | "Log period", "Save" CTA text |
| Calendar Day | Inter | 15pt | 600 | 1.0 | 0pt | The number inside a calendar cell |
| Meta / Caption | Inter | 13pt | 500 | 1.3 | 0.1pt | "Updated today", small captions |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab labels |
| Label (UPPER) | Inter | 12pt | 700 | 1.2 | 0.8pt | "TODAY", "FERTILE WINDOW" tags |

### Principles
- **Friendly, not bold**: headlines are medium-large (24-30pt) at weight 700 — present but never aggressive; Flo reassures.
- **The wheel number leads**: the largest, most important figure is the cycle-day / countdown at the wheel center.
- **Tabular numerals on data**: calendar days and the wheel center use tabular figures so grids and the count never reflow.
- **Weight band 400 / 600 / 700**: regular for body, semibold for chips/cards, bold for headlines/wheel. No light, no black.
- **Color is reassurance**: coral and lavender carry phase meaning; text stays warm-grey — never color body text to alarm.

## 4. Component Stylings

### Cycle Wheel *(signature)*
- A circular calendar, diameter 240-280pt, centered at the top of Today
- Outer phase ring: segments colored by phase — Coral (`#FF6B81`) for period days, Coral Soft for the lead-in, Lavender (`#C5B3E6`) for the fertile window, Lavender Deep for ovulation, and `#F3DDE3` for ordinary days
- A `#FF6B81` "today" dot sits on the ring at the current cycle position with a subtle pulse
- Center stack: the big figure ("Day 14", 30pt w700) above a supporting line ("Follicular phase" or "Period in 5 days", 14pt `#6E6A82`)
- Below the center: a soft coral pill CTA — "Log period" / "Edit period dates"
- Interaction: dragging around the ring scrubs the predicted date; the phase morphs and the center updates live
- Reveal: on load, the phase ring sweeps in and the center figure fades up over ~700ms

### Buttons

**Primary CTA**
- Shape: Full pill, height 52pt
- Background: `#FF6B81`
- Text: `#FFFFFF`, Inter 16pt w600
- Padding: 16pt vertical, 32pt horizontal, corner radius 26pt
- Pressed: background `#E85870`, scale 0.97, soft haptic
- Used for: "Log period", "Save", "Continue"

**Soft Secondary Button**
- Background: `#FFD9DF` (coral soft)
- Text: `#E85870`, Inter 16pt w600
- Padding: 14pt vertical, 28pt horizontal, corner radius 26pt
- Pressed: background `#FBE9ED`
- Used for: "Add symptom", secondary card actions

**Text Button**
- Font: Inter 15pt w600, color `#FF6B81`
- No background, 44pt hit area
- Used for: "See all", "Skip", "Edit"

**Icon Actions (Add, Edit, Info, Share)**
- Size: 20pt glyph, 44pt hit area
- Default: `#6E6A82`; Pressed/active: `#FF6B81`

### Cards & Containers

**Prediction Card** *(signature)*
- Background: `#FFF0F3` (blush) or white with a soft shadow
- Corner radius: 20pt, padding 20pt
- Leading: a 28pt soft illustrative glyph (droplet for period, leaf for fertile)
- Title: a label "NEXT PERIOD" 12pt w700 uppercase `#6E6A82`
- Emphasis: "In 5 days" — Inter 20pt w700 `#FF6B81`
- Sub: "Expected May 19 · 87% confidence" — Inter 14pt `#6E6A82`
- Trailing chevron if it deep-links

**Symptom Log Chip Grid** *(signature)*
- A wrapping grid of soft chips for daily logging
- Chip: background `#FFF0F3`, text `#1A1A2E` Inter 15pt w600, optional leading 18pt glyph
- Padding: 10pt vertical, 16pt horizontal, corner radius 18pt
- Selected: background `#FFD9DF`, 1.5pt border `#FF6B81`, text `#E85870`
- Tap: scale 0.96 + soft haptic; selection animates a gentle fill
- Grid gap: 10pt; categories grouped under 12pt uppercase labels (Mood, Flow, Symptoms)

**Insight Card**
- Background: `#FFFFFF`, soft shadow `rgba(255,107,129,0.10) 0 6px 20px`, corner radius 16pt, padding 18pt
- Top: a small illustrative banner or 24pt glyph in coral/lavender
- Title: Inter 18pt w600 `#1A1A2E`; Body: Inter 16pt w400 `#6E6A82`
- Pressed: background `#FFF0F3`

**Calendar Month Grid**
- 7-column grid of day cells, each 40pt
- Day number: Inter 15pt w600
- Cell states:
  - Ordinary: no fill, text `#1A1A2E`
  - Predicted period: coral outline ring (1.5pt `#FF6B81`), text `#FF6B81`
  - Logged period: solid `#E85870` fill, white text
  - Fertile window: `#EDE6F7` fill, text `#1A1A2E`
  - Ovulation: `#A893D6` ring/star marker
  - Today: a `#FF6B81` dot under the number
- Selected day: scales up slightly with a soft shadow

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.94)` with `.regularMaterial` blur, hairline `#F3DDE3` top border
- Tabs: Today, Calendar, Insights, Partner, More
- Icon size: 24pt
- Active color: `#FF6B81`
- Inactive: `#A09CB0`
- Labels: Inter 11pt w600, always shown
- Active icon filled; inactive outlined

**Top Bar**
- Height: 44pt + safe area, transparent over white
- Left: greeting or screen title
- Right: a small streak/log indicator then a profile glyph 22pt `#6E6A82`
- No solid background — it floats; a soft `#F3DDE3` divider appears on scroll

**Calendar Screen**
- Month grid (states above), a month switcher, a legend chip row explaining the colors
- A floating coral "+" log button bottom-right (56pt circle, `#FF6B81`, soft shadow)

### Input Fields

**Search / Note Field**
- Background: `#FFF0F3`, height 46pt, corner radius 23pt
- Leading `magnifyingglass` 16pt `#6E6A82`
- Placeholder: "Search articles & symptoms", 15pt `#A09CB0`
- Focus: 1.5pt border `#FF6B81`, background → `#FFFFFF`

**Note / Journal Textarea**
- Background: `#FFF0F3`, corner radius 16pt, padding 14pt
- Text `#1A1A2E` 16pt; placeholder `#A09CB0`

### Distinctive Components

**Cycle Phase Wheel** — the defining surface (see above): a circular phase calendar that rotates and morphs colors as the cycle advances.

**Prediction Banner**
- A reassuring full-width strip at the top of Today under the wheel: a soft blush banner with the forecast in coral and a calm supporting sentence

**Phase Legend**
- A small horizontal row of dot+label pairs (Period · Fertile · Ovulation · Predicted) that decodes the wheel and calendar colors

**Daily Log Prompt**
- A gentle card "How are you feeling today?" opening the symptom chip grid — soft, optional, never nagging

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard horizontal margin: 20pt (generous — Flo is airy and calm)
- Section spacing: 28pt between major blocks

### Grid & Container
- Single column, full device width with 20pt horizontal margins
- The cycle wheel is centered with generous vertical air at the top of Today
- Symptom chips wrap fluidly; calendar is a 7-column grid
- Cards are full content width with rounded corners

### Whitespace Philosophy
- **Calm needs space**: generous 20pt margins, 1.55 body leading, 28pt section gaps — the airiness is part of the reassurance.
- **The wheel is the anchor**: it gets the most vertical room on Today; nothing crowds it.
- **Soft density in logging**: the symptom grid is tighter (10pt gaps) but the chips themselves are pill-soft so it never feels like a form.

### Border Radius Scale
- Soft (12pt): small inner elements, note fields
- Standard (16pt): insight cards, calendar selected-cell
- Comfortable (18-20pt): chips, prediction cards, blush sections
- Pill (26pt): primary/secondary buttons, search field
- Circle (50%): the cycle wheel, the floating log button, avatars, today dot

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; blush `#FFF0F3` fill | Sections, the canvas |
| Card (Level 1) | `rgba(255,107,129,0.10) 0 6px 20px` + white fill | Insight cards lifting off blush |
| Raised (Level 2) | `rgba(255,107,129,0.14) 0 10px 28px` | Prediction card, the focused/selected calendar day |
| Floating (Level 3) | `rgba(255,107,129,0.22) 0 8px 24px` | The floating coral log button |
| Sheet (Level 4) | `rgba(26,26,46,0.18) 0 -16px 48px` + white | Bottom sheets (log flow, date picker) |
| Tab Bar Material | `rgba(255,255,255,0.94)` + `.regularMaterial` | Floats above scrolling content |

**Shadow Philosophy**: Flo's shadows are soft, warm, and *tinted coral* — `rgba(255,107,129,...)` rather than neutral black — so cards feel like they belong to the blush world rather than casting harsh grey. Shadows are diffuse (20-28pt blur) and low opacity; depth is gentle. The cycle wheel itself sits flat on the canvas (it is a precise instrument, not a 3D object); only cards, the floating log button, and sheets lift.

### Motion
- **Wheel reveal**: phase ring sweeps in (~700ms ease-out) and the center figure fades + scales up from 0.9 in sync
- **Wheel scrub/advance**: dragging rotates the ring; phase colors crossfade-morph over 300ms as segments change
- **Chip select**: scale 0.96 → 1.0, fill animates from blush to coral-soft over 200ms, soft haptic
- **Card tap**: scale 0.98 → 1.0 over 200ms ease-out, soft haptic
- **Calendar day select**: cell scales up ~1.08 with a soft shadow bloom over 200ms
- **Screen transitions**: gentle fade + 8pt rise, 350ms ease-out (never a hard slide)
- **Log success**: a soft sage check and a single gentle haptic; the wheel updates with a smooth morph
- **Prediction update**: the forecast number cross-dissolves (no hard swap)

## 7. Do's and Don'ts

### Do
- Use a pure-white canvas with the barely-there blush (`#FFF0F3`) as the soft surface
- Lead with Flo Coral (`#FF6B81`) for the primary action, active tab, and "today" marker
- Use lavender (`#C5B3E6`) as the phase partner for the fertile window / ovulation
- Make the cycle wheel the anchor of Today — large, centered, with generous air
- Keep everything soft and rounded — 16-26pt radii, no hard edges
- Tint shadows coral (`rgba(255,107,129,...)`) so depth belongs to the blush world
- Phrase predictions reassuringly ("Period in 5 days") — calm, never alarming
- Use soft sage for positive confirmations and soft amber for gentle alerts — never red
- Use tabular figures on the wheel center and calendar so numbers never reflow

### Don't
- Don't use a clinical white-and-grey medical aesthetic — Flo is warm and unclinical
- Don't introduce loud or alarming colors — even alerts are soft amber, never red
- Don't make headlines aggressively large or heavy — friendly weight 700 at 24-30pt
- Don't use neutral black shadows — Flo's shadows are coral-tinted and soft
- Don't square the corners — the entire app is gently rounded
- Don't add a third phase color — coral and lavender carry the whole phase story
- Don't crowd the cycle wheel — it needs vertical breathing room as the anchor
- Don't animate harshly — phase morphs and fades are gentle, 200-350ms
- Don't color body text to draw alarm — color is reassurance, carried by coral/lavender

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cycle wheel 240pt; calendar cells 38pt |
| iPhone 13/14/15 | 390pt | Cycle wheel 260pt; standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the floating top bar |
| iPhone 15/16 Pro Max | 430pt | Cycle wheel 280pt; calendar cells 44pt |
| iPad | 768pt+ | Two-pane: wheel + predictions beside the calendar; wider chip grid |

### Dynamic Type
- Greetings, titles, body, card titles: full scale
- Wheel center figure: scales modestly but capped so it fits inside the wheel (~34pt max)
- Calendar day numbers, tab labels, captions: fixed (layout-sensitive at 11-15pt)
- Prediction emphasis: scales with body

### Orientation
- Today / Calendar / Insights / Partner: **portrait-locked** (the wheel + scroll layout is vertical)
- iPad: **rotates** — landscape uses the two-pane layout

### Touch Targets
- Primary CTA: 52pt tall — generous
- Symptom chips: full chip is the tap target (≥44pt effective)
- Calendar cells: 40pt cells with 44pt effective hit
- Floating log button: 56pt circle
- Tab icons: 24pt glyph, 44pt effective hit
- The cycle wheel ring is draggable; the center CTA is a separate 44pt+ target

### Safe Area Handling
- Top: floating bar respects the notch / Dynamic Island
- Bottom: tab bar and the floating log button float above the home indicator
- Sides: 20pt content insets
- The cycle wheel centers within the safe content area

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface blush: `#FFF0F3`
- Surface sunken (pressed): `#FBE9ED`
- Divider: `#F3DDE3`
- Text primary: `#1A1A2E`
- Text secondary: `#6E6A82`
- Flo Coral (accent): `#FF6B81`
- Coral pressed: `#E85870`
- Coral soft (selected): `#FFD9DF`
- Lavender (fertile): `#C5B3E6`
- Lavender deep (ovulation): `#A893D6`

### Example Component Prompts
- "Create a SwiftUI Flo cycle wheel: a 260pt circular phase calendar. Outer ring segments colored by phase — #FF6B81 for period days, #C5B3E6 for the fertile window, #A893D6 for ovulation, #F3DDE3 for ordinary days. A pulsing #FF6B81 'today' dot on the ring at the current position. Center stack: 'Day 14' in Inter 30pt weight 700 #1A1A2E above 'Follicular phase' in Inter 14pt #6E6A82. Below the center, a soft coral pill 'Log period'. On load, sweep the ring in and fade+scale the center up over 700ms ease-out."
- "Build a Flo prediction card: blush #FFF0F3 background, 20pt radius, 20pt padding. Leading a 28pt droplet glyph in #FF6B81. A label 'NEXT PERIOD' in Inter 12pt weight 700 uppercase #6E6A82, then 'In 5 days' in Inter 20pt weight 700 #FF6B81, then 'Expected May 19 · 87% confidence' in Inter 14pt #6E6A82. Soft coral-tinted shadow rgba(255,107,129,0.10) 0 6px 20px."
- "Design the symptom log chip grid: wrapping chips, gap 10pt, grouped under 12pt uppercase #6E6A82 labels. Each chip: blush #FFF0F3 background, text #1A1A2E Inter 15pt weight 600, optional 18pt leading glyph, padding 10pt vertical / 16pt horizontal, 18pt radius. Selected: background #FFD9DF, 1.5pt #FF6B81 border, text #E85870. Tap scales 0.96 with a soft haptic and animates the fill over 200ms."
- "Build the Flo calendar month grid: 7-column grid of 40pt day cells, day number Inter 15pt weight 600. States: ordinary (no fill, #1A1A2E text); predicted period (1.5pt #FF6B81 outline ring, #FF6B81 text); logged period (solid #E85870 fill, white text); fertile window (#EDE6F7 fill); ovulation (#A893D6 ring marker); today (a #FF6B81 dot under the number). Selected day scales 1.08 with a soft shadow."
- "Create the Flo primary CTA: full pill 52pt tall, #FF6B81 background, label in Inter 16pt weight 600 #FFFFFF, padding 16pt vertical / 32pt horizontal, 26pt radius. Pressed: background #E85870, scale 0.97, soft haptic."

### Iteration Guide
1. Canvas is pure white with a barely-there blush surface `#FFF0F3` — soft and unclinical
2. Flo Coral `#FF6B81` leads (action, active tab, today); lavender `#C5B3E6` is the phase partner
3. The cycle wheel is the anchor — large, centered, rotates and morphs colors as the cycle advances
4. Everything is gently rounded (16-26pt) — no hard edges anywhere
5. Shadows are soft and coral-tinted (`rgba(255,107,129,...)`), not neutral black
6. Predictions are reassuring sentences; alerts are soft amber, confirmations soft sage — never red
7. Headlines are friendly weight 700 at 24-30pt; the wheel center figure is the lead number (tabular)
8. Motion is gentle — phase morphs and fades at 200-350ms, nothing harsh
9. Flo is light-first — a dark mode exists for OS parity but light is the primary experience
