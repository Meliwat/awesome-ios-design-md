# Design System Inspiration of McDonald's (iOS)

## 1. Visual Theme & Atmosphere

McDonald's iOS app is **a loyalty-and-ordering machine wearing the most recognizable brand colors on earth**. The instant you open it, two things establish identity: the **Golden Arches Yellow `#FFC72C`** and **McDonald's Red `#DA291C`**. The canvas is white (`#FFFFFF`) in light mode and a near-black charcoal (`#121212`) in dark mode, but the screen is *carried* by the yellow — it is the rewards points number, the primary CTA pill, the active tab, the order FAB, and the arches logomark. Red is the energetic counterpoint: deal flags, the secondary "Add to Mobile Order" button, urgency states. The aesthetic is bold, rounded, friendly, and unmistakably fast-food — pill-shaped buttons everywhere, generous rounding, big confident type, and appetizing product photography on every deal tile.

The signature screen is the **home / deals + mobile-order hub**. It opens with a personal greeting ("Hi, Alex" with the arches), then the hero of the whole app: the **MyMcDonald's Rewards** card — a points balance in huge yellow numerals, a progress bar toward the next reward, and a "X points away from a free …" line. Below it, a **Deals & Rewards** grid of two-up tiles (product photo + red "DEAL"/"FREE" flag + name + offer + "or N,NNN pts"), and a **Start an order** mode selector: four large pill/tile choices — **Pickup**, **Curbside**, **Drive Thru**, **Delivery** — the active one outlined in yellow. The second signature surface is the **deal/coupon detail** — a dashed-yellow-bordered coupon card with the food image, the offer, validity, and an "Add to order" yellow pill that drops the deal into a mobile order.

The color system is deliberately tiny and iconic: yellow + red carry everything, over a neutral canvas, with a single success green for "Order Ready". There is a hard brand rule baked into the system — **yellow always pairs with near-black text (`#1A1A1A`), never white**; the arches are never recolored. The mood is *cheerful efficiency*: get the user to a deal and into a mobile order in as few taps as possible, and make the rewards balance feel rewarding every time the app opens.

Typography is McDonald's proprietary **Speedee** — a friendly, slightly rounded humanist sans designed for the brand's signage and digital. On iOS it falls back to SF Pro (or a rounded grotesque) when Speedee isn't bundled; for documentation and the catalog, **Archivo** is the closest free analog. The hierarchy is heavy and confident: screen titles at 32pt/900, the rewards number at 28pt/900, card titles at 18pt/800, body at 16pt/400. Weight (800/900) and the yellow accent do the heavy lifting.

**Key Characteristics:**
- White canvas (`#FFFFFF`) light / near-black charcoal (`#121212`) dark — carried by **Golden Arches Yellow `#FFC72C`**
- Two-color brand system: **Yellow `#FFC72C`** (primary CTA, rewards, active tab, FAB) + **Red `#DA291C`** (deal flags, secondary CTA, urgency)
- Hard rule: yellow always pairs with near-black text `#1A1A1A` — **never white on yellow**; arches never recolored
- MyMcDonald's Rewards card — the home hero: huge yellow points number + progress bar + "X points to free …"
- Deals grid — two-up product-photo tiles with a red "DEAL"/"FREE"/"NEW" flag, offer line, and points alternative
- Mobile-order mode selector — Pickup / Curbside / Drive Thru / Delivery as four large tiles, active outlined yellow
- Pill-shaped buttons everywhere — 999pt radius CTAs; rounded 16pt cards
- Center "Order" FAB in the tab bar — elevated yellow circle, the app's primary action
- Coupon/deal cards — dashed yellow border, food photo, "Add to order" yellow pill
- Friendly heavy type — Speedee (Archivo fallback), screen titles 32pt/900, weight + yellow carry hierarchy
- Personal greeting header — "Hi, {name}" with the arches and a notification bell

## 2. Color Palette & Roles

