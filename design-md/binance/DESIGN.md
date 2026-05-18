# Design System Inspiration of Binance (iOS)

## 1. Visual Theme & Atmosphere

Binance's iOS app is a dark, dense, data-first trading terminal compressed into a phone. The default and dominant experience is dark mode: a near-black canvas (`#0B0E11`) onto which thousands of live numbers stream — prices, percentages, order books, candlesticks, P&L. The atmosphere is "professional exchange, not consumer wallet." Where Coinbase rounds its corners and pads generously to feel friendly, Binance tightens everything: 12pt row paddings, hairline `#2B3139` dividers, and tabular monospace figures so that a markets list shows ten pairs without scrolling. The screen should feel like a Bloomberg terminal that happens to fit in a hand — information density is a feature, not a flaw.

The single brand accent is **Binance Yellow** (`#F0B90B`) — a saturated amber-gold that appears on the logo, the primary "Convert"/"Deposit" CTA, the active tab, selected market-tab underlines, and links. It is used sparingly and deliberately: yellow means "this is the Binance action." Everything else is carried by **semantic market color**: green (`#0ECB81`) for up / buy / bids, red (`#F6465D`) for down / sell / asks. These two colors are absolute and never invert between themes — green is always up, red is always down, because a trader's muscle memory depends on it. The candlestick chart uses the same green/red for bullish/bearish candles.

Typography is split-brain by design. UI chrome (titles, labels, body, tab names) is set in a clean humanist sans — Binance ships a custom **BinancePlex** family, closely modeled on IBM Plex Sans, with PingFang SC for CJK. But every piece of **numeric data** — balances, prices, the order book, 24h volume, percentages — is rendered in a **monospace** face (BinancePlex Mono / IBM Plex Mono fallback) with tabular figures, so decimal points line up vertically in lists and the order book and balances don't "jitter" as digits change. This sans/mono split is the most recognizable typographic signature of the app.

The signature screens are the **Markets list** (favorites/spot/futures tabs, each row = coin icon + pair + 24h volume + monospace price + a colored percentage pill) and the **Trade screen** (candlestick chart on top, depth-shaded order book, Buy/Sell ticket). Secondary signature surfaces: the **Convert** flow (a frictionless from/to coin swap with a single yellow confirm) and the **Wallet/Funding** overview. The home screen leads with an "Est. Total Value" balance hero in monospace plus quick-action buttons (Deposit / Withdraw / Convert).

**Key Characteristics:**
- Dark-native canvas (`#0B0E11`) — the app was designed in dark; light theme is an afterthought
- Binance Yellow (`#F0B90B`) is the *only* brand accent — used for primary CTA, active tab, links
- Absolute market semantics: green `#0ECB81` = up/buy, red `#F6465D` = down/sell — never inverted
- Sans/mono split: chrome in BinancePlex (IBM Plex Sans), all numbers in monospace tabular figures
- Markets list: dense rows, colored percentage pills, monospace prices, favorites/spot/futures tabs
- Candlestick chart with green/red candles, time-interval chips, MA/depth overlays
- Depth-shaded order book — ask/bid rows with a translucent quantity bar behind the numbers
- Convert flow — a single-screen from/to swap with one yellow confirm
- Buy/Sell ticket with limit/market toggle, percentage slider (25/50/75/100%)
- Bottom tab bar (5): Home, Markets, Trade, Futures, Wallets — active in yellow
- Small radii (2–8pt) — terminal-tight, not consumer-rounded

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Binance Yellow** (`#F0B90B`): The single brand accent — primary CTA background, active tab, active market-tab underline, links, focused inputs, selected chips.
- **Yellow Pressed** (`#C99400`): Pressed/active state of yellow buttons.
- **Yellow Tint** (`#F0B90B1F`): 12% yellow wash behind selected rows and yellow ghost chips.

