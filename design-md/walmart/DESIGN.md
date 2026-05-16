# Design System Inspiration of Walmart (iOS)

## 1. Visual Theme & Atmosphere

Walmart's iOS app is an unapologetically bright, white-canvas retail environment built for speed and trust. The interface sits on pure white (`#FFFFFF`) with the faintest cool-blue tinted surfaces (`#F2F8FD`) to separate cards from the page. Nothing is dim, nothing is moody — this is a store you can shop at 6am in a parking lot, so contrast is high, type is sturdy, and the famous Walmart Blue (`#0071DC`) anchors every primary action. The atmosphere is "everyday low price made digital": dense product grids, big tap targets, and a relentless focus on getting you from search to cart.

The accent system is a two-color signature. Walmart Blue (`#0071DC`) carries every verb — "Add to cart", links, the active tab, the filled checkbox — while Spark Yellow (`#FFC220`) is reserved for two ritual moments: the six-ray Spark logo and the savings-callout chrome (Rollback tags, "Save" badges, the yellow CTA on deal modules). Yellow never carries body text or large fills; it punctuates a price. The discipline is what makes the yellow read instantly as *money saved*.

Typography is Bogle, Walmart's proprietary humanist sans (Inter is the closest free substitute). It's friendly but no-nonsense — open apertures, sturdy stems, comfortable at small sizes because price-per-unit and fine print live everywhere in a grocery app. The hierarchy is compact and weight-driven: 12–28pt concentrated at 400 / 600 / 700. The most expressive type moment is a product price: the dollar amount in 22–28pt weight 700 next to a struck-through "was" price in gray and a Rollback tag in Walmart Blue on Spark Yellow.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) with cool-tinted surface (`#F2F8FD`) for cards
- Walmart Blue (`#0071DC`) as the single action color — buttons, links, active tab
- Spark Yellow (`#FFC220`) reserved for the Spark logo and savings chrome only
- The six-ray Spark mark — Walmart's instantly recognizable sunburst logo
- Rollback price tag — the distinctive blue-on-yellow savings callout
- Pickup / Delivery toggle pill at the top of Shop — fulfillment is a first-class choice
- Bogle font (proprietary, humanist sans), weights 400 / 600 / 700
- Bottom tab bar: Shop, Services, Search, Cart, Account — Search is center
- Haptic "bump" on add-to-cart, confirming the item dropped in

## 2. Color Palette & Roles

### Primary
- **Walmart Blue** (`#0071DC`): Primary CTA ("Add to cart", "Continue"), links, active tab, selected toggle, filled checkbox/radio.
- **Blue Pressed** (`#004F9A`): Active/pressed state for blue CTAs and the pressed link state.
- **Blue Tint** (`#E6F1FC`): Selected-row wash, info banners, blue chip background.
- **Spark Yellow** (`#FFC220`): The six-ray Spark logo, Rollback tag fill, "Save" badge, deal-module CTA.
- **Spark Yellow Pressed** (`#E5A800`): Pressed state for the yellow deal CTA.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary page background, card fill on white-on-white layouts.
- **Surface Tint** (`#F2F8FD`): Cool-blue tinted surface — section backgrounds, card wells, the area behind product grids.
- **Surface Gray** (`#F5F5F5`): Neutral input fill, skeleton placeholders, image-tile backing.
- **Divider** (`#E2E8F0`): Hairline rules between list rows and around cards.
- **Border Strong** (`#C9D2DE`): Input outline at rest, segmented-control border.
- **Text Primary** (`#2E2F32`): Product titles, prices, headings, primary UI text (a near-black charcoal, not pure black).
- **Text Secondary** (`#74767C`): Unit price, "was" struck price, metadata, helper text.
- **Text Tertiary** (`#9BA0A8`): Disabled labels, placeholder text, very low-emphasis captions.

### Semantic
- **Success Green** (`#1A7F37`): "In stock", order-confirmed, free-shipping eligibility.
- **Savings Green** (`#2A8703`): "You save $X" inline savings text on price blocks.
- **Warning Amber** (`#B25E00`): "Only 3 left", low-stock and time-limited warnings.
- **Error Red** (`#D03A2D`): Form errors, payment failure, out-of-stock state.
- **Star Gold** (`#FFB81C`): Product rating stars (a touch warmer than Spark Yellow).

