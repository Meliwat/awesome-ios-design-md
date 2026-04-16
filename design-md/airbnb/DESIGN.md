# Design System Inspiration of Airbnb (iOS)

## 1. Visual Theme & Atmosphere

Airbnb's iOS app is an editorial magazine for places to stay. The canvas is white, photography is the hero, and the UI chrome gets out of the way so a Tuscan farmhouse, a Tokyo micro-apartment, or a Joshua Tree dome can carry the full emotional weight of the screen. Where most travel apps feel like a database, Airbnb feels like a coffee-table book you can tap into — generous margins, soft corner radii (16pt on every stay card), and a color vocabulary pulled from hospitality rather than tech. The restraint is deliberate: a host's home is the brand, not Airbnb's logo.

The accent is Primary Coral (`#FF385C`) — the vibrant, slightly pink red Airbnb refreshed into in 2020 for web and marketing. It carries the primary CTAs ("Reserve", "Save", the red price bubbles on the map) and the selected-state underline on the category bar. The heritage Rausch (`#FF5A5F`) — the original brand red — still lives inside the animated Belo logomark and appears on splash moments and illustration art. Teal Babu (`#00A699`) surfaces sparingly on Experiences and Airbnb Plus badges; Arches orange (`#FC642D`) on Trips and certain category icons. Everything else is a careful ladder of warm grays and white — content-first, never decorative.

Typography is Airbnb Cereal, the proprietary face Dalton Maag drew for the brand in 2018 — a warm, slightly humanist geometric sans with six weights (Light, Book, Medium, Bold, Extra Bold, Black). Type hierarchy is editorial: large-title navigation bars at 32pt Extra Bold, stay-card titles at 15pt Medium, host bylines and review counts at 14pt Book in Foggy gray. The tone the face communicates matters as much as the metrics — Cereal is soft, round, and approachable, which is why SF Pro (warm at body sizes) is the correct system fallback.

**Key Characteristics:**
- White canvas (`#FFFFFF`) with Surface Gray (`#F7F7F7`) for section fills and search field backgrounds
- Primary Coral (`#FF385C`) as the main action color; Rausch (`#FF5A5F`) as heritage accent on the Belo logomark
- Photography-first stay cards — 4:3 or 3:2 cover photo, 16pt corner radius, full card width
- Save heart in top-right of every stay card — filled Primary Coral when saved, white w/ black outline when unsaved
- Rating row (star glyph + number + review count in Foggy) under every card, always
- Horizontal category bar at the top of Explore — icon + label chips, underline on the selected category
- Pill-shaped "Where to?" search bar with Location / Check in / Check out / Who segmentation
- Bottom tab bar: Explore / Wishlists / Trips / Inbox / Profile — all labeled, active state in Primary Coral
- Airbnb Cereal at 6 weights; warm, round, and tightly spaced
- Subtle shadows only (`rgba(0,0,0,0.08)`) — the app is content-first, not elevated

## 2. Color Palette & Roles

### Primary
- **Primary Coral** (`#FF385C`): Current primary — Reserve CTA, save-heart fill, category underline, map price bubbles, active tab, link color.
- **Rausch** (`#FF5A5F`): Heritage red — the animated Belo logomark, splash screen, illustration moments.
- **Coral Pressed** (`#E31C5F`): Active/pressed state on Primary Coral CTAs.

### Extended Brand
- **Babu** (`#00A699`): Airbnb Plus badge, Experiences accent, occasional verified markers.
- **Arches** (`#FC642D`): Trips module, specific category icons (e.g., "Amazing views"), host education.
- **Beach** (`#FFB400`): Yellow used on the star glyph in rating rows.

### Canvas, Surfaces & Dividers
- **Canvas White** (`#FFFFFF`): Primary canvas everywhere — cards, sheets, most screens.
- **Surface Gray** (`#F7F7F7`): Section backgrounds, search-field fill, empty-state panels.
- **Surface Gray 2** (`#EBEBEB`): Chip backgrounds, input field fill on forms.
- **Divider** (`#EBEBEB`): Hairline row dividers, card outlines when used.
- **Shadow Black** (`rgba(0,0,0,0.08)`): Standard soft card shadow.

