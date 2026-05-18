# Design System Inspiration of Fidelity (iOS)

## 1. Visual Theme & Atmosphere

Fidelity's iOS app is **institutional calm rendered as software**. It is the home screen of one of the largest brokerages in the world, and the design carries that weight: the interface is dense with numbers, but the chrome around those numbers is deliberately quiet so the data never has to compete with decoration. Open the app and you land on the **Summary** screen — a greeting, your total account value set in a huge 34pt numeral, a green/red day-change line, a sparkline, and then a stacked list of accounts, watchlist symbols, and positions. There is no hero photograph, no marketing illustration, no playful empty-state mascot. The "hero" is your money, typeset large.

The signature gesture of the app is the **portfolio + quote pairing**: the Summary screen's value header and sparkline sit directly above a tap-through list that opens a full **quote screen** — symbol, last price, day change, an interactive line/candlestick chart with 1D/1W/1M/1Y/5Y/All ranges, bid/ask, and a sticky **Trade** button pinned to the bottom. This drill path (Summary → Account → Position → Quote → Trade ticket) is the spine of the entire product, and every screen on it shares the same visual language: a calm dark or light surface, a green primary action, and semantic green/red for performance.

The color system is unusually disciplined. Fidelity's brand is **Fidelity Green** (`#368727`) with a deeper **Heritage Green** (`#00754A`) — these carry logos, primary buttons, selected states, and brand moments. The single most important rule in the entire system is that **green and red are reserved for financial semantics**: a number is green if it gained, red if it lost, neutral gray if flat. The app never uses red for a destructive button styling or green for a generic success toast in a way that could be confused with a price move. Everything else — backgrounds, dividers, labels, icons — lives in a near-greyscale scale (with a faint green cast in dark mode) so the only saturated pixels on screen are the ones that mean something financially.

Typography is a **clean grotesque sans paired with a tabular/monospaced numeric treatment**. Fidelity's brand face is a custom grotesque ("Average Sans" family in their brand system); on iOS it falls back to SF Pro, and a faithful free substitute is **Inter**. Balances, prices, percentages, and dollar deltas are set with **tabular figures** so columns of money line up perfectly down a list — this monospaced-numeral discipline is a core part of the brand's "we are precise with your money" feeling. Headings are 700 weight; body is 400; numeric data leans 600–700 for emphasis.

**Key Characteristics:**
- Calm institutional surfaces — light `#FFFFFF` / deep evergreen `#0E1411` dark — minimum chrome, maximum data
- Fidelity Green (`#368727`) + Heritage Green (`#00754A`) brand anchors — primary actions, logos, selected states
- Green/red are sacred financial semantics (`#15833E` gain / `#D32F2F` loss light; `#15B374` / `#E5544B` dark) — never decorative
- Total account value as the screen hero — 34pt numeral, day-change line, sparkline directly below
- Portfolio → quote drill path is the product spine — Summary → Account → Position → Quote → Trade
- Tabular / monospaced numerals — money columns align perfectly down every list
- Sparkline + range tabs (1D / 1W / 1M / 1Y / 5Y / All) — the recurring chart unit
- Sticky bottom **Trade** button on every quote screen — the primary conversion affordance
- Bottom tab bar: Summary / Planning / Invest / News / Profile — five-tab institutional nav
- Watchlist + holdings rows: ticker chip, name, price, % change — the atomic list cell

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Fidelity Green** (`#368727`): Primary brand & action color — Trade button, primary CTAs, brand moments, selected toggle fills.
- **Fidelity Green Pressed** (`#2A6B1F`): Pressed state of the primary button.
- **Heritage Green** (`#00754A`): Deeper brand green — secondary brand surfaces, logo lockups, "Transfer" / banking actions, headers.
- **Leaf Accent** (`#4FB23B` light contexts use `#368727`; dark uses `#4FB23B`): Links, selected tab tint, inline text actions where contrast on dark requires a brighter green.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F6F4`): Grouped-list background, cards, subtle elevated rows.
- **Surface Pressed** (`#E8ECE8`): Pressed / selected rows.
- **Divider** (`#E1E5E1`): Hairline dividers between list rows.
- **Hairline Strong** (`#C9D0CA`): Section separators, table rules.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0E1411`): Primary dark canvas — Fidelity's evergreen-tinted dark, NOT pure black.
- **Dark Surface 1** (`#16201B`): Cards, grouped sections, elevated surfaces.
- **Dark Surface 2** (`#1F2C24`): Ticker chips, raised rows, pressed surfaces.
- **Dark Divider** (`#26342B`): Hairline dividers.

