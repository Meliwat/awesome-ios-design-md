# Design System Inspiration of Zwift (iOS)

## 1. Visual Theme & Atmosphere

Zwift's iOS app is a window into a video game. Unlike a typical fitness tracker, the dominant surface is not a list or a card stack — it is a **full-bleed 3D virtual world**. When you ride, the screen is a rendered cycling game (Watopia's volcano, London's parks, a snow-capped Alpe), and every piece of interface is a **glassmorphic overlay** floating on top of it: a translucent route banner at the top, a row of big metric tiles at the bottom (the **rider HUD**: Power / Cadence / Heart-rate), an action bar, and ride-event toasts. The signature screen of Zwift is **the ride HUD**: your avatar on a road, the route name + lap progress, and three-to-five oversized numbers that must be readable from two metres away on an iPad propped in front of a turbo trainer.

The design philosophy is "game first, chrome second, numbers huge." Because the content behind the UI is a moving, full-color 3D scene, all chrome must be semi-transparent dark glass (`rgba(12,12,12,0.6)` with an 8pt blur) so the road, riders, and scenery read through it — never opaque cards that would block the world. The numbers are the other obsession: power, cadence, heart-rate, speed, distance, and W/kg are rendered in a **tall condensed display face** at 28–34pt+ so a sweating rider can glance at them mid-effort. Outside the ride — the home menu, route picker, workout library, profile, club hub — the app falls back to a more conventional dark app shell on a solid near-black base (`#161616`) with the same orange accent, but the *defining* experience is the heads-up overlay on the game.

The color system pairs a hot brand orange with a fixed, semantic data palette. **Zwift Orange (`#FC6719`)** is the brand action color: the Start Ride button, the rider's default jersey, route-progress fill, the active power-up, achievement bursts. Critically, the live ride metrics use a **fixed semantic color mapping** that Zwift users learn by muscle memory: **Power = orange (`#FC6719`)**, **Cadence = yellow (`#E8C547`)**, **Heart-rate = red (`#F0413E`)**, **W/kg = cyan (`#2BD4D9`)**, distance/time neutral white. This mapping is consistent across the phone app, the in-game HUD, and the companion overlays so a rider always knows which number is which by color alone. The app-shell surfaces use a neutral dark ladder (`#161616` → `#1F1F1F` → `#2A2A2A` → `#353535`), and achievement / segment-PR moments use a green (`#57C84D`).

