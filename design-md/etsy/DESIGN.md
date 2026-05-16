# Design System Inspiration of Etsy (iOS)

## 1. Visual Theme & Atmosphere

Etsy's iOS app is a warm, handcrafted marketplace built on a soft cream canvas (`#FAF5EF`) that signals "handmade, not mass-produced" the moment it loads. Where most commerce apps go stark white, Etsy chooses a gentle paper-warm tone — the digital equivalent of a craft fair under string lights. Product photography is the hero: every listing leads with a large, color-rich maker photo, and the cream surround makes those photos feel like prints on a gallery wall rather than SKUs in a database. The interface is unhurried, tactile, and personal.

The accent system is a single confident burnt orange. Etsy Orange (`#F1641E`) owns the primary CTA ("Add to cart", "Buy it now"), the active tab indicator, sale badges, and the favorite-heart active state. The favorite heart is the emotional core of the app — Etsy is a wishlist-driven, browse-and-save experience — and it bounces and fills orange the moment you tap it. Beyond orange and the warm cream, the palette is restrained: white cards floating on cream, `#F1F1F1` grouped fills, `#E1DDD5` warm hairline dividers, near-black text. The warmth is the brand; the orange is the verb.

Typography is a friendly, slightly editorial sans (Etsy uses Graphik in production; substitute "Inter"). The hierarchy is approachable and shopping-forward: 12-28pt with weights at 400 / 500 / 700. The most expressive type moment is the listing detail — the item title at 22pt over a tall maker photo, with the price in confident 24pt bold and the review stars right beneath. Shop names and maker copy lean warm and human; metadata recedes into a soft gray.

**Key Characteristics:**
- Warm-cream canvas (`#FAF5EF`) — handmade, paper-warm, not stark white
- Etsy Orange (`#F1641E`) as the single accent — primary action, active tab, sale, favorites
- Favorite-heart — the emotional core; bounces and fills orange on tap
- Handmade product card — large maker photo, title, price, review stars, "Bestseller" tag
- Shop banner header — a maker's storefront banner + avatar + shop name
- Listing image gallery dots — full-bleed photo carousel with paged dots
- Bottom tab bar (Home / Search / You / Favorites / Cart) with orange active state
- Haptics on favorite-heart tap, add-to-cart, and gallery paging

## 2. Color Palette & Roles

### Primary
- **Etsy Orange** (`#F1641E`): Primary CTA, "Add to cart", active tab indicator, sale badge, favorite-heart active.
- **Orange Pressed** (`#D5571A`): Active/pressed state for orange CTAs and the heart on tap.
- **Orange Tint** (`#FDEDE4`): Soft orange wash behind sale callouts and selected chips.

### Canvas & Surface
- **Canvas Cream** (`#FAF5EF`): Primary warm canvas — the handmade paper tone.
- **Card White** (`#FFFFFF`): Product cards, sheets, and elevated surfaces floating on cream.
- **Surface** (`#F1F1F1`): Grouped fills, search-field background, segmented controls.
- **Divider** (`#E1DDD5`): Warm hairline dividers between rows and sections.
- **Surface Pressed** (`#EFEAE2`): Pressed state on cream list rows and tappable cards.

### Text
- **Text Primary** (`#222222`): Item titles, prices, headlines, primary UI text.
- **Text Secondary** (`#595959`): Shop names, metadata, descriptions, captions.
- **Text Tertiary** (`#8A8A8A`): Disabled labels, placeholders, very low-emphasis hints.

### Semantic
- **Favorite Active** (`#F1641E`): Filled favorite heart.
- **Favorite Idle** (`#FFFFFF` w/ `rgba(0,0,0,0.45)` backing): Unsaved heart over photos.
- **Star Filled** (`#222222`): Review star glyphs (Etsy uses near-black stars, not gold).
- **Sale Red** (`#A61A12`): "On sale" / discount price strike accent (deep brick, used sparingly).
- **Success Green** (`#258635`): "In your cart", order-confirmed states.
- **Error Red** (`#A61A12`): Form validation, checkout failure.

