# Design System Inspiration of Charles Schwab (iOS)

## 1. Visual Theme & Atmosphere

The Charles Schwab iOS app is **the digital lobby of a 50-year-old brokerage** — confident, trustworthy, and built to make handling six- and seven-figure accounts feel safe rather than thrilling. Where a fintech challenger uses gradients and confetti, Schwab uses a deep maritime navy, a single bright cyan-blue action color, and rows of precisely-aligned numbers. Open the app and you land on the **Accounts** screen: a navy gradient hero carrying your **Total Value** in a large 34pt numeral and a green/red day-change line, then a stacked list of every account you hold — Individual Brokerage, Roth IRA, Schwab Bank Checking — each with its balance and percent change, and a prominent **Trade** button. The brand voice is "we have done this for decades; your money is in steady hands."

The signature gesture of the app is the **account-summary → trade pairing**: the Accounts list is the entry point, and the **trade ticket** is the destination. Tap an account → see Positions/Balances/Activity → tap a symbol → see the quote → tap **Trade** → land in the ticket: a segmented **Buy/Sell** control, Symbol, Quantity, Order type (Market/Limit/Stop), price, and a live **Estimated cost** card, finished by a **Review order** then **Place order** flow. This account → trade spine, and the ticket itself, is the most recognizable, most-used surface in the product and is treated as the visual centerpiece.

The color system is exceptionally restrained. The brand is **Schwab Blue** (`#009DDC`, the bright cyan-leaning blue from the logo) on **Schwab Navy** (`#003B5C`, the deep institutional navy). Schwab Blue is the one-and-only action color — primary buttons, the Trade CTA, selected toggles, links (brightened to `#46BEEC` Sky on dark for contrast). Everything else lives in a navy-to-greyscale scale. As with any serious brokerage, **green and red are reserved strictly for financial semantics**: a number is green if it gained, red if it lost, neutral if flat — never a decorative green success state or a red destructive button that could be mistaken for a price move. The only saturated pixels on screen are the blue brand action and the green/red of performance.

Typography is a **clean humanist sans with tabular numerics**. Schwab's brand face is a custom humanist sans ("Charles Modern" in their system); on iOS it falls back to SF Pro, and the closest free substitute is **Source Sans 3** (Adobe, SIL OFL) — an open, legible humanist sans that matches Schwab's measured, professional tone. Every monetary figure — total value, account balances, prices, quantities, percentages, estimated cost — is set in **tabular lining figures** so columns align perfectly down the accounts list and trade ticket. Headings are 700–800 weight; body is 400; numbers lean 700–800 so the eye lands on the value.

**Key Characteristics:**
- Deep maritime navy surfaces — light `#FFFFFF` / navy `#0A1622` dark — confident, institutional, calm
- Schwab Blue (`#009DDC`) as the single action color on Schwab Navy (`#003B5C`)
- Navy gradient account hero (`#003B5C → #002A42`) carrying Total Value — kept even on dark
- Green/red strictly financial semantics — gain `#16895F`/`#18B07B`, loss `#C8443D`/`#E2564E`
- Account → trade spine — Accounts → Account detail → Quote → Trade ticket
- The trade ticket is the visual centerpiece — segmented Buy/Sell + tabular fields + estimated-cost card
- Tabular / monospaced numerals — balances, prices, quantities align down every column
- Accounts list cell: account icon, name, masked number, balance, % change
- Balances tiles — Cash available / Buying power / Market value / Day change in a 2-up grid
- Bottom tab bar: Accounts / Trade / Research / Messages / More — five-tab institutional nav

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Schwab Blue** (`#009DDC`): The single primary action color — Trade CTA, primary buttons, selected toggle fills, brand moments. On-color text is dark navy `#002233` (the blue is bright enough that white-on-blue fails contrast at small sizes).
- **Schwab Blue Pressed** (`#0080B5`): Pressed state of the primary button.
- **Schwab Navy** (`#003B5C`): Deep brand navy — account hero gradient, headers, secondary "Transfer" / banking actions (white text), brand lockups.
- **Sky Accent** (`#0078A8` on light / `#46BEEC` on dark): Links, selected tab tint, inline text actions where contrast on the dark navy requires a brighter blue.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Tint** (`#F2F6F8`): Grouped-list background, cards.
- **Surface Pressed** (`#E6EDF1`): Pressed / selected rows.
- **Divider** (`#DBE3E8`): Hairline dividers between list rows.
- **Hairline Strong** (`#C3CFD6`): Section separators, table rules.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0A1622`): Primary dark canvas — Schwab's maritime navy, NOT pure black.
- **Dark Surface 1** (`#11212F`): Cards, grouped sections, elevated surfaces.
- **Dark Surface 2** (`#18303F`): Account icons, raised rows, pressed surfaces, ticket fields.
- **Dark Divider** (`#1F3A4A`): Hairline dividers.
- **Hero Gradient** (`#003B5C → #002A42`): Account hero — same on light & dark.