### Dark Mode (Limited Use)
Walmart's iOS app is a light-first experience optimized for in-aisle and outdoor readability. A dark variant exists for system-wide dark mode but the canonical experience is the bright white store.
- **Dark Canvas** (`#0E0F11`)
- **Dark Surface** (`#1A1C1F`)
- **Dark Text** (`#F4F5F7`)
- **Blue (dark-adjusted)** (`#4D9BEA`) — lifted for contrast on dark

## 3. Typography Rules

### Font Family
- **Primary**: `Bogle` (proprietary Walmart humanist sans, Regular / Medium / Semibold / Bold)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures used in price blocks and quantity steppers so columns align
- **CJK/Non-Latin**: Falls to system stack — Walmart ships in the US/CA/MX with Spanish localization

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Bogle | 28pt | 700 | 1.2 | -0.3pt | "Cart", "Account" large nav title |
| Section Header | Bogle | 22pt | 700 | 1.25 | -0.2pt | "Deals", "Buy it again" shelf headers |
| Price (Large) | Bogle | 22pt | 700 | 1.1 | -0.2pt | Primary product price on PDP / card |
| Subsection | Bogle | 18pt | 600 | 1.3 | -0.1pt | "Pickup at Supercenter", group titles |
| Product Title | Bogle | 16pt | 600 | 1.3 | 0pt | Item name on card, 2-line clamp |
| Body | Bogle | 15pt | 400 | 1.45 | 0pt | Descriptions, item specs |
| Button (Primary) | Bogle | 16pt | 600 | 1.0 | 0pt | "Add to cart", "Continue" pill |
| Price (Inline) | Bogle | 15pt | 700 | 1.2 | 0pt | Price on dense list rows |
| Was / Struck Price | Bogle | 13pt | 400 | 1.2 | 0pt | Strikethrough original price, `#74767C` |
| Meta / Unit Price | Bogle | 13pt | 400 | 1.3 | 0pt | "$0.21/oz", "Sold by Walmart" |
| Tag / Badge | Bogle | 12pt | 700 | 1.0 | 0.2pt | "Rollback", "Best seller" |
| Tab Label | Bogle | 11pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Caption | Bogle | 11pt | 400 | 1.3 | 0pt | Legal, "Prices may vary" |

### Principles
- **Weight does the work**: Hierarchy is built on 400 → 600 → 700, not on dramatic size jumps — a grocery app stays dense
- **Price is the loudest element**: The dollar figure always outweighs the product name in visual weight
- **Tabular numerals everywhere price or quantity appears**: Columns of prices must align vertically
- **Charcoal, not black**: Primary text is `#2E2F32` — softer than pure black, easier on a bright white field
- **Yellow is never type**: Spark Yellow appears only as a fill behind dark text (Rollback), never as text color
- **Dynamic Type respected** on titles, body, prices; tab labels and badges hold fixed sizes

## 4. Component Stylings

### Buttons

**Primary CTA ("Add to cart" / "Continue")**
- Background: `#0071DC`
- Text: `#FFFFFF`, Bogle 16pt weight 600
- Height: 48pt, full-width on PDP / 40pt inline on cards
- Corner radius: 24pt (full pill at 48pt height)
- Pressed: background `#004F9A`, scale 0.98, haptic "bump"
- Disabled: background `#C9D2DE`, text `#FFFFFF`

**Secondary Button ("Save for later" / outline)**
- Background: transparent
- Text: `#0071DC`, Bogle 16pt weight 600
- Border: 1.5pt solid `#0071DC`
- Height: 48pt, corner radius 24pt
- Pressed: background `#E6F1FC`

**Deal / Savings CTA (Spark Yellow)**
- Background: `#FFC220`
- Text: `#2E2F32` (dark charcoal — never white on yellow)
- Height: 44pt, corner radius 22pt
- Used for: "Shop deals", "Get the deal" inside savings modules only
- Pressed: background `#E5A800`

**Quantity Stepper**
- Pill container, 1pt border `#C9D2DE`, height 36pt, radius 18pt
- Minus / plus glyphs 18pt in `#0071DC`, count center in `#2E2F32` weight 600 (tabular)
- At quantity 1, the minus becomes a trash icon

**Icon Button (heart / share / list)**
- Size: 24pt glyph, 44pt hit area
- Default: `#74767C`; Active (saved to list): `#0071DC`
- Heart filled when item is in a list

**Text Link**
- Bogle 15pt weight 600, color `#0071DC`, no underline
- Pressed: `#004F9A`

### Cards & Containers

