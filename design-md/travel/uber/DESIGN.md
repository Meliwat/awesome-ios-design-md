# Design System Inspiration of Uber (iOS)

## 1. Visual Theme & Atmosphere

Uber's iOS app is built around a single, enormous object: the map. Every primary screen — Home, Searching, On Trip, Arrival — is 60-80% map, with a bottom sheet sliding up to hold whatever UI chrome is required at that moment. The interface is essentially a transparent layer over geography; the city is the canvas. This is radically different from most consumer apps: there is no feed, no shelf, no hero image. The hero is always the road, and Uber's visual language exists to stay out of its way.

The palette is almost aggressively monochrome. Pure black (`#000000`) and pure white (`#FFFFFF`) do the vast majority of the work — CTAs, text, backgrounds, map pins — and the result is a UI that feels more like signage than software. When color appears it is functional, never decorative: green (`#05A357`) means "your driver is here" or "confirmed", red (`#D72113`) means "surge" or "cancel", blue (`#0A47FF`) means "info" or "Business profile", amber (`#FFCB00`) means "high surge". There are no gradients, no glows, no skeuomorphic touches. Surfaces are flat; elevation is expressed by a single hairline and a shadow so subtle it reads more as a seam than a drop.

Typography is Uber Move — a custom geometric sans designed for signage-clarity at speed. It ships in three cuts: Uber Move (Display) for anything 20pt and up, Uber Move Text for body and UI, and Uber Move Mono for ETAs, addresses, and receipt numerals. The mono variant is a signature move: when you see "6 min" or "212 Market St" set in Uber Move Mono, the app telegraphs machine-precise information — this is the data, not the voice. Hierarchy is tight: 12-36pt, with weights concentrated at 400 / 500 / 700 / 900 (Regular, Medium, Bold, Heavy). The "Where to?" placeholder — black text on white, Uber Move Text 18pt Medium — is probably the most recognized piece of UI type in rideshare.

**Key Characteristics:**
- Map as the hero surface — every primary screen is 60-80% map
- Bottom sheet as the primary container — "Where to?" entry point, ride options, active trip details
- Stark monochrome UI chrome — black and white, no gradients, minimal shadow
- Uber Move font family across Display, Text, and Mono cuts
- Geometric iconography with 2pt strokes and rounded joins
- Functional-only color — green / red / blue / amber each do one job
- Destination pin in Uber black with a subtle ground shadow
- Pulsing green beacon for driver arrival
- Dark-mode map with inverted gray ramp and `#0C0C0C` canvas

## 2. Color Palette & Roles

### Primary
- **Uber Black** (`#000000`): Primary brand color. CTAs, primary text, destination pin, logotype, bottom tab active state.
- **Uber White** (`#FFFFFF`): Canvas, text on dark, map background in light mode, bottom sheet surface.

### Gray Ramp (Base Design System)
- **Gray 50** (`#F6F6F6`): Light surface, secondary sheet background, disabled fills.
- **Gray 100** (`#EEEEEE`): Hairline rows, list backgrounds.
- **Gray 200** (`#E5E5E5`): Dividers, input borders default.
- **Gray 400** (`#AFAFAF`): Tertiary text, disabled icons.
- **Gray 600** (`#757575`): Secondary text, metadata, placeholder.
- **Gray 700** (`#545454`): Body text on white (softer than pure black for paragraphs).
- **Gray 900** (`#2F2F2F`): Dark surfaces in light mode, tooltip backgrounds.
- **Gray 950** (`#1A1A1A`): Elevated dark surface in dark mode.

### Functional Color (Semantic)
- **Green 600** (`#05A357`): Active trip, driver arrived, confirmed, success. The "your ride is here" pulse.
- **Red 600** (`#D72113`): Surge pricing, destructive actions (Cancel Ride), error, high-demand indicator.
- **Blue 600** (`#0A47FF`): Info, in-app links, Uber for Business profile chip, "learn more".
- **Amber 500** (`#FFCB00`): High-tier surge multiplier, caution, reserved-trip badge.

