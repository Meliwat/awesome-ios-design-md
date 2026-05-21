# Design System Inspiration of Domino's (iOS)

## 1. Visual Theme & Atmosphere

Domino's iOS app is built around a single, famous moment of anticipation: the pizza tracker. The canvas is clean white (`#FFFFFF`) with a light gray surface (`#F4F4F4`) chunking content into clear, scannable blocks — the app's job is to get an order placed fast and then turn the wait into a show. Everything is bright, high-contrast, and confident, optimized for a hungry user who wants to tap "reorder", customize a pie, and watch a progress bar fill from "Prep" to "Out for delivery".

The accent system is the brand's two-color identity: Domino's Red (`#E31837`) for primary action — order CTAs, the build-your-pizza add buttons, deal cards, the active tracker stage — and Domino's Blue (`#006491`) for the secondary, informational layer — links, the tracker's completed checkmarks, and supporting badges. The red-and-blue split is lifted straight from the domino logomark (a red-and-blue domino square), and the two colors never blur roles: red is "do it", blue is "info / done".

Typography is a sturdy grotesque — Archivo (with Helvetica as the system-tone fallback) at weights 400 / 700. The hierarchy is practical and bold: screen titles at 26pt weight 700, the tracker's current-stage label at 22pt weight 700, menu item names at 17pt weight 700, prices at 16pt weight 700, and a recurring 13pt secondary gray for descriptions, ETA strings, and calorie counts. There are no delicate display moments — the type is workmanlike and legible because the star of the app is the moving tracker, not the words.

**Key Characteristics:**
- Clean white canvas (`#FFFFFF`) with `#F4F4F4` surface blocks — bright, scannable, fast
- Domino's Red (`#E31837`) for action — order, build, deals, active tracker stage
- Domino's Blue (`#006491`) for info — links, completed checkmarks, supporting badges
- Five-stage pizza tracker (Prep → Bake → Box → Quality Check → Out for delivery) — the signature
- Build-your-pizza layout — size, crust, sauce, cheese, toppings with a live pie preview
- Deal cards — bold red-bordered tiles with price callouts
- Domino-square logo motif — the red/blue split square as a recurring brand mark
- Archivo / Helvetica type, weights 400 / 700 — sturdy and workmanlike
- Tracker stage fill — the active segment animates as the order progresses

## 2. Color Palette & Roles

### Primary
- **Domino's Red** (`#E31837`): Primary CTA ("Checkout", "Add to Order"), build add buttons, deal accents, active tracker stage, selected state.
- **Red Pressed** (`#C2122E`): Active/pressed state for red buttons.
- **Red Tint** (`#FCE7EB`): Light wash behind "added" chips and the active tracker stage label.

### Secondary
- **Domino's Blue** (`#006491`): Links, completed-stage checkmarks on the tracker, informational badges, secondary buttons.
- **Blue Pressed** (`#00547A`): Pressed state for blue elements.
- **Blue Tint** (`#E0EEF4`): Wash behind informational banners and completed tracker segments.

### Canvas & Text
- **Canvas White** (`#FFFFFF`): Primary canvas, cards, sheets.
- **Surface** (`#F4F4F4`): Section separators, grouped backgrounds, skeletons, search fill.
- **Surface Deep** (`#EAEAEA`): Pressed state on surface, alternating zones.
- **Divider** (`#E2E2E2`): Hairline dividers between list rows and card sections.
- **Text Primary** (`#1F1F1F`): Titles, item names, prices, primary UI text.
- **Text Secondary** (`#6E6E6E`): Descriptions, ETA strings, calorie counts, metadata.
- **Text Tertiary** (`#9A9A9A`): Disabled labels, very low-emphasis captions.

### Semantic
- **Success Green** (`#1E8E3E`): Order-delivered state, "saved" confirmation.
- **Tracker Track** (`#E2E2E2`): The unfilled portion of the tracker progress rail.
- **Warning Amber** (`#F5A623`): "Store closing soon", limited availability.
- **Error Red** (`#E31837`): Validation errors, item-unavailable (shares brand red).

### Dark Mode (Limited Use)
Domino's iOS app is light-only by design — the bright, high-contrast catalog and tracker depend on white. A dark variant exists for system dark mode but is not the designed experience.
- **Dark Canvas** (`#161616`)
- **Dark Surface** (`#222222`)
- **Dark Text** (`#FFFFFF`)
- Domino's Red and Blue stay identical — both read on dark.

