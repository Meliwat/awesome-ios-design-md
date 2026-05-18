# Design System Inspiration of Monzo (iOS)

## 1. Visual Theme & Atmosphere

Monzo's iOS app is built around one unforgettable object: the **Hot Coral debit card** (`#FF3464`). That coral is the brand — it appears the moment you open the app as a full-width card hero at the top of the Home screen, and it threads through every primary action, every selected state, every "this is the thing to tap" moment. Around the coral, Monzo wraps a calm, trustworthy **navy** (`#14233C`) that grounds the palette and keeps the bank feeling like a bank rather than a toy. The combination — playful coral, serious navy — is the whole personality: a bank that is fun to look at but safe to keep your money in.

The Home screen is a vertical scroll: the coral card hero at top, a horizontally-scrolling **Pots** strip below it (round-cornered "coins" with emoji and a savings goal each), and then the **transaction feed** — the screen that defines Monzo. Every transaction is a row with a circular merchant icon (Monzo enriches transactions with real merchant logos, falling back to a colored emoji circle by category), the merchant name in semibold, a category + time line beneath it in muted text, and the amount on the right. Money in is green; money out is the primary text color. Tapping a transaction opens a rich detail screen with a map, category, receipt, notes, and the ability to split or exclude from spending. This feed-as-interface is Monzo's signature: your money is a timeline you scroll, not a table you query.

The atmosphere is **friendly fintech**. Copy is conversational ("Pots help you set money aside", "You've spent £24.10 today"). Illustrations are rounded and warm. Numbers are big and confident — the balance is the largest type on the screen. There are no skeuomorphic textures, no gradients except on the card itself and the Pot coins, no heavy borders. Surfaces separate by elevation and subtle dividers, not boxes. The app feels light, fast, and human.

Typography is **Inter** — Monzo's brand and product typeface — a geometric humanist sans with excellent number legibility (critical for a banking app where balances and amounts must be instantly readable). Weights concentrate at 400 (body), 500–600 (labels and merchant names), 700 (section titles), and 800 (the balance and screen titles). The type system is confident and slightly tight (negative tracking on large sizes) to match the modern fintech feel.

**Key Characteristics:**
- Hot Coral debit card (`#FF3464`) as the Home-screen hero — the single most recognizable Monzo element
- Monzo Navy (`#14233C`) as the grounding brand color and dark-surface base
- Transaction feed as the primary interface — circular merchant icons, emoji fallbacks, category + time meta, amount on right
- Pots — round-cornered savings "coins" with emoji + goal, horizontally scrolling on Home
- Big confident numbers — the balance is the largest type element; Inter's tabular figures
- Inter typeface throughout — geometric humanist sans, tight tracking on large sizes
- Conversational copy — "Available to spend", "You've spent £24.10 today"
- Category emoji + color system — every transaction category has an emoji and a tint
- Rich transaction detail — map, category, receipt, notes, split, exclude-from-spending
- Light, flat surfaces — elevation and dividers, not boxes; gradients only on card + Pot coins
- Bottom tab bar: Home, Payments, Pots (or Save), Trends, Account — coral active state

## 2. Color Palette & Roles

### Primary (Interactive)
- **Hot Coral** (`#FF3464`): The brand color — debit-card hero, primary CTAs, active tab, selected states, key links. Identical in light and dark.
- **Hot Coral Pressed** (`#E02855`): Pressed/active state on coral buttons and the card.
- **Coral Tint Wash** (`#FFE3EA` light / `#3A1E29` dark): 8–12% coral fill behind selected rows and coral chips.

