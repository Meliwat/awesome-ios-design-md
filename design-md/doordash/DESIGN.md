# Design System Inspiration of DoorDash (iOS)

## 1. Visual Theme & Atmosphere

DoorDash's iOS app is warm, appetizing, and unapologetically commercial — a design optimized for one job: turning browsing into a placed order in under sixty seconds. The visual language leans on large, saturated food photography set against bright white surfaces, with the signature DoorDash Red (`#EB1700`) deployed as a beacon for every "take action" moment. Where Instagram's chrome retreats, DoorDash's chrome leans in: prominent search bars, bold category pills, pulsing promo banners, and a red floating checkout button that follows you down every scroll. This is e-commerce design at its most confident — the app assumes you're hungry and your attention is scarce.

The layout rhythm is card-first and card-dense. Restaurants appear as large, photo-heavy tiles with the name, rating stars, delivery time, and delivery fee stacked in a tight block underneath. Horizontal carousels segment content by intent ("Fastest near you", "$0 delivery fee", "Order it again"), and each carousel scrolls infinitely to the right — tapping the hero image and scrolling horizontally feel like the two primary gestures in the app. The grid is not subtle: photos are cropped aggressively, contrasts are pushed, and food is lit to look hotter than it really is.

Typography is TT Norms Pro (DoorDash's proprietary face, very close to a tightened Proxima Nova / SF Pro hybrid). Titles are set in weight 700 at 20-28pt — assertive, not elegant — and body copy sits at weight 400 with generous tracking that keeps restaurant names and delivery times readable at speed. The hierarchy is brutal and efficient: price in large bold numerals, restaurant name in 17-20pt semibold, meta-data (rating, fee, time) in 13pt regular gray. The color temperature is warm throughout: off-white (`#FFFFFF`) body, charcoal text (`#191919`), neutral cool grays for borders, and a small supporting palette of warm highlights (orange `#FF8000` for ratings, green `#008B4A` for $0 fee badges).

**Key Characteristics:**
- DoorDash Red (`#EB1700`) used aggressively for every primary CTA — checkout, place order, add to cart
- Photo-first merchant cards with 16:9 hero imagery — food is the content, chrome steps back only here
- TT Norms Pro in weight 700 for titles, 400 for body — bold hierarchy
- Warm white canvas (`#FFFFFF`) with near-black text (`#191919`) — high contrast, appetizing
- Horizontal carousels by "intent" stacked vertically — infinite scroll of themed rails
- Rating orange (`#FF8000`) and $0-fee green (`#008B4A`) as semantic accents
- Floating checkout button with item count — anchors the bottom of every screen once cart is populated
- Map integration on tracking screens — rounded 16pt corners, red pin with DoorDash logo
- 16pt corner radius on cards, 28pt on primary CTAs — rounder than most e-commerce apps

## 2. Color Palette & Roles

### Primary (Brand)
- **DoorDash Red** (`#EB1700`): Primary CTA backgrounds, logo mark, promo badges, delivery pin. The defining chromatic identity.
- **Red Pressed** (`#C31500`): Active/pressed state for red CTAs.
- **Red Tint** (`#FFEBE8`): Promo banner backgrounds, "New" chip fills, sale-tag surfaces.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary background — light mode.
- **Charcoal** (`#191919`): Primary text, headings, strong body.
- **Text Secondary** (`#757575`): Restaurant meta, timestamps, helper text.
- **Text Tertiary** (`#AFAFAF`): Disabled state, placeholder, very-low-emphasis labels.
- **Divider** (`#E5E5E5`): Hairline dividers, card borders (where used), list row separators.
- **Surface Muted** (`#F7F7F7`): Search bar fill, quiet card backgrounds, category chip fill.
- **Surface Tint** (`#FAFAFA`): Sectional backgrounds to separate content bands.

### Semantic
- **Rating Orange** (`#FF8000`): Star glyph for rating, $$ badges at higher tier, "Popular" chips.
- **Fee Green** (`#008B4A`): "$0 delivery" green tags, DashPass savings indicators, success toasts.
- **Warning Amber** (`#F5B800`): "Busy" / "Estimated delay" notices, mild warnings.
- **Error Red** (`#D1350F`): Form validation errors (slightly deeper than brand red), destructive actions.
- **Info Blue** (`#0066E3`): Info banners, link text, map legend.

### Dark Mode Surfaces
- **Background** (`#191919`): Dark mode canvas.
- **Surface 1** (`#262626`): Card backgrounds, sheet surfaces.
- **Surface 2** (`#303030`): Elevated cards on dark backgrounds, search bar fill.
- **Divider Dark** (`#3A3A3A`): Hairline dividers in dark mode.

### Status Colors
- **Store Open Green Dot** (`#008B4A`): Merchant "open now" indicator.
- **Store Closed Gray** (`#8E8E8E`): Unavailable merchants, "Pre-order only" state.
- **DashPass Teal** (`#006B82`): DashPass member perks, blue-teal signature for subscription UI.

## 3. Typography Rules

### Font Family
- **Brand / UI**: `TT Norms Pro` — proprietary DoorDash face. Fallback stack: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, sans-serif`.
- **Monospace**: `SF Mono` for order numbers, promo codes.
- TT Norms Pro is used across both UI and prose — there is no separate display face.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Title | TT Norms Pro | 28pt | 700 | 1.15 | -0.4pt | "Featured restaurants", merchant screen header |
| Section Title | TT Norms Pro | 22pt | 700 | 1.2 | -0.3pt | "Trending near you", "Order it again" |
| Merchant Name | TT Norms Pro | 18pt | 700 | 1.25 | -0.2pt | Restaurant card title |
| Menu Item Title | TT Norms Pro | 17pt | 600 | 1.3 | -0.15pt | Dish name on menu list |
| Body | TT Norms Pro | 15pt | 400 | 1.4 | 0pt | Descriptions, terms text |
| Price Large | TT Norms Pro | 20pt | 700 | 1.0 | -0.2pt | Total, subtotal, order price |
| Price Body | TT Norms Pro | 15pt | 600 | 1.2 | 0pt | Menu item price, delivery fee |
| Rating / Meta | TT Norms Pro | 13pt | 400 | 1.3 | 0pt | "4.8 · 20 min · $0 delivery" |
| Category Chip | TT Norms Pro | 14pt | 600 | 1.0 | 0pt | "Pizza", "Sushi", "Healthy" |
| Button (Primary) | TT Norms Pro | 16pt | 700 | 1.0 | 0pt | "Checkout ($23.40)", "Place Order" |
| Button (Secondary) | TT Norms Pro | 14pt | 600 | 1.0 | 0pt | "Add to cart", "View more" |
| Tab Label | TT Norms Pro | 11pt | 600 | 1.0 | 0.1pt | Tab bar labels — DoorDash labels its tabs |
| Promo Badge | TT Norms Pro | 11pt | 700 | 1.0 | 0.3pt | "20% OFF", "FREE DELIVERY" uppercase |
| Nav Title | TT Norms Pro | 17pt | 700 | 1.1 | -0.1pt | Default navigation bar title |
| Nav Large Title | TT Norms Pro | 34pt | 700 | 1.1 | -0.5pt | iOS large-title variant on Browse tab |

### Principles
- **Weights concentrated at 400 / 600 / 700**: No thin or black weights. 700 (bold) carries all titles and primary CTAs.
- **Prices always bold**: Any currency amount displayed in the UI is weight 700 minimum, often 700 at 18-28pt. Money is never light.
- **Dynamic Type support**: TT Norms Pro scales across all accessibility sizes; numeric prices scale to a max of 140%.
- **Tight title tracking**: -0.15 to -0.5pt on titles to pack more merchant name onto narrow cards.

## 4. Component Stylings

### Buttons

**Primary CTA (Full-width "Checkout", "Place Order")**
- Background: `#EB1700`
- Text: `#FFFFFF`
- Padding: 16pt vertical, full-width (minus 16pt margins)
- Corner radius: 28pt (pill-rounded) for bottom-anchored CTA; 12pt for in-card CTAs
- Font: TT Norms Pro, 16pt, weight 700
- Pressed: background shifts to `#C31500`, scale 0.98
- Disabled: `#AFAFAF` background, white text, 60% opacity

**Primary CTA with Counter (Checkout with price)**
- Layout: left-aligned "Checkout" label, right-aligned dynamic price
- Height: 52pt, corner radius 28pt, padding 16pt horizontal
- Always shows item count (tiny white pill on red) on the leading side when items >1

**Secondary Pill ("Add to Cart", "View Menu")**
- Background: `#191919` or `transparent`
- Text: `#FFFFFF` or `#191919`
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 20pt
- Font: TT Norms Pro, 14pt, weight 600
- Border: 1pt solid `#E5E5E5` for transparent variant

**Category Chip**
- Background: `#F7F7F7` (inactive) / `#191919` (active)
- Text: `#191919` (inactive) / `#FFFFFF` (active)
- Padding: 8pt vertical, 14pt horizontal
- Corner radius: 20pt (full pill)
- Font: TT Norms Pro, 14pt, weight 600
- Icon (optional): 16pt leading SF Symbol or emoji

**Icon Button (Heart / Save / Share)**
- Size: 28pt circular hit zone on light background
- Fill: 40% white scrim over image in hero tiles so icons stay legible
- Active heart: `#EB1700` fill with pop animation
- Share: SF Symbol `square.and.arrow.up`

**Promo Badge (inline CTA)**
- Background: `#FFEBE8` (red tint)
- Text: `#EB1700`
- Padding: 4pt vertical, 8pt horizontal
- Corner radius: 4pt
- Font: TT Norms Pro, 11pt, weight 700 UPPERCASE
- Examples: "20% OFF", "FREE DELIVERY", "NEW"

### Cards & Containers

**Merchant Card (Horizontal carousel)**
- Width: 260pt
- Image aspect: 16:10 at the top
- Corner radius: 12pt (image), 0pt on text block
- Padding: 12pt text block
- Background: `#FFFFFF` with `rgba(0,0,0,0.06) 0 4px 12px` shadow on scroll
- Structure: image → save heart (top-right, white scrim) → promo badge overlay → merchant name → rating + time + fee row

**Merchant Card (Full-width vertical)**
- Full device width minus 16pt margins
- Image aspect: 16:9
- Corner radius: 16pt (consistent throughout card)
- Padding: 14pt text
- DashPass chip overlay top-left when applicable
- Same info hierarchy as horizontal variant

**Menu Item Row**
- Layout: left-side text block, right-side 88pt × 88pt square thumbnail
- Corner radius on thumbnail: 8pt
- Text: item name (17pt w600), description (13pt w400 gray, 2-line clamp), price (15pt w600)
- Divider: 0.5pt hairline between rows
- Tap: full row highlights to `#F7F7F7` for 120ms

**Cart Row**
- Layout: 48pt × 48pt thumbnail → item name + customizations (stacked) → right-aligned price → trailing stepper
- Stepper: "-" and "+" in 28pt circles with `#F7F7F7` fill, bold char in `#191919`
- Divider between rows

**Order Tracking Card**
- Height: 160pt map embed on top, info block below
- Corner radius: 16pt
- Driver photo: 56pt circular, positioned over the map edge (overlap)
- Progress bar: 4pt height, `#EB1700` fill, `#E5E5E5` track, 4pt corner radius
- ETA text: 28pt weight 700, right-aligned

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: `#FFFFFF` with 0.5pt top border in `#E5E5E5` (not translucent — solid)
- Tabs: 4-5 (Home, Grocery, Offers, Orders, Account)
- Icon size: 24pt, active state in `#EB1700`
- Label: 11pt weight 600, shown always
- Active indicator: filled SF Symbol variant + red color + bolder label
- Cart badge: red dot with item count (white number, 10pt w700)

**Top Nav Bar**
- Height: 44pt + safe area
- Background: `#FFFFFF` (no blur — opaque)
- Left: address selector (location pin + "Home ▾" in 15pt w700)
- Right: search icon, account/avatar
- Large-title variant: 34pt w700 on Browse tab, collapses to 17pt on scroll
- Sticky search bar below nav when scrolled: 44pt height, `#F7F7F7` fill, 12pt radius

**Filter / Sort Pills Bar**
- Horizontal scroll below nav
- Pills: `#F7F7F7` inactive, `#191919` active
- Active count badge: small red circle with white count

### Image Treatment
- Food photography at 16:9 or 16:10 aspect — never square, never portrait
- Corner radius 12-16pt on all thumbnail/card images
- Slight color grade: warm, high-saturation, shadows lifted
- Restaurant logo: 56pt circular, 2pt white border to pop against photography
- Empty state: light gray illustration + title + helper body + CTA

### Input Fields

**Search Bar (Top of Home)**
- Background: `#F7F7F7`
- Height: 44pt
- Corner radius: 12pt
- Leading: SF Symbol `magnifyingglass`, 16pt, `#757575`
- Placeholder: "Find food or restaurants", 15pt w400 `#757575`
- Focus: border becomes 1pt `#191919`

**Address Input (Checkout)**
- Background: `#FFFFFF`
- Height: 56pt
- Corner radius: 12pt
- Border: 1pt `#E5E5E5` default, 1pt `#191919` focus, 1pt `#D1350F` error
- Label: 11pt w600 uppercase floating above input
- Helper text: 11pt w400 `#757575` or `#D1350F` for error

**Quantity Stepper**
- Three elements: "-" button, count, "+" button
- Buttons: 28pt circular, `#F7F7F7` background, 16pt weight 700 character
- Count: 16pt w700 centered between buttons
- Disabled "-": 40% opacity when count at minimum

### Distinctive Components

**Category Carousel (Home)**
- Horizontal scroll, rounded 96pt × 120pt tiles
- Photo 96pt × 96pt with 16pt radius, label 13pt w600 centered below
- 12 visible categories ("Pizza", "Sushi", "Mexican", "Healthy", etc.)
- Tap: full-screen filter applied

**DashPass Promo Banner**
- Height: 72pt, full-width
- Background: `#006B82` (DashPass teal) with white text
- Layout: icon + "Save $7 on this order with DashPass" + ">" chevron
- Corner radius: 12pt, 16pt horizontal margin

**Floating Checkout Button**
- Position: fixed bottom, 16pt inset from safe area
- Width: full screen minus 16pt margins
- Red pill (28pt radius), 52pt tall
- Shows on every screen where cart has items
- Swipes up to reveal cart drawer; tap proceeds to checkout

**Map Tracking**
- Full-width map, 240pt tall
- Red delivery pin (DoorDash logo embedded)
- Driver avatar + name card floating at top of map
- Live route polyline in `#EB1700`, 4pt width, rounded caps
- ETA banner fixed to bottom of map

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 2, 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Card padding standard: 12-16pt
- Section vertical rhythm: 24pt between carousels, 16pt within
- Safe-area-aware bottom for floating CTA (extends under home indicator with blur)

### Grid & Container
- Content width: device width
- Horizontal padding: 16pt on content rows
- Carousel cards poke beyond the right edge (cropped 30%) to communicate "there's more, swipe"
- 2-column grid on iPad-compact variant, 1-column on iPhone

### Whitespace Philosophy
- **Dense but scannable**: DoorDash packs more per viewport than Instagram — a viewport shows the location bar, search, 2 category chips, 1 promo banner, and the start of the "Trending" carousel. Every pixel sells.
- **Section rhythm via color bands**: Alternating `#FFFFFF` and `#FAFAFA` backgrounds separate content rails without drawing explicit lines.
- **Floating CTA dominance**: The red checkout button occupies about 8% of the viewport bottom and never disappears once cart > 0. This is the single most valuable real estate in the UI.

### Border Radius Scale
- Chip (4pt): Promo badges, small tags
- Thumbnail (8pt): 88pt menu item thumbnails
- Card (12pt): Horizontal merchant cards, info banners
- Full Card (16pt): Full-width merchant cards, map panels, order summary
- CTA Pill (20pt): Secondary pill buttons
- Primary CTA (28pt): Checkout/Place Order buttons — the app's most rounded element
- Circle (50%): Merchant logo avatar, driver avatar, stepper buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Backgrounds, list rows, text blocks |
| Card (Level 1) | `rgba(0, 0, 0, 0.06) 0 4px 12px` | Merchant cards, menu items when lifted |
| Floating CTA (Level 2) | `rgba(0, 0, 0, 0.16) 0 8px 24px` | Bottom-pinned checkout button, active sheets |
| Sheet (Level 3) | `rgba(0, 0, 0, 0.24) 0 -8px 24px` | Bottom sheets, filter modals |
| Map Overlay | `rgba(0, 0, 0, 0.12) 0 2px 8px` | Driver card floating over map |

**Shadow Philosophy**: DoorDash uses shadows to guide action. The checkout CTA has a heavier shadow than any merchant card — a visual weight hierarchy that tells your eye "this is the thing to tap." Merchant cards only elevate when they're in a horizontal carousel; full-width list cards stay flat.

### Motion
- **Card entrance**: Fade + 8pt rise-up, 300ms, ease-out
- **Add-to-cart**: Button pulses (scale 0.95 → 1.05 → 1.0), 250ms with success haptic
- **Cart badge update**: Spring bounce on badge number change
- **Carousel snap**: Rubber-band edge, 60fps paging
- **Order placed**: Full-screen red confetti + checkmark + haptic success notification

## 7. Do's and Don'ts

### Do
- Use DoorDash Red (`#EB1700`) for every primary CTA — it's the "spend money" color
- Photograph food at 16:9 with warm lighting; never use stock-flat food photos
- Set titles in weight 700 — hierarchy is bold, not subtle
- Keep the floating checkout button pinned bottom when cart > 0
- Round primary CTAs to 28pt (pill shape); round cards to 16pt
- Show price in weight 700 always — money is never light
- Use promo red tint (`#FFEBE8`) for offer banners, NOT full red — save `#EB1700` for buttons
- Stack content in infinite horizontal carousels organized by intent
- Badge rating in orange (`#FF8000`) and $0 fee in green (`#008B4A`) — two semantic accents only

### Don't
- Don't use DoorDash Red for non-interactive elements — no red text, no red icons beyond the pin/logo
- Don't use small-title nav bars on top-level tabs — DoorDash uses large-title iOS style
- Don't understate price — always weight 700, never italicized
- Don't compose food cards without a photo — no placeholders, no gray blocks in production
- Don't use square food photos — 16:9 is the law
- Don't introduce new accent colors — orange and green are the only semantic chromas
- Don't hide the cart badge — it must be visible the instant an item is added
- Don't use heavy drop shadows on non-interactive cards — elevation implies action
- Don't use weight 300 or 900 — stick to 400/600/700

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Category chips wrap to 2 rows; carousel cards resize to 240pt |
| iPhone 13 / 14 / 15 | 390pt | Standard layout — 260pt carousel cards |
| iPhone 15 / 16 Pro | 393pt | Dynamic Island accommodated; nav adjusts |
| iPhone 15 / 16 Pro Max | 430pt | 280pt carousel cards, slightly more breathing room |
| iPad | 768pt+ | Split view: sidebar nav, 2-column merchant grid |

### Dynamic Type
- Merchant names, prices, body: scale fully across all sizes
- Category chips cap at 140% to prevent wrapping breakage
- Tab labels scale up to 14pt max
- Price numerals scale with the corresponding price label

### Orientation
- All primary tabs: **portrait-locked**
- Map tracking: **rotates to landscape** for a wider map view
- Menu item detail sheet: **portrait-locked**

### Touch Targets
- Primary CTAs: 52pt tall, full-width minus 16pt margins — huge tap zone
- Category chips: min 32pt height, 14pt horizontal padding
- Steppers: 28pt circles with 44pt effective hit area via padding
- Heart / Save / Share icons: 28pt visible, 44pt hit area

### Safe Area Handling
- Top: standard iOS safe area, nav bar + large title honor it
- Bottom: floating CTA hovers 16pt above home indicator; tab bar extends into indicator area
- Sides: content insets 16pt from edges; merchant cards in carousels "bleed" 16pt past the right edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Primary CTA: DoorDash Red (`#EB1700`)
- CTA pressed: `#C31500`
- Canvas: `#FFFFFF` (light) / `#191919` (dark)
- Text: `#191919` (light) / `#FFFFFF` (dark)
- Secondary text: `#757575` (light) / `#AFAFAF` (dark)
- Divider: `#E5E5E5` (light) / `#3A3A3A` (dark)
- Surface muted: `#F7F7F7`
- Rating orange: `#FF8000`
- $0 Delivery green: `#008B4A`
- Promo tint: `#FFEBE8`
- DashPass teal: `#006B82`
- Error: `#D1350F`

### Example Component Prompts
- "Create a SwiftUI DoorDash merchant card (260pt wide): 16:10 hero food photo at the top with 12pt corner radius and a white-scrim heart icon in the top-right corner. Below: merchant name in TT Norms Pro 18pt weight 700, rating row showing orange star (#FF8000), rating number, '·' separator, delivery time in minutes, '·' separator, and delivery fee ('$0 Delivery' in #008B4A when applicable). Apply rgba(0,0,0,0.06) 0 4px 12px shadow."
- "Build the floating checkout CTA: 52pt tall, pill-rounded (28pt radius), full-width minus 16pt margins, background #EB1700, pinned bottom with 16pt safe-area inset. Left-aligned label 'Checkout' in TT Norms Pro 16pt weight 700 white; right-aligned dynamic total like '$23.40' same typography. Leading pill with white bg and red item count number (10pt weight 700). Drop shadow rgba(0,0,0,0.16) 0 8px 24px."
- "Design a category chip row: horizontally scrolling TT Norms Pro 14pt weight 600 chips, padding 8pt × 14pt, corner radius 20pt. Inactive: background #F7F7F7, text #191919. Active: background #191919, text #FFFFFF. Each chip has a 16pt leading emoji (🍕 Pizza, 🍣 Sushi, 🥗 Healthy)."
- "Create a menu item row: 88pt × 88pt food thumbnail with 8pt corner radius on the right, stacked text block on the left with item name (TT Norms Pro 17pt w600), 2-line clamped description (13pt w400 #757575), and price (15pt w600). 0.5pt hairline divider between rows. Row highlights to #F7F7F7 on tap for 120ms."
- "Build an order tracking header: 240pt tall map with rounded 16pt corners, red (#EB1700) route polyline, red DoorDash pin at the destination. Driver card floating over the top-left of the map: 56pt circular driver photo, driver name TT Norms Pro 17pt w700, vehicle make/model 13pt w400 #757575, star rating row. ETA below in 28pt weight 700 right-aligned."

### Iteration Guide
1. Red (`#EB1700`) is the take-action color — it appears ONLY on things users should tap to commit
2. Photos are the content — every merchant must have a real 16:9 food image, never a placeholder
3. Weight 700 for all titles and prices; weight 600 for secondary actions; weight 400 for prose
4. Corner radius scales with commitment level: chips 20pt → cards 16pt → checkout 28pt
5. Carousels are organized by user intent ("Fastest", "$0 delivery", "Order it again") not by cuisine
6. Dark mode is `#191919` canvas, not true black — DoorDash keeps food photos appetizing with a slight warmth
7. The floating CTA is non-negotiable — it's the app's whole point
8. Rating orange and $0-fee green are the only semantic chromas beyond brand red
