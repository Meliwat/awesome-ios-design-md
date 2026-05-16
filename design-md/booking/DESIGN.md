# Design System Inspiration of Booking.com (iOS)

## 1. Visual Theme & Atmosphere

Booking.com's iOS app is dense, blue, and relentlessly conversion-focused — an information-rich travel marketplace where every screen is engineered to move a traveler from "browsing" to "booked". The canvas is clean white (`#FFFFFF`) with a light gray surface (`#F2F2F2`) chunking the feed into scannable blocks, so the real estate goes to what sells a stay: a big photo, a name, a review score, and a price. Where a lifestyle travel app breathes, Booking.com packs — property cards are tight, stacked with badges ("Genius", "Free cancellation", "Breakfast included"), and every element earns its pixels by nudging the decision forward.

The accent system is the brand's two blues. Booking Blue (`#003580`) — a deep navy — owns the brand surfaces: the top app bar, the Genius loyalty banner, headers, and the logotype. A brighter CTA Blue (`#0071C2`) owns action: the "Search" button, "Reserve", "See availability", links, and selected filters. A warm yellow (`#FEBB02`, lifted from the brand mark and used for star ratings) provides the only non-blue accent. The split is disciplined: navy = brand/structure, bright blue = act, yellow = rating. The most recognizable element is the **review-score badge** — a small solid-navy rounded rectangle showing a one-decimal score (e.g. `8.9`) next to a word label ("Fabulous"), a Booking.com signature you can spot from across a screen.

Typography is Inter (a BookingSans-equivalent grotesque) at weights 400 / 600 / 700. The hierarchy is compact and utilitarian because the cards are dense: screen titles at 22pt weight 700, property names at 17pt weight 700, the price at 17pt weight 700 right-aligned, the review-score number at 15pt weight 700 inside its badge, and a recurring 13pt secondary gray for location, distance, room type, and the "Includes taxes and fees" fine print. There are no decorative display moments — type is in service of density and trust.

**Key Characteristics:**
- Clean white canvas (`#FFFFFF`) with `#F2F2F2` surface blocks — dense, scannable, conversion-first
- Booking Blue (`#003580`) for brand/structure — app bar, Genius banner, headers
- CTA Blue (`#0071C2`) for action — Search, Reserve, links, selected filters
- Brand yellow (`#FEBB02`) only for star ratings — the lone non-blue accent
- Review-score badge — a solid-navy rounded chip with a one-decimal score + word ("8.9 Fabulous")
- Dense property card — photo, name, score badge, location, room info, price, badges
- Search form card — destination + dates + guests stacked in a single rounded card
- Genius loyalty banner — a navy blue strip promoting member discounts
- Map toggle — switch between the list feed and a price-pin map
- Filter chips — a horizontal row of selectable refinements
- Inter type, weights 400 / 600 / 700 — utilitarian, trust-forward

## 2. Color Palette & Roles

### Primary
- **Booking Blue** (`#003580`): Brand surfaces — top app bar, Genius banner, section headers, the score badge, the logotype.
- **CTA Blue** (`#0071C2`): Primary action — "Search", "Reserve", "See availability", links, selected filter chips, active tab.
- **CTA Blue Pressed** (`#005A9C`): Active/pressed state for the action blue.
- **Blue Tint** (`#E7F0F7`): Light wash behind selected filters, info banners, and the selected map pin.

### Accent
- **Brand Yellow** (`#FEBB02`): Star-rating fill only — the single non-blue accent.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary canvas, cards, sheets.
- **Surface** (`#F2F2F2`): Section separators, grouped backgrounds, skeletons, search-row fill.
- **Surface Deep** (`#E6E6E6`): Pressed state on surface, alternating zones.
- **Divider** (`#E0E0E0`): Hairline dividers between list rows and card sections.
- **Text Primary** (`#1A1A1A`): Property names, prices, titles, primary UI text.
- **Text Secondary** (`#6B6B6B`): Location, distance, room type, dates, metadata.
- **Text Tertiary** (`#949494`): Disabled labels, very low-emphasis captions, struck original price.

### Semantic
- **Success Green** (`#008009`): "Free cancellation", "Breakfast included", available.
- **Deal Red** (`#CC0000`): Urgency cues ("Only 2 rooms left"), discount price, "−25%".
- **Genius Blue** (`#003580`): Genius badge background (shares Booking Blue).
- **Warning Amber** (`#F5A623`): "High demand", limited availability.
- **Error Red** (`#CC0000`): Form validation, payment errors (shares deal red).