### Brand Anchor
- **Monzo Navy** (`#14233C`): The serious counterweight — dark headers, dark-mode card/row surface, illustration ink. The brand's "trust" color.
- **Navy Deep** (`#0E1620`): Darkest navy — dark-mode canvas, derived one step darker than brand navy.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#F6F7F9`): Primary light canvas — a cool off-white, never pure white.
- **Surface** (`#FFFFFF`): Cards, transaction rows, sheets — pure white lifts above the cool canvas.
- **Surface Pressed** (`#EEF0F3`): Pressed/selected row state.
- **Divider** (`#E4E7EB`): Hairline dividers between transaction rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#0E1620`): Primary dark canvas — deep navy, NOT pure black.
- **Dark Surface 1** (`#14233C`): Cards, rows, sheets — the brand navy itself.
- **Dark Surface 2** (`#1B2F4D`): Elevated surfaces, pressed rows, Pot add-tile.
- **Dark Divider** (`#243A57`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#14233C`): Primary text — the brand navy, used as ink on light.
- **Text Secondary Light** (`#5C6B82`): Category, time, metadata, secondary labels.
- **Text Tertiary Light** (`#94A1B5`): Disabled, placeholders, faint captions.
- **Text Primary Dark** (`#F4F6F9`): Primary text on dark.
- **Text Secondary Dark** (`#9FB0C6`): Metadata on dark.
- **Text Tertiary Dark** (`#61748E`): Disabled / placeholder on dark.

### Pot & Category Accents
Pots and transaction categories use a saturated accent set (coin gradients, category tints, progress bars).

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Mint (Savings) | `#2FB59A` | `#5CE0C4` |
| Sky (Bills) | `#3E7FC4` | `#5AA9F0` |
| Gold (Treats) | `#E0A23A` | `#FFC75F` |
| Violet (Subscriptions) | `#7A4FD0` | `#A98AE6` |
| Coral (General) | `#FF3464` | `#FF3464` |
| Teal (Transport) | `#1F9E8F` | `#46C7B7` |

### Semantic
- **Income Green** (`#1FA971` light / `#2FCB8F` dark): Money-in amounts, positive deltas.
- **Error Red** (`#E0354A` light / `#FF5A6E` dark): Declined payments, errors, destructive confirmation.
- **Warning Amber** (`#D98A1F` light / `#FFB347` dark): Low-balance and budget warnings.
- **Pending Grey** (`#94A1B5`): Pending/authorized-not-settled transactions (slightly faded row).

## 3. Typography Rules

### Font Family
- **Brand & Product**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist sans with excellent figure legibility; Monzo uses it across product and marketing for confident, readable numbers.
- **Numerals**: Inter tabular figures (`font-feature-settings: "tnum"`) for balances, amounts, and any aligned numeric column.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 800 | 1.15 | -0.6pt | "Home", "Payments" — large nav title |
| Balance / Hero Number | Inter | 30pt | 800 | 1.1 | -0.5pt | Card balance, Pot total — tabular |
| Section Title | Inter | 22pt | 700 | 1.2 | -0.3pt | "Current account", "Pots" |
| Subsection | Inter | 18pt | 700 | 1.25 | -0.2pt | Pot detail header, sheet title |
| Body | Inter | 16pt | 400 | 1.45 | 0pt | Descriptive copy, sheet body |
| Merchant / Row Title | Inter | 15pt | 600 | 1.3 | -0.1pt | Transaction merchant name |
| Amount | Inter | 15pt | 700 | 1.3 | -0.1pt | Transaction amount (tabular) |
| Meta | Inter | 13pt | 500 | 1.35 | 0pt | Category · time, secondary line |
| Label / Caption | Inter | 12pt | 500 | 1.3 | 0.1pt | "Available to spend", chip text |
| Button | Inter | 16pt | 700 | 1.0 | 0pt | Primary CTA label |
| Tab Label | Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Card Number | Inter | 13pt | 600 | 1.0 | 2pt | •••• grouped digits on card |

### Principles
- **Numbers lead**: The balance is the largest, boldest element on the Home screen — money is the headline.
- **Tabular figures everywhere money appears**: amounts, balances, Pot totals align vertically with `tnum`.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: body 400, meta 500, merchant 600, titles 700, balance/screen-title 800. No thin, no italic.
- **Tighten as it grows**: negative tracking from -0.2pt at 18pt down to -0.6pt at 32pt for the confident fintech feel.
- **Dynamic Type first-class**: titles, balance, body, merchant, meta scale; tab labels and card number stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Send money / Confirm / Add £)**
- Shape: Rounded rectangle, 14pt corner radius
- Background: `#FF3464` (Hot Coral)
- Text: `#FFFFFF`, Inter 16pt weight 700
- Padding: 15pt vertical, 28pt horizontal; full-width on sheets (16pt side insets)
- Pressed: background `#E02855` + scale 0.98
- Disabled: `#FF3464` at 35% opacity, text at 60%

