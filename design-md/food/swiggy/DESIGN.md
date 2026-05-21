# Design System Inspiration of Swiggy (iOS)

## 1. Visual Theme & Atmosphere

Swiggy's iOS app is **appetite-driven commerce**. The entire visual language exists to do one thing: make you hungry, then make ordering feel instantaneous. The canvas is white (`#FFFFFF`) in light mode and a near-black charcoal (`#121212`) in dark mode, but the canvas is almost never what you notice — what you notice is the *food*. Every screen is photo-forward: 16:10 restaurant hero images with a dark bottom scrim that holds an offer in bold white type ("60% OFF UPTO ₹120"), square dish thumbnails with a floating green "ADD" button, and a relentless rhythm of cards that pull the eye down the list.

The signature screen is the **restaurant list** (the "Swiggy" / Food home tab): a sticky location header ("Home ⌄" with the delivery address beneath it), an orange-iconed search pill ("Search 'biryani'"), a horizontally-scrolling filter chip row (Filter, Under 30 mins, Rating 4.0+, Pure Veg, Offers), and then the restaurant cards themselves. Each card is a complete decision unit: photo + offer overlay, restaurant name in heavy 18pt, a green rating chip (`#48C479` with the score and a star), delivery-time and distance metadata, cuisine tags, and a dashed-divider footer carrying a free-delivery or coupon line in Swiggy Orange. The second signature surface is the **restaurant menu / dish list** — vegetarian/non-veg marks (the green-dot-in-a-square, red-triangle-in-a-square), dish name, price, a green in-card rating, a description, and a square food image with the bottom-overlapping "ADD" button + customisation "›" hint.

The color system is built around a single, unmistakable hero: **Swiggy Orange `#FC8019`**. It is warm, energetic, slightly tangy — engineered to read as "fresh food, fast." It is the primary CTA, the active tab, the search/location icons, the footer accent, and the brand wordmark plate. Supporting it: the **rating green `#48C479`** (high ratings) and **rating amber `#DB7C38`** (lower ratings) for the trust signal Swiggy users scan first; **Instamart magenta-purple `#982C61`** for the grocery vertical; and **Dineout red `#D7385E`** for table-booking. Offers are rendered as gradient chips (orange→red) or bold scrim text. The palette is deliberately small and loud — Swiggy is not a calm app, it is a *crave-and-tap* app.

Typography is a clean grotesque. Swiggy's brand face is a proprietary/Proxima-Nova-lineage sans (the product historically uses ProximaNova and a Basis-Grotesque-style UI face); on iOS it falls back to SF Pro when a bundled face is unavailable. The hierarchy is heavy and confident: screen titles at 32pt/800, restaurant card names at 18pt/800, body at 16pt/400. Weight does the work — Swiggy leans on 700/800 to make food names and prices unmissable.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / near-black charcoal (`#121212`) dark — but the screen is carried by food photography, not chrome
- Single hero accent: **Swiggy Orange `#FC8019`** — CTA, active tab, icons, footer accent, wordmark
- Photo-forward restaurant cards — 16:10 hero image + dark bottom scrim + bold white offer text
- Green rating chip (`#48C479`) / amber (`#DB7C38`) — the trust signal users scan first, always pill-shaped with a star
- Sticky location header — "Home ⌄" + delivery address, with avatar at trailing edge
- Veg / non-veg marks — green-dot-in-square / red-triangle-in-square, mandatory on every dish (Indian FSSAI convention)
- Floating "ADD" button — green-outline, bottom-overlapping the square dish image, with a customisation "›"
- Dashed-divider card footers — free-delivery / coupon line in Swiggy Orange
- Filter chip row — horizontally scrollable, 8pt-radius rounded rectangles, active state filled
- Multi-vertical tab bar — Swiggy (food), Instamart (grocery), Dineout (table booking), Cart
- Offer chips — orange→red gradient or white-on-scrim bold type ("60% OFF UPTO ₹120")
- Heavy grotesque type — screen titles 32pt/800, food names 18pt/800; weight carries hierarchy

## 2. Color Palette & Roles

