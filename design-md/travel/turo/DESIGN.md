# Design System Inspiration of Turo (iOS)

## 1. Visual Theme & Atmosphere

Turo's iOS app is **Airbnb-for-cars**, and like Airbnb, its design philosophy is that **the photo is the product**. Open any listing and the first thing you see is a full-bleed, edge-to-edge vehicle photo carousel — no card frame, no border, no chrome obscuring it. A 2023 Tesla Model 3 in a sunlit driveway, a Porsche 911 on a coastal road, a Jeep Wrangler at a trailhead: the imagery does the selling, and the entire UI is engineered to get out of its way. Turo is a peer-to-peer car-sharing marketplace — real people (hosts) listing their personal cars to real people (guests) — so trust signals (host profile, All-Star badge, trip count, response time, reviews) are woven through every screen with the same care a hotel app gives star ratings.

The brand is anchored by **Turo Purple** (`#593BFB`), a confident electric violet that is the single booking-action color — "Continue", "Book this car", the selected map pin, the active filter. It is Turo's "Spotify Green": when you see purple, it means *commit*. The counterweight is **Turo Teal** (`#5CE0B8`), a fresh mint that signals **trust and value** — the All-Star Host badge, savings callouts, "Free cancellation", instant-book lightning, the favorite heart. Purple drives the transaction; teal reassures you it's a good, safe one. Together they read as modern, optimistic, and a little premium — Turo is not a budget rental counter, it's a marketplace of interesting cars.

The signature interaction is the **listing detail → booking flow**: a tall photo hero with a paginated carousel, the car's year/make/model in heavy type, a star rating with trip count, a horizontal **specs strip** (fuel type, seats, transmission, doors), a prominent **host card** (avatar, name, All-Star badge, trips, response time), the trip dates/location, and — pinned to the bottom always — a **sticky price + Book bar** showing the daily rate and an estimated total with a single full-width purple CTA. The other signature surface is the **map + photo-card search**: results render as photo-led cards in a list that's twinned with a map of purple price pins, exactly like Airbnb's stays.

Typography is **Turo's brand sans** (a geometric humanist face) with **Manrope** as the open-source fallback for implementation — clean, slightly rounded, excellent at large display sizes for car names and prices. Hierarchy is photo-deferential: titles and prices are heavy (800), but body copy is calm and readable, never competing with the imagery. The dark theme (documented here as the primary catalog rendering) drops to a near-black `#0F0F12` specifically so full-bleed photography reads with maximum punch — the canvas disappears and the cars glow.

**Key Characteristics:**
- **The car photo is the product** — full-bleed, edge-to-edge photo carousel, no card frame on the hero
- **Listing detail → sticky Book bar** — the signature flow: photo hero → specs → host → pinned price + purple CTA
- **Turo Purple `#593BFB`** is the single booking-action color — "Continue", "Book", selected pin, active filter
- **Turo Teal `#5CE0B8`** is the trust/value accent — All-Star Host badge, savings, free cancellation, instant book
- **Host-centric trust** — avatar, All-Star badge, trip count, response time, reviews on every listing
- **Photo-card search twinned with a map** — list of photo-led cards + map of purple price pins (Airbnb-style)
- **Specs strip** — fuel / seats / transmission / doors as compact teal-iconed tiles
- **Near-black canvas (`#0F0F12`)** dark so photography pops; photos never dimmed, only chrome
- **Generously rounded surfaces** (10–14pt cards, 19px avatars) — modern, premium, friendly
- **Bottom tab bar** — Search / Trips / Favorites / Inbox / More — purple active tint

## 2. Color Palette & Roles

### Primary (Interactive)
- **Turo Purple** (`#593BFB`): THE booking-action color — primary CTA fill ("Continue", "Book this car"), selected map pin, active filter chip, active tab. The brand-defining accent.
- **Purple Bright** (`#7C5CFF`): Inline link / interactive text — lifts on the dark canvas for WCAG AA; also the lighter end of purple gradients.
- **Purple Pressed** (`#4A2FD6`): Pressed state of the primary CTA.

