# Design System Inspiration of Crypto.com (iOS)

## 1. Visual Theme & Atmosphere

Crypto.com's iOS app is a premium, dark, navy-tinted financial super-app. Where Binance feels like a bare trading terminal and Coinbase feels like a friendly consumer wallet, Crypto.com positions itself as *aspirational fintech*: a deep near-black canvas with a distinct cool-blue undertone (`#03060F` → `#0B1426`, never neutral gray), generous rounding, and a metallic Visa card that is treated as the hero object of the entire product. The atmosphere is "private bank meets crypto exchange" — luxe, calm, and money-confident. The app spans buy/sell, the Crypto.com Visa card, Earn, staking CRO, Pay, NFT, and DeFi, but the home screen always foregrounds two things: your **total balance** and your **Visa card**.

The brand uses two distinct blues. **Crypto.com Brand Navy** (`#002D74`, lightening to `#103F68`) is the deep institutional blue used in hero gradients, promotional banners, and the logo lockup — it signals "the brand." **Electric Accent Blue** (`#1199FA`) is the interactive color — primary CTAs, links, the active tab, selected states, the floating center "Trade" action. This navy/accent pairing is core to the identity: deep navy is decorative and brand-bearing; electric blue is functional and clickable. Market data is carried by semantic green (`#00C08B`) and red (`#F6485D`), which never invert.

The **Visa card** is the single most recognizable element. It renders as a metallic, weighty rectangle with a brushed-metal gradient that differs per tier — Midnight Blue (1% cashback), Ruby Steel (2%), Royal/Jade Green (3%), Icy White / Frosted Rose Gold (also 3%), and Obsidian (5%, the flagship). The card always shows a metal chip, a masked PAN in monospace, the cardholder name, the embossed tier name in a refined gold/metal color, and the italic VISA wordmark bottom-right. Reproductions must keep the card glossy, heavy, and gradient-driven — it is the brand's status symbol.

Typography is a clean geometric humanist sans for all UI (Crypto.com uses a custom-tuned grotesque close to **Manrope**), paired with a **monospace** (Roboto Mono) for every monetary figure — balances, prices, PAN, APR, cashback percentages — so columns and the card number align with tabular figures. Headlines are heavy (700–800) and tight; body is regular; numbers are mono semibold.

**Key Characteristics:**
- Deep navy-tinted dark canvas (`#03060F` → `#0B1426`) — cool blue undertone, not neutral gray
- Two blues: Brand Navy (`#002D74`/`#103F68`) for hero/brand; Electric Accent (`#1199FA`) for interaction
- The metallic Visa card is the hero object — tiered brushed-metal gradients, gold embossing, italic VISA
- Card tiers map to cashback: Midnight Blue 1% → Ruby Steel 2% → Jade Green 3% → Obsidian 5%
- Total-balance hero in monospace at the top of Home, with today's P&L
- Pill-shaped CTAs (fully rounded), generous 18–24pt card radii — luxe, not terminal-tight
- Quick-action circle row: Deposit / Send / Trade / Earn
- Watchlist / Prices list: coin icon + symbol + name + mono price + colored % change
- Earn / Stake CRO: APR rows, term selectors, allocation rings
- Bottom tab bar (5) with an emphasized floating-circle center "Trade" action
- Market semantics green `#00C08B` / red `#F6485D` — never inverted

## 2. Color Palette & Roles

### Brand (decorative / hero)
- **Brand Navy** (`#002D74`): Deep institutional blue — hero gradient base, logo lockup, promo banners.
- **Brand Navy 2** (`#103F68`): Lighter navy — gradient top, banner highlights, selected navy chips.
- **Navy Gradient**: `linear-gradient(135deg, #002D74 0%, #103F68 100%)` — the signature brand sweep.

### Primary (Interactive)
- **Accent Blue** (`#1199FA`): Primary CTA, links, active tab, floating Trade button, selected states, focus rings.
- **Accent Pressed** (`#0B7AD1`): Pressed state of accent buttons.
- **Accent Tint** (`#1199FA1F`): 12% accent wash behind selected rows / info chips.

