# Design System Inspiration of Webull (iOS)

## 1. Visual Theme & Atmosphere

Webull's iOS app is a **dark-native trading terminal in your pocket**. Where Fidelity or Schwab present a calm brokerage lobby, Webull presents a Bloomberg-terminal aesthetic for retail traders: a near-black canvas, dense candlestick charts, a live order-book ladder, an options chain, and a split green/red Buy/Sell action pair. The app is built for people who stare at charts — the entire visual system optimizes for chart legibility and information density, not for warmth or simplicity. There is no meaningful "light mode"; like every serious trading product, dark is the only mode that matters.

The signature surface is the **quote/chart screen paired with the watchlist**. The Quotes tab is the home of the product: a watchlist of symbols, each a row with ticker, name, a tiny sparkline, last price, and a saturated percent-change chip. Tap any symbol and you land on the full quote screen — symbol header with last price in a huge tabular numeral, a full-width interactive **candlestick chart** with a timeframe strip (1m/5m/1D/5D/1M/1Y) and indicators, the **order-book ladder** (bid/ask with depth bars), key stats, options chain, and a docked **Buy / Sell** button pair. This chart-screen + watchlist pairing, and the candlestick chart itself, is the most recognizable and most-used part of Webull.

The color system is **loud where it counts**. Webull's brand is a **blue→cyan gradient** (`#1B9EFB → #20D5C4`) — used for the logo, the primary Trade action, selected timeframe chips, the paper-trading badge, links, and focus. Unlike a calm brokerage where green/red is sparing, Webull's performance color is **pervasive and saturated**: up is `#00C076`, down is `#FA5252`, and it floods the screen — every candle, sparkline, % chip, order-ladder row, and the Buy (green) / Sell (red) button pair. The near-black canvas (`#0B0E11`) exists precisely to make these saturated greens, reds, and the blue→cyan gradient pop with maximum contrast. Surfaces lift only barely; chart grid lines are nearly invisible so the data dominates.

Typography is a **clean grotesque with strong tabular DIN-style numerics**. Webull's numbers — prices, the ladder, option strikes, % changes — must align to the decimal point in dense tables, so every figure uses **tabular lining figures**. The brand UI face on iOS falls back to SF Pro; the closest free substitute is **Inter** with `tnum` enabled. Headings are 700–800; body is 400; numbers lean 700–800 and tabular always. The overall feel is precise, technical, fast — a tool, not a brochure.

