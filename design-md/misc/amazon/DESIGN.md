# Design System Inspiration of Amazon (iOS)

## 1. Visual Theme & Atmosphere

Amazon's iOS app is a relentlessly functional storefront — a design that has been A/B-tested down to the pixel, where every element is optimized to move a customer from "I might want that" to "Place your order" in the fewest possible taps. There is no pretense of minimalism here. The screen is dense, the hierarchy is aggressive, and every product tile packs photo, title, rating, review count, price, Prime badge, delivery date, and an Add-to-Cart button into a tight rectangle. The visual language is Amazon's own take on "utility first": whatever the design sacrifices in breathing room, it recovers in decision-enabling information density. The top search bar is the emotional anchor of the app — it's tall, bright, and sits above every screen like a lighthouse.

The accent system is built around two hard colors: Amazon's iconic yellow-orange (`#FF9900`) — the "Add to Cart" color, the smile-arrow color, the primary-CTA color — and a deep navy (`#131921`) that forms the top navigation bar. The yellow is not the warm, friendly yellow of food apps; it's a saturated, commercial yellow that signals "buy this now" on every screen. The navy is the voice of the chrome — it holds the search bar, the shipping-address banner, and the category breadcrumbs. Below the nav bar, the canvas is pure white (`#FFFFFF`) to let product photography breathe. Red (`#B12704`) does the work of "price" and "savings" — struck-through original prices, "Limited-time deal" banners, countdown timers. A teal blue (`#007185`) handles Prime-branded UI and in-link colors. Five-star ratings use a rich gold (`#FFA41C`) that's warmer than the primary yellow.

Typography is Amazon Ember — Amazon's proprietary typeface since 2015, a humanist sans designed for e-commerce legibility at small sizes. It's what you see on every product tile, review, and button across the app. Hierarchy is assertive: prices at 20-28pt weight 700 (sometimes with the cents in superscript for visual snap), product titles at 15-16pt weight 400 clamped to two lines, metadata at 13pt. The Prime checkmark is often inline with the price — a blue `prime` wordmark that signals "this ships free and fast". Amazon's typography is never decorative; it's engineered for fast-scanning shopping lists where every byte of information has a reason to be there.

**Key Characteristics:**
- Amazon Yellow-Orange (`#FF9900`) as the singular CTA color — Add to Cart, Checkout, Buy Now
- Deep Navy (`#131921`) top nav bar with bright search bar and yellow Search button
- Dense product grids and lists — photo + title + rating + price + Prime + delivery packed tight
- Amazon Ember font across the app — weights 300/400/500/700, optimized for small UI
- Price Red (`#B12704`) for deals, savings, and struck-through original prices
- Prime Teal Blue (`#007185`) for Prime badging, in-app links, membership UI
- Five-star ratings rendered in gold (`#FFA41C`) with review count in tiny blue link style
- Search bar at the top of every screen — voice mic + barcode-scanner camera icons
- "FREE Delivery by [date]" line beneath price — the delivery promise as UI
- Bottom tab bar: Home | Menu | Cart (badge) | You | Search-expand — not the 5 tabs of design-led apps

## 2. Color Palette & Roles

### Primary (Brand)
- **Amazon Yellow-Orange** (`#FF9900`): Primary CTA — Add to Cart, Buy Now, Search button, Prime Day badges. The defining CTA color.
- **Yellow Pressed** (`#E68A00`): Pressed/active state for yellow buttons.
- **Yellow Tint** (`#FFF4DF`): Promotional banner backgrounds, deal-tag fills.
- **Yellow Hover (Web cross-reference)** (`#FFD275`): Used rarely in iOS; included for parity.

