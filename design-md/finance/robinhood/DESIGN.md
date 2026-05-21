# Design System Inspiration of Robinhood (iOS)

## 1. Visual Theme & Atmosphere

Robinhood is a brokerage rendered as a clean tech consumer product. The canvas is white, the chart is the hero, and the entire interface revolves around a single recurring motif: a giant line graph showing your portfolio value, rendered in Robinhood Green when up and Robinhood Red when down. Where Schwab, Fidelity, and the rest of legacy brokerage feel like 1990s financial software with spreadsheet density, Robinhood reduces the act of "checking your portfolio" to a single screen — a number, a curve, and a buy/sell button. The radical commitment to simplicity is the brand.

The accent is Robinhood Green (`#00C805`) — a vivid, slightly-yellow lime green pulled from the original 2014 brand identity. It carries the upward portfolio movement, the chart line when the day is green, the "Trading" badge on profitable positions, and the buy-button gradient. Its counter is Robinhood Red (`#FF5000`) — actually orange, not pure red, deliberately differentiated from the legacy financial-software red because Robinhood wanted to feel friendlier even on the downside. Together green/orange render every chart, every position card, every notification: portfolio is up (green) or portfolio is down (orange).

Typography is Capsule Sans Text, the proprietary face Robinhood commissioned from type foundry XYZ Type around 2021 as part of their broader brand evolution. It's a contemporary geometric sans with a slight humanist warmth — a tall x-height, open apertures, generous letter spacing on numerics. Before Capsule, Robinhood used a customized version of `Apercu` and `Helvetica`; the move to Capsule was about owning the typographic system. Tabular figures throughout: every price, every percentage, every share count uses tabular numerals so columns of numbers align visually — this matters on a brokerage app where the user's eye is constantly scanning vertical columns of prices.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with `#F7F7F7` for subtle section fills — clean tech-product aesthetic
- Robinhood Green (`#00C805`) for upward movement; Robinhood Red (`#FF5000`) — actually orange — for downward
- Giant portfolio chart on the Home tab — line graph filling 30-40% of the viewport, with a draggable scrubber
- 1D / 1W / 1M / 3M / YTD / 1Y / 5Y / ALL chart-range chips below the chart
- "Buying power" + portfolio value displayed in giant Capsule Sans 40-48pt at the top of every Investing screen
- Bottom tab bar: Investing / Search / Crypto / Cash / Account — 5 tabs, labeled
- Position cards as compact horizontal rows with ticker / shares / market value / day change
- Capsule Sans Text everywhere; tabular numerals on all prices and percentages
- Minimal shadows — almost flat — the chart is the visual hero, UI chrome recedes
- Dark mode is a true black variant — the chart's green line is even more electric on black

## 2. Color Palette & Roles

### Primary
- **Robinhood Green** (`#00C805`): Up days, buy CTAs, profitable positions, success states.
- **Green Dim** (`#21CE99`): Older heritage green still seen on splash and some chart fills.
- **Green Pressed** (`#00A904`): Active/pressed state on Buy buttons.
- **Green Background** (`#E6F9E0`): Tinted background for "Day's gain" pills, success banners.

### Counter
- **Robinhood Red** (`#FF5000`): Down days, sell CTAs (subtle), loss states. NOTE: This is actually a saturated orange, not red — deliberately differentiated.
- **Red Background** (`#FFEDE5`): Tinted background for "Day's loss" pills.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas everywhere on light mode.
- **Surface Gray** (`#F7F7F7`): Section backgrounds, watchlist row press states.
- **Surface Gray 2** (`#EFEFEF`): Input field fill, settings rows.
- **Divider** (`#E6E6E6`): Hairline row dividers in lists.
- **Shadow Black** (`rgba(0,0,0,0.04)`): Very subtle shadows only — Robinhood is essentially flat.

### Text
- **Text Primary** (`#000000`): Primary text — position titles, prices, screen titles.
- **Text Secondary** (`#5C6166`): Secondary text — share counts, dates, ticker symbols when grayed.
- **Text Tertiary** (`#9B9EA3`): Tertiary text — placeholders, disabled labels.
- **Text Muted** (`#C2C5CA`): Skeleton state, very faded labels.