### Primary (Interactive)
- **Golden Arches Yellow** (`#FFC72C`): The brand. Primary CTA pill, rewards points number, active tab, order FAB, progress fill, arches logomark. **Always with `#1A1A1A` text/icon on top.**
- **Yellow Pressed** (`#E6B015`): Pressed state for yellow CTAs.
- **Yellow Tint** (`#FFC72C14`): 8% yellow wash behind the active order-mode tile.

### Secondary (Brand Red)
- **McDonald's Red** (`#DA291C`): Deal/offer flags, the secondary "Add to Mobile Order" button, urgency/error, "See all" links.
- **Red Pressed** (`#B71F14`): Pressed state for red CTAs.
- **Dark Red** (`#B0210E`): Pressed deal flags, deep accents.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F4F5`): Card backgrounds, grouped sections, the rewards card base.
- **Surface Pressed** (`#EAEAEB`): Pressed rows / tiles.
- **Divider** (`#E3E3E5`): Hairline dividers; dashed variant on coupons (yellow).

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — near-black, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Card backgrounds, rewards card, sheets.
- **Dark Surface 2** (`#2A2A2A`): Elevated tiles, coupon background, badge fills.
- **Dark Divider** (`#2C2C2E`): Hairline dividers.

### Text
- **Text Primary Light** (`#1A1A1A`): Primary text — near-black; the only color allowed on yellow.
- **Text Secondary Light** (`#5C5C5C`): Metadata, validity, sub-copy.
- **Text Tertiary Light** (`#8A8A8A`): Disabled, low-emphasis.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#A0A0A0`): Metadata on dark.
- **Text On Yellow** (`#1A1A1A`): Mandatory text/icon color over `#FFC72C` in both modes.

### Roles Summary

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#121212` |
| Surface 1 | `#F4F4F5` | `#1C1C1E` |
| Surface 2 | `#EAEAEB` | `#2A2A2A` |
| Divider | `#E3E3E5` | `#2C2C2E` |
| Text Primary | `#1A1A1A` | `#F2F2F2` |
| Text Secondary | `#5C5C5C` | `#A0A0A0` |
| Brand Yellow / CTA | `#FFC72C` | `#FFC72C` |
| Text on Yellow | `#1A1A1A` | `#1A1A1A` |
| Brand Red | `#DA291C` | `#DA291C` |
| Error | `#DA291C` | `#DA291C` |

### Semantic
- **Success Green** (`#2B8A3E`): "Order Ready", payment success, "Reward redeemed".
- **Error Red** (`#DA291C`): Payment failure, item unavailable, destructive delete (shares the brand red).
- **Warning Amber** (`#E6A700`): "Deal expiring soon", store-closing notice.
- **Info Blue** (`#2E6DB4`): Informational links, store-info notices in checkout.
- **Rewards Gold** (`#FFC72C`): Points balance, progress fill, reward chips (the brand yellow).

## 3. Typography Rules

### Font Family
- **Brand / All UI**: `Speedee` — McDonald's proprietary humanist sans (friendly, slightly rounded; designed for signage + digital). Bundled in the official app.
- **iOS Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Web/Catalog Fallback**: `Archivo` (closest free grotesque with the same confident weight range)
- Numerals are tabular in points, prices, and timers so balances and totals align.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Speedee | 32pt | 900 | 1.2 | -0.4pt | "Rewards", "Menu" large title |
| Rewards Number | Speedee | 28pt | 900 | 1.0 | -0.5pt | The points balance — yellow |
| Hero / Tagline | Speedee | 26pt | 800 | 1.25 | -0.3pt | "i'm lovin' it", promo headline |
| Greeting | Speedee | 22pt | 800 | 1.25 | -0.4pt | "Hi, {name}" |
| Card Title | Speedee | 18pt | 800 | 1.3 | -0.3pt | "MyMcDonald's Rewards", section card |
| Section Header | Speedee | 19pt | 800 | 1.3 | -0.3pt | "Deals & Rewards", "Start an order" |
| Body | Speedee | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| Deal Title | Speedee | 15pt | 600 | 1.4 | 0pt | "Buy one, get one $1" |
| Price / Points | Speedee | 14pt | 700 | 1.4 | 0pt | "$5.99" / "1,900 pts" — tabular |
| Meta | Speedee | 14pt | 400 | 1.4 | 0pt | "Valid through Sun", store name |
| Badge / Flag | Speedee | 12pt | 800 | 1.1 | 0.3pt | "DEAL", "FREE", "NEW" — UPPERCASE |
| Button (Primary) | Speedee | 15pt | 800 | 1.0 | 0.1pt | Pill CTAs |
| Caption | Speedee | 12pt | 400 | 1.4 | 0pt | T&C, tiny print |
| Tab Label | Speedee | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |
| Mode Tile Label | Speedee | 11pt | 700 | 1.1 | 0pt | "Pickup", "Curbside" |

