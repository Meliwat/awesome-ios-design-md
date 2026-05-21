# Design System Inspiration of Noom (iOS)

## 1. Visual Theme & Atmosphere

Noom is a behavior-change app dressed as a friendly coach, and the design reflects that the entire time. The aesthetic is warm, optimistic, and conversational — closer to a supportive messaging app than a clinical calorie tracker. The light canvas is a soft near-white (`#FBFBFD`); the dark canvas is a comfortable `#121212`. On top of that calm field, Noom places two unmistakable signature elements: the **daily psychology lesson card** and the **food log driven by the green / yellow / red food-color system**. Everything else — the weight graph, the coach chat, the streak chips — orbits these two.

The **daily lesson card** is the emotional front door. It's a bold rounded card (20pt radius) filled with the brand's blue→teal gradient (`#2A5DF6 → #1FC29B`), carrying an uppercase eyebrow ("TODAY'S LESSON"), a punchy psychology-flavored title ("Why Willpower Is a Myth"), a thin white progress bar, and a white pill CTA ("Continue lesson →"). It is deliberately the brightest, most saturated thing on the screen because Noom's whole thesis is that *learning the psychology* — not white-knuckling a diet — is what changes behavior. The card is a daily ritual.

The **food-color system** is the single most recognizable Noom mechanic and it must be treated as sacred. Every food logged is classified Green, Yellow, or Red by caloric density: Green (`#34C759`) = lowest density, eat the most; Yellow (`#FFC531`) = moderate, eat in balance; Red (`#FF5A52`) = highest density, eat mindfully. These three colors appear as the leading dot on every food row, as a category pill, and as a stacked horizontal "today's food" bar that shows the green/yellow/red ratio at a glance. Users are trained to *read food by color* — so these hues are fixed, identical in light and dark, and never repurposed for anything else in the UI.

The brand palette is a confident **Noom Blue** (`#2A5DF6`) paired with a fresh **Noom Teal** (`#1FC29B`), grounded by a deep **Noom Navy** (`#0C1B4D`). Blue is the primary action color (buttons, active tab, links); teal is the positive/progress accent (weight-loss delta, goal line, secondary highlights). The coach feature gets its own warm **Coach Purple** (`#7B61FF`) so the human-coaching surface feels distinct from data surfaces.

Typography is **Poppins** — a geometric humanist sans with friendly circular bowls. It's approachable and slightly playful without being childish, which matches the encouraging coach voice. Numbers (current weight, calories) are heavy (700–800); body and coach copy are regular (400) and conversational. Buttons are fully pill-shaped, reinforcing the soft, friendly, non-clinical feel.

**Key Characteristics:**
- Warm, conversational, coach-led aesthetic — supportive, never clinical
- Soft near-white canvas (`#FBFBFD`) light / comfortable `#121212` dark
- Daily psychology lesson card — bold blue→teal gradient hero, the emotional front door
- Food-color system (Green `#34C759` / Yellow `#FFC531` / Red `#FF5A52`) — the signature mechanic, fixed across themes
- Stacked food-ratio bar — green/yellow/red proportions of the day at a glance
- Noom Blue (`#2A5DF6`) primary + Noom Teal (`#1FC29B`) progress accent + Noom Navy (`#0C1B4D`)
- Weight graph with a teal goal line and blue trend curve
- Coach chat surface in its own Coach Purple (`#7B61FF`) world
- Fully pill-shaped buttons (999pt radius) — soft and friendly
- Poppins geometric sans — heavy numerals, conversational body
- Encouraging streak/momentum copy ("Day 24 · You're building momentum")
- Minimal chrome — 5-tab bottom bar: Today / Learn / Log / Coach / Profile

## 2. Color Palette & Roles

### Primary (Interactive)
- **Noom Blue** (`#2A5DF6`): Primary buttons, active tab, links, selected states, weight trend line.
- **Noom Blue Pressed** (`#1E47C8`): Pressed state for primary actions.
- **Noom Teal** (`#1FC29B`): Progress/positive accent — weight-loss delta, goal line, success highlights, gradient end.
- **Noom Navy** (`#0C1B4D`): Deep brand anchor — onboarding backgrounds, dark hero text, splash.

