# Design System Inspiration of KAYAK (iOS)

## 1. Visual Theme & Atmosphere

KAYAK's iOS app is a **metasearch instrument** — a tool, not a store. Where Expedia sells you a stay, KAYAK *compares the entire market* and tells you whether to buy now or wait. The light canvas is a clean white (`#FFFFFF` / `#F4F5F7`), the chrome is restrained and data-dense, and the emotional payoff is *intelligence*: a price-forecast banner ("Prices are likely to rise — book soon, 87% confidence"), a horizontally scrolling **price calendar** (each day shows its cheapest fare, color-coded green→neutral→red), a **fare-compare matrix** (airlines × stops grid), and the signature **Hacker Fare** (two separate one-way tickets stitched into a cheaper round trip). The whole app radiates "we did the math for you."

The single brand expression is **KAYAK Orange** (`#FF690F`). It is the *only* accent color in the entire system and it is used with discipline: the selected date in the price calendar, the Hacker Fare tag, the primary "View Deal" CTA, the active bottom tab, the forecast banner's trend glyph. Everything else is a near-neutral greyscale (canvas, surfaces, dividers, text) plus a small set of *functional* semantic colors that are not brand colors: a price-signal ramp (green `#1E9E5A` low / neutral mid / red `#E5484D` high) and a forecast-advice triad (buy = green, wait = amber `#E8A317`, rise = red). KAYAK deliberately has no secondary brand hue — orange does all the brand work, and color elsewhere always *means something* (this fare is cheap; prices are rising).

The structural anchor is the **flight results screen with the price calendar**. Open a search and you land on: a compact route header (`SFO → JFK · Oct 12 · 1 adult · Economy · Round-trip`), the horizontal price-calendar strip, the forecast banner, then a vertical list of **fare cards**. Each fare card is the workhorse: airline row (logo + name, optional orange Hacker Fare tag), a depart-time → duration-arc-with-stops → arrive-time leg, and a bottom row with the booking-provider count and a large bold price with "round-trip" beneath. This card pattern repeats for hotels and cars with the same DNA (compare many, surface the cheapest, show the provider).