### Text
- **Text Primary Light** (`#1A1F1B`): Primary text — near-black with a faint warm-green cast.
- **Text Secondary Light** (`#5A655C`): Metadata, labels, secondary rows.
- **Text Tertiary Light** (`#8A938C`): Disabled, lowest emphasis.
- **Text Primary Dark** (`#E8EFE9`): Primary text on dark.
- **Text Secondary Dark** (`#9DB0A2`): Metadata on dark.
- **Text Tertiary Dark** (`#6B7E72`): Disabled on dark.

### Semantic — Financial (the most important colors in the system)
| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| Gain (positive) | `#15833E` | `#15B374` | Positive price change, positive $ delta, positive % |
| Loss (negative) | `#D32F2F` | `#E5544B` | Negative price change, negative $ delta, negative % |
| Flat (unchanged) | `#5A655C` | `#9DB0A2` | 0.00% change — uses secondary text color |
| Up Fill (chart) | `#36872729` | `#36872729` | Sparkline / area chart gradient fill (16% green) |

### Semantic — System
- **Error / Alert** (`#D32F2F` light / `#E5544B` dark): Form validation, blocked-action banners (visually identical to Loss — context disambiguates).
- **Warning Amber** (`#C8862B`): Caution banners, pending-transfer notices.
- **Success Confirmation** (`#15833E` light / `#15B374` dark): Order-placed confirmation (same as Gain — intentional brand consistency).
- **Info Blue** (`#1E6FB8`): Informational links to disclosures, "Learn more".
- **Gold Tier** (`#C8A24B`): Premium / Private Client Group accent, rewards tier badges.

## 3. Typography Rules

### Font Family
- **Brand**: `Average Sans` — Fidelity's custom grotesque sans used across brand and product.
- **iOS Fallback**: `-apple-system, 'SF Pro Text', 'SF Pro Display'` — system face on device.
- **Recommended free substitute**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist grotesque with excellent tabular-figure support, the closest free match.
- **Numeric treatment**: Tabular / monospaced lining figures **everywhere money appears** (balances, prices, %, $ deltas, table columns). In Inter use `font-feature-settings: "tnum" 1;`; on SF use `.monospacedDigit()`.
- **Fallback Stack**: `'Average Sans', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance Hero | Inter | 34pt | 800 | 1.1 | -0.6pt | Total account value — tabular figures |
| Screen Title | Inter | 26pt | 700 | 1.2 | -0.3pt | "Accounts", "Summary" — large-title nav |
| Section Header | Inter | 22pt | 700 | 1.25 | -0.2pt | "Today's positions", "Watchlist" |
| Card Title | Inter | 18pt | 700 | 1.3 | -0.1pt | Fund / position names |
| Body | Inter | 16pt | 400 | 1.5 | 0pt | Paragraph copy, descriptions |
| Row Value | Inter | 16pt | 700 | 1.3 | 0pt | Price / value in a list row — tabular |
| Row Symbol | Inter | 15pt | 600 | 1.3 | 0pt | Ticker symbol in a holding row |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | "Last updated 4:00 PM ET", account masks |
| Numeric Mono | Inter (tnum) | 13pt | 600 | 1.3 | 0pt | $ delta + % change line — always tabular |
| Caption | Inter | 12pt | 500 | 1.35 | 0pt | Account type, disclosure footnotes |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Range Tab | Inter | 11pt | 600 | 1.0 | 0.2pt | 1D / 1W / 1M / 1Y chart range chips |

### Principles
- **Money is tabular, always**: every figure that represents currency, a price, a quantity, or a percentage uses lining tabular figures so columns align down a list. This is the single most recognizable typographic trait of the brand.
- **Weight signals data importance**: balances and prices are 700–800; labels are 400–500. The eye should land on the number first.
- **Headings are bold grotesque**: 700 weight, slightly negative tracking — institutional, confident, never light or condensed.
- **Restrained scale**: the type ramp is compact (10 → 34pt). Hierarchy comes from weight and tabular emphasis, not dramatic size jumps.
- **Dynamic Type first-class**: balance, titles, body, meta scale; tab labels, range tabs, and chart-axis labels stay fixed for layout integrity.

## 4. Component Stylings

### Buttons

**Primary Button (Trade / Continue / Place Order)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#368727` (Fidelity Green)
- Text: `#FFFFFF`, Inter 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal; full-width variant spans content inset minus 16pt
- Pressed: background `#2A6B1F` + scale 0.98
- Disabled: `#368727` at 40% opacity, text `#FFFFFF` at 60%
- Sticky variant: pinned to bottom safe area on quote / trade screens with a 0.5pt top divider and surface backdrop