### Text
- **Hof** (`#484848`): Primary text — stay titles, large nav titles, body copy.
- **Foggy** (`#767676`): Secondary text — host bylines, review counts, metadata, strikethrough prices.
- **Foggy Light** (`#B0B0B0`): Tertiary — placeholder text, disabled labels.
- **Ink Black** (`#222222`): Occasional full-black text on hero moments (stay detail title).

### Semantic
- **Success Green** (`#008A05`): Confirmation banners ("Booking confirmed"), Superhost check.
- **Warning Amber** (`#FFB400`): Rating stars, trip alerts.
- **Error Red** (`#C13515`): Form validation errors, failed payments.
- **Info Blue** (`#428BFF`): Rarely used — external links, some info callouts.

### Dark Mode
Airbnb introduced dark mode on iOS in 2020. It is warm-dark, not true black — the canvas preserves photography integrity.
- **Dark Canvas** (`#121212`): Primary dark background.
- **Dark Surface 1** (`#1C1C1E`): Cards, sheets, search field fill.
- **Dark Surface 2** (`#2A2A2A`): Pressed states, chip fills.
- **Dark Divider** (`#2C2C2E`): Hairlines.
- **Dark Text Primary** (`#DDDDDD`): Hof inverted — stay titles, nav titles.
- **Dark Text Secondary** (`#A0A0A0`): Foggy inverted.
- **Primary Coral (dark)** (`#FF385C`): Unchanged — the coral reads well on dark canvas.

## 3. Typography Rules

### Font Family
- **Primary**: `Airbnb Cereal` (proprietary, designed by Dalton Maag, 2018)
- **Weights available**: Light (300), Book (400), Medium (500), Bold (700), Extra Bold (800), Black (900)
- **Fallback stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Helvetica Neue', Arial, sans-serif` — SF Pro is the correct system substitute because Cereal is warm and tightly spaced, not a pure grotesque
- **Web/marketing Google Fonts substitute**: `Plus Jakarta Sans` or `Manrope` — both capture Cereal's warmth

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Nav Title | Cereal | 32pt | 800 (Extra Bold) | 1.15 | -0.4pt | "Explore", "Wishlists", "Trips" |
| Hero Title (Stay Detail) | Cereal | 26pt | 700 (Bold) | 1.2 | -0.3pt | Listing title at the top of a stay |
| Section Header | Cereal | 22pt | 700 | 1.2 | -0.2pt | "Where you'll sleep", "Reviews" |
| Subsection | Cereal | 18pt | 700 | 1.25 | -0.1pt | "Meet your host", "Things to know" |
| Card Title | Cereal | 15pt | 500 (Medium) | 1.3 | -0.05pt | Stay-card headline — city/name |
| Body | Cereal | 16pt | 400 (Book) | 1.4 | 0pt | About-the-host paragraph, descriptions |
| Body Small | Cereal | 14pt | 400 | 1.4 | 0pt | Amenities list items, policy text |
| Metadata | Cereal | 14pt | 400 | 1.3 | 0pt | "2,340 reviews", dates, guest count |
| Rating Number | Cereal | 14pt | 500 | 1.2 | 0pt | "4.92" next to the star glyph |
| Price (Inline) | Cereal | 15pt | 700 | 1.2 | 0pt | "$214 night" with "night" in 400 |
| Price (Hero) | Cereal | 22pt | 800 | 1.1 | -0.2pt | Total price above Reserve CTA |
| Button (Primary) | Cereal | 16pt | 700 | 1.0 | 0pt | "Reserve", "Continue", "Save" |
| Button (Secondary) | Cereal | 14pt | 500 | 1.0 | 0pt | Outline / text buttons |
| Tab Label | Cereal | 10pt | 500 | 1.0 | 0.1pt | Bottom tab bar labels |
| Chip Label | Cereal | 12pt | 500 | 1.0 | 0pt | Category bar chip text |
| Caption | Cereal | 12pt | 400 | 1.3 | 0pt | Photo credits, fine print |

