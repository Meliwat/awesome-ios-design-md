# Design System Inspiration of Uber Eats (iOS)

## 1. Visual Theme & Atmosphere

Uber Eats' iOS app is a bright, photo-first marketplace built on a pure white canvas (`#FFFFFF`) where food photography does the selling. The interface is intentionally clean and high-contrast: white surfaces, near-black type, and a single confident green accent. Restaurant cards are large, image-led tiles — a wide hero photo with the name, rating, ETA, and delivery fee stacked underneath in tight, legible rows. The whitespace is generous so the food imagery never competes with chrome, and elevated elements (the sticky cart bar, sheets) lift off the white with soft, low shadows rather than borders.

The accent system is a single green. Uber Eats Green (`#06C167`) — the brand's fresh, slightly cool green — owns every primary action: the "Add to cart" button, the sticky cart bar, active filter pills, the "Track order" CTA, and selected states. Near-black (`#000000`) carries titles and the secondary "Schedule" / outline actions, and a mid gray (`#6B6B6B`) handles metadata (ratings, ETA, fees). There is no second brand hue — against full-color food photography and a white canvas, the green reads instantly as "order." A dark mode is fully supported (inverting to a near-black canvas) but the default and primary experience is light.

Typography is Uber Move (Uber's brand typeface), rendered here with an Inter fallback that keeps its tight, confident, slightly geometric character. The hierarchy is compact and commercial: 11-30pt at weights 400 / 500 / 700. There are no decorative display fonts — the food photos and prices are the visual interest. Type merchandises: restaurant names at 17-18pt weight 700, section headers at 20-22pt weight 700, metadata (4.8 · 25 min · $0.99 Delivery Fee) at 13-14pt weight 400 in gray, the price/CTA in 16pt weight 700. The most expressive type moment is the restaurant header, where the name sits at 24-30pt weight 700 over the hero photo's darkened lower edge.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) — photo-first, generous whitespace, dark mode supported
- Uber Eats Green (`#06C167`) as the single accent — every primary action and selected state
- Photo-first restaurant card — wide hero image + name + rating + ETA + delivery fee
- Category pill row — horizontally-scrolling cuisine/filter pills under the search bar
- Sticky cart bar — a green bar pinned to the bottom showing item count + total + "View cart"
- Live map order tracking — full-screen map with the courier moving along the route (the signature post-order screen)
- Promo banner — a rounded full-bleed offer card slotted into the feed
- Uber Move typeface (Inter fallback), weights 400/500/700
- Cart count bump on add; map courier marker animates along the route
- Bottom tab bar: Home, Browse, Search, Cart, Account

## 2. Color Palette & Roles

### Primary
- **Uber Eats Green** (`#06C167`): Primary "Add" / "Place order" CTA, sticky cart bar, "Track order" CTA, active filter pill, selected radio/checkbox, in-cart badge.
- **Green Pressed** (`#05A658`): Pressed/active state for green CTAs and the cart bar.
- **Green Tint** (`#E7F8EF`): Subtle success/selected backgrounds (selected option row, "Saved" toast).

### Canvas & Surfaces (Light — default)
- **Canvas** (`#FFFFFF`): Primary app background — pure white, photo-first.
- **Surface** (`#F3F3F3`): Card fills with no photo, search bar, skeleton loaders, pressed list rows.
- **Surface 2** (`#EEEEEE`): Higher elevation neutral — chip default, segmented control track.
- **Divider** (`#E8E8E8`): 1pt separators between menu items and list rows.

### Text (Light)
- **Text Primary** (`#000000`): Restaurant names, screen headers, prices, primary labels.
- **Text Secondary** (`#6B6B6B`): Ratings, ETA, delivery fee, descriptions, metadata.
- **Text Tertiary** (`#A6A6A6`): Disabled labels, placeholder text, very low-emphasis captions.

### Dark Mode (Supported)
- **Dark Canvas** (`#000000`): Primary dark background.
- **Dark Surface** (`#1C1C1E`): Cards without photo, search bar, sheets.
- **Dark Surface 2** (`#2C2C2E`): Higher elevation, chip default.
- **Dark Divider** (`#2C2C2E`): Hairline separators.
- **Dark Text Primary** (`#FFFFFF`), **Dark Text Secondary** (`#A6A6A6`)
- **Uber Eats Green stays `#06C167`** — the green reads well on both light and dark; only neutrals invert.