### Market Semantics (never invert)
- **Up / Buy Green** (`#00C08B`): Positive change, Buy button, gains, success.
- **Down / Sell Red** (`#F6485D`): Negative change, Sell button, losses, error.

### Card Metals (tier-specific, theme-invariant)
- **Card Gold** (`#C8A24A`): Embossed tier name, chip, accents on dark card faces.
- **Midnight Blue**: `linear-gradient(135deg,#1A1A1E,#2A2A30)` — 1% tier.
- **Ruby Steel**: `linear-gradient(135deg,#5A1020,#8A1830)` — 2% tier.
- **Jade Green**: `linear-gradient(135deg,#0E3A30,#14564A)` — 3% tier.
- **Obsidian**: `linear-gradient(135deg,#0A0A0C,#26262C)` — 5% flagship tier.

### Canvas & Surfaces (Dark — default)
- **Canvas** (`#03060F`): Primary background — near-black with a cool blue cast.
- **Surface 1** (`#0B1426`): Cards, sheets, list containers.
- **Surface 2** (`#103F68`): Brand-navy elevated surfaces, promo cards (reuses Navy 2).
- **Surface 3** (`#15263F`): Pressed rows, chip backgrounds, input fields.
- **Divider** (`#1C3354`): 1pt hairlines.

### Canvas & Surfaces (Light — secondary theme)
- **Canvas Light** (`#FFFFFF`): Light background.
- **Surface 1 Light** (`#F4F7FB`): Cards, grouped containers.
- **Divider Light** (`#E3E9F2`): Hairline dividers.

### Text
- **Text Primary Dark** (`#F4F7FB`): Primary text, prices, headings.
- **Text Secondary Dark** (`#8DA0BD`): Labels, metadata, secondary values (cool blue-gray).
- **Text Tertiary Dark** (`#5B6E8C`): Disabled, very low-emphasis.
- **Text Primary Light** (`#0A1F44`): Primary text on light (navy-black).
- **Text Secondary Light** (`#5C6B85`): Secondary text on light.

### Semantic (status)
- **Success** (`#00C08B`): Confirmed deposit/trade (reuses Up Green).
- **Error** (`#F6485D`): Failed action, validation (reuses Down Red).
- **Warning** (`#F5A623`): Risk warnings, KYC pending.
- **Info** (`#1199FA`): Informational toasts (reuses Accent).

### Color → Role Table

| Role | Dark | Light |
|------|------|-------|
| Canvas | `#03060F` | `#FFFFFF` |
| Surface 1 | `#0B1426` | `#F4F7FB` |
| Surface 3 | `#15263F` | `#EDF1F7` |
| Divider | `#1C3354` | `#E3E9F2` |
| Brand Navy | `#002D74` | `#002D74` |
| Accent (interactive) | `#1199FA` | `#1199FA` |
| Up / Buy | `#00C08B` | `#00C08B` |
| Down / Sell | `#F6485D` | `#F6485D` |
| Text primary | `#F4F7FB` | `#0A1F44` |
| Text secondary | `#8DA0BD` | `#5C6B85` |
| Link | `#1199FA` | `#1199FA` |

## 3. Typography Rules

