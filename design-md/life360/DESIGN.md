# Design System Inspiration of Life360 (iOS)

## 1. Visual Theme & Atmosphere

Life360's iOS app is a **map-first family safety app**, and almost everything about its design serves one job: let a parent open the app and instantly see where everyone is. The home screen is a full-bleed interactive map. There is no feed, no dashboard, no list to scroll first — you launch the app and you are looking at a map of your family, with each person represented by a circular photo pin dropped on their real location. The atmosphere is "reassuring control": a calm violet brand, warm member colors, soft rounded surfaces, and a deliberately un-alarming tone that only turns urgent (red) for genuine safety events.

The spine of the app is the **family map**. A dark-styled map fills the screen; member pins (a circular avatar in a white ring with a downward pointer) sit on each person's location; tapping a pin or rising the bottom sheet reveals a member list with name, current place, "X min ago," and battery. A floating **Circle selector** pill sits at the top ("The Carters ▾") because Life360 organizes families into "Circles" — a household is one Circle, the kids' carpool might be another. A draggable **bottom member sheet** lists everyone with their status. This map + circle-selector + member-sheet trio is the entire core experience; Places, Driving, and Safety are secondary tabs that all refer back to it.

The single most important design rule is **member identity color**. Every person in a Circle is assigned a fixed color (amber, teal, pink, blue, purple, etc.). That color is *the same everywhere that person appears*: their map pin ring, their avatar in the member sheet, their trail/breadcrumb line, their entry in driving reports, their detail screen header. A parent doesn't read names on a busy map — they recognize "the teal one is Jordan." This consistent per-member hue is to Life360 what the method-color is to a developer tool: the primary information channel.

The brand color is **Life360 Purple** — a deep violet (`#582C83`) and its lighter UI tint (`#7E57C2`). It is the brand spine: the Circle selector, Place geofence circles, primary buttons, the active tab, the logo. It is *not* a member color and never used to represent a person. Safety semantics get two reserved colors: **Safe green** (`#34C759`) for "at a safe Place / arrived safely" and **SOS / Alert red** (`#FF6B6B`) for emergency, crash detection, and "left a Place" alerts. Red is rationed hard — the app's emotional design depends on red meaning *something is actually wrong*.

The map itself is dark-styled at night: a deep base (`#1A2138`), muted desaturated roads (`#2C3654`), darkened parks (`#1B3A2E`) and water (`#16314A`). This keeps the colorful member pins as the brightest, most salient thing on screen — exactly where attention should go. The night canvas behind chrome is a violet-tinted near-black (`#161325`).

