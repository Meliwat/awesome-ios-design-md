# Design System Inspiration of Delta (iOS)

## 1. Visual Theme & Atmosphere

Fly Delta's iOS app is a calm, navy-grounded travel concierge that organizes itself entirely around one question: *where is my trip right now?* The moment you open it, the **Today** tab puts your most imminent flight front and center — a full-bleed navy header showing your origin and destination airport codes in big bold type, a small plane gliding along a connecting line, and a status pill ("On Time", "Delayed", "Now Boarding") that is the single most important word on the screen. Everything else (rebooking, bag tracker, SkyMiles, amenities) is one tap away but visually subordinate. The app's job is to remove travel anxiety, and the design does that by being uncluttered, status-first, and predictable.

The signature surface is the **digital boarding pass**. It is a tall card with a navy→red gradient strip across the top, the passenger name in uppercase, a clean three-cell `Gate / Seat / Group` data grid, a "SKY PRIORITY" or zone band, and a scannable barcode block at the bottom. This pass is the emotional and functional center of the product — it is what you hold up at the gate, it lives in Apple Wallet, and the in-app version stacks a live **flight-status timeline** above it (Departed → In Air → Arrive, each step a dot on a vertical rail with a time and gate). The combination of *boarding pass + flight status timeline* is the screen people open the app to see.

The color system is disciplined and corporate-confident. **Delta Blue** (`#003268`) is the brand anchor — it's the navigation accent, the gradient base, and the trust color. The **Delta "widget" red** (`#C8102E`), drawn from the triangular logomark, is the single high-energy accent and is reserved almost exclusively for the primary call-to-action ("Check In", "Continue"). There is no rainbow; Delta deliberately limits chroma so that functional status colors — on-time green, delayed amber, canceled red — read instantly and are never confused with brand decoration. SkyMiles **gold** (`#C99700`) is the loyalty/Medallion accent and appears as a Medallion tier badge or a "SkyMiles" emphasis.

Typography is a clean humanist sans in the Whitney/Open-Sans family — highly legible at a glance, generous in weight contrast (800 for airport codes and screen titles, 400 for body, 700 for eyebrows and uppercase labels). Airport IATA codes and gate/seat numbers are rendered large and bold because they are the data travelers scan under pressure. The type system favors **scannability over personality**: it should read like a confident airline counter display, not a lifestyle brand.

**Key Characteristics:**
- Navy-grounded, status-first travel concierge — the imminent flight is always the hero
- Digital boarding pass as the signature surface — navy→red strip, name, Gate/Seat/Group grid, barcode
- Flight-status timeline — vertical dot rail (Departed → In Air → Arrive) with times and gates
- Delta Blue (`#003268`) as the brand/navigation anchor; widget red (`#C8102E`) as the single CTA accent
- SkyMiles gold (`#C99700`) for Medallion / loyalty emphasis
- Functional status palette (green / amber / red) kept distinct from brand color
- Today / Book / Trips / SkyMiles / More bottom tab structure
- Big bold IATA codes and gate/seat numbers — data sized for at-a-glance scanning
- Apple Wallet integration — the boarding pass is a first-class Wallet pass
- Calm, uncluttered, predictable — designed to reduce travel anxiety

## 2. Color Palette & Roles

### Primary (Interactive)
- **Delta Red** (`#C8102E`): Primary interactive color — "Check In", "Continue", primary CTA background. The single high-energy accent, used sparingly.
- **Delta Red Pressed** (`#A30D26`): Pressed state for the primary CTA.
- **Delta Blue** (`#003268`): Brand anchor — navigation accent, boarding-pass gradient base, link/selected color, trust surfaces.
- **Delta Blue Bright** (`#0F4C9A`): Secondary CTA / interactive blue on dark surfaces where `#003268` would be too low-contrast.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F6F9`): Grouped list background, subtle elevated cards.
- **Surface Pressed** (`#E8ECF3`): Pressed/selected rows.
- **Divider** (`#D6DCE7`): Hairline dividers between rows and cards.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0B1320`): Primary dark canvas — a deep navy-charcoal, NOT neutral black, so the brand navy still reads.
- **Dark Surface 1** (`#131C2B`): Cards, boarding pass body, grouped sections.
- **Dark Surface 2** (`#1C2738`): Nested chips, zone bands, pressed rows.
- **Dark Divider** (`#25314A`): Hairline dividers.