### Text
- **Text Primary Light** (`#10222E`): Primary text — deep navy-black.
- **Text Secondary Light** (`#5A707C`): Metadata, labels.
- **Text Tertiary Light** (`#8597A0`): Disabled, lowest emphasis.
- **Text Primary Dark** (`#E6EEF3`): Primary text on dark.
- **Text Secondary Dark** (`#9DB2BF`): Metadata on dark.
- **Text Tertiary Dark** (`#6A8392`): Disabled on dark.
- **On-Hero Text** (`#FFFFFF` primary / `#9DC4D8` secondary): Text inside the navy hero.

### Semantic — Financial (the most important colors in the system)
| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| Gain (positive) | `#16895F` | `#18B07B` | Positive change, positive $ delta / % |
| Loss (negative) | `#C8443D` | `#E2564E` | Negative change, negative $ delta / % |
| Flat (unchanged) | `#5A707C` | `#9DB2BF` | 0.00% — uses secondary text color |
| On-Hero Gain | `#5FE3B0` | `#5FE3B0` | Day-change line inside the navy hero (brightened for navy bg) |

### Semantic — System
- **Error / Alert** (`#C8443D` light / `#E2564E` dark): Form validation, blocked-trade banners (visually identical to Loss — context disambiguates).
- **Warning Amber** (`#C8862B`): Caution banners, market-closed / extended-hours notices.
- **Success Confirmation** (`#16895F` light / `#18B07B` dark): Order-placed confirmation (same as Gain — intentional brand consistency).
- **Info Blue** (`#0078A8`): Informational links to disclosures, "Learn more".
- **Gold Tier** (`#C8A24B`): Schwab private-client / premium accent, rewards badges.

## 3. Typography Rules

