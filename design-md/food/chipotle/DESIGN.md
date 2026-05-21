# Design System Inspiration of Chipotle (iOS)

## 1. Visual Theme & Atmosphere

Chipotle's iOS app is built like a foil-wrapped burrito: warm, hand-made, and stripped to the essentials. The canvas is a cream paper tone (`#FFF5E1`) — not stark white — evoking the kraft-paper bags and butcher-paper liners of the restaurant. Against that warm field, food photography of bowls and burritos sits on clean white cards (`#FFFFFF`), and the entire experience funnels toward one ritual: building your order, ingredient by ingredient, from a vertical list of choices. This is fast-casual design with a craft sensibility — utilitarian, but never cold.

The accent is Chipotle Red (`#A81612`) — a deep, brick-toned red borrowed from the brand's logo and signage. It is reserved exclusively for action: the "Add to Bag" button, the order CTA, the rewards points, selected ingredient checkmarks, and the active tab. A deep espresso brown (`#451400`) carries all primary text, reinforcing the earthy, food-forward palette — there is no black anywhere in the app. The combination of brick red on cream with brown text feels like a menu board chalked on kraft paper.

Typography is the loudest design choice. Headers are set in a bold condensed grotesque (Archivo / Archivo Narrow, with Avenir for body) in ALL CAPS — "BURRITO BOWL", "BUILD YOUR OWN", "ENTRÉES" — echoing the stenciled, industrial type painted on Chipotle's walls. Weights run 400 / 700 / 800, with the condensed bold caps doing the heavy lifting at the top of every screen. Body copy and ingredient names drop to a calmer sentence-case Avenir at 15pt. The contrast between shouting condensed headers and quiet body text is the signature typographic rhythm.

**Key Characteristics:**
- Cream paper canvas (`#FFF5E1`) — kraft-bag warmth, never stark white
- Chipotle Red (`#A81612`) as the only accent — Add to Bag, order CTA, rewards, selected state
- Espresso brown (`#451400`) for all text — no black anywhere
- ALL-CAPS bold condensed headers (Archivo) — stenciled, menu-board energy
- Build-your-burrito ingredient stepper — a scrollable list of single/multi-select rows
- Foil-texture hero — a brushed metallic gradient behind featured items
- Rewards points ring — circular progress toward the next free entrée
- Step progress indicator — a slim segmented bar tracking the build flow
- Sentence-case Avenir body — the quiet counterpoint to the shouting headers

## 2. Color Palette & Roles

### Primary
- **Chipotle Red** (`#A81612`): Primary CTA ("Add to Bag", "Checkout"), order button, rewards points, selected ingredient checkmark, active tab, progress fill.
- **Red Pressed** (`#8C1210`): Active/pressed state for red buttons.
- **Red Tint** (`#F4E2E1`): Light wash behind "added" confirmation chips and selected ingredient rows.

### Canvas & Text
- **Cream Canvas** (`#FFF5E1`): Primary canvas — the warm kraft-paper field.
- **Surface White** (`#FFFFFF`): Cards, ingredient list container, sheets, food-photo backgrounds.
- **Cream Deep** (`#F4E8D0`): Pressed state on cream, alternating list zones.
- **Divider** (`#E8DCC4`): Hairline dividers between ingredient rows and card sections.
- **Text Primary** (`#451400`): Headers, ingredient names, prices, primary UI text — espresso brown.
- **Text Secondary** (`#8A6B4F`): Calorie counts, descriptions, metadata, helper text — milk-chocolate brown.
- **Text Tertiary** (`#B49A82`): Disabled labels, very low-emphasis captions.

### Brand Support
- **Adobe Tan** (`#AC8C5B`): Warm tan used in the foil hero gradient and decorative dividers.
- **Foil Silver** (`#D9D4C9` → `#A8A296`): Brushed-metal gradient for the featured-item hero (evokes burrito foil).

### Semantic
- **Success Green** (`#3C7A3C`): Order-confirmed state, "ready for pickup" badge.
- **Calorie Gray** (`#8A6B4F`): Calorie count label (shares secondary brown).
- **Error Red** (`#A81612`): Validation errors, item-unavailable (shares brand red).
- **Spice Orange** (`#E07B26`): "Spicy" / hot indicator on salsas.

