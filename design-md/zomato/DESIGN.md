# Design System Inspiration of Zomato (iOS)

## 1. Visual Theme & Atmosphere

Zomato's iOS app is a dual-purpose restaurant platform: it is both a **food-delivery** app and a **dining-out / discovery** app, and the entire design system is organized around that split. The canvas is pure white (`#FFFFFF`) and the brand color is the unmistakable **Zomato Red** (`#E23744`) — used for the logomark, the primary action, the active tab, the "ADD" affordance and key CTAs. But the most distinctive element of Zomato's visual language is its **rating pill**: a small, bold, color-coded chip showing a numeric score with a star — and that chip's color is *semantic*. Green (`#267E3E`) means a great rating, amber (`#DB7C38`) means average, and red (`#E23744`) means poor. The rating pill appears everywhere — on restaurant cards, restaurant headers, and individual dishes — and its color is the single fastest signal in the entire app.

The two signature UI moments are the **restaurant detail screen** and the **order flow**. The detail screen opens with a full-bleed food/storefront hero photo (~200pt) under a transparent nav (back + search + heart in dark scrims), then an info card that pulls up over the photo with a rounded-22pt top, the restaurant name in heavy 24pt, a cuisine·locality·distance meta line, and **two rating pills side by side** — one for Delivery, one for Dining — each with its review count underlined below it. Under that sits the **Delivery / Dining Out segmented toggle** (a pill segment, active half filled Zomato Red) that re-themes the whole screen: Delivery shows a scrollable menu with photo'd items and a bordered red "ADD" button per row; Dining Out shows offers, photos, reviews and a "Book a table" path. Each menu item carries a **veg/non-veg mark** (a small bordered square — green dot = veg, maroon triangle = non-veg, a legal requirement in India that became a brand signature), an optional amber "★ Bestseller" tag, the dish name, price, and its own green rating pill.

Zomato's other recognizable detail is the **bordered "ADD" button**: rather than a solid red fill, the menu add control is a white/surface button with a 1pt Zomato-Red border and red text, sitting half-overlapping the dish thumbnail with a soft shadow. On tap it morphs into a bordered red quantity stepper. The premium tier (**Zomato Pro / Gold**) is signaled with a gold (`#EFC75E`) accent used sparingly on membership surfaces; Dining Out reservation flows lean on a calm blue (`#256FEF`).

Typography is a clean, dense grotesque. Zomato's brand face is a custom sans (Okra-family); the closest free substitute is **Inter** (or system SF Pro). The hierarchy is bold and information-dense — restaurant names 24pt 800, screen titles 900, menu categories 17pt 800, and a lot of small, tightly-set metadata because Zomato pages carry a high density of facts (cuisine, distance, timing, price, rating, review count, veg status).

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / warm near-black (`#121212`) dark — dense, information-rich
- **Zomato Red** (`#E23744`) for brand, primary action, active tab, "ADD", key CTAs
- **Semantic rating pill** — green (`#267E3E`) great / amber (`#DB7C38`) average / red (`#E23744`) poor — the fastest signal in the app
- **Delivery / Dining Out duality** — a segmented toggle re-themes the screen between the two modes
- Full-bleed hero photo + info card pulled up over it with a rounded-22pt top — the restaurant detail signature
- **Veg / non-veg mark** — bordered square: green dot = veg, maroon triangle = non-veg (an India-mandated, brand-defining glyph)
- **Bordered "ADD" button** — white/surface with a 1pt Zomato-Red border + red text, overlapping the dish thumbnail, morphs into a bordered red stepper
- Amber "★ Bestseller" tags on popular dishes
- Two rating pills side-by-side (Delivery + Dining) with underlined review counts on the restaurant header
- Zomato Pro / Gold signaled with a gold (`#EFC75E`) accent; Dining reservations use a calm blue (`#256FEF`)
- Bottom tab bar: Delivery / Dining / Live / Profile — active icon tinted Zomato Red
- Dense, heavy grotesque type — restaurant names 800, screen titles 900, lots of tight metadata

## 2. Color Palette & Roles

### Primary (Interactive)
- **Zomato Red** (`#E23744`): Primary brand & interactive color — logomark, primary buttons, "ADD", active tab, key CTAs, active segment.
- **Zomato Red Pressed** (`#C42531`): Pressed/active state on red buttons and controls.

