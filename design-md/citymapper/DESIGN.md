# Design System Inspiration of Citymapper (iOS)

## 1. Visual Theme & Atmosphere

Citymapper's iOS app is a **transit nerd's instrument panel disguised as a friendly app**. Its whole reason to exist is to answer "how do I get from here to there, right now, the smartest way?" — and the visual language is built around a single insight: **every transport mode gets its own loud, consistent color, and a route is read as a colored strip**. Open the app, type a destination, and you get a stack of route cards; each card shows a **leg strip** — a horizontal row of color-coded mode chips (a green walk pill → a blue tube badge → a red bus badge → a green walk pill) with little arrows between them. You don't read the words; you read the *colors and the ETA*. This mode-color system (walk green, bus red, tube/metro blue, rail purple, bike cyan, cab amber) is the brand. It is consistent across every city Citymapper supports, and it never changes between light and dark mode — a bus is always red.

The canvas is clean and near-neutral so the mode colors and the live map carry all the energy: white (`#FFFFFF`) in light, a deep blue-black (`#0C0E14`) in dark. Citymapper's own brand accent is an **electric blue (`#2B5BFF`)** used for the destination pin, primary links, the metro mode, and selected states. But the single most iconic control in the entire product is the **GO button**: a fat, friendly, rounded **green (`#00C281`)** pill with a play triangle and the word "GO", which launches **GO trip mode** — a live, step-by-step navigation experience that counts down to your next action ("Get off in 2 stops", "Walk 4 min to the platform", "Your bus is 1 min away") with big type, a progress line, and disruption alerts. The GO pill is shape- and color-locked: it looks the same on every screen, in every city, in every theme.

The structure is: an **origin→destination card** at the top (two dots — a gray "from" dot and a blue "to" dot — connected by a short line, exactly like a transit map), a list of **route options** ranked by Citymapper's blend of speed/cost/effort (tagged "Fastest", "Cheapest", "Easiest", "Rain-safe"), and the **GO** button. Tapping a route opens **step-by-step**, and starting it enters **GO trip mode**. Other signature surfaces: the **departures board** (a live list of next departures at a stop, each row a colored line badge + destination + minutes, with imminent departures in amber), the **Nearby** screen (stops around you with live arrivals), and **disruption banners** (orange) that surface line closures inline in route results.

Typography is a friendly geometric grotesque — Citymapper uses a custom face, well-approximated by **Inter** with heavy weights. The personality is *confident and chunky*: ETAs and the GO label are extra-bold (800–900), place names are semibold, supporting text is regular gray. It's playful but never cute at the expense of legibility — you have to read this while half-running for a train.

**Key Characteristics:**
- Mode-color system — walk `#00B894`, bus `#E8453C`, tube/metro `#2B5BFF`, rail `#8E44D8`, bike `#00A8C5`, cab `#FFB400` — theme-invariant, the brand's soul
- **Leg strip** — a route shown as a horizontal row of color-coded mode chips with arrows between
- **GO button** — fat green (`#00C281`) rounded pill with a play triangle; shape/color-locked everywhere
- **GO trip mode** — live step-by-step navigation with big countdown type and a progress line
- Origin→destination card — gray "from" dot + blue "to" dot joined by a transit-map line
- Route options ranked & tagged — Fastest / Cheapest / Easiest / Rain-safe
- **Departures board** — live next-departure list; colored line badge + destination + minutes (amber when imminent)
- Disruption banners — orange inline alerts for closures and delays
- Clean near-neutral canvas (white / deep blue-black `#0C0E14`) so mode colors + map pop
- Electric blue (`#2B5BFF`) is Citymapper's own accent; green is reserved for GO
- Chunky confident type (Inter / custom grotesque), extra-bold ETAs
- Bottom tabs: Get me / Nearby / Saved / You

## 2. Color Palette & Roles

### Primary (Interactive)
- **Citymapper Blue** (`#2B5BFF`): The brand accent — destination pin, primary links, selected states, the "metro/tube" mode color, secondary CTAs.
- **Citymapper Blue Bright** (`#4D7BFF`): Dark-mode link & active-tab variant for contrast on the dark canvas.
- **Citymapper Blue Pressed** (`#1E45CC`): Pressed state of blue CTAs.
- **GO Green** (`#00C281`): Reserved exclusively for the GO button and GO trip mode — never used for generic UI.

### Transit Mode Colors (Theme-Invariant — the brand)
These define a route's readability and **do not change between light and dark**.

