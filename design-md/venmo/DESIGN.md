# Design System Inspiration of Venmo (iOS)

## 1. Visual Theme & Atmosphere

Venmo's iOS app is one of the few fintech apps that feels like a social network, not a bank — and that's the whole point. The first screen you see after login isn't a balance or a receipt, it's a feed of your friends' transactions written in plain English with emoji memos. "Alex paid Jordan 🍕🍺 Pizza Friday". The visual language is designed to make financial transactions feel casual, playful, and narratively interesting. The Venmo blue (`#008CFF`) is the hero color — saturated, friendly, slightly brighter than a traditional "banking blue" — and it does all the heavy lifting on CTAs, links, and branded moments. Beyond that, the app is remarkably restrained: clean white canvas, soft gray dividers, charcoal text, and a few semantic colors (green for received money, red for requests).

The layout rhythm is feed-first. Transaction rows stack vertically, each one telling a tiny social story: avatar | actor name | verb (paid/charged) | recipient name | emoji-rich memo | timestamp | comment-like-share row (Instagram-style). The typography cascades from this: a payment actor name is Medium weight, the verb is Regular, the recipient is Medium again, and the memo is Regular with emoji given generous breathing room. The emoji is not decorative here — it IS the primary signal of what the transaction was about, because Venmo has trained its users that "🍕" means pizza without needing any words. Payment amounts are never shown inline on the public feed (by design), only to the two parties.

Typography is Venmo Sans (internal, similar to Brown Std / Source Sans). It ships in weights 400 and 700 for most UI, with a custom Display cut for big moments — the balance on the Me tab (giant, bold, dollar sign + digits filled dynamically), the "You paid Alex $12.50" confirmation screen, and the sign-up welcome hero. The Display face is slightly geometric, slightly humanist, and heavy enough to feel substantial. The Pay / Request split button is the absolute center of the app and deserves the emphasis: it's a blue pill, 50pt tall, with two equal halves divided by a subtle white stroke, and it sits fixed at the bottom of the tab bar area.

**Key Characteristics:**
- Venmo Blue (`#008CFF`) as the singular brand color — bright, friendly, not a banking navy
- **Social transactions feed** — the defining signature: Instagram-style rows with like/comment on every payment
- Emoji-rich memos as the main "what was this for" cue — `🍕🍺 Pizza Friday` is canonical Venmo copy
- Pay / Request split pill button — blue, 50pt tall, at the center of the bottom navigation
- Large balance display on the Me tab — Venmo Display 48-64pt with $ prefix
- Venmo Sans (Brown-adjacent), weights 400/700 — humanist sans with warm geometry
- Checkmark confirmation animation on payment send — blue circle, animated checkmark stroke
- Light canvas-first — white or off-white with soft divider gray `#F2F2F2`
- Blue gradient hero sections on signup/onboarding — soft blue gradient behind big headlines
- QR code pay / scan — square QR with blue Venmo logo center, the preferred in-person flow

## 2. Color Palette & Roles

### Primary (Brand)
- **Venmo Blue** (`#008CFF`): The defining brand color. Primary CTAs, links, Pay button, send confirmation, tab active.
- **Lighter Blue** (`#3D95CE`): Hover / secondary blue tint, used in illustrations and marketing.
- **Deep Blue** (`#0078DE`): Pressed state on primary buttons.
- **Blue Tint** (`#E6F4FF`): Soft blue wash for promo banners, info callouts, pressed row background.
- **Blue Gradient** (hero screens): `linear-gradient(#008CFF 0%, #0078DE 100%)` — vertical or slightly diagonal, used on signup, onboarding, checkmark animation.

### Canvas & Text (Light)
- **Canvas White** (`#FFFFFF`): Primary background.
- **Surface Muted** (`#F7F7F7`): Card/row pressed state, quiet sectional backgrounds.
- **Divider** (`#F2F2F2`): Hairline dividers between feed rows.
- **Divider Strong** (`#E0E0E0`): Group separators, setting row bottoms.
- **Text Primary** (`#2F3033`): Primary body, names, headlines (slightly warm charcoal, not pure black).
- **Text Secondary** (`#6B6E76`): Timestamps, metadata, helper text.
- **Text Tertiary** (`#9AA0A8`): Disabled states, placeholders.