### Dark Mode (Limited Use)
Etsy's iOS app is warm-light-first. A dark variant exists for system-dark contexts; the canvas goes warm-dark and Etsy Orange stays identical — it reads well on both.
- **Dark Canvas** (`#1A1714`)
- **Dark Card** (`#241F1B`)
- **Dark Text Primary** (`#F2EDE6`)
- **Dark Text Secondary** (`#A8A096`)
- **Orange** (`#F1641E`) — unchanged across themes

## 3. Typography Rules

### Font Family
- **Primary**: `Graphik` (proprietary) — friendly, slightly editorial grotesque
- **Substitute**: `Inter` — closest free face for the warm grotesque tone
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Non-Latin**: System fallback per-script — Etsy ships globally

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 28pt | 700 | 1.15 | -0.4pt | Home / Favorites large nav title |
| Listing Title | Inter | 22pt | 700 | 1.25 | -0.2pt | Item name on listing detail |
| Section Header | Inter | 20pt | 700 | 1.2 | -0.2pt | "You may also like", "From this shop" |
| Price (Hero) | Inter | 24pt | 700 | 1.1 | -0.3pt | The listing price on detail |
| Card Title | Inter | 15pt | 500 | 1.35 | 0pt | Product title in grid card (2-line clamp) |
| Card Price | Inter | 16pt | 700 | 1.2 | -0.1pt | Price on a grid card |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Item description, shop story |
| Shop Name | Inter | 14pt | 500 | 1.3 | 0pt | Maker / shop name on card + detail |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Orange "Add to cart", "Buy it now" |
| Meta / Count | Inter | 13pt | 400 | 1.3 | 0pt | "1,284 sales", "Free shipping" |
| Badge | Inter | 11pt | 700 | 1.0 | 0.3pt | "BESTSELLER", "ETSY'S PICK" |
| Tab Label | Inter | 11pt | 500 | 1.0 | 0.2pt | Bottom tab bar labels |
| Review Count | Inter | 13pt | 500 | 1.2 | 0pt | "(1,284)" beside the stars |

### Principles
- **Weights concentrated at 400 / 500 / 700**: regular, medium, bold — medium does the warm mid-emphasis work
- **Editorial, human tone**: shop names and maker copy in medium weight, never shouty
- **Price is confident, not loud**: 24pt bold near-black, the stars sit immediately beneath
- **Negative tracking only on titles**: -0.4pt max; body sits at 0pt for readability
- **Orange is never body text** — orange is structural (action, active, sale, favorite) only

## 4. Component Stylings

### Buttons

