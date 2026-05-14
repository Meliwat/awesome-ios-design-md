# Design System Inspiration of Coinbase (iOS)

## 1. Visual Theme & Atmosphere

Coinbase is the crypto exchange rendered as a serious institutional financial product. The canvas is white, the brand is a single saturated blue — Coinbase Blue (`#0052FF`) — and the entire interface is engineered to make holding cryptocurrencies feel as legitimate as holding a brokerage account. Where Cash App leans pop-cultural and Robinhood leans clean-tech, Coinbase leans grown-up institution: thoughtful spacing, deliberate typography, news-grade list density, and a chart aesthetic borrowed from Bloomberg terminals more than from consumer fintech. The app's job is to convince you that this is a real exchange, not a Discord meme economy.

The primary accent is **Coinbase Blue** (`#0052FF`) — a vivid, slightly-purple electric blue that's been the company's identity since the 2017 rebrand. It carries the primary CTAs ("Buy", "Sell", "Send"), the active tab, the portfolio chart line when up (paired with a green for up days), and the company wordmark. The "C" Coin logomark (a circle with a horizontal line through it) is the brand's geometric anchor — used at every entry point and as a loading indicator. Crypto-specific brand colors fill the asset list: **Bitcoin Orange** (`#F7931A`), **Ethereum Purple** (`#627EEA`), **USD Coin Blue** (`#2775CA`), each rendered as the circular asset icon in the portfolio list.

Typography is **Coinbase Sans** and **Coinbase Display** — proprietary faces designed by Tobias Frere-Jones (Frere-Jones Type) for Coinbase as part of the 2021 brand evolution. Coinbase Sans is a contemporary geometric sans with subtle humanist tension at body sizes; Coinbase Display is a tighter, sharper variant used for hero numbers (the portfolio value at the top of the app, the giant amount on the Buy/Sell entry screen). Coinbase Mono — also from Frere-Jones — appears on transaction hashes, wallet addresses, and order book numbers when those technical readouts are exposed. Tabular numerals run throughout: every portfolio value, every price, every percentage uses tabular figures so the asset list reads as a clean institutional column.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with `#F7F8FA` for subtle surface fills — institutional clean
- Coinbase Blue (`#0052FF`) as the single brand accent for CTAs, active tab, and chart line when default
- Portfolio chart with a 2pt line, no fill below — green when up, red when down — sparkline-style mini-versions inside asset rows
- Asset list with the crypto-specific brand color in the circular icon (Bitcoin orange, ETH purple, USDC blue) + price + 24h percent change
- Buy / Sell / Send / Receive — 4 primary actions at the top of every asset detail screen
- Bottom tab bar: Home / Trade / Cards / Earn / Wallet — 5 tabs in newer versions; older = Home / Trade / Lend / Wallet / Profile
- Coinbase Sans for body, Coinbase Display for hero amounts, Coinbase Mono for technical readouts
- Subtle shadows (`rgba(0,0,0,0.04)`) — institutional minimalism, not flat-zero
- News-grade density: 56-64pt asset rows with rich sparklines on the right
- The Coinbase "C" mark — a circle with a horizontal line — used as a loading indicator with a pulse animation

## 2. Color Palette & Roles

### Primary
- **Coinbase Blue** (`#0052FF`): Primary CTA fill, active tab, link color, wordmark — the iconic Coinbase electric blue.
- **Coinbase Blue Pressed** (`#0040CC`): Active/pressed state.
- **Coinbase Blue Tint** (`#E5EDFF`): Tinted background for "Buy" hover states, info banners.

### Brand Identifiers
- **Coinbase Black** (`#0A0B0D`): Almost-black with a slight cool tint — primary text, dark mode canvas. Distinct from pure `#000`.
- **Coinbase Charcoal** (`#1A1C1F`): Darker than text but lighter than canvas in dark mode.

### Crypto Asset Colors
- **Bitcoin Orange** (`#F7931A`): The Bitcoin "₿" icon background.
- **Ethereum Purple** (`#627EEA`): The Ethereum diamond icon background.
- **USDC Blue** (`#2775CA`): USD Coin icon (different from Coinbase Blue — deeper, more navy).
- **Solana Purple** (`#9945FF`): Solana icon.
- **Cardano Blue** (`#0033AD`): Cardano icon.
- **Tether Green** (`#26A17B`): Tether (USDT) icon.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas everywhere on light mode.
- **Surface Gray** (`#F7F8FA`): Section backgrounds, list row press states.
- **Surface Gray 2** (`#EEF0F3`): Input field fill, chip backgrounds.
- **Divider** (`#E1E4E8`): Hairline row dividers.

