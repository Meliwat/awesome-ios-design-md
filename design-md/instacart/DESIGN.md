# Design System Inspiration of Instacart (iOS)

## 1. Visual Theme & Atmosphere

Instacart's iOS app is a bright, grocery-aisle catalog engineered around a single repeated motion: add an item, watch the cart total climb, check out. The canvas is pure white (`#FFFFFF`) with a barely-there gray surface (`#F6F7F8`) used to separate stacked content blocks, so product photography — produce, packaged goods, store logos — sits on a clean shelf with no visual noise. This is supermarket-as-software: thousands of SKUs need to scan fast, and the chrome gets out of the way so the photo and the price do the talking.

The accent system is anchored by Instacart Green (`#0AAD0A`) — a saturated, friendly grocery green used for every primary action: the "Add" buttons on product cards, the quantity stepper, the persistent green cart bar pinned to the bottom of the screen, and active tab state. A warm carrot orange (`#FF7009`) plays a deliberate secondary role for savings, deals, and "on sale" flags — a nod to the carrot in the Instacart logomark. The two colors never compete: green is the verb (add, buy, checkout), orange is the incentive (save, deal, coupon).

Typography is Inter — a clean, highly legible grotesque used at weights 400 / 600 / 700. The hierarchy is compact and utilitarian because the screen is dense with items: store names at 17pt weight 700, product titles at 15pt weight 400 (two-line clamp), prices at 15pt weight 700, and a recurring 13pt weight 400 secondary gray for unit pricing ("$0.42/oz"), delivery windows, and item counts. There are no hero headlines — the store card and the product grid are the headline, and every type size is tuned for a thumb scrolling a long list of groceries.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) with a faint `#F6F7F8` surface to chunk content — supermarket-clean
- Instacart Green (`#0AAD0A`) as the primary action color — Add buttons, steppers, cart bar, active tabs
- Carrot orange (`#FF7009`) reserved for deals, savings, and "on sale" flags
- Quantity stepper (− n +) is the signature interaction — every product card and cart row has one
- Persistent green cart bar pinned to the bottom — running item count + subtotal, always one tap from checkout
- Store card row — logo, name, delivery ETA, delivery fee — the home screen is a list of stores
- Replacement-preference toggle — choose "Best match", "Pick specific", or "Don't replace" per item
- Inter font, weights 400 / 600 / 700, tuned for dense grocery lists
- Add-to-cart fly-up animation — tapped item arcs toward the cart bar

## 2. Color Palette & Roles

### Primary
- **Instacart Green** (`#0AAD0A`): Primary CTA, "Add" button, quantity stepper, cart bar background, active tab, selected toggle.
- **Green Pressed** (`#098F09`): Active/pressed state for green buttons and the cart bar tap.
- **Green Tint** (`#E6F6E6`): Light green wash behind "in cart" badges and success confirmation chips.

### Secondary / Deal
- **Carrot Orange** (`#FF7009`): Deal flags, "on sale" price color, savings badges, coupon callouts.
- **Orange Tint** (`#FFF0E6`): Background wash for deal banners and promo strips.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary canvas, card backgrounds, sheets.
- **Surface** (`#F6F7F8`): Section separators, search field fill, skeleton placeholders, grouped backgrounds.
- **Divider** (`#E8E9EB`): Hairline dividers between list rows and card sections.
- **Text Primary** (`#242529`): Product titles, store names, prices, primary UI text.
- **Text Secondary** (`#72757E`): Unit pricing, delivery windows, item counts, metadata, placeholder text.
- **Text Tertiary** (`#A6A8AE`): Disabled labels, very low-emphasis helper text.

### Semantic
- **Sale Red** (`#D6203A`): Strikethrough original price next to a discounted carrot-orange price (rare; orange is primary for deals).
- **Success Green** (`#0AAD0A`): Order-placed confirmation, "Added" toast (same as primary).
- **Rating Star** (`#FF7009`): Star rating fill on product reviews (shares the carrot tone).
- **Error Red** (`#D6203A`): Out-of-stock warnings, payment failures, form validation.

### Dark Mode (Limited Use)
Instacart's iOS app is effectively light-only — the grocery-catalog model depends on a white shelf for product photography. A dark variant exists for system-wide dark mode but the primary, designed experience is light.
- **Dark Canvas** (`#1A1B1E`)
- **Dark Surface** (`#242529`)
- **Dark Text** (`#FFFFFF`)
- Instacart Green and carrot orange remain identical — both read well on dark.

