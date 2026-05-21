# Design System Inspiration of Grubhub (iOS)

## 1. Visual Theme & Atmosphere

Grubhub's iOS app is an appetite-first food delivery marketplace. The canvas is pure white (`#FFFFFF`) and every screen is engineered to push you toward a decision: pick a restaurant, add a dish, track the bag. The hero of the experience is **restaurant photography** — wide 16:9 food and storefront images sit at the top of every restaurant card, full-bleed within a rounded container, never cropped to a square or hidden behind a tint. Around that photography the chrome is deliberately quiet: a thin "Deliver to" address row, a single search field, a scrollable strip of cuisine chips, then a vertical river of restaurant cards. The brand makes its presence felt through two saturated colors — **Grubhub Red** (`#F63440`) for the primary action and the brand mark, and **Grubhub Orange** (`#FF8000`) for deals, savings and value messaging — set against an otherwise neutral, almost editorial layout.

The two signature UI moments are the **restaurant list** and **live order tracking**. The restaurant list is the home screen: address pinned at top, search below, cuisine chips, then cards that each carry a photo, a Grubhub+ Perks badge, the restaurant name, a green rating chip, delivery time, fee, and an orange deal line. The moment you place an order the app flips into **order tracking** — a persistent banner and a full tracking screen with an animated progress bar, an ETA window ("Arriving 7:45 – 7:55 PM"), a step timeline (Order confirmed → Driver picking up → On the way → Delivered), a live map with the courier moving, and the driver's name. This tracking surface is the emotional payoff of the app and gets the most motion and the calmest, most reassuring blue accent (`#00A0DF`).

The membership layer — **Grubhub+** — is woven through the UI as a gold (`#FFB81C`) "Grubhub+ Perks" badge on eligible restaurant cards, a "$0 delivery fee" line in orange, and an upsell card. It is a value signal, not a chrome color: gold appears only on Perks badges and the membership pill. The color system is therefore strictly three-tone for brand (Red action, Orange savings, Gold membership) plus a quiet utility set (green rating, blue tracking, neutral text).

Typography is a clean grotesque. Grubhub's brand face is **Graphik**; in an unlicensed clone the closest free substitute is **Inter** (or Helvetica Now / system SF Pro). Hierarchy is bold and product-driven, not editorial: screen titles are heavy (weight 800–900), restaurant names are 16pt bold, metadata drops to a muted gray 14pt, and deal lines are 13pt bold orange. Numbers — prices, ETAs, ratings, fees — are everywhere and always get extra weight because the decision is economic.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / warm near-black (`#121212`) dark — content-forward, low chrome
- Wide 16:9 restaurant photography in 14pt rounded containers — the visual anchor of every card
- Two-tone brand: Grubhub Red (`#F63440`) for actions/brand, Grubhub Orange (`#FF8000`) for deals & savings
- Grubhub+ membership surfaced as a gold (`#FFB81C`) "Perks" badge + "$0 delivery fee" messaging
- "Deliver to" address row pinned top-left with a bag/cart button top-right (item count badge)
- Cuisine chip rail — horizontally scrolling pill filters, active chip filled Grubhub Red
- Restaurant card: photo + Perks badge + saved heart + name + green rating chip + time/fee + orange deal
- Live order tracking — persistent ETA banner + full screen with progress bar, step timeline, courier map
- Track Blue (`#00A0DF`) accent reserved for the order-tracking surface only
- Quantity stepper (− n +) with Grubhub Red circular controls on menu and bag
- Pill-shaped primary buttons (fully rounded, `999px`) — "Add to bag", "Checkout", "Get Grubhub+"
- Bottom tab bar: Delivery / Search / Orders / Saved / Account — active icon tinted Grubhub Red

## 2. Color Palette & Roles

### Primary (Interactive)
- **Grubhub Red** (`#F63440`): Primary interactive color — primary buttons, brand logomark, active tab, active filter chip, saved-heart fill, stepper controls.
- **Grubhub Red Pressed** (`#D2202B`): Pressed/active state on red buttons and controls.
- **Grubhub Orange** (`#FF8000`): Deals, savings, "$0 delivery fee", value messaging, secondary CTA ("Get Grubhub+").
- **Grubhub Orange Pressed** (`#E06E00`): Pressed state on orange buttons.