### Text
- **Text Primary** (`#0A0B0D`): Coinbase Black — primary text, portfolio values, asset titles.
- **Text Secondary** (`#5B616E`): Secondary text — ticker symbols, dates, metadata.
- **Text Tertiary** (`#80868F`): Tertiary — placeholders, disabled labels.
- **Text Muted** (`#A0A4AA`): Skeleton state, very faded labels.

### Semantic
- **Success Green** (`#05B169`): Up portfolio movement, gain indicators, success banners.
- **Success Green Tint** (`#E6F7EF`): Tinted background for gain pills.
- **Loss Red** (`#CF202F`): Down portfolio movement, loss indicators.
- **Loss Red Tint** (`#FCE7E9`): Tinted background for loss pills.
- **Warning Amber** (`#F5A623`): Pending transactions, network congestion alerts.
- **Info Blue** (`#1652F0`): Educational tooltips, occasional info callouts (slightly different from primary).

### Dark Mode
Coinbase introduced dark mode in 2020. The dark canvas is a deep near-black with a subtle blue undertone.
- **Dark Canvas** (`#0A0B0D`): Primary dark background.
- **Dark Surface 1** (`#13151A`): Cards, sheets.
- **Dark Surface 2** (`#1E2026`): Pressed states, chip backgrounds.
- **Dark Divider** (`#2A2E36`): Hairlines.
- **Dark Text Primary** (`#FFFFFF`): Inverted.
- **Dark Text Secondary** (`#A0A4AA`): Inverted secondary.
- **Coinbase Blue (dark)** (`#3B6CFF`): Brightened slightly for AA contrast on the dark canvas.

## 3. Typography Rules

### Font Family
- **Primary**: `Coinbase Sans` (proprietary, drawn by Frere-Jones Type for Coinbase ~2021)
- **Display variant**: `Coinbase Display` — used for hero portfolio values and large amounts
- **Mono variant**: `Coinbase Mono` — used for wallet addresses, transaction hashes, order book numbers
- **Weights available**: Regular (400), Medium (500), Bold (700)
- **Tabular figures**: enabled on all financial figures, percentages, percent-change deltas — non-negotiable
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'Helvetica Neue', sans-serif`
- **Mono fallback**: `'SF Mono', 'Roboto Mono', 'Menlo', monospace`
- **Google Fonts substitute**: `Inter` for Sans, `JetBrains Mono` for Mono

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Portfolio Hero | Coinbase Display | 40pt | 700 | 1.0 | -0.5pt | Portfolio value at top of Home — TABULAR |
| Buy/Sell Amount | Coinbase Display | 56pt | 700 | 1.0 | -1pt | Giant $-amount on Buy/Sell entry — TABULAR, auto-shrinks |
| Screen Title | Coinbase Display | 28pt | 700 | 1.1 | -0.3pt | "Home", "Trade", "Wallet" |
| Section Header | Coinbase Sans | 20pt | 700 | 1.15 | -0.2pt | "Your assets", "Watchlist", "Markets" |
| Asset Title | Coinbase Sans | 16pt | 600 | 1.3 | 0pt | "Bitcoin", "Ethereum" — full name |
| Ticker Symbol | Coinbase Sans | 13pt | 500 | 1.3 | 0.3pt | "BTC", "ETH" — tracked wider |
| Asset Price | Coinbase Sans | 16pt | 500 | 1.2 | 0pt | "$67,234.18" — TABULAR |
| Asset 24h Change | Coinbase Sans | 13pt | 500 | 1.3 | 0pt | "+$1,234 (+1.87%)" — TABULAR, green/red |
| Body | Coinbase Sans | 15pt | 400 | 1.5 | 0pt | About-the-asset paragraph, market commentary |
| Body Small | Coinbase Sans | 13pt | 400 | 1.4 | 0pt | News meta, transaction history |
| Wallet Address | Coinbase Mono | 13pt | 500 | 1.4 | 0pt | "0x742d35Cc6634C0532925a3b844Bc..." |
| Tx Hash | Coinbase Mono | 12pt | 400 | 1.4 | 0pt | Transaction hashes |
| Button Primary | Coinbase Sans | 16pt | 600 | 1.0 | 0pt | "Buy Bitcoin", "Sell", "Send" |
| Tab Label | Coinbase Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| All-Caps Section | Coinbase Sans | 11pt | 700 | 1.2 | 0.6pt | "PORTFOLIO VALUE", "MARKETS" — small caps |
| Range Chip | Coinbase Sans | 13pt | 600 | 1.0 | 0.3pt | "1H / 1D / 1W / 1M / 1Y / ALL" range chips |

### Principles
- **Coinbase Display for hero, Coinbase Sans for body**: The 40pt+ portfolio value uses Display; all body, list rows, secondary text use Sans. Display is slightly tighter and more confident — it's there to make the big number feel institutional.
- **Tabular numerals everywhere financial**: every $-amount, every percentage, every quantity uses `fontVariant: ['tabular-nums']`. The asset list's vertical column of prices must align cleanly — this is the institutional clean look.
- **Coinbase Mono for technical readouts**: wallet addresses, transaction hashes, order book prices when shown. Never use Sans for an address; never use Mono for a regular price.
- **Tracked-wide ticker symbols**: BTC, ETH, USDC at 13pt with +0.3pt letter spacing — treated as branded micro-monograms, same convention as Robinhood.
- **Text in Coinbase Black, not pure black**: `#0A0B0D` carries a cool tint that aligns with the blue brand identity. Pure `#000` would feel cold and disconnected.