### Font Family
- **Brand**: `Charles Modern` — Schwab's custom humanist sans used across brand and product.
- **iOS Fallback**: `-apple-system, 'SF Pro Text', 'SF Pro Display'` — system face on device.
- **Recommended free substitute**: `Source Sans 3` (by Paul D. Hunt for Adobe, SIL OFL) — open humanist sans, the closest free match for Schwab's measured professional tone.
- **Numeric treatment**: Tabular / monospaced lining figures **everywhere money appears** (total value, balances, prices, quantities, %, estimated cost). In Source Sans 3 use `font-feature-settings: "tnum" 1;`; on SF use `.monospacedDigit()`.
- **Fallback Stack**: `'Charles Modern', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Total Value Hero | Source Sans 3 | 34pt | 800 | 1.1 | -0.6pt | Total Value in the navy hero — tabular |
| Screen Title | Source Sans 3 | 26pt | 800 | 1.2 | -0.3pt | "Accounts", "Trade" — large title |
| Section Header | Source Sans 3 | 22pt | 700 | 1.25 | -0.2pt | "Place a trade", "Balances" |
| Card Title | Source Sans 3 | 18pt | 700 | 1.3 | -0.1pt | Account / position names |
| Body | Source Sans 3 | 16pt | 400 | 1.5 | 0pt | Paragraph copy, disclosures |
| Row Value | Source Sans 3 | 16pt | 800 | 1.3 | 0pt | Balance / value in a row — tabular |
| Account Name | Source Sans 3 | 15pt | 700 | 1.3 | 0pt | Account name in a list row |
| Meta | Source Sans 3 | 14pt | 400 | 1.4 | 0pt | "As of 4:00 PM ET · Delayed", masks |
| Numeric Mono | Source Sans 3 (tnum) | 13pt | 700 | 1.3 | 0pt | $ delta + % line — always tabular |
| Caption | Source Sans 3 | 12pt | 600 | 1.35 | 0pt | Account mask, footnotes |
| Button | Source Sans 3 | 16pt | 800 | 1.0 | 0.2pt | Primary / secondary buttons |
| Tab Label | Source Sans 3 | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Ticket Field | Source Sans 3 (tnum) | 16pt | 800 | 1.0 | 0pt | Right-aligned trade-ticket values |

### Principles
- **Money is tabular, always**: every currency figure, price, quantity, and percentage uses lining tabular figures so the accounts list and trade ticket align column-perfect. This is the most recognizable typographic trait.
- **Weight signals data importance**: total value and balances are 800; labels are 400–600. The number reads first.
- **Headings are bold humanist**: 700–800 weight, slightly negative tracking — confident and professional, never light or condensed.
- **Restrained scale**: compact ramp (10 → 34pt). Hierarchy is weight + tabular emphasis, not dramatic size jumps.
- **Dynamic Type first-class**: total value, titles, body, meta scale; tab labels, ticket field values, and table headers stay fixed for layout integrity.

## 4. Component Stylings

### Buttons

**Primary Button (Trade / Review order / Place order)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#009DDC` (Schwab Blue)
- Text: `#002233` (dark navy — the blue is too bright for white text at body size), Source Sans 3 16pt weight 800
- Padding: 14–15pt vertical, 28pt horizontal; full-width variant spans content inset
- Pressed: background `#0080B5` + scale 0.98
- Disabled: `#009DDC` at 40% opacity, text at 60%
- Sticky variant: pinned to bottom safe area on quote / ticket screens with a 0.5pt top divider + surface backdrop

**Navy / Secondary Action Button (Transfer / Move money)**
- Background: `#003B5C` (Schwab Navy)
- Text: `#FFFFFF`, Source Sans 3 16pt weight 800
- Same shape / padding as primary
- Pressed: darken 10% + scale 0.98

**Outline Button (Add funds / Secondary)**
- Background: transparent
- Border: 1.5pt `#0078A8` light / `#46BEEC` dark
- Text: `#0078A8` light / `#46BEEC` dark, Source Sans 3 15pt weight 700
- Corner radius: 8pt
- Pressed: 8% blue tint fill

**Pill / Filter Button**
- Background: `#F2F6F8` light / `#18303F` dark
- Border: 1pt divider
- Text: primary text, Source Sans 3 14pt weight 600
- Corner radius: 500pt (pill)
- Selected: fill `#009DDC`, text `#002233`

**Text Button (View all activity / All accounts)**
- Text: `#0078A8` light / `#46BEEC` dark, Source Sans 3 14pt weight 700
- No background, no underline
- Pressed: 60% opacity

### Core Atoms

**Account Card / List Cell**
- Height: 66pt
- Leading: 38pt account-type icon — `#18303F` rounded 10pt, glyph stroked in Sky `#46BEEC` (chart for brokerage, clock for retirement, card for bank)
- Middle: account name (15pt w700) over masked number (12pt w600 secondary, e.g. "Brokerage ••4471")
- Trailing (right-aligned): balance (16pt w800 tabular) over % change (12pt w700, gain green / loss red)
- Divider: 0.5pt hairline below
- Pressed: full-row `#18303F` highlight; chevron disclosure optional

