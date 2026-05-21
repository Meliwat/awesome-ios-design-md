# Design System Inspiration of Yelp (iOS)

## 1. Visual Theme & Atmosphere

Yelp's iOS app is a **trust engine wearing a clean white skin**. The entire product exists to answer one question — "is this place any good?" — and every screen is engineered to surface that answer fast: a business name, a row of five stars, a review count, a price band, and a wall of full-color photos and user reviews. The canvas is pure white (`#FFFFFF`) with crisp hairline dividers and 8pt gray section bands that chunk the long business-detail scroll into digestible blocks. There is exactly one loud color — **Yelp Red** (`#FF1A1A` interactive, `#D32323` in the logo) — and it is rationed: the "Write a Review" button, the active tab, the burst logomark, and almost nothing else. Everything else is grayscale typography doing the work.

The signature screen is the **business detail page**. It opens with a full-bleed business photo header (≈184pt tall, with a photo counter like "1 / 248 photos"), then a tight header block: business name in Open Sans 24pt 800, the 5-star rating row, the review-count link, a `$$ · Bakeries · Cafes` category line, and an `Open · Closes 8:00 PM` hours line where "Open" is green. Below that sits the **action row** — a red "Write a Review" pill flanked by ghost icon buttons (Save, Share, Directions) — then the long flow of "Recommended Reviews": each a card with a circular avatar, reviewer name + location + review count, a star row, a date, the review body, and the famous **Useful / Funny / Cool** vote actions. This is the heart of Yelp; the photo, the stars, and the reviews are the product.

The other Yelp-defining moment is the **5-star rating control**. Yelp's stars are not a generic widget — they're a brand asset. The filled star uses a warm orange-red (`#F25C05` on this dark catalog; `#F15C00`-class in production), the empty star is a flat gray, and ratings render in half-star increments (1.0 → 5.0). Stars appear everywhere: on the business header, on every review, on search result rows, on photos, and in the rating filter. The second brand fixture is the **category chip row** — pill-shaped filters (Restaurants, Coffee, Hotels, Nightlife, Delivery, Reservations) with a small red-stroked glyph; the selected chip flips to a solid red fill.

Typography is **Open Sans** — the humanist, highly legible workhorse Yelp uses across web and app for body and review text (production also pairs it with a display weight for hero headers). Hierarchy is product-UI, not editorial: screen title 32pt, business name 24pt, section header 20pt, card header 16pt, body 14pt, meta 13pt, caption 12pt. Headings are heavy (700-800); body is 400; the high-frequency review-vote and meta text is 600. The type system optimizes for **scanning a dense list of opinions**, not for reading a long-form article.

**Key Characteristics:**
- Pure white canvas (`#FFFFFF`) light / near-black (`#161616`) dark — content-and-photos forward, minimal chrome
- One rationed brand color — **Yelp Red** `#FF1A1A` / `#D32323` — for primary CTA, active tab, logomark only
- The **5-star rating** is a brand asset — warm orange-red filled star, gray empty, half-star increments, everywhere
- Business detail = full-bleed photo header → name + stars + reviews → action row → review wall
- **Recommended Reviews** cards with avatar, reviewer identity, stars, date, body, and Useful / Funny / Cool votes
- Category **filter chips** — pills with red glyph; selected chip is solid red
- Open Sans typography — humanist, legible, tuned for scanning dense opinion lists
- 8pt gray section bands (light) / `#0C0C0C` bands (dark) chunk the long detail scroll
- `$ $$ $$$ $$$$` price band + `Open`/`Closed` hours status (green/red) as universal business metadata
- Bottom tab bar: Search, Nearby, Write (center, red), Activity, Me

## 2. Color Palette & Roles

### Primary (Interactive)
- **Yelp Red** (`#FF1A1A`): Primary interactive color — "Write a Review" button, active tab, primary CTAs, selected chip fill.
- **Yelp Red (Logo/Brand)** (`#D32323`): The burst logomark and brand lockup; slightly deeper than the interactive red.
- **Yelp Red Pressed** (`#B81E1E`): Pressed/active state on red buttons.
- **Link Blue** (`#0073BB`): Hyperlinks, "see all photos", phone numbers, web URL — lightens to `#4FA3D9` on dark.