### Navy / Chrome
- **Deep Navy** (`#131921`): Top navigation header, primary chrome surface.
- **Secondary Navy** (`#232F3E`): Sub-nav, category strip, account menu panel.
- **Navy Pressed** (`#0F141C`): Pressed state on navy surfaces.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary background below nav.
- **Surface Muted** (`#F3F3F3`): Sectional backgrounds, search panel fill, chip fills.
- **Surface Tint** (`#F7F8F8`): Secondary row background, quiet cards.
- **Divider** (`#DDDDDD`): Hairline dividers, list row separators.
- **Border Default** (`#D5D9D9`): Input borders, card borders.
- **Text Primary** (`#0F1111`): Product titles, prices, primary body text.
- **Text Secondary** (`#565959`): Metadata, "shipped by", review date, helper text.
- **Text Tertiary** (`#848A8C`): Disabled state, placeholder.

### Semantic
- **Price Red** (`#B12704`): Deal prices, savings amounts, struck-through originals, "Limited time deal" chips.
- **Alert Red** (`#CC0C39`): Error states, "Out of stock", destructive actions.
- **Success Green** (`#007600`): "In stock" indicator, delivery-date emphasis ("FREE Delivery Tomorrow").
- **Prime Teal** (`#007185`): Prime badge, checkout links, "Prime" membership UI, in-body links.
- **Prime Sky** (`#00A8E1`): Prime Video accent, supporting Prime visual.
- **Rating Gold** (`#FFA41C`): 5-star rating glyphs.
- **Low Stock Amber** (`#C45500`): "Only 3 left in stock" warnings.

### Status / Prime
- **Prime Badge Stroke** (`#007185`): Prime checkmark badge outline.
- **Prime Wordmark Blue** (`#00A8E1`): The `prime` word mark next to products.
- **Checkmark Green** (`#067D62`): Order confirmation, subscription-eligible.

### Dark Mode
- **Dark Canvas** (`#0F1111`): Primary dark background.
- **Dark Surface 1** (`#1A1F25`): Elevated surface, card background on dark.
- **Dark Surface 2** (`#232F3E`): Higher elevation, modal sheets (matches secondary navy).
- **Dark Divider** (`#3A4553`).
- **Dark Text Primary** (`#F5F5F5`).
- **Dark Text Secondary** (`#AAB7B8`).

## 3. Typography Rules

### Font Family
- **Brand / UI**: `Amazon Ember` (proprietary since 2015 — used across every Amazon surface: app, web, packaging, Kindle)
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif`
- **Monospace**: `SF Mono` for order numbers, promo codes, ASINs
- **CJK / Non-Latin**: Ember's global cuts ship for Arabic (Ember Arabic) and Chinese/Japanese/Korean; otherwise falls back to Noto or system

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Nav Hero Title | Ember | 22pt | 700 | 1.15 | -0.2pt | Category page hero |
| Section Title | Ember | 20pt | 700 | 1.2 | -0.1pt | "Frequently bought together", "Buy again" |
| Product Title | Ember | 16pt | 400 | 1.3 | 0pt | Product card title (2-line clamp) |
| Product Title (PDP) | Ember | 20pt | 500 | 1.3 | -0.1pt | Full product detail title |
| Price Hero | Ember | 28pt | 700 | 1.0 | -0.3pt | PDP price with superscript cents |
| Price Card | Ember | 18pt | 700 | 1.1 | -0.1pt | Price on product tile |
| Price Struck | Ember | 14pt | 400 | 1.2 | 0pt | Original price, strikethrough, gray |
| Body | Ember | 15pt | 400 | 1.45 | 0pt | Descriptions, review text, bullets |
| Rating Count | Ember | 13pt | 400 | 1.3 | 0pt | "(12,345)" — blue link style |
| Delivery Line | Ember | 13pt | 500 | 1.3 | 0pt | "FREE Delivery Tomorrow" |
| Meta | Ember | 12pt | 400 | 1.3 | 0pt | Review date, "Verified Purchase" |
| Button (Primary) | Ember | 15pt | 500 | 1.0 | 0pt | "Add to Cart", "Buy Now" |
| Button (Secondary) | Ember | 14pt | 400 | 1.0 | 0pt | "See all reviews", text links |
| Prime Badge | Ember | 11pt | 700 | 1.0 | 0.3pt | "prime" wordmark (lowercase) |
| Tab Label | Ember | 10pt | 500 | 1.0 | 0pt | Tab bar labels |
| Search Placeholder | Ember | 16pt | 400 | 1.0 | 0pt | "Search Amazon" placeholder |
| Badge / Promo | Ember | 11pt | 700 | 1.0 | 0.5pt | "Limited time deal" uppercase |

### Principles
- **Price is always bold**: Every price in the UI sits at weight 700, minimum 16pt, often 20-28pt. Money is never light.
- **Superscript cents on hero prices**: The dollar figure is 28pt, the cents are 14pt superscript — a signature Amazon move.
- **Product titles are 2-line clamped**: No matter the shelf, titles wrap to exactly two lines with ellipsis on the third.
- **Star-rating inline with count**: Five 12pt gold stars followed by "(12,345)" in 13pt teal-blue link color — tappable.
- **Weights concentrated at 400 / 500 / 700**: No thin, no black. 400 is body, 500 is buttons, 700 is prices and headlines.
- **No italics**: Emphasis via weight or color.

## 4. Component Stylings

### Buttons

**Primary CTA — "Add to Cart" (Yellow)**
- Background: `#FF9900`
- Text: `#0F1111` (intentionally black-on-yellow for contrast, not white)
- Padding: 12pt vertical, 16pt horizontal
- Height: 44pt standard, 48pt on PDP
- Corner radius: 8pt (rounded rectangle — not a pill)
- Font: Amazon Ember 15pt weight 500
- Pressed: background `#E68A00`, scale 0.98, light haptic
- Border: 1pt `#FCD200` highlight on top edge (subtle 3D cue)
- Shadow: `rgba(0,0,0,0.1) 0 1px 2px`
- Used for: "Add to Cart", "Buy Now", "Proceed to Checkout"