Typography is a two-face system: a **tall condensed display face (Barlow Semi Condensed, the closest free analog to Zwift's in-game numerics)** for all big numbers, screen titles, route names, and buttons — chosen because condensed numerals pack maximum legibility into the small glass tiles — and **Inter** for body copy, list rows, captions, and settings where readability at small sizes matters more than punch. Every numeric read-out uses tabular figures so a `99` → `100` watt change does not make the HUD jump. Hierarchy: HUD metrics at 28–34pt/800 tabular, screen titles at 28pt/800 (often all-caps), section headers at 22pt/700, card/route titles at 18pt/700, body at 16pt/400 Inter, list rows at 15pt/600 Inter, captions at 13pt, and metric unit labels at 11pt/700 uppercase.

**Key Characteristics:**
- **Full-bleed 3D virtual world** is the canvas during a ride — the UI floats on a rendered game
- **Glassmorphic overlays** — all in-ride chrome is translucent dark glass (`rgba(12,12,12,0.6)` + blur)
- **Rider HUD** — big metric tiles (Power / Cadence / Heart-rate / W/kg) readable from 2m away
- **Fixed semantic metric colors** — Power orange, Cadence yellow, HR red, W/kg cyan (muscle-memory)
- **Zwift Orange (`#FC6719`)** is the brand action color — Start Ride, jersey, route progress, power-up
- **Tall condensed display face** (Barlow Semi Condensed analog) for all numbers/titles/buttons
- **Route banner** — translucent top strip: world + route name, distance/lap, orange progress bar
- **Power-Up** — a glanceable in-ride ability tile (orange when armed) — a game mechanic in the UI
- **Tabular figures** on every metric so the HUD never jumps as numbers change
- **App-shell fallback** — menus, route picker, workouts on a solid `#161616` dark base, same orange
- **Achievement bursts** — green (`#57C84D`) badges/route-completion + orange confetti moments
- Bottom action bar (Action / Power-Up / Workout / Riders / Menu) — game controls, not page nav

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Zwift Orange** (`#FC6719`): Brand action color — Start Ride, rider jersey, route-progress fill, armed power-up, primary CTAs, active nav.
- **Zwift Orange Pressed** (`#D9530E`): Pressed state on primary buttons.
- **Zwift Orange Soft** (`#3A2110`): ~12–20% orange tint — tonal fills, selected list rows, soft badges.

### Ride Metric Semantics (FIXED — do not remap)
- **Power** (`#FC6719`): Watts read-out (shares the brand orange — power is the hero metric).
- **Cadence** (`#E8C547`): RPM read-out — warm yellow.
- **Heart-Rate** (`#F0413E`): BPM read-out — red.
- **W/kg & Effort** (`#2BD4D9`): Watts-per-kilo, FTP %, effort gauge — cyan.
- **Speed / Distance / Time** (`#FFFFFF`): Neutral white — context, not effort.

### Glass Overlay Surfaces (over the 3D world)
- **Glass Panel** (`rgba(12,12,12,0.60)` + blur 8): HUD tiles, route banner, toasts.
- **Glass Panel Heavy** (`rgba(15,15,15,0.80)` + blur 12): action bar, modals over the world.
- **Glass Stroke** (`rgba(255,255,255,0.10)`): 1pt hairline on glass for definition.
- **Glass Button** (`rgba(255,255,255,0.10)` + blur): secondary in-ride glass buttons.

### App-Shell Surfaces (solid, off-ride)
- **App Canvas** (`#161616`): Menus, route picker, workouts, profile background.
- **Card Surface** (`#1F1F1F`): Route cards, list rows, sheets.
- **Surface 2** (`#2A2A2A`): Inputs, raised chips, segmented track.
- **Surface 3** (`#353535`): Thumbnails, hovered/pressed rows.
- **Divider** (`#303030`): Hairline dividers in the app shell.

### Text
- **Text Primary** (`#FFFFFF`): Titles, HUD numbers — pure white for max contrast on world + dark.
- **Text Secondary** (`#A8AAAD`): Captions, unit labels, metadata.
- **Text Tertiary** (`#6C6E72`): Disabled, low-emphasis.
- **Text on Orange** (`#FFFFFF`): Label on the orange CTA.

### Semantic
- **Success / Achievement** (`#57C84D`): Route-complete, badge unlock, segment PR.
- **Warning** (`#E8C547`): Trainer disconnect, low-battery sensor (shares cadence yellow).
- **Error** (`#F0413E`): Pairing failure, sync error (shares HR red).
- **Drop / Ride-On** (`#FC6719`): "Ride On!" kudos burst (brand orange).

### Color Roles Table

| Role | Value | Context |
|------|-------|---------|
| Brand action | `#FC6719` | Start Ride, jersey, progress, power-up |
| Power metric | `#FC6719` | Watts (hero number) |
| Cadence metric | `#E8C547` | RPM |
| Heart-rate metric | `#F0413E` | BPM |
| W/kg metric | `#2BD4D9` | Watts/kg, FTP%, effort |
| Glass panel | `rgba(12,12,12,0.60)` | In-ride overlays |
| Glass heavy | `rgba(15,15,15,0.80)` | Action bar, modals |
| App canvas | `#161616` | Off-ride shell |
| Card surface | `#1F1F1F` | Route/list cards |
| Surface 2 / 3 | `#2A2A2A` / `#353535` | Inputs, thumbs |
| Divider | `#303030` | App-shell hairlines |
| Text primary | `#FFFFFF` | Titles, HUD |
| Text secondary | `#A8AAAD` | Captions, units |
| Success | `#57C84D` | Achievements |

## 3. Typography Rules

### Font Family
- **Display / Numeric**: `Barlow Semi Condensed` (by Jeremy Tribby, SIL OFL) — the closest free analog to Zwift's in-game condensed numerics. Tall, condensed, high-impact; used for all big numbers, screen titles, route names, and button labels because condensed glyphs pack maximum legibility into small glass tiles viewed from a distance.
- **Body / UI**: `Inter` (by Rasmus Andersson, SIL OFL) — used for body copy, list rows, captions, settings, and any dense text where small-size readability beats punch.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numeric rule**: every metric (watts, rpm, bpm, W/kg, km, time, FTP%) renders with **tabular figures** so the HUD never jumps as numbers change.
- **All-caps for action**: screen titles and button labels are frequently uppercase (game-UI energy); body text is sentence case.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| HUD Metric (primary) | Barlow Semi Cond. | 34pt | 800 | 1.0 | -0.3pt | Watts/HR/cadence on glass — **tabular** |
| HUD Metric (compact) | Barlow Semi Cond. | 28pt | 800 | 1.0 | -0.2pt | Smaller tiles (W/kg, distance) — **tabular** |
| Screen Title | Barlow Semi Cond. | 28pt | 800 | 1.1 | 0.4pt | "RIDE", "WORKOUTS" — often all-caps |
| Section Header | Barlow Semi Cond. | 22pt | 700 | 1.2 | 0.2pt | "Choose Your Route" |
| Card / Route Title | Barlow Semi Cond. | 18pt | 700 | 1.25 | 0.2pt | "Watopia · Volcano Flat" |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Descriptions, event info, settings |
| List Row | Inter | 15pt | 600 | 1.35 | 0pt | Route/event/club rows |
| Caption | Inter | 13pt | 500 | 1.4 | 0pt | Distance/lap, timestamps, helper text |
| Metric Unit | Barlow Semi Cond. | 11pt | 700 | 1.0 | 0.6pt | WATTS / RPM / BPM — uppercase |
| Button | Barlow Semi Cond. | 16pt | 700 | 1.0 | 0.3pt | Primary button — uppercase |
| Tab / Action Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom action-bar labels |
| Badge | Barlow Semi Cond. | 11pt | 800 | 1.0 | 0.4pt | Achievement / power-up label — uppercase |

### Principles
- **Numbers are the product**: HUD metrics use the condensed display face at 28–34pt+, tabular, so they read from a trainer 2m away — never shrink them to fit.
- **Two faces, clear split**: condensed display for numbers/titles/buttons; Inter for body/lists/captions — never mix within one element.
- **Tabular figures mandatory on metrics**: a 2-digit → 3-digit watt change must not shift the HUD.
- **All-caps signals action / game energy**: titles and buttons; body stays sentence case for readability.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Inter regular/medium/semibold for text; Barlow bold/extrabold for display.
- **Dynamic Type with floors**: body, list rows, captions scale; HUD metrics, unit labels, tab labels stay fixed (the glass-tile layout and glance-legibility depend on it).

## 4. Component Stylings

### Buttons

**Primary Button (Start Ride / Join Event)**
- Shape: rounded rectangle, 10pt corner radius
- Background: `#FC6719` (Zwift Orange) / text `#FFFFFF`
- Padding: 14pt vertical, 30pt horizontal; full-width hero variant spans content inset
- Font: Barlow Semi Condensed 16pt weight 700, **uppercase**, +0.3pt tracking
- Pressed: background `#D9530E` + scale 0.98
- Disabled: `#2A2A2A` background, `#6C6E72` text

**Secondary / Outline Button (Free Ride)**
- Background: transparent
- Border: 1.5pt `rgba(255,255,255,0.5)` (on world) or `#353535` (in app shell)
- Text: `#FFFFFF`, Barlow Semi Condensed 14pt weight 700, uppercase
- Corner radius: 10pt; padding 11pt / 22pt
- Pressed: background `rgba(255,255,255,0.1)`

**Glass Button (in-ride: Change Route / Camera)**
- Background: `rgba(255,255,255,0.10)` + blur 8
- Text: `#FFFFFF`, Barlow Semi Condensed 14pt/700 uppercase
- Corner radius: 10pt; padding 11pt / 20pt
- Sits directly on the 3D world; 1pt `rgba(255,255,255,0.10)` stroke

**Text Button (View all worlds)**
- Background: transparent
- Text: `#FC6719`, Inter 14pt weight 700
- No border; used in the app shell only

**Power-Up Tile (in-ride game ability)**
- 56pt rounded square (10pt radius), glass when empty, **solid `#FC6719`** when armed
- Center: power-up glyph (aero helmet, feather, truck) 24pt white
- A brief orange pulse + haptic when collected; tap (or volume button) to deploy

### Core Atoms

**HUD Metric Tile** (the core atom)
- A glass tile: `rgba(12,12,12,0.60)` + blur 8, 12pt radius, ~10pt padding, min-width ~88pt
- Value: Barlow Semi Condensed 34pt/800, **tabular**, in the metric's fixed semantic color
- Unit: Barlow Semi Condensed 11pt/700 uppercase `#A8AAAD`, 4pt below the value
- Tiles sit in a row near the bottom of the world; 3–5 tiles depending on screen size
- Optional trend caret (▲/▼) for power vs. target during a structured workout

**Route Banner**
- Translucent top strip: `rgba(15,15,15,0.62)` + blur 8, 12pt radius, 14pt inset from edges
- Leading: 30pt orange flag/route badge
- Title: route name in Barlow Semi Condensed 16pt/700; sub: `12.4 / 19.3 km · Lap 2 of 3` Inter 11pt `#A8AAAD`
- Trailing/below: a 4pt progress bar, track `rgba(255,255,255,0.18)`, fill `#FC6719`

**Action Bar (in-ride)**
- Heavy glass: `rgba(18,18,18,0.92)` + blur 20, 0.5pt `#303030` top stroke, ~78pt incl. home indicator
- 5 controls: Action (U-turn/elbow/wave), Power-Up, Workout, Riders, Menu
- 22pt icons; default `#A8AAAD`; the armed Power-Up tints `#FC6719` with a soft fill
- Labels: Inter 10pt/600

**Route Card (off-ride picker)**
- `#1F1F1F` card, 12pt radius; a world thumbnail (16:9), an orange "elevation" sparkline
- Title (Barlow 18pt/700), stats row (distance / elevation / lead-in) Inter 13pt `#A8AAAD`
- Trailing chevron; tap → route detail with a "Start" CTA

**Event Row (rides / races / workouts list)**
- `#1F1F1F` row; leading time block (Barlow tabular), title (Inter 15pt/600), category badge (A/B/C/D color-coded), W/kg range in cyan
- Trailing "Join" pill (orange when within join window)

**Achievement Burst**
- Center toast on the world: green (`#57C84D`) badge + Barlow 11pt/800 uppercase label
- "ROUTE COMPLETE" / "NEW PR" / "LEVEL UP" + a brief orange confetti sweep + haptic

**Ride-On Burst ("Ride On!")**
- A small orange thumbs/lightning burst overlay when another rider gives you kudos — `#FC6719`, 600ms fade

### Navigation

**Bottom Action Bar (in-ride — game controls, not page nav)**
- See Action Bar above — Action / Power-Up / Workout / Riders / Menu
- This is the in-game control surface, distinct from the app-shell tab bar

**App-Shell Tab Bar (off-ride)**
- Height: ~64pt incl. home indicator; background `#161616` with 0.5pt `#303030` top border
- Tabs: Home, Workouts, Events, Clubs, Profile
- 22pt icons; active `#FC6719`; inactive `#6C6E72`; labels Inter 10pt/600
- No tint pill — active is icon color only

**Top Bar (app shell)**
- Screen title in Barlow Semi Condensed 28pt/800 (often uppercase), collapsing on scroll
- Leading back chevron; trailing contextual icon (filter, search, settings)

### Input Fields

**Search Field (route / event / rider search)**
- 40pt tall, `#2A2A2A` bg, 10pt radius, leading 16pt magnifier `#A8AAAD`
- Placeholder Inter 15pt `#6C6E72`; focus → 1.5pt `#FC6719` border

**Stepper / Slider (FTP, weight, trainer difficulty)**
- Track `#2A2A2A`; filled portion + thumb `#FC6719`; value in Barlow tabular
- Used in workout setup and profile

**Segmented Control (Free Ride / Workout / Event)**
- Track `#2A2A2A`, 10pt radius; selected segment `#FC6719` text-on-fill or `#353535` with white text; unselected `#A8AAAD`

### Distinctive Components

**The Rider HUD**
- The signature Zwift component: a row of glass metric tiles over the 3D world, each value in its fixed semantic color, big condensed tabular numerals — readable from a turbo trainer

**Glassmorphic Overlay System**
- Every in-ride panel is translucent dark glass + blur so the game reads through — never opaque

**Power-Up Mechanic**
- An in-UI game ability: a glass tile that fills solid orange when a power-up is armed, deployable mid-ride — a videogame affordance living in a fitness app

**Route / Elevation Profile**
- A live elevation strip showing the course; the rider's position marker advances along it; gradient % shown in a small glass chip; the climb segment can recolor by gradient

**Structured Workout Bar**
- During a workout: a colored interval graph (zones Z1–Z6 in a blue→red ramp) scrolling under a "now" line; target power vs. actual shown with the trend caret

**Achievement & Level System**
- XP bar, level-up bursts, route badges, drops (in-game currency) — green/orange game feedback

**Companion Mini-Map**
- A glass mini-map of the world with rider dots; tap to open the full map / "ride with" a friend

**Ride-On Kudos**
- The orange "Ride On!" burst — Zwift's signature social micro-interaction

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 10, 12, 14, 16, 20, 24, 32, 40, 56
- In-ride overlay inset: 14pt from screen edges (world bleeds full-screen behind)
- HUD tile gap: 8–12pt; tile internal padding ~10pt
- App-shell content inset: 16pt; card gap 12pt
- Section gap: 24pt

### Grid & Container
- In-ride: world is full-bleed; overlays are anchored (banner top, HUD bottom, action bar bottom)
- HUD: a centered/spread row of 3–5 equal tiles; drops to 3 on iPhone, up to 5 on iPad
- App shell: single column, 16pt insets; route grid can be 2-up on iPad
- Elevation profile: full-width strip, fixed ~64pt height

### Whitespace Philosophy
- **The world is the whitespace**: in-ride, negative space *is* the 3D scene — keep overlays minimal so the game breathes
- **Glass, not blocks**: never cover the world with opaque panels; translucency keeps spatial context
- **Numbers get maximum air**: HUD tiles are generously padded so the big numerals never feel cramped
- **App shell is conventional**: off-ride, standard dark-app spacing — cards, lists, 16pt insets

### Border Radius Scale
- Square (0pt): full-bleed world, elevation strip edges
- Subtle (4pt): micro chips, segment ticks
- Small (8pt): route badges, small glass chips
- Standard (10pt): buttons, power-up tile, glass buttons, search, segmented control
- Comfortable (12pt): HUD tiles, route banner, route cards, sheets
- Large (20pt): bottom sheets, modals
- Pill (500pt): "Join" pills, filter chips, category badges
- Circle (50%): rider avatars, mini-map dots

## 6. Depth & Elevation

Zwift's depth model is unusual: the *real* depth is the rendered 3D world itself (true perspective, parallax, lighting). The 2D UI sits in flat layers *above* the game, separated by translucency + blur rather than drop shadows — a shadow on glass would fight the scene.

| Layer | Treatment | Use |
|-------|-----------|-----|
| World (Layer -1) | Rendered 3D scene, real perspective/lighting | The ride itself |
| Glass (Layer 0) | `rgba(12,12,12,0.60)` + blur 8, 1pt `rgba(255,255,255,0.10)` stroke | HUD tiles, route banner |
| Glass Heavy (Layer 1) | `rgba(18,18,18,0.92)` + blur 20 | Action bar, in-ride modals |
| App Card (Layer 1, off-ride) | `#1F1F1F`, no shadow | Route/event cards in the shell |
| Floating (Layer 2) | `rgba(0,0,0,0.5) 0 8px 24px` (app shell only) | Sheets, dropdowns off-ride |
| Sheet (Layer 3) | `rgba(0,0,0,0.6) 0 -10px 32px` + 20pt top radius | Bottom sheets |
| Overlay scrim | `rgba(0,0,0,0.55)` | Behind modals (dims the world or shell) |

**Shadow Philosophy**: in-ride, depth is the game's real 3D rendering; UI separation is achieved with frosted glass + a 1pt light stroke, never drop shadows (which would look pasted onto the scene). Drop shadows are only used in the off-ride app shell for sheets and dropdowns, where there is no 3D scene to respect.

### Motion
- **HUD metric update**: numbers tick smoothly (no jump) — interpolate value changes over ~200ms; tabular figures keep width fixed
- **Power-Up collected**: tile fills orange with a 250ms scale-pop + glow + medium haptic; "armed" state pulses subtly
- **Power-Up deployed**: tile empties with a 200ms sweep + a world VFX (draft boost, etc.)
- **Achievement burst**: badge scales 0.8→1.0 (300ms spring), green glow, orange confetti sweep (~800ms), success haptic
- **Ride-On**: orange burst fades in/out over 600ms with a soft haptic
- **Route progress**: the orange bar advances continuously (linear, 1:1 with distance)
- **Overlay show/hide**: glass panels fade + 8pt slide (200ms ease-out); tap world to toggle HUD visibility
- **App-shell nav**: standard iOS push (300ms); route picker cards parallax slightly on scroll
- **Lap / segment**: a brief glass toast slides down (250ms), shows split time, auto-dismisses (3s)
- **Haptics**: medium on power-up collect/deploy, success on achievement, soft on Ride-On and lap

## 7. Do's and Don'ts

### Do
- Keep the 3D world full-bleed during a ride — it is the canvas, not a background image
- Make every in-ride panel glassmorphic (`rgba(12,12,12,0.6)` + blur) so the game reads through
- Use the fixed metric color mapping — Power orange, Cadence yellow, HR red, W/kg cyan — always
- Render HUD numbers in the tall condensed face at 28–34pt+, tabular, for 2-metre legibility
- Use Zwift Orange (`#FC6719`) for the brand actions — Start Ride, jersey, route progress, power-up
- Treat the Power-Up as a real game affordance (glass when empty, solid orange when armed)
- Interpolate metric value changes (~200ms) so the HUD ticks smoothly and never jumps
- Use uppercase Barlow for titles and buttons (game energy); Inter sentence-case for body
- Fall back to a conventional `#161616` dark app shell for menus / route picker / profile
- Celebrate achievements with green (`#57C84D`) + an orange confetti burst + haptic
- Separate UI from the world with frosted glass + a 1pt light stroke, not drop shadows

### Don't
- Don't cover the world with opaque panels — translucency preserves spatial context
- Don't remap the metric semantic colors — riders know them by muscle memory
- Don't shrink HUD numbers to fit more chrome — legibility from a trainer is non-negotiable
- Don't use proportional figures on metrics — tabular only, so the HUD doesn't jump
- Don't put drop shadows on in-ride glass — they look pasted onto the 3D scene
- Don't mix the two faces within one element (no Inter digits in a Barlow title, etc.)
- Don't use the app-shell solid surfaces over the world — those are off-ride only
- Don't make the bottom action bar a "page nav" — it's in-game controls (Action/Power-Up/etc.)
- Don't animate the world's UI aggressively — overlays fade/slide quietly (200–300ms)
- Don't lowercase button labels — Zwift's CTAs are uppercase, condensed, energetic
- Don't use orange for cadence/HR — orange is power + brand only

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | HUD shows 3 tiles; route banner sub-line truncates |
| iPhone 13 / 14 / 15 | 390pt | Standard 3-tile HUD |
| iPhone 15 / 16 Pro | 393pt | Dynamic Island avoided by the route banner; HUD unaffected |
| iPhone 15 / 16 Pro Max | 430pt | HUD can show 4 tiles; larger numerals |
| iPad (portrait) | 768pt | HUD 4–5 tiles; mini-map visible; route picker 2-up |
| iPad (landscape) | 1024pt+ | The primary "trainer" layout — full HUD, mini-map, elevation strip all visible |

### Dynamic Type
- Scales: body, list rows, captions, settings (Inter)
- Fixed: HUD metrics, metric unit labels, screen titles, button labels, tab labels (Barlow display — glance-legibility and glass-tile layout depend on it)
- Numeric metrics never scale with Dynamic Type but always retain tabular figures

### Orientation
- **In-ride: landscape-preferred** (the trainer layout) — the world + full HUD shine in landscape; portrait shows a reduced 3-tile HUD
- App shell: portrait-primary; supports rotation on iPad
- Elevation strip and mini-map re-flow per orientation

### Touch Targets
- HUD tiles: large by nature (~88pt+ wide), tap to expand a metric detail
- Power-Up tile: 56pt, 44pt+ effective hit
- Action-bar controls: 22pt glyph, 49pt hit
- App-shell rows: 44pt+ tall
- "Join" / category pills: 32pt+ tall

### Safe Area Handling
- In-ride: the route banner clears the Dynamic Island / notch; the action bar respects the home indicator; the world bleeds under both
- App shell: standard safe-area insets; tab bar respects the home indicator
- Keyboard (search): app-shell content scrolls above the keyboard
- Sides: 14pt overlay inset in-ride, 16pt content inset in the shell

## 9. Agent Prompt Guide

### Quick Color Reference
- Brand action / Power: `#FC6719`
- Orange pressed: `#D9530E`
- Cadence (RPM): `#E8C547`
- Heart-rate (BPM): `#F0413E`
- W/kg & effort: `#2BD4D9`
- Glass panel: `rgba(12,12,12,0.60)` + blur 8
- Glass heavy (action bar): `rgba(18,18,18,0.92)` + blur 20
- Glass stroke: `rgba(255,255,255,0.10)`
- App canvas (off-ride): `#161616`
- Card surface: `#1F1F1F`
- Surface 2 / 3: `#2A2A2A` / `#353535`
- Divider: `#303030`
- Text primary: `#FFFFFF`
- Text secondary: `#A8AAAD`
- Success / achievement: `#57C84D`

### Example Component Prompts
- "Build the Zwift rider HUD in SwiftUI: a horizontal row of 3 glass metric tiles over a full-bleed 3D scene. Each tile: `.ultraThinMaterial` tinted `rgba(12,12,12,0.60)`, 12pt radius, ~10pt padding, 1pt `rgba(255,255,255,0.10)` stroke. Value in Barlow Semi Condensed 34pt weight 800, `.monospacedDigit()`, in the metric's fixed color (Power `#FC6719`, Cadence `#E8C547`, HR `#F0413E`); unit label below in Barlow 11pt/700 uppercase `#A8AAAD`. Interpolate value changes over 200ms so numbers tick, never jump."

- "Create the Zwift route banner: a translucent top strip, `rgba(15,15,15,0.62)` + blur 8, 12pt radius, 14pt inset from screen edges, clearing the Dynamic Island. Leading 30pt orange route badge. Title 'Watopia · Volcano Flat' in Barlow Semi Condensed 16pt/700 white; sub-line '12.4 / 19.3 km · Lap 2 of 3' in Inter 11pt `#A8AAAD`. Below: a 4pt progress bar, track `rgba(255,255,255,0.18)`, fill `#FC6719`, advancing linearly with distance."

- "Build the Zwift Power-Up tile: a 56pt rounded square (10pt radius). Empty state: glass (`rgba(255,255,255,0.10)` + blur) with a faint glyph. Armed state: solid `#FC6719` with a white 24pt power-up glyph (aero helmet / feather / truck). On collect, scale-pop 0.8→1.0 over 250ms + an orange glow + a medium haptic, then a subtle armed pulse. Tap to deploy: empty with a 200ms sweep."

- "Render the Zwift in-ride action bar: heavy glass `rgba(18,18,18,0.92)` + blur 20, 0.5pt `#303030` top stroke, ~78pt tall incl. home indicator. 5 controls: Action, Power-Up, Workout, Riders, Menu — 22pt icons, default `#A8AAAD`, labels Inter 10pt/600. The Power-Up control tints `#FC6719` with a soft fill when a power-up is armed. This is a game-control surface, not page navigation."

- "Create the Zwift primary CTA: a `#FC6719` button, 10pt radius, 14×30pt padding, label in Barlow Semi Condensed 16pt weight 700 UPPERCASE with +0.3pt tracking, white text. Pressed: `#D9530E` + scale 0.98. Use for 'START RIDE' / 'JOIN EVENT'."

- "Render the Zwift off-ride route card: `#1F1F1F`, 12pt radius, a 16:9 world thumbnail, an orange elevation sparkline, title in Barlow 18pt/700, a stats row (distance / elevation / lead-in) in Inter 13pt `#A8AAAD`, trailing chevron. App-shell canvas behind is solid `#161616` (no glass off-ride)."

### Iteration Guide
1. In-ride, the 3D world is full-bleed — UI floats on top as glass, never as opaque cards
2. All in-ride chrome is glassmorphic: `rgba(12,12,12,0.6)` + blur, 1pt white stroke — no drop shadows
3. Metric colors are FIXED and learned by muscle memory — Power orange, Cadence yellow, HR red, W/kg cyan
4. HUD numbers are tall condensed Barlow at 28–34pt+, tabular — readable from a trainer 2m away
5. Zwift Orange `#FC6719` is brand action (Start Ride, jersey, route progress, armed power-up)
6. The Power-Up is a real game affordance — glass when empty, solid orange when armed
7. Interpolate metric changes (~200ms) so the HUD ticks smoothly and never jumps
8. Uppercase Barlow for titles/buttons (game energy); Inter sentence-case for body/lists
9. Off-ride menus/route picker/profile fall back to a conventional solid `#161616` dark shell
10. Achievements are green `#57C84D` + an orange confetti burst + haptic; in-ride is landscape-preferred
