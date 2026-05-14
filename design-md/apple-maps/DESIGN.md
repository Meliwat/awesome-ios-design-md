# Design System Inspiration of Apple Maps (iOS)

## 1. Visual Theme & Atmosphere

Apple Maps is the canonical reference for what a system-native iOS map should feel like — a soft cream-paper canvas, generous translucent search card, and the precise dance of SF Symbols, native blurs, and just-right typography that defines every first-party Apple app. The map itself is the brand: the cartography uses a warm parchment beige (`#F6F1E6`) over land, soft `#B9D9EB` blues over water, slate roads, and the carefully chosen Apple transit colors (BART orange, MTA green, MUNI gold) on subway and rail lines. Where Google Maps reaches for saturated category icons and dense POI clutter, Apple Maps reaches for whitespace — the map breathes, labels render only at the right zoom level, and the UI chrome floats above the cartography as a translucent companion rather than a frame.

The accent is the system iOS Blue (`#007AFF` adjacent — Apple Maps uses a slightly more saturated `#0A84FF` in their direction lines and Look Around button). It carries the directions polyline, the "current location" puck (a pulsing blue dot with a directional cone), and primary actions inside the search card. Categories are color-coded with restraint: Food orange `#FF9500`, Coffee brown `#A5694C`, Drinks teal `#5AC8FA`, Shopping yellow `#FFCC00`, Parking purple `#AF52DE`, Transit varies by line. The signature Maps Red `#FF3B30` is reserved for the dropped pin and "place card" header — it's the same iOS system red, never a custom hue.

Typography is SF Pro — Display for nav titles and place names, Text for body and list rows, Compact for the place-card subtitles where width is precious. Apple uses tabular figures aggressively on times, distances, and addresses ("0.4 mi · 2 min"). The weight stack runs Regular (400), Medium (500), Semibold (600), and Bold (700) — the system stack used everywhere else in iOS. The Maps app is one of the most disciplined demonstrations of San Francisco at scale: every label on the map, every row in the place card, every time estimate uses the system face at the exact metrics that ship in iOS itself.

**Key Characteristics:**
- Map canvas: warm cream/parchment `#F6F1E6` over land with `#B9D9EB` water — the cartography IS the brand
- System iOS Blue `#0A84FF` for directions polyline, current-location puck, primary CTAs
- Maps Red `#FF3B30` for the dropped pin and the place-card identity bar
- Floating translucent search card (`.regularMaterial` blur) — sliding bottom sheet with three detents
- "Where to?" placeholder, search field corner radius matches Apple's standard 13pt
- Translucent floating buttons (compass, location, 3D view toggle) — top-right vertical stack, `.regularMaterial` blur, circular
- Category color-coding: Food `#FF9500`, Coffee `#A5694C`, Drinks `#5AC8FA`, Shopping `#FFCC00`, Parking `#AF52DE`
- The current-location puck — pulsing blue dot with a directional cone for heading
- SF Pro at the system stack — Display, Text, Compact variants; tabular figures on every distance/time
- 3D building extrusions when zoomed past street level — a signature Apple Maps detail
- Subway/transit colors: BART orange, MTA green, NYC subway color per line — accurate to the agency

## 2. Color Palette & Roles

### Primary
- **Maps Blue** (`#0A84FF`): Directions polyline, current-location puck, Look Around button, primary CTAs inside the place card. This is the iOS system Blue, slightly more saturated than the generic `systemBlue` (Apple Maps boosts it for legibility on cream canvas).
- **Maps Blue Pressed** (`#0967D2`): Active state on direction tap and primary buttons.
- **Maps Red** (`#FF3B30`): Dropped pin, place-card identity bar, "Report an issue" labels. The iOS system Red — unchanged.