| Mode | Color | Tint (bg use) |
|------|-------|---------------|
| Walk | `#00B894` | `rgba(0,184,148,0.18)` |
| Bus | `#E8453C` | `rgba(232,69,60,0.18)` |
| Tube / Metro / Subway | `#2B5BFF` | `rgba(43,91,255,0.18)` |
| Rail / Train / Overground | `#8E44D8` | `rgba(142,68,216,0.18)` |
| Bike / Scooter | `#00A8C5` | `rgba(0,168,197,0.18)` |
| Cab / Rideshare | `#FFB400` | `rgba(255,180,0,0.18)` |
| Ferry | `#0094C6` | `rgba(0,148,198,0.18)` |

> Real transit lines (e.g., London Victoria line navy, NYC A/C/E blue) override these defaults with the operator's official line color when known. The mode colors are the *fallback and category* color.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F5F8`): Route cards, grouped sections.
- **Surface Pressed** (`#E8EAF0`): Pressed rows.
- **Divider** (`#E3E5EC`): Hairlines between route legs and departure rows.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0C0E14`): Primary dark canvas — a deep blue-black, NOT pure black.
- **Dark Surface 1** (`#15171F`): Route cards, elevated surfaces.
- **Dark Surface 2** (`#1E212B`): Departures board, hovered/selected cards.
- **Dark Divider** (`#282C38`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#10131A`): ETAs, place names, titles — near-black.
- **Text Secondary Light** (`#5A6473`): Metadata, "min" suffix, captions.
- **Text Tertiary Light** (`#8A93A3`): Disabled, placeholder, leg arrows.
- **Text Primary Dark** (`#ECEEF3`): Primary text on dark.
- **Text Secondary Dark** (`#98A0AE`): Secondary on dark.
- **Text Tertiary Dark** (`#646C7A`): Tertiary on dark.

### Surface / Tint Map

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0C0E14` |
| Surface 1 | `#F4F5F8` | `#15171F` |
| Surface 2 | `#E8EAF0` | `#1E212B` |
| Divider | `#E3E5EC` | `#282C38` |
| Blue tint | `#2B5BFF14` | `#2B5BFF29` |
| GO Green tint | `#00C2811F` | `#00C2812E` |

### Semantic
- **Disruption Orange** (`#FF8A00`): Line closures, severe delays — inline banners and the "minutes soon" departure state.
- **Live Green** (`#00C281`): "Live" indicator dot, on-time confirmation (shares the GO green).
- **Delay Red** (`#E8453C`): Cancelled/severely-delayed services (shares the bus red intentionally — "red = stop/problem").
- **Info Blue** (`#2B5BFF`): Informational tips, "we updated this route".

## 3. Typography Rules

### Font Family
- **Primary**: Citymapper's custom geometric grotesque (a chunky, friendly sans tuned for glanceable transit data).
- **iOS Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Helvetica Neue', sans-serif`
- **Web/Catalog Substitute**: `Inter` (weights 400–900) — closest free face matching the chunky grotesque feel.
- **Numerics**: Tabular figures for ETAs, minutes, and the departures board so countdowns don't jitter.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Grotesque | 32pt | 900 | 1.1 | -0.5pt | "Get me somewhere" / GO-mode primary instruction |
| ETA Hero | Grotesque | 26pt | 800 | 1.15 | -0.3pt | Route card "24 min", GO-mode countdown |
| Section | Grotesque | 22pt | 800 | 1.2 | -0.2pt | "Best routes", "Departures" |
| Row Title | Grotesque | 18pt | 700 | 1.3 | -0.1pt | Line name, step title |
| Body | Grotesque | 16pt | 400 | 1.45 | 0pt | Step descriptions, route notes |
| Place | Grotesque | 15pt | 600 | 1.3 | 0pt | Origin/destination, stop names |
| Meta | Grotesque | 14pt | 400 | 1.4 | 0pt | "Updated 12s ago · live" |
| Mode Badge | Grotesque | 12pt | 800 | 1.0 | 0.3pt | Line/route number on a colored chip — uppercase |
| ETA Suffix | Grotesque | 13pt | 600 | 1.0 | 0pt | The "min" after a number |
| GO Label | Grotesque | 18pt | 900 | 1.0 | 0.4pt | The word "GO" in the button — uppercase |
| Button | Grotesque | 15pt | 800 | 1.0 | 0pt | Secondary buttons |
| Tag | Grotesque | 10pt | 800 | 1.0 | 0.4pt | "Fastest"/"Cheapest" pills — uppercase |
| Departure Min | Grotesque | 16pt | 800 | 1.0 | 0pt | Minutes on the departures board, tabular |
| Tab Label | Grotesque | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **ETAs and GO are the loudest type** (800–900): the number you act on dominates every screen.
- **Tabular figures for anything counting down**: route ETAs, departure minutes, GO-mode countdowns.
- **Mode badges are uppercase, chunky, tracked**: line numbers/names sit in colored chips at 12pt 800.
- **Confident, not cute**: heavy weights, tight tracking on big type — readable while moving.
- **Dynamic Type first-class**: display, ETA, body scale; mode badges, tab labels, the GO label stay fixed (chips are layout-sensitive).

## 4. Component Stylings

### Buttons

**GO Button (the icon of the app)**
- Shape: Full rounded pill, 28pt corner radius (≈ height/2), height 54pt
- Background: `#00C281` (GO Green) — never any other color
- Content: a play triangle (filled) + the word "GO" in 18pt weight 900, color `#003322` (dark green text for warmth) or white per contrast
- Shadow: `0 10px 22px -10px rgba(0,194,129,0.7)`
- Pressed: scale 0.98 + brightness 0.95
- **Locked**: identical shape, color, and label on every screen and in every theme — it is the brand control

**Primary Button (Get directions / secondary CTA)**
- Background: `#2B5BFF` (Citymapper Blue)
- Text: white, 15pt weight 800
- Corner radius: 12pt; padding 13pt/24pt
- Pressed: `#1E45CC`

**Outline Button (Save place)**
- Background: transparent
- Text & border: `#2B5BFF` light / `#4D7BFF` dark, 1.5pt border
- Corner radius: 12pt; padding 11pt/18pt

**Text Button (More routes)**
- Font: 14pt weight 700
- Color: `#2B5BFF` / `#4D7BFF` dark
- Pressed: 60% opacity

### Cards & Containers

**Origin→Destination Card**
- Background: `#F4F5F8` / `#15171F`, 1pt divider border, 14pt corner radius, 12pt/14pt padding
- Two rows: a 10pt gray dot ("from") and a 10pt blue dot ("to"), joined by a 2pt vertical divider segment between them — reads like a transit-map stop pair
- Place text: 14–15pt weight 600

**Route Option Card**
- Background: `#F4F5F8` / `#15171F`, 1pt divider border, 16pt corner radius, 14pt/16pt padding, 12pt vertical gap between cards
- "Best" route: border switches to GO Green `#00C281`
- Head: ETA "24" 22pt 800 + "min" 13pt 600 secondary, with a right-aligned tag pill ("Fastest"/"Cheapest")
- **Leg strip**: horizontal flex row of mode chips: each chip is a colored rounded-8pt pill with a 13pt white mode glyph + a short label/number (e.g., "4" for walk minutes, "Victoria" for a line); chips separated by a tertiary "›" arrow

**Departures Board**
- Container: `#1E212B` / surface, 14pt radius, `overflow: hidden`
- Row: 13pt/16pt padding, 1pt bottom divider (none last)
- Leading: a 34×24 line badge (mode-colored, 12pt 900 white text — e.g., "VIC", "63", "NR")
- Middle: destination 14pt weight 600
- Trailing: minutes 16pt weight 800 — GO Green normally, **Disruption Orange when ≤ 3 min** ("leave now")

**Mode Chip / Badge**
- Rounded 8–10pt pill, mode color background, white text 12pt 800 uppercase, optional 13pt glyph
- The atomic unit of every route view

### Navigation

**Top (Origin→Destination)**
- The O→D card *is* the top region on the route screen — no separate nav bar; back chevron floats top-left when drilled in

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `#FFFFFF` / `rgba(12,14,20,0.94)` with `backdrop-filter: blur(20px)`, 0.5pt top divider
- Tabs (4): Get me / Nearby / Saved / You
- Icon: 22pt; active `#2B5BFF` light / `#4D7BFF` dark (filled), inactive tertiary gray
- Label: 10pt weight 600, always shown
- No pill indicator — color + icon-fill change only

**GO Trip Mode (full-screen takeover)**
- Replaces normal chrome: a big current-instruction header (ETA Hero / Display weight), a vertical progress line with mode-colored segments and stop dots, a "next action" card, and a persistent disruption strip if relevant
- A floating "End" / minimize control; the GO green theme dominates

### Input Fields

**Destination Search**
- Height: 48pt
- Background: `#F4F5F8` / `#1E212B`
- Corner radius: 12pt
- Leading 18pt search glyph (tertiary)
- Placeholder: "Where to?" 15pt 400 tertiary
- Focus: 2pt `#2B5BFF` border

**Origin Field (inline in O→D card)**
- Borderless row in the O→D card; tap to edit, opens the same search sheet

### Distinctive Components

**Leg Strip**
- The signature route visualization — a horizontal sequence of mode chips + arrows
- Reads in <1s: color = mode, number = line/duration
- Never render a route as plain text — always the colored strip

**GO Trip Mode Countdown**
- Big "Get off in 2 stops" / "Bus in 1 min" instruction (ETA Hero/Display weight)
- A live vertical progress line; the current leg's segment animates/pulses in its mode color
- Haptic + voice cues at each step transition

**Departures Board**
- Live, auto-refreshing; minutes tick down; imminent (≤3 min) turn orange
- "Live" green dot + "updated Ns ago" caption

**Route Tag Pill**
- Tiny uppercase pill on a route card: "Fastest" (GO-green tint), "Cheapest" (blue tint), "Easiest", "Rain-safe" (a cloud glyph), "Step-free"
- Communicates *why* you'd pick this route

**Disruption Banner**
- Inline orange (`#FF8A00`) strip in route results: "⚠ Victoria line: minor delays"
- Tap to expand details; severe closures use Delay Red `#E8453C`

**Nearby Stops List**
- Each entry: stop name + a row of mode badges with the soonest arrival per line
- The "I just want to see what's around me" surface

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 12, 16, 18, 24, 32, 40, 54
- Page horizontal inset: 16pt
- Card internal padding: 14–16pt
- Route-card vertical gap: 12pt
- Leg-strip chip gap: 6pt

### Grid & Container
- iPhone: single-column route list at 16pt insets; map fills above when in map mode
- iPad: two-pane — route list (max 420pt) on the left, live map on the right; GO mode is full-screen on both
- Departures board: single column, fixed row height
- Leg strip: horizontal wrap, never truncates the mode color (label may abbreviate)

### Whitespace Philosophy
- **Color does the work, not whitespace**: the canvas is calm so mode colors and the map carry energy
- **The GO button always has clear space** below the route list — it must never be crowded
- **Glance-optimized density**: route cards are compact enough that 3–4 fit without scrolling
- **One green action**: GO is the only green; nothing else competes for that meaning

### Border Radius Scale
- Square (0pt): map, full-bleed banners
- Subtle (4pt): tiny tags
- Standard (8pt): mode chips, route tag pills
- Comfortable (10–12pt): badges, inputs, primary button
- Card (14–16pt): route cards, O→D card, departures board
- GO Pill (28pt ≈ height/2): the GO button
- Pill (500pt): filter chips
- Circle (50%): O→D dots, avatar, live dot

## 6. Depth & Elevation

Citymapper is mostly flat; depth highlights the live map layer and the one action that matters.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; 1pt border/divider | Route cards, departures rows, sections |
| Map Sheet (Level 1) | `0 -8px 24px rgba(0,0,0,0.16)` | The route list / bottom sheet sitting over the map |
| GO Action (Level 2) | `0 10px 22px -10px rgba(0,194,129,0.7)` (green-tinted) | The GO button only |
| Floating (Level 2) | `0 6px 20px rgba(16,19,26,0.14)` | Disruption detail popover, mode-filter sheet |
| Modal Scrim | `rgba(8,10,16,0.5)` | Behind sheets/modals |

**Shadow Philosophy**: The bottom-sheet-over-map is the core spatial metaphor — the route list floats above a live map and you can drag it up/down. The only persistently shadowed control is the GO button, with a green-tinted glow that makes it the obvious thing to press. Everything else is flat with borders. On dark mode the GO glow stays (green reads on the deep blue-black); card separation uses the `#15171F` → `#1E212B` surface ramp.

### Motion
- **Route list ⇄ map drag**: the sheet tracks the finger 1:1; snap points at peek / half / full with `spring(response: 0.35, damping: 0.85)`
- **GO start**: GO pill scales 0.98 then the screen cross-dissolves into GO trip mode (350ms); the progress line draws in from the top over 500ms
- **GO-mode step transition**: the completed leg's line segment fills its mode color (250ms ease-out); the instruction text swaps with a 200ms vertical slide+fade; medium haptic + optional voice cue
- **Departure tick**: minutes decrement with a 150ms number cross-fade; crossing the ≤3-min threshold animates the color to orange (200ms)
- **Leg strip reveal**: on a fresh search, route cards stagger-in (each `opacity 0→1` + 8pt rise, 60ms apart); leg chips within a card pop in left-to-right (40ms apart, slight scale 0.9→1)
- **Disruption banner**: slides down from the top of the result list in 250ms ease-out; pulses its orange border once to draw the eye
- **Tab switch**: instant content swap; active icon does a subtle 0.9→1 scale (120ms)
- **Pull to refresh departures**: custom transit-y loader; on release, minutes restream with the cross-fade
- **Haptic feedback**: light tick on route select & tab change; medium impact on GO start and each GO-mode step; warning haptic when a disruption affects the active route

## 7. Do's and Don'ts

### Do
- Give every transport mode its loud, consistent color (walk green / bus red / tube blue / rail purple / bike cyan / cab amber) and keep it identical across themes
- Render routes as a **leg strip** of colored mode chips with arrows — color first, words second
- Keep the **GO button** a fat green (`#00C281`) pill, shape- and color-locked on every screen and theme
- Use Citymapper Blue (`#2B5BFF`) for the brand accent / destination / metro; reserve green for GO
- Show the O→D as a gray "from" dot + blue "to" dot joined by a transit-map line
- Tag routes with *why* ("Fastest", "Cheapest", "Easiest", "Rain-safe")
- Make the departures board live, with imminent (≤3 min) departures in orange
- Use tabular figures for every ETA, minute, and countdown
- Surface disruptions inline as orange banners in route results
- Keep the canvas calm (white / deep blue-black `#0C0E14`) so mode colors and the map pop
- Use chunky extra-bold type for ETAs and GO — readable while moving

### Don't
- Don't recolor modes per theme — a bus is always red, light or dark
- Don't render a route as a plain text list — it must be the colored leg strip
- Don't use green for anything except GO / live / on-time — green means "go"
- Don't change the GO button's shape or color contextually — it is the brand control
- Don't use pure black (`#000000`) for dark canvas — it's deep blue-black `#0C0E14`
- Don't bury the GO button or crowd it — it always has clear space
- Don't let real line colors get overridden by category color when the official line color is known (use the line's true color, fall back to mode color)
- Don't animate countdowns abruptly — cross-fade the minute number (150ms)
- Don't hide disruptions in a sub-screen — they belong inline, in orange, in the results
- Don't thin out ETA type — it's the loudest thing on screen (800–900)
- Don't add a second "primary" green action competing with GO

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Leg-strip labels abbreviate sooner; 3 route cards visible |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the map / O→D card |
| iPhone 15/16 Pro Max | 430pt | Larger map peek; leg strips show fuller line names |
| iPad (portrait) | 768pt | Two-pane: route list (≤420pt) + live map |
| iPad (landscape) | 1024pt+ | Same two-pane wider; GO trip mode full-screen with side step list |

