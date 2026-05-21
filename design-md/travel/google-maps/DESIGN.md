# Design System Inspiration of Google Maps (iOS)

## 1. Visual Theme & Atmosphere

Google Maps' iOS app is a pure map-first interface — the earth is the canvas and the UI is a handful of floating shapes laid over it. Unlike most apps that treat the map as a feature, Maps treats the map as the entire product: every search result, every place card, every navigation instruction exists as a translucent sheet or rounded pill stationed over rendered geography. The result is a chrome-light surface where road vectors, park polygons, and water bodies do all the visual heavy lifting, and the UI chrome melts into whatever landscape is beneath it. This is why a Maps screenshot taken in Manhattan looks chromatically different from one taken in Yosemite — the app is tinted by the terrain.

The accent system is a disciplined deployment of Google's four-color identity. Google Blue (`#4285F4`) is the operating accent — it marks the route polyline, the "Your location" dot with its soft accuracy halo, the "Directions" floating action button, the active tab, and any pin placed on a "home" or "work" saved place. The three remaining Google logo colors each have a single semantic job: Red (`#EA4335`) is the default pin color for a dropped/searched place (the one most users recognize as "the Google Maps pin"), Green (`#34A853`) marks saved or favorited places, Yellow (`#FBBC04`) flags alerts, construction, and caution overlays. Orange (`#FB8C00`) is used for category pins (restaurants, gas stations, hotels). Beyond these, color appears only in map tiles — park green `#C8E6C9`, water blue `#AADAFF`, highway shield yellow `#FDF6E3`, neutral building fills `#F0F0F0`.

Typography is Google Sans for headings and Roboto for body — Google's unified two-font system across its app ecosystem. Google Sans carries geometric, open, slightly humanist shapes that feel neutral on any locale; Roboto is flatter, optimized for dense UI copy and address display. The hierarchy is conservative: place card titles at 20pt weight 500, addresses in 14pt Roboto weight 400, distances in tabular numerals so "1.2 mi" and "14.7 mi" stack cleanly in a list. Turn-by-turn navigation breaks the quiet rule — the turn card shoots to 36-48pt display type in Google Sans weight 700 so the next maneuver is legible at a glance behind the wheel.

**Key Characteristics:**
- Map is the hero — full-bleed to every edge, UI floats as translucent sheets and pills
- Google Blue (`#4285F4`) as the operating accent — route line, location dot, Directions FAB
- Four-color pin system: Red (default), Green (saved), Blue (home/work), Orange (category)
- Floating top search bar ("Search here") with voice-mic and profile avatar
- Bottom sheet drawer with drag-to-expand detents — search results, place details, transit
- Category chip row (Restaurants / Hotels / Coffee / Things to do) beneath the search bar
- Blue "Your location" dot with translucent accuracy circle and heading cone
- Blue route polyline (5pt) with darker blue casing for legibility on any map color
- Turn-by-turn immersive mode: giant blue arrow, top turn card, bottom ETA, speed chip
- Dark mode uses `#202124` canvas with the map's dark-tile palette

## 2. Color Palette & Roles

### Primary (Google Logo Colors)
- **Google Blue** (`#4285F4`): Primary UI accent, route polyline, "Your location" dot, Directions FAB, active tab indicator, home/work pin.
- **Google Red** (`#EA4335`): Default pin color for searched/dropped places, the iconic Maps pin.
- **Google Yellow** (`#FBBC04`): Alerts, construction icons, speed trap overlays, caution chips.
- **Google Green** (`#34A853`): Saved/favorited place pins, "on-time" transit, success states.
- **Category Orange** (`#FB8C00`): Category pins (restaurants, gas stations, hotels) and related chips.

### Blue Ramp (Operating Accent)
- **Blue Primary** (`#4285F4`): Route, FAB, location dot.
- **Blue Pressed** (`#1A73E8`): Pressed state on primary CTAs.
- **Blue Dark** (`#174EA6`): Route polyline casing outline, navigation-mode chrome.
- **Blue Halo** (`rgba(66,133,244,0.18)`): Accuracy circle around "Your location" dot, selection pill backgrounds.