### Market Semantics (never invert across themes)
- **Up / Buy / Bid Green** (`#0ECB81`): Positive price change, Buy button, bid rows, bullish candles, gain P&L.
- **Down / Sell / Ask Red** (`#F6465D`): Negative price change, Sell button, ask rows, bearish candles, loss P&L.
- **Up Pressed** (`#0BA572`) / **Down Pressed** (`#D93849`): Pressed states for Buy/Sell.

### Canvas & Surfaces (Dark — default)
- **Canvas** (`#0B0E11`): Primary app background.
- **Surface 1** (`#181A20`): Cards, sheets, table containers, nav bars.
- **Surface 2** (`#1E2026`): Elevated rows, search field, segmented controls, input fields.
- **Surface 3** (`#2B3139`): Pressed rows, chip backgrounds, skeleton, hairline dividers.
- **Divider** (`#2B3139`): 1pt hairlines between list rows and sections.

### Canvas & Surfaces (Light — secondary theme)
- **Canvas Light** (`#FFFFFF`): Light background.
- **Surface 1 Light** (`#F5F5F5`): Cards, grouped containers.
- **Surface 2 Light** (`#FAFAFA`): Elevated rows, input fields.
- **Divider Light** (`#EAECEF`): Hairline dividers.

### Text
- **Text Primary Dark** (`#EAECEF`): Primary text, prices, headings on dark.
- **Text Secondary Dark** (`#848E9C`): Labels, metadata, secondary values, placeholders.
- **Text Tertiary Dark** (`#5E6673`): Disabled, very low-emphasis hints, axis labels.
- **Text Primary Light** (`#1E2329`): Primary text on light.
- **Text Secondary Light** (`#707A8A`): Secondary text on light.

### Semantic (status)
- **Success** (`#0ECB81`): Order filled, deposit confirmed (reuses Up Green).
- **Error** (`#F6465D`): Order failed, validation error (reuses Down Red).
- **Warning** (`#F0B90B`): Risk warnings, liquidation alerts (reuses brand Yellow).
- **Info** (`#3375BB`): Informational toasts, KYC banners.

### Color → Role Table

| Role | Dark | Light |
|------|------|-------|
| Canvas | `#0B0E11` | `#FFFFFF` |
| Surface 1 | `#181A20` | `#F5F5F5` |
| Surface 2 | `#1E2026` | `#FAFAFA` |
| Surface 3 / Divider | `#2B3139` | `#EAECEF` |
| Brand accent (Yellow) | `#F0B90B` | `#F0B90B` |
| Up / Buy | `#0ECB81` | `#0ECB81` |
| Down / Sell | `#F6465D` | `#F6465D` |
| Text primary | `#EAECEF` | `#1E2329` |
| Text secondary | `#848E9C` | `#707A8A` |
| Text tertiary | `#5E6673` | `#A1A7B3` |
| Link | `#F0B90B` | `#F0B90B` |

## 3. Typography Rules

