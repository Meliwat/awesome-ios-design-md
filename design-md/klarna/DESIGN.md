# Design System Inspiration of Klarna (iOS)

## 1. Visual Theme & Atmosphere

Klarna's iOS app is a soft, friendly shopping-and-payments space whose whole personality is *smooth*. The canvas is clean white (`#FFFFFF`) with a barely-there pink-tinted surface (`#FFF2F5`) that warms grouped content without shouting. The brand's signature soft pink (`#FFB3C7`) — the "Klarna Pink" — is the action color, and it always carries near-black text (`#0B051D`), never white. The pairing reads as approachable and modern: this is buy-now-pay-later as something light and reassuring, not a stern bank.

The accent system is a pink/black duet. Klarna Pink (`#FFB3C7`) is the primary CTA, the active state, and the highlight; near-black ink (`#0B051D`) is the structural color for headings, amounts, and the dark hero moments. There is no blue, no gradient, no second accent — the restraint, plus the unusually *soft* corner radii and slow, gliding transitions, is what makes the experience feel unmistakably Klarna.

Typography is Klarna Text, a clean, slightly rounded humanist sans (Inter is the closest free substitute). It's calm and legible — amounts at weight 700, body relaxed at 400. The most expressive moment is the **Pay-in-4 schedule timeline**: four dated dots connected by a track, the first filled pink ("paid"), the rest outlined, each with a date and an amount — a literal picture of "split into 4, nothing scary."

**Key Characteristics:**
- Bright white canvas (`#FFFFFF`) with a soft pink-tinted surface (`#FFF2F5`)
- Klarna Pink (`#FFB3C7`) as the single action color — always with near-black text, never white
- Near-black ink (`#0B051D`) for structure: headings, amounts, dark hero panels
- Pay-in-4 schedule timeline — 4 dated dots, the signature payment-plan device
- In-app shopping browser card — Klarna wraps any store in its own chrome
- Smooth pink CTA with generous radius — soft, pill-like, calm
- Order list rows — clean, scannable purchase history with plan status
- Klarna Text (proprietary humanist sans), weights 400 / 600 / 700
- Bottom tabs: Shop, Payments, Rewards, Profile
- Smooth slide transitions — screens glide, nothing snaps

## 2. Color Palette & Roles

### Primary
- **Klarna Pink** (`#FFB3C7`): Primary CTA ("Pay now", "Continue"), active tab, highlight chip, selected state, the paid dot on the schedule. Always paired with near-black text.
- **Klarna Pink Pressed** (`#F49CB4`): Pressed/active state for the pink CTA and the pressed pink chip.
- **Klarna Pink Tint** (`#FFF2F5`): Soft surface — grouped wells, selected-row wash, subtle highlight bands.
- **Black** (`#0B051D`): Structural near-black — headings, amounts, dark hero panels, primary ink.
- **Black Hover** (`#1A1330`): Pressed state on dark/black surfaces and dark buttons.

### Canvas & Surfaces
- **Canvas White** (`#FFFFFF`): Primary page background, card fill.
- **Surface Pink** (`#FFF2F5`): Soft pink-tinted well — section backgrounds, grouped containers, the area behind plan modules.
- **Surface Neutral** (`#F6F4F8`): Neutral input fill, skeleton placeholders.
- **Divider** (`#F0DDE3`): Hairline rules between rows and around cards (a warm, pink-leaning grey).
- **Border Strong** (`#E2D2D8`): Input outline at rest, segmented-control border.
- **Black Surface** (`#0B051D`): The dark hero / card-back panel background.

### Text
- **Text Primary** (`#0B051D`): Body text, primary amounts, row titles (a deep near-black with a violet cast).
- **Text on Pink** (`#0B051D`): Text on Klarna Pink fills — always the near-black, never white.
- **Text on Black** (`#FFFFFF`): Text on the dark hero panel.
- **Text Secondary** (`#6E6878`): Metadata, helper text, plan sub-labels.
- **Text Tertiary** (`#9B96A3`): Disabled labels, placeholders, low-emphasis captions.

### Semantic
- **Success Green** (`#0E8A4F`): "Paid", completed plan, order delivered.
- **Upcoming Amber** (`#A9700E`): "Due in 5 days", upcoming-payment reminder.
- **Error Red** (`#C8102E`): Failed payment, declined, form errors.
- **Info Black** (`#0B051D`): Informational emphasis inside plan breakdowns.
- **Schedule Track** (`#F0DDE3`): The connecting line behind the Pay-in-4 dots when not yet paid.

