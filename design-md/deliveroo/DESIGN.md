# Design System Inspiration of Deliveroo (iOS)

## 1. Visual Theme & Atmosphere

Deliveroo's iOS app is a single-brand-color food delivery experience. The canvas is pure white (`#FFFFFF`) and the entire interface is built around one unmistakable hue — **Deliveroo Teal** (`#00CCBC`, internally "Aquamarine"). That teal is the kangaroo logomark, the primary button, the active tab, the rating star, the Deliveroo Plus mark, the basket button and the search-focus ring. Where most marketplaces juggle three or four accent colors, Deliveroo is disciplined: one bright teal does everything, set against a clean white page and full-color food photography. The teal is so saturated that its "on" color is rarely white — labels on teal buttons are a deep teal-ink (`#003733`) for legibility, a signature Deliveroo move.

The two signature UI moments are the **restaurant feed** and the **restaurant menu**. The feed is the home screen: a "Deliver to" address row with a teal chevron, a fully-rounded pill search field ("Restaurants, groceries, dishes"), a Deliveroo Plus banner, a row of rounded-square category icons (Restaurants / Grocery / Fast / Treats / Offers) each with a teal glyph, then sectioned restaurant cards under headers like "Popular near you". Each card carries a wide 5:3 food photo, a teal "PLUS · Free delivery" or offer badge, a saved heart, the restaurant name in heavy weight, a teal rating star, a cuisine meta line, and a distinctive **fee pill** — a small rounded chip with a teal moped/scooter icon showing "£1.49 delivery · Min £12". Tapping a card opens the **menu**: a hero photo, restaurant name and rating, a sticky category nav, then menu item rows — each a name, a muted description, a bold price, and an 84pt thumbnail with a floating teal `+` button hanging off its corner. That floating teal `+` is the core "add" gesture and the most recognizable Deliveroo menu detail.

**Deliveroo Plus** (the subscription) appears as a teal banner on the feed ("Free delivery on this order — Join"), a teal "PLUS" badge on eligible restaurant cards, and a teal pill in the basket. Plus uses the same teal — there is no separate membership color. The promotional offer color, used sparingly for percentage deals, is a warm gold (`#FFC100`).

Typography is a clean, heavy grotesque. Deliveroo's brand face is a custom sans in the geometric-grotesque family; the closest free substitute is **Inter** (or system SF Pro). The hierarchy is bold and confident — section headers and restaurant names are heavy (weight 800), screen titles are 900, body drops to a readable 500, and metadata is a muted gray. Deliveroo leans heavier than most delivery apps: the bold weight plus the single teal is the brand's whole personality.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / warm near-black (`#121212`) dark — clean, photo-forward
- **One brand color**: Deliveroo Teal (`#00CCBC`) for everything — logo, buttons, ratings, Plus, active tab, basket
- Teal "on" color is deep teal-ink (`#003733`), not white — a Deliveroo signature for button labels
- Kangaroo logomark — the brand's mascot, always in teal
- Wide 5:3 restaurant photography in 14pt rounded containers — full-color, the card anchor
- Distinctive **fee pill** — teal moped icon + "£1.49 delivery · Min £12" rounded chip on every card
- Floating teal `+` button hanging off the corner of menu-item thumbnails — the core "add" gesture
- Rounded-square category icons (Restaurants / Grocery / Fast / Treats / Offers) with teal glyphs
- Deliveroo Plus surfaced as a teal banner + teal "PLUS" badge — same teal, no separate color
- Fully-rounded pill search field and pill buttons (`999px`)
- Promo gold (`#FFC100`) reserved for percentage-off deals only
- Bottom tab bar: Home / Search / Orders / Favourites / Account — active icon tinted Deliveroo Teal

## 2. Color Palette & Roles

### Primary (Interactive)
- **Deliveroo Teal** (`#00CCBC`): The single brand & primary interactive color — logomark, primary buttons, active tab, rating star, basket button, Plus mark, search-focus ring, floating menu `+`.
- **Teal Pressed** (`#00A99C`): Pressed/active state on teal buttons and controls.
- **Teal Ink** (`#003733`): The "on-teal" content color — labels, glyphs and icons sitting on a teal fill (used instead of white for legibility).