### Dark Mode
- **Dark Canvas** (`#1A1A1A`): Primary dark canvas.
- **Dark Surface 1** (`#242424`): Elevated cards.
- **Dark Surface 2** (`#2E2E2E`): Sheets, higher elevation.
- **Dark Divider** (`#2A2A2A`).
- **Dark Text Primary** (`#FFFFFF`).
- **Dark Text Secondary** (`#A0A0A0`).

### Semantic
- **Received Green** (`#4BB543`): "+$24.50" payment-received amounts, success states.
- **Charge Red** (`#D32E2E`): "-$24.50" payment-sent amounts in your activity, error validation, destructive actions.
- **Pending Orange** (`#F5A623`): Pending transactions, "Scheduled" status.
- **Info Blue Link** (`#008CFF`): In-body links, comment @mentions.

### Hero Illustrative
- **Sunshine Yellow** (`#FFE066`): Accent on marketing illustrations and occasional emoji overlays.
- **Coral** (`#FF8E72`): Occasional accent in reward / promo graphics.

## 3. Typography Rules

### Font Family
- **Primary (UI)**: `Venmo Sans` (internal, ~Brown Std / Source Sans lineage) — weights 400 / 600 / 700
- **Display (Hero moments)**: `Venmo Display` — used for balance numbers, sign-up hero, confirmation amounts
- **Legacy / fallback**: `Brown Std`, `Source Sans Pro` — many early brand assets used Brown specifically
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Source Sans Pro', Helvetica, Arial, sans-serif`
- **CJK / Non-Latin**: Noto Sans fallback

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Balance Display | Venmo Display | 56pt | 700 | 1.0 | -0.8pt | Me-tab balance hero ("$124.50") |
| Payment Confirm | Venmo Display | 44pt | 700 | 1.0 | -0.5pt | Payment amount on confirm screen |
| Sign-up Hero | Venmo Display | 36pt | 700 | 1.1 | -0.4pt | Welcome headline |
| Screen Title | Venmo Sans | 22pt | 700 | 1.2 | -0.2pt | "Activity", "Me", "Pay or Request" |
| Feed Transaction Line | Venmo Sans | 15pt | 400 | 1.4 | 0pt | Mixed weights inline (see below) |
| Feed Actor / Recipient | Venmo Sans | 15pt | 600 | 1.4 | 0pt | Names in feed row (bolded) |
| Feed Memo | Venmo Sans | 15pt | 400 | 1.4 | 0pt | Memo + emoji text |
| Timestamp | Venmo Sans | 13pt | 400 | 1.3 | 0pt | "2h ago", "Nov 12" |
| Comment Body | Venmo Sans | 13pt | 400 | 1.4 | 0pt | Comments under a transaction |
| Amount (Activity) | Venmo Sans | 17pt | 700 | 1.1 | -0.1pt | "+$12.50" / "-$12.50" |
| Button (Primary) | Venmo Sans | 16pt | 700 | 1.0 | 0.2pt | "Pay", "Request", "Confirm" |
| Button (Secondary) | Venmo Sans | 15pt | 600 | 1.0 | 0pt | "Cancel", outline buttons |
| Input Field | Venmo Sans | 16pt | 400 | 1.3 | 0pt | Memo input, amount input |
| Amount Input (Hero) | Venmo Display | 72pt | 700 | 1.0 | -1.0pt | Central amount input on Pay screen |
| Tab Label | Venmo Sans | 11pt | 600 | 1.0 | 0.1pt | Tab bar labels |
| Meta / Helper | Venmo Sans | 12pt | 400 | 1.3 | 0pt | Terms, helper text |