### Dark Mode
- **Dark Canvas** (`#0C0C0C`): Primary dark background — very near black but not pure, reduces OLED bleed.
- **Dark Surface 1** (`#1A1A1A`): Elevated surface, bottom sheet in dark mode.
- **Dark Surface 2** (`#2F2F2F`): Higher elevation, modal, cards on dark.
- **Dark Divider** (`#3A3A3A`): Hairline on dark.
- **Dark Text Primary** (`#FFFFFF`): Headlines and body on dark.
- **Dark Text Secondary** (`#AFAFAF`): Metadata on dark.
- Functional colors are lightened slightly on dark (`#22C77A`, `#FF4C3F`, `#3D6DFF`) to maintain contrast over `#0C0C0C`.

### Map Tint
- **Light Map Land** (`#F2F2F2`), **Roads** (`#FFFFFF`), **Water** (`#D9E5F2`)
- **Dark Map Land** (`#1E1E1E`), **Roads** (`#2F2F2F`), **Water** (`#0F1B2A`)
- **Route Polyline** (`#000000` light / `#FFFFFF` dark) — 5pt stroke with white/black casing for legibility over any map color

## 3. Typography Rules

### Font Family
- **Display**: `Uber Move` (20pt and up — logo, large headlines, "Where to?")
- **Body / UI**: `Uber Move Text` (below 20pt — row titles, labels, body copy)
- **Numerals / Address**: `Uber Move Mono` (ETA, address display, receipt line items)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'DM Sans', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Falls back to system fonts with equivalent optical weight

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Headline | Uber Move | 36pt | 700 | 1.1 | -0.6pt | Onboarding, large promos |
| Sheet Title | Uber Move | 24pt | 700 | 1.15 | -0.4pt | "Choose a ride", "Confirm pickup" |
| Where To Placeholder | Uber Move Text | 18pt | 500 | 1.2 | -0.1pt | Bottom sheet search input |
| Row Title | Uber Move Text | 16pt | 500 | 1.25 | -0.1pt | Ride option name, address row |
| Body | Uber Move Text | 15pt | 400 | 1.4 | 0pt | Descriptions, receipts |
| Metadata / Secondary | Uber Move Text | 14pt | 400 | 1.3 | 0pt | ETA subtext, driver rating |
| Price | Uber Move Mono | 16pt | 500 | 1.2 | 0pt | Fare on ride option card |
| ETA / Numerals | Uber Move Mono | 14pt | 500 | 1.2 | 0pt | "6 min", "$18.42", "212 Market St" |
| Label (UPPER) | Uber Move Text | 11pt | 700 | 1.2 | 0.6pt | "SUGGESTED", "PROMO" chips |
| Button (Primary) | Uber Move Text | 17pt | 500 | 1.0 | 0pt | "Confirm Uber X", "Request" |
| Button (Secondary) | Uber Move Text | 15pt | 500 | 1.0 | 0pt | Text buttons, chip buttons |
| Tab Label | Uber Move Text | 11pt | 500 | 1.0 | 0.2pt | Bottom tab bar |
| Caption | Uber Move Text | 12pt | 400 | 1.3 | 0pt | Fine print, legal |

### Principles
- **Three cuts do three jobs**: Display for headlines, Text for UI, Mono for data. Never swap them.
- **Weights concentrated at 400 / 500 / 700**: Regular, Medium, Bold — Heavy (900) is reserved for the Uber logotype
- **Negative tracking on display sizes only**: -0.1 to -0.6pt on 18pt+, 0pt on body
- **Mono numerals signal precision**: anything with a fare, ETA, or address gets the mono treatment
- **No italics**: the type system is strictly upright — emphasis comes from weight, never slant

## 4. Component Stylings

### Buttons

**Primary CTA (Black Bar)**
- Background: `#000000`
- Text: `#FFFFFF`, Uber Move Text 17pt weight 500
- Height: 56pt, full-width in bottom sheet with 16pt horizontal inset
- Corner radius: 8pt
- Pressed: background `#2F2F2F`, scale 0.98, medium haptic
- Loading: replace label with 20pt white activity indicator
- Used for: "Confirm Uber X", "Request", "Continue", "Add Payment"