### Canvas & Text (Light Mode)
- **Canvas White** (`#FFFFFF`): Bottom sheet surface, search bar fill, card backgrounds below the map.
- **Surface Muted** (`#F1F3F4`): Category chip fill, disabled fill, sectional backgrounds.
- **Divider** (`#DADCE0`): Hairline dividers between list rows and tabs.
- **Text Primary** (`#202124`): Place titles, primary UI text.
- **Text Secondary** (`#5F6368`): Addresses, metadata, secondary labels.
- **Text Tertiary** (`#80868B`): Low-emphasis labels, disabled text.

### Map Tile Palette (Light)
- **Road White** (`#FFFFFF`): Street surface.
- **Highway Yellow** (`#FDF6E3`): Major highways, freeways — the signature faint-yellow strip.
- **Water Blue** (`#AADAFF`): Lakes, rivers, ocean.
- **Park Green** (`#C8E6C9`): Parks, green spaces.
- **Building Fill** (`#F0F0F0`): Building footprints at zoom, with `#E0E0E0` outline.
- **Land Neutral** (`#F5F5F5`): Default land tint between roads.

### Dark Mode
- **Dark Canvas** (`#202124`): Canvas below the map, sheet backgrounds.
- **Dark Surface 1** (`#2D2E31`): Elevated surface, search bar fill in dark.
- **Dark Surface 2** (`#3C4043`): Higher elevation, modal sheets.
- **Dark Divider** (`#3C4043`): Hairline dividers in dark mode.
- **Dark Text Primary** (`#E8EAED`).
- **Dark Text Secondary** (`#9AA0A6`).
- **Dark Map Land** (`#242F3E`), **Roads** (`#38414E`), **Water** (`#17263C`), **Parks** (`#023E58`).

### Semantic
- **Traffic Red** (`#D50000`): Heavy traffic segment overlay on route.
- **Traffic Orange** (`#FB8C00`): Slow traffic segment overlay.
- **Traffic Green** (`#0F9D58`): Clear traffic segment overlay.
- **Alert Yellow** (`#FBBC04`): Construction, caution triangles.
- **Transit Pink** (`#E63946`): Metro/subway line colors are city-specific; the default catch is a warm red.

## 3. Typography Rules

### Font Family
- **Headings / Display**: `Google Sans` (geometric, slightly humanist — used for place titles, turn directions, tab headers)
- **Body / UI**: `Roboto` (dense, optimized for UI — used for addresses, list metadata, body copy)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Helvetica, Arial, sans-serif`
- **Numerals**: Roboto tabular figures for distances, ETA, speed
- **CJK / Non-Latin**: Noto Sans family siblings fall back for every script Google ships

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Navigation Turn | Google Sans | 36pt | 700 | 1.1 | -0.4pt | "Turn right onto Main St" in driving mode |
| Screen Title | Google Sans | 28pt | 500 | 1.15 | -0.3pt | "Your places", "Settings" hero |
| Place Title (Card) | Google Sans | 20pt | 500 | 1.2 | -0.2pt | Place sheet hero title |
| Section Header | Google Sans | 16pt | 500 | 1.25 | -0.1pt | "Photos", "Reviews", "About" |
| Row Title | Roboto | 16pt | 500 | 1.3 | 0pt | Search result row title |
| Body | Roboto | 14pt | 400 | 1.4 | 0pt | Place description, review text |
| Address | Roboto | 14pt | 400 | 1.35 | 0pt | Street address line, monospaced digits |
| Meta / Distance | Roboto | 13pt | 400 | 1.3 | 0pt | "1.2 mi · 4 min · Open now" |
| Rating | Roboto | 14pt | 500 | 1.2 | 0pt | "4.5 ★ (2,345)" — bold rating, lighter count |
| Category Chip | Google Sans | 14pt | 500 | 1.0 | 0pt | "Restaurants", "Coffee" |
| Button (Primary) | Google Sans | 16pt | 500 | 1.0 | 0pt | "Directions", "Save" |
| Button (Secondary) | Google Sans | 14pt | 500 | 1.0 | 0pt | "Call", "Share", "Start" |
| Tab Label | Google Sans | 11pt | 500 | 1.0 | 0.2pt | Tab bar labels |
| ETA / Speed | Roboto | 16pt | 500 | 1.0 | 0pt | Navigation ETA, speed chip |
| Distance Pill | Roboto | 13pt | 500 | 1.0 | 0pt | Map distance labels at route apex |

### Principles
- **Two-font system**: Google Sans for headlines and CTAs, Roboto for everything dense and data-driven. Never swap them.
- **Weights concentrated at 400 / 500 / 700**: Regular, Medium, Bold. 500 is the workhorse weight; 700 is reserved for nav turn cards and emphasis.
- **Tabular numerals always**: distances, ratings, ETAs, speeds use Roboto's tabular figure set so columns align.
- **Rating star uses Google Yellow**: `#FBBC04` (not orange) — consistent with Google product rating stars.
- **Turn card type is the only display moment**: 36-48pt, Google Sans 700, reserved for in-navigation mode.
- **No italics**: emphasis through weight only.