## 3. Typography Rules

### Font Family
- **Primary**: `Archivo` (sturdy grotesque used across the product)
- **System-tone fallback**: `Helvetica` / `Helvetica Neue` — the closest workmanlike substitute
- **Numerals**: Archivo tabular figures for prices, ETAs, counts (`font-variant-numeric: tabular-nums`)
- **Fallback Stack**: `'Archivo', 'Helvetica Neue', -apple-system, BlinkMacSystemFont, 'SF Pro Text', Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Archivo | 26pt | 700 | 1.2 | -0.3pt | "Order", "Tracker" nav large title |
| Tracker Stage | Archivo | 22pt | 700 | 1.2 | -0.2pt | Current stage headline ("In the oven") |
| Section Header | Archivo | 18pt | 700 | 1.25 | -0.2pt | "Specialty Pizzas", "Sides" |
| Item Name | Archivo | 17pt | 700 | 1.3 | 0pt | Menu item / topping title |
| Price | Archivo | 16pt | 700 | 1.2 | 0pt | Item price, tabular figures |
| Body | Archivo | 15pt | 400 | 1.45 | 0pt | Descriptions, instructions |
| Button (Primary) | Archivo | 16pt | 700 | 1.0 | 0.3pt | Red "Checkout", "Add to Order" |
| Deal Price | Archivo | 28pt | 700 | 1.0 | -0.4pt | Big price callout on deal cards, tabular |
| Meta / ETA | Archivo | 13pt | 400 | 1.3 | 0pt | "25–35 min", "Est. 7:45 PM", calories |
| Badge | Archivo | 11pt | 700 | 1.2 | 0.4pt | "NEW", "POPULAR", "DEAL" |
| Tab Label | Archivo | 11pt | 700 | 1.0 | 0.2pt | Bottom tab bar labels |
| Caption | Archivo | 11pt | 400 | 1.3 | 0pt | Fine print, allergen notes |

### Principles
- **Bold carries the hierarchy**: Titles, item names, prices, and buttons are all weight 700 — Domino's leans on weight, not size jumps, for emphasis
- **Workmanlike, not delicate**: No light weights, no thin display type — the tracker is the show, type is functional support
- **Tabular figures for numbers**: Prices, ETAs, deal callouts, and order totals use tabular Archivo so they align
- **Secondary gray for support copy**: Descriptions, ETA strings, and calorie counts use `#6E6E6E` at 13-15pt to recede behind bold names and prices
- **Slight negative tracking on big type**: Screen titles and deal prices tighten to -0.3 / -0.4pt; body sits at 0pt

## 4. Component Stylings

### Buttons

**Primary CTA ("CHECKOUT" / "ADD TO ORDER")**
- Background: `#E31837`
- Text: `#FFFFFF`, Archivo 16pt weight 700, 0.3pt tracking (often ALL CAPS for the order CTA)
- Height: 52pt (full-width sheet CTA)
- Corner radius: 8pt
- Pressed: background `#C2122E`, scale 0.98
- Disabled: background `#E2E2E2`, text `#9A9A9A`
- Often carries a trailing price: "ADD TO ORDER · $13.99"

**Secondary / Blue ("TRACK ORDER", "VIEW DETAILS")**
- Background: `#006491`
- Text: `#FFFFFF`, Archivo 15pt weight 700
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Pressed: background `#00547A`

**Outline ("CUSTOMIZE", "EDIT")**
- Background: transparent
- Border: 2pt solid `#1F1F1F`
- Text: `#1F1F1F`, Archivo 14pt weight 700
- Corner radius: 8pt
- Pressed: background `#F4F4F4`

**Text / Link Button ("See all", "Change store")**
- Font: Archivo 14pt weight 700
- Color: `#006491` (blue = info/links)
- No underline, 44pt hit area

**Quantity Control (cart line, topping amount)**
- Squared 8pt-radius outline group: "−" | qty | "+"
- Border 1.5pt `#E2E2E2`, glyphs `#1F1F1F`, qty Archivo 16pt weight 700 tabular
- Pressed glyph: background `#F4F4F4`

### Cards & Containers

