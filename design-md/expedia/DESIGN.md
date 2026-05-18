# Design System Inspiration of Expedia (iOS)

## 1. Visual Theme & Atmosphere

Expedia's iOS app is a **deal-forward travel marketplace** wrapped in a confident, trip-blue identity. The light canvas is a clean near-white (`#FFFFFF` / `#F5F7FA`) and the app's emotional center of gravity is the moment you see a *better price*: bundle savings, member prices, and One Key rewards are surfaced everywhere, almost always in the brand's signature **Expedia Yellow** (`#FFC94D`). The app is dense but not cluttered — it leans on full-bleed property and destination photography, big legible price typography, and a yellow "savings" accent that punches against the otherwise blue-and-grey chrome. Where Airbnb whispers and Notion disappears, Expedia *sells*: every card has a reason-to-believe (a strikethrough price, a "−24% Bundle" flag, a "Member price" tag, an "Earn 2,040 One Key cash" line).

The structural anchor is the **search-and-results loop**. Open the app and you land on a search surface with a segmented mode switch — **Stays / Flights / Cars / Packages (Bundle)** — sitting above a single tappable search pill ("San Diego, CA · Oct 12–17 · 2 travelers · 1 room"). Tap search and you drop into a vertically scrolling **results list** of property cards: a 16:10 hero photo with a deal flag top-left and a save-heart top-right, then title, neighborhood, a navy review-score badge ("9.2 Wonderful · 1,847 reviews"), a price block (strikethrough → discounted → "/ night"), and a gold One Key earn line. This card is the workhorse of the entire app and its anatomy is repeated for flights (fare cards) and cars.

Color is purposeful and role-bound. **Expedia Yellow** `#FFC94D` is the *savings/deal/loyalty highlight* — bundle tags, member-price flags, the One Key chip, primary "Bundle & save" CTAs. **Action Blue** `#1668E3` is the *interactive* color — links, secondary CTAs, the reserve button, selected states. **Expedia Navy** `#00355F` is the *brand/trust* color — the logomark field, review-score badge backgrounds, dark headers. **One Key gold** `#F5C518` is a tightly-scoped *rewards-only* color — One Key cash balances and "earn" lines, never used for generic UI. There is a clear hierarchy: navy says "trusted travel brand," blue says "tap me," yellow says "you're saving money," gold says "this is your loyalty currency."

