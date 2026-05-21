# Design System Inspiration of Nubank (iOS)

## 1. Visual Theme & Atmosphere

Nubank's iOS app is the most single-mindedly **purple** product in fintech. The brand color — **Nu Purple** (`#820AD1`) — is not an accent; it is the identity. The moment you open the app a full-bleed purple header fills the top of the screen with your greeting ("Olá, Ana"), your avatar, and a few white chrome icons (settings, notifications, help). Below that purple sky, the rest of the app is a calm stack of **tiles** — the NuConta account balance, quick-action shortcuts (Pix, Pagar, Cartões, Investir), the credit-card invoice tile, recent activity — each a clean rounded card on a near-white (light) or deep-aubergine (dark) canvas. The contrast is the design: a bold, confident purple block that says "Nubank" instantly, then quiet, legible content beneath it.

The Home screen is deliberately uncluttered. Nubank's design philosophy is "fewer, clearer choices": the most common things you do (send a Pix, pay a bill, check the card invoice, see the balance) are surfaced as large, obvious tiles and circular quick-action shortcuts; everything else lives one tap deeper. There are no dense dashboards, no charts fighting for attention, no marketing banners stacked five-high. Each tile is tappable and pushes a focused flow. The famous **Roxinho** (the little purple credit card) is rendered as a clean purple gradient mini-card inside the credit tile and as a full card in the Cartões section.

The atmosphere is **warm, human, and reassuring**. Copy is friendly Portuguese-first ("Saldo disponível", "Sua fatura fecha em 5 dias") with a conversational, jargon-free tone. Numbers are large and confident — the NuConta balance and the invoice amount are the headline of their tiles. Illustrations and the in-app "NuConcierge"/chat use rounded, friendly shapes. Nothing is skeuomorphic; surfaces separate by elevation, generous padding, and hairline dividers, not heavy borders. The app feels light, fast, and trustworthy — a bank that doesn't feel like a bank.

Typography is a clean geometric humanist sans (Nubank uses **Graphik / Nu Sans** in brand and product; **Inter** is the closest free fallback for implementation). It is highly legible at small sizes — essential for currency values where every digit matters — with tight tracking on large headings. Weights concentrate at 400 (body), 500–600 (labels and row titles), 700 (greeting and section titles), and 800 (balances and screen titles). Currency uses tabular figures so amounts align.

**Key Characteristics:**
- Nu Purple (`#820AD1`) as the identity — a full-bleed purple hero header is the first thing you see
- Tile-based Home — NuConta balance, quick actions, credit-card invoice, activity, each a clean rounded card
- "Fewer, clearer choices" — the most common actions surfaced large; everything else one tap deeper
- Roxinho purple card — rendered as a clean purple gradient, the brand's most recognized object
- Big confident currency numbers — the balance and invoice are the headline of their tiles; tabular figures
- Calm content canvas — near-white light / deep aubergine dark beneath the bold purple block
- Friendly, jargon-free copy — Portuguese-first, conversational, reassuring
- Pix-first — Pix is the primary money-movement action, a large quick-action shortcut
- Pill-shaped buttons — fully rounded primary and secondary buttons
- Flat surfaces — elevation + padding + hairline dividers, not heavy boxes
- Bottom tab bar: Início, Cartões, Investir, Buscar, Perfil — soft-purple active state

## 2. Color Palette & Roles