Typography is a friendly, rounded-humanist sans (Life360's brand face is custom; **Plus Jakarta Sans** is the closest free analog) — approachable and legible, never corporate-cold. Hierarchy is simple: a bold 32pt Circle/screen title, member names at 18pt bold, place/status text at 13–15pt, body at 16pt. Weights stay in the 400–800 range with member names and CTAs bold to feel confident and reassuring.

**Key Characteristics:**
- Map-first home screen — full-bleed dark-styled map, no feed/list before it
- Member identity color — each person has a fixed hue used on pin + avatar + trail + reports, everywhere
- Circle selector pill — top-floating "The Carters ▾"; families are organized into Circles
- Member pins — circular avatar in a 3pt white ring with a downward pointer, dropped on the map
- Draggable member sheet — bottom sheet listing name + place + "X min ago" + battery
- Life360 Purple (`#582C83`/`#7E57C2`) — brand spine (Circle UI, Places, CTAs), never a member color
- Places & geofences — translucent purple circles on the map; arrive/leave alerts
- Safety colors reserved — Safe green `#34C759`, SOS/Alert red `#FF6B6B`, rationed hard
- Battery on every member row — green when healthy, red when low (a core safety signal)
- Rounded, reassuring surfaces — large 22–28pt radii, soft shadows, calm violet night canvas

## 2. Color Palette & Roles

### Primary (Brand — never a member color)
- **Life360 Purple** (`#582C83`): Core brand violet — logo, deep-fill accents, Place geofence stroke base.
- **Purple Light** (`#7E57C2`): The UI working tint — Circle selector accent, primary buttons, active tab, geofence fill.
- **Purple Bright** (`#8B5CF6`): Emphasis / highlighted selection, focused states.
- **Purple Pressed** (`#4A2470`): Pressed state of purple CTAs.

### Canvas & Surfaces (Dark — default)
- **Night Canvas** (`#161325`): Violet-tinted near-black behind chrome/sheets.
- **Surface 1** (`#1F1B33`): Cards, the Circle selector pill, popovers.
- **Surface 2** (`#2A2542`): Pressed rows, secondary chips, icon-button fills.
- **Sheet** (`#211D36`): The bottom member sheet and tab bar background.
- **Divider** (`#332E4D`): Hairline separators.
- **Border** (`#3D3759`): Pill/card borders, input borders.

### Map Palette (dark-styled — keeps member pins salient)
- **Map Base** (`#1A2138`): The map background.
- **Map Road** (`#2C3654`): Roads (desaturated, recede).
- **Map Park** (`#1B3A2E`): Parks / green space (darkened).
- **Map Water** (`#16314A`): Water bodies (darkened).
- **Map Label** (`#7E7A99`): Street/place labels on the map (low-emphasis).

### Canvas & Surfaces (Light)
- **Canvas White** (`#FFFFFF`): Light chrome/sheets.
- **Surface Gray** (`#F5F3FA`): Cards, Circle pill.
- **Map Base Light** (`#EDEFF5`): Day-styled map base.
- **Divider Light** (`#E6E3F0`): Hairline separators.

### Text
- **Text Primary Dark** (`#ECEAF5`): Primary text & member names on dark.
- **Text Secondary Dark** (`#A6A0C2`): Place text, "X min ago," metadata.
- **Text Tertiary Dark** (`#6F6990`): Disabled, low-emphasis, inactive tab.
- **Text Primary Light** (`#1C1830`): Primary text on light.
- **Text Secondary Light** (`#6F6990`): Metadata on light.

### Member Identity Colors (the semantic core)
Assigned per person, fixed for that person everywhere (pin ring, avatar, trail, reports, detail header). A Circle cycles through these in join order.

| Slot | Color | Use |
|------|-------|-----|
| Member Amber | `#F2A33C` | Member 1 identity |
| Member Teal | `#2DD4BF` | Member 2 identity |
| Member Pink | `#F472B6` | Member 3 identity |
| Member Blue | `#60A5FA` | Member 4 identity |
| Member Purple | `#7E57C2` | Member 5 identity (note: brand purple may be reserved — prefer a violet that reads distinct) |
| Member Coral | `#FB7185` | Member 6 identity |
| Member Lime | `#A3E635` | Member 7 identity |
| Member Sky | `#38BDF8` | Member 8 identity |

### Safety Semantic (reserved — rationed hard)
- **Safe Green** (`#34C759`): At a safe Place, arrived safely, healthy battery, "all good."
- **SOS / Alert Red** (`#FF6B6B`): Emergency SOS, Crash Detection, low battery, "left a Place" alert.
- **Warning Amber** (`#FFB020`): Driving event (hard brake / speeding), attention-needed.
- **Info Purple** (`#7E57C2`): Place arrival/neutral notifications (brand-tinted, non-urgent).

## 3. Typography Rules

### Font Family
- **Primary**: `Plus Jakarta Sans` (by Tokotype, SIL OFL) — the closest free analog to Life360's friendly rounded-humanist brand sans; warm, approachable, legible at small sizes on a map.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Not user-switchable**: a single brand face throughout.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Plus Jakarta Sans | 32pt | 800 | 1.15 | -0.5pt | Circle name / screen title |
| Title | Plus Jakarta Sans | 26pt | 700 | 1.2 | -0.3pt | "Family Map", section headers |
| Section | Plus Jakarta Sans | 22pt | 700 | 1.25 | -0.2pt | "Today's Driving", "Places" |
| Member Name | Plus Jakarta Sans | 18pt | 700 | 1.3 | -0.1pt | Member sheet & detail header name |
| Body | Plus Jakarta Sans | 16pt | 400 | 1.5 | 0pt | Notification copy, descriptions |
| Row Title | Plus Jakarta Sans | 15pt | 600 | 1.35 | 0pt | "At Home · 2 min ago" |
| Place / Status | Plus Jakarta Sans | 13pt | 600 | 1.3 | 0pt | "Driving · 28 mph", place subtitle |
| Meta | Plus Jakarta Sans | 14pt | 400 | 1.4 | 0pt | "Last updated just now" |
| Caption | Plus Jakarta Sans | 12pt | 600 | 1.3 | 0.4pt | "PLACE ALERT" eyebrow (uppercase) |
| Pin Initial | Plus Jakarta Sans | 16pt | 700 | 1.0 | 0pt | Letter inside an avatar-less pin |
| Tab Label | Plus Jakarta Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Plus Jakarta Sans | 15pt | 700 | 1.0 | 0pt | Primary CTAs |
| SOS Label | Plus Jakarta Sans | 15pt | 800 | 1.0 | 0.5pt | The SOS button text |

### Principles
- **Friendly, not corporate**: the rounded-humanist face and bold member names keep the tone reassuring — this is an app a worried parent opens.
- **Member names are always bold (700)**: the person is the subject; their name leads every row at weight 700 in their context.
- **Status is concise & secondary**: place/speed/time in 13–15pt secondary color — informative, never shouting.
- **Weights 400 / 600 / 700 / 800**: body 400, row/status 600, names/headings 700, display & SOS 800. No thin.
- **Uppercase only for tiny eyebrows**: "PLACE ALERT", "SOS ACTIVATED" at 12pt +0.4pt tracking. Never uppercase body.
- **Dynamic Type**: scale display/title/section/body/member-name; keep pin initials, tab labels, and map labels fixed (map-layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Add a Place / Save / Invite)**
- Shape: Pill, 28pt corner radius (fully rounded for tall buttons)
- Background: `#7E57C2` (Purple Light)
- Text: `#FFFFFF`, Plus Jakarta Sans 15pt weight 700
- Padding: 14pt vertical, 26pt horizontal
- Pressed: background `#4A2470` + scale 0.98
- Disabled: background `#2A2542`, text `#6F6990`

**Secondary Button (Check In / Cancel)**
- Background: `#2A2542` dark / `#F5F3FA` light
- Text: `#ECEAF5` dark / `#1C1830` light, weight 600
- Border: 1pt `#3D3759`
- Padding: 12pt vertical, 22pt horizontal, 24pt radius
- Pressed: background `#1F1B33`

**SOS Button (emergency)**
- Background: `#FF6B6B` (Alert Red)
- Text: `#FFFFFF`, Plus Jakarta Sans 15pt weight 800, +0.5pt tracking
- Padding: 14pt vertical, 26pt horizontal, 28pt radius
- Press behavior: press-and-hold with a radial fill progress ring; haptic ramp; releases an emergency flow
- The ONLY large red surface in the app — reserved for genuine emergency

**Text Button (Invite Members / link action)**
- Background: none
- Text: `#7E57C2`, Plus Jakarta Sans 14pt weight 700
- No underline; subtle opacity dip on press

**Icon Button (bell / map controls)**
- 30pt circle, `#2A2542` fill, 15pt glyph `#ECEAF5`
- Pressed: fill `#1F1B33`

### Core Atoms

**Member Pin (the atomic Life360 element)**
- A circular avatar (member photo) inside a 3pt solid `#FFFFFF` ring, 46pt diameter
- A downward triangular pointer (8pt) at the bottom anchoring it to the map point
- The ring is white; the *avatar background* (when no photo) is the member's identity color with their initial in white 16pt 700
- Soft drop shadow `rgba(0,0,0,0.5) 0 4px 12px` so it lifts off the dark map
- Selected: ring grows to 4pt + a subtle member-color glow halo

**Member Sheet Row**
- Leading 42pt avatar circle in the member's identity color (initial or photo)
- `m-info`: member name (18pt 700 `#ECEAF5`) + place/status line (13–15pt 600 `#A6A0C2`) with a small place/clock glyph
- Trailing battery indicator: a 22×11pt battery glyph + percentage; green `#34C759` when healthy, red `#FF6B6B` when low (≤20%)
- Full-row tap → that member's detail; row height ≥ 62pt

**Circle Selector Pill**
- Top-floating, `rgba(31,27,51,0.92)` with `backdrop-filter: blur(16px)`, 1pt `#3D3759`, 999pt radius
- Circle name (15pt 700) + chevron ▾ (tap → Circle switcher sheet)
- Trailing notification bell (30pt icon button), badge dot in `#FF6B6B` if unread alerts

**Place / Geofence**
- On the map: a translucent circle, fill `rgba(126,87,194,0.16)`, stroke 2pt `rgba(126,87,194,0.55)`
- A small place tag pill near it: `#1F1B33`, 1pt `#3D3759`, 999pt, leading emoji (🏠 / 🏫 / 🏢) + place name 11pt 700

**Status / Place Chip**
- Pill, 7pt dot + label, 12.5pt 700
- Safe: text `#34C759`, fill `rgba(52,199,89,0.14)`
- Place (neutral): text `#7E57C2`, fill `rgba(126,87,194,0.16)`
- Alert: text `#FF6B6B`, fill `rgba(255,107,107,0.14)`

**Card / Sheet**
- `#1F1B33`/`#211D36`, 16–22pt radius, soft shadow `rgba(0,0,0,0.4–0.5)`

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#211D36`, 0.5pt top border `#332E4D`
- Tabs (4): Map, Places, Driving, Safety
- Icon size: 21pt, 2pt stroke
- Active: `#7E57C2` (Purple Light) / Inactive: `#6F6990`
- Labels: Plus Jakarta Sans 10pt 600, always shown

**Bottom Member Sheet (draggable)**
- Detents: collapsed (peek showing ~2 rows), half, full
- `#211D36`, 22pt top radius, 38×4pt grabber, `rgba(0,0,0,0.5) 0 -8px 30px` shadow
- Drags with 1:1 finger tracking; snaps to nearest detent with spring damping 0.85

**Member Detail (push)**
- Header band tinted with the member's identity color (subtle gradient), large avatar, name (26pt 700)
- Tabs: Location · History · Driving · Battery
- Map snippet with their breadcrumb trail in their identity color

### Input Fields

**Search Place / Address Field**
- Height: 44pt, `#1F1B33`, 1pt `#3D3759`, 24pt radius
- Leading 16pt magnifier `#6F6990`, text 15pt `#ECEAF5`
- Focus: border `#7E57C2` + 2pt `rgba(126,87,194,0.4)` ring
- Placeholder "Search for a place" `#6F6990`

**Place Name Field**
- 44pt, `#1F1B33`, 1pt `#3D3759`, 16pt radius
- Leading emoji picker chip (tap to pick 🏠/🏫/🏢/⭐)

**Circle Invite Code Field**
- Segmented 6-cell code input, each cell `#2A2542` 12pt radius; active cell border `#7E57C2`

### Distinctive Components

**Member Pin + Identity Color**
- The atomic element and the brand's core idea: a person *is* their color on the map. See Core Atoms.

**Family Map**
- Dark-styled map (`#1A2138` base, muted roads/parks/water) so member pins are the brightest objects; pinch/pan; auto-frames all members.

**Circle Switcher**
- Sheet listing all the user's Circles (Household, Carpool, etc.), each with member avatar stack + member count; tap to switch the whole map context.

**Place Geofence + Alerts**
- Translucent purple circle on the map; arriving/leaving fires a notification ("Jordan arrived at School"); Safe-green for arrivals, Alert-red for unexpected departures.

**Battery Status Row**
- Every member row shows battery — a deliberate safety affordance (a dead phone = lost visibility). Green healthy, red ≤20%.

**Driving Report Card**
- Per-trip card: route mini-map with the member's identity-color trail, distance/duration, and event chips (Hard Brake amber, Speeding amber, Phone Usage). Weekly summary score.

**SOS / Emergency Flow**
- Press-and-hold SOS button with a radial fill + haptic ramp; on trigger, alerts Circle + (Premium) dispatch; full-screen red confirmation. The only place red dominates.

**Breadcrumb Trail**
- A member's recent path drawn as a line in *their* identity color with timestamped dots — instantly attributable without a legend.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48
- Member-sheet row padding: 10pt vertical / 18pt horizontal; row gap implicit via padding
- Floating chrome inset from edges: 16pt
- Section gap inside sheets: 20–24pt

### Grid & Container
- iPhone: the map is full-bleed (edge to edge, behind the status bar); all UI floats over it (Circle pill top, member sheet bottom)
- iPad: optional split — map (left/main) + a persistent member list panel (right ~340pt)
- Member sheet content is single-column rows; detail screens use a centered max-width

### Whitespace Philosophy
- **The map is the whitespace**: the dark map *is* the canvas; chrome floats over it and is kept minimal so the map breathes
- **Color over labels**: member identity hues do navigational work — the eye finds a person by color, so the map needs few text labels
- **Calm density**: the member sheet is information-rich (name + place + time + battery) but rows are tall and softly separated to stay reassuring, not busy
- **Red is whitespace-protected**: nothing red appears unless it's a real safety event — its rarity is part of the layout's emotional logic

### Border Radius Scale
- Square (0pt): the full-bleed map
- Standard (8pt): small inner chips, segmented cells
- Card (12pt): nested cards, list cards
- Comfortable (16pt): place fields, inner panels
- Sheet (22pt): the bottom member sheet, large sheets
- Pill / Button (28pt): primary & SOS buttons (tall pills)
- Pill (500pt): chips, the Circle selector, place tags
- Circle (50%): member pins, avatars, icon buttons

## 6. Depth & Elevation

Life360 uses real, soft elevation — chrome must visibly float above the map.

| Level | Treatment | Use |
|-------|-----------|-----|
| Map (Level 0) | The dark map itself, no shadow | Background canvas |
| Pin (Level 1) | `rgba(0,0,0,0.5) 0 4px 12px` | Member pins, place tags lifting off the map |
| Floating (Level 2) | `rgba(0,0,0,0.45) 0 6px 20px` + blur | Circle selector pill, map control buttons |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -8px 30px` | Bottom member sheet, Circle switcher, modals |
| Emergency Overlay | Full-screen `#FF6B6B`-tinted scrim | SOS activated confirmation |

**Shadow Philosophy**: Unlike flat developer tools, Life360 *needs* depth — a member pin or the member sheet must read as "above the map," because the map is a literal spatial plane. Shadows are soft and dark (0.45–0.5 opacity, large blur) to register over the dark map without harsh edges. Floating chrome (Circle pill, controls) also uses a backdrop blur so the map shows through faintly, reinforcing "this hovers over your family's location." The map itself never casts or receives shadow — it is the ground.

### Motion
- **Pin movement**: when a member's location updates, their pin animates along the path (not teleport) over ~600ms ease-in-out; the pin does a subtle scale 1.0→1.08→1.0 "pulse" on update
- **Map auto-frame**: on open or "recenter," the map eases (400ms) to fit all member pins with padding
- **Member sheet drag**: 1:1 finger tracking; release snaps to nearest detent with `spring(response: 0.3, damping: 0.85)`
- **Pin tap → focus**: tapping a pin eases the map to center it (350ms) and expands the sheet to that member; the pin ring grows + member-color glow
- **Geofence entry**: the place circle does a soft 1-pulse ripple (scale 1.0→1.06, fade) when a member enters; arrival toast slides down
- **SOS press**: press-and-hold draws a radial fill over ~1.5s with a haptic intensity ramp; release before complete cancels with a quick spring-back
- **Tab switch**: cross-fade content 200ms; the map persists underneath when switching to/from Map
- **Breadcrumb draw**: a trail animates in by drawing the polyline head-to-tail over ~500ms in the member's color
- **Haptics**: soft impact on sheet detent snap; selection tick on pin tap; escalating haptic during SOS hold; warning haptic on a new Alert-red notification

## 7. Do's and Don'ts

### Do
- Make the home screen a full-bleed dark-styled map — no feed/list before it
- Give every member a fixed identity color and use it everywhere they appear (pin, avatar, trail, reports)
- Use Life360 Purple (`#582C83`/`#7E57C2`) for brand/Circle/Places/CTAs — never to represent a person
- Style the map dark (`#1A2138` base, muted roads/parks/water) so member pins are the brightest objects
- Render member pins as a photo in a 3pt white ring with a downward pointer + soft shadow
- Show battery on every member row — it is a core safety signal (green healthy / red low)
- Reserve Safe green `#34C759` and SOS/Alert red `#FF6B6B` strictly for safety state — ration red hard
- Draw Place geofences as translucent purple circles with a place-emoji tag
- Float chrome over the map with soft dark shadows + backdrop blur
- Animate pin movement along a path (never teleport) and auto-frame all members

### Don't
- Don't open to a feed, dashboard, or list — the map is the home screen
- Don't reuse a member's color for another member, or use the brand purple as a member color
- Don't let red appear for non-safety events — its rarity is the emotional design
- Don't use a bright/day map style by default at night — colored pins must stay the most salient thing
- Don't flatten the pins or sheet — Life360 needs real soft elevation (it's a spatial plane)
- Don't drop the battery indicator from member rows — a dead phone is a safety event
- Don't make the SOS button a normal tap — it's press-and-hold with a fill + haptic ramp
- Don't label every pin with text — color is how members are identified; keep the map clean
- Don't use cold/corporate type — the face is friendly rounded-humanist, names are bold and warm
- Don't teleport pins on location update — animate along the path with a gentle pulse

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Member-sheet peek shows ~1.5 rows; Circle pill text truncates |
| iPhone 13/14/15 | 390pt | Standard map + floating chrome |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; Circle pill sits below it |
| iPhone 15/16 Pro Max | 430pt | Larger pins (50pt); sheet peek shows ~2.5 rows |
| iPad (portrait) | 768pt | Map + bottom sheet, wider pins; place fields centered |
| iPad (landscape) | 1024pt+ | Split: full-bleed map + persistent right member panel (~340pt) |