**Account Hero (navy gradient)**
- Background: linear-gradient `#003B5C → #002A42`, 160°
- Row 1: screen title (22pt w800 white) + bell/alerts circular icon button
- "TOTAL VALUE" label: 12pt w600 `#9DC4D8`, uppercase, 0.2pt tracking
- Total value: 34pt w800 white tabular
- Day-change line: ▲/▼ + `+$5,204.18` (13pt w700 tabular `#5FE3B0` gain) + `(+0.86%) today` (`#9DC4D8`)

**Trade Ticket** (the centerpiece)
- Container: `#18303F` card, 12pt radius, 16pt padding
- Segmented Buy/Sell: inset track `#0A1622`, 8pt radius, 3pt padding; active segment `#009DDC` fill, text `#002233` 13pt w700; inactive text secondary
- Field rows: label (13pt w600 secondary, left) + value (16pt w800 tabular, right-aligned); 1pt divider between rows
- Estimated-cost row: same layout, value emphasized
- Sticky **Review order** primary button below; Review screen restates everything before **Place order**

**Balances Tiles**
- 2-column grid, 12pt gap
- Tile: `#18303F`, 12pt radius, 14×16pt padding
- Label (12pt w600 secondary) over value (19pt w800 tabular); day-change tile colors the value gain/loss

**Quote Card**
- `#18303F`, 12pt radius; symbol (16pt w800) + name (12pt secondary) | price (16pt w800 tabular) + % (12pt w700 gain/loss)

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: surface with `saturate(180%) blur(20px)` translucency, 0.5pt top divider
- Tabs (5): Accounts, Trade, Research, Messages, More
- Icon size: 22pt
- Active: `#46BEEC` (dark) / `#0078A8` (light), filled glyph
- Inactive: tertiary text, stroked glyph
- Labels: Source Sans 3 10pt w600, always shown

**Top Nav (Account / Quote / Ticket screens)**
- Height: 44pt + safe area
- Leading: back chevron + screen/account title (collapses on scroll past hero)
- Trailing: alerts bell + ⋯ overflow (statements, settings, share)
- Large-title collapses to inline on scroll (iOS large-title pattern); hero is the navy gradient block

**Segmented Control (Positions / Balances / Activity on an account)**
- Underline style: active segment carries a 2pt `#009DDC` underline, label primary; inactive secondary
- Height: 44pt; equal-width segments

### Input Fields

**Search Bar (Find a symbol / Research)**
- Height: 40pt
- Background: `#F2F6F8` / `#18303F`, 8pt corner radius
- Leading magnifier 16pt secondary; placeholder "Search symbol or keyword" 14pt secondary
- Focus: 1.5pt `#009DDC` border

**Trade Ticket Field (Quantity / Limit price)**
- Inline within the ticket card: label left, value right-aligned 16pt w800 tabular
- Tapping a field opens a numeric keypad sheet; the focused row shows a 1.5pt `#009DDC` bottom rule
- Error: row rule `#C8443D`, helper text loss red 12pt

### Distinctive Components

**Trade Ticket (full)**
- Symbol header (last price 22pt w800 tabular + day change gain/loss), Buy/Sell segmented, Quantity, Order type (Market/Limit/Stop) selector, price field (for limit/stop), Time-in-force, Estimated-cost card, sticky **Review order**
- Review screen: read-only restatement of every field in tabular figures + a final `#009DDC` **Place order** button; success screen with confirmation number

**Account Detail (Positions / Balances / Activity)**
- Segmented underline header; Positions = symbol/qty/value table (tabular, right-aligned, sortable); Balances = tiles grid; Activity = dated transaction rows

**Positions Table**
- Columns: Symbol | Qty | Price | Mkt value (tabular, right-aligned); sortable headers; flat hairline rows; footer total in 800 weight

**Research / Quote Detail**
- Symbol header, line/candlestick chart with range tabs (1D/5D/1M/6M/1Y/5Y), bid/ask, key stats grid, news list; sticky Trade button