### Dynamic Type
- Display, ETA hero, body, place: full scale
- Mode badges, route tag pills, the GO label, departure-min, tab labels: FIXED (chips are layout-sensitive)
- GO-mode instruction scales up generously (you read it from a pocket-glance distance)

### Orientation
- All screens rotate
- iPad landscape locks the two-pane split; GO mode rotates with a side-by-side step list + map
- The leg strip wraps rather than truncating mode color

### Touch Targets
- GO button: 54pt tall pill, full width of its inset
- Route card: full-card tap, ≥ 72pt tall
- Mode chip (when tappable): ≥ 44pt hit even if visually ~28pt
- Departures row: full-row tap, ≥ 50pt
- Tab bar icons: 22pt glyph, 44pt hit

### Safe Area Handling
- Top: O→D card / map respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; the route sheet's peek height sits above the home indicator; the GO button floats above safe area
- Keyboard: destination search sheet rises above the keyboard
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#0C0E14`
- Surface 1: `#F4F5F8` light / `#15171F` dark
- Surface 2: `#E8EAF0` light / `#1E212B` dark
- Divider: `#E3E5EC` light / `#282C38` dark
- Text primary: `#10131A` light / `#ECEEF3` dark
- Text secondary: `#5A6473` light / `#98A0AE` dark
- **Citymapper Blue (accent)**: `#2B5BFF` (bright `#4D7BFF`, pressed `#1E45CC`)
- **GO Green (GO button only)**: `#00C281`
- Mode — Walk: `#00B894`
- Mode — Bus: `#E8453C`
- Mode — Tube/Metro: `#2B5BFF`
- Mode — Rail: `#8E44D8`
- Mode — Bike: `#00A8C5`
- Mode — Cab: `#FFB400`
- Disruption Orange: `#FF8A00`

