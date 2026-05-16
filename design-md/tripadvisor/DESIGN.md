# Design System Inspiration of Tripadvisor (iOS)

## 1. Visual Theme & Atmosphere

Tripadvisor's iOS app is a bright, optimistic travel canvas built on pure white (`#FFFFFF`) with a single electric signature: Tripadvisor Green (`#34E0A1`), the spring-fresh mint introduced with the owl rebrand. The interface treats traveler photography as the primary chromatic content — place cards lead with full-bleed user-submitted images of hotels, beaches, and restaurants — while the chrome itself stays restrained, light, and out of the way. This is a discovery app: the white background functions like a clean gallery wall, letting thousands of crowd-sourced photos and reviews do the talking. Nothing competes with content; the UI is a quiet frame around other people's trips.

The accent system is ritual and instantly recognizable. Tripadvisor Green appears on the primary CTA pill ("Write a review", "Book now"), the active bottom-tab indicator, and — most distinctively — the five-circle bubble rating that is the brand's most iconic visual unit. Filled bubbles are green; empty bubbles are a hairline outline. The owl mark (two bubble-eyes on a black silhouette) anchors the brand at the top of the home screen. Beyond green and owl-black, the palette is grayscale: white surfaces, a soft `#F2F2F2` for grouped cards, and `#E0E0E0` hairline dividers. The restraint is the point — when green appears, it means "this is the action" or "this is the score".

Typography is a humanist sans (the app uses Trip Sans / a Helvetica-class face in production; substitute "Inter"). The hierarchy is compact and utilitarian: 12-28pt with weights at 400 / 600 / 700. There are no oversized hero headlines because the photography *is* the headline. Place names sit at 17pt weight 700, review snippets at 15pt weight 400 in near-black, metadata (review counts, distance, price tier) at 13pt weight 400 in `#6B6B6B` gray. The most expressive type moment is the place-detail hero, where the venue name gets 24pt bold directly over a darkened traveler photo.

**Key Characteristics:**
- Pure-white canvas (`#FFFFFF`) with Tripadvisor Green (`#34E0A1`) as the single accent
- Traveler photography as the primary chromatic element — the UI borrows color from content
- Five-circle bubble rating — the brand's most iconic, unmistakable unit
- Owl logomark (bubble-eyes) anchoring the home header
- Category tiles ("Hotels", "Things to do", "Restaurants", "Flights") as a fast entry grid
- "Travelers' Choice" award badge in green on top-rated place cards
- Bottom tab bar (Explore / Search / Trips / Review / More) with green active state
- Haptics on bubble rating selection, save-to-trip heart, and tab switches

## 2. Color Palette & Roles

### Primary
- **Tripadvisor Green** (`#34E0A1`): Primary CTA pill, active tab indicator, filled bubble rating, "Travelers' Choice" badge, selected filter chip.
- **Green Pressed** (`#21C589`): Active/pressed state for green CTAs and bubbles during selection.
- **Owl Black** (`#000000`): Logomark silhouette, primary headings, icon glyphs at full emphasis.

### Canvas & Surface
- **Canvas White** (`#FFFFFF`): Primary canvas — the clean gallery wall behind all content.
- **Surface** (`#F2F2F2`): Grouped card backgrounds, segmented controls, search-field fill.
- **Divider** (`#E0E0E0`): Hairline dividers between list rows and section breaks.
- **Surface Pressed** (`#E8E8E8`): Pressed state on light list rows and tappable cards.

### Text
- **Text Primary** (`#000000`): Place names, screen titles, review headlines, primary UI text.
- **Text Secondary** (`#6B6B6B`): Review counts, distance, price tier, metadata, captions.
- **Text Tertiary** (`#9B9B9B`): Disabled labels, placeholder text, very low-emphasis hints.

### Semantic
- **Empty Bubble** (`#D9D9D9`): Unfilled portion of the five-circle bubble rating.
- **Save Heart Active** (`#34E0A1`): Saved-to-trip heart fill.
- **Price Tier** (`#000000`): "$$ - $$$" affordability glyphs (filled vs `#D9D9D9` empty).
- **Award Gold** (`#C9A227`): "Travelers' Choice" laurel accent when rendered in heritage gold variant.
- **Error Red** (`#D6122E`): Form validation, booking failure states.