### Principles
- **Weights concentrated at 400 / 500 / 700 / 800**: Book for body, Medium for titles, Bold for section headers, Extra Bold for large nav titles. Light and Black are reserved for marketing moments.
- **Tight but positive tracking at display sizes**: -0.4pt at 32pt falling to 0pt at body.
- **Hof over pure black**: Body text sits at `#484848`, not `#000000` — this is what makes the app feel warm and editorial rather than hard.
- **Dynamic Type respected on body and metadata**: Large nav title, section headers, body, and metadata scale; tab labels, chip labels, and price bubble on the map are fixed.
- **Rating row is its own micro-typography**: 14pt star + 14pt Medium number + 14pt Book "(count)" in Foggy — repeated across the whole app.

## 4. Component Stylings

### Buttons

**Primary Pill CTA (Reserve / Continue / Save)**
- Background: `#FF385C`
- Text: `#FFFFFF`, Cereal 16pt weight 700
- Padding: 14pt vertical, 24pt horizontal
- Corner radius: 8pt (NOT a full pill — Airbnb uses a soft rectangle for the sticky Reserve button; the full-pill version appears on map/chips)
- Height: 48pt minimum
- Pressed: background `#E31C5F`, scale 0.98
- Haptic: `.impactOccurred(.medium)` on Reserve; `.notificationOccurred(.success)` on booking complete

**Full-Pill Filter CTA (Map / Filters / Category actions)**
- Background: `#FFFFFF`
- Border: 1pt solid `#222222`
- Text: `#222222`, Cereal 14pt weight 500
- Padding: 10pt vertical, 18pt horizontal
- Corner radius: 500pt (full pill)
- Shadow: `rgba(0,0,0,0.12) 0 2px 8px`

**Secondary Outline Button**
- Background: transparent
- Border: 1pt solid `#222222`
- Text: `#222222`, Cereal 16pt weight 500
- Padding: 12pt vertical, 20pt horizontal
- Corner radius: 8pt

**Text Link ("Show all 24 photos", "Read more")**
- Cereal 14pt weight 500
- Color: `#222222` with underline
- No background, 44pt hit target
- Used heavily under hero photo grids and in review expansion

**Save Heart (every stay card)**
- Container: 32pt × 32pt, centered in a 44pt tap target, positioned top-right of the cover photo with 12pt inset
- Unsaved state: filled white heart with 1.5pt `#222222` outline; drop shadow `rgba(0,0,0,0.2) 0 2px 4px` for legibility over varied photography
- Saved state: filled `#FF385C`, outline removed
- Animation: scale 1.0 → 1.25 → 1.0 spring bounce on tap, paired with `.impactOccurred(.soft)` haptic

### Cards & Containers

**Stay Card (The Hero Component)**
- Width: full content width (viewport - 32pt horizontal margins)
- Structure (top to bottom): 4:3 photo carousel → 8pt gap → rating row → 2pt → title → 2pt → subtitle → 6pt → date range → 4pt → price row
- Photo: `aspectRatio 4:3`, corner radius 16pt, swipeable carousel with dot indicators bottom-center (6 dots max, white fill for current / `rgba(255,255,255,0.5)` for others)
- Heart: top-right on photo with 12pt inset
- Rating row: star glyph `#484848` + "4.92 (324)" in `#484848` Cereal 14pt Medium + Book
- Title: "Private room in Reykjavik" in Cereal 15pt Medium `#484848`, 1-line truncate
- Subtitle: "Hosted by Sigrún · Superhost" in Cereal 14pt Book `#767676`, 1-line truncate
- Dates: "Oct 12 – 17" in Cereal 14pt Book `#767676`
- Price row: "$214 night" — "$214" Cereal 15pt Bold `#484848`, "night" Cereal 15pt Book `#484848`; on sale, old price in `#767676` with strikethrough before the new price
- Tap: scale 0.98 briefly; no elevation change — photos are already visually lifted by their 16pt radius

**Rating Row Primitive**
- Star: `star.fill` SF Symbol, 12pt, color `#484848` (NOT yellow — Airbnb renders the star dark gray on cards, reserving yellow for the review-detail screens)
- Number: Cereal 14pt weight 500 `#484848`
- Review count: " · 2,340" in Cereal 14pt weight 400 `#767676` (leading middle dot with thin space)
- Spacing: 4pt between star and number, inline with middle dot + count