### Example Component Prompts
- "Build a Citymapper route option card in SwiftUI: a `#F4F5F8` (dark `#15171F`) container, 1pt divider border, 16pt corner radius, 14pt/16pt padding. Header row: a left ETA group — `24` in 22pt weight 800 `#10131A` next to `min` in 13pt weight 600 `#5A6473` — and a right-aligned tag pill `Fastest` (10pt 800 uppercase, GO-green tint `rgba(0,194,129,0.18)`, `#00C281` text, 999pt radius). Below: a horizontal leg strip — mode chips, each an 8pt-radius pill in its mode color with a 13pt white glyph and a label (walk `#00B894` '4', tube `#2B5BFF` 'Victoria', walk `#00B894` '6'), separated by a `›` arrow in `#8A93A3`. If this is the best route, switch the card border to `#00C281`."

- "Create the Citymapper GO button: a full-pill rounded rectangle 28pt corner radius, 54pt tall, full-width minus 16pt insets, background `#00C281`, containing a filled play triangle and the word 'GO' in 18pt weight 900 letter-spacing 0.4pt, with a green-tinted shadow `0 10px 22px -10px rgba(0,194,129,0.7)`. Pressed: scale 0.98 + brightness 0.95 + medium haptic. This control's shape and color are LOCKED — identical on every screen and in both light and dark themes."