### Semantic
- **Rating Star** (`#000000`): The star glyph in "4.8" is filled near-black (not yellow — Uber Eats keeps ratings monochrome and quiet).
- **Promo Green** (`#06C167`): "$0 Delivery Fee" / offer text reuses the brand green.
- **Surge / Busy Amber** (`#FF8A00`): "Busy — longer wait" notice on a restaurant.
- **Error Red** (`#E11900`): Form errors, payment-failed, item-unavailable.

## 3. Typography Rules

### Font Family
- **Primary**: `Uber Move` (Uber brand typeface) — tight, confident, slightly geometric grotesque
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for prices, ratings, ETA, and order totals

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Restaurant Header | Uber Move | 30pt | 700 | 1.1 | -0.4pt | Restaurant name on its detail header |
| Screen Title (Large) | Uber Move | 28pt | 700 | 1.15 | -0.4pt | "Account", "Cart" large nav title |
| Section Header | Uber Move | 22pt | 700 | 1.2 | -0.3pt | "Featured", "Popular near you" |
| Subsection | Uber Move | 20pt | 700 | 1.2 | -0.3pt | Menu category ("Burgers") |
| Restaurant Name (card) | Uber Move | 17pt | 700 | 1.25 | -0.1pt | Name under a restaurant card photo |
| Menu Item Title | Uber Move | 16pt | 500 | 1.3 | -0.1pt | Item name in a menu row |
| Body | Uber Move | 15pt | 400 | 1.45 | 0pt | Item description, restaurant info |
| Price / CTA | Uber Move | 16pt | 700 | 1.0 | 0pt | "$12.49", "Add to cart" label |
| Metadata | Uber Move | 14pt | 400 | 1.3 | 0pt | "4.8 · 25 min · $0.99 Delivery Fee" |
| Pill / Tag | Uber Move | 14pt | 500 | 1.0 | 0pt | Category pill label |
| Caption | Uber Move | 13pt | 400 | 1.3 | 0pt | "$0.99 Delivery Fee", small notes |
| Label (UPPER) | Uber Move | 11pt | 700 | 1.2 | 0.6pt | "DELIVERY · 25 MIN" eyebrow |
| Button (Primary) | Uber Move | 16pt | 700 | 1.0 | 0.2pt | Green "Add" / "Place order" |
| Tab Label | Uber Move | 10pt | 500 | 1.0 | 0.2pt | Bottom tab bar labels |
| Cart Badge | Uber Move | 12pt | 700 | 1.0 | 0pt | Item count on cart bar/icon |

### Principles
- **Weights concentrated at 400 / 500 / 700**: Regular for body/metadata, medium for menu items/pills, bold for names, headers, prices, and CTAs — no thin, no semibold-as-light, no black
- **Commercial clarity**: No decorative display type; food photography and prices are the visual interest
- **Negative tracking on big type, neutral on small**: -0.4pt at 30pt easing to 0pt at body
- **All-caps only for eyebrows**: "DELIVERY · 25 MIN" — never for restaurant names or menu items
- **Monochrome ratings**: the rating star and number are near-black, not colored — Uber Eats keeps merchandising quiet so green = action stands alone

## 4. Component Stylings

### Buttons

**Primary CTA (Add / Place Order — The Green Button)**
- Shape: Rounded rectangle, height 52pt, full-width within content margins
- Background: `#06C167`
- Text: `#FFFFFF`
- Corner radius: 12pt (Uber Eats uses a friendlier radius than most)
- Font: Uber Move 16pt weight 700, letter-spacing 0.2pt; may show "Add to cart · $12.49" with price right-aligned
- Shadow: none by default; the sticky cart-bar variant uses `rgba(0,0,0,0.12) 0 -2px 12px` above it
- Pressed: background `#05A658`, scale 0.98, with `.impactOccurred(.light)` haptic