### Primary (Interactive / Brand)
- **Nu Purple** (`#820AD1`): The identity — hero header, primary CTAs, brand surfaces, the Roxinho card. Identical in light and dark.
- **Purple Bright** (`#9B2BE0`): Gradient top / hover / progress fill on dark.
- **Purple Pressed** (`#6A07AD`): Pressed state on purple buttons and the hero.
- **Purple Soft** (`#C9A6E8`): Dark-mode interactive tint — active tab, secondary-button border/text, tile icons on dark.
- **Purple Wash** (`#F3E8FB` light / `#2A1240` dark): 8–14% purple fill behind selected rows and purple chips.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#F4F2F7`): Primary light canvas — a faint cool lilac-white, never pure white.
- **Surface** (`#FFFFFF`): Tiles, cards, sheets — pure white lifts above the lilac canvas.
- **Surface Pressed** (`#ECE8F2`): Pressed/selected tile state.
- **Divider** (`#E6E1EE`): Hairline dividers between rows and tile sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#15101C`): Primary dark canvas — deep aubergine tinted from Nu Purple, NOT pure black.
- **Dark Surface 1** (`#1F1729`): Tiles, cards, sheets.
- **Dark Surface 2** (`#2A2038`): Elevated surfaces, quick-action circles, pressed rows.
- **Dark Divider** (`#362B47`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#1A1523`): Primary text — a near-black aubergine, NOT pure black.
- **Text Secondary Light** (`#6B6178`): Metadata, captions, labels.
- **Text Tertiary Light** (`#9C93AB`): Disabled, placeholders.
- **Text Primary Dark** (`#F3EEF8`): Primary text on dark — a purple-tinted off-white.
- **Text Secondary Dark** (`#B6A8C8`): Metadata on dark.
- **Text Tertiary Dark** (`#7A6B8E`): Disabled / placeholder on dark.
- **Text On-Purple** (`#FFFFFF`): All text and icons on the purple hero / purple buttons.

### Product Accents
Used for category icons, investment tiles, statuses, and the Ultravioleta tier.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Positive Green | `#1FB76A` | `#2ED47A` |
| Investment Gold | `#D9A227` | `#F5C24B` |
| Highlight Pink | `#D9469E` | `#E85FB0` |
| Info Blue | `#3D7AE0` | `#5A95F0` |
| Ultravioleta Graphite | `#2B2B2E` | `#1C1C1F` |

### Semantic
- **Positive / Income** (`#1FB76A` light / `#2ED47A` dark): Money received, Pix in, positive deltas.
- **Error Red** (`#E03A4A` light / `#FF5C6C` dark): Failed payments, errors, destructive confirmation.
- **Warning Amber** (`#D9821F` light / `#FFB347` dark): Invoice-due and low-balance warnings.
- **Pending Grey** (`#9C93AB`): Pending/processing transactions (slightly faded row).

## 3. Typography Rules

### Font Family
- **Brand & Product**: `Graphik` / `Nu Sans` — geometric humanist sans used across Nubank brand and product for confident, legible currency values.
- **Implementation Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest freely-licensable substitute; near-identical proportions and excellent figure legibility.
- **Numerals**: tabular figures (`font-feature-settings: "tnum"`) for balances, invoice amounts, and any aligned currency column.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Graphik/Inter | 32pt | 800 | 1.15 | -0.6pt | Large nav title ("Início", "Cartões") |
| Balance / Hero Number | Graphik/Inter | 26pt | 800 | 1.1 | -0.5pt | NuConta balance, invoice total — tabular |
| Greeting / Section | Graphik/Inter | 22pt | 700 | 1.2 | -0.3pt | "Olá, Ana", section headings |
| Subsection | Graphik/Inter | 18pt | 700 | 1.25 | -0.2pt | Tile title, sheet header |
| Body | Graphik/Inter | 16pt | 400 | 1.45 | 0pt | Descriptive copy, sheet body |
| Row / Tile Title | Graphik/Inter | 15pt | 600 | 1.3 | -0.1pt | Activity row title, tile label |
| Amount | Graphik/Inter | 15pt | 700 | 1.3 | -0.1pt | Activity amount (tabular) |
| Meta | Graphik/Inter | 13pt | 500 | 1.35 | 0pt | "Hoje · 14:02", secondary line |
| Label / Caption | Graphik/Inter | 12pt | 500 | 1.3 | 0.1pt | "Saldo disponível", chip text |
| Button | Graphik/Inter | 16pt | 700 | 1.0 | 0pt | Pill-button label |
| Tab Label | Graphik/Inter | 10pt | 500 | 1.0 | 0.1pt | Bottom tab labels |
| Quick-Action Label | Graphik/Inter | 11pt | 500 | 1.2 | 0pt | Pix / Pagar / Investir captions |

### Principles
- **Currency leads**: The NuConta balance and the invoice amount are the largest, boldest elements in their tiles.
- **Tabular figures wherever money appears**: balances, invoice totals, amounts align vertically with `tnum`.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: body 400, meta 500, row title 600, greeting/section 700, balance/screen-title 800. No thin, no italic.
- **Tighten as it grows**: negative tracking from -0.2pt at 18pt down to -0.6pt at 32pt for a confident, modern feel.
- **Dynamic Type first-class**: screen title, balance, greeting, body, row title, meta scale; tab labels and quick-action labels stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Fazer Pix / Pagar / Continuar)**
- Shape: Full pill, 500pt corner radius
- Background: `#820AD1` (Nu Purple)
- Text: `#FFFFFF`, Graphik/Inter 16pt weight 700
- Padding: 15pt vertical, 28pt horizontal; full-width on sheets (18pt side insets)
- Pressed: background `#6A07AD` + scale 0.98
- Disabled: `#820AD1` at 35% opacity, text at 60%