### Canvas & Surfaces (Light — default)
- **Canvas** (`#FBFBFD`): Primary light background — soft near-white, not pure white.
- **Surface** (`#FFFFFF`): Cards, sheets, list backgrounds in light mode.
- **Surface Subtle** (`#F2F3F7`): Pressed rows, segmented-control track, chip fills.
- **Divider Light** (`#E6E7EC`): Hairline separators between food rows / sections.

### Canvas & Surfaces (Dark)
- **Dark Canvas** (`#121212`): Primary dark background.
- **Dark Surface 1** (`#1C1C1E`): Card backgrounds (weight card, food log).
- **Dark Surface 2** (`#262629`): Raised chips, ghost buttons, segmented track.
- **Dark Divider** (`#2E2E31`): Hairline separators on dark.

### Text
- **Text Primary Light** (`#1A1A1F`): Headlines, weight numbers on light.
- **Text Secondary Light** (`#6A6A70`): Metadata, body, captions on light.
- **Text Primary Dark** (`#F0F0F2`): Headlines, numbers on dark.
- **Text Secondary Dark** (`#9A9AA0`): Metadata, body on dark.
- **Text Tertiary** (`#6A6A70`): Axis labels, disabled (both modes).

### Food-Color System (semantic — FIXED across light & dark)

| Class | Color | Pill Fill (light tint) | Meaning |
|-------|-------|------------------------|---------|
| Green | `#34C759` | `rgba(52,199,89,0.16)` | Lowest calorie density — eat the most |
| Yellow | `#FFC531` | `rgba(255,197,49,0.16)` | Moderate density — eat in balance |
| Red | `#FF5A52` | `rgba(255,90,82,0.16)` | Highest density — eat mindfully |

### Coach Surface
- **Coach Purple** (`#7B61FF`): Coach avatar ring, coach message accents, "Message coach" CTA.
- **Coach Bubble (incoming)** (`#F2F0FF` light / `#221E3A` dark): Coach chat bubble background.
- **User Bubble** (`#2A5DF6`): User's outgoing chat bubble (Noom Blue).

### Semantic
- **Success** (`#34C759`): Goal reached, lesson complete (same as Food Green — intentional).
- **Error** (`#FF5A52`): Form errors, log-failure (same hue as Food Red — intentional).
- **Warning** (`#FFC531`): Over-budget warning (same as Food Yellow — intentional).
- **Streak Flame** (`#FF8A3D`): Streak/momentum badge accent.

## 3. Typography Rules

