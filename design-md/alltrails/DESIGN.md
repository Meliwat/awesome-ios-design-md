# Design System Inspiration of AllTrails (iOS)

## 1. Visual Theme & Atmosphere

AllTrails' iOS app is a clean, outdoorsy light space organized around two things: the **map** and the **trail card**. The canvas is pure white (`#FFFFFF`) with a faintly cool sage surface (`#F2F4F1`) for grouped sections — the off-white of a topographic map's paper. The app feels practical and trustworthy rather than decorative: it is a tool people use to decide where to hike and then navigate while they do it, often outdoors in bright sun, so contrast is high, type is legible, and trail photography does the emotional work. The visual language says "reliable field guide", not "lifestyle magazine".

The accent system is led by **AllTrails Green** (`#428000`) — a deep, natural forest green that carries the brand. It is the primary action, the active tab, the "Record" button, the selected map pin. Alongside it sits a small, functional **difficulty scale**: Easy is the same green (`#428000`), Moderate is amber (`#C77700`), Hard is a muted brick red (`#B3261E`). This three-step scale appears as a pill on every trail card and is the single most information-dense color decision in the app — a hiker scanning a list reads difficulty by color before reading a word. Beyond green and the difficulty scale, everything is neutral: near-black ink (`#1A1A1A`) for text, a warm grey (`#6B6F68`) for secondary. The star rating uses a warm gold but the palette is otherwise restrained.

