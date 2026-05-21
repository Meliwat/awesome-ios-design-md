# Design System Inspiration of Taco Bell (iOS)

## 1. Visual Theme & Atmosphere

Taco Bell's iOS app is **a neon, dark-first ordering playground**. Unlike most QSR apps that sit on white, Taco Bell commits to a **purple-tinted near-black canvas (`#0E0A14`)** — the dark isn't neutral gray, it carries a violet bias so the brand purple feels native rather than painted on top. Over that canvas runs the signature **Taco Bell Purple → Electric Magenta gradient (`#702082 → #C72BC8`)**: it's the primary CTA, the active customizer step, the box preview backdrop, and the brand bell plate. The energy color is **Hot Sauce Yellow `#FFC700`** — used for prices, reward chips, and eyebrows — with **Fire Orange `#FF6A1A`** and **Hot Pink `#E928A0`** as occasional heat accents. The mood is bold, playful, slightly rebellious — "Live Más" rendered in pixels.

The signature screen is the **build-your-box customizer**. Taco Bell's product genius is configurability — the Cravings Box, the build-your-own Crunchwrap, "make it a combo," "make it Supreme/Fresco," "add Diablo sauce." The customizer screen is the heart of the app: an **eyebrow + builder title** ("BUILD YOUR BOX" / "Cravings Box"), a **gradient box-preview hero** with a stylized taco illustration, a glow, the box name + item count, and a yellow price; a **horizontally-scrolling numbered step pill row** (Main ✓ → Side ② → Drink ③ → Sweet ④) where the active step glows with the purple-magenta gradient and completed steps go green; then the **option rows** themselves — a food thumbnail, name, description, optional "+$1.00" upcharge, and a control: a magenta **radio** for single-select, or a magenta-outline **stepper** for quantities. A persistent bottom **CTA bar** shows the running box total in yellow and a gradient "Add to Order" button. The second signature surface is the **Cravings menu** — tiles with a purple-gradient food image, name, description, a yellow price, and a gradient "Add" pill that opens the same customizer.

The color system is intentionally loud and gradient-driven. There is no flat single brand color — the purple-to-magenta gradient *is* the brand on screen, and yellow is the spark that draws the eye to value (price, reward, "free"). Everything else is a tightly-controlled neutral on the violet-black so the neon pops. The aesthetic rule: gradients for action and brand presence, yellow for energy/value, deep violet-black for the stage.