Typography is **Expedia Sans** (the company's proprietary geometric humanist family, designed by Pentagram for the 2017+ rebrand), which falls back gracefully to **Inter** / SF Pro on iOS when the bundled face is unavailable. The hierarchy is product-UI rhythm (display → screen title → section → card title → body → meta → badge), not editorial. Prices are the loudest non-display element: an 18pt 800-weight number with a strikethrough antecedent and a quiet "/ night" suffix. Weights concentrate at 400 (body), 600 (subtitles, badges), 700 (card titles, sections), 800 (display, prices).

**Key Characteristics:**
- Light near-white canvas (`#FFFFFF` / `#F5F7FA`) light / navy-tinted charcoal (`#0E1116`) dark — content-forward, deal-forward
- Search-and-results loop — segmented Stays/Flights/Cars/Bundle switch → single search pill → vertical results list
- Property/fare result card — 16:10 hero, deal flag (top-left), save heart (top-right), title, location, review-score badge, price block, One Key earn line
- Expedia Yellow (`#FFC94D`) = savings/deal/loyalty highlight; never generic chrome
- Action Blue (`#1668E3`) = interactive (links, CTAs, selection)
- Expedia Navy (`#00355F`) = brand/trust (logomark, review badge, headers)
- One Key gold (`#F5C518`) = rewards-only (balances, earn lines, One Key chip)
- Navy review-score badge ("9.2 Wonderful · 1,847 reviews") — Expedia's signature trust signal
- Bundle + Save — packaging stay+flight+car for a discounted "Bundle" price (the core monetization gesture)
- Strikethrough → discounted price pattern on nearly every card
- Trips tab — itinerary timeline grouping flights, stays, cars under a single trip
- Bottom tab bar: Search / Saved / Trips / Support / Account

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Expedia Yellow** (`#FFC94D`): Savings/deal/loyalty highlight — bundle tags, member-price flags, One Key chip, "Bundle & save" primary CTA fill (text on it is navy `#00355F`).
- **Yellow Deep** (`#FFB31A`): Pressed state for yellow CTAs and tags.
- **Action Blue** (`#1668E3`): Primary interactive — "Reserve" CTA, links, secondary CTAs, selected segment underline, in-card actions.
- **Action Blue Pressed** (`#0F4FB0`): Pressed state for blue CTAs.
- **Expedia Navy** (`#00355F`): Brand/trust — logomark field, review-score badge background, dark hero overlays, top app bar on detail screens.
- **Navy Soft** (`#14416B`): Lighter navy for secondary badges and chips on dark.
- **One Key Gold** (`#F5C518`): Rewards-only — One Key cash balance, "Earn X One Key cash" lines, One Key membership chip. Never used for generic UI.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, card backgrounds.
- **Surface Gray** (`#F5F7FA`): App background behind cards, grouped sections.
- **Surface Pressed** (`#ECEFF4`): Pressed rows, selected list items.
- **Divider** (`#E3E7ED`): Hairline dividers between rows, card borders.
- **Scrim** (`#00000066`): 40% black overlay on hero photos for text legibility.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0E1116`): Primary dark canvas — carries a subtle navy bias (NOT pure black) echoing Expedia Navy.
- **Dark Surface 1** (`#161B22`): Card backgrounds, elevated surfaces.
- **Dark Surface 2** (`#1F2630`): Chips, segmented controls, secondary fills.
- **Dark Divider** (`#2A323E`): Hairline dividers and card borders.
- **Dark Scrim** (`#00000080`): 50% black overlay on hero photos.

### Text
- **Text Primary Light** (`#1A1F26`): Primary body and titles on light.
- **Text Secondary Light** (`#5A6573`): Metadata, neighborhoods, captions.
- **Text Tertiary Light** (`#8A95A3`): Strikethrough prices, disabled.
- **Text Primary Dark** (`#E8EBEF`): Primary text on dark.
- **Text Secondary Dark** (`#9AA4B2`): Metadata on dark.
- **Text Tertiary Dark** (`#6A7585`): Strikethrough prices, disabled on dark.

### Semantic
- **Success Green** (`#1A8B4B`): "Free cancellation", high review scores (9.0+ badge), confirmed booking.
- **Error Red** (`#D93A3A`): Form errors, "Only 2 rooms left at this price" urgency, cancellation warnings.
- **Warning Amber** (`#E8830C`): Price-change alerts, "Prices may rise" banners.
- **Star Gold** (`#FFC94D`): Star ratings on properties (shares Expedia Yellow).
- **Member Price Tint** (`#FFC94D1F`): 12% yellow background behind member-price callouts.

### Review-Score Badge Color Ramp
The navy/blue/green badge that fronts every property — color shifts with score band.

| Score Band | Badge Background | Label Text |
|------------|------------------|------------|
| 9.0–10 (Wonderful) | `#1A8B4B` | Wonderful |
| 8.0–8.9 (Excellent) | `#1668E3` | Excellent |
| 7.0–7.9 (Very Good / Good) | `#14416B` | Good |
| 6.0–6.9 (Pleasant) | `#5A6573` | Pleasant |
| < 6.0 | `#8A95A3` | — |

## 3. Typography Rules

### Font Family
- **Primary**: `Expedia Sans` (proprietary, by Pentagram for Expedia Group, 2017+ rebrand) — geometric humanist sans tuned for travel commerce: legible price figures, generous x-height, confident bold weights.
- **Fallback Stack**: `'Expedia Sans', -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Closest free substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — matched x-height, geometric construction, excellent tabular figures for prices.
- **Numerals**: Tabular/lining figures for all prices and review scores (so columns of prices align).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Expedia Sans | 32pt | 800 | 1.15 | -0.6pt | Home hero ("Find your next trip") |
| Screen Title | Expedia Sans | 26pt | 800 | 1.2 | -0.4pt | Destination / results header |
| Section | Expedia Sans | 22pt | 700 | 1.25 | -0.2pt | "Bundle + Save deals" |
| Card Title | Expedia Sans | 18pt | 700 | 1.3 | -0.2pt | Property / fare name |
| Price (Now) | Expedia Sans | 18pt | 800 | 1.2 | -0.2pt | Discounted price figure |
| Body | Expedia Sans | 16pt | 400 | 1.5 | 0pt | Descriptions, policy text |
| Card Subtitle | Expedia Sans | 15pt | 600 | 1.35 | 0pt | Neighborhood, "0.4 mi from center" |
| Review Score | Expedia Sans | 15pt | 800 | 1.0 | 0pt | "9.2" inside navy badge |
| Meta | Expedia Sans | 14pt | 400 | 1.4 | 0pt | "1,847 reviews · Free cancellation" |
| Price (Strike) | Expedia Sans | 13pt | 400 | 1.0 | 0pt | Strikethrough antecedent price |
| Badge / Tag | Expedia Sans | 12pt | 600 | 1.0 | 0.2pt | "−24% Bundle", "Member price" |
| One Key Line | Expedia Sans | 11pt | 700 | 1.2 | 0.1pt | "Earn 2,040 One Key cash" |
| Button | Expedia Sans | 16pt | 700 | 1.0 | 0pt | Primary/secondary CTAs |
| Tab Label | Expedia Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Price is the loudest content element**: discounted price at 18pt 800, strikethrough at 13pt 400 tertiary, "/ night" suffix at 12pt secondary — the eye lands on savings.
- **Tabular figures everywhere prices appear**: prices, review scores, point balances must align in vertical lists.
- **Weight concentrated at 400 / 600 / 700 / 800**: body regular, subtitles/badges semibold, titles/sections bold, display/prices extra-bold. No thin, no black.
- **Navy on yellow, white on blue**: text on Expedia Yellow is always navy `#00355F`; text on Action Blue is always white `#FFFFFF`.
- **Dynamic Type first-class**: display, titles, body, prices scale; tab labels, badges, One Key lines stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button (Reserve / Continue)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#1668E3` (Action Blue)
- Text: `#FFFFFF`, Expedia Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Full-width on detail/checkout screens (leading + trailing 16pt insets)
- Pressed: background `#0F4FB0` + scale 0.98
- Disabled: `#1668E3` at 38% opacity

**Savings / Bundle Button (Bundle & save)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FFC94D` (Expedia Yellow)
- Text: `#00355F` (Expedia Navy), Expedia Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#FFB31A` + scale 0.98
- Used for the package/bundle conversion CTA — the highest-intent action

**Secondary / Outline Button (Add a flight)**
- Background: transparent
- Text: `#1668E3` (Action Blue)
- Border: 1pt `#1668E3`
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 8pt
- Pressed: background `#1668E31A` (10% blue)

**Text Button (See all 38 deals)**
- Font: Expedia Sans 14pt weight 600
- Color: `#1668E3`
- No underline; chevron trailing optional
- Pressed: 60% opacity

**Icon Button (Save heart)**
- Size: 20pt glyph in 30pt circular hit area
- On hero photo: filled-black-40% circle behind for contrast
- Unsaved: hollow heart, `#FFFFFF` stroke 1.8pt, `rgba(0,0,0,0.4)` fill
- Saved: filled `#1668E3` with `#FFFFFF` 1.6pt stroke
- Toggle: scale-bounce 1.0 → 1.2 → 1.0 in 220ms + soft haptic

### Core Atoms

**Property / Result Card**
- Container: `#FFFFFF` light / `#161B22` dark, 16pt corner radius, 0.5pt `#E3E7ED` / `#2A323E` border, subtle shadow `0 4px 12px rgba(0,0,0,0.08)` light / `0 4px 12px rgba(0,0,0,0.4)` dark
- Hero photo: full-width, 16:10 aspect ratio, square top corners (clipped to card radius)
- **Deal flag** (top-left, 12pt inset): `#FFC94D` background, `#00355F` text, Expedia Sans 12pt w800, 6pt corner radius, 4pt vertical / 9pt horizontal padding ("−24% Bundle", "Member price")
- **Save heart** (top-right, 10pt inset): see Icon Button
- Body padding: 12pt top, 14pt horizontal, 14pt bottom
- Title: Expedia Sans 18pt w700
- Subtitle (location): Expedia Sans 15pt w600 secondary, 3pt below title
- **Review-score row** (8pt below subtitle): navy badge (`#14416B`/`#1A8B4B`/`#1668E3` per band) with white 15pt w800 score + 6pt gap + word ("Wonderful") in 12pt w700 + " · 1,847 reviews" in 12pt secondary
- **Price row** (10pt below): optional strikethrough `#8A95A3` 13pt + discounted `#1A1F26` 18pt w800 + "/ night" 12pt secondary
- **One Key earn line** (7pt below): gold dot/coin + "Earn 2,040 One Key cash" Expedia Sans 11pt w700 `#F5C518`

**Flight / Fare Card** (variant of result card)
- Top row: airline logo (24pt) + airline name + cabin
- Center: depart time → duration arc with stops dots → arrive time (times Expedia Sans 18pt w700, airport codes 13pt secondary)
- Bottom: price block (right-aligned, 18pt w800) + "Round trip" 12pt secondary + One Key earn line
- "Hacker fare" / "Self-transfer" badge: amber `#E8830C` chip when mixed-carrier

**Review-Score Badge**
- Rounded rectangle, 6pt corner radius
- Background per score band (see §2 ramp)
- Text: white, Expedia Sans 15pt w800
- Padding: 3pt vertical, 7pt horizontal
- Always paired with a word label and review count to its right

**Price Block**
- Strikethrough antecedent: Expedia Sans 13pt w400 `#8A95A3`, `line-through`
- Discounted/now: Expedia Sans 18pt w800 `#1A1F26` (or `#E8EBEF` dark)
- Period suffix: Expedia Sans 12pt w400 secondary, baseline-aligned
- "Member price" / "Bundle price" label: 12pt w600, color = green `#1A8B4B` for member, navy `#00355F` on yellow tag for bundle

**One Key Chip / Pill**
- Pill, 500pt corner radius
- Background: `#1F2630` dark / `#FFC94D1F` light
- Leading 7pt gold dot or coin glyph
- Text: Expedia Sans 11pt w700 `#F5C518`
- Content: "12,480 One Key" or "$124.80 One Key cash"

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `#0E1116` with 0.5pt top divider `#E3E7ED` / `#2A323E`
- Tabs (5): Search, Saved, Trips, Support, Account
- Icon size: 22pt
- Active: `#1668E3` (Action Blue), filled glyph
- Inactive: `#8A95A3` / `#6A7585`, outline glyph
- Labels: Expedia Sans 10pt w600, always shown
- No pill indicator — color + fill change only

**Top App Bar (Detail / Results)**
- Height: 44pt + safe area
- On photo hero: transparent → solidifies to `#FFFFFF`/`#0E1116` on scroll
- Leading: back chevron (white over photo, primary text once solid)
- Trailing: share + save heart
- Title: appears centered on scroll past hero

**Segmented Mode Switch (Stays / Flights / Cars / Bundle)**
- 4 equal segments, each a 10pt-radius pill, 9pt vertical padding
- Inactive: `#F5F7FA`/`#1F2630` background, `#5A6573`/`#9AA4B2` text, 0.5pt divider border
- Active: `#FFC94D` background, `#00355F` text, no border
- Transition: 200ms ease-out background + text color crossfade

### Input Fields

**Search Pill (the entry point)**
- Height: 52pt
- Background: `#FFFFFF`/`#161B22`, 0.5pt `#E3E7ED`/`#2A323E` border
- Corner radius: 14pt
- Shadow: `0 4px 12px rgba(0,0,0,0.08)` / `rgba(0,0,0,0.4)`
- Leading: `magnifyingglass` 17pt secondary
- Two-line content: line 1 destination Expedia Sans 14pt w700; line 2 "Oct 12 – 17 · 2 travelers · 1 room" 11pt secondary
- Tap: pushes full search form (destination autocomplete, date range, traveler stepper)

**Text Field (forms / traveler details)**
- Height: 52pt
- Background: `#F5F7FA`/`#1F2630`
- Corner radius: 8pt
- Border: 1pt transparent → `#1668E3` on focus
- Label: floating, Expedia Sans 12pt secondary
- Error: 1pt `#D93A3A` border + helper text `#D93A3A` 12pt

**Date Range / Calendar**
- Two-month vertical scroll calendar
- Selected start/end: `#1668E3` filled circle, white text
- In-range: `#1668E31A` (10% blue) connecting band
- Today: `#1668E3` ring

### Distinctive Components

**Bundle + Save Module**
- A horizontally laid "stay + flight (+ car)" stack with a yellow savings ribbon
- Header: "Bundle and save $214" — savings figure in `#00355F` on `#FFC94D1F` band
- CTA: yellow "Bundle & save" button
- Each component row: icon + name + individual price (strikethrough) → bundled price

**One Key Rewards Strip**
- Surface `#F5F7FA`/`#1F2630`, 12pt corner radius, 0.5pt divider
- Leading 36pt gold coin ("1K"), title "One Key cash available" 13pt w700, subtitle 11pt secondary
- Trailing balance: Expedia Sans 16pt w800 `#F5C518`
- Appears on home, checkout, and Account

**Trips Timeline**
- Vertical itinerary grouped by trip → by day
- Each item: type icon (flight/bed/car) + time + title + status chip
- Connecting vertical rail `#E3E7ED`/`#2A323E` between items
- Confirmed status: green `#1A8B4B` chip; pending: amber `#E8830C`

**Price Forecast / Change Banner**
- Full-width inline banner, `#FFC94D1F` background, 8pt radius
- Leading trend glyph, text "Prices for these dates are trending up" 13pt w600
- Optional "Track price" outline button trailing

**Map Price Pins**
- Pill, 12pt radius, drop shadow `0 2px 6px rgba(0,0,0,0.4)`
- Default: `#FFFFFF`/`#161B22` bg, navy text
- Selected: `#1668E3` bg, white text
- Viewed: `#ECEFF4`/`#0E1116` muted

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Card vertical gap: 16pt
- Card internal padding: 12–14pt
- Screen horizontal inset: 16pt
- Section header to first card: 10pt

### Grid & Container
- iPhone: single-column results list, 16pt insets, full-bleed hero photos within cards
- iPad: 2-column results grid (max content width 980pt centered, 24pt insets); detail uses split (list + map/detail)
- Map view: full-bleed map with floating result carousel at bottom

### Whitespace Philosophy
- **Commerce density with breathing room**: cards are information-dense (photo, title, score, price, loyalty) but separated by a generous 16pt gap so each is a discrete decision unit
- **Savings gets the spotlight**: yellow deal flags and One Key lines get their own visual lane — never crowded by body copy
- **Photo-first**: every result leads with a 16:10 photo; chrome is minimal over it (just deal flag + heart)

### Border Radius Scale
- Square (0pt): hero photo top edge (clipped to card), dividers
- Subtle (4pt): inline price chips
- Tag (6pt): deal flags, review-score badge
- Standard (8pt): buttons, text fields, inline banners
- Card (12–16pt): result cards, rewards strip, modules
- Pill (500pt): One Key chip, segmented control, map pins
- Circle (50%): save-heart hit area, avatars, One Key coin

## 6. Depth & Elevation

Expedia uses **soft, functional elevation** — cards lift just enough to read as tappable units; deal flags and CTAs sit flat on their surfaces.

| Level | Treatment (Light) | Treatment (Dark) | Use |
|-------|-------------------|------------------|-----|
| Flat (0) | none | none | Page background, deal flags, badges |
| Card (1) | `0 4px 12px rgba(0,0,0,0.08)` | `0 4px 12px rgba(0,0,0,0.4)` | Result cards, rewards strip, search pill |
| Floating (2) | `0 8px 24px rgba(0,0,0,0.12)` | `0 8px 24px rgba(0,0,0,0.55)` | Map result carousel, sticky CTA bar |
| Sheet (3) | `0 -8px 28px rgba(0,0,0,0.14)` | `0 -8px 28px rgba(0,0,0,0.6)` | Bottom sheets (filters, traveler picker) |
| Modal Scrim | `rgba(0,0,0,0.4)` | `rgba(0,0,0,0.55)` | Dim behind modals & sheets |

**Shadow Philosophy**: shadow signals "this is a tappable card / it floats." Deal flags, review badges, and the One Key chip are deliberately flat (color carries them). On dark, shadows deepen to 0.4–0.6 opacity to register over `#0E1116`.

### Motion
- **Card tap → detail**: shared-element hero photo expands, push transition 320ms ease-out
- **Save heart**: scale 1.0 → 1.2 → 1.0 over 220ms + soft haptic; deal recalculates silently
- **Segmented switch**: active pill background + text crossfade 200ms ease-out
- **Price update (date change)**: old price cross-dissolves to new in 180ms; brief `#FFC94D` flash on the price if it dropped
- **Bundle reveal**: savings ribbon slides down + scales from 0.96 over 240ms ease-out when a flight is added
- **Filter sheet**: bottom sheet slides up 300ms ease-out; result count animates as filters toggle
- **Sticky CTA bar**: fades/translates in over 200ms once the user scrolls past the fold on a detail screen
- **Pull-to-refresh**: Expedia-yellow spinner
- **Haptics**: soft impact on save, segment switch, date-range commit; success haptic on booking confirmation

## 7. Do's and Don'ts

### Do
- Use Expedia Yellow (`#FFC94D`) strictly for savings/deal/loyalty highlights — bundle tags, member prices, One Key chip, the "Bundle & save" CTA
- Put navy `#00355F` text on yellow, white on Action Blue `#1668E3`
- Lead every result card with a 16:10 photo; deal flag top-left, save heart top-right
- Show the review-score badge with score + word + count ("9.2 Wonderful · 1,847 reviews")
- Use the strikethrough → discounted price pattern when there is a real saving
- Reserve One Key gold (`#F5C518`) exclusively for rewards balances and earn lines
- Use Action Blue (`#1668E3`) for the primary "Reserve" CTA and all links
- Give cards a 16pt gap and a soft `0 4px 12px` shadow so each is a discrete decision
- Carry the search-and-results loop: segmented mode switch → search pill → vertical list
- Use a navy-biased dark canvas (`#0E1116`), NOT pure black
- Animate a brief yellow price flash when a price drops

### Don't
- Don't use Expedia Yellow for generic chrome, body text, or non-savings UI — it dilutes the savings signal
- Don't put white text on yellow or yellow text on blue (contrast + brand violation)
- Don't use One Key gold for anything other than rewards (no generic icons, no body accents)
- Don't show a strikethrough price when there is no real discount
- Don't bury the review-score badge — it's the primary trust signal and must front the card
- Don't use pure black (`#000000`) for the dark canvas — `#0E1116` carries the navy bias
- Don't make the bundle/savings CTA blue — the highest-intent action is yellow
- Don't over-shadow flat elements (deal flags, badges, One Key chip stay flat)
- Don't crowd the One Key earn line with other copy — it has its own lane
- Don't animate aggressively — motion is functional, 180–320ms ease-out
- Don't replace the segmented mode switch with a dropdown — Stays/Flights/Cars/Bundle is a glanceable, always-visible switch

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; hero photo 16:10; segmented switch labels may abbreviate |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; sticky CTA respects safe area |
| iPhone 15/16 Pro Max | 430pt | Larger hero photos; 2-line meta has more room |
| iPad (portrait) | 768pt | 2-column results grid; detail in split view (list + detail) |
| iPad (landscape) | 1024pt+ | Results list + persistent map pane side-by-side |

### Dynamic Type
- Display, screen title, section, card title, body, prices: full scale
- Tab labels, deal flags/badges, One Key lines, review-score number: FIXED (layout-sensitive)
- Prices keep tabular figures at every scale

### Orientation
- iPhone: portrait-primary; landscape supported on detail/photo gallery
- iPad: full rotation; landscape unlocks side-by-side list + map

### Touch Targets
- Save heart: 30pt circle hit (20pt glyph)
- Tab bar icons: 22pt glyph, 44pt hit
- Segmented switch segment: full segment height (≥ 36pt)
- Search pill: full 52pt height tappable
- Primary CTA: ≥ 48pt tall

### Safe Area Handling
- Top app bar respects safe area + Dynamic Island
- Sticky CTA bar and bottom tab respect home indicator
- Map result carousel floats above bottom safe area
- Keyboard: search form scrolls above keyboard; date picker is a sheet above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF` / surface `#F5F7FA`
- Canvas (dark): `#0E1116`
- Surface (dark card): `#161B22`
- Divider: `#E3E7ED` light / `#2A323E` dark
- Text primary: `#1A1F26` light / `#E8EBEF` dark
- Text secondary: `#5A6573` light / `#9AA4B2` dark
- Expedia Yellow (savings): `#FFC94D` (text on it: navy `#00355F`)
- Action Blue (interactive): `#1668E3`
- Expedia Navy (brand/trust): `#00355F`
- One Key Gold (rewards): `#F5C518`
- Success: `#1A8B4B`
- Error: `#D93A3A`
- Review badge — Wonderful: `#1A8B4B`, Excellent: `#1668E3`, Good: `#14416B`

### Example Component Prompts
- "Build the Expedia property result card in SwiftUI: `#FFFFFF` card (`#161B22` dark), 16pt corner radius, 0.5pt `#E3E7ED` border, shadow `0 4px 12px rgba(0,0,0,0.08)`. Top: 16:10 hero photo with a `#FFC94D` deal flag top-left (text `#00355F`, Expedia Sans 12pt w800, 6pt radius, '−24% Bundle') and a save heart top-right (20pt glyph in 30pt black-40% circle). Body 12/14/14pt padding: title Expedia Sans 18pt w700; location 15pt w600 `#5A6573`; review row = `#1A8B4B` badge with white 15pt w800 '9.2' + '· Wonderful · 1,847 reviews'; price row = strikethrough `#8A95A3` 13pt '$268' then `#1A1F26` 18pt w800 '$204' + '/ night' 12pt secondary; One Key line = gold dot + 'Earn 2,040 One Key cash' 11pt w700 `#F5C518`."

- "Create the Expedia segmented mode switch: 4 equal pills (Stays / Flights / Cars / Bundle), 10pt radius, 9pt vertical padding. Inactive: `#F5F7FA` bg, `#5A6573` text, 0.5pt `#E3E7ED` border. Active: `#FFC94D` bg, `#00355F` text, no border. 200ms ease-out crossfade on switch."

- "Build the Expedia search pill: 52pt tall, `#FFFFFF` bg, 14pt radius, 0.5pt `#E3E7ED` border, shadow `0 4px 12px rgba(0,0,0,0.08)`. Leading magnifyingglass 17pt `#5A6573`. Two lines: 'San Diego, CA' Expedia Sans 14pt w700; 'Oct 12 – 17 · 2 travelers · 1 room' 11pt `#5A6573`. Tap pushes full search form."

- "Render the Expedia One Key rewards strip: `#F5F7FA` surface (`#1F2630` dark), 12pt radius, 0.5pt divider. Leading 36pt gold (`#F5C518`) coin '1K'. Title 'One Key cash available' Expedia Sans 13pt w700; subtitle 11pt `#5A6573`. Trailing balance Expedia Sans 16pt w800 `#F5C518`."

- "Build the Expedia primary CTA pair: 'Reserve' = `#1668E3` fill, white Expedia Sans 16pt w700, 8pt radius, 14/28pt padding, pressed `#0F4FB0` + scale 0.98. 'Bundle & save' = `#FFC94D` fill, `#00355F` text, same metrics, pressed `#FFB31A`."

- "Create the Expedia review-score badge: rounded rect 6pt radius, background by band — `#1A8B4B` (9.0+ Wonderful), `#1668E3` (8.0+ Excellent), `#14416B` (7.0+ Good). White Expedia Sans 15pt w800 score, 3/7pt padding, followed by word label 12pt w700 + ' · 1,847 reviews' 12pt `#5A6573`."

### Iteration Guide
1. Canvas is near-white (`#FFFFFF`/`#F5F7FA`) light, navy-biased charcoal `#0E1116` dark — NOT true black
2. Expedia Yellow `#FFC94D` is the savings/deal/loyalty color ONLY — never generic chrome; text on it is navy `#00355F`
3. Action Blue `#1668E3` is interactive — Reserve CTA, links, selection; white text on it
4. Every result card leads with a 16:10 photo, deal flag top-left, save heart top-right
5. The review-score badge (score + word + count) fronts every card — it's the trust signal
6. Use strikethrough → discounted price only when there's a real saving; prices use tabular figures
7. One Key gold `#F5C518` is rewards-only (balances, earn lines, One Key chip)
8. The search-and-results loop is the spine: segmented switch (Stays/Flights/Cars/Bundle) → search pill → vertical list
9. The highest-intent CTA ("Bundle & save") is yellow, not blue
10. Motion is functional 180–320ms ease-out; flash the price yellow when it drops