### Membership & Promo
- **Plus Mint** (`#C4F4EF`): Soft mint used for Deliveroo Plus banner text/illustration on a deep-teal panel.
- **Promo Gold** (`#FFC100`): Percentage-off deal accents only (e.g. "20% off, up to £10") — used sparingly.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray** (`#F4F4F2`): Category-icon tiles, subtle cards, grouped sections.
- **Surface Pressed** (`#EAEAE8`): Pressed rows and cells.
- **Divider** (`#E8E8E6`): Hairline dividers between menu rows and sections.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#121212`): Primary dark canvas — warm near-black, NOT pure black.
- **Dark Surface 1** (`#1C1C1E`): Cards, search field, elevated surfaces.
- **Dark Surface 2** (`#262629`): Category tiles, fee pill, pressed cells.
- **Dark Divider** (`#2C2C2E`): Hairline dividers.

### Text
- **Text Primary Light** (`#1D1D1B`): Primary text — near-black, restaurant names, prices.
- **Text Secondary Light** (`#6B6B6B`): Metadata, cuisine, descriptions.
- **Text Tertiary Light** (`#A0A0A0`): Disabled, low-emphasis captions.
- **Text Primary Dark** (`#F4F4F2`): Primary text on dark.
- **Text Secondary Dark** (`#9E9E9E`): Secondary text on dark (works on both modes).

### Semantic
- **Error Red** (`#E2483D`): Form errors, failed payment, removal confirmation.
- **Success Teal** (`#00CCBC`): Order placed, item added confirmation (reuses the brand teal).
- **Warning Gold** (`#FFC100`): "Closing soon", surge messaging (shares the promo gold).
- **Info Teal** (`#00CCBC`): Order-status informational accents.

### Color Usage Table

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#121212` |
| Surface 1 | `#F4F4F2` | `#1C1C1E` |
| Surface 2 | `#EAEAE8` | `#262629` |
| Divider | `#E8E8E6` | `#2C2C2E` |
| Text Primary | `#1D1D1B` | `#F4F4F2` |
| Text Secondary | `#6B6B6B` | `#9E9E9E` |
| Brand / Primary Action | `#00CCBC` | `#00CCBC` |
| On-Teal (labels) | `#003733` | `#003733` |
| Promo (deals) | `#FFC100` | `#FFC100` |
| Error | `#E2483D` | `#E2483D` |

## 3. Typography Rules

