# Design System Inspiration of eBay (iOS)

## 1. Visual Theme & Atmosphere

eBay's iOS app is a clean, utilitarian white canvas (`#FFFFFF`) built for one thing: scanning a deep catalog of listings fast. Where boutique commerce apps lean editorial and warm, eBay leans dense and efficient — it is a global marketplace of 1.9 billion listings, and the UI is engineered for browse velocity. The signature visual is the listing card: a square product photo, a tight title, a confident price, and a status badge that tells you *how* you can buy it — "Buy It Now", an auction bid, or "Best Offer". The canvas stays white and quiet so the product photos and the buy-mechanic badges carry the screen.

The accent system is eBay's iconic four-color brand mark — red, blue, yellow, green — but the app uses them with discipline. eBay Blue (`#0064D2`) is the primary action and link color. eBay Red (`#E53238`) carries urgency: "Time left" countdowns, watch-heart active, price drops. Yellow (`#F5AF02`) and green (`#86B817`) appear as the brand-logo accents and occasional highlights (ratings, "eBay Refurbished"). The defining moment is the bid-vs-Buy-It-Now distinction: a listing's badge instantly signals whether you bid against others or buy outright, and the watch-heart lets you track an auction's clock. Beyond the four brand colors, the palette is grayscale: white cards, `#F7F7F7` fills, `#E5E5E5` hairlines, near-black text.

Typography is a neutral, highly legible sans (eBay uses Market Sans in production; substitute "Inter"). The hierarchy is dense and price-forward: 11-28pt with weights at 400 / 600 / 700. The most expressive type moment is the listing card price — a bold 17pt number, with the buy-mechanic badge and "Time left" immediately adjacent so a shopper can triage a listing in under a second. Titles wrap to two lines; metadata recedes into gray.

**Key Characteristics:**
- Clean white canvas (`#FFFFFF`) — dense, utilitarian, browse-velocity-first
- eBay's four-color brand mark — red / blue / yellow / green, used with discipline
- eBay Blue (`#0064D2`) as the primary action + link; eBay Red (`#E53238`) for urgency
- Listing card — square photo, title, price, bid vs Buy-It-Now badge, watch-heart
- Bid vs Buy-It-Now badges — the signature buy-mechanic distinction
- "Time left" countdown — red urgency timer on auction listings
- Search bar with scan/camera entry — eBay's catalog-first front door
- Bottom tab bar (Home / Saved / Notifications / My eBay / Selling) with blue active state
- Haptics on watch-heart toggle, bid placement, and tab switches

## 2. Color Palette & Roles

### Primary (eBay Four-Color Brand)
- **eBay Blue** (`#0064D2`): Primary CTA, links, active tab indicator, "Buy It Now" badge, selected states.
- **Blue Pressed** (`#0050A8`): Active/pressed state for blue CTAs.
- **eBay Red** (`#E53238`): "Time left" countdown, watch-heart active, price-drop badge, urgency.
- **eBay Yellow** (`#F5AF02`): Brand-mark accent, rating stars, "Top Rated" highlight.
- **eBay Green** (`#86B817`): Brand-mark accent, "eBay Refurbished" / positive-status chips.

### Canvas & Surface
- **Canvas White** (`#FFFFFF`): Primary canvas behind all listings.
- **Surface** (`#F7F7F7`): Grouped fills, search-field background, segmented controls.
- **Divider** (`#E5E5E5`): Hairline dividers between rows and listing cards.
- **Surface Pressed** (`#EFEFEF`): Pressed state on white list rows and tappable cards.

### Text
- **Text Primary** (`#191919`): Listing titles, prices, headlines, primary UI text.
- **Text Secondary** (`#707070`): Item specifics, seller info, metadata, captions.
- **Text Tertiary** (`#9B9B9B`): Disabled labels, placeholders, strikethrough originals.

### Semantic
- **Buy It Now** (`#0064D2`): "Buy It Now" badge text/border (blue = buy outright).
- **Auction / Bid** (`#191919`): "12 bids" / "Place bid" — neutral near-black (the contrast with blue is the signal).
- **Time Left** (`#E53238`): Auction countdown when < 24h; otherwise `#707070`.
- **Watch Active** (`#E53238`): Filled watch-heart.
- **Best Offer** (`#86B817`): "or Best Offer" accent.
- **Free Shipping** (`#258635`): "Free shipping" positive label.
- **Error Red** (`#C7000B`): Form validation, checkout failure (a deeper, non-brand red).

