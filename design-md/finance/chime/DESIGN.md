# Design System Inspiration of Chime (iOS)

## 1. Visual Theme & Atmosphere

Chime's iOS app is a **calm, confidence-building money dashboard** built around one number: how much you can spend right now. The product's emotional job is to make banking feel friendly and stress-free for people who've been burned by overdraft fees and minimum balances, so the design is soft, rounded, and reassuring rather than corporate or austere. The light canvas is a near-white off-white (`#F6FBF8`); the signature surface is the **balance hero card** — a full-bleed Chime Mint (`#1EC677` → `#12A862`) gradient panel with the available balance rendered huge in near-black mint-tinted ink, the cents set smaller as a superscript, and two soft action buttons ("Move money", "Pay anyone") tucked inside. Mint is not an accent here — it is the brand's entire personality, and the balance card wears it head to toe.

The signature screen is the **account home**. It opens with a friendly greeting ("Hi, Jordan"), then the mint balance hero, then the **SpotMe banner** — Chime's fee-free overdraft feature shown as a card with a star/shield glyph, a status line, and the available SpotMe limit in bright mint. Below that sits "Recent activity": a clean list of transactions, each a rounded merchant tile (gradient or mint-soft), a merchant name, a meta line ("Today · Card ····4821"), and an amount where **incoming money is mint-positive and outgoing is neutral ink** (never alarming red — Chime deliberately avoids making everyday spending feel negative). Early direct deposit ("2 days early") is celebrated inline. This screen — balance, SpotMe, activity — is the heart of the product.

The other Chime-defining moment is the **instant transaction alert**: the moment a card swipes or a deposit lands, Chime fires a push and an in-app toast — a rounded card with a circular mint check icon, a title ("Direct deposit received"), a sub-line, and the amount in positive mint. This real-time feedback loop ("we told you the second it happened") is core to Chime's trust model. The second fixture is the **status chip** — Posted / Pending / SpotMe covered / Declined — small pills in mint, amber, bright-mint, and coral that annotate transaction state.

Typography is a clean, friendly geometric sans — production Chime uses a custom/brand-adjacent face; **DM Sans** is the faithful free stand-in (rounded terminals, optical-size aware, approachable). Hierarchy is finance-dashboard: the balance hero is enormous (44pt 700), screen titles 32pt, section headers 22pt, card titles 18pt, body 16pt, row titles 15pt, meta 14pt, captions 12pt. Numbers are always 700 — the balance and every transaction amount are the data the user came for. The tone is **big, soft, legible, and calm**.

**Key Characteristics:**
- Soft off-white canvas (`#F6FBF8`) light / deep green-charcoal (`#0E1411`) dark — calm, never austere
- **Chime Mint** (`#1EC677` / `#00D67E`) is the entire brand personality, not an accent
- The **balance hero** is a full mint-gradient card with a giant available balance + superscript cents
- **SpotMe** banner — fee-free overdraft shown with a glyph, status, and the available limit in bright mint
- **Instant transaction alerts** — real-time push + in-app toast with a mint check icon the moment money moves
- Incoming money is **mint-positive**; everyday spending is neutral ink, never alarming red
- Status chips: Posted / Pending / SpotMe covered / Declined (mint / amber / bright-mint / coral)
- Early direct deposit ("2 days early") celebrated inline — a core trust signal
- Heavily rounded geometry (24pt hero, 14pt buttons, 14pt tiles, pill chips) — friendly, soft
- DM Sans typography — geometric, rounded, approachable; numbers always 700
- Bottom tab bar: Home, Move, Card, Grow, Me — active in bright mint

## 2. Color Palette & Roles