Typography is Inter (AllTrails' product face is a clean humanist sans in this family / the system font), used at 400 / 600 / 700. It is functional and highly legible — sized for reading a trail name and its stats at a glance, possibly on a phone strapped to a pack in the sun. Headlines are medium-large (24-28pt), trail names are 17pt semibold, and the stat row (length / elevation / time) is compact 14pt. The most expressive moment is the **route trace draw**: when a trail detail or navigation view opens, the route line animates as if being drawn onto the topographic map, tracing the path from trailhead to summit.

**Key Characteristics:**
- Pure-white canvas with a cool sage section surface (`#F2F4F1`) — topo-map paper
- AllTrails Green (`#428000`) as the lead accent — action, active tab, Record, selected pin
- Difficulty scale as a color language — Easy green / Moderate amber / Hard brick red
- The trail card — photo, name, difficulty pill, length/elevation/time stats, star rating (signature)
- Map-first explore — a topographic map base with trail pins is the primary surface
- Record GPS button — a prominent green capture control for live tracking
- Photo-review row — community trail photos scrolling horizontally
- Practical, high-contrast, legible — designed to be read outdoors in bright sun
- Bottom tabs (Explore / Saved / Navigator / Community / Profile)
- Inter typeface, 400/600/700 — functional field-guide legibility

## 2. Color Palette & Roles

### Primary
- **AllTrails Green** (`#428000`): The lead accent — primary CTA, active tab, the "Record" button, selected map pin, "Easy" difficulty.
- **Green Pressed** (`#336300`): Pressed/active state for green controls.
- **Green Soft** (`#E8F0DF`): Green at low intensity — selected-chip background, subtle highlights, the recording-active banner.

### Difficulty Scale (Color = Meaning)
- **Easy** (`#428000`): Easy-rated trails (same as AllTrails Green — easy is "go").
- **Moderate** (`#C77700`): Moderate-rated trails (a clear amber).
- **Hard** (`#B3261E`): Hard-rated trails (a muted brick red — caution, not alarm).
- Each appears as a difficulty pill on every trail card and as the pin tint on the map.

### Canvas & Surfaces
- **Canvas White** (`#FFFFFF`): The primary canvas and card backgrounds.
- **Surface Sage** (`#F2F4F1`): The cool off-white grouped-section surface — topo-paper.
- **Surface Raised** (`#FFFFFF`): White cards lifting off sage with a soft neutral shadow.
- **Surface Sunken** (`#EBEEE8`): Pressed state on sage surfaces, input fills.
- **Divider** (`#E2E6DF`): Hairline separators — a soft sage-grey.

### Text
- **Text Primary** (`#1A1A1A`): Trail names, headlines, stats, primary copy — a near-black.
- **Text Secondary** (`#6B6F68`): Location, descriptions, metadata, labels (warm grey).
- **Text Tertiary** (`#9A9E96`): Very low-emphasis metadata, placeholder text, disabled labels.

### Semantic
- **Star Gold** (`#F2A93B`): The rating stars (a warm amber-gold, used only for ratings).
- **Recording Live** (`#B3261E`): The live-recording pulse dot during an active GPS track.
- **Trail Open / Good** (`#428000`): "Open", "Good conditions" status (reuses brand green).
- **Trail Caution** (`#C77700`): "Partially closed", "Muddy" conditions (reuses Moderate amber).
- **Trail Closed** (`#B3261E`): "Closed", "Hazard" conditions (reuses Hard red).
- **Map Route Line** (`#428000`): The traced route polyline on the map (brand green, 4pt, rounded).

### Light Mode
AllTrails' iOS app is light-first by design — the high-contrast topo-paper light space is built to be read outdoors in bright sun. A dark mode exists for OS parity and night navigation (Canvas `#121512`, Surface `#1C201B`, Text `#ECEFEA`, with Green and the difficulty scale preserved), but the light experience is primary and is what this spec describes.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` — AllTrails' product face is a clean humanist sans in this family (system font on device)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: tabular figures for the stat row and ratings (`font-variant-numeric: tabular-nums`) so trail stats align in a list

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 28pt | 700 | 1.2 | -0.4pt | "Explore", "Saved" large nav title |
| Trail Detail Title | Inter | 24pt | 700 | 1.25 | -0.3pt | Trail name on the detail hero |
| Section Header | Inter | 20pt | 700 | 1.3 | -0.2pt | "Nearby trails", "Reviews" |
| Card Title (Trail Name) | Inter | 17pt | 600 | 1.3 | -0.1pt | Trail name on a trail card |
| Body | Inter | 16pt | 400 | 1.55 | 0pt | Trail descriptions, review text |
| Stat Value | Inter | 15pt | 700 | 1.2 | 0pt | "5.2 mi", "1,200 ft", "2h 45m" |
| Subtitle / Location | Inter | 14pt | 400 | 1.4 | 0pt | "Marin County, California" |
| Difficulty Pill | Inter | 13pt | 700 | 1.0 | 0.3pt | "EASY" / "MODERATE" / "HARD" |
| Stat Label | Inter | 13pt | 400 | 1.3 | 0pt | "Length", "Elevation gain" |
| Button (Primary) | Inter | 16pt | 600 | 1.0 | 0.1pt | "Record", "Directions", "Save" |
| Rating Value | Inter | 14pt | 700 | 1.2 | 0pt | "4.7" beside the star row |
| Meta / Review Count | Inter | 13pt | 400 | 1.3 | 0pt | "(1,284 reviews)", timestamps |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab labels |
| Map Pin Label | Inter | 12pt | 700 | 1.0 | 0pt | Distance/price-style label on a map pin |
| Label (UPPER) | Inter | 12pt | 700 | 1.2 | 0.8pt | "TRAIL", "OUT & BACK" tags |

### Principles
- **Legible in the field**: type is sized and weighted to be read in bright sun on a moving phone — high contrast, no thin weights, generous body leading (1.55).
- **The trail name leads the card; the stat row supports**: name 17pt w600, stats 15pt w700 in a compact tabular row.
- **Tabular numerals on stats**: length, elevation, time, and ratings use tabular figures so a scrolling list of trail cards keeps its columns aligned.
- **Weight band 400 / 600 / 700**: regular for body, semibold for trail names/buttons, bold for titles/stats/difficulty. No light, no black.
- **Color encodes difficulty, not emphasis**: the green/amber/red scale is reserved for the difficulty pill, map pins, and conditions — never for prose stress.

## 4. Component Stylings

### Trail Card *(signature)*
- Full content width (list) or 260pt (horizontal shelf), photo on top
- Photo: 4:3 ratio, corner radius 12pt, with a saved-heart button top-right (white glyph, subtle shadow; filled green when saved) and a photo-count chip bottom-left
- Below the photo:
  - Row 1: difficulty pill (left) + star rating "★ 4.7 (1,284)" (right) — pill is `EASY`/`MODERATE`/`HARD` in 13pt w700 uppercase on the difficulty color at ~14% as a soft fill with the color as text, or solid for emphasis
  - Row 2: trail name — Inter 17pt w600 `#1A1A1A`, 1-line clamp
  - Row 3: location — Inter 14pt `#6B6F68`
  - Row 4: stat row — "Length 5.2 mi · Est. 2h 45m · ↑ 1,200 ft" in 14-15pt, value w700, label w400 `#6B6F68`, tabular
- Tap: scale 0.98 + soft haptic; whole card is the target

### Difficulty Pill
- Shape: pill, height 22pt
- Easy: text `#428000` on `#E8F0DF` (or solid `#428000` with white text for the detail hero)
- Moderate: text `#C77700` on `#FBEEDD`
- Hard: text `#B3261E` on `#F7E0DE`
- Font: Inter 13pt w700 uppercase, 0.3pt tracking, 6pt vertical / 12pt horizontal padding, radius 11pt

### Buttons

**Primary CTA**
- Shape: Pill, height 50pt
- Background: `#428000`
- Text: `#FFFFFF`, Inter 16pt w600, optional leading SF Symbol 18pt
- Padding: 14pt vertical, 28pt horizontal, corner radius 25pt
- Pressed: background `#336300`, scale 0.97, soft haptic
- Used for: "Directions", "Save", "Get Pro"

**Record Button** *(signature)*
- A prominent capture control: 60pt circle, `#428000` fill, white `record.circle`-style glyph 26pt, soft green shadow `rgba(66,128,0,0.30) 0 6px 18px`
- During an active recording: morphs to a `stop.fill` in a `#B3261E` circle with a subtle pulse, and a live banner appears (`#E8F0DF` background, recording dot `#B3261E`, elapsed time + distance)

**Outline / Secondary Button**
- Background: transparent, 1.5pt border `#428000`
- Text: `#428000`, Inter 16pt w600
- Padding: 13pt vertical, 24pt horizontal, corner radius 25pt
- Pressed: background `#E8F0DF`

**Icon Actions (Save, Share, Directions, Filter)**
- Size: 20pt glyph, 44pt hit area
- Default: `#6B6F68`; Active/pressed: `#428000`
- Save filled becomes solid `#428000` heart

### Cards & Containers

**Map Pin (Trail marker)**
- A teardrop or rounded pill pin on the map tinted by difficulty (green/amber/red)
- Selected: enlarges ~1.2×, gains a white ring and a soft shadow, and surfaces a mini trail card sheet at the bottom
- Cluster pin: a green circle with a count when zoomed out

**Trail Detail Hero**
- Full-bleed trail photo (or the map), ~280pt, with a back/share overlay row
- Below: trail name 24pt w700, a difficulty pill + star rating row, the stat row, then a primary button row (Directions / Save / Record)

**Review Card**
- Background `#FFFFFF`, 1pt `#E2E6DF` border, corner radius 12pt, padding 16pt
- Reviewer avatar 32pt + name 15pt w600 + date 13pt `#6B6F68`
- Star row (gold) + review body 16pt `#1A1A1A`
- Optional photo thumbnails row

**Photo-Review Row** *(signature)*
- A horizontal scroll of square community photos (88pt), 8pt gap, corner radius 10pt
- A trailing "See all 240" tile

**Conditions Card**
- Background `#F2F4F1`, corner radius 12pt
- A status chip (Open green / Caution amber / Closed red) + a short condition note + "Updated 2 days ago" meta

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.96)` with `.regularMaterial` blur, hairline `#E2E6DF` top border
- Tabs: Explore, Saved, Navigator, Community, Profile
- Icon size: 24pt
- Active color: `#428000`
- Inactive: `#9A9E96`
- Labels: Inter 11pt w600, always shown
- The center "Navigator" tab is slightly emphasized (it leads to live navigation)