### Dark Mode (Limited Use)
Booking.com's iOS app is light-only by design — the dense, photo-and-badge marketplace depends on white. A dark variant exists for system dark mode but is not the designed experience.
- **Dark Canvas** (`#1A1A1A`)
- **Dark Surface** (`#262626`)
- **Dark Text** (`#FFFFFF`)
- Booking Blue, CTA Blue, and yellow stay identical — all read on dark.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (BookingSans-equivalent grotesque used across the product)
- **Numerals**: Inter tabular figures for prices, review scores, counts (`font-variant-numeric: tabular-nums`)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 22pt | 700 | 1.2 | -0.3pt | "Search", "Stays" nav title |
| Section Header | Inter | 18pt | 700 | 1.25 | -0.2pt | "Browse by property type" |
| Property Name | Inter | 17pt | 700 | 1.25 | -0.1pt | Hotel / stay card title |
| Price | Inter | 17pt | 700 | 1.2 | 0pt | Total price, right-aligned, tabular |
| Score Badge Num | Inter | 15pt | 700 | 1.0 | 0pt | The "8.9" inside the navy badge, tabular |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Descriptions, policy copy |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Blue "Search", "Reserve" |
| Score Word | Inter | 14pt | 700 | 1.2 | 0pt | "Fabulous", "Very good" beside the badge |
| Meta / Location | Inter | 13pt | 400 | 1.3 | 0pt | "0.5 km from centre", "1 night, 2 adults" |
| Badge / Tag | Inter | 12pt | 600 | 1.2 | 0.1pt | "Genius", "Free cancellation" |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Caption / Fine | Inter | 11pt | 400 | 1.3 | 0pt | "Includes taxes and fees", struck price |

### Principles
- **Density over drama**: There are no hero headlines — the photo + name + score badge + price is the unit; type is tuned to fit more decisions per screen
- **Bold for the decision drivers**: Property name, price, and the score number are weight 700; supporting context is regular `#6B6B6B`
- **Tabular figures everywhere numeric**: Prices, review scores, "Only N left", and night counts use tabular Inter so columns and badges stay aligned
- **The score badge is typographic**: A one-decimal number (always `X.X`, e.g. `8.9` not `8.90`) in weight 700 white on navy — consistency of this format is brand recognition
- **Trust copy is small but present**: "Includes taxes and fees", free-cancellation, and policy lines stay at 11-13pt — never hidden, the transparency is part of conversion

## 4. Component Stylings

### Buttons

**Primary CTA ("Search" / "Reserve" / "See availability")**
- Background: `#0071C2`
- Text: `#FFFFFF`, Inter 16pt weight 700
- Height: 52pt (full-width), 48pt (inline)
- Corner radius: 8pt
- Pressed: background `#005A9C`, scale 0.98
- Disabled: background `#E0E0E0`, text `#949494`

**Secondary / Outline ("Change search", "Edit")**
- Background: `#FFFFFF`
- Border: 1.5pt solid `#0071C2`
- Text: `#0071C2`, Inter 15pt weight 700
- Corner radius: 8pt
- Pressed: background `#E7F0F7`

**Text / Link Button ("Show on map", "See all 248 photos")**
- Font: Inter 14pt weight 700
- Color: `#0071C2`
- No underline (or underline on press), 44pt hit area

**Map Toggle (list ⇄ map)**
- A pill floating above the list (or in the nav): "Map" with a pin glyph
- Background: `#FFFFFF` with a soft shadow / or `#003580` filled when on map
- Text: `#0071C2` (or white when active)

**Quantity Stepper (guests / rooms)**
- Round 32pt − and + buttons, 1.5pt `#0071C2` border, glyph `#0071C2`
- Center count: Inter 16pt weight 700 tabular `#1A1A1A`
- Disabled minus at 1: border/glyph `#E0E0E0`

### Cards & Containers