**Secondary / Outline Button**
- Background: transparent
- Border: 1pt solid `#000000`
- Text: `#000000`, Uber Move Text 15pt weight 500
- Height: 44pt
- Corner radius: 8pt
- Pressed: background `#F6F6F6`

**Destructive Button (Cancel Ride)**
- Background: transparent
- Text: `#D72113`, Uber Move Text 17pt weight 500
- Height: 56pt
- Pressed: background `rgba(215,33,19,0.08)`, warning haptic on confirmation sheet

**Icon-Only Map Control**
- Size: 44pt circle
- Background: `#FFFFFF` with shadow `rgba(0,0,0,0.12) 0 2px 8px`
- Icon: 22pt, `#000000`, 2pt stroke geometric
- Used for: recenter (crosshair), zoom in/out, layer toggle
- Position: floats over the map, safe-area-aware, above the bottom sheet

**Chip (Saved Place / Promo)**
- Background: `#F6F6F6`
- Text: `#000000`, Uber Move Text 14pt weight 500
- Padding: 10pt vertical, 14pt horizontal
- Corner radius: 500pt (full pill)
- Leading icon: 16pt glyph (home, work, star)
- Pressed: `#E5E5E5`

### Cards & Containers

**Ride Option Card (UberX, Comfort, XL...)**
- Layout: 72pt tall horizontal row inside a selectable list
- Leading: 56×56pt car illustration (black 3D render on transparent background)
- Center: Product name (Uber Move Text 16pt w500) + ETA line (Uber Move Mono 13pt w400 `#757575`) + capacity pill
- Trailing: Price (Uber Move Mono 16pt w500 `#000000`), struck-through original price in `#757575` if surge/discount
- Selected state: 2pt `#000000` border, background `#F6F6F6`
- Unselected: 1pt `#E5E5E5` border, `#FFFFFF` background
- Tap: light haptic, border animates in over 150ms

**Active Trip Card (Driver on the Way / Arrived)**
- Sits inside the bottom sheet at ~280pt tall
- Top row: driver photo (48pt circle) + driver name (Uber Move Text 16pt w500) + star rating (14pt mono)
- Car line: make/model (14pt w400 `#545454`) + license plate in a `#F6F6F6` pill with Uber Move Mono 14pt w500
- ETA badge: large pill on the map, `#000000` background, white Uber Move Mono 18pt
- Action row: Message, Call, Share Trip — 44pt circular `#F6F6F6` buttons with 22pt black icons
- Separator: 1pt `#E5E5E5` hairline between sections

**Bottom Sheet ("Where to?" entry point)**
- Position: anchored to bottom safe area, 3 detents (collapsed 140pt, medium 360pt, expanded near-full)
- Background: `#FFFFFF` light / `#1A1A1A` dark
- Corner radius: 16pt top-left, 16pt top-right, 0pt bottom
- Grabber: 36×4pt pill `#E5E5E5`, centered 6pt from top
- Shadow: `rgba(0,0,0,0.08) 0 -4px 24px`
- Contents: "Where to?" input + suggested places + payment chip + scheduled-ride row

**"Where to?" Input**
- Height: 56pt
- Background: `#F6F6F6`
- Corner radius: 8pt
- Leading: 20pt black square "From" dot + vertical 1pt dashed line + 20pt black square "To" dot
- Placeholder: Uber Move Text 18pt w500 `#000000` reading "Where to?"
- Trailing: "Later" chip for scheduling (Uber Move Text 14pt w500, clock glyph)
- Focus: expands to full-screen search with all saved places and recent destinations

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt top border `#E5E5E5`, `.regularMaterial` blur when content scrolls beneath
- Tabs: Home, Services, Activity, Account
- Icon size: 24pt, 2pt stroke, rounded joins
- Active color: `#000000` (filled variant)
- Inactive: `#757575` (outline variant)
- Labels: Uber Move Text 11pt w500, always shown
- Haptic on tab change: light

**Top Nav (Trip / Details Screen)**
- Height: 44pt + safe area
- Background: `#FFFFFF` or transparent over map
- Leading: 44pt back chevron circle `#FFFFFF` with shadow (floats over map)
- Center: screen title, Uber Move 18pt w500, only when not over map
- Trailing: share or more icon