### Font Family
- **Primary**: `Poppins` (by Indian Type Foundry / Jonny Pinhorn, Google Fonts, SIL OFL) — a geometric sans with near-circular bowls; friendly and modern, matching Noom's encouraging coach voice without feeling childish.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Poppins 700–800 for primary stats (current weight, calories, day count). Use tabular figures on the weight readout so digits don't shift.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Poppins | 32pt | 800 | 1.15 | -0.4pt | "Today", "Progress" |
| Greeting | Poppins | 26pt | 700 | 1.2 | -0.3pt | "Good morning, Maya" |
| Hero Stat | Poppins | 24pt | 800 | 1.1 | -0.3pt | Current weight, calorie total |
| Section Title | Poppins | 22pt | 700 | 1.25 | -0.2pt | "Today's lesson", "Weight" |
| Card Title | Poppins | 18pt | 700 | 1.3 | -0.1pt | Lesson title, food card title |
| Body | Poppins | 16pt | 400 | 1.5 | 0pt | Lesson copy, coach messages |
| List Item | Poppins | 15pt | 600 | 1.4 | 0pt | Food row name |
| Meta | Poppins | 14pt | 400 | 1.4 | 0pt | "Day 24 · building momentum" |
| Tag / Eyebrow | Poppins | 12pt | 600 | 1.0 | 1.0pt | "TODAY'S LESSON" — uppercase, tracked |
| Button | Poppins | 16pt | 700 | 1.0 | 0.1pt | Primary CTA label |
| Pill / Category | Poppins | 11pt | 700 | 1.0 | 0.2pt | Green/Yellow/Red food pill |
| Axis Label | Poppins | 9pt | 700 | 1.0 | 0pt | Weight-graph x ticks |
| Tab Label | Poppins | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Conversational, not clinical**: copy reads like a supportive coach ("You're building momentum"), and the type weight stays light for prose so it feels like talking, not reading a chart.
- **Heavy stats, light prose**: weight and calories are 700–800; lessons and coach messages are 400.
- **Friendly geometry**: Poppins's circular bowls are the point — do not swap for a tight grotesque; the roundness signals warmth.
- **Eyebrows are uppercase + tracked**: the small label above a lesson/section title carries 1pt tracking.
- **Dynamic Type**: titles, greeting, hero stat, section, card title, body, list item scale; tag, pill, axis, tab labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Continue Lesson / Log Food)**
- Shape: Full pill, 999pt corner radius
- Background: `#2A5DF6` (Noom Blue)
- Text: `#FFFFFF`, Poppins 16pt weight 700
- Padding: 15pt vertical, 30pt horizontal
- Pressed: `#1E47C8` + scale 0.98
- Disabled: `#E6E7EC` fill / `#2E2E31` dark, `#9A9AA0` text

**Secondary / Outline Button**
- Background: transparent
- Text: `#2A5DF6`, Poppins 15pt weight 700
- Border: 1.5pt `#2A5DF6`
- Padding: 13pt vertical, 26pt horizontal
- Corner radius: 999pt (pill)
- Pressed: background `rgba(42,93,246,0.08)`

**Ghost / Tertiary Button (Skip for now)**
- Background: `#F2F3F7` light / `#262629` dark
- Text: `#1A1A1F` / `#F0F0F2`, Poppins 14pt weight 600
- No border
- Corner radius: 999pt
- Padding: 12pt vertical, 20pt horizontal

**White Pill (on the gradient lesson card)**
- Background: `#FFFFFF`
- Text: `#2A5DF6`, Poppins 13pt weight 700
- Corner radius: 999pt
- Padding: 9pt vertical, 16pt horizontal
- Trailing "→" arrow glyph

**Text Button (See all lessons)**
- Font: Poppins 14pt weight 700, color `#2A5DF6`
- No background, no underline
- Pressed: opacity 0.6

### Core Atoms

**Card**
- Background: `#FFFFFF` light / `#1C1C1E` dark
- Border: 1pt `#E6E7EC` / `#2E2E31`
- Corner radius: 18pt (data cards), 20pt (gradient lesson card)
- Padding: 16–18pt
- Shadow (light): `rgba(20,27,77,0.06) 0 6px 18px`; dark: none (border only)

**Food Row**
- Leading 10pt food-color dot (Green/Yellow/Red)
- Food name: Poppins 15pt weight 600
- Trailing category pill (tinted, 999pt radius, 11pt weight 700) + kcal value
- 9pt vertical padding, 1pt bottom divider

**Food-Color Pill**
- Pill, 999pt radius, Poppins 11pt weight 700, 3pt/9pt padding
- Green: text `#34C759` on `rgba(52,199,89,0.16)`
- Yellow: text `#FFC531` (light) / on `rgba(255,197,49,0.16)`
- Red: text `#FF5A52` on `rgba(255,90,82,0.16)`
- (On colored surfaces the pill may invert to solid fill + white text)

**Stacked Food-Ratio Bar**
- Horizontal bar, 10pt tall, 5pt radius, no gaps between segments
- Segments fill Green → Yellow → Red left to right, widths = proportion of the day's intake
- Sits above the food list as the day's at-a-glance summary