### Font Family
- **Brand Face**: Deliveroo's custom geometric grotesque (a heavy, friendly sans in the company's brand system).
- **Recommended Substitute (unlicensed clone)**: `Inter` (by Rasmus Andersson, SIL OFL) — geometric humanist grotesque that holds Deliveroo's heavy-weight, tight-tracking feel at small sizes.
- **System Fallback**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for prices, fees, minimums and quantity steppers (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Inter | 32pt | 900 | 1.15 | -0.6pt | "Home", large nav title |
| Restaurant Hero | Inter | 26pt | 800 | 1.2 | -0.4pt | Restaurant detail header name |
| Section Header | Inter | 22pt | 800 | 1.25 | -0.3pt | "Popular near you" |
| Sub-section | Inter | 20pt | 800 | 1.3 | -0.2pt | "Most ordered", menu category |
| Restaurant Name | Inter | 16pt | 800 | 1.3 | 0pt | Name on restaurant card |
| Menu Item Name | Inter | 15pt | 700 | 1.35 | 0pt | Item title on menu row |
| Body | Inter | 15pt | 500 | 1.45 | 0pt | Item description, paragraphs |
| Price | Inter | 14pt | 800 | 1.3 | 0pt | Prices, totals — tabular |
| Meta | Inter | 14pt | 400 | 1.4 | 0pt | Cuisine · dish · time, muted gray |
| Fee Pill | Inter | 13pt | 700 | 1.3 | 0pt | "£1.49 delivery · Min £12" |
| Badge | Inter | 12pt | 800 | 1.0 | -0.1pt | "PLUS · Free delivery" |
| Button | Inter | 16pt | 800 | 1.0 | 0pt | Primary/secondary button label |
| Tab Label | Inter | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | Inter | 12pt | 600 | 1.35 | 0pt | Timestamps, footnotes |

### Principles
- **Heavy by default**: Deliveroo runs bolder than peers — section headers and names sit at weight 800, screen titles at 900. The weight + the single teal is the brand voice.
- **Tight tracking on big type**: large headings carry negative letter-spacing (-0.3 to -0.6pt) for a confident, compact look.
- **Muted metadata**: cuisine, time and descriptions stay in `#6B6B6B` so the photo, name and teal accents lead.
- **Numbers are bold and tabular**: prices, fees and minimums are weight 800 and tabular — the economic line is firm.
- **Dynamic Type first-class**: screen titles, restaurant names, body and prices scale; badges, fee pill, tab labels and category labels stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Add to basket" / "Add for £9.95")**
- Shape: Fully rounded pill, `999px` corner radius
- Background: `#00CCBC` (Deliveroo Teal) / same on dark
- Text: `#003733` (Teal Ink — NOT white), Inter 16pt weight 800
- Height: 52pt; padding 14pt × 28pt; often full-width with 16pt side insets
- Pressed: background `#00A99C` + scale 0.98
- Disabled: `#00CCBC` at 35% opacity, text 60%

**Dark / Checkout Button ("Checkout · £24.50")**
- Background: `#1D1D1B` light / `#F4F4F2` dark (inverted)
- Text: `#FFFFFF` light / `#121212` dark, Inter 15pt weight 800
- Shape: pill `999px`; padding 13pt × 24pt; embeds the basket total in the label (tabular)

**Outline / Secondary Button ("Group order" / "Filters")**
- Background: transparent
- Border: 1.5pt `#E8E8E6` light / `#2C2C2E` dark
- Text: `#1D1D1B` light / `#F4F4F2` dark, Inter 14pt weight 700
- Shape: pill `999px`; padding 11pt × 20pt
- Pressed: background `#F4F4F2`

**Text Button ("See full menu" / inline link)**
- Text: `#00CCBC` (Deliveroo Teal), Inter 14pt weight 800
- No background, no underline
- Pressed: 60% opacity

**Button State Reference**

| State | Primary (Teal) | Dark (Checkout) | Outline | Text |
|-------|----------------|-----------------|---------|------|
| Default | `#00CCBC` / `#003733` | `#1D1D1B` / white | transparent / `#1D1D1B` | `#00CCBC` |
| Pressed | `#00A99C` + scale 0.98 | darken 8% | bg `#F4F4F2` | 60% opacity |
| Disabled | `#00CCBC` @ 35% / ink 60% | `#1D1D1B` @ 35% | border @ 40% | 40% opacity |
| Loading | `#003733` spinner on teal | white spinner | teal spinner | — |
| Focus (a11y) | 2pt `#00CCBC` ring, 2pt offset | 2pt ring | 2pt `#00CCBC` ring | underline on focus |

Note: the teal "on" content is always Teal Ink `#003733`, never white — the only WCAG-AA-passing pairing on `#00CCBC`.

### Core Atoms

**Restaurant Card**
- Photo: full-width within card, aspect ratio 5:3, corner radius 14pt
- Plus / offer badge: top-left, teal `#00CCBC` fill, `#003733` text, 6pt radius, 12pt 800 ("PLUS · Free delivery"); promo variant uses gold `#FFC100`
- Saved heart: top-right, 32pt circle, `rgba(0,0,0,0.4)` scrim, white outline; fills `#00CCBC` when saved
- Row 1: restaurant name (16pt 800, left) + rating: teal star `#00CCBC` + number 13pt 700 (right)
- Row 2: meta line — "Pizza · Sourdough · 25–35 min" in `#6B6B6B` 14pt 400
- Row 3: **fee pill** — small rounded chip, `#F4F4F2`/`#262629` dark, teal moped icon + "£1.49 delivery · Min £12" 13pt 700
- Card vertical gap: 22pt; sections grouped under 22pt 800 headers; no card border/shadow in light mode

**Category Icon (feed)**
- Tile: 54pt rounded square, 16pt radius, `#F4F4F2` / `#262629` dark background
- Glyph: 24pt teal `#00CCBC` stroked icon
- Active tile: teal `#00CCBC` fill with `#003733` glyph
- Label below: 11pt 600 `#6B6B6B`, active `#1D1D1B`
- Horizontal scroll, 18pt gap

**Menu Item Row (restaurant detail)**
- Layout: text block (left) + 84pt rounded-12pt thumbnail (right)
- Name: 15pt 700 `#1D1D1B`; description: 13pt `#6B6B6B` 2-line clamp; price: 14pt 800 `#1D1D1B`
- Floating `+`: 28pt teal `#00CCBC` circle with `#003733` `+` glyph, offset hanging off the thumbnail's bottom-right corner, soft shadow
- Divider: 1pt `#E8E8E6` between rows

**Fee Pill**
- Inline rounded chip `999px`, `#F4F4F2` / `#262629` dark, padding 5pt × 10pt
- Leading teal moped/scooter icon 12pt + text 13pt 700 `#1D1D1B`

**Quantity Stepper (− n +)**
- Controls: 32pt teal `#00CCBC` circles, `#003733` `−` / `+` glyphs 18pt 800
- Count: 16pt 800 tabular, min-width 18pt centered, 14pt gap between elements
- No container chrome (controls float on white); disabled `−` at quantity 1 fades to 35%

**Plus Pill / Banner**
- Banner: deep-teal panel (`linear-gradient(120deg,#003E3A,#00524B)`), 14pt radius, "deliveroo plus" 13pt 900 teal + body in mint `#C4F4EF` + a teal "Join" pill
- Inline pill: solid teal `#00CCBC`, `#003733` text 12pt 900 — "deliveroo plus · Free delivery"

### Navigation

**Top Address Bar**
- Height: ~44pt + safe area
- Leading: "Deliver to" label 11pt 700 `#6B6B6B` over address value 15pt 800 `#1D1D1B` with a 12pt teal chevron-down
- Trailing: basket button — 36pt teal `#00CCBC` circle with a `#003733` bag glyph

**Search Field**
- Height: 46pt; fully rounded pill `999px`; background `#F4F4F2` / `#1C1C1E` dark
- Leading magnifier 16pt `#6B6B6B`; placeholder "Restaurants, groceries, dishes" 14pt 500 `#6B6B6B`
- Focus: 1.5pt `#00CCBC` ring

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `#FFFFFF` / `rgba(18,18,18,0.94)` blurred dark, 0.5pt top divider
- Tabs (5): Home, Search, Orders, Favourites, Account
- Icon size: 22pt; active color `#00CCBC` (icon fills); inactive `#888`
- Labels: Inter 10pt weight 700, always shown

**Restaurant Detail Top Nav**
- Transparent over the hero photo at the top of scroll; collapses to a solid bar with the restaurant name (compact) once scrolled past the hero
- Leading back chevron; trailing search + share + heart in white circular scrims while transparent

### Input Fields

**Text Field (address, promo code, instructions)**
- Height: 48pt; corner radius 12pt; background `#F4F4F2` / `#1C1C1E` dark
- Border: none default; focus 1.5pt `#00CCBC`
- Label: floating 12pt `#6B6B6B`; input 16pt `#1D1D1B`
- Error: 1.5pt `#E2483D` border + helper text `#E2483D` 12pt

**Search-with-results (restaurant / dish search)**
- Same pill field; results below as rows: leading 44pt rounded-8pt thumbnail, title 15pt 700, meta 13pt `#6B6B6B`
- Pressed row: `#EAEAE8`

### Distinctive Components

**Floating Teal `+` (menu add)**
- 28pt teal `#00CCBC` circle, `#003733` `+` glyph, soft `0 2px 6px rgba(0,0,0,0.18)` shadow
- Hangs off the bottom-right corner of an 84pt menu-item thumbnail
- Tap → adds one and morphs into a quantity stepper inline; the basket bar slides up

**Fee Pill**
- The Deliveroo card signature: a rounded chip showing the delivery fee and order minimum with a leading teal moped icon — appears on every restaurant card

**Deliveroo Plus Banner & Badge**
- Banner: deep-teal gradient panel with the lowercase "deliveroo plus" wordmark, a benefit line in mint, and a teal "Join" pill
- Badge: teal "PLUS · Free delivery" tag on eligible restaurant photos (top-left)

**Sticky Basket Bar**
- Slides up from the bottom once an item is added: teal `#00CCBC` bar, `#003733` text — "View basket · 2 items · £24.50" with the count and total embedded (tabular)
- On the basket screen it becomes the dark "Checkout · £24.50" pill

**Category Icon Row**
- Rounded-square teal-glyph tiles (Restaurants / Grocery / Fast / Treats / Offers); active tile inverts to a teal fill

**Live Order Status (Tracking)**
- Map with the rider marker moving (teal kangaroo/moped marker), an ETA headline, and a 4-step status strip ("Order confirmed → Preparing → Rider on the way → Delivered") with teal progress; calm, single-teal accent

**Item Customize Sheet**
- Bottom sheet, 16pt top radius; dish photo (5:3, 14pt radius) + name 20pt 800 + price 14pt 800 tabular
- Option groups with "Required" / "Choose up to 2" subtitles in `#6B6B6B` 12pt; choice rows with a leading teal `#00CCBC` radio/checkbox when chosen, label 15pt 500, "+ £1.20" delta trailing
- Sticky teal pill at the bottom: "Add to basket · £12.50" in Teal Ink `#003733`, live tabular total

**Group Order Bar**
- A teal-outlined banner at the top of a restaurant menu when a group order is active: avatars of joined members + "{n} people · started by {name}" + a teal "Share link" pill
- Each member's items are attributed under their name in the basket

**Address / Deliver-To Sheet**
- From the top address row: saved addresses as rows (leading teal pin, label 15pt 800, address 13pt `#6B6B6B`, trailing teal radio); "Add a new address" row in `#00CCBC` 15pt 800; a map preview tops the sheet when adding

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 18, 22, 24, 32, 40, 48, 64
- Restaurant card vertical gap: 22pt; section header bottom margin: 14pt
- Screen horizontal inset: 16pt
- Category tile gap: 18pt

### Grid & Container
- iPhone: single-column sectioned card feed at 16pt insets; photos full-width within insets
- iPad: 2-column restaurant grid at 24pt insets, max content width 900pt centered
- Category row and offer carousels: horizontal scroll, content edge-aligned to 16pt
- Menu screen: single-column item rows; sticky teal-underlined category nav under the hero

### Whitespace Philosophy
- **Sectioned breathing room**: 22pt between cards and clear 22pt 800 section headers organize the feed without boxes
- **Quiet chrome, teal punctuation**: the page is white and calm; teal appears only where it means "act" or "brand"
- **Fee pill is the anchor detail**: the delivery-fee chip gives each card a firm economic footing
- **Photo-led**: the 5:3 photo dominates; name + rating + fee pill stack tightly beneath

### Border Radius Scale
- Square (0pt): full-bleed map, dividers
- Subtle (6pt): badges, small thumbnails
- Standard (8pt): list thumbnails
- Card (12–14pt): menu thumbnails (12pt), restaurant photos (14pt), text fields
- Comfortable (16pt): category tiles, bottom sheets, banner, modals
- Pill (999px): buttons, search field, fee pill, basket bar, Plus pill
- Circle (50%): avatars, basket button, stepper controls, floating `+`, map markers

## 6. Depth & Elevation

Deliveroo is mostly flat — separation comes from photography, sectioning and the single teal, with shadow reserved for floating actionable surfaces.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Restaurant cards, category tiles, list rows |
| Raised (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Floating menu `+`, sticky basket bar, deal cards |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -4px 24px` | Item-customize sheet, basket sheet, address sheet |
| Banner | `rgba(0,204,188,0.10) 0 4px 16px` | Deliveroo Plus banner (subtle teal-tinted lift) |
| Modal Scrim | `rgba(0,0,0,0.4)` | Dim behind sheets and modals |

**Shadow Philosophy**: shadow marks "this floats and is tappable" — the floating `+`, the sticky basket bar, the customize sheet. Restaurant cards and category tiles never carry shadow; the photo and the teal carry the screen. On dark mode shadows fade, so floating surfaces add a 0.5pt divider border as the elevation cue.

### Motion
- **Add via floating `+`**: the `+` scales 1 → 0.9 → 1 in 180ms, then morphs into an inline stepper; basket bar slides up 280ms ease-out + soft haptic
- **Quantity stepper**: number cross-fades + 120ms scale tick per tap; soft haptic
- **Basket bar update**: count/total cross-fade with a 200ms ease; the bar pulses subtly on add
- **Card tap → menu**: shared-element hero photo expands into the menu header over a 320ms iOS push
- **Category tile select**: teal fill scales in 150ms ease-out; glyph color flips
- **Search focus**: pill ring fades in 150ms; results list fades/slides up 200ms
- **Order tracking**: rider marker animates along the route; status strip advances with a 250ms ease-out + medium haptic per stage
- **Sheet present**: customize/basket sheets slide up 300ms ease-out; scrim fades in parallel
- **Pull-to-refresh**: teal spinner on the feed
- **Haptics**: light on category select, soft on stepper / add, success notification on order placed, medium per tracking-stage advance

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) as the light canvas; warm near-black `#121212` for dark — never true black
- Use Deliveroo Teal (`#00CCBC`) as the ONE brand color — logo, buttons, ratings, Plus, active tab, basket, floating `+`
- Put `#003733` (Teal Ink) — not white — as the content color on teal fills
- Lead every restaurant card with a wide 5:3 photo in a 14pt rounded container
- Show the **fee pill** (teal moped icon + "£X delivery · Min £Y") on every restaurant card
- Hang a floating teal `+` off the corner of each menu-item thumbnail — the core add gesture
- Use a heavy weight (800) for section headers and restaurant names; 900 for screen titles
- Surface Deliveroo Plus with the same teal (banner + "PLUS" badge) — no separate membership color
- Reserve gold (`#FFC100`) for percentage-off promos only
- Use fully-rounded pill buttons and a pill search field (`999px`)
- Embed the count and total in the sticky basket bar ("View basket · 2 items · £24.50")

### Don't
- Don't introduce a second brand accent — Deliveroo is intentionally mono-teal
- Don't put white text on a teal button — use Teal Ink `#003733`
- Don't crop restaurant photos to squares or tint them — wide 5:3 and full-color
- Don't drop the fee pill — the delivery fee + minimum chip is a Deliveroo card signature
- Don't replace the floating `+` with a full-width "Add" button on menu rows — the corner `+` is the gesture
- Don't use light/thin weights for headers — Deliveroo runs heavy (800/900)
- Don't spread the promo gold across the chrome — it is percentage-deal only
- Don't shadow restaurant cards in light mode — photo + sectioning create separation
- Don't use sharp-cornered buttons — everything actionable is a pill
- Don't dim food photography in dark mode — only chrome darkens
- Don't animate aggressively — motion is functional (`+` morph, basket slide, stage advance)

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Category tiles ~50pt; photo aspect tightens slightly; single column |
| iPhone 13/14/15 | 390pt | Standard layout |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated above address bar |
| iPhone 15/16 Pro Max | 430pt | Larger photos; offer carousel shows ~1.3 cards |
| iPad (portrait) | 768pt | 2-column restaurant grid; address + search in a wider header |
| iPad (landscape) | 1024pt+ | 3-column grid; menu shows category nav + items side-by-side |

### Dynamic Type
- Screen title, restaurant name, body, prices: full scale
- Plus/offer badge, fee pill, tab labels, category labels: FIXED (layout-sensitive)
- Menu item descriptions: scale to XL

### Orientation
- iPhone: portrait-locked on most flows; tracking map may rotate
- iPad: full rotation; menu shows a two-pane (categories + items) layout in landscape

### Touch Targets
- Restaurant card: full-card tap, ≥ 88pt tall
- Floating `+`: 28pt glyph in 44pt hit area
- Stepper controls: 32pt glyph in 44pt hit area
- Category tile: 54pt, full-tile tap
- Tab bar icon: 22pt glyph, 44pt hit
- Basket button: 36pt circle, 44pt hit
- Primary buttons: 52pt tall

### Safe Area Handling
- Top: address bar respects safe area and Dynamic Island
- Bottom: tab bar and sticky basket bar respect the home indicator
- Tracking map: extends under the status bar with a gradient scrim for legibility
- Sides: 16pt content inset throughout

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#121212`
- Surface (light): `#F4F4F2`
- Divider (light): `#E8E8E6`
- Text primary: `#1D1D1B` light / `#F4F4F2` dark
- Text secondary: `#6B6B6B` light / `#9E9E9E` dark
- Deliveroo Teal (the one brand color): `#00CCBC`
- Teal pressed: `#00A99C`
- Teal Ink (on-teal labels): `#003733`
- Plus mint: `#C4F4EF`
- Promo gold (deals only): `#FFC100`
- Error red: `#E2483D`

### Example Component Prompts
- "Build a Deliveroo restaurant card in SwiftUI: full-width 5:3 photo with 14pt corner radius. Top-left a teal `#00CCBC` 'PLUS · Free delivery' badge (Inter 12pt 800, `#003733` text, 6pt radius); top-right a 32pt circular `rgba(0,0,0,0.4)` heart (white outline, fills `#00CCBC` when saved). Below: row with restaurant name (Inter 16pt 800 `#1D1D1B`, leading) and a rating (teal `#00CCBC` star + number Inter 13pt 700, trailing). Next: 'Pizza · Sourdough · 25–35 min' Inter 14pt 400 `#6B6B6B`. Then a fee pill — a `999px` `#F4F4F2` chip, padding 5pt × 10pt, with a leading teal moped icon and '£1.49 delivery · Min £12' Inter 13pt 700. 22pt bottom margin, no card border or shadow."

- "Create a Deliveroo menu item row: a text block (name Inter 15pt 700 `#1D1D1B`; description Inter 13pt `#6B6B6B` 2-line clamp; price Inter 14pt 800 `#1D1D1B`) on the left and an 84pt rounded-12pt thumbnail on the right. Hang a 28pt teal `#00CCBC` circular `+` button off the thumbnail's bottom-right corner (offset so it overhangs), `#003733` `+` glyph, soft `0 2px 6px rgba(0,0,0,0.18)` shadow. On tap it adds one and morphs into an inline quantity stepper; the sticky basket bar slides up. 1pt `#E8E8E6` divider between rows."

- "Build the Deliveroo primary button: full-width pill (`999px`), 52pt tall, background `#00CCBC`, label Inter 16pt 800 in Teal Ink `#003733` (NOT white). Pressed: background `#00A99C` + scale 0.98. Checkout variant is a dark pill: background `#1D1D1B`, white label, embedding the total — 'Checkout · £24.50' (tabular)."

- "Create the Deliveroo sticky basket bar: a full-width teal `#00CCBC` pill that slides up from the bottom when an item is added, 280ms ease-out, with `#003733` text 'View basket · 2 items · £24.50' (count and total tabular). It pulses subtly on each add (scale 1 → 1.03 → 1, 200ms) with a soft haptic."

- "Render the Deliveroo category icon row: rounded-square 54pt tiles, 16pt radius, `#F4F4F2` background, each with a 24pt teal `#00CCBC` stroked glyph and an 11pt 600 `#6B6B6B` label below (Restaurants, Grocery, Fast, Treats, Offers). The active tile inverts to a teal `#00CCBC` fill with a `#003733` glyph and `#1D1D1B` label. Horizontal scroll, 18pt gap, 16pt side insets."

- "Render the Deliveroo bottom tab bar: 5 tabs — Home, Search, Orders, Favourites, Account. 22pt icons, labels Inter 10pt 700 always shown. Active tab tinted `#00CCBC` with filled icon; inactive `#888`. Background `#FFFFFF` light / `rgba(18,18,18,0.94)` blurred dark with a 0.5pt top divider."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) light, warm near-black `#121212` dark — never true black
2. There is ONE brand color: Deliveroo Teal `#00CCBC` — used for logo, buttons, ratings, Plus, tab, basket, `+`
3. On a teal fill the content color is Teal Ink `#003733`, never white — a Deliveroo signature
4. Restaurant photos are wide 5:3 in 14pt rounded containers — full-color, never tinted or squared
5. Every restaurant card shows a fee pill: teal moped icon + "£X delivery · Min £Y" rounded chip
6. Menu rows have a floating teal `+` hanging off the thumbnail corner — the core add gesture
7. Type runs heavy — section headers/names at 800, screen titles at 900, tight negative tracking
8. Deliveroo Plus uses the same teal (banner + "PLUS" badge); gold `#FFC100` is promo-only
9. All buttons and the search field are fully-rounded pills (`999px`); basket bar embeds count + total
10. Cards/tiles have no border/shadow in light mode — photo + sectioning + teal create the separation