### Semantic
- **Success Green** (`#00C805`): Same as primary — purchase complete, alert success.
- **Error Red** (`#E62232`): TRUE red — used for hard error states (order failed), distinct from the Robinhood Red orange.
- **Warning Yellow** (`#FFB800`): Pending order, market closed indicator.
- **Info Blue** (`#1D6FF2`): Used sparingly for educational links and tooltips.

### Crypto Accents
- **Bitcoin Orange** (`#F7931A`): On Bitcoin position cards and the Crypto tab.
- **Ethereum Purple** (`#627EEA`): On Ethereum position cards.

### Dark Mode
Robinhood introduced dark mode in 2019. The dark canvas is a deep black, and the green line glows electrically.
- **Dark Canvas** (`#000000`): Primary dark background.
- **Dark Surface 1** (`#181B1F`): Cards, sheets, watchlist rows.
- **Dark Surface 2** (`#23272D`): Pressed states, chip fills.
- **Dark Divider** (`#2D3138`): Hairlines.
- **Dark Text Primary** (`#FFFFFF`): Inverted text.
- **Dark Text Secondary** (`#A4A8AD`): Inverted secondary.
- **Robinhood Green (dark)** (`#00C805`): Identical — the green reads electric on black.

## 3. Typography Rules

### Font Family
- **Primary**: `Capsule Sans Text` (proprietary, drawn by XYZ Type for Robinhood ~2021)
- **Display variant**: `Capsule Sans Display` — used for hero portfolio values at 40pt+
- **Weights available**: Regular (400), Medium (500), Semibold (600), Bold (700)
- **Tabular figures**: enabled by default on all price / percentage / share / count text — non-negotiable
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'Helvetica Neue', sans-serif`
- **Google Fonts substitute**: `Inter` (closest match), with `tabular-nums` font-feature-setting

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Portfolio Hero | Capsule Sans Display | 40pt | 700 | 1.0 | -0.5pt | "$24,847.93" at top of Investing tab — TABULAR |
| Day Change Hero | Capsule Sans Text | 16pt | 500 | 1.2 | 0pt | "+$847.93 (+3.52%) Today" beneath the portfolio value |
| Screen Title | Capsule Sans Text | 22pt | 700 | 1.15 | -0.2pt | "Investing", "Crypto", "Cash" — left-aligned at top |
| Section Header | Capsule Sans Text | 18pt | 600 | 1.2 | -0.1pt | "Stocks", "Crypto", "Options", "Lists" |
| Position Title | Capsule Sans Text | 16pt | 600 | 1.3 | 0pt | "Apple Inc." — full company name |
| Ticker Symbol | Capsule Sans Text | 13pt | 500 | 1.3 | 0.3pt | "AAPL" — tracked slightly wider |
| Position Value | Capsule Sans Text | 17pt | 500 | 1.2 | 0pt | "$1,247.92" — TABULAR |
| Position Change | Capsule Sans Text | 13pt | 500 | 1.3 | 0pt | "+$24.13 (+1.97%)" — TABULAR, colored green/orange |
| Chart Axis Label | Capsule Sans Text | 11pt | 500 | 1.0 | 0.2pt | "9:30 AM" timestamps on the chart axis |
| Body | Capsule Sans Text | 15pt | 400 | 1.4 | 0pt | About-the-stock paragraph, news previews |
| Body Small | Capsule Sans Text | 13pt | 400 | 1.4 | 0pt | News article meta, settings descriptions |
| Button Primary | Capsule Sans Text | 16pt | 600 | 1.0 | 0pt | "Trade", "Buy", "Sell" |
| Range Chip | Capsule Sans Text | 13pt | 600 | 1.0 | 0.3pt | "1D / 1W / 1M" chart-range labels |
| Tab Label | Capsule Sans Text | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| All-Caps Section | Capsule Sans Text | 11pt | 700 | 1.2 | 0.6pt | "BUYING POWER", "TODAY'S RETURN" — small caps headers |

### Principles
- **Tabular numerals everywhere**: Every price, percentage, share count, and money figure uses tabular-nums. This is the single most important typography rule on the app — columns of numbers must align visually as the user scrolls a watchlist.
- **Capsule Sans Display for hero portfolio values**: The 40pt top-of-screen number uses the Display variant (slightly tighter, more confident); body text uses Capsule Sans Text.
- **Tracked-wide ticker symbols**: AAPL, GOOGL, BTC render at 13pt with +0.3pt letter spacing — Robinhood treats ticker symbols as branded micro-monograms.
- **Pure black on white for primary**: Robinhood uses `#000000` for primary text, not a softened gray — this is a clean, modern tech-product choice, deliberate counterpoint to legacy brokerages.
- **Green/orange semantic always**: Numeric color is bound to direction (up = green, down = orange), never decorative — never use green on a down number.