### Font Family
- **UI Sans**: Crypto.com's custom grotesque — use **Manrope** (by Mikhail Sharanda, SIL OFL) as the open stand-in. Geometric humanist with a confident, slightly tight feel.
- **Numeric Mono**: **Roboto Mono** (by Christian Robertson, Apache 2.0) for all monetary figures, PAN, APR, %. Always tabular figures.
- **Fallback Stack (sans)**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Fallback Stack (mono)**: `'SF Mono', ui-monospace, Menlo, Consolas, monospace`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Sans | 32pt | 800 | 1.2 | -0.6pt | Large nav title (Prices, Earn) |
| Balance Hero | Mono | 30pt | 600 | 1.15 | -0.5pt | Total balance, tabular |
| Section Title | Sans | 22pt | 800 | 1.25 | -0.2pt | "Your Visa Card", "Watchlist" |
| Row Title | Sans | 18pt | 700 | 1.3 | -0.1pt | Coin name, detail heading |
| Body | Sans | 16pt | 400 | 1.5 | 0pt | Descriptions, explanatory copy |
| Price / Number | Mono | 15pt | 600 | 1.3 | 0pt | List prices, APR, % change |
| List Label | Sans | 15pt | 700 | 1.3 | 0pt | "CRO Visa Card", labels |
| Card PAN | Mono | 15pt | 500 | 1.3 | 2pt | Masked card number on Visa card |
| Card Tier | Sans | 13pt | 700 | 1.0 | 1.5pt | Embossed tier name, uppercased |
| Meta | Sans | 14pt | 400 | 1.4 | 0pt | Term, payout cadence, hints |
| Pill / Chip | Sans | 13pt | 600 | 1.0 | 0pt | Filter chips, term chips |
| Caption | Sans | 12pt | 600 | 1.35 | 0.1pt | "Available", footnotes |
| Tab Label | Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | Sans | 15pt | 700 | 1.0 | 0pt | Primary / Buy / Sell |

### Principles
- **Sans for words, mono for money**: Balances, prices, %, APR, cashback, the card PAN — all monospace tabular. Words/labels are Manrope.
- **Heavy headlines**: Titles and section heads at 700–800; the brand voice is confident, not delicate.
- **Tabular figures always on numbers**: Prices align down the watchlist; the card number stays evenly spaced.
- **Color encodes sign, not weight**: A +/- value's color (green/red) carries meaning; weight stays 600.
- **Tight tracking on display sizes**: Screen titles and balance get slight negative letter-spacing for a modern, premium feel.
- **Dynamic Type**: Titles, body, labels scale; card text (PAN/tier), numeric mono columns, and tab labels stay FIXED (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Buy Crypto / Confirm / Continue)**
- Shape: Fully rounded pill (radius 500pt)
- Background: `#1199FA` (Accent Blue)
- Text: `#FFFFFF`
- Padding: 13pt vertical, 28pt horizontal; full-width on forms (height 52pt)
- Font: Sans 15pt weight 700
- Pressed: background `#0B7AD1` + scale 0.98
- Disabled: background `#1199FA` at 40% opacity, text white at 60%

**Buy Button**
- Background: `#00C08B`; Text `#FFFFFF`; pill; same metrics as primary
- Pressed: darken ~12%

**Sell Button**
- Background: `#F6485D`; Text `#FFFFFF`; pill
- Pressed: darken ~12%

**Ghost / Secondary Button (Stake CRO / Learn more)**
- Background: transparent; Text `#1199FA`
- Border: 1.5pt `#1199FA`
- Corner radius: pill; Padding: 11pt / 22pt
- Font: Sans 14pt weight 700
- Pressed: background `#1199FA1F`

**Text Button (See all / View)**
- Background: transparent; Text `#1199FA`; Font Sans 14pt weight 700; no underline
- Pressed: text `#0B7AD1`

**Quick-Action Circle**
- 46pt circle, `#0B1426` bg, `#1199FA` 20pt glyph, label Sans 11pt w600 `#8DA0BD` beneath

### Core Atoms

**Coin Icon**
- 34pt circle (30pt dense), brand-colored token glyph
- BTC `#F7931A`, ETH `#627EEA`, CRO `#1199FA`, SOL `#14F195`

**Percentage Change**
- Mono 12–13pt w600, colored `#00C08B` (up) / `#F6485D` (down); no fill in lists (text only); pill-filled variant on price detail headers

**APR Badge**
- Mono 15pt w600 `#00C08B`, e.g. `8.00% APR`; or a pill `#00C08B1F` bg / `#00C08B` text

**Visa Card** (signature — see Distinctive Components)

**Term Chips (Earn)**
- Pill, `#15263F` bg, `#8DA0BD` 13pt w600; selected `#1199FA1F` bg / `#1199FA` text — e.g. Flexible / 1M / 3M