### Rating
- **Rating Star (Filled)** (`#F25C05`): The 5-star scale filled star — a warm orange-red, identical in light and dark so it stays legible.
- **Rating Star (Empty)** (`#E3E3E0` light / `#3A3A3A` dark): Unfilled portion of the star scale.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas, business detail, cards.
- **Surface Gray** (`#F5F5F5`): Search filter bar, subtle elevated chips, input fills.
- **Section Band** (`#F0F0F0`): The 8pt band that separates business-detail sections.
- **Divider** (`#E0E0E0`): Hairline between review cards and list rows.
- **Pressed Row** (`#EDEDED`): Pressed list row / chip.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#161616`): Primary dark canvas — near-black, not pure black.
- **Dark Surface 1** (`#1E1E1E`): Cards, sheets, elevated surfaces.
- **Dark Surface 2** (`#2A2A2A`): Chips, input fills, pressed rows.
- **Dark Divider** (`#303030`): Hairline dividers.
- **Dark Section Band** (`#0C0C0C`): The section separator band on dark.

### Text
- **Text Primary Light** (`#2B2B2B`): Primary body, business name, review text.
- **Text Secondary Light** (`#6E6E6E`): Metadata, reviewer location, dates, category line.
- **Text Tertiary Light** (`#9A9A9A`): Disabled, very low emphasis.
- **Text Primary Dark** (`#E6E6E6`): Primary body on dark.
- **Text Secondary Dark** (`#A8A8A8`): Secondary on dark.
- **Text Tertiary Dark** (`#6E6E6E`): Disabled / low emphasis on dark.

### Semantic

| Role | Light Mode | Dark Mode | Use |
|------|-----------|-----------|-----|
| Open / Success | `#2DA44E` | `#2DA44E` | "Open" hours status, success toast |
| Closed / Error | `#E03E3E` | `#E03E3E` | "Closed" status, delete confirmation |
| Warning | `#D9730D` | `#E08B2D` | "Closing soon" hours warning |
| Link | `#0073BB` | `#4FA3D9` | Hyperlinks, photo-count links, phone |
| Rating Star | `#F25C05` | `#F25C05` | The 5-star scale (identical both modes) |
| Verified / Elite | `#A2231D` | `#C24A4A` | "Elite" reviewer badge accent |

### Price Band
The `$` `$$` `$$$` `$$$$` band is rendered in **Text Secondary** with the active count in **Text Primary** — it is metadata, never colored. Example: `$$` = first two glyphs `#2B2B2B`, remaining two `#9A9A9A`.

## 3. Typography Rules

### Font Family
- **Primary**: `Open Sans` (by Steve Matteson for Ascender, Apache 2.0 / now SIL OFL via Google Fonts) — a humanist sans optimized for legibility on screen, used across Yelp web and app for body and review copy.
- **Display (production)**: Yelp pairs Open Sans with a heavier display cut for hero headers; Open Sans 800 is the faithful free stand-in.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Monospace**: not used in product UI (only internal/dev contexts).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Open Sans | 32pt | 800 | 1.2 | -0.4pt | Large nav title ("Restaurants", "Activity") |
| Business Name | Open Sans | 24pt | 800 | 1.2 | -0.3pt | Business detail header |
| Section Header | Open Sans | 20pt | 700 | 1.25 | -0.2pt | "Recommended Reviews", "Popular dishes" |
| Card Header | Open Sans | 16pt | 800 | 1.3 | -0.1pt | Sub-section / dish-card title |
| Emphasis | Open Sans | 15pt | 700 | 1.3 | 0pt | Rating number, price, key stats |
| Body | Open Sans | 14pt | 400 | 1.55 | 0pt | Review text, descriptions |
| Reviewer Name | Open Sans | 14pt | 700 | 1.3 | 0pt | Reviewer name on a review card |
| Meta | Open Sans | 13pt | 600 | 1.4 | 0pt | Location, review count, category line |
| Caption | Open Sans | 12pt | 600 | 1.4 | 0.1pt | Dates, "Useful / Funny / Cool" labels |
| Button | Open Sans | 15pt | 700 | 1.0 | 0pt | Primary / secondary buttons |
| Tab Label | Open Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Chip | Open Sans | 13pt | 600 | 1.0 | 0pt | Category filter chip text |
| Link | Open Sans | 14pt | 700 | 1.4 | 0pt | "See all 248 photos", inline links |