## 4. Component Stylings

### Buttons

**Primary CTA ("Buy", "Send", "Continue")**
- Background: `#0052FF` (Coinbase Blue)
- Text: `#FFFFFF`, Coinbase Sans 16pt weight 600
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 12pt
- Height: 48pt minimum
- Width: typically full-width minus 16pt margins
- Pressed: background `#0040CC`, scale 0.98
- Haptic: `.impactOccurred(.medium)` on Buy confirmation

**Secondary Button ("Sell", "Cancel")**
- Background: `#EEF0F3` (Surface Gray 2)
- Text: `#0A0B0D`, Coinbase Sans 16pt weight 600
- Same dimensions
- Pressed: background `#E1E4E8`

**4-Up Action Row (Buy / Sell / Send / Receive)**
- Container: 4 equal-width buttons in a single row at the top of an asset detail screen
- Each button: stacked icon (28pt) + label (Coinbase Sans 13pt 500)
- Background: `#F7F8FA` (Surface Gray), 12pt corner radius
- Icons: Buy = up-arrow, Sell = down-arrow, Send = paper-plane, Receive = QR-code or down-arrow-into-tray
- Icon color: `#0052FF` (Coinbase Blue)
- Label: `#0A0B0D`
- 8pt gap between buttons
- Padding: 12pt vertical inside each

**Chart Range Chip (1H / 1D / 1W / 1M / 1Y / ALL)**
- Container: 32pt × 28pt
- Background: transparent default, `#EEF0F3` selected
- Text: `#5B616E` Coinbase Sans 13pt weight 600 letter-spaced +0.3pt; selected flips to `#0A0B0D`
- Corner radius: 8pt

### Cards & Containers

**Portfolio Hero Block (top of Home)**
- Container: full-width, no card chrome
- Layout: "PORTFOLIO BALANCE" all-caps label in Coinbase Sans 11pt 700 +0.6pt letter-spaced `#5B616E` → portfolio value "$12,847.93" in Coinbase Display 40pt 700 `#0A0B0D` tabular → 24h change "+$847.93 (+6.59%)" in Coinbase Sans 16pt 500 `#05B169` (green) or `#CF202F` (red)
- 16pt horizontal margin
- Vertical spacing: 8pt label-to-value, 4pt value-to-change

**Portfolio Chart (Home)**
- Width: full-bleed
- Height: 180pt (slightly shorter than Robinhood — Coinbase prioritizes the asset list below)
- Line: 2pt stroke, default `#0052FF` (Coinbase Blue), but switches to `#05B169` (green) when 24h is positive or `#CF202F` (red) when negative if the user has the day-color mode enabled
- No fill below the line
- Scrubber: identical pattern to Robinhood — vertical line + price bubble follows finger
- Range chips below: 1H / 1D / 1W / 1M / 1Y / ALL