**Secondary Button (Pagar fatura / Editar)**
- Background: transparent
- Text + border: `#820AD1` light / `#C9A6E8` dark, Graphik/Inter 15pt weight 600
- Border: 1.5pt same as text
- Corner radius: 500pt
- Pressed: purple-wash fill `#F3E8FB` light / `#2A1240` dark

**Ghost / Tertiary Button (Ver extrato)**
- Background: `#FFFFFF` light / `#2A2038` dark
- Text: `#1A1523` light / `#F3EEF8` dark, Graphik/Inter 15pt weight 600
- Border: 1pt divider color
- Corner radius: 500pt
- Pressed: surface-pressed fill

**Text Button (Ver tudo)**
- Font: Graphik/Inter 14pt weight 600
- Color: `#820AD1` light / `#C9A6E8` dark
- No underline, no background
- Pressed: 60% opacity

### Core Atoms

**Account / Credit Tile** (the core atom)
- Surface card, 16pt corner radius, 18pt padding, full-width minus 18pt insets
- Header row: 30pt leading icon (stroke, purple-soft on dark / Nu Purple on light) + 15pt w600 label
- Primary value: balance/invoice in 26pt w800 tabular, primary text
- Secondary line: "Saldo disponível" / "vence 10 jun" in 13pt w500 secondary text
- Optional progress bar (credit limit): 6pt tall, 3pt radius, track = divider, fill = Purple Bright
- Tap: pushes the account / invoice detail; whole tile is the target

**Quick-Action Shortcut**
- Circular 52pt button, surface-2 fill, 22pt stroke icon in purple-soft/Nu Purple
- Caption below: Graphik/Inter 11pt w500 secondary, max 2 lines centered
- Horizontal scroll row, 10pt gap, under the NuConta tile
- Pressed: scale 0.96 + slight tint

**Activity Row**
- Height: ~58pt, 22pt horizontal inset
- Leading: 38pt circular icon (surface-2 fill, stroke glyph in purple-soft) — Pix arrow, card, etc.
- Title: Graphik/Inter 15pt w600 primary text
- Subtitle: source · time, Graphik/Inter 13pt w500 secondary
- Trailing: amount Graphik/Inter 15pt w700 tabular; income green `#1FB76A`/`#2ED47A`, spend in primary text
- Pending: row at 60% opacity + small "Pendente" tag
- Tap: pushes transaction/Pix receipt detail

**Chip / Status Tag**
- Background: status tint at 12–16%
- Text: status color, Graphik/Inter 12pt w600
- Corner radius: 500pt, 6pt vertical / 12pt horizontal padding

### Navigation

**Purple Hero Header (Home)**
- Full-bleed Nu Purple (or 160° `#820AD1 → #9B2BE0 → #6A07AD` gradient) block at the top
- Top row: circular avatar (38pt, white@18% fill) + white chrome icons (settings, notifications, help)
- Greeting: "Olá," 14pt w500 white@85% + name 22pt w700 white
- No shadow; the content canvas begins immediately below with the first tile overlapping slightly

**Bottom Tab Bar**
- Height: 52pt + safe area; background `#FFFFFF` light / `#15101C` dark with 0.5pt top divider, 20px blur
- Tabs (5): Início, Cartões, Investir, Buscar, Perfil
- Icon size: 22pt; active = filled glyph in Nu Purple `#820AD1` (light) / Purple Soft `#C9A6E8` (dark); inactive = stroke glyph in tertiary text
- Labels: Graphik/Inter 10pt w500, always shown
- No tint pill — active is icon fill + label color change

**Large Title Header (inner screens)**
- Screen title Graphik/Inter 32pt w800 left-aligned, collapses to a 17pt centered title on scroll
- Leading back chevron; trailing contextual action
- Canvas-colored; 0.5pt divider appears when content scrolls under

### Input Fields

**Currency / Amount Entry (Pix value)**
- Large centered amount, Graphik/Inter 26–32pt w800, purple caret, no input box, system number pad
- "R$" prefix in same style at 60% size
- Helper text below in 13pt w500 secondary