### Navigation

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `#FFFFFF` / `rgba(18,18,18,0.94)` dark with `backdrop-filter: blur(20px)`, 0.5pt top border
- Tabs (5): Today · Learn · Log · Coach · Profile
- Icon size: 22pt
- Active: `#2A5DF6` (icon fills solid)
- Inactive: `#9A9AA0` / `#6A6A70`
- Label: Poppins 10pt weight 600, always shown
- No tint pill — color/fill change only
- The center "Log" tab may render as a slightly raised circular `#2A5DF6` button in some flows

**Top Header (no nav bar)**
- The "Today" screen has no UINavigationBar — a greeting block (greeting 26pt weight 700 + meta line) sits in the scroll content
- Detail screens (Lesson, Food detail) use a sheet/push with a leading back chevron and centered title

### Input Fields

**Search / Add Food Field**
- Height: 48pt
- Background: `#F2F3F7` / `#262629` dark
- Corner radius: 999pt (pill) — search is a pill, matching the button language
- Leading magnifier 18pt `#9A9AA0`
- Placeholder: "Search foods or scan" Poppins 15pt `#9A9AA0`
- Focus: 1.5pt `#2A5DF6` border

**Coach Chat Composer**
- Pill field, `#F2F3F7` / `#262629`, 999pt radius
- Trailing circular send button `#7B61FF` (Coach Purple) when text present
- Placeholder: "Message your coach"

**Segmented Control (Day / Week / Month)**
- Container: `#F2F3F7` / `#262629`, 999pt radius, 4pt inset
- Selected segment: `#FFFFFF` (light) / `#3A3A3E` (dark) pill, text `#2A5DF6`
- Slide animation 220ms ease-out

**Stepper / Weight Logger**
- Large Poppins 28pt weight 800 numeral
- − / + circular buttons `#F2F3F7`, blue glyph
- Unit toggle (lb / kg) as a small pill segment

### Distinctive Components

**Daily Psychology Lesson Card — signature**
- Rounded card, 20pt radius, fill = linear gradient `#2A5DF6 → #1FC29B` (~135°)
- Decorative translucent circle bleeding off the top-right corner (`rgba(255,255,255,0.10)`)
- Uppercase eyebrow "TODAY'S LESSON" 11–12pt weight 700, white at 85% opacity, 1pt tracking (may include "· 4 MIN")
- Title: Poppins 18pt weight 700 white, max ~80% width, 1.3 line-height
- Progress: thin 6pt white bar on `rgba(255,255,255,0.25)` track + "2 of 5" label 11pt weight 700 white
- CTA: white pill, blue text, "Continue lesson →"

**Food Log (with food-color system) — signature**
- Header row: "Today's food" 14pt weight 700 + "1,240 / 1,500 kcal" 12pt weight 600 secondary
- Stacked food-ratio bar (green/yellow/red)
- List of food rows, each: color dot + name + category pill + kcal

**Weight Graph Card**
- Header: "Weight" 14pt weight 700 + range "Last 30 days" 11pt weight 600 tertiary
- Hero readout: "164.2 lb" Poppins 24pt weight 800 + teal delta "▼ 6.8 lb" 13pt weight 600
- Chart: blue trend curve 2.5pt over a `#2A5DF6` 30%→2% gradient fill; dashed teal goal line `#1FC29B`; current-point marker dot ringed in surface color
- X-axis: 3 date ticks 9pt weight 700 tertiary

**Coach Chat**
- Coach avatar with a Coach Purple ring; incoming bubbles `#F2F0FF` / `#221E3A`, 18pt radius
- User bubbles `#2A5DF6`, white text, 18pt radius, trailing-aligned
- Composer pill with purple send button

**Streak / Momentum Chip**
- Pill, `#F2F3F7` / `#262629`, flame glyph in `#FF8A3D`
- "Day 24" Poppins 13pt weight 700