Typography is a clean, slightly techy geometric sans (Taco Bell's brand face lineage); on iOS it falls back to SF Pro, and for documentation/catalog **Sora** is the closest free analog. Hierarchy is heavy and confident: screen titles 32pt/800, builder titles 26pt/800, option names 15pt/600, body 16pt/400. Weight (700/800) plus the gradient and yellow carry hierarchy.

**Key Characteristics:**
- **Dark-first, purple-tinted near-black canvas `#0E0A14`** — violet bias so the brand feels native, never neutral gray
- **Purple → Magenta gradient `#702082 → #C72BC8`** is the brand on screen — primary CTA, active step, box backdrop, bell plate
- **Hot Sauce Yellow `#FFC700`** — the energy/value highlight: prices, reward chips, eyebrows, "free"
- **Build-your-box customizer** — the signature flow: gradient box preview + numbered step pills + radio/stepper option rows
- **Numbered step pills** — Main / Side / Drink / Sweet; active glows gradient, done turns green `#36C275`
- **Box-preview hero** — gradient panel with a stylized taco, glow blooms, name + item count + yellow price
- **Persistent bottom CTA bar** — running box total in yellow + a gradient "Add to Order" button
- **Cravings menu tiles** — purple-gradient food image, name, yellow price, gradient "Add" pill → opens customizer
- **Radio + stepper controls** — magenta-tinted single-select radios and magenta-outline quantity steppers
- **Neon accents** — Fire Orange `#FF6A1A`, Hot Pink `#E928A0` for heat/limited-time energy
- **Heavy techy-geometric type** — screen titles 32pt/800; gradient + yellow + weight carry hierarchy
- **Rounded but punchy** — 14pt CTA radius, 20pt box hero, pill chips; bold not soft

## 2. Color Palette & Roles

### Primary (Interactive — Gradient)
- **Brand Gradient**: `linear-gradient(120deg, #702082 0%, #C72BC8 100%)` — primary CTA, active customizer step, box-preview backdrop, "Add" pills, brand bell. This gradient *is* the brand on screen.
- **Taco Bell Purple** (`#702082`): Solid fallback / gradient start; flat purple surfaces and outlines.
- **Purple Bright** (`#8A2BA0`): Hover/secondary purple, mid-gradient.
- **Electric Magenta** (`#C72BC8`): Gradient end; the single-select radio fill, stepper outline, active-tab tint, focus ring.
- **Magenta Pressed** (`#A8239F`): Pressed state for magenta controls.

### Energy / Value
- **Hot Sauce Yellow** (`#FFC700`): Prices, reward chips, eyebrows, "FREE", progress highlights. Text on yellow is `#2A1530` (deep plum-black), never white.
- **Fire Orange** (`#FF6A1A`): Spicy/heat tags, limited-time flares.
- **Hot Pink** (`#E928A0`): Promo accents, "new" energy.

### Canvas & Surfaces (Dark — primary)
- **Purple-Black Canvas** (`#0E0A14`): The primary canvas — near-black with a violet bias.
- **Dark Surface 1** (`#1A1322`): Cards, step pills, customizer rows base.
- **Dark Surface 2** (`#261B32`): Elevated tiles, Cravings cards, badge fills.
- **Dark Divider** (`#34273F`): Hairline dividers between option rows and sections.

### Canvas & Surfaces (Light — secondary)
Taco Bell is dark-first, but a light theme keeps the same gradient brand on a warm-white stage.
- **Canvas Light** (`#FFFFFF`): Light canvas.
- **Surface Light 1** (`#F6F2F8`): Cards, step pills (violet-tinted off-white).
- **Surface Light 2** (`#EDE6F1`): Elevated tiles.
- **Divider Light** (`#E3D9EA`): Hairlines.

### Text
- **Text Primary Dark** (`#F4EFF7`): Primary text on dark — a violet-tinted near-white.
- **Text Secondary Dark** (`#A99BB6`): Descriptions, metadata, step labels.
- **Text Tertiary Dark** (`#6E6079`): Disabled, low-emphasis.
- **Text Primary Light** (`#1F1626`): Primary text on light.
- **Text Secondary Light** (`#6E6079`): Metadata on light.
- **Text On Yellow** (`#2A1530`): Mandatory text/icon over `#FFC700`.
- **Text On Gradient** (`#FFFFFF`): Text/icon over the purple-magenta gradient.

### Roles Summary

| Role | Dark (primary) | Light (secondary) |
|------|----------------|-------------------|
| Canvas | `#0E0A14` | `#FFFFFF` |
| Surface 1 | `#1A1322` | `#F6F2F8` |
| Surface 2 | `#261B32` | `#EDE6F1` |
| Divider | `#34273F` | `#E3D9EA` |
| Text Primary | `#F4EFF7` | `#1F1626` |
| Text Secondary | `#A99BB6` | `#6E6079` |
| Brand (gradient) | `#702082 → #C72BC8` | `#702082 → #C72BC8` |
| Value highlight | `#FFC700` | `#FFC700` |
| Error | `#FF4D6D` | `#E11D48` |

### Semantic
- **Success Green** (`#36C275`): Completed customizer step, order placed, "saved".
- **Error Red** (`#FF4D6D`): Item unavailable, payment failure, destructive delete.
- **Warning Orange** (`#FF6A1A`): "Few left", "ends tonight" (shares the fire-orange accent).
- **Info Magenta** (`#C72BC8`): Informational links, hints.
- **Reward Yellow** (`#FFC700`): Reward chips, points, "FREE" flags (the value highlight).

## 3. Typography Rules

### Font Family
- **Brand / All UI**: Taco Bell's brand sans lineage — a clean, slightly techy geometric humanist (used across the app's headings and UI). Bundled in the official app.
- **iOS Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Web/Catalog Fallback**: `Sora` (closest free geometric sans with the same confident weight range)
- Numerals are tabular in prices, totals, step counts, and timers so values align.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Brand Sans | 32pt | 800 | 1.2 | -0.4pt | "Live Más", "Menu" large title |
| Builder Title | Brand Sans | 26pt | 800 | 1.25 | -0.5pt | "Cravings Box" customizer hero |
| Section Header | Brand Sans | 22pt | 700 | 1.3 | -0.2pt | "Build your box" |
| Step / Card Title | Brand Sans | 18pt | 800 | 1.3 | -0.3pt | "Choose your side" |
| Body | Brand Sans | 16pt | 400 | 1.5 | 0pt | Descriptions, paragraph copy |
| Option Name | Brand Sans | 15pt | 600 | 1.4 | 0pt | "Nacho Fries" |
| Price / Total | Brand Sans | 15pt | 800 | 1.3 | 0pt | "$5.99" — yellow, tabular |
| Meta | Brand Sans | 14pt | 400 | 1.4 | 0pt | Option description, "+$1.00" |
| Eyebrow | Brand Sans | 12pt | 700 | 1.1 | 1.0pt | "BUILD YOUR BOX" — UPPERCASE, yellow |
| Step Pill | Brand Sans | 12pt | 700 | 1.0 | 0pt | "Main", "Side" + number badge |
| Button (Primary) | Brand Sans | 15pt | 800 | 1.0 | 0.2pt | Gradient CTA / "Add to Order" |
| Chip / Badge | Brand Sans | 12pt | 800 | 1.1 | 0.3pt | "STEP 2 OF 4", "FREE REWARD" |
| Caption | Brand Sans | 12pt | 400 | 1.4 | 0pt | T&C, tiny print |
| Tab Label | Brand Sans | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |

### Principles
- **Weight + gradient + yellow carry hierarchy**: emphasis lives at 700/800, in the purple-magenta gradient (action), and in Hot Sauce Yellow (value). The builder title and the yellow price are the two anchors of the customizer.
- **Eyebrows are yellow and UPPERCASE**: every builder/section gets a 12pt/700 +1pt-tracked yellow eyebrow — it's a signature Taco Bell editorial tic.
- **Tabular numerals for money/steps**: prices, the running box total, "STEP 2 OF 4", and timers all align.
- **Punchy, not soft**: heavy weights + tight tracking on titles keep the tone bold and a little rebellious.
- **Dynamic Type**: titles, body, option copy scale; step pills, eyebrows, chips/badges, tab labels stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Add to Order / Continue / Checkout)**
- Shape: Rounded rectangle, 14pt corner radius
- Background: `linear-gradient(120deg, #702082 0%, #C72BC8 100%)`
- Text: `#FFFFFF`, Brand Sans 15pt weight 800
- Padding: 14–15pt vertical, 28pt horizontal (full-width in the bottom CTA bar)
- Pressed: `filter: brightness(0.95)` + scale 0.98
- Disabled: `#261B32` fill, text `#6E6079`

**Value Button (Apply Reward / Redeem)**
- Background: `#FFC700` (Hot Sauce Yellow)
- Text: `#2A1530` (deep plum-black — never white), Brand Sans 15pt weight 800
- Corner radius: 14pt
- Pressed: `filter: brightness(0.95)`

**Outline Button (Customize / Edit)**
- Background: transparent
- Border: 1.5pt `#C72BC8` (magenta)
- Text: `#C72BC8`, Brand Sans 14pt weight 700
- Corner radius: 12pt
- Padding: 12pt vertical, 22pt horizontal
- Pressed: background `rgba(199,43,200,0.12)`

**Text Button (Skip this step / See all)**
- Text: `#FFC700` (yellow), Brand Sans 14pt weight 800
- No background, no border
- Pressed: opacity 0.6

**Add Pill (Cravings tile)**
- Pill, 999pt radius, brand gradient fill
- "Add" / "ADD" in `#FFFFFF`, Brand Sans 12pt weight 800 UPPERCASE +0.3pt
- Padding: 7pt vertical, 16pt horizontal
- Tapping opens the item customizer

### Core Atoms

**Step Pill**
- Pill, 999pt radius, 30pt tall
- Leading 16pt number badge (circle) + label
- Default: `#1A1322` fill, 1pt `#34273F` border, text/badge `#A99BB6`
- Active: brand gradient fill, white text, badge `rgba(255,255,255,0.25)`
- Done: 1pt `#36C275` border, text `#F4EFF7`, badge `#36C275` with a `✓` in `#06210F`
- Horizontally scrollable row, 8pt gap