### Map Cartography
- **Map Cream** (`#F6F1E6`): Land surface — the signature warm parchment beige.
- **Map Water** (`#B9D9EB`): Oceans, rivers, lakes — soft cool blue.
- **Map Park Green** (`#D6E5C9`): Parks, open green space.
- **Map Road** (`#FFFFFF`): Major roads — bright white over cream.
- **Map Road Outline** (`#D5CFC0`): Minor road outlines, thin strokes.
- **Map Highway** (`#FFD966`): Highway fill (US convention), thin yellow.
- **Map Building 2D** (`#EFE9DD`): Building footprints at street zoom.
- **Map Building 3D** (`#E6DFD0`): Extruded buildings, slightly cooler.
- **Map Label** (`#594F3F`): Map label text (city, neighborhood, street names) — a warm dark slate.

### Category Colors
- **Food Orange** (`#FF9500`): Restaurants, food category pins.
- **Coffee Brown** (`#A5694C`): Coffee shops, cafés.
- **Drinks Teal** (`#5AC8FA`): Bars, nightlife.
- **Shopping Yellow** (`#FFCC00`): Stores, retail.
- **Parking Purple** (`#AF52DE`): Parking lots, garages.
- **Hotel Indigo** (`#5856D6`): Lodging.
- **Health Pink** (`#FF2D55`): Hospitals, medical.
- **Gas Red** (`#FF3B30`): Gas stations.
- **EV Charge Green** (`#34C759`): EV charging.

### Transit Colors (Match Real Agency)
Apple Maps uses each transit agency's actual line colors — these are a few canonical examples:
- **BART Orange** (`#F69E13`), **BART Yellow** (`#FFDE0A`), **BART Red** (`#ED1C24`)
- **MTA Green** (`#00933C`), **MTA Yellow** (`#FCCC0A`), **MTA Red** (`#EE352E`), **MTA Blue** (`#0039A6`)
- **MUNI Red** (`#BD2D38`), **MUNI Gold** (`#FFCC33`)
- **NYC Subway L** (`#A7A9AC` Gray), **TFL Tube Bakerloo** (`#894E24`), etc.

### Canvas, Surfaces & Dividers (UI Chrome)
- **Card Canvas** (`#FFFFFF`): Place card, search card, sheet backgrounds.
- **Surface Gray** (`#F2F2F7`): Search field fill, secondary surface, category chip backgrounds.
- **Surface Gray 2** (`#E5E5EA`): Pressed states on list rows.
- **Divider** (`#C7C7CC`): iOS standard hairline (0.5pt at @2x renders as 1px).
- **Blur Material** (`.regularMaterial`): The translucent map controls and sticky card headers — system blur, NOT a custom value.

### Text
- **Ink** (`#000000`): Primary text — place names, nav titles. Apple uses pure black on Maps for crispness against cream.
- **Secondary** (`#3C3C43` at 60% opacity): Subtitles, addresses, "0.4 mi · 2 min", category labels.
- **Tertiary** (`#3C3C43` at 30% opacity): Disabled labels, "No reviews yet".
- **Quaternary** (`#3C3C43` at 18% opacity): Placeholder text, separator labels.
- **Inverse White** (`#FFFFFF`): Text on saturated buttons.

### Semantic
- **Success Green** (`#34C759`): Route ETA badge (when on time), "Open now" indicator.
- **Warning Amber** (`#FF9500`): "Limited service", congestion ahead, parking limited.
- **Error Red** (`#FF3B30`): "Route closed", construction, severe traffic.
- **Info Blue** (`#0A84FF`): Hyperlinks inside the place card, "Open in Apple Maps" actions.

### Dark Mode
Apple Maps dark mode is one of the showcases of iOS dark — a true cool charcoal canvas with brighter Maps Blue.
- **Dark Canvas** (`#000000`): Beneath everything.
- **Dark Map Land** (`#2A2826`): Warm-toned dark gray over land — the dark equivalent of the cream paper.
- **Dark Map Water** (`#1C2638`): Deep navy water.
- **Dark Map Park** (`#2C3A2A`): Dark forest green.
- **Dark Card Surface** (`#1C1C1E`): Place card, sheets.
- **Dark Surface 2** (`#2C2C2E`): Pressed states.
- **Dark Divider** (`#38383A`): Hairlines.
- **Dark Text Primary** (`#FFFFFF`): Primary text on cards.
- **Dark Text Secondary** (`#EBEBF5` at 60% opacity): Secondary text.
- **Maps Blue (Dark)** (`#409CFF`): Slightly brighter blue for dark mode legibility.