**Orange CTA — "Buy Now" / Checkout**
- Background: `#F08804` (slightly deeper than Amazon Yellow to distinguish urgency)
- Same padding, radius, font as primary
- Used for single-click "Buy Now" and the final "Place your order" on checkout

**Secondary Button (Outline)**
- Background: `#FFFFFF`
- Border: 1pt `#D5D9D9`
- Text: `#0F1111`, Ember 14pt weight 400
- Padding: 10pt vertical, 12pt horizontal
- Corner radius: 8pt
- Pressed: `#F7F8F8` fill
- Used for: "See all options", "Compare with similar"

**Link Button (Teal)**
- Background: transparent
- Text: `#007185`, Ember 14pt weight 400
- No underline default; underline appears on tap
- Used for: review count, "See more", in-body product links

**Icon Button (Nav — Mic, Barcode)**
- Size: 44pt hit area
- Icon: 22pt, `#FFFFFF` on navy nav, `#565959` on white
- Pressed: 20% overlay

### Cards & Containers

**Product Card (Grid Tile)**
- Width: 50% of viewport minus gaps (2-column grid default), or 150pt in a carousel
- Aspect: product photo square 1:1, white background, padded 8pt
- Content stack beneath image:
  - Product title: Ember 15pt w400 `#0F1111`, 2-line clamp
  - Rating row: 5 gold stars (12pt each, `#FFA41C`) + `(#### )` review count in Ember 12pt teal-blue
  - Price line: "$24.99" Ember 18pt w700, struck-through original "$34.99" in Ember 13pt w400 `#565959`
  - Prime line: blue `prime` wordmark + "FREE Delivery Tomorrow" in Ember 12pt w500
  - Optional "Add to Cart" yellow button at bottom (on grid list variant)
- Corner radius: 4pt on the image, 0pt on the wrapper
- Divider: 1pt `#DDDDDD` hairline between rows in list view

