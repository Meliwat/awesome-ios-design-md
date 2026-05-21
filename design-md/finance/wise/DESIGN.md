# Design System Inspiration of Wise (iOS)

## 1. Visual Theme & Atmosphere

Wise's iOS app (formerly TransferWise) is a bright, confident money app whose entire personality is *transparency*. The canvas is clean white (`#FFFFFF`) with neutral surfaces (`#F7F7F7`), and the brand's signature deep forest green (`#163300`) anchors headings, the navigation, and dark hero panels. Where money moves, a vivid Bright Green (`#9FE870`) does the work — it's the CTA, the active state, the highlight on a balance. The combination — almost-black forest paired with a near-fluorescent green — reads as serious *and* optimistic, the visual equivalent of "we'll show you exactly what this costs."

The accent system is a forest/bright-green duet. Bright Green (`#9FE870`) is the primary action and highlight; it always carries dark forest text (`#163300`), never white — that contrast is the brand. Forest (`#163300`) is the structural color: large titles, the dark account hero, key numbers. Everything else is neutral. There is no blue, no gradient, no second accent — the discipline is what makes the bright green feel like *Wise*.

Typography is Wise Sans, a friendly, slightly quirky humanist sans with a distinctive lowercase character (Inter is the closest free substitute). It runs heavy where it matters — balances and titles at weight 800 — and the most expressive moment is the multi-currency balance: a stack of rows, each with a circular flag chip, a currency code, and a precisely-aligned tabular figure, topped by a total in forest at 32–40pt weight 800.

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) with neutral surfaces (`#F7F7F7`)
- Deep forest green (`#163300`) for structure: titles, nav, dark hero panels
- Bright Green (`#9FE870`) for action/highlight — always with forest text, never white
- Multi-currency balance rows — flag chip + code + tabular figure, decimal-aligned
- Transparent fee-breakdown card — the signature "here's exactly the cost" device
- Mid-market rate ticker — the live exchange rate, stated plainly
- Send-money flow stepper — a clear, numbered multi-step money journey
- Wise Sans (proprietary humanist sans), weights 400 / 600 / 800
- Bottom tabs: Home, Card, Recipients, Payments, Account
- Number roll-up on balances and the total — figures animate into place

## 2. Color Palette & Roles

### Primary
- **Bright Green** (`#9FE870`): Primary CTA ("Send money", "Continue"), active tab, highlight chip, selected state. Always paired with forest text.
- **Bright Green Pressed** (`#8AD45C`): Pressed/active state for the bright-green CTA.
- **Bright Green Tint** (`#EAF9DC`): Selected-row wash, success banner background, subtle highlight fill.
- **Forest** (`#163300`): Structural — large titles, dark account hero, primary numbers, the dark nav.
- **Forest Hover** (`#0E2200`): Pressed state on forest surfaces and dark buttons.

### Canvas & Surfaces
- **Canvas White** (`#FFFFFF`): Primary page background, card fill.
- **Surface Neutral** (`#F7F7F7`): Section wells, input fill, grouped containers.
- **Surface Sunken** (`#EFEFEF`): Pressed neutral surface, skeleton placeholders.
- **Divider** (`#E5E5E5`): Hairline rules between rows and around cards.
- **Border Strong** (`#D2D2D2`): Input outline at rest, segmented-control border.
- **Forest Surface** (`#0E2200`): The dark account-hero panel background (forest, deepened).

### Text
- **Text Primary** (`#0E0F0C`): Body text, primary amounts, row titles (a near-black with a faint warm cast).
- **Text on Forest** (`#FFFFFF`): Text/numbers on the dark forest hero.
- **Text Secondary** (`#6B6F66`): Metadata, currency names, helper text, "you send" labels.
- **Text Tertiary** (`#9A9D95`): Disabled labels, placeholders, low-emphasis captions.

### Semantic
- **Success Green** (`#2F8F4E`): "Money arrived", completed transfer (a readable green, distinct from the fluorescent brand green).
- **Pending Amber** (`#B5781E`): "In progress", estimated-arrival warnings.
- **Error Red** (`#D4332B`): Form errors, failed payment, declined card.
- **Info Forest** (`#163300`): Informational emphasis inside fee breakdowns and statements.
- **Rate Blue-Grey** (`#5B6670`): The neutral mid-market rate ticker text when not highlighted.