### Dark Mode (Limited Use)
eBay's iOS app is light-first. A dark variant exists for system-dark contexts; the four brand colors stay identical — they are equity-locked.
- **Dark Canvas** (`#0F0F0F`)
- **Dark Surface** (`#1C1C1E`)
- **Dark Text Primary** (`#F2F2F2`)
- **Dark Text Secondary** (`#A0A0A0`)
- **Blue** (`#0064D2`) / **Red** (`#E53238`) — unchanged across themes

## 3. Typography Rules

### Font Family
- **Primary**: `Market Sans` (proprietary) — neutral, highly legible grotesque
- **Substitute**: `Inter` — closest free face for the neutral grotesque tone
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Non-Latin**: System fallback per-script — eBay ships globally

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 28pt | 700 | 1.15 | -0.4pt | Home / My eBay large nav title |
| Listing Title (Detail) | Inter | 22pt | 700 | 1.3 | -0.2pt | Item title on listing detail |
| Section Header | Inter | 20pt | 700 | 1.2 | -0.2pt | "Similar items", "Sponsored" |
| Price (Hero) | Inter | 24pt | 700 | 1.1 | -0.3pt | The listing price on detail |
| Card Price | Inter | 17pt | 700 | 1.2 | -0.1pt | Price on a listing card |
| Card Title | Inter | 15pt | 400 | 1.35 | 0pt | Listing title in card (2-line clamp) |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Item description, return policy |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Blue "Buy It Now", "Add to cart" |
| Badge | Inter | 12pt | 700 | 1.0 | 0.2pt | "BUY IT NOW", "12 BIDS" |
| Meta / Specifics | Inter | 13pt | 400 | 1.3 | 0pt | "Brand New · Free returns", seller |
| Time Left | Inter | 13pt | 700 | 1.2 | 0pt | "1d 4h left" countdown |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Condition Tag | Inter | 11pt | 600 | 1.0 | 0.3pt | "BRAND NEW", "PRE-OWNED" |

### Principles
- **Weights concentrated at 400 / 600 / 700**: regular, semibold, bold — no thin, no black
- **Price-forward density**: the card price is bold 17pt, sitting beside the buy-mechanic badge
- **Neutral, no-nonsense tone**: titles in regular weight, prices/badges carry the emphasis
- **Negative tracking only on titles/prices**: -0.4pt max; body sits at 0pt
- **Color carries the buy-mechanic** — blue "Buy It Now" vs neutral "X bids" is the key contrast

## 4. Component Stylings

### Buttons