### Dark Mode (Limited Use)
Tripadvisor's iOS app is light-first. A dark variant exists for system-dark contexts; tokens invert toward a near-black canvas while Tripadvisor Green stays identical — it reads well on both.
- **Dark Canvas** (`#0F0F0F`)
- **Dark Surface** (`#1C1C1E`)
- **Dark Text Primary** (`#FFFFFF`)
- **Dark Text Secondary** (`#A0A0A0`)
- **Green** (`#34E0A1`) — unchanged across themes

## 3. Typography Rules

### Font Family
- **Primary**: `Trip Sans` (proprietary, post-rebrand) — humanist sans with open apertures
- **Substitute**: `Inter` — closest free face for the humanist grotesque tone
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Non-Latin**: System fallback per-script — Tripadvisor ships in 40+ languages

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title (Large) | Inter | 28pt | 700 | 1.15 | -0.4pt | Home / Explore large nav title |
| Place Hero | Inter | 24pt | 700 | 1.2 | -0.3pt | Venue name on place-detail hero over photo |
| Section Header | Inter | 22pt | 700 | 1.2 | -0.3pt | "Top experiences", "Hotels near you" |
| Place Name | Inter | 17pt | 700 | 1.25 | -0.1pt | Card title in list / search results |
| Card Title | Inter | 16pt | 600 | 1.3 | -0.1pt | Category tile label, secondary cards |
| Review Body | Inter | 15pt | 400 | 1.45 | 0pt | Review text snippet, descriptions |
| Button (Primary) | Inter | 16pt | 700 | 1.0 | 0pt | Green "Write a review", "Book now" |
| Button (Secondary) | Inter | 15pt | 600 | 1.0 | 0pt | Outline / text buttons |
| Meta / Count | Inter | 13pt | 400 | 1.3 | 0pt | "1,284 reviews", "0.4 mi", "$$ - $$$" |
| Tab Label | Inter | 11pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Badge | Inter | 11pt | 700 | 1.0 | 0.4pt | "TRAVELERS' CHOICE", "#1 of 240" |
| Caption | Inter | 12pt | 400 | 1.35 | 0pt | Photo credit, "Photo by traveler" |

### Principles
- **Weights concentrated at 400 / 600 / 700**: regular, semibold, bold — no thin, no black
- **Compact utilitarian scale**: discovery is browse-heavy; titles cap at 28pt
- **Negative tracking only on large titles**: -0.4pt max; body sits at 0pt for legibility
- **Number-dense metadata in gray**: review counts and distances always `#6B6B6B`
- **Green is never body text** — green is structural (action, score, badge) only

## 4. Component Stylings

### Buttons

**Primary CTA Pill ("Write a review" / "Book now")**
- Background: `#34E0A1`
- Text: `#000000` (intentional black for max contrast on the bright mint)
- Padding: 14pt vertical, 28pt horizontal
- Corner radius: 999pt (full pill)
- Font: Inter 16pt weight 700
- Pressed: background `#21C589`, scale 0.97, with `.impactOccurred(.light)` haptic
- Disabled: background `#D9D9D9`, text `#9B9B9B`

**Secondary Outline Button ("Save" / "Directions")**
- Background: transparent
- Text: `#000000`
- Border: 1pt solid `#000000`
- Padding: 12pt vertical, 24pt horizontal
- Corner radius: 999pt
- Font: Inter 15pt weight 600
- Pressed: background `#F2F2F2`

**Save Heart (Add to Trip)**
- Size: 24pt glyph, 44pt hit area
- Default: outline `#FFFFFF` with `rgba(0,0,0,0.35)` fill (over photos)
- Active: solid `#34E0A1`
- Bounce on activate: scale 1.0 → 1.2 → 1.0 over 280ms with success haptic

**Text Button ("See all 240 hotels")**
- Font: Inter 15pt weight 600
- Color: `#000000`
- Underline on press only, 44pt hit area

### Cards & Containers

**Place Card (List / Search Result)**
- Background: `#FFFFFF`
- Corner radius: 16pt on the photo, card itself flush
- Photo: full-width, aspect 3:2, traveler image with rounded corners
- Save heart top-right over photo (24pt)
- Photo dots bottom-center if multiple images (6pt, active `#FFFFFF`)
- Structure: photo → "Travelers' Choice" badge (if awarded) → place name (17pt w700) → bubble rating + count → category · price tier → distance
- Tap: scale 0.98 with light haptic; whole card tappable