**Top Bar / Search**
- On Explore: a floating rounded search bar over the map — "Search by city, park, or trail" — white, soft shadow, 50pt tall, leading `magnifyingglass`, trailing filter glyph
- On lists: a standard large title with a filter/sort row of chips beneath

**Map / List Toggle**
- A floating pill control ("Map" / "List") that switches between the topo map and the trail list — `#FFFFFF`, soft shadow, the active half filled `#428000` with white text

### Input Fields

**Search Bar**
- Background: `#FFFFFF`, height 50pt, corner radius 25pt, soft shadow `rgba(0,0,0,0.12) 0 4px 12px`
- Leading `magnifyingglass` 18pt `#6B6F68`
- Placeholder: "Search by city, park, or trail", 16pt `#9A9E96`
- Focus: 1.5pt border `#428000`

**Filter Chip**
- Background: `#FFFFFF` (1pt `#E2E6DF`) default / `#E8F0DF` with `#428000` text + border when selected
- Padding: 8pt vertical, 16pt horizontal, corner radius 18pt
- Examples: "Easy", "Dogs allowed", "Waterfall", "Under 5 mi"

### Distinctive Components

**Map-First Explore** — the defining surface: a topographic map base with difficulty-tinted trail pins, a floating search bar, and a Map/List toggle. The map is the primary way to find a trail.