## 4. Component Stylings

### Buttons

**Primary Trade CTA ("Trade", "Buy AAPL")**
- Background: `#000000` (NOT green — Robinhood uses black for the universal Trade button; green is reserved for upward movement)
- Text: `#FFFFFF`, Capsule Sans 16pt weight 600
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 8pt
- Height: 48pt minimum
- Pressed: background `#1A1A1A`, scale 0.98
- Haptic: `.impactOccurred(.medium)` on Buy / Sell confirm

**Buy Button (within Trade modal)**
- Background: `#00C805` (Robinhood Green)
- Text: `#FFFFFF`, Capsule Sans 16pt weight 600
- Same dimensions as primary
- Pressed: `#00A904`

**Sell Button (within Trade modal)**
- Background: `#FF5000` (Robinhood Red — orange)
- Text: `#FFFFFF`, Capsule Sans 16pt weight 600
- Same dimensions

**Chart Range Chip (1D / 1W / 1M)**
- Container: 36pt × 28pt
- Background: transparent
- Text: `#5C6166` Capsule Sans 13pt weight 600 letter-spaced +0.3pt
- Active state: text color flips to the chart line color (`#00C805` if up, `#FF5000` if down); a 2pt underline appears beneath in the same color
- Tap target: 44pt minimum width

**Secondary Outline Button ("Set Alert", "Add to List")**
- Background: transparent
- Border: 1pt `#E6E6E6`
- Text: `#000000`, Capsule Sans 15pt weight 500
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 8pt

### Cards & Containers

**Portfolio Hero Block (top of Investing)**
- Container: full-width, no card chrome, just space
- Layout: "BUYING POWER" all-caps label in Capsule Sans 11pt 700 +0.6pt letter-spacing `#5C6166` → portfolio value "$24,847.93" in Capsule Sans Display 40pt 700 `#000000` tabular → day change "+$847.93 (+3.52%) Today" in Capsule Sans 16pt 500 `#00C805` (green) or `#FF5000` (orange)
- 24pt horizontal margin
- Vertical spacing: 8pt between label and value, 4pt between value and day change

**Portfolio Chart**
- Width: full-bleed (extends to screen edges)
- Height: 200-240pt
- Line: 2pt stroke, `#00C805` (green) if today's change is positive, `#FF5000` (orange) if negative
- No fill below the line (early Robinhood used a gradient fill; modern Robinhood is just the line)
- X-axis: 11pt timestamps at 9:30 AM, 12:00 PM, 4:00 PM (market hours) when 1D is selected
- Y-axis: no labels (the values are inferred from the hero number)
- Scrubber: when user drags across the chart, a vertical line appears, the hero portfolio value updates to that timestamp's value, and a small price bubble follows the touch point
- Chart range chips below the chart, horizontally arranged with the active chip underlined

**Position Row (Stocks list)**
- Height: 56pt minimum
- Leading: 32pt × 32pt rounded square (8pt radius) ticker icon — either a colored badge with the ticker symbol or, more commonly, a fetched company logo
- Middle: company name in Capsule Sans 16pt 600 `#000000` above ticker symbol + share count in Capsule Sans 13pt 500 `#5C6166` ("AAPL · 4 shares")
- Trailing: market value in Capsule Sans 17pt 500 `#000000` (TABULAR) above day change in Capsule Sans 13pt 500 in green or orange (TABULAR), right-aligned
- Divider: 0.5pt `#E6E6E6` bottom

**Watchlist Card**
- Same as position row but with NO share count — just ticker, company name, current price, day change
- Often grouped under a "Lists" section with collapsible headers

**News Card**
- Container: rounded rectangle, 12pt radius, 1pt `#E6E6E6` border
- Layout: square thumbnail 80pt left-aligned + headline in Capsule Sans 15pt 600 + source + time in Capsule Sans 13pt 400 `#5C6166`
- Tap: opens article in in-app webview

### Navigation