**Secondary Button (Add to pot / Edit)**
- Background: `#FFFFFF` light / `#1B2F4D` dark
- Text: `#14233C` light / `#F4F6F9` dark, Inter 15pt weight 600
- Border: 1pt `#E4E7EB` light / `#243A57` dark
- Corner radius: 14pt
- Pressed: background `#EEF0F3` light / `#243A57` dark

**Pill / Tertiary Button (Split bill / Filter)**
- Background: transparent
- Text + border: `#FF3464`, Inter 14pt weight 600
- Border: 1.5pt `#FF3464`
- Corner radius: 500pt (full pill)
- Pressed: background coral-wash `#FFE3EA` light / `#3A1E29` dark

**Text Button (See all transactions)**
- Font: Inter 14pt weight 600
- Color: `#FF3464`
- No underline, no background
- Pressed: 60% opacity

### Core Atoms

**Transaction Row** (the core atom)
- Height: ~62pt, 20pt horizontal inset
- Leading: 42pt circular merchant icon — real merchant logo if known, else colored emoji circle by category (background = category tint at ~18% on dark / ~14% on light)
- Title: merchant name, Inter 15pt w600, primary text color
- Subtitle: `category · time`, Inter 13pt w500, secondary text
- Trailing: amount, Inter 15pt w700 tabular; income in green `#1FA971`/`#2FCB8F`, spend in primary text
- Pending: whole row at 60% opacity with a small "Pending" tag
- Tap: pushes the rich transaction detail screen
- Divider: hairline `#E4E7EB`/`#243A57` inset to text leading edge (or none on grouped white surface)

**Pot Coin (Home strip)**
- Size: 92×92pt, 16pt corner radius
- Background: linear-gradient of the Pot's accent color (e.g., mint `#2FB59A → #5CE0C4`)
- Center: 30pt emoji glyph (user-chosen)
- Shadow: `0 8px 16px -8px rgba(0,0,0,0.25)`
- Below coin: name Inter 12pt w600, amount Inter 11pt w500 secondary
- "New pot" tile: dashed 1pt border, surface-2 fill, `+` glyph in tertiary text

**Pot Card (detail / list)**
- Surface card, 16pt radius, 16pt padding
- Leading: 48pt rounded-square coin (12pt radius) with emoji
- Body: name Inter 15pt w700, "£640 of £1,000 goal" Inter 12pt w500 secondary
- Progress bar: 6pt tall, 3pt radius, track = divider color, fill = Pot accent color, width = progress %

**Chip / Category Tag**
- Background: category tint at 12–16%
- Text: category accent color, Inter 12pt w600
- Corner radius: 500pt (pill), 6pt vertical / 12pt horizontal padding

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area; background `#FFFFFF` light / `#0E1620` dark with 0.5pt top divider, 20px blur
- Tabs (5): Home, Payments, Pots (or Save), Trends, Account
- Icon size: 22pt; active = filled glyph in Hot Coral `#FF3464`; inactive = stroke glyph in tertiary text
- Labels: Inter 10pt w500, always shown
- No tint pill — active is icon fill + label color change to coral

**Large Title Header**
- Screen title Inter 32pt w800, left-aligned, collapses to a 17pt centered title on scroll
- Trailing: circular avatar (34pt) or contextual action icon
- Background: canvas color; no shadow until content scrolls under (then 0.5pt divider)

**Transaction Detail (pushed screen)**
- Top: large merchant icon (64pt circle) centered, merchant name 22pt w700, amount 30pt w800
- Map snapshot card (if location known), 16pt radius
- Rows: Category, Date, Notes, Receipt, "Split bill", "Exclude from spending" — grouped on white/navy surface

### Input Fields

**Text Field (Send money amount / Notes)**
- Amount entry: large centered Inter 30pt w800, coral caret, no box — number-pad keyboard
- Standard field: 52pt tall, surface fill, 12pt radius, no border at rest
- Focus: 1.5pt `#FF3464` border
- Placeholder: tertiary text, Inter 16pt w400