**Route Trace** — on a trail detail / navigation view, the route polyline (`#428000`, 4pt, rounded joins) animates as if being drawn from the trailhead, plus elevation-profile chart below.

**Record / Live Tracking** — the green Record button starts a GPS track; a live banner shows elapsed time, distance, and pace with a `#B3261E` pulse; the route draws live on the map as you move.

**Elevation Profile** — a small area chart of the trail's elevation gain along distance, in green with a soft fill, beneath the trail stats.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard horizontal margin: 16pt
- Section spacing: 24pt between major blocks

### Grid & Container
- Single column, full device width with 16pt horizontal margins
- Trail list: full-width cards stacked, 16pt vertical gap
- Horizontal shelves: 260pt cards, 12pt gap, peek the next card
- The map is full-bleed behind floating controls on Explore

### Whitespace Philosophy
- **Practical density**: trail cards are information-rich (photo + name + difficulty + stats + rating) but spaced cleanly so a hiker can scan a list quickly.
- **The map gets the screen**: on Explore the map is full-bleed; UI floats above it rather than boxing it in.
- **Legibility over decoration**: generous body leading and clear stat rows beat ornamental layout — this is a field tool.

### Border Radius Scale
- Soft (10pt): photo thumbnails, review-photo tiles
- Standard (12pt): trail-card photo, review cards, conditions cards
- Comfortable (16-18pt): filter chips, sheets
- Pill (25pt): primary/outline buttons, search bar, Map/List toggle
- Pill-small (11pt): difficulty pill
- Circle (50%): Record button, avatars, map cluster pins

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; sage `#F2F4F1` fill | Grouped sections, the canvas |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 4px 12px` + white fill | Trail cards, review cards lifting off sage |
| Floating Control (Level 2) | `rgba(0,0,0,0.12) 0 4px 14px` | The search bar, Map/List toggle over the map |
| Record / FAB (Level 3) | `rgba(66,128,0,0.30) 0 6px 18px` (green-tinted) | The Record capture button |
| Map Pin Selected | `rgba(0,0,0,0.25) 0 4px 10px` | The enlarged selected trail pin |
| Sheet (Level 4) | `rgba(0,0,0,0.18) 0 -16px 48px` + white | The mini-card sheet, filter sheet |
| Tab Bar Material | `rgba(255,255,255,0.96)` + `.regularMaterial` | Floats above scrolling content |

**Shadow Philosophy**: AllTrails uses soft, neutral shadows for the general UI (cards, controls floating over the map) so they read cleanly against bright photography and the topo base. The one exception is the Record button, whose shadow is green-tinted so it feels like the brand's primary capture moment. Shadows are diffuse (12-18pt blur) and low opacity — the app stays clean and practical, never heavy.

### Motion
- **Route trace draw**: the route polyline draws from trailhead to end over ~1000ms ease-out; the elevation profile fills in sync
- **Map pin select**: pin scales ~1.2× with a white ring, the mini trail card slides up from the bottom over 300ms ease-out
- **Card tap**: scale 0.98 → 1.0 over 200ms ease-out, soft haptic
- **Map/List toggle**: the active fill slides between halves over 250ms ease
- **Record start**: button morphs green circle → red stop, the live banner expands down, a single firm haptic
- **Live tracking**: the route polyline extends in real time as GPS points arrive; a `#B3261E` dot pulses
- **Save (heart)**: heart fills green + a subtle scale bounce 1.0 → 1.15 → 1.0 over 300ms
- **Screen transitions**: standard push; the map cross-dissolves when switching to/from list

