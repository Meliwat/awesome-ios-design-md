# Design System Inspiration of Flighty (iOS)

## 1. Visual Theme & Atmosphere

Flighty's iOS app is a deep, premium black canvas (`#0B0B0F`) that treats live flight data like an instrument panel. The aesthetic is "aviation cockpit meets Apple-grade polish": a near-black field, crisp white type, and a single electric blue accent that carries every active state. The signature visual is the live flight map — a dark world map with a glowing blue great-circle arc connecting origin and airport, the plane sliding along it in real time. This is a power-user tool that earns its darkness: the black recedes so the arc, the on-time percentage, and the gate number can glow.

The accent system is disciplined and status-driven. Flighty Blue (`#0A84FF`) owns the primary action, the live arc, the active tab, and selected states. Status semantics are strict: on-time green (`#30D158`) and delay amber (`#FFD60A`) appear only on flight-status chips, the delay timeline, and the Live-Activity-style status bar. Beyond blue, green, and amber, the palette is grayscale — layered dark surfaces (`#1A1A1F`, `#222228`), `#2E2E36` hairlines, white and gray text. The restraint makes the status colors read instantly: a glance tells you delayed-amber or on-time-green without reading a word.

Typography is a precise, technical sans (Flighty uses SF Pro in production; the design intent is "instrument-grade legibility"). The hierarchy is data-forward: 11-32pt with weights at 400 / 600 / 700. The most expressive type moment is the flight card: a large 28-32pt route or time paired with a colored status chip and a monospace-feel gate/terminal block. Numbers — times, on-time %, gate, terminal — are the heroes; descriptive copy recedes into gray.

**Key Characteristics:**
- Deep-black canvas (`#0B0B0F`) — premium, instrument-panel darkness
- Flighty Blue (`#0A84FF`) as the single accent — primary action, live arc, active states
- Live flight map arc — a glowing blue great-circle over a dark world map, the signature
- On-time green (`#30D158`) / delay amber (`#FFD60A`) — strict status semantics only
- Flight card — route, times, on-time %, gate/terminal in a dense status block
- Live-Activity-style status bar — the persistent flight progress strip
- Delay timeline — a vertical timeline of schedule changes with colored nodes
- Haptics on flight selection, status changes, and arc/plane focus

## 2. Color Palette & Roles

### Primary
- **Flighty Blue** (`#0A84FF`): Primary CTA, live map arc, active tab indicator, selected segment, link text.
- **Blue Pressed** (`#0066CC`): Active/pressed state for blue CTAs and the arc focus pulse.
- **Blue Glow** (`rgba(10,132,255,0.45)`): Soft halo behind the live arc and the plane marker.

### Status (Strict Semantics)
- **On-Time Green** (`#30D158`): "On time" status chip, on-time % ring, positive timeline nodes.
- **Delay Amber** (`#FFD60A`): "Delayed" status chip, delay timeline nodes, schedule-change warnings.
- **Cancelled Red** (`#FF453A`): "Cancelled" / "Diverted" status chip, critical timeline nodes.

### Canvas & Surface
- **Canvas Black** (`#0B0B0F`): Primary deep-black canvas behind all content and the map.
- **Surface 1** (`#1A1A1F`): Card backgrounds, elevated rows.
- **Surface 2** (`#222228`): Higher elevation — sheets, active rows, status bar.
- **Surface 3** (`#2C2C34`): Pressed state on dark surfaces.
- **Divider** (`#2E2E36`): Hairline dividers between rows and sections.

### Text
- **Text Primary** (`#FFFFFF`): Times, route codes, screen headlines, primary UI text.
- **Text Secondary** (`#8E8E96`): Airport names, metadata, captions, helper copy.
- **Text Tertiary** (`#5A5A62`): Disabled labels, placeholders, very low-emphasis hints.

### Map
- **Map Land** (`#16161B`): Dark landmasses on the flight map.
- **Map Water** (`#0B0B0F`): Ocean (same as canvas — seamless).
- **Map Graticule** (`#1F1F26`): Faint lat/long grid lines.
- **Arc Path Past** (`#0A84FF`): Flown portion of the great-circle, full opacity.
- **Arc Path Remaining** (`rgba(10,132,255,0.35)`): Not-yet-flown portion, dimmed dashed.