### Input Fields

**Search / Address Input**
- Background: `#F6F6F6`
- Border: none default, 1pt `#000000` on focus
- Height: 48pt
- Corner radius: 8pt
- Leading glyph: 18pt, `#000000`
- Placeholder: Uber Move Text 16pt w400 `#757575`
- Text color: `#000000`

**List Row (Saved Place / Recent)**
- Height: 56pt
- Leading: 32pt square icon tile `#F6F6F6` with 16pt glyph `#000000`
- Title: Uber Move Text 16pt w500 `#000000`
- Subtitle: Uber Move Mono 13pt w400 `#757575` (address)
- Trailing: 16pt chevron `#AFAFAF`
- Pressed: background `#F6F6F6`
- Divider: 1pt `#EEEEEE` hairline indented 56pt

### Distinctive Components

**Destination Pin**
- Shape: Black rounded square with tapered point, 32×40pt
- Fill: `#000000`
- Ground shadow: `rgba(0,0,0,0.25) 0 4px 8px`, ellipse 28×8pt centered below
- Drop animation on place: 0.3s spring with subtle bounce

**Driver Car Icon (on map)**
- Base: small black car silhouette 24×24pt oriented along route heading
- Arrival state: pulsing green ring `#05A357` at 32-48pt, 1.2s ease-in-out loop, 40% opacity
- Heading arrow: white triangle caret overlay when moving

**Active Route Polyline**
- Width: 5pt
- Color: `#000000` light / `#FFFFFF` dark
- Casing: 1pt `#FFFFFF` (light) / `#000000` (dark) outline for legibility
- Endcaps: rounded
- Traveled portion: 40% opacity; remaining portion: full opacity
- Animated stroke on trip start, 400ms ease-out

**Payment Method Chip**
- Fixed at bottom of "Where to?" sheet, above the search input
- Background: transparent
- Height: 44pt, full width, tappable
- Leading: 28×18pt card glyph (Visa, Apple Pay, etc.)
- Label: Uber Move Text 15pt w500 `#000000` ("Personal · Visa ·· 4242")
- Trailing: 14pt chevron `#757575`
- Tap: opens payment method sheet with haptic

**Pulse Beacon (Driver Arrived)**
- Emanates from driver car icon on the map
- Three concentric circles of `#05A357`, 30% / 20% / 10% opacity
- Scale from 1.0 to 2.4 over 1.8s, staggered 0.6s between rings
- Triggered on arrival geofence, stops on trip start

**Surge Badge**
- Position: overlay on affected ride-option card
- Background: `#D72113` (or `#FFCB00` for moderate)
- Text: `#FFFFFF`, Uber Move Text 11pt w700 ("1.5x")
- Corner radius: 4pt
- Padding: 2pt vertical, 6pt horizontal
- Lightning glyph leading the multiplier

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Standard margin: 16pt horizontal inside sheet, 0pt outside (map is edge-to-edge)
- Bottom sheet internal padding: 16pt horizontal, 20pt top (below grabber), 24pt bottom above safe area

### Grid & Container
- Map: full viewport, edge-to-edge, below the Dynamic Island or status bar
- Bottom sheet: full-width, attached to bottom, floats above map with 16pt top radius
- Floating controls (recenter, zoom): 16pt from trailing edge, offset above current sheet detent

### Whitespace Philosophy
- **Map gets the room**: 60-80% of viewport, always — nothing competes with geography
- **Sheet breathes at top**: 20pt padding below the grabber, so titles never feel cramped
- **List rows pack tight**: 56pt rows in saved places and ride options, relying on hairline dividers
- **CTAs get hero space**: the black 56pt "Confirm" button always sits at the bottom of the sheet with 24pt of air above