**Product Card (Grid / Shelf)**
- Width: ~170pt in a 2-up grid (12pt gutter), shelf tiles 150pt
- Background: `#FFFFFF`, 1pt border `#E2E8F0`, corner radius 12pt
- Structure: square image (`#F5F5F5` backing) → Rollback tag (if any) → price block → title (16pt w600, 2-line clamp) → rating stars → "Add to cart" pill
- Pressed: border `#C9D2DE`, subtle shadow lift
- Tap image: opens PDP; tap pill: adds to cart with bump haptic

**Price Block**
- Current price `$24.97` in 22pt weight 700 `#2E2F32` (tabular)
- "was $34.97" struck, 13pt `#74767C` to the right or below
- Savings line "Save $10.00" 13pt weight 600 `#2A8703`
- Rollback tag sits directly above the price

**Cart Line Item**
- Height: dynamic ~96pt
- 64×64pt product thumbnail (8pt radius) → title (2-line) + seller + price → trailing quantity stepper
- Divider `#E2E8F0` between items
- "Save for later" and "Remove" as text links below the row

**Category Tile (Shop landing)**
- 2-up or 3-up grid of soft tiles, background `#F2F8FD`, radius 12pt
- Icon/illustration top, label 15pt weight 600 `#2E2F32` below
- Pressed: background `#E6F1FC`

**Order / Tracking Card**
- White card, 1pt `#E2E8F0`, radius 12pt
- Status pill top ("Out for delivery" — `#1A7F37` on tint), progress track in `#0071DC`

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt top border `#E2E8F0` (opaque — no blur, readability first)
- Tabs: Shop, Services, **Search** (center), Cart, Account
- Icon size: 24pt; Cart shows a `#0071DC` count badge
- Active: icon + label `#0071DC` (filled icon variant)
- Inactive: `#74767C` (outlined icon variant)
- Labels: Bogle 11pt weight 600, always shown

**Top App Bar (Shop / Home)**
- Height: 56pt + safe area, background `#FFFFFF`
- Left: Spark logo (28pt). Center: location/fulfillment chip ("Sacramento Supercenter ▾"). Right: list/notification icons
- Search bar pinned directly below, full-width

**Pickup / Delivery Toggle Pill**
- Segmented pill at top of Shop, container `#F2F8FD`, height 40pt, radius 20pt
- Two segments: "Pickup" / "Delivery" (sometimes "Shipping")
- Selected segment: white pill with shadow + text `#0071DC` weight 600
- Unselected: text `#74767C`
- Sliding-thumb transition on switch (220ms ease)

**Search Header (results)**
- Sticky filter / sort row below search field
- Filter chips: pill, `#F2F8FD` default, `#E6F1FC` + 1pt `#0071DC` when active

### Input Fields

**Search Bar**
- Background: `#F5F5F5`, height 44pt, corner radius 22pt
- Leading SF Symbol `magnifyingglass` 18pt `#74767C`
- Placeholder "Search Walmart" 16pt `#74767C`
- Focus: background `#FFFFFF`, 1.5pt border `#0071DC`
- Trailing: barcode-scan icon `#0071DC`

**Text Field (checkout / address)**
- Floating-label style, height 56pt, background `#FFFFFF`
- Rest border: 1pt `#C9D2DE`, radius 8pt
- Focus: 1.5pt `#0071DC`, label floats up and tints `#0071DC`
- Error: 1.5pt `#D03A2D`, helper text `#D03A2D` below

**Checkbox / Radio**
- 22pt box, 2pt border `#74767C` unselected
- Selected: fill `#0071DC`, white check; radius 4pt (checkbox) / circle (radio)

### Distinctive Components

**Rollback Price Tag**
- The signature savings callout. Pill or flag shape, background `#FFC220` (Spark Yellow)
- Text "Rollback" in `#2E2F32` (dark), Bogle 12pt weight 700, slight uppercase tracking
- Sits flush above the price block on cards and PDP
- Variant: "Clearance" uses `#D03A2D` fill with white text; "Best seller" uses `#0071DC` on `#E6F1FC`

**Spark Logo Mark**
- Six radiating tapered rays around a small hub, color `#FFC220`
- Appears top-left of the app bar and as the launch/loading mark
- Rays animate a brief 200ms staggered fan-in on cold launch

**Category Grid (Shop landing)**
- A dense, scannable grid of tinted category tiles ("Grocery", "Electronics", "Home", "Pharmacy"…)
- 3 columns, soft `#F2F8FD` tiles, illustrative icons — the visual table of contents of the store

**Add-to-Cart Bump**
- On tap, the "Add to cart" pill briefly scales 1.0 → 0.96 → 1.04 → 1.0 (260ms) while the cart tab badge increments with a small pop and a soft haptic — the confirming "it dropped in" gesture