**Secondary Button ("Schedule" / "Add to favorites")**
- Background: `#FFFFFF` (light) / `#1C1C1E` (dark)
- Text: `#000000` (light) / `#FFFFFF` (dark)
- Border: 1pt solid `#E8E8E8` (light) / `#2C2C2E` (dark)
- Height: 52pt, corner radius 12pt
- Font: Uber Move 16pt weight 700
- Pressed: background `#F3F3F3`, scale 0.98

**Quantity Stepper (− 1 +)**
- Pill, height 36pt, background `#F3F3F3`
- `−` / `+` glyphs near-black 18pt at 36pt circular hit zones; count centered 16pt w700
- `+` tap: green flash on the count + cart-count bump elsewhere

**Icon Actions (Heart/favorite, Share, Info)**
- Size: 22pt glyph, 44pt hit area
- Default: `#000000` (light) / `#FFFFFF` (dark)
- Active: favorite filled `#06C167`
- Often placed top-right over the restaurant hero photo on a `rgba(255,255,255,0.9)` 36pt circle

**Text Button ("See all")**
- Font: Uber Move 14pt weight 700
- Color: `#000000` with a trailing chevron, 44pt hit area, after each section header

### Cards & Containers

**Restaurant Card (photo-first)**
- Width: full-width minus 16pt margins (feed) or 280pt (horizontal "Featured" rail)
- Hero photo: 16:9, corner radius 12pt; optional `♥` top-right and a promo ribbon top-left ("$0 Delivery Fee" in `#06C167`)
- Below the photo (no card border, sits on white):
  - Restaurant name 17pt w700 `#000000`
  - Metadata row: ★ 4.8 (near-black) · "25 min" · "$0.99 Delivery Fee" — 14pt w400 `#6B6B6B`, "$0 Delivery Fee" segment in `#06C167`
- Press: photo scales 0.98, subtle; whole card tappable

**Category Pill Row**
- Horizontal scroll directly under the search bar
- Pills: icon + label, height 40pt, background `#F3F3F3` default / `#06C167` selected (white text + green fill)
- Corner radius: 500pt (full pill), 8pt gap, peek last pill at the right edge

**Menu Item Row**
- Height: auto (~ 96-112pt)
- Layout: left text column (item name 16pt w500, description 14pt w400 `#6B6B6B` 2-line clamp, price 15pt w400) → right 88pt × 88pt rounded item photo (12pt radius) with a `+` 28pt circular button overlapping its bottom-right corner
- Background: `#FFFFFF`; pressed `#F3F3F3`
- Divider: 1pt `#E8E8E8` between rows

**Sticky Cart Bar**
- Pinned to the bottom above the tab bar (or above the home indicator on the menu screen)
- Full-width green `#06C167` bar, height 56pt, corner radius 12pt with 16pt side margins
- Left: white circular count badge "3"; Center: "View cart"; Right: total "$28.40" — all `#FFFFFF`, Uber Move 16pt w700
- Appears with a slide-up + spring when the first item is added; total animates on change
- Shadow above it: `rgba(0,0,0,0.12) 0 -2px 12px`

**Promo Banner**
- Full-bleed rounded card in the feed, 16pt radius, brand-tinted (`#E7F8EF` or a photo) with bold offer copy ("$0 Delivery Fee on your first order") and a green inline CTA chip

**Restaurant Header**
- Full-bleed hero photo, height ~ 240pt, darkened at the bottom (`rgba(0,0,0,0) → rgba(0,0,0,0.45)`)
- Restaurant name 30pt w700 `#FFFFFF` bottom-left over the photo
- Below (on white): rating · delivery time · fee row, then an info chip row ("More info", "Group order")

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(255,255,255,0.96)` (light) / `rgba(0,0,0,0.96)` (dark) with `.regularMaterial` blur, hairline top divider `#E8E8E8`
- Tabs: Home, Browse, Search, Cart, Account
- Icon size: 24pt
- Active color: `#000000` (light) / `#FFFFFF` (dark) — the active tab is near-black/white, NOT green (green is reserved for action surfaces)
- Inactive: `#A6A6A6`
- Cart tab: green count badge `#06C167` with white number when items present
- Labels: Uber Move 10pt w500, always shown
- Active tab icon: filled SF Symbol; inactive: outlined