### Principles
- **Feed rows mix weights inline**: actor name bold, verb regular, recipient bold, memo regular — emphasis flows naturally through a single sentence
- **Display face reserved for numbers and welcome moments**: big-number moments (balance, payment confirm, amount input) earn the Display cut
- **Dollar sign glued to number**: no space between `$` and the digits — "$24.50" not "$ 24.50"
- **Emoji in memos kept at native OS emoji rendering**: don't re-render them; use the system emoji font stack
- **Weights concentrated at 400 / 600 / 700**: Regular, semibold, bold
- **No italics**: straight upright throughout
- **Tabular numerals on balances and amounts**: so columns align in activity lists

## 4. Component Stylings

### Buttons

**Pay / Request Split Pill (The Signature)**
- Shape: Pill (full 50pt rounded), split into two halves by a 1pt white divider
- Width: full-width minus 16pt side margins
- Height: 50pt
- Background: `#008CFF` Venmo Blue, solid fill
- Text: `#FFFFFF`, Venmo Sans 16pt weight 700 with 0.2pt tracking
- Left half: "Pay" with optional leading arrow-right icon; Right half: "Request" with optional leading arrow-left icon
- Pressed state per half: background darkens to `#0078DE`, scale 0.98, haptic medium
- Shadow: `rgba(0,140,255,0.25) 0 4px 12px` (branded blue shadow)

**Primary Confirm ("Send $24.50")**
- Background: `#008CFF`
- Text: `#FFFFFF`, Venmo Sans 16pt w700
- Height: 52pt
- Corner radius: 26pt (full pill)
- Full-width minus 16pt margins
- Pressed: `#0078DE`, scale 0.98, success haptic on send commit
- Loading: white activity indicator replaces label

**Outline Secondary**
- Background: transparent
- Border: 1.5pt `#008CFF`
- Text: `#008CFF`, Venmo Sans 15pt w600
- Height: 44pt, corner radius 22pt (pill)
- Pressed: background `#E6F4FF`, light haptic

**Destructive ("Cancel payment")**
- Background: transparent
- Text: `#D32E2E`, Venmo Sans 15pt w600
- Height: 44pt
- No border
- Used sparingly — Venmo prefers neutralized language

**Icon Button (Feed Row)**
- Size: 32pt hit area (fits beside timestamps in feed rows)
- Icon: 18pt `#6B6E76` default, `#008CFF` on tap (like / comment)
- No background
- Used for: Like (heart), Comment (bubble), Share (arrow up from square)

### Cards & Containers