### Light Mode (Limited Use)
Flighty's iOS app is effectively dark-only — the instrument-panel aesthetic depends on it. A light variant exists for share cards / embeds but is not the primary experience.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#0B0B0F`)
- **Blue** (`#0A84FF`) — unchanged

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` (Text + Display optical variants) — instrument-grade legibility
- **Substitute**: `Inter` — closest free face for the technical sans tone
- **Numeric**: prefer tabular/monospaced figures for times, gate, terminal, on-time %
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Flight Time (Hero) | SF Pro Display | 32pt | 700 | 1.05 | -0.5pt | Departure/arrival time on flight card |
| Screen Title (Large) | SF Pro Display | 28pt | 700 | 1.15 | -0.4pt | Flights / Airport large nav title |
| Route Code | SF Pro Display | 24pt | 700 | 1.1 | 0.5pt | "SFO → JFK" airport codes |
| Section Header | SF Pro Display | 22pt | 700 | 1.2 | -0.3pt | "Today", "Delay history" |
| Card Title | SF Pro Text | 17pt | 600 | 1.25 | -0.1pt | Airline + flight no. on card |
| Body | SF Pro Text | 15pt | 400 | 1.45 | 0pt | Status explanation, descriptions |
| Button (Primary) | SF Pro Text | 16pt | 600 | 1.0 | 0pt | Blue "Add flight", "Track" |
| Status Chip | SF Pro Text | 13pt | 700 | 1.0 | 0.3pt | "ON TIME", "DELAYED 22m" |
| Gate / Terminal | SF Pro Text (mono) | 15pt | 600 | 1.0 | 0.5pt | "Gate B24 · Term 2" tabular figures |
| Meta / Count | SF Pro Text | 13pt | 400 | 1.3 | 0pt | "5h 42m · 2,586 mi", airport names |
| On-Time % | SF Pro Display | 20pt | 700 | 1.0 | 0pt | "87% on time" ring label |
| Tab Label | SF Pro Text | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Timestamp | SF Pro Text (mono) | 11pt | 600 | 1.0 | 0pt | Timeline node times |

### Principles
- **Weights concentrated at 400 / 600 / 700**: regular, semibold, bold — no thin, no black
- **Tabular figures for all flight data**: times, gate, terminal, distance, on-time % never shift width
- **Times are the hero**: the departure/arrival time gets 32pt bold, the largest type on screen
- **Slight positive tracking on route codes**: +0.5pt on "SFO → JFK" for a technical, spaced read
- **Color, not weight, signals status** — the green/amber/red chip carries the meaning

## 4. Component Stylings

### Buttons

**Primary CTA ("Add flight" / "Track")**
- Background: `#0A84FF`
- Text: `#FFFFFF`
- Padding: 15pt vertical, 28pt horizontal
- Corner radius: 14pt
- Font: SF Pro Text 16pt weight 600
- Pressed: background `#0066CC`, scale 0.98, with `.impactOccurred(.medium)` haptic
- Disabled: background `#222228`, text `#5A5A62`

**Secondary Button ("Share flight")**
- Background: `#1A1A1F`
- Text: `#FFFFFF`
- Border: 1pt solid `#2E2E36`
- Padding: 13pt vertical, 24pt horizontal
- Corner radius: 14pt
- Font: SF Pro Text 15pt weight 600
- Pressed: background `#2C2C34`

**Icon Actions (Refresh, Notify, Share, Map)**
- Size: 22pt glyph, 44pt hit area
- Default: `#8E8E96`
- Active: `#0A84FF` (notifications armed, map focused)
- Spacing: 20pt between icons in the card action row

**Text Button ("View full timeline")**
- Font: SF Pro Text 15pt weight 600
- Color: `#0A84FF`
- No underline, 44pt hit area

### Cards & Containers

**Flight Card**
- Background: `#1A1A1F`, corner radius 16pt, border 1pt `#2E2E36`
- Header: airline + flight no. (17pt w600) + status chip (colored, trailing)
- Route block: origin code 24pt w700 — a thin arc-with-plane glyph — destination code 24pt w700
- Time row: departure time 32pt w700 + arrival time 32pt w700 (tabular), gate/terminal 15pt mono below each
- Meta: "5h 42m · 2,586 mi · Nonstop" 13pt `#8E8E96`
- On-time ring: small circular progress with "% on time" label
- Action row: refresh · notify · share · open-map