**Product Detail Page (PDP) Structure**
- Image carousel at top (full-width, 1:1 aspect, page dots)
- Title block: 20pt w500 product title, brand link, 5-gold-stars + review count
- Price block: $28.99 hero (28pt w700) with superscript cents, struck-through "$34.99", "You save $6" in red
- Prime + delivery block: "FREE delivery Tuesday, Oct 12" in 14pt w500 green
- Variant picker: color swatches 44pt circles, size chips 44pt tall pills
- Quantity stepper: leading "−" | "1" | "+" → `#F3F3F3` tile 32pt tall, 8pt radius
- Add to Cart yellow + Buy Now orange buttons stacked, full-width 48pt tall each
- "Secure transaction" line with lock icon underneath
- Sections below: "About this item" bullets, "From the manufacturer" photos, reviews

**Review Card**
- Avatar (32pt circle) + reviewer name + "Verified Purchase" green tag
- Title: Ember 16pt w700
- Date: Ember 12pt w400 `#565959`
- Rating: 5 gold stars
- Body: Ember 15pt w400
- "Helpful" button: outline, 14pt w400, shows count

**Cart Row**
- Image: 80×80pt product photo with 4pt radius
- Title: Ember 16pt w400 `#0F1111`, 2-line clamp
- Stock line: "In Stock" in `#007600` Ember 13pt w500
- Delivery: Ember 13pt w400 `#565959`
- Quantity stepper (as above) + "Delete" | "Save for later" | "Compare" text links in blue
- Trailing: price Ember 16pt w700

### Navigation

**Top Nav Header**
- Height: 56pt + safe area
- Background: `#131921` deep navy
- Leading: 28pt Amazon smile logo white
- Center-right: 44pt tall pill search bar (full width minus logo)
  - Search bar background: `#FFFFFF`
  - Corner radius: 8pt
  - Leading: 20pt `#565959` magnifying-glass icon
  - Placeholder: Ember 16pt w400 `#565959` "Search Amazon"
  - Trailing inside: yellow 44pt `#FF9900` square with `#0F1111` mic + barcode glyphs, 8pt right radius only
  - Tapping the bar expands to full-screen search
- Trailing: bell notification icon + optional cart mini-badge

**Sub-Nav (Deliver To)**
- Directly beneath main nav: `#232F3E` secondary navy strip
- Height: 36pt
- Content: "Deliver to [Name] — City Zip" Ember 14pt w400 white, leading pin icon
- Tap opens address sheet

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` with 0.5pt top border `#DDDDDD`
- Tabs: Home | Menu (hamburger) | Cart (badge) | You | Search
- Icon size: 24pt
- Active color: `#FF9900` (Amazon Yellow) with filled icon variant
- Inactive: `#565959`
- Labels: Ember 10pt w500, always shown
- Cart badge: red `#CC0C39` circle with white count, top-right of Cart icon

**Category Breadcrumbs / Departments**
- Horizontal scroll chips right below sub-nav
- Background: `#FFFFFF`
- Chip: Ember 14pt w400 `#007185` (link-styled), no pill background, 16pt gaps

### Input Fields

**Search Input (Expanded)**
- Height: 44pt
- Background: `#FFFFFF`
- Border: 1pt `#D5D9D9`
- Focus: border `#FF9900` 2pt
- Corner radius: 8pt
- Font: Ember 16pt w400 `#0F1111`

**Filter Chip**
- Background: `#F3F3F3`
- Border: 1pt `#D5D9D9`
- Text: Ember 14pt w400 `#0F1111`
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 20pt (pill)
- Leading chevron if multi-select dropdown

**Quantity Stepper**
- Wrapper: `#F3F3F3` fill, 1pt `#D5D9D9` border, 8pt radius, 32pt tall
- Minus (−) | count | plus (+) segments
- Count cell: Ember 15pt w500 center
- Pressed: `#E7E9EC` for that segment

### Distinctive Components

**Prime Badge Inline**
- Text "prime" in lowercase Ember 11pt w700, color `#00A8E1` (Prime sky)
- Paired with a small blue check or arrow glyph
- Sits on its own line directly beneath the price

**"FREE Delivery" Date Line**
- Ember 13pt w500 `#007600`
- "FREE delivery Tuesday, Oct 12" with date bolded
- Sometimes prefixed with `prime` wordmark