**Category Tile (Home Grid)**
- Size: 2-column grid, ~168pt tall, 12pt gap
- Background: `#F2F2F2`
- Corner radius: 16pt
- Icon: 32pt centered illustrative glyph (owl-green accents)
- Label: Inter 16pt weight 600 `#000000` below icon
- Examples: "Hotels", "Things to do", "Restaurants", "Flights", "Vacation Rentals", "Cruises"

**Place Detail Hero**
- Height: 280pt
- Full-bleed traveler photo with bottom scrim `linear-gradient(transparent, rgba(0,0,0,0.55))`
- Venue name 24pt w700 white over scrim, bottom-left
- Bubble rating + review count in white below name
- Floating back chevron + share + save heart in `rgba(0,0,0,0.35)` circular 36pt buttons
- Photo count pill bottom-right: "1 / 248" on `rgba(0,0,0,0.5)`

**Review Card**
- Background: `#FFFFFF`, divider `#E0E0E0` bottom
- Reviewer avatar 40pt circle + name 15pt w600 + "12 contributions" 12pt `#6B6B6B`
- Bubble rating row (20pt bubbles) + review date
- Title 16pt w700, body 15pt w400 (4-line clamp + "Read more")
- Helpful vote row: thumbs-up outline + count

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: `rgba(255,255,255,0.96)` with `.regularMaterial` blur
- Tabs: Explore, Search, Trips, Review, More
- Icon size: 24pt
- Active: `#34E0A1` icon + label (green is the active indicator here)
- Inactive: `#6B6B6B`
- Labels: Inter 11pt w600, always shown
- Active icon: filled SF Symbol variant; inactive: outlined
- Hairline top border `#E0E0E0`

**Top Nav (Home)**
- Height: 52pt + safe area
- Owl logomark left (28pt), location pill center ("New York City ▾"), profile avatar right (28pt circle)
- Search bar pinned below: full-width, `#F2F2F2` fill, 48pt tall, 999pt radius
- Becomes compact + blurred on scroll

**Place Detail Sticky Bar**
- Appears on scroll past hero: 52pt, white with blur
- Back chevron + venue name (17pt w700, truncated) + share/save

### Input Fields

**Search Bar**
- Background: `#F2F2F2`
- Height: 48pt
- Corner radius: 999pt (full pill)
- Leading SF Symbol `magnifyingglass` 18pt `#6B6B6B`
- Placeholder: "Hotels, things to do, restaurants…", 15pt w400 `#6B6B6B`
- Focus: background `#FFFFFF`, 1pt border `#000000`, trailing "Cancel" text button appears

**Filter Chip**
- Background: `#F2F2F2` default / `#34E0A1` selected
- Text: `#000000` (both states — green is bright enough)
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 999pt
- Font: Inter 14pt weight 600
- Leading icon optional (e.g., funnel for "Filters")
- Examples: "Best Value", "Traveler Ranked", "Distance", "$ - $$"

### Distinctive Components

**Five-Circle Bubble Rating**
- Five circles in a row, each 16pt (lists) / 20pt (detail) / 24pt (review form)
- Filled: solid `#34E0A1`; empty: solid `#D9D9D9`
- Half-bubble: 50% green clip for partial scores (e.g., 4.5)
- Spacing: 2pt between circles
- Trailing review count in 13pt `#6B6B6B`: "4.5 (1,284)"
- In the review-write flow: tappable, with bubble-fill animation + haptic per selection

**"Travelers' Choice" Badge**
- Pill: `#34E0A1` background, `#000000` text, 11pt w700 uppercase
- Optional laurel-leaf SF Symbol flanking text
- Padding: 4pt vertical, 10pt horizontal, 999pt radius
- Sits top-left over the place-card photo or above the place name

**Category Tile Grid**
- 2-column grid of `#F2F2F2` rounded tiles with illustrative icons
- The fast entry point on Home — Hotels / Things to do / Restaurants / Flights
- Tap: tile scale 0.97 + light haptic, push to category browse