**Live Flight Map**
- Full-bleed dark map: land `#16161B`, water `#0B0B0F`, faint graticule `#1F1F26`
- Great-circle arc: flown portion solid `#0A84FF`, remaining dashed `rgba(10,132,255,0.35)`
- Plane marker: white glyph rotated to heading, soft blue glow halo `rgba(10,132,255,0.45)`
- Origin/destination pins: 8pt dots with white ring, code label beside
- Bottom sheet over the map: drag-up flight detail (`#1A1A1F`, 20pt top radius)

**Live-Activity-Style Status Bar**
- Height: 64pt, background `#222228`, corner radius 16pt
- Left: airline logo chip, flight no.
- Center: a horizontal progress track — origin dot ⟶ plane (current %) ⟶ destination dot, blue fill
- Right: status chip + ETA time (tabular)
- Mirrors the Dynamic Island / Lock Screen Live Activity layout exactly

**Delay Timeline**
- Vertical timeline, 2pt `#2E2E36` rail
- Nodes: 12pt dots — on-time green `#30D158`, delay amber `#FFD60A`, cancelled red `#FF453A`
- Each row: timestamp (11pt mono) + event title (15pt w600) + delta ("+22m" colored)
- Latest node pulses softly with its status color

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(11,11,15,0.92)` with `.regularMaterial` blur (dark)
- Tabs: Flights, Search, Airport, Profile
- Icon size: 24pt
- Active: `#0A84FF` icon + label (blue is the active indicator)
- Inactive: `#8E8E96`
- Labels: SF Pro Text 11pt w600, always shown
- Active icon: filled SF Symbol; inactive: outlined
- Hairline top border `#2E2E36`

**Top Nav (Flights)**
- Height: 52pt + safe area
- Large title left (28pt w700 white), add-flight "+" button right (blue, 28pt circle)
- Segmented control below: "Upcoming · Past · Tracked" (`#1A1A1F` track, blue selected)
- Compact + blurred on scroll

**Map Detail Sheet**
- Drag-handle sheet over the live map, 3 detents (peek / half / full)
- Background `#1A1A1F`, 20pt top corner radius, grabber `#2E2E36`

### Input Fields

**Search Field (Flight / Airport)**
- Background: `#1A1A1F`
- Height: 48pt
- Corner radius: 12pt
- Leading SF Symbol `magnifyingglass` 18pt `#8E8E96`
- Placeholder: "Flight number or route", 15pt w400 `#8E8E96`
- Focus: 1.5pt border `#0A84FF`, cursor blue
- Text color: `#FFFFFF`

**Segmented Control**
- Track: `#1A1A1F`, 4pt inset
- Selected segment: `#0A84FF` fill, white text, 10pt radius
- Unselected: transparent, `#8E8E96` text
- Font: SF Pro Text 14pt weight 600

### Distinctive Components

**Live Flight Map Arc**
- A great-circle arc rendered over the dark map between origin and destination
- Flown portion: solid `#0A84FF`, 2.5pt stroke; remaining: dashed `rgba(10,132,255,0.35)`, 2pt
- A white plane glyph sits at the live position, rotated to heading, with a `rgba(10,132,255,0.45)` glow
- Arc draws in on appear (origin → plane position) over ~900ms ease-out
- The single most important visual on the screen — the map exists to host it

**Flight Status Chip**
- On time: `#30D158` text on `rgba(48,209,88,0.15)` fill
- Delayed: `#FFD60A` text on `rgba(255,214,10,0.15)` fill, e.g. "DELAYED 22m"
- Cancelled: `#FF453A` text on `rgba(255,69,58,0.15)` fill
- Padding: 5pt vertical, 10pt horizontal, 999pt radius, 13pt w700 uppercase
- Always the fastest-read element on a flight card

**On-Time Percentage Ring**
- Circular progress (3pt stroke), track `#2E2E36`, fill `#30D158`
- Center label: "87%" 20pt w700 + "on time" 11pt `#8E8E96`
- Used on flight cards and the route stats screen

**Delay Timeline Node**
- Colored dot on the vertical rail; the latest one emits a soft pulsing ring in its status color
- Encodes the entire schedule-change history at a glance

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard margin: 16pt horizontal, 20pt between cards, 24pt between sections
- Flight-card internal padding: 18pt; route block centered with 16pt gaps

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Flight cards: single column, full-width, 16pt vertical gap
- Live map: full-bleed (edge to edge), sheet floats above with insets
- Timeline: single rail, full-width rows with hairline dividers