### Dynamic Type
- Display, Title, Section, Member Name, Body, Row Title: full scale
- Pin initials, tab labels, map labels, place-tag text: FIXED (map-layout-sensitive)
- Member-sheet rows grow row height to fit scaled name/status text

### Orientation
- Map and sheet support rotation; sheet detents recompute for landscape
- iPad landscape uses the split map + member-panel layout
- The map auto-reframes members on rotation

### Touch Targets
- Member pin: ≥46pt tap (the visible bubble)
- Member sheet row: full-row tap, ≥62pt tall
- Circle selector pill: ≥40pt tall, full-pill tap
- Tab bar icons: 21pt glyph, 44pt hit
- SOS button: ≥52pt tall, press-and-hold (not a quick tap)

### Safe Area Handling
- Top: the Circle selector pill sits below the safe area / Dynamic Island; the map extends behind it
- Bottom: the member sheet and tab bar respect the home indicator; sheet detents are computed from the safe area
- The map is full-bleed (extends under status bar and home indicator) — only chrome respects insets
- Keyboard: place-search field lifts above the keyboard; the sheet expands to accommodate

## 9. Agent Prompt Guide

### Quick Color Reference
- Night canvas (dark): `#161325` / light `#FFFFFF`
- Surface 1 (cards/pill): `#1F1B33`
- Sheet / tab bar: `#211D36`
- Map base: `#1A2138` (road `#2C3654`, park `#1B3A2E`, water `#16314A`)
- Divider / border: `#332E4D` / `#3D3759`
- Text primary: `#ECEAF5` dark / `#1C1830` light
- Text secondary: `#A6A0C2`
- Brand — Life360 Purple `#582C83`, Purple Light `#7E57C2`, Bright `#8B5CF6`
- Member colors: amber `#F2A33C`, teal `#2DD4BF`, pink `#F472B6`, blue `#60A5FA`, coral `#FB7185`
- Safe green `#34C759` · SOS/Alert red `#FF6B6B` · Warning amber `#FFB020`