**Text Field**
- Height: 52pt, surface fill, 12pt radius, no border at rest
- Focus: 1.5pt `#820AD1` border
- Label floats above on focus in 12pt w500 secondary
- Placeholder: tertiary text, Graphik/Inter 16pt w400

**Search Bar (Buscar)**
- Height: 44pt, surface-pressed fill, 500pt radius (pill)
- Leading magnifier 16pt secondary text
- Placeholder "Buscar" Graphik/Inter 15pt w400 tertiary

### Distinctive Components

**Purple Hero Header** — see Navigation; the brand's signature first impression, identical across themes.

**NuConta Balance Tile**
- The headline tile: "Conta" label + R$ balance 26pt w800 + "Saldo disponível"
- Tap → account detail with extrato (statement) list
- Optional eye-toggle to hide/show the balance (privacy)

**Credit-Card (Roxinho) Tile**
- 40×26pt purple gradient mini-card + "Cartão de crédito" label
- Invoice total 22pt w800, "Fatura atual · vence {date}" 13pt w500
- Limit-used progress bar (Purple Bright fill), "Limite disponível R$ …" below
- Tap → full card screen with the large Roxinho card, controls (block, virtual card, limit)

**Quick Actions Strip** — circular shortcuts (Pix, Pagar, Cartões, Investir, Empréstimo); see Core Atoms.

**Pix Flow**
- Pix is first-class: amount entry → recipient (key/contact) → confirm → animated success receipt
- Success screen: large green check, amount, recipient, "Pix enviado", share-receipt button

**Investment / NuInvest Tile**
- Total invested 22pt w800, daily delta in green/red with arrow
- Simple sparkline or category split in product-accent colors

**Ultravioleta Surface (premium tier)**
- Graphite/near-black surface with metallic purple accents — a distinct premium skin layered over the same components

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 24, 32, 40, 48, 64
- Screen horizontal inset: 18–22pt (tiles 18pt; rows/headers 22pt)
- Tile internal padding: 18pt; tile-to-tile vertical gap 14pt
- Hero header bottom padding: 22pt

### Grid & Container
- iPhone: single column, 18pt tile insets, full-bleed purple hero
- iPad: content max-width ~640pt centered; tiles in a centered column, optional 2-up tile grid (account + credit side-by-side)
- Quick actions: fixed 72pt-wide cells in a horizontal scroll

### Whitespace Philosophy
- **Bold block, calm content**: the purple hero is dense and confident; everything below breathes with generous padding
- **One idea per tile**: each tile holds a single concept (balance, invoice, activity) with clear internal hierarchy
- **Fewer, clearer choices**: the screen is intentionally short — common actions large, the rest one tap deeper
- **Flat, not boxed**: tiles separate by elevation + 14pt gaps + hairline dividers; no heavy borders

### Border Radius Scale
- Subtle (4pt): progress-bar fills, tiny tags
- Small (8pt): inline chips, mini-card on the credit tile (~5pt)
- Standard (12pt): input fields, small controls
- Comfortable (14pt): nested mini-tiles
- Tile (16pt): account/credit tiles, sheets, cards
- Large (24pt): bottom-sheet top corners
- Pill (500pt): all buttons, search bar, status chips
- Circle (50%): avatars, quick-action circles, activity icons

## 6. Depth & Elevation

Nubank is mostly flat; elevation separates tiles from the canvas and floats sheets.

| Level | Treatment | Use |
|-------|-----------|-----|
| Hero (Level 0) | No shadow, full-bleed color block | Purple Home header |
| Flat (Level 0) | No shadow, hairline divider | Activity rows, list sections |
| Tile (Level 1) | `0 6px 18px -10px rgba(40,12,60,0.15)` (dark: deeper) | Account / credit / investment tiles, cards |
| Sheet (Level 2) | `0 -8px 28px rgba(20,8,30,0.22)` | Bottom sheets (Pix, pay, confirm) |
| Modal Scrim | `rgba(12,6,20,0.55)` | Dim behind sheets and modals |

**Shadow Philosophy**: Shadows are soft and purple-tinted, used only to lift tiles off the canvas and float sheets — never on the purple hero (it is a flat color block) and never on list rows (dividers do that job). In dark mode shadows read weakly, so a 1pt `#362B47` border can substitute as the tile/sheet elevation cue.

