# Design System Inspiration of Acorns (iOS)

## 1. Visual Theme & Atmosphere

Acorns' iOS app is a **friendly, optimistic micro-investing app** built on one idea: investing should feel approachable, automatic, and rewarding — not like a Bloomberg terminal. The brand is anchored by two colors working in tandem: **Acorns Oak** (`#5A2B82`), a deep, trustworthy purple used for brand surfaces, the Round-Ups card, and structural moments; and **Acorn Green** (`#6FBF4E`), a bright, leafy green that is both the primary action color and the universal "your money is growing" signal. The acorn-to-oak-tree metaphor ("from little acorns mighty oaks grow") runs through everything — growth, patience, small things compounding.

The signature screen is the **Invest dashboard**. At the top, the **portfolio total** is the headline — a large, confident dollar figure with an all-time gain shown beneath it in green with a triangle. Below it sits the **allocation donut** — a multi-segment ring (large company, small company, international, real estate, bonds) in a warm palette, with a center label naming the portfolio style (Conservative → Aggressive). A legend lists each holding with its percentage and dollar value. Then the **Round-Ups card** — a purple gradient card showing accumulated spare change ("$4.62 · invests at $5.00"), the feature that made Acorns famous: every card purchase rounds up to the next dollar and the difference is invested. Below that, **Found Money** (now "Acorns Earn") — cashback from partner brands that gets invested automatically, shown as a feed of brand rows with green "+$" amounts.

The atmosphere is **warm, encouraging, and lightly playful**. Copy is motivational and plain-English ("Your money is hard at work", "Small amounts add up"). Numbers are big and friendly. Illustrations are rounded, organic, often featuring acorns, oak leaves, and growth motifs. The type is a rounded humanist sans (Acorns uses a rounded geometric face in brand/product; **Nunito Sans** is the closest free fallback), which reads as friendly and trustworthy rather than corporate. Surfaces are clean and flat with generous padding; only the Round-Ups card and the donut carry color and gradient — everything else is calm.

**Key Characteristics:**
- Acorns Oak purple (`#5A2B82`) + Acorn Green (`#6FBF4E`) — the dual brand: trust + growth
- Invest dashboard as the signature screen — portfolio total, allocation donut, Round-Ups, Found Money
- Allocation donut — multi-segment ring with center portfolio-style label + legend
- Round-Ups card — purple gradient card with accumulated spare change and the $5 invest threshold
- Found Money / Earn — partner-cashback feed with green "+$" amounts that auto-invest
- Big friendly numbers — the portfolio total is the largest type; gains in green with a ▲
- Rounded humanist type (Nunito Sans fallback) — friendly, trustworthy, approachable
- Motivational plain-English copy — "Your money is hard at work", "Small amounts add up"
- Green = action + growth; Oak purple = brand + structure
- Acorn / oak / leaf growth motifs in illustration
- Flat, calm surfaces — only the Round-Ups card and donut carry color
- Bottom tab bar: Invest, Later, Spend, Earn, Profile — green active state

## 2. Color Palette & Roles

### Primary (Interactive / Growth)
- **Acorn Green** (`#6FBF4E`): The primary action color AND the growth/positive signal — primary CTAs, gains, "+$" amounts, active tab. Identical across themes.
- **Green Bright** (`#84D962`): Dark-mode interactive/positive tint (lifts for contrast on dark).
- **Green Pressed** (`#5AA53D`): Pressed state on green buttons.
- **Green Wash** (`#E9F6E3` light / `#1E2C18` dark): 8–12% green fill behind positive chips/highlights.