- "Render the Citymapper origin→destination card: a `#F4F5F8` (dark `#15171F`) container, 14pt radius, 1pt divider border, 12pt/14pt padding. Two 34pt rows: row 1 has a 10pt `#5A6473` gray 'from' dot then place text in 15pt weight 600; row 2 has a 10pt `#2B5BFF` blue 'to' dot then place text. Between the dots, a 2pt-wide `#E3E5EC` vertical line segment connecting them — reads like a transit-map stop pair."

- "Build the Citymapper departures board: a `#1E212B` container, 14pt radius, `overflow: hidden`. Each row 13pt/16pt padding, 1pt `#282C38` bottom divider (none on last): leading a 34×24 line badge (mode-colored, e.g. tube `#2B5BFF`) with 12pt weight 900 white text ('VIC'), a flexible destination in 14pt weight 600 `#ECEEF3`, and trailing minutes in 16pt weight 800 — `#00C281` normally, but `#FF8A00` when ≤ 3 minutes. Tabular figures; minutes cross-fade as they decrement."

- "Create the Citymapper GO trip-mode header: a near-full-bleed panel with a big current instruction in 26–32pt weight 800–900 `#10131A` ('Get off in 2 stops'), a sub-line in 16pt 400 `#5A6473` ('Tate Modern · 8 min'), and a vertical progress line on the left with stop dots and mode-colored segments — the current leg's segment in its mode color, pulsing. A floating 'End' control top-right. The GO-green theme dominates."