**Fulfillment ETA Strip**
- A slim strip under the search bar showing "Pickup today, 4pm" / "Delivery as soon as 2pm" with a `#0071DC` clock icon — fulfillment promise is always visible

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Standard screen margin: 16pt horizontal
- Card grid gutter: 12pt; section vertical gap: 24pt
- Dense list rows: 12pt internal vertical padding

### Grid & Container
- Content width: full device width, 16pt side margins
- Product grid: 2-column on phones (12pt gutter), 3–4 on iPad
- Horizontal shelves: 16pt left inset, last tile peeks at the right edge to signal scroll

### Whitespace Philosophy
- **Dense but breathable**: It's a retail catalog — grids are tight, but each card has 12pt internal padding so prices never crowd
- **Tinted wells group content**: `#F2F8FD` surfaces visually batch a shelf or module without hard borders
- **Price gets air**: The price block always has clear space above (Rollback tag) and below (Add to cart) so it never collides with the title

### Border Radius Scale
- Square (0pt): Full-bleed hero banners, product imagery edges
- Soft (8pt): Text inputs, small thumbnails, status pills
- Standard (12pt): Product cards, category tiles, modules
- Comfortable (16pt): Bottom sheets, modals
- Pill (22–24pt): CTAs, search bar, toggle, filter chips
- Circle (50%): Avatars, icon-button hit circles, radio

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt `#E2E8F0` border | List rows, tinted wells, the canvas |
| Card (Level 1) | `rgba(16,24,40,0.06) 0 1px 3px` | Product cards, category tiles at rest |
| Raised (Level 2) | `rgba(16,24,40,0.10) 0 4px 12px` | Pressed card, selected toggle thumb, sticky search |
| Sheet (Level 3) | `rgba(16,24,40,0.16) 0 -8px 32px` | Bottom sheets (fulfillment picker, filters) |
| Sticky Bar | `rgba(16,24,40,0.08) 0 -2px 8px` | Sticky "Add to cart" bar above tab bar on PDP |
| Modal Scrim | `rgba(16,24,40,0.45)` | Dim behind modals and sheets |

**Shadow Philosophy**: Shadows are soft, cool-toned, and restrained — on a bright white canvas a hairline border does most of the separation work, and shadow only appears to signal *interaction* (press, sticky, float). The system never uses heavy or dark drop shadows; elevation is communicated with a 1px border first, a faint 6%-opacity shadow second.

### Motion
- **Add-to-cart bump**: pill scale 1.0 → 0.96 → 1.04 → 1.0 over 260ms, cart badge pop + soft haptic
- **Pickup/Delivery toggle**: thumb slides 220ms ease-in-out, label colors crossfade
- **Card press**: scale 0.98 + border darken, 150ms
- **Price update (quantity change)**: number rolls vertically 200ms when subtotal changes
- **Spark launch**: six rays stagger-fan in over 200ms on cold start
- **Pull-to-refresh**: standard spinner, no custom flourish

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas and `#F2F8FD` for grouped surfaces — keep it bright
- Reserve Walmart Blue (`#0071DC`) for actions, links, and the active tab — it is the verb
- Reserve Spark Yellow (`#FFC220`) for the Spark logo and savings chrome (Rollback) only
- Put dark charcoal text (`#2E2F32`) on Spark Yellow — never white on yellow (contrast)
- Make the price the heaviest element on a card — 22pt weight 700
- Use tabular numerals for all prices and quantity steppers
- Keep the Pickup/Delivery toggle at the top of Shop — fulfillment is a primary choice
- Give the add-to-cart action a confirming bump + haptic
- Use 1pt `#E2E8F0` borders for separation before reaching for shadow

### Don't
- Don't use a dark or moody canvas — Walmart is a bright, high-contrast store
- Don't use Spark Yellow for body text, large fills, or as a second action color
- Don't put white text on Spark Yellow — it fails contrast and breaks the brand
- Don't let the product title outweigh the price visually
- Don't use heavy drop shadows — separation is border-first, shadow-second and subtle
- Don't use pure black `#000000` for text — charcoal `#2E2F32` is the brand tone
- Don't hide the fulfillment toggle or ETA — the pickup/delivery promise is core
- Don't round product imagery corners independently of the card (image fills to the 12pt card radius)
- Don't animate aggressively — motion is short (150–260ms) and confirmation-focused

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-col grid, ~165pt cards, tighter shelf tiles |
| iPhone 13/14/15 | 390pt | Standard 2-col grid, ~170pt cards |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the app bar |
| iPhone 15/16 Pro Max | 430pt | Larger cards (~190pt), more shelf peek |
| iPad | 768pt+ | 3–4 col grid, two-pane PDP, persistent filter rail |