**Asset Row (Your assets / Markets list)**
- Height: 64pt
- Leading: 40pt circular icon in the asset's brand color (Bitcoin orange, ETH purple, USDC blue, etc.) with the asset symbol or glyph in white centered
- Middle: asset name in Coinbase Sans 16pt 600 `#0A0B0D` above ticker + holdings ("BTC · 0.1842 BTC") in Coinbase Sans 13pt 500 `#5B616E` (ticker tracked +0.3pt)
- Trailing right-aligned: market value in Coinbase Sans 16pt 500 `#0A0B0D` tabular + 24h change in Coinbase Sans 13pt 500 tabular in green or red
- Mini sparkline (optional): 56pt × 20pt SVG line graph between the middle text block and the price, in the 24h-direction color
- Divider: 0.5pt `#E1E4E8` bottom

**Watchlist Row**
- Same as asset row but no holdings line — just ticker name + current price + 24h change + mini sparkline
- Heart icon trailing: tap to remove from watchlist

**Trade Confirmation Card**
- Container: large rounded rectangle, 16pt radius, 1pt `#E1E4E8` border, white background
- Padding: 24pt
- Layout: asset icon (48pt) + "Buy Bitcoin" Coinbase Display 22pt 700 → list of details (Amount, Spot price, Fee, Total) in Coinbase Sans 15pt — left label `#5B616E`, right value `#0A0B0D` tabular
- Bottom CTA: "Confirm purchase" full-width

### Navigation

**Top Nav Bar (Home tab)**
- Height: 56pt + safe area
- Background: `#FFFFFF`
- Leading: Coinbase "C" mark (28pt) in Coinbase Blue (the circle-with-line logomark)
- Trailing: notification bell (24pt) + a search magnifying glass (24pt)
- Title: when present (on inner screens), centered, Coinbase Sans 17pt 600 `#0A0B0D`
- Border bottom: 0.5pt `#E1E4E8`

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt `#E1E4E8` top divider
- 5 tabs: Home / Trade / Cards / Earn / Wallet (newer); older versions: Home / Trade / Lend / Wallet / Profile
- Icon size: 24pt outlined / filled when active
- Active state: icon + label `#0052FF`
- Inactive: `#5B616E`
- Labels: Coinbase Sans 10pt 600 +0.1pt letter spacing
- Tap haptic: `.selectionChanged()`

**Sticky Buy/Sell Footer (Asset Detail bottom)**
- Height: 64pt + safe area
- Background: `#FFFFFF` with 0.5pt `#E1E4E8` top divider
- 2 buttons side by side: "Buy" (Coinbase Blue fill) + "Sell" (Surface Gray fill, black text)
- Each 48pt height, 12pt corner radius, half-width with 8pt gap

### Input Fields

**Search Bar**
- Background: `#EEF0F3` (Surface Gray 2)
- Height: 44pt
- Corner radius: 22pt (full pill)
- Leading icon: `magnifyingglass` 16pt `#5B616E`
- Placeholder: "Search assets" in Coinbase Sans 15pt 400 `#80868F`
- Text: Coinbase Sans 15pt 400 `#0A0B0D` on focus

**Buy/Sell Amount Field**
- Fullscreen modal — the $-amount centered: Coinbase Display 56pt 700 `#0A0B0D` tabular
- Currency selector: tap to switch between USD entry and crypto entry (e.g., enter "$100" or enter "0.001 BTC")
- Standard numeric keypad below
- Auto-shrinks at 5+ digits

**Wallet Address Display**
- Coinbase Mono 13pt 500, monospaced
- Background: `#F7F8FA`, 8pt corner radius, 12pt padding
- Tap to copy with a brief toast "Copied" animation
- Truncated middle: "0x742d35Cc...8b9b9d04"

### Distinctive Components

**The "C" Coin Mark (Logomark)**
- A perfect circle with a horizontal line through the middle — the geometric Coinbase brand mark
- Rendered in Coinbase Blue (`#0052FF`) at 28pt in the top-left of the Home tab
- Used as a loading indicator: a subtle 1500ms rotation or pulse animation
- Always perfectly geometric — no stylized variants