**Single-Select Radio**
- 24pt circle, 2pt border
- Off: `#34273F` border, transparent
- On: `#C72BC8` border + a 16pt inset `#C72BC8` filled dot
- Tap target ≥ 44pt; whole option row is tappable

**Quantity Stepper**
- `−` / `+` in 26pt circles, 1.5pt `#C72BC8` outline, magenta glyphs
- Quantity centered, Brand Sans 14pt/800 `#F4EFF7`, tabular
- Used for add-on quantities (extra sauce, sides)

**Reward / Status Chip**
- Pill, 999pt radius
- "STEP 2 OF 4" / "FREE REWARD": brand gradient or yellow `#FFC700` (text `#2A1530`)
- "DONE": green `#36C275` (text `#06210F`); "CANTINA": magenta-outline transparent
- Brand Sans 12pt weight 800 UPPERCASE +0.3pt

**Eyebrow**
- Brand Sans 12pt weight 700, UPPERCASE, +1pt tracking
- Color `#FFC700` (yellow) above builder/section titles

### Navigation

**Builder Header**
- Yellow eyebrow ("BUILD YOUR BOX")
- Builder title — Brand Sans 26pt/800 `#F4EFF7`
- Sub-line — 13pt `#A99BB6`
- No heavy chrome; the box-preview hero sits directly beneath

**Bottom Tab Bar**
- Height: 70pt incl. safe area
- Background: `rgba(14,10,20,0.96)` with blur, 0.5pt top `#34273F` divider
- Tabs (5): **Home**, **Menu**, **Rewards**, **Bag**, **Account**
- Icon 22pt; active = filled glyph in `#C72BC8` (magenta); inactive = stroke `#7A6C86`
- Labels always shown, 10pt/700
- Bag shows a count badge (gradient dot, white number)

**Top Nav (Menu / Item detail)**
- Transparent or `#0E0A14` solid; leading back chevron (circular icon button)
- Center: screen title, Brand Sans 18pt/800
- Trailing: search / bag as icon buttons
- Sticky category strip on the menu once scrolled

**Persistent Bottom CTA Bar**
- `rgba(14,10,20,0.96)` background, 1pt `#34273F` top divider
- Left: "Box total" eyebrow + amount (`#F4EFF7` 19pt/800, the amount sometimes yellow in cart)
- Right: full-width-flexed gradient "Add to Order" button
- Always visible during a build; total updates live

### Input Fields

**Search Field**
- Height: 48pt
- Background: `#1A1322` / `#F6F2F8`
- Corner radius: 14pt
- Leading 18pt `magnifyingglass` `#A99BB6`
- Placeholder: "Search the menu" `#A99BB6` 15pt
- Focus: 1.5pt `#C72BC8` border + faint magenta glow

**Form Field (address / payment)**
- Height: 52pt
- Background: `#1A1322` / `#F6F2F8`
- Corner radius: 12pt
- Floating label 11pt `#A99BB6` on focus; text `#F4EFF7` 15pt
- Focus: 1.5pt `#C72BC8` border
- Error: 1.5pt `#FF4D6D` border + 12pt red helper text

**Promo Code Field**
- Inline, dashed `#C72BC8` border, 12pt radius
- "APPLY" trailing text button in `#FFC700` 13pt/800 UPPERCASE

### Distinctive Components

**Box-Preview Hero**
- 20pt rounded panel, brand-purple-gradient background with radial magenta + yellow glow blooms
- Center: stylized food/taco illustration (or product photo)
- Bottom-left: box name (Brand Sans 13pt/800 white) + item count sub (12pt yellow)
- Bottom-right: box price in `#FFC700` 20pt/800
- Updates as the user completes steps (illustration/contents morph)

**Numbered Step Pill Row**
- Horizontally-scrollable pills representing the build sequence
- Exactly one **active** (gradient), zero-or-more **done** (green), the rest **default**
- Tapping a pill jumps to that step (if reachable)

**Customizer Option Row**
- 46pt rounded food thumbnail + name (15pt/600) + description (12pt secondary) + optional "+$1.00" upcharge (13pt/700 secondary) + control (radio or stepper)
- 1pt `#34273F` divider between rows
- Selecting advances the active step pill and updates the running total + box hero