### Primary (Interactive)
- **Chime Mint** (`#1EC677`): Primary brand + interactive color — balance hero gradient base, primary buttons, logomark.
- **Mint Bright** (`#00D67E`): Brighter mint — active tab, links, "See all", high-energy accents.
- **Mint Pressed** (`#16A862`): Pressed state on mint buttons.
- **Mint Soft Fill** (`#E4F7EE` light / `#123A2A` dark): Tinted backgrounds for soft buttons, transaction tiles, SpotMe icon chips.
- **SpotMe Mint** (`#2EE6A6`): The brighter signal mint reserved for SpotMe and positive-money moments on dark.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#F6FBF8`): Primary light canvas — a barely-green off-white, not pure white.
- **Surface 1** (`#FFFFFF`): Cards, list containers, SpotMe banner, sheets.
- **Surface 2** (`#F0F6F2`): Pressed rows, secondary fills.
- **Divider** (`#E4EDE8`): Hairline between transaction rows.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0E1411`): Primary dark canvas — a deep green-charcoal, not neutral black.
- **Dark Surface 1** (`#16201B`): Cards, banners, list containers.
- **Dark Surface 2** (`#1F2C25`): Toasts, pressed rows, elevated surfaces.
- **Dark Divider** (`#243029`): Hairline dividers.

### Text
- **Text Primary Light** (`#0F1A14`): Primary body, transaction names.
- **Text Secondary Light** (`#5C6E63`): Metadata, meta lines, captions.
- **Text Tertiary Light** (`#90A399`): Disabled, low emphasis.
- **Balance Ink** (`#062014`): Near-black mint-tinted ink used on the mint hero for max contrast.
- **Text Primary Dark** (`#EAF2EC`): Primary body on dark.
- **Text Secondary Dark** (`#9DB0A4`): Secondary on dark.
- **Text Tertiary Dark** (`#6B7E72`): Disabled on dark.

### Semantic

| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| Positive (money in) | `#0FAE63` | `#00D67E` | Incoming transfers, deposits, "+$" amounts |
| SpotMe / Covered | `#12B981` | `#2EE6A6` | SpotMe limit, "SpotMe covered" chip |
| Negative / Declined | `#E5484D` | `#FF6B6B` | Declined transactions, negative balance, errors |
| Pending / Warning | `#E08600` | `#FFB23E` | "Pending" chip, holds, attention states |
| Posted / Success | `#0FAE63` | `#00D67E` | "Posted" chip, success toast |

**Spending is neutral, not negative.** Outgoing everyday card transactions render in **Text Primary ink** (`-$38.42`), NOT red. Red (`#E5484D` / `#FF6B6B`) is reserved for *declined*, *failed*, or *negative-balance* states — Chime intentionally does not make routine spending feel like a loss.

## 3. Typography Rules

### Font Family
- **Primary**: A clean geometric humanist sans. Production Chime uses a brand-tuned face; **`DM Sans`** (by Colophon for Google, SIL OFL) is the faithful free stand-in — rounded terminals, optical-size axis, friendly and highly legible at large display sizes.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numeric**: Use tabular/lining figures for balances and amounts so columns align (`font-variant-numeric: tabular-nums`).
- **Monospace**: not used in product UI.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance Hero | DM Sans | 44pt | 700 | 1.05 | -1.5pt | The available balance on the mint card; cents 26pt superscript |
| Screen Title | DM Sans | 32pt | 700 | 1.15 | -0.6pt | Large nav title ("Move money", "Card") |
| Section Header | DM Sans | 22pt | 700 | 1.2 | -0.3pt | "Recent activity", "SpotMe" |
| Card Title | DM Sans | 18pt | 700 | 1.3 | -0.1pt | SpotMe banner title, feature cards |
| Body | DM Sans | 16pt | 400 | 1.5 | 0pt | Descriptions, explanatory copy |
| Row Title | DM Sans | 15pt | 600 | 1.3 | 0pt | Transaction merchant name |
| Amount | DM Sans | 15pt | 700 | 1.2 | 0pt | Transaction amount (tabular) |
| Meta | DM Sans | 14pt | 400 | 1.4 | 0pt | "Today · Card ····4821" |
| Caption | DM Sans | 12pt | 600 | 1.4 | 0.1pt | "2 days early", chip text |
| Button | DM Sans | 16pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | DM Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Chip | DM Sans | 12pt | 700 | 1.0 | 0.1pt | Status chip text |
| Link | DM Sans | 13pt | 700 | 1.3 | 0pt | "See all", inline links |

