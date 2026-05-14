# Design System Inspiration of PayPal (iOS)

## 1. Visual Theme & Atmosphere

PayPal is the elder statesman of fintech rendered as a friendly, recognizable consumer-finance utility. The canvas is white, the brand is blue — specifically the deep navy PayPal Blue (`#003087`) that's been the company's identity since the original 1998 logo, paired with a lighter, more contemporary PayPal Sky (`#0070BA`) introduced in the 2014 rebrand. Where Cash App rebels and Robinhood streamlines, PayPal reassures: the design language signals 25 years of trusted money movement, not nightclub energy. The interface revolves around three things — your balance, your recent activity, and the Send/Request buttons — laid out in calm rectangular cards on a clean white canvas.

The primary accent is **PayPal Blue** (`#003087`) — a deep, almost navy blue used for the wordmark, primary CTAs ("Send", "Request", "Continue"), and the active state on tabs. The lighter **PayPal Sky** (`#0070BA`) — the second blue introduced in the 2014 rebrand — appears as a secondary accent on icons, link colors, and the gradient versions of the wordmark. The dual-blue palette is iconic: the "P-P" overlap in the modern logo uses both colors layered. The rest of the palette is muted institutional gray with selective use of **PayPal Cobalt** (`#001C64`) for the deepest dark moments — the splash screen, certain illustration backgrounds, and dark mode primary surfaces.

Typography is **PayPal Sans Big** (display) and **PayPal Sans Small** (body) — proprietary faces drawn by Monotype for PayPal in 2018, replacing their earlier use of Helvetica Neue. They're warm contemporary sans serifs with rounded corners on the terminals (PayPal Sans Big is notably more rounded than PayPal Sans Small for legibility at large sizes), and they share a common skeleton so display and body weights stack cleanly. Tabular figures throughout: every $-amount, every transaction value uses tabular-nums so the vertical scan of the Activity feed reads cleanly. The voice is reassuring, not playful — PayPal Sans's roundness is gentle, not whimsical.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with `#F5F7FA` for subtle section fills — calm institutional look
- PayPal Blue (`#003087`) for primary CTAs, active tab, wordmark; PayPal Sky (`#0070BA`) as secondary accent and link color
- Balance card at top of the Wallet tab — large rounded rectangle, white surface, $-balance in PayPal Sans Big 36pt
- Big Send Money flow as the primary action — fullscreen amount entry leading to a recipient picker
- Activity feed as a stacked vertical list with rich icons — each row has a colored circular icon indicating transaction type
- Bottom tab bar: Home / Send / Wallet / Activity / Finances — 5 tabs (the "Send" tab is sometimes a center FAB-style button)
- PayPal Sans Big at hero sizes; PayPal Sans Small for body and metadata; tabular numerals on amounts
- Subtle 8pt-radius rounded cards everywhere — the calm, blocky composition is the brand
- Light shadows (`rgba(0,0,0,0.06)`) — slightly more elevated than a pure tech-product feel, more like a card wallet
- Recognizable PayPal "P-P" mark in the top-left corner of most screens — the brand is signaled at all times

## 2. Color Palette & Roles

### Primary
- **PayPal Blue** (`#003087`): Primary CTA fill, wordmark color, active tab color — the iconic deep blue.
- **PayPal Sky** (`#0070BA`): Secondary accent — link color, secondary CTA, gradient highlight, icon color.
- **PayPal Cobalt** (`#001C64`): Deepest blue — splash screen, illustration backgrounds, dark mode surface 1.

### Brand Gradients
- **PayPal Gradient Blue** (`linear-gradient(135deg, #003087 0%, #0070BA 100%)`): Used on the modern wordmark's "P-P" overlap effect and occasionally on hero banners.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas everywhere on light mode.
- **Surface Gray** (`#F5F7FA`): Section backgrounds, secondary fills.
- **Surface Gray 2** (`#EEF1F4`): Input field fill, card press states.
- **Divider** (`#E5E8ED`): Hairline row dividers, card outlines.
- **Card White** (`#FFFFFF`): Balance card and wallet item cards.