### Font Family
- **UI Sans**: `BinancePlex` (Binance's custom face, derived from IBM Plex Sans by IBM / Bold Monday, SIL OFL). Use **IBM Plex Sans** as the open fallback.
- **Numeric Mono**: `BinancePlex Mono` for all numbers. Use **IBM Plex Mono** as the open fallback. Always enable tabular figures (`font-variant-numeric: tabular-nums`).
- **CJK**: `PingFang SC` (iOS system) for Chinese; `Hiragino Sans` for Japanese.
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Stack (mono)**: `'SF Mono', ui-monospace, Menlo, Consolas, monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Sans | 32pt | 700 | 1.2 | -0.6pt | Large nav title (Markets, Wallets) |
| Balance Hero | Mono | 28pt | 600 | 1.15 | -0.5pt | Est. total value, tabular |
| Section Title | Sans | 22pt | 700 | 1.25 | -0.2pt | "Spot Wallet", "Recent Trades" |
| Row Title | Sans | 18pt | 600 | 1.3 | -0.1pt | Coin pair, page heading on detail |
| Body | Sans | 16pt | 400 | 1.5 | 0pt | Descriptions, explanatory copy |
| Price / Number | Mono | 15pt | 600 | 1.3 | 0pt | List prices, order book, % change |
| List Label | Sans | 14pt | 600 | 1.3 | 0pt | "Order Book", "Trades", labels |
| Meta | Sans | 14pt | 400 | 1.4 | 0pt | 24h volume, high/low, timestamps |
| Pill / Chip | Sans | 13pt | 600 | 1.0 | 0pt | % change pill, time-interval chips |
| Caption | Sans | 12pt | 500 | 1.35 | 0.1pt | "Last updated", footnotes |
| Mono Caption | Mono | 12pt | 500 | 1.35 | 0pt | Order book secondary qty, depth |
| Tab Label | Sans | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Sans | 15pt | 600 | 1.0 | 0pt | Primary / Buy / Sell |

### Principles
- **Sans for words, mono for numbers**: This is the rule. Any glyph that represents a quantity (price, %, volume, balance, qty) is monospace with tabular figures. Any glyph that is a word/label is sans.
- **Tabular figures always on numbers**: Decimal points must align vertically down a markets list and the order book. Never use proportional figures for live data.
- **Weight concentrated at 400 / 500 / 600 / 700**: Body 400, captions/meta 500, labels/prices/buttons 600, titles 700. No thin, no black.
- **Color encodes sign, not weight**: A +/- value's color (green/red) carries the meaning; weight stays 600. Do not bold gains and regular losses.
- **Dense by default**: Line heights are tight (1.2–1.4) for data rows; only body copy gets 1.5.
- **Dynamic Type**: Titles, body, labels scale; numeric mono columns, tab labels, and order-book rows stay FIXED to preserve column alignment.

## 4. Component Stylings

### Buttons

**Primary Button (Convert / Deposit / Confirm)**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#F0B90B` (Binance Yellow)
- Text: `#0B0E11` (near-black on yellow — never white)
- Padding: 12pt vertical, 28pt horizontal; full-width on forms (height 48pt)
- Font: Sans 15pt weight 600
- Pressed: background `#C99400` + scale 0.98
- Disabled: background `#F0B90B` at 40% opacity, text `#0B0E11` at 50%

**Buy Button**
- Background: `#0ECB81`; Text `#FFFFFF`; 8pt radius; same metrics as primary
- Pressed: `#0BA572`

**Sell Button**
- Background: `#F6465D`; Text `#FFFFFF`; 8pt radius
- Pressed: `#D93849`

**Ghost / Secondary Button (Withdraw / Transfer)**
- Background: `#2B3139` (Surface 3); Text `#EAECEF`
- Border: none
- Corner radius: 8pt; Padding: 11pt / 20pt
- Font: Sans 14pt weight 600
- Pressed: background lightens to `#363D47`

**Text Button (View all / See more)**
- Background: transparent; Text `#F0B90B` (yellow)
- Font: Sans 14pt weight 600; no underline
- Pressed: text `#C99400`

**Percentage Slider Chips (25 / 50 / 75 / 100%)**
- Pill, `#2B3139` bg, `#848E9C` text 13pt w600
- Selected: `#F0B90B1F` bg, `#F0B90B` text

### Core Atoms

**Coin Icon**
- 30pt circle (24pt in dense lists), brand-colored token glyph or coin logo
- BTC `#F7931A`, ETH `#627EEA`, BNB `#F0B90B`, USDT `#26A17B`, SOL `#14F195`

**Percentage Change Pill**
- Min-width 64pt, height 28pt, 4pt corner radius
- Background: solid `#0ECB81` (up) or `#F6465D` (down)
- Text: `#FFFFFF`, Mono 13pt w600, e.g. `+2.34%` / `-0.92%`
- Variant (chart screen): no fill, colored text only

**Price Cell**
- Mono 15pt w600 `#EAECEF` primary line; Mono 12pt `#848E9C` USD-equivalent secondary line, right-aligned

**Time Interval Chips (chart)**
- Row: `1m 5m 15m 1H 4H 1D 1W`
- Inactive: `#848E9C` 13pt w600, no bg
- Active: `#F0B90B` text, `#F0B90B1F` pill bg, 4pt radius

**Tag / Badge**
- "NEW", "10x", "Vol" badge — 4pt radius, `#2B3139` bg, `#848E9C` 10pt w700; leverage badges use `#F0B90B1F` bg / `#F0B90B` text

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#0B0E11` at 96% with hairline top border `#2B3139`
- Tabs (5): Home, Markets, Trade, Futures, Wallets
- Icon size: 22pt (Trade often a centered emphasized icon)
- Active: `#F0B90B` (filled icon + yellow label)
- Inactive: `#5E6673` (outline icon + tertiary label)
- Labels: Sans 10pt w500, always shown

**Top Nav (detail screens)**
- Height: 44pt + safe area
- Leading: back chevron `#EAECEF` 22pt
- Center: pair title Sans 18pt w600 + small % change beneath
- Trailing: star (favorite) + share + ⋯

**Market Tabs (segmented under header)**
- Row of text tabs: Favorites / Spot / Futures / Hot
- Active: `#EAECEF` 14pt w600 + 2pt `#F0B90B` underline
- Inactive: `#848E9C` 14pt w600

**Buy/Sell Segmented Toggle**
- Two-segment pill; selected Buy = `#0ECB81` fill / white text; selected Sell = `#F6465D` fill / white text; unselected = `#2B3139` / `#848E9C`

### Input Fields

**Search Bar**
- Height: 36pt; Background: `#1E2026`; Border: none; Corner radius: 8pt
- Leading `magnifyingglass` 14pt `#848E9C`
- Placeholder: "Search BTC, ETH, BNB…" Sans 13pt `#848E9C`
- Focus: 1pt `#F0B90B` border

**Amount / Order Input**
- Height: 48pt; Background: `#1E2026`; Corner radius: 8pt
- Value: Mono 18pt w600 `#EAECEF`, right-aligned; leading currency label `#848E9C`
- Trailing "MAX" text button in `#F0B90B`
- Focus: 1pt `#F0B90B` border; Error: 1pt `#F6465D` border + helper text `#F6465D` 12pt

**Stepper / Quantity (limit order)**
- `−` / `+` 32pt square `#2B3139` buttons flanking a centered mono value field

### Distinctive Components

**Markets List Row**
- Height: ~56pt; 12pt vertical / 16pt horizontal padding; 1pt `#2B3139` divider
- Layout: [coin icon 30pt] [pair name + 24h vol stacked] [price + USD stacked, right] [% pill 64pt]
- Pair: Sans 14pt w600 `#EAECEF`; quote currency `#5E6673` 11pt
- Volume: Sans 11pt `#848E9C`
- Price: Mono 14pt w600; USD-eq: Mono 11pt `#848E9C`
- Long-press: quick "Trade / Add to favorites" sheet

**Candlestick Chart**
- Bullish candle: `#0ECB81`; Bearish candle: `#F6465D`
- Grid lines: `#2B3139` at 40% opacity; axis labels Mono 10pt `#5E6673`
- Crosshair on touch: dashed `#848E9C` with a floating mono price tag
- Overlays: MA(7/25/99) thin lines; volume histogram beneath in muted green/red
- Interval chips above; chart-type toggle (candles / depth / line) top-right

**Order Book**
- Two stacks: asks (red, top) and bids (green, bottom), spread row in the middle
- Each row: price (Mono, colored) left, quantity (Mono `#EAECEF`) right
- **Depth shading**: a translucent bar (`rgba(246,70,93,0.14)` ask / `rgba(14,203,129,0.14)` bid) grows from the right edge proportional to cumulative size — the book's signature look
- Spread/last-price row: Mono 16pt w600, colored to last tick direction, with a ↑/↓ arrow
- Tap a row to prefill the order ticket price

**Convert Card**
- Two stacked tiles ("From" / "To") separated by a circular swap button
- Each tile: coin selector chip (icon + ticker + chevron) + Mono amount input + "Available" line
- Single full-width yellow "Preview Conversion" CTA; result shows a locked-rate countdown ring

**Trade Ticket (Buy/Sell)**
- Buy/Sell segmented toggle on top
- Limit/Market tab; price input (limit only) + amount input + 25/50/75/100% slider
- "Avbl" balance line in `#848E9C`
- Submit = full-width `#0ECB81` "Buy BTC" or `#F6465D` "Sell BTC"

**P&L / Portfolio Pill**
- Today's change shown as Mono `+$214.86 (+1.70%)` colored green/red beneath the balance hero

**Spot/Futures Mode Switch**
- Pill segmented control; Futures mode tints accents subtly and exposes a leverage badge (`10x` in `#F0B90B1F`)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40
- List row vertical padding: 12pt (dense); screen horizontal inset: 16pt
- Card internal padding: 16pt; section gap: 12pt
- Inter-element gap in rows: 8–10pt

### Grid & Container
- iPhone: 16pt horizontal insets; full-bleed chart and order book
- Markets list: single column, full-width rows, hairline dividers (no card chrome)
- iPad: two-pane — markets list left (360pt) + chart/trade detail right
- Chart occupies ~38% of trade screen height; order book + ticket fill the rest

### Whitespace Philosophy
- **Density is the product**: tight 12pt paddings let a markets list show 8–10 pairs at once — this is intentional, not cramped
- **Hairlines over cards**: list rows are separated by 1pt `#2B3139` dividers, not card gaps
- **Numbers breathe via alignment**: tabular mono + right-alignment creates visual order without extra space
- **Yellow is rare**: large neutral fields make the single yellow CTA pop

### Border Radius Scale
- Square (0pt): chart area, full-bleed dividers, candlesticks
- Micro (2pt): order-book depth bars, tiny badges
- Subtle (4pt): % pills, chips, time-interval chips, tags
- Standard (8pt): buttons, inputs, search, cards, sheets
- Comfortable (12–16pt): bottom sheets, modals
- Pill (500pt): segmented toggles, percentage slider chips
- Circle (50%): coin icons, swap button, avatars

## 6. Depth & Elevation

Binance is mostly flat — depth comes from surface-tone steps (`#0B0E11` → `#181A20` → `#1E2026` → `#2B3139`), not shadows. Shadows appear only on floating sheets and the active trade ticket.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; tone step only | List rows, cards, chart, order book |
| Raised (Level 1) | `rgba(0,0,0,0.4) 0 2px 8px` | Sticky Buy/Sell ticket, floating action bar |
| Sheet (Level 2) | `rgba(0,0,0,0.5) 0 -6px 24px` | Bottom sheets (coin picker, order confirm) |
| Modal Overlay | `rgba(0,0,0,0.6)` scrim | Behind modals, KYC, leverage sheet |

**Shadow Philosophy**: On a near-black canvas, shadows barely register, so Binance separates layers with progressively lighter surface tones and 1pt `#2B3139` borders on floating panels. Shadow is used only to lift the order ticket and bottom sheets so "tap outside to dismiss" reads clearly.

### Motion
- **Price tick flash**: when a price updates, the cell briefly flashes green (up) or red (down) background at ~10% opacity for 150ms, then fades — the heartbeat of the app
- **Order book reflow**: rows shift in 120ms ease-out as depth changes; no springy bounce (data must feel reliable)
- **Tab switch (Markets tabs)**: underline slides to the new tab in 200ms ease-out; content cross-fades 150ms
- **Buy/Sell toggle**: segment fill slides + color-morphs green↔red in 180ms ease-out
- **Chart interaction**: crosshair tracks finger 1:1 (no lag); interval change re-renders instantly
- **Convert swap**: from/to tiles swap with a 250ms vertical flip + soft haptic
- **Sheet present**: bottom sheet slides up 300ms ease-out; scrim fades in
- **Pull-to-refresh**: yellow circular spinner; haptic on trigger
- **Haptics**: soft impact on order submit, selection tick on percentage-slider snap, success notification haptic on filled order

## 7. Do's and Don'ts

### Do
- Use the deep canvas `#0B0E11` as the default — Binance is dark-native
- Make Binance Yellow `#F0B90B` the *only* brand accent (CTA, active tab, links)
- Set ALL numbers in monospace with tabular figures so columns align
- Keep green `#0ECB81` = up/buy and red `#F6465D` = sell/down — absolute, never inverted
- Use dense 12pt row padding and 1pt `#2B3139` hairline dividers in lists
- Render the order book with translucent depth bars growing from the right edge
- Flash a cell green/red for ~150ms when its price ticks
- Use solid colored % pills (`#0ECB81`/`#F6465D`) in the markets list
- Put black text on yellow buttons (`#0B0E11` on `#F0B90B`) — never white
- Separate layers with surface-tone steps + 1pt borders, not heavy shadows
- Keep the Convert flow to a single screen with one yellow confirm

### Don't
- Don't use white or any color other than `#F0B90B` for the primary CTA
- Don't render prices/percentages in a proportional sans — alignment breaks
- Don't invert up/down colors in light mode (green stays up, red stays down)
- Don't pad rows generously "to feel friendly" — density is the Binance identity
- Don't put white text on the yellow button (fails contrast and is off-brand)
- Don't add heavy drop shadows on list cards — use tone steps
- Don't bold gains while leaving losses regular — color carries the sign, weight stays 600
- Don't introduce a second brand accent color — yellow is singular
- Don't animate the order book with bouncy springs — data must feel stable
- Don't use rounded 16pt+ corners on trading rows — radii stay 2–8pt there
- Don't hide the 24h % change — it is the primary scannable signal per row

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Markets rows compress to 24pt icons; chart ~34% height |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Chart taller; order book shows more depth levels |
| iPad (portrait) | 768pt | Two-pane: markets list (360pt) + detail |
| iPad (landscape) | 1024pt+ | Three-pane: list + chart + order book/ticket side-by-side |

### Dynamic Type
- Screen titles, body, labels, captions: scale with Dynamic Type
- Numeric mono columns (markets prices, order book, balances), tab labels, % pills: FIXED — scaling them would break tabular column alignment
- Chart axis labels: fixed

### Orientation
- Portrait default everywhere
- Trade screen supports landscape: full-width chart with a side order book (pro layout)
- Markets list locks portrait

### Touch Targets
- Markets row: full-row tap, 56pt tall
- Tab bar icons: 22pt glyph, 44pt+ hit
- Percentage slider chips: 32pt tall, ≥44pt hit
- Order-book row: full-row tap to prefill price (≥28pt)
- Buy/Sell buttons: ≥48pt tall

### Safe Area Handling
- Top: nav respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; sticky trade ticket sits above the home indicator
- Keyboard: amount inputs scroll above keyboard; percentage slider stays visible above the keyboard on the trade ticket

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#0B0E11`
- Surface 1 / 2 / 3: `#181A20` / `#1E2026` / `#2B3139`
- Brand accent (Binance Yellow): `#F0B90B` (pressed `#C99400`)
- Up / Buy / Bid: `#0ECB81`
- Down / Sell / Ask: `#F6465D`
- Text primary: `#EAECEF` / secondary `#848E9C` / tertiary `#5E6673`
- Link: `#F0B90B`
- Canvas (light): `#FFFFFF`; text primary light `#1E2329`
- Depth bar (ask): `rgba(246,70,93,0.14)`; (bid): `rgba(14,203,129,0.14)`

### Example Component Prompts
- "Build the Binance markets list row in SwiftUI: 56pt tall, `#0B0E11` background, 1pt `#2B3139` bottom divider, 16pt horizontal / 12pt vertical insets. Leading 30pt circular coin icon. Then a VStack: pair `BTC` in IBM Plex Sans 14pt w600 `#EAECEF` with `/USDT` quote in `#5E6673` 11pt, and `Vol 1.42B` in 11pt `#848E9C` below. Trailing: a VStack right-aligned with price `67,284.10` in IBM Plex Mono 14pt w600 `#EAECEF` and `$67,284.10` in Mono 11pt `#848E9C`. Far trailing: a 64pt min-width pill, 4pt radius, solid `#0ECB81` background, white Mono 13pt w600 text `+2.34%`."

- "Create the Binance order book in SwiftUI: a VStack of ask rows then a spread row then bid rows. Each row is an HStack: price on the left in IBM Plex Mono 12pt w600 (`#F6465D` for asks, `#0ECB81` for bids), quantity on the right in Mono 12pt `#EAECEF`. Behind each row, a depth bar grows from the trailing edge: a Rectangle filled `Color.red.opacity(0.14)` for asks / `Color.green.opacity(0.14)` for bids, width = cumulativeSize / maxSize. The middle spread row shows last price in Mono 16pt w600 colored by last tick direction with a ↑ or ↓ glyph."

- "Build the Binance balance hero: label 'Est. Total Value' in IBM Plex Sans 12pt `#848E9C` with a trailing eye icon. Below: `12,840.57` in IBM Plex Mono 28pt w600 `#EAECEF` with `USDT` suffix in 14pt `#848E9C`. Below that: today's P&L `+$214.86 (+1.70%)` in IBM Plex Mono 12pt colored `#0ECB81`. Then a row of three buttons: 'Deposit' full `#F0B90B` (black text), 'Withdraw' and 'Convert' as `#2B3139` ghost buttons, all 36pt tall, 8pt radius."

- "Render the Binance Buy/Sell trade ticket: a 2-segment toggle (Buy `#0ECB81` / Sell `#F6465D`, unselected `#2B3139` `#848E9C`). Below: Limit/Market text tabs. A price input (mono, 48pt tall, `#1E2026`, 8pt radius) and an amount input. A 4-stop percentage slider with chips 25/50/75/100 (`#2B3139` → selected `#F0B90B1F`/`#F0B90B`). An 'Avbl 1,204.50 USDT' line in `#848E9C`. Full-width submit button `#0ECB81` 'Buy BTC' (white text, 48pt, 8pt radius)."

- "Build the Binance Convert card: two stacked tiles on `#181A20`, 8pt radius, separated by a 36pt circular `#F0B90B` swap button. Each tile: a coin selector chip (24pt icon + ticker + chevron) at top-left, a right-aligned IBM Plex Mono 18pt w600 amount field, and an 'Available 0.482 BTC' line in `#848E9C` 12pt. Below both tiles: a full-width `#F0B90B` 'Preview Conversion' button with black text."

- "Create the Binance bottom tab bar: 5 items (Home, Markets, Trade, Futures, Wallets) over a `#0B0E11`@96% bar with a 1pt `#2B3139` top border, 56pt + safe area. Active item icon + label `#F0B90B` (filled icon); inactive `#5E6673` (outline icon). Labels IBM Plex Sans 10pt w500."

### Iteration Guide
1. Canvas is `#0B0E11` (dark-native) — light theme only swaps to `#FFFFFF` / `#1E2329`; accents never change
2. Binance Yellow `#F0B90B` is the *single* brand accent — black text on it, never white
3. Every number is IBM Plex Mono with tabular figures — words are IBM Plex Sans
4. Up/buy = `#0ECB81`, down/sell = `#F6465D`, absolute and never inverted
5. Markets rows are dense (12pt padding) with 1pt `#2B3139` hairline dividers — density is the identity
6. The order book's signature is the translucent depth bar growing from the trailing edge
7. Price cells flash green/red ~150ms on tick — the app's heartbeat
8. % change is shown as a solid colored pill (`#0ECB81`/`#F6465D`) in the markets list
9. Radii are tight on trading UI: 2pt depth bars, 4pt pills/chips, 8pt buttons/inputs
10. Separate layers with surface-tone steps + 1pt borders, not shadows; shadows only lift sheets and the trade ticket