**Message Center Row**
- Subject (16pt w700, unread = dot + bolder), preview (13pt secondary 1-line), timestamp (12pt secondary)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- List row vertical padding: 14pt; account cell height target 66pt
- Section header top margin: 16pt
- Screen content inset: 20pt horizontal (16pt for full-bleed hero / cards)

### Grid & Container
- iPhone: content at 20pt horizontal insets; the navy hero is full-bleed
- iPad: two-pane — accounts/research rail (360pt) + detail/ticket pane; max content width 760pt centered
- Lists are single-column hairline-separated; cards reserved for hero, ticket, balances tiles, quote

### Whitespace Philosophy
- **Trust through order**: rows are evenly spaced, numbers right-aligned, columns true — the layout itself signals precision
- **The hero gets weight**: the navy total-value block is given generous vertical room; lists below are efficient
- **Cards only where grouping matters**: hero, trade ticket, balances tiles, quote — never a card around a plain account row

### Border Radius Scale
- Square (0pt): Full-bleed hero, table rules, status bar region
- Subtle (4pt): Inline chips, small tags
- Standard (8pt): Buttons, search bar, segmented track, pills
- Comfortable (12pt): Cards (account hero inner, ticket, balances tiles, quote)
- Large (16–20pt): Bottom sheets, modal containers
- Pill (500pt): Filter pills, segmented toggles
- Circle (50%): Avatars, circular icon buttons (bell)

## 6. Depth & Elevation

Schwab is mostly flat — depth is functional, never decorative.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Account rows, plain sections, dividers |
| Hero (Level 1) | Navy gradient block, no shadow | Total-value hero |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` light / 1pt `#1F3A4A` border dark | Ticket / balances / quote cards |
| Sticky Bar (Level 2) | 0.5pt top divider + `saturate(180%) blur(20px)` backdrop | Sticky Trade / Review-order bar, tab bar |
| Sheet (Level 3) | `rgba(0,0,0,0.14) 0 -4px 24px` | Trade keypad sheet, order-type picker, filter sheet |
| Modal Scrim | `rgba(0,0,0,0.45)` | Dim behind sheets / alerts |

**Shadow Philosophy**: shadows say "grouped" or "floating and dismissible" — nothing more. On dark mode, shadows vanish against `#0A1622`, so cards and floating panels get a 1pt `#1F3A4A` border as the elevation cue. The navy hero needs no shadow — its gradient and color separate it from the canvas naturally. The sticky bar uses a translucent blurred backdrop so list content reads faintly beneath it.

### Motion
- **Total value / balance updates**: live ticks cross-fade the digit color to gain/loss for ~600ms then settle; digits do not roll (calm money UI)
- **Trade segmented toggle**: the active `#009DDC` pill slides between Buy/Sell over 200ms ease-out
- **Chart range switch**: tapping 1D→1Y morphs the path over 280ms ease-out
- **Push navigation**: standard iOS push (300ms horizontal slide) Accounts → Account → Quote → Ticket
- **Sticky Trade / Review button**: always visible; gains a top divider as content scrolls beneath
- **Trade keypad / order-type sheet**: slides up 300ms ease-out; Review pushes within the flow
- **Tab switch**: instant content swap; selected icon fills and tints with a 120ms cross-fade
- **Pull-to-refresh**: Schwab Blue spinner; light-impact haptic on trigger
- **Haptics**: light impact on tab change, segmented toggle, range-tab change; medium impact on "Place order"; success notification haptic on order accepted

## 7. Do's and Don'ts