**Heritage / Secondary Action Button (Transfer / Move money)**
- Background: `#00754A` (Heritage Green)
- Text: `#FFFFFF`, Inter 16pt weight 700
- Same shape / padding as primary
- Pressed: darken 10% + scale 0.98

**Outline Button (Add to watchlist / Secondary)**
- Background: transparent
- Border: 1.5pt `#368727` light / `#4FB23B` dark
- Text: `#368727` light / `#4FB23B` dark, Inter 15pt weight 700
- Corner radius: 8pt
- Pressed: 8% green tint fill

**Pill / Filter Button**
- Background: `#F4F6F4` light / `#1F2C24` dark
- Border: 1pt divider
- Text: primary text, Inter 14pt weight 600
- Corner radius: 500pt (pill)
- Selected: fill `#368727`, text `#FFFFFF`

**Text Button (View all holdings / Edit)**
- Text: `#368727` light / `#4FB23B` dark, Inter 14pt weight 700
- No background, no underline
- Pressed: 60% opacity

### Core Atoms

**Holding / Watchlist Row**
- Height: 64pt
- Leading: 38pt ticker chip — `#F4F6F4` / `#1F2C24` rounded 10pt, symbol in 12pt weight 800; brand-held funds tint to 18% green
- Middle: symbol (15pt w600) over name (12pt w400 secondary, truncates)
- Trailing (right-aligned): value (16pt w700 tabular) over % change (12pt w600, gain green / loss red)
- Divider: 0.5pt hairline below
- Pressed: full-row `#E8ECE8` / surface-2 highlight; chevron disclosure optional

**Balance Header Block**
- Greeting: 13pt w500 secondary
- Screen title: 26pt w700
- "Total account value" label: 12pt w500 secondary
- Balance: 34pt w800 tabular
- Day-change line: triangle glyph + `+$3,841.20` (13pt w600 tabular gain/loss) + `(+0.90%) today` (muted)

**Sparkline / Area Chart**
- Stroke: 2pt — `#4FB23B` (gain day) or `#E5544B` (loss day)
- Fill: vertical gradient of the stroke color, 32% → 0% opacity
- Baseline: optional 0.5pt dashed divider at the open price
- Range tabs below: 1D / 1W / 1M / 1Y / 5Y / All — 11pt w600 pills; active = `#368727` fill, white text

**Quote Card**
- Background: `#F4F6F4` / `#1F2C24`, 12pt corner radius, 14×16pt padding
- Left: symbol (16pt w800) over name (12pt secondary)
- Right: price (16pt w700 tabular) over % (12pt w700 gain/loss)

**Performance Pill**
- Padding: 8pt × 14pt, 8pt corner radius
- Gain: bg `#15B37429` (16% gain green), text gain green, leading ▲
- Loss: bg `#E5544B29`, text loss red, leading ▼
- Flat: bg surface-2, text secondary, leading —

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: surface with `saturate(180%) blur(20px)` translucency, 0.5pt top divider
- Tabs (5): Summary, Planning, Invest, News, Profile
- Icon size: 22pt
- Active: `#4FB23B` (dark) / `#368727` (light), filled glyph
- Inactive: tertiary text, stroked glyph
- Labels: Inter 10pt w600, always shown

**Top Nav (Quote / Detail screens)**
- Height: 44pt + safe area
- Leading: back chevron + screen/symbol title (compact, appears on scroll past hero)
- Trailing: watchlist star (toggle) + ⋯ overflow (alerts, share, account actions)
- Large-title collapses to inline title on scroll (iOS large-title pattern)

**Segmented Control (Positions / Activity / Balances on an account)**
- Underline style: active segment carries a 2pt `#368727` underline, label in primary; inactive in secondary
- Height: 44pt; full-width equal segments