### Dark Mode (Limited Use)
Klarna's iOS app is light-first; the dark hero/card-back already supplies a dark moment within the soft layout. A full dark variant exists for system dark mode but the canonical experience is bright with pink accents.
- **Dark Canvas** (`#0B051D`)
- **Dark Surface** (`#171127`)
- **Dark Text** (`#F4F2F7`)
- **Klarna Pink (unchanged)** (`#FFB3C7`) — reads well on dark, still near-black text on fills

## 3. Typography Rules

### Font Family
- **Primary**: `Klarna Text` (proprietary humanist sans, Regular / Medium / Bold)
- **Display**: `Klarna Display` used for the largest hero numbers (optional optical variant)
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures mandatory in amounts, the Pay-in-4 schedule, and order totals
- **CJK/Non-Latin**: Falls to system stack — Klarna ships across EU/US with broad localization

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Amount (Hero) | Klarna Display | 36pt | 700 | 1.05 | -0.5pt | Total due / order total on hero, tabular |
| Screen Title (Large) | Klarna Text | 28pt | 700 | 1.15 | -0.4pt | "Payments", "Shop" nav title |
| Section Header | Klarna Text | 22pt | 700 | 1.2 | -0.3pt | "Upcoming payments", "Your orders" |
| Plan Amount | Klarna Text | 22pt | 700 | 1.1 | -0.2pt | Per-installment amount, tabular |
| Subsection | Klarna Text | 18pt | 600 | 1.3 | -0.1pt | Merchant / group titles |
| Amount (Row) | Klarna Text | 16pt | 600 | 1.2 | 0pt | Order amount on a row, tabular |
| Body / Title | Klarna Text | 16pt | 600 | 1.3 | 0pt | Order title, row title |
| Body | Klarna Text | 15pt | 400 | 1.5 | 0pt | Descriptions, plan explanations |
| Button (Primary) | Klarna Text | 16pt | 700 | 1.0 | 0pt | Pink CTA label |
| Meta / Subtitle | Klarna Text | 13pt | 400 | 1.3 | 0pt | "Due 14 Jun", "4 payments · biweekly" |
| Label (UPPER) | Klarna Text | 11pt | 700 | 1.2 | 0.5pt | "TOTAL", section labels |
| Tab Label | Klarna Text | 11pt | 600 | 1.0 | 0.1pt | Bottom tab bar labels |
| Caption | Klarna Text | 11pt | 400 | 1.3 | 0pt | Legal, "No interest if paid on time" |

### Principles
- **Calm hierarchy**: Big where it matters (amounts, titles at 700), relaxed body at 400 — nothing aggressive
- **Tabular figures everywhere money appears**: Schedule amounts, order totals, plan lines align to the decimal
- **Near-black is the structural ink**: Headings and amounts use `#0B051D`, not pure black
- **Pink is never type**: Klarna Pink is a fill/highlight; it always sits *behind* near-black text
- **Soft tracking**: Negative tracking only on large numbers/titles (-0.3 to -0.5pt); body sits at 0pt with relaxed 1.5 line-height
- **Dynamic Type respected** on titles, body, amounts; tab labels and uppercase labels fixed

## 4. Component Stylings

### Buttons

**Primary CTA ("Pay now" / "Continue")**
- Background: `#FFB3C7`
- Text: `#0B051D` (near-black — never white on pink)
- Height: 56pt, full-width; corner radius 28pt (full, very soft pill)
- Pressed: background `#F49CB4`, scale 0.98, soft haptic
- Disabled: background `#F6F4F8`, text `#9B96A3`

**Black Button (secondary primary, e.g. "Add card")**
- Background: `#0B051D`
- Text: `#FFFFFF`
- Height: 56pt, radius 28pt
- Pressed: `#1A1330`

**Outline Button ("See details")**
- Background: transparent
- Text: `#0B051D`, 1.5pt border `#0B051D`
- Height: 56pt, radius 28pt
- Pressed: background `#FFF2F5`

**Text Link**
- Klarna Text 15pt weight 600, color `#0B051D`, underline on press
- Pressed: `#1A1330`

**Icon Button (settings, share, help)**
- 24pt glyph, 44pt hit area
- Default `#6E6878`; active `#0B051D`

**Quick Action (Shop: Browse, Pay, Rewards, Card)**
- 56pt circle, background `#FFF2F5`, glyph 22pt `#0B051D`
- The primary action circle uses `#FFB3C7` fill with a near-black glyph
- Label below 13pt `#6E6878`

### Cards & Containers