**Top Bar (Home)**
- Address selector pill top-left ("Deliver to · Home ▾", 16pt w700) — tappable
- Profile avatar (28pt circular) top-right
- Below: a full-width search bar (`#F3F3F3`, 48pt, 12pt radius, leading `magnifyingglass`)

**Live Map Order Tracking (signature)**
- Full-screen map (light map style) with: the route polyline in near-black, a green courier marker that animates along it, the restaurant pin and the destination pin
- A draggable bottom sheet (corner radius 16pt, white) over the map: ETA headline ("Arriving in 12 min", 22pt w700), a green progress stepper (Preparing → Picked up → On the way → Delivered), courier card (avatar + name + vehicle + message/call buttons), and order summary
- The courier marker eases between location updates; ETA text updates in place

### Input Fields

**Search Bar**
- Background: `#F3F3F3` (light) / `#1C1C1E` (dark)
- Height: 48pt, corner radius 12pt
- Leading SF Symbol `magnifyingglass` 18pt `#6B6B6B`
- Placeholder: "Search Uber Eats", 16pt w400 `#6B6B6B`
- Text color: `#000000` (light) / `#FFFFFF` (dark)
- Focus: no border; a subtle `#06C167` caret and a trailing "Cancel" text button appears

**Selected Option Row (menu customization)**
- Radio/checkbox: unselected 22pt circle 1.5pt `#A6A6A6`; selected filled `#06C167` with white check
- Selected row background tints to `#E7F8EF`

### Distinctive Components

**Live Map Order Tracking**
- The signature Uber Eats screen: a full-screen map where a green courier marker eases smoothly along a near-black route polyline between the restaurant and the destination, beneath a draggable white sheet with a green status stepper and live ETA. Movement on the map IS the feedback — "where's my food" answered visually and in real time. This is the brand's defining post-order moment.

**Photo-First Restaurant Card**
- A wide 16:9 food photo (12pt radius) with the name, monochrome rating, ETA, and delivery fee in tight rows beneath — no card border, sitting directly on white. The photo sells; the metadata reassures.

**Sticky Cart Bar**
- A persistent green bar that slides up the moment the first item is added, always showing count + total + "View cart" — the running tally never leaves the screen while you browse a menu.

**Category Pill Row + Cart-Count Bump**
- The horizontally-scrolling cuisine pills (green when selected) drive discovery; adding an item triggers a quick scale "bump" on the cart count (and the cart tab badge) for tactile confirmation.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard margin: 16pt horizontal
- Between feed cards: 24pt vertical
- Between sections: 32pt vertical
- Pill gap: 8pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Feed: single-column full-width restaurant cards
- "Featured" rails: horizontal scroll, 280pt cards, peek last at right edge
- Menu: single-column item rows with a fixed 88pt photo on the right
- Map screen: full-bleed map with a draggable sheet (initial peek ~ 280pt)

### Whitespace Philosophy
- **Photo-led generosity**: Cards get 24pt vertical breathing room so each food photo stands alone — the white canvas is a deliberate gallery
- **Tight metadata, loose layout**: The rating/ETA/fee row is compact (single line) under generous photo spacing
- **The cart bar owns the bottom**: It floats above content with a soft top shadow — the one persistently elevated element