**Search Bar**
- Height: 44pt, surface-pressed fill `#EEF0F3` / `#1B2F4D`, 12pt radius
- Leading magnifier icon 16pt secondary text
- Placeholder "Search transactions" Inter 15pt w400 tertiary

### Distinctive Components

**Hot Coral Card Hero**
- Full-width minus 20pt insets, ~132pt tall, 18pt corner radius
- Background: coral gradient `#FF3464 → #FF5A7F → #E02855` (135°)
- "MONZO" wordmark top-right, balance 30pt w800 white, "Available to spend" 12pt w500 white@85%, masked card number bottom-left
- Tap: flips to card controls (freeze, PIN, details) with a 3D flip animation
- Shadow: `0 14px 30px -14px rgba(255,52,100,0.4)`

**Pots Strip**
- Horizontal scroll of Pot coins on Home, 12pt gap, 20pt side insets
- "Pots" section label above in Inter 13pt w600 secondary
- Drag-money interaction: long-press a transaction or use the Pot detail to move money in/out with a coin animation

**Transaction Feed**
- Day-grouped: each group has a header row (`TODAY`, `YESTERDAY`, date) Inter 13pt w700 secondary + day total on the right
- Infinite scroll, newest first
- Pull-to-refresh with a coral spinner

**Spending / Trends Ring**
- Category breakdown as a segmented progress ring or stacked bar in category accent colors
- Center: total spent this period, Inter 30pt w800
- Legend rows with category emoji + amount

**Split Bill Sheet**
- Bottom sheet, 16pt top radius
- Avatars of people in the split, amount each, coral "Request" CTA

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt (card, Pots, section labels)
- Transaction row inset: 20pt; icon-to-text gap 14pt
- Section vertical gap: 22–24pt

### Grid & Container
- iPhone: single column, 20pt side insets, full-width card and feed
- iPad: content max-width ~620pt centered; feed and Pots in a centered column, optional 2-pane (account list + detail)
- Pot coins: fixed 92pt squares in a horizontal scroll; Pot cards full-width minus insets

### Whitespace Philosophy
- **Numbers breathe**: the balance and amounts get generous space — never cramped
- **Feed rhythm**: ~62pt rows with comfortable 14pt icon gap; day headers add 12pt top breathing room
- **Flat, not boxed**: surfaces separate by elevation + hairline dividers; only the card and Pot coins use gradient fills
- **Card is the hero**: the coral card always sits at the top with clear margin — nothing competes with it

### Border Radius Scale
- Subtle (4pt): progress-bar fills, tiny tags
- Small (8pt): inline chips, small controls
- Standard (12pt): input fields, search bar, Pot coin inner square
- Comfortable (14pt): buttons, secondary surfaces
- Card (16–18pt): cards, sheets, transaction-detail map, Pot coins
- Pill (500pt): pill buttons, category chips
- Circle (50%): merchant icons, avatars

## 6. Depth & Elevation

Monzo is mostly flat; elevation is reserved for the card hero, Pot coins, sheets, and floating actions.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, hairline divider | Transaction rows, list sections, headers |
| Raised (Level 1) | `0 8px 16px -8px rgba(0,0,0,0.18)` (dark: deeper) | Pot coins, secondary cards |
| Card Hero (Level 2) | `0 14px 30px -14px rgba(255,52,100,0.40)` | Coral debit-card hero (colored shadow) |
| Sheet (Level 3) | `0 -8px 28px rgba(0,0,0,0.20)` | Bottom sheets (Send, Split, Pot move) |
| Modal Scrim | `rgba(8,15,24,0.55)` | Dim behind sheets and modals |

**Shadow Philosophy**: Shadows convey "this is a physical object you can pick up" — most strongly on the coral card (which even gets a tinted coral shadow) and Pot coins (which feel like literal coins you drag money into). Content rows stay flat with dividers. In dark mode, shadows deepen and a subtle 1pt navy border can substitute as the elevation cue on sheets.