**Menu Item Card**
- Layout: food photo top (4:3, 8pt radius) → name + description + price + red "Add"
- Background: `#FFFFFF`, 8pt radius, soft gray shadow
- Name: Archivo 17pt weight 700 `#1F1F1F`
- Description: Archivo 13pt weight 400 `#6E6E6E`, 2-line clamp
- Price: Archivo 16pt weight 700 `#1F1F1F`
- Pressed: scale 0.98

**Deal Card**
- Bold tile, ~150pt tall, 8pt radius, 2pt `#E31837` border
- Big deal price: Archivo 28pt weight 700 `#E31837` tabular
- Deal name + fine print in `#1F1F1F` / `#6E6E6E`
- Red "ADD DEAL" CTA bottom-right
- The domino-square logo motif sits as a small corner mark

**Pizza Tracker Card**
- Full-width hero card, ~180pt tall, 12pt radius
- Top: current-stage headline (Archivo 22pt weight 700 `#1F1F1F`) + ETA line (`#6E6E6E`)
- The five-stage rail (see Distinctive)
- "Track on the map" link in `#006491`

**Build-Your-Pizza Preview**
- A circular pizza graphic (~160pt) at the top that updates live as crust/sauce/cheese/toppings are chosen
- Below: stacked option groups (Size, Crust, Sauce, Cheese, Toppings) as selectable rows/segments

**Cart Line Row**
- Layout: 56pt thumbnail → item name + customizations summary → price + quantity control
- Customizations: "Medium · Hand Tossed · Extra Cheese, Pepperoni" Archivo 13pt weight 400 `#6E6E6E`, 2-line clamp
- Divider `#E2E2E2` between rows

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, hairline top border `#E2E2E2`
- Tabs: Order, Tracker, Deals, Account
- Icon size: 24pt
- Active color: `#E31837` (filled icon + label)
- Inactive: `#6E6E6E` (outlined icon)
- Labels: Archivo 11pt weight 700, always shown

**Top Nav**
- Height: 52pt + safe area
- Carryout/delivery toggle + store top-left (Archivo 15pt weight 700 + chevron)
- Screen title center: Archivo 26pt weight 700 `#1F1F1F`
- Cart icon top-right (24pt) with a red count badge

### Input Fields

**Search Field**
- Background: `#F4F4F4`
- Height: 44pt
- Corner radius: 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#6E6E6E`
- Placeholder: "Search the menu" Archivo 15pt weight 400 `#6E6E6E`
- Focus: 2pt border `#E31837`, background `#FFFFFF`

**Filter / Option Chip**
- Background: `#F4F4F4` default / `#E31837` filled selected
- Text: `#1F1F1F` default / `#FFFFFF` selected, Archivo 13pt weight 700
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 8pt
- Examples: "Pizzas", "Sides", "Drinks", "Desserts"

### Distinctive Components

**Five-Stage Pizza Tracker**
- The signature component. A horizontal rail of five segments: **Prep → Bake → Box → Quality Check → Out for delivery**.
- Each stage is a node (24pt circle) connected by a 4pt rail.
- **Completed stages**: node fills `#006491` with a white checkmark; the rail behind them is `#006491`.
- **Active stage**: node fills `#E31837` (pulsing subtly), label below in Archivo 13pt weight 700 `#E31837`; the rail fills `#E31837` up to this node and the segment ahead animates filling.
- **Upcoming stages**: node hollow with `#E2E2E2` border; label `#9A9A9A`; rail `#E2E2E2`.
- Above the rail: the big current-stage headline ("Your pizza is in the oven!") at 22pt weight 700.
- Below: ETA ("Estimated delivery 7:45 PM") at 13pt `#6E6E6E`.
- A small domino-square mark animates near the active node.

**Tracker Stage Fill**
- When the order advances a stage, the active node's color hands off: the prior node settles to blue (with check), the new node turns red, and the rail segment fills red → over ~600ms ease-out, with a soft haptic.

**Build-Your-Pizza Live Preview**
- The circular pie graphic updates as the user picks: crust ring thickness changes with crust type, a sauce wash tints the base, cheese adds a layer, each topping scatters its garnish across the pie. Selecting an option animates the corresponding layer in over ~250ms.

