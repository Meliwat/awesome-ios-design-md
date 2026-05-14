# Design System Inspiration of Waze (iOS)

## 1. Visual Theme & Atmosphere

Waze's iOS app is a community-driven driving game disguised as a navigation app. Where Apple Maps wants you to forget the UI exists and Google Maps wants you to find a place, Waze wants you to drive with friends — and every visual decision flows from that emotional brief. The map is loud, the icons are cartoony, the hazard reports are technicolor speech bubbles, and the brand voice is closer to a children's puzzle book than a serious mapping product. This is by design: Waze's superpower is its drivers, and the playful aesthetic makes contributing (reporting a police trap, a pothole, a crash) feel like a game, not a chore.

The accent is **Waze Purple** (`#7E55BE`) — the iconic violet of the Waze chat-bubble logomark, the navigation polyline, and the floating action button. Surrounding it is **Waze Cyan** (`#33CCFF`) — used on the location puck and informational glyphs, and on the second layer of the Waze logo. Together they create the signature electric cool-tone duo that no other map app uses. Pinned over the map: red `#EF6A65` police reports, orange `#F69833` traffic, yellow `#F9C42E` road closures, green `#75C73E` cleared hazards, each rendered as a chunky cartoon speech bubble with a small Wazer emoji avatar. The map cartography is more saturated than Apple's — bright cyan water, mint-green parks, warm white land.

Typography is **Boing**, Waze's proprietary rounded sans designed for both wayfinding legibility and brand warmth — the rounded terminals echo the cartoon speech bubbles. Boing has Regular, Bold, and Black weights, plus a Boing Mono for in-app distance/time display. Where Boing isn't available, the fallback is `SF Pro Rounded` — the rounded variant of SF Pro on iOS — chosen specifically because Waze leans into roundness as a brand attribute. The hierarchy is bold and big: 28pt-32pt street names in Boing Black, 18pt step instructions, tabular figures on every "0.4 mi" or "12 min" with a slight letter spacing for breathing room at glance-while-driving.

**Key Characteristics:**
- **Waze Purple** (`#7E55BE`) as the primary brand — logo, polyline, FAB, primary CTAs
- **Waze Cyan** (`#33CCFF`) as the secondary brand — current-location puck, info glyphs, second logo color
- **Cartoon speech-bubble hazard reports** — Red police, Orange traffic, Yellow closures, Green cleared, with rounded corners and a triangular tail pointing to the location
- **Bright saturated cartography** — cyan water, mint parks, warm white roads — louder than Apple Maps or Google Maps
- **Boing typeface** — rounded proprietary sans by Waze; SF Pro Rounded as iOS fallback
- **Floating Wazer avatar** — the user's emoji-style mood character on the map, "happy", "stuck in traffic", "lucky"
- **Bottom-up reporting flow** — tap the floating "+" report button, choose hazard type from a wheel of cartoon icons
- **Big purple "Go" button** when starting navigation — full-width bottom CTA at 56pt height
- **Speed-limit tile and current-speed indicator** — circular badges in the bottom-left during navigation, US-style speed-limit signage
- **ETA shared with friends** — banner asking to share with Beacon, social moments built into the trip flow
- **Carpool integration** — Waze Carpool branding lives inside the same chrome (less prominent post-2024)

## 2. Color Palette & Roles

### Primary Brand
- **Waze Purple** (`#7E55BE`): The brand violet — logomark gradient stop, navigation polyline, FAB background, primary CTAs ("Go", "Start"), selected-state on category chips.
- **Waze Purple Deep** (`#5B3C9A`): Pressed state on FAB and primary buttons.
- **Waze Purple Tint** (`#E8DEF5`): Selected row backgrounds, info banner fills.
- **Waze Cyan** (`#33CCFF`): The cool counterpart — current-location puck, info icon fills, the lighter half of the gradient logo.
- **Cyan Deep** (`#0099E5`): Pressed state on cyan buttons, cyan glyphs.