### Rating Scale (Semantic — color encodes the score)
- **Rating Green** (`#267E3E`): Great rating (≈ 4.0+). Rating pill fill, dish-rating star.
- **Rating Amber** (`#DB7C38`): Average rating (≈ 3.0–3.9). Rating pill fill, "Bestseller" tag text.
- **Rating Red** (`#E23744`): Poor rating (< 3.0). Rating pill fill (reuses the brand red).
- **Rating Grey** (`#9C9C9C`): New / unrated. Neutral pill fill.

### Membership & Dining
- **Gold (Pro)** (`#EFC75E`): Zomato Pro / Gold membership accents — used sparingly on membership surfaces.
- **Dining Blue** (`#256FEF`): Dining Out reservation flows, "Book a table", table-booking confirmations.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F4F5`): Search field, segmented control track, subtle cards.
- **Surface Pressed** (`#EBEBEC`): Pressed rows and cells.
- **Divider** (`#E8E8E8`): Hairline dividers between menu rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — warm near-black, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Cards, info card, "ADD" button, elevated surfaces.
- **Dark Surface 2** (`#262629`): Segmented control track, pressed cells.
- **Dark Divider** (`#2C2C2E`): Hairline dividers.

### Text
- **Text Primary Light** (`#1C1C1C`): Primary text — near-black, restaurant names, prices.
- **Text Secondary Light** (`#696969`): Metadata, cuisine, locality, distance.
- **Text Tertiary Light** (`#9C9C9C`): Disabled, low-emphasis captions.
- **Text Primary Dark** (`#F2F2F2`): Primary text on dark.
- **Text Secondary Dark** (`#9C9C9C`): Secondary text on dark (works on both modes).

### Veg / Non-Veg
- **Veg Green** (`#267E3E`): Veg-mark border + dot (shares the rating green).
- **Non-Veg Maroon** (`#B91C1C`): Non-veg-mark border + triangle.

### Semantic
- **Error Red** (`#E03A3A`): Form errors, failed payment, removal confirmation.
- **Success Green** (`#267E3E`): Order placed, item added confirmation (reuses the rating green).
- **Warning Amber** (`#DB7C38`): "Closes soon", surge messaging (shares the rating amber).
- **Info Blue** (`#256FEF`): Dining / reservation informational accents.

### Color Usage Table

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#121212` |
| Surface 1 | `#F4F4F5` | `#1C1C1E` |
| Surface 2 | `#EBEBEC` | `#262629` |
| Divider | `#E8E8E8` | `#2C2C2E` |
| Text Primary | `#1C1C1C` | `#F2F2F2` |
| Text Secondary | `#696969` | `#9C9C9C` |
| Primary Action | `#E23744` | `#E23744` |
| Rating Great | `#267E3E` | `#267E3E` |
| Rating Average | `#DB7C38` | `#DB7C38` |
| Rating Poor | `#E23744` | `#E23744` |
| Pro / Gold | `#EFC75E` | `#EFC75E` |
| Dining Blue | `#256FEF` | `#256FEF` |

## 3. Typography Rules

### Font Family
- **Brand Face**: Zomato's custom grotesque (the company uses an Okra-family humanist sans).
- **Recommended Substitute (unlicensed clone)**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist grotesque that holds Zomato's dense, heavy-weight feel at small sizes.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for prices, ratings, review counts and quantity steppers (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 900 | 1.15 | -0.5pt | "Order food", large nav title |
| Restaurant Hero | Inter | 24pt | 800 | 1.2 | -0.4pt | Restaurant detail header name |
| Section Header | Inter | 20pt | 800 | 1.25 | -0.3pt | "Recommended", section title |
| Menu Category | Inter | 17pt | 800 | 1.3 | -0.2pt | "Kebabs & Starters" |
| Item Name | Inter | 15pt | 700 | 1.35 | 0pt | Dish name on menu row |
| Body | Inter | 14pt | 500 | 1.45 | 0pt | Dish description, paragraphs |
| Price | Inter | 14pt | 700 | 1.3 | 0pt | Prices, totals — tabular |
| Meta | Inter | 13pt | 400 | 1.4 | 0pt | Cuisine · locality · distance, muted |
| Rating Pill | Inter | 14pt | 800 | 1.0 | 0pt | Score inside the color pill |
| Rating Caption | Inter | 11pt | 400 | 1.3 | 0pt | "12.4K Delivery", underlined |
| Overline / Tag | Inter | 11pt | 800 | 1.0 | 0.4pt | "BESTSELLER", uppercase amber |
| Button | Inter | 16pt | 800 | 1.0 | 0pt | Primary button label |
| ADD Button | Inter | 14pt | 800 | 1.0 | 0.3pt | "ADD" on menu rows |
| Tab Label | Inter | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 500 | 1.35 | 0pt | Timestamps, footnotes |