**Property Card (the core unit)**
- Layout: leading photo (~120pt square or 4:3, 8pt radius) → details column → trailing price block (or stacked on small widths)
- Background: `#FFFFFF`, 8pt radius, 1pt `#E0E0E0` border or soft shadow
- Name: Inter 17pt weight 700 `#1A1A1A`
- Below name: location link `#0071C2` 13pt + distance `#6B6B6B` 13pt
- Room/policy lines: 13pt `#6B6B6B`; green policy text `#008009` ("Free cancellation")
- Score row: the navy score badge + word + "(1,284 reviews)" 12pt `#6B6B6B`
- Price block (right): struck original price `#949494` 13pt (optional), big total `#1A1A1A` 17pt weight 700, "Includes taxes and fees" 11pt `#6B6B6B`
- Urgency line: `#CC0000` 12pt weight 600 ("Only 2 rooms left at this price")
- Pressed: scale 0.99

**Search Form Card**
- A single rounded `#FFFFFF` card (8pt radius, soft shadow) stacking:
  - Destination row (location pin glyph + "Where are you going?")
  - Dates row (calendar glyph + "Fri 12 Jul — Sun 14 Jul")
  - Guests row (person glyph + "2 adults · 0 children · 1 room")
  - Full-width CTA-blue "Search" button
- Each row 52pt, divided by `#E0E0E0` hairlines

**Genius Loyalty Banner**
- Full-width navy `#003580` strip / card, 8pt radius
- "Genius" wordmark + "You're a Genius level 1 member" + benefit line in white
- Optional discount chip in `#FEBB02`

**Filter Chip Row**
- Horizontal scroll of pills above the results
- Default: `#FFFFFF`, 1pt `#E0E0E0` border, text `#1A1A1A`
- Selected: `#E7F0F7` fill, 1.5pt `#0071C2` border, text `#0071C2` weight 700
- Leading "Filter" pill with a sliders glyph and an active-count badge

### Navigation

**Top App Bar**
- Background: `#003580` (Booking Blue)
- Height: 56pt + safe area
- Title / destination + dates summary in white, Inter 16pt weight 700
- Back chevron + edit/search affordances in white
- On detail screens it can collapse to a translucent bar over the photo gallery

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, hairline top border `#E0E0E0`
- Tabs: Search, Saved, Bookings, Profile, Help
- Icon size: 24pt
- Active color: `#0071C2` (filled icon + label)
- Inactive: `#6B6B6B` (outlined icon)
- Labels: Inter 11pt weight 600, always shown

### Input Fields

**Destination / Search Field**
- Background: `#FFFFFF` (in the card) or `#F2F2F2` (standalone)
- Height: 52pt, corner radius 8pt
- Leading SF Symbol `magnifyingglass` / `mappin` 18pt `#6B6B6B`
- Placeholder: "Where are you going?" Inter 16pt weight 400 `#6B6B6B`
- Focus: 2pt `#0071C2` border

**Date / Guest Pickers**
- Rows that open a calendar sheet / stepper sheet
- Selected range highlighted in `#0071C2` on the calendar; today ringed in `#0071C2`

### Distinctive Components

**Review-Score Badge**
- The signature mark. A small solid `#003580` rounded rectangle (4-6pt radius), ~30×24pt.
- Contains a one-decimal score in white, Inter 15pt weight 700, tabular — always `X.X` (e.g. `8.9`, `9.2`, `7.4`).
- Immediately to its right: a bold word label (`#1A1A1A`, Inter 14pt weight 700) — "Fabulous" (9+), "Very good" (8+), "Good" (7+) — followed by "· 1,284 reviews" in `#6B6B6B` 12pt.
- The badge is corner-anchored on photos in carousels and inline on cards/detail headers. Its rigid format (navy chip + one decimal + word) is the brand tell.

**Search Form Card**
- The home screen's focal element: a single elevated white card consolidating destination, dates, and guests with the big blue Search button — the entire conversion funnel in one tap target group.

**Genius Banner**
- A recurring navy promotional strip that surfaces member pricing; visually distinct (full navy) so the loyalty value is unmissable without competing with property cards.

**Map Toggle + Price Pins**
- A toggle flips the list into a map of price-pins (rounded `#FFFFFF` bubbles showing total price; the selected pin turns `#003580` with white text and a slight scale-up). Tapping a pin slides a single property card up from the bottom.

**Urgency / Scarcity Cues**
- Inline `#CC0000` weight-600 lines ("Only 2 rooms left", "In high demand — booked 14 times today") placed directly under the price to compress the decision.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52
- Standard margin: 16pt horizontal
- Card internal padding: 12pt
- Filter chip gap: 8pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Property cards: full-width single-column rows (photo-left layout on phone)
- Browse tiles: 2-column grid (12pt gap)
- Map: full-bleed with floating list/map toggle and a bottom property card