### Hazard Report Colors (The Cartoon System)
- **Police Red** (`#EF6A65`): Police reports (speed traps, checkpoints) — chunky speech bubble.
- **Traffic Orange** (`#F69833`): Traffic jams, congestion alerts.
- **Closure Yellow** (`#F9C42E`): Road closures, construction.
- **Cleared Green** (`#75C73E`): Recently cleared hazards (with timer), gas-station deals.
- **Hazard Brown** (`#8B6F47`): Potholes, debris, hazard-on-shoulder.
- **Camera Gray** (`#6B6B6B`): Speed cameras, red-light cameras.

### Map Cartography
- **Map Cream** (`#FFFCF2`): Warm white land surface (slightly cooler than Apple Maps).
- **Map Water** (`#9BDEEF`): Bright cyan water — louder and cooler than Apple's water blue.
- **Map Park** (`#C5E89B`): Mint green parks.
- **Map Road Major** (`#FFFFFF`): Major roads, white with a soft outline.
- **Map Road Minor** (`#F5F0E5`): Minor roads.
- **Map Highway** (`#FFD970`): Highway fill, slightly more saturated than Apple's.
- **Map Building** (`#E8E2D1`): Building footprints.
- **Map Label** (`#3D3D3D`): Map label text — slate gray.

### Canvas, Surfaces & Dividers
- **Card Canvas** (`#FFFFFF`): Place card, sheets, bottom panels.
- **Surface Gray** (`#F5F5F7`): Search field, section backgrounds.
- **Surface Gray 2** (`#EAEAEC`): Pressed states.
- **Divider** (`#D6D6D9`): Hairlines.

### Text
- **Ink** (`#1A1A1A`): Primary text — Waze's slightly warm near-black.
- **Secondary** (`#6B6B6B`): Subtitles, addresses, ETA secondary info.
- **Tertiary** (`#A0A0A0`): Placeholder, disabled.
- **Inverse White** (`#FFFFFF`): Text on Waze Purple and other saturated backgrounds.

### Semantic
- **Success Green** (`#34C759`): "On your way" confirmations.
- **Warning Amber** (`#F9C42E`): Speed warning, slow traffic ahead.
- **Error Red** (`#EF6A65`): "Route blocked", form errors.
- **Info Cyan** (`#33CCFF`): Tips, info banners.

### Dark Mode (Night Driving Mode)
Waze's dark mode is engineered for night driving — a true near-black map with the same loud accent colors maintained for at-a-glance legibility.
- **Dark Map Land** (`#1E2026`): Cool charcoal over land.
- **Dark Map Water** (`#0F3D5E`): Deep teal-navy water.
- **Dark Map Park** (`#1F3A1F`): Dark forest.
- **Dark Card Surface** (`#262932`): Sheets, panels.
- **Dark Surface 2** (`#3A3D47`): Pressed states.
- **Dark Divider** (`#4A4D58`): Hairlines.
- **Dark Text Primary** (`#FFFFFF`).
- **Dark Text Secondary** (`#B8BAC5`).
- **Waze Purple (Dark)** (`#9F76DA`): Brightened for dark legibility.
- **Waze Cyan (Dark)** (`#5DD9FF`).

## 3. Typography Rules