**Key Characteristics:**
- Dark-native only — near-black canvas `#0B0E11`; no meaningful light mode (it's a trading terminal)
- Webull blue→cyan brand gradient `#1B9EFB → #20D5C4` — logo, Trade action, selected timeframes, paper badge
- Pervasive saturated performance color — up `#00C076`, down `#FA5252` — candles, sparklines, chips, ladder, buttons
- Candlestick chart as the centerpiece — full-width, timeframe strip, indicators, dashed prev-close line
- Quote screen + watchlist pairing is the product spine — Quotes → Symbol → Chart → Trade
- Order-book ladder — bid/ask rows with depth bars and a mid/spread divider
- Split Buy (green) / Sell (red) action pair — docked on the quote screen
- Options chain — Call | Strike | Put three-column table, green calls / red puts
- Tabular / monospaced numerals everywhere — ladder, chain, prices align to the decimal
- Bottom tab bar: Markets / Quotes / Trade / Portfolio / Menu — five-tab terminal nav
- Paper-trading badge (cyan) — Webull's signature "practice with fake money" mode

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Webull Blue** (`#1B9EFB`): Primary brand blue — start of the gradient, primary Trade button, links, selected timeframe, focus rings. On-color text is near-black `#04121C`.
- **Webull Cyan** (`#20D5C4`): End of the gradient — paper-trading badge, accent highlights, gradient terminus.
- **Brand Gradient** (`#1B9EFB → #20D5C4`, 135°): Logo, primary Trade CTA, hero accents, selected/active brand surfaces.
- **Webull Blue Pressed** (`#1684D6`): Pressed state of the primary blue button.

### Canvas & Surfaces (Dark — the only mode)
- **Dark Canvas** (`#0B0E11`): Primary canvas — near-black, maximizes chart/candle contrast.
- **Dark Surface 1** (`#14181D`): Cards, panels, grouped sections.
- **Dark Surface 2** (`#1C2127`): Raised rows, chips, pressed surfaces, inputs.
- **Divider** (`#232931`): Hairline dividers between rows and sections.
- **Grid Line** (`#1A1F26`): Chart grid lines — almost invisible, never competes with candles.

### Optional Light Mode (rarely used; provided for completeness)
- **Light Canvas** (`#FFFFFF`), **Light Surface** (`#F4F6F8`), **Light Divider** (`#E4E8EC`), **Light Text** (`#101418`). Webull ships a light theme but the product is designed and overwhelmingly used in dark; treat dark as canonical.

### Text
- **Text Primary** (`#EAEEF2`): Primary text on dark.
- **Text Secondary** (`#8B95A1`): Metadata, names, labels.
- **Text Tertiary** (`#5A636E`): Disabled, axis labels, lowest emphasis.
- **On-Brand Text** (`#04121C`): Text on the blue/cyan gradient or solid blue.
- **On-Up Text** (`#03150D`) / **On-Down Text** (`#1B0606`): Text on the green Buy / red Sell buttons.

### Semantic — Market (loud and pervasive, the core of the system)
| Role | Color | Use |
|------|-------|-----|
| Up / Bid / Call | `#00C076` | Up candles, positive %, gain chips, bid ladder rows, Buy button, calls |
| Down / Ask / Put | `#FA5252` | Down candles, negative %, loss chips, ask ladder rows, Sell button, puts |
| Flat / Unchanged | `#8B95A1` | 0.00% — uses secondary text color |
| Up Fill (depth/area) | `#00C07624` | Bid depth bars, area-chart fill (≈14% green) |
| Down Fill (depth/area) | `#FA525224` | Ask depth bars, down area fill (≈14% red) |
| Prev-close line | `#1B9EFB` (dashed) | Dashed reference line at previous close on the chart |

> Region note: in many Asia markets Webull inverts up=red / down=green. The tokens stay the same; only the up/down→color mapping flips by a user/region setting. Default here is US (up=green).

### Semantic — System
- **Alert Amber** (`#F7A600`): Price-alert triggers, halted/extended-hours banners, caution.
- **Error Red** (`#FA5252`): Form validation, order-rejected (same as Down — context disambiguates).
- **Success** (`#00C076`): Order-filled confirmation (same as Up — intentional).
- **Info / Link** (`#1B9EFB`): Informational links, disclosures.
- **Paper Badge** (`#20D5C4` on `#04121C`): The signature paper-trading indicator.

## 3. Typography Rules

### Font Family
- **Brand UI**: a clean grotesque with DIN-style numerics (Webull uses a custom UI sans + a DIN-like numeric face for prices).
- **iOS Fallback**: `-apple-system, 'SF Pro Text', 'SF Pro Display'` — system face on device.
- **Recommended free substitute**: `Inter` (by Rasmus Andersson, SIL OFL) with tabular figures; for an even more terminal feel, prices may use a condensed DIN-like face such as `Oswald`/`Barlow Semi Condensed` — but Inter `tnum` is the simplest faithful default.
- **Numeric treatment**: Tabular / monospaced lining figures **everywhere** (last price, %, ladder, option chain, stats). In Inter use `font-feature-settings: "tnum" 1;`; on SF use `.monospacedDigit()`. The ladder and option chain MUST align to the decimal.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Last Price | Inter | 32pt | 800 | 1.1 | -0.6pt | Quote-screen last price — tabular, colored up/down |
| Screen Title | Inter | 24pt | 800 | 1.2 | -0.3pt | "Quotes", "Markets" |
| Section Header | Inter | 20pt | 700 | 1.25 | -0.2pt | "Order book", "Options" |
| Symbol Header | Inter | 17pt | 700 | 1.3 | -0.1pt | "TSLA · NASDAQ" |
| Body | Inter | 15pt | 400 | 1.5 | 0pt | Descriptions, disclosures |
| Row Symbol | Inter | 14pt | 600 | 1.3 | 0pt | Watchlist ticker |
| Row Value | Inter | 15pt | 700 | 1.3 | 0pt | Watchlist last price — tabular |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | "Vol 84.2M · Mkt Cap 791B" |
| Numeric Mono | Inter (tnum) | 12pt | 700 | 1.3 | 0pt | % change line, ladder sizes — tabular |
| Ladder / Chain | Inter (tnum) | 13pt | 700 | 1.3 | 0pt | Bid/ask prices, option strikes — tabular |
| Label | Inter | 11pt | 600 | 1.0 | 0.4pt | "BID/ASK", "LEVEL 2", column heads (uppercase) |
| Button | Inter | 15pt | 800 | 1.0 | 0.3pt | Buy / Sell / Trade buttons |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Timeframe | Inter | 11pt | 700 | 1.0 | 0.2pt | 1m/5m/1D/5D/1M/1Y chips |

### Principles
- **Everything numeric is tabular**: the order ladder, option chain, and price columns must align to the decimal — this is the defining typographic requirement of a trading terminal.
- **Last price is colored, not just bold**: the big quote-screen price takes the up/down color, not a neutral text color — instant directional read.
- **Weight signals data importance**: prices and the ladder are 700–800; names/labels 400–600.
- **Dense but legible**: line heights are tight (1.1–1.3) to fit more data; never so tight numbers collide.
- **Dynamic Type with limits**: last price, titles, body scale modestly; the ladder, option chain, timeframe chips, and tab labels stay FIXED — these are tabular, layout-critical grids.

## 4. Component Stylings

### Buttons

**Buy Button (green) / Sell Button (red)** — the signature action pair
- Shape: Rounded rectangle, 6pt corner radius
- Buy: background `#00C076`, text `#03150D` (near-black-green), Inter 15pt weight 800
- Sell: background `#FA5252`, text `#1B0606`, Inter 15pt weight 800
- Layout: a docked horizontal pair on the quote screen, equal width, 10pt gap
- Padding: 14pt vertical; full available width split 50/50
- Pressed: darken 8% + scale 0.98

**Primary Button (Trade / Confirm order)**
- Background: `#1B9EFB` (or the blue→cyan gradient for hero CTAs)
- Text: `#04121C`, Inter 15pt weight 800
- Corner radius: 6pt; padding 13pt × 26pt
- Pressed: `#1684D6` + scale 0.98

**Outline Button (Add to list / Secondary)**
- Background: transparent
- Border: 1pt `#1B9EFB`
- Text: `#1B9EFB`, Inter 14pt weight 700
- Corner radius: 6pt
- Pressed: 10% blue tint fill

**Ghost Button (Indicators / Settings)**
- Background: `#1C2127`
- Border: 1pt `#232931`
- Text: primary text, Inter 13pt weight 600
- Corner radius: 6pt

### Core Atoms

**Watchlist Row**
- Height: 56pt
- Left: ticker (14pt w600) over name (11pt w400 secondary, truncates)
- Center: 56×28pt sparkline — stroke `#00C076` if up on day, `#FA5252` if down
- Right (right-aligned): last price (15pt w700 tabular) over a % chip
- % chip: solid pill, 4pt radius, `#00C076` bg + `#03150D` text (up) / `#FA5252` bg + `#1B0606` text (down), 11pt w800 tabular
- Divider: 0.5pt hairline below

**Quote Header**
- Symbol (20pt w800) + exchange/name (12pt secondary); optional cyan `PAPER` badge top-right
- Last price: 32pt w800 tabular, colored up/down
- Delta line: `+7.32 (+3.04%) ▲` — 14pt w700 tabular, up/down colored

**Candlestick Chart** (the centerpiece)
- Full-width, ~158pt tall on the quote screen (expands to full-screen landscape)
- Up candle: body + wick `#00C076`; down candle: `#FA5252`
- Grid lines: `#1A1F26` (nearly invisible) horizontal rules only
- Previous-close: 1pt dashed `#1B9EFB` reference line
- Crosshair on touch: vertical+horizontal `#8B95A1` lines + OHLC tooltip
- Timeframe strip below: 1m/5m/1D/5D/1M/1Y chips — 11pt w700; active = `#1B9EFB` text on `#1B9EFB`@16% fill, 4pt radius

**Order-Book Ladder (Level 2)**
- Rows: price (left, 64pt, w700 tabular — bid `#00C076` / ask `#FA5252`) + size (secondary, tabular)
- Depth bar: behind each row, right-anchored, width ∝ size, `#00C07624` (bid) / `#FA525224` (ask)
- Mid/spread divider: centered last price + spread, 13pt w800, bordered top & bottom 0.5pt
- Asks stacked above the mid; bids below (book convention)

**Options Chain**
- Three columns: Call (left, `#00C076` w700) | Strike (center, primary w800) | Put (right, `#FA5252` w700)
- Uppercase 11pt column header, 0.5pt divider; rows 0.5pt-separated, tabular
- ATM strike row subtly highlighted (`#1C2127` bg)

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: canvas with `saturate(180%) blur(20px)` translucency, 0.5pt top divider
- Tabs (5): Markets, Quotes, Trade, Portfolio, Menu
- Icon size: 22pt
- Active: `#1B9EFB`, filled glyph
- Inactive: tertiary text, stroked glyph
- Labels: Inter 10pt w600, always shown

**Top Nav (Quote screen)**
- Height: 44pt + safe area
- Leading: back chevron + symbol (compact); Trailing: search, add-to-watchlist star, ⋯ overflow (alerts, share, settings)
- The candlestick chart begins immediately below — no large hero block (data-first)

**Segmented / Tabs (Chart / Options / News / Financials on a symbol)**
- Underline style: active segment label primary with a 2pt `#1B9EFB` underline; inactive secondary
- Height: 40pt

### Input Fields

**Search Bar (Find symbol)**
- Height: 38pt
- Background: `#1C2127`, 6pt corner radius
- Leading magnifier 16pt secondary; placeholder "Search symbol / company" 13pt secondary
- Focus: 1pt `#1B9EFB` border

**Order Entry Field (Quantity / Limit price)**
- Background: `#1C2127`, 6pt radius, 1pt `#232931` border
- Value right-aligned 16pt w800 tabular; ± stepper buttons on either side (44pt hit)
- Focus: border `#1B9EFB`; error: border `#FA5252`, helper text red 12pt

### Distinctive Components

**Order Ticket (Buy/Sell flow)**
- Opens from the docked Buy/Sell pair: Action (Buy/Sell, inherits the green/red color), Order type (Market/Limit/Stop/Stop-Limit), Quantity stepper, Limit price, Time-in-force, estimated cost; a colored confirm button (green for buy, red for sell) and a quick-confirm review
- Order-type selector: segmented chips, active = `#1B9EFB`@16% fill

**Paper-Trading Badge**
- The signature "practice money" indicator: cyan `#20D5C4` pill, `#04121C` text, 10pt w800, 4pt radius, "PAPER" — appears on the quote header and portfolio when in paper mode

**Depth/Heatmap Bar**
- Behind ladder rows; some screens show a market-depth area chart (cumulative bid/ask) using the up/down fills

**Mini Sparkline**
- 56×28pt inline trend in watchlist rows; 2pt stroke, up/down colored, no fill

**Indicator Chips Row**
- On the chart: MA / EMA / BOLL / MACD / RSI toggle chips — ghost when off, `#1B9EFB`@16% when on

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 6, 8, 10, 12, 16, 20, 24, 32, 40
- Watchlist row height: 56pt; ladder row ~26pt (dense)
- Section header top margin: 12pt
- Screen content inset: 18pt horizontal (chart is full-bleed; ladder 18pt)

### Grid & Container
- iPhone: 18pt horizontal insets; the candlestick chart is full-bleed edge-to-edge
- iPad: multi-pane terminal — watchlist rail + chart + ladder/options simultaneously
- Tables (ladder, option chain) are fixed-column grids; numbers right/decimal-aligned
- Landscape: the chart goes full-screen with crosshair scrubbing (a core trader gesture)

### Whitespace Philosophy
- **Density is the feature**: traders want more data per screen — rows are compact, chrome is minimal, the chart is huge
- **The chart dominates**: it gets the most pixels; everything else is efficient supporting data
- **Color does the wayfinding**: green/red and the blue gradient carry meaning so layout can stay tight and unlabeled

### Border Radius Scale
- Square (0pt): Chart area, table cells, full-bleed regions
- Tiny (2pt): Depth bars, micro tags
- Subtle (4pt): % chips, timeframe chips, paper badge, indicator chips
- Standard (6pt): Buttons, search bar, inputs, order-type chips
- Comfortable (10pt): Cards, panels, bottom sheets
- Large (16pt): Modal containers, full sheets
- Pill (500pt): Rare — some filter toggles
- Circle (50%): Avatars, circular icon buttons

## 6. Depth & Elevation

Webull is flat and dark — depth is functional only.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Chart, ladder, watchlist rows, panels |
| Panel (Level 1) | 1pt `#232931` border, optional `#14181D` fill | Cards, grouped sections |
| Floating (Level 2) | `rgba(0,0,0,0.5) 0 8px 24px` + 1pt `#232931` border | Crosshair tooltip, dropdowns, order-type popover |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -8px 32px` + 1pt top border | Order ticket sheet, indicator picker |
| Modal Scrim | `rgba(0,0,0,0.65)` | Dim behind sheets/dialogs |

**Shadow Philosophy**: on a near-black canvas shadows barely register, so elevation is communicated almost entirely by a 1pt `#232931` border and a slight surface lift, not by drop shadow. Floating elements (crosshair tooltip, order-type popover, ticket sheet) get a heavy dark shadow plus the border so they read above the dense data. The chart never has a shadow — it is the ground plane.

### Motion
- **Live price tick**: the last price flashes its up/down color brightly for ~250ms then settles to the steady up/down color; the ladder cell that changed flashes its row tint; digits do NOT roll
- **Chart timeframe switch**: candles re-layout with a 250ms ease-out; axis re-scales
- **Chart crosshair scrub**: 1:1 finger tracking, no easing; OHLC tooltip follows instantly
- **Ladder updates**: rows update in place; size changes briefly flash the depth bar opacity
- **Buy/Sell → ticket**: ticket sheet slides up 280ms ease-out; the sheet's confirm button inherits green/red
- **Tab switch**: instant content swap; selected icon fills and tints with a 120ms cross-fade
- **Landscape chart**: rotate-to-fullscreen transitions in 300ms; crosshair becomes primary interaction
- **Pull-to-refresh** (watchlist/markets): blue→cyan gradient spinner; light haptic on trigger
- **Haptics**: light impact on timeframe change & tab change; selection tick while scrubbing key levels; medium impact on order submit; success notification on fill

## 7. Do's and Don'ts

### Do
- Treat dark as the only canonical mode — near-black `#0B0E11` to maximize candle/contrast
- Use the blue→cyan gradient (`#1B9EFB → #20D5C4`) for brand, the Trade action, selected timeframes, and the paper badge
- Make performance color loud and pervasive — up `#00C076`, down `#FA5252` on candles, sparklines, chips, ladder, buttons
- Make the candlestick chart the visual centerpiece — full-width, timeframe strip, dashed prev-close line
- Use a split Buy (green) / Sell (red) docked pair on the quote screen
- Set every numeric value tabular so the ladder and option chain align to the decimal
- Color the big last price up/down (not neutral) for an instant directional read
- Keep chart grid lines nearly invisible (`#1A1F26`) so candles dominate
- Use a cyan `PAPER` badge to mark paper-trading mode
- Communicate elevation with a 1pt `#232931` border + surface lift, not drop shadow

### Don't
- Don't design a primary light mode — a trading terminal is dark-native
- Don't make performance color timid — Webull's identity is saturated green/red everywhere
- Don't use the blue→cyan gradient for gain/loss — it's brand/action only; green/red is market color
- Don't use proportional figures for the ladder, chain, or prices — tabular is mandatory
- Don't render the last price in neutral text — it must carry the up/down color
- Don't put bright grid lines on the chart — they must recede behind candles
- Don't add marketing illustrations or photographic heroes — it's a terminal, data-first
- Don't roll/odometer the price digits on tick — only a brief color flash
- Don't separate Buy and Sell into distant CTAs — they are a docked, equal, color-coded pair
- Don't hardcode up=green globally — respect the region setting that may invert up/down color in Asia markets

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Chart ~140pt; ladder shows 3 levels each side; tighter 14pt insets |
| iPhone 15/16 | 393pt | Standard layout; chart ~158pt; 5 ladder levels |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Taller chart ~190pt; more ladder depth visible |
| iPhone (landscape) | — | Full-screen candlestick chart with crosshair — the core power gesture |
| iPad (portrait) | 768pt | Two-pane: watchlist rail + quote/chart |
| iPad (landscape) | 1024pt+ | Terminal layout: watchlist + chart + ladder + options simultaneously |

### Dynamic Type
- Last price, screen title, section header, body: modest scale
- Ladder, option chain, timeframe chips, tab labels, column headers: FIXED (tabular grids, layout-critical)
- Tabular figures remain tabular at every size

### Orientation
- iPhone: portrait for browse/trade; landscape = full-screen chart with crosshair + drawing tools
- iPad: split/terminal multi-pane; rotation preserves the selected symbol and chart timeframe

### Touch Targets
- Watchlist row: 56pt tall, full-row tap
- Tab bar icon: 22pt glyph, 56pt hit
- Timeframe chip: 28pt tall, 44pt hit
- Buy/Sell buttons: 48pt tall each
- Quantity stepper ±: 44pt hit
- Chart crosshair: full-chart drag region

### Safe Area Handling
- Top: top nav respects safe area and Dynamic Island; chart starts right below
- Bottom: docked Buy/Sell pair and tab bar respect home indicator; both add safe-area padding
- Keyboard: order-entry fields lift above the keyboard; the keypad sheet covers lower content
- Sides: 18pt content inset; chart is full-bleed

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark, canonical): `#0B0E11`
- Surface 1: `#14181D` · Surface 2: `#1C2127`
- Divider: `#232931` · Chart grid: `#1A1F26`
- Webull Blue: `#1B9EFB` · Webull Cyan: `#20D5C4` · Gradient: `#1B9EFB → #20D5C4`
- On-brand text: `#04121C`
- Text primary: `#EAEEF2` · secondary: `#8B95A1` · tertiary: `#5A636E`
- Up / bid / call: `#00C076` (on-text `#03150D`)
- Down / ask / put: `#FA5252` (on-text `#1B0606`)
- Up fill: `#00C07624` · Down fill: `#FA525224`
- Alert amber: `#F7A600` · Prev-close line: `#1B9EFB` dashed