### Secondary / Trust
- **Turo Teal** (`#5CE0B8`): Trust & value accent — All-Star Host badge, savings/discount callouts, "Free cancellation", instant-book lightning, favorite-saved heart, success.
- **Teal Deep** (`#1FB890`): Teal text on light backgrounds, pressed teal, success-state contrast.

### Canvas & Surfaces (Light Mode)
- **Canvas** (`#FFFFFF`): Primary light canvas.
- **Surface 1** (`#F6F5FA`): Grouped backdrop behind cards, subtle elevated surfaces.
- **Surface 2** (`#FFFFFF`): Card and sheet fills (lifted with a soft shadow above Surface 1).
- **Surface Pressed** (`#ECEAF4`): Pressed list rows.
- **Divider** (`#E6E4EF`): Hairline dividers.

### Canvas & Surfaces (Dark Mode)
- **Canvas** (`#0F0F12`): Primary dark canvas — near-black so photography pops; NOT a tinted gray.
- **Dark Surface 1** (`#1A1A1F`): Card and sheet fills.
- **Dark Surface 2** (`#25252C`): Raised tiles, spec tiles, segmented controls.
- **Dark Divider** (`#2D2D35`): Hairline dividers on dark.

### Text
- **Text Primary Light** (`#15131F`): Primary text on light — a deep ink, NOT pure black.
- **Text Secondary Light** (`#6B6878`): Metadata, captions.
- **Text Tertiary Light** (`#9C99AB`): Disabled, placeholder.
- **Text Primary Dark** (`#F2F1F6`): Primary text on the near-black canvas.
- **Text Secondary Dark** (`#A4A2B3`): Metadata on dark.
- **Text Tertiary Dark** (`#6F6D7E`): Disabled / inactive on dark.
- **Text On Purple** (`#FFFFFF`): Text on the purple CTA.
- **Text On Teal** (`#06231B`): Text on teal fills — deep green-black for AA contrast.

### Semantic

| Role | Light | Dark |
|------|-------|------|
| Success / Confirmed | `#1FB890` | `#5CE0B8` |
| Warning / Pending | `#E8920C` | `#F4A93A` |
| Error / Declined | `#E03A52` | `#F5536B` |
| Info | `#593BFB` | `#7C5CFF` |
| Star Rating | `#FFB400` | `#FFB400` |
| Savings / Discount | `#1FB890` | `#5CE0B8` |
| Price (primary) | `#15131F` | `#F2F1F6` |

### Semantic Notes
- **Purple is the transaction**: there is one primary purple CTA per screen — the booking action. Filter/selected states also use purple, but the Book bar's button is the loudest purple on screen.
- **Teal is the reassurance**: it never drives the main transaction; it confirms the choice is safe and good (All-Star, free cancellation, savings, success). It is also the favorite-saved heart.
- **Photos are sacred**: imagery is never tinted, dimmed, or overlaid with brand color blocks — gradients on photos are only for legibility scrims at the very top/bottom.
- **Star gold is reserved**: `#FFB400` only for the rating star — never a CTA or decoration.

## 3. Typography Rules