**Cravings Menu Tile**
- 16pt rounded card on `#261B32`
- Leading 72pt purple-gradient food image
- Body: name (15pt/800), description (12pt secondary), then a footer row: yellow price + trailing gradient "Add" pill
- "Add" opens the customizer for that item

**Make-It-A-Combo Upsell Sheet**
- Bottom sheet, 28pt top corners
- "Make it a combo?" gradient header, +$X yellow delta, side/drink quick-pick
- "Add combo" gradient CTA / "No thanks" text button

**Rewards / Cantina Card**
- Gradient or dark card with points balance in `#FFC700`, progress toward next reward
- "FREE REWARD" yellow chip when redeemable

**Sauce Picker**
- A signature Taco Bell touch — Mild / Hot / Fire / Diablo chips with heat-colored borders (`#FFC700` → `#FF6A1A` → `#FF4D6D`), multi-select with the magenta stepper

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Option row vertical padding: 14pt
- Screen horizontal inset: 18pt
- Intra-card content gaps: 2–10pt
- Section/builder header top margin: 16pt

### Grid & Container
- iPhone: single-column build flow; step pills in a scrollable row; option rows full-width
- Box-preview hero spans content width at ~150pt tall
- Cravings menu: single-column tiles (or 2-up on larger devices)
- iPad: 2-column (menu list + live box preview/customizer panel), max content 760pt centered, 24pt insets
- The bottom CTA bar is pinned and full-width

### Whitespace Philosophy
- **The stage is dark and roomy**: the violet-black canvas + generous gaps let the neon gradient and yellow pop
- **The box hero owns the top of the customizer**: it's the largest, most colorful block — it shows what you're building
- **Option rows breathe**: 14pt vertical padding + thin dividers, never boxed-in, so the list scans fast
- **Gradient = action, yellow = value, violet-black = stage**: a disciplined three-role color logic keeps the loud palette coherent

### Border Radius Scale
- Square (0pt): dividers, full-bleed edges
- Subtle (6pt): tiny tags
- Compact (10pt): inline chips, small controls
- Standard (12–14pt): buttons, search/form fields, Cravings cards
- Hero (20pt): box-preview panel
- Sheet (28pt): bottom sheets (combo upsell, sauce picker)
- Pill (999pt): step pills, "Add" pills, reward/status chips, steppers
- Circle (50%): radios, stepper buttons, avatars

## 6. Depth & Elevation

Taco Bell uses glow more than shadow — on a dark stage, a colored bloom reads as "this is the live/active thing."

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, 1pt divider/border | Option rows, default step pills, list cards |
| Glow (Level 1) | Radial magenta/yellow bloom inside the panel | Box-preview hero, active step pill, focused field |
| Lifted (Level 2) | `rgba(0,0,0,0.5) 0 4px 14px` | Bottom CTA bar, floating "Add" confirmations |
| Sheet (Level 3) | `rgba(0,0,0,0.55) 0 -6px 28px` | Bottom sheets (combo upsell, sauce picker) |
| Modal Overlay | `rgba(8,5,12,0.6)` | Dim behind sheets and dialogs |

**Shadow Philosophy**: Cards are flat (a divider/border defines them). The expressive depth cue is **glow**: the box-preview hero blooms purple-magenta and yellow from its corners; the active step pill and a focused field carry a faint magenta halo. Drop shadow is reserved for the pinned CTA bar and bottom sheets so they detach from the scrolling stage. On the (secondary) light theme, glows soften and a normal soft shadow replaces them on the hero and sheets.

### Motion
- **Add to Order**: the gradient CTA flashes brighter (brightness 1.0→1.12→1.0, 220ms), the box total rolls up, a soft haptic fires; the item animates into the Bag tab with a magenta trail
- **Step advance**: when an option is chosen, the completed step pill cross-fades default → gradient → green over 250ms; the row's radio fills with a 150ms scale-in; the box-preview contents morph (200ms ease-out)
- **Box-preview build**: as steps complete, the illustration/photo updates with a quick crossfade + a subtle glow pulse
- **Cravings "Add" → customizer**: shared-element gradient image zoom into the customizer, 300ms ease-out
- **Combo upsell sheet**: slides up 300ms ease-out from the CTA; dim fades; drag-to-dismiss with rubber-banding
- **Sauce pick**: chip border ignites (color sweep) on select; multi-select stepper bumps with a soft tick
- **Reward redeem**: yellow chip flips, a confetti-free but bright scale bounce (1.05) + success haptic
- **Pull-to-refresh**: gradient bell spinner; haptic tick on release
- **Haptics**: soft impact on add-to-order, light tick on step/radio/sauce select, success notification on order placed / reward redeemed