### Whitespace Philosophy
- **Dense by intent**: This is a marketplace — cards pack name, score, location, policy, and price tightly; 12pt internal padding and `#E0E0E0` hairlines keep it scannable without wasted space
- **Surface chunks the feed**: `#F2F2F2` separates sections (search card / Genius banner / results / browse) so a long scroll stays navigable
- **The badge and price are visual anchors**: Whitespace is arranged so the navy score badge and the bold price are the two elements the eye lands on per card

### Border Radius Scale
- Sharp (0pt): Full-bleed photo gallery edges
- Tight (4-6pt): The review-score badge — deliberately small radius
- Standard (8pt): Buttons, cards, chips, search field, banners — the practical default
- Comfortable (12pt): Bottom sheets, the map's floating property card
- Large (16pt): Modal sheets
- Pill (500pt): Filter chips, map toggle, count badges
- Circle (50%): Avatars, guest steppers, icon buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | 1pt `#E0E0E0` border, no shadow | Property cards in the feed (border-defined) |
| Card (Level 1) | `rgba(26,26,26,0.08) 0 2px 6px` | The search form card, browse tiles |
| Raised (Level 2) | `rgba(26,26,26,0.12) 0 4px 14px` | The map's floating property card, sticky filter bar |
| Sheet (Level 3) | `rgba(26,26,26,0.16) 0 -8px 30px` | Bottom sheets (calendar, guests, sort) |
| Overlay Dim | `rgba(26,26,26,0.45)` | Modal scrim behind sheets |

**Shadow Philosophy**: Elevation is restrained and mostly border-defined — feed property cards use a 1pt `#E0E0E0` border rather than a shadow, which keeps a dense list visually quiet. Soft neutral shadows (mixed from the `#1A1A1A` ink) are reserved for elements that genuinely float: the search form card on the home screen and the property card that slides over the map. This keeps the marketplace from feeling noisy while still cueing the few interactive surfaces.

### Motion
- **Card tap**: scale 0.99 → 1.0 spring on press release
- **Filter chip toggle**: fill/border crossfade to selected over ~150ms; subtle scale 0.97 bounce
- **Map pin select**: tapped pin scales 1.0 → 1.12, recolors to `#003580`; property card slides up 0.3s ease-out
- **Search submit**: button press 0.98 scale; transition to results with a quick fade/slide
- **Score badge**: static — no animation; consistency is the point
- **Sheet present**: calendar/guests sheet slides up 0.3s ease-out with scrim fade
- **Genius banner**: no motion — a stable trust/value surface

## 7. Do's and Don'ts

### Do
- Use clean white (`#FFFFFF`) as the canvas — the marketplace needs density on white
- Use Booking Blue (`#003580`) for brand/structure: app bar, Genius banner, the score badge
- Use CTA Blue (`#0071C2`) for action: Search, Reserve, links, selected filters
- Render the review-score badge as a solid-navy rounded chip with a one-decimal score + word
- Always format the score as `X.X` (one decimal) — `8.9`, never `8.90` or `8`
- Use brand yellow (`#FEBB02`) only for star ratings
- Pack property cards densely but separate with `#E0E0E0` hairlines/borders
- Use tabular figures for prices, scores, and "Only N left" cues
- Keep trust copy ("Includes taxes and fees", free-cancellation) visible, not hidden
- Define feed cards with a 1pt border, not a heavy shadow

### Don't
- Don't use a tinted or dark canvas — the dense card feed needs white
- Don't blur the blue roles — navy is brand/structure, bright blue is action
- Don't restyle the score badge per screen — its rigid navy + one-decimal format is the brand tell
- Don't show the score as an integer or two decimals — always one decimal
- Don't add accent colors beyond the two blues and the rating yellow
- Don't use heavy drop shadows on feed cards — borders keep the list quiet
- Don't hide taxes/fees or cancellation policy — transparency is part of conversion
- Don't use fully-rounded pill buttons for primary CTAs — they are squared 8pt
- Don't let urgency red (`#CC0000`) bleed into non-scarcity UI — it's a targeted nudge

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Price block stacks under details; ~96pt card photo |
| iPhone 13/14/15 | 390pt | Standard photo-left card with side price block |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the navy app bar content |
| iPhone 15/16 Pro Max | 430pt | Larger card photo (~140pt), roomier price block |
| iPad | 768pt+ | 2-column card grid; split-view list + map; persistent filter sidebar |