### Border Radius Scale
- Square (0pt): Map tiles, full-bleed images, bottom edges of the sheet
- Subtle (4pt): Surge badge, small status pills, thumbnail tiles
- Standard (8pt): Buttons, inputs, ride option cards, list row highlights
- Comfortable (12pt): Modal cards, bottom-sheet inner cards
- Sheet (16pt): Top corners of the bottom sheet
- Full Pill (500pt): Chips, saved-place tags, license plate pill
- Circle (50%): Driver photo, floating map controls, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Map, list rows inside the sheet |
| Hairline (Level 1) | 1pt `#E5E5E5` border | Ride option card, input default |
| Lifted Control (Level 2) | `rgba(0,0,0,0.12) 0 2px 8px` | Floating map buttons (recenter, zoom), destination pin |
| Bottom Sheet (Level 3) | `rgba(0,0,0,0.08) 0 -4px 24px` | The "Where to?" sheet, ride options sheet |
| Modal (Level 4) | `rgba(0,0,0,0.25) 0 12px 40px` | Confirm sheets, payment selector |
| Dim Overlay | `rgba(0,0,0,0.5)` | Behind modal sheets |

**Shadow Philosophy**: Uber's shadows are restrained. Because the canvas is white and most UI is black or gray, even a 12% shadow reads distinctly. The bottom sheet's upward shadow is the most prominent moment — it's the one piece of elevation that consistently separates UI from map. Every other shadow is a whisper: just enough to float controls above the map, never enough to read as "card".

### Motion
- **CTA press**: scale 0.98 → 1.0 spring (damping 0.8), background darkens to `#2F2F2F`, medium haptic on release
- **Sheet detent change**: 0.35s spring, damping 0.85 — the sheet snaps cleanly to collapsed / medium / expanded
- **Ride option select**: border animates 1pt → 2pt over 150ms, light haptic
- **Destination pin drop**: 0.3s spring with bounce, shadow grows with the pin
- **Driver pulse**: 1.8s ease-in-out, infinite loop, three staggered rings
- **Route polyline draw**: 400ms ease-out stroke-dashoffset on trip confirmation
- **Recenter tap**: camera flyTo 0.6s ease-in-out, no haptic (soft action)
- **Map camera follow on trip**: smooth 60fps heading-aware follow, snaps back after user pan after 3s idle

## 7. Do's and Don'ts

### Do
- Keep the map at 60%+ of viewport on every primary screen
- Use pure `#000000` for primary CTAs, destination pin, and active bottom-tab icon
- Render the "Where to?" bottom sheet with 16pt top-corner radius and visible grabber
- Use Uber Move Mono for every ETA, fare, and address — never the body text cut
- Reserve green `#05A357` for "driver arrived" and "confirmed" states only
- Float map controls above the current sheet detent, respecting safe areas
- Use the license-plate pill in `#F6F6F6` with Uber Move Mono 14pt to display plate numbers
- Trigger a medium haptic on "Confirm", a success haptic on "Driver arrived", a warning haptic on "Cancel ride"
- Use the `#0C0C0C` dark canvas — not pure black — to match the dark-mode map

### Don't
- Don't add gradients, glows, or glossy effects — Uber's chrome is flat, period
- Don't color primary CTAs — the primary button is always black, never branded green or blue
- Don't use green or red outside their semantic roles (success / destructive) — they lose meaning
- Don't round bottom sheet corners on all four sides — only top corners, bottom is flush
- Don't use drop shadows heavier than 0.25 opacity anywhere — elevation stays whisper-quiet
- Don't mix body font into numeric data — "6 min" is mono, always
- Don't hide the payment chip on the entry sheet — it must always be visible and tappable
- Don't center-align body text — Uber's UI is left-aligned (LTR); centering is reserved for headlines and buttons
- Don't block the map with full-screen opaque sheets during an active trip — user must always see the car

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Bottom sheet medium detent drops to 320pt, ride options stack 64pt tall |
| iPhone 13/14/15 | 390pt | Standard 72pt ride options, 360pt medium detent |
| iPhone 15/16 Pro | 393pt | Dynamic Island pushes top safe area; map controls sit 8pt below the island |
| iPhone 15/16 Pro Max | 430pt | Ride-option illustrations scale to 64pt, more breathing room in sheet |
| iPad | 768pt+ | Map stays full-bleed; sheet becomes a 420pt floating panel anchored bottom-leading |