**Asset Icon Circles**
- Every cryptocurrency in the list gets a 40pt circular icon in its official brand color
- The asset's glyph (Bitcoin "₿", Ethereum diamond, USDC "$") sits centered in white
- These are mini brand patches — Coinbase maintains a library of asset icons matching each token's official identity
- The visual variety of orange (BTC) + purple (ETH) + blue (USDC) + green (USDT) creates the colorful asset-list signature

**Mini Sparkline (in asset rows)**
- 56pt × 20pt inline SVG line graph showing the asset's 24h price trend
- 1.5pt stroke, color matches the 24h direction (green or red)
- No axis, no labels, no fill — purely a directional micro-cue
- Sits between the ticker text and the price, right-aligned

**Buy / Sell / Send / Receive Quad**
- The 4-up action row at the top of every asset detail screen is unique to Coinbase
- Each action is equal weight, equal size — Coinbase wants the user to see "you can do all of these things, this is a real exchange"
- 12pt corner radius cards, 4-column equal grid, icons in Coinbase Blue

**Coinbase Earn Badge**
- A green pill on the right side of certain assets indicating earn opportunities ("Earn 5% APY")
- Background: `#E6F7EF`, text: `#05B169`, Coinbase Sans 12pt 600, 16pt corner radius

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Standard horizontal margin: 16pt — slightly tighter than airy Airbnb, denser than Cash App
- Section vertical gap: 32pt between major sections; 16pt between rows
- Card internal padding: 24pt

### Grid & Container
- Content width: full device width minus 16pt horizontal margins
- Asset rows: 100% width within margins
- Portfolio chart: full-bleed
- 4-up action row: 4 equal columns with 8pt gaps

### Whitespace Philosophy
- **Institutional density**: 64pt asset rows are tighter than retail apps because users expect to scan many holdings — but not as dense as a stock terminal
- **Hero has room**: the portfolio chart + hero block reserves the top 40% of the Home tab
- **Lists breathe enough**: 16pt section gaps and 0.5pt dividers (not hard rules) keep the asset list readable without crowding

### Border Radius Scale
- Square (0pt): rare
- Small (4pt): badges, status pills
- Standard (8pt): wallet address display, info banners
- Comfortable (12pt): primary CTAs, action row cards, modal sheets
- Soft (16pt): asset detail cards, trade confirmation card, modal top
- Pill (500pt): search bar, filter chips, earn badge
- Circle (50%): avatars, asset icons, the Coinbase C mark

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, section backgrounds, canvas |
| Whisper (Level 1) | `rgba(0,0,0,0.04) 0 2px 6px` | Asset cards on Markets, trade confirmation card |
| Card (Level 2) | `rgba(0,0,0,0.08) 0 4px 12px` | Floating action buttons, contextual menus |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 24px` | Bottom sheets, confirm modals |
| Overlay Dim | `rgba(0,0,0,0.50)` | Behind modal sheets |

**Shadow Philosophy**: Coinbase uses very subtle elevation — the brand reads as institutional clean, not glossy. Shadows are 0.04-0.08 opacity and short (2-12pt blur). Compared to PayPal (gentle card-wallet feel), Coinbase is even more restrained. The portfolio chart and asset icons do the visual lifting, not drop shadows.

### Motion
- **Buy/Sell tap**: scale 1.0 → 0.98 → 1.0 spring (response 0.25, damping 0.7), `.impactOccurred(.medium)` haptic
- **Order confirmation**: success modal slides up 350ms; green checkmark icon (`#05B169`) scales 0.5 → 1.0 with a delayed 100ms; `.notificationOccurred(.success)` haptic
- **Chart scrubber drag**: identical to Robinhood — vertical line follows finger, hero portfolio value updates in real-time via `.contentTransition(.numericText())`
- **Asset row tap**: row background fades from white to `#F7F8FA` over 150ms before pushing the asset detail screen
- **Tab switch**: icon transitions outline/filled over 200ms, label color animates `#5B616E` → `#0052FF`; `.selectionChanged()` haptic
- **The C-mark loading**: a slow 1500ms rotation (the circle spins; the horizontal line stays fixed inside) or a subtle 1200ms opacity pulse for shorter loads
- **Wallet address copy**: brief 1500ms green toast "Copied" appearing at the top — Coinbase doesn't use heavy confetti