## 3. Typography Rules

### Font Family
- **Primary Display**: `SF Pro Display` for sizes ≥20pt (place card title, large nav)
- **Primary Text**: `SF Pro Text` for body, rows, metadata
- **Primary Compact**: `SF Pro Compact` for narrow secondary labels (place card subtitle when condensed)
- **Weights available**: Regular (400), Medium (500), Semibold (600), Bold (700), Heavy (800) — Heavy reserved for the place-card title only
- **Map labels**: SF Pro Text Regular at sizes from 9pt to 18pt depending on zoom and importance
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', sans-serif`
- **Web/marketing Google Fonts substitute**: `Inter` (closest neutral grotesque) — falls back gracefully
- **Tabular figures**: Mandatory on all distances (`0.4 mi`), times (`2 min`), ETAs (`6:14 PM`), prices (`$$`)

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Place Card Title | SF Pro Display | 28pt | 700 (Bold) | 1.15 | -0.4pt | "Tartine Bakery" |
| Place Card Subtitle | SF Pro Text | 15pt | 400 | 1.3 | 0pt | "Bakery · French · $$" |
| Hero Search Placeholder | SF Pro Display | 17pt | 400 | 1.3 | 0pt | "Search Maps" |
| Nav Title (Inline) | SF Pro Display | 17pt | 600 | 1.2 | -0.2pt | "Directions" |
| Section Header (Card) | SF Pro Text | 13pt | 600 | 1.2 | 0.4pt UPPERCASE | "HOURS", "OVERVIEW" |
| List Row Title | SF Pro Text | 17pt | 400 | 1.3 | 0pt | Search suggestion row |
| List Row Subtitle | SF Pro Text | 13pt | 400 | 1.2 | 0pt | Suggestion address |
| ETA Time | SF Pro Display | 22pt | 600 | 1.1 | -0.2pt tnum | "6:14 PM" |
| ETA Duration | SF Pro Display | 17pt | 500 | 1.2 | 0pt tnum | "32 min · 1.2 mi" |
| Direction Step Title | SF Pro Display | 22pt | 600 | 1.2 | -0.2pt | "Turn right onto Market St" |
| Direction Step Distance | SF Pro Text | 17pt | 500 | 1.3 | 0pt tnum | "0.4 mi" |
| Category Pill Label | SF Pro Text | 14pt | 500 | 1.0 | 0pt | "Food", "Coffee" |
| Map Label (Major City) | SF Pro Display | 14pt | 600 | 1.0 | 0pt | "San Francisco" |
| Map Label (Neighborhood) | SF Pro Text | 12pt | 500 | 1.0 | 0pt | "Mission District" |
| Map Label (Street) | SF Pro Text | 10pt | 400 | 1.0 | 0pt | "Market St" |
| Button Label | SF Pro Text | 17pt | 600 | 1.0 | 0pt | "Directions", "Call" |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | 0.1pt | (No bottom tab in Apple Maps) |
| Caption | SF Pro Text | 12pt | 400 | 1.3 | 0pt | Photo credits, fine print |
| Transit Line Label | SF Pro Text | 12pt | 700 (Bold) | 1.0 | 0pt | "BART" inside the colored badge |

