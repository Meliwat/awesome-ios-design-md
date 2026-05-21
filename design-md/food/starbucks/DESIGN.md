# Design System Inspiration of Starbucks (iOS)

## 1. Visual Theme & Atmosphere

Starbucks' iOS app is a thoughtful, warm retail experience wrapped around a single, rewards-heavy hero: your Stars dashboard. Open the app and the first thing you see is a big Gold-gradient progress ring at the top, counting your Stars toward the next reward — a tiny daily dopamine loop that's been tuned over years to reinforce the visit. The color palette is all house: the famous Siren Green (`#00704A`) for primary CTAs, a darker "House Green" (`#1E3932`) that shows up as the warm dark canvas (and crucially is NOT black — it's a very dark forest green tied to Starbucks' store interiors), a soft mint (`#D4E9E2`) that tints reward moments, and cream-gold (`#CBA258`) for the Gold tier status and Stars themselves. The UI evokes the same hospitality as a store: warm, quiet, a little bit premium, and organized around the ritual of ordering the same drink.

The layout rhythm is category-first. Once you pick "Order" from the tab bar, you're met with a grid of category cards — Featured, Hot Coffees, Cold Coffees, Refreshers, Lemonades, Frappuccino, Tea, Hot Chocolate, Bakery, Oatmeal, Sandwiches, Salads, and so on — each rendered as a soft-corner card with a photo of the drink and a simple label. Tapping a card drills into a list of drinks, and then into the drink builder: size selector (Short / Tall / Grande / Venti / Trenta) as a segmented horizontal row, milk options as a dropdown, syrup pumps with +/− steppers, whipped cream toggle, espresso shots. The builder is the heart of the app's commerce flow, and Starbucks has made it feel like a barista's checklist — precise, low-friction, and customizable down to the pump.

Typography is SoDo Sans — Starbucks' proprietary typeface since 2020, named after SoDo (Seattle's South of Downtown neighborhood where the company's HQ sits). Designed by Dalton Maag, SoDo Sans is a humanist sans with open apertures, subtle warmth, and a weight ladder concentrated at 400 / 600 / 700 / 800 (Regular / Semibold / Bold / ExtraBold). The hierarchy is calm: section titles at 22pt weight 700, body at 15pt weight 400, rewards-specific numbers in SoDo Sans 800 for emphasis. The Stars count on the dashboard ring is a centerpiece — 36-48pt SoDo Sans 800 gold, with smooth tabular digits. The app avoids large display moments except for onboarding hero screens and the once-a-year campaign takeovers (Pumpkin Spice Latte season, Holiday cups).

**Key Characteristics:**
- Siren Green (`#00704A`) as the primary CTA and accent — the brand's defining color
- House Green (`#1E3932`) as the warm dark canvas — NOT black, it's deep forest green
- Rewards Stars dashboard — circular progress ring in gold gradient, Stars count center, "X Stars until reward" subtitle
- Gold tier accent (`#CBA258`) for rewards, status, and the star glyph itself
- Order-ahead category grid — soft-rounded cards with photography per category
- Drink builder with size selector pills (Short / Tall / Grande / Venti / Trenta) and +/− pump steppers
- SoDo Sans typography — humanist sans, weights 400/600/700/800
- QR code pay flow — "Scan to pay" hero with Stars balance summary
- Store locator with green pin and map card
- Tab bar: Home | Scan | Order | Gift | Offers — Scan at the center is the in-store anchor
- Warm White (`#F9F9F9`) canvas — softer than pure white for a welcoming feel

## 2. Color Palette & Roles

### Primary (Brand)
- **Starbucks Green / Siren Green** (`#00704A`): Primary brand color — CTAs, logo, Order buttons, tab-active state.
- **Green Pressed** (`#00563A`): Pressed state on green CTAs.
- **House Green** (`#1E3932`): Secondary dark brand color — dark-mode canvas, deep accent, section backgrounds. Named for the rich interior paint of Starbucks' stores.
- **Accent Green / Mint** (`#D4E9E2`): Soft mint — reward-moment tint, promo background, active selection fill.