### Motion
- **Card flip**: tapping the card does a 3D Y-axis flip (350ms ease-in-out) to reveal card controls; flip back on dismiss
- **Pot money move**: a coin/disc animates from the card or source row into the Pot with a 400ms spring + soft haptic; balance numbers tick up/down with a 250ms count animation
- **Feed insert**: new transactions slide in from top with 250ms ease-out + subtle highlight wash that fades over 800ms
- **Pull-to-refresh**: coral circular spinner; release triggers a 220ms settle
- **Tab change**: instant icon/label color crossfade (120ms); no slide
- **Push navigation**: standard iOS push (300ms) into transaction detail
- **Number ticker**: balances and Pot totals animate digit-by-digit on change (ease-out, ~250ms)
- **Haptics**: soft impact on Pot move, light impact on card flip, success haptic on completed payment, light tick on tab change

## 7. Do's and Don'ts

### Do
- Make the Hot Coral card (`#FF3464`) the unmistakable Home-screen hero — top of the scroll, clear margin, coral-tinted shadow
- Ground the palette with Monzo Navy (`#14233C`) — it's the dark surface and the light-mode ink
- Build the transaction feed as the primary interface — circular merchant icons, emoji fallbacks, `category · time` meta, amount on the right
- Use big confident Inter numbers with tabular figures for balances and amounts
- Render Pots as round-cornered "coins" with an emoji and a savings goal; horizontal strip on Home
- Color income green and keep spend in primary text — money in vs money out reads instantly
- Keep copy conversational and human ("You've spent £24.10 today")
- Reserve gradients for the card and Pot coins only — everything else is flat
- Use coral for every "primary action" and active state; navy for trust/structure
- Group the feed by day with header + day total

### Don't
- Don't bury the coral card or replace it with a generic balance number — the card *is* the brand
- Don't use pure black for dark mode — deep navy `#0E1620` is correct
- Don't use pure white for the light canvas — cool off-white `#F6F7F9`, white only for cards/rows
- Don't render transactions as a dense spreadsheet table — they are rich, tappable feed rows
- Don't drop the merchant icons / category emoji — the colorful circles are core to the feel
- Don't add gradients or heavy shadows to content rows — the feed is flat
- Don't use a tint pill on the active tab — active is coral icon fill + coral label only
- Don't use thin or italic Inter weights — the system lives at 400–800 upright
- Don't desaturate Pot coins or category colors in dark mode — chrome dims, content does not
- Don't make amounts non-tabular — columns of money must align

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Card ~120pt tall; Pot coins 84pt; balance 28pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in large-title header |
| iPhone 15/16 Pro Max | 430pt | Card ~140pt tall; Pot coins 96pt; larger balance |
| iPad (portrait) | 768pt | Content max-width 620pt centered; feed single column |
| iPad (landscape) | 1024pt+ | Optional 2-pane: account/Pots list (left) + detail (right) |

### Dynamic Type
- Screen title, balance, section title, body, merchant, meta: full scale
- Tab labels, card number, Pot-coin caption: FIXED (layout-sensitive)
- Amounts scale but remain tabular-aligned

### Orientation
- iPhone: portrait-primary; rotation supported with reflow
- iPad: split-view; transaction detail becomes the secondary pane in landscape

### Touch Targets
- Transaction row: full-row tap, ≥56pt effective height
- Tab bar icons: 22pt glyph, 44pt hit
- Pot coin: 92pt square, fully tappable
- Primary buttons: ≥48pt tall
- Card hero: full card is the flip target

### Safe Area Handling
- Top: large-title header respects safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; sheets respect bottom inset
- Keyboard: amount entry and notes scroll above keyboard; number pad never covers the running total
- Sides: 20pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Hot Coral (brand / CTA): `#FF3464`
- Coral pressed: `#E02855`
- Monzo Navy (anchor / dark surface): `#14233C`
- Dark canvas: `#0E1620`
- Light canvas: `#F6F7F9`
- Light surface (cards/rows): `#FFFFFF`
- Text primary: `#14233C` light / `#F4F6F9` dark
- Text secondary: `#5C6B82` light / `#9FB0C6` dark
- Income green: `#1FA971` light / `#2FCB8F` dark
- Error red: `#E0354A` light / `#FF5A6E` dark
- Pot mint: `#2FB59A` light / `#5CE0C4` dark
- Pot gold: `#E0A23A` light / `#FFC75F` dark
- Divider: `#E4E7EB` light / `#243A57` dark