### Principles
- **System face, system stack**: Apple Maps uses SF Pro at the exact metrics shipped with iOS. Don't substitute a custom face — the goal is to feel system-native.
- **Tabular figures everywhere numeric**: distances, times, ETAs, prices, phone numbers
- **Display for headlines, Text for body, Compact for narrow secondary**: this is the canonical Apple optical hierarchy
- **Letter spacing tightens at display sizes** (-0.4pt at 28pt) and relaxes to 0 at body
- **Heavy (800) reserved for place-card title** — almost nothing else uses Heavy
- **Semibold (600) is the default emphasis weight**, not Bold (700)
- **Black `#000000` for primary text on cream canvas** — Apple uses pure black for crispness; the cream background warms it visually
- **Section headers in 13pt UPPERCASE Semibold with 0.4pt tracking** — the canonical iOS section-header pattern
- **Dynamic Type respected on everything except map labels** (zoom-controlled), tab labels, and ETA numbers (layout-sensitive)

## 4. Component Stylings

### The Search Card (Sliding Bottom Sheet — Hero Component)

The defining Apple Maps interaction. A translucent card that slides up over the map with three detents.

- **Width**: full screen
- **Detents**: small (88pt — just the search bar visible), medium (~50% of screen), large (full-screen modal)
- **Background**: `.regularMaterial` blur over the underlying map — a translucent frosted layer
- **Top corner radius**: 10pt (medium and large detents); 0pt on small (just the search bar)
- **Drag indicator (grabber)**: 36pt × 5pt rounded rectangle in `#3C3C43` at 30% opacity, 8pt below the top edge
- **Search field**:
  - Full width minus 32pt margins
  - Height: 44pt
  - Background: `#F2F2F7` (Surface Gray) inside the blur material
  - Corner radius: 10pt
  - Leading: 16pt SF Symbol `magnifyingglass`, color `#3C3C43` at 60% opacity
  - Text: SF Pro Display 17pt Regular `#000000`, placeholder "Search Maps" in `#3C3C43` at 30%
  - Trailing: microphone (`mic.fill`) and "Cancel" button when active
- **Below the search field**: horizontal category pills row — Food / Coffee / Drinks / Shopping / Parking / Transit
- **Below that (medium detent)**: Favorites, Recents, Guides — each in a 2-col grid of large rounded tiles

### Category Pill (Below Search Field)

- **Size**: 64pt wide × 88pt tall
- **Background**: `#FFFFFF` solid white card (NOT translucent — the category pills sit on the search card)
- **Corner radius**: 14pt
- **Icon**: 28pt SF Symbol filled, color matches category (e.g., `fork.knife` in `#FF9500` Orange for Food)
- **Label**: SF Pro Text 14pt Medium `#000000`, centered below icon, 1-line
- **Padding**: 16pt vertical, 8pt horizontal
- **Tap**: scale 0.96, opens category-filtered search

### The Dropped Pin

- **Shape**: SF Symbol `mappin.and.ellipse` 32pt OR custom pin shape — teardrop with a circle base
- **Color**: `#FF3B30` Maps Red (always, regardless of category — this is the universal "you tapped here" pin)
- **Shadow**: `rgba(0,0,0,0.3) 0 3px 6px` for legibility over varied cartography
- **Position**: anchored at the tip of the teardrop, exactly on the tapped coordinate
- **Drop animation**: appears from above with a 400ms ease-out bounce on long-press

### The Current-Location Puck

- **Container**: 22pt circle, blue `#0A84FF` solid fill with a 3pt white outer ring
- **Cone** (when heading is known): a 90° triangular cone extending from the puck in the heading direction, gradient from `rgba(10,132,255,0.4)` to transparent over 60pt
- **Pulse**: a subtle 30pt outer ring, opacity 0.2, scales 1.0 → 1.6 → 1.0 over 2000ms, infinite loop
- **Compass indicator**: a small chevron arrow inside the puck pointing in the direction of travel when navigating

### Place Card (Detail View)

When a user taps a pin or selects a search result, the place card slides up.

- **Top section** (60pt):
  - Place title: SF Pro Display 28pt Bold `#000000`, e.g., "Tartine Bakery"
  - Subtitle: SF Pro Text 15pt Regular `#3C3C43`@60%, "Bakery · French · $$"
  - "X" close button: top-right, 30pt circle with `xmark` SF Symbol, `.regularMaterial` background