**Tag / Badge**
- "NEW", "Staking", promo badges — pill, `#15263F` bg, `#8DA0BD` 10pt w700; reward badges use gold `#C8A24A`

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#03060F` at 96% with 1pt top border `#1C3354`
- Tabs (5): Home, Prices, **Trade (floating center)**, Earn, Card
- Center "Trade": a 44pt `#1199FA` filled circle lifted -6pt above the bar with a white swap glyph — the signature focal action
- Side icon size: 20pt
- Active: `#1199FA` (filled icon + accent label)
- Inactive: `#5B6E8C` (outline icon + tertiary label)
- Labels: Sans 10pt w600, always shown (none under the center button)

**Top Nav (detail screens)**
- Height: 44pt + safe area
- Leading: back chevron `#F4F7FB` 22pt
- Center: title Sans 18pt w700
- Trailing: search / share / ⋯ in `#F4F7FB`

**Segmented Control (Spot/Margin, Buy/Sell)**
- Pill track `#15263F`; selected thumb `#1199FA` (or `#00C08B`/`#F6485D` for Buy/Sell) with white text

### Input Fields

**Search Bar**
- Height: 40pt; Background: `#15263F`; Border: none; Corner radius: 12pt
- Leading `magnifyingglass` 16pt `#8DA0BD`
- Placeholder: "Search coins" Sans 14pt `#8DA0BD`
- Focus: 1.5pt `#1199FA` border

**Amount Input (Buy/Sell/Send)**
- Large centered Mono value (28–34pt) `#F4F7FB`, currency suffix `#8DA0BD`
- Background often transparent on a dedicated amount screen; field variant: 52pt `#15263F`, 12pt radius
- Trailing "MAX" text button in `#1199FA`
- Error: helper text `#F6485D` 12pt + 1.5pt `#F6485D` border

**Stepper (term/quantity)**
- `−` / `+` circular `#15263F` buttons flanking a centered mono value

### Distinctive Components

**Metallic Visa Card** (the hero)
- Aspect ratio ~1.586:1; corner radius 18pt; height ~168–200pt on phone
- Background: tier-specific brushed-metal gradient (Midnight/Ruby/Jade/Obsidian — see Card Metals)
- Subtle gloss overlay: a faint 8% white diagonal sheen top-left
- Elements: embossed tier name top-left in `#C8A24A` Sans 13pt w700 +1.5px tracking uppercased; Crypto.com mark top-right (white); metal chip ~36×26pt rounded-5pt with gold gradient; masked PAN `5482 •••• •••• 7310` in Roboto Mono 15pt w500 +2px tracking bottom-left; cardholder name uppercased Sans 12pt w600 75% white; italic bold `VISA` wordmark bottom-right white
- Shadow: `rgba(0,0,0,0.8) 0 14px 30px -14px` — the card "floats" with weight
- Stacked-cards carousel when multiple cards; tap flips to reveal CVV/details

**Total Balance Hero**
- Label "Total Balance (USD)" Sans 12pt `#8DA0BD`
- Value Roboto Mono 30pt w600 `#F4F7FB`, e.g. `$ 18,402.65`
- Today P&L Mono 12pt colored green/red: `+$320.18 (+1.77%) Today`
- Optional eye toggle to hide

**Quick-Action Row**
- 4 evenly-spaced circle actions (Deposit / Send / Trade / Earn), 46pt circles `#0B1426`, accent glyphs

**Watchlist / Price Row**
- ~56pt; 20pt horizontal / 11pt vertical padding
- Layout: [coin icon 34pt] [symbol + full name stacked] [price + % stacked, right]
- Symbol Sans 15pt w700 `#F4F7FB`; name Sans 12pt `#8DA0BD`
- Price Mono 15pt w600 `#F4F7FB`; % Mono 12pt w600 colored
- Optional sparkline (40×24) in row, colored to trend

**Earn / Allocation Row**
- [coin icon 30pt] [name + term/cadence] ··· [APR Mono 15pt w600 `#00C08B`]
- Allocation ring: circular progress in accent/navy on `#15263F` track