### Primary (Interactive)
- **Swiggy Orange** (`#FC8019`): The brand. Primary CTA background, active tab, search/location icons, footer accent links, wordmark plate, segmented-control selection.
- **Swiggy Orange Pressed** (`#E06D0C`): Pressed/active state for orange CTAs.
- **Swiggy Orange Tint** (`#FC80191A`): 10% orange wash behind selected chips, promo banners.

### Rating & Trust
- **Rating Green** (`#48C479`): Rating chips for scores ≥ 4.0 — the single most-scanned element on a card.
- **Rating Amber** (`#DB7C38`): Rating chips for scores 3.0–3.9.
- **Rating Red** (`#E84A4A`): Rating chips for scores < 3.0 (rare; also error).

### Vertical Accents
- **Instamart Purple** (`#982C61`): Instamart (grocery) tab, headers, CTAs within Instamart.
- **Dineout Red** (`#D7385E`): Dineout (table booking) tab and accents.
- **Offer Gradient** (`#FC8019` → `#D7385E`): Discount chips, hero offer banners.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F4F6`): Search pill fill, chip backgrounds, sectioned groups.
- **Surface Pressed** (`#ECECEE`): Pressed rows / chips.
- **Divider** (`#E2E2E7`): Hairline dividers; dashed variant on card footers.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — near-black, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Card backgrounds, search pill, sheets.
- **Dark Surface 2** (`#2A2A2A`): Elevated chips, "ADD" button background.
- **Dark Divider** (`#2C2C2E`): Hairline dividers; dashed variant on footers.

### Text
- **Text Primary Light** (`#02060C`): Primary text — near-black, slightly cool.
- **Text Secondary Light** (`#686B78`): Metadata, cuisine tags, distance.
- **Text Tertiary Light** (`#93959F`): Disabled, very-low-emphasis.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A0`): Metadata on dark.
- **Text Tertiary Dark** (`#6A6A6A`): Disabled on dark.

### Roles Summary

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#121212` |
| Surface 1 | `#F4F4F6` | `#1C1C1E` |
| Surface 2 | `#ECECEE` | `#2A2A2A` |
| Divider | `#E2E2E7` | `#2C2C2E` |
| Text Primary | `#02060C` | `#F2F2F2` |
| Text Secondary | `#686B78` | `#A0A0A0` |
| Brand / CTA | `#FC8019` | `#FC8019` |
| Rating (high) | `#48C479` | `#48C479` |
| Rating (mid) | `#DB7C38` | `#DB7C38` |
| Error | `#E84A4A` | `#E84A4A` |

### Semantic
- **Success Green** (`#48C479`): Order-placed, rating, "Free delivery" confirmation.
- **Error Red** (`#E84A4A`): Payment failure, item-unavailable, destructive delete.
- **Warning Amber** (`#DB7C38`): "Few items left", surge-fee notice.
- **Info Blue** (`#1A73E8`): Informational links inside checkout.
- **Veg Mark** (`#48C479`): Vegetarian indicator square + dot.
- **Non-Veg Mark** (`#E84A4A` / classic `#A1303A`): Non-veg indicator square + triangle.

## 3. Typography Rules

### Font Family
- **Brand / Display**: `ProximaNova` lineage — Swiggy's marketing & heavy headings face (geometric humanist sans).
- **UI / Text**: `Basis Grotesque`-style grotesque for body and metadata (neutral, highly legible at small sizes).
- **iOS Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Web/Catalog Fallback**: `Plus Jakarta Sans` (close geometric humanist match for documentation/preview)
- Numerals are tabular in price, rating, and delivery-time contexts so columns align.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | ProximaNova | 32pt | 800 | 1.2 | -0.4pt | "Restaurants", nav large-title |
| Hero | ProximaNova | 26pt | 800 | 1.25 | -0.3pt | "What's on your mind?" |
| Section Header | ProximaNova | 22pt | 700 | 1.3 | -0.2pt | "Top restaurant chains" |
| Card Title (Restaurant) | ProximaNova | 18pt | 800 | 1.3 | -0.3pt | Restaurant name on card |
| Subsection | ProximaNova | 17pt | 700 | 1.35 | -0.2pt | "Recommended", category headers |
| Body | Basis Grotesque | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| Dish Title | Basis Grotesque | 15pt | 600 | 1.4 | 0pt | Menu item name |
| Price | Basis Grotesque | 14pt | 600 | 1.4 | 0pt | "₹289" — tabular numerals |
| Meta | Basis Grotesque | 14pt | 400 | 1.4 | 0pt | Cuisine tags, distance, ETA |
| Rating Chip | Basis Grotesque | 12pt | 700 | 1.0 | 0pt | Score in green/amber pill |
| Offer / Badge | ProximaNova | 12pt | 800 | 1.1 | 0.3pt | "60% OFF UPTO ₹120" |
| Button (Primary) | ProximaNova | 15pt | 700 | 1.0 | 0.2pt | UPPERCASE — "CONTINUE" |
| Button (ADD) | ProximaNova | 13pt | 800 | 1.0 | 0.3pt | UPPERCASE green-outline |
| Caption | Basis Grotesque | 12pt | 400 | 1.4 | 0pt | Tiny print, T&C |
| Tab Label | Basis Grotesque | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Location Title | ProximaNova | 17pt | 800 | 1.2 | -0.2pt | "Home ⌄" header |