### Dynamic Type
- Product titles, prices, body: full scale
- Tab labels, Rollback/badge tags: fixed (layout-sensitive)
- Price block scales but caps to keep grid alignment
- Quantity stepper digits: fixed tabular size

### Orientation
- Shop / Search / Cart / Account: **portrait-locked** on iPhone
- iPad: supports landscape with multi-column grid and split PDP
- Barcode scanner: **portrait-locked**, full-screen camera

### Touch Targets
- Primary "Add to cart": 40–48pt height, full pill — easy in-aisle tap
- Quantity stepper buttons: 36pt with 44pt effective hit area
- Tab bar icons: 24pt glyph, 44pt effective hit
- Card image and CTA are independently tappable (browse vs. add)

### Safe Area Handling
- Top: app bar + search respect the safe area / Dynamic Island
- Bottom: sticky "Add to cart" bar on PDP floats above the tab bar; both clear the home indicator
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface tint (wells): `#F2F8FD`
- Surface gray (inputs): `#F5F5F5`
- Divider: `#E2E8F0`
- Border strong: `#C9D2DE`
- Text primary: `#2E2F32`
- Text secondary: `#74767C`
- Walmart Blue (action): `#0071DC`
- Blue pressed: `#004F9A`
- Blue tint: `#E6F1FC`
- Spark Yellow (logo / savings): `#FFC220`
- Savings green: `#2A8703`
- Star gold: `#FFB81C`
- Error red: `#D03A2D`

### Example Component Prompts
- "Create a SwiftUI Walmart product card: 170pt wide, white background with 1pt #E2E8F0 border and 12pt corner radius. Square image on a #F5F5F5 backing, a 'Rollback' tag (Spark Yellow #FFC220 fill, #2E2F32 text, Inter 12pt weight 700) above the price. Price '$24.97' in Inter 22pt weight 700 #2E2F32 with 'was $34.97' struck in 13pt #74767C beside it. Title 'Great Value Whole Milk, 1 Gallon' in 16pt weight 600 #2E2F32, 2-line clamp. Below: 5 rating stars in #FFB81C, then a full-width 40pt 'Add to cart' pill, #0071DC background, white 16pt weight 600 text, 20pt radius."
- "Build the Walmart Add to cart button: 48pt tall full-width pill (24pt radius), #0071DC background, 'Add to cart' in Inter 16pt weight 600 white. Pressed: background #004F9A, scale 0.98, soft haptic 'bump', and increment a cart badge with a small pop."
- "Design the Walmart Pickup/Delivery toggle: segmented pill at the top of Shop, container #F2F8FD, height 40pt, 20pt radius. Two segments 'Pickup' and 'Delivery'. Selected segment is a white pill with a soft shadow and #0071DC weight 600 text; unselected is #74767C. Sliding-thumb transition 220ms ease."
- "Create the Walmart bottom tab bar: white background, 0.5pt #E2E8F0 top border, 56pt + safe area. Five tabs: Shop, Services, Search (center), Cart, Account. Active icon+label #0071DC with filled icon; inactive #74767C outlined. Cart tab shows a #0071DC circular count badge. Labels Inter 11pt weight 600."
- "Build a Walmart price block: 'Rollback' tag (Spark Yellow #FFC220, dark #2E2F32 text) on its own line, then price '$12.48' in 22pt weight 700 #2E2F32 with tabular numerals, 'was $18.96' struck in 13pt #74767C, and 'Save $6.48' in 13pt weight 600 #2A8703 below."

### Iteration Guide
1. Canvas is bright `#FFFFFF`; group content on cool-tint `#F2F8FD` wells — never go dark
2. Walmart Blue `#0071DC` is the only action color (CTA, link, active tab); Spark Yellow `#FFC220` is logo + savings chrome only
3. Dark charcoal text on yellow — `#2E2F32`, never white on `#FFC220`
4. The price is the loudest element on every card — 22pt weight 700, tabular numerals
5. The Rollback tag (blue/dark text on Spark Yellow) is the signature savings device — sits above the price
6. Keep the Pickup/Delivery toggle and ETA strip visible at the top of Shop
7. Separate with 1pt `#E2E8F0` borders first; add a faint 6% shadow only for press/sticky/float
8. Confirm add-to-cart with a 260ms bump + cart-badge pop + soft haptic