### Text
- **Text Primary** (`#001435`): Almost-black with a slight blue tint — primary text, balance amounts, screen titles.
- **Text Secondary** (`#2C2E2F`): Body text on lighter contexts.
- **Text Muted** (`#687173`): Metadata — transaction subtitles, dates, secondary labels.
- **Text Tertiary** (`#9DA3A6`): Placeholders, disabled labels.

### Semantic
- **Success Green** (`#1C8B43`): Money received confirmation, successful transaction.
- **Success Green Bg** (`#E4F5EA`): Tinted background for "Received" pills on Activity rows.
- **Error Red** (`#D20021`): Payment failed, validation errors.
- **Error Red Bg** (`#FCE5E8`): Tinted background for error states.
- **Warning Amber** (`#FFB81C`): Pending payments, identity verification needed.

### Activity Icon Colors
- **Sent Icon** (`#003087`): PayPal Blue for outgoing money.
- **Received Icon** (`#1C8B43`): Success green for incoming money.
- **Card Icon** (`#0070BA`): Sky blue for card-related transactions.
- **Reward Icon** (`#FFB81C`): Amber for cashback / rewards.

### Dark Mode
PayPal supports dark mode since 2020. The dark canvas is a deep cobalt-tinted near-black to preserve the brand's blue identity even in dark.
- **Dark Canvas** (`#0A0E1A`): Primary dark background — has a slight blue tint, NOT pure black.
- **Dark Surface 1** (`#141A2A`): Cards, sheets.
- **Dark Surface 2** (`#1F2740`): Pressed states.
- **Dark Divider** (`#2A3142`): Hairlines.
- **Dark Text Primary** (`#FFFFFF`): Inverted.
- **Dark Text Secondary** (`#A8AEC4`): Inverted secondary.
- **PayPal Blue (dark)** (`#3B82F6`): On dark canvas, PayPal Blue shifts to a brighter, more readable blue to maintain contrast.

## 3. Typography Rules