### Principles
- **The balance is the loudest thing on screen**: 44pt 700 with tight -1.5pt tracking; cents shrink to a superscript so the dollars dominate.
- **Numbers are always 700**: every amount — balance, transactions, SpotMe limit — is bold, in tabular figures, so money is instantly scannable and aligned.
- **Friendly weights**: 400 body, 600 row titles, 700 headers/numbers/buttons. No thin, no black — nothing harsh.
- **Calm hierarchy**: large, soft, generously spaced; the eye goes balance → SpotMe → activity with no visual stress.
- **Dynamic Type first-class**: balance, titles, headers, body, row titles, meta scale; tab labels and chip text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Move money", "Pay anyone", "Continue")**
- Shape: Rounded rectangle, 14pt corner radius
- Background: `#1EC677` (Chime Mint) / `#1EC677` dark (identical)
- Text: `#062014` (near-black mint ink), DM Sans 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal; min height 52pt
- Pressed: background `#16A862` + scale 0.98
- Disabled: background `#1EC677` 40% / text `#062014` 50%

**Secondary / Outline Button ("Pay anyone", "Learn more")**
- Background: transparent
- Text: `#0FAE63` light / `#00D67E` dark, DM Sans 15pt weight 700
- Border: 1.5pt `#1EC677`
- Corner radius: 14pt
- Padding: 12pt vertical, 22pt horizontal
- Pressed: background `#E4F7EE` / `#123A2A`

**Soft Button ("Add to Spot", quick actions)**
- Background: `#E4F7EE` light / `#123A2A` dark (Mint Soft Fill)
- Text: `#0FAE63` light / `#00D67E` dark, DM Sans 14pt weight 700
- No border; corner radius 12pt
- Padding: 12pt vertical, 20pt horizontal

**Balance-Card Button (inside the mint hero — "Move money", "Pay anyone")**
- Background: `rgba(6,32,20,0.14)` (translucent dark over mint)
- Text: `#062014`, DM Sans 13pt weight 700, with a 15pt leading glyph
- Corner radius: 12pt; height 40pt; equal-width, 10pt gap

**Text Button ("See all activity")**
- Font: DM Sans 13-14pt weight 700
- Color: `#0FAE63` light / `#00D67E` dark
- No background, no border, no underline

### Balance Hero Card (the signature surface)

- Container: full-width minus 16pt insets, 24pt corner radius
- Background: linear gradient `#1EC677 → #12A862` (≈150°)
- Shadow: `rgba(30,198,119,0.45) 0 16px 32px -16px` (a mint glow, light) / `rgba(0,0,0,0.5)` (dark)
- Label: "Checking" + a small card glyph, DM Sans 13pt 600 `rgba(6,32,20,0.7)`
- Amount: DM Sans 44pt 700 `#062014`, tracking -1.5pt; cents 26pt superscript
- Sub-line: "Available to spend" DM Sans 13pt 600 `rgba(6,32,20,0.65)`
- Actions: two equal translucent-dark buttons, 20pt below the amount

### Transaction Row (core atom)

- Layout: 42pt rounded (14pt) merchant tile + name/meta column + right-aligned amount
- Tile: gradient (merchant) or `#E4F7EE`/`#123A2A` mint-soft (Chime actions: Pay Anyone, deposits); a glyph or the merchant initial in 700
- Name: DM Sans 15pt 600 `#0F1A14`
- Meta: DM Sans 14pt 400 `#5C6E63` — "Today · Card ····4821" / "Fri · 2 days early"
- Amount: DM Sans 15pt 700, tabular; **incoming = positive mint** (`#0FAE63`/`#00D67E`, `+$60.00`), **outgoing = neutral ink** (`#0F1A14`, `-$38.42`)
- Separator: 1pt `#E4EDE8` / `#243029` divider; no card border