## 4. Component Stylings

### Buttons

**Primary FAB (Directions)**
- Shape: Circle, 56pt diameter, pinned 16pt from trailing/bottom edges over the map
- Background: `#4285F4`
- Icon: SF Symbol `arrow.triangle.turn.up.right.diamond.fill` (or custom Google Directions glyph) at 24pt in `#FFFFFF`
- Shadow: `rgba(0,0,0,0.2) 0 4px 12px`
- Pressed: scale 0.95, background `#1A73E8`, medium haptic
- Used for: Directions entry from any place sheet

**Primary Pill CTA ("Start", "Save")**
- Background: `#4285F4`
- Text: `#FFFFFF`, Google Sans 16pt weight 500
- Height: 44pt
- Padding: 0 24pt
- Corner radius: 22pt (full pill)
- Pressed: `#1A73E8`, scale 0.98, light haptic
- Used for: "Start" navigation, "Save" place, "Add stop"

**Secondary Outline Button ("Call", "Share")**
- Background: transparent
- Border: 1pt solid `#DADCE0`
- Text: `#4285F4`, Google Sans 14pt weight 500
- Height: 36pt
- Corner radius: 18pt
- Leading icon: 18pt, Google Blue
- Pressed: background `#F1F3F4`

**Icon-Only Map Control (Recenter, Layers, Compass)**
- Size: 40pt circle
- Background: `#FFFFFF` with shadow `rgba(0,0,0,0.15) 0 2px 6px`
- Icon: 20pt, `#5F6368`, rounded-join geometric glyph
- Pressed: `#F1F3F4` fill
- Position: floats over the map, trailing side, stacks vertically above the Directions FAB

**Category Chip (Restaurants / Hotels / Coffee)**
- Background: `#FFFFFF` with 1pt `#DADCE0` hairline border
- Text: `#202124`, Google Sans 14pt weight 500
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 18pt (pill)
- Leading icon: 16pt colored glyph matching category (fork+knife in orange, etc.)
- Active (tapped): background `#E8F0FE` + border `#4285F4` + text `#1967D2`
- Shadow: `rgba(0,0,0,0.08) 0 1px 3px`

### Cards & Containers

**Place Card (Bottom Sheet Hero)**
- Sits at the top of the bottom-sheet place details screen
- Leading: photo thumbnail 72×72pt with 12pt corner radius
- Place title: Google Sans 20pt w500 `#202124` (2-line clamp)
- Rating line: "4.5" in Roboto 14pt w500 `#202124` + 4.5 ★ glyphs in `#FBBC04` + "(2,345)" in Roboto 14pt w400 `#5F6368`
- Category line: "Italian restaurant · $$ · 1.2 mi"
- Open-now pill: `#34A853` "Open now" + Roboto 13pt " · Closes 10 PM" in `#5F6368`
- Action row beneath: Directions (blue pill FAB) | Save | Share | Call
- Separator: 1pt `#DADCE0` hairline below action row

**Search Result Row**
- Height: 72pt
- Leading: 48×48pt category-colored circle with 20pt white glyph (e.g., orange fork for restaurant)
- Title: Roboto 16pt w500 `#202124`
- Subtitle: Roboto 13pt w400 `#5F6368` — "1.2 mi · 123 Main St"
- Trailing: small 28pt "Directions" icon button (blue) if not currently navigating
- Pressed: background `#F1F3F4`

**Bottom Sheet Drawer**
- Position: anchored to bottom, 3 detents (collapsed 140pt, medium 380pt, expanded near-full)
- Background: `#FFFFFF` light / `#2D2E31` dark
- Corner radius: 16pt top corners, 0pt bottom
- Grabber: 32×4pt `#DADCE0` pill centered 8pt from top
- Shadow: `rgba(0,0,0,0.12) 0 -4px 20px`
- Scroll behavior: sheet-inner scrolling kicks in at expanded detent