### Text
- **Text Primary Light** (`#10243F`): Primary text — a navy-tinted near-black.
- **Text Secondary Light** (`#5A6A82`): Metadata, labels, captions.
- **Text Tertiary Light** (`#8A98AE`): Disabled, low-emphasis.
- **Text Primary Dark** (`#E8ECF3`): Primary text on dark.
- **Text Secondary Dark** (`#9AA6BC`): Metadata on dark.
- **Text Tertiary Dark** (`#5F6E88`): Disabled on dark.

### Brand & Loyalty

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Delta Blue | `#003268` | `#003268` |
| Delta Blue Bright | `#0F4C9A` | `#0F4C9A` |
| Delta Red (widget) | `#C8102E` | `#C8102E` |
| Widget Maroon (logo shadow) | `#862633` | `#862633` |
| SkyMiles Gold | `#C99700` | `#C99700` |
| Sky Light (header tint) | `#E7F0FA` | `#1A2C44` |

### Flight Status (Functional — constant across modes)

| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| On Time | `#1E8E5A` | `#4FD18C` | "On Time", departed-on-time, arrived |
| Boarding | `#0F4C9A` | `#6FB0EA` | "Now Boarding", "Boarding Soon" |
| Delayed | `#C99700` | `#E0B743` | "Delayed", schedule change |
| Canceled / Error | `#C8102E` | `#E0414E` | "Canceled", IROP, error |
| Gate Changed | `#D9730D` | `#F0954E` | "Gate Changed" alert |

### Semantic
- **Error Red** (`#C8102E` / `#E0414E` dark): Inline error, canceled flight, failed payment.
- **Success Green** (`#1E8E5A` / `#4FD18C` dark): Check-in complete, payment success.
- **Warning Amber** (`#C99700` / `#E0B743` dark): Delay, schedule change, expiring fare.
- **Selection Highlight** (`#0F4C9A1F`): 12% blue overlay on selected rows.

## 3. Typography Rules