### Example Component Prompts
- "Build the Life360 member pin in SwiftUI: a 46pt circle with the member's photo (or their identity color + white initial in Plus Jakarta Sans 16pt w700), wrapped in a 3pt solid white ring, with a downward 8pt triangular pointer below it anchoring it to the map point. Soft shadow `rgba(0,0,0,0.5) 0 4px 12px`. On selection, grow the ring to 4pt and add a faint glow halo in the member's identity color. The identity color is fixed for that person everywhere they appear."

- "Create the Life360 member sheet row: leading 42pt avatar circle filled with the member's identity color (initial or photo), then a column with the member name in Plus Jakarta Sans 18pt w700 `#ECEAF5` and a place/status line in 13–15pt w600 `#A6A0C2` with a small pin/clock glyph, then a trailing battery indicator (a 22×11pt battery glyph + percentage; green `#34C759` if >20%, red `#FF6B6B` if ≤20%). Row ≥62pt tall, full-row tap. Sheet background `#211D36`."

- "Render the Life360 Circle selector pill: top-floating, `rgba(31,27,51,0.92)` with a 16pt backdrop blur, 1pt `#3D3759` border, 999pt radius, sitting below the safe area. Circle name in Plus Jakarta Sans 15pt w700 `#ECEAF5` + a ▾ chevron, and a trailing 30pt bell icon button (`#2A2542` fill) with a `#FF6B6B` badge dot if there are unread alerts."