### Dark Mode (Limited Use)
Chipotle's iOS app is light-only by design — the cream-paper warmth is core to the brand. A dark variant exists for system dark mode but is not the designed experience.
- **Dark Canvas** (`#1C1410`)
- **Dark Surface** (`#2A2018`)
- **Dark Text** (`#FFF5E1`)
- Chipotle Red stays identical — the brick tone reads on dark.

## 3. Typography Rules

### Font Family
- **Display / Headers**: `Archivo` (and `Archivo Narrow` for the most condensed caps) — bold, used in ALL CAPS
- **Body / UI**: `Avenir` (Avenir Next) — calm sentence-case for ingredient names and copy
- **Numerals**: Avenir tabular figures for prices, calories, points (`font-variant-numeric: tabular-nums`)
- **Fallback Stack**: `'Archivo', 'Avenir Next', -apple-system, BlinkMacSystemFont, 'SF Pro Text', Helvetica, Arial, sans-serif`

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Hero Header (CAPS) | Archivo Narrow | 32pt | 800 | 1.05 | 0.4pt | "BUILD YOUR OWN" foil hero |
| Screen Title (CAPS) | Archivo | 26pt | 800 | 1.1 | 0.3pt | "ENTRÉES", "REWARDS" nav title |
| Section Header (CAPS) | Archivo | 18pt | 700 | 1.15 | 0.6pt | "RICE", "PROTEIN", "TOPPINGS" |
| Item Name | Avenir Next | 17pt | 700 | 1.25 | 0pt | Menu item / ingredient title |
| Body | Avenir Next | 15pt | 400 | 1.45 | 0pt | Descriptions, instructions |
| Price | Avenir Next | 16pt | 700 | 1.2 | 0pt | Item price, tabular figures |
| Button (CAPS) | Archivo | 16pt | 800 | 1.0 | 0.8pt | "ADD TO BAG", "CHECKOUT" |
| Points Value | Archivo | 34pt | 800 | 1.0 | 0pt | Rewards-ring center number |
| Meta / Calories | Avenir Next | 13pt | 400 | 1.3 | 0pt | "300 Cal", "Add for $2.00" |
| Badge (CAPS) | Archivo | 11pt | 700 | 1.2 | 0.6pt | "NEW", "SPICY", "LIMITED" |
| Tab Label | Avenir Next | 11pt | 700 | 1.0 | 0.2pt | Bottom tab bar labels |
| Caption | Avenir Next | 11pt | 400 | 1.3 | 0pt | Fine print, allergen notes |

### Principles
- **Headers SHOUT, body whispers**: Display type is ALL CAPS condensed bold (Archivo); body and ingredient names are calm sentence-case Avenir — the contrast is the brand
- **Positive letter-spacing on caps**: ALL-CAPS headers and buttons carry 0.3-0.8pt tracking so the condensed forms breathe; sentence-case body sits at 0pt
- **Weights at 400 / 700 / 800**: Regular Avenir for body, bold Avenir for item names and prices, extra-bold Archivo for headers and buttons
- **Tabular figures for numbers**: Prices, calorie counts, and rewards points use tabular Avenir so they align
- **Brown, never black**: All text is espresso `#451400` or milk-chocolate `#8A6B4F` — pure black is never used

## 4. Component Stylings

### Buttons

**Primary CTA ("ADD TO BAG" / "CHECKOUT")**
- Background: `#A81612`
- Text: `#FFFFFF`, Archivo 16pt weight 800, ALL CAPS, 0.8pt tracking
- Height: 54pt (full-width sheet CTA)
- Corner radius: 8pt (slightly squared — kraft, not pill)
- Pressed: background `#8C1210`, scale 0.98
- Disabled: background `#E8DCC4`, text `#B49A82`
- Often shows a trailing price: "ADD TO BAG · $9.65"

**Secondary / Outline ("CUSTOMIZE", "ADD A SIDE")**
- Background: transparent
- Border: 2pt solid `#451400`
- Text: `#451400`, Archivo 14pt weight 700, ALL CAPS, 0.6pt tracking
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 8pt
- Pressed: background `#F4E8D0`

**Text Button ("Skip", "Edit")**
- Font: Avenir Next 14pt weight 700
- Color: `#A81612`
- No underline, 44pt hit area

**Quantity Control (on cart line)**
- Squared 8pt-radius outline group: "−" | qty | "+"
- Border 1.5pt `#E8DCC4`, glyphs `#451400`, qty Avenir 16pt weight 700 tabular
- Pressed glyph: background `#F4E8D0`

### Cards & Containers