### Font Family
- **Brand Face**: Delta uses a Whitney-class humanist sans (the airline's corporate typeface lineage). For non-licensed reproduction, **Open Sans** (by Steve Matteson, Apache 2.0) is the closest free analog — same humanist proportions, excellent legibility at small sizes and on signage.
- **iOS Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Mono (data, confirmation codes)**: `'SF Mono', Menlo, monospace` — record locators, e-ticket numbers.
- **Numerals**: tabular figures for gate/seat/times so columns align in the boarding pass and timeline.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Route IATA | Open Sans | 34pt | 800 | 1.05 | -0.5pt | `ATL → JFK` airport codes, the trip hero |
| Screen Title | Open Sans | 26pt | 800 | 1.15 | -0.3pt | "Your trip today", screen H1 |
| Section Header | Open Sans | 22pt | 700 | 1.2 | -0.2pt | "Flight status", "Trip extras" |
| Card / Pax Name | Open Sans | 18pt | 700 | 1.25 | 0pt | Passenger name (uppercase) on pass |
| Data Value (large) | Open Sans | 22pt | 800 | 1.1 | 0pt | Gate/Seat/Group cell values |
| Body | Open Sans | 16pt | 400 | 1.5 | 0pt | Paragraph, descriptions |
| Data Value (inline) | Open Sans | 15pt | 600 | 1.3 | 0pt | "Seat 14C · SKY PRIORITY" |
| Meta | Open Sans | 14pt | 400 | 1.4 | 0pt | "Est. arrival 1:02 PM EDT" |
| Eyebrow / Label | Open Sans | 12pt | 700 | 1.0 | 0.6pt | "TODAY · DL 1248" uppercase |
| Cell Label | Open Sans | 10pt | 700 | 1.0 | 0.6pt | "GATE" / "SEAT" / "GROUP" uppercase |
| Button | Open Sans | 16pt | 700 | 1.0 | 0.2pt | Primary/secondary buttons |
| Tab Label | Open Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Status Pill | Open Sans | 12pt | 700 | 1.0 | 0.2pt | "On Time" / "Delayed" |

### Principles
- **Data is bold and big**: Airport codes, gate, seat, group, and times are the highest-priority glyphs — sized large with weight 800 because travelers scan them under stress.
- **Uppercase for labels, not content**: Eyebrows and cell labels ("GATE", "TODAY · DL 1248") are uppercase with positive tracking; passenger content stays cased.
- **Weight ladder 400 / 600 / 700 / 800**: Body 400, inline data 600, headers/eyebrows 700, hero numerals/titles 800. No thin, no black.
- **Tabular numerals everywhere structured**: Times, gate, seat, mileage balances use tabular figures so they align in stacked rows.
- **Dynamic Type first-class**: Titles, body, meta scale; the boarding-pass barcode, tab labels, and pill text stay fixed (layout/scan-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Check In / Continue)**
- Shape: Rounded rectangle, 6pt corner radius
- Background: `#C8102E` (Delta Red) — the single high-energy accent, one primary CTA per screen
- Text: `#FFFFFF`, Open Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal; full-width on action screens
- Pressed: background `#A30D26` + scale 0.98
- Disabled: `#C8102E` at 38% opacity, text at 60%

**Secondary Button (View Boarding Pass)**
- Background: `#0F4C9A` (Delta Blue Bright) light & dark
- Text: `#FFFFFF`, Open Sans 15pt weight 700
- Corner radius: 6pt; padding 13pt / 24pt
- Pressed: background `#003268` + scale 0.98

**Outline Button (Change Seat / Modify)**
- Background: transparent
- Text: `#10243F` light / `#E8ECF3` dark, Open Sans 14pt weight 600
- Border: 1.5pt `#5A6A82` light / `#9AA6BC` dark
- Corner radius: 6pt; padding 12pt / 20pt
- Pressed: background `#0F4C9A14`

**Text Button (Flight details)**
- Text: `#0F4C9A` light / `#6FB0EA` dark, Open Sans 14pt weight 700
- No background, no underline
- Pressed: 60% opacity

### Boarding Pass (signature surface)

- Container: `#FFFFFF` light / `#131C2B` dark, 18pt corner radius, 1pt divider border, subtle shadow
- **Top strip**: 6pt tall, linear-gradient `#003268 → #C8102E` (Delta Blue to widget red) — the brand signature on the pass
- **Passenger row**: "PASSENGER" label (10pt 700 caps secondary) + name in 18pt 700 uppercase primary
- **Data grid**: three equal cells `Gate / Seat / Group` — each with a 10pt uppercase label and a 22pt weight-800 value; the Group/Medallion value can render in SkyMiles gold `#C99700`
- **Zone band**: full-width `#1C2738` (dark) / `#E8ECF3` (light) rounded 10pt strip — "Boards 10:12 AM · SKY PRIORITY" with the zone in 14pt weight-800
- **Barcode block**: 56pt tall, a high-contrast bar pattern (Aztec/PDF417 in production). On dark mode it inverts to a *light* bar pattern so a gate scanner still reads a dimmed screen
- Tap → full-screen pass with brightness auto-boost and Wallet "Add" affordance

### Flight Status Timeline (signature surface)

- Container: card, 16pt corner radius, 18pt padding
- Title: "Flight Status" 13pt weight 700
- **Vertical rail**: a column of 12pt dots connected by 2pt bars
  - Completed/active step dot: filled status color (on-time green `#1E8E5A`) with a 2pt ring
  - Upcoming step dot: `#5F6E88` (tertiary) with matching ring
  - Connector bar: `#25314A` divider color
- **Step**: bold label (13pt 700, e.g. "Departed ATL — On Time") + meta line (12pt secondary, "Gate B16 · 10:42 AM EDT")
- Steps: typically Departed → In Air / Cruising → Arrive (Terminal + Baggage Carousel)
- Live updates animate the active dot pulsing softly; a status change re-colors the affected step

### Trip Header (Today hero)

- Full-bleed gradient `#003268 → #06203F` navy block, 8pt top / 16pt bottom padding
- Eyebrow: "TODAY · DL 1248" 11pt weight 700 caps, light-blue `#8FB4DD`
- Status pill (top-right): rounded-999 chip, status-tinted (on-time = green-on-translucent)
- Route line: large IATA code (34pt 800 white) — animated plane glyph between two thin connecting lines — second IATA code
- Sub-row: "Gate B16 · 10:42 AM" left, "Seat 14C · 1:02 PM" right, 12pt light-blue

### Cards & Lists

**Trip Card (Trips tab)**
- `#FFFFFF` / `#131C2B`, 16pt radius, 1pt divider
- Leading: small route summary (IATA → IATA, 16pt 800)
- Trailing: status pill + chevron
- 16pt padding, 12pt vertical gap between cards

**Grouped List Row (More / settings)**
- 52pt tall, leading SF Symbol 22pt, title 16pt 400, trailing chevron `#8A98AE`
- Divider inset 56pt from leading
- Pressed: `#E8ECF3` / `#1C2738` background

### Status Chips

- Rounded-999 pill, 12pt weight 700, 7pt vertical / 14pt horizontal padding
- On Time: `#1E8E5A` text on `#1E8E5A1F`
- Now Boarding: `#0F4C9A` text on `#0F4C9A24`
- Delayed: `#C99700` text on `#C997001F`
- Canceled: `#C8102E` text on `#C8102E1F`
- (Dark mode uses the lighter status variants on the same translucent fill)

### Navigation

**Bottom Tab Bar**
- Height: 68pt + safe area
- Background: `#FFFFFF` / `#0B1320` with 0.5pt top divider, blur saturate(180%) blur(20px)
- Tabs (5): Today, Book, Trips, SkyMiles, More
- Icon size: 22pt; active color `#003268` light / `#5BA0E0` dark (filled glyph), inactive `#8A98AE` / `#5F6E88`
- Labels: Open Sans 10pt weight 600, always shown
- No tint pill — active is icon fill + label color change

**Top Nav (detail screens)**
- Height: 44pt + safe area, navy `#003268` or transparent over the trip header
- Leading: back chevron (white over navy)
- Center: contextual title (e.g. "DL 1248")
- Trailing: share / refresh icon

### Input Fields

**Search / Field**
- Height: 48pt, `#F4F6F9` / `#1C2738`, 8pt radius, no border
- Leading icon 18pt secondary; placeholder 15pt secondary
- Focus: 1.5pt `#0F4C9A` border

**Segmented Control (One-way / Round-trip / Multi-city)**
- Track `#E8ECF3` / `#1C2738`, 8pt radius
- Selected segment: white `#FFFFFF` / `#0F4C9A` knob, label 14pt weight 700
- Unselected label: 14pt weight 600 secondary

### Distinctive Components

**Apple Wallet Pass**
- The boarding pass is exported as a `.pkpass`: navy strip, airline logo, IATA codes, barcode
- In-app "Add to Apple Wallet" black pill button below the pass
- Wallet pass updates live (gate change pushes a lock-screen notification)

**Bag Tracker**
- A horizontal step strip: Checked → Loaded → In Transit → Arrived → Carousel
- Each step a dot; current step gets the on-time green fill; carousel number shown large

**SkyMiles Balance Card**
- Navy gradient card, "SkyMiles" wordmark, large mileage balance (tabular 800), Medallion tier badge in gold `#C99700`
- MQM/MQD progress bars toward next Medallion tier

**Flight Status Search Result**
- Card with route, flight number, status pill, on-time-bar (a small horizontal scheduled-vs-actual bar), gate, terminal

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Card internal padding: 16–18pt
- Vertical gap between cards: 12–16pt
- Screen horizontal inset: 16pt

### Grid & Container
- iPhone: content at 16pt horizontal insets; trip header full-bleed
- iPad: content max-width 700pt centered, 24pt insets; two-column trips list on landscape
- Boarding pass: full-width minus 16pt insets on phone; max 420pt centered on larger screens

### Whitespace Philosophy
- **Status first, chrome minimal**: the most imminent flight gets the most pixels; secondary actions are compact rows
- **Pass breathes**: the boarding pass has generous 18pt internal padding so gate/seat values never crowd
- **Calm density**: lists are 52pt rows with clear dividers — readable at a glance in a busy terminal
- **Gradient as anchor**: navy header gradients ground each major screen so the brand is always present

### Border Radius Scale
- Square (0pt): full-bleed headers, barcode block edges, gradient strip
- Subtle (4pt): inline tags, progress bars
- Standard (6pt): buttons, fields, segmented control
- Comfortable (10pt): zone bands, inner chips
- Card (16pt): list cards, timeline card
- Pass (18pt): the boarding pass container
- Pill (500pt): status chips, "Add to Wallet"
- Circle (50%): timeline dots, avatars, Medallion badge

## 6. Depth & Elevation

Delta is mostly flat with purposeful elevation on the boarding pass and floating actions.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, trip header (gradient is the depth cue) |
| Card (Level 1) | `rgba(0,0,0,0.10) 0 4px 12px` light / `rgba(0,0,0,0.45) 0 4px 14px` dark | Trip cards, timeline card |
| Pass (Level 2) | `rgba(0,0,0,0.16) 0 8px 24px` light / `rgba(0,0,0,0.55) 0 10px 28px` dark | Boarding pass — the most-elevated content |
| Sheet (Level 3) | `rgba(0,0,0,0.18) 0 -6px 28px` | Bottom sheets (seat map, fare details) |
| Modal Overlay | `rgba(0,0,0,0.40)` | Dim behind modals/sheets |

**Shadow Philosophy**: Elevation signals "this is the thing you act on". The boarding pass sits highest because it's the object you hold up at the gate. On dark mode shadows are deeper and a 1pt `#25314A` border doubles as the elevation cue since shadows nearly vanish on `#0B1320`.

### Motion
- **Plane glide (trip header)**: the plane glyph eases along the connecting line over ~1200ms ease-in-out on screen appear, then settles
- **Status change**: when a flight status updates, the pill cross-fades color over 250ms and a soft haptic fires; the affected timeline step re-colors with a 300ms ease
- **Active timeline dot**: a slow 1.6s pulse (scale 1 → 1.15 → 1) on the in-progress step
- **Boarding pass open**: tap → spring scale-up + screen-brightness auto-boost; dismiss → 250ms ease-down
- **Bag tracker advance**: the active step dot fills with a 300ms ease and a light haptic when a scan event arrives
- **Tab switch**: instant content swap, 150ms cross-fade
- **Pull-to-refresh (status)**: navy spinner; on success, a soft success haptic and the status pill animates
- **Page navigation**: iOS native push, 300ms horizontal slide
- **Haptics**: soft impact on status change, gate change (notification haptic), check-in success (success haptic), pass added to Wallet

## 7. Do's and Don'ts

### Do
- Anchor the brand with Delta Blue (`#003268`) and reserve widget red (`#C8102E`) for the single primary CTA
- Make the boarding pass the highest-elevation, most-detailed surface — navy→red strip, name, Gate/Seat/Group, barcode
- Put a flight-status timeline above the pass (Departed → In Air → Arrive) with times and gates
- Size airport codes, gate, seat, and times large and bold (800) — they're scanned under stress
- Keep functional status colors (green/amber/red) distinct from brand color
- Invert the barcode to a light pattern in dark mode so gate scanners still read a dimmed screen
- Use SkyMiles gold (`#C99700`) only for Medallion / loyalty emphasis
- Use a deep navy-charcoal (`#0B1320`) for dark canvas — never neutral black, the navy must stay alive
- Surface the imminent flight first on the Today tab; everything else is subordinate
- Offer "Add to Apple Wallet" prominently and keep the Wallet pass live (gate-change push)

### Don't
- Don't use red as a general accent — it's the CTA and the canceled/error color only
- Don't render gate/seat/IATA in small or light type — these are the highest-priority glyphs
- Don't confuse brand navy with the "Boarding" status blue — keep status semantics separate
- Don't use a pure-black dark canvas — `#0B1320` keeps the brand navy reading
- Don't add a Material tint pill to the tab bar — active is icon fill + color only
- Don't over-animate — motion is calm and functional (status changes, plane glide), never decorative bounce
- Don't bury the boarding pass behind taps on travel day — it should be one tap from Today
- Don't use multiple competing CTAs on one screen — one red primary action
- Don't let the barcode go low-contrast in dark mode — it must remain machine-scannable
- Don't decorate the trip header with imagery that fights the status pill — the status word is king

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Pass data grid stays 3-up; timeline compresses meta to one line |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; trip header offsets content below island |
| iPhone 15/16 Pro Max | 430pt | Larger IATA codes; pass scales up, more meta visible |
| iPad (portrait) | 768pt | Two-column: trips list (left) + selected trip detail (right) |
| iPad (landscape) | 1024pt+ | Trip detail max 700pt centered; pass max 420pt |

### Dynamic Type
- Route IATA, screen title, section header, body, meta: full scale
- Boarding-pass barcode, cell labels (10pt), tab labels (10pt), status-pill text: FIXED (scan/layout-sensitive)
- Data values scale one step then clamp to preserve the grid

### Orientation
- iPhone: portrait-locked on the boarding pass (it's a hold-up surface); other screens rotate
- iPad: full rotation; split trips/detail layout in landscape

### Touch Targets
- Tab bar icons: 22pt glyph, 44pt+ hit area
- Primary button: 48pt+ tall
- List rows: 52pt tall, full-row tap
- Timeline step: full-row tap reveals segment detail
- "Add to Wallet" pill: 44pt tall

### Safe Area Handling
- Top: trip header and nav respect safe area and Dynamic Island
- Bottom: tab bar + home indicator respected
- Boarding pass: never clipped by the home indicator; barcode kept above safe-area inset
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#0B1320`
- Surface 1 (dark): `#131C2B`
- Divider (dark): `#25314A`
- Text primary: `#10243F` light / `#E8ECF3` dark
- Text secondary: `#5A6A82` light / `#9AA6BC` dark
- Delta Blue (brand/nav): `#003268`
- Delta Blue Bright (secondary CTA): `#0F4C9A`
- Delta Red (primary CTA / canceled): `#C8102E`
- Delta Red pressed: `#A30D26`
- SkyMiles gold: `#C99700`
- Status on-time: `#1E8E5A` (`#4FD18C` dark)
- Status delayed: `#C99700` (`#E0B743` dark)
- Status boarding: `#0F4C9A` (`#6FB0EA` dark)

### Example Component Prompts
- "Build the Delta digital boarding pass in SwiftUI: a card with 18pt corner radius, `#131C2B` background (dark), a 6pt top strip with a `#003268 → #C8102E` linear gradient. Below: 'PASSENGER' label in Open Sans 10pt weight 700 uppercase `#9AA6BC`, then the passenger name in 18pt weight 700 uppercase `#E8ECF3`. Then a 3-cell HStack `Gate / Seat / Group`, each cell a 10pt uppercase label `#5F6E88` over a 22pt weight-800 value `#E8ECF3` (Group value in SkyMiles gold `#C99700`). A `#1C2738` rounded-10pt zone band 'Boards 10:12 AM · SKY PRIORITY' with the zone in 14pt weight 800. Foot: a 56pt high-contrast barcode block that inverts to light bars on dark mode."

- "Create the Delta flight-status timeline: a 16pt-radius card, title 'Flight Status' 13pt weight 700. A vertical rail of 12pt dots connected by 2pt `#25314A` bars. Completed steps get a `#1E8E5A` filled dot with a 2pt ring; upcoming steps get a `#5F6E88` dot. Each step: bold label 13pt 700 (e.g. 'Departed ATL — On Time') + meta 12pt `#9AA6BC` ('Gate B16 · 10:42 AM EDT'). Steps: Departed → In Air → Arrive (Terminal + Carousel). Pulse the active dot scale 1→1.15→1 over 1.6s."

- "Build the Delta Today trip header: a full-bleed `#003268 → #06203F` navy gradient block. Eyebrow 'TODAY · DL 1248' 11pt weight 700 uppercase `#8FB4DD`. Top-right status pill (rounded-999, on-time = green text on translucent green). Route row: IATA code 34pt weight 800 white, an animated plane glyph between two thin white connecting lines, second IATA code. Sub-row: 'Gate B16 · 10:42 AM' left, 'Seat 14C · 1:02 PM' right, 12pt `#9FC0E2`."

- "Render the Delta primary button: full-width, `#C8102E` background, 6pt corner radius, white Open Sans 16pt weight 700, 14pt vertical padding. Pressed: `#A30D26` + scale 0.98. Only one per screen ('Check In')."

- "Create the Delta status chip set: rounded-999 pills, 12pt weight 700, 7pt/14pt padding. On Time = `#1E8E5A` on `#1E8E5A1F`; Now Boarding = `#0F4C9A` on `#0F4C9A24`; Delayed = `#C99700` on `#C997001F`; Canceled = `#C8102E` on `#C8102E1F`. Dark mode swaps to the lighter status variant on the same translucent fill."

- "Build the Delta bottom tab bar: 5 tabs (Today, Book, Trips, SkyMiles, More), 22pt icons, active `#003268` light / `#5BA0E0` dark with filled glyph + 10pt weight-600 label, inactive `#8A98AE`. No tint pill. 0.5pt top divider, blur background."

### Iteration Guide
1. Delta Blue `#003268` is the brand/navigation anchor; widget red `#C8102E` is the single primary-CTA accent (and the canceled/error color) — never a decorative accent
2. The boarding pass is the signature surface and the highest-elevation content: navy→red strip, uppercase name, Gate/Seat/Group grid, scannable barcode
3. A flight-status timeline sits above the pass — vertical dot rail, Departed → In Air → Arrive, times and gates
4. Airport IATA codes, gate, seat, and times are the biggest, boldest glyphs (weight 800) — sized for at-a-glance scanning under stress
5. Functional status colors (green/amber/red) are kept distinct from brand color and are constant across light/dark
6. Dark canvas is a deep navy-charcoal `#0B1320` — never neutral black — so the brand navy stays alive
7. The barcode inverts to a light pattern on dark mode so gate scanners still read a dimmed screen
8. SkyMiles gold `#C99700` is reserved for Medallion / loyalty emphasis only
9. Motion is calm and functional — plane glide, status cross-fade, active-dot pulse — never decorative bounce
10. Today shows the imminent flight first; everything else is visually subordinate; "Add to Apple Wallet" is prominent and the Wallet pass stays live