**Owl Logomark**
- Two bubble-eyes (the rating circles) forming the owl's face on a black silhouette
- Used at 28pt in the home nav; never recolored — green eyes, black body

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 56
- Standard margin: 16pt horizontal, 24pt between sections
- Card-to-card vertical gap: 20pt; category tile gap: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Category tiles: 2-column grid, 12pt gutter
- Horizontal carousels ("Top experiences"): 16pt left inset, peek next card
- Review list: single column, full-width cards with hairline dividers

### Whitespace Philosophy
- **Photo-forward density**: place cards give the traveler photo a full 3:2 block — generous
- **Tight metadata stacking**: rating, category, price, distance pack into ~64pt below each photo
- **Detail hero is immersive**: 280pt photo with the venue name floating over a scrim

### Border Radius Scale
- Square (0pt): hairline dividers, section breaks
- Soft (8pt): inline thumbnails, small chips when not full-pill
- Card (16pt): place-card photos, category tiles, review media
- Comfortable (20pt): bottom sheets, modal cards
- Full Pill (999pt): primary CTA, filter chips, search bar, badges
- Circle (50%): bubble rating, avatars, floating photo-overlay buttons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, canvas, category tiles |
| Card (Level 1) | `rgba(0,0,0,0.06) 0 2px 8px` | Place cards lifted slightly off white |
| Floating Control (Level 2) | `rgba(0,0,0,0.18) 0 2px 6px` | Photo-overlay buttons (back, save, share) |
| Sheet (Level 3) | `rgba(0,0,0,0.16) 0 -8px 32px` | Bottom sheets, filter panel |
| Sticky Nav | `rgba(0,0,0,0.06) 0 1px 0` + blur | Search bar / detail bar on scroll |
| Tab Bar Material | `.regularMaterial` over `rgba(255,255,255,0.96)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Shadows are soft, low-opacity, and short-throw — on a white canvas a 0.06-opacity blur is enough to lift a card without making it heavy. The only stronger shadows are the floating controls placed over photos (0.18 opacity) so they stay legible against bright traveler images. Everything else is essentially flat — the photography supplies the visual weight, not the chrome.

### Motion
- **Bubble fill (review write)**: each tapped bubble fills left-to-right green over 150ms, with `.impactOccurred(.light)` per selection
- **Save heart**: scale 1.0 → 1.2 → 1.0 over 280ms + `.success` haptic, fill animates
- **Card tap**: scale 0.98 spring (damping 0.8), light haptic
- **Hero parallax**: detail photo scrolls 0.5× under the sticky bar on pull
- **Tab switch**: cross-fade 200ms, active icon swaps to filled variant
- **Photo carousel**: 60fps horizontal paging with snap, dot indicator updates

## 7. Do's and Don'ts

### Do
- Use `#FFFFFF` as the canvas — Tripadvisor is a bright gallery, not a tinted surface
- Reserve Tripadvisor Green (`#34E0A1`) for the primary action, active tab, and the bubble rating
- Render the five-circle bubble rating as the score unit everywhere — it is the brand signature
- Use black text (`#000000`) on green CTA buttons — the mint is bright; black maximizes contrast
- Lead place cards with full-bleed traveler photography at 3:2, 16pt corner radius
- Use the owl logomark (green bubble-eyes, black body) unaltered in the home nav
- Keep metadata (review counts, distance, price tier) in `#6B6B6B` gray
- Show the "Travelers' Choice" badge as a green pill on top-rated places
- Use light, short-throw shadows (0.06 opacity) — the white canvas needs only a hint of lift

### Don't
- Don't tint the canvas — keep it pure `#FFFFFF`, not off-white or gray
- Don't use green for body text or as a decorative accent — it is structural only
- Don't draw star ratings — Tripadvisor uses circular bubbles, never stars
- Don't use white text on the green button — black is intentional for contrast
- Don't add a second accent color — the restraint is what makes the green and bubbles iconic
- Don't recolor or skew the owl logomark
- Don't use heavy drop shadows — they fight the clean white gallery feel
- Don't use thin or black font weights — stay at 400 / 600 / 700
- Don't shrink the bubble rating below 16pt — it must stay readable as the core score

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Single-column category tiles fall to 2-col tighter; 3:2 photos shrink |
| iPhone 13/14/15 | 390pt | Standard 2-col category grid, full 3:2 place photos |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Larger photos, hero grows to 320pt |
| iPad | 768pt+ | 3-column place grid, hero photo 480pt, side-by-side detail layout |