**Category Chip (top-of-Explore bar)**
- Height: 56pt (icon stacked above label)
- Icon: 24pt SF Symbol or custom glyph, `#717171` default, `#222222` when selected
- Label: Cereal 12pt weight 500, `#717171` default, `#222222` selected
- Selected state: 2pt `#222222` underline, full-width of the chip, 8pt below the label
- Spacing: 32pt between chips
- Horizontal scroll, 16pt leading/trailing insets, last chip peeks off the right edge

**Search Pill (Explore top)**
- Height: 56pt
- Background: `#FFFFFF` on a 20pt shadow zone above the category bar
- Border: 0.5pt `#EBEBEB`
- Corner radius: 500pt (full pill, 28pt effective)
- Shadow: `rgba(0,0,0,0.08) 0 4px 12px`, `rgba(0,0,0,0.04) 0 1px 2px`
- Content (horizontal): `magnifyingglass` 16pt `#222222` → "Where to?" Cereal 14pt weight 700 `#222222` → vertical divider → "Anywhere" Cereal 12pt Book `#767676` → divider → "Any week" → divider → "Add guests" → filter icon on far right
- Collapsed variant (after scroll): just "Where to?" + "Anywhere · Any week · Add guests" in 12pt Book

**Stay Detail Photo Grid**
- Top-of-screen grid: 1 large hero on the left (2/3 width, full height), 4 smaller thumbnails stacked on the right in a 2×2 grid
- Total height: ~320pt on iPhone
- Corner radius: 16pt on the outer frame; 0pt on internal photos (the frame clips)
- "Show all photos" pill overlay: bottom-right corner, 8pt inset, `#FFFFFF` background, 1pt `#222222` border, 500pt radius, Cereal 14pt weight 500 with `square.grid.2x2` icon