### Principles
- **Dense and heavy**: Zomato pages carry a lot of facts — names sit at weight 800, screen titles at 900, with tight negative tracking on big type; metadata is small and muted to keep density readable.
- **The rating number leads**: inside a pill the score is weight 800 — it is the single most-scanned value, so it is always bold and high-contrast on its colored chip.
- **Color over labels**: a rating's quality is read from the pill color before the number; the veg/non-veg status is read from the mark color, not text.
- **Muted metadata**: cuisine, locality and distance stay in `#696969` so the photo, name, rating pill and price lead.
- **Dynamic Type first-class**: screen titles, restaurant names, body and prices scale; rating pills, the veg mark, tags, tab labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Add item · ₹320" / "Place order" / "Pay")**
- Shape: Rounded rectangle, 12pt corner radius
- Background: `#E23744` (Zomato Red) / same on dark
- Text: `#FFFFFF`, Inter 16pt weight 800
- Height: 52pt; padding 14pt × 28pt; often full-width with 16pt insets, embeds price (tabular)
- Pressed: background `#C42531` + scale 0.98
- Disabled: `#E23744` at 35% opacity, text 60%

**"ADD" Button (the Zomato signature — bordered, not filled)**
- Background: `#FFFFFF` / `#1C1C1E` dark (surface, NOT red fill)
- Border: 1pt `#E23744`
- Text: `#E23744`, Inter 14pt weight 800, letter-spacing 0.3pt, label "ADD"
- Shape: 10pt corner radius; padding 8pt × 22pt
- Position: overlaps the bottom of the dish thumbnail by ~16pt with a soft `0 3px 8px rgba(0,0,0,0.12)` shadow
- On tap: morphs in place into a bordered red quantity stepper

**Outline / Secondary Button ("Customise" / "Book a table")**
- Background: transparent (Dining "Book a table" uses Dining Blue text/border)
- Border: 1.5pt `#E8E8E8` light / `#2C2C2E` dark
- Text: `#1C1C1C` light / `#F2F2F2` dark, Inter 14pt weight 700
- Shape: 10pt corner radius; padding 12pt × 20pt
- Pressed: background `#F4F4F5`

**Text Button ("View full menu" / inline link)**
- Text: `#E23744` (Zomato Red), Inter 14pt weight 800
- No background, no underline
- Pressed: 60% opacity

**Button State Reference**

| State | Primary (Red) | ADD (Bordered) | Outline | Text |
|-------|---------------|----------------|---------|------|
| Default | `#E23744` / white | surface / 1pt `#E23744` / `#E23744` | transparent / `#1C1C1C` | `#E23744` |
| Pressed | `#C42531` + scale 0.98 | bg `#FDECEE` tint | bg `#F4F4F5` | 60% opacity |
| Active (added) | — | morphs to bordered red stepper | — | — |
| Disabled | `#E23744` @ 35% / text 60% | border @ 40% / text 40% | border @ 40% | 40% opacity |
| Focus (a11y) | 2pt `#E23744` ring, 2pt offset | 2pt `#E23744` ring | 2pt ring | underline on focus |

### Core Atoms

**Rating Pill (the signature)**
- Shape: 7pt corner radius chip, padding 4pt × 9pt
- Fill: semantic — `#267E3E` (great) / `#DB7C38` (average) / `#E23744` (poor) / `#9C9C9C` (new)
- Content: a 11pt white star + the score in Inter 14pt weight 800 white
- On a restaurant header: two pills side-by-side (Delivery + Dining) with an underlined 11pt review-count caption beneath each
- On a dish row: a smaller green pill or inline star + score + "(218)" count in `#696969`

**Veg / Non-Veg Mark**
- A 16pt square with a 1.5pt colored border, 3pt corner radius
- Veg: `#267E3E` border + a centered `#267E3E` dot
- Non-Veg: `#B91C1C` border + a centered `#B91C1C` triangle
- Always leads a menu-item row (legally required in India; a brand signature elsewhere)