### Motion
- **Tile press**: subtle scale 0.98 + 80ms ease-out on tap-down; releases into a push transition
- **Balance reveal**: eye-toggle wipes the masked dots into digits over 200ms ease-out
- **Pix success**: a green check draws in (stroke animation ~400ms) with a success haptic; amount counts up briefly
- **Number ticker**: balances and invoice totals animate digit-by-digit on change (~250ms ease-out)
- **Hero parallax**: on scroll, the purple hero compresses slightly and the first tile slides up over it (1:1 then eased)
- **Quick action**: circle scales 0.96 on press with a light haptic; flow opens with a 300ms push
- **Tab change**: instant icon/label color crossfade (120ms); no slide
- **Push navigation**: standard iOS push (300ms) into tile detail
- **Haptics**: light impact on quick-action and tile press, success haptic on completed Pix/payment, soft tick on tab change

## 7. Do's and Don'ts

### Do
- Make Nu Purple (`#820AD1`) the identity — a full-bleed purple hero header is the first thing the user sees, identical in light and dark
- Build the Home as a calm stack of tiles — NuConta balance, quick actions, credit invoice, activity
- Follow "fewer, clearer choices" — surface the common actions large; push everything else one tap deeper
- Render the Roxinho as a clean purple gradient card — the brand's most recognized object
- Use big confident currency numbers with tabular figures for balances and invoice totals
- Keep copy friendly and jargon-free (Portuguese-first: "Saldo disponível", "Sua fatura fecha em 5 dias")
- Make Pix a first-class quick action with an animated success receipt
- Use fully pill-shaped buttons (500pt radius) for primary and secondary actions
- Color income green and keep spend in primary text
- Keep the content canvas calm (lilac-white / deep aubergine) so the purple hero pops

### Don't
- Don't dilute the purple — no rainbow of brand colors; Nu Purple owns the identity
- Don't use pure black for dark mode — deep aubergine `#15101C` is correct
- Don't use pure white for the light canvas — faint lilac-white `#F4F2F7`, white only for tiles
- Don't cram the Home with dense dashboards, charts, or stacked banners — it is intentionally short
- Don't put a shadow on the purple hero — it is a flat color block
- Don't use sharp-cornered rectangular buttons — buttons are full pills
- Don't render activity as a dense spreadsheet — they are clean, tappable rows with circular icons
- Don't use a tint pill on the active tab — active is icon fill + label color only
- Don't use thin or italic weights — the system lives at 400–800 upright
- Don't desaturate the purple or product accents in dark mode — chrome dims, brand does not

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ~140pt; balance 24pt; quick-action circles 48pt |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island sits over the purple hero (white status content) |
| iPhone 15/16 Pro Max | 430pt | Hero ~165pt; balance 28pt; larger tiles |
| iPad (portrait) | 768pt | Content max-width 640pt centered; tiles single column |
| iPad (landscape) | 1024pt+ | 2-up tile grid (account + credit), detail as secondary pane |

### Dynamic Type
- Screen title, balance, greeting, body, row title, meta: full scale
- Tab labels, quick-action labels: FIXED (layout-sensitive)
- Amounts scale but remain tabular-aligned

### Orientation
- iPhone: portrait-primary; rotation supported with tile reflow
- iPad: split-view; tile detail becomes the secondary pane in landscape

### Touch Targets
- Tile: full-tile tap, ≥72pt height
- Quick-action circle: 52pt glyph target, ≥48pt
- Activity row: full-row tap, ≥56pt effective
- Tab bar icons: 22pt glyph, 44pt hit
- Primary buttons: ≥48pt tall

### Safe Area Handling
- Top: purple hero extends under the status bar / Dynamic Island (white status content); content respects safe area
- Bottom: tab bar + home indicator respected; sheets respect bottom inset
- Keyboard: amount entry scrolls above the number pad; the running value never hides
- Sides: 18–22pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Nu Purple (identity / CTA): `#820AD1`
- Purple bright (gradient / fill): `#9B2BE0`
- Purple pressed: `#6A07AD`
- Purple soft (dark interactive): `#C9A6E8`
- Light canvas: `#F4F2F7`
- Light surface (tiles): `#FFFFFF`
- Dark canvas: `#15101C`
- Dark surface 1 / 2: `#1F1729` / `#2A2038`
- Text primary: `#1A1523` light / `#F3EEF8` dark
- Text secondary: `#6B6178` light / `#B6A8C8` dark
- Positive green: `#1FB76A` light / `#2ED47A` dark
- Error red: `#E03A4A` light / `#FF5C6C` dark
- Divider: `#E6E1EE` light / `#362B47` dark