### Whitespace Philosophy
- **Data gets a generous block**: the route + time hero sits in an 18pt-padded card with air around the 32pt times
- **The map is sacred**: the live flight map is full-bleed; nothing competes with the arc
- **Quiet chrome**: nav, segmented control, and rows are tight so the times and arc dominate

### Border Radius Scale
- Square (0pt): hairline dividers, the full-bleed map
- Control (10pt): selected segment in the segmented control
- Field (12pt): search fields
- Control (14pt): primary/secondary buttons
- Card (16pt): flight cards, status bar
- Comfortable (20pt): bottom sheets, map detail sheet
- Full Pill (999pt): status chips
- Circle (50%): on-time ring, avatars, timeline nodes, plane glow halo

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, canvas, the map |
| Card (Level 1) | `rgba(0,0,0,0.4) 0 4px 16px` | Flight cards lifted off the deep-black canvas |
| Status Bar (Level 2) | `rgba(0,0,0,0.5) 0 6px 20px` | The Live-Activity-style status bar |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 48px` | Map detail sheet, bottom sheets |
| Arc Glow | `rgba(10,132,255,0.45)` blur 18 | Halo behind the live arc + plane marker |
| Tab Bar Material | `.regularMaterial` over `rgba(11,11,15,0.92)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Because the canvas is near-black, shadows must be deep and soft to register — cards use a 0.4-opacity, 16pt blur to separate from `#0B0B0F`. The only *colored* light in the app is the blue arc glow: a soft `rgba(10,132,255,0.45)` halo that makes the great-circle and plane marker feel illuminated, like a live signal on an instrument. Everything else is flat — depth comes from the layered dark surfaces, not heavy shadow.

### Motion
- **Arc draw-in**: on flight open, the great-circle animates from origin to the live plane position over ~900ms ease-out, the glow fading in behind it
- **Plane advance**: the plane marker interpolates smoothly along the arc as live position updates (60fps)
- **Status change**: when status flips (on-time → delayed), the chip cross-fades color + a single `.impactOccurred(.medium)` haptic, the timeline appends a node with a pulse
- **Status-bar progress**: the origin→destination track fills continuously; the plane glides, not steps
- **Card press**: scale 0.98 spring (damping 0.8) + medium haptic
- **Sheet detents**: rubber-band spring between peek / half / full

## 7. Do's and Don'ts

### Do
- Use `#0B0B0F` as the canvas — deep, premium instrument-panel black
- Reserve Flighty Blue (`#0A84FF`) for the primary action, the live arc, and active states
- Keep status colors strict: green = on time, amber = delayed, red = cancelled — chips only
- Render the live flight map arc as the screen's hero — the map exists to host it
- Use tabular figures for all flight data (times, gate, terminal, on-time %)
- Give departure/arrival times the 32pt bold treatment — they are the heroes
- Add a soft blue glow (`rgba(10,132,255,0.45)`) behind the arc and plane marker
- Use deep, soft shadows (0.4 opacity, 16pt blur) — the black canvas swallows subtle ones
- Mirror the Live Activity layout in the status bar for cross-surface consistency

### Don't
- Don't use pure black `#000000` as the canvas — `#0B0B0F` keeps depth in the map
- Don't use status colors decoratively — green/amber/red mean exactly on-time/delayed/cancelled
- Don't add accent colors beyond blue — the restraint makes the status colors read instantly
- Don't let chrome compete with the map — the arc must be the strongest element
- Don't use proportional figures for times — they jitter as data updates; use tabular
- Don't use thin or black font weights — stay at 400 / 600 / 700
- Don't shadow the map or timeline — depth is layered surfaces, not drop shadows
- Don't animate the plane in steps — it must glide smoothly along the arc
- Don't bury the status chip — it is the fastest-read element on a flight card

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Flight times drop to 28pt; map sheet peek detent shorter |
| iPhone 13/14/15 | 390pt | Standard 32pt times, full-bleed map |
| iPhone 15/16 Pro | 393pt | Dynamic Island hosts the Live Activity; nav adapts |
| iPhone 15/16 Pro Max | 430pt | Times scale to 36pt, larger map viewport |
| iPad | 768pt+ | Map + flight detail side by side; timeline in a right rail |

### Dynamic Type
- Flight times, route codes, body: full scale
- Status chip, gate/terminal, timeline timestamps, tab labels: fixed (tabular, layout-sensitive)
- On-time % ring label: scales modestly within the ring
- Map labels: fixed