### Example Component Prompts
- "Build the Webull quote header in SwiftUI on canvas `#0B0E11`: symbol 'TSLA' (20pt w800 `#F5F8FA`) over 'Tesla, Inc. · NASDAQ' (12pt `#8B95A1`), a cyan `PAPER` badge top-right (`#20D5C4` bg, `#04121C` text, 10pt w800, 4pt radius). Last price `248.50` in Inter 32pt w800 `.monospacedDigit()` colored `#00C076` (up). Delta line `+7.32 (+3.04%) ▲` 14pt w700 tabular `#00C076`."

- "Render a Webull candlestick chart: full-width ~158pt tall. Up candles body+wick `#00C076`, down `#FA5252`. Horizontal grid lines `#1A1F26` only. A 1pt dashed `#1B9EFB` previous-close line. Below: timeframe chip strip 1m/5m/1D/5D/1M/1Y in Inter 11pt w700; active chip is `#1B9EFB` text on `#1B9EFB` at 16% opacity, 4pt radius. On touch, show a `#8B95A1` crosshair + OHLC tooltip tracking 1:1."

- "Create the Webull order-book ladder: ask rows above, bid rows below a centered mid divider. Each row: price left (64pt, 13pt w700 tabular — `#FA5252` for asks, `#00C076` for bids) + size right (12pt `#8B95A1` tabular). Behind each row a right-anchored depth bar, width ∝ size, `#FA525224` for asks / `#00C07624` for bids. Mid divider: '248.50 · Spread 0.08' 13pt w800 `#EAEEF2`, 0.5pt `#232931` top+bottom borders."