### SpotMe Banner (distinctive component)

- Container: Surface 1 / Surface 1 dark, 1pt divider, 16pt corner radius, 14×16pt padding
- Icon chip: 38pt rounded (12pt) `#E4F7EE`/`#123A2A`, a star/shield glyph in `#12B981`/`#2EE6A6`
- Title: "SpotMe is on" DM Sans 14pt 700
- Sub: "Overdraft up to your limit, fee-free" DM Sans 12pt 400 `#5C6E63`
- Trailing: the available SpotMe limit, DM Sans 15pt 700 `#12B981`/`#2EE6A6`
- Tappable → SpotMe detail / limit-boost flow

### Instant Transaction Alert (distinctive component)

- Container: Surface 2 / Surface 2 dark, 1pt divider, 18pt corner radius, elevated shadow
- Icon: 44pt solid mint circle with a near-black check glyph
- Title: "Direct deposit received" DM Sans 14pt 700
- Sub: "Payroll · Available now · 2 days early" DM Sans 12pt 400 `#5C6E63`
- Trailing amount: DM Sans 16pt 700 positive mint
- Appears as a push notification AND an in-app toast the instant money moves

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: Surface 1 / Dark Canvas, blurred, 0.5pt top divider
- Tabs (5): Home, Move, Card, Grow, Me
- Icon size: 22pt; active `#00D67E` (icon fills solid); inactive `#90A399`/`#6B7E72`
- Labels: DM Sans 10pt weight 600, always shown
- No tint pill — just the mint color + filled icon swap

**Top App Header (home)**
- Greeting ("Hi, Jordan") DM Sans 18pt 700 leading
- Trailing: notifications bell + profile avatar, 22pt icons

**Top Nav (sub-screens)**
- Height: 44pt + safe area
- Leading back chevron; centered compact title; optional trailing action

### Input Fields

**Amount Entry (Move money / Pay anyone)**
- Huge centered amount, DM Sans 44pt 700, mint cursor
- A numeric keypad sheet below; "$" prefix in `#5C6E63`
- Below: the funding source row (account + balance)

**Text Field**
- Height: 52pt
- Background: Surface 2 / Dark Surface 2
- Corner radius: 14pt
- Label floats above on focus; focus ring 1.5pt `#1EC677`
- Placeholder DM Sans 16pt 400 `#90A399`

### Distinctive Components

**Balance Hero Card** — see above; the single most recognizable Chime surface.

**SpotMe Banner & SpotMe Coverage** — see above; the fee-free-overdraft brand pillar.

**Instant Transaction Alert** — see above; the real-time trust loop.

**Status Chip**
- Pill, 500pt radius, DM Sans 12pt 700, 7×14pt padding
- Posted: `#E4F7EE`/`#123A2A` bg, `#0FAE63`/`#00D67E` text
- Pending: amber-soft bg, `#E08600`/`#FFB23E` text
- SpotMe covered: `rgba(46,230,166,0.14)` bg, `#12B981`/`#2EE6A6` text
- Declined: red-soft bg, `#E5484D`/`#FF6B6B` text

**Early Direct Deposit Badge**
- Inline meta "2 days early" in `#0FAE63`/`#00D67E` 600, beside the deposit row — a celebrated trust signal

**Move Money / Pay Anyone Sheet**
- Bottom sheet, 20pt top corners, contact list + amount entry + mint primary CTA pinned bottom

**Grow / Savings Tile**
- Rounded card, mint progress ring or sparkline, APY in mint 700

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 16pt (cards) / 20pt (headers, section labels)
- Card internal padding: 22-24pt (hero), 14-16pt (banners, rows)
- Transaction row vertical padding: 12pt with a hairline divider