### Font Family
- **Display**: `PayPal Sans Big` (proprietary, drawn by Monotype for PayPal ~2018)
- **Body**: `PayPal Sans Small` (proprietary, sibling family — designed to pair with PayPal Sans Big at small sizes)
- **Weights available**: Regular (400), Medium (500), Bold (700)
- **Tabular figures**: enabled on all $-amounts, transaction values, percentages
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'Helvetica Neue', 'SF Pro Text', Arial, sans-serif`
- **Google Fonts substitute**: `Inter` (closest match), with `tabular-nums`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance Hero | PayPal Sans Big | 36pt | 700 | 1.0 | -0.5pt | "$1,247.92" on the balance card — TABULAR |
| Send Amount Hero | PayPal Sans Big | 56pt | 700 | 1.0 | -1pt | The $-amount on Send Money fullscreen — TABULAR |
| Screen Title | PayPal Sans Big | 24pt | 700 | 1.15 | -0.2pt | "Wallet", "Activity", "Finances" — left-aligned |
| Section Header | PayPal Sans Big | 18pt | 700 | 1.2 | -0.1pt | "Recent activity", "PayPal Balance", "Cards" |
| Card Title | PayPal Sans Big | 16pt | 500 | 1.3 | 0pt | "Visa debit card ending in 4242" |
| Body | PayPal Sans Small | 16pt | 400 | 1.45 | 0pt | About-the-transaction paragraph, help text |
| Body Small | PayPal Sans Small | 14pt | 400 | 1.4 | 0pt | Settings descriptions, fine print |
| Activity Title | PayPal Sans Small | 15pt | 500 | 1.3 | 0pt | Activity row recipient name |
| Activity Subtitle | PayPal Sans Small | 13pt | 400 | 1.3 | 0pt | "Yesterday, 3:42 PM · Completed" |
| Activity Amount | PayPal Sans Big | 16pt | 500 | 1.2 | 0pt | "$24.50" on activity rows — TABULAR |
| Button Primary | PayPal Sans Big | 17pt | 700 | 1.0 | 0pt | "Send", "Request", "Continue" |
| Button Secondary | PayPal Sans Small | 15pt | 500 | 1.0 | 0pt | Outline buttons |
| Tab Label | PayPal Sans Small | 11pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Link | PayPal Sans Small | 15pt | 500 | 1.4 | 0pt | "Add a payment method" links in PayPal Sky |
| Chip | PayPal Sans Small | 13pt | 500 | 1.0 | 0pt | Filter chips, "Completed", "Pending" |

### Principles
- **PayPal Sans Big for everything display + button + amount**: Headings, balances, screen titles, primary button text — even tab bar would use Big if there were enough room. The decision rule: if it's marketing the brand or commanding attention, use Big.
- **PayPal Sans Small for body + metadata**: Activity subtitles, body paragraphs, settings descriptions, fine print. Small is the workhorse.
- **Tabular numerals on all financial figures**: Balance, transaction amounts, percentages, card numbers — non-negotiable. The Activity feed's vertical column of $-amounts must align perfectly.
- **Tight tracking at hero sizes**: -1pt at 56pt down to 0pt at body — gives the balance a confident, calm presence.
- **Bold for buttons, Medium for everything else**: PayPal Sans Big weight 700 on primary CTAs, 500 on card titles and activity rows; 400 is reserved for body / metadata.

## 4. Component Stylings

### Buttons

**Primary CTA ("Send", "Continue", "Add Money")**
- Background: `#003087` (PayPal Blue)
- Text: `#FFFFFF`, PayPal Sans Big 17pt weight 700
- Padding: 16pt vertical, 24pt horizontal
- Corner radius: 24pt (a soft pill — PayPal uses radius equal to half-height for the soft-pill look)
- Height: 48pt minimum (radius 24pt creates a pill)
- Width: typically full-width minus 16pt margins
- Pressed: background `#001C64`, scale 0.98
- Haptic: `.impactOccurred(.medium)` on Send confirmation

**Secondary Outline Button ("Cancel", "More options")**
- Background: transparent
- Border: 1.5pt `#003087`
- Text: `#003087`, PayPal Sans Small 15pt weight 500
- Padding: 14pt vertical, 22pt horizontal
- Corner radius: 24pt

**Link Button ("Forgot password", "Learn more")**
- Background: none
- Text: `#0070BA` (PayPal Sky), PayPal Sans Small 15pt weight 500
- Underline on hover/press
- 44pt hit target

**Send/Request Pair (in Send Money flow)**
- Side-by-side pill buttons
- "Request": `#FFFFFF` background, 1.5pt `#003087` border, `#003087` text
- "Send": `#003087` background, white text
- Both: 48pt height, 24pt radius, half-width with 8pt gap between

### Cards & Containers

**Balance Card (top of Wallet)**
- Container: full-width minus 16pt horizontal margins
- Background: `#FFFFFF`
- Border: 1pt `#E5E8ED`
- Corner radius: 16pt
- Shadow: `rgba(0,0,0,0.06) 0 2px 8px`
- Padding: 20pt
- Layout: "PayPal balance" label in PayPal Sans Small 13pt 500 `#687173` → balance "$1,247.92" in PayPal Sans Big 36pt 700 `#001435` tabular → row of action buttons (Add Money / Transfer)

**Activity Row**
- Height: 68pt minimum (slightly taller than competitors to allow rich icon + 2-line text)
- Background: `#FFFFFF`
- Leading: 40pt circular icon container in the transaction-type color (Sent = `#003087`, Received = `#1C8B43`, Card = `#0070BA`, Reward = `#FFB81C`) with a white SF Symbol glyph centered
- Middle: recipient name in PayPal Sans Small 15pt 500 `#001435` above subtitle in PayPal Sans Small 13pt 400 `#687173`
- Trailing: $-amount in PayPal Sans Big 16pt 500 — `#1C8B43` if received (with "+"), `#001435` if sent (no prefix or "−")
- Divider: 0.5pt `#E5E8ED` bottom
- Tap: background fades to `#F5F7FA` over 150ms

