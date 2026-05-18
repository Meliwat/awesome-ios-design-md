# Design System Inspiration of Skyscanner (iOS)

## 1. Visual Theme & Atmosphere

Skyscanner's iOS app is a **calm, white, decision-making instrument**. It is not a lifestyle travel app full of wanderlust photography — it is a price-comparison engine, and the entire visual language is engineered to make a confident "is this a good deal?" judgment in under three seconds. The canvas is pure white (`#FFFFFF`) with a single, unmistakable brand color — **Skyscanner Sky Blue (`#0770E3`)** — carrying every primary action: the Search button, the active trip-type segment, selected calendar dates, and the focused input ring. There is no secondary accent competing for attention. The blue *is* the call to action, everywhere, every time.

The signature moment is the **price-as-color system**. Skyscanner's core insight is that a number alone ("£148") doesn't tell you if it's good — so the app paints prices with a **traffic-light scale**: green (`#00A698`) for cheap, amber (`#FFB81C`) for average, red (`#E5392E`) for expensive. This shows up in the **month price grid** (a calendar where every day is tinted by how its fare compares), the **price-trend bar chart**, the **"Cheapest"/"Best"/"Fastest"** result tabs, and the **Everywhere** map (where destinations are labeled with color-coded fares). Once you've seen the green-amber-red language, you read Skyscanner fares the way you read a thermometer.

The other signature is **"Everywhere"** as a destination. Most flight apps require you to know where you're going. Skyscanner's most-loved feature lets you set the destination to a literal place called **"Everywhere"** and the app returns a ranked list of the cheapest countries and cities to fly to. This reframes the whole product from "book the flight I want" to "find the trip I can afford" — and the UI treats Everywhere as a first-class search target with its own globe iconography and exploratory result layout.

The structure is a **stacked search card** (From / To / Dates, with a circular swap button straddling the From–To divider), a prominent blue **Search flights** button, and below it a **results list of flight rows** — each row a horizontal timeline: departure time + airport on the left, a thin line with a dot and a "Direct" / "1 stop" label in the middle, arrival time + airport, and a bold fare on the right. Carrier logos are small and monochrome-friendly; the app never lets airline branding overwhelm its own neutral chrome.