### Grid & Container
- iPhone: single column, cards at 16pt insets, 24pt hero radius
- iPad: content centered max 600pt; same card system, larger hero
- Transaction list: full-width rows within a 16pt-inset container

### Whitespace Philosophy
- **The hero owns the top third**: the balance card is big and breathes — generous internal padding, a soft mint glow shadow
- **Calm density below**: SpotMe and activity are clean rounded rows with comfortable 12pt padding; nothing is cramped
- **Rounded everything**: 24pt hero, 14-16pt cards, 14pt buttons, 14pt tiles, pill chips — the softness is the brand
- **No harsh lines**: dividers are faint hairlines; structure comes from rounded surfaces and spacing, not borders

### Border Radius Scale
- Square (0pt): full-bleed media (rare)
- Subtle (8pt): small inline chips, nested elements
- Standard (12pt): soft buttons, balance-card buttons, transaction tiles
- Comfortable (14pt): primary/secondary buttons, text fields
- Card (16pt): SpotMe banner, feature cards, list containers
- Hero (24pt): the balance hero card, large promo cards
- Pill (500pt): status chips, segmented toggles
- Circle (50%): avatars, alert icons, the logomark

## 6. Depth & Elevation

Chime uses soft, colored elevation — shadows are gentle and the hero glows mint:

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Transaction rows, list content (separated by hairlines) |
| Card (Level 1) | `rgba(15,26,20,0.06) 0 2px 12px` | SpotMe banner, feature cards |
| Hero Glow (Level 2) | `rgba(30,198,119,0.45) 0 16px 32px -16px` | The balance hero card (a mint glow, not a gray shadow) |
| Toast / Sheet (Level 3) | `rgba(15,26,20,0.16) 0 12px 28px -12px` | Instant transaction alert, bottom sheets |
| Modal Overlay | `rgba(6,16,12,0.45)` | Scrim behind sheets and modals |

**Shadow Philosophy**: Chime's signature elevation is the **mint glow** under the balance card — a tinted shadow that makes the hero feel like it's lit from within, reinforcing that the balance is the bright, positive center of the app. Everything else uses soft neutral shadows or none. On dark mode, the mint glow softens and neutral shadows nearly vanish, so rounded `#16201B` surfaces and `#243029` dividers carry structure.

### Motion
- **Balance count-up**: on home load / after a transfer, the balance animates from previous → new with a 600ms ease-out odometer roll
- **Instant alert toast**: slides down from the top with a 350ms spring (damping 0.8), a soft success haptic, auto-dismiss after 4s or swipe-up
- **Transaction row insert**: a new transaction fades + slides in at the top of activity over 250ms ease-out
- **SpotMe boost**: the limit number scales-bumps 1.0 → 1.15 → 1.0 with a medium haptic when increased
- **Move money flow**: amount keypad sheet slides up 300ms ease-out; on success, a full-screen mint check draws in (stroke animation, 500ms) + success haptic
- **Tab switch**: instant; active icon fills mint, no slide
- **Pull to refresh**: a mint spinner; balance re-counts if it changed
- **Haptic feedback**: soft success on incoming money / deposit alert, medium on SpotMe boost & transfer success, light on tab and chip taps

## 7. Do's and Don'ts

### Do
- Use soft off-white (`#F6FBF8`) light / deep green-charcoal (`#0E1411`) dark — calm, never austere
- Make the balance hero a full Chime Mint gradient card with a giant balance + superscript cents
- Treat Mint (`#1EC677` / `#00D67E`) as the brand's whole personality, applied generously
- Render incoming money in positive mint and everyday spending in **neutral ink** — never red
- Reserve red (`#E5484D` / `#FF6B6B`) for declined / failed / negative-balance only
- Fire an instant transaction alert (push + toast) the moment money moves, with a mint check icon
- Show SpotMe prominently with its available limit in bright mint
- Celebrate early direct deposit inline ("2 days early")
- Round everything heavily — 24pt hero, 14pt buttons, pill chips — softness is the brand
- Set amounts in tabular 700 figures so columns align
- Use the mint-glow shadow under the hero — it makes the balance feel lit from within