**Star Rating Row**
- 5 stars (12pt each, `#FFA41C` filled / `#E3E6E6` empty), 2pt gap between
- Trailing: "4.7" Ember 13pt w400 `#0F1111` + " · (12,345)" in `#007185` teal-blue link color
- Tap: opens review section

**Deal Countdown Banner**
- Background: `#B12704` red gradient to `#CC0C39`
- Icon: white lightning bolt
- Text: "Lightning Deal" Ember 14pt w700 white + "Ends in 02h 45m 10s" Ember 13pt w500 white, countdown updates live
- Corner radius: 4pt
- Padding: 8pt vertical, 12pt horizontal

**"Frequently Bought Together" Carousel**
- Horizontal row of 3-4 product thumbnails with "+" signs between them
- Bundle total: "Total price: $89.97" Ember 18pt w700
- "Add all 3 to Cart" yellow button

**Checkout Button Pill**
- Full-width 48pt tall on final checkout
- Background: `#F08804` (Buy Now orange)
- Text: Ember 16pt w500 `#0F1111`
- Shown in the bottom drawer of cart when total is > $0

**Add-to-Cart Toast**
- Appears at bottom, slides up 300ms ease-out
- Green `#007600` checkmark + "Added to your Cart" Ember 15pt w500
- Background: `#FFFFFF` with `rgba(0,0,0,0.15) 0 -4px 12px` shadow
- Auto-dismisses after 2.5s

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard margin: 16pt horizontal
- Card padding: 12pt interior
- Gap between cards in grid: 12pt
- Gap between rows in list: 8pt (tight — Amazon packs density)

### Grid & Container
- Product grid: 2-column default, 3-column on wide devices
- Horizontal carousels: 150pt tile width standard
- List view rows: full-width with 80pt product photo leading
- PDP: single column, full-width image carousel, sections stacked

### Whitespace Philosophy
- **Density is intentional**: Product tiles cram photo, title, rating, price, Prime, delivery — this is a shopping tool, not a gallery.
- **White is air**: canvas white gives product photos room to breathe without chrome intruding.
- **Price gets space**: every price sits with at least 8pt above and below to read as the hero datum.
- **Sections are clearly bordered**: 8pt gray bands (`#F3F3F3`) separate "Frequently bought together" from "From the manufacturer" from reviews.

### Border Radius Scale
- Square (0pt): Nav bars, full-bleed image carousels, wrapper containers
- Subtle (4pt): Product images in cards, deal badges, review chips
- Standard (8pt): Buttons (Add to Cart, filter chips), inputs, quantity steppers, cards
- Comfortable (12pt): Modal sheets, promotional banner tiles
- Pill (20pt): Filter chips with pill styling
- Circle (50%): Avatars, Prime badge dot, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, card interiors, most product tiles |
| Hairline (Level 1) | 1pt `#DDDDDD` border | Default input, product card border, section dividers |
| Button Lift (Level 2) | `rgba(0,0,0,0.1) 0 1px 2px` + 1pt top highlight `#FCD200` | Yellow CTA buttons (subtle 3D cue) |
| Sticky Nav (Level 3) | `rgba(0,0,0,0.15) 0 2px 6px` | Nav bar when content scrolls beneath |
| Modal / Sheet (Level 4) | `rgba(0,0,0,0.2) 0 -4px 16px` | Cart drawer, variant picker sheet |
| Toast (Level 5) | `rgba(0,0,0,0.15) 0 -4px 12px` | Add-to-cart toast, flash messages |
| Dim Overlay | `rgba(0,0,0,0.5)` | Behind modal sheets |

**Shadow Philosophy**: Amazon's app is mostly flat, but the Add-to-Cart button has an intentional 1pt yellow highlight on its top edge — a vestigial skeuomorph from Amazon's earliest web buttons that signals "press me". Every other elevation is quiet: a hairline border, a 1-2pt shadow under a sticky nav. The app rejects heavy shadows because product photos are the content and shadows would compete.