**Large Nav Bar**
- Robinhood's nav bar is collapsing: screen title sits at 22pt 700 left-aligned at the top of every screen content area
- 16pt horizontal margin, 8pt top safe-area inset
- Trailing icon: usually a search magnifying glass (24pt) and an account/profile circle (32pt)

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` (light) / `#000000` (dark) with 0.5pt `#E6E6E6` top divider
- 5 tabs: Investing (chart icon), Search (magnifying glass), Crypto (currency-bitcoin icon), Cash (dollar/wallet), Account (person circle)
- Icon size: 24pt outline / fill on selected
- Active state: icon + label both flip to `#000000`
- Inactive: `#9B9EA3`
- Labels: Capsule Sans 10pt weight 600 letter-spaced +0.2pt — visible always
- Tap haptic: `.selectionChanged()`

**Sticky Trade Footer (Stock Detail bottom)**
- Height: 64pt + safe area
- Background: `#FFFFFF` (light) / `#000000` (dark)
- 1 single "Trade" button — 100% width minus 32pt horizontal margins, 48pt height, black background, white text, 8pt corner radius
- Tapping opens the Buy/Sell modal sheet

### Input Fields

**Search Bar**
- Background: `#F7F7F7` (Surface Gray)
- Height: 44pt
- Corner radius: 12pt
- Leading icon: `magnifyingglass` 16pt `#5C6166`
- Placeholder: "Search by ticker, name, or category" in Capsule Sans 15pt 400 `#9B9EA3`
- Text on focus: `#000000`

**Order Entry Field (Buy/Sell modal)**
- Numeric keypad input — the dollar amount or share count
- Display: enormous Capsule Sans Display 48-64pt at the top of the modal
- Auto-shrinks as digits add (similar pattern to Cash App but smaller scale)

### Distinctive Components

**The Portfolio Chart Scrubber**
- The single most recognizable interaction in Robinhood: drag your finger across the chart
- A thin vertical line follows the finger
- The hero portfolio value at the top updates in real-time to that timestamp's value
- A small `#000000` solid bubble appears at the data point with the exact $ value in Capsule Sans Mono Display 13pt 600 white
- The day-change pill below the hero also updates in real-time
- Release: the line and bubble animate away (200ms fade), values snap back to current

**Day-Change Pill**
- Inline below the portfolio value: "+$847.93 (+3.52%) Today" in Capsule Sans 16pt 500
- Color: `#00C805` (green) if positive, `#FF5000` (orange) if negative
- NO background or pill chrome — just colored text
- Tabular numerals so amounts align vertically across days when shown in lists

**Order Confirmation Modal**
- Full-screen modal with the order details in a vertical list (Stock / Action / Shares / Price / Estimated Cost)
- Bottom button: "Review Order" → "Place Buy Order" → swipe-to-confirm gesture for the final commit
- Success animation: green confetti burst + Capsule Sans Display 32pt "Order Placed" in `#00C805`

**Buying Power Indicator**
- Always visible at the top of any Trade modal: "Buying Power: $4,287.12" in Capsule Sans 13pt 500 `#5C6166` right-aligned

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Standard horizontal margin: 16pt — slightly tighter than airy Airbnb because the data density is higher
- Section vertical gap: 32pt between major sections; 16pt between rows

### Grid & Container
- Content width: full device width minus 16pt horizontal margins on lists
- Position rows: 100% width, stacked vertically
- Charts: full-bleed (extend to edges, no margin)
- Watchlist sections: collapsible — header row with chevron, then 4-6 rows visible

### Whitespace Philosophy
- **Data density without crowding**: 56pt position rows are tight but readable; section gaps are generous (32pt)
- **The chart breathes**: 24pt vertical buffer above and below the chart so the line graph never feels cramped
- **Numbers have room**: tabular numerals plus generous right-padding on position rows so prices align visually

