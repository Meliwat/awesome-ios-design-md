# Design System Inspiration of United (iOS)

## 1. Visual Theme & Atmosphere

The United iOS app is a deep-navy, business-class-feeling travel tool that organizes itself around the *journey detail* — not just "where am I going" but the full anatomy of the flight: aircraft type, duration, terminals, gates, your Economy Plus seat, your MileagePlus Premier status, and the boarding pass that ties it together. Where some airline apps lead with a single status word, United leads with a richly detailed **flight-detail** screen: origin and destination airport codes large, city names underneath, an aircraft glyph and flight duration in the middle, and scheduled times with gates and terminals along the bottom. The app feels like the digital equivalent of a premium boarding lounge — informative, navy, and confident.

The signature flow is **flight detail → boarding pass**. The boarding pass is a tall card with a solid **United Blue** (`#002244`) header carrying the United globe mark and a gold "PREMIER · GROUP 1" band, a clean `Gate / Seat / Boards` data grid, a perforated dashed divider with circular notch cut-outs (the "tear here" affordance that makes it feel like a physical pass), and a scannable barcode block. Paired with the boarding pass is the **interactive seat map** — a cabin grid where Economy Plus rows are tinted translucent blue, taken seats are recessed dark, and the selected seat glows in electric Rhapsody Blue. This *flight detail + boarding pass + seat map* triad is the heart of the product.

The color system is a confident two-blue brand. **United Blue** (`#002244`) is the deep, trustworthy brand navy — headers, gradients, the boarding-pass banner. **Rhapsody Blue** (`#1414FF`) is the electric, almost-violet primary-action accent — it's the "Check In" button, the selected seat, the active tab. The two blues are deliberately different jobs: navy is *brand and surface*, Rhapsody is *action*. **Premier gold** (`#C2A14D`) is the MileagePlus elite-status accent — a Premier 1K / Gold / Silver badge, a "Group 1" priority band — and is never used as a general accent. Functional flight-status colors (on-time green, delayed amber, canceled red) are kept separate from the brand blues so status reads instantly.

Typography is United's clean humanist sans lineage (Open Sans is the closest free analog) — generous weight contrast (800 for airport codes and titles, 400 for body, 700 for eyebrows). Airport IATA codes, gate, seat, and times are rendered large and bold because they're scanned under travel pressure. The type system favors **structured legibility** — it should read like a refined airline information display.

**Key Characteristics:**
- Deep-navy, detail-rich travel tool — the flight-detail screen is the hero, not a single status word
- Signature flow: flight detail → boarding pass, with an interactive seat map
- Boarding pass with solid United Blue header + globe mark, gold Premier/Group band, perforated dashed divider with notch cut-outs, scannable barcode
- Interactive seat map — Economy Plus tinted, taken recessed, selected glowing Rhapsody Blue
- Two-blue brand: United Blue (`#002244`) for brand/surface, Rhapsody Blue (`#1414FF`) for action
- Premier gold (`#C2A14D`) for MileagePlus elite status only
- Functional status palette (green / amber / red) kept distinct from brand blues
- Home / Book / Flights / Trips / MileagePlus bottom tab structure
- Big bold IATA codes, gate, seat, times — sized for at-a-glance scanning
- Apple Wallet integration — boarding pass is a first-class live Wallet pass
- Refined, navy, business-class-feeling — informative without anxiety

## 2. Color Palette & Roles

