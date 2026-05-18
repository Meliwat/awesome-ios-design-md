# Design System Inspiration of Southwest (iOS)

## 1. Visual Theme & Atmosphere

Southwest's iOS app is built around a single product truth that no other airline shares: **there are no assigned seats**. You don't pick 14C. You get a *boarding position* — a letter group (A, B, or C) and a number (1–60) — and you board in that order. So the entire app is engineered around two moments that don't exist anywhere else in air travel: the **24-hour check-in countdown** (you must check in exactly 24 hours before departure to earn the lowest possible boarding number) and the **boarding-position reveal** (a giant `A23` readout that tells you exactly where you stand in line). Every other screen — booking, trips, Rapid Rewards — is in service of getting you to a good boarding position and a friendly, low-stress trip.

The brand is anchored by the **Heart**, Southwest's tri-color logo: a heart split into Southwest Blue (`#304CB2`), Bold Red (`#E51D23`), and Warm Yellow (`#F9B612`). These three colors are not decorative — they are a strict role system. **Blue is structure** (canvas, headers, primary chrome, the brand frame). **Yellow is action** (the primary CTA, the boarding-position number, the "do this now" signal — it is Southwest's "Spotify Green"). **Red is alerts only** (cancellations, irregular operations, destructive confirms) — it is never used for decoration, never for a happy CTA. This discipline is the signature of the system: a Southwest screen is mostly navy and white with one loud yellow element, and red appears only when something is wrong.

The app's personality is **warm, plainspoken, and a little playful** — "Bags fly free", "Wanna Get Away", "Transfarency", "LUV" (the NYSE ticker and the spirit). Copy is friendly and direct; the UI never tries to be clever or minimal for its own sake. Cards are generously rounded (16–20pt), the boarding position is enormous and unmissable, and the check-in countdown is treated like a launch clock. The light theme is white surfaces on a soft cool-gray wash with Southwest Blue chrome; the dark theme (documented here as the primary catalog rendering) drops to a deep navy `#0E1726` (a darkened Southwest Blue, never neutral black) with the exact same Yellow and Red so the brand reads identically in any light.