## 7. Do's and Don'ts

### Do
- Use a pure-white canvas with a cool sage section surface (`#F2F4F1`) — topo-paper
- Lead with AllTrails Green (`#428000`) for the primary action, active tab, and Record
- Use the difficulty scale as a color language — Easy green / Moderate amber / Hard red — on every trail card and map pin
- Make the trail card the workhorse — photo, difficulty pill, name, location, tabular stat row, rating
- Keep the map full-bleed on Explore with controls floating above it
- Use tabular figures for the stat row and ratings so a scrolling list keeps its columns
- Animate the route trace as if drawing the path onto the map (~1000ms)
- Give the Record button a green-tinted shadow — it is the brand's capture moment
- Keep type high-contrast and legible — this app is read outdoors in bright sun

### Don't
- Don't use a low-contrast or moody aesthetic — AllTrails must be readable in the field
- Don't repurpose the difficulty colors for decoration — green/amber/red mean difficulty/conditions
- Don't box the map in — on Explore it is full-bleed; UI floats over it
- Don't use thin type weights — legibility outdoors requires 400+ and high contrast
- Don't reflow trail stats with proportional figures — always tabular-nums
- Don't make the Record button ambiguous — it is a prominent green circle that becomes a red stop
- Don't over-decorate cards — they are a practical field guide, not a lifestyle feed
- Don't color body text for emphasis — the difficulty scale is reserved for difficulty/conditions
- Don't animate harshly — the route trace and transitions are smooth, 200-1000ms

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Trail-card photo shorter; stat row may wrap to 2 lines |
| iPhone 13/14/15 | 390pt | Standard full-width trail cards |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the floating search bar |
| iPhone 15/16 Pro Max | 430pt | Larger trail photos; map shows more pins |
| iPad | 768pt+ | Split view: map on one side, trail list on the other; 2-column card grid |

### Dynamic Type
- Titles, trail names, body, reviews: full scale
- Stat values and ratings: scale modestly but stay tabular so list columns hold
- Difficulty pill, tab labels, map-pin labels: fixed (layout-sensitive at 11-13pt)
- The stat row gracefully wraps to two lines at the largest sizes

### Orientation
- Explore (map) / Saved / Community / Profile: **portrait and landscape** — the map benefits from landscape
- Navigator (live navigation): **rotates** — landscape is useful while driving to the trailhead
- iPad: **rotates** — split map+list layout adapts

### Touch Targets
- Primary CTA: 50pt tall; Record button: 60pt circle — easy to hit with gloves/cold hands
- Trail card: the entire card is one tap target
- Map pins: enlarged hit area (~44pt) even though the glyph is smaller
- Tab icons: 24pt glyph, 44pt effective hit
- Save/share icons: 20pt glyph, 44pt hit area