### Dark Mode (Limited Use)
Wise's iOS app is light-first; the forest hero already provides a "dark" moment within the bright layout. A full dark variant exists for system dark mode but the canonical experience is bright with forest accents.
- **Dark Canvas** (`#0E0F0C`)
- **Dark Surface** (`#1B1E18`)
- **Dark Text** (`#F4F5F2`)
- **Bright Green (unchanged)** (`#9FE870`) — reads well on dark, still with forest text on fills

## 3. Typography Rules

### Font Family
- **Primary**: `Wise Sans` (proprietary humanist sans, Regular / Semibold / Extrabold)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures mandatory in balances, the currency stack, and fee breakdowns
- **CJK/Non-Latin**: Falls to system stack — Wise ships globally with broad localization

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance (Hero) | Wise Sans | 40pt | 800 | 1.05 | -0.6pt | Total balance on the forest hero, tabular |
| Screen Title (Large) | Wise Sans | 32pt | 800 | 1.1 | -0.5pt | "Home", "Recipients" nav title |
| Section Header | Wise Sans | 22pt | 800 | 1.2 | -0.3pt | "Your accounts", "Recent" |
| Currency Figure | Wise Sans | 22pt | 800 | 1.1 | -0.2pt | Per-currency balance figure, tabular |
| Subsection | Wise Sans | 18pt | 600 | 1.3 | -0.1pt | "You send", group titles |
| Amount (Row) | Wise Sans | 16pt | 600 | 1.2 | 0pt | Transaction amount, tabular |
| Body / Title | Wise Sans | 16pt | 600 | 1.3 | 0pt | Recipient name, row title |
| Body | Wise Sans | 15pt | 400 | 1.45 | 0pt | Descriptions, fee explanations |
| Button (Primary) | Wise Sans | 16pt | 800 | 1.0 | 0pt | Bright-green CTA label |
| Meta / Subtitle | Wise Sans | 13pt | 400 | 1.3 | 0pt | "Sent 12 May · GBP → EUR", currency name |
| Label (UPPER) | Wise Sans | 11pt | 800 | 1.2 | 0.6pt | "YOU SEND", section labels |
| Tab Label | Wise Sans | 11pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Caption | Wise Sans | 11pt | 400 | 1.3 | 0pt | Legal, "Rate guaranteed for 24h" |

### Principles
- **Heavy where it matters**: Balances and titles run weight 800 — Wise's type is confident, not delicate
- **Tabular figures everywhere money appears**: Total, currency rows, fee lines must align to the decimal
- **Forest is the structural ink**: Large titles and the hero use `#163300`, not pure black
- **Bright green is never type**: `#9FE870` is a fill/highlight; it always sits *behind* forest text
- **Negative tracking on big numbers**: -0.4 to -0.6pt on the hero balance and large titles; body at 0pt
- **Dynamic Type respected** on titles, body, amounts; tab labels and uppercase labels fixed

## 4. Component Stylings

### Buttons

**Primary CTA ("Send money" / "Continue")**
- Background: `#9FE870`
- Text: `#163300` (forest — never white on bright green)
- Height: 52pt, full-width; corner radius 16pt
- Pressed: background `#8AD45C`, scale 0.98, soft haptic
- Disabled: background `#EFEFEF`, text `#9A9D95`

**Forest Button (secondary primary, e.g. "Add money")**
- Background: `#163300`
- Text: `#FFFFFF`
- Height: 52pt, radius 16pt
- Pressed: `#0E2200`

**Outline Button ("Details")**
- Background: transparent
- Text: `#163300`, 1.5pt border `#163300`
- Height: 52pt, radius 16pt
- Pressed: background `#F7F7F7`

**Text Link**
- Wise Sans 15pt weight 600, color `#163300`, underline on press
- Pressed: `#0E2200`

**Icon Button (settings, share, statement)**
- 24pt glyph, 44pt hit area
- Default `#6B6F66`; active `#163300`

**Quick Action (Home: Send, Add, Request, Convert)**
- 52pt circle, background `#F7F7F7`, glyph 22pt `#163300`
- The primary "Send" circle uses `#9FE870` fill with a forest glyph
- Label below 13pt `#6B6F66`

### Cards & Containers

**Forest Account Hero (signature)**
- Full-width panel, background `#0E2200` (deep forest), corner radius 20pt, padding 24pt
- "Total balance" label 11pt `#9FE870` uppercase, total figure 40pt w800 white tabular
- A small currency-switch chip and a `#9FE870` "Add money" affordance
- This is the one "dark moment" inside the bright layout