### Do
- Use Schwab Blue (`#009DDC`) as the single action color; Schwab Navy (`#003B5C`) for the hero and money-movement actions
- Put dark navy text on Schwab Blue buttons (`#002233`) — white-on-`#009DDC` fails contrast at body sizes
- Reserve green/red strictly for financial semantics — gain `#16895F`/`#18B07B`, loss `#C8443D`/`#E2564E`
- Set every monetary figure in tabular/monospaced lining figures so the accounts list and ticket align
- Lead with the navy total-value hero — 34pt, bold, day-change line, kept even on dark
- Treat the trade ticket as the visual centerpiece — segmented Buy/Sell + tabular right-aligned fields + estimated-cost card
- Keep the Accounts → Account → Quote → Trade spine consistent in every screen's chrome
- Use a deep maritime navy `#0A1622` for dark mode — never pure black
- Use flat hairline single-column rows for the accounts list; cards only for hero/ticket/tiles/quote
- Use a 2-up Balances tile grid for Cash / Buying power / Market value / Day change

### Don't
- Don't use white text on Schwab Blue buttons — contrast fails; use dark navy `#002233`
- Don't use red for a generic destructive button or green for a non-financial success in a way that reads as a price move
- Don't use proportional figures for money — columns must align; tabular is mandatory
- Don't add marketing illustrations, mascots, or photographic heroes to core trading screens
- Don't animate the digits of a changing balance with a roll — only a brief color cross-fade
- Don't use pure black (`#000000`) for the dark canvas — `#0A1622` maritime navy is correct
- Don't put card borders/shadows around plain account rows — they are flat hairlines
- Don't introduce a second saturated accent — palette is Schwab Blue + semantic green/red on navy-greyscale
- Don't drop the navy gradient from the hero on dark mode — it carries the brand identity
- Don't over-animate; money UI is calm — 120–300ms ease-out, no bounce on financial data

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Total value 30pt; tighter 16pt insets; tiles stack to 1-up if cramped |
| iPhone 15/16 | 393pt | Standard layout; total value 34pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the navy hero |
| iPhone 15/16 Pro Max | 430pt | Total value 38pt; larger account rows |
| iPad (portrait) | 768pt | Two-pane: accounts/research rail (360pt) + detail/ticket pane |
| iPad (landscape) | 1024pt+ | Three-zone: nav rail + accounts list + ticket/quote detail |

### Dynamic Type
- Total value, screen title, section header, body, meta: full scale
- Tab labels, ticket field values, table column headers: FIXED (layout-sensitive)
- Tabular numerals remain tabular at all sizes (never substitute proportional)

### Orientation
- iPhone: portrait-primary; chart screens may rotate to a full-screen landscape chart with crosshair scrub
- iPad: split-view controller — primary rail + secondary detail; rotation preserves selection

### Touch Targets
- Account row: 66pt tall, full-row tap
- Tab bar icon: 22pt glyph, 56pt hit
- Segmented Buy/Sell: 40pt tall segments, full-segment tap
- Sticky Trade / Review button: 52pt tall minimum
- Bell / circular icon button: 34pt, 44pt hit

### Safe Area Handling
- Top: navy hero + top nav respect safe area and Dynamic Island
- Bottom: sticky Trade/Review button and tab bar respect home indicator; both add safe-area padding
- Keyboard: the numeric keypad sheet positions over the ticket; sticky bar repositions above it
- Sides: 20pt content inset; hero / cards extend to 16pt or full-bleed

## 9. Agent Prompt Guide

### Quick Color Reference
- Brand action (Schwab Blue): `#009DDC` — on-color text `#002233`
- Schwab Navy (hero / money movement): `#003B5C`
- Sky accent (dark links / selected): `#46BEEC` / light `#0078A8`
- Canvas (light): `#FFFFFF` / (dark): `#0A1622`
- Surface 1 (dark): `#11212F` · Surface 2 (dark): `#18303F`
- Divider (dark): `#1F3A4A` · (light): `#DBE3E8`
- Text primary: `#10222E` light / `#E6EEF3` dark
- Text secondary: `#5A707C` light / `#9DB2BF` dark
- Gain: `#16895F` light / `#18B07B` dark
- Loss: `#C8443D` light / `#E2564E` dark
- On-hero gain: `#5FE3B0` · Hero gradient: `#003B5C → #002A42`
- Gold tier: `#C8A24B` · Info link: `#0078A8`