**Card Tier Selector**
- Horizontal carousel of metal-gradient tier tiles; each shows tier name + cashback %; CRO-stake requirement under the flagship tiers

**Promo / Brand Banner**
- Full-width rounded (18pt) card with the Navy gradient (`#002D74 → #103F68`), white heading + accent CTA pill — the only place deep brand navy fills a large surface

**Spot Trade Card**
- Buy/Sell pill toggle, amount input, asset selector chip, fee line in `#8DA0BD`, full-width green/red submit pill

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40
- Screen horizontal inset: 20pt (slightly generous — premium feel)
- Card internal padding: 20pt; list row vertical padding: 11pt
- Section header to first row: 10pt; inter-section gap: 20pt

### Grid & Container
- iPhone: 20pt horizontal insets; the Visa card is inset 20pt (not full-bleed) so its rounded metal edges read
- Watchlist/Earn: single column, full-width rows separated by hairlines or surface cards
- iPad: two-pane — list/home left, detail/trade right; card carousel scales up

### Whitespace Philosophy
- **Premium breathing room**: 20pt insets and generous card padding signal "private bank," not "dense terminal"
- **The card gets air**: the Visa card sits in its own padded zone with shadow so it reads as a physical object
- **Hairlines + cards mixed**: watchlist uses hairlines; promotional/earn content uses rounded surface cards
- **One large navy moment**: deep brand navy only fills the promo banner; everywhere else navy is a tint or accent

### Border Radius Scale
- Square (0pt): full-bleed dividers, sparkline baselines
- Subtle (6pt): small chips, badges, metal chip on card
- Standard (10pt): inputs, small cards
- Card (14pt): list/content cards, sheets
- Hero Card (18pt): the Visa card, promo banners
- Comfortable (24pt): bottom sheets, modals
- Pill (500pt): all primary/secondary buttons, segmented controls, filter/term chips
- Circle (50%): coin icons, quick-action circles, the floating Trade button, avatars

## 6. Depth & Elevation

Crypto.com uses moderate elevation — more than Binance (the card and CTAs are meant to feel tactile and premium) but still restrained. Depth comes from navy-tinted surface steps plus a few deliberate shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; surface step only | List rows, hairline-separated content |
| Card (Level 1) | `rgba(0,0,0,0.35) 0 6px 16px` | Content cards, promo banners |
| Hero Card (Level 2) | `rgba(0,0,0,0.8) 0 14px 30px -14px` | The metallic Visa card — heaviest, "floats" |
| Floating Action (Level 2) | `rgba(17,153,250,0.4) 0 6px 16px` | The center Trade circle (accent-tinted glow) |
| Sheet (Level 3) | `rgba(0,0,0,0.55) 0 -8px 28px` | Bottom sheets |
| Modal Overlay | `rgba(0,0,0,0.6)` scrim | Behind modals |

**Shadow Philosophy**: Shadows are reserved to make money-objects feel real — the Visa card carries the deepest shadow so it reads as a physical metal card; the floating Trade button gets an accent-tinted glow to draw the eye to the primary action. List content stays flat with surface steps. On the near-black canvas, floating panels also get a 1pt `#1C3354` border as a secondary elevation cue.

### Motion
- **Card flip**: tap the Visa card → 3D Y-axis flip (400ms ease-in-out) to reveal CVV/full PAN; soft haptic
- **Card carousel**: horizontal paged scroll with subtle scale (0.94 → 1.0) on the focused card
- **Balance reveal**: eye-toggle cross-dissolves the balance to `••••••` over 200ms
- **Price tick**: list price text color-pulses to green/red briefly (~150ms) on update — subtle, not a full background flash
- **Tab switch**: instant content swap; the floating Trade button does a 1.0→0.92→1.0 press-spring on tap
- **Sheet present**: bottom sheet slides up 320ms ease-out; scrim fades
- **Buy/Sell toggle**: thumb slides + color-morphs green↔red 200ms ease-out
- **Pull-to-refresh**: accent circular spinner; haptic on trigger
- **Confirmation**: a green check-circle scales in with a success haptic on completed trade/deposit
- **Haptics**: soft impact on card flip, selection tick on term/segment change, success notification on completed transaction