**Route Card (Directions Sheet)**
- Modes row (Drive / Transit / Walk / Bike / Rideshare): segmented control with blue underline under active
- Primary route: "25 min (18.4 mi)" Google Sans 20pt w500 + "Fastest route, usual traffic" Roboto 13pt w400 `#5F6368`
- Alternative routes listed below in lighter gray polyline (map overlay) with "+3 min" delta chip
- "Start" CTA: blue pill 44pt full-width bottom of card

### Navigation

**Top Floating Search Bar**
- Position: floats 8pt below top safe area, 16pt horizontal margins
- Height: 48pt
- Background: `#FFFFFF`
- Corner radius: 24pt (full pill)
- Shadow: `rgba(0,0,0,0.15) 0 2px 8px`
- Layout: leading 28pt profile avatar (circular) | center placeholder "Search here" (Google Sans 16pt w400 `#5F6368`) | trailing 24pt voice-mic icon (Google Blue when listening)
- Tap: expands to full-screen search with keyboard and recent searches below

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt top border `#DADCE0`, `.regularMaterial` blur
- Tabs: Explore, Go (commute), Saved, Contribute, Updates
- Icon size: 24pt, 2pt stroke, geometric
- Active color: `#4285F4` (filled variant with colored fill)
- Inactive: `#5F6368` (outline variant)
- Labels: Google Sans 11pt w500, always shown
- Haptic on tab change: light

**Navigation Mode Top Turn Card**
- Position: anchored at top, below safe area, full-width minus 16pt margin
- Background: `#4285F4`
- Corner radius: 16pt
- Layout: leading giant 48pt turn arrow white | turn instruction "Turn right onto Main St" in Google Sans 22pt w700 white | distance "in 0.3 mi" Roboto 14pt w400 white 70%
- Shadow: `rgba(0,0,0,0.2) 0 4px 16px`
- Next turn preview below: smaller 32pt turn arrow + "Then left onto Oak Ave" in 14pt

**Navigation Mode Bottom ETA Card**
- Position: anchored to bottom, full-width above safe area
- Background: `#FFFFFF` (light) / `#2D2E31` (dark)
- Corner radius: 16pt top corners
- Content: ETA "4:32 PM" Google Sans 22pt w500 + "18 min · 12 mi" Roboto 14pt w400 `#5F6368` + traffic color bar
- End-trip exit pill: `#EA4335` "Exit" button 36pt trailing

### Input Fields

**Search Input (Expanded)**
- Background: `#FFFFFF` with 1pt `#DADCE0` bottom border inside a scroll container
- Height: 56pt
- Leading: 20pt back chevron `#5F6368`
- Placeholder: "Search Google Maps", Google Sans 16pt w400 `#5F6368`
- Trailing: 20pt voice-mic + 20pt clear (X) when text present
- Text color: `#202124`

**Saved Place Row**
- Height: 56pt
- Leading: 40pt colored icon circle (Home = blue house, Work = blue briefcase, custom = green bookmark)
- Title: Roboto 16pt w500 `#202124`
- Subtitle: Roboto 13pt w400 `#5F6368` address
- Trailing: 16pt chevron `#80868B`

### Distinctive Components

**Location Dot**
- Inner dot: 12pt solid `#4285F4`
- White stroke ring: 3pt white around the blue dot
- Accuracy circle: 60-200pt radius (depends on GPS accuracy), `rgba(66,133,244,0.18)` fill with `rgba(66,133,244,0.4)` 1pt outline
- Heading cone (when compass active): 40pt translucent blue triangle wedge emanating outward from the dot
- Gentle pulse: scale 1.0 → 1.15 → 1.0 over 1.8s infinite (subtle, not attention-grabbing)

**Map Pin (Dropped Place)**
- Red Default: 32×40pt tear-drop shape, `#EA4335` fill with 3pt white inner circle, bottom-point anchored at coordinate
- Green Saved: same shape, `#34A853` fill
- Blue Home/Work: same shape with building/house glyph in white
- Orange Category: same shape with category glyph (fork, gas, bed) in white
- Drop animation: 0.35s spring with bounce, shadow grows with the pin
- Ground shadow: `rgba(0,0,0,0.25) 0 4px 6px`, 28×8pt ellipse below

