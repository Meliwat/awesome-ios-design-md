# Design System Inspiration of Revolut (iOS)

## 1. Visual Theme & Atmosphere

Revolut's iOS app is a near-black financial cockpit where money looks engineered, not nostalgic. The canvas sits at `#0A0A0F` — a cool, almost-black with the faintest blue cast — so that the product's signature gradient (an electric violet-to-indigo sweep, `#5B6BFF → #9C6BFF`) and the metal-card hero read like premium hardware on a dark workbench. Nothing is beige, nothing is "bank green"; this is fintech as a precision instrument. Balance numbers are huge, spend analytics are charted in a single donut, and every surface is a slightly-lifted slab (`#16161F` / `#1E1E2A`) floating on the void.

The accent system is a gradient ritual. The violet→purple sweep carries the brand: the primary CTA, the active tab, the selected segment, the analytics donut's spent arc, and the sheen that rolls across the metal card. A solid `#6B5BFF` is the fallback when a gradient can't be used (thin strokes, small icons, focus rings). Everything else is white, gray, or the deep canvas. The restraint makes the gradient feel like *Revolut* — when it appears, it's the product speaking.

Typography is Aeonik, Revolut's geometric grotesque (Inter is the closest free substitute). It's confident and slightly tight — perfect for big balance figures and dense transaction lists. The hierarchy is built for numbers: a balance can hit 34–40pt weight 700, transaction amounts sit at 16pt weight 600 (green for incoming, white for outgoing), and the most expressive moment is the multi-currency balance — a stack of currency tiles, each with a flag, a code, and a precisely-aligned tabular figure.

**Key Characteristics:**
- Near-black canvas (`#0A0A0F`) with cool-blue cast — fintech as precision hardware
- Violet→purple gradient (`#5B6BFF → #9C6BFF`) as the single brand accent
- Metal-card hero with a moving sheen — the centerpiece of the Home / Cards tab
- Multi-currency balance tiles — flag chip + code + tabular figure, perfectly aligned
- Spend-analytics donut — one ring, gradient spent arc, category legend
- Transaction rows with merchant logos — incoming green, outgoing white
- Aeonik font (proprietary geometric grotesque), weights 400 / 600 / 700
- Bottom tabs: Home, Invest, Crypto, Lifestyle, Cards
- Card 3D flip + sheen, haptic on reveal — the signature motion moment

## 2. Color Palette & Roles

### Primary
- **Gradient Start** (`#5B6BFF`): Left/top stop of the brand gradient — CTAs, active tab, donut arc.
- **Gradient End** (`#9C6BFF`): Right/bottom stop of the brand gradient.
- **Brand Solid** (`#6B5BFF`): Solid fallback when a gradient cannot be used — strokes, small icons, focus rings, links.
- **Brand Pressed** (`#5648D6`): Pressed/active state for solid brand elements.
- **Brand Tint** (`#1C1B33`): Selected-row wash, info banners on dark, gradient-ghost backgrounds.

### Canvas & Surfaces
- **Canvas** (`#0A0A0F`): Primary near-black background, the void everything floats on.
- **Surface 1** (`#16161F`): Card backgrounds, balance tiles, list containers.
- **Surface 2** (`#1E1E2A`): Higher elevation — sheets, modals, active rows, the metal-card backing.
- **Surface 3** (`#28283A`): Pressed state on dark surfaces, segmented-control track.
- **Divider** (`#2A2A38`): Hairline rules between rows and around tiles.
- **Border Subtle** (`#33334A`): Card outline at rest, input border.

### Text
- **Text Primary** (`#FFFFFF`): Balances, headings, primary amounts, primary UI text.
- **Text Secondary** (`#9A9AAA`): Merchant subtitles, metadata, currency names, helper text.
- **Text Tertiary** (`#6A6A7E`): Disabled labels, placeholders, very low-emphasis captions.

### Semantic
- **Income Green** (`#1FD17B`): Incoming transactions, positive change, "received".
- **Spend Red** (`#FF5A6A`): Declined, negative alerts, "you spent more".
- **Warning Amber** (`#FFB23F`): Pending, attention-required, card-frozen banner.
- **Crypto Gold** (`#F7C948`): Crypto-specific accents, BTC highlight in portfolio.
- **Info Blue** (`#3F9BFF`): Informational links inside support / statements.