### Input Fields

**Search Bar (Find a symbol)**
- Height: 40pt
- Background: `#F4F6F4` / `#1F2C24`, 8pt corner radius
- Leading magnifier 16pt secondary; placeholder "Search quotes & news" 14pt secondary
- Focus: 1.5pt `#368727` border

**Trade Ticket Field (Quantity / Limit price)**
- Height: 52pt
- Background: surface, 8pt corner radius, 1pt divider border
- Label floats above (12pt w600 secondary); value 18pt w700 tabular, right-aligned
- Focus: border `#368727` 1.5pt
- Error: border `#D32F2F`, helper text in loss red 12pt

### Distinctive Components

**Trade Ticket**
- Full-sheet form: symbol header (price + day change), Action (Buy/Sell segmented), Quantity, Order type (Market/Limit/Stop), Time-in-force, estimated cost summary card, sticky **Preview order** primary button
- Estimated-cost card: surface, 12pt radius, label/value rows with tabular figures
- Review screen restates everything before a final green **Place order** button

**Account Summary Card**
- Account name + masked number (••3814), 18pt w700 title
- Value: 22pt w800 tabular
- Day change line in gain/loss
- Tap → account detail (Positions / Activity / Balances segmented)

**Positions Table**
- Columns: Symbol | Last | Day chg % | Mkt value (tabular, right-aligned)
- Sortable headers; alternating-free flat rows separated by hairlines
- Footer total row in 700 weight

**News / Research Tile**
- Headline (16pt w700, 2-line clamp), source + timestamp (12pt secondary), optional related-symbol chips with inline gain/loss

**Performance Donut / Asset-Allocation Ring**
- Ring segments use a calm tonal palette (greens + neutral grays + one gold for "Other"); center shows total value tabular; legend rows below with % tabular

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- List row vertical padding: 12pt; row height target 64pt
- Section header top margin: 20pt
- Screen content inset: 20pt horizontal (16pt for full-bleed cards/charts)

### Grid & Container
- iPhone: content at 20pt horizontal insets; charts and grouped cards may extend to 16pt
- iPad: grouped two-column layout — accounts/watchlist rail + detail pane; max content width 760pt centered
- Lists are single-column, hairline-separated; no card borders on simple rows (cards only for summary/quote/estimate blocks)

### Whitespace Philosophy
- **Data density with breathing rows**: rows are compact but never cramped — 12pt vertical padding keeps numbers scannable
- **Cards only where grouping adds meaning**: account summary, quote, estimated cost — never a card around a plain list row
- **The number gets the space**: the balance hero is given generous vertical room; everything else is efficient

### Border Radius Scale
- Square (0pt): Full-bleed charts, table rules, status bar region
- Subtle (4pt): Inline chips, small tags
- Standard (8pt): Buttons, search bar, input fields, performance pills
- Comfortable (12pt): Cards (account, quote, estimated cost)
- Large (16–20pt): Bottom sheets, modal containers, chart panel
- Pill (500pt): Filter pills, range tabs, segmented toggles
- Circle (50%): Avatars, donut chart center hole

## 6. Depth & Elevation

Fidelity is mostly flat — depth is used sparingly and functionally, never decoratively.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, plain sections, dividers |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 1px 3px` light / 1pt `#26342B` border dark | Account / quote / estimate cards |
| Sticky Bar (Level 2) | 0.5pt top divider + `saturate(180%) blur(20px)` backdrop | Sticky Trade button bar, tab bar |
| Sheet (Level 3) | `rgba(0,0,0,0.12) 0 -4px 24px` | Trade ticket sheet, filter sheet |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind sheets / alerts |

**Shadow Philosophy**: shadows say "this groups separately" or "this floats above and is dismissible" — nothing more. On dark mode, shadows are barely visible against `#0E1411`, so cards and floating panels get a 1pt `#26342B` border as the elevation cue instead. The sticky Trade bar uses a translucent blurred backdrop so scrolling content reads faintly beneath it without losing the button's prominence.