## 7. Do's and Don'ts

### Do
- Use the navy-tinted canvas `#03060F` / `#0B1426` — cool blue undertone, never neutral gray
- Treat the metallic Visa card as the hero — tier gradients, gold embossing, italic VISA, heavy shadow
- Keep two blues distinct: Brand Navy `#002D74`/`#103F68` for hero/banners, Accent `#1199FA` for interaction
- Use fully-rounded pill buttons and 18–24pt card radii — premium, not terminal-tight
- Set all money (balance, price, APR, %, PAN) in Roboto Mono with tabular figures
- Keep green `#00C08B` = up/buy and red `#F6485D` = down/sell — never inverted
- Give the center Trade tab a floating accent circle with an accent-tinted glow
- Map card tiers to cashback (Midnight 1% → Ruby 2% → Jade 3% → Obsidian 5%)
- Show the total-balance hero with today's P&L at the top of Home
- Fill large navy surfaces only for the promo/brand banner

### Don't
- Don't use a neutral gray dark theme — the canvas must carry a cool navy cast
- Don't render the Visa card flat or with a single solid color — it must be a brushed-metal gradient with gloss and shadow
- Don't use Brand Navy `#002D74` for clickable CTAs (use Accent `#1199FA`); don't use Accent for the hero gradient
- Don't use sharp/small radii on cards or buttons — buttons are pills, cards are 14–18pt
- Don't render prices/APR in a proportional sans — alignment and the card number break
- Don't invert market colors in light mode (green stays up, red stays down)
- Don't omit the floating center Trade action — it's a navigation signature
- Don't put the embossed tier name in plain white — it's a gold/metal color (`#C8A24A`)
- Don't fill the whole screen with deep navy — navy is decorative, used in banners/gradients only
- Don't bold gains while leaving losses regular — color carries the sign
- Don't drop the heavy shadow on the card — its physical weight is the brand

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card height ~150pt; quick actions may wrap to 4-up tight |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Card height ~200pt; larger balance hero |
| iPad (portrait) | 768pt | Two-pane: home/list left + detail right; card carousel centered, larger |
| iPad (landscape) | 1024pt+ | Three-pane: nav + list + detail/trade |

### Dynamic Type
- Screen titles, section heads, body, labels: scale with Dynamic Type
- Card text (tier name, PAN), numeric mono columns (prices, APR, balance), tab labels: FIXED — scaling breaks the card layout and tabular alignment
- The balance hero scales one step then truncates with ellipsis if extreme

### Orientation
- Portrait default everywhere
- Card detail / chart screens support landscape (wider chart)
- Home and Earn lock portrait

### Touch Targets
- Watchlist row: full-row tap, 56pt
- Tab icons: 20pt glyph, 44pt+ hit; floating Trade circle: 44pt
- Quick-action circles: 46pt
- Pill buttons: ≥48pt tall
- Card tap (flip): full card surface

### Safe Area Handling
- Top: nav respects safe area + Dynamic Island
- Bottom: tab bar + home indicator respected; the floating Trade circle stays above the home indicator
- Keyboard: amount inputs scroll above keyboard; the MAX button and confirm pill remain visible

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#03060F`; Surface 1: `#0B1426`; Surface 3: `#15263F`; Divider: `#1C3354`
- Brand Navy: `#002D74` → `#103F68` (hero gradient)
- Accent (interactive): `#1199FA` (pressed `#0B7AD1`)
- Up / Buy: `#00C08B`; Down / Sell: `#F6485D`
- Card gold: `#C8A24A`
- Text primary: `#F4F7FB` / secondary `#8DA0BD` / tertiary `#5B6E8C`
- Link: `#1199FA`
- Canvas (light): `#FFFFFF`; text primary light `#0A1F44`