### Light Mode (Limited Use)
Revolut's iOS app is a dark-first product; a light variant exists for system light mode but the canonical experience — and all brand photography — is the dark cockpit.
- **Light Canvas** (`#FFFFFF`)
- **Light Surface** (`#F4F4F8`)
- **Light Text** (`#0A0A0F`)
- **Brand (light-adjusted)** (`#5648D6`) — solid, no gradient ghosting on light

## 3. Typography Rules

### Font Family
- **Primary**: `Aeonik` (proprietary geometric grotesque, Regular / Medium / Bold)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures mandatory in balances, transaction amounts, and the currency stack
- **CJK/Non-Latin**: Falls to system stack — Revolut ships across EU/UK/US/APAC

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance (Hero) | Aeonik | 40pt | 700 | 1.05 | -0.6pt | Total balance on Home, tabular |
| Screen Title (Large) | Aeonik | 28pt | 700 | 1.15 | -0.4pt | "Accounts", "Invest" nav title |
| Section Header | Aeonik | 22pt | 700 | 1.2 | -0.3pt | "Spending", "Your cards" |
| Tile Balance | Aeonik | 22pt | 700 | 1.1 | -0.2pt | Per-currency balance figure, tabular |
| Subsection | Aeonik | 18pt | 600 | 1.3 | -0.1pt | Group titles, "This month" |
| Amount (Row) | Aeonik | 16pt | 600 | 1.2 | 0pt | Transaction amount, tabular |
| Body / Merchant | Aeonik | 16pt | 600 | 1.3 | 0pt | Merchant name on a transaction row |
| Body | Aeonik | 15pt | 400 | 1.45 | 0pt | Descriptions, statement copy |
| Button (Primary) | Aeonik | 16pt | 600 | 1.0 | 0pt | Gradient CTA label |
| Meta / Subtitle | Aeonik | 13pt | 400 | 1.3 | 0pt | "Today, 14:32", category, currency name |
| Label (UPPER) | Aeonik | 11pt | 700 | 1.2 | 0.6pt | "AVAILABLE", section labels |
| Tab Label | Aeonik | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Caption | Aeonik | 11pt | 400 | 1.3 | 0pt | Legal, "Rates update every 60s" |

### Principles
- **Numbers are the headline**: The balance is the largest type on the screen — 34–40pt weight 700
- **Tabular figures everywhere money appears**: Balances, amounts, and the currency stack must align to the decimal
- **Weight, not size, builds row hierarchy**: Merchant 16pt w600 vs. category 13pt w400 — compact lists
- **Gradient is never type**: The brand sweep is a fill/stroke, never applied to text glyphs
- **Negative tracking on big numbers**: -0.4 to -0.6pt on hero balance and large titles; body sits at 0pt
- **Dynamic Type respected** on titles, body, amounts; tab labels and uppercase labels fixed

## 4. Component Stylings

### Buttons

**Primary Gradient CTA ("Add money" / "Continue")**
- Background: linear gradient `#5B6BFF → #9C6BFF` (≈115°)
- Text: `#FFFFFF`, Aeonik 16pt weight 600
- Height: 52pt, full-width; corner radius 16pt
- Pressed: gradient dims to ~85% + scale 0.98, soft haptic
- Disabled: `#28283A`, text `#6A6A7E`

**Secondary Button ("Details" / outline)**
- Background: `#1E1E2A`
- Text: `#FFFFFF`, Aeonik 16pt weight 600
- No border (filled-tonal), height 52pt, radius 16pt
- Pressed: `#28283A`

**Tertiary / Ghost ("Skip")**
- Transparent, text `#9A9AAA` weight 600, no border
- Pressed: text `#FFFFFF`

**Circular Quick Action (Home action bar: Add, Exchange, More)**
- 56pt circle, background `#1E1E2A`, glyph 22pt
- Default glyph `#FFFFFF`; the primary "Add money" circle uses the gradient fill with white glyph
- Label below in 13pt `#9A9AAA`

**Icon Button (freeze, settings, statement)**
- 24pt glyph, 44pt hit area
- Default `#9A9AAA`; active `#6B5BFF`

**Text Link**
- Aeonik 15pt weight 600, color `#6B5BFF`, no underline; pressed `#5648D6`

### Cards & Containers