**Route Polyline**
- Width: 5pt
- Color: `#4285F4` fill
- Casing: 1.5pt `#174EA6` outline
- Endcaps: rounded
- Traveled portion during navigation: 40% opacity
- Traffic overlay: red/orange/green segments drawn on top at 3pt width

**Distance Label**
- Appears at route apex midpoint during search
- Pill: white background with 1pt `#DADCE0` border
- Text: "18 mi" in Roboto 13pt w500 `#202124`
- Padding: 4pt vertical, 10pt horizontal
- Shadow: `rgba(0,0,0,0.1) 0 1px 3px`

**Pegman / Street View Chip**
- Draggable orange-yellow Pegman figure bottom-right
- Background: `#FB8C00`
- Size: 40pt circle with miniature person silhouette
- Triggered by drag: blue highlights appear on streets with Street View coverage

**Category Chip Row**
- Horizontal scroll directly below top search bar
- 10 chips: Restaurants, Coffee, Gas, Hotels, Groceries, Shopping, Things to do, More
- Gap: 8pt between chips
- Scrolls to the right with the last chip peek-visible

**Speed Chip (Navigation)**
- Bottom-left corner in nav mode
- 56pt circle
- Background: `#FFFFFF` light / `#3C4043` dark
- Current MPH number in Roboto 22pt w500
- "mph" label in Roboto 11pt w400 `#5F6368`
- Turns red with white text when over limit

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Map-relative margins: 16pt from screen edges for floating controls and search bar
- Sheet internal: 16pt horizontal, 20pt top (below grabber), 24pt bottom above safe area
- Gaps inside place sheet: 16pt between sections, 12pt between action buttons

### Grid & Container
- Map: full viewport, edge-to-edge, underneath Dynamic Island and status bar
- Floating search bar: 16pt horizontal margin, pill shape
- Category chip row: starts 16pt below search bar, peek-edge right side
- Bottom sheet: full-width, attached bottom, floats with 16pt top radius
- Map controls: stacked trailing edge, 16pt from edge, offset above current sheet detent

### Whitespace Philosophy
- **Map is king**: 60-85% of viewport, always. The UI exists to yield to geography.
- **Pills and sheets don't touch**: 16pt between any two floating elements to preserve map breathability.
- **Place cards breathe**: 16pt padding inside, 24pt between sections.
- **Turn cards are dense by necessity**: navigation mode's UI is denser to fit every detail needed at speed.

### Border Radius Scale
- Square (0pt): Map tiles (they are the geometry itself)
- Soft (8pt): Inline badges, small status pills
- Standard (12pt): Place thumbnails, photo tiles, inner card surfaces
- Comfortable (16pt): Bottom sheet top, turn cards, ETA card
- Pill (18-24pt): Chips, secondary buttons, primary pill CTAs
- Full Pill (full): Top floating search bar, category chips
- Circle (50%): FAB, location dot, avatar, map controls

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Map tiles, map pins (rely on ground shadow) |
| Hairline (Level 1) | 1pt `#DADCE0` border | Category chips (default), segmented controls |
| Floating Pill (Level 2) | `rgba(0,0,0,0.15) 0 2px 8px` | Top search bar, distance labels on map |
| Floating Control (Level 3) | `rgba(0,0,0,0.15) 0 2px 6px` | Map control buttons (recenter, layers, compass) |
| FAB (Level 4) | `rgba(0,0,0,0.2) 0 4px 12px` | Directions FAB |
| Bottom Sheet (Level 5) | `rgba(0,0,0,0.12) 0 -4px 20px` | Bottom drawer for search results and place detail |
| Turn Card (Level 6) | `rgba(0,0,0,0.2) 0 4px 16px` | Navigation top turn card on blue |
| Dim Overlay | `rgba(0,0,0,0.5)` | Behind modal sheets (route alternatives) |

**Shadow Philosophy**: Because every piece of UI sits on top of a textured, colored map, shadows need to separate "UI plane" from "map plane" without overpowering the geography. A 15% black shadow at 8pt blur reads cleanly against both park-green and ocean-blue tiles. The FAB's shadow is the most aggressive (20% / 12pt) because the button is the primary action and must lift cleanly above any terrain. Map pins get a ground-hugging ellipse shadow instead of a drop — this is what makes them feel dropped into the world rather than placed on a canvas.