### Primary (Interactive)
- **Rhapsody Blue** (`#1414FF`): Primary interactive color — "Check In", "Continue", selected seat, active tab. United's electric action accent.
- **Rhapsody Blue Pressed** (`#0E0EC8`): Pressed state.
- **Rhapsody Bright** (`#4A4AFF`): Active-state tint on dark surfaces (tab/icon) where `#1414FF` is too dark to read.
- **United Blue** (`#002244`): Brand anchor — flight-detail gradient, boarding-pass header, trust surfaces.
- **United Blue Mid** (`#1B3D6E`): Secondary CTA / interactive navy on dark surfaces.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F3F5FA`): Grouped list background, subtle cards.
- **Surface Pressed** (`#E5EAF3`): Pressed/selected rows.
- **Divider** (`#D4DCE9`): Hairline dividers.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0A1424`): Primary dark canvas — a deep navy, NOT neutral black, so the brand navy reads.
- **Dark Surface 1** (`#111E33`): Cards, boarding-pass body, grouped sections.
- **Dark Surface 2** (`#1A2A45`): Nested chips, seat cells, pressed rows.
- **Dark Divider** (`#243652`): Hairline dividers, perforation line.

### Text
- **Text Primary Light** (`#0C1F38`): Primary text — a navy-tinted near-black.
- **Text Secondary Light** (`#566884`): Metadata, labels.
- **Text Tertiary Light** (`#8493AC`): Disabled, low-emphasis.
- **Text Primary Dark** (`#E9EEF6`): Primary text on dark.
- **Text Secondary Dark** (`#97A6BE`): Metadata on dark.
- **Text Tertiary Dark** (`#5E7090`): Disabled on dark.

### Brand & Loyalty

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| United Blue | `#002244` | `#002244` |
| United Blue Mid | `#1B3D6E` | `#1B3D6E` |
| Rhapsody Blue | `#1414FF` | `#1414FF` |
| Rhapsody Bright | `#4A4AFF` | `#4A4AFF` |
| Premier Gold | `#C2A14D` | `#C2A14D` |
| Premier Gold Bright | `#D8B863` | `#D8B863` |
| Sky Tint (header) | `#E6EDF7` | `#16243C` |

### Flight Status (Functional — constant across modes)

| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| On Time | `#1E8E5A` | `#4FD18C` | "On Time", departed-on-time, arrived |
| Boarding | `#1414FF` | `#7A7AFF` | "Boarding", "Boarding Soon" |
| Delayed | `#C98A1E` | `#E0A943` | "Delayed", schedule change |
| Canceled / Error | `#D8434F` | `#F0808A` | "Canceled", IROP, error |
| Gate Changed | `#D9730D` | `#F0954E` | "Gate Changed" alert |

### Semantic
- **Error Red** (`#D8434F` / `#F0808A` dark): Inline error, canceled flight.
- **Success Green** (`#1E8E5A` / `#4FD18C` dark): Check-in / payment success.
- **Warning Amber** (`#C98A1E` / `#E0A943` dark): Delay, schedule change.
- **Selection Highlight** (`#1414FF1F`): 12% Rhapsody overlay on selected rows.

## 3. Typography Rules

### Font Family
- **Brand Face**: United uses a clean humanist corporate sans. For non-licensed reproduction, **Open Sans** (by Steve Matteson, Apache 2.0) is the closest free analog — humanist proportions, strong legibility at small sizes and on departure-board-style data.
- **iOS Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Mono (record locators, e-tickets)**: `'SF Mono', Menlo, monospace`
- **Numerals**: tabular figures for gate/seat/times/miles so columns align.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Route IATA | Open Sans | 32pt | 800 | 1.05 | -0.5pt | `SFO → EWR` airport codes |
| Screen Title | Open Sans | 26pt | 800 | 1.15 | -0.3pt | "Your upcoming flight" |
| Section Header | Open Sans | 22pt | 700 | 1.2 | -0.2pt | "Choose your seat" |
| Card / Pax Name | Open Sans | 18pt | 700 | 1.25 | 0pt | Passenger name (uppercase) |
| Data Value (large) | Open Sans | 22pt | 800 | 1.1 | 0pt | Gate/Seat/Boards cell values |
| Body | Open Sans | 16pt | 400 | 1.5 | 0pt | Paragraph, descriptions |
| Data Value (inline) | Open Sans | 15pt | 600 | 1.3 | 0pt | "Seat 7A · Economy Plus" |
| Meta | Open Sans | 14pt | 400 | 1.4 | 0pt | "5h 32m · Boeing 777-300ER" |
| Eyebrow / Label | Open Sans | 12pt | 700 | 1.0 | 0.6pt | "UA 482 · BOEING 777" uppercase |
| Cell Label | Open Sans | 10pt | 700 | 1.0 | 0.6pt | "GATE" / "SEAT" / "BOARDS" |
| Button | Open Sans | 16pt | 700 | 1.0 | 0.2pt | Primary/secondary buttons |
| Tab Label | Open Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Status Pill | Open Sans | 12pt | 700 | 1.0 | 0.2pt | "On Time" / "Boarding" |