## 3. Typography Rules

### Font Family
- **Primary**: `Inter` (used across the product as the brand grotesque)
- **Numerals**: Inter tabular figures for prices, quantities, and totals (`font-variant-numeric: tabular-nums`)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Non-Latin**: System fallback for CJK / Cyrillic / Arabic — Instacart is North-America-centric but localizes

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 26pt | 700 | 1.2 | -0.4pt | "Stores", "Cart" nav large title |
| Section Header | Inter | 20pt | 700 | 1.25 | -0.3pt | "Buy it again", "Deals for you" |
| Store Name | Inter | 17pt | 700 | 1.3 | -0.2pt | Store card title |
| Item Title | Inter | 15pt | 400 | 1.35 | 0pt | Product card name, 2-line clamp |
| Price | Inter | 15pt | 700 | 1.2 | 0pt | Product price, tabular figures |
| Price (Sale) | Inter | 15pt | 700 | 1.2 | 0pt | Carrot orange `#FF7009` |
| Body | Inter | 15pt | 400 | 1.45 | 0pt | Item descriptions, instructions |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Green "Add to cart", "Checkout" |
| Stepper Value | Inter | 16pt | 700 | 1.0 | 0pt | The "n" in the − n + stepper, tabular |
| Meta / Unit Price | Inter | 13pt | 400 | 1.3 | 0pt | "$0.42/oz", "Delivery by 2pm" |
| Badge / Deal | Inter | 12pt | 700 | 1.2 | 0.2pt | "SALE", "20% off", coupon flag |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Caption | Inter | 11pt | 400 | 1.3 | 0pt | Fine print, "EBT eligible" |

### Principles
- **Weight concentrated at 400 / 600 / 700**: Regular for item names and body, semibold for emphasis, bold for prices, store names, and buttons
- **Prices are always bold and tabular**: Right-aligned numerals must align across a list of items — tabular figures, weight 700
- **Two-line clamp on product titles**: Item names truncate at two lines with ellipsis; never push the price out of alignment
- **Secondary gray for all metadata**: Unit price, delivery windows, and counts use `#72757E` at 13pt to recede behind the product name and price
- **Deal color is type, not just background**: A sale price is rendered in carrot orange `#FF7009` — the color itself signals savings

## 4. Component Stylings

### Buttons

**Primary CTA ("Add to cart" / "Checkout" / "Continue")**
- Background: `#0AAD0A`
- Text: `#FFFFFF`, Inter 16pt weight 700
- Height: 52pt (full-width sheet CTA), 48pt (inline)
- Corner radius: 12pt
- Pressed: background `#098F09`, scale 0.98
- Disabled: background `#E8E9EB`, text `#A6A8AE`

**Compact "Add" Button (on product card, before any quantity)**
- Shape: pill, 32pt tall
- Background: `#FFFFFF`, border 1.5pt `#0AAD0A`
- Text: `#0AAD0A`, Inter 14pt weight 700, label "Add"
- Pressed: background `#E6F6E6`
- On tap: morphs into the quantity stepper (see Distinctive)

**Secondary / Outline ("Save for later", "Edit")**
- Background: transparent
- Border: 1.5pt solid `#242529`
- Text: `#242529`, Inter 15pt weight 600
- Padding: 11pt vertical, 20pt horizontal
- Corner radius: 12pt
- Pressed: background `#F6F7F8`

**Text Button ("See all", "View details")**
- Font: Inter 14pt weight 700
- Color: `#0AAD0A`
- No underline, 44pt hit area

**Icon Button (heart save, share, filter)**
- Size: 22pt glyph, 44pt hit area
- Default: `#72757E`
- Active (saved): `#0AAD0A`

### Cards & Containers

**Store Card (Home row)**
- Layout: horizontal — 56pt rounded store logo (12pt radius) → store name + meta column → trailing chevron
- Height: ~88pt with 16pt vertical padding
- Name: 17pt weight 700 `#242529`
- Meta line: "Delivery by 2pm · $3.99 delivery" 13pt weight 400 `#72757E`
- Background: `#FFFFFF`, divider `#E8E9EB` between rows
- Pressed: background `#F6F7F8`