### Border Radius Scale
- Square (0pt): rare
- Small (4pt): badges, alert pills
- Standard (8pt): primary buttons, modal containers, news cards
- Comfortable (12pt): search bar, watchlist row press state
- Medium (16pt): full sheet top corners
- Large (24pt): modal full top corners
- Circle (50%): avatars, the Account tab profile icon

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Everything by default — Robinhood is essentially flat |
| Subtle (Level 1) | `rgba(0,0,0,0.04) 0 2px 6px` | Card edges (news cards, position cards) when explicitly raised |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -6px 24px` | Modal sheets sliding up from bottom |
| Overlay Dim | `rgba(0,0,0,0.5)` | Behind modal sheets |

**Shadow Philosophy**: Robinhood is a clean tech-product look — shadows are minimal, low-opacity (0.04-0.12), and short. The chart's line is the only visual lift in the app; UI chrome stays flat. This contrasts with banking apps that use heavy elevation; Robinhood reads modern by avoiding it.

### Motion
- **Chart scrubber drag**: vertical line follows the finger at 60fps with no smoothing — instant response. Portfolio value at the top updates via a `.contentTransition(.numericText())` morph in <100ms.
- **Buy/Sell confirm**: scale 1.0 → 0.97 → 1.0 spring (response 0.2, damping 0.7), `.impactOccurred(.medium)` haptic on tap, `.notificationOccurred(.success)` on order placed.
- **Tab switch**: instant icon + label color change, `.selectionChanged()` haptic.
- **Order Placed confetti**: 60fps green particle burst from the center, 800ms duration, paired with the Order Placed text fade-in.
- **Pull-to-refresh on Investing tab**: portfolio chart re-renders the line, hero value pulses briefly.
- **Position row tap**: row background fades from white to `#F7F7F7` over 150ms before pushing the stock detail screen.

## 7. Do's and Don'ts

### Do
- Use `#00C805` (Robinhood Green) for all upward portfolio movement, buy CTAs, profitable positions
- Use `#FF5000` (Robinhood Red — orange) for downward movement and loss states — note this is orange, NOT red
- Use a true red `#E62232` only for hard error states (order failed, system error) — keep it distinct from the loss orange
- Render every $-amount and every percentage in Capsule Sans Text with tabular numerals
- Use Capsule Sans Display (40pt+ Bold) for the hero portfolio value at the top of the Investing tab
- Track ticker symbols (AAPL, GOOGL) at +0.3pt letter spacing — they're branded micro-monograms
- Make the chart line 2pt stroke in the day's color (green or orange) with no fill below
- Keep the Trade button black with white text — the universal CTA is achromatic; green/orange are reserved for Buy/Sell within the trade flow
- Use pure black `#000000` for primary text — Robinhood's clean tech-product aesthetic depends on this
- Keep shadows minimal (0.04 opacity) — the app is essentially flat; the chart is the only visual lift

### Don't
- Don't use green for a down number or orange for an up number — color is bound to direction
- Don't use legacy red `#FF0000` for losses — Robinhood Red is specifically orange (`#FF5000`)
- Don't add gradients to the chart line area — modern Robinhood is just the line, no fill
- Don't render numbers in proportional figures — always tabular numerals
- Don't crowd position rows below 56pt — the data density is already at its limit
- Don't use chart line color for non-chart UI — keep green/orange semantic to portfolio movement
- Don't soften primary text to gray — `#000000` is the brand-correct black
- Don't use heavy shadows or elevation — Robinhood is flat
- Don't add a fill or border to the day-change pill — it's just colored text inline
- Don't dim inactive tab icons too aggressively — `#9B9EA3` is the correct inactive gray (still readable)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Portfolio hero caps at 36pt; chart height reduces to 180pt |
| iPhone 13/14/15 | 390pt | Standard 40pt hero; 220pt chart |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; chart sits below it |
| iPhone 15/16 Pro Max | 430pt | Portfolio hero at 48pt; 240pt chart |
| iPad | 768pt+ | 2-column layout: chart + portfolio on left, watchlist on right |

### Dynamic Type
- Body, secondary text, list rows scale with Dynamic Type
- The portfolio hero (40pt) scales but clamps at 56pt to preserve layout
- Chart axis labels (11pt) are fixed — they're layout-sensitive
- Tab labels (10pt) are fixed
- Position values honor scaling but clamp at 22pt

### Orientation
- Portrait only on phones — Robinhood is portrait-locked except on iPad where landscape exposes the chart full-width with the watchlist as a side panel

### Touch Targets
- Position row: full 56pt hit area
- Chart scrubber: full chart height as the hit zone — touch anywhere to scrub
- Chart range chips: 44pt minimum hit width even though visual is 36pt
- Trade CTA: 48pt height, full-width minus 32pt
- Tab bar: 56pt full row with 24pt icons centered