### Example Component Prompts
- "Build the Schwab Accounts hero in SwiftUI: a 160° linear gradient `#003B5C → #002A42` block, full-bleed. Row 1: 'Accounts' (22pt w800 white) and a 34pt circular bell button (`rgba(255,255,255,0.10)` fill, white stroke icon). 'TOTAL VALUE' label (12pt w600 `#9DC4D8`, uppercase). Total `$612,408.55` in Source Sans 3 34pt w800 white `.monospacedDigit()`. Day-change line: `▲ +$5,204.18` (13pt w700 `#5FE3B0`) + `(+0.86%) today` in `#9DC4D8`."

- "Create a Schwab account card: 66pt tall, leading 38pt icon chip (`#18303F` bg, 10pt radius, glyph stroked `#46BEEC`), middle = account name 15pt w700 over masked number 12pt w600 `#9DB2BF` ('Brokerage ••4471'), trailing right-aligned = balance 16pt w800 tabular over % change 12pt w700 (`#18B07B` up / `#E2564E` down). 0.5pt `#1F3A4A` divider below; pressed = full-row `#18303F`."

- "Build the Schwab trade ticket: a `#18303F` card (12pt radius, 16pt padding). Segmented Buy/Sell — inset `#0A1622` track, 8pt radius, 3pt pad; active segment `#009DDC` fill text `#002233` 13pt w700, inactive `#9DB2BF`. Then field rows (Symbol, Quantity, Order type, Limit price, Est. cost): label 13pt w600 `#9DB2BF` left + value 16pt w800 tabular right-aligned, 1pt `#1F3A4A` divider between. Below, a full-width `#009DDC` 'Review order' button (text `#002233` 16pt w800, 8pt radius, 52pt tall)."

- "Render the Schwab sticky Trade bar: pinned to bottom safe area, surface backdrop `saturate(180%) blur(20px)`, 0.5pt `#1F3A4A` top divider, a full-width `#009DDC` button 'Trade' (text `#002233` Source Sans 3 16pt w800, 52pt tall, 8pt radius); pressed `#0080B5` + scale 0.98; medium haptic on tap."

- "Create the Schwab Balances tiles: a 2-column grid, 12pt gap. Each tile `#18303F`, 12pt radius, 14×16pt padding: label 12pt w600 `#9DB2BF` over value 19pt w800 tabular. The 'Day change' tile colors its value `#18B07B` if positive (`#E2564E` if negative). Cash available / Buying power / Market value / Day change."

- "Render a Schwab segmented control (Positions / Balances / Activity): equal-width segments, 44pt tall; active segment label `#E6EEF3` with a 2pt `#009DDC` underline; inactive `#9DB2BF`; switching slides nothing — just swaps the underline + content, light haptic."

### Iteration Guide
1. Canvas is `#FFFFFF` light / maritime navy `#0A1622` dark — never pure black, never gray
2. Schwab Blue `#009DDC` is the ONE action color; Schwab Navy `#003B5C` for hero + money movement — nothing else saturated except semantics
3. Dark navy text on Schwab Blue buttons (`#002233`) — white-on-blue fails contrast
4. Green/red mean gain/loss ONLY — `#16895F`/`#18B07B` gain, `#C8443D`/`#E2564E` loss — never decorative
5. Every dollar, price, %, and quantity uses tabular/monospaced figures so columns align — non-negotiable
6. The navy total-value hero leads — 34pt w800, day-change line, gradient kept even on dark
7. The trade ticket is the centerpiece — segmented Buy/Sell + right-aligned tabular fields + estimated-cost card
8. The spine is Accounts → Account → Quote → Trade ticket — one calm chrome on every step
9. Accounts are flat hairline single-column rows — cards only for hero/ticket/tiles/quote
10. Motion is calm — color cross-fade on balance ticks, 200ms segmented slide, 280ms chart morph, no rolling digits