**Deal Card Price Pop**
- On a deal card, the big red price (28pt weight 700) is the focal element; tapping "ADD DEAL" bumps the cart badge and flashes a red-tint confirmation.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52
- Standard margin: 16pt horizontal
- Card padding: 16pt internal
- Tracker rail: 24pt nodes, 4pt rail, equal segment widths

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Menu grid: single-column cards or 2-column tiles for category browse (12pt gap)
- Deal grid: 2-column deal tiles (12pt gap)
- Tracker: full-width hero card spanning the content width

### Whitespace Philosophy
- **The tracker gets the stage**: On the active-order screen the tracker hero owns the top third with generous padding — the wait is the experience
- **Cards float on white**: White cards with soft gray shadows separate from the canvas; `#F4F4F4` chunks major sections
- **Dense but bright**: The menu packs items efficiently, but high contrast and white space keep it scannable for a fast reorder

### Border Radius Scale
- Sharp (0pt): Full-bleed photography edges, very rare
- Squared (8pt): Buttons, cards, chips, deal tiles, search field — the practical default
- Comfortable (12pt): Tracker hero card, bottom sheets
- Large (16pt): Modal sheets
- Pill (500pt): Count badges, small status pills
- Circle (50%): Tracker nodes, pizza preview, icon buttons, the domino-square mark base

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, divider only | List rows, grouped content |
| Card (Level 1) | `rgba(31,31,31,0.08) 0 2px 6px` | Menu cards, deal tiles lifting off white |
| Raised (Level 2) | `rgba(31,31,31,0.12) 0 4px 14px` | The tracker hero card, persistent order footer |
| Sheet (Level 3) | `rgba(31,31,31,0.16) 0 -8px 30px` | Bottom sheets (customize, item detail) |
| Overlay Dim | `rgba(31,31,31,0.45)` | Modal scrim behind sheets |

**Shadow Philosophy**: Shadows are neutral and soft (mixed from the `#1F1F1F` ink) so cards read as a clean lift on bright white — no heavy drops, the brightness carries the structure. The most prominent elevation is the pizza-tracker hero card: a `4px 14px` raise that makes the moving tracker the focal object on the active-order screen. Menu and deal cards lift just enough to separate from the canvas.

### Motion
- **Tracker stage advance**: prior node → blue + check; new node → red; rail segment fills red over ~600ms ease-out; soft haptic
- **Active node pulse**: the red active node breathes scale 1.0 → 1.06 → 1.0 on a ~1.6s loop
- **Build-pizza layer add**: selected crust/sauce/cheese/topping layer fades+scales in over ~250ms
- **Add to Order**: button press scale 0.98 → 1.0 spring; cart badge bumps 1.0 → 1.2 → 1.0; success haptic
- **Card tap**: scale 0.98 → 1.0 spring on release
- **Sheet present**: bottom sheet slides up 0.3s ease-out with scrim fade

## 7. Do's and Don'ts

### Do
- Use clean white (`#FFFFFF`) as the canvas — the catalog and tracker need bright contrast
- Use Domino's Red (`#E31837`) for action: order, build, deals, active tracker stage
- Use Domino's Blue (`#006491`) for info: links, completed checkmarks, supporting badges
- Render the tracker as a five-node rail: Prep → Bake → Box → Quality Check → Out for delivery
- Color completed stages blue (with checkmark), the active stage red, upcoming gray
- Make the tracker hero the focal object on the active-order screen
- Use bold weight 700 for titles, item names, prices, and buttons
- Use tabular figures for prices, ETAs, deal callouts, and totals
- Update the build-your-pizza preview live as options change

### Don't
- Don't use a dark or tinted canvas — Domino's is bright and high-contrast
- Don't mix red and blue roles — red is action, blue is info/done
- Don't make the active tracker stage blue or completed stages red — the color logic is the meaning
- Don't bury the tracker below the fold on an active order — it's the experience
- Don't use light or thin type weights — Domino's type is sturdy 400/700
- Don't use proportional figures for prices or ETAs — they will misalign
- Don't use fully-rounded pill buttons — Domino's buttons are squared 8pt
- Don't show a static pizza while the user customizes — the preview must update live
- Don't add accent colors beyond red and blue — the domino two-color identity is the brand

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Tracker labels abbreviate ("Qual." for Quality Check); 2-up deal grid tighter |
| iPhone 13/14/15 | 390pt | Standard five-node tracker with full labels |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the carryout/store selector |
| iPhone 15/16 Pro Max | 430pt | Larger pizza preview + food photos, roomier tracker |
| iPad | 768pt+ | Two-pane: menu list left, live order/tracker right; 3-column deal grid |