**Black Hero / Payment Summary (signature dark moment)**
- Full-width panel, background `#0B051D`, corner radius 24pt, padding 24pt
- "TOTAL DUE" label 11pt `#FFB3C7` uppercase, amount 36pt w700 white tabular
- A pink "Pay now" affordance and "Next payment 14 Jun" subtitle in `#9B96A3`

**Pay-in-4 Schedule Card (signature)**
- Card `#FFFFFF` (or on `#FFF2F5` well), 1pt `#F0DDE3`, radius 20pt, padding 20pt
- A horizontal timeline: 4 dots connected by a 2pt track
- Dot states: **paid** = filled `#FFB3C7` with a near-black check; **next/due** = `#FFFFFF` with a 2pt `#0B051D` ring; **upcoming** = `#FFFFFF` with a 2pt `#F0DDE3` ring
- Under each dot: date (13pt `#6E6878`) and amount (15pt w700 `#0B051D`, tabular)
- The track between paid dots is `#FFB3C7`; ahead of "now" it's `#F0DDE3`
- This is Klarna's centerpiece — "split in 4" made literal and calm

**Order Row**
- Height ~72pt
- Leading: 44pt rounded-square merchant logo (radius 12pt) on `#FFF2F5`
- Center: merchant 16pt w600 `#0B051D` + meta "2 items · Pay in 4" 13pt `#6E6878`
- Trailing: amount 16pt w600 `#0B051D` tabular + a small status pill ("Paid" `#0E8A4F`, "Due 14 Jun" `#A9700E`)
- Pressed: `#FFF2F5`; divider `#F0DDE3`

**In-App Shopping Browser Card (signature)**
- Klarna wraps a merchant site in its own chrome: a rounded top bar with the merchant URL pill, a Klarna pink "Pay with Klarna" footer bar, and a draggable card sheet (radius 24pt top corners) over a dimmed page
- The footer "Pay with Klarna" bar is `#FFB3C7` with near-black text and a smooth slide-up entrance

**Plan Detail Sheet**
- Bottom sheet `#FFFFFF`, top grabber, radius 24pt top corners
- The Pay-in-4 schedule at top, then itemized lines (label ←→ value, tabular), then a pink "Pay now" CTA

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, 0.5pt top border `#F0DDE3` (opaque — calm and clear)
- Tabs: Shop, Payments, Rewards, Profile
- Icon size: 24pt
- Active: icon + label `#0B051D` (filled icon); a soft `#FFB3C7` pill/dot can mark the active tab
- Inactive: `#9B96A3`
- Labels: Klarna Text 11pt weight 600, always shown

**Top Bar (Shop / Payments)**
- Title large left; right: search + notifications (24pt)
- The black hero or shop search sits directly below

**Segmented Control (Upcoming / Paid, plan types)**
- Track `#FFF2F5`, height 40pt, radius 20pt
- Selected segment: white pill with soft shadow, near-black text weight 600 (or `#FFB3C7` fill for emphasis)
- Unselected: `#6E6878`
- Smooth sliding thumb 260ms ease (slightly slower — the Klarna glide)

### Input Fields

**Search Bar (Shop)**
- Background `#FFF2F5`, height 48pt, radius 24pt
- Leading `magnifyingglass` 18pt `#6E6878`; placeholder "Search stores & products" 16pt `#6E6878`
- Focus: 1.5pt `#0B051D` ring

**Text Field (card / address)**
- Filled style, background `#F6F4F8`, height 56pt, radius 16pt
- Rest: 1pt `#E2D2D8`; focus: 2pt `#0B051D`, floating label tints near-black
- Error: 2pt `#C8102E`, helper `#C8102E`

**Amount / Card Entry**
- Large field, value in Klarna Display 30pt w700 `#0B051D` tabular
- Below: helper 13pt `#6E6878`

### Distinctive Components

**Pay-in-4 Schedule Timeline**
- The signature object: four dated dots on a connecting track that visualizes the payment plan — first paid (filled pink + check), the rest outlined, each labeled with a date and a tabular amount. The entire BNPL proposition rendered as one calm, legible picture.

**In-App Shopping Browser**
- Klarna's in-app web wrapper: any merchant store opens inside Klarna's chrome with a URL pill, a persistent pink "Pay with Klarna" footer, and a smooth slide-up checkout sheet — shopping never leaves the soft Klarna frame.

**Smooth Pink CTA**
- The very soft (28pt radius) pink button with the slow, gliding press/scale and slide transitions — the tactile signature of "smooth".

**Order List Rows**
- Clean purchase-history rows with merchant logo, plan summary, tabular amount, and a status pill — the calm record of what you bought and how it's being paid.