### Dynamic Type
- Property names, body, policy copy: full scale
- Price and score-badge number: scale but stay tabular and aligned; badge keeps `X.X`
- Tab labels and small tags: fixed (layout-sensitive)
- Score word ("Fabulous"): scales with the name line

### Orientation
- Search / results / detail: **portrait-locked** on iPhone
- iPad: supports landscape with split list + map and a filter sidebar

### Touch Targets
- Property card: full-card tappable, generous height
- Filter chips: 32pt tall, full pill tappable
- Guest steppers − / +: 32pt with 44pt effective hit
- Map pins: ~36pt bubble, 44pt effective hit
- Primary CTA: 52pt tall, full width
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: navy app bar respects the safe area and Dynamic Island
- Bottom: tab bar and the map's floating property card clear the home indicator
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F2F2F2`
- Divider: `#E0E0E0`
- Text primary: `#1A1A1A`
- Text secondary: `#6B6B6B`
- Booking Blue (brand): `#003580`
- CTA Blue (action): `#0071C2`
- CTA Blue pressed: `#005A9C`
- Blue tint: `#E7F0F7`
- Brand yellow (stars): `#FEBB02`
- Success green: `#008009`
- Urgency / deal red: `#CC0000`

### Example Component Prompts
- "Create the SwiftUI Booking.com review-score badge: a solid #003580 rounded rectangle (5pt corner radius, ~30×24pt) containing '8.9' in Inter 15pt weight 700 tabular #FFFFFF. To its right, 'Fabulous' in Inter 14pt weight 700 #1A1A1A, then '· 1,284 reviews' in Inter 12pt #6B6B6B. Always render the score with exactly one decimal."
- "Build a Booking.com property card: white background, 8pt corner radius, 1pt #E0E0E0 border (no shadow). Leading 120pt 4:3 photo (8pt radius). Details: name 'Grand Plaza Hotel' Inter 17pt weight 700 #1A1A1A; 'City centre' link #0071C2 13pt + '0.5 km from centre' #6B6B6B 13pt; 'Free cancellation' #008009 13pt; the navy score badge row. Trailing price block: struck '$240' #949494 13pt, '$198' Inter 17pt weight 700 #1A1A1A, 'Includes taxes and fees' #6B6B6B 11pt, and '#CC0000' weight-600 'Only 2 rooms left'."
- "Design the search form card: a single white card, 8pt radius, soft shadow, stacking three 52pt rows divided by #E0E0E0 hairlines — destination (mappin glyph + 'Where are you going?'), dates (calendar glyph + 'Fri 12 Jul — Sun 14 Jul'), guests (person glyph + '2 adults · 0 children · 1 room') — then a full-width #0071C2 'Search' button (52pt, Inter 16pt weight 700 #FFFFFF, 8pt radius)."
- "Create the Genius loyalty banner: a full-width #003580 card, 8pt radius, with the 'Genius' wordmark and 'You're a Genius level 1 member — enjoy 10% off' in white Inter, and a #FEBB02 '10% off' chip."
- "Build the Booking.com bottom tab bar: white background, hairline #E0E0E0 top border, 5 tabs — Search, Saved, Bookings, Profile, Help. Active tab #0071C2 filled icon + Inter 11pt weight 600 label; inactive #6B6B6B outlined. The top app bar above content is solid Booking Blue #003580 with white title text."

### Iteration Guide
1. Canvas is clean white `#FFFFFF` — the dense card marketplace needs it
2. Booking Blue `#003580` = brand/structure (app bar, Genius banner, score badge); CTA Blue `#0071C2` = action (Search, Reserve, links, selected filters)
3. The review-score badge is the signature: solid-navy rounded chip + one-decimal score (`X.X`) + bold word — never restyle it per screen
4. Brand yellow `#FEBB02` only for star ratings; no other accents beyond the two blues
5. Property cards are dense and border-defined (1pt `#E0E0E0`), not shadow-heavy — the score badge and price are the visual anchors
6. Tabular figures for prices, scores, and scarcity cues so columns/badges align
7. Keep trust copy (taxes/fees, free cancellation) visible — it is part of conversion
8. Buttons and cards are squared 8pt; only the search card and the map's floating card actually float