Typography is **Southwest Sans** (the airline's proprietary humanist sans, by Monotype) with **Inter** as the open-source fallback for implementation. Hierarchy is loud and confident: the boarding group can reach 88pt weight 900, screen titles are 32pt weight 900, and even body copy leans heavier (400–500) than a typical iOS app, matching the friendly, high-contrast tone. Numbers — boarding positions, fares, points balances, countdown clocks — are always the heaviest, largest things on their screen.

**Key Characteristics:**
- No seat map anywhere — the product is a **boarding position** (group A/B/C + number 1–60), not a seat
- **24-hour check-in countdown** — a launch-clock ring; check in exactly at T-24h for the best position
- Giant **boarding-position readout** (`A23`) — Warm Yellow group letter, white position number, the loudest element on screen
- **Heart tri-color role system** — Blue = structure, Yellow = action, Red = alerts only (never decoration)
- **Wanna Get Away fare ladder** — Wanna Get Away / WGA Plus / Anytime / Business Select, dollars + Rapid Rewards points
- **Rapid Rewards** points-forward loyalty — balance pill, A-List / A-List Preferred tiers, Companion Pass
- "Bags fly free", "Transfarency", "no change fees" — friendly, plainspoken value props surfaced as cards
- Deep navy canvas (`#0E1726`) dark / soft cool-gray + white light — never neutral black, always brand-tinted
- Generously rounded cards (16–20pt), heavy numeric type (up to 900 weight), confident high-contrast hierarchy
- Bottom tab bar: Home / Book / Trips / Rapid Rewards / Account — Yellow active tint, no pill

## 2. Color Palette & Roles

### Primary (Interactive)
- **Warm Yellow** (`#F9B612`): THE primary action color — primary CTA fill ("Check in", "Continue"), the boarding-position group letter, "do this now" signals. Southwest's brand-defining accent.
- **Warm Yellow Pressed** (`#E0A300`): Pressed/active state of the primary CTA.
- **Southwest Blue** (`#304CB2`): Structural brand color — secondary CTA fill, headers, the Heart frame, selected chips, brand surfaces.
- **Bright Blue** (`#2567E8`): Link / interactive text color — used for inline links and ghost-button borders; on dark it brightens for WCAG AA on the navy canvas.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#FFFFFF`): Primary light canvas — clean white.
- **Surface Wash** (`#F2F5FB`): App background behind cards, grouped-table backdrop — a soft cool gray-blue, never neutral.
- **Surface Card** (`#FFFFFF`): Card and sheet fills, elevated above the wash with a soft shadow.
- **Surface Pressed** (`#E7ECF6`): Pressed list rows.
- **Divider** (`#DCE2EE`): Hairline dividers between rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Navy Canvas** (`#0E1726`): Primary dark canvas — a darkened Southwest Blue, NOT neutral black.
- **Dark Surface 1** (`#16223A`): Card and sheet fills.
- **Dark Surface 2** (`#1F2E4A`): Raised tiles, segmented controls, secondary fills.
- **Dark Divider** (`#2A3A57`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#1A2233`): Primary text on light — a deep navy-ink, NOT pure black.
- **Text Secondary Light** (`#5A6783`): Metadata, captions, secondary labels.
- **Text Tertiary Light** (`#8C99B3`): Disabled, very low-emphasis, placeholder.
- **Text Primary Dark** (`#EAF0FA`): Primary text on the navy canvas.
- **Text Secondary Dark** (`#9DAAC4`): Metadata on dark.
- **Text Tertiary Dark** (`#67748F`): Disabled / inactive on dark.
- **Text On Yellow** (`#1A1A1A`): Text/icon color on the Warm Yellow CTA — near-black for AA contrast.
- **Text On Blue** (`#FFFFFF`): Text on Southwest Blue fills.

### Heart Tri-Color (Brand)
The three Heart colors are a strict role system — not a palette to mix freely.

| Heart Color | Hex | Role |
|-------------|-----|------|
| Southwest Blue | `#304CB2` | Structure — chrome, headers, secondary CTA, brand frame |
| Bold Red | `#E51D23` | Alerts only — cancellations, irregular ops, destructive confirm |
| Warm Yellow | `#F9B612` | Action — primary CTA, boarding position, "do this now" |

### Semantic

| Role | Light | Dark |
|------|-------|------|
| Success / On Time | `#1E8E4E` | `#1FAE5E` |
| Warning / Delay | `#F9B612` | `#F9B612` |
| Error / Cancelled | `#E51D23` | `#E51D23` |
| Info | `#304CB2` | `#2567E8` |
| Boarding Position (group) | `#F9B612` | `#F9B612` |
| Boarding Position (number) | `#1A2233` on yellow card / `#FFFFFF` on blue card | `#FFFFFF` |
| Points / Rapid Rewards | `#304CB2` | `#2567E8` |

### Semantic Notes
- **Red is sacred**: `#E51D23` appears ONLY for flight cancellations, irregular operations, schedule changes, and destructive confirmations ("Cancel reservation"). Never a CTA, never decoration, never a "sale" badge.
- **Yellow is the one CTA**: there is exactly one Warm Yellow element per screen in most cases — the primary action. If two yellows compete, the layout is wrong.
- **Blue carries everything else**: navigation, structure, secondary actions, the brand frame, points. It is the workhorse.
- **The boarding position owns its screen**: the `A23` readout is always the largest, heaviest, highest-contrast element wherever it appears.

## 3. Typography Rules

### Font Family
- **Brand Font**: `Southwest Sans` (proprietary humanist sans by Monotype for Southwest Airlines) — friendly, rounded terminals, excellent at large display sizes for fares and boarding positions.
- **Implementation Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — closest free humanist sans with a full 100–900 weight range and superb numerals; use for clones.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: tabular, lining figures everywhere numbers update (countdown clock, fares, points balance) so digits don't jitter.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Boarding Group | Southwest Sans | 88pt | 900 | 0.9 | -2pt | The `A` letter — Warm Yellow, the loudest glyph in the app |
| Boarding Number | Southwest Sans | 52pt | 800 | 1.0 | -1pt | The `23` — white, paired with the group letter |
| Screen Title | Southwest Sans | 32pt | 900 | 1.1 | -0.4pt | "Your trip", "Book a flight" |
| Airport Code | Southwest Sans | 26pt | 800 | 1.05 | -0.4pt | `DAL`, `MDW` — 3-letter codes on the flight strip |
| Section Header | Southwest Sans | 22pt | 800 | 1.2 | -0.2pt | "Wanna Get Away", "Choose your flight" |
| Subsection | Southwest Sans | 18pt | 700 | 1.3 | -0.1pt | "Rapid Rewards", "Trip details" |
| Body | Southwest Sans | 16pt | 400 | 1.5 | 0pt | Paragraph copy, value props |
| Body Emphasis | Southwest Sans | 16pt | 600 | 1.5 | 0pt | Inline emphasis, bolded value words |
| Card Title | Southwest Sans | 15pt | 600 | 1.35 | 0pt | "Board in group A, position 23" |
| Fare Price | Southwest Sans | 19pt | 800 | 1.1 | 0pt | "$98" on a fare card |
| Eyebrow / Label | Southwest Sans | 13pt | 700 | 1.2 | 0.6pt | "BOARDING POSITION" — uppercase, tracked |
| Meta | Southwest Sans | 12pt | 500 | 1.3 | 0pt | "WN 2418 · Mon, Jun 9" |
| Button | Southwest Sans | 16pt | 800 | 1.0 | 0.1pt | Primary/secondary button labels |
| Tab Label | Southwest Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Countdown Clock | Southwest Sans | 11pt (ring) / 34pt (full) | 800 | 1.0 | 0pt | Tabular figures, monospace-stable |

### Principles
- **Numbers are the heroes**: boarding positions, fares, points, and countdown clocks are always the heaviest and largest elements on their screen — heavier than any heading near them.
- **Friendly, not minimal**: body and label weights run heavier (400/500/600) than a typical iOS app to match the warm, confident brand voice. Avoid thin/light weights entirely.
- **Tabular figures always**: any number that can change (clock, balance, fare) uses tabular lining numerals so layout never shifts.
- **Uppercase tracked eyebrows**: small labels above big numbers ("BOARDING POSITION", "CONFIRMATION") are 13pt 700 uppercase with +0.6pt tracking.
- **Dynamic Type**: titles, body, card titles, fare prices scale; boarding group/number, tab labels, eyebrow labels, and the countdown clock stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Check in / Continue / Select flight)**
- Shape: rounded rectangle, 8pt corner radius, full-width on forms
- Background: `#F9B612` (Warm Yellow) — the one CTA color
- Text: `#1A1A1A` Southwest Sans 16pt weight 800
- Height: 52pt
- Pressed: background `#E0A300` + scale 0.98
- Disabled: `#F9B612` at 38% opacity, text at 50%

**Secondary Button (structural action)**
- Background: `#304CB2` (Southwest Blue)
- Text: `#FFFFFF` Southwest Sans 16pt weight 800
- Height: 52pt, 8pt corner radius
- Pressed: background darken 8% + scale 0.98

**Ghost / Outline Button**
- Background: transparent
- Border: 1.5pt `#2567E8` (Bright Blue)
- Text: `#2567E8` light / brighten on dark, Southwest Sans 14pt weight 700
- Height: 44pt, 8pt corner radius
- Pressed: background `#2567E8` at 8% opacity

**Destructive Button (Cancel reservation)**
- Background: transparent, text `#E51D23` Southwest Sans 16pt weight 700
- OR filled `#E51D23` / `#FFFFFF` for the final destructive confirm only
- This is the ONLY place red appears as an action

**Text Button (link)**
- Southwest Sans 14pt weight 700, color `#2567E8`
- No underline; pressed: 60% opacity

### Boarding Position Card (the signature atom)
- Container: 20pt corner radius, gradient `#304CB2 → #243C8E` (Southwest Blue), soft blue glow shadow
- Eyebrow: "BOARDING POSITION" — 13pt 700 uppercase, `rgba(255,255,255,0.78)`, +1.2pt tracking
- Group letter: 88pt weight 900, `#F9B612` (Warm Yellow), the dominant element
- Position number: 52pt weight 800, `#FFFFFF`, baseline-aligned with the group letter
- Caption: "Board in group A, position 23" — 13pt 500, `rgba(255,255,255,0.85)`
- Footer (after a 1pt 18%-white divider): two columns — "BOARDING STARTS / 4:25 PM" and "CONFIRMATION / K9F2Q2", labels 11pt 600 uppercase, values 16pt 700 white
- Optional yellow radial accent bleed in the top-right corner

### Check-In Countdown (the second signature atom)
- Container: card, 16pt corner radius, `Surface 1`, 1pt divider border
- Ring: 54pt circular progress, track `Divider`, progress `#F9B612` (Warm Yellow), 5pt stroke, rounded cap, rotates -90° start
- Center clock: tabular `HH:MM` 11pt 800 in `#F9B612`
- Texts: title "Check-in opens soon" 15pt 700; subtitle "Opens 24 hrs before departure to lock your spot" 12pt secondary
- Trailing action: small Warm Yellow pill "Notify" → becomes "Check in now" (full yellow CTA) when the ring completes
- When live: the ring fills, the clock turns into a live countdown, and the primary CTA becomes the loud full-width "Check in" button

### Flight Strip
- Container: card, 16pt corner radius, `Surface 1`
- Route row: origin code (26pt 800) — center mini-route (a 60×14 arrow line in Warm Yellow + "Nonstop · 2h 35m" 10pt uppercase) — destination code (26pt 800)
- Meta row: "WN 2418 · Mon, Jun 9" left, "Gate B14 · 4:55 PM" right, 12pt 500 secondary
- Status chip (optional): "On Time" success / "Delayed" yellow / "Cancelled" red `#E51D23`

### Wanna Get Away Fare Ladder
- Horizontal row of fare cards (Wanna Get Away / WGA Plus / Anytime / Business Select)
- Each card: 12pt corner radius, `Surface 2` fill, 1pt divider border
- Selected card: 2pt `#F9B612` border + Yellow-at-10% tint fill
- Fare name: 11pt 700 uppercase tracked, secondary
- Price: dollars 19pt 800 primary; "5,940 pts" 11pt 600 secondary on the line below
- Bottom-up presentation: cheapest (Wanna Get Away) leftmost

### Rapid Rewards Points Pill
- Pill, 500pt radius, `#304CB2` (Blue) fill, white text, leading 14pt star glyph filled `#F9B612`
- Tier variant: `#F9B612` fill, `#1A1A1A` text, blue star — used for "A-List" / "A-List Preferred" badges
- Balance format: "54,820 pts" Southwest Sans 14pt 800 tabular

### Navigation

**Bottom Tab Bar**
- Height: 68pt total (50pt + safe area)
- Background: `#FFFFFF` light / `#0E1726` dark, 0.5pt top divider, blur on scroll
- Tabs (5): Home, Book, Trips, Rapid Rewards, Account
- Icon: 22pt; active fill, inactive stroke
- Active color: `#F9B612` (Warm Yellow) — NO pill/indicator; just icon+label tint change
- Inactive: `#8C99B3` light / `#67748F` dark
- Labels: Southwest Sans 10pt 600, always shown

**Top App Bar**
- Height: 44pt + safe area, transparent over canvas
- Title: 22–32pt 800/900 (large title on Home/Trips, compact on detail)
- Leading: back chevron (detail) or none
- Trailing: clock/help icon in a 34pt circular `Surface 2` button

**Segmented Control (One-way / Round trip)**
- Track: `Surface 2`, 10pt radius, 4pt inset
- Selected segment: `#FFFFFF` light / `#304CB2` dark thumb, label 14pt 700
- Unselected label: secondary 14pt 500

### Input Fields

**Search / Field**
- Height: 52pt
- Background: `#FFFFFF` light (1pt `#DCE2EE` border) / `#1F2E4A` dark
- Corner radius: 10pt
- Leading 18pt icon `#5A6783`
- Label floats above on focus; focus border 2pt `#304CB2`
- Placeholder: 16pt 400 tertiary

**Airport Picker Row**
- 56pt tall, leading 3-letter code in a tinted square, city name 16pt 600 + "DAL · Dallas Love Field" 12pt secondary
- Selected: `#304CB2` at 8% tint + leading checkmark

**Date / Calendar**
- Month grid; selected date `#304CB2` filled circle, white text
- Range: endpoints filled blue, in-between days `#304CB2` at 12% tint
- Today: 1.5pt `#F9B612` ring

### Distinctive Components

**Boarding Position Reveal**
- After check-in completes, the position animates in: the card scales 0.96 → 1.0, the group letter counts up A→ (or just pops), and a soft success haptic fires. This is the app's payoff moment.

**24-Hour Check-In Clock**
- The launch-clock metaphor: a ring that empties toward T-0, a tabular HH:MM:SS when within the final hour, and a state flip to the full Warm Yellow "Check in" CTA exactly at T-24h.

**Bags Fly Free / Transfarency Cards**
- Friendly value-prop cards: a leading illustrative icon, a bold one-line claim ("Bags fly free"), and a plain-language sub. Blue or neutral surface, never red. These reinforce the warm brand voice.

**Digital Boarding Pass**
- Full-screen pass: large `A23` position, QR/barcode, flight strip, "Confirmation K9F2Q2", brand Heart watermark. The position number remains the visual hero even here.

**Rapid Rewards Hub**
- Big tabular points balance (32pt 900), tier badge pill (A-List), Companion Pass progress bar, "Points + cash" toggle on fares.

**Flight Status Timeline**
- Vertical timeline: Scheduled → Boarding → Departed → Arrived; current node Warm Yellow filled, completed nodes Blue, future nodes divider-gray; a red node + banner only on cancellation/irregular ops.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt
- Card internal padding: 18–22pt
- Gap between stacked cards: 18pt
- Boarding card is visually dominant — it gets the most vertical real estate above the fold

### Grid & Container
- iPhone: single column, 20pt insets, full-width cards and CTAs
- iPad: content max-width 640pt centered; the boarding card may sit beside the flight strip in a 2-up on wide layouts
- The primary CTA is always full-width and pinned (or bottom of scroll) on action screens

### Whitespace Philosophy
- **The position breathes**: the boarding card is surrounded by generous space so the `A23` reads instantly — never crowded by chrome
- **One loud thing per screen**: lots of calm navy/white, one Warm Yellow focal point; red only on exceptions
- **Cards over lists**: information is grouped into rounded cards with clear internal hierarchy, not dense table rows
- **Friendly density**: comfortable, not compact — Southwest is not a power-user tool

### Border Radius Scale
- Square (0pt): full-bleed dividers, the barcode strip
- Subtle (4pt): tiny chips, accent ticks
- Standard (8pt): buttons, fields-adjacent elements
- Field (10pt): inputs, segmented controls
- Card (12pt): fare cards, list cards
- Comfortable (16pt): primary content cards, sheets
- Hero (20pt): the boarding-position card
- Pill (500pt): points pills, status chips, filter chips
- Circle (50%): avatars, the countdown ring, icon buttons

## 6. Depth & Elevation

Southwest uses soft, brand-tinted elevation — never harsh neutral shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, inline text, dividers |
| Card (Level 1) | `rgba(20,30,55,0.08) 0 2px 10px` light / 1pt `#2A3A57` border on dark | Standard cards, fare cards |
| Hero (Level 2) | `rgba(48,76,178,0.30) 0 14px 30px -16px` (blue-tinted glow) | Boarding-position card |
| Sheet (Level 3) | `rgba(14,23,38,0.18) 0 -6px 24px` | Bottom sheets (fare details, seat-free explainer) |
| Modal Overlay | `rgba(14,23,38,0.55)` scrim | Behind modals and sheets |

**Shadow Philosophy**: elevation is used to lift the boarding card above everything else — its shadow is a *blue glow*, not gray, reinforcing the Heart. Standard cards get a faint cool shadow on light and a 1pt divider border on dark (shadows read poorly on the navy canvas). Nothing else floats.

### Motion
- **Boarding-position reveal**: card scales `0.96 → 1.0` over 280ms `easeOut`, group letter cross-fades + 6pt rise, soft success haptic — the payoff moment
- **Check-in countdown tick**: clock digits flip with a 120ms vertical roll; the ring animates its stroke continuously; at T-0 the ring fills and the CTA cross-fades to the full Warm Yellow "Check in" over 240ms
- **Primary CTA press**: scale `1.0 → 0.98` 120ms + Warm Yellow → Pressed Yellow
- **Fare selection**: selected card border animates Yellow in over 150ms; price area subtle scale 1.0 → 1.03 → 1.0
- **Tab change**: instant icon/label tint to Warm Yellow; no pill slide (there is no pill)
- **Sheet present**: bottom sheet rises 320ms `easeOut` with scrim fade
- **Page push**: native iOS push, 300ms horizontal slide
- **Cancellation alert**: the red banner slides down 220ms and a warning haptic fires — the only place red animates
- **Haptics**: success on check-in/position reveal; light on CTA press and fare select; warning on cancellation

## 7. Do's and Don'ts

### Do
- Use Warm Yellow (`#F9B612`) as the single primary CTA and the boarding-position group letter
- Make the boarding position (`A23`) the largest, heaviest, highest-contrast element on its screen
- Treat the 24-hour check-in as a launch-clock countdown — a ring that empties toward T-0
- Use Southwest Blue (`#304CB2`) for structure, chrome, secondary actions, and points
- Reserve Bold Red (`#E51D23`) strictly for cancellations, irregular ops, and destructive confirms
- Use a deep navy canvas (`#0E1726`) for dark mode — a darkened Southwest Blue, never neutral black
- Use heavy numeric type (up to 900) with tabular figures for positions, fares, points, clocks
- Keep the voice warm and plainspoken — "Bags fly free", "Wanna Get Away", "no change fees"
- Present fares bottom-up: Wanna Get Away cheapest and leftmost
- Show Rapid Rewards as a points-forward pill with tier badges (A-List)
- Round content cards generously (16pt) and the boarding card most of all (20pt)

### Don't
- Don't show a seat map or seat selection — Southwest has no assigned seats; the product is a position
- Don't use Yellow for more than one thing per screen — if two yellows compete, the layout is wrong
- Don't use Red for CTAs, sales badges, or decoration — red means something is wrong
- Don't use neutral black (`#000000`) for the dark canvas or body text — use navy `#0E1726` / ink `#1A2233`
- Don't bury the boarding position in a list row — it owns its screen
- Don't use thin/light font weights — the brand is friendly and confident, weights run heavy
- Don't make the check-in moment quiet — it is the highest-stakes interaction in the app
- Don't add a tab-bar pill/indicator — active state is a Warm Yellow tint only
- Don't let numbers jitter — always tabular lining figures on anything that updates
- Don't make it feel like a power-user tool — comfortable density, generous whitespace, warm copy
- Don't desaturate the Heart colors in dark mode — Yellow and Red stay identical across themes

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Boarding group drops to 72pt; single-column; CTA full-width pinned |
| iPhone 13/14/15 | 390pt | Standard layout; boarding group 88pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; boarding card clears the island |
| iPhone 15/16 Pro Max | 430pt | Boarding group up to 96pt; more vertical breathing room |
| iPad (portrait) | 768pt | Content max 640pt centered; boarding card + flight strip stacked |
| iPad (landscape) | 1024pt+ | 2-up: boarding card beside flight strip; fare ladder in a 4-wide row |

### Dynamic Type
- Scales: screen titles, section headers, body, card titles, fare prices, value-prop copy
- Fixed (layout-critical): boarding group/number, countdown clock, tab labels, eyebrow labels, airport codes
- Code/barcode strip never scales

### Orientation
- iPhone: portrait-primary; boarding pass supports landscape (barcode rotates for scanners)
- iPad: full rotation; split layouts reflow boarding card / flight strip side-by-side

### Touch Targets
- Primary CTA: 52pt tall
- Tab icons: 22pt glyph, 48pt+ hit area
- Fare card: full card tappable, ≥ 64pt tall
- Icon buttons: 34pt visible, 44pt hit
- Date cells: 40pt circular hit

### Safe Area Handling
- Top: app bar and boarding card respect the Dynamic Island/notch
- Bottom: tab bar + home indicator respected; pinned CTA sits above the home indicator
- Boarding pass: full-bleed, barcode kept clear of safe-area insets for scannability

## 9. Agent Prompt Guide

### Quick Color Reference
- Primary CTA / boarding group (Warm Yellow): `#F9B612` (pressed `#E0A300`)
- Structure / secondary (Southwest Blue): `#304CB2`
- Link / bright blue: `#2567E8`
- Alerts only (Bold Red): `#E51D23`
- Canvas (light): `#FFFFFF` · wash `#F2F5FB`
- Canvas (dark): `#0E1726` · surface 1 `#16223A` · surface 2 `#1F2E4A`
- Divider: `#DCE2EE` light / `#2A3A57` dark
- Text primary: `#1A2233` light / `#EAF0FA` dark
- Text secondary: `#5A6783` light / `#9DAAC4` dark
- Text on Yellow: `#1A1A1A` · text on Blue: `#FFFFFF`
- Success: `#1E8E4E` light / `#1FAE5E` dark

### Example Component Prompts
- "Build the Southwest boarding-position card in SwiftUI: 20pt corner radius, gradient `#304CB2 → #243C8E`, soft blue glow shadow `rgba(48,76,178,0.3) 0 14px 30px -16px`. Eyebrow 'BOARDING POSITION' Southwest Sans 13pt w700 uppercase `rgba(255,255,255,0.78)` +1.2pt tracking. Group letter 'A' 88pt w900 `#F9B612`; position number '23' 52pt w800 white, baseline-aligned. Caption 'Board in group A, position 23' 13pt w500 `rgba(255,255,255,0.85)`. 1pt 18%-white divider, then two columns: 'BOARDING STARTS / 4:25 PM' and 'CONFIRMATION / K9F2Q2'."

- "Create the Southwest 24-hour check-in countdown: card 16pt radius `#16223A` with 1pt `#2A3A57` border. Leading 54pt ring — track `#2A3A57`, progress `#F9B612` 5pt stroke rounded cap rotated -90°, center tabular clock '23:41' 11pt w800 `#F9B612`. Title 'Check-in opens soon' 15pt w700, sub 'Opens 24 hrs before departure to lock your spot' 12pt secondary. Trailing Warm Yellow pill 'Notify'. When the ring completes, swap the pill for a full-width `#F9B612` 52pt 'Check in' CTA with `#1A1A1A` text."

- "Build the Southwest primary button: 52pt tall, 8pt corner radius, `#F9B612` fill, label Southwest Sans 16pt w800 `#1A1A1A`. Pressed: `#E0A300` + scale 0.98. There is exactly one of these per screen — it is the only Warm Yellow element."

- "Render the Wanna Get Away fare ladder: a horizontal row of cards (Wanna Get Away, WGA Plus, Anytime, Business Select) cheapest leftmost. Card 12pt radius `#1F2E4A`, 1pt `#2A3A57` border; selected card 2pt `#F9B612` border + Yellow-10% fill. Fare name 11pt w700 uppercase tracked secondary; price '$98' 19pt w800 primary; '5,940 pts' 11pt w600 secondary below."

- "Build the Southwest bottom tab bar: 5 tabs Home / Book / Trips / Rapid Rewards / Account, height 68pt, `#0E1726` dark bg, 0.5pt `#2A3A57` top divider. Active tint `#F9B612` (icon fill + label), inactive `#67748F`. NO pill or indicator — only a color tint change. Labels Southwest Sans 10pt w600 always visible."

- "Create the Southwest flight status timeline: vertical nodes Scheduled → Boarding → Departed → Arrived. Current node `#F9B612` filled, completed nodes `#304CB2`, future nodes `#2A3A57`. On cancellation only, render a node and top banner in `#E51D23` with a warning haptic."

### Iteration Guide
1. There is NO seat map — never add seat selection; the product is a boarding position (group A/B/C + number 1–60)
2. The boarding position (`A23`) is the loudest element on its screen — Warm Yellow group letter, white number, 88/52pt weight 900/800
3. The 24-hour check-in is a launch clock — a ring emptying toward T-0 that flips to the full Yellow CTA at T-24h
4. Warm Yellow `#F9B612` is the ONE primary CTA color — one per screen; Blue `#304CB2` is everything structural
5. Bold Red `#E51D23` is alerts ONLY — cancellations, irregular ops, destructive confirms; never decoration or a CTA
6. Dark canvas is navy `#0E1726` (a darkened Southwest Blue), never neutral black; light text is ink `#1A2233`
7. Numbers are heavy and tabular — positions, fares, points, clocks never jitter and outweigh nearby headings
8. Fares present bottom-up: Wanna Get Away cheapest and leftmost; show dollars + Rapid Rewards points together
9. Cards are generously rounded (16pt) and the boarding card most of all (20pt) with a blue-glow shadow
10. Voice is warm and plainspoken — "Bags fly free", "Wanna Get Away", "no change fees"; never minimal-for-its-own-sake