- "Render a Citymapper disruption banner: a full-width inline strip in the route results, `#FF8A00` background at 18% (`rgba(255,138,0,0.18)`) with a 1pt `#FF8A00` border, 10pt radius, a ⚠ glyph in `#FF8A00`, and text 'Victoria line: minor delays' in 14pt weight 700. On appear, slide down 250ms ease-out and pulse the border once. Severe closures use Delay Red `#E8453C` instead of orange."

### Iteration Guide
1. Canvas is calm — white light / deep blue-black `#0C0E14` dark (NOT true black) — so mode colors and the map carry the energy
2. Every transport mode has a loud, consistent color (walk `#00B894` / bus `#E8453C` / tube `#2B5BFF` / rail `#8E44D8` / bike `#00A8C5` / cab `#FFB400`) — theme-invariant, the brand's soul
3. Routes are ALWAYS a leg strip of colored mode chips + `›` arrows — color first, words second
4. The GO button is a fat green `#00C281` pill, shape/color-locked on every screen and in both themes — it is THE brand control
5. Green means GO / live / on-time only — Citymapper Blue `#2B5BFF` is the brand accent (also the metro mode)
6. O→D = gray "from" dot + blue "to" dot joined by a transit-map line segment
7. Tag routes with *why* (Fastest / Cheapest / Easiest / Rain-safe)
8. The departures board is live; minutes use tabular figures and turn orange ≤ 3 min
9. Disruptions are inline orange (`#FF8A00`) banners in results — never hidden in a sub-screen
10. ETAs and GO are the loudest type (800–900); real line colors override category mode colors when known