**Wallet Item Card (linked card, bank, crypto)**
- Container: rounded rectangle 12pt radius, 1pt `#E5E8ED` border, white background
- Padding: 16pt
- Layout: card-art image leading (40pt × 26pt rounded rectangle showing the issuer logo) + "Visa debit card ending in 4242" in PayPal Sans Big 16pt 500 + "Linked Aug 2024" in PayPal Sans Small 13pt 400 `#687173`
- Tap: pushes card detail view

**Send Money Recipient Card**
- Circular avatar (48pt) + name in PayPal Sans Big 16pt 500 + email/phone in PayPal Sans Small 13pt 400 `#687173`
- Selected state: 2pt `#003087` border around the avatar

### Navigation

**Top Nav Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`
- Leading: PayPal "P-P" mark (24pt) in PayPal Blue and Sky gradient
- Trailing: notification bell (24pt) and a help "?" icon, or a profile circle
- Title: when present, sits center, PayPal Sans Big 17pt 500 `#001435`
- Border bottom: 0.5pt `#E5E8ED`

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt `#E5E8ED` top divider
- 5 tabs: Home / Send / Wallet / Activity / Finances
- Icon size: 24pt outlined / filled when active
- Active state: icon + label `#003087`
- Inactive: `#687173`
- Labels: PayPal Sans Small 11pt 500 +0.1pt letter spacing, visible always
- Tap haptic: `.selectionChanged()`
- The Send tab is sometimes rendered as a center-floating FAB-style circle with the wordmark — in newer designs it's just a regular tab

**Modal Sheet (Confirm Send, Filters)**
- Slides up from bottom over a dimmed black overlay (`rgba(0,0,0,0.6)`)
- Top corners: 16pt radius
- Background: `#FFFFFF`
- Drag indicator: 4pt × 32pt pill at top, `#E5E8ED`
- Dismiss: swipe down or tap outside

### Input Fields

**Text Field (Email, Phone, Card Number)**
- Background: `#FFFFFF`
- Border: 1pt `#E5E8ED` default, 2pt `#003087` focused, 2pt `#D20021` if error
- Corner radius: 8pt
- Height: 56pt
- Floating label: sits inside at 16pt PayPal Sans Small 400 `#687173` when empty; animates to 12pt 500 `#003087` at the top when focused or filled
- Text: PayPal Sans Big 16pt 400 `#001435`

**Search Bar**
- Background: `#EEF1F4` (Surface Gray 2)
- Height: 44pt
- Corner radius: 22pt (full pill)
- Leading icon: `magnifyingglass` 16pt `#687173`
- Placeholder: "Search transactions" in PayPal Sans Small 15pt 400 `#9DA3A6`

**Send Amount Field**
- Fullscreen modal, the $-amount is centered: enormous PayPal Sans Big 56pt 700 `#001435` tabular
- Currency selector below: "USD" in PayPal Sans Small 13pt 500 `#687173` with a chevron
- Numeric keypad at the bottom (standard iOS keypad)
- Below the keypad: "Send $X.XX" CTA that updates the button text live with the amount

### Distinctive Components

**The "P-P" Wordmark / Logomark**
- The PayPal wordmark uses a deliberate overlap of two italicized P's — the first in PayPal Sky (`#0070BA`), the second in PayPal Blue (`#003087`), where the second overlays the first to create a darker blended zone in the middle
- The compact "P-P" mark (without the full "PayPal" text) is a square logomark used in the top-left of most screens at 24-32pt
- NEVER render the wordmark in a single color — the dual-blue overlap IS the brand

**Activity Filter Chips (Activity tab)**
- Horizontal scroll row at the top of the Activity tab
- Chips: "All", "Sent", "Received", "Pending", "Card", "Refunds"
- Default: `#EEF1F4` background, `#687173` text, PayPal Sans Small 13pt 500
- Selected: `#003087` background, white text
- 32pt height, 16pt horizontal padding, 16pt corner radius