**Metal Card Hero (signature)**
- Full-width card, aspect ≈ 1.586 (ISO card), corner radius 16pt
- Background: dark brushed-metal gradient with a diagonal sheen highlight that animates on appear/flip
- Overlaid: Revolut wordmark, last-4 digits in Aeonik tabular, contactless glyph
- Variant chips: Standard / Plus / Premium / Metal change the base gradient (graphite, steel, rose-gold ghost)
- Tap: 3D flip (Y-axis) revealing CVV/expiry on the back, with haptic

**Currency Balance Tile**
- Background `#16161F`, 1pt `#2A2A38`, radius 16pt, height ~72pt
- Leading: 28pt circular flag chip; then currency code (16pt w600 white) + name (13pt w400 `#9A9AAA`)
- Trailing: balance figure 22pt w700 white, tabular, right-aligned
- Pressed: `#1E1E2A`
- A horizontal carousel of these is the multi-currency switcher

**Spend Analytics Donut Card**
- Card `#16161F`, radius 20pt, padding 20pt
- Centered ring: track `#28283A`, spent arc filled with the brand gradient, ~14pt stroke
- Center label: total spent 22pt w700 white + "this month" 13pt `#9A9AAA`
- Below: category legend rows (color dot + name + amount), top 4 categories

**Transaction Row**
- Height 64pt
- Leading: 40pt circular merchant logo (or category glyph on `#1E1E2A`)
- Center: merchant 16pt w600 white + meta "Today, 14:32 · Groceries" 13pt `#9A9AAA`
- Trailing: amount 16pt w600 — `#1FD17B` if incoming (prefixed `+`), `#FFFFFF` if outgoing
- Pressed: `#1E1E2A`; divider `#2A2A38`

**Account Switcher Sheet**
- Bottom sheet `#1E1E2A`, top grabber, radius 24pt top corners
- List of accounts/profiles with avatar, name, and balance; selected row has a gradient left bar

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(10,10,15,0.92)` with `.regularMaterial` blur
- Tabs: Home, Invest, Crypto, Lifestyle, Cards
- Icon size: 24pt
- Active: gradient-tinted icon + label `#FFFFFF` (a 2pt gradient pill can sit under the active icon)
- Inactive: `#6A6A7E`
- Labels: Aeonik 10pt weight 600, always shown

**Top Bar (Home)**
- Profile avatar (32pt circle) top-left → name/plan; right: search + notifications (24pt)
- Total balance sits large directly below, with an "Accounts" disclosure

**Segmented Control (Analytics period / Card type)**
- Track `#1E1E2A`, height 36pt, radius 18pt
- Selected segment: gradient pill, white text weight 600
- Unselected: `#9A9AAA`
- Sliding thumb 220ms ease

### Input Fields

**Amount Entry (Exchange / Send)**
- Large centered field, value in Aeonik 34pt w700 white, currency chip trailing
- No box — the number *is* the input; a thin gradient caret blinks
- Sub-line: converted value 15pt `#9A9AAA`, live-updating

**Text Field (profile / address)**
- Filled style, background `#16161F`, height 56pt, radius 12pt
- Rest: 1pt `#33334A`; focus: 1.5pt gradient stroke (or `#6B5BFF` solid), label floats and tints
- Error: 1.5pt `#FF5A6A`, helper `#FF5A6A`

**Search Bar**
- Background `#16161F`, height 44pt, radius 22pt
- Leading `magnifyingglass` 18pt `#9A9AAA`; placeholder 16pt `#9A9AAA`
- Focus: 1pt `#6B5BFF` ring

### Distinctive Components

**Metal Card Carousel + Sheen**
- The Cards tab is a horizontally-paged carousel of card faces; the active card catches a moving diagonal sheen (a translucent white band sweeping ~1.2s on appear and on flip). This is the brand's signature object.

**Spend Donut**
- A single ring summarizing the month: track + gradient spent arc + center total. Tapping a legend row highlights its segment (arc brightens, others dim). The "one ring to read your month" device.

**Multi-Currency Stack**
- The balance switcher: a tight vertical/horizontal stack of currency tiles with flag chips and tabular figures, decimal-aligned. Exchange pulls two of these into a converter.

**Card 3D Flip**
- Tapping the card hero flips it on the Y-axis (front → back) over ~450ms with a subtle perspective and a confirming haptic, revealing CVV/expiry; tapping again flips back.