**Product Card (Grid tile)**
- Width: ~150pt in a 2-up grid (12pt gap)
- Structure: square product photo (1:1, 12pt radius) → price (15pt w700) → unit price (13pt w400 `#72757E`) → title (15pt w400, 2-line clamp) → Add button / stepper bottom-right overlapping the photo
- Background: `#FFFFFF`
- Deal flag: carrot-orange `#FF7009` pill, top-left of photo, "20% off"
- Tap: scale 0.97

**Cart Row**
- Height: ~96pt
- Layout: 64pt product thumbnail (12pt radius) → title + unit price + replacement preference link → trailing quantity stepper
- Title: 15pt weight 400 `#242529`, 2-line clamp
- Replacement line: "Replacement: Best match" 13pt weight 400 `#0AAD0A` (tappable)
- Divider `#E8E9EB` between rows

**Aisle / Department Tile**
- Square or 4:3 tile in a 2-up grid, 12pt radius
- Photo with a label overlay bottom-left: 15pt weight 700 `#FFFFFF` on a subtle dark scrim
- Used on the "Browse aisles" grid

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, hairline top border `#E8E9EB`
- Tabs: Stores, Buy it again, Lists, Account, Cart
- Icon size: 24pt
- Active color: `#0AAD0A` (filled icon + label)
- Inactive: `#72757E` (outlined icon)
- Labels: Inter 11pt weight 600, always shown
- Cart tab carries a green count badge bubble when items > 0

**Green Cart Bar (Signature)**
- Pinned above the tab bar (or bottom-anchored within a store), full width minus 16pt side insets
- Height: 56pt
- Background: `#0AAD0A`, corner radius 12pt
- Left: white circular item-count badge ("8 items")
- Center/Right: "View cart" + subtotal in white, Inter 16pt weight 700, tabular figures
- Pressed: `#098F09`, subtle scale 0.99
- Slides up from below when the first item is added; persistent thereafter

**Top Nav / Search Header**
- Height: 52pt + safe area
- Store/location selector top-left (17pt weight 700 + chevron)
- Search field below: pill, 44pt, `#F6F7F8` fill, leading `magnifyingglass` 18pt `#72757E`, placeholder "Search Wegmans"
- Filter chip row beneath search (pill chips, 32pt tall)

### Input Fields

**Search Field**
- Background: `#F6F7F8`
- Height: 44pt
- Corner radius: 12pt
- Leading SF Symbol `magnifyingglass` 18pt `#72757E`
- Placeholder: 15pt weight 400 `#72757E`
- Focus: 1.5pt border `#0AAD0A`, background `#FFFFFF`

**Filter / Category Chip**
- Background: `#F6F7F8` default / `#242529` selected
- Text: `#242529` default / `#FFFFFF` selected, Inter 14pt weight 600
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 500pt (full pill)
- Examples: "On sale", "EBT", "Organic", "Gluten-free"

### Distinctive Components

**Quantity Stepper (− n +)**
- The signature control. Pill, 32pt tall, background `#0AAD0A`
- Layout: white "−" glyph (44pt hit) | white quantity (16pt weight 700, tabular) | white "+" glyph (44pt hit)
- Tapping "+" on a fresh product morphs the compact "Add" pill into the stepper (width animates 64pt → 104pt over 200ms)
- Decrementing to 0 morphs back to the "Add" pill
- Pressed glyph: background flashes `#098F09`
- Haptic `.impactOccurred(.light)` on every increment/decrement
- Appears on product cards, cart rows, and the product detail sheet

**Replacement-Preference Selector**
- A per-item choice surfaced on the cart row and in a bottom sheet
- Three options as a radio list: "Best match" (default), "Pick specific replacement", "Don't replace this item"
- Selected row: 1.5pt `#0AAD0A` border, green radio dot, 12pt radius
- Unselected: `#E8E9EB` border, hollow radio
- The chosen value renders inline on the cart row in green: "Replacement: Best match"

**Add-to-Cart Fly-Up**
- On "+" tap, a small ghost of the product thumbnail arcs from the card toward the green cart bar
- The cart-bar count badge bumps scale 1.0 → 1.2 → 1.0 and the subtotal updates
- ~450ms ease-out arc, paired with a light haptic