### Example Component Prompts
- "Build the Monzo Hot Coral card hero in SwiftUI: full-width minus 20pt insets, 132pt tall, 18pt corner radius, background a 135° gradient `#FF3464 → #FF5A7F → #E02855`. 'MONZO' wordmark top-right in Inter 11pt w700 white@90%, balance `£2,418.60` Inter 30pt w800 white (tabular figures), 'Available to spend' Inter 12pt w500 white@85% below it, masked card number `•••• •••• •••• 9042` bottom-left Inter 13pt w600 white@90% 2pt tracking. Shadow `0 14px 30px -14px rgba(255,52,100,0.4)`. Tap → 3D Y-axis flip (350ms) to card controls."

- "Create a Monzo transaction row: 62pt tall, 20pt horizontal inset. Leading 42pt circular icon — merchant logo if available, else a category-emoji circle (background = category tint at 14% light / 18% dark). Merchant name Inter 15pt w600 `#14233C`, subtitle `Eating out · 9:12` Inter 13pt w500 `#5C6B82`, trailing amount Inter 15pt w700 tabular — income `#1FA971`, spend `#14233C`. Pending state: whole row 60% opacity + 'Pending' tag. Tap pushes the rich detail screen."

- "Render the Monzo Pots strip: horizontal scroll, 12pt gap, 20pt side insets, section label 'Pots' Inter 13pt w600 `#5C6B82` above. Each Pot coin: 92×92pt, 16pt radius, fill = a 135° gradient of its accent (mint `#2FB59A → #5CE0C4`), centered 30pt emoji, shadow `0 8px 16px -8px rgba(0,0,0,0.2)`. Below: name Inter 12pt w600, amount Inter 11pt w500 secondary. Last tile = 'New pot': dashed 1pt border, surface-2 fill, `+` in tertiary text."

- "Build the Monzo Pot detail card: surface card 16pt radius 16pt padding. Leading 48pt rounded-square (12pt radius) coin with emoji. Body: name Inter 15pt w700, '£640 of £1,000 goal' Inter 12pt w500 secondary. Progress bar 6pt tall 3pt radius, track = divider color, fill = Pot accent color at 64% width. 'Add money' coral primary button below."

- "Create the Monzo bottom tab bar: 52pt + safe area, `#FFFFFF` light / `#0E1620` dark, 0.5pt top divider, 20px blur. Five tabs: Home, Payments, Pots, Trends, Account. 22pt icons; active = filled glyph + label in Hot Coral `#FF3464`; inactive = stroked glyph + label in tertiary text. No tint pill. Labels Inter 10pt w500 always shown."

- "Build the Monzo Send-money amount screen: large centered amount entry in Inter 30pt w800 with a coral caret, no input box, system number pad. Below: recipient row (avatar + name). Bottom: full-width coral primary 'Send' button (14pt radius, Inter 16pt w700 white). Success → success haptic + checkmark animation."

### Iteration Guide
1. The Hot Coral card (`#FF3464`) is the Home-screen hero — top of scroll, clear margin, coral-tinted shadow; never a plain balance number
2. Ground everything in Monzo Navy (`#14233C`) — light-mode ink and dark-mode surface; dark canvas is deeper navy `#0E1620`, never black
3. The transaction feed is the interface: circular merchant icons, emoji fallbacks by category, `category · time` meta, amount on the right, day-grouped
4. Numbers lead — balance is the biggest type; Inter tabular figures for all money
5. Pots are round "coins" with emoji + goal — horizontal strip on Home, gradient fills
6. Income green, spend in primary text — money in vs out is instantly legible
7. Flat surfaces + hairline dividers everywhere; gradients only on the card and Pot coins
8. Coral = every primary action and active state; no tint pill on the active tab
9. Copy is conversational and human; warmth is part of the brand
10. Dark mode dims chrome only — merchant emoji, Pot coins, and category colors stay saturated