**Slide Transitions**
- Screens and sheets glide in/out over ~300–360ms ease (slightly slower than typical) so the app always feels smooth, never abrupt.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Standard screen margin: 20pt horizontal (a touch roomier — calmer feel)
- Card internal padding: 20–24pt; section vertical gap: 24pt
- Order rows: 0pt gap, separated by `#F0DDE3` hairlines

### Grid & Container
- Content width: full device width, 20pt side margins
- Quick-action bar: 4 circular actions, evenly distributed
- Order rows: full-width stacked
- Schedule card: full-width, single horizontal timeline

### Whitespace Philosophy
- **Soft and roomy**: Slightly larger margins (20pt) and generous card padding — the calm BNPL feel
- **One dark anchor**: The black hero/summary is the single deep panel; everything else stays light and pink-tinted
- **Schedule gets air**: The Pay-in-4 timeline always has clear space so the four dots read instantly

### Border Radius Scale
- Soft (12pt): Merchant logos, small chips, inline tags
- Standard (16pt): Inputs, small tiles
- Comfortable (20pt): Cards, the schedule card, modules
- Roomy (24pt): The black hero, bottom sheets (top corners)
- Pill (28pt): Primary CTAs (very soft full pill), filter chips
- Circle (50%): Quick-action circles, schedule dots, avatars

### Whitespace note
Klarna's radii run notably soft — the primary button at 28pt and cards at 20pt are larger than most fintech systems, reinforcing the friendly, smooth personality.

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt `#F0DDE3` border | Order rows, pink wells, canvas |
| Card (Level 1) | `rgba(11,5,29,0.05) 0 2px 8px` | Schedule card, order card at rest |
| Raised (Level 2) | `rgba(11,5,29,0.08) 0 8px 20px` | Pressed card, selected segment thumb |
| Hero (Level 2b) | `rgba(11,5,29,0.18) 0 12px 32px` | The black hero / payment-summary panel |
| Sheet (Level 3) | `rgba(11,5,29,0.16) 0 -12px 36px` | Bottom sheets, the checkout sheet |
| Footer Bar | `rgba(11,5,29,0.10) 0 -4px 16px` | The pink "Pay with Klarna" footer in the browser |

**Shadow Philosophy**: On the soft bright canvas, separation is border-first (1pt warm `#F0DDE3`) and shadows are very soft, low-opacity, and cool-violet-tinted — they appear mainly to lift the black hero and the checkout sheet, and to signal a press. The system never uses hard shadows; the softness *is* the brand.

### Motion
- **Slide transitions**: screens/sheets glide in over ~300–360ms ease — the signature slow glide
- **CTA press**: pink scale 0.98 + darken to `#F49CB4`, soft haptic, 160ms
- **Schedule fill**: when a payment is made, its dot fills pink with a check (260ms) and the track segment behind it fills pink
- **Footer reveal**: the pink "Pay with Klarna" bar slides up ~320ms ease on store load
- **Segmented thumb**: white/pink pill slides 260ms ease (deliberately unhurried)
- **Amount roll**: the total due updates with a gentle 300ms crossfade after a payment

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas with `#FFF2F5` for soft grouped wells; keep one dark anchor (the black hero)
- Use near-black `#0B051D` for structure — headings, amounts, the dark hero
- Reserve Klarna Pink `#FFB3C7` for the primary action and key highlights
- Always put near-black text (`#0B051D`) on Klarna Pink — never white
- Make amounts the heaviest element — 22–36pt weight 700, tabular
- Use tabular numerals for the schedule, order totals, and plan lines
- Render the Pay-in-4 plan as the 4-dot connected timeline — calm and literal
- Keep radii soft (28pt CTA, 20–24pt cards) and transitions slow/gliding
- Wrap merchant stores in Klarna's chrome with the persistent pink "Pay with Klarna" footer

### Don't
- Don't put white text on Klarna Pink — it fails contrast and breaks the brand
- Don't introduce blue, gradients, or a second accent — pink + near-black is the whole story
- Don't use pure black for text — the structural ink is `#0B051D` (violet-cast near-black)
- Don't hide the Pay-in-4 schedule behind a summary — the 4-dot timeline *is* the product
- Don't use proportional figures for money — always tabular, decimal-aligned
- Don't use sharp/small corner radii — Klarna's softness (28pt CTA) is intentional
- Don't make more than one panel dark — the black hero is the single anchor
- Don't snap transitions — motion is unhurried (300–360ms glide)
- Don't use light/thin weights — Klarna Text amounts run 700

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero amount caps ~30pt, schedule dots tighten, 16pt margins |
| iPhone 13/14/15 | 390pt | Standard hero, amount ~36pt, 20pt margins, 4 quick actions |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the top bar |
| iPhone 15/16 Pro Max | 430pt | Larger hero, more order rows above the fold |
| iPad | 768pt+ | Two-pane (orders list + plan detail), centered checkout sheet |