### Gold Tier (Rewards)
- **Gold Star** (`#CBA258`): Star glyph color, Gold tier member status, Stars counter number on dashboard.
- **Gold Alt** (`#C1A265`): Variant gold used in gradients and star-ring progress.
- **Gold Gradient** (progress ring): `linear-gradient(90deg, #CBA258 0%, #E4C896 50%, #CBA258 100%)` — subtle sheen.

### Canvas & Text (Light)
- **Warm White** (`#F9F9F9`): Primary canvas — softer than pure white for a welcoming feel.
- **Canvas White** (`#FFFFFF`): Card surfaces, cleaner inner containers.
- **Surface Muted** (`#F1F1F1`): Quiet sectional backgrounds, chip fills.
- **Divider** (`#E8E8E8`): Hairline dividers.
- **Text Primary** (`#000000`): Primary body text — Starbucks uses true black for body on warm white.
- **Text Secondary** (`#595959`): Metadata, timestamps, helper text.
- **Text Tertiary** (`#8E8E8E`): Disabled state, placeholder.

### Dark Mode
- **Dark Canvas** (`#1E3932`): House Green as the dark canvas — very dark forest green, NOT black.
- **Dark Surface 1** (`#2D4A43`): Elevated surfaces on dark.
- **Dark Surface 2** (`#3B5852`): Higher elevation, modals.
- **Dark Divider** (`#3A4F48`).
- **Dark Text Primary** (`#FFFFFF`).
- **Dark Text Secondary** (`#C9CDCB`).

### Semantic
- **Success Green** (`#00754A`): Order-placed confirmations, "Ready for pickup" status.
- **Alert Red** (`#DD3333`): Errors, "Out of stock" badges, destructive.
- **Info Blue** (`#006FCF`): In-body links, informational chips.
- **Warning Amber** (`#F5A623`): Delayed store, "Busy" chip, pending payment.

### Category / Photography Overlay
- **Photo Overlay Light** (`rgba(0,0,0,0.1)`): Subtle darken on category cards for label legibility.
- **Photo Overlay Dark** (`rgba(0,0,0,0.35)`): Stronger overlay on photo tiles with white labels.

## 3. Typography Rules