### Principles
- **Weight + yellow carry hierarchy**: emphasis lives at 800/900 and in the Golden Arches Yellow. The rewards number is the single largest, heaviest, most colorful element on the home screen.
- **Friendly, not corporate**: Speedee's slight roundness keeps the heavy weights warm — pair with rounded shapes (pill buttons, 16pt cards) to keep the tone cheerful.
- **Tabular numerals for points/money/timers**: the rewards balance, deal points, prices, and the "order ready in X min" timer all align.
- **UPPERCASE flags**: deal flags and status badges are uppercase with +0.3pt tracking — punchy and scannable.
- **Dynamic Type**: titles, body, deal copy scale; the rewards number, badges/flags, tab labels, and mode-tile labels stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Order Now / Add to order / Redeem)**
- Shape: **Pill**, 999pt corner radius
- Background: `#FFC72C` (Golden Arches Yellow)
- Text: `#1A1A1A` (mandatory — never white), Speedee 15pt weight 800
- Padding: 14pt vertical, 28pt horizontal (full-width in cart/checkout)
- Pressed: background `#E6B015` + scale 0.98
- Disabled: background `#E3E3E5` / `#2A2A2A`, text `#8A8A8A`

**Secondary Button (Add to Mobile Order / Continue)**
- Shape: Pill, 999pt corner radius
- Background: `#DA291C` (McDonald's Red)
- Text: `#FFFFFF`, Speedee 15pt weight 800
- Pressed: background `#B71F14` + scale 0.98

**Outline Button (Change Location / Edit)**
- Background: transparent
- Border: 1.5pt `#1A1A1A` / `#F2F2F2`
- Text: `#1A1A1A` / `#F2F2F2`, Speedee 14pt weight 700
- Corner radius: 999pt (pill)
- Padding: 12pt vertical, 22pt horizontal

**Text Button (View Deal Details / See all)**
- Text: `#DA291C` (brand red), Speedee 14pt weight 800
- No background, no border
- Pressed: opacity 0.6

**Icon Button (bell, close, back)**
- 36pt circle, `#F4F4F5` / `#1C1C1E` background
- 18pt glyph, `#1A1A1A` / `#F2F2F2`

### Core Atoms

**Rewards Points Pill**
- Pill, 999pt radius, `#FFC72C` fill
- "2,150 PTS" in `#1A1A1A`, Speedee 12pt weight 800, tabular numerals
- Sometimes a leading 11pt star glyph in `#1A1A1A`

**Deal Flag**
- Rounded rectangle, 6pt corner radius, `#DA291C` fill
- "DEAL" / "FREE" / "NEW" in `#FFFFFF`, Speedee 12pt weight 800 UPPERCASE
- Top-left corner of the deal-tile image, 8pt inset

**Progress Bar (Rewards)**
- Track: `#3A3A3A` (dark) / `#E3E3E5` (light), 8pt tall, 4pt radius
- Fill: `linear-gradient(90deg, #FFC72C, #FFD75E)`, 4pt radius
- Caption beneath: "Just {N} points away from {reward}" — secondary text, reward name bold primary

**Status Badge**
- Pill, 999pt radius
- "ORDER READY" green `#2B8A3E` / white text; "PREPARING" yellow `#FFC72C` / `#1A1A1A`; "DEAL" red
- Speedee 12pt weight 800 UPPERCASE

**Order-Mode Tile**
- Rounded rectangle, 12pt corner radius
- Default: 1.5pt `#E3E3E5`/`#2C2C2E` border, transparent fill, icon + label `#1A1A1A`/`#F2F2F2`
- Active: 1.5pt `#FFC72C` border, `#FFC72C14` fill, icon + label tinted `#FFC72C`
- 22pt centered icon, 11pt/700 label below

### Navigation

**Greeting Header**
- Leading: 32×28pt arches logomark (yellow, never recolored)
- Title: "Hi, {name}" — Speedee 22pt/800, the name often tinted `#FFC72C`
- Trailing: 36pt circular bell button (`#1C1C1E` bg)
- Scrolls away; a compact title bar replaces it on scroll

**Bottom Tab Bar**
- Height: 70pt incl. safe area
- Background: `rgba(255,255,255,0.94)` / `rgba(18,18,18,0.94)` with blur, 0.5pt top divider
- Tabs (5): **Home**, **Rewards**, **Order** (center FAB), **Menu**, **More**
- Center **Order FAB**: 50pt elevated yellow `#FFC72C` circle, `#1A1A1A` cart glyph, `rgba(255,199,44,0.4) 0 6px 16px` shadow, lifted -6pt above the bar; label "Order" in yellow
- Side tabs: 22pt glyph; active = filled glyph in `#FFC72C`; inactive = stroke `#888`; labels 10pt/700 always shown

**Top Nav (Menu / Deal detail)**
- Height: 56pt incl. safe area
- Leading back chevron (circular icon button)
- Center: screen title, Speedee 18pt/800
- Trailing: bag / search / store-info as circular icon buttons
- Sticky category strip on the menu once it scrolls

### Input Fields

**Search Field**
- Height: 48pt
- Background: `#F4F4F5` / `#1C1C1E`
- Corner radius: 999pt (pill) — consistent with the rounded language
- Leading 18pt `magnifyingglass` `#5C5C5C`/`#A0A0A0`
- Placeholder: "Search the menu" `#5C5C5C`/`#A0A0A0` 15pt
- Focus: 1.5pt `#FFC72C` border

**Form Field (address / payment)**
- Height: 52pt
- Background: `#F4F4F5` / `#1C1C1E`
- Corner radius: 12pt
- Floating label 11pt `#5C5C5C` on focus; text `#1A1A1A`/`#F2F2F2` 15pt
- Focus: 1.5pt `#FFC72C` border
- Error: 1.5pt `#DA291C` border + 12pt red helper text

**Promo Code Field**
- Inline, dashed `#FFC72C` border, 12pt radius
- "APPLY" trailing text button in `#DA291C` 13pt/800 UPPERCASE

### Distinctive Components

**MyMcDonald's Rewards Card**
- Rounded 16pt card, subtle radial yellow glow in a corner
- "MyMcDonald's Rewards" eyebrow (12pt/700 secondary UPPERCASE)
- Points balance: huge `#FFC72C` Speedee 28pt/900 + " points" secondary
- Progress bar (yellow gradient fill) + "Just {N} points away from {reward}"
- Tapping opens the full rewards catalog (100 / 200 / 400 / 600 / 1500 pt tiers)

**Deal Tile (two-up grid)**
- Rounded 16pt card
- Top: ~96pt product photo with a red `DEAL`/`FREE` flag top-left
- Body: deal name 14pt/800 → offer line 12pt secondary → "or N,NNN pts" in `#FFC72C` 11pt/800 with a leading star
- Full-tile tap opens the deal detail

**Deal / Coupon Detail Card**
- Dashed `#FFC72C` border, 14pt radius
- Leading food image strip + body (name 14pt/800, sub 12pt secondary, validity)
- "Add to order" yellow pill (`#FFC72C`, `#1A1A1A` text) — drops the deal into a mobile order

**Order-Mode Selector**
- "Start an order" card holding four tiles in a row: Pickup / Curbside / Drive Thru / Delivery
- Selecting a mode reveals the store picker / curbside-spot entry inline

**Curbside Check-In Sheet**
- Bottom sheet, 20pt top corners
- "I'm here" big yellow pill, spot-number stepper, vehicle description
- Live status: "We'll bring your order out to spot {N}" with a progress indicator

**Order Status Tracker**
- Stepped: Order Received → Preparing → Almost ready → Ready / Out for delivery
- Active step in `#FFC72C`; "ORDER READY" badge flips to green `#2B8A3E`
- Estimated-time number in heavy Speedee, tabular

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Card vertical gap: 16pt
- Screen horizontal inset: 18pt
- Intra-card content gaps: 3–8pt
- Section header top margin: 16pt

### Grid & Container
- iPhone: single-column scroll; deals in a 2-up grid (12pt gutter); order modes in a 4-up row
- Horizontal carousels (featured deals, favorites): 12pt gap, peek of next tile
- iPad: 2–3 column deal grid, max content width 760pt centered, 24pt insets
- Order FAB is a fixed 50pt circle centered in the tab bar, lifted -6pt

### Whitespace Philosophy
- **Rounded and roomy**: pill buttons + 16pt cards + generous gaps keep the tone friendly, never cramped
- **The rewards card owns the top**: it's the largest, most colorful block — points are the reason to open the app
- **Photos sell deals**: every deal tile leads with appetizing product imagery; chrome stays minimal around it
- **Two colors, disciplined**: yellow for primary action + rewards, red for deals/urgency; everything else neutral so the brand colors pop

### Border Radius Scale
- Square (0pt): full-bleed imagery edges, dividers
- Subtle (6pt): deal flags, small badges
- Compact (8pt): chips, inline tags
- Comfortable (12pt): form fields, order-mode tiles
- Card (16pt): rewards card, deal tiles, sheets' content cards
- Sheet (20pt): bottom sheets (curbside, customise, payment)
- Pill (999pt): all CTAs, search field, rewards/status pills
- Circle (50%): icon buttons, the order FAB, avatars

## 6. Depth & Elevation

McDonald's is mostly flat and rounded; elevation marks the one action the app most wants you to take.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt divider/border | Deal tiles, rewards card, list rows |
| Lifted (Level 1) | `rgba(0,0,0,0.10) 0 2px 8px` light / `rgba(0,0,0,0.5) 0 4px 12px` dark | Sticky bottom CTA bar, floating coupon |
| Order FAB (Level 2) | `rgba(255,199,44,0.40) 0 6px 16px` | The center Order FAB — a glowing yellow call-to-action |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -4px 24px` | Bottom sheets (curbside, customise, payment) |
| Modal Overlay | `rgba(0,0,0,0.5)` | Dim behind sheets and dialogs |

**Shadow Philosophy**: Cards are flat (border/divider defines them). The one element that *glows* is the Order FAB — its yellow-tinted shadow makes it read as the app's heartbeat, lifting above the tab bar. Sticky CTA bars get a soft top shadow so they detach from scrolling content. On dark mode, shadows deepen and the Order FAB's yellow glow is even more prominent; bottom sheets gain a 1pt `#2C2C2E` top border.

### Motion
- **Add to Mobile Order**: tapping a deal's CTA animates the item flying into the Order FAB; the FAB pulses once (scale 1.0→1.08→1.0, 260ms) with a soft haptic; a count badge pops on the FAB
- **Rewards progress**: on app open or after earning, the yellow fill animates from current → new over 600ms ease-out; the points number rolls (counting up)
- **Order-mode select**: tile border + fill cross-fade to yellow over 150ms; the store-picker section expands beneath over 220ms ease-out
- **Deal tile tap**: shared-element image zoom into the deal detail, 300ms ease-out
- **Curbside "I'm here"**: button fills, a checkmark draws (200ms), status text crossfades to "On our way to spot N"
- **Order status step**: each step ticks with a soft pop; the "READY" badge flips green with a 1.05 scale bounce + success haptic
- **Bottom sheet**: slides up 300ms ease-out; dim fades in parallel; drag-to-dismiss with rubber-banding
- **Pull-to-refresh**: yellow arches spinner; haptic tick on release
- **Haptics**: soft impact on add-to-order, light tick on mode select & chip toggle, success notification on order placed / reward redeemed

## 7. Do's and Don'ts

### Do
- Use Golden Arches Yellow (`#FFC72C`) for the primary CTA, rewards number, active tab, and Order FAB
- Always put `#1A1A1A` near-black text/icons on yellow — it's a hard brand rule
- Use McDonald's Red (`#DA291C`) for deal flags, the secondary CTA, and urgency
- Make the MyMcDonald's Rewards card the home hero — big yellow points number + progress bar
- Lead every deal tile with appetizing product photography + a red flag
- Use pill (999pt) buttons and rounded 16pt cards throughout — the language is round and friendly
- Elevate the center Order FAB with a yellow-tinted glow shadow
- Offer the four order modes (Pickup / Curbside / Drive Thru / Delivery) as large tiles, active outlined yellow
- Roll the points number and animate the progress fill when rewards change
- Use tabular numerals for points, prices, and timers
- Keep dark mode near-black (`#121212`) with full-saturation food photography

### Don't
- Don't put white text on yellow — ever; near-black `#1A1A1A` only
- Don't recolor or restyle the Golden Arches
- Don't introduce a third loud brand color competing with yellow + red
- Don't use sharp-cornered buttons — CTAs are pills, cards are 16pt rounded
- Don't bury the rewards balance — it's the reason the app is opened; it leads the home screen
- Don't shrink or crop deal photography to thumbnails — product imagery sells the deal
- Don't hide the Order FAB — it's the elevated center of the tab bar and the app's primary action
- Don't use pure black (`#000000`) for the dark canvas — `#121212` is correct
- Don't desaturate food imagery in dark mode — chrome recedes, food does not
- Don't animate aggressively — motion is cheerful and quick (150–300ms), tied to ordering/rewards
- Don't use timid sentence-case flags — deal flags are UPPERCASE and punchy

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; deals stay 2-up but shorter photos; compact greeting |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; greeting respects it |
| iPhone 15/16 Pro Max | 430pt | Larger deal photos; bigger rewards number |
| iPad (portrait) | 768pt | 2–3 column deal grid; rewards card spans top; content max 760pt centered |
| iPad (landscape) | 1024pt+ | 3-column deals; persistent order summary panel on the right |

### Dynamic Type
- Screen titles, section headers, body, deal copy: full scale
- Rewards number, deal flags, status badges, tab labels, mode-tile labels: FIXED (layout-critical)
- Prices/points scale one step then truncate gracefully

### Orientation
- iPhone: portrait-locked for the order flow (category standard)
- iPad: supports landscape with a 3-column grid + sticky order panel

### Touch Targets
- Pill CTA: ≥ 48pt tall
- Order FAB: 50pt circle, 56pt+ hit area
- Order-mode tile: full-tile tap, ≥ 64pt tall
- Tab bar icon: 22pt glyph, 44pt+ hit
- Rewards card: full-card tap to open catalog

### Safe Area Handling
- Top: greeting + Dynamic Island respected
- Bottom: Order FAB and tab bar above the home indicator; sticky CTA bar pins above the tab bar
- Keyboard: address/promo forms scroll above keyboard; curbside sheet positions above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface (dark card): `#1C1C1E`
- Divider: `#E3E3E5` light / `#2C2C2E` dark
- Text primary: `#1A1A1A` light / `#F2F2F2` dark
- Text secondary: `#5C5C5C` light / `#A0A0A0` dark
- Brand Yellow / CTA: **`#FFC72C`** (pressed `#E6B015`) — text on it is **always `#1A1A1A`**
- Brand Red / deal flag / secondary CTA: **`#DA291C`** (pressed `#B71F14`)
- Success (Order Ready): `#2B8A3E`
- Info: `#2E6DB4`

### Example Component Prompts
- "Build the MyMcDonald's Rewards card in SwiftUI: a 16pt rounded card on `#1C1C1E` with a faint radial `#FFC72C` glow in the top-right corner. Eyebrow 'MyMcDonald's Rewards' in Speedee 12pt/700 `#A0A0A0` UPPERCASE. Below: the points balance '2,150' in Speedee 28pt/900 `#FFC72C` followed by ' points' in 13pt/700 `#A0A0A0` — tabular numerals. Then an 8pt-tall progress bar (track `#3A3A3A`, fill `linear-gradient(90deg, #FFC72C, #FFD75E)`, 4pt radius) at ~62%, and a caption 'Just 350 points away from a free McNuggets' (`#A0A0A0`, 'free McNuggets' bold `#F2F2F2`). On appear, animate the fill and roll the number up over 600ms."

- "Create a McDonald's deal tile: 16pt rounded card on `#1C1C1E`. Top ~96pt product photo with a red `#DA291C` 'DEAL' flag (6pt radius, white Speedee 12pt/800 UPPERCASE) inset 8pt top-left. Body: name 'Big Mac' Speedee 14pt/800 `#F2F2F2`; offer 'Buy one, get one $1' 12pt `#A0A0A0`; then 'or 1,900 pts' in `#FFC72C` 11pt/800 with a leading 11pt star. Whole tile tappable. Lay out two per row with a 12pt gutter."

- "Render the McDonald's order-mode selector: a card holding four tiles in a row — Pickup, Curbside, Drive Thru, Delivery. Each tile: 12pt rounded, 1.5pt `#2C2C2E` border, transparent fill, a 22pt centered icon and an 11pt/700 label `#F2F2F2`. The active tile uses a 1.5pt `#FFC72C` border, `rgba(255,199,44,0.08)` fill, and tints the icon + label `#FFC72C`. Selecting one expands a store-picker section beneath over 220ms ease-out."

- "Build the McDonald's bottom tab bar: 5 tabs — Home, Rewards, Order (center FAB), Menu, More. The center Order item is a 50pt `#FFC72C` circle with a `#1A1A1A` cart glyph, lifted -6pt above the bar, with a `rgba(255,199,44,0.40) 0 6px 16px` shadow and the label 'Order' in `#FFC72C`. Side tabs: 22pt glyph, active = filled glyph `#FFC72C`, inactive = stroke `#888`, labels 10pt/700 always shown. Bar background `rgba(18,18,18,0.94)` blurred with a 0.5pt `#2C2C2E` top divider."

- "Create the McDonald's primary button: a pill (999pt radius), `#FFC72C` background, text in `#1A1A1A` (NEVER white) Speedee 15pt/800, 14pt vertical / 28pt horizontal padding, full-width in cart. Pressed: `#E6B015` + scale 0.98. Provide a secondary variant: `#DA291C` background, white text, same shape."

- "Render the McDonald's deal/coupon detail card: 14pt rounded card with a dashed `#FFC72C` border. Leading 84pt food-image strip; body with name Speedee 14pt/800 `#F2F2F2`, sub 12pt `#A0A0A0`, validity line; an 'Add to order' yellow pill (`#FFC72C` bg, `#1A1A1A` text, 999pt radius, Speedee 11pt/800 UPPERCASE). Tapping 'Add to order' animates the item flying into the Order FAB, which pulses scale 1.0→1.08→1.0 over 260ms with a soft haptic and a count-badge pop."

### Iteration Guide
1. Yellow (`#FFC72C`) carries primary action + rewards; red (`#DA291C`) carries deals + urgency — only these two brand colors
2. Text on yellow is ALWAYS `#1A1A1A` near-black, never white — hard brand rule; arches never recolored
3. The MyMcDonald's Rewards card is the home hero — huge yellow points number + animated progress bar
4. Every deal tile leads with appetizing product photography + a red `DEAL`/`FREE` flag
5. All CTAs are pills (999pt); all cards are 16pt rounded — the language is round and friendly
6. The center Order FAB is an elevated, yellow-glowing circle — the app's primary action
7. Offer Pickup / Curbside / Drive Thru / Delivery as four tiles; the active one is outlined yellow
8. Roll the points number and animate the progress fill whenever rewards change
9. Dark canvas is `#121212` (near-black); food photography stays full-saturation
10. Motion is cheerful and quick (150–300ms), always tied to ordering or rewards (fly-to-FAB, points roll, status flip)