**Live FX Ticker**
- A slim strip in Exchange showing the mid-market rate with a 60s refresh and a tiny up/down delta in `#1FD17B`/`#FF5A6A`.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard screen margin: 16pt horizontal
- Card internal padding: 20pt; section vertical gap: 24pt
- Transaction rows: 0pt gap, separated by `#2A2A38` hairlines

### Grid & Container
- Content width: full device width, 16pt side margins
- Quick-action bar: 4 circular actions, evenly distributed
- Card carousel: full-width pages, ~12pt peek of the next card
- Currency tiles: full-width stacked, or 1.2-up horizontal carousel

### Whitespace Philosophy
- **Floating slabs on a void**: Surfaces are clearly separated from the `#0A0A0F` canvas by elevation + radius, not heavy borders
- **Numbers get air**: The hero balance and donut center always have generous clear space — money is the focal point
- **Dense lists, calm cards**: Transaction lists are tight (64pt rows); analytic cards are roomy (20pt padding)

### Border Radius Scale
- Soft (8pt): Small chips, flag containers, inline tags
- Standard (12pt): Inputs, small tiles
- Comfortable (16pt): Buttons, cards, the metal-card hero
- Roomy (20pt): Analytics cards, feature modules
- Sheet (24pt): Bottom sheets (top corners)
- Pill (≥18pt half-height): Segmented control, filter chips
- Circle (50%): Quick-action circles, avatars, flag chips

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, transaction list rows |
| Surface (Level 1) | `rgba(0,0,0,0.4) 0 2px 10px` | Balance tiles, list containers |
| Card (Level 2) | `rgba(0,0,0,0.5) 0 8px 24px` | Metal-card hero, analytics card |
| Glow (Level 2g) | `rgba(108,91,255,0.30) 0 8px 28px` | Gradient CTA / active state ambient glow |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 48px` | Bottom sheets, account switcher |
| Tab Material | `.regularMaterial` over `rgba(10,10,15,0.92)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On a near-black canvas, shadows must be deep (0.4–0.6 opacity) to register, and they're cool-toned, never warm. The one expressive shadow is the gradient *glow*: the primary CTA and active states cast a soft violet ambient halo so the brand color feels lit from within. Everything else relies on elevation + radius to lift off the void.

### Motion
- **Card flip**: Y-axis 3D rotation front→back ~450ms with perspective, confirming haptic on reveal
- **Card sheen**: diagonal white band sweeps across the active card ~1.2s on appear/flip
- **Gradient CTA press**: scale 0.98 + gradient dim to 85% + soft haptic, 150ms
- **Donut draw**: spent arc animates from 0 to value over 700ms ease-out on screen entry
- **Balance reveal**: tap-to-hide blurs/un-blurs the figure with a 250ms crossfade
- **Segmented thumb**: gradient pill slides 220ms ease-in-out

## 7. Do's and Don'ts

### Do
- Use `#0A0A0F` as the canvas — cool near-black, not pure `#000000`, not warm
- Reserve the violet→purple gradient (`#5B6BFF → #9C6BFF`) for the primary action, active tab, and the spent arc
- Use the solid `#6B5BFF` only where a gradient can't render (thin strokes, tiny icons, focus rings)
- Make the balance the largest element on the screen — 34–40pt weight 700, tabular
- Use tabular numerals for every balance, amount, and currency tile so columns align
- Color incoming amounts `#1FD17B` and outgoing `#FFFFFF` on transaction rows
- Give the card hero a 3D flip + sheen with a confirming haptic
- Let the gradient CTA cast a soft violet glow — the brand is lit from within
- Float surfaces on the void with elevation + radius, not heavy borders

### Don't
- Don't use pure black `#000000` or a warm dark — the canvas has a cool blue cast
- Don't apply the gradient to text glyphs — it's a fill/stroke device only
- Don't add a second accent hue — the gradient is the entire brand color story
- Don't let a merchant name outweigh its amount, or an amount outweigh the hero balance
- Don't use proportional figures for money — always tabular, decimal-aligned
- Don't use light/thin font weights — Aeonik starts at 400
- Don't over-skeuomorph the card — the sheen is a subtle band, not a glossy reflection
- Don't animate the donut or sheen aggressively — 700ms / 1.2s, smooth and singular
- Don't hide the live FX delta in Exchange — rate transparency is core

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Smaller card hero, balance caps ~34pt, 3 quick actions visible |
| iPhone 13/14/15 | 390pt | Standard card hero, balance ~40pt, 4 quick actions |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the top bar |
| iPhone 15/16 Pro Max | 430pt | Larger card hero, more carousel peek, donut scales up |
| iPad | 768pt+ | Two-pane (accounts list + detail), card carousel centered, larger donut |