**Currency Balance Row**
- Background `#FFFFFF`, divider `#E5E5E5`, height ~64pt (or as a card with 1pt border, radius 16pt)
- Leading: 32pt circular flag chip
- Center: currency code (16pt w600 `#0E0F0C`) + name (13pt w400 `#6B6F66`)
- Trailing: balance 22pt w800 `#0E0F0C`, tabular, right-aligned
- Pressed: background `#F7F7F7`

**Fee-Transparency Card (signature)**
- Card `#FFFFFF`, 1pt `#E5E5E5`, radius 16pt, padding 20pt
- Vertically itemized breakdown, each line: label (15pt `#6B6F66`) ←→ value (16pt w600 `#0E0F0C`, tabular)
- Lines: "You send", "Our fee" (struck-through "hidden fees: £0" allowed), "Amount we convert", "Guaranteed rate", "Recipient gets"
- The "Recipient gets" line is emphasized: forest text, larger, with a `#EAF9DC` highlight band
- A connecting vertical guide line ties the steps; this is Wise's transparency centerpiece

**Mid-Market Rate Ticker**
- Slim row: "1 GBP = 1.1632 EUR" in 15pt w600 `#0E0F0C`, a tiny live dot, and "guaranteed for 24h" 11pt `#6B6F66`
- A small up/down delta in `#2F8F4E` / `#D4332B`

**Transaction Row**
- Height 64pt
- Leading: 40pt circular recipient/merchant avatar (or flag for conversions)
- Center: name 16pt w600 `#0E0F0C` + meta "Sent 12 May · GBP → EUR" 13pt `#6B6F66`
- Trailing: amount 16pt w600 — `#2F8F4E` if money in, `#0E0F0C` if out
- Pressed: `#F7F7F7`; divider `#E5E5E5`

**Send-Money Stepper (signature)**
- A numbered vertical/segmented progress for the transfer flow: Recipient → Amount → Review → Pay
- Active step: forest dot with white numeral; completed: `#9FE870` dot with a forest check; upcoming: `#E5E5E5`
- Connecting rail in `#E5E5E5`, filling forest as steps complete

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, 0.5pt top border `#E5E5E5` (opaque — clarity first)
- Tabs: Home, Card, Recipients, Payments, Account
- Icon size: 24pt
- Active: icon + label `#163300` (filled icon); a `#9FE870` underline/dot can mark the active tab
- Inactive: `#6B6F66`
- Labels: Wise Sans 11pt weight 600, always shown

**Top Bar (Home)**
- Profile avatar (32pt) top-left; right: notifications + settings (24pt)
- The forest account hero sits large directly below

**Segmented Control (Send / Request, account filters)**
- Track `#F7F7F7`, height 40pt, radius 20pt
- Selected segment: white pill with shadow, forest text weight 600 (or `#9FE870` fill for emphasis)
- Unselected: `#6B6F66`
- Sliding thumb 220ms ease

### Input Fields

**Amount Entry (Send / Convert)**
- Large field, value in Wise Sans 34pt w800 `#0E0F0C` tabular, currency selector chip trailing (flag + code + chevron)
- Below: live converted value 15pt `#6B6F66`, updating with the rate
- No box — the number is the field; a forest caret blinks

**Text Field (recipient / address)**
- Filled style, background `#F7F7F7`, height 56pt, radius 12pt
- Rest: 1pt `#D2D2D2`; focus: 2pt `#163300`, floating label tints forest
- Error: 2pt `#D4332B`, helper `#D4332B`

**Search Bar (recipients)**
- Background `#F7F7F7`, height 44pt, radius 22pt
- Leading `magnifyingglass` 18pt `#6B6F66`; placeholder 16pt `#6B6F66`
- Focus: 1.5pt `#163300` ring

### Distinctive Components

**Fee-Transparency Breakdown**
- The signature object: an itemized, connected list that shows *every* component of a transfer cost — your amount, the (low, explicit) fee, the amount converted, the guaranteed mid-market rate, and exactly what the recipient gets, with that final line highlighted in `#EAF9DC`. Wise's entire brand is "no hidden fees" made visible.

**Multi-Currency Balance Stack**
- A tight vertical list of currency rows, each with a circular flag chip and a decimal-aligned tabular figure, summing to a forest total on the hero. Tapping a row drills into that currency's activity.