### Dynamic Type
- Body, row titles, ETA mono: full scale
- Button labels: scale up to 120% then truncate
- Destination pin label on map: fixed 11pt (never scales — layout-sensitive)
- Sheet titles: scale to 28pt max
- License plate pill: fixed size always (visual consistency)

### Orientation
- Home / Searching / On Trip: **portrait-locked**
- Receipts / Trip History: **portrait-locked** (reading context)
- iPad only: landscape supported with sheet on trailing edge instead of bottom

### Touch Targets
- Primary CTA: 56pt tall, full width inside sheet — always reachable
- Floating map controls: 44pt hit area
- List rows: 56pt, full-row tappable
- Tab bar icons: 24pt icon inside 44pt effective hit
- Payment chip: 44pt full width

### Safe Area Handling
- Top: map extends under status bar / Dynamic Island; critical controls respect safe area
- Bottom: sheet extends to home indicator; content padded 24pt above home indicator
- Sides: map full-bleed; sheet content 16pt horizontal padding

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#0C0C0C`
- Primary black: `#000000`
- Gray 50 (surfaces): `#F6F6F6`
- Gray 200 (dividers): `#E5E5E5`
- Gray 400 (tertiary text): `#AFAFAF`
- Gray 600 (secondary text): `#757575`
- Gray 700 (body on white): `#545454`
- Gray 900 (dark surface light-mode): `#2F2F2F`
- Green (active/confirmed): `#05A357`
- Red (destructive/surge): `#D72113`
- Blue (info/Business): `#0A47FF`
- Amber (surge high): `#FFCB00`

### Example Component Prompts
- "Create a SwiftUI Uber 'Where to?' bottom sheet: 16pt top corner radius, `#FFFFFF` background, 36×4pt `#E5E5E5` grabber centered 6pt from top. Below: 56pt tall input with `#F6F6F6` background, leading 'From' dot and 'To' dot connected by dashed line, placeholder 'Where to?' in Uber Move Text 18pt weight 500 `#000000`. Below that: payment-method chip showing 'Personal · Visa ·· 4242' with trailing chevron."
- "Build the primary Confirm CTA: 56pt tall full-width button, `#000000` background, `#FFFFFF` Uber Move Text 17pt weight 500 label reading 'Confirm Uber X'. 8pt corner radius. Pressed: scale 0.98, background `#2F2F2F`, medium haptic."
- "Design a ride option card: 72pt tall selectable row, 56×56pt leading car illustration, center stacked with 'UberX' in Uber Move Text 16pt weight 500 and '3 min away · 4 seats' in Uber Move Mono 13pt weight 400 `#757575`, trailing '$14.82' in Uber Move Mono 16pt weight 500. Selected state: 2pt `#000000` border and `#F6F6F6` background."
- "Create the driver-arrived beacon: pulsing green `#05A357` circles emanating from a small black car icon on the map. Three concentric rings at 30% / 20% / 10% opacity, scale from 1.0 to 2.4 over 1.8s, staggered 0.6s. Small Uber Move Mono badge '2 min' in `#000000` with white background chip above the car."
- "Build the active trip card inside the bottom sheet: 48pt circular driver photo, driver name 'Marcus T.' Uber Move Text 16pt weight 500, car line 'Black Toyota Camry' Uber Move Text 14pt weight 400 `#545454`, license plate '7ABC123' in Uber Move Mono 14pt weight 500 inside a `#F6F6F6` pill. Three 44pt circular `#F6F6F6` action buttons for Message, Call, Share Trip."

### Iteration Guide
1. Map fills 60-80% of the viewport — everything else is a layer on top
2. Primary CTA is always black (`#000000`), never a brand color
3. Use Uber Move Mono for every number, ETA, fare, and address — the data-precision cue
4. Bottom sheet has 16pt top corners and a visible grabber — the signature anchor
5. Green is "your driver" / "confirmed"; red is "surge" / "cancel" — never decorative
6. Float map controls above the current sheet detent with safe-area awareness
7. Dark mode uses `#0C0C0C` canvas (not pure black) to match the dark map's OLED-friendly tone
8. Shadows whisper — the sheet's upward shadow is the most prominent moment, everything else is 12% or less