### Motion
- **Add to Cart tap**: scale 0.98 → 1.0 spring (damping 0.8), background `#E68A00`, light haptic; success haptic when cart count increments
- **Cart badge increment**: number bumps with 200ms ease-out scale 1.0 → 1.3 → 1.0
- **Search expand**: 250ms ease-in-out morph from navy search bar into full-screen search panel
- **Image carousel swipe**: 60fps paging, page dots animate between
- **Review "Helpful" tap**: number increments with 150ms fade
- **Deal countdown**: updates every second, no animation (efficiency)
- **Add-to-cart toast**: slides up 300ms ease-out, holds 2.5s, slides down 200ms ease-in
- **Variant swatch select**: 1pt → 2pt black border animates over 150ms + light haptic
- **Buy Now confirm**: full-screen green checkmark animation on order placed (500ms scale + fade)

## 7. Do's and Don'ts

### Do
- Use Amazon Yellow (`#FF9900`) on every primary purchase CTA — Add to Cart, Buy Now, Search button
- Use black text on yellow buttons (`#0F1111` on `#FF9900`) — the iconic contrast
- Keep the top nav in deep navy `#131921` with a white/yellow search bar
- Pack product tiles with photo + title (2-line clamp) + rating + price + Prime + delivery
- Render prices in Ember 700 at 18-28pt — money is never light
- Use superscript cents on hero prices ($28 + small 99 superscript)
- Use gold (`#FFA41C`) for rating stars — not yellow, not orange
- Use teal blue (`#007185`) for the Prime wordmark and in-body links
- Use price red (`#B12704`) for strike-throughs, savings, and deal banners
- Show "FREE Delivery by [date]" in green on every eligible product

### Don't
- Don't swap the yellow CTA for another color — Add to Cart is Amazon Yellow, period
- Don't use white text on yellow buttons — intentionally black
- Don't use black canvas — the app is light-first; dark mode is a variant
- Don't over-round buttons — 8pt radius, not pills (the utility feel is the brand)
- Don't hide the search bar — it's the anchor of every screen
- Don't use decorative fonts for prices or headlines — Ember only, weight 700 for prices
- Don't use italics — emphasis via weight
- Don't use 5-star ratings in orange — gold `#FFA41C` is the brand
- Don't collapse product tile metadata — Amazon customers expect rating, price, Prime, delivery visible simultaneously
- Don't introduce gradients on the yellow CTA — the flat color is load-bearing

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-column product grid, 135pt tiles |
| iPhone 13/14/15 | 390pt | 2-column standard, 150pt tiles |
| iPhone 15/16 Pro | 393pt | Dynamic Island pushes nav 8pt lower |
| iPhone 15/16 Pro Max | 430pt | 2-column with 170pt tiles, 3-column in landscape |
| iPad | 768pt+ | 3-4 column grid, PDP gets side-by-side image + buy-box |

### Dynamic Type
- Product titles, body, reviews: full scale
- Prices: scale up to 140% then cap (layout-sensitive)
- Prime wordmark: fixed size always (visual consistency)
- Tab labels: fixed 10pt
- Nav search placeholder: scales modestly up to 18pt

### Orientation
- Home, Browse, PDP: **portrait-preferred** (landscape supported on iPad)
- Image carousel on PDP: rotates to landscape for full-screen zoom
- Video reviews: rotate to landscape

### Touch Targets
- Add to Cart yellow button: 44-48pt tall — the primary hit zone
- Star rating row: 44pt effective hit area (tap opens reviews)
- Product tile: full tile tappable, entire 240pt tall card is one hit zone
- Quantity stepper: 32pt tall but with 44pt effective touch on + / − cells

### Safe Area Handling
- Top: navy nav bar extends under status bar; content respects top safe area
- Bottom: cart drawer extends to home indicator; tab bar floats above it
- Sides: 16pt content insets; product grid goes edge-to-edge with internal padding

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Deep navy (nav): `#131921`
- Secondary navy (sub-nav): `#232F3E`
- Amazon Yellow (CTA): `#FF9900`
- Yellow pressed: `#E68A00`
- Buy Now orange: `#F08804`
- Price red: `#B12704`
- Prime teal: `#007185`
- Prime sky: `#00A8E1`
- Rating gold: `#FFA41C`
- Success green: `#007600`
- Text primary: `#0F1111`
- Text secondary: `#565959`
- Divider: `#DDDDDD`
- Border default: `#D5D9D9`