### Principles
- **Scanning over reading**: sizes/weights help the eye jump between business name → stars → review count → price → hours, not flow through prose.
- **Heavy headers, regular body**: business name and section headers are 700-800; review body is 400; never bold a review paragraph.
- **Numbers are emphasis**: rating numbers, review counts, and prices are 700 even at small sizes — they're the decision data.
- **Meta is 600, not 400**: the high-frequency metadata (location, dates, vote labels) uses 600 so it stays readable at 12-13pt.
- **Dynamic Type first-class**: titles, names, section headers, body, meta scale; tab labels and chip text stay fixed.

## 4. Component Stylings

### Buttons

**Primary Button ("Write a Review" / "Add Photo" / "Start Order")**
- Shape: Rounded rectangle, 8pt corner radius
- Background: `#FF1A1A` (Yelp Red) / `#FF1A1A` dark (identical)
- Text: `#FFFFFF`, Open Sans 15pt weight 700
- Padding: 13pt vertical, 26pt horizontal; min height 44pt
- Leading icon (optional): 16pt stroke glyph, white
- Pressed: background `#B81E1E` + scale 0.98
- Disabled: background `#F5BFBF` / text `#FFFFFF` 60%

**Secondary / Outline Button ("Add Photo", "Check In")**
- Background: transparent
- Text: `#2B2B2B` light / `#E6E6E6` dark, Open Sans 14pt weight 600
- Border: 1pt `#E0E0E0` light / `#303030` dark
- Corner radius: 8pt
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#EDEDED` / `#2A2A2A`

**Ghost Icon Button (Save / Share / Directions in the action row)**
- Layout: 56pt wide, 44pt tall, icon over a 9pt label
- Icon: 16pt stroke, `#6E6E6E` / `#A8A8A8`
- Label: Open Sans 9pt weight 600, same color
- Border: 1pt divider; corner radius 8pt
- Selected (Saved): icon + label flip to `#FF1A1A`, icon fills solid

**Pill Button ("Directions", "Call", filter pills)**
- Background: `#F5F5F5` / `#2A2A2A`
- Border: 1pt divider
- Corner radius: 500pt (full pill)
- Text: Open Sans 13pt weight 600 `#2B2B2B` / `#E6E6E6`
- Padding: 9pt vertical, 16pt horizontal

**Link Button ("See all 248 photos")**
- Font: Open Sans 14pt weight 700
- Color: `#0073BB` light / `#4FA3D9` dark
- No underline by default; underline on press
- No background, no border

### Star Rating (the core atom)

The 5-star rating is Yelp's signature component and appears on virtually every screen.

- 5 star glyphs in a row, 2pt inter-star gap
- **Filled**: `#F25C05` (warm orange-red) — identical light and dark
- **Empty**: `#E3E3E0` light / `#3A3A3A` dark
- **Half**: clipped fill at 50% width (Yelp renders to nearest half-star: 1.0, 1.5, … 5.0)
- Sizes by context: business header 18pt, review card 15pt, list row 14pt, large rating filter 22pt
- Adjacent rating number: Open Sans 15pt weight 700 `#2B2B2B`
- Adjacent review count: Open Sans 13-14pt weight 600 `#6E6E6E`, format "3,812 reviews"
- Interactive (rating input): tappable per-star + drag for half-stars, soft haptic per increment

### Review Card (distinctive component)