### Dynamic Type
- Titles, body, merchant names, amounts: full scale
- Hero balance: scales but caps to protect the single-line layout
- Tab labels, uppercase labels: fixed
- Currency-tile figures: fixed tabular size for column alignment

### Orientation
- All primary tabs: **portrait-locked** on iPhone
- iPad: landscape supported with split layout
- Card flip / scanner: **portrait-locked**

### Touch Targets
- Gradient CTA: 52pt height — unmissable
- Quick-action circles: 56pt
- Transaction rows: 64pt, full-row tappable
- Tab icons: 24pt glyph, 44pt effective hit
- Card hero: full-card tap to flip

### Safe Area Handling
- Top: top bar + balance respect safe area / Dynamic Island
- Bottom: tab bar blur respects the home indicator; sheets present above it
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0A0A0F`
- Surface 1 (tiles/cards): `#16161F`
- Surface 2 (sheets/active): `#1E1E2A`
- Divider: `#2A2A38`
- Text primary: `#FFFFFF`
- Text secondary: `#9A9AAA`
- Gradient start: `#5B6BFF`
- Gradient end: `#9C6BFF`
- Brand solid (fallback): `#6B5BFF`
- Brand pressed: `#5648D6`
- Income green: `#1FD17B`
- Spend red: `#FF5A6A`
- Crypto gold: `#F7C948`

### Example Component Prompts
- "Create a SwiftUI Revolut currency balance tile: 72pt tall, background #16161F with 1pt #2A2A38 border and 16pt corner radius. Leading 28pt circular flag chip, then 'USD' in Inter 16pt weight 600 #FFFFFF with 'US Dollar' 13pt weight 400 #9A9AAA below. Trailing balance '$4,820.50' in Inter 22pt weight 700 #FFFFFF, tabular numerals, right-aligned. Pressed background #1E1E2A."
- "Build the Revolut primary CTA: 52pt tall full-width button, 16pt radius, background a 115° linear gradient from #5B6BFF to #9C6BFF, 'Add money' in Inter 16pt weight 600 white. Pressed: gradient dims to 85%, scale 0.98, soft haptic. Cast a soft violet glow rgba(108,91,255,0.30) 0 8px 28px."
- "Design the Revolut spend-analytics donut card: #16161F card, 20pt radius, 20pt padding. A centered ring (~14pt stroke) with track #28283A and the spent arc filled with the #5B6BFF→#9C6BFF gradient, animating 0→value over 700ms. Center: '£1,284' in 22pt weight 700 white + 'this month' 13pt #9A9AAA. Below: 4 legend rows, each a color dot + category + amount."
- "Create the Revolut metal-card hero: full-width ISO-ratio card (≈1.586), 16pt radius, dark brushed-metal gradient with a diagonal white sheen band that sweeps ~1.2s on appear. Overlaid Revolut wordmark, tabular last-4 '•••• 4821', contactless glyph. Tap flips it on the Y-axis over 450ms with a confirming haptic to reveal CVV/expiry."
- "Build a Revolut transaction row: 64pt tall, 40pt circular merchant logo leading, merchant 'Spotify' 16pt weight 600 white + 'Today, 14:32 · Subscriptions' 13pt #9A9AAA, trailing amount. Outgoing '-£9.99' in white weight 600; incoming '+£40.00' in #1FD17B. Pressed #1E1E2A, divider #2A2A38."

### Iteration Guide
1. Canvas is cool near-black `#0A0A0F` — never pure black, never warm
2. The violet→purple gradient (`#5B6BFF → #9C6BFF`) is the entire brand accent; solid `#6B5BFF` only where a gradient can't render
3. Never apply the gradient to text — it's a fill/stroke device
4. The balance is the largest element — 34–40pt weight 700, tabular numerals
5. Money columns (tiles, amounts, donut center) are always tabular and decimal-aligned
6. Incoming amounts are `#1FD17B`, outgoing are `#FFFFFF`
7. The metal-card hero (flip + sheen + haptic) and the spend donut are the signature objects
8. Float surfaces with deep cool shadows + radius; let the gradient CTA glow violet (lit from within)