### Font Family
- **Brand Font**: Turo's brand sans (a geometric humanist face used across product and marketing) — clean, slightly rounded terminals, strong at large display sizes for car names and prices.
- **Implementation Fallback**: `Manrope` (by Mikhail Sharanda, SIL OFL) — closest free geometric-humanist sans with a 200–800 range and tight, modern numerals; use for clones.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: tabular lining figures for prices, totals, trip counts, and ratings so layout never shifts.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Manrope | 32pt | 800 | 1.15 | -0.4pt | "Find your drive", search hero |
| Price Hero | Manrope | 26pt | 800 | 1.1 | -0.3pt | "$83 / day" in the Book bar |
| Car Title | Manrope | 23pt | 800 | 1.2 | -0.3pt | "Tesla Model 3 · 2023" on the listing |
| Section Header | Manrope | 18pt | 700 | 1.3 | -0.1pt | "Meet your host", "Trip details" |
| Body | Manrope | 16pt | 400 | 1.5 | 0pt | Description, policies, value props |
| Body Emphasis | Manrope | 16pt | 600 | 1.5 | 0pt | Inline emphasis |
| Card Title | Manrope | 15pt | 600 | 1.35 | 0pt | Search-card car title, host name |
| Price (card) | Manrope | 14pt | 800 | 1.2 | 0pt | "$214 / day" on a search card |
| Meta | Manrope | 14pt | 400 | 1.4 | 0pt | "214 trips · Joined 2019" |
| Eyebrow / Badge | Manrope | 13pt | 700 | 1.2 | 0.5pt | "ALL-STAR HOST" — uppercase, tracked |
| Caption | Manrope | 12pt | 500 | 1.3 | 0pt | "$249 est. total · 3 days" |
| Button | Manrope | 16pt | 800 | 1.0 | 0.1pt | Primary/secondary button labels |
| Tab Label | Manrope | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Rating Number | Manrope | 13pt | 700 | 1.0 | 0pt | "4.97" beside the star |

### Principles
- **Photo-deferential hierarchy**: type is confident but never competes with the car photo — the image is always the loudest element above the fold; type leads once you scroll past the hero
- **Prices are heavy**: daily rate, estimated total, and savings are 800 weight with tabular numerals so they read instantly and never jitter
- **Trust copy is calm**: host details, policies, and reviews are 400–600 weight, readable, never shouty — trust is conveyed by clarity, not emphasis
- **Tracked uppercase badges**: All-Star Host, Instant Book, and similar badges are 13pt 700 uppercase with +0.5pt tracking
- **Dynamic Type**: titles, body, card titles, prices scale; tab labels, badge eyebrows, and the rating number stay fixed (layout-critical)

## 4. Component Stylings

### Buttons

**Primary Button (Continue / Book this car)**
- Shape: rounded rectangle, 10pt corner radius, full-width in the Book bar
- Background: `#593BFB` (Turo Purple) — the booking-action color
- Text: `#FFFFFF` Manrope 16pt weight 800
- Height: 52pt (44pt in the compact Book bar)
- Pressed: background `#4A2FD6` + scale 0.98
- Disabled: `#593BFB` at 40% opacity, text at 55%

**Teal Button (Book instantly / accept)**
- Background: `#5CE0B8` (Turo Teal)
- Text: `#06231B` Manrope 16pt weight 800
- Height: 52pt, 10pt corner radius
- Used for instant-book and host-accept actions where teal's "trusted/fast" meaning applies

**Ghost / Outline Button (Message host)**
- Background: transparent
- Border: 1.5pt `#7C5CFF`
- Text: `#7C5CFF` Manrope 14pt weight 700
- Height: 44pt, 10pt corner radius
- Pressed: background `#7C5CFF` at 8% opacity

**Text Button (See all 32 photos)**
- Manrope 14pt weight 700, color `#7C5CFF`
- No underline; pressed: 60% opacity

### Photo Hero (the signature atom)
- Full-bleed, edge-to-edge, ~260pt tall on the listing detail (no card frame, no inset)
- Paginated carousel: page dots at the bottom-center; active dot is an 18pt-wide pill `#FFFFFF`, inactive 6pt `rgba(255,255,255,0.45)`
- Top overlay controls: back, share, favorite — each a 38pt circular `rgba(15,15,18,0.55)` blurred button with white 2.2pt-stroke icon
- Optional legibility scrim: a subtle top gradient only behind the status bar / controls — never over the car body
- Favorite heart: outline white when unsaved, filled `#5CE0B8` (Turo Teal) when saved

### Specs Strip
- A horizontal row of 3–4 compact tiles directly under the title
- Each tile: 12pt corner radius, `Surface 1` fill, 1pt divider border
- Leading icon 18pt stroke `#5CE0B8` (Turo Teal)
- Value 13pt weight 700 primary ("Electric", "5 seats", "Auto"); label 10pt secondary ("Fuel", "Capacity", "Transmission")

