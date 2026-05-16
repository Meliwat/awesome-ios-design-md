# Design System Inspiration of Lyft (iOS)

## 1. Visual Theme & Atmosphere

Lyft's iOS app is a full-screen map with a friendly card floating over it. There is no tab bar — the map *is* the app. A live, edge-to-edge map fills the entire viewport; everything else (the "Where to?" search, the ride-type picker, the ETA, the driver card) lives in a rounded bottom sheet that slides up from below. The design philosophy is "the world is the canvas, the controls come to you": you should always see where you are, and the interface arrives as a soft, springy panel exactly when you need to make a choice.

The accent is Lyft Pink (`#FF00BF`) — a vivid, unmistakable magenta lifted from the brand mark. It is reserved for the single most important action on screen: the "Select Lyft" / "Confirm" CTA, the active ride-type selection, the pickup pin, and key brand moments. Everything else is a calm, near-neutral system: white surfaces, soft gray dividers, near-black text. The pink is loud precisely because the rest of the UI is quiet — when it appears, it means "this is the thing to tap." Lyft ships a full dark mode (`#11111F` canvas) for night rides, and the pink holds its punch on both.

Typography is Inter with a deliberately *rounded* geometric feel — soft, approachable, friendly (Lyft's brand voice is warm, not corporate). Weights run 400 / 600 / 700. The hierarchy is sheet-driven: the sheet title ("Choose a ride", "Confirm pickup") sits at 22pt weight 700, ride-type names at 17pt weight 700, prices at 17pt weight 700 right-aligned, the big "Where to?" prompt at 20pt weight 600, and a recurring 14pt secondary gray for ETAs, descriptions, and capacity ("4 seats · 3 min away"). Generously rounded corners (16-28pt) everywhere reinforce the soft, pill-and-bubble personality.

**Key Characteristics:**
- Full-screen edge-to-edge map — no tab bar; the map is the primary surface
- Rounded bottom sheet — every control arrives in a soft, springy panel that slides up
- Lyft Pink (`#FF00BF`) as the single accent — Confirm CTA, active ride-type, pickup pin
- Very rounded geometry — 16-28pt radii everywhere; pill buttons; bubble cards
- "Where to?" search bar — the persistent entry point floating over the map
- Ride-type selector cards — Wait & Save / Standard / XL / Lux Black, each with icon + price + ETA
- Pickup pin — a pink map pin with a soft drop shadow and a precise tip
- ETA strings — "3 min away", "12:45 arrival" in calm secondary gray
- Full dark mode (`#11111F`) for night rides — pink holds on both themes
- Rounded bottom-sheet spring — the panel's signature springy entrance and detent snapping

## 2. Color Palette & Roles

### Primary
- **Lyft Pink** (`#FF00BF`): Primary CTA ("Select Lyft", "Confirm"), active ride-type selection, pickup pin, brand moments.
- **Pink Pressed** (`#D500A0`): Active/pressed state for the pink CTA.
- **Pink Tint** (`#FFE5F7`): Light wash behind the selected ride-type row and pink confirmation chips (light mode).

### Canvas & Text — Light
- **Canvas White** (`#FFFFFF`): Sheet background, cards, primary surfaces.
- **Surface** (`#F4F4F7`): Grouped backgrounds, search field fill, segmented track, skeletons.
- **Surface Deep** (`#E9E9EF`): Pressed state on surface, inactive segments.
- **Divider** (`#E5E5EA`): Hairline dividers between sheet rows and sections.
- **Text Primary** (`#11111F`): Sheet titles, ride-type names, prices, primary UI text — near-black ink.
- **Text Secondary** (`#6B6B7B`): ETAs, descriptions, capacity, metadata.
- **Text Tertiary** (`#9A9AA8`): Disabled labels, very low-emphasis captions.

### Canvas & Text — Dark
- **Dark Canvas** (`#11111F`): Sheet background, primary surfaces at night.
- **Dark Surface** (`#1C1C2B`): Grouped backgrounds, search field fill, segmented track.
- **Dark Surface Deep** (`#26263A`): Pressed state on dark surface.
- **Dark Divider** (`#2C2C40`): Hairline dividers in dark mode.
- **Dark Text Primary** (`#FFFFFF`): Sheet titles, ride-type names, prices.
- **Dark Text Secondary** (`#A0A0B4`): ETAs, descriptions, metadata.
- **Pink Tint Dark** (`#33102B`): Selected ride-type wash on dark.

### Map
- **Route Line** (`#FF00BF`): The active trip route polyline drawn on the map (pink).
- **Pickup Pin** (`#FF00BF`): The pickup location marker.
- **Destination Pin** (`#11111F`): The drop-off marker (near-black for contrast against pink pickup).

### Semantic
- **Success Green** (`#00A862`): Trip-confirmed state, payment success.
- **Rating Gold** (`#FFB400`): Driver star rating fill.
- **Warning Amber** (`#F5A623`): Surge / "prices are higher", limited availability.
- **Error Red** (`#E5484D`): Payment failures, ride-cancel errors.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (with a rounded, friendly geometric character — Lyft's brand voice)
- **Numerals**: Inter tabular figures for prices, ETAs, fare estimates (`font-variant-numeric: tabular-nums`)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Text', Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Sheet Title | Inter | 22pt | 700 | 1.2 | -0.3pt | "Choose a ride", "Confirm pickup" |
| Where To Prompt | Inter | 20pt | 600 | 1.25 | -0.2pt | The big "Where to?" search prompt |
| Ride-Type Name | Inter | 17pt | 700 | 1.25 | -0.1pt | "Standard", "Lux Black", "Wait & Save" |
| Price | Inter | 17pt | 700 | 1.2 | 0pt | Fare estimate, right-aligned, tabular |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Descriptions, trip detail copy |
| Button (Primary) | Inter | 17pt | 700 | 1.0 | 0pt | Pink "Select Lyft", "Confirm" |
| Driver Name | Inter | 18pt | 700 | 1.2 | -0.1pt | On the en-route driver card |
| Meta / ETA | Inter | 14pt | 400 | 1.3 | 0pt | "3 min away", "4 seats", "12:45 arrival" |
| Ride-Type Sub | Inter | 13pt | 400 | 1.3 | 0pt | "Affordable rides, all to yourself" |
| Badge | Inter | 11pt | 700 | 1.2 | 0.3pt | "FASTEST", "VALUE", "PRIORITY" |
| Caption | Inter | 12pt | 400 | 1.3 | 0pt | Fine print, fare-breakdown labels |
| Pin Label | Inter | 13pt | 700 | 1.0 | 0pt | Floating label on map pins / ETA bubble |

### Principles
- **Sheet-driven hierarchy**: The biggest type is the sheet title; there are no hero headlines because the map is the hero — type guides the decision in the panel
- **Friendly, rounded tone**: Inter is chosen for its approachable geometry; pair with `SF Pro Rounded` as the system fallback to keep the soft personality
- **Weights at 400 / 600 / 700**: Regular for body and ETAs, semibold for the "Where to?" prompt, bold for titles, ride-type names, prices, and the CTA
- **Tabular figures for fares**: Prices, ETAs, and fare breakdowns use tabular Inter so the right-aligned price column stays aligned across ride types
- **Quiet secondary gray**: ETAs, capacity, and descriptions sit in `#6B6B7B` (or `#A0A0B4` on dark) so the ride-type name and price lead

## 4. Component Stylings

### Buttons

**Primary CTA ("Select Lyft" / "Confirm pickup" / "Confirm")**
- Background: `#FF00BF`
- Text: `#FFFFFF`, Inter 17pt weight 700
- Height: 56pt (full-width sheet CTA)
- Corner radius: 16pt (soft, not full-pill — the sheet CTA is a rounded rect)
- Pressed: background `#D500A0`, scale 0.98
- Disabled: background `#E5E5EA` (light) / `#26263A` (dark), text tertiary

**Secondary / Neutral ("Edit", "Add a stop")**
- Background: `#F4F4F7` (light) / `#1C1C2B` (dark)
- Text: `#11111F` (light) / `#FFFFFF` (dark), Inter 16pt weight 600
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 16pt
- Pressed: surface-deep

**Pill Button (map overlay — "Recenter", "Safety")**
- Background: `#FFFFFF` (light) / `#1C1C2B` (dark) with a soft shadow
- Text/icon: `#11111F` / `#FFFFFF`
- Shape: full pill (radius 500pt), 40pt tall, floats over the map
- Pressed: subtle scale 0.96

**Icon Button (map controls — locate, layers, back)**
- Circular, 44pt, white (light) / dark-surface (dark) with soft shadow
- Glyph 20pt `#11111F` / `#FFFFFF`
- Floats over the map at the screen edges

### Cards & Containers

**Bottom Sheet (the core container)**
- Background: `#FFFFFF` (light) / `#11111F` (dark)
- Top corners: 28pt radius (very rounded — the signature soft panel)
- Grab handle: 36pt × 4pt rounded bar, `#E5E5EA` / `#2C2C40`, centered, 8pt from top
- Detents: collapsed (peek), medium (ride list), large (full)
- Shadow: large soft upward shadow so it floats above the map
- Slides up with a spring; snaps between detents

**Ride-Type Selector Card / Row**
- Layout: leading vehicle illustration (~56pt) → name + capacity/ETA sub → trailing price
- Height: ~72pt
- Selected: `#FFE5F7` (light) / `#33102B` (dark) wash, 2pt `#FF00BF` rounded border (16pt radius), name and price weight 700
- Unselected: transparent, 1pt `#E5E5EA` divider between rows
- Tap: row springs subtly, selection ring animates in
- Optional badge ("FASTEST", "VALUE") as a small pill near the name

**"Where to?" Search Bar**
- Floating rounded bar over the map (or top of the collapsed sheet)
- Background: `#FFFFFF` (light) / `#1C1C2B` (dark), soft shadow
- Height: 56pt, corner radius 16pt
- Leading: a small pink square/dot accent (saved-place dot) or `magnifyingglass`
- Text: "Where to?" Inter 20pt weight 600 `#6B6B7B`

**Driver / En-Route Card**
- Appears in the sheet once a driver is matched
- Driver avatar (56pt circle) + name (18pt weight 700) + rating (gold star + number) + vehicle/plate
- Big rounded "Contact" / "Share trip" pill actions
- ETA bubble pinned on the map showing "3 min"

**Map Pins**
- **Pickup pin**: Lyft Pink teardrop with a white center dot, soft drop shadow, precise tip
- **Destination pin**: near-black teardrop, same silhouette
- **Vehicle markers**: small rounded car glyphs that move along the route

### Navigation

**No Tab Bar — Map + Sheet Model**
- The app has **no bottom tab bar**. Primary navigation is: full-screen map + a bottom sheet whose detents and contents change by trip state (search → choose ride → confirm pickup → en route → rate).
- A hamburger/avatar button floats top-left over the map for the menu (account, history, payment).
- Back/dismiss is a circular icon button top-left within flows.

**Sheet State Progression**
1. **Idle**: collapsed sheet with "Where to?" + saved places
2. **Choose a ride**: medium sheet, ride-type list, pink "Select Lyft" CTA
3. **Confirm pickup**: map centers on pickup pin, sheet shows address + "Confirm pickup"
4. **En route**: driver card, ETA, route polyline on map
5. **Rate**: post-trip rating + tip in the sheet

### Input Fields

**Destination Search Field (expanded)**
- Background: `#F4F4F7` (light) / `#1C1C2B` (dark)
- Height: 52pt, corner radius 14pt
- Leading dot accent (pink for "pickup", dark for "destination") in a vertical from/to stack
- Placeholder: Inter 16pt weight 400 secondary
- Focus: 2pt `#FF00BF` border

**Saved-Place Row**
- Leading rounded icon chip (Home / Work / star), label + address
- 56pt tall, full-row tappable, divider between rows

### Distinctive Components

**Ride-Type Selector Sheet**
- The signature decision surface. A vertically scrolling list of ride options inside the bottom sheet: **Wait & Save**, **Standard**, **XL**, **Lux Black** (and others).
- Each row: vehicle illustration, name (17pt weight 700), one-line description (13pt secondary), capacity + ETA ("4 seats · 3 min"), and a right-aligned tabular price (17pt weight 700).
- The selected row gets the pink-tint wash + 2pt pink rounded border; the others stay flat.
- A persistent pink "Select [Type]" CTA at the bottom updates its label to the chosen ride.
- Switching selection springs the ring from the old row to the new with a light haptic.

**Rounded Bottom-Sheet Spring**
- The sheet enters and snaps between detents with a spring (response ~0.45, damping ~0.85) — overshooting slightly then settling, giving Lyft its signature soft, bouncy panel feel.
- Dragging the grab handle resists past the large detent and snaps to the nearest detent on release.

**Pickup Pin Drop**
- When "Confirm pickup" appears, the pink pin drops onto the map with a small bounce and a soft shadow that scales with the drop — a tactile "you are here" moment.

**Map ETA Bubble**
- A small white (or dark) rounded bubble tethered to the vehicle marker showing the live ETA ("3 min"), updating as the car moves.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Sheet content margin: 20pt horizontal (a touch wider than typical — the soft, generous feel)
- Ride-type row spacing: rows touch, separated by hairline dividers or selection rings

### Grid & Container
- The map is full-bleed, edge to edge, behind everything
- The sheet spans the full width, pinned to the bottom, with rounded top corners
- Map overlay controls float at screen edges with 16pt insets
- Ride-type list is a single full-width column inside the sheet

### Whitespace Philosophy
- **The map breathes; the sheet is dense-but-soft**: The map is uncluttered (just pins + route); the sheet packs the decision compactly but with generous 20pt margins and rounded forms so it never feels cramped
- **Controls come to you**: Whitespace is mostly the map itself — UI only occupies the sheet and a few floating pills, keeping the world visible
- **Rounded everything**: The soft radii (16-28pt) are the breathing room — corners are never sharp

### Border Radius Scale
- Soft (12-14pt): Search fields, small inputs
- Comfortable (16pt): Buttons, ride-type selection rings, secondary buttons
- Large (20-24pt): Cards, map overlay panels
- Sheet (28pt): The bottom sheet's top corners — the signature very-rounded panel
- Pill (500pt): Map overlay pills (Recenter, Safety), badges
- Circle (50%): Avatars, icon buttons, saved-place chips, vehicle markers

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Map base, rows inside the sheet |
| Floating Control (Level 1) | `rgba(17,17,31,0.16) 0 4px 12px` | Map overlay pills + icon buttons |
| Sheet (Level 2) | `rgba(17,17,31,0.20) 0 -10px 40px` | The bottom sheet floating above the map |
| Pin Shadow | `rgba(17,17,31,0.30) 0 6px 12px` (scales on drop) | Pickup / destination map pins |
| Overlay Dim | `rgba(17,17,31,0.45)` | Modal scrim behind full-screen flows |

**Shadow Philosophy**: Shadows are soft and cool (mixed from the `#11111F` ink), tuned so floating elements clearly separate from the live map without looking heavy. The most important shadow is the bottom sheet's large upward glow — it must read as a distinct soft panel hovering over the moving map. Map pins carry a focused drop shadow that briefly scales during the pickup-pin drop. On dark mode shadows deepen slightly so the sheet still separates from the night map.

### Motion
- **Sheet entrance / detent snap**: spring (response ~0.45, damping ~0.85), slight overshoot then settle
- **Ride-type select**: selection ring springs from old to new row ~250ms; light haptic; price/name bolden
- **Pickup pin drop**: pin falls with a small bounce; shadow scales 0.6 → 1.0 as it lands; soft haptic
- **Map recenter**: camera eases back to the user with a 0.4s ease-in-out
- **CTA press**: scale 0.98 → 1.0 spring
- **ETA bubble update**: number cross-fades when the ETA changes (no layout jump — tabular figures)

## 7. Do's and Don'ts

### Do
- Make the map full-screen and edge-to-edge — it is the primary surface
- Put every control in a rounded bottom sheet that slides up with a spring
- Use Lyft Pink (`#FF00BF`) only for the primary CTA, active ride-type, and the pickup pin
- Use very rounded geometry — 28pt sheet corners, 16pt buttons, pill map controls
- Render the ride-type selector as a list with vehicle icon, name, sub, ETA, and a right-aligned tabular price
- Give the selected ride-type a pink-tint wash + 2pt pink rounded border
- Support a full dark mode (`#11111F` canvas) for night rides — keep pink at full strength
- Use tabular figures for prices and ETAs so the price column aligns
- Keep secondary info (ETA, capacity) in calm gray so name + price lead

### Don't
- Don't add a bottom tab bar — Lyft navigates via map + sheet, not tabs
- Don't cover the map with opaque chrome — controls float or live in the sheet
- Don't use pink for decoration, dividers, or body text — it's the single action color
- Don't use sharp corners — the soft, rounded personality is core to the brand
- Don't make the sheet appear without the spring — the bouncy entrance is the signature feel
- Don't use proportional figures for fares — the right-aligned price column will misalign
- Don't let the destination pin be pink — pink is the pickup; destination is near-black
- Don't drop the dark theme — night rides are a primary use case
- Don't crowd the map with more than pins + the active route polyline

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Sheet medium detent shows ~3 ride types; smaller vehicle illustrations |
| iPhone 13/14/15 | 390pt | Standard sheet detents; ~4 ride types visible at medium |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the floating top-left menu button |
| iPhone 15/16 Pro Max | 430pt | Larger sheet, ~5 ride types visible; bigger map viewport |
| iPad | 768pt+ | Sheet becomes a fixed left rail panel over a full-bleed map; ride list in the rail |

### Dynamic Type
- Sheet title, ride-type names, body: full scale
- Prices and ETAs: scale but stay tabular and right-aligned
- Badges and pin labels: fixed (layout-sensitive)
- "Where to?" prompt: scales to a max, never wraps

### Orientation
- Map + sheet flow: **portrait-locked** on iPhone
- iPad: supports landscape with the rail-panel + full map layout

### Touch Targets
- Primary CTA: 56pt tall, full sheet width
- Ride-type rows: full-row tappable, ~72pt
- Map overlay icon buttons / pills: 44pt / 40pt minimum
- Grab handle: small visually but a generous ~44pt drag zone
- Saved-place rows: 56pt, full-row tappable

### Safe Area Handling
- Top: floating menu + map controls respect the safe area and Dynamic Island
- Bottom: the sheet's content and CTA respect the home indicator; collapsed peek clears it
- The map renders behind the safe areas (full-bleed) with controls inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas (sheet): `#FFFFFF`
- Light surface: `#F4F4F7`
- Light divider: `#E5E5EA`
- Light text primary: `#11111F`
- Light text secondary: `#6B6B7B`
- Dark canvas (sheet): `#11111F`
- Dark surface: `#1C1C2B`
- Dark text primary: `#FFFFFF`
- Dark text secondary: `#A0A0B4`
- Lyft Pink (action): `#FF00BF`
- Pink pressed: `#D500A0`
- Pink tint (light): `#FFE5F7`

### Example Component Prompts
- "Create the SwiftUI Lyft ride-type selector row: ~72pt tall, leading 56pt vehicle illustration, then a column with name 'Standard' Inter 17pt weight 700 #11111F and sub '4 seats · 3 min away' Inter 14pt weight 400 #6B6B7B, and a right-aligned price '$18.40' Inter 17pt weight 700 tabular. When selected, the row gets a #FFE5F7 wash and a 2pt #FF00BF rounded border (16pt radius); selecting springs the ring in with a light haptic."
- "Build the Lyft primary CTA: full-width 56pt button, background #FF00BF, label 'Select Lyft' Inter 17pt weight 700 #FFFFFF, 16pt corner radius. Pressed: background #D500A0, scale 0.98. The label updates to the chosen ride type."
- "Design the Lyft bottom sheet: top corners 28pt radius, background #FFFFFF (light) / #11111F (dark), a 36×4pt rounded grab handle in #E5E5EA centered 8pt from the top, a large soft upward shadow so it floats over a full-screen map. It enters and snaps between collapsed/medium/large detents with a spring (response 0.45, damping 0.85, slight overshoot)."
- "Create the Lyft pickup pin: a Lyft-Pink (#FF00BF) teardrop marker with a white center dot, a precise bottom tip, and a soft cool drop shadow rgba(17,17,31,0.30) 0 6px 12px. On 'Confirm pickup' it drops onto the map with a small bounce and the shadow scales 0.6 → 1.0 as it lands."
- "Build the 'Where to?' search bar: a floating rounded bar over the map, 56pt tall, 16pt corner radius, background #FFFFFF (light) / #1C1C2B (dark) with a soft shadow, a small pink saved-place dot leading, and 'Where to?' in Inter 20pt weight 600 #6B6B7B."

### Iteration Guide
1. The map is full-screen and edge-to-edge — there is NO tab bar; controls live in a rounded bottom sheet
2. Lyft Pink `#FF00BF` is the single action color: primary CTA, active ride-type, pickup pin — nothing decorative
3. Geometry is very rounded — 28pt sheet corners, 16pt buttons, pill map controls; never sharp
4. The bottom sheet enters and snaps between detents with a slightly overshooting spring — that bounce is the signature feel
5. The ride-type selector is the key decision surface: icon + name + sub + ETA + right-aligned tabular price; selected row gets pink-tint + 2pt pink border
6. Ship a full dark mode (`#11111F` canvas) for night rides — pink stays at full strength on both
7. Pickup pin is pink; destination pin is near-black `#11111F` for contrast
8. Tabular figures for fares and ETAs; secondary info stays calm gray so name + price lead