### Font Family
- **Primary**: `Boing` — proprietary rounded sans by Waze, with Regular, Bold, and Black weights, plus a `Boing Mono` for tabular numerics
- **Fallback stack**: `'Boing', -apple-system-rounded, 'SF Pro Rounded', 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Quicksand', sans-serif`
- **Web/marketing Google Fonts substitute**: `Quicksand` (open-source rounded sans, closest to Boing's warmth) or `Nunito` for a more neutral fallback
- **iOS native fallback**: `SF Pro Rounded` — explicitly the rounded variant, available via `.systemRounded` design parameter on iOS 13+
- **Tabular figures**: Mandatory on distances ("0.4 mi"), times ("12 min"), ETAs ("6:14 PM"), speeds ("65 mph")

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Street Name | Boing | 32pt | 900 (Black) | 1.1 | -0.4pt | "Market Street" on next-turn card |
| Next Turn Distance | Boing Mono | 28pt | 700 (Bold) | 1.1 | 0pt tnum | "0.4 mi" |
| Step Title | Boing | 22pt | 700 | 1.2 | -0.2pt | "Turn right onto Market St" |
| Step Subtitle | Boing | 17pt | 400 | 1.3 | 0pt | "in 0.4 mi" |
| ETA Bottom Time | Boing Mono | 24pt | 700 | 1.1 | 0pt tnum | "12 min" |
| ETA Distance | Boing Mono | 17pt | 500 | 1.2 | 0pt tnum | "5.2 mi · 6:14 PM" |
| Place Card Title | Boing | 26pt | 700 | 1.15 | -0.3pt | "Tartine Bakery" |
| Place Card Subtitle | Boing | 15pt | 400 | 1.3 | 0pt | "Bakery · French · $$" |
| Search Placeholder | Boing | 17pt | 400 | 1.3 | 0pt | "Where to?" |
| Section Header | Boing | 13pt | 700 | 1.2 | 0.6pt UPPERCASE | "RECENT", "FAVORITES" |
| List Row Title | Boing | 17pt | 500 | 1.3 | 0pt | Recent destination |
| List Row Subtitle | Boing | 13pt | 400 | 1.2 | 0pt | Address |
| Speed Limit Number | Boing Mono | 22pt | 900 (Black) | 1.0 | 0pt tnum | "35" in the speed-limit circle |
| Current Speed | Boing Mono | 32pt | 700 | 1.0 | 0pt tnum | "42" big number, mph small |
| Hazard Speech Title | Boing | 14pt | 700 | 1.1 | 0pt | "Police" / "Traffic" |
| Hazard Speech Time | Boing | 11pt | 400 | 1.1 | 0pt | "5 min ago" |
| Button Primary | Boing | 17pt | 700 | 1.0 | 0pt | "Go", "Start", "Report" |
| Caption | Boing | 12pt | 400 | 1.3 | 0pt | Photo credits, fine print |
| Tab Label | Boing | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Roundness is the brand**: Boing has rounded terminals on every glyph — the typography mirrors the cartoon speech bubbles and the rounded buttons. SF Pro Rounded is the only correct fallback.
- **Boing Mono for ALL numerics** — distances, times, speeds, ETAs. Variable widths break the navigation glance pattern.
- **Black (900) reserved for hero moments** — street names on next-turn cards, the speed-limit number. Almost everything else is Bold (700) or Regular (400).
- **Tight tracking at display sizes** (-0.4pt at 32pt), 0 at body.
- **Big and bold for driving glance**: street names are 32pt Black because the user reads them at 60 mph; subtle, light type is not appropriate
- **UPPERCASE section headers with 0.6pt tracking** — the same iOS rhythm as other apps, but in Boing Bold (700)
- **Dynamic Type honored on the place card and search results**; FIXED on the next-turn card, ETA bar, speed-limit number, hazard speech bubbles (all layout- and glance-critical)

## 4. Component Stylings

### The Cartoon Hazard Speech Bubble (The Signature)

The single most recognizable component in Waze. A chunky rounded rectangle that points to the hazard location like a speech bubble.

- **Container shape**: rounded rectangle with a triangular tail
- **Corner radius**: 14pt
- **Width**: variable — minimum 88pt, expands for content
- **Height**: 56pt (single-line) or 68pt (with timestamp)
- **Padding**: 8pt vertical, 12pt horizontal
- **Background**: solid hazard color at full saturation (e.g., Police Red `#EF6A65`)
- **Tail**: 12pt × 8pt isoceles triangle pointing down from the bottom-center, same color as the container
- **Icon**: 24pt cartoon-style glyph in white, leading position (e.g., police hat, exclamation, cone, snowflake)
- **Title**: Boing 14pt Bold white, e.g., "Police"
- **Timestamp (if shown)**: Boing 11pt Regular white at 80% opacity, e.g., "5 min ago"
- **Shadow**: `rgba(0,0,0,0.20) 0 4px 12px`
- **Hover/Tap state**: scale 1.0 → 1.08 over 200ms, with a subtle white inner glow

**Police Speech Bubble**: Background `#EF6A65`, icon = police hat with a small badge glyph (custom or `shield.fill`)
**Traffic Speech Bubble**: Background `#F69833`, icon = traffic cone or stopped-car
**Closure Speech Bubble**: Background `#F9C42E`, icon = no-entry circle or barrier
**Cleared Speech Bubble**: Background `#75C73E`, icon = checkmark + small timer
**Pothole Speech Bubble**: Background `#8B6F47`, icon = ground crack
**Camera Speech Bubble**: Background `#6B6B6B`, icon = camera

### Floating Action Button (Report)

- **Diameter**: 56pt
- **Position**: bottom-right, 16pt inset, 16pt above the bottom ETA bar
- **Background**: `#7E55BE` Waze Purple
- **Glyph**: a stylized white exclamation point or speech-bubble icon at 24pt
- **Shadow**: `rgba(126,85,190,0.40) 0 6px 16px` — tinted purple shadow
- **Pressed**: background `#5B3C9A`, scale 0.94
- **Tap**: opens the report wheel — a circular arrangement of 6-8 hazard icons that the user picks
- **Haptic**: `.impactOccurred(.medium)`

### Report Wheel (Hazard Picker)

The cartoon dial that appears when the user taps the FAB.

- **Position**: full-screen modal sheet from bottom
- **Background**: `#FFFFFF` at the wheel area, semi-transparent purple `rgba(126,85,190,0.85)` scrim over the rest of the screen
- **Wheel**: 280pt diameter circle, centered horizontally, 80pt from bottom
- **Slices**: 8 wedges, each containing a cartoon hazard icon (police, traffic, closure, pothole, camera, hazard, gas, cleared)
- **Slice icons**: 44pt × 44pt centered in each wedge
- **Slice colors**: each wedge is the hazard color at 20% saturation; tap saturates to full
- **Center**: 56pt circle with a white "X" close button
- **Animation**: wheel spins in from a small circle, 350ms spring
- **Tap on slice**: slice expands, then a confirmation card appears

### Current-Location Puck (Waze Style)

Waze's puck is distinct from Apple's — it's the iconic blue arrow shape.

- **Shape**: a chunky arrow/cone pointing in the heading direction, NOT a circular dot
- **Color**: `#33CCFF` Waze Cyan filled body with a 3pt white outline
- **Size**: 32pt wide × 36pt tall
- **Rotation**: rotates with heading
- **Shadow**: `rgba(0,0,0,0.30) 0 3px 8px` for visibility over saturated cartography
- **Pulse**: a subtle 60pt cyan outer ring at 0.15 opacity, expanding to 1.5x over 2 seconds infinite

### Wazer Avatar (Floating User Emoji)

A unique-to-Waze element: the user's chosen mood character appears on the map as a small emoji-style cartoon avatar.

- **Position**: floats alongside the location puck, 8pt up-and-right inset
- **Size**: 24pt × 24pt
- **Shape**: a circle with the Wazer mood emoji inside (Happy, Stuck, Lucky, Tired, etc.)
- **Border**: 2pt white ring
- **Pop animation**: when chosen, scales 0 → 1.2 → 1.0 with a 400ms spring

### The Next-Turn Card (Top of Screen During Navigation)

The hero component while driving.

- **Position**: top of screen below safe area, full-width
- **Height**: 100pt
- **Background**: solid `#7E55BE` Waze Purple
- **Layout**: leading 64pt × 64pt direction-arrow icon (white, custom Waze direction glyph) + main content + trailing exit-route button
- **Direction arrow**: rotated to match the turn (right, left, slight, sharp)
- **Distance row**: Boing Mono 28pt Bold white tabular, e.g., "0.4 mi"
- **Street name**: Boing 22pt Bold white, e.g., "Market Street", 2-line truncate
- **Sub-instruction**: Boing 14pt Regular white at 80% opacity, e.g., "then turn left in 0.6 mi"
- **Corner radius**: 0pt on top (extends into safe area), 16pt on bottom corners
- **Tap**: opens the full directions list

### ETA Bottom Bar

- **Position**: sticky bottom, full-width
- **Height**: 80pt + safe area
- **Background**: `#FFFFFF`
- **Layout**: left side ETA info + center route alternative pill + right side "Stop" button
- **ETA time**: Boing Mono 24pt Bold `#1A1A1A` tabular, e.g., "12 min"
- **Distance + arrival**: Boing Mono 17pt Medium `#6B6B6B` tabular, "5.2 mi · 6:14 PM"
- **Alternative route pill** (when offered): a centered green `#75C73E` pill with "Save 3 min" — tap to switch routes
- **Stop button**: outlined red `#EF6A65` pill at right with white background, "End"

### Speed Limit & Current Speed Tile (Bottom-Left)

The classic US-style speed-limit signage rendered as a tile.

- **Container**: 80pt × 80pt
- **Background**: `#FFFFFF` with `rgba(0,0,0,0.10) 0 4px 8px` shadow
- **Corner radius**: 12pt
- **Speed limit**: top section — a 40pt × 40pt white circle with 2pt `#1A1A1A` border, "35" in Boing Mono 22pt Black centered
- **Above the circle**: "SPEED LIMIT" in Boing 9pt Bold UPPERCASE `#1A1A1A`
- **Current speed**: bottom section — large number "42" in Boing Mono 32pt Bold `#1A1A1A`, "mph" in Boing 11pt Regular `#6B6B6B`
- **Speeding indicator**: when current speed > limit, current speed number flips to `#EF6A65` red and a small `exclamationmark` icon appears

### Search Card (Bottom Sheet)

- **Detents**: small (88pt — just search bar), medium (~50%), large (full-screen)
- **Background**: `#FFFFFF` (NOT translucent — Waze uses a solid white card unlike Apple)
- **Top corner radius**: 16pt
- **Drag indicator**: 36pt × 5pt rounded rect in `#D6D6D9`, 8pt below top
- **Search field**:
  - Height: 48pt
  - Background: `#F5F5F7`
  - Corner radius: 16pt (more pill-like than Apple's)
  - Leading: `magnifyingglass` SF Symbol 17pt `#6B6B6B`
  - Placeholder: "Where to?" Boing 17pt Regular `#A0A0A0`
- **Quick destination row** (below search): horizontal scroll of Home, Work, recents — each a 44pt-tall pill with icon + label
- **Favorites grid**: 2-col grid of 80pt-tall tiles with destination icon and name

### Buttons

**Primary CTA — The Big Purple "Go" Button**
- Used at the bottom of search results to start navigation
- Background: `#7E55BE` Waze Purple
- Text: `#FFFFFF`, Boing 17pt Bold
- Padding: 16pt vertical, 32pt horizontal
- Corner radius: 28pt (full pill at 56pt height)
- Height: 56pt
- Pressed: background `#5B3C9A`, scale 0.97
- Tinted purple shadow: `rgba(126,85,190,0.30) 0 4px 12px`

**Secondary Pill (Save / Share)**
- Background: `#FFFFFF`
- Border: 1.5pt `#7E55BE`
- Text: `#7E55BE`, Boing 17pt Bold
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 24pt (full pill)
- Height: 48pt

**Text-Only Action**
- Text: `#7E55BE`, Boing 17pt Bold
- 44pt hit target

**Cancel Pill**
- Background: `#FFFFFF`
- Border: 1.5pt `#EF6A65`
- Text: `#EF6A65`, Boing 17pt Bold
- Same metrics as Secondary Pill

### Distinctive Components

**Beacon Share Banner**
- Appears mid-trip: a horizontal `#7E55BE` purple banner offering "Share with friends via Beacon"
- Height: 56pt
- White text + white user avatar circles overlapping
- Sliding dismiss

**Route Polyline**
- Stroke: `#7E55BE` Waze Purple, 6pt width (thicker than Apple's 5pt)
- Cap: rounded
- Animated chevron pattern moving along the route at 1 chevron per 2 seconds — the "ant trail" effect
- Alternative routes shown in `#9F9F9F` gray, 4pt width

**Mood Selector (Wazer Picker)**
- Modal sheet with 6-8 cartoon mood icons in a grid
- Each is a 64pt circle with the cartoon emoji centered
- Tap selects, scales 1.0 → 1.15 → 1.0 with a "pop" sound effect

**Gas Price Pin (Map Overlay)**
- A small green `#75C73E` pill with the gas price displayed in Boing Mono Bold
- Icon prefix: gas pump glyph
- Used on gas stations along the route

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 56, 64, 80
- Standard horizontal margin: 16pt on cards
- Section vertical gap: 24pt between sections in the search card; 16pt between rows

### Grid & Container
- Map: full-screen, edge-to-edge
- Next-turn card: full-width, top-edge to 16pt below safe area
- ETA bar: full-width sticky bottom
- Search card: bottom sheet with three detents

### Whitespace Philosophy
- **Loud, not cramped**: hazard speech bubbles are chunky with 8pt internal padding — they're meant to be readable at 60 mph from 18 inches away
- **Big buttons, big targets**: the "Go" button is 56pt tall, the FAB is 56pt diameter — Waze is a one-handed driving app
- **The map breathes**: even with hazard pins, the cartography colors remain visible — pins overlay, never replace

### Border Radius Scale
- Square (0pt): rare
- Small (8pt): place card sections
- Standard (12pt): speed-limit tile, photo cards
- Card (14pt): hazard speech bubbles, primary card containers
- Sheet (16pt): bottom sheet top, search field
- Pill (24pt-28pt): secondary buttons (24pt), "Go" button (28pt)
- Circle (50%): FAB, mood avatars, speed-limit inner circle

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Map, ETA bar background |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 2px 8px` | Place card, search card |
| Floating (Level 2) | `rgba(0,0,0,0.10) 0 4px 8px` | Speed-limit tile |
| Hazard (Level 3) | `rgba(0,0,0,0.20) 0 4px 12px` | Hazard speech bubbles, gas pins |
| FAB (Level 4) | `rgba(126,85,190,0.40) 0 6px 16px` | The Report FAB — TINTED PURPLE shadow |
| "Go" Button (Level 4) | `rgba(126,85,190,0.30) 0 4px 12px` | Primary purple button |
| Overlay (Level 5) | `rgba(0,0,0,0.40) 0 0 0 9999px` | Report wheel scrim |

**Shadow Philosophy**: Waze uses heavier and more saturated shadows than other map apps because the cartography is loud — soft 0.08 shadows would disappear against the bright cyan water and mint parks. Hazard speech bubbles get a 0.20-opacity drop shadow to lift them off the map. The FAB and "Go" button use tinted purple shadows (matching the brand) to reinforce identity at the most prominent action points.

### Motion
- **FAB tap**: scale 1.0 → 0.94 → 1.0 over 200ms; shadow tightens then releases
- **Report wheel open**: 350ms spring (damping 0.7) — wheel spins in from a small circle to full size
- **Hazard speech bubble appearance**: scale 0 → 1.15 → 1.0 with a 400ms spring "pop"
- **Hazard speech bubble dismiss (timer expiring)**: 1000ms fade with a slight rotation; for cleared hazards, a green checkmark animates over the top before dismiss
- **Current-location puck**: subtle 2000ms pulse infinite, scale 1.0 → 1.5 → 1.0, opacity 0.15 → 0 → 0.15
- **Polyline ant trail**: chevron pattern animates along the route at 1 chevron per 2 seconds — the "moving" indicator
- **Next-turn card update**: when the next turn changes, the card cross-fades with a 250ms ease + the direction arrow rotates to match
- **Reach destination**: a confetti/sparkle animation over the destination pin with `.notificationOccurred(.success)` haptic
- **"Go" button tap**: scale 0.97, then `.impactOccurred(.heavy)` haptic; immediately transitions to navigation mode with a 400ms cross-fade

## 7. Do's and Don'ts

### Do
- Use Waze Purple `#7E55BE` as the brand primary — logo, polyline, FAB, "Go" button
- Use Waze Cyan `#33CCFF` as the secondary — current-location puck, info glyphs
- Render hazard reports as chunky cartoon speech bubbles with rounded 14pt corners, full-saturation colors, white icons inside, and a triangular tail pointing to the hazard location
- Use Boing typography (or SF Pro Rounded as fallback) — roundness is the brand
- Use Boing Mono on every numeric — distance, time, speed, ETA
- Make the map LOUD — cyan water, mint parks, warm white roads
- Render the current-location puck as a cyan arrow (NOT a dot) with a chunky 3pt white outline
- Show the speed-limit + current-speed tile in the bottom-left during navigation, US-style sign format
- Use the tinted purple shadow on the FAB `rgba(126,85,190,0.40) 0 6px 16px` — reinforces brand
- Animate hazard speech bubbles with a "pop" — scale 0 → 1.15 → 1.0 spring
- Use the chevron ant-trail animation on the route polyline
- Keep the report wheel a literal cartoon dial with 8 wedges — it's playful by design

### Don't
- Don't use a plain blue location dot — Waze's puck is a CYAN ARROW, not a circular dot
- Don't render hazard reports as small SF Symbol pins — they're chunky cartoon speech bubbles with tails
- Don't tone down the cartography — bright cyan water and mint parks are the signature; making them subtle defeats the brand
- Don't replace Boing with a sharp grotesque (Helvetica, Inter without rounding) — the warmth comes from roundness
- Don't make the "Go" button anything other than full-pill 56pt-tall Waze Purple
- Don't omit Boing Mono on numerics — variable widths break the at-a-glance read
- Don't use a generic black drop shadow on the FAB — the tinted purple shadow is signature
- Don't use Waze Purple at less than 100% saturation for primary actions — the deep violet is the brand
- Don't show police hazard reports without the rounded cartoon shape — the cartoon-ness is what makes contributing fun
- Don't position the FAB on the left — it lives bottom-right
- Don't use the dot pulse from Apple Maps — Waze's pulse is a 60pt cyan outer ring at 0.15 opacity, expanding to 1.5x

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Next-turn card compresses; report wheel sized down to 240pt |
| iPhone 13/14/15 | 390pt | Standard layout — full-size report wheel, 100pt next-turn card |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in next-turn card with custom inset |
| iPhone 15/16 Pro Max | 430pt | Slightly larger speed-limit tile and FAB |
| iPad Mini | 744pt | Search card becomes a 360pt left sidebar; map fills the rest |
| iPad Pro 11"+ | 834pt+ | Persistent sidebar + map + right-side details pane |

### Dynamic Type
- Place card title, body, list rows: full scale
- Next-turn card, ETA numbers, speed-limit, hazard speech bubble text: FIXED (glance- and layout-critical)
- Map labels: zoom-controlled, fixed at user's preferred Dynamic Type within limits

### Orientation
- Portrait: default
- Landscape: next-turn card stays at top but compresses to 64pt height; speed tile moves to top-right
- iPad: persistent sidebar across orientations

### Touch Targets
- FAB: 56pt
- "Go" button: 56pt height, 200pt+ width
- Hazard speech bubble: full bubble is tappable for confirmation/dismissal
- Speed tile: tap opens speed settings
- Map controls: 44pt each
- Wazer avatar: tap opens mood picker, 32pt hit area on 24pt glyph

### Safe Area Handling
- Top: next-turn card extends into safe area with 16pt content padding from the safe area top
- Bottom: ETA bar respects safe area; FAB sits 16pt above ETA bar's safe-area inset

## 9. Quick Reference Cheat Sheet

### Quick Color Reference
- **Waze Purple**: `#7E55BE`
- **Waze Purple Deep**: `#5B3C9A`
- **Waze Cyan**: `#33CCFF`
- **Cyan Deep**: `#0099E5`
- **Police Red**: `#EF6A65`
- **Traffic Orange**: `#F69833`
- **Closure Yellow**: `#F9C42E`
- **Cleared Green**: `#75C73E`
- Map cream: `#FFFCF2`
- Map water (bright cyan): `#9BDEEF`
- Map park (mint): `#C5E89B`
- Ink: `#1A1A1A`
- Secondary: `#6B6B6B`

### Example Component Prompts
- "Create a SwiftUI Waze hazard speech bubble for a Police report: a 14pt-corner rounded rectangle in `#EF6A65` Police Red with a 12pt × 8pt triangular tail pointing down from the bottom-center. Inside: a 24pt cartoon police-hat icon in white on the left, then 'Police' in Boing 14pt Bold white, then '5 min ago' in Boing 11pt Regular white at 80% opacity. Shadow `rgba(0,0,0,0.20) 0 4px 12px`. Appear animation: scale 0 → 1.15 → 1.0 over 400ms spring."
- "Build the Waze current-location puck: a chunky 32pt × 36pt cyan `#33CCFF` arrow shape pointing in the heading direction (rotates with user heading), with a 3pt white outer outline. Shadow `rgba(0,0,0,0.30) 0 3px 8px`. Add a 60pt outer cyan ring at 0.15 opacity that pulses scale 1.0 → 1.5 → 1.0 over 2000ms infinite."
- "Design the Waze next-turn card: top of screen, full-width, 100pt tall, solid `#7E55BE` Waze Purple background with 16pt bottom corner radius. Leading 64pt white direction-arrow icon rotated to match the turn. Then '0.4 mi' in Boing Mono 28pt Bold white tabular, 'Market Street' in Boing 22pt Bold white, and 'then turn left in 0.6 mi' in Boing 14pt Regular white at 80% opacity."
- "Build the Waze 'Go' button: full-pill at 56pt height, `#7E55BE` Waze Purple background, 'Go' in Boing 17pt Bold white centered. Tinted purple shadow `rgba(126,85,190,0.30) 0 4px 12px`. On press: background flips to `#5B3C9A` Deep Purple, scale 0.97, haptic `.impactOccurred(.heavy)`. Transitions to navigation mode with a 400ms cross-fade."
- "Create the Waze speed-limit + current-speed tile (bottom-left during navigation): 80pt × 80pt white card with 12pt corner radius and `rgba(0,0,0,0.10) 0 4px 8px` shadow. Top section: 'SPEED LIMIT' in Boing 9pt Bold UPPERCASE `#1A1A1A`, then a 40pt white circle with a 2pt `#1A1A1A` border and '35' in Boing Mono 22pt Black centered. Bottom section: '42' in Boing Mono 32pt Bold `#1A1A1A`, 'mph' in Boing 11pt Regular `#6B6B6B`. If current > limit, the current speed number flips to `#EF6A65` red."

### Iteration Guide
1. Brand is Waze Purple `#7E55BE` + Waze Cyan `#33CCFF` — both saturated, no muted variants for primary chrome
2. Hazard reports are chunky CARTOON SPEECH BUBBLES — rounded rectangles with triangular tails, full-saturation colors, white icons
3. Current-location puck is a CYAN ARROW (`#33CCFF`), not a dot — chunky 32×36pt with a 3pt white outline
4. Cartography is loud: cyan water `#9BDEEF`, mint parks `#C5E89B`, warm white roads
5. Boing typography or SF Pro Rounded fallback — roundness is non-negotiable; Boing Mono on every numeric
6. Tinted purple shadow on the FAB and "Go" button — `rgba(126,85,190,0.40)` and `rgba(126,85,190,0.30)`
7. Speech-bubble pop animation: scale 0 → 1.15 → 1.0 in 400ms spring — this is the signature appearance
8. Route polyline is 6pt thick `#7E55BE`, animated chevron ant-trail moving at 1 chevron per 2 seconds
9. The report wheel is a literal cartoon dial — embrace it, don't tone it down
10. Dark mode keeps both Waze Purple and Cyan at brand saturation — only the cartography darkens