### Host Card (the trust atom)
- Row card: 14pt corner radius, `Surface 1`, 1pt divider border, full-width
- Leading 46pt circular avatar (photo or purple→teal gradient with initials)
- Name 15pt weight 700 + inline All-Star badge
- All-Star badge: pill, `rgba(92,224,184,0.16)` fill, `#5CE0B8` text + leading star, 10pt weight 800 uppercase
- Sub 12pt secondary: "214 trips · Joined 2019 · Responds in ~10 min"
- Trailing chevron `›` tertiary — taps through to the full host profile

### Sticky Price + Book Bar (the conversion atom)
- Pinned to the bottom, always visible on the listing
- Background: blurred `rgba(15,15,18,0.96)` panel (light: `rgba(255,255,255,0.96)`), 0.5pt top divider
- Left: daily rate "$83" 20pt weight 800 + "/ day" 13pt secondary; below, "$249 est. total · 3 days" 11pt `#5CE0B8` underlined (tap → price breakdown sheet)
- Right: full-width-ish purple "Continue" CTA, 10pt radius, 14pt weight 800
- This bar never scrolls away — it is the conversion anchor

### Search / Listing Cards
- Photo-led card, 14pt corner radius, `Surface 2`, 1pt divider border
- Photo on top (~110pt), favorite heart top-right (white outline / teal filled)
- Body: car title 13pt weight 700; rating row "4.99 (87) · 156 trips" 11pt secondary with a 11pt gold star; price "$214 / day" 14pt weight 800
- Twinned with a map of price pins in the search experience

### Map Price Pins
- Pill, 500pt radius, soft shadow
- Default: `#593BFB` (purple) fill, white text
- Visited: `Surface 2` fill, secondary text, 1pt divider border
- Selected: `#FFFFFF` fill, `#593BFB` text — the inverse, drawing the eye to the focused car

### Navigation

**Bottom Tab Bar**
- Height: 68pt total (50pt + safe area)
- Background: `#FFFFFF` light / `#0F0F12` dark, 0.5pt top divider, blur on scroll
- Tabs (5): Search, Trips, Favorites, Inbox, More
- Icon 22pt; active fill, inactive stroke
- Active color: `#593BFB` (Turo Purple) — icon + label tint, no pill
- Inactive: `#9C99AB` light / `#6F6D7E` dark
- Labels: Manrope 10pt weight 600, always shown

**Top Bar (over photo hero)**
- Transparent over the photo; back/share/favorite are floating circular blurred buttons
- On a non-photo screen: solid canvas, title 23–32pt 800, back chevron leading

**Filter Bar (search)**
- Horizontal scroll of pill chips below the search field
- Default chip: `Surface 2`, 1pt divider, label 13pt 600
- Active chip: `#593BFB` fill, white text

### Input Fields

**Search Field (Where to? · When?)**
- Height: 52pt
- Background: `#FFFFFF` light (1pt `#E6E4EF`) / `#25252C` dark
- Corner radius: 12pt
- Leading 18pt search icon secondary; "Where to? · Add dates" placeholder 16pt 500 secondary
- Focus: 2pt `#593BFB` border

**Date / Trip Picker**
- Calendar grid; selected endpoints `#593BFB` filled circle white text; in-between days `#593BFB` at 12% tint
- Today: 1.5pt `#5CE0B8` ring

### Distinctive Components

**Photo Carousel with Page Dots**
- Swipeable full-bleed photos; the pill-style active dot is the signature pagination affordance

**All-Star Host Badge**
- Turo's trust hallmark — a teal star pill on the host card and search cards; the brand's "verified/excellent" signal

**Instant Book Lightning**
- A teal lightning glyph + "Book instantly" — cars bookable without host approval; teal because it's the fast, trusted path

**Trip Card (Trips tab)**
- Upcoming/past trips as photo cards with status pill: Confirmed (teal), Pending (amber), Completed (secondary)

**Price Breakdown Sheet**
- Bottom sheet from the "est. total" link: line items (daily rate × days, trip fee, young-driver fee, taxes) with a bold total — purple "Continue" pinned