**Menu Item Card**
- Layout: food photo top (4:3, 8pt radius) or leading 72pt square thumbnail → name + calorie + price
- Background: `#FFFFFF`, 8pt radius, soft warm shadow
- Name: Avenir 17pt weight 700 `#451400`
- Calorie line: "630 Cal" Avenir 13pt weight 400 `#8A6B4F`
- Price: Avenir 16pt weight 700 `#451400`
- Pressed: scale 0.98

**Foil Hero (Featured Item)**
- Full-width banner, ~200pt tall, 12pt radius
- Background: brushed-metal gradient (`#D9D4C9` → `#A8A296`, diagonal) with a subtle vertical streak texture (evokes burrito foil)
- Overlaid ALL-CAPS Archivo Narrow header (32pt weight 800 `#451400`) + subtitle + red CTA
- Used for "BUILD YOUR OWN" and limited-time items

**Rewards Points Ring**
- Circular progress ring, ~120pt diameter
- Track: `#E8DCC4`; progress arc: `#A81612` (conic, from top)
- Center: points value (Archivo 34pt weight 800 `#A81612`, tabular) + "POINTS" label (Archivo 11pt weight 700 caps `#8A6B4F`)
- Below: "1,375 points until your next free entrée" (Avenir 13pt `#8A6B4F`)

**Cart Line Row**
- Layout: 56pt photo thumbnail → item name + customizations summary → price + quantity control
- Customizations: "White Rice, Black Beans, Chicken, Mild" Avenir 13pt weight 400 `#8A6B4F`, 2-line clamp
- Divider `#E8DCC4` between rows

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF`, hairline top border `#E8DCC4`
- Tabs: Order, Scan, Delivery, More
- Icon size: 24pt
- Active color: `#A81612` (filled icon + label)
- Inactive: `#8A6B4F` (outlined icon)
- Labels: Avenir 11pt weight 700, ALL CAPS-friendly, always shown
- The Scan tab is visually centered/emphasized (in-store pay)

**Top Nav**
- Height: 52pt + safe area
- Pickup/delivery toggle + location top-left (Avenir 15pt weight 700 + chevron)
- Screen title center: Archivo 26pt weight 800 ALL CAPS `#451400`
- Bag icon top-right (24pt) with a red count badge

**Step Progress Bar (Build Flow)**
- A slim segmented bar pinned below the nav during the build-your-burrito flow
- Segments: equal-width pills, 4pt tall, 2pt gap
- Completed/active: `#A81612`; upcoming: `#E8DCC4`
- Slides the active segment fill forward as the user advances steps

### Input Fields

**Search Field**
- Background: `#FFFFFF`, border 1.5pt `#E8DCC4`
- Height: 44pt
- Corner radius: 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#8A6B4F`
- Placeholder: "Search the menu" Avenir 15pt weight 400 `#8A6B4F`
- Focus: 2pt border `#A81612`

**Filter / Diet Chip**
- Background: `#FFFFFF` border 1.5pt `#E8DCC4` default / `#A81612` filled selected
- Text: `#451400` default / `#FFFFFF` selected, Avenir 13pt weight 700
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 8pt (squared)
- Examples: "Vegetarian", "Keto", "Whole30", "High Protein"

### Distinctive Components

**Build-Your-Burrito Ingredient Stepper**
- The signature flow. A vertically scrolling list grouped by step section (RICE → BEANS → PROTEIN → TOPPINGS → SALSAS).
- Each section has an ALL-CAPS Archivo header (18pt weight 700, 0.6pt tracking `#451400`) and a rule below.
- **Single-select rows** (Rice, Beans): radio-style — selected shows a filled `#A81612` circular check at trailing; row gets `#F4E2E1` wash + 1.5pt `#A81612` left rule.
- **Multi-select rows** (Toppings, Salsas): checkbox-style — selected shows a filled `#A81612` rounded-square check; can stack ("Extra" toggle adds a "+$2.00" tag in `#8A6B4F`).
- Each row: ingredient name (Avenir 17pt weight 700 `#451400`), optional calorie/price meta (13pt `#8A6B4F`), trailing selector.
- Tapping a row animates the check scaling 0 → 1 with a spring + light haptic.
- A persistent footer shows the running total and a red "ADD TO BAG · $X" CTA.

**Step Progress Slide**
- As the user finishes a section and scrolls/advances, the segmented progress bar slides its red fill to the next segment over ~250ms ease-out.