### Brand Anchor
- **Acorns Oak** (`#5A2B82`): The trust/brand purple — Round-Ups card, brand surfaces, secondary CTA, structural headers, the first donut slice.
- **Oak Bright** (`#7B43A8`): Round-Ups gradient top / hover.
- **Oak Pressed** (`#47206A`): Pressed state on purple buttons.
- **Oak Soft** (`#C8AEDF`): Dark-mode purple tint — icon glyphs, outline-button border/text on dark.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#F5F3F8`): Primary light canvas — a faint warm lilac-white, never pure white.
- **Surface** (`#FFFFFF`): Cards, list sections, sheets — pure white lifts above the canvas.
- **Surface Pressed** (`#EDE9F2`): Pressed/selected row state.
- **Divider** (`#E5E1EC`): Hairline dividers in legends and lists.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#14121A`): Primary dark canvas — deep aubergine tinted from Oak, NOT pure black.
- **Dark Surface 1** (`#1E1B27`): Cards, list sections, sheets.
- **Dark Surface 2** (`#292533`): Elevated surfaces, icon chips, pressed rows.
- **Dark Divider** (`#383143`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#1F1A2B`): Primary text — a near-black aubergine, NOT pure black.
- **Text Secondary Light** (`#6E6480`): Metadata, captions, percentages.
- **Text Tertiary Light** (`#9C92AD`): Disabled, placeholders.
- **Text Primary Dark** (`#F2EFF6`): Primary text on dark.
- **Text Secondary Dark** (`#ADA4BC`): Metadata on dark.
- **Text Tertiary Dark** (`#756B85`): Disabled / placeholder on dark.

### Allocation / Chart Palette
The donut and any portfolio chart use a fixed warm allocation palette (consistent slice → asset-class mapping).

| Slice / Role | Color |
|--------------|-------|
| Large Company | `#5A2B82` (Oak) |
| Small Company | `#6FBF4E` (Green) |
| International | `#45C2B0` (Teal) |
| Real Estate / REIT | `#F2C84B` (Gold) |
| Bonds | `#F2785C` (Coral) |
| Emerging Mkts | `#9B6FD4` (Lilac) |

### Semantic
- **Positive / Gain** (`#6FBF4E` light / `#84D962` dark): All-time gain, daily up, "+$" cashback.
- **Negative / Loss** (`#E0584F` light / `#FF6B6B` dark): Daily down, negative delta.
- **Warning Amber** (`#E0A52A` light / `#F2C84B` dark): Funding/verification warnings.
- **Pending Grey** (`#9C92AD`): Pending Round-Ups / processing transfers (slightly faded).

## 3. Typography Rules

### Font Family
- **Brand & Product**: a rounded geometric humanist sans (Acorns uses a friendly rounded face across brand and product for an approachable, trustworthy tone).
- **Implementation Fallback**: `Nunito Sans` (by Vernon Adams / Jacques Le Bailly, SIL OFL) — a well-balanced rounded humanist sans, the closest freely-licensable substitute; ships an optical-size axis.
- **Numerals**: tabular figures (`font-feature-settings: "tnum"`) for the portfolio total, allocation values, and any aligned currency column.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Rounded', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Portfolio Total | Nunito Sans | 38pt | 900 | 1.05 | -1pt | The headline dollar figure — tabular |
| Hero Number | Nunito Sans | 28pt | 900 | 1.1 | -0.5pt | Round-Ups total, sub-balances — tabular |
| Section Title | Nunito Sans | 22pt | 800 | 1.2 | -0.3pt | "Invest", screen titles |
| Subsection | Nunito Sans | 18pt | 800 | 1.25 | -0.2pt | "Round-Ups", card titles |
| Body | Nunito Sans | 16pt | 400 | 1.45 | 0pt | Motivational copy, sheet body |
| Row Title | Nunito Sans | 15pt | 700 | 1.3 | -0.1pt | Legend item, Found Money brand |
| Amount | Nunito Sans | 15pt | 800 | 1.3 | -0.1pt | Legend value, "+$" cashback (tabular) |
| Meta | Nunito Sans | 13pt | 600 | 1.35 | 0pt | "1% back · 3 days ago", percentages |
| Label / Caption | Nunito Sans | 12pt | 700 | 1.3 | 0.4pt | "ALL TIME", "ROUND-UPS" (uppercased) |
| Button | Nunito Sans | 16pt | 800 | 1.0 | 0pt | Pill-button label |
| Tab Label | Nunito Sans | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |
| Delta | Nunito Sans | 14pt | 700 | 1.2 | 0pt | "▲ $214.38 (2.7%)" |

### Principles
- **Portfolio total leads**: the largest, heaviest element on the Invest screen — the number you watch grow.
- **Heavy weights for friendliness**: Acorns leans on 800–900 for headline numbers and titles; the rounded face at heavy weight reads warm and confident, not aggressive.
- **Tabular figures wherever money appears**: portfolio total, allocation values, deltas align with `tnum`.
- **Uppercase labels with tracking**: section/eyebrow labels ("ALL TIME", "ROUND-UPS") are 12pt w700 uppercased with +0.4pt tracking.
- **Dynamic Type first-class**: portfolio total, section, body, row title, meta scale; tab labels and eyebrow labels stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Button (Invest now / Confirm / Continue)**
- Shape: Full pill, 500pt corner radius
- Background: `#6FBF4E` (Acorn Green)
- Text: `#14302A` (dark forest, for AA contrast on the bright green), Nunito Sans 16pt weight 800
- Padding: 15pt vertical, 30pt horizontal; full-width on sheets (18pt side insets)
- Pressed: background `#5AA53D` + scale 0.98
- Disabled: `#6FBF4E` at 35% opacity, text at 60%

**Secondary Button (One-time invest / brand action)**
- Background: `#5A2B82` (Acorns Oak)
- Text: `#FFFFFF`, Nunito Sans 15pt weight 800
- Corner radius: 500pt
- Pressed: background `#47206A` + scale 0.98

**Outline Button (Adjust portfolio / Edit)**
- Background: transparent
- Text + border: `#5A2B82` light / `#C8AEDF` dark, Nunito Sans 15pt weight 800
- Border: 1.5pt same as text
- Corner radius: 500pt
- Pressed: oak-wash fill `#F1ECF7` light / `#2A1E3A` dark

**Text Button (See history)**
- Font: Nunito Sans 14pt weight 800
- Color: `#6FBF4E` light / `#84D962` dark
- No underline, no background
- Pressed: 60% opacity

### Core Atoms

**Allocation Donut** (the signature atom)
- Multi-segment conic ring, ~168pt outer diameter, ~110pt inner hole (canvas-colored center)
- Segments use the fixed allocation palette (Oak / Green / Teal / Gold / Coral / Lilac), gaps 0–2°
- Center stack: portfolio-style label ("Aggressive") 11pt w700 uppercased secondary + "100% invested" 18pt w800 primary
- Animated on load: each segment sweeps in clockwise over ~600ms ease-out
- Tap a segment: highlights it + scrolls the legend to that holding

**Legend Row**
- 9pt vertical padding, hairline divider between rows
- Leading: 12pt rounded-square color chip matching the slice
- Name: Nunito Sans 14pt w700 primary; trailing percentage 13pt w600 secondary
- Far-trailing: dollar value Nunito Sans 14pt w800 primary, right-aligned (tabular), fixed ~78pt column

**Found Money / Earn Row**
- Height: ~62pt, 22pt horizontal inset
- Leading: 40pt rounded-square (12pt radius) brand chip — brand color + monogram/logo
- Title: brand name Nunito Sans 15pt w700; subtitle "1% back · 3 days ago" 13pt w600 secondary
- Trailing: "+$3.20" Nunito Sans 15pt w800 in green (gain)
- Tap: pushes the offer / cashback detail

**Stat / Sub-balance Tile**
- Surface card, 16pt radius, 16pt padding
- Eyebrow label uppercased 12pt w700 secondary
- Value Nunito Sans 28pt w900 tabular primary
- Optional delta line in green/red with ▲/▼

### Navigation

**Invest Header**
- Screen title "Invest" Nunito Sans 22pt w800 left-aligned
- Trailing: 34pt circular notification bell (surface-2 fill, Oak-soft glyph)
- Canvas-colored; 0.5pt divider appears when content scrolls under

**Bottom Tab Bar**
- Height: 52pt + safe area; background `#FFFFFF` light / `#14121A` dark with 0.5pt top divider, 20px blur
- Tabs (5): Invest, Later, Spend, Earn, Profile
- Icon size: 22pt; active = filled glyph in Acorn Green `#6FBF4E` (light) / Green Bright `#84D962` (dark); inactive = stroke glyph in tertiary text
- Labels: Nunito Sans 10pt w700, always shown
- No tint pill — active is icon fill + label color change to green

**Large Title (inner screens)**
- Title Nunito Sans 22–28pt w800/900 left-aligned, collapses to 17pt centered on scroll
- Leading back chevron; trailing contextual action

### Input Fields

**Amount Entry (one-time invest / recurring)**
- Large centered amount, Nunito Sans 28–38pt w900, green caret, no input box, system number pad
- "$" prefix in same style at ~70% size
- Helper text below in 13pt w600 secondary

**Text Field**
- Height: 52pt, surface fill, 12pt radius, no border at rest
- Focus: 1.5pt `#6FBF4E` border
- Label floats above on focus in 12pt w700 secondary
- Placeholder: tertiary text, Nunito Sans 16pt w400

**Toggle / Switch (Round-Ups Auto, recurring)**
- iOS-style switch, ON track = `#6FBF4E`, knob white
- Used heavily for automation settings

### Distinctive Components

**Allocation Donut + Legend** — see Core Atoms; the brand's signature data visualization, full-color in both themes.

**Round-Ups Card**
- Full-width minus 18pt insets, 18pt corner radius
- Background: Oak gradient `#5A2B82 → #7B43A8` (150°)
- Eyebrow "ROUND-UPS" 13pt w700 white@85% + "Auto" pill (white@16% fill)
- Accumulated amount 28pt w900 white, "Pending · invests at $5.00" 13pt w600 white@80%
- Tap → Round-Ups detail (multiplier, linked accounts, history)

**Portfolio Total + All-Time Gain**
- Centered: total 38pt w900 tabular; below it "▲ $214.38 (2.7%) all-time" — ▲ + amount + % in green, "all time" in secondary
- Tappable to switch range (1D / 1W / 1M / 1Y / All / Max)

**Found Money / Earn Feed** — partner-cashback rows with green "+$"; see Core Atoms.

**Growth Illustration / Tree Moment**
- Milestone screens use an acorn → sprout → oak illustration in Oak + Green
- Celebratory confetti-leaf animation on first invest / round-number milestones

**Goal / Potential Projection**
- A gentle upward-curve chart (green line, soft green area fill) projecting growth over years
- "If you invest $X/week, you could have $Y by {year}" motivational framing

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 22, 24, 32, 40, 48, 64
- Screen horizontal inset: 18–22pt (cards 18pt; rows/legend/headers 22pt)
- Card internal padding: 16–18pt; section vertical gap 16–22pt
- Donut to legend gap: 18pt

### Grid & Container
- iPhone: single column, full-width cards, centered donut
- iPad: content max-width ~640pt centered; donut + legend can sit side-by-side (donut left, legend right)
- Legend dollar-value column: fixed ~78pt right-aligned

### Whitespace Philosophy
- **The number is the hero**: the portfolio total gets generous space above the donut — nothing competes
- **Calm around color**: the donut and Round-Ups card are the only saturated elements; everything around them breathes
- **One concept per card**: Round-Ups, Found Money, projections each get their own clean card
- **Friendly density**: rows are comfortable (~62pt), labels are airy with tracking — never cramped

### Border Radius Scale
- Subtle (4pt): tiny color chips in legend
- Small (8pt): inline tags
- Standard (12pt): input fields, Found-Money brand chips
- Card (16pt): stat tiles, list sections, sheets
- Comfortable (18pt): Round-Ups card
- Large (24pt): bottom-sheet top corners
- Pill (500pt): all buttons, "Auto" pill
- Circle (50%): the donut, avatars, notification bell

## 6. Depth & Elevation

Acorns is mostly flat; elevation lifts cards off the canvas and floats sheets. The donut and Round-Ups card carry color, not heavy shadow.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, hairline divider | Legend rows, Found Money rows, list sections |
| Card (Level 1) | `0 6px 18px -10px rgba(40,18,70,0.16)` (dark: deeper) | Stat tiles, list cards |
| Round-Ups (Level 1) | Soft Oak-tinted `0 10px 24px -12px rgba(74,32,106,0.4)` | The Round-Ups gradient card |
| Sheet (Level 2) | `0 -8px 28px rgba(20,12,30,0.22)` | Bottom sheets (invest, edit Round-Ups) |
| Modal Scrim | `rgba(15,9,22,0.55)` | Dim behind sheets and modals |

**Shadow Philosophy**: Shadows are soft and warm-tinted, used only to separate cards from the canvas and float sheets — never on the donut (it reads through color and motion) and never on legend/feed rows (dividers do that). In dark mode shadows read weakly, so a 1pt `#383143` border can substitute as the card/sheet elevation cue.

### Motion
- **Donut sweep**: on load, segments draw clockwise from 12 o'clock over ~600ms ease-out; the center label cross-fades in at ~400ms
- **Number ticker**: the portfolio total and Round-Ups amount count up digit-by-digit on change (~300ms ease-out)
- **Round-number celebration**: first invest and milestone amounts trigger a leaf/confetti burst (~700ms) + success haptic
- **Card press**: subtle scale 0.98 + 80ms ease-out on tap-down; releases into a push
- **Range switch**: tapping a range animates the projection/total with a 250ms crossfade
- **Found Money credit**: a new "+$" row slides in from top (250ms) with a brief green highlight wash that fades over 800ms
- **Tab change**: instant icon/label color crossfade (120ms); no slide
- **Push navigation**: standard iOS push (300ms) into detail
- **Haptics**: light impact on card press and range switch, success haptic on completed invest / milestone, soft tick on tab change

## 7. Do's and Don'ts

### Do
- Pair Acorns Oak (`#5A2B82`) and Acorn Green (`#6FBF4E`) — purple for brand/structure, green for action and growth
- Make the Invest dashboard the signature screen — portfolio total, allocation donut, Round-Ups, Found Money
- Render the allocation donut with the fixed warm palette + a center portfolio-style label + a legend
- Make the portfolio total the headline number — big, w900, tabular, with an all-time gain in green and a ▲
- Render Round-Ups as a purple gradient card with accumulated spare change and the $5 invest threshold
- Show Found Money / Earn as a partner-cashback feed with green "+$" amounts
- Use heavy rounded type (Nunito Sans 800–900) for headline numbers — friendly and confident
- Keep copy motivational and plain-English ("Your money is hard at work", "Small amounts add up")
- Use full pill buttons; green = primary action, Oak purple = secondary/brand
- Animate the donut sweep and the number ticker — growth should feel alive

### Don't
- Don't use green and purple interchangeably — green is action/growth, Oak is brand/structure
- Don't use pure black for dark mode — deep aubergine `#14121A` is correct
- Don't use pure white for the light canvas — warm lilac-white `#F5F3F8`, white only for cards
- Don't make the dashboard look like a trading terminal — it is calm, friendly, and motivational
- Don't desaturate the donut or Round-Ups card in dark mode — the data viz stays full-color
- Don't put heavy shadows on the donut or rows — cards lift softly; rows use dividers
- Don't use thin or condensed type — the system lives at 400–900 with a rounded warm face
- Don't put white text directly on bright green — use dark forest `#14302A` for AA contrast
- Don't use a tint pill on the active tab — active is green icon + green label only
- Don't bury the portfolio total — it is the hero of the Invest screen

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Portfolio total 32pt; donut ~144pt; tighter card insets |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above the Invest header |
| iPhone 15/16 Pro Max | 430pt | Portfolio total 42pt; donut ~184pt; larger cards |
| iPad (portrait) | 768pt | Content max-width 640pt centered; donut + legend stacked |
| iPad (landscape) | 1024pt+ | Donut left / legend right side-by-side; detail as secondary pane |

### Dynamic Type
- Portfolio total, section, body, row title, meta, delta: full scale
- Tab labels, eyebrow labels ("ALL TIME"): FIXED (layout-sensitive)
- Donut center label scales modestly; legend values stay tabular-aligned

### Orientation
- iPhone: portrait-primary; rotation supported with card reflow
- iPad: split-view; the donut + legend become the primary pane, holdings detail secondary

### Touch Targets
- Donut segment: tappable arc, ≥44pt effective along the ring thickness
- Legend / Found Money row: full-row tap, ≥56pt effective
- Tab bar icons: 22pt glyph, 44pt hit
- Notification bell: 34pt, ≥44pt hit
- Primary buttons: ≥48pt tall

### Safe Area Handling
- Top: Invest header respects safe area and Dynamic Island
- Bottom: tab bar + home indicator respected; sheets respect bottom inset
- Keyboard: amount entry scrolls above the number pad; the running value never hides
- Sides: 18–22pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Acorn Green (action / growth): `#6FBF4E`
- Green bright (dark positive): `#84D962`
- Green pressed: `#5AA53D`
- Acorns Oak (brand / structure): `#5A2B82`
- Oak bright (Round-Ups gradient top): `#7B43A8`
- Oak soft (dark interactive): `#C8AEDF`
- Light canvas: `#F5F3F8`
- Light surface (cards): `#FFFFFF`
- Dark canvas: `#14121A`
- Dark surface 1 / 2: `#1E1B27` / `#292533`
- Text primary: `#1F1A2B` light / `#F2EFF6` dark
- Text secondary: `#6E6480` light / `#ADA4BC` dark
- Donut palette: Oak `#5A2B82`, Green `#6FBF4E`, Teal `#45C2B0`, Gold `#F2C84B`, Coral `#F2785C`
- Divider: `#E5E1EC` light / `#383143` dark

### Example Component Prompts
- "Build the Acorns portfolio header in SwiftUI: centered, total `$8,142.67` in Nunito Sans 38pt w900 tabular `#1F1A2B`. Below it a delta line: a ▲ glyph + `$214.38 (2.7%)` in Acorn Green `#6FBF4E` + `all time` in `#6E6480`, Nunito Sans 14pt w700. Tappable to cycle range (1D/1W/1M/1Y/All). The number counts up digit-by-digit over 300ms on change."

- "Create the Acorns allocation donut: a conic ring ~168pt outer, ~110pt inner hole (canvas-colored center). Segments using the fixed palette in order — Large Company `#5A2B82` 38%, Small Company `#6FBF4E` 26%, International `#45C2B0` 18%, Real Estate `#F2C84B` 18%. Center stack: 'Aggressive' Nunito Sans 11pt w700 uppercased `#6E6480` + '100% invested' 18pt w800 `#1F1A2B`. On load, each segment sweeps in clockwise from 12 o'clock over 600ms ease-out; center label fades in at 400ms."

- "Render an Acorns legend row: 9pt vertical padding, hairline `#E5E1EC` divider. Leading 12pt rounded-square (4pt radius) color chip matching the slice. Name Nunito Sans 14pt w700 `#1F1A2B`, then percentage 13pt w600 `#6E6480`, then a right-aligned ~78pt dollar-value column Nunito Sans 14pt w800 tabular `#1F1A2B`."

- "Build the Acorns Round-Ups card: full-width minus 18pt insets, 18pt corner radius, background a 150° Oak gradient `#5A2B82 → #7B43A8`. Top row: 'ROUND-UPS' Nunito Sans 13pt w700 white@85% (uppercased) + an 'Auto' pill (white@16% fill, 11pt w800 white). Below: accumulated `$4.62` Nunito Sans 28pt w900 white, then 'Pending · invests at $5.00' 13pt w600 white@80%. Soft Oak-tinted shadow `0 10px 24px -12px rgba(74,32,106,0.4)`. Tap → Round-Ups detail."

- "Create an Acorns Found Money row: 62pt tall, 22pt inset. Leading 40pt rounded-square (12pt radius) brand chip (brand color + white monogram). Brand name Nunito Sans 15pt w700 `#F2EFF6`, subtitle '1% back · 3 days ago' 13pt w600 `#ADA4BC`. Trailing '+$3.20' Nunito Sans 15pt w800 in Green Bright `#84D962`. Tap pushes the offer detail."

- "Create the Acorns primary button: full pill (500pt radius), `#6FBF4E` background, label in dark forest `#14302A` Nunito Sans 16pt w800, 15pt vertical / 30pt horizontal padding, full-width on sheets. Pressed: `#5AA53D` + scale 0.98. Secondary variant: `#5A2B82` Oak background + white text. Outline variant: transparent + 1.5pt `#5A2B82` (light) / `#C8AEDF` (dark) border + same-color text."

### Iteration Guide
1. Two brand colors with distinct jobs: Acorn Green `#6FBF4E` = action + growth; Acorns Oak `#5A2B82` = brand + structure — never interchangeable
2. The Invest dashboard is the signature: portfolio total → allocation donut → Round-Ups card → Found Money
3. The portfolio total is the hero number — big, Nunito Sans w900, tabular, all-time gain in green with a ▲
4. The donut uses the fixed warm palette + a center portfolio-style label + a legend; it sweeps in on load
5. Round-Ups is a purple gradient card with accumulated spare change and the $5 invest threshold
6. Found Money / Earn is a partner-cashback feed with green "+$" amounts
7. Type is heavy and rounded (Nunito Sans 800–900 for numbers/titles) — friendly, not corporate
8. Canvas is warm lilac-white (`#F5F3F8`) / deep aubergine (`#14121A`) — never pure white, never black
9. Green never carries white text directly — use dark forest `#14302A` for AA; no tint pill on the active tab
10. Dark mode dims chrome only — the donut, Round-Ups card, and Found Money greens stay fully saturated