### Font Family
- **Primary**: `SoDo Sans` (proprietary since 2020, designed by Dalton Maag — named after Seattle's SoDo neighborhood)
- **Display variant**: `SoDo Sans Display` — slightly tighter and more distinctive for hero moments
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Lato', 'Helvetica Neue', Arial, sans-serif`
- **CJK / Non-Latin**: Global SoDo cuts support major scripts; Noto Sans is the fallback
- **Numerals**: SoDo Sans tabular figures for Stars counts, prices, order counts

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Rewards Stars Hero | SoDo Sans | 48pt | 800 | 1.0 | -0.5pt | Stars count center of dashboard ring |
| Screen Title | SoDo Sans | 28pt | 700 | 1.15 | -0.3pt | "Order", "Rewards" hero |
| Sheet Title | SoDo Sans | 22pt | 700 | 1.2 | -0.2pt | Category title, modal headlines |
| Section Header | SoDo Sans | 18pt | 700 | 1.25 | -0.1pt | "Featured", "Hot Coffees" |
| Drink Title (Card) | SoDo Sans | 16pt | 600 | 1.3 | 0pt | Category card labels |
| Drink Title (Detail) | SoDo Sans | 22pt | 700 | 1.2 | -0.2pt | PDP drink name |
| Body | SoDo Sans | 15pt | 400 | 1.45 | 0pt | Descriptions, "About this drink" copy |
| Meta | SoDo Sans | 13pt | 400 | 1.3 | 0pt | "Grande · 150 cal · $5.45" |
| Price | SoDo Sans | 17pt | 700 | 1.1 | -0.1pt | Cart line item price |
| Stars Count (Inline) | SoDo Sans | 22pt | 800 | 1.0 | 0pt | Small Stars displays elsewhere |
| Button (Primary) | SoDo Sans | 16pt | 700 | 1.0 | 0.2pt | "Add to Order", "Pay" |
| Button (Secondary) | SoDo Sans | 15pt | 600 | 1.0 | 0pt | Outline buttons, links |
| Size Selector | SoDo Sans | 13pt | 600 | 1.0 | 0.2pt | "Short / Tall / Grande / Venti / Trenta" |
| Tab Label | SoDo Sans | 11pt | 600 | 1.0 | 0.1pt | Tab bar labels |
| Chip / Badge | SoDo Sans | 12pt | 700 | 1.0 | 0.3pt | "New", "Starbucks Rewards" uppercase |
| Caption | SoDo Sans | 12pt | 400 | 1.3 | 0pt | Fine print, T&Cs |

### Principles
- **Weights concentrated at 400 / 600 / 700 / 800**: Regular / Semibold / Bold / ExtraBold. 800 reserved for Stars counts and hero display moments.
- **Tabular numerals on Stars and prices**: columns in the rewards history must align
- **Caps on chips and badges**: "Rewards", "New", "Gold" are SoDo Sans 12pt w700 with 0.3pt tracking
- **Size selector uses 0.2pt tracking**: the "Short / Tall / Grande / Venti" row reads like engraved type on a coffee menu
- **No italics**: emphasis through weight
- **Warmth via line-height**: body copy at 1.45 line-height — generous, reading-friendly

## 4. Component Stylings

### Buttons

**Primary CTA ("Order", "Add to Order", "Pay")**
- Background: `#00704A` Siren Green
- Text: `#FFFFFF`, SoDo Sans 16pt w700 with 0.2pt tracking
- Height: 52pt
- Corner radius: 26pt (full pill)
- Padding: 0 24pt
- Pressed: `#00563A`, scale 0.98, medium haptic
- Loading: white activity indicator replaces label

**Secondary Outline Button**
- Background: transparent
- Border: 1.5pt `#00704A`
- Text: `#00704A`, SoDo Sans 15pt w600
- Height: 44pt, corner radius 22pt (pill)
- Pressed: background `#D4E9E2`, light haptic

**Icon Button (Header)**
- Size: 44pt hit area
- Icon: 22pt, `#000000` default, green on tap
- Background: transparent
- Used for: back chevron, store, help

**Size Selector Pill Row (THE signature)**
- 5 pills in a horizontal row: Short (8 oz) / Tall (12 oz) / Grande (16 oz) / Venti (20 oz) / Trenta (30 oz, cold only)
- Each pill: 36pt tall, auto-width (padding 0 14pt)
- Inactive: transparent background, 1.5pt `#E8E8E8` border, text `#000000` SoDo Sans 13pt w600
- Active: background `#00704A`, text `#FFFFFF`, no border
- Icon above label: small coffee cup silhouette 20pt tall, scaled by size
- Gap: 8pt between pills
- Pressed: light haptic + background slides via 200ms ease

**Customization Stepper (Syrup Pumps, Espresso Shots)**
- "− 2 +" pattern: leading "−" button (36pt circle, gray outlined), count "2" in SoDo Sans 17pt w700 center, trailing "+" button (36pt circle, gray outlined)
- Pressed on +/−: scale 0.9, light haptic
- Count updates live in the calorie/price summary

**Toggle (Whipped Cream, Ice)**
- iOS-style switch but with Starbucks green `#00704A` active tint

### Cards & Containers

**Rewards Stars Dashboard (THE signature component)**
- Position: top of Home tab, centered
- 180×180pt circular progress ring:
  - Track: `#E8E8E8`
  - Fill: Gold gradient `#CBA258 → #E4C896 → #CBA258`
  - Stroke width: 12pt
  - Progress: arc from 12 o'clock position, clockwise, based on currentStars / targetStars
  - Center: Stars count "47" in SoDo Sans 48pt w800 `#CBA258`, then "Stars" label in SoDo Sans 13pt w600 `#595959`
- Below ring: "75 Stars until free reward" subtitle in SoDo Sans 14pt w400 `#595959`
- Tap: navigates to Rewards detail screen

**Category Card (Order Grid)**
- Aspect: 2:3 (taller than square) — or 4:3 on wider variants
- Corner radius: 16pt
- Background: full-bleed photo of category item (e.g., steaming cappuccino)
- Overlay: `rgba(0,0,0,0.2)` bottom 40% vertical gradient for label legibility
- Label: SoDo Sans 16pt w700 white, bottom-left, 16pt padding
- Shadow: `rgba(0,0,0,0.08) 0 2px 8px`
- Tap: scale 0.98 + light haptic
- Grid: 2-column with 12pt gap on phone, 3-column on iPad

**Drink Detail Card (PDP)**
- Hero photo full-width 3:2 at top with 20pt rounded bottom corners
- Below: drink name 22pt w700, category badge ("Hot Coffee"), calorie line 13pt gray, description 15pt body
- Customize button: outlined green pill "Customize"
- Size selector row (as described above)
- Customization list expanded on tap:
  - Milk dropdown (2% Milk, Whole, Oat, Almond, Coconut, Breve, Heavy Cream, etc.)
  - Syrup pumps with steppers (Vanilla, Hazelnut, Caramel, etc.)
  - Espresso shots stepper
  - Whipped cream toggle
  - Sweetener packets stepper
- Price display bottom-right of each option as delta: "+$0.60 Oat Milk"
- Fixed bottom: "Add to Order — $5.45" pill button (green, 52pt)

**Order Confirmation Card**
- Background: `#D4E9E2` mint
- Icon: green checkmark circle
- Title: "Order placed" SoDo Sans 22pt w700
- Store: "Pike Place Market · Seattle" 15pt w400
- ETA: "Ready in 4-7 min" 15pt w600 `#00754A`
- Order summary rows with prices

**Scan to Pay Hero**
- Top: Stars balance summary card (gold gradient background, stars count + name)
- Center: QR/barcode display (300×180pt white with black barcode + numeric below)
- Bottom: "Balance: $24.50" 17pt w700, "Reload" green pill button
- Barcode scans at POS — primary in-store flow

**Store Locator Card**
- Map preview at top (16pt corner radius)
- Below: store name 17pt w700, address 14pt gray, distance "0.3 mi", "Open until 8 PM" in green
- Actions: "Start Order" | "Directions"

### Navigation

**Top Nav**
- Height: 44pt + safe area
- Background: `#F9F9F9` warm white (light) / `#1E3932` house green (dark)
- Leading: screen title in SoDo Sans 22pt w700 `#000000`
- Trailing: 24pt action icon (cart, store, help)
- No chrome below — content flows immediately

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt top border `#E8E8E8`
- Tabs: Home | Scan | Order | Gift | Offers
- Icon size: 24pt, 2pt stroke geometric
- Active: `#00704A` Siren Green (filled variant)
- Inactive: `#595959`
- Labels: SoDo Sans 11pt w600, always shown
- Scan tab icon is a QR-code glyph — the in-store anchor
- Haptic on tab change: light

### Input Fields

**Search Input (Find Store / Drink)**
- Background: `#F1F1F1`
- Height: 44pt
- Corner radius: 22pt (pill)
- Leading: 18pt magnifying glass `#595959`
- Placeholder: "Search drinks, food, and more" in SoDo Sans 15pt w400 `#8E8E8E`
- Focus: no border change

**Dropdown (Milk Picker)**
- Tappable row: label "Milk" left, current value "2% Milk" right with chevron
- Expands to a sheet with options + radio selectors
- Radio: green-filled circle active

### Distinctive Components

**Stars Progress Ring**
- 180pt circle with 12pt gold-gradient stroke
- Starts at 12 o'clock, fills clockwise proportional to stars/reward
- Center label: bold Stars count with "Stars" subtitle
- Animated fill on Stars earned (1.5s ease-out)

**Size Selector (Short/Tall/Grande/Venti/Trenta)**
- The row feels like a barista's menu — pills side by side with escalating cup-silhouette icons
- Small (Short) → Large (Trenta, cold only)
- Tapping a size slides the green background pill to the new position with a 250ms ease

**Category Grid Card**
- Photography is the content
- Label sits bottom-left with a subtle gradient overlay for legibility
- Corner radius 16pt, subtle shadow
- Entire card is tappable

**QR/Barcode Pay Card**
- Full-width card with white background, 20pt radius
- Top: Gold Stars count + "Pay" label
- Middle: 1D barcode + below it the numeric code "6241 1234 5678 9012"
- The barcode auto-refreshes every 30s for security
- Bottom: "Balance" amount + "Reload" pill

**Order Builder Customization Stepper**
- "Syrup: Vanilla"  — +/− stepper inline with pump count
- Default pumps shown based on drink size (3 for Grande, 4 for Venti)
- Pump silhouette icon can optionally replace the count number visually

**Reward Tile Banner**
- Full-width banner on Home
- Background: soft mint `#D4E9E2`
- Left: gold star icon + "25 Stars" count
- Right: "Free Drink" label + "Redeem" green pill button
- Corner radius: 16pt

**Offer Card (Personalized)**
- Photo hero top (e.g., a seasonal drink)
- Overlay: "Bonus Stars" tag in gold gradient top-right corner
- Title: "Earn 2x Stars on your next cold brew"
- Expiry: "Ends Sunday"
- "Add to offers" button

**Store Locator Pin**
- Green circle (`#00704A`) with white Siren logo center, 40pt diameter on map
- "Drive-thru available" icon badge bottom-right if applicable
- Tap: opens store detail sheet

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Standard margin: 16pt horizontal
- Card padding: 16pt interior
- Gap between cards in grid: 12pt
- Gap between sections: 24pt

### Grid & Container
- Home: single column, full-width Stars dashboard at top, sections stacked below
- Order grid: 2-column on phone, 3-column on wide / iPad
- PDP: single column, 16pt horizontal padding, hero image full-width
- Cart: list rows with 80pt drink thumbnails, price trailing

### Whitespace Philosophy
- **Warm and calm**: generous 1.45 line-height on body copy, 24pt between sections — reads like a café menu
- **Stars dashboard breathes**: 180pt ring centered with 40pt of canvas on either side
- **Category cards dense but legible**: 2-column grid with 12pt gaps
- **Drink builder is methodical**: each customization in its own row, 16pt padding, dividers between

### Border Radius Scale
- Square (0pt): Rare, mostly photo backgrounds
- Subtle (4pt): Small badges, Stars progress dot markers
- Standard (12pt): Modal inner cards, small buttons
- Comfortable (16pt): Category cards, reward tiles, offer cards
- Cards (20pt): Hero cards, drink-detail top radius
- Pill (22-26pt): Primary buttons, search inputs, size selector pills
- Circle (50%): Stars ring, stepper buttons, tab icons, store pin

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, list rows, body backgrounds |
| Hairline (Level 1) | 1pt `#E8E8E8` border | Outline buttons, size selector inactive, input default |
| Card (Level 2) | `rgba(0,0,0,0.08) 0 2px 8px` | Category cards, offer cards, reward tiles |
| Primary Button (Level 3) | `rgba(0,112,74,0.2) 0 4px 12px` | Siren Green CTAs (branded green shadow) |
| Sheet (Level 4) | `rgba(0,0,0,0.15) 0 -4px 20px` | Bottom sheets, drink customization modals |
| Store Card (Level 5) | `rgba(0,0,0,0.12) 0 2px 16px` | Store locator result card |
| Dim Overlay | `rgba(0,0,0,0.5)` | Behind modals |

**Shadow Philosophy**: Starbucks shadows are warm and soft — 8% black at 8pt blur on category cards is enough to create the "tiles floating on warm white" feel without going industrial. The primary Siren Green button gets a *branded green shadow* (0.2 opacity green) that reinforces the brand without feeling harsh. The Stars dashboard uses no shadow — the gold gradient ring is already the focal point.

### Motion
- **Size selector slide**: active green pill slides to new size over 250ms ease-out
- **Stepper press**: +/− button scale 0.9 + light haptic; count updates with a subtle 80ms scale bump
- **Category card tap**: scale 0.98 spring (damping 0.8), light haptic, then navigate
- **Stars ring fill animation**: gold stroke animates from 0 → target progress over 1.5s ease-out on first appearance
- **Reward earned**: Stars count ticks up one by one with 80ms between, ring fills proportionally, success haptic on last Star earned
- **Order placed confirmation**: green checkmark scales 0 → 1.2 → 1.0 over 400ms spring with success haptic
- **Bottom sheet**: 0.35s spring with damping 0.85 snap
- **Primary CTA press**: scale 0.98 + background darkens to `#00563A`, medium haptic
- **Cart add**: small "+" emanates from button toward cart icon over 400ms (motion path)
- **QR/Barcode refresh**: fade cross-dissolve 300ms every 30s, no haptic

## 7. Do's and Don'ts

### Do
- Use Siren Green (`#00704A`) on every primary CTA — Add to Order, Pay, Order button
- Use House Green (`#1E3932`) as the dark canvas — very dark forest green, NOT black
- Place the Stars dashboard ring at the top of the Home tab — it's the daily habit hook
- Render the Stars count in SoDo Sans 48pt w800 gold `#CBA258` centered in the ring
- Use the 5-pill size selector for drinks (Short / Tall / Grande / Venti / Trenta)
- Use mint (`#D4E9E2`) for reward-moment tints and soft promo banners
- Use Warm White (`#F9F9F9`) canvas — softer than pure white
- Use SoDo Sans weights 400/600/700/800 — ExtraBold reserved for Stars and hero
- Render Scan tab as the central in-store anchor
- Trigger medium haptic on primary tap; success haptic on Stars earned and order placed

### Don't
- Don't use pure black for the dark canvas — `#1E3932` House Green is load-bearing
- Don't use a banking-style blue for links — Info Blue is sparingly used
- Don't use display type larger than 48pt — Stars count is the only display moment
- Don't skip the size selector — it's the mental model of ordering at Starbucks
- Don't collapse category cards — photography IS the content
- Don't render stars in yellow — gold (`#CBA258`) is the brand
- Don't use red/orange destructive colors for routine actions — Starbucks UI is hospitable
- Don't hide the Stars progress when a user has earned some — it's the motivational core
- Don't over-animate — motion is calm (250-400ms, dampings 0.7-0.85)
- Don't introduce gradients outside: Stars ring (gold), hero card backgrounds, or primary-button branded shadow

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Stars ring shrinks to 160pt, 2-column category grid with smaller cards |
| iPhone 13/14/15 | 390pt | Standard 180pt Stars ring, 2-column 170pt category cards |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected above nav |
| iPhone 15/16 Pro Max | 430pt | Stars ring 200pt, 2-column wider cards |
| iPad | 768pt+ | 3-4 column category grid; Stars ring stays 200pt, centered; PDP becomes side-by-side |

### Dynamic Type
- Category titles, body, descriptions: full scale
- Stars count in the ring: fixed 48pt (layout-sensitive)
- Size selector: fixed 13pt (pill widths are tight)
- Tab labels: fixed 11pt
- CTA labels: scale up to 120% then truncate

### Orientation
- Home / Rewards / Order / Gift / Offers: **portrait-preferred** (landscape supported on iPad)
- Store locator: landscape-enabled (maps benefit)
- Scan to pay: **portrait-locked** (barcode readability)

### Touch Targets
- Primary CTA: 52pt tall
- Size pills: 36pt tall — small, but 44pt effective via generous horizontal padding
- Category card: full card tappable
- Stars ring: full ring tappable (180pt circle)
- Tab bar icons: 24pt icon with 44pt hit

### Safe Area Handling
- Top: respected on nav
- Bottom: tab bar respects home indicator
- Sides: 16pt content insets; category grid edge-to-edge with internal padding

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#F9F9F9`
- Canvas (dark): `#1E3932`
- Card surface: `#FFFFFF`
- Surface muted: `#F1F1F1`
- Divider: `#E8E8E8`
- Text primary: `#000000`
- Text secondary: `#595959`
- Siren Green (primary): `#00704A`
- Green pressed: `#00563A`
- House Green: `#1E3932`
- Mint: `#D4E9E2`
- Gold Star: `#CBA258`
- Gold alt: `#C1A265`
- Success Green: `#00754A`
- Alert Red: `#DD3333`

### Example Component Prompts
- "Create a SwiftUI Starbucks Stars dashboard: 180pt circular progress ring centered at top of Home. Track color `#E8E8E8` with 12pt stroke. Progress fill using a gold gradient `#CBA258 → #E4C896 → #CBA258`, starting at 12 o'clock and filling clockwise based on `currentStars / targetStars`. Center shows Stars count '47' in SoDo Sans 48pt w800 `#CBA258`, then 'Stars' subtitle in SoDo Sans 13pt w600 `#595959`. Below the ring: '75 Stars until free reward' in SoDo Sans 14pt w400 `#595959`. Animate the fill from 0 to target over 1.5s ease-out on appear."
- "Build the drink size selector row: 5 pills for 'Short', 'Tall', 'Grande', 'Venti', 'Trenta', each 36pt tall, auto-width (padding 0 14pt), with 8pt gap between pills. Inactive: transparent background, 1.5pt `#E8E8E8` border, label in SoDo Sans 13pt w600 `#000000` with 0.2pt tracking. Active: `#00704A` Siren Green background, white text, no border. Above each label: small coffee-cup silhouette icon scaled to size (Short smallest → Trenta largest). Tapping a size slides the green pill to the new position over 250ms ease-out with a light haptic."
- "Design the primary Order CTA: 52pt tall pill button, full-width minus 16pt margins. Background `#00704A` Siren Green, label 'Add to Order — $5.45' in SoDo Sans 16pt w700 white with 0.2pt tracking, full-pill 26pt corner radius. Shadow `rgba(0,112,74,0.2) 0 4px 12px` (branded green glow). Pressed: `#00563A`, scale 0.98, medium haptic."
- "Create a category card for the Order grid: 2:3 aspect, 16pt corner radius, full-bleed photo of a steaming cappuccino as background. Apply a bottom 40% vertical gradient `rgba(0,0,0,0.0) → rgba(0,0,0,0.5)` for label legibility. Label 'Hot Coffees' in SoDo Sans 16pt w700 white, bottom-left corner with 16pt padding. Shadow `rgba(0,0,0,0.08) 0 2px 8px`. Tap: scale 0.98 spring + light haptic."
- "Build the Scan-to-Pay screen: Warm White `#F9F9F9` canvas. Top: gold-gradient Stars balance card (16pt corner radius) showing Stars count and tier badge. Middle: white 20pt-radius card containing a 1D barcode centered, with the numeric code '6241 1234 5678 9012' in SoDo Sans 13pt w400 tabular below it. Bottom: 'Balance' label + '$24.50' amount in SoDo Sans 22pt w700, then a 'Reload' Siren Green pill button. Barcode refreshes every 30s with a 300ms cross-dissolve."
- "Design a customization stepper row for syrup pumps: leading label 'Vanilla Syrup' in SoDo Sans 15pt w600, then a stepper on the right: minus button (36pt circle, transparent with `#E8E8E8` border, `-` glyph in `#000000`), count '2' in SoDo Sans 17pt w700 center, plus button (36pt circle, same style, `+` glyph). Pressing +/− scales the button 0.9 with a light haptic and updates the count live."

### Iteration Guide
1. Siren Green (`#00704A`) is the CTA color; House Green (`#1E3932`) is the dark canvas — never use pure black
2. Stars dashboard (gold-gradient progress ring with center count) is the defining daily hook — place it at the top of Home
3. Size selector pills are the mental model of ordering: Short / Tall / Grande / Venti / Trenta with escalating cup icons
4. Warm White (`#F9F9F9`) as the light canvas — softer than pure white, more welcoming
5. SoDo Sans weights 400/600/700/800 — ExtraBold reserved for Stars count (48pt) and hero moments
6. Gold (`#CBA258`) is the Stars and rewards color — NOT yellow, NOT orange
7. Use mint (`#D4E9E2`) for reward-moment tints and gentle promo backgrounds
8. Category cards lean on photography — let the drink photos carry the content, labels minimal
9. Haptics: light on pills and steppers, medium on primary tap, success on Stars earned and order placed