**Mid-Market Rate Ticker**
- The live exchange rate stated plainly with a "guaranteed for 24h" reassurance and a tiny live pulse — rate honesty as a UI element.

**Send-Money Flow Stepper**
- A clearly numbered, connected multi-step journey (Recipient → Amount → Review → Pay) so moving money never feels opaque; completed steps flip to a `#9FE870` check.

**Number Roll-Up**
- Balances and the hero total animate by rolling digits into place (~500ms ease-out) on load and after a transfer — money "lands" visibly.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard screen margin: 16pt horizontal
- Card internal padding: 20–24pt; section vertical gap: 24pt
- Transaction rows: 0pt gap, separated by `#E5E5E5` hairlines

### Grid & Container
- Content width: full device width, 16pt side margins
- Quick-action bar: 4 circular actions, evenly distributed
- Currency rows: full-width stacked
- Fee card: full-width, single column, connected guide line

### Whitespace Philosophy
- **Bright and open**: Generous whitespace around the forest hero and the fee card — transparency needs room to breathe
- **One dark anchor**: The forest hero is the single deep panel; everything else stays light
- **Numbers get air**: The total and "Recipient gets" line always have clear space — the figures are the point

### Border Radius Scale
- Soft (8pt): Small chips, flag containers, inline tags
- Standard (12pt): Inputs, small tiles
- Comfortable (16pt): Buttons, cards, fee card
- Roomy (20pt): The forest hero, feature modules
- Sheet (24pt): Bottom sheets (top corners)
- Pill (≥20pt half-height): Segmented control, filter chips
- Circle (50%): Quick-action circles, avatars, flag chips

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt `#E5E5E5` border | List rows, neutral wells, canvas |
| Card (Level 1) | `rgba(20,30,10,0.06) 0 1px 4px` | Currency cards, fee card at rest |
| Raised (Level 2) | `rgba(20,30,10,0.10) 0 6px 16px` | Pressed card, selected segment thumb |
| Hero (Level 2f) | `rgba(14,34,0,0.25) 0 12px 32px` | The forest account hero panel |
| Sheet (Level 3) | `rgba(20,30,10,0.16) 0 -10px 32px` | Bottom sheets, recipient picker |
| Sticky Bar | `rgba(20,30,10,0.08) 0 -2px 8px` | Sticky "Continue" bar above the tab bar in the send flow |

**Shadow Philosophy**: On the bright canvas, separation is border-first (1pt `#E5E5E5`) and shadows are soft, cool-green-tinted, and restrained — they appear mainly to lift the forest hero off the white field and to signal press/sticky. The system never uses heavy black shadows; the forest panel's own depth is its strongest elevation.

### Motion
- **Number roll-up**: digits roll vertically into place over ~500ms ease-out on load / after transfer
- **CTA press**: bright-green scale 0.98 + darken to `#8AD45C`, soft haptic, 150ms
- **Stepper advance**: completed dot flips to a `#9FE870` check (200ms), rail fills forest
- **Rate ticker pulse**: a tiny live dot pulses every refresh; delta arrow fades in
- **Segmented thumb**: white/green pill slides 220ms ease-in-out
- **Fee card reveal**: lines stagger-in 40ms apart as the breakdown expands

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas; keep the layout bright with one forest anchor (the hero)
- Use forest `#163300` for structure — large titles, the dark hero, primary numbers
- Reserve Bright Green `#9FE870` for the primary action and key highlights
- Always put forest text (`#163300`) on bright green — never white
- Make the total/balance the heaviest element — 32–40pt weight 800, tabular
- Use tabular numerals for the total, every currency row, and fee lines
- Show the full fee breakdown with the "Recipient gets" line highlighted in `#EAF9DC`
- State the mid-market rate plainly with a "guaranteed for 24h" reassurance
- Roll numbers into place so money visibly "lands"