### Border Radius Scale
- Sharp (0pt): Full-bleed restaurant hero photo, full-screen map
- Soft (8pt): Category pills (when not full-pill), small chips
- Friendly (12pt): Restaurant photos, primary buttons, search bar, item photos, cards
- Comfortable (16pt): Bottom sheets, promo banners, map tracking sheet
- Full Pill (500pt): Category pills, address selector, filter pills
- Circle (50%): Avatars, the `+` add button, courier map marker, cart count badge

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, restaurant cards (sit on white), list rows |
| Pill / Chip (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` | Selected category pill, floating filter |
| Card (Level 2) | `rgba(0,0,0,0.08) 0 4px 16px` | Modals, the address-picker sheet |
| Sticky Cart Bar | `rgba(0,0,0,0.12) 0 -2px 12px` | Green cart bar floating above content |
| Map Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 28px` | Draggable order-tracking sheet over the map |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 28px` | Bottom sheets, customization modals |
| Tab Bar Material | `.regularMaterial` over `rgba(255,255,255,0.96)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On the white canvas, shadows are soft, neutral, and low-opacity (0.06-0.16) — just enough to lift the cart bar and sheets off the page without a heavy "Material" drop. Restaurant cards intentionally have NO shadow or border; the food photo's own framing provides separation. Elevation is reserved for things that float over content (cart bar, sheets, map sheet) — everything in the scroll is flat.

### Motion
- **Add to cart**: the item's `+` flashes green, the cart count + cart-tab badge scale 1.0 → 1.25 → 1.0 over 280ms ("bump"), `.impactOccurred(.light)`
- **Sticky cart bar appear**: slides up 56pt + spring (damping 0.75) on first add; total cross-fades on change
- **Map courier marker**: eases between GPS updates with a 1.0s ease-in-out translate along the polyline; the route draws in on first load
- **Pill select**: background fills green over 150ms; pill scales 0.97 on press
- **Card press**: photo scales 0.98 over 150ms ease-out
- **Sheet drag**: rubber-band physics with snap points (peek / half / full)

## 7. Do's and Don'ts

### Do
- Use a pure white canvas (`#FFFFFF`) by default — photo-first, gallery-like whitespace (dark mode inverts neutrals only)
- Reserve Uber Eats Green (`#06C167`) for primary actions and selected states — cart bar, Add, Track, active pill
- Keep the same green (`#06C167`) in dark mode — only neutrals invert
- Let restaurant cards sit borderless and shadowless on white — the food photo provides the framing
- Keep ratings monochrome (near-black star) — quiet merchandising so green = action stands out
- Keep the sticky cart bar persistent with count + total + "View cart" while browsing a menu
- Make the live map the post-order hero — green courier marker easing along a near-black route
- Use a friendly 12pt radius on buttons/photos and 16pt on sheets — Uber Eats is softer than most
- Make the active tab near-black/white (NOT green) — green is for action surfaces only

### Don't
- Don't default to a dark canvas — the brand is bright and photo-first (dark mode is a supported alternate, not the default)
- Don't add a second accent hue — the single green is the entire action language
- Don't color the rating star yellow — Uber Eats keeps it monochrome and quiet
- Don't put borders/shadows on restaurant cards — they sit flat on white; the photo frames itself
- Don't use thin or black weights — Uber Move here is 400 / 500 / 700 only
- Don't make the active tab green — that dilutes green's "this is an action" meaning
- Don't use sharp corners on buttons/photos — the friendly 12pt radius is part of the brand feel
- Don't hide the cart total while browsing — the running tally must stay on screen
- Don't over-animate the map — the courier marker eases smoothly (≈1s), it doesn't jump or bounce

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Featured rail cards 240pt; full-width feed cards |
| iPhone 13/14/15 | 390pt | Standard 280pt featured cards |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the address/search bar |
| iPhone 15/16 Pro Max | 430pt | Larger hero photos; map sheet peek taller |
| iPad | 768pt+ | 2-column feed grid; map screen = map left + tracking panel right (split) |

### Dynamic Type
- Restaurant names, menu item titles, descriptions, ETA headline: full scale
- Metadata / pill labels / delivery-fee captions: scales but clamps at +2 steps (single-line layout-sensitive)
- Primary CTA label: scales to 18pt max; button height fixed at 52pt
- Tab labels, cart badge, map ETA chip: fixed (never scale)

### Orientation
- Home / Browse / Search / Restaurant / Cart: **portrait-locked**
- Live map tracking: portrait (map fills, sheet draggable)
- iPad: supports landscape split (feed/menu + detail; map + tracking panel)

### Touch Targets
- Primary CTA / cart bar: 52-56pt height, full-width — impossible to miss
- Restaurant cards: full-card tappable
- `+` add button / quantity stepper: 36-44pt circular hit
- Map sheet grabber: 44pt drag zone
- Tab bar icons: 24pt icon, 44pt effective hit

### Safe Area Handling
- Top: address/search bar respects safe area / Dynamic Island
- Bottom: tab bar floats above home indicator; the sticky cart bar sits just above the tab bar (or above the home indicator on menu screens); the map sheet respects the home indicator at its peek
- Sides: 16pt content margins; featured rails bleed to the right edge intentionally

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Surface: `#F3F3F3`
- Surface 2: `#EEEEEE`
- Divider: `#E8E8E8`
- Primary text: `#000000`
- Secondary text: `#6B6B6B`
- Tertiary text: `#A6A6A6`
- Uber Eats Green (action): `#06C167`
- Green pressed: `#05A658`
- Green tint (selected bg): `#E7F8EF`
- Dark canvas / surface: `#000000` / `#1C1C1E`
- Error red: `#E11900`

### Example Component Prompts
- "Create a SwiftUI Uber Eats photo-first restaurant card: full-width 16:9 food photo with 12pt corner radius, a heart icon top-right on a rgba(255,255,255,0.9) 36pt circle, and a '$0 Delivery Fee' ribbon top-left in #06C167. Below the photo (no border, on white): name 'Sunrise Diner' in Uber Move 17pt weight 700 #000000, then a metadata row '★ 4.8 · 25 min · $0.99 Delivery Fee' in 14pt weight 400 #6B6B6B (the star near-black, not yellow). Photo scales 0.98 on press."
- "Build the Uber Eats sticky cart bar: pinned above the tab bar, full-width green #06C167 bar with 16pt side margins, 56pt tall, 12pt corner radius, shadow rgba(0,0,0,0.12) 0 -2px 12px above it. Left: a white circular badge '3'; center: 'View cart'; right: '$28.40' — all #FFFFFF Uber Move 16pt weight 700. It slides up 56pt with a spring (damping 0.75) when the first item is added; the total cross-fades on change."
- "Design the Uber Eats live map order-tracking screen: a full-screen light-style map with a near-black route polyline, a green #06C167 circular courier marker that eases along it (1s ease-in-out between updates), restaurant and destination pins. Over it, a draggable white sheet (16pt top radius) with 'Arriving in 12 min' in Uber Move 22pt weight 700, a green 4-step progress stepper (Preparing → Picked up → On the way → Delivered), and a courier card with avatar, name, vehicle, and message/call buttons."
- "Create the Uber Eats menu item row: left column with item name 'Double Smash Burger' in Uber Move 16pt weight 500 #000000, a 2-line description in 14pt weight 400 #6B6B6B, and '$12.49' in 15pt weight 400; right side an 88×88pt food photo with 12pt radius and a green #06C167 28pt circular '+' button overlapping its bottom-right corner. 1pt #E8E8E8 divider below; pressed background #F3F3F3. Tapping '+' bumps the cart count (scale 1.0 → 1.25 → 1.0)."
- "Build the Uber Eats bottom tab bar: 52pt + safe area, background rgba(255,255,255,0.96) with regularMaterial blur and a hairline #E8E8E8 top divider. Tabs Home, Browse, Search, Cart, Account. Active icon + label near-black #000000 (NOT green), inactive #A6A6A6, filled SF Symbol when active. Cart tab shows a green #06C167 circular count badge with a white number when items are present. Labels Uber Move 10pt weight 500."

### Iteration Guide
1. Canvas is pure white `#FFFFFF` by default — photo-first, gallery whitespace (dark mode inverts neutrals only)
2. Uber Eats Green (`#06C167`) is the single accent for actions/selected — and it stays the same hue in dark mode
3. Restaurant cards sit borderless + shadowless on white — the food photo provides its own framing
4. Ratings are monochrome (near-black star, not yellow) — quiet merchandising so green = action stands alone
5. The sticky green cart bar (count + "View cart" + total) is persistent while browsing a menu
6. The live map is the post-order hero — green courier marker easing (~1s) along a near-black route under a draggable sheet
7. Friendly radii: 12pt buttons/photos, 16pt sheets — softer than most apps; the active tab is near-black/white, NOT green
8. Typography is Uber Move (Inter fallback), weights 400/500/700 only — no thin, no black