### Motion
- **Balance / number updates**: live price ticks cross-fade the digit color to gain/loss for 600ms then settle; the digits themselves do not slide (avoid distracting motion on money)
- **Chart range switch**: tapping 1D→1Y morphs the path with a 280ms ease-out interpolation; the fill gradient follows
- **Chart scrub**: dragging a finger across the chart shows a vertical crosshair + value bubble that tracks 1:1 with the touch, no easing
- **Push navigation**: standard iOS push (300ms horizontal slide) for Account → Position → Quote
- **Sticky Trade button**: always visible; on scroll it gains a subtle top divider as content passes beneath
- **Trade ticket sheet**: slides up 300ms ease-out; review screen pushes within the sheet
- **Tab switch**: instant content swap; selected icon fills and tints with a 120ms cross-fade
- **Pull-to-refresh**: green spinner in Fidelity Green; haptic light-impact on trigger
- **Haptics**: light impact on tab change and range-tab change; medium impact on "Place order" confirmation; success notification haptic on order filled

## 7. Do's and Don'ts

### Do
- Use Fidelity Green (`#368727`) for the primary action and brand moments; Heritage Green (`#00754A`) for money-movement actions
- Reserve green/red strictly for financial semantics — gain `#15833E`/`#15B374`, loss `#D32F2F`/`#E5544B`
- Set every monetary figure in tabular/monospaced lining figures so columns align
- Make the total account value the visual hero — 34pt, bold, with a day-change line and sparkline directly below
- Keep the Summary → Account → Position → Quote → Trade drill path consistent in every screen's chrome
- Pin a sticky green **Trade** button to the bottom of every quote screen
- Use a deep evergreen `#0E1411` for dark mode — never pure black
- Use hairline-separated single-column lists for holdings; reserve cards for summary/quote/estimate groupings
- Keep range tabs (1D/1W/1M/1Y/5Y/All) as compact pills under every chart
- Use a calm tonal ring (greens + grays + one gold) for asset allocation

### Don't
- Don't use red for a generic destructive button or green for a non-financial success toast in a way that reads like a price move
- Don't use proportional figures for money — columns must align; tabular is mandatory
- Don't add marketing illustrations, mascots, or photographic heroes to core trading screens
- Don't animate the digits of a changing balance with a slide/roll — only a brief color cross-fade
- Don't use pure black (`#000000`) for the dark canvas — `#0E1411` evergreen is correct
- Don't put card borders/shadows around plain list rows — Fidelity lists are flat hairlines
- Don't introduce a second saturated accent color — the palette is green + semantic red on near-greyscale
- Don't bury the Trade button below the fold — it is sticky and always reachable
- Don't use bright/neon greens for brand surfaces in light mode — `#368727`/`#00754A` are the institutional anchors
- Don't over-animate; money UI is calm — 120–300ms ease-out, no bounce on financial data

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Balance hero 30pt; tighter 16pt insets; sparkline 80pt tall |
| iPhone 15/16 | 393pt | Standard layout; balance 34pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Balance hero 38pt; chart 120pt tall; larger rows |
| iPad (portrait) | 768pt | Two-pane: accounts/watchlist list (360pt) + detail pane |
| iPad (landscape) | 1024pt+ | Three-zone: nav rail + list + quote/chart detail; chart full-height |

### Dynamic Type
- Balance hero, screen title, section header, body, meta: full scale
- Tab labels, range tabs, chart-axis labels, table column headers: FIXED (layout-sensitive)
- Tabular numerals remain tabular at all sizes (never substitute proportional)

### Orientation
- iPhone: portrait-primary; charts may rotate to a full-screen landscape chart view with crosshair scrubbing
- iPad: split-view controller — primary list rail + secondary detail; rotation preserves selection

### Touch Targets
- List row: 64pt tall, full-row tap
- Tab bar icon: 22pt glyph, 56pt hit
- Range-tab pill: 28pt tall, 44pt hit
- Sticky Trade button: 52pt tall minimum
- Watchlist star toggle: 24pt glyph, 44pt hit

### Safe Area Handling
- Top: large-title and top nav respect safe area and Dynamic Island
- Bottom: sticky Trade button and tab bar respect home indicator; both add safe-area padding
- Keyboard: trade-ticket fields scroll above the keyboard; sticky bar repositions above the keyboard during entry
- Sides: 20pt content inset; charts/cards extend to 16pt

## 9. Agent Prompt Guide