Typography is **Skyscanner Relative** (the brand's custom humanist sans by Dalton Maag), falling back to SF Pro / Inter on iOS where the custom face isn't bundled. It is friendly but information-dense: fares and times are heavy (700–800), supporting metadata is light (400) and gray. The type system never gets decorative — this is a tool, and the type works like instrument labeling.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / cool navy-black (`#0B0F14`) dark — neutral, tool-like, never photo-driven
- Single brand accent: **Sky Blue `#0770E3`** carries 100% of primary actions
- **Traffic-light price system** — green `#00A698` cheap / amber `#FFB81C` average / red `#E5392E` expensive — the core mental model
- **Month price grid** — a calendar where every day is tinted by fare vs. typical price
- **"Everywhere"** as a first-class destination — find the cheapest place to fly, not just a known route
- **Stacked search card** — From / To / Dates with a circular swap button on the From–To divider
- **Best / Cheapest / Fastest** result tabs — Skyscanner's signature sort triad
- **Flight result row** — horizontal timeline (time → line+dot+stops → time → bold fare)
- **Price alerts** — track a route and get pinged when the fare drops
- Skyscanner Relative typeface — friendly humanist sans, instrument-grade hierarchy
- Bottom tab bar: Explore / Trips / Saved / Profile

## 2. Color Palette & Roles

### Primary (Interactive)
- **Sky Blue** (`#0770E3`): The brand color and the single primary-action color — Search button, active segment, selected dates, focused input, links, primary CTAs.
- **Sky Blue Pressed** (`#0A5BBA`): Pressed/active state of the primary button.
- **Sky Bright** (`#05A8FA`): Dark-mode link & "Everywhere" accent — a lighter blue that holds contrast on the dark canvas.
- **Deep Navy** (`#05203C`): Skyscanner's deep brand navy — used for high-contrast headers, splash, and selected-state backgrounds in some surfaces.

### Price Traffic-Light System (Theme-Invariant)
These three colors are the core of Skyscanner's identity and **do not shift hue between light and dark**.

| Role | Color | Meaning | Tint (bg use) |
|------|-------|---------|---------------|
| Price Low | `#00A698` | Cheap / good deal | `rgba(0,166,152,0.16)` |
| Price Average | `#FFB81C` | Typical fare | `rgba(255,184,28,0.16)` |
| Price High | `#E5392E` | Expensive / avoid | `rgba(229,57,46,0.16)` |

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F5F7FA`): Search card fill, grouped sections, inactive segments.
- **Surface Pressed** (`#E9EDF2`): Pressed rows, selected list items.
- **Divider** (`#E2E7EC`): Hairline dividers inside the search card and between result rows.
- **Focus Ring** (`#0770E333`): 20% Sky Blue ring on focused inputs.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0B0F14`): Primary dark canvas — a cool navy-black, NOT pure black.
- **Dark Surface 1** (`#141A22`): Search card, elevated surfaces.
- **Dark Surface 2** (`#1C242E`): Result rows, hovered/selected cards.
- **Dark Divider** (`#25303C`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#0E1B2C`): Primary text — fares, times, titles — a near-navy black.
- **Text Secondary Light** (`#5C6B7A`): Metadata, airport codes, sort captions.
- **Text Tertiary Light** (`#8B97A3`): Disabled, placeholder, very low emphasis.
- **Text Primary Dark** (`#E8EDF2`): Primary text on dark.
- **Text Secondary Dark** (`#9AA7B4`): Secondary on dark.
- **Text Tertiary Dark** (`#66727E`): Tertiary on dark.

### Page / Surface Tints

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#0B0F14` |
| Surface 1 | `#F5F7FA` | `#141A22` |
| Surface 2 | `#E9EDF2` | `#1C242E` |
| Divider | `#E2E7EC` | `#25303C` |
| Sky Blue tint | `#0770E314` | `#0770E329` |
| Low (green) tint | `#00A6981F` | `#00A6982E` |
| Average (amber) tint | `#FFB81C1F` | `#FFB81C2E` |
| High (red) tint | `#E5392E1F` | `#E5392E2E` |

### Semantic
- **Error Red** (`#E5392E`): Form errors, no-results state — shares the "expensive" red intentionally.
- **Success Green** (`#00A698`): Price-drop confirmation, saved-alert toast — shares the "cheap" green.
- **Warning Amber** (`#FFB81C`): Fare-change warnings, "price went up since you last looked".
- **Info Blue** (`#0770E3`): Informational banners, "prices update every few minutes".

## 3. Typography Rules

### Font Family
- **Primary**: `Skyscanner Relative` (by Dalton Maag for Skyscanner) — a friendly, slightly humanist grotesque designed for dense travel data and global script coverage.
- **iOS Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Helvetica Neue', sans-serif`
- **Web/Catalog Substitute**: `Inter` — closest free face matching Relative's x-height and humanist proportions.
- **Numerics**: Tabular figures enabled for all fares, times, and the price calendar so columns align.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Relative | 32pt | 800 | 1.15 | -0.5pt | "Explore everywhere" hero |
| Screen Title | Relative | 26pt | 700 | 1.2 | -0.3pt | "London → Barcelona" results header |
| Section | Relative | 22pt | 700 | 1.25 | -0.2pt | "Cheapest month", "Price trend" |
| Card Title | Relative | 18pt | 700 | 1.3 | -0.1pt | Flight duration "Direct · 2h 15m" |
| Fare (Large) | Relative | 19pt | 800 | 1.2 | -0.2pt | Result-row price, tabular |
| Body | Relative | 16pt | 400 | 1.45 | 0pt | Descriptions, fare-rule text |
| Price (Inline) | Relative | 15pt | 600 | 1.3 | 0pt | "from £42 return", tabular |
| Time (Result) | Relative | 17pt | 700 | 1.2 | 0pt | Departure/arrival times, tabular |
| Meta | Relative | 14pt | 400 | 1.4 | 0pt | "3 results · sorted by Best" |
| Airport Code | Relative | 12pt | 600 | 1.3 | 0.4pt | "LHR", "BCN" — uppercase |
| Field Label | Relative | 12pt | 600 | 1.3 | 0.2pt | "DEPART · RETURN" — uppercase, tertiary |
| Button | Relative | 16pt | 700 | 1.0 | 0pt | Primary/secondary buttons |
| Chip | Relative | 13pt | 700 | 1.0 | 0pt | Price hint chips, filter chips |
| Tab Label | Relative | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Calendar Day | Relative | 11pt | 700 | 1.0 | 0pt | Day number in price grid, tabular |
| Calendar Price | Relative | 8pt | 600 | 1.0 | 0pt | Tiny fare under day number |

### Principles
- **Numbers are the hero**: fares, times, and dates are the heaviest type on screen (700–800); everything supporting is 400 gray.
- **Tabular figures everywhere money or time appears**: the price calendar, result list, and trend chart all need vertical digit alignment.
- **Uppercase only for codes & labels**: airport codes and field labels are uppercase with positive tracking; nothing else is.
- **Instrument-grade restraint**: no italics, no decorative weights — type is labeling for a tool.
- **Dynamic Type first-class**: titles, body, fares scale; tab labels, airport codes, and calendar micro-prices stay fixed (layout-sensitive grid).

## 4. Component Stylings

### Buttons

**Primary Button (Search flights / Continue)**
- Shape: Rounded rectangle, 12pt corner radius
- Background: `#0770E3` (Sky Blue)
- Text: `#FFFFFF`, Relative 16pt weight 700
- Height: 50pt; full-width minus 18pt insets
- Padding: 14pt vertical, 28pt horizontal
- Shadow: `0 8px 18px -8px rgba(7,112,227,0.7)` light / none dark
- Pressed: background `#0A5BBA` + scale 0.98

**Secondary / Outline Button (Set price alert)**
- Background: transparent
- Text & border: `#0770E3`, 1.5pt border
- Corner radius: 12pt
- Padding: 12pt vertical, 20pt horizontal
- Font: Relative 14pt weight 700
- Pressed: background `#0770E314`

**Pill Button (Filters / Sort)**
- Background: `#F5F7FA` light / `#1C242E` dark
- Border: 1pt `#E2E7EC` / `#25303C`
- Text: `#0E1B2C` / `#E8EDF2`, Relative 13pt weight 600
- Corner radius: 500pt (full pill)
- Padding: 9pt vertical, 16pt horizontal

**Text Button (View 64 deals)**
- Font: Relative 14pt weight 600
- Color: `#0770E3` light / `#05A8FA` dark
- No underline
- Pressed: 60% opacity

### Cards & Containers

**Search Card (the stacked From/To/Dates input)**
- Background: `#FFFFFF` with 1pt `#E2E7EC` border light / `#141A22` with 1pt `#25303C` border dark
- Corner radius: 16pt, `overflow: hidden`
- Internal field: 14pt vertical / 16pt horizontal padding, 1pt bottom divider between fields (none on last)
- Leading icon: 18pt Sky Blue stroke icon
- Field label: 12pt 600 uppercase tertiary; value: 15pt 600 primary
- **Swap button**: 30pt circle, 1pt divider border, straddling the From–To divider line, vertically centered on it

**Flight Result Row**
- Background: `#F5F7FA` light / `#1C242E` dark
- Border: 1pt divider
- Corner radius: 14pt
- Padding: 16pt
- Layout (horizontal): [time 17pt700 + airport 12pt600] → [duration 11pt tertiary, 1pt line with trailing dot, "Direct"/"1 stop" 10pt green/amber] → [time + airport] → [fare 19pt800 + "return" 11pt]
- Carrier logo: 20pt, rendered grayscale-tolerant

**Price Calendar Day Cell**
- Square aspect, 7pt corner radius
- Background: `#F5F7FA` light / `#141A22` dark (1pt divider border)
- Content: day number 11pt 700 primary, micro-fare 8pt 600 colored by traffic-light
- Selected: background `#0770E3`, all text white
- Out-of-month: 35% opacity
- Cheapest day in view: green fare + subtle green ring

**Section Container**
- Background: `#FFFFFF` / `#0B0F14` (flush, no card) OR grouped `#F5F7FA` / `#141A22`
- 16pt horizontal page inset
- Section header: 22pt 700, 16pt top margin

### Navigation

**Top App Bar**
- Height: 44pt + safe area
- Leading: Skyscanner wordmark ("sky" primary + "scanner" Sky Blue) on home; back chevron on detail
- Trailing: circular avatar (gradient `#0770E3`→`#05A8FA`) or filter icon
- Background: canvas, no border on scroll-top; 0.5pt divider once scrolled

**Trip-Type Segmented Control**
- Three segments: Return / One way / Multi-city
- Inactive: `#F5F7FA` fill, `#5C6B7A` text, 1pt divider border, 8pt radius
- Active: `#0770E3` fill, white text
- 12pt font weight 600, 8pt vertical padding

**Bottom Tab Bar**
- Height: 64pt + safe area
- Background: `#FFFFFF` / `rgba(11,15,20,0.94)` with `backdrop-filter: blur(20px)` and 0.5pt top divider
- Tabs (4): Explore / Trips / Saved / Profile
- Icon: 22pt; active `#0770E3` (filled), inactive `#8B97A3` / `#66727E`
- Label: Relative 10pt weight 600, always shown
- No pill indicator — active is color + icon-fill change only

### Input Fields

**Search Field (inside search card)**
- See Search Card above — borderless rows separated by dividers, leading Sky Blue icon

**Standalone Text Input**
- Height: 50pt
- Background: `#F5F7FA` / `#1C242E`
- Border: 1pt `#E2E7EC` / `#25303C`
- Corner radius: 12pt
- Focus: 2pt `#0770E3` border + `#0770E333` glow ring
- Placeholder: 15pt 400 tertiary

**Everywhere Affordance**
- Inline pill/link below the To field: globe icon 14pt `#05A8FA` + "Explore Everywhere — find the cheapest destination" in 12pt 600 Sky Bright
- Tapping sets destination to the literal "Everywhere" target

### Distinctive Components

**Month Price Grid**
- A 7-column calendar; each day cell tinted by its fare vs. typical: green/amber/red micro-price
- Header: "Cheapest month — October"; sub: "Green is a good deal · prices per person"
- Selected date: full Sky Blue fill
- The single most recognizable Skyscanner surface — never render dates without price color

**Best / Cheapest / Fastest Tabs**
- A 3-segment toggle above the result list
- "Best" = Skyscanner's blended score (default), "Cheapest" = lowest fare, "Fastest" = shortest duration
- Active tab: 2pt `#0770E3` underline + `#0E1B2C` text; inactive: `#5C6B7A`
- Each tab can show its winning fare inline (e.g., "Cheapest £42")

**Everywhere Result List**
- When destination = Everywhere: a ranked list of countries/cities
- Row: destination name + small map thumbnail + "from £29" in green
- Tapping a country drills into its cheapest cities, then routes

**Price Alert Toggle**
- A bell + "Track prices" pill on a route
- On: Sky Blue fill, bell filled; sends push when fare drops
- Off: outline, gray bell

**Price Hint Chip**
- Small rounded chip showing a fare with traffic-light tint background
- `£38 · Cheap` (green tint), `£81 · Average` (amber tint), `£152 · Expensive` (red tint)
- 13pt 700, 8pt radius, 7pt/14pt padding

**Fare-Trend Bar Chart**
- Horizontal bars per day/week, each colored green/amber/red
- A dashed "typical price" line lets users see how today compares
- Tap a bar → that date's results

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48, 64
- Page horizontal inset: 18pt (slightly wider than 16 — Skyscanner gives the search card room)
- Card internal padding: 16pt
- Field row padding: 14pt vertical / 16pt horizontal
- Result-row vertical gap: 12pt

### Grid & Container
- iPhone: content at 18pt horizontal insets; search card full-width minus insets
- iPad: search card max-width 560pt centered; results list 2-column on landscape
- Price calendar: fixed 7-column grid, 5pt gap, square cells
- Result list: single column, 12pt gap

### Whitespace Philosophy
- **Tool clarity over generosity**: enough air to scan fast, not so much it scrolls forever — dense by intent
- **The search card is the anchor**: it gets the most padding and a clear border so it reads as "the thing you act on"
- **Color does the grouping**: traffic-light price color replaces heavy borders/dividers as the grouping signal in calendars and lists
- **One blue action per screen**: never two competing primary buttons

### Border Radius Scale
- Square (0pt): full-bleed banners, status separators
- Subtle (4pt): micro-tags
- Day cell (7pt): price-calendar cells
- Standard (8pt): segmented control, price chips, small surfaces
- Card (12pt): primary button, inputs, result-row outer
- Comfortable (16pt): search card, bottom sheets
- Pill (500pt): filter/sort pills, alert toggle
- Circle (50%): swap button, avatar, carrier logo container

## 6. Depth & Elevation

Skyscanner is mostly flat — depth is reserved for the one action that matters (Search) and for floating layers.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; 1pt divider/border for separation | Result rows, calendar cells, sections |
| Action (Level 1) | `0 8px 18px -8px rgba(7,112,227,0.7)` (blue-tinted) | Primary Search button only |
| Floating (Level 2) | `0 6px 20px rgba(14,27,44,0.12)` | Date picker sheet, filter sheet, autocomplete dropdown |
| Sheet (Level 3) | `0 -6px 28px rgba(0,0,0,0.18)` | Bottom sheets (sort, fare details, price-alert setup) |
| Modal Scrim | `rgba(8,12,18,0.45)` | Dim behind modals/sheets |

**Shadow Philosophy**: The only persistently-shadowed element is the Sky Blue Search button — its blue-tinted glow makes it feel pressable and pulls the eye to the single primary action. Everything else relies on borders and surface-tint steps. On dark mode, the button's glow is dropped (it would muddy the navy canvas); separation comes from the `#141A22` → `#1C242E` surface ramp instead.

### Motion
- **Search submit**: button scales to 0.98 (100ms), then a full-screen results push (300ms iOS native); a thin top progress bar fills while live fares stream in
- **Live price streaming**: result rows fade-in individually as carriers respond (each row `opacity 0→1` + 6pt rise, 200ms ease-out, staggered); fares "tick" with a 150ms color crossfade if they change
- **Calendar day select**: tapped cell fills Sky Blue with a 150ms ease-out background transition; previously-selected cell fades back
- **Best/Cheapest/Fastest switch**: underline slides between tabs in 200ms ease-out; list re-sorts with a 250ms cross-dissolve + position animation
- **Swap From/To**: swap button rotates 180° in 250ms; From and To values cross-fade and slide vertically past each other
- **Everywhere reveal**: tapping Everywhere expands the result list from the search card with a 300ms ease-out height/opacity
- **Price alert toggle**: bell does a small 1.0→1.15→1.0 bounce (200ms) and fills Sky Blue on enable; soft haptic
- **Pull to refresh fares**: custom plane/loader; on release, fares restream with the staggered fade
- **Haptic feedback**: light impact on calendar select & tab switch; success haptic on price-alert created & on a price-drop notification tap

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) light / cool navy-black (`#0B0F14`) dark — neutral, tool-like
- Make Sky Blue (`#0770E3`) the single primary-action color — one blue action per screen
- Always color prices with the traffic-light scale (green `#00A698` / amber `#FFB81C` / red `#E5392E`)
- Render the month price grid with per-day fare color — never a plain calendar
- Treat "Everywhere" as a real, selectable destination with globe iconography
- Use the stacked search card with the circular swap button on the From–To divider
- Offer Best / Cheapest / Fastest as the result sort triad (Best = default)
- Use tabular figures for every fare, time, and calendar price
- Keep the traffic-light hues identical between light and dark — price meaning is theme-invariant
- Give the Search button the only persistent (blue-tinted) shadow
- Surface "Set price alert" prominently — tracking is a signature Skyscanner behavior

### Don't
- Don't use travel/destination photography as primary chrome — Skyscanner is a tool, not a magazine
- Don't introduce a second accent color — the blue does all primary work
- Don't show fares as plain black text without traffic-light color in calendars/charts
- Don't render a normal date picker where a price calendar belongs
- Don't shift the green/amber/red hues for dark mode — they must mean the same thing
- Don't put two primary (filled-blue) buttons on the same screen
- Don't use pure black (`#000000`) for dark canvas — it's cool navy-black `#0B0F14`
- Don't let airline branding/logos overpower Skyscanner's neutral chrome — keep them small and monochrome-tolerant
- Don't animate fares jumping abruptly — color-crossfade price changes (150ms)
- Don't bury the swap button — it lives on the From–To divider as a circular control
- Don't hide "Everywhere" — it is the product's most-loved differentiator

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Calendar cells slightly tighter; search-card padding 14pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top app bar |
| iPhone 15/16 Pro Max | 430pt | Larger calendar cells; result rows show fuller route text |
| iPad (portrait) | 768pt | Search card centered max 560pt; results list single wide column |
| iPad (landscape) | 1024pt+ | Two-pane: search/filters left, results right; calendar shows 2 months |

### Dynamic Type
- Display, screen title, body, fares: full scale
- Tab labels, airport codes, field labels, calendar day numbers and micro-prices: FIXED (price grid is layout-sensitive)
- Result-row times scale up to XL but the row reflows to keep fare right-aligned

### Orientation
- All screens rotate
- iPad landscape adds the two-pane split (search ↔ results) and a dual-month calendar
- The price calendar reflows columns only on iPad; iPhone stays 7-wide

### Touch Targets
- Search button: 50pt tall
- Calendar day cell: ≥ 38pt hit (visually square, padded to target)
- Swap button: 30pt visual, 44pt hit
- Tab bar icons: 22pt glyph, 44pt hit
- Best/Cheapest/Fastest tab: full segment, ≥ 44pt tall

### Safe Area Handling
- Top: app bar respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; sheets inset above home indicator
- Keyboard: autocomplete dropdown positions above keyboard; search card scrolls clear
- Sides: 18pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#0B0F14`
- Surface 1: `#F5F7FA` light / `#141A22` dark
- Surface 2: `#E9EDF2` light / `#1C242E` dark
- Divider: `#E2E7EC` light / `#25303C` dark
- Text primary: `#0E1B2C` light / `#E8EDF2` dark
- Text secondary: `#5C6B7A` light / `#9AA7B4` dark
- **Sky Blue (primary action)**: `#0770E3`
- Sky Blue pressed: `#0A5BBA`
- Sky Bright (dark links / Everywhere): `#05A8FA`
- Deep Navy: `#05203C`
- **Price Low (cheap)**: `#00A698`
- **Price Average**: `#FFB81C`
- **Price High (expensive)**: `#E5392E`
- Error: `#E5392E` · Success: `#00A698` · Warning: `#FFB81C`

### Example Component Prompts
- "Build the Skyscanner search card in SwiftUI: a `#FFFFFF` rounded-16pt container with a 1pt `#E2E7EC` border, `overflow: hidden`. Three stacked rows (From / To / Dates) each with 14pt vertical / 16pt horizontal padding and a 1pt `#E2E7EC` bottom divider (no divider on the last). Each row: leading 18pt Sky Blue `#0770E3` location/calendar icon, a 12pt weight-600 uppercase tertiary `#8B97A3` label, and a 15pt weight-600 `#0E1B2C` value below it. Place a 30pt circular swap button (1pt `#E2E7EC` border, white fill, 14pt gray double-arrow icon) vertically centered on the From–To divider, offset to the trailing edge."

- "Create the Skyscanner month price grid: a 7-column grid with 5pt gaps and square cells (7pt corner radius). Each cell: `#F5F7FA` background with a 1pt `#E2E7EC` border, day number in Relative 11pt weight 700 `#0E1B2C` centered, and below it a micro-fare in 8pt weight 600 colored green `#00A698` if cheap, amber `#FFB81C` if average, red `#E5392E` if expensive. The selected date is a full `#0770E3` fill with white text. Out-of-month days are 35% opacity. Header above: 'Cheapest month — October' (22pt 700) and 'Green is a good deal · prices per person' (11pt `#5C6B7A`)."

- "Render a Skyscanner flight result row: a `#F5F7FA` (dark `#1C242E`) card, 1pt divider border, 14pt corner radius, 16pt padding. Horizontal layout: left = departure time `07:25` (Relative 17pt 700 tabular) over airport code `LHR` (12pt 600 `#5C6B7A`); center = duration `2h 15m` (11pt `#8B97A3`) over a 1pt `#E2E7EC` line with a 5pt Sky Blue dot at its end, with `Direct` below in 10pt 600 `#00A698`; then arrival time + code; right-aligned = fare `£42` (19pt 800 `#0E1B2C`, tabular) over `return` (11pt `#5C6B7A`)."

- "Build the Skyscanner Best/Cheapest/Fastest tab strip: three text tabs, the active one with `#0E1B2C` text and a 2pt `#0770E3` underline that slides 200ms ease-out between tabs; inactive tabs `#5C6B7A`. Each tab shows its winning fare inline below the label in 13pt 600 (e.g., 'Cheapest' / '£42'). Default selected = 'Best'. Switching tabs cross-dissolves and re-sorts the result list over 250ms."

- "Create the Skyscanner trip-type segmented control: three equal segments Return / One way / Multi-city, 8pt corner radius, 12pt weight-600 text, 8pt vertical padding. Inactive segments: `#F5F7FA` fill, `#5C6B7A` text, 1pt `#E2E7EC` border. Active segment: `#0770E3` fill, white text, no border."

- "Render the Skyscanner primary Search button: full-width minus 18pt insets, 50pt tall, 12pt corner radius, `#0770E3` background, white Relative 16pt weight-700 'Search flights' centered, with a blue-tinted shadow `0 8px 18px -8px rgba(7,112,227,0.7)` (drop the shadow in dark mode). Pressed: background `#0A5BBA` + scale 0.98 + light haptic."

### Iteration Guide
1. Canvas is pure white light / cool navy-black `#0B0F14` dark — neutral and tool-like, never photo-driven
2. Sky Blue `#0770E3` is the *only* primary-action color — one blue action per screen
3. Prices are ALWAYS colored by the traffic-light scale: green `#00A698` cheap, amber `#FFB81C` average, red `#E5392E` expensive — and these hues never change between themes
4. The month price grid (per-day fare color) is the signature surface — never a plain calendar
5. "Everywhere" is a real, selectable destination with globe iconography — surface it prominently
6. Use the stacked search card with the circular swap button straddling the From–To divider
7. Best / Cheapest / Fastest is the result sort triad; Best is the default blended score
8. Fares, times, and calendar prices use tabular figures so columns align
9. Only the Search button gets a persistent (blue-tinted) shadow; everything else uses borders + surface-tint steps
10. Keep airline logos small and monochrome-tolerant — Skyscanner's neutral chrome always wins