**Goal Progress Ring (optional weight goal)**
- Track `#E6E7EC` / `#2E2E31`; progress arc `#1FC29B` (teal = progress)
- Center: percent or remaining lbs in Poppins 20pt weight 800

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 18, 20, 24, 32, 40
- Screen horizontal inset: 18pt
- Card internal padding: 16–18pt
- Vertical gap between cards/sections: 14–16pt
- Food-row vertical padding: 9pt

### Grid & Container
- iPhone: single column, 18pt horizontal insets
- The "Today" feed is a vertical stack of full-width cards (lesson → weight → food log)
- iPad: content max-width 640pt centered
- The stacked food-ratio bar always spans the full card content width

### Whitespace Philosophy
- **Friendly and breathable**: generous 14–16pt gaps so the screen never feels like a dense tracker
- **The lesson card leads**: it's the most saturated, top-most element — nothing competes with it visually
- **Food rows are scannable**: consistent dot → name → pill → kcal rhythm; the color dot does the heavy lifting
- **Encouraging headroom**: the greeting + momentum line sit high with air, setting a positive tone before any data

### Border Radius Scale
- Square (0pt): stacked food-ratio bar ends are squared internally (the bar itself is 5pt)
- Soft (8pt): small inner chips, chart bar tops
- Standard (12pt): food-color tiles, small cards
- Comfortable (16–18pt): data cards, coach bubbles, search containers' larger variants
- Lesson (20pt): the daily lesson gradient card, sheets
- Large (28pt): bottom-sheet top corners
- Pill (999pt): ALL buttons, category pills, search field, segmented control, streak chip
- Circle (50%): avatars, goal ring, send button, raised "Log" tab

## 6. Depth & Elevation

Noom uses soft, friendly elevation in light mode and flat surfaces in dark. The lesson card carries the most visual weight via color, not shadow.

| Level | Treatment (Light) | Use |
|-------|-------------------|-----|
| Flat (Level 0) | No shadow | Background feed, dividers, text |
| Card (Level 1) | `rgba(20,27,77,0.06) 0 6px 18px` | Data cards (weight, food log) |
| Lesson (Level 1.5) | `rgba(42,93,246,0.22) 0 10px 26px` | Daily lesson gradient card (colored glow) |
| Sheet (Level 2) | `rgba(20,27,77,0.12) 0 -8px 28px` | Bottom sheets (add food, scan) |
| Overlay | `rgba(12,27,77,0.45)` | Dim behind modals and sheets |

**Shadow Philosophy**: shadows are soft and brand-tinted toward Noom Navy (`rgba(20,27,77,…)`) rather than pure black, so cards feel warm. The lesson card uses a colored blue glow so it appears to *radiate* — reinforcing its role as the energetic focal point. On **dark mode**, drop shadows almost entirely and rely on the `#2E2E31` border + the `#1C1C1E` surface lift; the lesson card keeps a faint blue glow.

### Motion
- **Lesson card entrance**: scales 0.96 → 1.0 with fade over 320ms ease-out; the decorative circle drifts in subtly
- **Food row add**: new row slides down + fades in 250ms ease-out; the stacked ratio bar re-animates its segment widths over 400ms ease-out
- **Weight graph draw**: trend curve reveals left→right via stroke-dashoffset over 700ms ease-out; the dashed goal line fades in after 200ms; the marker dot pops with a 150ms spring
- **Tab switch**: cross-fade 200ms; active icon fills + tint tweens 150ms
- **Segmented control**: selected pill slides 220ms ease-out
- **Coach message**: incoming bubble fades + rises 250ms; a typing-indicator (3 dots) pulses while the coach "writes"
- **Lesson progress**: the white progress bar animates its width on completion with a 400ms ease-out + soft haptic
- **Sheet present**: slides up 300ms ease-out; backdrop fades 200ms
- **Reduce Motion**: replace draw/scale with 200ms cross-fades; ratio bar and graph render at final state

## 7. Do's and Don'ts