**Deal / Savings Flag**
- Carrot-orange `#FF7009` pill, top-left of the product photo
- Text: white, Inter 12pt weight 700, e.g. "20% off", "BOGO", "$2 off"
- Sale price below the photo renders in `#FF7009`; original price strikethrough in `#72757E`

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56
- Standard margin: 16pt horizontal
- Card padding: 12-16pt internal
- Product grid gap: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Product grid: 2-column on phone (12pt gap), 3-4 column on iPad
- Store list: full-width single-column rows
- Aisle browse: 2-column tile grid

### Whitespace Philosophy
- **Dense but breathable**: Grocery is a high-SKU experience — product cards pack tightly in a 2-up grid, but a 12pt gutter and white canvas keep each photo legible
- **The cart bar owns the bottom**: A fixed 56pt green bar always reserves bottom space — content scrolls behind it but never under the tab bar
- **Surface to chunk, not to decorate**: `#F6F7F8` separates major sections (Deals / Buy it again / Aisles); it is structural, never an accent

### Border Radius Scale
- Sharp (0pt): Full-bleed photography edges, very rare
- Standard (8pt): Small chips, badges, deal flags
- Comfortable (12pt): Product photos, cards, buttons, stepper, search field, cart bar
- Large (16pt): Bottom sheets, modals
- Full Pill (500pt): Filter chips, the compact "Add" button
- Circle (50%): Store logos when circular, count badges, icon buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, divider only | List rows, store rows, grouped content |
| Card (Level 1) | `rgba(20,21,25,0.06) 0 1px 3px` | Product cards lifting off the canvas |
| Raised (Level 2) | `rgba(20,21,25,0.10) 0 4px 12px` | The green cart bar, sticky filter header |
| Sheet (Level 3) | `rgba(20,21,25,0.16) 0 -8px 32px` | Bottom sheets (replacement picker, product detail) |
| Overlay Dim | `rgba(20,21,25,0.45)` | Modal scrim behind sheets |

**Shadow Philosophy**: Shadows are soft, low-opacity, and cool-neutral (mixed from the `#242529` ink, not pure black) so they read as a gentle lift on a bright white shelf rather than a hard drop. The most prominent elevation is the green cart bar — a `4px 12px` raise that keeps it floating clearly above scrolling product content. Product cards barely lift; the white-on-white separation is mostly carried by the `#E8E9EB` divider and generous spacing.

### Motion
- **Stepper increment**: "Add" pill → stepper width morph 64pt → 104pt over 200ms ease-out, light haptic
- **Add-to-cart fly-up**: ghost thumbnail arcs to cart bar ~450ms ease-out; count badge bounces 1.0 → 1.2 → 1.0
- **Cart bar entrance**: slides up 56pt from below with a spring (response 0.35, damping 0.8) on first add
- **Card tap**: scale 0.97 → 1.0 spring on press release
- **Sheet present**: bottom sheet slides up 0.3s ease-out with scrim fade
- **Replacement select**: radio dot scales 0 → 1 with a 150ms spring, border color crossfades to green

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the canvas — the grocery catalog needs a clean shelf
- Reserve Instacart Green (`#0AAD0A`) for actions: Add, stepper, cart bar, checkout, active tab
- Use carrot orange (`#FF7009`) only for deals, savings, and sale prices — it's the incentive color
- Render the quantity stepper as a green pill with white − n + and tabular numerals
- Keep the green cart bar pinned and persistent once items are in the cart
- Use tabular figures for all prices, quantities, and totals so columns align
- Two-line clamp product titles; never let a title push the price out of alignment
- Animate the "Add" pill morphing into the stepper on first tap
- Surface the replacement preference inline on the cart row in green

### Don't
- Don't use a gray or tinted canvas — product photography needs white
- Don't use green for decoration or non-action text — it's the verb color
- Don't use carrot orange for primary buttons — orange is savings, green is action
- Don't omit the persistent cart bar when the cart has items — it's the core loop
- Don't use proportional figures for prices — columns will misalign
- Don't stack more than two lines on a product title — truncate with ellipsis
- Don't make the stepper a generic gray control — the green pill is the signature
- Don't use heavy drop shadows — elevation is soft and cool-neutral on white
- Don't hide unit pricing — "$0.42/oz" is a deliberate trust signal in `#72757E`

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-up grid with tighter 10pt gutter, ~140pt tiles |
| iPhone 13/14/15 | 390pt | Standard 2-up grid, ~150pt tiles |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the store/location selector |
| iPhone 15/16 Pro Max | 430pt | ~170pt tiles, larger product photos |
| iPad | 768pt+ | 3-4 column product grid, store list in a 2-column master, cart as a side panel |