### Principles
- **Weight carries hierarchy**: Swiggy concentrates emphasis at 700/800. Restaurant names, prices, and offers are heavy so the eye locks onto them while scrolling fast.
- **UPPERCASE for action**: Primary CTAs and the "ADD" button are uppercase with +0.2–0.3pt tracking — it signals "commit."
- **Tabular numerals everywhere money/time appears**: prices, ETAs, ratings align in columns.
- **Display vs UI split**: ProximaNova-lineage for headings and actions; the grotesque for dense metadata and descriptions.
- **Dynamic Type**: titles, body, descriptions scale; rating chips, tab labels, veg marks, and offer badges stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Continue / Pay / Place Order)**
- Shape: Rounded rectangle, 12pt corner radius
- Background: `#FC8019` (Swiggy Orange)
- Text: `#FFFFFF`, ProximaNova 15pt weight 700, UPPERCASE, +0.2pt tracking
- Padding: 14pt vertical, 28pt horizontal (full-width in checkout/cart)
- Pressed: background `#E06D0C` + scale 0.98
- Disabled: background `#E2E2E7` / `#2A2A2A`, text `#93959F`

**Secondary / Outline Button (Repeat Last Order / Customise)**
- Background: transparent
- Border: 1.5pt `#FC8019`
- Text: `#FC8019`, ProximaNova 13pt weight 700, UPPERCASE
- Corner radius: 10pt
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#FC80191A`

**ADD Button (the signature dish CTA)**
- Background: `#FFFFFF` light / `#2A2A2A` dark
- Border: 1pt `#48C479`
- Text: `#48C479` (or `#1BA672` light), ProximaNova 13pt weight 800, UPPERCASE
- Corner radius: 10pt
- Padding: 7–9pt vertical, 22–26pt horizontal
- Shadow: `rgba(0,0,0,0.5) 0 4px 12px`
- Position: bottom-overlapping the dish image by -12pt, horizontally centered
- After tap: morphs into a `[ − qty + ]` stepper with the same border/colour

**Text Button (View Full Menu / Show More)**
- Text: `#FC8019`, ProximaNova 13pt weight 700, UPPERCASE
- No background, no border
- Pressed: opacity 0.6

**Stepper (Quantity)**
- Pill, 10pt radius, 1pt `#48C479` border
- `−` and `+` glyphs in green, quantity centered in green 800
- Min height 32pt; matches the ADD button footprint exactly

### Core Atoms

**Rating Chip**
- Pill, 6pt corner radius
- Background: `#48C479` (≥4.0) / `#DB7C38` (3.0–3.9) / `#E84A4A` (<3.0)
- Content: 10pt white star polygon + score in white, Basis Grotesque 12pt weight 700
- Padding: 2pt vertical, 6pt horizontal
- Always immediately left of the ETA / distance metadata

**Veg / Non-Veg Mark**
- 16pt square, 1.5pt border, 3pt corner radius
- Veg: green border `#48C479`, centered 7pt green filled dot
- Non-veg: red border `#E84A4A`, centered upward triangle
- Mandatory leading element on every dish row