### Membership & Status
- **Perks Gold** (`#FFB81C`): Grubhub+ Perks badge fill, membership pill border/text — membership signal only.
- **Track Blue** (`#00A0DF`): Live order-tracking accent — progress bar, ETA dot, "on the way" status; used only on the tracking surface.
- **Rating Green** (`#18A957`): Star icon and rating chip on restaurant cards (Grubhub renders ratings in green, not gold).

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F6F6F4`): Search field, chip backgrounds, subtle cards, grouped sections.
- **Surface Pressed** (`#ECECEA`): Pressed rows and tappable cells.
- **Divider** (`#E6E6E6`): Hairline dividers between menu rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — warm near-black, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Cards, search field, elevated surfaces.
- **Dark Surface 2** (`#262626`): Chips, steppers, pressed cells.
- **Dark Divider** (`#2C2C2E`): Hairline dividers.

### Text
- **Text Primary Light** (`#1B1B1B`): Primary text — near-black, restaurant names, prices.
- **Text Secondary Light** (`#707070`): Metadata, cuisine, delivery time, fee.
- **Text Tertiary Light** (`#A0A0A0`): Disabled, low-emphasis captions.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A0`): Secondary text on dark (works on both modes).

### Semantic
- **Error Red** (`#F0454F`): Form errors, failed payment, removal confirmation (distinct from brand red).
- **Success Green** (`#18A957`): Order placed, item added confirmation, rating chip.
- **Warning Amber** (`#FF8000`): "Restaurant closes soon", surge messaging (shares the brand orange).
- **Info Blue** (`#00A0DF`): Order-tracking informational status.

### Color Usage Table

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#121212` |
| Surface 1 | `#F6F6F4` | `#1C1C1E` |
| Surface 2 | `#ECECEA` | `#262626` |
| Divider | `#E6E6E6` | `#2C2C2E` |
| Text Primary | `#1B1B1B` | `#F2F2F2` |
| Text Secondary | `#707070` | `#A0A0A0` |
| Primary Action | `#F63440` | `#F63440` |
| Deal / Savings | `#FF8000` | `#FF8000` |
| Membership (Perks) | `#FFB81C` | `#FFB81C` |
| Tracking Accent | `#00A0DF` | `#00A0DF` |
| Rating | `#18A957` | `#18A957` |
| Error | `#F0454F` | `#F0454F` |

## 3. Typography Rules