**Menu Item Row (restaurant detail)**
- Layout: info block (left) + 96pt thumbnail with the "ADD" button overlapping its bottom (right)
- Order: veg/non-veg mark → optional amber "★ Bestseller" → name (15pt 700) → price (14pt 700) → rating line (10pt green star + score + count)
- Description (when present): 14pt `#696969`, 2-line clamp
- Divider: 1pt `#E8E8E8` between rows

**Bestseller Tag**
- Inline, leading ★ glyph + "Bestseller" in Inter 11pt 800 amber `#DB7C38` (uppercase optional)
- No background; sits above the dish name

**Quantity Stepper (bag / after ADD)**
- A bordered control echoing the ADD button: 1pt `#E23744` border, 10pt radius, surface background
- `−` / `+` controls 36pt, Zomato-Red glyphs 18pt 800; count 15pt 800 red, 24pt min-width tabular
- No solid fill — it stays a bordered red control to match the ADD affordance

**Restaurant Card (listing)**
- Photo: full-width 16:9, 12pt radius; top-right saved heart in a dark scrim
- Overlaid bottom-left: offer ribbon ("50% OFF up to ₹100") in red/maroon
- Below: name (16pt 800) + rating pill (right); cuisine·price-for-two (13pt `#696969`); delivery-time + distance line

### Navigation

**Restaurant Detail Top Nav (over hero)**
- Transparent over the 200pt hero photo; back + search + heart in 34pt `rgba(0,0,0,0.4)` circular scrims with white glyphs
- Collapses to a solid bar with the restaurant name (compact) once scrolled past the hero

**Delivery / Dining Out Segmented Toggle**
- A pill segment: track `#F4F4F5` / `#262629` dark, 999px radius, 4pt inner padding
- Two segments; active half filled Zomato Red `#E23744` with white 13pt 700 text; inactive `#696969`
- Switching re-themes the screen content (menu vs. offers/reviews/booking)

**Search Field**
- Height: 46pt; corner radius 10pt; background `#F4F4F5` / `#1C1C1E` dark
- Leading magnifier 16pt `#696969`; placeholder "Restaurant name or a dish" 14pt `#696969`
- Focus: 1.5pt `#E23744` border

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `rgba(18,18,18,0.94)` blurred dark, 0.5pt top divider
- Tabs (4): Delivery, Dining, Live, Profile
- Icon size: 22pt; active color `#E23744` (icon fills); inactive `#888`
- Labels: Inter 10pt weight 600, always shown

### Input Fields

**Text Field (address, instructions, search)**
- Height: 48pt; corner radius 10pt; background `#F4F4F5` / `#1C1C1E` dark
- Border: none default; focus 1.5pt `#E23744`
- Label: floating 12pt `#696969`; input 16pt `#1C1C1C`
- Error: 1.5pt `#E03A3A` border + helper text `#E03A3A` 12pt

**Search-with-results (restaurant / dish search)**
- Same field; results below as rows: leading 44pt rounded-8pt thumbnail, title 15pt 700, rating pill trailing
- Pressed row: `#EBEBEC`

### Distinctive Components

**Semantic Rating Pill**
- The defining Zomato component: a color-coded score chip whose fill *is* the rating quality (green/amber/red). Appears on cards, headers and dishes — the fastest signal in the app

**Veg / Non-Veg Mark**
- The India-mandated bordered-square glyph (green dot / maroon triangle) on every menu item — instantly recognizable as Zomato/Indian-food UX

**Bordered "ADD" → Stepper**
- The white-with-red-border ADD button overlapping the dish thumbnail that morphs into a bordered red stepper — Zomato's signature add gesture

**Delivery / Dining Out Toggle**
- The screen-level segmented control that switches the entire restaurant detail between delivery (menu + ADD) and dining (offers + reviews + book-a-table)

**Hero + Pull-Up Info Card**
- Full-bleed hero photo with a rounded-22pt info card sliding up over it carrying name, meta and the dual rating pills

**Offer Ribbon**
- A bold red/maroon offer strip on restaurant cards and headers ("50% OFF up to ₹100", "Free delivery")

**Zomato Pro / Gold Banner**
- A gold-accented (`#EFC75E`) membership card promising extra discounts and free delivery — used sparingly

**Live Order Tracking**
- A map with the rider marker, an ETA headline, and a status strip ("Restaurant accepted → Food being prepared → Rider picked up → Delivered") in Zomato Red progress