**Offer Badge / Chip**
- On photo: white ProximaNova 16pt/800 over the scrim with an 11pt/600 sub ("UPTO ₹120")
- Standalone chip: gradient `#FC8019 → #D7385E`, 8pt radius, white 12pt/800 UPPERCASE
- Swiggy One badge: 1pt orange border, transparent, `#FC8019` 11pt/800

**Filter Chip**
- Rounded rectangle, 8pt corner radius
- Default: `#FFFFFF`/`#1C1C1E` fill, 1pt `#E2E2E7`/`#2C2C2E` border, text `#02060C`/`#F2F2F2` 12pt/600
- Active: `#ECECEE`/`#2A2A2A` fill, border `#686B78`, optional leading icon
- Height: 32pt; horizontally scrollable row, 8pt gap

**Card Footer (Free-Delivery Strip)**
- Separated from card body by a 1pt **dashed** divider (`#E2E2E7`/`#2C2C2E`)
- Leading 13pt clock/percent icon in `#FC8019`
- Text: `#FC8019`, 12pt weight 700 — "Free delivery on orders above ₹199"

### Navigation

**Sticky Location Header**
- Leading: 18pt orange map-pin `#FC8019`
- Title: "Home ⌄" — ProximaNova 17pt/800, chevron 14pt
- Sub-line: full delivery address, 12pt `#686B78`, indented to align under title
- Trailing: 34pt circular profile avatar
- Collapses into a compact bar on scroll; search pill can pin under it

**Search Pill**
- Height: 46pt
- Background: `#F4F4F6` / `#1C1C1E`
- Corner radius: 14pt
- Leading 18pt orange `magnifyingglass`; trailing 18pt orange `mic`
- Placeholder: rotating ("Search 'biryani'", "Search 'cake'") `#686B78`/`#A0A0A0` 14pt/500

**Bottom Tab Bar**
- Height: 70pt incl. safe area
- Background: `rgba(255,255,255,0.94)` / `rgba(18,18,18,0.94)` with blur, 0.5pt top divider
- Tabs (4): **Swiggy** (food/home), **Instamart** (grocery), **Dineout** (table booking), **Cart**
- Icon 22pt; active = filled glyph in `#FC8019`; inactive = stroke `#888`
- Labels always shown, 10pt weight 600
- Cart shows a count badge (orange dot, white number)

**Top Nav (Restaurant detail)**
- Transparent over the hero image, gradient-scrim backed
- Leading back chevron (white over image, `#02060C` once scrolled)
- Trailing: search, share, favourite (heart)
- Sticky category strip appears once the menu starts scrolling

### Input Fields

**Search Field (active)**
- Same 46pt pill; on focus, border becomes 1pt `#FC8019`, cursor orange
- Recent searches and trending dishes shown below as tappable rows

**Address / Form Field**
- Height: 52pt
- Background: `#F4F4F6` / `#1C1C1E`
- Corner radius: 12pt
- Label floats to 11pt `#686B78` on focus; text `#02060C`/`#F2F2F2` 15pt
- Focus: 1pt `#FC8019` border
- Error: 1pt `#E84A4A` border + 12pt red helper text

**Coupon Field**
- Inline within cart, dashed `#686B78` border, 8pt radius
- "APPLY" trailing text button in `#FC8019` 13pt/800 UPPERCASE

### Distinctive Components

**Photo-Forward Restaurant Card**
- Full-bleed 16:10 image, 16pt corner radius
- Bottom scrim: `linear-gradient(180deg, transparent 45%, rgba(0,0,0,0.78) 100%)`
- Offer text bottom-left in white ProximaNova 16pt/800 + 11pt/600 sub
- Top-left optional "AD" flag (`rgba(0,0,0,0.4)` pill, white 10pt/700)
- Top-right favourite heart in a 32pt `rgba(0,0,0,0.4)` circle
- Below image: name (18pt/800) → rating chip + " · " + ETA (14pt/600) → cuisine tags (14pt `#686B78`) → locality · distance → dashed footer

**Dish Row (Menu Item)**
- Leading veg/non-veg mark
- Name 15pt/700 → price 14pt/600 → in-card green rating "4.5 (1.2K)" → 2-line description 12pt `#686B78`
- Trailing 110pt square image with the bottom-overlapping **ADD** button / stepper
- "More Details ›" link if customisable