**Status Pills (on Activity rows or detail screens)**
- "Completed": `#E4F5EA` background, `#1C8B43` text, PayPal Sans Small 12pt 500
- "Pending": `#FFF6E0` background, `#A06B00` text
- "Failed": `#FCE5E8` background, `#D20021` text
- "Refunded": `#EEF1F4` background, `#687173` text
- 24pt height, 10pt horizontal padding, 4pt radius

**Send Money Success Screen**
- Full-screen confirmation: PayPal-blue checkmark icon (80pt) in a circle, then "$24.50 sent to Sarah Kim" in PayPal Sans Big 22pt 700 centered, then "Available now" subtitle
- A celebratory but restrained moment — no confetti (PayPal is institutional)
- Two buttons at the bottom: "Done" (primary blue) and "Send another" (outline)

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard horizontal margin: 16pt — institutional density, not airy
- Section vertical gap: 24pt between major sections; 16pt between subsections
- Card internal padding: 20pt

### Grid & Container
- Content width: full device width minus 16pt horizontal margins
- Cards and rows: 100% width within margins
- Activity rows: 100% width edge-to-edge (the divider provides separation, not margin)

### Whitespace Philosophy
- **Card-based composition**: most content sits inside rounded rectangle cards with shadows — PayPal reads as a wallet of cards
- **Moderate density**: 68pt activity rows are slightly taller than competitors because the rich icon needs room; not as dense as a Mint, not as airy as Airbnb
- **Generous CTA padding**: primary buttons are 48pt tall full-width — they're the focal point of every screen

### Border Radius Scale
- Square (0pt): rare
- Small (4pt): status pills, badges
- Standard (8pt): text fields, modal containers
- Comfortable (12pt): wallet item cards, news cards
- Soft (16pt): balance card, filter chips, modal sheet top corners
- Pill (24pt): primary CTAs (half of 48pt height)
- Full Pill (500pt): search bar, "Add Money" button when narrow
- Circle (50%): avatars, activity icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, section backgrounds, canvas |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Balance card, wallet item cards, news cards |
| Floating (Level 2) | `rgba(0,0,0,0.10) 0 4px 12px` | Send-to-recipient card when selected, modal triggers |
| Sheet (Level 3) | `rgba(0,0,0,0.20) 0 -8px 24px` | Bottom sheets, confirm modals |
| Overlay Dim | `rgba(0,0,0,0.60)` | Behind modal sheets |

**Shadow Philosophy**: PayPal uses slightly more elevation than purely flat tech products — the brand reads as a card wallet, so cards lift subtly from the canvas. But the lift is gentle (0.06-0.10 opacity), never aggressive. Compared to Cash App (zero shadows) PayPal is elevated; compared to a banking app's hard 3D, PayPal is restrained.

### Motion
- **Send button tap**: scale 1.0 → 0.97 → 1.0 spring (response 0.25, damping 0.7), `.impactOccurred(.medium)` haptic
- **Send confirmation**: success screen slides up with a 350ms spring; the checkmark icon scales 0.5 → 1.0 with a delayed 100ms; `.notificationOccurred(.success)` haptic
- **Activity row press**: background fades to `#F5F7FA` over 150ms, scale stays 1.0
- **Tab switch**: icon transitions between outline/filled variants over 200ms, label color animates `#687173` → `#003087`; `.selectionChanged()` haptic
- **Modal sheet drag**: 60fps drag-to-dismiss with a velocity-based snap at the bottom 100pt threshold
- **Floating label animation**: input field's placeholder lifts from inline (16pt) to floating (12pt at top-left) over 200ms when the field is focused or has content
- **Balance update (after Send)**: balance number animates with `.contentTransition(.numericText())` over 400ms to its new value

## 7. Do's and Don'ts