Typography is a clean grotesque/humanist sans (KAYAK's product face; **Inter** / SF Pro is the faithful substitute on iOS). The hierarchy is tool-UI rhythm: display → route title → section → price (loud, 20pt 800, tabular) → body → time/label → meta → tag. Prices and times are the loudest non-display elements and always use tabular figures so calendar columns and matrix cells align perfectly. Weights cluster at 400 (body), 600 (times, labels), 700 (sections, tags), 800 (display, prices, route).

**Key Characteristics:**
- Clean white canvas (`#FFFFFF` / `#F4F5F7`) light / near-neutral charcoal (`#101214`) dark — tool, not store
- KAYAK Orange (`#FF690F`) is the *single* brand accent — selected dates, Hacker Fare tag, primary CTA, active tab; nothing else is brand-colored
- Price-calendar strip — horizontal scroll, each day shows its cheapest fare, color-coded green/neutral/red
- Price-forecast banner — "likely to rise / drop" + confidence %, the metasearch intelligence signal
- Fare-compare matrix — airlines × stops grid, cheapest cell highlighted green
- Hacker Fare — two one-way tickets combined into a cheaper round trip, flagged in orange
- Fare card — airline row, depart→duration-arc-with-stops→arrive leg, provider count + bold tabular price
- Functional semantic ramp — price low/mid/high (green/neutral/red) + forecast buy/wait/rise (green/amber/red), distinct from brand orange
- Provider transparency — "2 booking sites", "JetBlue.com" — KAYAK sends you onward, it doesn't sell
- Tabular figures everywhere prices/times appear so calendar + matrix columns align
- Bottom tab bar: Search / Trends (Explore) / Trips / Price Alerts / Profile

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **KAYAK Orange** (`#FF690F`): The single brand accent — selected price-calendar date, Hacker Fare tag, primary "View Deal" CTA, active bottom tab, forecast trend glyph, links-as-actions.
- **Orange Pressed** (`#E0560A`): Pressed state for the orange CTA and tappable orange elements.
- **Orange Soft** (`#FF8A42`): Subtle orange for hover/secondary emphasis and 12%-tint banner borders.
- **Link Blue** (`#2E7CF6`): Plain hyperlinks and inline text links (terms, provider names) — NOT a brand color, used only where orange would over-signal.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, card backgrounds.
- **Surface Gray** (`#F4F5F7`): App background behind cards, grouped sections.
- **Surface Pressed** (`#E9EBEE`): Pressed rows, selected list items.
- **Divider** (`#E2E5E9`): Hairline dividers between rows, card borders, leg-line.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#101214`): Primary dark canvas — near-neutral, NO brand tint.
- **Dark Surface 1** (`#181B1E`): Card backgrounds, elevated surfaces.
- **Dark Surface 2** (`#21262B`): Chips, matrix header, airline-logo tiles.
- **Dark Divider** (`#2C3338`): Hairline dividers, card borders, leg-line.

### Text
- **Text Primary Light** (`#16191C`): Prices, route, titles.
- **Text Secondary Light** (`#5C656E`): Times' airport codes, meta, labels.
- **Text Tertiary Light** (`#8B949C`): Disabled, stop dots, faint meta.
- **Text Primary Dark** (`#E9ECEF`): Prices, route, titles on dark.
- **Text Secondary Dark** (`#9BA3AB`): Meta on dark.
- **Text Tertiary Dark** (`#6B747C`): Disabled, faint meta on dark.

### Functional Semantic — Price Signal (NOT brand colors)
Used in the price-calendar strip and fare-compare matrix to encode *cheapness*.

| Band | Color | Use |
|------|-------|-----|
| Low (cheapest ~third) | `#1E9E5A` | Calendar day price, "best" matrix cell |
| Mid | `#16191C` (primary text) / `#E9ECEF` dark | Calendar day price, ordinary matrix cell |
| High (priciest ~third) | `#E5484D` | Calendar day price |

### Functional Semantic — Forecast Advice (NOT brand colors)
Used in the forecast banner and advice chips.

| Advice | Color | Tint Bg |
|--------|-------|---------|
| Buy now (good price) | `#1E9E5A` | `#1E9E5A29` (16%) |
| Wait (may drop) | `#E8A317` | `#E8A31729` (16%) |
| Book soon (rising) | `#E5484D` | `#E5484D29` (16%) |

### Semantic (general)
- **Success** (`#1E9E5A`): Confirmed booking, price drop captured.
- **Warning** (`#E8A317`): "Prices volatile", caution states.
- **Error** (`#E5484D`): Form errors, "Fare no longer available".
- **Hacker Fare Tag** (`#FF690F`): The Hacker Fare flag uses brand orange (it's a KAYAK-branded product feature, not a generic semantic).

## 3. Typography Rules

### Font Family
- **Primary**: KAYAK's product sans — a clean grotesque/humanist face used across the rebranded app for dense comparison UI: tall x-height, unambiguous figures, confident bold weights.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Helvetica Neue', Arial, sans-serif`
- **Closest free substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — matched proportions and superb tabular numerals for prices and the calendar.
- **Numerals**: Tabular/lining figures mandatory for all prices, times, durations, confidence %, and matrix cells.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | KAYAK Sans | 32pt | 800 | 1.15 | -0.6pt | Home ("Compare flights") |
| Route Title | KAYAK Sans | 26pt | 800 | 1.2 | -0.4pt | "SFO → JFK" results header |
| Section | KAYAK Sans | 22pt | 700 | 1.25 | -0.2pt | "Best price by date" |
| Price (Card) | KAYAK Sans | 20pt | 800 | 1.2 | -0.4pt | Fare price, tabular |
| Leg Time | KAYAK Sans | 18pt | 800 | 1.2 | -0.3pt | "7:05" depart/arrive, tabular |
| Body | KAYAK Sans | 16pt | 400 | 1.5 | 0pt | Explanatory copy |
| Time / Label | KAYAK Sans | 14pt | 600 | 1.35 | 0pt | "Nonstop", airport code label |
| Meta | KAYAK Sans | 13pt | 400 | 1.4 | 0pt | "2 booking sites · 1 stop SEA" |
| Calendar Price | KAYAK Sans | 13pt | 800 | 1.0 | 0pt | Per-day cheapest fare, tabular |
| Tag / Badge | KAYAK Sans | 11pt | 700 | 1.0 | 0.3pt | "HACKER FARE" |
| Calendar Day | KAYAK Sans | 11pt | 600 | 1.0 | 0pt | "Sat 11" |
| Button | KAYAK Sans | 16pt | 700 | 1.0 | 0pt | Primary/secondary CTAs |
| Tab Label | KAYAK Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Price and time are the loudest content elements**: fare price 20pt 800 tabular, leg time 18pt 800 tabular — the eye lands on the number and when it leaves.
- **Tabular figures are mandatory**: the price calendar and fare-compare matrix only work if every digit aligns; never use proportional figures for prices/times.
- **Weight concentrated at 400 / 600 / 700 / 800**: body regular, times/labels semibold, sections/tags bold, display/prices extra-bold. No thin, no black.
- **Orange text is rare**: orange is for the Hacker Fare tag, the active tab label, and the forecast trend glyph — body text and links are never orange (links are blue `#2E7CF6`).
- **Dynamic Type first-class**: display, route, section, body, prices, times scale; calendar day/price, tags, tab labels stay fixed (the strip and matrix are layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (View Deal / Search)**
- Shape: Rounded rectangle, 10pt corner radius
- Background: `#FF690F` (KAYAK Orange)
- Text: `#FFFFFF`, KAYAK Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Full-width on detail/booking screens (16pt insets)
- Pressed: background `#E0560A` + scale 0.98
- Disabled: `#FF690F` at 38% opacity

**Secondary / Outline Button (Filters / Sort)**
- Background: transparent
- Text: `#16191C` / `#E9ECEF` dark
- Border: 1pt `#E2E5E9` / `#2C3338`
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 10pt
- Pressed: background `#F4F5F7` / `#21262B`

**Ghost / Chip Button (Track price)**
- Background: `#F4F5F7` / `#21262B`
- Text: `#16191C` / `#E9ECEF`
- Border: 0.5pt `#E2E5E9` / `#2C3338`
- Padding: 11pt vertical, 18pt horizontal
- Corner radius: 10pt

**Text Button (See all 142 results)**
- Font: KAYAK Sans 14pt weight 700
- Color: `#FF690F`
- No underline; chevron trailing optional
- Pressed: 60% opacity

### Core Atoms

**Fare Card (the workhorse)**
- Container: `#FFFFFF` / `#181B1E`, 14pt corner radius, 0.5pt `#E2E5E9` / `#2C3338` border, shadow `0 2px 8px rgba(0,0,0,0.06)` light / `0 4px 12px rgba(0,0,0,0.4)` dark
- Padding: 14pt all
- **Airline row** (top): logo tile (26pt, 7pt radius, `#F4F5F7`/`#21262B`) + airline name(s) KAYAK Sans 12pt w600 secondary; trailing optional **Hacker Fare tag** (`#FF690F` bg, white 11pt w700, 5pt radius, 3/7pt padding) or amber "Self-transfer" chip
- **Leg row** (12pt below): depart time (18pt w800 tabular) + airport code (11pt secondary) | duration arc center: duration 11pt secondary, 1pt `#E2E5E9` leg-line with a 5pt end-dot, stops text 11pt w700 orange ("1 stop · SEA") or secondary ("Nonstop") | arrive time (mirror)
- **Bottom row** (14pt below, 12pt padding-top, 0.5pt top divider): provider info left ("2 booking sites" / "JetBlue.com") 11pt secondary; price right (20pt w800 tabular) with "round-trip" 11pt secondary beneath
- Tap → fare detail (full leg breakdown, fare rules, provider list)

**Property / Car Card** (same DNA): photo or icon header, name, comparison meta, provider count, bold tabular price + period.

**Price-Calendar Day Cell**
- Size: 50pt wide, ~58pt tall
- Container: `#FFFFFF`/`#181B1E`, 10pt radius, 0.5pt `#E2E5E9`/`#2C3338` border
- Top: day label "Sat 11" KAYAK Sans 11pt w600 secondary
- Bottom: cheapest price KAYAK Sans 13pt w800 tabular, color = price-signal band (green `#1E9E5A` low / primary mid / red `#E5484D` high)
- Selected: `#FF690F` fill, white day + price, no border
- Horizontal scroll strip; selecting a day re-queries fares for that date

**Forecast Banner**
- Full-width inline, 10pt radius, background `#FF690F1F` (12% orange) with 0.5pt `#FF690F66` border (rising) — or green/amber tint per advice
- Leading trend glyph 18pt orange (chart-uptrend) — or buy/wait glyph
- Title: "Prices are likely to rise" KAYAK Sans 12pt w700
- Subtitle: "We recommend booking soon · 87% confidence" 11pt secondary

**Fare-Compare Matrix**
- Container: 10pt radius, 0.5pt `#E2E5E9`/`#2C3338` border, rows divided 0.5pt
- Header row: `#F4F5F7`/`#21262B` bg, column labels (Nonstop / 1 stop / 2+ stops) 12pt secondary, leading "Airline" label cell 1.4× width
- Body row: airline name (left, 12pt w600 primary) + price cells (12pt w800 primary, tabular); the cheapest cell in a row/column rendered green `#1E9E5A` w800; empty = "—" tertiary
- Tap a cell → filter results to that airline/stops combination

**Hacker Fare Tag**
- Pill/rounded-rect, 5pt radius
- Background: `#FF690F` (brand orange — it's a KAYAK product feature)
- Text: white, KAYAK Sans 11pt w700, letter-spacing 0.3pt, "HACKER FARE"

**Advice Chip**
- Pill, 999pt radius
- Background: advice tint (`#1E9E5A29` / `#E8A31729` / `#E5484D29`)
- Leading 6pt dot in advice color
- Text: advice color, KAYAK Sans 13pt w700 ("Buy now — good price")

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF`/`#101214` with 0.5pt top divider `#E2E5E9`/`#2C3338`
- Tabs (5): Search, Trends (Explore), Trips, Price Alerts, Profile
- Icon size: 22pt
- Active: `#FF690F` (orange), filled glyph
- Inactive: `#8B949C`/`#6B747C`, outline glyph
- Labels: KAYAK Sans 10pt w600, always shown
- No pill indicator — color + fill change only

**Top App Bar (Results / Detail)**
- Height: 44pt + safe area
- Background: `#FFFFFF`/`#101214`, 0.5pt bottom divider
- Leading: back chevron; Center: compact route ("SFO → JFK"); Trailing: edit-search + share
- Below it: the route-meta line and sort/filter chip row

### Input Fields

**Search Form (entry)**
- Trip-type segmented control (Round-trip / One-way / Multi-city): orange underline on active segment
- Origin / Destination fields: 52pt tall, `#F4F5F7`/`#21262B` bg, 10pt radius, leading airplane glyph, swap button between them
- Date field: opens 2-month price-calendar sheet (each day shows cheapest fare, color-coded)
- Travelers / cabin stepper: bottom sheet
- Focus: 1pt `#FF690F` border on active field

**Search Field (autocomplete)**
- Height: 48pt
- Background: `#F4F5F7`/`#21262B`, 10pt radius
- Leading `magnifyingglass` 17pt secondary
- Placeholder: "City or airport" 14pt secondary
- Results: airport rows with code badge + city + country

### Distinctive Components

**Price Calendar (full sheet)**
- 2-month vertical scroll; every day cell shows its cheapest fare (tabular) color-coded green/neutral/red
- Legend at top: "Lowest" green dot · "Typical" · "Highest" red dot
- Selecting a date (or range for round-trip) re-prices and dismisses

**Price-Forecast Module**
- Headline advice ("Prices are likely to rise") + confidence % + a small trend sparkline
- "Track this price" → creates a Price Alert (orange CTA)

**Fare-Compare Matrix** (see Core Atoms) — KAYAK's signature "see the whole market at once"

**Price Alerts List**
- Each alert row: route + dates + current price + delta arrow (green down / red up) + sparkline
- Toggle to pause; tap → results

**Provider Hand-off Sheet**
- When booking, KAYAK shows the list of booking sites with their prices ("JetBlue.com $284", "Expedia $291") — transparency is a feature; KAYAK does not sell, it routes

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 14, 16, 20, 24, 32, 40
- Fare-card vertical gap: 12pt
- Fare-card internal padding: 14pt
- Screen horizontal inset: 16pt
- Calendar day gap: 6pt

### Grid & Container
- iPhone: single-column fare list, 16pt insets, horizontal calendar strip above
- iPad: 2-column fare grid (max content 980pt centered, 24pt insets); detail in split view
- Matrix: horizontally scrollable if airline list is long

### Whitespace Philosophy
- **Dense but legible**: KAYAK shows a lot (calendar + forecast + matrix + many fares) so each unit is tightly padded (14pt) but separated by a clear 12pt gap — it's an instrument panel, not an editorial page
- **Numbers get the spotlight**: prices and times are the largest non-display type; never crowd a price with secondary copy
- **Orange is scarce on purpose**: at most one or two orange elements per viewport (selected date, primary CTA, or a Hacker Fare tag) — scarcity keeps it meaningful

### Border Radius Scale
- Square (0pt): leg-line, dividers
- Subtle (4pt): inline price chips
- Tag (5–6pt): Hacker Fare tag, logo tiles
- Standard (10pt): buttons, cards-day-cells, fields, matrix, banner
- Card (14pt): fare cards, modules
- Pill (500pt / 999pt): advice chips, segmented control thumb
- Circle (50%): stop dots, avatars, swap button

## 6. Depth & Elevation

KAYAK uses **minimal, functional elevation** — it's an instrument, so cards lift only enough to read as discrete, tappable comparison units.

| Level | Treatment (Light) | Treatment (Dark) | Use |
|-------|-------------------|------------------|-----|
| Flat (0) | none | none | Page background, banner, matrix, tags |
| Card (1) | `0 2px 8px rgba(0,0,0,0.06)` | `0 4px 12px rgba(0,0,0,0.4)` | Fare cards, calendar day cells |
| Floating (2) | `0 8px 24px rgba(0,0,0,0.1)` | `0 8px 24px rgba(0,0,0,0.55)` | Sticky sort/filter bar, sticky CTA |
| Sheet (3) | `0 -8px 28px rgba(0,0,0,0.12)` | `0 -8px 28px rgba(0,0,0,0.6)` | Price-calendar sheet, filters, provider hand-off |
| Modal Scrim | `rgba(0,0,0,0.4)` | `rgba(0,0,0,0.55)` | Dim behind sheets/modals |

**Shadow Philosophy**: shadow only says "this is a tappable card." The forecast banner, matrix, advice chips, and Hacker Fare tag are deliberately flat — color and tint carry them. On dark, shadows deepen to 0.4–0.6 opacity over `#101214`.

### Motion
- **Calendar day select**: tapped cell fills orange over 160ms ease-out; fare list cross-dissolves to the new date's results in 220ms; brief skeleton shimmer while re-pricing
- **Forecast reveal**: banner slides down + fades in 240ms ease-out on results load
- **Fare card → detail**: push transition 300ms ease-out; leg row is the shared anchor
- **Matrix cell tap**: cell flashes orange tint 140ms; results filter with a 200ms list reorder
- **Sort/filter sheet**: bottom sheet slides up 300ms ease-out; result count animates as filters toggle
- **Price-drop in Price Alerts**: delta arrow pulses green once (180ms) when a tracked price falls
- **Pull-to-refresh**: orange spinner; re-runs the metasearch
- **Haptics**: soft impact on calendar day select, segment switch, filter toggle; success haptic on a captured price drop

## 7. Do's and Don'ts

### Do
- Use KAYAK Orange (`#FF690F`) as the *single* brand accent — selected date, Hacker Fare tag, primary CTA, active tab; keep it scarce (≤ 1–2 per viewport)
- Encode price cheapness with the functional ramp (green `#1E9E5A` low / neutral mid / red `#E5484D` high) in the calendar and matrix
- Use the forecast-advice triad (buy = green, wait = amber `#E8A317`, rise = red) — distinct from brand orange
- Use tabular figures for every price, time, duration, confidence %, and matrix cell so columns align
- Lead the results screen with the price-calendar strip + forecast banner, then the fare list
- Show provider transparency ("2 booking sites", "JetBlue.com") — KAYAK routes, it doesn't sell
- Flag Hacker Fares with the orange tag (it's a KAYAK product feature)
- Make plain links blue `#2E7CF6`, not orange
- Use a near-neutral dark canvas (`#101214`) with NO brand tint
- Keep cards tightly padded (14pt) but clearly gapped (12pt) — it's an instrument

### Don't
- Don't introduce a second brand color — orange does all the brand work; everything else is neutral or functional-semantic
- Don't make body text or plain links orange — that dilutes the accent's meaning
- Don't use proportional figures for prices/times — the calendar and matrix break without tabular digits
- Don't reuse the price-signal green/red as brand colors — they *mean* cheap/expensive, nothing else
- Don't hide the forecast or provider list — the "we did the math / we route you onward" transparency is the product
- Don't tint the dark canvas — `#101214` is near-neutral so the lone orange accent stays loud
- Don't over-shadow flat elements (banner, matrix, tags, advice chips stay flat)
- Don't crowd a price with secondary copy — the number is the headline
- Don't animate aggressively — motion is functional 140–300ms ease-out
- Don't replace the price calendar with a plain date picker — the color-coded cheapest-fare strip is the signature

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; calendar shows ~5.5 day cells; matrix horizontally scrolls |
| iPhone 13/14/15 | 390pt | Standard layout; ~6 calendar cells visible |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | ~7 calendar cells; more room for provider text |
| iPad (portrait) | 768pt | 2-column fare grid; calendar full-width; matrix fully visible |
| iPad (landscape) | 1024pt+ | Fare list + persistent forecast/matrix pane side-by-side |

### Dynamic Type
- Display, route, section, body, prices, leg times: full scale
- Calendar day/price, Hacker Fare tag, tab labels, matrix cells: FIXED (strip + grid are layout-sensitive)
- Prices/times keep tabular figures at every scale

### Orientation
- iPhone: portrait-primary; landscape supported on fare detail
- iPad: full rotation; landscape unlocks side-by-side list + forecast/matrix

### Touch Targets
- Calendar day cell: full 50×58pt tappable
- Matrix cell: full cell tappable (≥ 44pt row height)
- Tab bar icons: 22pt glyph, 44pt hit
- Primary CTA: ≥ 48pt tall
- Swap (origin/destination) button: 40pt circle

### Safe Area Handling
- Top app bar respects safe area + Dynamic Island
- Sticky sort/filter bar and bottom tab respect home indicator
- Price-calendar sheet and filter sheet respect bottom safe area
- Keyboard: airport autocomplete list scrolls above keyboard

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF` / surface `#F4F5F7`
- Canvas (dark): `#101214` (near-neutral, NO brand tint)
- Surface (dark card): `#181B1E`
- Divider: `#E2E5E9` light / `#2C3338` dark
- Text primary: `#16191C` light / `#E9ECEF` dark
- Text secondary: `#5C656E` light / `#9BA3AB` dark
- KAYAK Orange (single accent): `#FF690F` (pressed `#E0560A`)
- Link blue (plain links): `#2E7CF6`
- Price low / "best": `#1E9E5A`  ·  Price high: `#E5484D`
- Forecast: buy `#1E9E5A` · wait `#E8A317` · rise `#E5484D`

### Example Component Prompts
- "Build the KAYAK fare card in SwiftUI: `#FFFFFF` card (`#181B1E` dark), 14pt corner radius, 0.5pt `#E2E5E9` border, shadow `0 2px 8px rgba(0,0,0,0.06)`, 14pt padding. Top row: 26pt logo tile (7pt radius, `#F4F5F7`) + 'Alaska · Delta' KAYAK Sans 12pt w600 `#5C656E`, trailing `#FF690F` 'HACKER FARE' tag (white 11pt w700, 5pt radius). Leg row 12pt below: '7:05' 18pt w800 tabular + 'SFO' 11pt secondary | center duration '5h 28m' 11pt secondary over a 1pt `#E2E5E9` line with end-dot + '1 stop · SEA' 11pt w700 `#FF690F` | mirror arrive. Bottom row 14pt below with 0.5pt top divider: '2 booking sites' 11pt secondary left; '$247' 20pt w800 tabular + 'round-trip' 11pt secondary right."

- "Create the KAYAK price-calendar strip: horizontal scroll of 50pt cells, each 10pt radius, 0.5pt `#E2E5E9` border. Top: 'Sat 11' 11pt w600 `#5C656E`. Bottom: cheapest price 13pt w800 tabular, color = `#1E9E5A` if low / `#16191C` mid / `#E5484D` high. Selected cell: `#FF690F` fill, white text, no border. Selecting re-prices the fare list."

- "Build the KAYAK price-forecast banner: full-width, 10pt radius, `#FF690F1F` background, 0.5pt `#FF690F66` border. Leading 18pt orange uptrend glyph. Title 'Prices are likely to rise' KAYAK Sans 12pt w700 `#16191C`; subtitle 'We recommend booking soon · 87% confidence' 11pt `#5C656E`. Swap to green/amber tint for buy/wait advice."

- "Render the KAYAK fare-compare matrix: 10pt-radius container, 0.5pt `#E2E5E9` border, rows divided 0.5pt. Header `#F4F5F7` bg with 'Airline' (1.4× width) / 'Nonstop' / '1 stop' / '2+ stops' 12pt `#5C656E`. Body rows: airline 12pt w600 primary + price cells 12pt w800 tabular; cheapest cell in the row rendered `#1E9E5A` w800; empty '—' tertiary."

- "Build the KAYAK primary CTA: `#FF690F` fill, white KAYAK Sans 16pt w700, 10pt radius, 14/28pt padding, pressed `#E0560A` + scale 0.98. Use it for 'View Deal' and 'Search'."

- "Create the KAYAK forecast advice chip: pill 999pt radius, background advice-tint (`#1E9E5A29` buy / `#E8A31729` wait / `#E5484D29` rise), leading 6pt dot in advice color, text in advice color KAYAK Sans 13pt w700 ('Buy now — good price')."

### Iteration Guide
1. Canvas is white (`#FFFFFF`/`#F4F5F7`) light, near-neutral charcoal `#101214` dark — NO brand tint on dark
2. KAYAK Orange `#FF690F` is the ONLY brand color — selected date, Hacker Fare tag, primary CTA, active tab; keep it scarce
3. The price calendar (color-coded cheapest fare per day) is the signature — green low / neutral mid / red high
4. The forecast banner ("likely to rise/drop" + confidence %) is the metasearch intelligence payoff
5. Use tabular figures for every price/time/duration — the calendar and matrix break otherwise
6. The fare card (airline row → leg with duration-arc-stops → provider + bold price) is the workhorse
7. Functional semantics (price low/mid/high, forecast buy/wait/rise) are NOT brand colors — they always mean something
8. Plain links are blue `#2E7CF6`; orange is never body text
9. Show provider transparency — KAYAK routes you onward, it doesn't sell
10. Motion is functional 140–300ms ease-out; the calendar cross-dissolves the fare list on date change