### Dynamic Type
- Titles, body, names, amounts: full scale
- Hero amount: scales but caps to protect the single-line layout
- Tab labels, uppercase labels: fixed
- Schedule amounts/dates: fixed tabular size for alignment under dots

### Orientation
- All primary tabs and the checkout flow: **portrait-locked** on iPhone
- iPad: landscape supported with split layout

### Touch Targets
- Pink CTA: 56pt height — unmissable, very soft pill
- Quick-action circles: 56pt
- Order rows: 72pt, full-row tappable
- Tab icons: 24pt glyph, 44pt effective hit
- Schedule dots: 44pt hit area each

### Safe Area Handling
- Top: top bar + hero respect safe area / Dynamic Island
- Bottom: tab bar opaque, respects home indicator; the checkout sheet and pink footer float above it
- Sides: 20pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface pink (wells): `#FFF2F5`
- Divider: `#F0DDE3`
- Text primary: `#0B051D`
- Text secondary: `#6E6878`
- Black (structure): `#0B051D`
- Black hover: `#1A1330`
- Klarna Pink (action): `#FFB3C7`
- Klarna Pink pressed: `#F49CB4`
- Success green: `#0E8A4F`
- Upcoming amber: `#A9700E`
- Error red: `#C8102E`

### Example Component Prompts
- "Create a SwiftUI Klarna Pay-in-4 schedule card: white card, 1pt #F0DDE3, 20pt radius, 20pt padding. A horizontal timeline of 4 dots on a 2pt track. Dot 1 is paid: filled #FFB3C7 with a near-black check; dot 2 is next: white with a 2pt #0B051D ring; dots 3–4 upcoming: white with a 2pt #F0DDE3 ring. Track between paid dots is #FFB3C7, ahead is #F0DDE3. Under each dot: date 13pt #6E6878 and amount 15pt weight 700 #0B051D tabular."
- "Build the Klarna primary CTA: 56pt tall full-width button, 28pt radius (very soft pill), background #FFB3C7, 'Pay now' in Inter 16pt weight 700 #0B051D (near-black — never white on pink). Pressed: background #F49CB4, scale 0.98, soft haptic, ~160ms."
- "Design the Klarna black payment-summary hero: full-width panel, background #0B051D, 24pt radius, 24pt padding, shadow rgba(11,5,29,0.18) 0 12px 32px. 'TOTAL DUE' label 11pt #FFB3C7 uppercase, amount '£128.40' in 36pt weight 700 white tabular, 'Next payment 14 Jun' 13pt #9B96A3, and a #FFB3C7 'Pay now' affordance."
- "Create a Klarna order row: 72pt tall, leading 44pt rounded-square merchant logo (12pt radius) on #FFF2F5, merchant 'ASOS' 16pt weight 600 #0B051D + '2 items · Pay in 4' 13pt #6E6878, trailing amount '£64.00' 16pt weight 600 #0B051D tabular and a status pill 'Due 14 Jun' in #A9700E. Pressed #FFF2F5, divider #F0DDE3."
- "Build the Klarna in-app shopping browser footer: a persistent bottom bar, background #FFB3C7, 'Pay with Klarna' in Inter 16pt weight 700 #0B051D, that slides up ~320ms ease when a merchant store loads, over a dimmed page."

### Iteration Guide
1. Canvas is bright `#FFFFFF` with soft `#FFF2F5` wells; keep one dark anchor — the black `#0B051D` hero
2. Near-black `#0B051D` is the structural ink (headings, amounts, hero); Klarna Pink `#FFB3C7` is action/highlight only
3. Near-black text on pink — `#0B051D`, never white on `#FFB3C7`
4. Amounts are the heaviest element — 22–36pt weight 700, tabular numerals
5. Money columns (schedule, order totals, plan lines) are always tabular and decimal-aligned
6. The Pay-in-4 four-dot timeline is the signature object — never hide the plan behind a summary
7. Keep radii soft (28pt CTA, 20–24pt cards) and transitions slow/gliding (300–360ms) — softness is the brand
8. Separate with 1pt warm `#F0DDE3` borders; very soft low-opacity violet-tinted shadow only for the hero/sheet/press