### Do
- Use PayPal Blue `#003087` for primary CTAs ("Send", "Continue") and active tab states — it's the core brand action color
- Use PayPal Sky `#0070BA` as the secondary accent — link color, icon color, gradient highlight
- Render the wordmark with the dual-blue P-P overlap — sky behind, blue front, NEVER a single color
- Use PayPal Sans Big for headings, balance amounts, button text — the rounded display weight is the brand voice
- Use PayPal Sans Small for body, metadata, transaction subtitles — Big at 14pt is wrong
- Make the balance card the visual anchor of the Wallet tab — white surface, 16pt radius, subtle shadow
- Use rich circular icons on Activity rows colored by transaction type (Sent = blue, Received = green, Card = sky, Reward = amber)
- Use tabular numerals on every $-amount — the Activity feed's vertical column of prices must align
- Use the soft-pill CTA shape (radius = half-height) for primary buttons — 48pt tall, 24pt radius
- Use `#001435` (almost-black with slight blue tint) for primary text, NOT pure black — the warmth aligns with the brand

### Don't
- Don't render the wordmark in a single solid color — the dual-blue P-P overlap is the brand
- Don't use pure black `#000000` for primary text — `#001435` (slight blue tint) is correct
- Don't use vivid greens or yellows as primary accents — green and amber are reserved for status (success / pending)
- Don't replace PayPal Sans with Helvetica or generic sans — the rounded terminals are intentional and warm
- Don't use sharp-corner buttons — primary CTAs are soft pills (radius equals half-height)
- Don't make activity rows shorter than 64pt — the rich icon plus 2-line text needs the room
- Don't use yellow or orange as a primary brand accent — PayPal is blue-first; amber is for warnings only
- Don't render the activity icon as monochrome gray — the transaction-type colors are part of the brand
- Don't crowd the Send Money screen — the giant amount needs to feel calm and confident
- Don't use the deep `#001C64` Cobalt outside of splash / illustration backgrounds — keep it reserved

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Balance hero caps at 32pt; balance card padding reduces to 16pt |
| iPhone 13/14/15 | 390pt | Standard 36pt balance hero; 20pt card padding |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected; nav bar sits below it |
| iPhone 15/16 Pro Max | 430pt | Balance hero at 40pt; activity row goes 72pt for more breathing room |
| iPad | 768pt+ | 2-column: balance + cards on left, activity on right; sidebar nav replaces tab bar |

### Dynamic Type
- Body text, activity subtitles, secondary labels scale fully with Dynamic Type
- Balance hero (36pt) scales but clamps at 48pt
- Send Amount hero (56pt) scales but clamps at 72pt
- Activity row amount (16pt) honors scaling up to 22pt max
- Tab labels (11pt) and filter chips (13pt) are fixed — layout-sensitive

### Orientation
- Portrait only on phones — PayPal is portrait-locked
- iPad supports landscape with the sidebar nav

### Touch Targets
- Activity row: full 68pt hit area
- Tab bar: 56pt full row with 24pt icons
- Primary CTA: 48pt height, full-width minus 32pt margins
- Filter chip: 32pt visual, 44pt minimum hit width
- Input field: 56pt height with 44pt actionable middle

### Safe Area Handling
- Top: nav bar respects safe area + Dynamic Island
- Bottom: tab bar pins above home indicator
- Modal sheets: top corners 16pt, bottom respects home indicator
- Sides: 16pt content insets on most screens

## 9. Quick Reference Cheat Sheet

### Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F5F7FA`
- Divider: `#E5E8ED`
- Text Primary: `#001435`
- Text Muted: `#687173`
- PayPal Blue: `#003087`
- PayPal Sky: `#0070BA`
- PayPal Cobalt: `#001C64`
- Success Green: `#1C8B43`
- Success Bg: `#E4F5EA`
- Error Red: `#D20021`
- Warning Amber: `#FFB81C`
- Dark Canvas: `#0A0E1A`
- Dark Surface 1: `#141A2A`
- Dark Text Primary: `#FFFFFF`