### Don't
- Don't color routine spending red — it makes everyday life feel like a loss; spending is neutral ink
- Don't use pure white as the light canvas — it's the soft green-tinted `#F6FBF8`
- Don't use neutral black for the dark canvas — it's the green-charcoal `#0E1411`
- Don't make the brand stingy with mint — Chime is mint head-to-toe, not "white with a green button"
- Don't use sharp corners or hard borders — the softness is the entire emotional pitch
- Don't bury the balance — it's the loudest element, 44pt 700, top third of the screen
- Don't render amounts in proportional figures — use tabular so columns align
- Don't make SpotMe subtle — the fee-free overdraft is a headline trust feature
- Don't use a Material tint pill on tabs — active is just mint color + filled icon
- Don't animate harshly — motion is soft springs and ease-outs, haptics gentle

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Balance amount 38pt; hero padding tightens to 18pt |
| iPhone 13/14/15 | 390pt | Standard layout; balance 44pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the top header |
| iPhone 15/16 Pro Max | 430pt | Balance 48pt; hero padding 26pt; larger tiles |
| iPad (portrait) | 768pt | Content centered max 600pt; hero scales, activity full-width within column |
| iPad (landscape) | 1024pt+ | Two-pane: home + detail; hero in the left column |

### Dynamic Type
- Balance, screen titles, section headers, card titles, body, row titles, meta: full scale
- Tab labels, chip text, the superscript cents ratio: FIXED (layout-sensitive)
- Amounts scale but stay tabular so columns keep aligning

### Orientation
- All screens rotate
- iPad landscape pins a master list beside a detail pane
- The balance hero re-centers and scales on rotation

### Touch Targets
- Tab bar icons: 22pt glyph, 49pt bar, 44pt hit
- Balance-card buttons: 40pt tall, full half-width hit
- Transaction row: full-row tappable, 66pt min height
- Primary buttons: 52pt min height
- Status chips (if interactive): 44pt hit

### Safe Area Handling
- Top: header respects the Dynamic Island; the instant-alert toast appears below the safe area
- Bottom: tab bar + home indicator respected; sheet CTAs sit above the home indicator
- Keyboard: amount keypad is a sheet; content scrolls above it
- Sides: 16pt card inset, 20pt header inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#F6FBF8`
- Canvas (dark): `#0E1411`
- Surface 1: `#FFFFFF` light / `#16201B` dark
- Surface 2: `#F0F6F2` light / `#1F2C25` dark
- Divider: `#E4EDE8` light / `#243029` dark
- Text primary: `#0F1A14` light / `#EAF2EC` dark
- Text secondary: `#5C6E63` light / `#9DB0A4` dark
- Chime Mint: `#1EC677`
- Mint Bright: `#00D67E`
- Mint Pressed: `#16A862`
- Mint Soft Fill: `#E4F7EE` light / `#123A2A` dark
- SpotMe Mint: `#12B981` light / `#2EE6A6` dark
- Balance ink (on mint): `#062014`
- Positive (money in): `#0FAE63` light / `#00D67E` dark
- Negative / declined: `#E5484D` light / `#FF6B6B` dark
- Pending / warning: `#E08600` light / `#FFB23E` dark

### Example Component Prompts
- "Build the Chime balance hero in SwiftUI: a full-width card minus 16pt insets, 24pt corner radius, a `#1EC677 → #12A862` 150° linear gradient, and a mint-glow shadow `rgba(30,198,119,0.45) 0 16px 32px -16px`. Inside: a 'Checking' label with a small card glyph (DM Sans 13pt 600 `rgba(6,32,20,0.7)`); the available balance '$2,847' DM Sans 44pt 700 `#062014` tracking -1.5pt with '.19' as a 26pt superscript; 'Available to spend' DM Sans 13pt 600 `rgba(6,32,20,0.65)`; and a 20pt-below row of two equal translucent-dark buttons ('Move money', 'Pay anyone') — `rgba(6,32,20,0.14)` bg, `#062014` DM Sans 13pt 700, 15pt leading glyph, 12pt radius, 40pt tall."