### Safe Area Handling
- Top: floating search bar respects the notch / Dynamic Island
- Bottom: tab bar and the Record button float above the home indicator
- Sides: 16pt content insets; the map extends edge-to-edge under floating controls
- Live navigation keeps critical info clear of the safe-area insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface sage: `#F2F4F1`
- Surface sunken (pressed): `#EBEEE8`
- Divider: `#E2E6DF`
- Text primary: `#1A1A1A`
- Text secondary: `#6B6F68`
- AllTrails Green (accent): `#428000`
- Green pressed: `#336300`
- Difficulty — Easy `#428000`, Moderate `#C77700`, Hard `#B3261E`
- Star gold: `#F2A93B`

### Example Component Prompts
- "Create a SwiftUI AllTrails trail card: full width, a 4:3 photo with 12pt corner radius, a white saved-heart button top-right (filled #428000 when saved), a photo-count chip bottom-left. Below: a row with a difficulty pill ('MODERATE' Inter 13pt weight 700 uppercase, #C77700 text on #FBEEDD, 11pt radius) on the left and '★ 4.7 (1,284)' on the right; then the trail name Inter 17pt weight 600 #1A1A1A; then location Inter 14pt #6B6F68; then a tabular stat row 'Length 5.2 mi · Est. 2h 45m · ↑ 1,200 ft' with values weight 700 and labels weight 400 #6B6F68. Whole card tappable; pressed scale 0.98."
- "Build the AllTrails Record button: a 60pt circle, #428000 fill, white record glyph 26pt, green-tinted shadow rgba(66,128,0,0.30) 0 6px 18px. On an active recording it morphs to a stop.fill in a #B3261E circle with a subtle pulse, and a live banner appears (#E8F0DF background, a #B3261E recording dot, elapsed time + distance in tabular Inter)."
- "Design the map-first Explore: a full-bleed topographic map with difficulty-tinted teardrop pins (green Easy / amber Moderate / red Hard). A floating white search bar at the top ('Search by city, park, or trail', 50pt tall, 25pt radius, soft shadow, leading magnifyingglass). A floating Map/List toggle pill bottom-center with the active half filled #428000. Selecting a pin enlarges it ~1.2× with a white ring and slides a mini trail card up from the bottom."
- "Create the difficulty pill set: three pills, Inter 13pt weight 700 uppercase 0.3pt tracking, 6pt vertical / 12pt horizontal padding, 11pt radius. Easy: #428000 text on #E8F0DF. Moderate: #C77700 text on #FBEEDD. Hard: #B3261E text on #F7E0DE. (Solid color fill with white text for the trail-detail hero variant.)"
- "Build the AllTrails bottom tab bar: 56pt + safe area, background rgba(255,255,255,0.96) with .regularMaterial blur, 1pt #E2E6DF top border. Tabs Explore / Saved / Navigator / Community / Profile, 24pt icons, active tint #428000, inactive #9A9E96, labels Inter 11pt weight 600. Slightly emphasize the center Navigator tab."

### Iteration Guide
1. Canvas is pure white with a cool sage section surface `#F2F4F1` — topo-paper, high contrast
2. AllTrails Green `#428000` leads (action, active tab, Record, selected pin)
3. The difficulty scale is a color language — Easy green / Moderate amber / Hard red — on every card and pin
4. The trail card is the workhorse — photo, difficulty pill, name, location, tabular stat row, rating
5. The map is full-bleed on Explore; controls float above it, never box it in
6. Use tabular figures for stats and ratings so a scrolling list keeps its columns aligned
7. Animate the route trace as if drawing the path onto the map (~1000ms ease-out)
8. The Record button is the one green-shadowed element — the brand's capture moment
9. AllTrails is light-first — a dark mode exists for OS parity / night nav but light is primary