**Host Profile**
- Full-screen: large avatar, All-Star badge, joined date, trips, response rate/time, reviews list, their other cars in a photo-card rail

**Specs / Features Grid**
- Below the specs strip: a grid of feature chips (Apple CarPlay, All-wheel drive, Pet friendly, etc.) with small teal-tinted icons

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt (the photo hero is the exception — full-bleed, 0 inset)
- Card internal padding: 12–16pt
- Gap between stacked sections: 18–20pt
- The photo hero gets the most vertical real estate above the fold; the Book bar is always pinned

### Grid & Container
- iPhone: single column, 20pt insets, full-bleed photo hero, pinned Book bar
- iPad / large: search shows a 2-up — photo-card list beside a live map; listing detail caps content at ~640pt with the photo hero still full-bleed
- The primary CTA lives in the pinned bottom bar, never inline mid-scroll

### Whitespace Philosophy
- **Let the car breathe**: the photo hero is uncropped by chrome; controls float, they don't frame
- **Calm around trust**: host card and policies get generous padding so trust signals read clearly, never crowded
- **Photo-card rhythm**: search results are evenly spaced photo cards with consistent corner radius — scannable like Airbnb stays
- **The Book bar is sacred space**: always reserved at the bottom; content scrolls under it, never over it

### Border Radius Scale
- Square (0pt): the full-bleed photo hero (edge-to-edge)
- Subtle (4pt): tiny chips, ticks
- Standard (8pt): small controls
- Button (10pt): primary/secondary buttons, the Book CTA
- Field (12pt): inputs, spec tiles
- Card (14pt): search cards, host card, trip cards
- Comfortable (20pt): bottom sheets, modals
- Pill (500pt): filter chips, badges, map pins, status pills
- Circle (50%): avatars, floating photo-hero controls

## 6. Depth & Elevation

Turo uses soft, restrained elevation — the photo carries the depth, UI stays calm.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, inline text, the photo hero itself |
| Card (Level 1) | `rgba(20,18,40,0.08) 0 2px 10px` light / 1pt `#2D2D35` border dark | Search cards, host card, spec tiles |
| Pinned Bar (Level 2) | Top divider + backdrop blur (`rgba(15,15,18,0.96)`) | The sticky price + Book bar |
| Floating Control (Level 2) | `rgba(0,0,0,0.35) 0 2px 8px` + blur | Photo-hero back/share/favorite buttons |
| Sheet (Level 3) | `rgba(15,15,18,0.5) 0 -8px 28px` | Price breakdown, filters, date picker sheets |
| Modal Overlay | `rgba(15,15,18,0.55)` scrim | Behind modals and sheets |

**Shadow Philosophy**: the car photo provides the visual richness; UI elevation is deliberately quiet so nothing competes with the imagery. Floating photo-hero controls get a soft dark blur so they're tappable over any photo. The Book bar uses a backdrop blur (not a hard shadow) so it reads as a glass shelf over the content. On dark, cards switch to a 1pt divider border since shadows read poorly on near-black.

### Motion
- **Photo carousel swipe**: 1:1 finger tracking; page dot morphs (active grows to an 18pt pill) over 200ms ease-out; soft paging haptic at each boundary
- **Favorite tap**: heart scales `1.0 → 1.25 → 1.0` over 280ms with a teal fill cross-fade + light haptic
- **Book bar CTA press**: scale `1.0 → 0.98` 120ms + Purple → Pressed Purple
- **Listing open**: shared-element-style transition — the search card photo expands into the full-bleed hero over 320ms ease-out
- **Map pin select**: pin scales `1.0 → 1.12` and recolors to the white selected state over 150ms; the matching card scrolls into view
- **Filter chip toggle**: fill cross-fades to purple over 150ms
- **Sheet present**: bottom sheet rises 320ms ease-out + scrim fade; "est. total" → breakdown sheet
- **Tab change**: instant icon/label tint to Purple; no pill slide (there is no pill)
- **Page push**: native iOS push, 300ms horizontal slide
- **Haptics**: light on favorite, CTA press, and filter toggle; soft paging tick on carousel; success on booking confirmed