### Dynamic Type
- Item names, body, descriptions: full scale
- Tracker stage headline: scales to a max, never wraps to more than 2 lines
- Prices, ETAs, deal callouts: scale but stay tabular and aligned
- Tab labels and tracker node labels: fixed (layout-sensitive; abbreviate on small widths)

### Orientation
- Order / Tracker / Deals / Account: **portrait-locked** on iPhone
- iPad: supports landscape with the two-pane menu + tracker layout

### Touch Targets
- Tracker is display-only but "Track on the map" link is a 44pt hit
- Build option rows: full-row tappable, ~56pt
- Quantity − / +: 44pt hit each
- Primary CTA / order footer: 52pt tall, full width
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: carryout/store selector respects the safe area and Dynamic Island
- Bottom: persistent order footer floats above the tab bar; both clear the home indicator
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F4F4F4`
- Divider: `#E2E2E2`
- Text primary: `#1F1F1F`
- Text secondary: `#6E6E6E`
- Domino's Red (action): `#E31837`
- Red pressed: `#C2122E`
- Domino's Blue (info): `#006491`
- Blue pressed: `#00547A`
- Tracker track: `#E2E2E2`
- Success green: `#1E8E3E`

### Example Component Prompts
- "Create the SwiftUI Domino's five-stage pizza tracker: a horizontal rail with five 24pt circular nodes labeled Prep, Bake, Box, Quality Check, Out for delivery, connected by a 4pt rail. Completed nodes fill #006491 with a white checkmark and a #006491 rail behind them; the active node ('Bake') fills #E31837 with a #E31837 label below and the rail filling red up to it; upcoming nodes are hollow with a #E2E2E2 border and #9A9A9A labels. Above: 'Your pizza is in the oven!' in Archivo 22pt weight 700 #1F1F1F. Below: 'Estimated delivery 7:45 PM' in Archivo 13pt #6E6E6E."
- "Build the primary order CTA: full-width 52pt button, background #E31837, label 'ADD TO ORDER · $13.99' in Archivo 16pt weight 700 ALL CAPS with 0.3pt tracking in #FFFFFF, 8pt corner radius. Pressed: background #C2122E, scale 0.98."
- "Design a Domino's deal card: ~150pt tall tile, white background, 8pt corner radius, 2pt #E31837 border. Big price '$7.99' in Archivo 28pt weight 700 #E31837 tabular, deal name 'Mix & Match' Archivo 17pt weight 700 #1F1F1F, fine print Archivo 13pt #6E6E6E, and a red 'ADD DEAL' CTA bottom-right. A small red/blue domino-square mark in the top corner."
- "Create the build-your-pizza live preview: a 160pt circular pizza graphic at the top that updates as options change — crust ring thickness from crust type, a sauce-color wash on the base, a cheese layer, and scattered topping garnishes. Below it, stacked option groups (Size, Crust, Sauce, Cheese, Toppings) as selectable segmented rows. Each option selection animates its layer in over 250ms."
- "Build the Domino's bottom tab bar: white background, hairline #E2E2E2 top border, 4 tabs — Order, Tracker, Deals, Account. Active tab #E31837 filled icon + Archivo 11pt weight 700 label; inactive #6E6E6E outlined. Cart count badge in red on the Order tab."

### Iteration Guide
1. Canvas is clean white `#FFFFFF` — the catalog and tracker need bright high contrast
2. Red `#E31837` = action (order, build, deals, active stage); Blue `#006491` = info (links, completed checks)
3. The five-stage tracker (Prep → Bake → Box → Quality Check → Out for delivery) is the signature — completed blue, active red, upcoming gray
4. Make the tracker hero the focal object on the active-order screen
5. Bold weight 700 carries the hierarchy — titles, item names, prices, buttons
6. Tabular figures for prices, ETAs, deal callouts, totals
7. Buttons and cards are squared 8pt — practical, not pill
8. The build-your-pizza preview updates live as options change; shadows are soft neutral on white