**Feed Transaction Row (THE Signature Component)**
- Padding: 12pt vertical, 16pt horizontal
- Background: `#FFFFFF`
- Leading: 40pt circular avatar (actor's photo)
- Main content column (flex-1):
  - Line 1 (transaction story): `[Actor Bold] paid [Recipient Bold] · 2h`
  - Line 2 (memo): `🍕🍺 Pizza Friday` (15pt w400)
  - Line 3 (interaction row): Heart icon + count | Comment icon + count
- Trailing: no amount shown (public feed privacy)
- Divider: 1pt `#F2F2F2` below each row
- Pressed state: background `#F7F7F7`

**Activity Row (Your Own Transactions)**
- Same structure but includes amount on trailing side
- Amount in Venmo Sans 17pt w700, colored:
  - `+$24.50` in `#4BB543` (you received)
  - `-$24.50` in `#2F3033` (you paid — neutral)
  - Pending: `$24.50` in `#F5A623` with "Pending" chip
- Status chip: pill with colored text

**Balance Card (Me Tab Hero)**
- Full-width, 180pt tall
- Background: gradient `#008CFF → #0078DE` vertical
- Content:
  - Top-left: "Venmo balance" in Venmo Sans 14pt w400 white 80%
  - Center: "$124.50" in Venmo Display 56pt w700 white (with tabular digits)
  - Bottom row: "Transfer to bank" pill (white bg, blue text) + "Add money" pill (white bg, blue text)
- Corner radius: 20pt
- Margin: 16pt horizontal

**Payment Confirmation Screen**
- Full-screen takeover after Pay tap
- Top: large blue gradient background
- Center: animated checkmark circle (80pt circle, blue background, white check stroke animates in 400ms)
- Below: "Sent" in Venmo Display 28pt w700 white + amount "$24.50" in 44pt Display w700 + "To Alex" in 16pt w400
- Confetti particle animation around the checkmark (200ms burst)
- "Done" pill button in white with blue text below

**Amount Input Screen (Pay Flow)**
- Pure canvas white, no decorations
- Centered: giant `$24.50` entry field in Venmo Display 72pt w700 `#2F3033`
- Dollar sign glued, digit-by-digit keypad taps update live
- Below: "Add memo..." text input, full-width, flat (no border, just underline on focus)
- Below memo: 👥 emoji picker with 12 popular emojis for quick selection
- Fixed bottom: "Pay $24.50" button (solid blue pill, 52pt, full-width)

### Navigation

**Top Nav (Screen Header)**
- Height: 44pt + safe area
- Background: `#FFFFFF` with 0.5pt bottom border `#F2F2F2` when content scrolls
- Leading: screen title "Activity" in Venmo Sans 22pt w700 `#2F3033`
- Trailing: 24pt `#008CFF` action icon (scan QR, add friend, etc.)
- No centered title; title is left-aligned (iOS large-title style but smaller)

**Bottom Tab Bar (with Pay/Request Pill)**
- Height: 64pt + safe area (taller to accommodate the split pill)
- Background: `#FFFFFF` with 0.5pt top border `#F2F2F2`
- 5 tab slots, but the center "Pay/Request" is a full-width pill overlaying the tab bar rather than a tab icon
- Tabs arranged: Home feed | Incomplete | [Pay/Request pill — spans center 60%] | Cards | Me
- Icon size: 24pt
- Active: `#008CFF` fill + label same color
- Inactive: `#6B6E76` outline icons
- Labels: Venmo Sans 11pt w600, always shown

**Scan / QR Sheet**
- Full-screen takeover with black background
- Center: QR viewfinder with blue corner brackets (animated scanning line)
- Bottom: "Show my QR" pill to flip to your own personal QR (appears on a soft blue gradient card)
- Your QR: centered 240×240pt QR code with Venmo logo in the middle circle (white background, blue logo)

### Input Fields

**Amount Input**
- Flat, no border
- Dollar sign auto-prepended
- Live digit update as user types
- Hero size: Venmo Display 72pt w700 on Pay screen
- Regular size: 22pt w700 on inline forms

**Memo Input**
- No border default
- Placeholder: "What's it for?" in Venmo Sans 16pt w400 `#9AA0A8`
- Emoji keyboard access via the system keyboard's emoji switcher
- Recent/suggested emoji row above the keyboard when active

**Search Input (Find Friends / Recipients)**
- Background: `#F2F2F2`
- Height: 40pt
- Corner radius: 20pt (pill)
- Leading: 16pt magnifying glass `#6B6E76`
- Placeholder: "Search people, businesses" in Venmo Sans 16pt w400 `#9AA0A8`
- Focus: `#FFFFFF` background + 1pt `#008CFF` border

**Comment Input (Under Transaction)**
- Flat, no border
- Placeholder: "Add a comment..." in 14pt w400 `#9AA0A8`
- Send button appears when text entered: 28pt blue circular button with `arrow.up.circle.fill` glyph

### Distinctive Components

**Feed Row Transaction Line (Inline Mixed Weights)**
- Single wrapped line with mixed typography:
  - `[Actor Bold 15pt w600]` `paid [Regular 15pt w400]` `[Recipient Bold 15pt w600]` ` · [Timestamp 13pt w400 #6B6E76]`
- Example: **Alex** paid **Jordan** · 2h
- Below: memo with emojis (15pt w400), then like/comment row

**Emoji Memo Picker**
- Above the memo input field on the Pay screen
- Horizontal scroll of 12 popular emojis (🍕 🍺 🚗 💰 🎉 ☕ 🏠 🎁 🛒 ✈️ 🎬 💵)
- Tap: inserts emoji into memo field, light haptic
- Selected emoji gets a soft blue tint circle background

**Pay/Request Split Pill at Tab Bar**
- Fixed in the center slot of the bottom nav area, overlapping the tab bar
- Two-button pill, `#008CFF` background, white divider between halves
- Left "Pay" | Right "Request"
- Tapping either opens the central action flow

**Payment-Sent Checkmark Animation**
- 80pt circle, `#008CFF` fill
- Checkmark drawn with a stroke-on animation over 300ms (ease-out)
- Confetti particle burst radiates from the circle (8-12 particles, colored blue/white/yellow, 800ms total)
- Success haptic on completion

**Social Like / Comment Row**
- Below every feed transaction
- Heart icon (18pt, `#6B6E76` default, `#008CFF` tapped) + "3" count in 13pt w400 `#6B6E76`
- Spacing 16pt between icons
- Comment icon same treatment
- Long-press heart: shows 6 reaction emojis (similar to Messenger)

**Friend Request Card**
- On the Me tab, "Friend requests" section
- 3-column grid of 100pt-wide cards with avatar, name, "Accept" + "Decline" buttons
- Blue accept, gray decline

**Business Profile Badge**
- Small blue verified checkmark next to business account names (like Twitter)
- `#008CFF` fill, white check
- Sits 4pt right of the name

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Standard margin: 16pt horizontal
- Feed row padding: 12pt vertical, 16pt horizontal
- Between cards: 12pt gap

### Grid & Container
- Feed: single-column full-width transaction rows
- Activity: same feed structure but with your own filter
- Me tab: balance card top, sections stacked below (Direct deposit, Tax hub, Cards, Settings)
- Pay screen: centered amount input, 16pt horizontal margin

### Whitespace Philosophy
- **Feed density is friendly**: rows are 80-100pt tall, dense enough to scan 5-6 transactions per screen but not cramped
- **Pay screen is spacious**: the amount is the entire screen — huge Display numerals centered, no decorations
- **Balance card breathes**: 20pt padding inside the gradient card, generous around the Display number
- **Settings tight**: standard iOS inset list rows (44pt tall with 16pt horizontal padding)

### Border Radius Scale
- Square (0pt): Nav bars, full-bleed backgrounds
- Subtle (4pt): Tag chips, small inline badges
- Standard (12pt): Inner cards, small buttons
- Comfortable (16pt): Sheet top corners, modal cards
- Card (20pt): Balance card, hero sections
- Pill (22-26pt): Secondary buttons, search inputs
- Full Pill (50pt/full): Primary CTAs, Pay/Request split
- Circle (50%): Avatars, action buttons, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Feed rows, canvas surfaces |
| Hairline (Level 1) | 1pt `#F2F2F2` border | Row separators, section tops |
| Small Lift (Level 2) | `rgba(0,0,0,0.06) 0 1px 3px` | Cards in friend-request grid |
| Primary Button (Level 3) | `rgba(0,140,255,0.25) 0 4px 12px` | Pay/Request split pill (branded blue shadow) |
| Sheet (Level 4) | `rgba(0,0,0,0.15) 0 -4px 20px` | Pay sheet, scan QR sheet |
| Confirmation Hero (Level 5) | Gradient + confetti | Payment-sent full-screen |
| Dim Overlay | `rgba(0,0,0,0.5)` | Behind modals, QR scanner dark |

**Shadow Philosophy**: Venmo's shadows are minimal — the app is mostly flat. The one visible shadow moment is the Pay/Request pill's branded blue glow (`rgba(0,140,255,0.25)`), which signals "press me, this is the important one". Everywhere else, shadows are whispers: 6% black at 1pt, just enough to lift friend-request cards off the canvas. The payment confirmation screen uses a full gradient instead of a shadow — the saturation is the depth.

### Motion
- **Pay/Request split press**: scale 0.98 spring + background darkens per half tapped, medium haptic
- **Amount input typing**: digits appear instantly, subtle 50ms scale bump per character (1.0 → 1.05 → 1.0)
- **Payment sent checkmark**: blue circle scales in 0→1.0 over 200ms spring, check stroke draws 300ms ease-out, confetti burst 800ms with particles scattering, success haptic
- **Feed row like**: heart scales 1.0 → 1.2 → 1.0 over 300ms, color changes to blue, light haptic
- **Pull to refresh**: minimal blue activity indicator
- **Screen transitions**: iOS default slide, no custom
- **Keyboard show**: amount input stays fixed, keyboard slides up under it
- **QR scanner open**: full-screen slide up 300ms ease-out, camera fades in
- **Balance reveal**: on Me tab mount, balance number animates from "$0.00" → actual value over 400ms (tick animation), tabular digit transition

## 7. Do's and Don'ts

### Do
- Use Venmo Blue (`#008CFF`) as the singular brand color — bright, friendly, not banking-navy
- Render the **feed transaction line** as mixed weights: actor bold, verb regular, recipient bold
- Use emoji in memos as the primary "what this is for" signal — `🍕🍺 Pizza Friday`
- Keep the Pay / Request split pill pinned at the bottom as the defining CTA
- Render amounts in Venmo Display: 72pt on Pay screen, 56pt on balance, 44pt on confirm
- Use soft `#2F3033` for text primary — warmer than pure black, on-brand
- Show tabular numerals on balances and amounts — columns must align
- Use green (`#4BB543`) for received money, red (`#D32E2E`) for requests/errors
- Trigger medium haptic on Pay tap, success haptic + confetti on send commit
- Render the payment-sent checkmark as an animated stroke drawing inside a blue circle

### Don't
- Don't show amounts on the public feed — Venmo's privacy model hides them except to the two parties
- Don't use navy or dark blue for the brand — Venmo Blue is saturated and bright (`#008CFF`)
- Don't separate the dollar sign from the number — it's "$24.50", one unit
- Don't replace emoji in memos with your own icon set — system emoji is the brand
- Don't use pure black for text — `#2F3033` is warmer and softer
- Don't animate aggressively — Venmo motion is calm (spring dampings 0.7-0.85)
- Don't use drop shadows beyond 15% opacity — the app is flat with one branded shadow (Pay button)
- Don't hide the Pay / Request pill — it's the permanent home-base action
- Don't skip the confirmation animation — the checkmark and confetti are the emotional payoff
- Don't use gradients on regular CTAs — gradient is reserved for balance card and hero moments

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Balance card shrinks to 160pt, Display balance scales to 48pt |
| iPhone 13/14/15 | 390pt | Standard 180pt balance card, 56pt balance number |
| iPhone 15/16 Pro | 393pt | Dynamic Island pushes nav 8pt lower |
| iPhone 15/16 Pro Max | 430pt | Balance scales to 64pt, Pay button spans more horizontal space |
| iPad | 768pt+ | Feed constrains to 480pt centered, balance card max 600pt |

### Dynamic Type
- Feed rows, body, comments: full scale
- Balance Display: scales to 72pt max, then truncates
- Amount input Display: fixed 72pt (layout-sensitive)
- Tab labels: fixed 11pt
- Button labels: scale to 120% then truncate

### Orientation
- Home / Activity / Me: **portrait-locked** (social feed is portrait content)
- Pay / Request: **portrait-locked**
- Receipt / Transaction detail: **portrait-locked**
- QR scanner: **portrait-locked**

### Touch Targets
- Pay/Request split pill: 50pt tall — both halves 44pt minimum touch area
- Feed rows: full-row tappable, 80-100pt tall
- Like / Comment icons: 32pt hit area with 18pt glyph
- Avatar: 40pt with 44pt hit area
- Tab bar icons: 24pt icon inside 44pt effective hit

### Safe Area Handling
- Top: safe area respected on nav
- Bottom: tab bar + Pay pill extend to home indicator; pill padded 24pt above safe-area-bottom
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface muted: `#F7F7F7`
- Divider: `#F2F2F2`
- Text primary: `#2F3033`
- Text secondary: `#6B6E76`
- Venmo Blue (primary): `#008CFF`
- Deep Blue (pressed): `#0078DE`
- Blue tint (soft): `#E6F4FF`
- Received green: `#4BB543`
- Charge red: `#D32E2E`
- Pending orange: `#F5A623`
- Dark canvas: `#1A1A1A`

### Example Component Prompts
- "Create a SwiftUI Venmo feed transaction row: 16pt horizontal / 12pt vertical padding, `#FFFFFF` background. Leading 40pt circular avatar. Main column: line 1 mixes weights — 'Alex' in Venmo Sans 15pt w600 `#2F3033`, ' paid ' in 15pt w400, 'Jordan' in 15pt w600, ' · 2h' in 13pt w400 `#6B6E76`. Line 2: emoji memo '🍕🍺 Pizza Friday' in 15pt w400. Line 3: like icon (18pt heart, `#6B6E76` default, `#008CFF` tapped) + count + comment icon + count, 16pt gap. Divider 1pt `#F2F2F2` below."
- "Build the Pay / Request split pill: fixed above the tab bar with 16pt side margins, full-width pill 50pt tall, background `#008CFF`, corner radius 25pt. Two equal halves divided by a 1pt white vertical line. Text 'Pay' left / 'Request' right in Venmo Sans 16pt w700 white with 0.2pt tracking. Blue shadow `rgba(0,140,255,0.25) 0 4px 12px`. Pressed per half: background `#0078DE`, scale 0.98, medium haptic."
- "Design the amount input screen for Pay: centered `$24.50` in Venmo Display 72pt w700 `#2F3033` with tabular numerals and dollar sign glued to the number. Below: memo input 'What's it for?' in Venmo Sans 16pt w400 placeholder `#9AA0A8`, no border. Above keyboard: horizontal scroll of 12 emoji chips (🍕 🍺 🚗 💰 🎉 ☕ 🏠 🎁 🛒 ✈️ 🎬 💵), tap to insert. Fixed bottom: 'Pay $24.50' pill button `#008CFF`, 52pt tall, white text Venmo Sans 16pt w700."
- "Build the payment-sent confirmation screen: full-screen blue gradient `#008CFF → #0078DE` vertical. Center: 80pt circle `#008CFF` solid fill with white checkmark drawn via a stroke animation (400ms ease-out). 8-12 white/blue particles burst outward from the circle (800ms). Below: 'Sent' in Venmo Display 28pt w700 white, amount '$24.50' in Venmo Display 44pt w700 white, 'To Alex' in Venmo Sans 16pt w400 white 80%. 'Done' white pill button at bottom with blue text."
- "Create the balance card for the Me tab: 180pt tall full-width card with 16pt horizontal margin, 20pt corner radius. Background: vertical linear gradient `#008CFF → #0078DE`. Top-left: 'Venmo balance' in Venmo Sans 14pt w400 white 80%. Center: '$124.50' in Venmo Display 56pt w700 white with tabular digits. Bottom row: 'Transfer to bank' white pill button (blue text, Venmo Sans 15pt w600) + 'Add money' white pill button."
- "Design the emoji memo picker: horizontal scroll above the keyboard on the Pay screen, `#FFFFFF` background. 12 emoji chips: 🍕 🍺 🚗 💰 🎉 ☕ 🏠 🎁 🛒 ✈️ 🎬 💵 in system emoji font, each 32pt tall, 40pt wide, 8pt gap. Tap: insert emoji into memo field, light haptic, brief 150ms `#E6F4FF` background flash on the tapped chip."

### Iteration Guide
1. Venmo Blue `#008CFF` is the only brand color — saturated, bright, friendly (NOT banking-navy)
2. The feed transaction row is the defining component — mixed weights inline, emoji-rich memo, like/comment row below
3. Amounts are NEVER shown on the public feed — only to the two parties (privacy model)
4. Pay / Request split pill is the permanent anchor — pinned above the tab bar, blue with white divider
5. Use emoji as the primary "what was this for" cue — `🍕🍺 Pizza Friday` is canonical
6. Display face for numbers: 72pt on Pay entry, 56pt on balance, 44pt on confirm — never for body
7. Text primary is `#2F3033` — warmer than black, softer on the eye
8. The payment-sent animation is the emotional payoff: stroke-drawing checkmark + confetti burst + success haptic
9. Tabular numerals on all amounts so columns align in activity lists