**Item Customization Sheet**
- Bottom sheet, 22pt top radius (matching the detail pull-up card); dish photo (16:9, 12pt radius) + veg/non-veg mark + name 20pt 800 + price 14pt 700 tabular
- Option groups: title 15pt 700 + "Required" / "Add up to 3" subtitle in `#696969` 12pt; choice rows with a leading Zomato-Red `#E23744` radio/checkbox when chosen, label 14pt 500, "+ ₹40" delta trailing
- Sticky red 12pt-radius button: "Add item · ₹360" with the live tabular total

**Cart / Bag Sheet**
- Bottom sheet, 22pt top radius; line items each with the veg/non-veg mark, name, the bordered red stepper, and price; "Save more with Gold" gold-accented row
- Bill summary (item total, taxes, delivery) and a sticky red "Proceed to Pay · ₹612" button (tabular)

**Offer Ribbon**
- A bold red/maroon strip on cards and the restaurant header — "50% OFF up to ₹100", "Free delivery" — 12pt 800 white on `#E23744`, 4pt radius; an applied-offer row in the cart shows the saved amount in green `#267E3E`

**Zomato Pro / Gold Banner**
- A gold-accented (`#EFC75E`) membership card on the home and cart promising extra discounts + free delivery; leading crown glyph, 12pt radius, used sparingly and never in the chrome

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 18, 22, 24, 32, 40, 48
- Menu row vertical padding: 14pt; section header bottom margin: 14pt
- Screen horizontal inset: 16–18pt
- Info card overlap over hero: -22pt with a 22pt top radius

### Grid & Container
- iPhone: single-column dense list at 16–18pt insets; hero photos full-bleed
- iPad: 2-column restaurant grid at 24pt insets, max content width 900pt centered
- Menu screen: single-column item rows; sticky category nav + a floating "Menu" / search button
- Listing feed: full-width cards with 16:9 photos

### Whitespace Philosophy
- **Density with rhythm**: Zomato is information-dense by design; 14pt row padding and clear 17–20pt 800 category headers keep the high fact-count scannable
- **Color does the sorting**: rating pills and veg marks let the eye triage quality and dietary fit without reading — that is the whitespace strategy
- **Photo-anchored detail**: the hero photo + pull-up card give the detail screen a strong focal entrance before the dense menu begins
- **The ADD button breathes**: it overlaps the thumbnail with a small shadow so it is always findable in the dense list

### Border Radius Scale
- Square (0pt): full-bleed hero, dividers
- Subtle (4pt): veg/non-veg mark
- Pill-chip (7pt): rating pills
- Standard (10pt): ADD button, stepper, search field, secondary buttons
- Card (12pt): listing photos, dish thumbnails
- Comfortable (22pt): the pull-up info card top, bottom sheets, modals
- Pill (999px): the Delivery/Dining segmented toggle, filter chips
- Circle (50%): avatars, hero nav scrims, map markers

## 6. Depth & Elevation