- **Action button row** (60pt, horizontal scroll if needed):
  - "Directions": filled `#0A84FF` pill, white text, 44pt height, 24pt horizontal padding, SF Pro Text 17pt Semibold
  - "Call": outlined `#0A84FF` pill (1pt border, white background, blue text)
  - "Share": outlined gray pill
  - "Save": outlined gray pill with heart icon
- **Photo row** (when available): horizontal scroll of 16:9 photo cards, 80pt tall, 12pt corner radius
- **Info sections** (each below):
  - Hours: row with green "Open" badge or red "Closed" + closing time
  - Address: row with map-pin icon + tap to copy
  - Phone: row with phone icon + tappable number
  - Website: row with globe icon + domain
- **Reviews section** (separate UPPERCASE section header): star rating row + "23 reviews" + review cards if expanded
- **Sticky "Directions" bottom button**: 48pt height, `#0A84FF` background, 8pt corner radius, white text

### Map Controls (Floating Top-Right Stack)

Three circular translucent buttons stacked vertically in the top-right corner over the map:

- **Container**: each button is 44pt × 44pt circle
- **Background**: `.regularMaterial` blur — soft frosted white over the map
- **Icons**: SF Symbols, 18pt, color `#000000` (or `#FFFFFF` in dark mode)
- **Stack**: 8pt vertical gap between buttons
- **Position**: top-right with 16pt inset from edge, 16pt below safe area top
- **Buttons (top to bottom)**:
  1. **Layer picker** (`square.3.layers.3d`) — opens map style selector (Standard / Satellite / Hybrid / Transit)
  2. **Current location** (`location.fill` when locked, `location` when not) — centers map on user
  3. **3D toggle / Compass** (`map.fill` or `compass`) — toggles 3D buildings

### Directions Screen Components

**Step Card (Active Direction)**
- Position: top of screen below safe area, full width
- Height: 96pt
- Background: `.regularMaterial` blur over the map
- Layout: 56pt SF Symbol direction arrow (e.g., `arrow.turn.up.right`) in `#0A84FF` + step title in SF Pro Display 22pt Semibold + distance in SF Pro Text 17pt Medium
- The arrow rotates to match the actual direction (left, right, slight left, sharp right, etc.)

**ETA Bar (Bottom of Directions)**
- Position: sticky bottom, full width
- Height: 80pt
- Background: `.regularMaterial` blur with `#FFFFFF` 92% over content
- Layout: "32 min" SF Pro Display 22pt Semibold + "1.2 mi · arriving 6:14 PM" SF Pro Text 17pt Regular `#3C3C43`@60%
- Right side: "End" button — outlined red pill (`#FF3B30` border, white background, red text) for stopping navigation

**Directions Polyline**
- Stroke: `#0A84FF` Maps Blue, 5pt width
- Cap: rounded
- Animated dash on the upcoming step: 12pt dash, 6pt gap, animating at 2 dashes per second to convey direction

### Buttons

**Primary Filled CTA (Directions / Continue)**
- Background: `#0A84FF`
- Text: `#FFFFFF`, SF Pro Text 17pt Semibold
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 8pt (or 22pt for full pill on the action row)
- Height: 44pt
- Pressed: background `#0967D2`, scale 0.97
- Haptic: `.impactOccurred(.medium)`

**Outlined Secondary (Call / Share / Save)**
- Background: `#FFFFFF`
- Border: 1pt `#0A84FF`
- Text: `#0A84FF`, SF Pro Text 17pt Semibold
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 22pt (full pill)
- Height: 44pt

**Text-Only Action**
- No background or border
- Text: `#0A84FF`, SF Pro Text 17pt Regular
- 44pt hit target

### Distinctive Components

**Look Around Button**
- Position: appears on the place card when Look Around is available
- 56pt height pill, full-width content area
- Background: `#0A84FF` with white SF Symbol `binoculars.fill` 18pt + "Look Around" in SF Pro Text 17pt Semibold white
- Tap: launches the 360° street imagery view with a 600ms cross-fade