### Font Family
- **Brand Face**: `Graphik` (by Commercial Type) — the workhorse grotesque in Grubhub's brand system.
- **Recommended Substitute (unlicensed clone)**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist grotesque with near-identical proportions and excellent small-size hinting.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for prices, fees, ETAs and quantity steppers (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 900 | 1.15 | -0.5pt | "Delivery", large nav title |
| Restaurant Hero | Inter | 26pt | 800 | 1.2 | -0.3pt | Restaurant detail header name |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.2pt | "Order again", "Most popular" |
| Card / Subsection | Inter | 18pt | 700 | 1.3 | -0.1pt | Menu category, card title |
| Restaurant Name | Inter | 16pt | 700 | 1.3 | 0pt | Name on restaurant card |
| Body | Inter | 15pt | 500 | 1.45 | 0pt | Menu item description, paragraphs |
| Price / Number | Inter | 15pt | 700 | 1.3 | 0pt | Prices, fees, totals — tabular |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | Cuisine · time · fee, muted gray |
| Deal Line | Inter | 13pt | 600 | 1.35 | 0pt | "$0 delivery fee", orange |
| Overline / Badge | Inter | 12pt | 700 | 1.0 | 0.4pt | "GRUBHUB+ PERKS", uppercase |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary/secondary button label |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 500 | 1.35 | 0pt | Timestamps, footnotes |

### Principles
- **Numbers carry weight**: every price, fee, ETA, rating and quantity is bold (700+) and tabular — the decision is economic, so the figures lead.
- **Bold product hierarchy, not editorial**: titles are heavy (800–900); this is a commerce app, not a magazine — contrast is created by weight and color, not by serif elegance.
- **Color encodes meaning in type**: black = neutral, orange = savings/deal, green = rating, blue = tracking status, red = primary action.
- **Muted metadata**: cuisine, time and fee lines stay in `#707070` so the photo and name dominate the card.
- **Dynamic Type first-class**: screen titles, restaurant names, body and prices scale; badges, tab labels and chip text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Add to bag" / "Checkout" / "Place order")**
- Shape: Fully rounded pill, `999px` corner radius
- Background: `#F63440` (Grubhub Red) / same on dark
- Text: `#FFFFFF`, Inter 16pt weight 700
- Height: 52pt; padding 14pt vertical, 28pt horizontal; often full-width with 16pt side insets
- Pressed: background `#D2202B` + scale 0.98
- Disabled: `#F6342F` at 35% opacity, text 60%

**Secondary Button ("Get Grubhub+" / value CTA)**
- Background: `#FF8000` (Grubhub Orange)
- Text: `#1A1206` (near-black for contrast on orange), Inter 15pt weight 700
- Shape: pill `999px`; padding 13pt × 24pt
- Pressed: `#E06E00`

**Outline / Tertiary Button ("Schedule" / "Filters")**
- Background: transparent
- Border: 1.5pt `#E6E6E6` light / `#2C2C2E` dark
- Text: `#1B1B1B` light / `#F2F2F2` dark, Inter 14pt weight 600
- Shape: pill `999px`; padding 11pt × 20pt
- Pressed: background `#F6F6F4`

**Text Button ("View full menu" / inline link)**
- Text: `#F63440` (Grubhub Red), Inter 14pt weight 700
- No background, no underline
- Pressed: 60% opacity

**Button State Reference**

| State | Primary (Red) | Secondary (Orange) | Outline | Text |
|-------|---------------|--------------------|---------|------|
| Default | `#F63440` / white | `#FF8000` / `#1A1206` | transparent / `#1B1B1B` | `#F63440` |
| Pressed | `#D2202B` + scale 0.98 | `#E06E00` | bg `#F6F6F4` | 60% opacity |
| Disabled | `#F63440` @ 35% / text 60% | `#FF8000` @ 35% | border @ 40% | 40% opacity |
| Loading | spinner (white) on `#F63440` | spinner on `#FF8000` | spinner `#F63440` | — |
| Focus (a11y) | 2pt `#F63440` ring, 2pt offset | 2pt `#FF8000` ring | 2pt `#F63440` ring | underline on focus |

### Core Atoms

**Restaurant Card**
- Photo: full-width within card, aspect ratio 16:9, corner radius 14pt
- Perks badge: top-left, `#FFB81C` fill, `#1A1206` text, 6pt radius, 12pt uppercase 700 (or an orange "20% OFF" deal badge)
- Saved heart: top-right, 30pt circle, `rgba(0,0,0,0.45)` scrim, white outline icon; filled `#F63440` when saved
- Row 1: restaurant name (16pt 700, left) + rating chip (right): green star + number `#18A957`
- Row 2: meta line — "Cuisine · 20–30 min · $0 delivery fee" in `#707070` 14pt 400
- Row 3 (optional): orange deal line "Free delivery with Grubhub+" 13pt 700 `#FF8000`
- Card vertical gap: 22pt; no card border or shadow in light mode (photo + whitespace separate)

**Cuisine Chip (filter rail)**
- Height: 34pt; padding 0 14pt; corner radius `999px`
- Inactive: background `#F6F6F4` / `#262626` dark, text `#707070`, 0.5pt divider border
- Active: background `#F63440`, text `#FFFFFF`, border `#F63440`
- Horizontal scroll, 10pt gap, no scrollbar

**Quantity Stepper (− n +)**
- Container: pill `999px`, `#F6F6F4` / `#262626` dark, 0.5pt divider border, padding 6pt × 8pt
- Controls: 30pt circle, `#F63440` fill, white `−` / `+` glyph 18pt 700
- Count: 15pt weight 700, tabular, min-width 18pt centered
- Disabled `−` at quantity 1: control fades to 35% opacity

**Rating Chip**
- Green star (`#18A957`) 12pt + number 13pt weight 600 `#1B1B1B`
- No background; sits at the right edge of the card name row

**Perks / Membership Pill**
- Inline pill `999px`, background `rgba(255,184,28,0.14)`, 1pt border `#FFB81C`, text `#FFB81C` 12pt 800
- Leading ★ glyph; e.g. "★ GRUBHUB+ · $0 delivery"

### Navigation

**Top Address Bar**
- Height: ~44pt + safe area
- Leading: "Deliver to" label 11pt 600 `#707070` over address value 15pt 700 `#1B1B1B` with a 12pt chevron-down
- Trailing: bag button — 34pt circle `#262626`/surface, bag icon, red count badge top-right (16pt, `#F63440`, white 9pt 800)

**Search Field**
- Height: 44pt; corner radius 12pt; background `#F6F6F4` / `#1C1C1E` dark
- Leading magnifier 16pt `#707070`; placeholder "Search Grubhub" 14pt `#707070`
- Focus: 1.5pt `#F63440` border

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `rgba(18,18,18,0.94)` blurred dark, 0.5pt top divider
- Tabs (5): Delivery, Search, Orders, Saved, Account
- Icon size: 22pt; active color `#F63440` (icon fills); inactive `#888`
- Labels: Inter 10pt weight 600, always shown

**Restaurant Detail Top Nav**
- Transparent over the hero photo on scroll-to-top; collapses to a solid bar with the restaurant name (compact) once scrolled past the hero
- Leading back chevron; trailing search + share icons in white circular scrims while transparent

### Input Fields

**Text Field (address, promo code, instructions)**
- Height: 48pt; corner radius 10pt; background `#F6F6F4` / `#1C1C1E` dark
- Border: none default; focus 1.5pt `#F63440`
- Label: floating 12pt `#707070`; input 16pt `#1B1B1B`
- Error: 1.5pt `#F0454F` border + helper text `#F0454F` 12pt

**Search-with-results (cuisine / dish search)**
- Same field; results below as rows: leading thumbnail 44pt rounded 8pt, title 15pt 600, meta 13pt `#707070`
- Pressed row: `#ECECEA`

### Distinctive Components

**Live Order Tracking Banner (persistent)**
- Card across the top of Home while an order is active
- Background: subtle blue-tint card (light: `#E8F6FC`; dark: `linear-gradient(120deg,#11363F,#0E2A33)`), 16pt radius
- Row: pulsing `#00A0DF` dot + ETA "Arriving 7:45 – 7:55 PM" 13pt 700 + restaurant name (trailing, muted)
- Progress bar: 4pt rounded track, `#00A0DF` fill animating left→right
- Status line: 12pt 600 `#00A0DF` — "Your driver Marcus is picking up your order"
- Tap → full tracking screen

**Order Tracking Screen**
- Top: live map with the courier marker moving toward the destination pin (Grubhub Red pin)
- ETA headline: 26pt 800 "Arriving 7:45 – 7:55 PM"
- Step timeline (vertical rail): nodes 16pt, 3pt connector line
  - Completed step: node + line `#00A0DF`
  - Active step: node `#F63440` with 4pt `rgba(246,52,64,0.22)` glow
  - Pending step: hollow node, `#E6E6E6`/`#2C2C2E` line
  - Steps: "Order confirmed" → "Driver picking up" → "On the way" → "Delivered"
- Driver card: avatar + name + vehicle + call/message buttons (red circular)

**Grubhub+ Perks Badge**
- Top-left of restaurant photo, gold `#FFB81C`, near-black text, 6pt radius
- Variants: "GRUBHUB+ PERKS", "$0 DELIVERY FEE", "20% OFF, UP TO $5" (deal variant uses orange `#FF8000` text on light gold or solid orange)

**Bag / Cart Sheet**
- Bottom sheet, 16pt top radius; line items with quantity stepper, subtotal, fees, "Add Grubhub+ to save $X" orange upsell row
- Sticky "Go to checkout · $42.18" red pill button at bottom (price embedded in label, tabular)

**Deal / Savings Strip**
- Horizontal carousel of orange-accented offer cards near the top of search ("$0 delivery on first order", "Spend $25, save $5")
- Card: white surface, orange left accent, 12pt radius

**Item Customization Sheet**
- Opens from a menu row; bottom sheet, 16pt top radius, scrim behind
- Header: dish photo (full-width 16:9, 14pt radius) + name 22pt 800 + price 15pt 700 tabular
- Option groups: a section title (15pt 700) + a "Required" or "Optional · pick up to 2" subtitle in `#707070` 12pt
- Choice rows: radio (single-select) or checkbox (multi-select) leading control in Grubhub Red `#F63440` when chosen, label 15pt 500, price delta "+ $1.50" trailing tabular
- Special-instructions text field below; sticky "Add 1 to bag · $14.99" red pill at the bottom with the live total

**Order History / Reorder Row**
- On the Orders tab: each past order is a row with the restaurant thumbnail (44pt rounded 8pt), name 16pt 700, date + item summary 13pt `#707070`, and a trailing pill-outline "Reorder" button (1.5pt `#F63440` border, `#F63440` text 14pt 700)
- Tapping "Reorder" re-adds the exact prior bag and slides up the bag sheet

**Address / Deliver-To Sheet**
- Triggered from the top address row; bottom sheet listing saved addresses (Home / Work / recents) as rows with a leading pin glyph, label 15pt 700, full address 13pt `#707070`, and a trailing radio selected in `#F63440`
- "Add a new address" row at the bottom in `#F63440` 15pt 700; a map preview tops the sheet when adding

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 22, 24, 32, 40, 48, 64
- Restaurant card vertical gap: 22pt
- Screen horizontal inset: 16pt
- Section vertical rhythm: 24pt between titled sections

### Grid & Container
- iPhone: single-column card list at 16pt insets; photos full-width within insets
- iPad: 2-column restaurant grid at 24pt insets, max content width 900pt centered
- Cuisine chip rail and deal carousel: horizontal scroll, content edge-aligned to 16pt
- Menu screen: single-column item rows; sticky category nav under the hero

### Whitespace Philosophy
- **Photo-led breathing room**: 22pt between cards lets each restaurant photo register without borders
- **Quiet chrome, loud content**: address row, search and chips are low-contrast; the photo + name + price carry the screen
- **Numbers get air**: prices and ETAs are spaced and bold so the economic decision is instant
- **Tracking is calm**: the tracking surface uses generous spacing and a single blue accent — reassurance over density

### Border Radius Scale
- Square (0pt): full-bleed map, dividers
- Subtle (6pt): badges, deal pills, small thumbnails
- Standard (8pt): list thumbnails, inline chips
- Card (12–14pt): search field, restaurant photos (14pt), surface cards (12pt)
- Comfortable (16pt): bottom sheets, tracking banner, modals
- Pill (999px): buttons, cuisine chips, steppers, membership pill
- Circle (50%): avatars, bag button, stepper controls, map markers

## 6. Depth & Elevation

Grubhub is mostly flat — separation comes from photography and whitespace, with shadow reserved for floating commerce surfaces.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Restaurant cards, chips, list rows |
| Raised (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Sticky checkout bar, deal cards, floating "View bag" button |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -4px 24px` | Bag sheet, address sheet, filter sheet |
| Tracking Banner | `rgba(0,160,223,0.10) 0 4px 16px` | Live order-tracking banner (subtle blue-tinted lift) |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind sheets and modals |

**Shadow Philosophy**: shadows mark "this floats above the list and is actionable" — the sticky checkout pill, the bag sheet, the tracking banner. Restaurant cards never carry shadow; the photo is the figure and white space is the ground. On dark mode shadows fade, so floating surfaces add a 0.5pt divider border as the elevation cue.

### Motion
- **Add to bag**: bag-count badge bumps with a 200ms spring (scale 1 → 1.25 → 1) + soft haptic; the item flies-to-bag is optional
- **Quantity stepper**: number cross-fades + 120ms scale tick on each tap; soft haptic
- **Tracking progress bar**: fill width animates continuously with a 600ms ease-in-out toward the live percentage
- **Tracking dot pulse**: the blue ETA dot pulses opacity/scale on a 1.4s loop
- **Step timeline advance**: the active node slides up the rail with a 250ms ease-out + medium haptic when a stage completes
- **Card tap → detail**: shared-element photo expands into the restaurant header over a 320ms iOS push
- **Chip select**: filled background scales in 150ms ease-out
- **Sheet present**: bag/checkout sheets slide up 300ms ease-out; scrim fades in parallel
- **Pull-to-refresh**: Grubhub Red spinner on the restaurant list
- **Haptics**: light on chip select, soft on stepper, success notification on order placed, medium when a tracking stage completes

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas; warm near-black `#121212` for dark — never true black
- Lead every restaurant card with a wide 16:9 photo in a 14pt rounded container
- Use Grubhub Red (`#F63440`) for the primary action, brand mark, active tab and active chip
- Use Grubhub Orange (`#FF8000`) for deals, savings and "$0 delivery fee" messaging — value, not action
- Surface Grubhub+ as a gold (`#FFB81C`) "Perks" badge and membership pill — membership only
- Render ratings with a green star (`#18A957`), not gold — this is a Grubhub signature
- Reserve Track Blue (`#00A0DF`) exclusively for the order-tracking surface
- Make every price, fee, ETA and quantity bold and tabular
- Use fully-rounded pill buttons (`999px`) for all primary/secondary CTAs
- Keep a persistent tracking banner on Home whenever an order is active
- Embed the total in the checkout button label ("Go to checkout · $42.18")

### Don't
- Don't crop restaurant photos to squares or hide them behind heavy tints — wide and full-color
- Don't use Grubhub Red for deals/savings — that is Orange's job; don't use Orange for the primary CTA
- Don't render ratings in gold/yellow stars — Grubhub uses green
- Don't spread Perks Gold across the chrome — it appears only on Perks badges and the membership pill
- Don't use the tracking blue anywhere except the order-tracking experience
- Don't use sharp-cornered rectangular buttons — primary CTAs are pills
- Don't shadow restaurant cards in light mode — photo + whitespace create separation
- Don't bury the ETA — it is the emotional payoff; show it large and early in tracking
- Don't use thin weights for prices — economic figures are always 700+
- Don't make the address row loud — it is utility chrome, not a headline
- Don't animate aggressively — motion is functional (bag bump, progress fill, stage advance)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Photo aspect tightens slightly; chips scroll; single column |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above address bar |
| iPhone 15/16 Pro Max | 430pt | Larger photos; deal carousel shows ~1.3 cards |
| iPad (portrait) | 768pt | 2-column restaurant grid; address bar + search in a wider header |
| iPad (landscape) | 1024pt+ | 3-column grid; map + tracking timeline shown side-by-side |

### Dynamic Type
- Screen title, restaurant name, body, prices: full scale
- Perks badge, deal line, tab labels, chip text, ETA dot label: FIXED (layout-sensitive)
- Menu item descriptions: scale to XL

### Orientation
- iPhone: portrait-locked on most flows; tracking map may rotate
- iPad: full rotation; tracking screen shows map + timeline in a split layout in landscape

### Touch Targets
- Restaurant card: full-card tap, ≥ 88pt tall
- Stepper controls: 30pt glyph in 44pt hit area
- Cuisine chip: 34pt tall, ≥ 44pt hit
- Tab bar icon: 22pt glyph, 44pt hit
- Bag button: 34pt circle, 44pt hit
- Primary buttons: 52pt tall

### Safe Area Handling
- Top: address bar respects safe area and Dynamic Island
- Bottom: tab bar and sticky checkout pill respect the home indicator
- Tracking map: extends under the status bar with a gradient scrim for legibility
- Sides: 16pt content inset throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface (light): `#F6F6F4`
- Divider (light): `#E6E6E6`
- Text primary: `#1B1B1B` light / `#F2F2F2` dark
- Text secondary: `#707070` light / `#A0A0A0` dark
- Grubhub Red (primary action): `#F63440`
- Red pressed: `#D2202B`
- Grubhub Orange (deals/savings): `#FF8000`
- Perks Gold (membership): `#FFB81C`
- Track Blue (order tracking): `#00A0DF`
- Rating Green: `#18A957`
- Error Red: `#F0454F`

### Example Component Prompts
- "Build a Grubhub restaurant card in SwiftUI: full-width 16:9 photo with 14pt corner radius. Top-left a gold `#FFB81C` 'GRUBHUB+ PERKS' badge (Inter 12pt 700 uppercase, `#1A1206` text, 6pt radius); top-right a 30pt circular `rgba(0,0,0,0.45)` heart button (white outline, fills `#F63440` when saved). Below photo: row with restaurant name (Inter 16pt 700 `#1B1B1B`, leading) and a rating chip (green `#18A957` star + number Inter 13pt 600, trailing). Next line: 'American · 20–30 min · $0 delivery fee' Inter 14pt 400 `#707070`. Optional next line: 'Free delivery with Grubhub+' Inter 13pt 700 `#FF8000`. 22pt bottom margin, no card border or shadow."

- "Create the Grubhub live order-tracking banner: full-width card, 16pt radius, light `#E8F6FC` background (dark: gradient `#11363F → #0E2A33`). Row: an 8pt `#00A0DF` dot with a pulsing 4pt glow, then 'Arriving 7:45 – 7:55 PM' Inter 13pt 700, then the restaurant name trailing in `#707070` 12pt. Below: a 4pt rounded progress track (`rgba(255,255,255,0.12)`) with a `#00A0DF` fill animating to ~62%. Status line: 'Your driver Marcus is picking up your order' Inter 12pt 600 `#00A0DF'. Tap opens the full tracking screen."

- "Build the Grubhub order-tracking step timeline: a vertical rail of 4 steps (Order confirmed, Driver picking up, On the way, Delivered). Each step: 16pt node + 3pt connector line + a text block (title Inter 14pt 700, subtitle 12pt `#707070`). Completed steps: node and line `#00A0DF`. The active step: node `#F63440` with a 4pt `rgba(246,52,64,0.22)` glow ring. Pending steps: hollow node with `#E6E6E6` border and `#E6E6E6` line. When a stage completes, slide the active node up the rail over 250ms ease-out with a medium haptic."

- "Create a Grubhub quantity stepper: pill container `999px`, `#F6F6F4` background, 0.5pt `#E6E6E6` border, 6pt × 8pt padding. Left and right controls are 30pt circles filled `#F63440` with white `−` / `+` glyphs (18pt 700). Center count Inter 15pt 700 tabular, 18pt min-width. At quantity 1 the `−` control fades to 35% opacity. Each tap cross-fades the number with a 120ms scale tick and a soft haptic."

- "Build the Grubhub primary button: full-width pill (`999px`), 52pt tall, background `#F63440`, label Inter 16pt 700 `#FFFFFF`. For the checkout variant embed the total: 'Go to checkout · $42.18' with the price tabular. Pressed: background `#D2202B` + scale 0.98. Secondary 'Get Grubhub+' variant: background `#FF8000`, text `#1A1206`."

- "Render the Grubhub bottom tab bar: 5 tabs — Delivery, Search, Orders, Saved, Account. 22pt icons, labels Inter 10pt 600 always shown. Active tab tinted `#F63440` with filled icon; inactive `#888`. Background `#FFFFFF` light / `rgba(18,18,18,0.94)` blurred dark with a 0.5pt top divider."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) light, warm near-black `#121212` dark — never true black
2. Restaurant photos are wide 16:9 in 14pt rounded containers — full-color, never tinted or squared
3. Grubhub Red `#F63440` = primary action/brand; Grubhub Orange `#FF8000` = deals/savings — never swap them
4. Grubhub+ shows as a gold `#FFB81C` Perks badge + membership pill only — not a chrome color
5. Ratings use a GREEN star `#18A957` — not gold; this is a Grubhub signature
6. Track Blue `#00A0DF` is exclusive to the order-tracking surface (banner, progress, timeline)
7. All primary/secondary buttons are fully-rounded pills (`999px`)
8. Every price, fee, ETA and quantity is bold (700+) and tabular
9. A persistent tracking banner sits on Home whenever an order is active; the ETA is shown large and early
10. Cards have no border/shadow in light mode — photo + 22pt whitespace create the separation