**Foil Texture**
- The featured hero uses a brushed-metal gradient with thin diagonal streaks at low opacity to read as crinkled foil — a tactile, on-brand background reserved for hero moments only.

**Rewards Ring Fill**
- On the Rewards screen, the red progress arc animates from 0 to its value over ~700ms ease-out on appear, with the center points number counting up.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 54
- Standard margin: 16pt horizontal
- Ingredient row height: 56-64pt
- Card padding: 16pt internal

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Menu grid: single-column cards, or 2-column tiles for category browse (12pt gap)
- Ingredient list: full-width single-column rows grouped by section

### Whitespace Philosophy
- **Generous around the build list**: Ingredient rows get 56-64pt height with comfortable padding — the build flow should feel unhurried and tactile
- **Cards float on cream**: White cards sit on the `#FFF5E1` field with a soft warm shadow; the cream is the breathing room
- **Headers get air**: ALL-CAPS section headers have 24pt above and a divider below — the menu-board rhythm depends on space around the shouting type

### Border Radius Scale
- Sharp (0pt): Full-bleed photography, very rare
- Squared (8pt): Buttons, cards, chips, search field, quantity control — the kraft, slightly-squared default
- Comfortable (12pt): Foil hero, bottom sheets
- Large (16pt): Modal sheets
- Pill (500pt): Progress segments, count badges
- Circle (50%): Rewards ring, ingredient checkmarks, icon buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, divider only | Ingredient rows, grouped list content |
| Card (Level 1) | `rgba(69,20,0,0.08) 0 2px 6px` | Menu cards lifting off the cream canvas |
| Raised (Level 2) | `rgba(69,20,0,0.12) 0 4px 14px` | The persistent "ADD TO BAG" footer, sticky step bar |
| Sheet (Level 3) | `rgba(69,20,0,0.18) 0 -8px 30px` | Bottom sheets (customize, item detail) |
| Overlay Dim | `rgba(69,20,0,0.40)` | Modal scrim behind sheets |

**Shadow Philosophy**: Shadows are warm — tinted from the espresso `#451400` ink rather than pure black — so a white card on cream reads as a gently raised paper layer, not a hard cutout. The most prominent elevation is the persistent "ADD TO BAG" footer during the build flow: a `4px 14px` warm raise that anchors the running total above the scrolling ingredient list. Menu cards lift just enough to separate from the cream.

### Motion
- **Ingredient select**: trailing check scales 0 → 1 with a spring (response 0.2, damping 0.7) + light haptic; row wash crossfades to `#F4E2E1`
- **Step progress slide**: active red segment fill slides forward ~250ms ease-out on section advance
- **Rewards ring fill**: red arc animates 0 → value over ~700ms ease-out; center number counts up
- **Add to Bag**: button press scale 0.98 → 1.0 spring; bag-count badge bumps 1.0 → 1.2 → 1.0; success haptic
- **Card tap**: scale 0.98 → 1.0 spring on release
- **Sheet present**: bottom sheet slides up 0.3s ease-out with warm scrim fade

## 7. Do's and Don'ts

### Do
- Use the cream canvas (`#FFF5E1`) — the kraft-paper warmth is core to the brand
- Reserve Chipotle Red (`#A81612`) for action: Add to Bag, order CTA, rewards, selected ingredients, active tab
- Set headers in ALL-CAPS bold condensed Archivo with positive tracking — the menu-board voice
- Keep body and ingredient names calm sentence-case Avenir — the quiet counterpoint
- Use espresso brown (`#451400`) for text — never pure black
- Render the build flow as a sectioned, scrollable ingredient stepper with single/multi-select rows
- Keep the running total + red "ADD TO BAG" footer persistent during the build
- Use slightly-squared 8pt radii on buttons and cards — kraft, not pill
- Use tabular figures for prices, calories, and rewards points

### Don't
- Don't use stark white as the canvas — the cream warmth is the brand
- Don't use red for decoration or non-action text — it's the action color
- Don't set body copy in ALL CAPS — only headers and buttons shout
- Don't use pure black for any text — brown carries the food-forward palette
- Don't use fully-rounded pill buttons — Chipotle's buttons are squared 8pt
- Don't break the build flow into disconnected screens with no progress indicator
- Don't hide the running total during customization — the price must always be visible
- Don't overuse the foil texture — it is a hero-only accent
- Don't mix in cool grays — every neutral is warm (cream, tan, brown)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Ingredient rows 56pt, single-column menu, tighter hero |
| iPhone 13/14/15 | 390pt | Standard 60pt ingredient rows, 200pt foil hero |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the pickup/location selector |
| iPhone 15/16 Pro Max | 430pt | 64pt ingredient rows, larger hero + food photos |
| iPad | 768pt+ | Two-pane build: menu list left, live order summary right; 3-column category browse |