Zomato is mostly flat — separation comes from photography, dividers, the pull-up card and the semantic color system, with shadow reserved for floating actionable surfaces.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Menu rows, list cells, segmented toggle |
| Raised (Level 1) | `rgba(0,0,0,0.12) 0 3px 8px` | "ADD" button / stepper over a thumbnail, sticky cart bar |
| Pull-up Card | `rgba(0,0,0,0.10) 0 -4px 16px` | The info card sliding up over the hero photo |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -4px 24px` | Customize sheet, cart sheet, address sheet |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind sheets, modals and the hero nav scrims |

**Shadow Philosophy**: shadow marks "this floats and is tappable" — primarily the ADD button overlapping the thumbnail and the sticky cart bar. The pull-up info card uses an upward shadow to read as a sheet rising over the photo. Menu rows and cards never carry shadow; the photo, dividers and rating pills carry the screen. On dark mode shadows fade, so floating surfaces add a 0.5pt divider border as the elevation cue.

### Motion
- **ADD → stepper**: the bordered ADD button cross-fades/expands in place into the bordered stepper over 180ms ease-out; cart bar slides up 280ms + soft haptic
- **Quantity stepper**: number cross-fades + 120ms scale tick per tap; soft haptic
- **Delivery/Dining toggle**: the active red fill slides between halves with a 220ms spring; content cross-fades 200ms
- **Hero pull-up card**: on scroll the card translates and the hero parallax-scrolls at ~0.5x; nav fades from transparent to solid as the hero leaves
- **Card → detail**: shared-element hero photo expands into the detail hero over a 320ms iOS push
- **Rating pill appear**: pills fade/scale-in subtly (no aggressive motion) — the color is the signal, not the animation
- **Sheet present**: customize/cart sheets slide up 300ms ease-out; scrim fades in parallel
- **Order tracking**: rider marker animates along the route; status strip advances with a 250ms ease-out + medium haptic per stage
- **Pull-to-refresh**: Zomato Red spinner on listing feeds
- **Haptics**: light on toggle switch, soft on stepper / ADD, success notification on order placed, medium per tracking-stage advance

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas; warm near-black `#121212` for dark — never true black
- Use Zomato Red (`#E23744`) for brand, primary action, "ADD", active tab and key CTAs
- Make the rating pill **semantic** — green `#267E3E` (great) / amber `#DB7C38` (average) / red `#E23744` (poor); the color must always mean the same thing
- Lead every menu item with the veg/non-veg mark (green dot square / maroon triangle square)
- Use the **bordered** ADD button (surface + 1pt red border + red text), not a solid red fill — and morph it into a bordered red stepper
- Overlap the ADD button on the dish thumbnail with a soft shadow so it is findable in a dense list
- Provide the Delivery / Dining Out segmented toggle on restaurant detail and let it re-theme the screen
- Open restaurant detail with a full-bleed hero photo + a pull-up info card (22pt top radius) carrying the dual rating pills
- Show two rating pills (Delivery + Dining) with underlined review counts on the restaurant header
- Keep type dense and heavy (names 800, titles 900), metadata small and muted
- Embed the price in the primary button ("Add item · ₹320")

### Don't
- Don't make all rating pills one color — the semantic green/amber/red scale is the core of Zomato
- Don't use a solid-red filled ADD button — Zomato's ADD is bordered with red text
- Don't drop the veg/non-veg mark from menu items — it is mandatory and a brand signature
- Don't merge Delivery and Dining into one undifferentiated screen — the toggle and its re-theming are central
- Don't tint or square the hero photo — it is full-bleed and full-color
- Don't use light/thin weights for names and categories — Zomato runs heavy (800/900)
- Don't spread the Pro gold across the chrome — it is membership-only and used sparingly
- Don't use the Dining Blue for delivery actions — blue is reservations/dining only
- Don't shadow menu rows or cards in light mode — photo + dividers + pills create separation
- Don't dim food photography in dark mode — only chrome darkens
- Don't animate aggressively — the rating color is the signal, motion stays functional

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Hero ~180pt; dish thumbnail ~84pt; single column |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; hero nav scrims clear of the island |
| iPhone 15/16 Pro Max | 430pt | Hero ~220pt; larger thumbnails |
| iPad (portrait) | 768pt | 2-column restaurant grid; detail = hero + 2-column menu |
| iPad (landscape) | 1024pt+ | 3-column grid; detail shows menu + sticky cart side-by-side |

### Dynamic Type
- Screen title, restaurant name, body, prices: full scale
- Rating pill, veg/non-veg mark, Bestseller tag, tab labels, ADD label: FIXED (layout-sensitive)
- Dish descriptions: scale to XL

### Orientation
- iPhone: portrait-locked on most flows; tracking map may rotate
- iPad: full rotation; detail shows a two-pane (menu + cart) layout in landscape

### Touch Targets
- Menu row: full-row tap, ≥ 88pt tall
- ADD button: ≥ 44pt hit (visually ~36pt tall)
- Stepper controls: 36pt glyph in 44pt hit area
- Segmented toggle half: ≥ 44pt tall
- Tab bar icon: 22pt glyph, 44pt hit
- Hero nav scrims: 34pt circle, 44pt hit
- Primary buttons: 52pt tall

### Safe Area Handling
- Top: hero nav scrims respect safe area and Dynamic Island; collapsed nav respects the notch
- Bottom: tab bar and sticky cart bar respect the home indicator
- Hero photo: extends under the status bar with a top gradient scrim for legibility
- Sides: 16–18pt content inset throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface (light): `#F4F4F5`
- Divider (light): `#E8E8E8`
- Text primary: `#1C1C1C` light / `#F2F2F2` dark
- Text secondary: `#696969` light / `#9C9C9C` dark
- Zomato Red (brand / primary): `#E23744`
- Red pressed: `#C42531`
- Rating great (green): `#267E3E`
- Rating average (amber): `#DB7C38`
- Rating poor (red): `#E23744`
- Veg green: `#267E3E` / Non-veg maroon: `#B91C1C`
- Pro gold: `#EFC75E` / Dining blue: `#256FEF`
- Error red: `#E03A3A`