### Motion
- **Pin drop**: 0.35s spring with overshoot 1.1 → 1.0, shadow scales up with pin, medium haptic
- **Search bar expand to full screen**: 0.28s ease-out, pill morphs into full-width search panel with list fade-in
- **Bottom sheet detent change**: 0.35s spring, damping 0.85 — snaps cleanly between collapsed/medium/expanded
- **FAB press**: scale 0.95 → 1.0 spring (damping 0.7), paired with `.impactOccurred(.medium)` haptic
- **Route draw on start**: polyline traces from origin to destination over 600ms ease-out, then camera pans to follow
- **Location dot pulse**: continuous 1.8s ease-in-out scale 1.0 → 1.15 → 1.0
- **Turn card swap**: slides up from below, previous card fades to 0, 300ms ease-in-out, light haptic on arrival at each turn
- **Camera flyTo on recenter**: 0.6s ease-in-out, no haptic (soft action)
- **Traffic overlay fade-in**: 400ms ease-out as traffic data loads
- **Dark mode cross-fade**: map tiles cross-fade over 1.2s on system theme toggle

## 7. Do's and Don'ts

### Do
- Keep the map at 60%+ of viewport on every primary screen — it's the product
- Use Google Blue (`#4285F4`) for the route polyline, location dot, and Directions FAB
- Float the top search bar as a pill with 16pt margins and a soft shadow
- Render the bottom sheet with 16pt top corners, visible grabber, three detents
- Use the four-color pin system: Red default, Green saved, Blue home/work, Orange category
- Use Google Sans for titles and CTAs, Roboto for body and metadata (tabular numerals on data)
- Display traffic as colored overlay on the route: red heavy, orange slow, green clear
- Trigger medium haptic on pin drop and "Start navigation"; light haptic on tab change
- Show the accuracy circle around the location dot — it's the trust signal
- Use `#202124` canvas in dark mode with the dark-map tile palette

### Don't
- Don't color the map itself with brand colors — the map uses its own tile palette
- Don't round map pins heavily — the tear-drop shape with 3pt white inner dot is the silhouette
- Don't drop pin shadows — use a ground-hugging ellipse for on-world feel
- Don't animate the map aggressively — camera moves are 0.3-0.6s ease, never snap
- Don't stack more than 3 floating map controls trailing edge — it clutters the map
- Don't use red (`#EA4335`) for generic UI actions — it reads as a pin or destructive
- Don't override tabular numerals for distances — columns must align
- Don't hide the bottom sheet when a route is active — the ETA card must remain visible
- Don't center-align body text in place cards — Maps content is left-aligned (LTR)
- Don't introduce new accent colors — the four Google logo colors each have a job already

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Category chip row fits 4 visible, bottom sheet collapsed 120pt |
| iPhone 13/14/15 | 390pt | Standard 5 visible chips, 140pt collapsed sheet |
| iPhone 15/16 Pro | 393pt | Dynamic Island pushes search bar 8pt lower |
| iPhone 15/16 Pro Max | 430pt | 6 visible chips, turn card scales to 180pt tall in nav mode |
| iPad | 768pt+ | Map full-bleed; sheet becomes a 420pt floating panel anchored leading side |

### Dynamic Type
- Place titles, addresses, row titles: full scale
- Navigation turn instruction: scales to 48pt max
- Distance/ETA labels: fixed size (layout-sensitive)
- Speed chip: fixed size
- Tab labels: scale up to 13pt, then truncate

### Orientation
- Explore / Saved / Updates: **portrait-locked**
- Navigation mode: **rotates to landscape** — turn card moves to top, ETA to bottom-right, map expands horizontally
- Street View: **rotates freely** — 360° horizontal pan works best with device orientation

### Touch Targets
- Directions FAB: 56pt (hero action)
- Top search bar: 48pt tall, full-row tappable
- Category chips: 36pt tall with 44pt effective hit
- Map pins: 40pt hit area despite 32pt visual
- Map controls: 40pt visible, 44pt hit
- Tab bar icons: 24pt icon inside 44pt hit