### Quick Color Reference
- Brand / primary action (Fidelity Green): `#368727`
- Heritage Green (money movement): `#00754A`
- Leaf accent (dark links / selected): `#4FB23B`
- Canvas (light): `#FFFFFF` / (dark): `#0E1411`
- Surface 1 (dark): `#16201B` · Surface 2 (dark): `#1F2C24`
- Divider (dark): `#26342B` · (light): `#E1E5E1`
- Text primary: `#1A1F1B` light / `#E8EFE9` dark
- Text secondary: `#5A655C` light / `#9DB0A2` dark
- Gain: `#15833E` light / `#15B374` dark
- Loss: `#D32F2F` light / `#E5544B` dark
- Flat: secondary text color
- Gold tier: `#C8A24B` · Info link: `#1E6FB8`

### Example Component Prompts
- "Build the Fidelity Summary header in SwiftUI: greeting 'Good morning' (13pt w500 secondary), screen title 'Accounts' (26pt w700), 'Total account value' label (12pt w500 secondary), then the balance `$428,164.92` in Inter 34pt weight 800 with `.monospacedDigit()`, then a day-change row: filled up-triangle + `+$3,841.20` (13pt w600 tabular, `#15B374`) + `(+0.90%) today` in `#9DB0A2`. Dark canvas `#0E1411`."

- "Create a Fidelity holding row: 64pt tall, leading 38pt ticker chip (`#1F2C24` bg, 10pt radius, symbol 12pt w800; if brand fund tint bg to `#36872729` and text `#4FB23B`), middle = symbol 15pt w600 over fund name 12pt w400 `#9DB0A2` (truncate), trailing right-aligned = market value 16pt w700 tabular over % change 12pt w600 (`#15B374` if up, `#E5544B` if down). 0.5pt `#26342B` divider below; pressed = full-row `#1F2C24`."

- "Render a Fidelity sparkline + range tabs: 2pt stroke in `#4FB23B` for an up day (or `#E5544B` down), vertical gradient fill of the same color 32%→0%. Below: pill row 1D / 1W / 1M / 1Y / 5Y / All, Inter 11pt w600; active pill `#368727` fill white text, inactive secondary text. Tapping a range morphs the path over 280ms ease-out. Finger-drag shows a vertical crosshair + tabular value bubble tracking 1:1."

- "Build the Fidelity sticky Trade bar: pinned to bottom safe area, surface backdrop with `saturate(180%) blur(20px)`, 0.5pt `#26342B` top divider, containing a full-width `#368727` button 'Trade', Inter 16pt w700 white, 52pt tall, 8pt corner radius; pressed `#2A6B1F` + scale 0.98; medium haptic on tap."

- "Create the Fidelity trade ticket sheet: symbol header (price 22pt w700 tabular + day change gain/loss), Buy/Sell segmented (underline-style, active 2pt `#368727`), Quantity field (52pt, label floats 12pt w600 secondary, value 18pt w700 tabular right-aligned, focus border `#368727` 1.5pt), Market/Limit/Stop segmented, an estimated-cost card (surface, 12pt radius, label/value tabular rows), and a sticky green `Preview order` primary button. Sheet slides up 300ms ease-out."

- "Render a Fidelity performance pill set: 8pt radius, 8×14pt padding. Gain pill = bg `#15B37429`, text `#15B374`, leading ▲. Loss pill = bg `#E5544B29`, text `#E5544B`, leading ▼. Flat = surface-2 bg, `#9DB0A2` text, leading —. All numerals tabular."

### Iteration Guide
1. Canvas is `#FFFFFF` light / deep evergreen `#0E1411` dark — never pure black, never gray
2. Fidelity Green `#368727` is the brand/action; Heritage `#00754A` for money movement — nothing else is saturated except semantics
3. Green/red mean gain/loss ONLY — `#15833E`/`#15B374` gain, `#D32F2F`/`#E5544B` loss — never decorative
4. Every dollar, price, %, and quantity uses tabular/monospaced figures so columns align — non-negotiable
5. The total account value is the screen hero — 34pt w800, day-change line + sparkline directly under it
6. The product spine is Summary → Account → Position → Quote → Trade — same calm chrome on every step
7. A sticky green Trade button is pinned to the bottom of every quote screen
8. Holdings are flat hairline-separated single-column rows — cards only for summary/quote/estimate blocks
9. Range tabs (1D/1W/1M/1Y/5Y/All) sit as compact pills under every chart; active = green fill
10. Motion is calm — color cross-fade on price ticks, 280ms chart morph, no rolling digits, no bounce