**Map Price Bubble**
- Shape: rounded rectangle, corner radius 12pt (NOT a full pill — Airbnb's map pins are slightly rounded rectangles)
- Background: `#FFFFFF` default, `#222222` when visited, `#FF385C` when selected
- Text: Cereal 14pt weight 700 — `$214` or the nightly price
- Shadow: `rgba(0,0,0,0.2) 0 2px 6px`
- Padding: 6pt vertical, 10pt horizontal
- Heart indicator: tiny 10pt heart inset top-right if saved

### Navigation

**Large-Title Nav Bar (Explore / Wishlists / Trips / Inbox / Profile)**
- Height: variable — 96pt when scrolled to top (large title), collapses to 44pt (inline title) on scroll
- Background: `#FFFFFF` (collapses to `.regularMaterial` blur when content scrolls under)
- Title: Cereal 32pt Extra Bold `#222222`, left-aligned, 16pt leading margin
- Trailing icon: 24pt avatar or action button
- Below title: metadata strip ("3 upcoming trips", "12 new messages") in Cereal 14pt Book `#767676`

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `#FFFFFF` with 0.5pt `#EBEBEB` top divider; semi-transparent `.regularMaterial` blur when content scrolls beneath
- Tabs: Explore, Wishlists, Trips, Inbox, Profile (all 5 always labeled)
- Icon size: 24pt, outlined SF Symbol default / filled when active
- Active color: `#FF385C` (icon + label)
- Inactive: `#717171`
- Labels: Cereal 10pt weight 500, visible always
- Tap haptic: `.selectionChanged()` on each switch

**Category Bar (below Explore large title)**
- Fixed position while scrolling within Explore
- Height: 72pt (56pt chip + 16pt bottom padding + underline space)
- Background: `#FFFFFF` with 0.5pt `#EBEBEB` bottom divider; slides up on scroll with the collapsing large title

**Sticky Booking Footer (Stay Detail bottom)**
- Height: 80pt + safe area
- Background: `#FFFFFF` with a 0.5pt `#EBEBEB` top divider, `.regularMaterial` blur above content
- Left: total price in Cereal 16pt Bold `#222222` + "total" + date range link in Cereal 14pt Book underline
- Right: "Reserve" pill button, 48pt height, Primary Coral
- The footer is persistent and translucent — always visible on scroll

### Input Fields

**Text Field (Payment / Contact forms)**
- Background: `#FFFFFF`
- Border: 1pt `#B0B0B0` default, 2pt `#222222` focused
- Corner radius: 8pt
- Height: 56pt
- Label: floating — sits inside at 16pt Book `#767676` when empty; animates to 12pt Medium `#222222` at the top-left corner when focused or filled
- Text: Cereal 16pt Book `#222222`

**Search Input (inline, "Where to?" modal)**
- On tap, the pill expands into a full-screen modal with a vertical list of "Location / Check in / Check out / Who" as stacked cards
- Each card: 80pt tall, white background, 16pt corner radius, label at top, value below, selected card has 1pt `#222222` outline

**Date Range Picker (Calendar)**
- Two-month view, scrollable vertically, months stacked
- Month label: Cereal 18pt Bold `#484848`, left-aligned
- Weekday row: Cereal 12pt weight 500 `#767676`, centered
- Date cells: 40pt × 40pt circle when selected
- Start/end date: solid `#222222` circle, white number
- Range (between start and end): `#F7F7F7` rounded-rect fill spanning the cells
- Today: 1pt outline, no fill
- Unavailable: strikethrough with `#B0B0B0`

### Distinctive Components

**Belo Logomark**
- The official symbol — a heart-house-pin composite — in Rausch (`#FF5A5F`), NOT Primary Coral
- Used on the splash screen, inside "About" modals, and occasionally as a loading indicator with a subtle 1200ms pulse
- Never render Belo in any color other than Rausch `#FF5A5F` or white on dark

**Host Badge Row**
- 48pt circular host avatar + "Hosted by [Name]" Cereal 16pt Medium + "Superhost · 3 years hosting" in Cereal 14pt Book `#767676`
- Appears on every stay detail; tap opens host profile sheet

**Amenities List**
- Column of rows, each: 24pt SF Symbol glyph (e.g., `wifi`, `tv.fill`, `parkingsign.circle`) + Cereal 16pt Book `#484848` label
- Unavailable amenities: glyph and text strikethrough, `#B0B0B0` color
- "Show all 24 amenities" underlined button at bottom

**Illustration Moments**
- Empty states ("No trips yet", "No messages"), onboarding, and host education use Airbnb's proprietary illustration style — soft, slightly hand-drawn vectors with a pastel palette, often a human figure doing something hospitable
- Illustrations are treated as hero elements, usually 180-240pt square, centered, with supporting copy 16pt below

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64, 80
- Standard horizontal margin: 24pt on stay detail, 16pt on Explore/lists
- Section vertical gap: 40pt between major sections on stay detail; 24pt between stacked stay cards

### Grid & Container
- Content width: full device width minus 16-24pt horizontal margins
- Stay cards: always full width of content area, stacked vertically (no 2-col grid in Explore — one card at a time with hero photo)
- Wishlist grid: 2-col square grid, 8pt gap
- Category chips: horizontal scroll, no column constraint

### Whitespace Philosophy
- **Generous vertical breathing**: 24pt between stacked stay cards, 40pt between sections on detail
- **Photography gets the margins**: the 4:3 cover photo sets the rhythm — white space around each card is essentially as tall as the photo is wide divided by ~8
- **Sticky chrome compresses the content**: category bar + tab bar means the effective scroll viewport is ~70% — plan card heights accordingly

### Border Radius Scale
- Square (0pt): rare — used only on strikethrough prices inline
- Small (4pt): badges, tiny pills
- Standard (8pt): text fields, Reserve CTA, sheet containers
- Soft (12pt): chip fills on search modal, map price bubbles
- Medium (16pt): stay-card photos, photo grid outer frame — the signature Airbnb radius
- Comfortable (24pt): modal sheet top corners
- Full Pill (500pt): category chips, search pill, filter buttons
- Circle (50%): avatars, date-picker selected cells, save-heart container

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, section backgrounds, canvas |
| Card (Level 1) | `rgba(0,0,0,0.08) 0 2px 8px` | Stay cards rarely — Airbnb relies on the photo radius for lift, shadows are subtle |
| Floating (Level 2) | `rgba(0,0,0,0.12) 0 4px 12px` | Search pill, filter button, map price bubble |
| Overlay (Level 3) | `rgba(0,0,0,0.16) 0 8px 24px` | Host profile sheet, photo lightbox, filter modal |
| Sheet (Level 4) | `rgba(0,0,0,0.24) 0 -12px 32px` | Bottom sheets, booking confirmation |
| Blur Material | `.regularMaterial` over `#FFFFFF` at 92% | Sticky booking footer, scrolled nav bar, tab bar |

**Shadow Philosophy**: Airbnb is a content-first, clean app — shadows are soft, low-opacity (0.08-0.16 max), and short (2-12pt blur). The 16pt corner radius on stay-card photos does most of the visual lifting; shadows are there to separate sticky chrome from content beneath, not to make cards "pop". Compared to a fintech or food-delivery app, Airbnb's elevations are whispered.

### Motion
- **Save heart tap**: scale 1.0 → 1.25 → 1.0 over 300ms spring (damping 0.6), paired with `.impactOccurred(.soft)` haptic
- **Photo carousel swipe**: 60fps horizontal paging with rubber-band at edges; dot indicators cross-fade
- **Category bar switch**: underline slides to the new chip via `matchedGeometryEffect` in 250ms spring
- **Search pill expand**: shared-element 400ms spring — pill morphs into the full-screen search modal, content cross-fades
- **Reserve tap**: scale 0.98 instant, then `.impactOccurred(.medium)` haptic; on success, `.notificationOccurred(.success)`
- **Map pin tap**: selected pin scales 1.0 → 1.1, color transitions `#FFFFFF` → `#FF385C`, background stay card slides up from the bottom
- **Belo loading**: slow pulse 1200ms opacity 0.6 → 1.0 → 0.6, Rausch `#FF5A5F`

## 7. Do's and Don'ts

### Do
- Use `#FF385C` as the primary action color for Reserve, save-heart fill, category underline, and active tab
- Use `#FF5A5F` (Rausch) for the Belo logomark and illustration moments — it's the heritage red that still appears
- Render stay-card photos at 4:3 with 16pt corner radius — this is the single most recognizable component in the app
- Place the save-heart top-right on every stay card, with a subtle drop shadow for legibility over varied photography
- Use Hof (`#484848`) for primary text, not `#000000` — Airbnb is warm, not hard
- Use Foggy (`#767676`) for the rating row review count, host byline, dates, and metadata
- Keep the category bar pinned under the large nav title on Explore
- Let photography set the chromatic mood — the UI chrome should recede
- Keep shadows soft (0.08 opacity) and short (2-8pt blur)
- Use Cereal weights 400/500/700/800 for almost everything
- Use SF Pro Display/Text as the fallback when Cereal isn't available — it's the warmest system face

### Don't
- Don't use pure black `#000000` for body text — `#484848` (Hof) is the correct value
- Don't render stay-card photos with sharp corners or rounded corners smaller than 16pt
- Don't use Primary Coral for body text or section headers — it's a verb, not a color
- Don't replace the Belo logomark's Rausch red with Primary Coral — they are intentionally different
- Don't use aggressive shadows — stay cards are lifted by their radius, not their drop
- Don't skip the rating row on a stay card — it's part of the card's identity
- Don't use a 2-column grid for stay cards in Explore — one full-width card at a time is the Airbnb pattern
- Don't use yellow for the rating-row star on cards — it's `#484848` dark gray on cards; yellow appears only on review-detail screens
- Don't hide the sticky Reserve footer on stay detail — it's persistent throughout the scroll
- Don't use Arches orange or Babu teal as primary UI accents — they are reserved for Trips and Experiences/Plus respectively

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Stay-card photo aspect shifts 4:3 → 3:2 for compactness |
| iPhone 13/14/15 | 390pt | Standard 4:3 photos, 24pt horizontal margins on detail |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected in large-title nav |
| iPhone 15/16 Pro Max | 430pt | Larger 4:3 photos, category bar shows one additional chip |
| iPad | 768pt+ | 2-col stay-card grid, photo grid uses 3×3 layout, sticky booking moves to right sidebar |

### Dynamic Type
- Large nav titles, section headers, body, metadata: full scale
- Rating numbers, category chip labels, tab labels: fixed (layout-sensitive)
- Price bubble on map: fixed size
- Stay-card title: scales but clamps at 18pt max

### Orientation
- Most screens: portrait-locked
- Photo lightbox (viewing all photos): rotates to landscape, full-bleed
- Map view: rotates

### Touch Targets
- Save heart: 44pt tap area on a 32pt glyph container
- Category chip: full-height 56pt, 44pt minimum width
- Tab bar icons: 24pt icon, full 56pt row
- Reserve CTA: 48pt height, full width minus 24pt margins

### Safe Area Handling
- Top: safe area honored — large nav title starts at safe-area top + 16pt
- Bottom: sticky Reserve footer respects home indicator; tab bar pins above home indicator on other screens
- Sides: 16-24pt content insets depending on screen

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface Gray: `#F7F7F7`
- Divider: `#EBEBEB`
- Hof (primary text): `#484848`
- Foggy (secondary text): `#767676`
- Primary Coral (current): `#FF385C`
- Coral Pressed: `#E31C5F`
- Rausch (heritage): `#FF5A5F`
- Babu (teal): `#00A699`
- Arches (orange): `#FC642D`
- Star yellow (on review screens only): `#FFB400`
- Error red: `#C13515`

### Example Component Prompts
- "Create a SwiftUI Airbnb stay card: full-width 4:3 photo with 16pt corner radius and a save-heart in the top-right (white-filled with black outline, shadow for legibility). Below the photo: 8pt gap, then a rating row with a 12pt star.fill in #484848 + '4.92' in Airbnb Cereal 14pt weight 500 + ' · 324' in Cereal 14pt weight 400 #767676. Then title 'Private room in Reykjavik' in Cereal 15pt weight 500 #484848, subtitle 'Hosted by Sigrún · Superhost' in Cereal 14pt weight 400 #767676, dates 'Oct 12 – 17' in Cereal 14pt weight 400 #767676, and price '$214 night' with the number in Cereal 15pt weight 700 #484848."
- "Build the Explore search pill: full-width minus 32pt margins, 56pt tall, white background with a 0.5pt #EBEBEB border and rgba(0,0,0,0.08) 0 4px 12px shadow. Leading 16pt magnifyingglass SF Symbol, then 'Where to?' in Cereal 14pt weight 700, then a vertical divider, then 'Anywhere · Any week · Add guests' in Cereal 12pt weight 400 #767676. Corner radius 500pt (full pill). Tap opens the search modal with a shared-element spring transition."
- "Design the category bar: horizontal scroll row pinned under the large nav title. Each chip is 56pt tall with a 24pt icon stacked above a 12pt weight 500 label. Default color #717171, selected color #222222. Selected chip has a 2pt #222222 underline 8pt below the label. 32pt spacing between chips, 16pt leading inset. Icons come from SF Symbols (flame, house.fill, tree.fill, etc.)."
- "Build the sticky booking footer on a stay-detail screen: 80pt tall plus safe area, white background with a 0.5pt #EBEBEB top divider and .regularMaterial blur. Left side: '$1,284 total' in Cereal 16pt weight 700 #222222 above a 'Oct 12 – 17' link in Cereal 14pt weight 400 #222222 with underline. Right side: 'Reserve' pill button, 48pt height, #FF385C background, Cereal 16pt weight 700 white text, 8pt corner radius. Haptic: .impactOccurred(.medium) on tap."
- "Create a map price bubble: rounded rectangle with 12pt corner radius, white background, 6pt vertical and 10pt horizontal padding. Text is '$214' in Cereal 14pt weight 700 #222222. Shadow: rgba(0,0,0,0.2) 0 2px 6px. Selected state: background turns #FF385C and text turns white. Visited state: background turns #222222 and text stays white."

### Iteration Guide
1. Canvas is `#FFFFFF`; use Surface Gray `#F7F7F7` only for search-field fill and section backgrounds
2. Primary action is `#FF385C` — Reserve CTA, save-heart fill, category underline, active tab
3. Rausch `#FF5A5F` is heritage — keep it on the Belo logomark and illustration moments, don't use it on modern CTAs
4. Body text is Hof `#484848`, NOT `#000000` — this is what makes the app feel warm
5. Stay-card photos are 4:3 with a 16pt corner radius — the single most signature visual in the app
6. Save heart sits top-right of every stay card with a drop shadow for legibility
7. Cereal at weights 400/500/700/800 for 95% of type; SF Pro is the correct system fallback
8. Shadows are subtle (0.08-0.16 opacity) — Airbnb is content-first, not elevated
9. Keep photography as the hero — UI chrome should always recede
10. Dark mode is warm `#121212` canvas with `#DDDDDD` primary text; Primary Coral stays identical