**Transit Line Badge**
- 24pt × 24pt rounded square in the transit line's exact agency color
- Centered letter or number (e.g., "L", "6", "N") in SF Pro Text 12pt Bold white
- Used on transit pins, route segments, and inside the place card for nearby stations

**Traffic Indicator (on roads)**
- Thin colored stripes drawn over the road geometry
- Green `#34C759`: light traffic
- Amber `#FF9500`: moderate
- Red `#FF3B30`: heavy
- Black `#000000`: closed

**Compass Glyph (Rotating)**
- Appears in the top-right map controls when the user rotates the map manually
- 44pt circle with `.regularMaterial`, animated arrow inside pointing north (red half) and south (white half)
- Tap to reset rotation to north-up with 400ms ease

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (iOS standard)
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56, 64
- Standard horizontal margin: 16pt on cards, 0pt on full-bleed map
- Section vertical gap: 24pt between sections in the place card; 16pt between rows

### Grid & Container
- Place card width: full screen
- Search card detents: 88pt / ~50% / full-screen
- Category pills: 64pt wide each, horizontal scroll
- Action button row: equal-width pills with 8pt gap

### Whitespace Philosophy
- **The map IS the canvas**: UI chrome floats above as translucent companions, never overwhelms the cartography
- **Generous breathing on cards**: 24pt section gaps; place card top section has 32pt above title
- **Sticky search card compresses the map** — at large detent, the user is effectively in a list view with the map peeking above; at small detent, the map is 88% of the screen
- **Map controls float top-right with 16pt inset** — never crowd the map edges

### Border Radius Scale
- Square (0pt): the map itself
- Standard (8pt): primary buttons, place-card sticky button
- Card (10pt): the search card top edge, search field background, place-card sections
- Medium (12pt): photo row cards, info row cards
- Soft (14pt): category pill backgrounds
- Comfortable (22pt): action row pill buttons (full pill at 44pt height)
- Circle (50%): map control buttons, current-location puck, close X button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Map itself |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 2px 8px` | Photo cards inside place card |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 12px` | Category pills, place-card top elevation |
| Overlay (Level 3) | `rgba(0,0,0,0.16) 0 6px 20px` | Dropped pin shadow |
| Sheet (Level 4) | `rgba(0,0,0,0.20) 0 -8px 24px` | Search card large detent |
| Blur Material | `.regularMaterial` | Search card background, map controls, step card, ETA bar — the signature Apple Maps treatment |

**Shadow Philosophy**: Apple Maps uses BLUR as its primary depth treatment, not shadow. The translucent `.regularMaterial` background on the search card, map controls, step card, and ETA bar lets the cartography show through faintly — this is what makes the UI feel like it floats. Drop shadows are used sparingly on photo cards and the dropped pin, where blur isn't appropriate. The combination of blur + cream-paper canvas + warm map colors creates the unmistakable Apple Maps atmosphere.

### Motion
- **Search card detent transition**: 350ms spring (damping 0.85, response 0.45s) — feels physical, like a sheet of paper sliding into place
- **Pin drop**: 400ms ease-out with a tiny bounce at the end — the pin "lands"
- **Current location lock**: when user taps location button, map pans to puck in 600ms ease-out, then triggers a brief 1.0 → 1.2 → 1.0 scale pulse on the puck
- **Category pill tap**: scale 0.96 instant, then 200ms ease-out spring back
- **Direction step transition**: 300ms cross-fade between consecutive step cards, with the arrow rotating in 250ms ease
- **Map style picker open**: 400ms shared-element transition — the small layer button morphs into the full picker sheet
- **Look Around launch**: 600ms cross-fade from map to 360° view; on close, reverse with a slight zoom
- **3D building extrude**: when zooming past street level, buildings raise from flat to 3D over 400ms ease
- **Pulse on current location**: 2000ms infinite — opacity 0.3 → 0.0 with scale 1.0 → 1.6