### Example Component Prompts
- "Create a SwiftUI Amazon top nav: 56pt + safe area, `#131921` deep navy background. Leading 28pt Amazon smile logo in white, then a full-width pill search bar. Search bar: 44pt tall, `#FFFFFF` fill, 8pt corner radius, leading 20pt magnifying-glass icon in `#565959`, placeholder 'Search Amazon' in Amazon Ember 16pt w400 `#565959`. Trailing inside the bar: a 44pt square `#FF9900` yellow button with `#0F1111` mic + barcode icons and only the right 8pt corners rounded."
- "Build the Add to Cart button: `#FF9900` background with 1pt `#FCD200` top highlight, 8pt corner radius, 44pt tall, full-width minus 16pt margins. Text 'Add to Cart' in Amazon Ember 15pt weight 500 `#0F1111` (intentionally black on yellow). Pressed: background `#E68A00`, scale 0.98, light haptic; success haptic when cart count increments."
- "Design a product card grid tile: 2-column layout 150pt wide, 1:1 product photo on top with 4pt corner radius, followed by: title 'Wireless Noise-Cancelling Headphones' in Ember 15pt w400 `#0F1111` (2-line clamp), rating row with 5 gold `#FFA41C` 12pt stars + '(12,345)' in Ember 12pt `#007185` teal-blue link color, price '$24.99' in Ember 18pt w700 with struck-through '$34.99' in Ember 13pt w400 `#565959` beside it, then blue `prime` wordmark + 'FREE Delivery Tomorrow' in Ember 12pt w500 green `#007600`."
- "Build the PDP price block: hero price '$28.99' in Ember 28pt w700 with the '99' as a 14pt superscript, struck-through original '$34.99' in Ember 14pt `#565959` on the next line, 'You save $6.00 (17%)' in Ember 14pt w500 `#B12704`. Below: '`prime` FREE delivery Tuesday, October 12' in Ember 14pt w500 `#007600` with the `prime` wordmark in `#00A8E1`."
- "Create a Lightning Deal countdown banner: gradient from `#B12704` to `#CC0C39`, 4pt corner radius, leading white lightning-bolt icon, 'Lightning Deal' in Amazon Ember 14pt w700 white, trailing countdown '02h 45m 10s' in Ember 13pt w500 white. Padding 8pt vertical, 12pt horizontal."
- "Build the bottom tab bar: 50pt + safe area, `#FFFFFF` background with 0.5pt top border `#DDDDDD`. Tabs: Home | Menu | Cart (with red `#CC0C39` badge showing count) | You | Search. Icon 24pt, active color `#FF9900` (Amazon Yellow, filled variant), inactive `#565959` (outline). Labels Ember 10pt w500 always shown."

### Iteration Guide
1. Yellow (`#FF9900`) with black text is the CTA — never white text, never another color for Add to Cart
2. Deep navy (`#131921`) is the nav chrome with a bright white search bar and yellow Search button — the silhouette of Amazon
3. Prices are Ember 700 weight, 18-28pt, with superscript cents on hero prices — money is never light
4. Product tiles pack photo, title (2-line clamp), rating (5 gold stars + teal review count), price with strikethrough, Prime + delivery line — all five pieces always visible
5. Use Prime teal (`#007185`) for the `prime` wordmark and body links; Prime sky (`#00A8E1`) for the wordmark accent
6. Rating stars are gold (`#FFA41C`) — not yellow, not orange — paired with a teal-blue review-count link
7. The deal red (`#B12704`) does double duty: struck-through original prices and deal countdown banners
8. Success green (`#007600`) for "FREE Delivery by [date]" promises — the delivery pitch as UI
9. Bottom tab bar is light with yellow active state — not green, not blue — the yellow is the brand even in chrome