- Container: no border/shadow; separated by 1pt `#E0E0E0` / `#303030` divider, 16pt vertical padding
- **Avatar**: 40pt circle; user photo or a colored monogram (gradient `#D32323 → #8A1818`, white initials Open Sans 15pt 700)
- **Reviewer line**: name Open Sans 14pt 700; sub-line "City, ST · {n} reviews" Open Sans 12pt 600 `#6E6E6E`; optional red "Elite '24" badge
- **Star row + date**: 15pt stars + date "Oct 9, 2024" Open Sans 12pt 600 `#6E6E6E`, space-between
- **Body**: Open Sans 14pt 400 line-height 1.55 `#2B2B2B`; "Read more" link after 4 lines
- **Photo strip** (optional): horizontal row of 72pt rounded (8pt) thumbnails
- **Vote actions**: `Useful` / `Funny` / `Cool`, each an icon + count, Open Sans 12pt 600 `#6E6E6E`; tapped state turns `#FF1A1A`

### Business Header Block

- Full-bleed photo header ≈184pt; back + share circular buttons (`rgba(0,0,0,0.45)`, 34pt) top-left/right; "1 / 248 photos" counter bottom-right (`rgba(0,0,0,0.6)`, 6pt radius)
- Business name: Open Sans 24pt 800
- Rating row: 18pt stars + rating number (15pt 700) + review-count link
- Category line: `$$ · Bakeries · Cafes` Open Sans 14pt 600 `#6E6E6E` with `·` separators
- Hours line: `Open · Closes 8:00 PM` — "Open" `#2DA44E` 700, rest `#6E6E6E`
- Followed by the action row, then an 8pt section band

### Navigation

**Bottom Tab Bar**
- Height: 49pt + safe area
- Background: `#FFFFFF` / `#161616` with 0.5pt top divider
- Tabs (5): Search, Nearby, **Write** (center, emphasized), Activity, Me
- Icon size: 22pt; active color `#FF1A1A` (icon fills solid); inactive `#7E7E7E`
- Labels: Open Sans 10pt weight 600, always shown
- Center "Write" tab is the review CTA — same red, slightly emphasized

**Top Nav (search / list screens)**
- Height: 44pt + safe area
- Leading: location pill ("San Francisco, CA ▾") or back chevron
- Center: screen title (compact)
- Trailing: filter / map toggle icon

**Top Nav (business detail)**
- Transparent over the photo header; back + share are floating circular buttons
- On scroll past the header, a solid bar fades in with the business name (compact, 17pt 700) centered

### Input Fields

**Search Bar**
- Height: 40pt
- Background: `#F5F5F5` / `#2A2A2A`
- Corner radius: 8pt
- Leading `magnifyingglass` 16pt `#6E6E6E`; placeholder "tacos, cheap dinner, Max's" Open Sans 14pt 400 `#9A9A9A`
- A second adjacent "Near" field on iPad / wide; on iPhone it's a stacked secondary row
- Focus: 1.5pt `#FF1A1A` border

**Filter Sheet**
- Bottom sheet, 12pt top corners, `#FFFFFF` / `#1E1E1E`
- Sections: Sort (Recommended / Highest Rated / Most Reviewed), Price ($–$$$$ toggle pills), Distance, Open Now switch, Category chips
- Apply button: full-width primary red, pinned bottom

### Distinctive Components

**5-Star Rating Control** — see *Star Rating* above; Yelp's single most recognizable asset.

**Recommended Reviews Card** — see *Review Card* above; the body of every business page.

**Category Filter Chip**
- Pill, 500pt radius, `#F5F5F5` / `#2A2A2A` bg, 1pt divider border
- Leading glyph 15pt stroked `#FF1A1A`; label Open Sans 13pt 600
- Selected: solid `#FF1A1A` fill, white glyph + label
- Horizontal scroll row under the search bar

**Price Band (`$ $$ $$$ $$$$`)**
- 4 `$` glyphs; active count in `#2B2B2B`, remainder in `#9A9A9A`; Open Sans 14pt 600 — pure metadata, never colored

**Open / Closed Hours Pill**
- Inline text: "Open" `#2DA44E` 700 / "Closed" `#E03E3E` 700 / "Closes soon" `#D9730D` 700, followed by `· Closes 8:00 PM` in `#6E6E6E`