- "Build the Webull docked Buy/Sell pair: a horizontal row, equal width, 10pt gap, 18pt side insets. Buy = `#00C076` bg, text `#03150D` Inter 15pt w800, 6pt radius, 48pt tall. Sell = `#FA5252` bg, text `#1B0606`. Pressed: darken 8% + scale 0.98; medium haptic; tapping opens the order-ticket sheet whose confirm button inherits the same green/red."

- "Render a Webull watchlist row: 56pt tall on `#0B0E11`. Left = ticker 14pt w600 `#EAEEF2` over name 11pt `#8B95A1` (truncate). Center = 56×28pt sparkline, 2pt stroke `#00C076` if up else `#FA5252`. Right = last price 15pt w700 tabular over a solid % chip (4pt radius, `#00C076` bg `#03150D` text if up / `#FA5252` bg `#1B0606` text if down, 11pt w800 tabular). 0.5pt `#232931` divider below."

- "Render a Webull options chain: 3-column grid Call | Strike | Put. Uppercase 11pt w600 `#5A636E` header with a 0.5pt `#232931` underline. Rows 13pt tabular, 0.5pt-separated: Call left `#00C076` w700, Strike center `#EAEEF2` w800, Put right `#FA5252` w700. Highlight the ATM strike row with `#1C2127`."

### Iteration Guide
1. Dark is the only canonical mode — near-black `#0B0E11`; do not build a primary light theme
2. Brand is the blue→cyan gradient `#1B9EFB → #20D5C4` — Trade action, selected timeframes, paper badge; never for gain/loss
3. Performance color is loud and everywhere — up `#00C076`, down `#FA5252` on candles, sparklines, chips, ladder, buttons
4. The candlestick chart is the centerpiece — full-bleed, timeframe strip, dashed `#1B9EFB` prev-close, near-invisible `#1A1F26` grid
5. The last price is colored up/down (not neutral) and tabular — instant directional read
6. Every numeric is tabular — the ladder and option chain align to the decimal, non-negotiable
7. Buy (green) / Sell (red) is one docked equal-width pair on the quote screen
8. The order ladder uses right-anchored depth bars at ~14% opacity behind tabular rows
9. Elevation is a 1pt `#232931` border + surface lift on near-black — not drop shadow
10. Motion is a brief color flash on tick (no rolling digits), 250ms timeframe re-layout, 1:1 crosshair scrub