## 7. Do's and Don'ts

### Do
- Make the car photo the hero — full-bleed, edge-to-edge, no card frame on the listing
- Use Turo Purple (`#593BFB`) as the single booking-action color (Continue, Book, selected pin, active filter)
- Use Turo Teal (`#5CE0B8`) for trust & value — All-Star Host, savings, free cancellation, instant book, favorite-saved
- Pin a sticky price + Book bar to the bottom of the listing — it is the conversion anchor
- Surface host trust on every listing: avatar, All-Star badge, trips, response time, reviews
- Twin search results with a map of purple price pins (Airbnb-style)
- Use a near-black canvas (`#0F0F12`) in dark mode so photography pops
- Keep prices heavy (800) with tabular numerals so they never jitter
- Use the pill-style active page dot as the carousel pagination affordance
- Round cards and surfaces generously (10–14pt); float photo-hero controls as circular blurred buttons
- Keep trust copy calm and clear — clarity conveys trust, not shouting

### Don't
- Don't put a card frame, border, or inset around the listing photo hero — it is full-bleed
- Don't tint, dim, or color-block over car photos — scrims are only thin legibility gradients at top/bottom
- Don't use teal for the main booking CTA — purple is the transaction; teal is the reassurance
- Don't use purple for trust badges — All-Star and savings are teal
- Don't let the Book bar scroll away — it is always pinned and always visible
- Don't use a tinted gray for the dark canvas — near-black `#0F0F12` keeps photos punchy
- Don't use star gold (`#FFB400`) for anything but the rating star
- Don't crowd the host card — trust signals need breathing room
- Don't add a tab-bar pill/indicator — active is a purple tint only
- Don't let prices jitter — always tabular lining numerals
- Don't bury the daily rate — it lives in the persistent Book bar with the est. total link

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Photo hero ~220pt; specs strip scrolls if 4 tiles; Book bar compact |
| iPhone 13/14/15 | 390pt | Standard layout; photo hero ~260pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated; floating controls clear the island |
| iPhone 15/16 Pro Max | 430pt | Photo hero ~300pt; larger host avatar |
| iPad (portrait) | 768pt | Search 2-up: photo-card list + live map; listing content max ~640pt, hero full-bleed |
| iPad (landscape) | 1024pt+ | Persistent map + card grid; listing detail in a wide column with a side rail of host's other cars |

### Dynamic Type
- Scales: display, car title, section headers, body, card titles, prices
- Fixed (layout-critical): tab labels, badge eyebrows, rating number, page-dot region
- Photo hero never scales (image-driven)

### Orientation
- iPhone: portrait-primary; photo hero supports landscape full-screen gallery view
- iPad: full rotation; map/card split reflows; gallery viewer is full-screen swipeable

### Touch Targets
- Primary/Book CTA: 52pt (44pt compact)
- Tab icons: 22pt glyph, 48pt+ hit area
- Floating photo-hero controls: 38pt visible, 44pt hit
- Search/host card: full-card tappable, ≥ 64pt
- Map pins: 44pt minimum hit

### Safe Area Handling
- Top: floating photo-hero controls respect the Dynamic Island/notch
- Bottom: the Book bar sits above the home indicator; content scrolls under both
- Photo gallery viewer: full-bleed, swipe gestures respect safe-area edges

## 9. Agent Prompt Guide

### Quick Color Reference
- Booking action (Turo Purple): `#593BFB` (pressed `#4A2FD6`, bright/link `#7C5CFF`)
- Trust / value (Turo Teal): `#5CE0B8` (deep `#1FB890`)
- Canvas (light): `#FFFFFF` · surface 1 `#F6F5FA` · surface 2 `#FFFFFF`
- Canvas (dark): `#0F0F12` · surface 1 `#1A1A1F` · surface 2 `#25252C`
- Divider: `#E6E4EF` light / `#2D2D35` dark
- Text primary: `#15131F` light / `#F2F1F6` dark
- Text secondary: `#6B6878` light / `#A4A2B3` dark
- Text on purple: `#FFFFFF` · text on teal: `#06231B`
- Star rating: `#FFB400` · success/savings: `#1FB890` light / `#5CE0B8` dark