### Dynamic Type
- Place names, review body, section headers: full scale
- Bubble rating: fixed (never scales — it is a geometric unit)
- Tab labels, badge text, photo-count pill: fixed
- Review snippets: scale with a 4-line clamp before "Read more"

### Orientation
- Explore / Search / Trips: **portrait-locked**
- Place detail photo gallery: **rotates to landscape** for full-screen photo viewing
- Maps view: **rotates to landscape**

### Touch Targets
- Primary CTA pill: 48pt+ tall — easy to hit
- Bubble rating in review form: each bubble 44pt effective hit area
- Save heart / floating controls: 44pt hit area minimum
- Tab bar icons: 24pt glyph, 44pt effective hit
- Place card: entire card tappable

### Safe Area Handling
- Top: owl nav + search bar respect the safe area / Dynamic Island
- Bottom: tab bar floats above the home indicator with blur
- Sides: 16pt content insets; hero photo bleeds full-width

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#FFFFFF`
- Surface: `#F2F2F2`
- Divider: `#E0E0E0`
- Text primary: `#000000`
- Text secondary: `#6B6B6B`
- Text tertiary: `#9B9B9B`
- Tripadvisor Green (action / score): `#34E0A1`
- Green pressed: `#21C589`
- Owl black: `#000000`
- Empty bubble: `#D9D9D9`
- Error red: `#D6122E`

### Example Component Prompts
- "Create a SwiftUI Tripadvisor five-circle bubble rating: a horizontal row of 5 circles, each 16pt, 2pt spacing. Filled circles use `#34E0A1`, empty use `#D9D9D9`, support a half-bubble for partial scores. Trailing review count in Inter 13pt weight 400 `#6B6B6B` like '4.5 (1,284)'."
- "Build a Tripadvisor place card: white card, full-width traveler photo at 3:2 with 16pt corner radius, a save heart top-right (24pt, white outline over photo). Below the photo: a green 'TRAVELERS' CHOICE' pill (`#34E0A1` bg, `#000000` text, 11pt w700 uppercase), place name 'Hotel Vista Mar' in Inter 17pt weight 700 `#000000`, then the five-circle bubble rating + '1,284 reviews', then 'Hotel · $$ - $$$ · 0.4 mi' in 13pt `#6B6B6B`. Entire card tappable, pressed scale 0.98."
- "Design the Tripadvisor primary CTA pill: full-pill button, `#34E0A1` background, label 'Write a review' in Inter 16pt weight 700 `#000000` (intentionally black on the bright mint), 14pt vertical / 28pt horizontal padding. Pressed: background `#21C589`, scale 0.97, light haptic."
- "Create the Tripadvisor home category grid: a 2-column grid of `#F2F2F2` tiles, 16pt corner radius, 12pt gap, each ~168pt tall with a centered 32pt illustrative icon and a label in Inter 16pt weight 600 `#000000` below — 'Hotels', 'Things to do', 'Restaurants', 'Flights'."
- "Build the Tripadvisor place-detail hero: 280pt full-bleed traveler photo with a bottom scrim `linear-gradient(transparent, rgba(0,0,0,0.55))`. Venue name 'Skyline Rooftop Bar' 24pt weight 700 white bottom-left over the scrim, five-circle bubble rating + '982 reviews' in white below. Floating circular 36pt back, share, and save-heart buttons on `rgba(0,0,0,0.35)`. Photo-count pill '1 / 248' bottom-right on `rgba(0,0,0,0.5)`."

### Iteration Guide
1. Canvas is pure `#FFFFFF` — a clean gallery wall, never tinted
2. Tripadvisor Green (`#34E0A1`) only for the primary action, active tab, and bubble rating — never body text
3. The five-circle bubble rating is the brand signature — bubbles, never stars
4. Black text on the green button — intentional contrast on the bright mint (`#000000` on `#34E0A1`)
5. Traveler photography is the color story — lead every place card with a full-bleed 3:2 photo
6. Keep metadata gray (`#6B6B6B`): review counts, distance, price tier
7. The owl logomark is fixed — green bubble-eyes, black body, never recolored
8. Shadows are light and short (0.06 opacity) — the white canvas only needs a hint of lift