### Example Component Prompts
- "Build the Zomato rating pill in SwiftUI: a 7pt-radius chip, padding 4pt × 9pt, fill determined semantically by the score — `#267E3E` if rating ≥ 4.0, `#DB7C38` if 3.0–3.9, `#E23744` if < 3.0, `#9C9C9C` if unrated. Content: an 11pt white star + the score in Inter 14pt weight 800 white. For the restaurant header, render two pills side-by-side (Delivery + Dining) each with an underlined 11pt `#696969` review-count caption beneath ('12.4K Delivery')."

- "Create a Zomato menu item row: leading 16pt veg/non-veg mark (a 1.5pt-bordered 3pt-radius square — `#267E3E` border + center dot for veg, `#B91C1C` border + center triangle for non-veg). Then an optional '★ Bestseller' tag in Inter 11pt 800 amber `#DB7C38`. Then the dish name Inter 15pt 700 `#1C1C1C`, price Inter 14pt 700, and a rating line (10pt green star + score + '(218)' in `#696969`). On the right: a 96pt rounded-12pt thumbnail with a bordered 'ADD' button overlapping its bottom edge by 16pt."

- "Build the Zomato 'ADD' button: NOT a solid red fill — a `#FFFFFF` (dark: `#1C1C1E`) surface with a 1pt `#E23744` border, 10pt corner radius, label 'ADD' in Inter 14pt 800 `#E23744` with 0.3pt letter-spacing, padding 8pt × 22pt, soft `0 3px 8px rgba(0,0,0,0.12)` shadow, positioned overlapping the bottom of the dish thumbnail. On tap it morphs in place into a bordered red quantity stepper (same 1pt red border, 10pt radius; `−`/`+` 36pt red glyphs, count 15pt 800 red tabular)."

- "Create the Zomato Delivery / Dining Out segmented toggle: a 999px pill track `#F4F4F5` (dark `#262629`) with 4pt inner padding and two equal segments. The active segment is filled Zomato Red `#E23744` with white Inter 13pt 700 text; the inactive segment text is `#696969`. Switching slides the red fill between halves over 220ms with a spring and cross-fades the screen content (menu ⇄ offers/reviews/book-a-table) over 200ms."

- "Build the Zomato restaurant detail header: a 200pt full-bleed hero photo under a transparent nav (back + search + heart in 34pt `rgba(0,0,0,0.4)` circular scrims, white glyphs). Below, an info card pulled up -22pt over the photo with a 22pt top corner radius, `#FFFFFF`/`#121212` background: restaurant name Inter 24pt 800, a 'North Indian, Mughlai · Colaba · 2.4 km' meta line Inter 13pt 400 `#696969`, then the two side-by-side rating pills. On scroll the hero parallaxes at 0.5x and the nav fades to a solid bar with the compact name."

- "Render the Zomato bottom tab bar: 4 tabs — Delivery, Dining, Live, Profile. 22pt icons, labels Inter 10pt 600 always shown. Active tab tinted `#E23744` with filled icon; inactive `#888`. Background `#FFFFFF` light / `rgba(18,18,18,0.94)` blurred dark with a 0.5pt top divider."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) light, warm near-black `#121212` dark — never true black
2. Zomato Red `#E23744` = brand/primary/ADD/active tab; pressed `#C42531`
3. The rating pill is SEMANTIC: green `#267E3E` great / amber `#DB7C38` average / red `#E23744` poor — color always means the same thing
4. Every menu item leads with a veg/non-veg mark (green dot square / maroon triangle square)
5. The ADD button is BORDERED (surface + 1pt red border + red text), overlaps the thumbnail, and morphs into a bordered red stepper — never a solid red fill
6. Restaurant detail has a Delivery / Dining Out segmented toggle that re-themes the whole screen
7. Detail opens with a full-bleed hero photo + a pull-up info card (22pt top radius) and two side-by-side rating pills
8. Type runs dense and heavy — names 800, screen titles 900, small muted metadata
9. Pro gold `#EFC75E` is membership-only; Dining Blue `#256FEF` is reservations-only
10. No shadow on menu rows/cards in light mode — photo + dividers + semantic pills create the separation