## 7. Do's and Don'ts

### Do
- Use the cream-paper canvas `#F6F1E6` for the map land surface — this is the most recognizable Apple Maps visual
- Use Maps Blue `#0A84FF` for the directions polyline, current-location puck, and primary CTAs
- Use Maps Red `#FF3B30` (the iOS system red) for the dropped pin and place-card identity
- Use `.regularMaterial` blur on the search card, map controls, step card, ETA bar — blur IS the depth system
- Use SF Pro at the system stack (Display, Text, Compact) — no custom face
- Use tabular figures on ALL distances, times, ETAs ("0.4 mi · 2 min · arriving 6:14 PM")
- Render the current-location puck as a 22pt blue dot with white ring and a pulsing 30pt outer ring
- Use the directional cone on the puck when heading is known
- Use category colors per the canonical set: Food `#FF9500`, Coffee `#A5694C`, Drinks `#5AC8FA`, Shopping `#FFCC00`, Parking `#AF52DE`
- Stack map controls top-right in a circular vertical pile with 8pt gaps
- Use real transit agency colors on transit line badges — BART orange, MTA green, etc.

### Don't
- Don't use a custom map style that overrides the cream `#F6F1E6` land color — it's signature
- Don't use a solid background on the search card — `.regularMaterial` blur is non-negotiable
- Don't put the current-location button at the bottom — it stacks in the top-right with the layer and 3D buttons
- Don't replace SF Pro with a custom font — Apple Maps is intentionally system-native
- Don't omit tabular figures from times and distances — column alignment matters
- Don't use Material elevation shadows (1pt + 3pt blur dual-shadow) — those are Google's; Apple uses single soft shadows and blur
- Don't recolor the dropped pin per category — the pin is always Maps Red, category color appears on the place icon inside the pin
- Don't render the place card with sharp corners — 10pt top corner radius is the standard
- Don't make the directions polyline thinner than 5pt or use a color other than `#0A84FF`
- Don't use Bold (700) for body text — Semibold (600) is the default emphasis weight in Apple Maps
- Don't omit the pulse animation on the current-location puck — it's the live-tracking signal

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Search card detents adjust — small is 80pt; category pills wrap to 1 row |
| iPhone 13/14/15 | 390pt | Standard layout — 6 category pills fit horizontally |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in search card top inset |
| iPhone 15/16 Pro Max | 430pt | 7-8 category pills visible, larger photo cards in place card |
| iPad Mini | 744pt | Search card becomes a left sidebar at 360pt wide; map takes remaining width |
| iPad Pro 11" | 834pt | Sidebar persistent, place card opens as right-side detail pane |
| iPad Pro 13" | 1024pt | Full split-view with sidebar + map + place card simultaneously |

### Dynamic Type
- Place card title, body, list rows, button labels: full scale
- Map labels, ETA numbers, distance/time tabular figures, category pill labels: fixed (layout and zoom-sensitive)
- Place card title clamps at 36pt max even at Accessibility XL

### Orientation
- Portrait: default; search card is bottom sheet
- Landscape on iPhone: search card becomes a left-side panel at ~360pt; map takes remaining
- iPad: sidebar + map at all orientations; place card switches between sheet (portrait) and right-pane (landscape)

### Touch Targets
- Map control buttons: 44pt × 44pt circle
- Category pill: 64pt × 88pt
- Action row button: 44pt height, 88pt minimum width
- Search field: full-width 44pt
- Pin tap: 44pt × 44pt hit area centered on the pin tip
- Direction step arrow: 56pt SF Symbol with full step-card tap area