**Primary CTA ("Add to cart" / "Buy it now")**
- Background: `#F1641E`
- Text: `#FFFFFF`
- Padding: 16pt vertical, 28pt horizontal
- Corner radius: 999pt (full pill — Etsy's CTAs are rounded and friendly)
- Font: Inter 16pt weight 700
- Pressed: background `#D5571A`, scale 0.98, with `.impactOccurred(.medium)` haptic
- Disabled: background `#E1DDD5`, text `#8A8A8A`

**Secondary Button ("Add to a list" / "Message seller")**
- Background: `#FFFFFF`
- Text: `#222222`
- Border: 1.5pt solid `#222222`
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 999pt
- Font: Inter 15pt weight 700
- Pressed: background `#F1F1F1`

**Favorite Heart**
- Size: 24pt glyph, 44pt hit area
- Idle (over photo): white outline heart on `rgba(0,0,0,0.45)` circular backing (36pt)
- Active: solid `#F1641E`
- Tap: scale 1.0 → 1.25 → 1.0 over 300ms spring bounce + success haptic, fill animates

**Text Button ("See all 240 items")**
- Font: Inter 15pt weight 700
- Color: `#222222`
- Underline on press only, 44pt hit area

### Cards & Containers

**Handmade Product Card (Grid)**
- Background: `#FFFFFF`, corner radius 12pt
- Photo: full-width, aspect 1:1, maker image with 12pt top corner radius
- Favorite heart top-right over photo (24pt, idle white)
- "Bestseller" tag top-left over photo (orange-tint pill) when applicable
- Structure: photo → title (15pt w500, 2-line clamp) → price (16pt w700) + optional strike → stars + "(1,284)" → shop name (14pt w500 `#595959`) → "Free shipping" meta
- Grid: 2-column, 12pt gutter, 16pt row gap
- Tap: card scale 0.98 + light haptic

**Listing Detail Hero**
- Full-bleed maker photo, aspect ~4:5, top of the screen
- Gallery dots bottom-center over the photo (6pt, active `#FFFFFF`)
- Floating circular 36pt back, share, favorite buttons on `rgba(0,0,0,0.45)`
- Photo-count pill bottom-right: "1 / 12" on `rgba(0,0,0,0.5)`
- Below photo: title 22pt w700, price 24pt w700, stars + review count, shop row

**Shop Banner Header**
- Banner strip: full-width maker banner image, ~120pt tall, 12pt bottom corners
- Shop avatar: 64pt circle, white 3pt ring, overlapping the banner bottom edge
- Shop name 20pt w700 + "1,284 sales · ★ 4.9" 13pt `#595959` below
- "Follow shop" pill (secondary outline) trailing

**Review Card**
- Background: `#FFFFFF`, divider `#E1DDD5` bottom
- Reviewer avatar 36pt circle + name 14pt w500 + date 13pt `#595959`
- Star row (16pt near-black stars) + review body 15pt w400 (4-line clamp + "Read more")
- Optional reviewer photo thumbnails (56pt, 8pt radius)

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(250,245,239,0.97)` with `.regularMaterial` blur (warm)
- Tabs: Home, Search, You, Favorites, Cart
- Icon size: 24pt
- Active: `#F1641E` icon + label (orange is the active indicator)
- Inactive: `#595959`
- Labels: Inter 11pt w500, always shown
- Active icon: filled SF Symbol; inactive: outlined
- Cart shows a count badge (orange dot) when non-empty
- Hairline top border `#E1DDD5`

**Top Nav (Home)**
- Height: 52pt + safe area
- Etsy wordmark left (orange, ~64pt), notification + cart icons right
- Search bar pinned below: full-width, `#FFFFFF` fill, 1pt `#E1DDD5` border, 999pt radius
- Compact + warm-blurred on scroll

**Listing Sticky Bar**
- Appears on scroll: 56pt, cream with blur, bottom-anchored
- Price (16pt w700) left + full-width-ish orange "Add to cart" CTA right

### Input Fields

**Search Bar**
- Background: `#FFFFFF`
- Height: 48pt
- Corner radius: 999pt (full pill)
- Border: 1pt `#E1DDD5`
- Leading SF Symbol `magnifyingglass` 18pt `#595959`
- Placeholder: "Search for anything", 15pt w400 `#595959`
- Focus: border `#222222` 1.5pt, trailing "Cancel" text button appears

**Filter Chip**
- Background: `#FFFFFF` default / `#FDEDE4` selected (with `#F1641E` 1.5pt border)
- Text: `#222222` (both states)
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 999pt
- Border: 1pt `#E1DDD5` default
- Font: Inter 14pt weight 500
- Examples: "Free shipping", "On sale", "Ready to ship", "Under $25"

### Distinctive Components

**Favorite Heart (the emotional core)**
- Outline heart idle, solid `#F1641E` active
- Bounce: 1.0 → 1.25 → 1.0 spring (damping 0.5) over 300ms on tap, success haptic
- Over photos: sits on a 36pt `rgba(0,0,0,0.45)` circular backing for contrast
- Appears on every product card, the listing hero, and search results — the save-driven heart of the app

**Handmade Warmth (the cream canvas)**
- The `#FAF5EF` canvas with white cards is the brand's signature surface treatment
- White cards cast a soft, warm-tinted shadow so they feel like prints lifted off paper
- Never replace the cream with stark white — the warmth is the differentiator

**Listing Image Gallery**
- Full-bleed paged photo carousel, 1:1 / 4:5 maker photos
- Paged dots bottom-center (6pt, inactive `rgba(255,255,255,0.5)`, active `#FFFFFF`)
- Swipe pages with snap + light haptic at each photo

**"Bestseller" / "Etsy's Pick" Tag**
- Orange-tint pill: `#FDEDE4` background, `#F1641E` text, 11pt w700 uppercase
- Padding: 4pt vertical, 10pt horizontal, 999pt radius
- Top-left over the product photo — social-proof at a glance

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard margin: 16pt horizontal, 24pt between sections
- Product grid: 12pt gutter, 16pt row gap

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Product grid: 2-column, 12pt gutter (the core browse layout)
- Horizontal carousels ("From this shop"): 16pt left inset, peek next card
- Listing detail: single column, full-bleed hero photo

### Whitespace Philosophy
- **Photo-forward warmth**: product photos get a generous 1:1 / 4:5 block; the cream surround gives them air
- **Tight info stacking**: title, price, stars, shop name pack into ~96pt under each photo
- **Detail hero is immersive**: a tall maker photo runs full-bleed before any chrome

### Border Radius Scale
- Square (0pt): warm hairline dividers
- Soft (8pt): inline thumbnails, reviewer photos
- Card (12pt): product cards, shop banner, sheets
- Comfortable (16pt): bottom sheets, modal cards
- Full Pill (999pt): primary CTA, filter chips, search bar, badges
- Circle (50%): favorite heart backing, avatars, gallery floating buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Cream canvas, list rows |
| Card (Level 1) | `rgba(120,90,50,0.10) 0 2px 10px` | White product cards lifted off the cream (warm-tinted shadow) |
| Floating Control (Level 2) | `rgba(0,0,0,0.20) 0 2px 6px` | Photo-overlay buttons (back, share, favorite) |
| Sheet (Level 3) | `rgba(80,60,40,0.16) 0 -8px 32px` | Bottom sheets, variation pickers |
| Sticky Nav | `rgba(120,90,50,0.08) 0 1px 0` + warm blur | Search bar / listing bar on scroll |
| Tab Bar Material | `.regularMaterial` over `rgba(250,245,239,0.97)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Shadows are soft and *warm-tinted* — not neutral gray but a low-opacity brown (`rgba(120,90,50,0.10)`) so white cards feel like prints resting on cream paper rather than floating in cold space. The only crisper shadows are the floating photo controls (0.20 opacity) so they stay legible over colorful maker photography. The cream canvas itself is flat — depth comes from the white cards lifting gently off it.

### Motion
- **Favorite-heart bounce**: scale 1.0 → 1.25 → 1.0 spring (damping 0.5) over 300ms, fill cross-fades to orange, success haptic
- **Add-to-cart**: button scale 0.98 + medium haptic; a small fly-to-cart animation of the photo thumbnail, cart badge bumps
- **Card tap**: scale 0.98 spring (damping 0.8) + light haptic
- **Gallery paging**: 60fps horizontal snap, dots update, light haptic per page
- **Sticky bar reveal**: cream bar slides up with blur as the hero scrolls off
- **Section reveal**: subtle fade + 8pt rise as carousels enter the viewport

## 7. Do's and Don'ts

### Do
- Use the warm cream canvas (`#FAF5EF`) — it is the handmade brand signature
- Float white product cards on the cream with a soft warm-tinted shadow
- Reserve Etsy Orange (`#F1641E`) for the primary action, active tab, sale, and favorites
- Make the favorite heart bounce and fill orange on tap — it is the emotional core
- Lead every product card and the listing detail with a large, color-rich maker photo
- Use near-black stars (`#222222`) for reviews — Etsy stars are not gold
- Keep CTAs full-pill and friendly, never sharp-cornered
- Show "Bestseller" / "Etsy's Pick" as an orange-tint pill for social proof
- Keep shop names and maker copy in medium weight — warm, human, never shouty

### Don't
- Don't replace the cream with stark white — the warmth is the differentiator
- Don't use orange for body text or decoration — it is structural only
- Don't render review stars in gold/yellow — Etsy uses near-black stars
- Don't use cold neutral-gray shadows — card shadows are warm-tinted brown
- Don't add a second accent color — the single orange + warm cream is the identity
- Don't square the CTA corners — Etsy buttons are full-pill and friendly
- Don't use thin font weights — stay at 400 / 500 / 700
- Don't shrink product photos — the maker photography is the hero
- Don't skip the heart bounce — the tactile favorite is core to the experience

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-col grid with tighter gutters; card text clamps sooner |
| iPhone 13/14/15 | 390pt | Standard 2-col product grid, full 1:1 photos |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger photos, listing hero grows to 4:5 full-bleed |
| iPad | 768pt+ | 3-4 column product grid, listing detail in a split layout |

### Dynamic Type
- Listing title, price, body: full scale
- Card title: scales with a 2-line clamp before truncation
- Badge text, tab labels, gallery photo-count pill: fixed
- Review star size: fixed (geometric unit)

### Orientation
- Home / Search / Favorites: **portrait-locked**
- Listing image gallery: **rotates to landscape** for full-screen photo viewing
- Cart / checkout: **portrait-locked**

### Touch Targets
- Primary CTA: 48pt+ tall
- Favorite heart: 44pt effective hit (over a 36pt backing on photos)
- Gallery floating controls: 44pt hit area
- Tab bar icons: 24pt glyph, 44pt effective hit
- Product card: entire card tappable

### Safe Area Handling
- Top: wordmark nav + search bar respect the safe area / Dynamic Island
- Bottom: tab bar floats above the home indicator with warm blur
- Sides: 16pt content insets; listing hero photo bleeds full-width

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FAF5EF`
- Card: `#FFFFFF`
- Surface: `#F1F1F1`
- Divider: `#E1DDD5`
- Text primary: `#222222`
- Text secondary: `#595959`
- Etsy Orange (action / favorite): `#F1641E`
- Orange pressed: `#D5571A`
- Orange tint (badges): `#FDEDE4`
- Star filled: `#222222`
- Error / sale red: `#A61A12`

### Example Component Prompts
- "Create a SwiftUI Etsy product card on a `#FAF5EF` cream canvas: a `#FFFFFF` card, 12pt corner radius, soft warm-tinted shadow. A full-width 1:1 maker photo with 12pt top corners, an idle white favorite heart top-right on a 36pt `rgba(0,0,0,0.45)` circular backing, and an orange-tint 'BESTSELLER' pill top-left (`#FDEDE4` bg, `#F1641E` text, 11pt w700 uppercase). Below: title 'Hand-thrown ceramic mug' Inter 15pt weight 500 (2-line clamp), price '$38.00' 16pt weight 700 `#222222`, a row of near-black stars + '(1,284)' 13pt w500, then shop name 'ClayAndEmber' 14pt weight 500 `#595959`, then 'Free shipping' 13pt `#595959`."
- "Build an Etsy favorite heart: a 24pt heart glyph, outline idle and solid `#F1641E` active. On tap it bounces scale 1.0 → 1.25 → 1.0 with a spring (damping 0.5) over 300ms, the fill cross-fades to orange, and a success haptic fires. Over photos, place it on a 36pt `rgba(0,0,0,0.45)` circular backing for contrast."
- "Design the Etsy primary CTA: a full-pill button, `#F1641E` background, label 'Add to cart' in Inter 16pt weight 700 white, 16pt vertical / 28pt horizontal padding. Pressed: background `#D5571A`, scale 0.98, medium haptic."
- "Create an Etsy shop banner header: a full-width ~120pt maker banner image with 12pt bottom corners, a 64pt circular shop avatar with a 3pt white ring overlapping the banner bottom edge, shop name 'ClayAndEmber' 20pt weight 700 `#222222`, '1,284 sales · ★ 4.9' 13pt `#595959`, and a 'Follow shop' secondary outline pill trailing."
- "Build an Etsy listing detail hero: a full-bleed maker photo at ~4:5, paged gallery dots bottom-center (6pt, active `#FFFFFF`), floating circular 36pt back / share / favorite buttons on `rgba(0,0,0,0.45)`, and a '1 / 12' photo-count pill bottom-right on `rgba(0,0,0,0.5)`. Below the photo: title 22pt weight 700 `#222222`, price '$38.00' 24pt weight 700, near-black review stars + '(1,284)', then the shop row."

### Iteration Guide
1. Canvas is warm cream `#FAF5EF` — the handmade signature, never stark white
2. White product cards float on the cream with a soft warm-tinted (brown) shadow
3. Etsy Orange (`#F1641E`) only for the primary action, active tab, sale, and favorites — never body text
4. The favorite heart is the emotional core — it bounces (1.0→1.25→1.0) and fills orange on tap
5. Maker photography is the hero — lead every card and the listing detail with a large color-rich photo
6. Review stars are near-black (`#222222`), never gold
7. CTAs are full-pill and friendly — never sharp-cornered
8. Keep shop/maker copy in medium weight (500) — warm, human, never shouty