### Don't
- Don't put white text on bright green — it fails contrast and breaks the brand
- Don't introduce blue, gradients, or a second accent — forest + bright green is the whole story
- Don't use pure black for text — primary ink is `#0E0F0C`, structure is forest `#163300`
- Don't hide or summarize the fee — the itemized transparency card *is* the product
- Don't use proportional figures for money — always tabular, decimal-aligned
- Don't use light/thin font weights — Wise Sans titles run 800
- Don't make more than one panel dark — the forest hero is the single deep anchor
- Don't skip the rate guarantee/ticker — rate honesty is core to the brand
- Don't animate aggressively — roll-ups and transitions are smooth (200–500ms)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Total caps ~34pt, 3 quick actions visible, tighter hero padding |
| iPhone 13/14/15 | 390pt | Standard hero, total ~40pt, 4 quick actions |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the top bar |
| iPhone 15/16 Pro Max | 430pt | Larger hero, more currency rows above the fold |
| iPad | 768pt+ | Two-pane (accounts list + activity), centered send flow, larger fee card |

### Dynamic Type
- Titles, body, names, amounts: full scale
- Hero total: scales but caps to protect the single-line layout
- Tab labels, uppercase labels: fixed
- Currency-row figures: fixed tabular size for column alignment

### Orientation
- All primary tabs and the send flow: **portrait-locked** on iPhone
- iPad: landscape supported with split layout

### Touch Targets
- Bright-green CTA: 52pt height — unmissable
- Quick-action circles: 52pt
- Transaction / currency rows: 64pt, full-row tappable
- Tab icons: 24pt glyph, 44pt effective hit
- Stepper dots: 44pt hit area each

### Safe Area Handling
- Top: top bar + hero respect safe area / Dynamic Island
- Bottom: tab bar opaque, respects home indicator; the send flow's sticky "Continue" floats above it
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface neutral: `#F7F7F7`
- Divider: `#E5E5E5`
- Text primary: `#0E0F0C`
- Text secondary: `#6B6F66`
- Forest (structure): `#163300`
- Forest hero surface: `#0E2200`
- Bright Green (action): `#9FE870`
- Bright Green pressed: `#8AD45C`
- Bright Green tint: `#EAF9DC`
- Success green: `#2F8F4E`
- Error red: `#D4332B`

### Example Component Prompts
- "Create a SwiftUI Wise currency balance row: 64pt tall on white with a 1pt #E5E5E5 bottom divider. Leading 32pt circular flag chip, then 'EUR' in Inter 16pt weight 600 #0E0F0C with 'Euro' 13pt weight 400 #6B6F66 below. Trailing balance '€3,180.55' in Inter 22pt weight 800 #0E0F0C, tabular numerals, right-aligned. Pressed background #F7F7F7."
- "Build the Wise primary CTA: 52pt tall full-width button, 16pt radius, background #9FE870, 'Send money' in Inter 16pt weight 800 #163300 (forest — never white on bright green). Pressed: background #8AD45C, scale 0.98, soft haptic."
- "Design the Wise forest account hero: full-width panel, background #0E2200, 20pt radius, 24pt padding, shadow rgba(14,34,0,0.25) 0 12px 32px. 'TOTAL BALANCE' label 11pt #9FE870 uppercase, total '£12,480.65' in 40pt weight 800 white tabular (roll digits into place on load), and a #9FE870 'Add money' affordance."
- "Build the Wise fee-transparency card: white card, 1pt #E5E5E5, 16pt radius, 20pt padding. Itemized rows label↔value: 'You send £1,000.00', 'Our fee £4.31', 'Amount we convert £995.69', 'Guaranteed rate 1.1632', and an emphasized 'Recipient gets €1,158.10' line in forest with a #EAF9DC highlight band. A vertical guide line connects the steps. All values tabular."
- "Create the Wise send-money stepper: numbered steps Recipient → Amount → Review → Pay. Completed step is a #9FE870 dot with a forest check, active is a forest dot with white numeral, upcoming is #E5E5E5; connecting rail #E5E5E5 fills forest as steps complete."

### Iteration Guide
1. Canvas is bright `#FFFFFF`; keep one dark anchor — the forest `#0E2200` hero
2. Forest `#163300` is the structural ink (titles, hero, numbers); Bright Green `#9FE870` is action/highlight only
3. Forest text on bright green — `#163300`, never white on `#9FE870`
4. The total/balance is the heaviest element — 32–40pt weight 800, tabular numerals
5. Money columns (currency rows, fee lines, total) are always tabular and decimal-aligned
6. The fee-transparency card with the highlighted "Recipient gets" line is the signature object — never hide the cost
7. State the mid-market rate plainly with a 24h guarantee; pulse a tiny live dot
8. Roll numbers into place (~500ms) so money visibly lands; separate with 1pt borders, soft green-tinted shadow only for the hero/press