- "Create a Chime transaction row: a 42pt rounded (14pt) merchant tile (gradient for merchants, `#E4F7EE`/`#123A2A` mint-soft for Chime actions like Pay Anyone or deposits, with a glyph or initial in 700), a merchant name DM Sans 15pt 600 `#0F1A14`, a meta line 'Today · Card ····4821' DM Sans 14pt 400 `#5C6E63`, and a right-aligned amount DM Sans 15pt 700 tabular — POSITIVE money in mint `#0FAE63`/`#00D67E` ('+$60.00'), everyday spending in NEUTRAL ink `#0F1A14` ('-$38.42'), never red. Separate rows with a 1pt `#E4EDE8`/`#243029` hairline, no card border."

- "Render the Chime SpotMe banner: Surface-1 card, 1pt divider, 16pt radius, 14×16pt padding. Leading 38pt rounded (12pt) `#E4F7EE`/`#123A2A` chip with a star/shield glyph `#12B981`/`#2EE6A6`; a 'SpotMe is on' title DM Sans 14pt 700; a sub 'Overdraft up to your limit, fee-free' DM Sans 12pt 400 `#5C6E63`; and the trailing available SpotMe limit '$200' DM Sans 15pt 700 `#12B981`/`#2EE6A6`. Whole banner tappable."

- "Build the Chime instant transaction alert toast: a Surface-2 card, 18pt radius, elevated shadow, sliding down from the top with a 350ms spring (damping 0.8) + soft success haptic, auto-dismiss after 4s. Leading 44pt solid `#1EC677` circle with a near-black check glyph; a title 'Direct deposit received' DM Sans 14pt 700; a sub 'Payroll · Available now · 2 days early' DM Sans 12pt 400 `#5C6E63`; a trailing amount '+$1,284.10' DM Sans 16pt 700 positive mint."

- "Create the Chime primary button: 14pt corner radius, `#1EC677` background, `#062014` text DM Sans 16pt 700, 14pt vertical / 28pt horizontal padding, 52pt min height. Pressed: `#16A862` + scale 0.98. Add a soft variant: `#E4F7EE`/`#123A2A` bg, `#0FAE63`/`#00D67E` text, 12pt radius, no border."

- "Render the Chime bottom tab bar: 49pt + safe area, Surface-1 / `#0E1411`, blurred, 0.5pt top divider, 5 tabs — Home, Move, Card, Grow, Me — 22pt icons, DM Sans 10pt 600 labels always shown. Active `#00D67E` with the icon filled solid; inactive `#90A399`/`#6B7E72`. No tint pill, no slide."

### Iteration Guide
1. Canvas is soft off-white `#F6FBF8` light / deep green-charcoal `#0E1411` dark — calm, NOT pure white or neutral black
2. Mint (`#1EC677` / `#00D67E`) is the entire brand personality — applied head-to-toe, not as a lone green button
3. The balance hero is a full mint-gradient card with a giant 44pt 700 balance + superscript cents + a mint-glow shadow
4. Incoming money is positive mint; everyday spending is NEUTRAL ink — red is only for declined/failed/negative
5. Fire an instant alert (push + toast) the moment money moves, with a solid-mint check icon
6. SpotMe is prominent — fee-free overdraft banner with its available limit in bright mint
7. Celebrate early direct deposit inline ("2 days early") — it's a core trust signal
8. Round everything heavily — 24pt hero, 14pt buttons, 14pt tiles, pill chips — softness is the pitch
9. DM Sans throughout; numbers always 700 and tabular so amount columns align
10. Motion is soft springs / ease-outs (balance count-up, alert slide), haptics gentle — calm, never harsh