**Offer / Coupon Sheet**
- Bottom sheet, 20pt top corners
- Each coupon: dashed-bordered card, code in mono-ish bold, savings line in `#48C479`, "APPLY" orange text button

**Cart Bar (floating)**
- Pinned above the tab bar when items exist
- Orange `#FC8019` rounded bar: "N items | ₹XXX" left, "VIEW CART ›" right, white ProximaNova 14pt/800
- Slides up with spring when first item added

**Live Order Tracking**
- Map with the delivery partner's animated pin (orange)
- Stepped progress: Order Placed → Preparing → On the way → Delivered
- ETA in heavy ProximaNova; partner name + call button row

**Instamart Switch**
- Tapping Instamart tab re-themes accents to `#982C61` (purple) while keeping the same layout grammar — proves the system flexes per vertical

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Card vertical gap: 22pt (cards breathe; food needs air)
- Screen horizontal inset: 16pt
- Intra-card content gaps: 4–6pt (tight, scannable)
- Section header top margin: 16pt

### Grid & Container
- iPhone: single-column card list, 16pt insets, full-width photos
- Horizontal carousels (top chains, what's on your mind): 8–12pt gap, peek of next item
- iPad: 2-column restaurant grid, max content width 760pt centered, 24pt insets
- Dish image is a fixed 110×110pt square; ADD button overlaps by -12pt

### Whitespace Philosophy
- **Photos earn the space**: the largest element on any card is the food image — chrome is minimal so imagery dominates
- **22pt between cards**: deliberate breathing room so each restaurant is a discrete decision
- **Dashed dividers, not boxes**: footers and dish rows are separated by dashed hairlines, never heavy borders or nested cards
- **One loud accent**: only Swiggy Orange (and the rating green) carry colour weight; everything else is neutral so the eye goes to action + trust

### Border Radius Scale
- Square (0pt): full-bleed hero edges, dividers
- Subtle (6pt): rating chips, small badges
- Compact (8pt): filter chips, offer chips
- Standard (10–12pt): buttons, search pill, form fields, ADD button
- Card (16pt): restaurant photos, dish thumbnails
- Sheet (20pt): bottom sheets (offers, customise, address)
- Pill (500pt): qty steppers, toggle switches
- Circle (50%): avatars, favourite-heart background

## 6. Depth & Elevation

Swiggy is mostly flat; elevation is reserved for "this floats above the list and demands a tap."

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Restaurant cards, dish rows, chips on the page |
| Lifted (Level 1) | `rgba(0,0,0,0.12) 0 2px 8px` light / `rgba(0,0,0,0.5) 0 4px 12px` dark | The floating **ADD** button, qty stepper |
| Floating Bar (Level 2) | `rgba(252,128,25,0.35) 0 6px 16px` | The orange floating cart bar |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -4px 24px` | Bottom sheets (offers, customise, address picker) |
| Modal Overlay | `rgba(0,0,0,0.5)` | Dim behind sheets and dialogs |

**Shadow Philosophy**: Cards themselves are flat (a 1pt divider or the photo edge defines them). Shadow appears only on the ADD button, the floating cart bar, and bottom sheets — each is a "tap me / acknowledge me" surface. The ADD button's shadow is what makes it feel like it's *hovering over the food*, ready to be pressed. On dark mode, shadows deepen (0.5 opacity) and bottom sheets gain a 1pt `#2C2C2E` top border as a secondary elevation cue.

### Motion
- **ADD → Stepper morph**: ADD button cross-fades to `[ − 1 + ]` over 180ms ease-out; soft haptic on tap
- **Cart bar reveal**: slides up from above the tab bar, 280ms spring (damping 0.8) when the first item is added; the price counter rolls
- **Card tap → restaurant**: hero image shared-element zoom into the detail screen, 320ms ease-out
- **Filter chip select**: fill colour cross-fades 150ms; list re-sorts with a 200ms fade-through
- **Pull-to-refresh**: orange Swiggy spinner; haptic tick on release
- **Live tracking**: delivery pin animates along the route at a steady tween; step ticks advance with a soft pop
- **Bottom sheet**: slides up 300ms ease-out; dim fades in parallel; drag-to-dismiss with rubber-banding
- **Scrim/offer text**: appears with the image (no separate animation) — feels instantaneous
- **Haptics**: soft impact on ADD, medium on "Place Order" success, light tick on chip toggle and pull-to-refresh

## 7. Do's and Don'ts

### Do
- Use Swiggy Orange (`#FC8019`) as the single hero accent — CTA, active tab, icons, footer
- Lead every restaurant card with a large 16:10 photo + dark bottom scrim + bold white offer text
- Put a green/amber rating chip (`#48C479` / `#DB7C38`) immediately before the ETA — it's the first thing users scan
- Show veg/non-veg marks on every dish (green-dot-square / red-triangle-square)
- Make the dish "ADD" button green-outline, bottom-overlapping the square image, with a shadow so it floats
- Use uppercase + tracking on primary CTAs and ADD
- Separate card footers and dish rows with dashed hairlines, never nested boxes
- Float an orange cart bar above the tab bar when items exist
- Use tabular numerals for prices, ETAs, and ratings
- Re-theme accents per vertical (Instamart purple `#982C61`, Dineout red `#D7385E`) while keeping the same layout grammar
- Keep dark mode near-black (`#121212`) with full-saturation food photography

### Don't
- Don't introduce a second loud brand colour competing with Swiggy Orange
- Don't shrink food photography or crop it square on list cards — 16:10 hero is the standard
- Don't hide or restyle the rating chip — it must be the green/amber pill with a star, left of ETA
- Don't omit veg/non-veg marks on dishes — it's a hard requirement in this market
- Don't make the ADD button a plain text link — it's a green-outline, shadowed, overlapping control
- Don't box every section in heavy cards — Swiggy uses dashed dividers and whitespace
- Don't use pure black (`#000000`) for the dark canvas — `#121212` is correct
- Don't desaturate or dim food imagery in dark mode — UI chrome recedes, food does not
- Don't animate aggressively — motion is fast and functional (150–320ms), tied to the order flow
- Don't bury the cart — it floats persistently once non-empty
- Don't use sentence-case timid CTAs — Swiggy commits with uppercase action verbs

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; restaurant photo ~210pt tall; compact location header |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; location header respects it |
| iPhone 15/16 Pro Max | 430pt | Larger photos (~250pt), more peek on carousels |
| iPad (portrait) | 768pt | 2-column restaurant grid; content max 760pt centered |
| iPad (landscape) | 1024pt+ | 2–3 column grid; persistent cart panel on the right |

### Dynamic Type
- Screen titles, section headers, body, descriptions: full scale
- Rating chips, offer badges, tab labels, veg marks, qty steppers: FIXED (layout-critical)
- Prices scale one step then truncate gracefully

### Orientation
- iPhone: portrait-locked for the order flow (standard for the category)
- iPad: supports landscape with a 2–3 column grid + sticky cart panel

### Touch Targets
- ADD button / stepper: ≥ 32pt tall, 44pt hit area
- Filter chip: 32pt tall, full-chip tap
- Tab bar icon: 22pt glyph, 44pt+ hit
- Rating chip: not tappable (informational)
- Restaurant card: full-card tap target

### Safe Area Handling
- Top: location header + Dynamic Island respected; hero image bleeds under status bar with scrim
- Bottom: floating cart bar sits above the tab bar, both above the home indicator
- Keyboard: address/coupon forms scroll above keyboard; sticky CTA pins above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface (dark card): `#1C1C1E`
- Divider: `#E2E2E7` light / `#2C2C2E` dark (dashed on footers)
- Text primary: `#02060C` light / `#F2F2F2` dark
- Text secondary: `#686B78` light / `#A0A0A0` dark
- Brand / CTA: **Swiggy Orange `#FC8019`** (pressed `#E06D0C`)
- Rating green: `#48C479` · Rating amber: `#DB7C38`
- Instamart purple: `#982C61` · Dineout red: `#D7385E`
- Offer gradient: `#FC8019 → #D7385E`
- Veg mark: `#48C479` · Non-veg mark: `#E84A4A`

### Example Component Prompts
- "Build the Swiggy restaurant card in SwiftUI: full-bleed 16:10 image with 16pt corner radius. Overlay a bottom scrim `linear-gradient(transparent 45%, rgba(0,0,0,0.78))`. Bottom-left offer text in ProximaNova 16pt weight 800 white ('60% OFF') with an 11pt weight 600 sub ('UPTO ₹120'). Top-left 'AD' pill (`rgba(0,0,0,0.4)`, white 10pt/700). Top-right favourite heart in a 32pt `rgba(0,0,0,0.4)` circle. Below: restaurant name ProximaNova 18pt/800 `#F2F2F2`; a row with a green `#48C479` rating chip (white star + '4.4', 6pt radius, 12pt/700) then ' · ' then '30–35 mins' 14pt/600; cuisine tags 14pt `#A0A0A0`; locality · distance; then a 1pt dashed `#2C2C2E` divider and a footer line in `#FC8019` 12pt/700 with a leading clock icon."

- "Create the Swiggy dish row: leading 16pt veg mark (1.5pt `#48C479` square, centered 7pt green dot). Dish name 15pt/700 `#F2F2F2`; '₹289' 14pt/600; an inline green rating '4.5 (1.2K)' with a star; a 2-line description 12pt `#A0A0A0`. Trailing 110×110pt food image, 14pt radius, with a white/`#2A2A2A` 'ADD' button (1pt `#48C479` border, `#48C479` text, ProximaNova 13pt/800 UPPERCASE, 10pt radius, `rgba(0,0,0,0.5) 0 4px 12px` shadow) overlapping the image bottom by -12pt, horizontally centered. On tap, morph ADD into a `[ − 1 + ]` stepper with the same border."

- "Render the Swiggy sticky location header: leading 18pt orange `#FC8019` map-pin; 'Home' in ProximaNova 17pt/800 `#F2F2F2` with a 14pt chevron-down; sub-line full address 12pt `#A0A0A0` indented under the title; trailing 34pt gradient circular avatar (`#FC8019 → #D7385E`). Below it a 46pt search pill, `#1C1C1E` fill, 14pt radius, leading orange magnifyingglass + trailing orange mic, placeholder 'Search \"biryani\"' 14pt/500 `#A0A0A0`."

- "Build the Swiggy bottom tab bar: 4 tabs — Swiggy (house, food/home), Instamart (grid), Dineout (paper-plane), Cart (cart with count badge). 22pt icons; active = filled glyph in `#FC8019`; inactive = stroke `#888`; labels always shown 10pt/600. Background `rgba(18,18,18,0.94)` with blur and a 0.5pt `#2C2C2E` top divider."

- "Create the Swiggy floating cart bar: pinned above the tab bar when items exist. Orange `#FC8019` rounded bar (12pt radius), left 'N items | ₹XXX' and right 'VIEW CART ›' in white ProximaNova 14pt/800. Slides up with a 280ms spring (damping 0.8) when the first item is added; the price counter rolls."

- "Render the Swiggy filter chip row: horizontally scrollable, 8pt gap, 16pt insets. Each chip 32pt tall, 8pt corner radius. Default: `#1C1C1E` fill, 1pt `#2C2C2E` border, text `#F2F2F2` 12pt/600. Active: `#2A2A2A` fill, `#686B78` border, optional leading 12pt icon. First chip 'Filter' has a leading sliders icon."

### Iteration Guide
1. Swiggy Orange (`#FC8019`) is the only loud accent — CTA, active tab, icons, footer; pressed `#E06D0C`
2. Every restaurant card is photo-first: 16:10 image + dark bottom scrim + bold white offer text
3. The green/amber rating chip (`#48C479` / `#DB7C38`) sits left of the ETA — the most-scanned element
4. Veg/non-veg marks are mandatory on dishes (green-dot-square / red-triangle-square)
5. The dish ADD button is green-outline, shadowed, overlapping the square image bottom by -12pt; morphs to a stepper
6. Footers and dish rows use dashed hairlines, never nested boxes
7. A floating orange cart bar appears above the tab bar once items exist
8. Primary CTAs and ADD are UPPERCASE with +0.2–0.3pt tracking
9. Dark canvas is `#121212` (near-black); food photography stays full-saturation
10. Verticals re-theme the accent (Instamart `#982C61`, Dineout `#D7385E`) but keep the same card/list grammar