**Primary CTA ("Buy It Now" / "Add to cart")**
- Background: `#0064D2`
- Text: `#FFFFFF`
- Padding: 15pt vertical, 28pt horizontal
- Corner radius: 999pt (full pill — eBay's modern CTAs are rounded)
- Font: Inter 16pt weight 700
- Pressed: background `#0050A8`, scale 0.98, with `.impactOccurred(.medium)` haptic
- Disabled: background `#E5E5E5`, text `#9B9B9B`

**Secondary / Bid Button ("Place bid")**
- Background: `#FFFFFF`
- Text: `#0064D2`
- Border: 1.5pt solid `#0064D2`
- Padding: 13pt vertical, 24pt horizontal
- Corner radius: 999pt
- Font: Inter 15pt weight 700
- Pressed: background `rgba(0,100,210,0.06)`

**Watch Heart**
- Size: 24pt glyph, 44pt hit area
- Idle: outline heart `#191919` (on cards) or white on `rgba(0,0,0,0.4)` (over photos)
- Active: solid `#E53238`
- Toggle: scale 1.0 → 1.2 → 1.0 over 260ms + success haptic, fill animates

**Text Button ("See all 1,284 results")**
- Font: Inter 15pt weight 700
- Color: `#0064D2`
- Underline on press only, 44pt hit area

### Cards & Containers

**Listing Card (Grid / List)**
- Background: `#FFFFFF`, corner radius 8pt, 1pt `#E5E5E5` border (list) or borderless grid
- Photo: square (1:1), product image, 8pt corner radius
- Watch heart top-right over photo (24pt)
- Structure: photo → title (15pt w400, 2-line clamp) → price (17pt w700) + optional strike original → buy-mechanic badge ("Buy It Now" blue / "12 bids" neutral) → "or Best Offer" → "Free shipping" + "Time left" (red if < 24h)
- Grid: 2-column, 12pt gutter; list rows full-width with hairline divider
- Tap: card scale 0.98 + light haptic

**Listing Detail Hero**
- Square photo carousel, full-width, paged dots bottom-center
- Floating circular 36pt back / share / watch buttons on `rgba(0,0,0,0.4)`
- Photo-count pill bottom-right: "1 / 9" on `rgba(0,0,0,0.5)`
- Below: title 22pt w700, price 24pt w700, buy-mechanic block, condition tag, seller card

**Buy-Mechanic Block (Detail)**
- For "Buy It Now": large blue "Buy It Now" pill + outline "Add to cart" + "Make offer"
- For Auction: current bid + "12 bids" + a red "Time left: 1d 4h" + "Place bid" + "Set max bid"
- A segmented hint chip row: "Buy It Now" | "or Best Offer" when both apply

**Seller Card**
- Background: `#FFFFFF`, divider `#E5E5E5`
- Seller name 15pt w700 + feedback "99.2% positive (12,481)" 13pt `#707070`
- Yellow rating stars (`#F5AF02`) + "Top Rated Plus" green badge when applicable
- "Visit store" / "Contact seller" secondary outline buttons

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.97)` with `.regularMaterial` blur
- Tabs: Home, Saved, Notifications, My eBay, Selling
- Icon size: 24pt
- Active: `#0064D2` icon + label (blue is the active indicator)
- Inactive: `#707070`
- Labels: Inter 11pt w600, always shown
- Active icon: filled SF Symbol; inactive: outlined
- Notifications shows a red count badge when non-empty
- Hairline top border `#E5E5E5`

**Top Nav (Home)**
- Height: 52pt + safe area
- eBay multicolor wordmark left (~56pt), cart + notification icons right
- Search bar pinned below: full-width, `#F7F7F7` fill, 1pt `#E5E5E5` border, 8pt radius, with a trailing camera/scan icon
- Compact + blurred on scroll

**Listing Sticky Bar**
- Appears on scroll: 56pt, white with blur, bottom-anchored
- Price (17pt w700) left + blue "Buy It Now" / "Place bid" CTA right

### Input Fields

**Search Bar**
- Background: `#F7F7F7`
- Height: 44pt
- Corner radius: 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#707070`
- Trailing SF Symbol `camera` 18pt `#0064D2` (visual search / scan)
- Placeholder: "Search for anything", 15pt w400 `#707070`
- Focus: background `#FFFFFF`, 1.5pt border `#0064D2`, "Cancel" appears

**Filter Chip**
- Background: `#FFFFFF` default / `#0064D2` selected
- Text: `#191919` default / `#FFFFFF` selected
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 999pt
- Border: 1pt `#E5E5E5` default
- Font: Inter 14pt weight 600
- Examples: "Buy It Now", "Auction", "Free shipping", "Brand New", "Best Match ▾"

### Distinctive Components

**Bid vs Buy-It-Now Badge (the signature)**
- "Buy It Now": `#0064D2` text on `rgba(0,100,210,0.10)` fill OR a 1pt blue-bordered pill — signals buy outright
- "Auction" / "X bids": `#191919` text on `#F7F7F7` fill — neutral, signals you bid against others
- "or Best Offer": appended `#86B817` text
- Padding: 4pt vertical, 8pt horizontal, 4pt radius (squarer than CTAs — these are tags), 12pt w700 uppercase
- The single fastest triage signal on a listing card

**"Time Left" Countdown**
- Format: "1d 4h left" / "4h 12m left" / "9m 32s left"
- Color: `#707070` when > 24h; switches to `#E53238` (urgent) when < 24h, monospace seconds when < 1h
- Sits directly under the bid count on auction cards — the clock that drives bidding