## 7. Do's and Don'ts

### Do
- Use Coinbase Blue `#0052FF` for primary CTAs, active tab, and the C-mark — it's the core brand action color
- Use Coinbase Display for hero portfolio values (40pt+); use Coinbase Sans for everything else
- Use Coinbase Mono ONLY for wallet addresses, transaction hashes, and order book numbers
- Render each crypto asset's icon with its official brand color (Bitcoin orange, ETH purple, USDC blue) in a 40pt circle with the asset's glyph in white centered
- Use tabular numerals on every price, percentage, and quantity — the institutional asset list look depends on it
- Make the asset list rich: ticker + holdings + price + 24h change + mini sparkline — Coinbase prides itself on info density
- Use the 4-up action row (Buy / Sell / Send / Receive) at the top of every asset detail — it's the brand pattern
- Track ticker symbols at +0.3pt letter spacing — same convention as Robinhood
- Use `#0A0B0D` Coinbase Black for primary text — NOT pure black; the slight cool tint aligns with the blue brand
- Use the geometric C-mark logomark in the top-left of the Home tab — always Coinbase Blue, always perfectly circular

### Don't
- Don't use any other blue as the brand accent — Coinbase Blue `#0052FF` is the only correct shade
- Don't use pure black `#000000` for text — `#0A0B0D` (slight cool tint) is correct
- Don't use Coinbase Sans for wallet addresses — Mono is required for technical readouts
- Don't replace the asset's brand color icon with a monochrome version — the orange / purple / blue diversity is part of the visual identity
- Don't crowd asset rows below 60pt — the rich icon + 2-line text + sparkline + price + change needs room
- Don't use green for a falling asset or red for a rising one — color binds to direction
- Don't add heavy shadows — Coinbase is institutional minimal, max 0.08 opacity
- Don't use generic stock icons for the 4-up action row — Buy/Sell/Send/Receive icons are part of the brand pattern
- Don't render the Coinbase C-mark stylized or distorted — it's a perfect circle with a horizontal line, always
- Don't soften primary text to gray — `#0A0B0D` is brand-correct

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Portfolio hero caps at 36pt; chart height reduces to 160pt; sparkline shortens to 48pt |
| iPhone 13/14/15 | 390pt | Standard 40pt hero; 180pt chart; 56pt sparkline |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; nav bar below it |
| iPhone 15/16 Pro Max | 430pt | Hero at 44pt; 200pt chart; 64pt sparkline |
| iPad | 768pt+ | 2-column: chart + portfolio on left, asset list + markets on right; sidebar nav replaces tab bar |

### Dynamic Type
- Body text, asset row subtitles, secondary labels scale fully
- Portfolio hero (40pt) scales but clamps at 56pt
- Buy/Sell entry (56pt) scales but clamps at 72pt
- Asset price (16pt) honors scaling up to 20pt
- Tab labels and range chips are fixed
- Wallet addresses and tx hashes (Mono) are fixed — must stay legible at the original size

### Orientation
- Portrait only on phones
- iPad supports landscape with sidebar nav

### Touch Targets
- Asset row: full 64pt hit area
- Tab bar: 56pt full row with 24pt icons
- Primary CTA: 48pt height, full-width minus 32pt margins
- 4-up action button: full card area, ~88pt each on phone
- Wallet address tap-to-copy: full pill area

### Safe Area Handling
- Top: nav respects safe area + Dynamic Island
- Bottom: tab bar pins above home indicator; sticky Buy/Sell footer respects it
- Modal sheets: 16pt top corners, bottom respects home indicator
- Sides: 16pt content insets

## 9. Quick Reference Cheat Sheet

### Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F7F8FA`
- Surface Gray 2: `#EEF0F3`
- Divider: `#E1E4E8`
- Text Primary (Coinbase Black): `#0A0B0D`
- Text Secondary: `#5B616E`
- Coinbase Blue: `#0052FF`
- Coinbase Blue Pressed: `#0040CC`
- Success Green: `#05B169`
- Loss Red: `#CF202F`
- Warning Amber: `#F5A623`
- Bitcoin Orange: `#F7931A`
- Ethereum Purple: `#627EEA`
- USDC Blue: `#2775CA`
- Solana Purple: `#9945FF`
- Dark Canvas: `#0A0B0D`
- Dark Surface 1: `#13151A`