### Example Component Prompts
- "Build the Nubank purple hero header in SwiftUI: full-bleed Nu Purple block (or 160° gradient `#820AD1 → #9B2BE0 → #6A07AD`) extending under the status bar. Top row: 38pt circular avatar (white@18% fill, initials) on the leading edge, white stroke icons (gearshape, bell, questionmark.circle) trailing with 18pt gap. Below: 'Olá,' Inter 14pt w500 white@85%, then name Inter 22pt w700 white. No shadow. The first content tile slides up to overlap the bottom edge slightly on scroll."

- "Create the Nubank NuConta tile: white surface card (16pt radius, 18pt padding, full-width minus 18pt insets) on a `#F4F2F7` canvas. Header row: 30pt stroke icon in Nu Purple + 'Conta' Inter 15pt w600. Balance `R$ 4.812,35` Inter 26pt w800 tabular `#1A1523`. 'Saldo disponível' Inter 13pt w500 `#6B6178` below. Soft purple-tinted shadow `0 6px 18px -10px rgba(40,12,60,0.15)`. Whole tile pushes the account detail."

- "Render the Nubank credit-card tile: surface card, header = 40×26pt purple gradient mini-card (`#820AD1 → #9B2BE0`, 5pt radius) + 'Cartão de crédito' Inter 15pt w600. Invoice `R$ 1.248,90` Inter 22pt w800, 'Fatura atual · vence 10 jun' Inter 13pt w500 secondary. Limit-used progress bar 6pt tall 3pt radius, track = divider, fill = `#9B2BE0` at 42%. 'Limite disponível R$ 6.751,10' Inter 13pt w500 secondary below. Tap → full Roxinho card screen."

- "Create a Nubank quick-actions strip: horizontal scroll, 10pt gap, under the NuConta tile. Each shortcut: 52pt circle, `#FFFFFF`/`#2A2038` fill, 22pt stroke icon in Nu Purple/Purple Soft, caption below Inter 11pt w500 secondary centered (max 2 lines, 72pt cell width). Items: Pix, Pagar, Cartões, Investir, Empréstimo. Press: scale 0.96 + light haptic."

- "Build a Nubank activity row: 58pt tall, 22pt horizontal inset. Leading 38pt circular icon (`#2A2038` fill, stroke glyph in Purple Soft — e.g. up-arrow for Pix sent). Title Inter 15pt w600 `#F3EEF8`, subtitle 'Hoje · 14:02' Inter 13pt w500 `#B6A8C8`, trailing amount Inter 15pt w700 tabular — income `#2ED47A`, spend `#F3EEF8`. Pending: 60% opacity + 'Pendente' tag. Tap pushes the receipt."

- "Create the Nubank primary button: full pill (500pt radius), `#820AD1` background, white Inter 16pt w700 label, 15pt vertical / 28pt horizontal padding, full-width on sheets. Pressed: `#6A07AD` + scale 0.98. Secondary variant: transparent fill, 1.5pt `#820AD1` (light) / `#C9A6E8` (dark) border + same-color text."

### Iteration Guide
1. Nu Purple (`#820AD1`) is the identity — a full-bleed purple hero header is the first thing seen, identical in light and dark, never shadowed
2. Home is a calm stack of tiles on a lilac-white (`#F4F2F7`) / deep-aubergine (`#15101C`) canvas — never pure white, never pure black
3. "Fewer, clearer choices" — the screen is intentionally short; common actions large, everything else one tap deeper
4. The NuConta balance and the credit invoice are the headline numbers — big, w800, tabular figures
5. The Roxinho is a clean purple gradient card — the brand's most recognized object
6. Pix is a first-class quick action with an animated green-check success receipt
7. Buttons are full pills (500pt radius); income is green, spend in primary text
8. Tiles lift off the canvas with soft purple-tinted shadows; rows are flat with dividers
9. Copy is friendly, jargon-free, Portuguese-first; warmth is part of the brand
10. Dark mode dims chrome only — the purple hero, Roxinho, and product accents stay vivid; text shifts to a purple-tinted off-white