**Photo Grid / Carousel**
- Business photos: full-bleed swipeable header + a tappable grid (3-up, 2pt gutter, 4pt corners) on the Photos tab
- Photo counter chip "1 / 248" overlays bottom-right of the header

**Elite Badge**
- Small rounded chip, `#A2231D` / `#C24A4A` text, "Elite '24", on qualifying reviewers' cards

**Check-In Button**
- Pill, red outline; on tap fills solid red with a soft haptic + "Checked in" toast

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Content inset: 16pt horizontal
- Review card vertical padding: 16pt; divider between cards
- Section band: 8pt full-bleed gray/black strip between detail sections

### Grid & Container
- iPhone: single column, 16pt insets, full-bleed photo header & section bands
- iPad: 2-column on search (list + map) or content centered max 720pt
- Photo grid: 3 columns, 2pt gutter
- Review photo strip: horizontal scroll, 72pt thumbs, 8pt gap

### Whitespace Philosophy
- **Decision data is dense, prose is airy**: name/stars/price/hours are packed tight at the top so the verdict is instant; review bodies get 1.55 line-height to stay readable
- **Section bands replace cards**: instead of bordered cards, Yelp uses 8pt gray bands to chunk the long detail scroll — flatter, faster to scan
- **Photos go full-bleed**: the business photo header and review photos extend edge-to-edge; only chrome respects the 16pt inset

### Border Radius Scale
- Square (0pt): full-bleed photo header, section bands
- Subtle (4pt): photo grid thumbnails
- Standard (8pt): buttons, search bar, input fields, photo-count chip
- Comfortable (12pt): bottom sheets, modals
- Pill (500pt): category chips, filter pills, check-in
- Circle (50%): avatars, floating photo-header buttons

## 6. Depth & Elevation

Yelp is mostly flat — it relies on dividers and 8pt section bands, not shadows. Shadows appear in four contexts:

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Review cards, list rows, content (separated by dividers / bands) |
| Raised (Level 1) | `rgba(0,0,0,0.10) 0 1px 4px` | Sticky search bar on scroll, floating photo-header buttons |
| Sheet (Level 2) | `rgba(0,0,0,0.16) 0 -4px 20px` | Filter bottom sheet, share sheet |
| Modal Overlay | `rgba(0,0,0,0.4)` | Scrim behind bottom sheets and full-screen photo viewer |

**Shadow Philosophy**: Yelp signals hierarchy with **structure** (dividers, gray bands, white cards on white) rather than elevation. The only persistent shadows are the floating buttons over photos (which need separation from arbitrary imagery) and the sticky search bar. On dark mode, shadows nearly vanish, so the 8pt `#0C0C0C` section band and `#303030` dividers carry the structure instead.

### Motion
- **Photo header parallax**: header image scrolls at ~0.5× content speed; compact title bar cross-fades in over 200ms when the header scrolls off
- **Star rating input**: each star fills with a 120ms ease-out + soft haptic per increment; drag for half-stars tracks the finger
- **Vote tap (Useful/Funny/Cool)**: count bumps with a 150ms spring + the icon flashes `#FF1A1A`
- **Filter sheet**: slides up 300ms ease-out; scrim fades 0 → 0.4 in 200ms
- **Tab switch**: instant; active icon swaps to filled red with no slide
- **Photo viewer**: tap a photo → zoom-out shared transition into full-screen pager (300ms)
- **Check-in**: outline pill fills red with a 200ms ease + medium haptic, then a "Checked in" toast slides from bottom
- **Pull to refresh** (search/activity): red spinner
- **Haptic feedback**: soft impact on each rating increment, medium on check-in, light on vote

## 7. Do's and Don'ts

### Do
- Use pure white (`#FFFFFF`) light / near-black (`#161616`) dark — content + photos forward
- Ration Yelp Red (`#FF1A1A`) to the primary CTA, active tab, and logomark — it loses power if overused
- Render the 5-star scale with the warm orange-red filled star (`#F25C05`) in **both** light and dark
- Support half-star increments (1.0, 1.5, … 5.0) — Yelp never shows arbitrary decimals as partial fills beyond halves
- Lead the business page with a full-bleed photo header + photo counter
- Chunk the detail scroll with 8pt gray (light) / `#0C0C0C` (dark) section bands, not bordered cards
- Show `Open`/`Closed` in green/red and the `$ $$ $$$ $$$$` band in gray metadata
- Put Useful / Funny / Cool on every review with icon + count, turning red when voted
- Use circular avatars with colored monogram fallback
- Keep the center "Write" tab as the review CTA