### Principles
- **Data is bold and big**: airport codes, gate, seat, boards-time are the highest-priority glyphs at weight 800 — scanned under stress.
- **Uppercase for labels, not content**: eyebrows and cell labels uppercase with positive tracking; passenger content stays cased.
- **Weight ladder 400 / 600 / 700 / 800**: body 400, inline data 600, headers/eyebrows 700, hero numerals/titles 800. No thin, no black.
- **Tabular numerals everywhere structured**: times, gate, seat, mileage balances use tabular figures so stacked rows align.
- **Dynamic Type first-class**: titles, body, meta scale; the barcode, seat-map glyphs, tab labels, and pill text stay fixed (layout/scan-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Check In / Continue)**
- Shape: Rounded rectangle, 4pt corner radius (United's buttons are crisp, low-radius)
- Background: `#1414FF` (Rhapsody Blue) — the electric action accent, one primary CTA per screen
- Text: `#FFFFFF`, Open Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal; full-width on action screens
- Pressed: background `#0E0EC8` + scale 0.98
- Disabled: `#1414FF` at 38% opacity

**Secondary Button (Boarding Pass)**
- Background: `#1B3D6E` (United Blue Mid) light & dark
- Text: `#FFFFFF`, Open Sans 15pt weight 700
- Corner radius: 4pt; padding 13pt / 24pt
- Pressed: `#002244` + scale 0.98

**Outline Button (Change Seat)**
- Background: transparent
- Text: `#0C1F38` light / `#E9EEF6` dark, Open Sans 14pt weight 600
- Border: 1.5pt `#566884` light / `#97A6BE` dark
- Corner radius: 4pt; padding 12pt / 20pt
- Pressed: `#1414FF14`

**Text Button (Flight details)**
- Text: `#1414FF` light / `#4A4AFF` dark, Open Sans 14pt weight 700
- No background, no underline; pressed: 60% opacity

### Boarding Pass (signature surface)

- Container: `#FFFFFF` light / `#111E33` dark, 16pt corner radius, 1pt divider border
- **Header band**: solid `#002244` United Blue, 12pt/18pt padding — United globe mark + "UNITED" wordmark (800, white) on the left; gold "PREMIER · GROUP 1" (11pt 700, `#D8B863`) on the right
- **Passenger row**: "PASSENGER" label (10pt 700 caps secondary) + name 18pt 700 uppercase primary
- **Data grid**: three equal cells `Gate / Seat / Boards` — each a 10pt uppercase label + 22pt weight-800 value; the Boards value renders in Premier gold `#D8B863`
- **Perforated divider**: a dashed 1pt line with two circular notch cut-outs at the edges (background-color circles) — the "tear here" affordance that makes it feel like a physical stub
- **Barcode block**: 54pt tall, high-contrast bar pattern; inverts to a *light* bar pattern in dark mode so a gate scanner reads a dimmed screen
- Tap → full-screen pass with brightness auto-boost + Wallet "Add" affordance

### Seat Map (signature surface)

- Container: card, 16pt corner radius, 18pt padding
- Title 13pt 700 + sub "Economy Plus · Row 7–9 · 7A selected" 12pt secondary
- **Cabin grid**: a `repeat(7, 1fr)` grid (3 seats · aisle · 3 seats), 6pt gap
  - Standard seat: `#1A2A45` cell, 6pt top / 3pt bottom radius (seat-shaped), 1pt divider border
  - Economy Plus seat: translucent `#1414FF` @ 18% fill, `#4A4AFF` @ 40% border
  - Taken seat: recessed `#0A1424` fill, dim border
  - Selected seat: solid `#1414FF` with a `#4A4AFF` glow ring
  - Aisle: transparent gap cell
- **Legend**: Econ+ / Selected / Taken swatches, 11pt secondary

### Flight Detail Header (hero)

- Full-bleed `#002244 → #04152C` navy gradient, 8pt top / 18pt bottom padding
- Eyebrow "UA 482 · BOEING 777" 11pt 700 caps, light-blue `#7FA0CE`
- Status pill top-right (rounded-999, status-tinted)
- Route row: left city (IATA 32pt 800 white + city name 12pt `#9FBADC`), center aircraft glyph + duration, right city (right-aligned)
- Times row: "8:05 AM · Gate F12" left, "4:37 PM · Term C" right, 13pt weight 600 `#CFE0F2`

### Status Chips

- Rounded-999 pill, 12pt weight 700, 7pt/14pt padding
- On Time: `#4FD18C` on `rgba(30,142,90,0.18)`
- Boarding: `#7A7AFF` on `rgba(20,20,255,0.22)`
- Delayed: `#E0A943` on `rgba(201,138,30,0.18)`
- Canceled: `#F0808A` on `rgba(216,67,79,0.18)`

### MileagePlus Premier Badge

- Rounded-8pt chip with a subtle gold gradient `rgba(194,161,77,0.18) → rgba(194,161,77,0.06)`, 1pt gold border
- Leading 22pt circular ring (2pt `#C2A14D` border) with the tier code ("1K", "PG", "PS") in 10pt weight 800 gold
- Label: tier name 13pt weight 700 in `#D8B863`
- Tiers: Premier Silver / Gold / Platinum / 1K / Global Services

### Cards & Lists

**Trip Card (Trips tab)**
- `#FFFFFF` / `#111E33`, 16pt radius, 1pt divider
- Route summary (IATA → IATA 16pt 800) + status pill + chevron, 16pt padding, 12pt gap

**Grouped List Row (MileagePlus / settings)**
- 52pt tall, leading SF Symbol 22pt, title 16pt 400, trailing chevron `#8493AC`
- Divider inset 56pt; pressed `#E5EAF3` / `#1A2A45`

### Navigation

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `#FFFFFF` / `#0A1424` with 0.5pt top divider, blur saturate(180%) blur(20px)
- Tabs (5): Home, Book, Flights, Trips, MileagePlus
- Icon 22pt; active `#1414FF` light / `#4A4AFF` dark (filled glyph), inactive `#8493AC` / `#5E7090`
- Labels: Open Sans 10pt weight 600, always shown; no tint pill

**Top Nav (detail screens)**
- Height: 44pt + safe area, navy `#002244` or transparent over the flight-detail header
- Leading back chevron (white over navy); center contextual title ("UA 482"); trailing share/refresh

### Input Fields

**Search / Field**
- Height: 48pt, `#F3F5FA` / `#1A2A45`, 4pt radius, no border
- Leading icon 18pt secondary; placeholder 15pt secondary
- Focus: 1.5pt `#1414FF` border

**Segmented Control (Round-trip / One-way / Multi-city)**
- Track `#E5EAF3` / `#1A2A45`, 4pt radius
- Selected segment knob `#FFFFFF` / `#1B3D6E`, label 14pt weight 700; unselected 14pt 600 secondary

### Distinctive Components

**Apple Wallet Pass** — boarding pass exported as `.pkpass` (navy header, globe, IATA, barcode); "Add to Apple Wallet" black pill below the pass; updates live (gate change pushes lock-screen).

**Bag Tracker** — horizontal step strip Checked → Loaded → In Transit → Arrived → Carousel; current step gets the on-time green fill; carousel number large.

**MileagePlus Balance Card** — navy gradient card, "MileagePlus" wordmark, large miles balance (tabular 800), Premier tier badge in gold; PQP/PQF progress bars toward next tier.

**Upgrade List** — a waitlist/upgrade roster (priority order) with your position highlighted in Rhapsody Blue.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Card internal padding: 16–18pt
- Vertical gap between cards: 12–16pt
- Screen horizontal inset: 16pt

### Grid & Container
- iPhone: content at 16pt insets; flight-detail header full-bleed
- iPad: content max-width 720pt centered, 24pt insets; two-column trips list landscape
- Boarding pass: full-width minus 16pt insets phone; max 420pt centered larger

### Whitespace Philosophy
- **Detail-rich but ordered**: the flight-detail screen carries a lot of data, organized into clear left/center/right zones so it never feels cluttered
- **Pass breathes**: 18pt internal padding so gate/seat values never crowd; the perforation gives visual rest
- **Seat map density**: tight 6pt-gap grid because seat selection benefits from seeing the whole cabin at once
- **Navy anchors**: navy gradients/headers ground each major screen so the brand is always present

### Border Radius Scale
- Square (0pt): full-bleed headers, barcode edges
- Tiny (3pt): seat-cell bottom corners
- Standard (4pt): buttons, fields, segmented control (United's crisp signature)
- Comfortable (8pt): Premier badge, inner chips
- Card (12–16pt): list cards, boarding pass, seat-map card
- Pill (500pt): status chips, "Add to Wallet"
- Circle (50%): perforation notches, Premier ring, avatars

## 6. Depth & Elevation

United is mostly flat with purposeful elevation on the boarding pass.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, flight-detail header (gradient is the depth cue) |
| Card (Level 1) | `rgba(0,0,0,0.10) 0 4px 12px` light / `rgba(0,0,0,0.45) 0 4px 14px` dark | Trip cards, seat-map card |
| Pass (Level 2) | `rgba(0,0,0,0.16) 0 8px 24px` light / `rgba(0,0,0,0.55) 0 10px 28px` dark | Boarding pass — most-elevated content |
| Sheet (Level 3) | `rgba(0,0,0,0.18) 0 -6px 28px` | Bottom sheets (fare details, upgrade) |
| Modal Overlay | `rgba(0,0,0,0.40)` | Dim behind modals/sheets |

**Shadow Philosophy**: elevation signals "the thing you act on / hold up at the gate". On dark mode shadows nearly vanish on `#0A1424`, so a 1pt `#243652` border doubles as the elevation cue on the pass and cards.

### Motion
- **Aircraft glide (flight-detail header)**: the aircraft glyph eases in on screen appear (~900ms ease-in-out), then settles between the cities
- **Seat select**: tapping a seat springs it to the Rhapsody fill (scale 0.9 → 1, 200ms spring) + light haptic; the selection summary updates
- **Status change**: pill cross-fades color over 250ms + soft haptic; affected detail row re-colors over 300ms
- **Boarding pass open**: tap → spring scale-up + screen-brightness auto-boost; dismiss → 250ms ease-down
- **Bag tracker advance**: active step fills with a 300ms ease + light haptic on a scan event
- **Tab switch**: 150ms cross-fade
- **Pull-to-refresh (status)**: navy spinner; success → soft success haptic
- **Page navigation**: iOS native push, 300ms horizontal slide
- **Haptics**: light impact on seat select, soft on status change, success on check-in, notification on gate change

## 7. Do's and Don'ts

### Do
- Use the two-blue brand correctly: United Blue (`#002244`) for brand/surface, Rhapsody Blue (`#1414FF`) for action
- Lead with the detail-rich flight-detail screen (left/center/right zones), then the boarding pass
- Put the United globe mark + gold Premier/Group band on the boarding-pass header
- Render the perforated dashed divider with circular notch cut-outs — it makes the pass feel physical
- Build an interactive seat map: Economy Plus tinted, taken recessed, selected glowing Rhapsody Blue
- Size airport codes, gate, seat, and times large and bold (800) — scanned under stress
- Use Premier gold (`#C2A14D`) only for MileagePlus elite status / priority bands
- Keep functional status colors (green/amber/red) distinct from the brand blues
- Use a deep navy (`#0A1424`) for dark canvas — never neutral black
- Invert the barcode to a light pattern in dark mode so gate scanners read a dimmed screen

### Don't
- Don't use Rhapsody Blue for brand surfaces or headers — it's the action accent, navy is the brand
- Don't use Premier gold as a general accent — it's elite-status / priority only
- Don't render gate/seat/IATA in small or light type — these are the highest-priority glyphs
- Don't confuse the "Boarding" status blue with Rhapsody action blue semantically
- Don't use a pure-black dark canvas — `#0A1424` keeps the brand navy reading
- Don't add a Material tint pill to the tab bar — active is icon fill + color only
- Don't drop the perforation divider — it's a recognizable part of the pass identity
- Don't over-animate — motion is refined (aircraft glide, seat spring), never decorative bounce
- Don't bury the boarding pass on travel day — it's one tap from Home/Flights
- Don't let the barcode go low-contrast in dark mode — it must remain machine-scannable

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Seat map stays 7-col; flight-detail meta wraps to one line |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; flight-detail header offsets below island |
| iPhone 15/16 Pro Max | 430pt | Larger IATA codes; seat cells scale up |
| iPad (portrait) | 768pt | Two-column: trips list (left) + selected flight detail (right) |
| iPad (landscape) | 1024pt+ | Flight detail max 720pt centered; seat map enlarges; pass max 420pt |

### Dynamic Type
- Route IATA, screen title, section, body, meta: full scale
- Boarding-pass barcode, seat-map cells, cell labels (10pt), tab labels (10pt), status-pill text: FIXED (scan/layout-sensitive)
- Data values scale one step then clamp to preserve the grid

### Orientation
- iPhone: portrait-locked on the boarding pass; other screens rotate
- iPad: full rotation; seat map can present in a larger landscape modal

### Touch Targets
- Tab bar icons: 22pt glyph, 44pt+ hit area
- Seat cell: minimum 32pt hit, expanded for small cabins
- Primary button: 48pt+ tall
- List rows: 52pt tall; "Add to Wallet" pill 44pt tall

### Safe Area Handling
- Top: flight-detail header and nav respect safe area + Dynamic Island
- Bottom: tab bar + home indicator respected
- Boarding pass: never clipped by the home indicator; barcode kept above safe-area inset
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#0A1424`
- Surface 1 (dark): `#111E33`
- Divider (dark): `#243652`
- Text primary: `#0C1F38` light / `#E9EEF6` dark
- Text secondary: `#566884` light / `#97A6BE` dark
- United Blue (brand/surface): `#002244`
- United Blue Mid (secondary CTA): `#1B3D6E`
- Rhapsody Blue (primary CTA / selected seat): `#1414FF`
- Rhapsody Bright (active on dark): `#4A4AFF`
- Premier gold: `#C2A14D` (bright `#D8B863`)
- Status on-time: `#1E8E5A` (`#4FD18C` dark)
- Status delayed: `#C98A1E` (`#E0A943` dark)
- Status canceled: `#D8434F` (`#F0808A` dark)

### Example Component Prompts
- "Build the United boarding pass in SwiftUI: a 16pt-radius card, `#111E33` background (dark). A solid `#002244` header band (12pt/18pt padding): United globe mark + 'UNITED' wordmark in Open Sans 13pt weight 800 white on the left, 'PREMIER · GROUP 1' in 11pt weight 700 `#D8B863` on the right. Body: 'PASSENGER' 10pt 700 caps `#97A6BE`, name 18pt 700 uppercase `#E9EEF6`. A 3-cell HStack `Gate / Seat / Boards` — 10pt uppercase label `#5E7090` over 22pt weight-800 value (Boards in gold `#D8B863`). A dashed 1pt perforation line with two circular `#0A1424` notch cut-outs at the edges. Foot: a 54pt high-contrast barcode that inverts to light bars on dark mode."

- "Create the United interactive seat map: a 16pt-radius card, title 13pt 700 + sub 12pt `#97A6BE`. A 7-column grid (3 seats · aisle · 3 seats), 6pt gap. Standard seat: `#1A2A45` cell, 6pt top / 3pt bottom corners, 1pt `#243652` border. Economy Plus: `#1414FF` @ 18% fill, `#4A4AFF` @ 40% border. Taken: recessed `#0A1424`. Selected: solid `#1414FF` with a `#4A4AFF` glow ring. Tapping a seat springs it (scale 0.9→1, 200ms) + light haptic. Legend: Econ+ / Selected / Taken."

- "Build the United flight-detail header: full-bleed `#002244 → #04152C` navy gradient. Eyebrow 'UA 482 · BOEING 777' 11pt 700 caps `#7FA0CE`; top-right status pill. Route row: left city (IATA 32pt 800 white + city name 12pt `#9FBADC`), center aircraft glyph + '5h 32m' duration, right city (right-aligned). Times row: '8:05 AM · Gate F12' left, '4:37 PM · Term C' right, 13pt weight 600 `#CFE0F2`."

- "Render the United primary button: full-width, `#1414FF` (Rhapsody Blue) background, 4pt corner radius (crisp), white Open Sans 16pt weight 700, 14pt vertical padding. Pressed: `#0E0EC8` + scale 0.98. One per screen ('Check In')."

- "Create the United MileagePlus Premier badge: a rounded-8pt chip with a `rgba(194,161,77,0.18) → rgba(194,161,77,0.06)` gold gradient and 1pt gold border. Leading 22pt circular ring (2pt `#C2A14D`) with the tier code ('1K') in 10pt weight 800 gold. Label: tier name 13pt weight 700 `#D8B863`."

- "Build the United bottom tab bar: 5 tabs (Home, Book, Flights, Trips, MileagePlus), 22pt icons, active `#1414FF` light / `#4A4AFF` dark filled glyph + 10pt weight-600 label, inactive `#8493AC`. No tint pill. 0.5pt top divider, blur background."

### Iteration Guide
1. Two-blue brand: United Blue `#002244` is brand/surface (headers, gradients, pass banner); Rhapsody Blue `#1414FF` is action (buttons, selected seat, active tab) — never swap their jobs
2. The signature flow is flight detail → boarding pass; lead with the detail-rich flight-detail screen organized into left/center/right zones
3. The boarding pass carries the United globe + gold Premier/Group band, a perforated dashed divider with circular notch cut-outs, and a scannable barcode
4. The interactive seat map tints Economy Plus, recesses taken seats, and glows the selected seat in Rhapsody Blue
5. Airport IATA codes, gate, seat, times are the biggest, boldest glyphs (weight 800) — scanned under stress
6. Premier gold `#C2A14D` is MileagePlus elite-status / priority-band only — never a general accent
7. Functional status colors (green/amber/red) stay distinct from the brand blues and are constant across modes
8. Dark canvas is a deep navy `#0A1424` — never neutral black — so the brand navy stays alive
9. The barcode inverts to a light pattern on dark mode so gate scanners read a dimmed screen
10. Buttons are crisp 4pt-radius; motion is refined (aircraft glide, seat spring, status cross-fade) — never decorative bounce