### Safe Area Handling
- Top: search bar floats 8pt below safe area top
- Bottom: sheet extends to home indicator; content padded 24pt above indicator
- Dynamic Island: search bar sits 8pt below the island bottom edge
- Sides: map edge-to-edge; floating controls 16pt inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#202124`
- Surface muted: `#F1F3F4`
- Divider: `#DADCE0`
- Text primary: `#202124`
- Text secondary: `#5F6368`
- Google Blue (route, FAB, location): `#4285F4`
- Blue pressed: `#1A73E8`
- Pin Red (default): `#EA4335`
- Pin Green (saved): `#34A853`
- Pin Orange (category): `#FB8C00`
- Rating Yellow: `#FBBC04`
- Map road: `#FFFFFF`
- Map highway: `#FDF6E3`
- Map water: `#AADAFF`
- Map park: `#C8E6C9`
- Map building: `#F0F0F0`

### Example Component Prompts
- "Create a SwiftUI Google Maps top search bar: floats 8pt below safe area with 16pt horizontal margins, 48pt tall pill (full corner radius), `#FFFFFF` background, `rgba(0,0,0,0.15) 0 2px 8px` shadow. Leading 28pt circular profile avatar, center placeholder 'Search here' in Google Sans 16pt w400 `#5F6368`, trailing 24pt microphone icon in `#5F6368`. Tap expands to full-screen search."
- "Build the Directions FAB: 56pt circle floating 16pt from trailing/bottom edges over the map, `#4285F4` background, 24pt white directional arrow SF Symbol `arrow.triangle.turn.up.right.diamond.fill`. Shadow `rgba(0,0,0,0.2) 0 4px 12px`. Pressed: scale 0.95, background `#1A73E8`, medium haptic."
- "Design the Your Location dot: 12pt solid `#4285F4` inner dot with 3pt white stroke ring, translucent `rgba(66,133,244,0.18)` accuracy circle 120pt radius around it with 1pt `rgba(66,133,244,0.4)` outline, 40pt heading cone triangle emanating outward from the dot when compass is active. Gentle pulse 1.0 → 1.15 → 1.0 over 1.8s."
- "Create a place bottom sheet: anchored to bottom, 3 detents (140pt / 380pt / near-full), 16pt top corner radius, `#FFFFFF` background, `rgba(0,0,0,0.12) 0 -4px 20px` shadow. 32×4pt `#DADCE0` grabber centered 8pt from top. Inside: 72pt photo thumbnail leading, place title 'Blue Bottle Coffee' in Google Sans 20pt w500, rating '4.5' + 4.5 gold stars in `#FBBC04` + '(2,345)' in Roboto 14pt, category 'Coffee shop · $$ · 1.2 mi', green 'Open now' pill. Action row: blue 44pt Directions pill + Save + Share + Call."
- "Build the navigation turn card: anchored at top below safe area with 16pt margins, `#4285F4` background, 16pt corner radius, shadow `rgba(0,0,0,0.2) 0 4px 16px`. Leading 48pt white turn arrow glyph, turn instruction 'Turn right onto Main St' in Google Sans 22pt w700 white, distance 'in 0.3 mi' in Roboto 14pt w400 white 70% opacity below. Smaller next-turn preview '32pt arrow + Then left onto Oak Ave' at 14pt below."
- "Design a category chip row: horizontal scroll directly below the search bar with 16pt leading inset and 8pt between chips. Each chip: 36pt tall pill with `#FFFFFF` background, 1pt `#DADCE0` border, 16pt colored category glyph leading, 'Restaurants' label in Google Sans 14pt w500 `#202124`, 14pt horizontal padding. Active state: background `#E8F0FE`, border `#4285F4`, text `#1967D2`."

### Iteration Guide
1. The map owns 60%+ of the viewport on every primary screen — UI is layered, never stacked below
2. Google Blue (`#4285F4`) is the operating accent — route, location dot, Directions FAB
3. Four pin colors, four jobs: Red default, Green saved, Blue home/work, Orange category
4. Google Sans for titles, Roboto for body and metadata; Roboto tabular numerals for all data
5. Bottom sheet has 16pt top corners with a visible 32×4pt `#DADCE0` grabber — the drag-to-expand contract
6. Float the search bar as a pill with a soft shadow; keep 16pt margins to the screen edges
7. Turn cards in nav mode use `#4285F4` background and 22pt bold white text — the only display moment
8. Location dot's accuracy halo is the trust signal — always render it with `rgba(66,133,244,0.18)` fill
9. Dark mode uses `#202124` canvas (not pure black) to match the dark map tile palette