## 7. Do's and Don'ts

### Do
- Use the purple → magenta gradient (`#702082 → #C72BC8`) for primary CTAs, the active step, the box hero — it's the brand on screen
- Keep the canvas a purple-tinted near-black (`#0E0A14`) — dark-first, never neutral gray
- Use Hot Sauce Yellow (`#FFC700`) for prices, reward chips, and UPPERCASE eyebrows — text on it is `#2A1530`, never white
- Make the build-your-box customizer the centerpiece: gradient box hero + numbered step pills + radio/stepper rows
- Show exactly one active step (gradient), mark completed steps green (`#36C275`)
- Keep a persistent bottom CTA bar with a live-updating total + gradient "Add to Order"
- Use glow (radial blooms) on the box hero and active step instead of heavy shadows
- Put a yellow eyebrow above builder/section titles, UPPERCASE with +1pt tracking
- Use magenta radios for single-select and magenta-outline steppers for quantities
- Roll the box total and morph the box-preview contents as steps complete
- Keep food imagery vivid against the neon chrome; use tabular numerals for money/steps

### Don't
- Don't use a flat single brand color where the gradient belongs — the purple→magenta gradient is the identity
- Don't put the app on white by default — it's dark-first with a violet bias
- Don't use a neutral gray dark canvas — `#0E0A14` carries purple
- Don't put white text on yellow — `#2A1530` deep plum-black only
- Don't bury the box-preview hero or the running total — they anchor the build
- Don't show multiple active steps — exactly one gradient-active at a time
- Don't replace the glow language with flat Material shadows on the hero/active elements
- Don't desaturate food imagery — the neon stage stays vivid
- Don't drop the yellow eyebrows — they're a signature editorial tic
- Don't animate sluggishly — motion is punchy and quick (150–300ms), tied to building/ordering
- Don't use timid sentence-case chips — step/reward chips are UPPERCASE and bold

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single column; box hero ~130pt; step pills scroll; compact builder header |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; builder header respects it |
| iPhone 15/16 Pro Max | 430pt | Taller box hero (~170pt); larger illustration |
| iPad (portrait) | 768pt | 2-column: menu list + live customizer/box-preview panel; content max 760pt centered |
| iPad (landscape) | 1024pt+ | Same 2-column; persistent bag/summary on the right |

### Dynamic Type
- Screen titles, builder titles, section headers, body, option copy: full scale
- Step pills, eyebrows, chips/badges, tab labels, the box-hero price: FIXED (layout-critical)
- Prices/totals scale one step then truncate gracefully

### Orientation
- iPhone: portrait-locked for the build flow (category standard)
- iPad: supports landscape with a 2-column build + sticky summary

### Touch Targets
- Step pill: ≥ 30pt tall, full-pill tap, 44pt hit
- Radio / stepper button: visual ~24–26pt, 44pt hit area
- Gradient CTA: ≥ 48pt tall
- Tab bar icon: 22pt glyph, 44pt+ hit
- Option row: full-row tap toggles the radio

### Safe Area Handling
- Top: builder header + Dynamic Island respected
- Bottom: the persistent CTA bar sits above the tab bar, both above the home indicator
- Keyboard: address/promo forms scroll above keyboard; combo/sauce sheets position above it

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark, primary): `#0E0A14`
- Surface 1 / 2: `#1A1322` / `#261B32`
- Divider: `#34273F`
- Text primary: `#F4EFF7` (dark) / `#1F1626` (light)
- Text secondary: `#A99BB6` (dark) / `#6E6079` (light)
- Brand gradient: `linear-gradient(120deg, #702082 0%, #C72BC8 100%)`
- Magenta (controls/focus): `#C72BC8`
- Value highlight: `#FFC700` — text on it is `#2A1530`
- Success (step done): `#36C275`
- Accents: Fire Orange `#FF6A1A`, Hot Pink `#E928A0`
- Error: `#FF4D6D`