**Watch Heart**
- Outline idle, solid `#E53238` active (eBay's watch color is red, not the brand blue)
- Bounce 1.0 → 1.2 → 1.0 over 260ms + success haptic on toggle
- Adds the listing to "Saved" and surfaces auction-ending reminders

**eBay Multicolor Wordmark**
- The four-letter logo: e (red `#E53238`), B (blue `#0064D2`), a (yellow `#F5AF02`), y (green `#86B817`)
- Used in the home nav; never recolored or rearranged — equity-locked brand mark

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard margin: 16pt horizontal, 20pt between sections
- Listing grid: 12pt gutter, 16pt row gap; list rows touch with hairline dividers

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Listing grid: 2-column, 12pt gutter (browse default); list mode = full-width rows
- Horizontal carousels ("Similar items"): 16pt left inset, peek next card
- Listing detail: single column, full-width square photo carousel

### Whitespace Philosophy
- **Density for velocity**: listing cards pack photo + title + price + badge + time into a tight block so shoppers can scan many fast
- **Photo carries each card**: a clean 1:1 product photo with minimal chrome around it
- **Detail hero is functional**: square photos with floating controls — utilitarian, not editorial

### Border Radius Scale
- Square (0pt): hairline dividers
- Tag (4pt): buy-mechanic badges, condition tags (intentionally squarer than CTAs)
- Soft (8pt): listing cards, photos, search bar, thumbnails
- Comfortable (16pt): bottom sheets, modals
- Full Pill (999pt): primary/secondary CTAs, filter chips
- Circle (50%): watch-heart backing, avatars, gallery floating buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | White canvas, list rows, list-mode cards (border only) |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 4px` | Grid listing cards lifted slightly off white |
| Floating Control (Level 2) | `rgba(0,0,0,0.18) 0 2px 6px` | Photo-overlay buttons (back, share, watch) |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Bottom sheets, bid sheet, filter panel |
| Sticky Nav | `rgba(0,0,0,0.06) 0 1px 0` + blur | Search bar / listing bar on scroll |
| Tab Bar Material | `.regularMaterial` over `rgba(255,255,255,0.97)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Shadows are minimal and functional — eBay is a dense catalog UI, so cards lean on hairline borders and a barely-there 0.06-opacity shadow rather than heavy elevation. Visual separation comes from whitespace and dividers, not depth. The only stronger shadows are the floating photo controls (0.18 opacity) so they stay legible over product photography, and the bid/cart bottom sheets. The flatness keeps a 1.9-billion-listing catalog feeling fast, not fussy.

### Motion
- **Watch-heart toggle**: scale 1.0 → 1.2 → 1.0 spring (damping 0.55) over 260ms, fill cross-fades red, success haptic
- **Bid placement**: confirm sheet slides up; on success the current-bid number ticks up and the bid count increments with a medium haptic
- **"Time left" tick**: when < 1h, the seconds update each second; the digits do not jitter (tabular)
- **Card tap**: scale 0.98 spring (damping 0.8) + light haptic
- **Photo carousel**: 60fps horizontal snap, dots update, light haptic per page
- **Sticky bar reveal**: white bar slides up with blur as the hero scrolls off

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas — keep the dense catalog clean and fast
- Use the four eBay brand colors with discipline: blue = action/link, red = urgency
- Make the bid-vs-Buy-It-Now badge the fastest triage signal — blue "Buy It Now" vs neutral "12 bids"
- Color the "Time left" countdown red (`#E53238`) when under 24h
- Use the red watch-heart (`#E53238`) — eBay's watch color is red, not the brand blue
- Render the eBay wordmark in its locked four colors — never recolor or rearrange
- Keep buy-mechanic badges squarer (4pt radius) than the full-pill CTAs — they are tags
- Use yellow stars (`#F5AF02`) for seller feedback ratings
- Keep cards dense with hairline separation, not heavy shadows — velocity over polish

### Don't
- Don't tint the canvas — keep it pure `#FFFFFF`
- Don't use the brand colors decoratively — blue/red carry specific meaning (action/urgency)
- Don't make "Buy It Now" and auction badges look the same — the contrast IS the information
- Don't use blue for the watch-heart — eBay watch is red
- Don't recolor or rearrange the eBay multicolor wordmark — it is equity-locked
- Don't add heavy drop shadows — the dense catalog stays flat for speed
- Don't use thin font weights — stay at 400 / 600 / 700
- Don't let the "Time left" digits jitter — use tabular figures for the countdown
- Don't full-pill the buy-mechanic tags — they are 4pt-radius tags, not buttons

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 2-col grid with tighter gutters; title clamps sooner |
| iPhone 13/14/15 | 390pt | Standard 2-col listing grid, full 1:1 photos |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger photos; detail hero square grows |
| iPad | 768pt+ | 3-4 column listing grid, listing detail in a split layout |

### Dynamic Type
- Listing title, price, body: full scale
- Card title: scales with a 2-line clamp before truncation
- Buy-mechanic badge, condition tag, "Time left", tab labels: fixed (layout-sensitive)
- "Time left" seconds: tabular fixed-width so the countdown doesn't reflow

### Orientation
- Home / Search / My eBay: **portrait-locked**
- Listing photo carousel: **rotates to landscape** for full-screen photo viewing
- Cart / checkout: **portrait-locked**

### Touch Targets
- Primary CTA: 48pt+ tall
- Watch heart: 44pt effective hit
- Gallery floating controls: 44pt hit area
- Tab bar icons: 24pt glyph, 44pt effective hit
- Listing card: entire card tappable

### Safe Area Handling
- Top: wordmark nav + search bar respect the safe area / Dynamic Island
- Bottom: tab bar floats above the home indicator with blur
- Sides: 16pt content insets; listing photo carousel bleeds full-width

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F7F7F7`
- Divider: `#E5E5E5`
- Text primary: `#191919`
- Text secondary: `#707070`
- eBay Blue (action / link): `#0064D2`
- Blue pressed: `#0050A8`
- eBay Red (urgency / watch): `#E53238`
- eBay Yellow (stars): `#F5AF02`
- eBay Green (Best Offer): `#86B817`
- Error red: `#C7000B`

### Example Component Prompts
- "Create a SwiftUI eBay listing card on a `#FFFFFF` canvas: a 1:1 product photo with 8pt corners, a watch-heart top-right (outline idle, solid `#E53238` active). Below: title 'Apple AirPods Pro (2nd Gen) — Sealed' in Inter 15pt weight 400 `#191919` (2-line clamp), price '$189.99' in 17pt weight 700, then a buy-mechanic badge — either a blue 'BUY IT NOW' tag (`#0064D2` text on `rgba(0,100,210,0.10)`, 12pt w700 uppercase, 4pt radius) OR a neutral '12 BIDS' tag (`#191919` on `#F7F7F7`). Append 'or Best Offer' in `#86B817`, then 'Free shipping' and a 'Time left: 1d 4h' that turns `#E53238` when under 24h."
- "Build the eBay bid-vs-Buy-It-Now badge component with two variants: a 'Buy It Now' tag (blue `#0064D2`, light-blue fill, 4pt radius, 12pt weight 700 uppercase) and an 'Auction / 12 bids' tag (near-black `#191919` on `#F7F7F7`). The visual contrast between blue and neutral is the buy-mechanic signal — keep them distinct."
- "Design the eBay primary CTA: a full-pill button, `#0064D2` background, label 'Buy It Now' in Inter 16pt weight 700 white, 15pt vertical / 28pt horizontal padding. Pressed: background `#0050A8`, scale 0.98, medium haptic. Pair with a secondary outline 'Place bid' button (white, 1.5pt `#0064D2` border, blue text)."
- "Create an eBay 'Time left' countdown: '1d 4h left' in Inter 13pt weight 700, color `#707070` when over 24 hours, switching to urgent `#E53238` when under 24 hours, with tabular monospaced seconds ('9m 32s left') when under an hour so the digits never jitter."
- "Build an eBay search bar: full-width, `#F7F7F7` fill, 8pt corner radius, 44pt tall, a leading `magnifyingglass` 18pt `#707070`, placeholder 'Search for anything' 15pt `#707070`, and a trailing blue `camera` icon (`#0064D2`) for visual search. On focus: white background, 1.5pt `#0064D2` border, a 'Cancel' text button appears."

### Iteration Guide
1. Canvas is pure `#FFFFFF` — a dense catalog tuned for browse velocity
2. Four brand colors, used with discipline: blue = action/link, red = urgency, yellow/green = accents
3. The bid-vs-Buy-It-Now badge is the signature — blue "Buy It Now" vs neutral "12 bids" contrast carries the buy-mechanic
4. "Time left" goes red (`#E53238`) under 24h, tabular seconds under 1h — never jitters
5. The watch-heart is red (`#E53238`), not the brand blue
6. Buy-mechanic badges are 4pt-radius tags — squarer than the full-pill CTAs
7. The eBay multicolor wordmark is equity-locked — never recolor or rearrange
8. Shadows are minimal (0.06 opacity); separation is whitespace + hairlines, not depth