### Dynamic Type
- Item names, body, descriptions: full scale
- ALL-CAPS headers and buttons: scale to a max, never wrap (condensed handles growth)
- Prices, calories, points: scale but stay tabular and aligned
- Tab labels and step segments: fixed (layout-sensitive)

### Orientation
- Order / Build / Scan / Delivery: **portrait-locked** on iPhone
- iPad: supports landscape with the two-pane build layout

### Touch Targets
- Ingredient rows: full-row tappable, 56-64pt
- Ingredient checkmark: 28pt glyph, 44pt effective hit (full row is the target)
- Quantity − / +: 44pt hit each
- "ADD TO BAG" footer button: 54pt tall, full width
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: pickup/location selector + step progress bar respect the safe area and Dynamic Island
- Bottom: persistent "ADD TO BAG" footer floats above the tab bar; both clear the home indicator
- Sides: 16pt content insets throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (cream): `#FFF5E1`
- Surface (white): `#FFFFFF`
- Cream deep (pressed): `#F4E8D0`
- Divider: `#E8DCC4`
- Text primary (espresso): `#451400`
- Text secondary (chocolate): `#8A6B4F`
- Chipotle Red (action): `#A81612`
- Red pressed: `#8C1210`
- Red tint: `#F4E2E1`
- Tan (foil/decor): `#AC8C5B`
- Success green: `#3C7A3C`

### Example Component Prompts
- "Create a SwiftUI Chipotle section header: the text 'PROTEIN' in Archivo 18pt weight 700, ALL CAPS, 0.6pt letter spacing, color #451400, with a 1pt #E8DCC4 divider rule directly below and 24pt of space above."
- "Build the build-your-burrito ingredient row (single-select): full-width, 60pt tall, on a #FFFFFF card. Leading: ingredient name 'White Rice' Avenir 17pt weight 700 #451400 with a '210 Cal' Avenir 13pt weight 400 #8A6B4F line below. Trailing: a 28pt circular selector — when selected it fills #A81612 with a white check and the row gets an #F4E2E1 wash plus a 1.5pt #A81612 left rule. Tapping springs the check 0 → 1 with a light haptic."
- "Design the primary CTA: full-width 54pt button, background #A81612, label 'ADD TO BAG · $9.65' in Archivo 16pt weight 800 ALL CAPS with 0.8pt tracking in #FFFFFF, 8pt corner radius. Pressed: background #8C1210, scale 0.98."
- "Create the Chipotle rewards ring: a 120pt circular progress ring, track #E8DCC4, red #A81612 conic progress arc from the top. Center: '1,250' in Archivo 34pt weight 800 #A81612 tabular, with 'POINTS' in Archivo 11pt weight 700 ALL CAPS #8A6B4F below. The arc animates 0 → value over 700ms ease-out on appear while the number counts up."
- "Build the foil hero banner: full-width, 200pt tall, 12pt corner radius, background a diagonal brushed-metal gradient from #D9D4C9 to #A8A296 with faint diagonal streaks (crinkled-foil feel). Overlay 'BUILD YOUR OWN' in Archivo Narrow 32pt weight 800 ALL CAPS #451400, a one-line subtitle in Avenir 15pt #8A6B4F, and a red 'START' CTA."

### Iteration Guide
1. Canvas is cream `#FFF5E1` — the kraft-paper warmth is the brand, never stark white
2. Chipotle Red `#A81612` is the only action color: Add to Bag, order, rewards, selected ingredients, active tab
3. Headers SHOUT in ALL-CAPS bold condensed Archivo with positive tracking; body whispers in sentence-case Avenir
4. All text is espresso brown `#451400` or chocolate `#8A6B4F` — never pure black
5. The build flow is a sectioned scrollable ingredient stepper — single-select (radio) and multi-select (checkbox) rows
6. Keep the running total + red "ADD TO BAG" footer persistent through the build
7. Buttons and cards are slightly squared (8pt radius) — kraft, not pill
8. Prices, calories, and rewards points use tabular figures; shadows are warm (espresso-tinted), not black