### Do
- Treat the food-color system (Green `#34C759` / Yellow `#FFC531` / Red `#FF5A52`) as sacred — fixed hues, identical in light & dark
- Make the daily psychology lesson card the front door — bold blue→teal gradient, most saturated element on screen
- Use Noom Blue `#2A5DF6` for all primary actions, active tab, and links
- Use Noom Teal `#1FC29B` for progress/positive signals — weight-loss delta, goal line, success
- Show the stacked green/yellow/red ratio bar so the day's food balance reads at a glance
- Make every button a full pill (999pt) — soft, friendly, non-clinical
- Keep copy conversational and encouraging ("Day 24 · You're building momentum")
- Use Poppins's circular geometry — it signals warmth; heavy numerals, light prose
- Give the lesson card a colored blue glow so it radiates as the focal point
- Give the coach surface its own Coach Purple `#7B61FF` world
- Use a soft near-white `#FBFBFD` light canvas, comfortable `#121212` dark — never harsh pure white/black

### Don't
- Don't recolor or reuse the Green/Yellow/Red food hues for anything else — users read food by color; consistency is the mechanic
- Don't make the lesson card subtle or gray — it must be the energetic, saturated focal point
- Don't use a tight geometric grotesque or a serif — Poppins's friendly roundness is core to the brand
- Don't use sharp-cornered or right-angled buttons — Noom buttons are always full pills
- Don't write clinical/medical copy ("caloric deficit achieved") — keep it like a supportive coach
- Don't add a second primary color competing with Noom Blue for actions
- Don't use pure black drop shadows — tint toward Noom Navy `rgba(20,27,77,…)`
- Don't bold body/coach copy by default — prose is 400; only stats are heavy
- Don't bury the daily lesson below the fold — it's the top-most card every day
- Don't tint the coach chat with Noom Blue throughout — Coach Purple distinguishes the human surface
- Don't use Material-style tab tint pills — active state is a color/fill change only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Lesson title 16pt; weight graph 80pt tall; 14pt insets |
| iPhone 13/14/15 | 390pt | Standard layout (reference) |
| iPhone 15/16 Pro | 393pt | Dynamic Island clearance in status zone |
| iPhone 15/16 Pro Max | 430pt | Lesson title 20pt; weight graph 104pt; larger hero stat |
| iPad (portrait) | 768pt | Content max-width 640pt centered; lesson card full-width within |
| iPad (landscape) | 1024pt+ | Two-column: lesson + weight on left, food log on right, max 900pt |

### Dynamic Type
- Scales: screen title, greeting, hero stat, section, card title, body, list item
- Fixed: tag/eyebrow, food-color pill, axis labels, tab labels
- Lesson title caps at +25% to keep the card from overflowing

### Orientation
- Phone: portrait-primary on the "Today" feed
- iPad: supports landscape with the two-column split

### Touch Targets
- Tab bar icons: 22pt glyph, 44pt hit
- Pill buttons: 48pt+ tall
- Food row: full-row tap, 44pt min height
- Segmented control segments: 36pt tall
- Coach send button: 40pt circle

### Safe Area Handling
- Top: greeting/header respects safe area + Dynamic Island
- Bottom: 68pt tab bar + home indicator; coach composer floats above the keyboard
- The lesson card never extends under the status bar — it sits inside the scroll content with top inset
- Sheets respect bottom safe area

## 9. Agent Prompt Guide

### Quick Color Reference
- Noom Blue (primary): `#2A5DF6` / pressed `#1E47C8`
- Noom Teal (progress): `#1FC29B`
- Noom Navy (anchor): `#0C1B4D`
- Canvas: `#FBFBFD` light / `#121212` dark
- Surface: `#FFFFFF` / `#1C1C1E`
- Divider: `#E6E7EC` / `#2E2E31`
- Food Green: `#34C759` · Food Yellow: `#FFC531` · Food Red: `#FF5A52` (FIXED both modes)
- Coach Purple: `#7B61FF`
- Text primary: `#1A1A1F` / `#F0F0F2`; secondary `#6A6A70` / `#9A9AA0`