### Safe Area Handling
- Top: map controls sit 16pt below safe area top, respect Dynamic Island
- Bottom: search card respects safe area; small detent shows search bar above home indicator with 16pt padding
- Sides: 16pt margins on card content; map is full-bleed

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- Map cream canvas: `#F6F1E6`
- Map water: `#B9D9EB`
- Map park: `#D6E5C9`
- **Maps Blue**: `#0A84FF`
- **Maps Blue Pressed**: `#0967D2`
- **Maps Red** (pin): `#FF3B30`
- **Food Orange**: `#FF9500`
- **Coffee Brown**: `#A5694C`
- **Drinks Teal**: `#5AC8FA`
- **Shopping Yellow**: `#FFCC00`
- **Parking Purple**: `#AF52DE`
- Card canvas: `#FFFFFF`
- Surface Gray: `#F2F2F7`
- Ink (text): `#000000`
- Secondary (60% opacity): `rgba(60,60,67,0.6)`
- Map label color: `#594F3F`

### Example Component Prompts
- "Create a SwiftUI Apple Maps search card: a sliding bottom sheet with three detents (88pt small, 50% medium, full-screen large), 10pt top corner radius, `.regularMaterial` blur background. Inside: a 36pt × 5pt drag indicator at top in `#3C3C43`@30%, then a full-width 44pt search field with `#F2F2F7` background, 10pt corner radius, leading `magnifyingglass` SF Symbol, placeholder 'Search Maps' in SF Pro Display 17pt Regular. Below the search field: horizontal scroll of category pills (64pt × 88pt white cards with 14pt corner radius, 28pt SF Symbol icon in category color, 14pt Medium label below)."
- "Build the Apple Maps current-location puck: a 22pt blue `#0A84FF` filled circle with a 3pt white outer ring, plus a 30pt outer pulsing ring at 0.2 opacity scaling 1.0 → 1.6 over 2000ms infinite. When heading is known, render a 90° triangular cone extending 60pt from the puck in the heading direction with a gradient from `rgba(10,132,255,0.4)` to transparent."
- "Design the Apple Maps place card: 28pt SF Pro Display Bold place title `#000000`, 15pt SF Pro Text Regular subtitle `Bakery · French · $$` in `#3C3C43`@60%. Below: a horizontal scroll of 44pt-tall action pills — 'Directions' filled `#0A84FF` with white text, 'Call' / 'Share' / 'Save' outlined `#0A84FF` border on white. Sticky bottom 'Directions' button at 48pt height, `#0A84FF` background, 8pt corner radius."
- "Build the Apple Maps directions step card: full-width sticky at top, 96pt tall, `.regularMaterial` blur over the map. Leading: 56pt `arrow.turn.up.right` SF Symbol in `#0A84FF`, rotated to match the actual direction. Center: 'Turn right onto Market St' in SF Pro Display 22pt Semibold `#000000`. Below: '0.4 mi' in SF Pro Text 17pt Medium tabular `#3C3C43`@60%."
- "Create the Apple Maps map controls stack: three circular 44pt buttons stacked vertically in the top-right corner with 8pt gaps, 16pt inset from the right edge and 16pt below safe area top. Each button is `.regularMaterial` blur with an 18pt SF Symbol inside: top is `square.3.layers.3d` (layer picker), middle is `location.fill` (current location), bottom is `map.fill` (3D toggle)."

### Iteration Guide
1. Map canvas is cream `#F6F1E6` for land, soft `#B9D9EB` for water — the cartography IS the brand
2. Maps Blue `#0A84FF` for directions polyline, current-location puck, primary CTAs
3. Maps Red `#FF3B30` for the dropped pin only — universal regardless of category
4. `.regularMaterial` blur on every floating UI surface — search card, map controls, step card, ETA bar
5. SF Pro at the system stack — Display, Text, Compact — no custom face
6. Tabular figures on every numeric label — distance, time, ETA, price
7. Map controls stack top-right with 8pt gaps; current location is the middle button
8. Category pills sit on the search card as 64×88pt white tiles with category-color icons
9. The current-location puck pulses every 2 seconds — opacity 0.3 → 0.0, scale 1.0 → 1.6
10. Dark mode uses warm `#2A2826` over land (the dark equivalent of cream), brighter Maps Blue `#409CFF`