### Example Component Prompts
- "Build the Taco Bell box-preview hero in SwiftUI: a 20pt rounded panel filled with `linear-gradient(150deg, #702082, #4A1559, #2B0C36)`. Add two radial glow blooms — magenta `rgba(199,43,200,0.35)` top-right and yellow `rgba(255,199,0,0.22)` bottom-left. Center a stylized taco illustration. Bottom-left: box name in Sora 13pt/800 white + an item-count sub in 11pt `#FFC700`. Bottom-right: the box price in `#FFC700` 20pt/800. When a step completes, crossfade the illustration and pulse the glow."

- "Create the Taco Bell numbered step pill row: a horizontally-scrollable HStack, 8pt gap. Each pill is 30pt tall, 999pt radius, with a leading 16pt circular number badge + label in Sora 12pt/700. Default: `#1A1322` fill, 1pt `#34273F` border, `#A99BB6` text. Active (exactly one): brand gradient `#702082 → #C72BC8` fill, white text, badge `rgba(255,255,255,0.25)`. Done: 1pt `#36C275` border, `#F4EFF7` text, badge `#36C275` with a `✓` in `#06210F`."

- "Render a Taco Bell customizer option row: a 46pt rounded food thumbnail, then name in Sora 15pt/600 `#F4EFF7` + a 12pt `#A99BB6` description, an optional '+$1.00' upcharge in 13pt/700 `#A99BB6`, and a trailing control. Single-select uses a 24pt radio (off: 2pt `#34273F` border; on: 2pt `#C72BC8` border + a 16pt inset `#C72BC8` dot). Quantity uses a stepper: `−`/`+` in 26pt circles with a 1.5pt `#C72BC8` outline and magenta glyphs, quantity in Sora 14pt/800 tabular. 1pt `#34273F` divider between rows. Selecting advances the active step pill and updates the running total."

- "Build the Taco Bell persistent bottom CTA bar: `rgba(14,10,20,0.96)` background, 1pt `#34273F` top divider. Left: a 'Box total' eyebrow 11pt `#A99BB6` over the amount in `#F4EFF7` 19pt/800 (tabular). Right: a flex-grow gradient button 'Add to Order' (`linear-gradient(120deg, #702082, #C72BC8)`, white Sora 15pt/800, 14pt radius, 15pt padding). On tap, brighten the gradient 1.0→1.12→1.0 over 220ms, roll the total, soft haptic, and animate the item into the Bag tab."

- "Create a Taco Bell Cravings menu tile: a 16pt rounded card on `#261B32`. Leading 72pt purple-gradient food image. Body: name in Sora 15pt/800 `#F4EFF7`, a 12pt `#A99BB6` description, then a footer row with the price in `#FFC700` 15pt/800 and a trailing gradient 'Add' pill (999pt radius, white Sora 12pt/800 UPPERCASE). Tapping 'Add' shared-element-zooms the gradient image into the customizer over 300ms."

- "Render the Taco Bell bottom tab bar: 5 tabs — Home, Menu, Rewards, Bag, Account. 22pt icons; active = filled glyph in `#C72BC8`; inactive = stroke `#7A6C86`; labels 10pt/700 always shown. The Bag tab has a gradient count badge with a white number. Bar background `rgba(14,10,20,0.96)` blurred with a 0.5pt `#34273F` top divider."

### Iteration Guide
1. The purple → magenta gradient (`#702082 → #C72BC8`) is the brand on screen — CTAs, active step, box hero
2. Canvas is a purple-tinted near-black `#0E0A14` — dark-first, never neutral gray
3. Hot Sauce Yellow `#FFC700` is the value highlight (prices, rewards, eyebrows); text on it is `#2A1530`
4. The build-your-box customizer is the centerpiece: gradient box hero + numbered step pills + radio/stepper rows
5. Exactly one step pill is gradient-active; completed steps turn green `#36C275`
6. A persistent bottom CTA bar shows a live-updating total + a gradient "Add to Order"
7. Depth is glow (radial blooms on the hero/active step), not heavy shadow — drop shadow only for the CTA bar & sheets
8. Yellow UPPERCASE eyebrows (+1pt tracking) sit above builder/section titles — a signature tic
9. Magenta radios for single-select, magenta-outline steppers for quantities
10. Motion is punchy and quick (150–300ms): step pill cross-fade to green, total roll, box-hero morph, gradient-CTA flash