- "Style the Life360 family map dark: base `#1A2138`, roads `#2C3654`, parks `#1B3A2E`, water `#16314A`, labels `#7E7A99`. The point is to keep the colorful member pins the brightest, most salient objects on screen — never use a bright day style at night."

- "Build a Life360 Place geofence on the map: a translucent circle, fill `rgba(126,87,194,0.16)`, 2pt stroke `rgba(126,87,194,0.55)`, with a small tag pill near it (`#1F1B33` fill, 1pt `#3D3759`, 999pt radius, leading place emoji 🏠/🏫/🏢 + place name in Plus Jakarta Sans 11pt w700). On member entry, do a soft single ripple pulse (scale 1.0→1.06, fade)."

- "Create the Life360 SOS button: a `#FF6B6B` pill, white Plus Jakarta Sans 15pt w800 +0.5pt tracking, 14pt vertical / 26pt horizontal padding, 28pt radius. It is press-and-hold: on hold, draw a radial fill over ~1.5s with an escalating haptic ramp; release before complete springs back and cancels; completion launches the emergency flow. This is the only large red surface in the app."

- "Render the Life360 primary button: `#7E57C2` background, white Plus Jakarta Sans 15pt w700, 14pt vertical / 26pt horizontal padding, 28pt pill radius. Pressed: `#4A2470` + scale 0.98. Purple is the brand spine — never use it to represent a member."

### Iteration Guide
1. The home screen is a full-bleed, dark-styled map — never a feed or list first
2. Every member has a FIXED identity color used everywhere they appear (pin ring/avatar, trail, reports, detail) — color is how you find a person
3. Life360 Purple (`#582C83`/`#7E57C2`) is the brand spine (Circle UI, Places, CTAs) and is NEVER a member color
4. Member pins = photo in a 3pt white ring + downward pointer + soft shadow, sitting on the dark map
5. Style the map dark (`#1A2138` base, muted roads/parks/water) so colored pins stay the most salient thing
6. The draggable bottom member sheet shows name + place + "X min ago" + battery per member
7. Battery is on every member row — a deliberate safety signal (green healthy / red ≤20%)
8. Safe green `#34C759` and SOS/Alert red `#FF6B6B` are reserved for safety only — ration red hard
9. Places are translucent purple geofence circles with a place-emoji tag; arrive/leave fires alerts
10. Chrome floats over the map with soft dark shadows + backdrop blur; pins animate along paths, never teleport