### Example Component Prompts
- "Build the Crypto.com metallic Visa card in SwiftUI: a `RoundedRectangle(cornerRadius: 18)` with a `LinearGradient([#0A0A0C, #1A1A1E, #26262C], topLeading→bottomTrailing)` (Obsidian tier). Overlay a faint 8% white diagonal sheen. Top-left: 'OBSIDIAN' in Manrope 13pt w700, tracking 1.5, color `#C8A24A`. Top-right: white Crypto.com mark 22pt. A 36×26 rounded-5 gold-gradient chip. Bottom-left: masked PAN `5482 •••• •••• 7310` in Roboto Mono 15pt w500 tracking 2; cardholder name uppercased Manrope 12pt w600 at 75% white below. Bottom-right: italic bold `VISA` white. Apply shadow `color: .black.opacity(0.8), radius: 30, x: 0, y: 14`. Aspect ratio 1.586."

- "Create the Crypto.com total-balance hero: label 'Total Balance (USD)' in Manrope 12pt `#8DA0BD`. Below: `$ 18,402.65` in Roboto Mono 30pt w600 `#F4F7FB` with `.monospacedDigit()`. Below: `+$320.18 (+1.77%) Today` in Roboto Mono 12pt colored `#00C08B`. Add a trailing eye toggle that cross-dissolves the value to `••••••` over 200ms."

- "Build the Crypto.com watchlist row: 56pt tall, 20pt horizontal / 11pt vertical insets. Leading 34pt circular coin icon. A VStack: symbol `BTC` in Manrope 15pt w700 `#F4F7FB` and `Bitcoin` in Manrope 12pt `#8DA0BD`. Trailing VStack right-aligned: price `$67,284.10` in Roboto Mono 15pt w600 `#F4F7FB` and `+2.34%` in Roboto Mono 12pt w600 `#00C08B` (red `#F6485D` if negative)."

- "Render the Crypto.com bottom tab bar: 5 items on a `#03060F`@96% bar, 1pt `#1C3354` top border. Side tabs Home/Prices/Earn/Card: 20pt icon, label Manrope 10pt w600; active `#1199FA` (filled icon), inactive `#5B6E8C`. The 3rd slot is a floating 44pt `#1199FA` circle lifted -6pt with a white swap glyph and an accent-tinted glow shadow `color: #1199FA.opacity(0.4), radius: 16, y: 6` — no label."

- "Build the Crypto.com primary CTA: a fully-rounded pill (`Capsule()`), `#1199FA` background, white Manrope 15pt w700 text, 13pt vertical / 28pt horizontal padding, full-width 52pt on forms. Pressed: `#0B7AD1` + scale 0.98."

- "Create the Crypto.com Earn row: leading 30pt coin icon, name `Cronos` Manrope 14pt w700 `#F4F7FB` with `3-month term` Manrope 11pt `#8DA0BD` below, trailing `8.00% APR` in Roboto Mono 15pt w600 `#00C08B`. Add a term-chip row above (Flexible / 1M / 3M) as pills `#15263F` → selected `#1199FA1F`/`#1199FA`."

### Iteration Guide
1. Canvas is navy-tinted dark `#03060F`/`#0B1426` — cool blue cast, never neutral gray
2. Two blues: Brand Navy `#002D74`/`#103F68` is decorative (hero/banner); Accent `#1199FA` is interactive
3. The metallic Visa card is the hero — tier gradient + gloss + gold embossing + heavy shadow + italic VISA
4. Card tiers map to cashback: Midnight 1% → Ruby 2% → Jade 3% → Obsidian 5%
5. All money (balance, price, APR, %, PAN) in Roboto Mono with tabular figures
6. Buttons are full pills; cards are 14–18pt; the look is premium, not terminal-tight
7. Up/buy `#00C08B`, down/sell `#F6485D`, never inverted across themes
8. The center Trade tab is a floating `#1199FA` circle with an accent-tinted glow
9. Total-balance hero + today's P&L sits at the top of Home
10. Deep navy fills only the promo/brand banner; elsewhere it's a tint or accent — light theme keeps accent/navy/card metals unchanged