### Example Component Prompts
- "Build the Noom daily lesson card in SwiftUI: a 20pt-radius card filled with a linear gradient `#2A5DF6 → #1FC29B` at ~135°, with a decorative `rgba(255,255,255,0.10)` circle clipped off the top-right. Inside: an uppercase eyebrow 'TODAY'S LESSON · 4 MIN' in Poppins 11pt weight 700 white at 85% opacity with 1pt tracking; a title in Poppins 18pt weight 700 white at max 80% width; a 6pt white progress bar (40% filled) on a `rgba(255,255,255,0.25)` track with a '2 of 5' label; and a white pill CTA 'Continue lesson →' with `#2A5DF6` text. On appear, scale 0.96→1.0 + fade over 320ms ease-out, and add a colored glow shadow `rgba(42,93,246,0.22) 0 10px 26px`."

- "Create a Noom food log row: a 10pt leading dot in the food class color (Green `#34C759`, Yellow `#FFC531`, or Red `#FF5A52`), the food name in Poppins 15pt weight 600, a trailing tinted category pill (999pt radius, Poppins 11pt weight 700 — e.g. 'Green' as `#34C759` text on `rgba(52,199,89,0.16)`), and the kcal value in Poppins 13pt weight 700 secondary, right-aligned in a 52pt column. 9pt vertical padding, 1pt bottom divider `#E6E7EC`/`#2E2E31`."

- "Build the Noom stacked food-ratio bar: a 10pt-tall, 5pt-radius horizontal bar with three adjoining segments (no gaps) — Green `#34C759`, Yellow `#FFC531`, Red `#FF5A52` — widths proportional to the day's intake. Place it directly under the 'Today's food' header. When a food is added, re-animate the segment widths over 400ms ease-out."

- "Build the Noom weight graph card: header 'Weight' Poppins 14pt weight 700 + range 'Last 30 days' 11pt weight 600 tertiary; a hero readout '164.2 lb' Poppins 24pt weight 800 with a teal '▼ 6.8 lb' delta 13pt weight 600 `#1FC29B`; a chart with a 2.5pt `#2A5DF6` trend curve over a `#2A5DF6` 30%→2% vertical gradient fill, a dashed teal goal line `#1FC29B`, and a current-point marker dot ringed in the surface color. Reveal the curve left→right over 700ms ease-out."

- "Build the Noom primary button: a full pill (999pt radius), `#2A5DF6` fill, white Poppins 16pt weight 700 text, 15pt/30pt padding. Pressed: `#1E47C8` + scale 0.98. The outline variant is transparent with a 1.5pt `#2A5DF6` border and `#2A5DF6` text."

- "Render the Noom bottom tab bar: 68pt + safe area, 5 tabs — Today, Learn, Log, Coach, Profile — 22pt icons, Poppins 10pt weight 600 labels. Active tab `#2A5DF6` with the icon filled solid; inactive `#9A9AA0`. No tint pill — fill/color change only."

### Iteration Guide
1. The food-color system (Green `#34C759` / Yellow `#FFC531` / Red `#FF5A52`) is sacred — fixed hues, identical in both themes, never reused elsewhere
2. The daily psychology lesson card is the front door — bold `#2A5DF6 → #1FC29B` gradient, the most saturated element, top of the feed
3. Noom Blue `#2A5DF6` is the only primary action color; Noom Teal `#1FC29B` is for progress/positive
4. Every button is a full pill (999pt) — soft and friendly, never sharp-cornered
5. The stacked green/yellow/red ratio bar shows the day's food balance at a glance — above the food list
6. Poppins's circular geometry signals warmth — heavy numerals (700–800), conversational body (400)
7. Copy is encouraging and coach-like ("Day 24 · You're building momentum") — never clinical
8. The coach surface is its own Coach Purple `#7B61FF` world, distinct from blue data surfaces
9. Light-first: soft `#FBFBFD` canvas with Navy-tinted soft shadows; dark `#121212` is flat with borders
10. Minimal chrome — 5-tab bar, no opaque nav bar; greeting lives in the scroll feed
