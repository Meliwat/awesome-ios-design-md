# Design System Inspiration of SoFi (iOS)

## 1. Visual Theme & Atmosphere

SoFi's iOS app is a **premium one-stop financial command center** — banking, investing, borrowing, and rewards under a single membership. The visual language signals "modern, ambitious, all-in-one wealth platform" rather than a single-purpose budgeting app, so the design leans confident and dark-native: an **electric SoFi Blue (`#00A0DF`)** sitting on a **deep navy (`#0A0E27`)** ground. This blue-on-navy pairing is so core to SoFi's marketing that the app reads like it was *designed for dark mode first* — the navy is the canonical brand canvas in both themes, and electric blue is the energy that cuts through it.

The signature screen is the **member home dashboard**. It opens with a full-bleed **member hero** — a `#00A0DF → #1B53C4 → #0A0E27` diagonal gradient panel carrying a friendly greeting, the member's **net worth** rendered huge in white (40pt 800), and a green pill showing the month's change ("+$1,840.22 this month"). Below the gradient sits the **account/product tile grid**: a 2-column set of rounded cards — Checking & Savings (with APY), Invest (with today's %), Loans (with the next payment due), Credit Card (with cash-back) — each a small blue-soft icon chip, a label, a big value, and a colored sub-stat. Below that, a "Recent activity" relay list. The dashboard's job is to make a multi-product financial life feel unified and in-control on one screen — that consolidated net-worth-plus-tiles view is the heart of SoFi.

The other SoFi-defining moment is the **product tile / cross-sell card** — SoFi's whole business is one member using many products, so the home and the Invest/Borrow tabs surface rounded cards ("Active Invest — Stocks, ETFs & crypto", "Loan in minutes — check your rate, no credit impact") with a blue-soft icon, a title, and a one-line value prop. The second fixture is the **performance treatment**: gains in green (`#2FD08A`), losses in coral (`#FF6B6B`), rewards in gold (`#F2C14E`) — applied consistently to investment %s, account deltas, and the rewards points loop.

Typography is a clean, confident geometric sans — production SoFi uses a brand-tuned face; **Manrope** is the faithful free stand-in (semibold-forward, modern, tight tracking at display sizes). Hierarchy is wealth-dashboard: the net-worth hero is huge (40pt 800), screen titles 32pt, section headers 22pt, tile values 18-19pt, body 16pt, row titles 15pt, meta 14pt, captions 12pt. Numbers are always 700-800 — net worth, balances, and percentages are the data the member came for. The tone is **bold, confident, dark, and consolidated**.

**Key Characteristics:**
- Dark-native brand — electric **SoFi Blue** (`#00A0DF`) on deep **navy** (`#0A0E27`); navy is the canonical canvas in both themes
- The **member hero** is a `#00A0DF → #1B53C4 → #0A0E27` gradient panel with net worth in white + a green month-change pill
- **Account/product tile grid** — 2-column rounded cards (Banking, Invest, Loans, Card) each with an icon chip, value, and colored sub-stat
- **Cross-sell product cards** — "one member, many products" is the business; rounded cards with a blue-soft icon + value prop
- Performance colors: gains green (`#2FD08A`), losses coral (`#FF6B6B`), rewards gold (`#F2C14E`)
- Blue brightens to `#29C2FF` for accents, links, and active tabs so it pops on navy
- Fully **pill-shaped buttons** (500pt radius) — premium fintech, not boxy
- Heavily rounded tiles (18pt) on lifted navy surfaces (`#121736` / `#1B2147`)
- Manrope typography — confident geometric sans; numbers always 700-800
- Bottom tab bar: Home, Invest, Banking, Borrow, Me — active in bright blue

## 2. Color Palette & Roles

### Primary (Interactive)
- **SoFi Blue** (`#00A0DF`): Primary brand + interactive color — member-hero gradient base, primary pill buttons, logomark.
- **Blue Bright** (`#29C2FF`): Brighter blue for accents, links, "See all", active tabs, icon glyphs on navy.
- **Blue Pressed** (`#0086BC`): Pressed state on blue buttons.
- **Blue Soft Fill** (`#E6F4FB` light / `#0E2A44` dark): Tinted backgrounds for soft buttons, tile icon chips.

### Canvas & Surfaces (Dark-Native — canonical)
- **Navy Canvas** (`#0A0E27`): The canonical brand canvas — deep navy, used in dark mode and as the marketing ground.
- **Surface 1** (`#121736`): Cards, tiles, list containers on navy.
- **Surface 2** (`#1B2147`): Elevated cards, sheets, pressed rows.
- **Divider** (`#252C55`): Hairline between rows and tile borders.

### Canvas & Surfaces (Light Mode)
- **Light Canvas** (`#F4F7FB`): The light-mode ground — a cool off-white (the brand identity still stays navy + blue).
- **Light Surface 1** (`#FFFFFF`): Cards, tiles in light mode.
- **Light Surface 2** (`#EEF2F8`): Pressed rows, secondary fills.
- **Light Divider** (`#E2E7F0`): Hairline dividers in light mode.

### Text (on navy / dark-native)
- **Text Primary** (`#E8EBF7`): Primary body, tile values, row titles.
- **Text Secondary** (`#9BA3C7`): Metadata, labels, captions.
- **Text Tertiary** (`#6B7299`): Disabled, low emphasis.
- **On-Gradient White** (`#FFFFFF`): All text over the member hero gradient.
- **On-Blue Ink** (`#042235`): Near-black blue ink on solid `#00A0DF` buttons for contrast.

### Text (Light Mode)
- **Text Primary Light** (`#0C1330`): Primary body in light mode.
- **Text Secondary Light** (`#5A6286`): Metadata in light mode.

### Semantic

| Role | Dark-Native | Light Mode | Use |
|------|-------------|-----------|-----|
| Positive / Gain | `#2FD08A` | `#0FA968` | Investment gains, "+$" deltas, "+%", APY uplift |
| Negative / Loss | `#FF6B6B` | `#E0484D` | Investment losses, "-%", payment-due warnings |
| Rewards Gold | `#F2C14E` | `#C8920F` | Rewards points, cash-back, member perks |
| Info / Link | `#29C2FF` | `#0086BC` | Links, "See all", informational accents |
| Success | `#2FD08A` | `#0FA968` | Transfer/success confirmation |
| Attention | `#FF6B6B` | `#E0484D` | Errors, due dates, alerts |

**Performance is color-coded, not neutral.** Unlike a pure spending app, SoFi *does* color money movement — investment and net-worth changes use green/coral because performance is the point. The green month-change pill on the hero is a signature.

## 3. Typography Rules

### Font Family
- **Primary**: A confident geometric sans. Production SoFi uses a brand-tuned face; **`Manrope`** (by Mikhail Sharanda / Mirko Velimirovic, SIL OFL) is the faithful free stand-in — semibold-forward, modern, tight at display sizes, excellent number legibility.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numeric**: Use tabular/lining figures for net worth, balances, and amounts (`font-variant-numeric: tabular-nums`).
- **Monospace**: not used in product UI.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Net Worth Hero | Manrope | 40pt | 800 | 1.05 | -1.2pt | The net-worth figure on the member gradient |
| Screen Title | Manrope | 32pt | 800 | 1.15 | -0.6pt | Large nav title ("Invest", "Borrow") |
| Section Header | Manrope | 22pt | 700 | 1.2 | -0.3pt | "Recent activity", "Your accounts" |
| Tile Value | Manrope | 18pt | 700 | 1.25 | -0.4pt | Account/product tile balance (19pt on large devices) |
| Card Title | Manrope | 16pt | 700 | 1.3 | -0.1pt | Cross-sell card title |
| Body | Manrope | 16pt | 400 | 1.5 | 0pt | Descriptions, value props |
| Row Title | Manrope | 15pt | 700 | 1.3 | 0pt | Activity row name |
| Amount | Manrope | 15pt | 800 | 1.2 | 0pt | Activity amount (tabular) |
| Meta | Manrope | 14pt | 400 | 1.4 | 0pt | "Today · Auto-invest" |
| Caption / Sub-stat | Manrope | 12pt | 600 | 1.4 | 0.1pt | "3.80% APY", "+2.4% today", chip text |
| Button | Manrope | 16pt | 700 | 1.0 | 0pt | Primary / secondary pill buttons |
| Tab Label | Manrope | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Chip | Manrope | 12pt | 700 | 1.0 | 0.1pt | Status chip text |
| Link | Manrope | 13pt | 700 | 1.3 | 0pt | "See all", inline links |

### Principles
- **Net worth is the loudest thing**: 40pt 800 in white on the gradient, tracking -1.2pt — the consolidated number is the headline.
- **Numbers are always 700-800**: net worth, tile values, balances, percentages — all heavy and tabular so the wealth picture is instantly scannable and aligned.
- **Confident weights**: 400 body, 600 captions, 700 row titles/headers/buttons, 800 hero/screen titles. Semibold-forward — modern and assured, never thin.
- **Consolidated hierarchy**: the eye goes net worth → account tiles → activity; the multi-product life is organized, not chaotic.
- **Dynamic Type first-class**: hero, titles, headers, body, row titles, meta scale; tab labels and chip text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Transfer money", "Invest now", "Continue")**
- Shape: **Full pill, 500pt corner radius** (SoFi is pill-shaped, not boxy)
- Background: `#00A0DF` (SoFi Blue)
- Text: `#042235` (near-black blue ink), Manrope 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal; min height 52pt
- Pressed: background `#0086BC` + scale 0.98
- Disabled: background `#00A0DF` 35% / text `#042235` 50%

**Secondary / Outline Button ("Invest now", "Learn more")**
- Background: transparent
- Text: `#29C2FF` (Blue Bright), Manrope 15pt weight 700
- Border: 1.5pt `#00A0DF`
- Shape: full pill, 500pt radius
- Padding: 12pt vertical, 24pt horizontal
- Pressed: background `#0E2A44` (Blue Soft)

**Soft Button ("Add account", quick actions)**
- Background: `#E6F4FB` light / `#0E2A44` dark (Blue Soft Fill)
- Text: `#0086BC` light / `#29C2FF` dark, Manrope 14pt weight 700
- Shape: full pill, 500pt radius; no border
- Padding: 12pt vertical, 20pt horizontal

**Text Button ("See all activity")**
- Font: Manrope 13-14pt weight 700
- Color: `#29C2FF` (dark) / `#0086BC` (light)
- No background, no border, no underline

### Member Hero (the signature surface)

- Container: full-bleed, extends into the top safe area; rounded only at the bottom is optional (often full-bleed top, content padded)
- Background: linear gradient `#00A0DF → #1B53C4 → #0A0E27` (≈165°) — blends into the navy canvas at the bottom
- Top row: greeting ("Good morning, Alex") Manrope 18pt 700 white + notification/profile icons (22pt white)
- Net-worth label: "Net worth" Manrope 13pt 600 `rgba(255,255,255,0.75)`
- Net-worth value: Manrope 40pt 800 white, tracking -1.2pt, tabular
- Change pill: "+$1,840.22 this month" Manrope 13pt 700, green `#6EF0B6` on `rgba(47,208,138,0.18)`, 500pt pill, with an up-arrow glyph

### Account / Product Tile (core atom)

- Layout: 2-column grid, 12pt gap, each tile 18pt corner radius
- Container: Surface 1, 1pt divider border
- Icon chip: 36pt rounded (11pt) `#0E2A44`/`#E6F4FB` with an 18pt `#29C2FF`/`#0086BC` stroked glyph
- Label: Manrope 12pt 600 `#9BA3C7`
- Value: Manrope 18-19pt 800 `#E8EBF7`, tracking -0.4pt, tabular
- Sub-stat: Manrope 11-12pt 600 — green (`#2FD08A` gain/APY), coral (`#FF6B6B` due/loss), or gold (`#F2C14E` rewards)
- Tappable → product detail screen

### Cross-Sell Product Card (distinctive component)

- Container: Surface 2, 1pt divider, 18pt radius, 16pt padding
- Icon chip: 36pt rounded (11pt) `#0E2A44` with an 18pt `#29C2FF` glyph
- Title: Manrope 14-16pt 700 `#E8EBF7`
- Value prop: Manrope 12pt 400 `#9BA3C7`, 1-2 lines ("Stocks, ETFs & crypto, no commissions")
- Often in a horizontal carousel of products on the home / Invest / Borrow tabs

### Activity (Relay) Row

- Layout: 40pt rounded (13pt) icon tile + name/meta column + right-aligned amount
- Icon tile: gradient or blue-soft, glyph tinted by product (blue for invest/banking, gold for rewards)
- Name: Manrope 15pt 700 `#E8EBF7`
- Meta: Manrope 14pt 400 `#9BA3C7` — "Today · Auto-invest"
- Amount: Manrope 15pt 800, tabular; **positive green** (`#2FD08A`, "+$2,140.55"), **negative neutral ink** (`#E8EBF7`, "-$250.00") — investment buys are neutral, gains/income are green
- Separator: 1pt `#252C55` divider; no card border

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: Navy / blurred, 0.5pt top divider `#252C55`
- Tabs (5): Home, Invest, Banking, Borrow, Me
- Icon size: 22pt; active `#29C2FF` (icon fills solid); inactive `#6B7299`
- Labels: Manrope 10pt weight 600, always shown
- No tint pill — just bright-blue color + filled icon swap

**Top of Member Home**
- The gradient hero *is* the top — no separate nav bar; greeting + icons live inside the gradient

**Top Nav (sub-screens)**
- Height: 44pt + safe area
- Leading back chevron (white on gradient screens, primary text elsewhere)
- Centered compact title; optional trailing action

### Input Fields

**Amount Entry (Transfer / Invest)**
- Large centered amount, Manrope 40pt 800, blue cursor; "$" prefix `#9BA3C7`
- Funding-source row below (account + balance)
- Numeric keypad sheet

**Text Field**
- Height: 52pt
- Background: Surface 1 / Light Surface 1
- Corner radius: 14pt
- Floating label on focus; focus ring 1.5pt `#00A0DF`
- Placeholder Manrope 16pt 400 `#6B7299`

### Distinctive Components

**Member Hero Gradient** — see above; the single most recognizable SoFi surface.

**Account/Product Tile Grid** — see above; the consolidated multi-product view.

**Cross-Sell Product Card** — see above; "one member, many products" embodied.

**Performance Chip**
- Pill, 500pt radius, Manrope 12pt 700, 7×14pt padding
- Active: `#0E2A44`/`#E6F4FB` bg, `#29C2FF`/`#0086BC` text
- Up: `rgba(47,208,138,0.16)` bg, `#2FD08A` text, "▲ +2.4%"
- Down: `rgba(255,107,107,0.16)` bg, `#FF6B6B` text, "▼ -0.8%"
- Rewards: `rgba(242,193,78,0.16)` bg, `#F2C14E` text, "+1,240 pts"

**Net-Worth Change Pill** — the green/coral month-delta pill on the hero (signature).

**Rewards Loop**
- Gold accent throughout — points balance, "earn points" CTAs, cash-back tiles — gold `#F2C14E`

**Sparkline / Mini Chart**
- On Invest tiles and detail: a thin blue (`#29C2FF`) line, green/coral fill area depending on direction

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 16pt (tiles) / 20pt (headers, section labels, gradient content)
- Tile grid: 2 columns, 12pt gap, 16-18pt internal padding
- Activity row vertical padding: 12pt with a hairline divider

### Grid & Container
- iPhone: gradient hero full-bleed; tile grid 2-col at 16pt insets
- iPad: content centered max 680pt; tiles 3-col; hero scales
- Cross-sell cards: horizontal carousel, ~78% viewport-width peeking cards

### Whitespace Philosophy
- **The gradient owns the top**: the member hero is full-bleed and tall, blending into the navy — it sets the premium tone before any content
- **Organized density below**: tiles are a clean 2-col grid with comfortable padding; the multi-product life feels structured, not crowded
- **Pills everywhere**: buttons and chips are fully rounded — the pill language signals modern, premium fintech
- **Rounded tiles on lifted navy**: structure comes from rounded `#121736`/`#1B2147` surfaces and faint dividers, not hard borders

### Border Radius Scale
- Square (0pt): full-bleed gradient top edge (blends, no corner)
- Subtle (8pt): nested inline elements
- Standard (11-12pt): tile icon chips, small fills
- Card (16pt): cross-sell cards, sheets
- Tile (18pt): account/product tiles, feature cards
- Comfortable (24pt): large promo cards, bottom-sheet tops
- Pill (500pt): all buttons, status chips, the change pill
- Circle (50%): avatars, the logomark dot

## 6. Depth & Elevation

SoFi uses subtle elevation on a dark ground — the gradient itself is the primary "depth," and cards lift gently off navy:

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Activity rows, content on navy (separated by dividers) |
| Tile (Level 1) | `rgba(0,0,0,0.25) 0 2px 12px` + 1pt `#252C55` border | Account/product tiles, cross-sell cards |
| Gradient Hero (Level 2) | The gradient itself + soft inner depth | The member hero — color *is* the elevation |
| Sheet (Level 3) | `rgba(0,0,0,0.4) 0 -8px 28px` | Bottom sheets (transfer, invest), modals |
| Modal Overlay | `rgba(5,8,20,0.5)` | Scrim behind sheets and modals |

**Shadow Philosophy**: On a dark navy ground, drop shadows are weak, so SoFi separates surfaces with **1pt `#252C55` borders + a soft dark shadow** on tiles, and lets the **gradient hero carry the visual hierarchy** at the top (the color transition reads as depth and importance). In light mode, tiles use a soft neutral shadow instead of the border. The gradient is the brand's signature elevation cue.

### Motion
- **Net-worth count-up**: on home load / after a transfer, net worth rolls from previous → new over 600ms ease-out
- **Gradient parallax**: the hero gradient scrolls slightly slower than content (~0.7×), deepening the premium feel
- **Tile press**: 150ms scale to 0.97 + subtle border brighten on touch-down
- **Performance tick**: investment %s and the change pill animate value + color when data refreshes (200ms)
- **Cross-sell carousel**: snap-paged horizontal scroll with 300ms decel; peeking next card
- **Transfer / invest success**: a blue check draws in (stroke animation, 500ms) + success haptic; net worth re-counts
- **Tab switch**: instant; active icon fills bright blue, no slide
- **Pull to refresh**: a blue spinner; tiles + net worth re-animate if changed
- **Haptic feedback**: medium on transfer/invest success, light on tile and tab taps, soft on pull-refresh release

## 7. Do's and Don'ts

### Do
- Treat SoFi as dark-native — electric blue `#00A0DF` on deep navy `#0A0E27`; navy is the canonical canvas
- Open the member home with the `#00A0DF → #1B53C4 → #0A0E27` gradient hero carrying net worth in white
- Show the consolidated 2-column account/product tile grid below the hero
- Brighten blue to `#29C2FF` for accents, links, and active tabs so it pops on navy
- Color performance: gains green `#2FD08A`, losses coral `#FF6B6B`, rewards gold `#F2C14E`
- Use the green/coral month-change pill on the hero — it's a signature
- Make every button a full pill (500pt radius) — premium, never boxy
- Surface cross-sell product cards ("one member, many products" is the business)
- Set net worth, balances, and percentages in heavy tabular figures (700-800)
- Lift tiles off navy with a 1pt `#252C55` border + soft dark shadow

### Don't
- Don't use a neutral or light gray ground — the brand is navy `#0A0E27` even where light mode lifts surfaces
- Don't render the member hero as a flat solid — the blue→navy gradient is the signature
- Don't use boxy/sharp buttons — SoFi buttons are fully pill-shaped
- Don't make blue dull on navy — accents brighten to `#29C2FF`
- Don't make performance neutral — gains/losses are green/coral; that's the point of a wealth app
- Don't bury net worth — it's the loudest element, 40pt 800 white on the gradient
- Don't render amounts in proportional figures — use tabular so columns align
- Don't hide cross-sell — multi-product membership is SoFi's core model
- Don't use a Material tint pill on tabs — active is just bright-blue color + filled icon
- Don't drop the rewards-gold accent — the points/cash-back loop is a brand pillar
- Don't animate harshly — motion is smooth ease-outs and snaps, haptics measured

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Net worth 34pt; gradient hero shorter; tiles slightly tighter |
| iPhone 13/14/15 | 390pt | Standard layout; net worth 40pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the in-gradient top row |
| iPhone 15/16 Pro Max | 430pt | Net worth 44pt; larger tiles; more gradient height |
| iPad (portrait) | 768pt | Content centered max 680pt; tile grid 3-col; hero scales |
| iPad (landscape) | 1024pt+ | Two-pane: dashboard + product detail; hero in the left column |

### Dynamic Type
- Net worth, screen titles, section headers, tile values, body, row titles, meta: full scale
- Tab labels, chip text, the change-pill ratio: FIXED (layout-sensitive)
- Amounts scale but stay tabular so columns keep aligning

### Orientation
- All screens rotate
- iPad landscape pins a master dashboard beside a detail pane
- The gradient hero re-fits and re-centers on rotation

### Touch Targets
- Tab bar icons: 22pt glyph, 49pt bar, 44pt hit
- Tile: full-tile tappable, ≥ 96pt tall
- Cross-sell card: full-card tap
- Primary pill buttons: 52pt min height
- Performance chips (if interactive): 44pt hit

### Safe Area Handling
- Top: the gradient hero extends into the top safe area; the in-gradient top row insets below the Dynamic Island
- Bottom: tab bar + home indicator respected; sheet CTAs sit above the home indicator
- Keyboard: amount keypad is a sheet; content scrolls above it
- Sides: 16pt tile inset, 20pt header/gradient-content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Navy canvas (canonical): `#0A0E27`
- Light canvas: `#F4F7FB`
- Surface 1: `#121736` (navy) / `#FFFFFF` (light)
- Surface 2: `#1B2147` (navy) / `#EEF2F8` (light)
- Divider: `#252C55` (navy) / `#E2E7F0` (light)
- Text primary: `#E8EBF7` (navy) / `#0C1330` (light)
- Text secondary: `#9BA3C7` (navy) / `#5A6286` (light)
- SoFi Blue: `#00A0DF`
- Blue Bright: `#29C2FF`
- Blue Pressed: `#0086BC`
- Blue Soft Fill: `#0E2A44` (navy) / `#E6F4FB` (light)
- On-blue ink: `#042235`
- Gradient: `#00A0DF → #1B53C4 → #0A0E27` (≈165°)
- Positive / gain: `#2FD08A` (navy) / `#0FA968` (light)
- Negative / loss: `#FF6B6B` (navy) / `#E0484D` (light)
- Rewards gold: `#F2C14E` (navy) / `#C8920F` (light)

### Example Component Prompts
- "Build the SoFi member hero in SwiftUI: a full-bleed panel that extends into the top safe area with a `#00A0DF → #1B53C4 → #0A0E27` 165° linear gradient blending into the navy canvas at the bottom. Top row: greeting 'Good morning, Alex' Manrope 18pt 700 white + a notification bell and profile icon (22pt white), inset below the Dynamic Island. Below: 'Net worth' Manrope 13pt 600 `rgba(255,255,255,0.75)`; the net-worth figure '$84,210.55' Manrope 40pt 800 white tracking -1.2pt tabular; and a change pill '+$1,840.22 this month' Manrope 13pt 700 `#6EF0B6` on `rgba(47,208,138,0.18)`, 500pt pill, with a leading up-arrow glyph."

- "Create a SoFi account tile: a 2-column-grid card, 18pt corner radius, Surface-1 `#121736` background, 1pt `#252C55` border. Inside: a 36pt rounded (11pt) `#0E2A44` icon chip with an 18pt `#29C2FF` stroked glyph; a label Manrope 12pt 600 `#9BA3C7` ('Invest'); a value Manrope 18pt 800 `#E8EBF7` tracking -0.4pt tabular ('$31,884.02'); and a sub-stat Manrope 12pt 600 colored by meaning — green `#2FD08A` for gains/APY ('+2.4% today'), coral `#FF6B6B` for due/loss ('$402 due Jun 1'), gold `#F2C14E` for rewards. Whole tile tappable."

- "Render a SoFi cross-sell product card: Surface-2 `#1B2147`, 1pt `#252C55` border, 18pt radius, 16pt padding. A 36pt rounded (11pt) `#0E2A44` icon chip with an 18pt `#29C2FF` glyph; a title Manrope 16pt 700 `#E8EBF7` ('Active Invest'); a value-prop line Manrope 12pt 400 `#9BA3C7` ('Stocks, ETFs & crypto, no commissions'). Lay several out in a horizontal snap carousel with ~78%-width peeking cards."

- "Create the SoFi primary button: a FULL PILL (500pt corner radius), `#00A0DF` background, `#042235` near-black blue ink text Manrope 16pt 700, 14pt vertical / 28pt horizontal padding, 52pt min height. Pressed: `#0086BC` + scale 0.98. Add an outline variant: transparent, 1.5pt `#00A0DF` border, `#29C2FF` text, same pill shape; and a soft variant: `#0E2A44` bg, `#29C2FF` text, no border, same pill."

- "Build a SoFi activity row: a 40pt rounded (13pt) icon tile (gradient/blue-soft, glyph tinted blue for invest/banking or gold for rewards), a name Manrope 15pt 700 `#E8EBF7`, a meta line Manrope 14pt 400 `#9BA3C7` ('Today · Auto-invest'), and a right-aligned amount Manrope 15pt 800 tabular — POSITIVE/income green `#2FD08A` ('+$2,140.55'), investment buys NEUTRAL ink `#E8EBF7` ('-$250.00'). Separate rows with a 1pt `#252C55` hairline, no card border."

- "Render the SoFi bottom tab bar: 49pt + safe area, navy `#0A0E27` blurred, 0.5pt top `#252C55` divider, 5 tabs — Home, Invest, Banking, Borrow, Me — 22pt icons, Manrope 10pt 600 labels always shown. Active `#29C2FF` with the icon filled solid; inactive `#6B7299`. No tint pill, no slide."

### Iteration Guide
1. SoFi is dark-native — electric blue `#00A0DF` on deep navy `#0A0E27`; navy is the canonical canvas in BOTH themes
2. The member home opens with the `#00A0DF → #1B53C4 → #0A0E27` gradient hero carrying net worth in white + a green change pill
3. Below the hero is a consolidated 2-column account/product tile grid (Banking, Invest, Loans, Card) with colored sub-stats
4. Blue brightens to `#29C2FF` for accents, links, and active tabs so it reads on navy
5. Performance is color-coded: gains green `#2FD08A`, losses coral `#FF6B6B`, rewards gold `#F2C14E` — not neutral
6. Every button is a FULL PILL (500pt radius) — premium fintech, never boxy
7. Surface cross-sell product cards — "one member, many products" is SoFi's core business model
8. Net worth, balances, percentages are heavy (700-800) and tabular so the wealth picture aligns
9. Tiles lift off navy with a 1pt `#252C55` border + soft dark shadow; the gradient is the signature elevation
10. Manrope throughout; motion is smooth ease-outs / snaps (net-worth count-up, gradient parallax), haptics measured