### Don't
- Don't color the price band — `$ $$ $$$ $$$$` is gray metadata, never red or green
- Don't make the filled star pure yellow or pure red — it's the warm orange-red `#F25C05`
- Don't bold review body text — body is 400; only names/headers/numbers are heavy
- Don't use bordered cards for review items — use dividers and section bands
- Don't overuse red — multiple red buttons on one screen kills the "Write a Review" hierarchy
- Don't show fractional stars beyond halves — round to nearest 0.5
- Don't drop the review count next to a rating — a star rating without "(3,812 reviews)" is untrustworthy in Yelp's model
- Don't pure-black the dark canvas — it's `#161616`; cards are `#1E1E1E`
- Don't hide the photo counter — the photo volume is a trust signal
- Don't animate aggressively — motion is 120-300ms ease-out, haptics are soft

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Photo header 160pt; search "Near" field stacks below query |
| iPhone 13/14/15 | 390pt | Standard layout; photo header ≈184pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the transparent top nav |
| iPhone 15/16 Pro Max | 430pt | Photo header 200pt; 3-up photo grid wider thumbs |
| iPad (portrait) | 768pt | Search = split list + map; business detail centered max 720pt |
| iPad (landscape) | 1024pt+ | Persistent map pane + list; detail in a wide centered column |

### Dynamic Type
- Screen title, business name, section header, body, meta, review text: full scale
- Tab labels, chip text, photo-count chip, vote labels: FIXED (layout-sensitive)
- Star glyphs scale proportionally with their adjacent rating number

### Orientation
- All screens rotate
- iPad landscape pins the map beside the results list
- Full-screen photo viewer rotates and re-fits

### Touch Targets
- Star (interactive input): 44pt hit per star, 22pt glyph
- Tab bar icons: 22pt glyph, 49pt bar height, 44pt hit
- Vote actions (Useful/Funny/Cool): 44pt hit each
- Floating photo-header buttons: 34pt visual, 44pt hit
- Primary buttons: 44pt min height

### Safe Area Handling
- Top: transparent business-detail nav respects the Dynamic Island; floating buttons sit below it
- Bottom: tab bar + home indicator respected; filter sheet apply button sits above the home indicator
- Photo header: extends into the top safe area (full-bleed) with buttons inset to safe area
- Keyboard: search results scroll above the keyboard

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Canvas (dark): `#161616`
- Surface gray (light): `#F5F5F5`
- Dark surface 1: `#1E1E1E`
- Divider: `#E0E0E0` light / `#303030` dark
- Section band: `#F0F0F0` light / `#0C0C0C` dark
- Text primary: `#2B2B2B` light / `#E6E6E6` dark
- Text secondary: `#6E6E6E` light / `#A8A8A8` dark
- Yelp Red (interactive): `#FF1A1A`
- Yelp Red (logo): `#D32323`
- Yelp Red pressed: `#B81E1E`
- Rating star (filled): `#F25C05` (both modes)
- Rating star (empty): `#E3E3E0` light / `#3A3A3A` dark
- Link: `#0073BB` light / `#4FA3D9` dark
- Open / success: `#2DA44E`
- Closed / error: `#E03E3E`

### Example Component Prompts
- "Build the Yelp business header in SwiftUI: a 184pt full-bleed photo at top with two 34pt circular `rgba(0,0,0,0.45)` buttons (back `chevron.left`, share) inset to the safe area, and a bottom-right '1 / 248 photos' chip (`rgba(0,0,0,0.6)`, 6pt radius, white Open Sans 12pt 600). Below: business name Open Sans 24pt weight 800 `#2B2B2B`; a star row (18pt, filled `#F25C05`, empty `#E3E3E0`, half-star supported) + rating number Open Sans 15pt 700 + '3,812 reviews' link `#0073BB`; a category line '$$ · Bakeries · Cafes' Open Sans 14pt 600 `#6E6E6E`; an hours line where 'Open' is `#2DA44E` 700 and '· Closes 8:00 PM' is `#6E6E6E`. End with an 8pt `#F0F0F0` section band."