### Example Component Prompts
- "Build the PayPal balance card: white surface, full-width minus 16pt margins, 16pt corner radius, 1pt `#E5E8ED` border, `rgba(0,0,0,0.06) 0 2px 8px` shadow, 20pt internal padding. Top label 'PayPal balance' in PayPal Sans Small 13pt weight 500 `#687173`. Below: balance amount '$1,247.92' in PayPal Sans Big 36pt weight 700 `#001435` with tabular numerals. Bottom row: two pill buttons side-by-side — 'Add Money' (PayPal Blue `#003087` fill, white text, 24pt radius pill) and 'Transfer' (white fill, 1.5pt `#003087` border, blue text)."
- "Style a PayPal activity row: 68pt tall white row. Leading 40pt circular icon container in the transaction-type color (Sent = `#003087` PayPal Blue, Received = `#1C8B43` green, Card = `#0070BA` sky, Reward = `#FFB81C` amber) with a white SF Symbol glyph centered. Middle: recipient name 'Sarah Kim' in PayPal Sans Small 15pt weight 500 `#001435` above 'Yesterday, 3:42 PM · Completed' in PayPal Sans Small 13pt weight 400 `#687173`. Trailing right-aligned: amount in PayPal Sans Big 16pt weight 500 — green `#1C8B43` with '+' prefix if received, `#001435` no prefix if sent. 0.5pt `#E5E8ED` bottom divider."
- "Create the PayPal Send Money screen: fullscreen modal with the $-amount centered. Use PayPal Sans Big 56pt weight 700 in `#001435` with tabular numerals. Below the amount, a 'USD' currency chip in PayPal Sans Small 13pt 500 `#687173`. Standard iOS numeric keypad at the bottom. At the very bottom, a full-width 'Send $24.50' CTA in PayPal Blue `#003087` background, white PayPal Sans Big 17pt weight 700 text, 48pt height, 24pt corner radius. The button text updates live with the entered amount."
- "Render the PayPal P-P wordmark: two italicized 'P' letters at 32pt — the first 'P' in PayPal Sky `#0070BA`, the second 'P' overlapping the first by ~30% in PayPal Blue `#003087`. The overlap zone darkens to a deeper blue blend. NEVER render this in a single color — the dual-blue overlap IS the brand."
- "Build the PayPal bottom tab bar: 56pt + safe area, white background with 0.5pt `#E5E8ED` top divider. 5 tabs: Home, Send, Wallet, Activity, Finances. Each tab is a 24pt SF Symbol icon (outline default, filled when active) + an 11pt PayPal Sans Small weight 500 label letter-spaced +0.1pt. Active state both icon and label flip to `#003087` (PayPal Blue). Inactive state `#687173`."

### Iteration Guide
1. Canvas is `#FFFFFF`; use Surface Gray `#F5F7FA` only for section fills and `#EEF1F4` for input fills
2. PayPal Blue `#003087` is the primary action color — Send CTA, active tab, wordmark; PayPal Sky `#0070BA` is the secondary accent
3. Primary text is `#001435` (almost-black with slight blue tint), NOT pure black — the warmth aligns with the brand
4. PayPal Sans Big for hero / button / amount; PayPal Sans Small for body / metadata; tabular numerals on financial figures
5. Balance card is the anchor of the Wallet tab — white surface, 16pt radius, subtle shadow `rgba(0,0,0,0.06) 0 2px 8px`
6. Activity rows use rich circular icons colored by transaction type — Sent = blue, Received = green, Card = sky, Reward = amber
7. Primary CTAs are soft pills — radius equals half-height (48pt tall → 24pt radius)
8. The dual-blue P-P wordmark is brand-load-bearing — never render in a single color
9. Status pills use tinted backgrounds — `#E4F5EA` for Completed, `#FCE5E8` for Failed, `#FFF6E0` for Pending
10. Shadows are gentle (`0.06-0.10` opacity) — PayPal is a card wallet, not a tech-product flat plane