### Example Component Prompts
- "Build the Coinbase Home portfolio block: 'PORTFOLIO BALANCE' label in Coinbase Sans 11pt weight 700 letter-spaced +0.6pt `#5B616E`. Below: portfolio value '$12,847.93' in Coinbase Display 40pt weight 700 `#0A0B0D` with tabular numerals. Below: 24h change '+$847.93 (+6.59%)' in Coinbase Sans 16pt weight 500 in `#05B169` (green) if positive or `#CF202F` (red) if negative. 16pt horizontal margin."
- "Style a Coinbase asset row: 64pt tall white row. Leading 40pt circular icon in the asset's brand color (`#F7931A` for Bitcoin, `#627EEA` for Ethereum, `#2775CA` for USDC) with the asset symbol or glyph in white centered. Middle: asset name 'Bitcoin' in Coinbase Sans 16pt weight 600 `#0A0B0D` above 'BTC · 0.1842 BTC' in Coinbase Sans 13pt weight 500 `#5B616E` (ticker tracked +0.3pt). Then a 56pt × 20pt mini sparkline SVG in the 24h-direction color (green up, red down). Trailing right-aligned: market value '$12,389.42' in Coinbase Sans 16pt weight 500 `#0A0B0D` tabular above '+$234.18 (+1.92%)' in Coinbase Sans 13pt weight 500 tabular green or red. 0.5pt `#E1E4E8` bottom divider."
- "Render the Coinbase 4-up action row on an asset detail screen: 4 equal-width buttons in a single row — Buy, Sell, Send, Receive. Each button is a 12pt-radius `#F7F8FA` rounded rectangle with a stacked 28pt Coinbase Blue icon (`#0052FF`) above a Coinbase Sans 13pt weight 500 label in `#0A0B0D`. 8pt gap between buttons, 12pt internal padding. Buy = up-arrow, Sell = down-arrow, Send = paper-plane, Receive = QR-code."
- "Build the Coinbase Buy/Sell amount screen: fullscreen modal with the $-amount centered. Use Coinbase Display 56pt weight 700 in `#0A0B0D` with tabular numerals. Currency selector below: 'USD' / 'BTC' tap-toggle in Coinbase Sans 13pt 500 `#5B616E` with a chevron. Standard numeric keypad at the bottom. Sticky CTA at the bottom: 'Preview buy' or 'Buy Bitcoin' in Coinbase Blue `#0052FF` fill, white Coinbase Sans 16pt 600 text, 48pt height, 12pt corner radius."
- "Render a Coinbase wallet address display: Coinbase Mono 13pt weight 500 in `#0A0B0D`, with the middle truncated like '0x742d35Cc...8b9b9d04'. Surface Gray `#F7F8FA` background, 8pt corner radius, 12pt padding. Tap-to-copy with a 1500ms green toast 'Copied' appearing at the top of the screen."

### Iteration Guide
1. Canvas is `#FFFFFF` (light) or `#0A0B0D` Coinbase Black (dark) — the dark canvas has a subtle cool tint
2. Coinbase Blue `#0052FF` is THE brand accent — primary CTAs, active tab, C-mark, link color
3. Primary text is `#0A0B0D` (Coinbase Black, slight cool tint), NOT pure black
4. Coinbase Display for hero portfolio values (40pt+); Coinbase Sans for everything else; Coinbase Mono ONLY for wallet addresses and tx hashes
5. Every asset gets a circular 40pt icon in its OFFICIAL brand color — Bitcoin orange, ETH purple, USDC blue — with white glyph centered
6. Tabular numerals on every price / percentage / quantity — the institutional asset list look depends on it
7. Mini sparkline (56pt × 20pt) inside each asset row showing 24h trend in the direction color
8. 4-up action row (Buy / Sell / Send / Receive) is the brand pattern on every asset detail screen
9. Track ticker symbols at +0.3pt letter spacing — branded micro-monograms
10. Shadows are 0.04-0.08 opacity — Coinbase is institutional clean, not glossy