- "Create a Yelp 5-star rating control: 5 star glyphs, 2pt gap, supporting half-star fill. Filled `#F25C05`, empty `#E3E3E0` light / `#3A3A3A` dark — identical filled color in both modes. Sizes: 18pt on a business header, 15pt on a review card, 14pt in a list row. Adjacent rating number Open Sans 15pt 700 `#2B2B2B`, review count Open Sans 13pt 600 `#6E6E6E` formatted '3,812 reviews'. Interactive mode: tap a star or drag for half-steps, 120ms ease-out fill + soft haptic per increment."

- "Render a Yelp Recommended Reviews card: 40pt circular avatar (photo or gradient `#D32323→#8A1818` monogram, white initials Open Sans 15pt 700); reviewer name Open Sans 14pt 700; sub-line 'San Francisco, CA · 142 reviews' Open Sans 12pt 600 `#6E6E6E`; a 15pt star row + date 'Oct 9, 2024' Open Sans 12pt 600 space-between; body Open Sans 14pt 400 line-height 1.55 `#2B2B2B`; a Useful / Funny / Cool action row (icon + count, Open Sans 12pt 600 `#6E6E6E`, turning `#FF1A1A` when voted). Separate cards with a 1pt `#E0E0E0` divider and 16pt vertical padding — no border, no shadow."

- "Build the Yelp action row: a flexible-width primary 'Write a Review' button (`#FF1A1A`, white Open Sans 15pt 700, 8pt radius, 44pt tall, leading 16pt pencil-in-circle glyph, pressed `#B81E1E` scale 0.98) followed by two 56×44pt ghost icon buttons ('Save' bookmark, 'Share' nodes), each icon 16pt `#6E6E6E` over a 9pt 600 label, 1pt divider border, 8pt radius. Saved state flips the Save button icon + label to `#FF1A1A`."

- "Create the Yelp category filter chip row: a horizontally scrolling row of pill chips (500pt radius, `#F5F5F5` bg, 1pt `#E0E0E0` border) each with a 15pt red-stroked glyph + Open Sans 13pt 600 label. The selected chip is a solid `#FF1A1A` fill with a white glyph + label. 10pt gap between chips, sits directly under the search bar."

- "Render the Yelp bottom tab bar: 49pt + safe area, `#FFFFFF` / `#161616`, 0.5pt top divider, 5 tabs — Search, Nearby, Write (center), Activity, Me — 22pt icons, Open Sans 10pt 600 labels always shown. Active tab `#FF1A1A` with the icon filled solid; inactive `#7E7E7E`. No tint pill, no slide on switch."

### Iteration Guide
1. Canvas is pure white (`#FFFFFF`) light / near-black `#161616` dark — content + photos forward, NOT off-white
2. Yelp Red `#FF1A1A` (logo `#D32323`) is rationed — primary CTA, active tab, logomark only; never multiple reds per screen
3. The 5-star scale is a brand asset: warm orange-red filled `#F25C05` (both modes), gray empty, half-star increments
4. Business detail leads with a full-bleed photo header + "1 / N photos" counter, then name → stars → reviews
5. Recommended Reviews cards carry avatar + identity + stars + date + body + Useful/Funny/Cool (red when voted)
6. Chunk the long scroll with 8pt gray (`#F0F0F0`) / dark (`#0C0C0C`) section bands, not bordered cards
7. `Open`/`Closed` is green/red; the `$ $$ $$$ $$$$` band is gray metadata, never colored
8. Open Sans throughout — heavy headers/numbers (700-800), 400 body, 600 meta; scanning over reading
9. Always pair a rating with its review count — "4.3 · 3,812 reviews" — a bare star rating is untrustworthy
10. Motion is quiet (120-300ms ease-out); soft haptic per rating increment, medium on check-in