### Example Component Prompts
- "Build the Turo listing photo hero in SwiftUI: full-bleed, edge-to-edge, 260pt tall, NO card frame or inset. A paginated photo carousel with page dots bottom-center — active dot an 18pt-wide white pill, inactive 6pt `rgba(255,255,255,0.45)`. Top overlay: back / share / favorite as 38pt circular `rgba(15,15,18,0.55)` blurred buttons with white 2.2pt-stroke icons. Favorite heart fills `#5CE0B8` when saved. No color overlay on the photo — only a subtle top legibility gradient behind the controls."

- "Create the Turo host card: 14pt corner radius, `#1A1A1F` fill, 1pt `#2D2D35` border, full-width. Leading 46pt circular avatar (purple→teal gradient with initials fallback). Name 'Marcus J.' 15pt w700 + inline All-Star badge: pill `rgba(92,224,184,0.16)` fill, `#5CE0B8` text, leading star, 10pt w800 uppercase. Sub '214 trips · Joined 2019 · Responds in ~10 min' 12pt secondary. Trailing chevron tertiary."

- "Build the Turo sticky price + Book bar: pinned to the bottom, always visible, blurred `rgba(15,15,18,0.96)` panel, 0.5pt top `#2D2D35` divider. Left: '$83' 20pt w800 + '/ day' 13pt secondary; below, '$249 est. total · 3 days' 11pt `#5CE0B8` underlined (tap → breakdown sheet). Right: purple 'Continue' CTA, `#593BFB`, 10pt radius, 16pt w800 white. The bar never scrolls away."

- "Render the Turo specs strip: a horizontal row of 3 tiles under the car title. Each tile 12pt radius `#1A1A1F` with 1pt `#2D2D35` border, centered: 18pt stroke icon `#5CE0B8`, value 13pt w700 ('Electric'), label 10pt secondary ('Fuel'). Tiles: Fuel, Capacity, Transmission."

- "Build the Turo search card: photo-led, 14pt radius `#25252C`, 1pt `#2D2D35` border. Photo top ~110pt with favorite heart top-right (white outline default, `#5CE0B8` filled saved). Body: car title 13pt w700; rating '4.99 (87) · 156 trips' 11pt secondary with an 11pt `#FFB400` star; price '$214 / day' 14pt w800 primary. Twin with a map of `#593BFB` price pins; selected pin inverts to white fill / `#593BFB` text."

- "Build the Turo bottom tab bar: 5 tabs Search / Trips / Favorites / Inbox / More, height 68pt, `#0F0F12` bg, 0.5pt `#2D2D35` top divider. Active tint `#593BFB` (icon + label), inactive `#6F6D7E`. NO pill/indicator — color tint only. Labels Manrope 10pt w600 always visible."

### Iteration Guide
1. The car photo is the product — full-bleed, edge-to-edge hero with NO card frame; controls float, never frame
2. Turo Purple `#593BFB` is the single booking-action color — Continue, Book, selected pin, active filter; one loud purple CTA per screen
3. Turo Teal `#5CE0B8` is trust & value only — All-Star Host, savings, free cancellation, instant book, favorite-saved; never the main CTA
4. The sticky price + Book bar is pinned to the bottom always — it is the conversion anchor; content scrolls under it
5. Host trust is on every listing — avatar, All-Star badge, trips, response time, reviews; calm copy conveys trust
6. Dark canvas is near-black `#0F0F12` so photography pops; photos are never tinted or dimmed, only chrome
7. Prices are heavy (800) with tabular numerals — daily rate, est. total, savings never jitter
8. Search twins photo-led cards with a map of purple price pins (Airbnb-style); selected pin inverts to white
9. Surfaces are generously rounded (10–14pt); photo-hero controls are circular blurred floating buttons
10. Star gold `#FFB400` is the rating star only — never a CTA or decoration; teal carries success/savings