### Safe Area Handling
- Top: large screen title sits 16pt below safe-area top
- Bottom: tab bar pins above home indicator; sticky Trade footer respects it
- Sides: 16pt content insets; chart goes edge-to-edge

## 9. Quick Reference Cheat Sheet

### Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F7F7F7`
- Divider: `#E6E6E6`
- Text Primary: `#000000`
- Text Secondary: `#5C6166`
- Robinhood Green (up): `#00C805`
- Robinhood Red (down, orange): `#FF5000`
- Green Pressed: `#00A904`
- Bitcoin Orange: `#F7931A`
- Ethereum Purple: `#627EEA`
- Error Red (true red): `#E62232`
- Dark Canvas: `#000000`
- Dark Surface 1: `#181B1F`
- Dark Text Primary: `#FFFFFF`

### Example Component Prompts
- "Build the Robinhood Investing tab hero block: 'BUYING POWER' label in Capsule Sans 11pt weight 700 letter-spaced +0.6pt in `#5C6166`, then directly below the portfolio value '$24,847.93' in Capsule Sans Display 40pt weight 700 `#000000` with tabular numerals. Below that, the day change '+$847.93 (+3.52%) Today' in Capsule Sans 16pt weight 500 in `#00C805` (green) if positive or `#FF5000` (orange) if negative. 24pt horizontal margin."
- "Create the Robinhood portfolio chart: full-bleed width, 220pt tall, with a 2pt stroke line in `#00C805` (positive day) or `#FF5000` (negative day) — no fill below the line. Add a scrubber: dragging your finger across the chart shows a vertical line at the touch point and a `#000000` solid bubble with the exact $ value at that timestamp in Capsule Sans 13pt weight 600 white tabular. Below the chart: 1D / 1W / 1M / 3M / YTD / 1Y / 5Y / ALL range chips — text-only, 13pt weight 600 letter-spaced +0.3pt, `#5C6166` default, active state flips to the chart line color with a 2pt underline."
- "Style a Robinhood position row: 56pt tall white row. Leading 32pt rounded square 8pt-radius company logo or ticker badge. Middle: company name 'Apple Inc.' in Capsule Sans 16pt weight 600 `#000000` above 'AAPL · 4 shares' in Capsule Sans 13pt weight 500 `#5C6166` (ticker tracked +0.3pt). Trailing right-aligned: market value '$1,247.92' in Capsule Sans 17pt weight 500 `#000000` tabular, day change '+$24.13 (+1.97%)' in Capsule Sans 13pt weight 500 tabular in `#00C805` (green) or `#FF5000` (orange) below. 0.5pt `#E6E6E6` bottom divider."
- "Render the Robinhood Trade button: full-width minus 32pt margins, 48pt height, `#000000` background (yes black, not green — green is for Buy within the trade modal), white text 'Trade' in Capsule Sans 16pt weight 600, 8pt corner radius. Tap opens the Buy/Sell modal."
- "Build the Robinhood bottom tab bar: 56pt + safe area, white background with 0.5pt `#E6E6E6` top divider. 5 tabs: Investing (chart line icon), Search (magnifying glass), Crypto (currency icon), Cash (wallet), Account (person circle). Each tab is 24pt icon + 10pt Capsule Sans weight 600 letter-spaced +0.2pt label, `#9B9EA3` inactive, `#000000` active. Tap haptic `.selectionChanged()`."

### Iteration Guide
1. Canvas is `#FFFFFF` (light) or `#000000` (dark) — true white, true black, no warm grays
2. Robinhood Green `#00C805` for up; Robinhood Red `#FF5000` (which is orange) for down — color binds to direction
3. Capsule Sans Text everywhere; tabular numerals on every price and percentage — non-negotiable
4. Portfolio hero is Capsule Sans Display 40pt 700 tabular at the top of every screen
5. The portfolio chart is 2pt stroke in the day's direction color, no fill below, full-bleed
6. The chart scrubber drag updates the hero number in real-time — this is THE Robinhood interaction
7. Trade button is BLACK, not green — green is reserved for Buy within the trade flow
8. Track ticker symbols at +0.3pt letter spacing
9. Use minimal shadows (0.04 opacity max) — Robinhood is essentially flat; the chart is the only visual lift
10. Day-change pill is just colored text inline — no background, no pill chrome