### Dynamic Type
- Product titles, store names, body: full scale
- Prices and stepper value: scale but stay tabular and right-aligned
- Tab labels and deal badges: fixed (layout-sensitive)
- Cart bar subtotal: scales to a max, truncates store name first

### Orientation
- Stores / Buy it again / Lists / Cart: **portrait-locked** on iPhone
- iPad: supports landscape with a multi-column grid and persistent cart side panel

### Touch Targets
- Stepper − and + glyphs: 44pt hit area each despite the compact pill
- "Add" pill: 32pt tall, full pill tappable
- Store rows: full-row tappable, ~88pt
- Cart bar: full 56pt bar tappable
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: store/location selector + search respect the safe area and Dynamic Island
- Bottom: green cart bar floats above the tab bar; both clear the home indicator
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F6F7F8`
- Divider: `#E8E9EB`
- Text primary: `#242529`
- Text secondary: `#72757E`
- Instacart Green (action): `#0AAD0A`
- Green pressed: `#098F09`
- Green tint: `#E6F6E6`
- Carrot orange (deals): `#FF7009`
- Orange tint: `#FFF0E6`
- Error / sale red: `#D6203A`

### Example Component Prompts
- "Create a SwiftUI Instacart quantity stepper: a 32pt-tall pill with background #0AAD0A, a white '−' glyph (44pt hit area) on the left, a center quantity '3' in Inter 16pt weight 700 with tabular figures in #FFFFFF, and a white '+' glyph (44pt hit area) on the right. Light haptic on each tap; pressed glyph flashes #098F09."
- "Build the persistent green cart bar: full width minus 16pt side insets, 56pt tall, background #0AAD0A, 12pt corner radius. Left: a white circular badge reading '8 items'. Right: 'View cart  $64.20' in Inter 16pt weight 700 #FFFFFF with tabular figures. Slides up from below with a spring on first add; pressed state #098F09."
- "Design an Instacart product card: ~150pt wide, square product photo with 12pt corner radius and a carrot-orange (#FF7009) '20% off' pill top-left. Below the photo: price '$4.99' Inter 15pt weight 700 #242529, unit price '$0.42/oz' 13pt weight 400 #72757E, title 'Organic Strawberries 1 lb' 15pt weight 400 #242529 two-line clamp. A compact 'Add' pill (white, 1.5pt #0AAD0A border, green text) overlaps the bottom-right of the photo and morphs into the stepper on tap."
- "Create the replacement-preference sheet: a bottom sheet with 16pt top corners, title 'If this is out of stock' Inter 20pt weight 700. A radio list with three rows — 'Best match' (selected: 1.5pt #0AAD0A border, filled green dot), 'Pick a specific replacement', and 'Don't replace this item' (unselected: #E8E9EB border, hollow dot). Each row 12pt radius, 56pt tall."
- "Build the Instacart bottom tab bar: white background, hairline #E8E9EB top border, 5 tabs — Stores, Buy it again, Lists, Account, Cart. Active tab #0AAD0A filled icon + Inter 11pt weight 600 label; inactive #72757E outlined. The Cart tab shows a green count-badge bubble when items > 0."

### Iteration Guide
1. Canvas is pure white `#FFFFFF` — product photography needs a clean shelf, never tint it
2. Instacart Green `#0AAD0A` is the action color: Add, stepper, cart bar, checkout, active tab — nothing decorative
3. Carrot orange `#FF7009` is the incentive color: deals, savings, sale prices only
4. The quantity stepper (− n +) is the signature — a green pill with white glyphs and tabular numerals
5. The green cart bar is persistent and pinned once the cart has items — it is the core loop
6. Prices, quantities, totals always use Inter tabular figures so columns align
7. Product titles clamp to two lines; the price stays bold and aligned
8. Elevation is soft and cool-neutral on white — the cart bar is the only prominent lift