### Orientation
- Flights / Airport / Profile: **portrait-locked**
- Live flight map: **rotates to landscape** — the arc gets a wider canvas
- Share card export: renders at a fixed aspect regardless of device orientation

### Touch Targets
- Primary CTA: 48pt+ tall
- Card action icons: 22pt glyph, 44pt hit area
- Map sheet grabber: 44pt drag zone
- Tab bar icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: nav respects safe area; the live map bleeds under the status bar with a subtle top scrim
- Bottom: tab bar floats above the home indicator with dark blur; map sheet detents respect it
- Dynamic Island: the live flight is surfaced as a Live Activity that matches the in-app status bar

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0B0B0F`
- Surface 1 (cards): `#1A1A1F`
- Surface 2 (sheets/status bar): `#222228`
- Divider: `#2E2E36`
- Text primary: `#FFFFFF`
- Text secondary: `#8E8E96`
- Flighty Blue (action / arc): `#0A84FF`
- Blue pressed: `#0066CC`
- On-time green: `#30D158`
- Delay amber: `#FFD60A`
- Cancelled red: `#FF453A`

### Example Component Prompts
- "Create a SwiftUI Flighty flight card on a `#0B0B0F` canvas: `#1A1A1F` card, 16pt radius, 1pt `#2E2E36` border. Header: 'United · UA 482' 17pt weight 600 white + a trailing green status chip 'ON TIME' (`#30D158` text on `rgba(48,209,88,0.15)`, 13pt w700 uppercase, full pill). Route block: 'SFO' 24pt w700 — thin blue arc glyph with a plane — 'JFK' 24pt w700. Time row: '7:45 AM' and '4:12 PM' at 32pt weight 700 with tabular figures, 'Gate B24 · Term 2' in 15pt monospaced beneath. Meta '5h 42m · 2,586 mi · Nonstop' 13pt `#8E8E96`."
- "Build the Flighty live flight map: a full-bleed dark map (land `#16161B`, water `#0B0B0F`, faint `#1F1F26` graticule). Draw a great-circle arc — flown portion solid `#0A84FF` 2.5pt, remaining portion dashed `rgba(10,132,255,0.35)` 2pt. Place a white plane glyph at the live position rotated to heading with a soft `rgba(10,132,255,0.45)` glow halo. Origin/destination 8pt white-ringed dots with code labels. The arc animates in from origin to the plane over ~900ms ease-out."
- "Design a Flighty status chip component with three states: 'ON TIME' (`#30D158` on `rgba(48,209,88,0.15)`), 'DELAYED 22m' (`#FFD60A` on `rgba(255,214,10,0.15)`), 'CANCELLED' (`#FF453A` on `rgba(255,69,58,0.15)`) — all 13pt weight 700 uppercase, 5pt vertical / 10pt horizontal padding, full-pill radius."
- "Create the Flighty Live-Activity-style status bar: a 64pt `#222228` card, 16pt radius. Left: airline chip + 'UA 482'. Center: a horizontal progress track with an origin dot, a blue-filled segment, a plane at the current percentage, and a destination dot. Right: a status chip + ETA '4:12 PM' in tabular figures. Match the Dynamic Island Live Activity layout exactly."
- "Build a Flighty delay timeline: a vertical 2pt `#2E2E36` rail with 12pt colored node dots — green `#30D158`, amber `#FFD60A`, red `#FF453A`. Each row: an 11pt monospaced timestamp, a 15pt weight 600 event title, and a colored delta like '+22m'. The latest node emits a soft pulsing ring in its status color."

### Iteration Guide
1. Canvas is `#0B0B0F` — deep instrument-panel black, NOT pure `#000000`
2. Flighty Blue (`#0A84FF`) only for the primary action, the live arc, and active states
3. Status colors are strict: green=on-time, amber=delayed, red=cancelled — chips only, never decorative
4. The live flight map arc is the hero — the map exists to host the glowing great-circle
5. All flight data uses tabular figures so times/gate/% don't jitter on update
6. Departure/arrival times get 32pt bold — the largest type on screen
7. Add a soft blue glow behind the arc + plane (`rgba(10,132,255,0.45)`) — the only colored light
8. Shadows are deep and soft (0.4 opacity, 16pt blur) — the black canvas swallows subtle ones
