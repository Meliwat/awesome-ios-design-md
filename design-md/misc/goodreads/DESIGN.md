# Design System Inspiration of Goodreads (iOS)

## 1. Visual Theme & Atmosphere

Goodreads' iOS app feels like a well-worn personal library rendered on a screen. The defining gesture of the entire product is a wall of book covers — full-color, photographic jacket art arranged in tight grids and horizontal shelves — sitting on a warm, paper-like tan canvas (`#F4F1EA`). The chrome around those covers is deliberately quiet and bookish: a deep literary brown (`#382110`) for primary surfaces and shelf headers, a serif-leaning headline voice, and a single saturated amber (`#E9A100`) reserved almost exclusively for the five-star rating. Nothing competes with the covers. The app's job is to make a reader's library, their friends' libraries, and the global catalog feel browsable, social, and personal — so the cover is the hero and the UI is the bookshelf it rests on.

The signature screen is the **book detail page**: a large cover thumbnail at top-left, the title set in a heavy serif, the author as a teal link, the global average rating with a row of amber stars and a ratings/reviews count, a prominent green **"Want to Read"** shelf button with a split secondary control (Rate / Review / Shelve), a "Rate this book" star strip, a serif description with a "…more" expander, and then an endless feed of community reviews — each with an avatar, name, their own star rating, and review prose set in a comfortable reading serif. The second signature surface is **My Books / shelves**: the reader's own library filtered by shelf (Read, Currently Reading, Want to Read, plus custom shelves), shown as a cover grid or a sortable list with per-book star ratings.

The color system is intentionally narrow and warm. Brown and tan do the structural work; amber does the rating work; a forest green (`#409D69`) marks the primary "add to shelf" action; a desaturated teal (`#00635D`) is the link/author color. There is no rainbow — Goodreads is a reading app, and its palette evokes paper, ink, and a gold star sticker on a book report. Reviews and descriptions are typeset for sustained reading: a transitional serif at 15pt with a generous 1.6 line-height, because people read long reviews here. UI labels, buttons, and metadata use a clean grotesque sans so they stay crisp at small sizes and never compete with the editorial serif.

**Key Characteristics:**
- Warm tan paper canvas (`#F4F1EA`) light / warm ink-brown (`#161310`) dark — bookish, never clinical
- Literary brown (`#382110`) for shelf headers, primary brand surfaces, the logomark
- Full-color book covers in tight grids and horizontal shelves — the cover is the hero element
- Amber five-star rating (`#E9A100`) — the single load-bearing accent, on every cover, review, and the rate strip
- Serif headline + body voice (book titles, descriptions, reviews) paired with a UI sans for chrome
- Green "Want to Read" / shelf CTA (`#409D69`) — the primary action on every book
- Shelf model: Read / Currently Reading / Want to Read + unlimited custom shelves
- Community reviews feed: avatar + name + their stars + serif prose, infinite scroll
- Teal author/link color (`#00635D`) — quiet, never blue
- Reading progress (percent / page) on Currently Reading books and the Reading Challenge widget
- Minimal bottom tab bar: Home / My Books / Browse / Updates / Profile

## 2. Color Palette & Roles

### Primary (Brand & Interactive)
- **Goodreads Brown** (`#382110`): Primary brand surface — shelf headers, logomark background, nav accents, "Shelve" button.
- **Brown Deep** (`#58371F`): Pressed brown, gradient stop, avatar fill.
- **Tan Paper** (`#F4F1EA`): The signature canvas / "page" color in light mode; text-on-brown color.
- **Shelf Green** (`#409D69`): Primary "Want to Read" / add-to-shelf CTA.
- **Shelf Green Pressed** (`#348355`): Pressed state of the green CTA.
- **Link Teal** (`#00635D`): Author names, hyperlinks, "see all reviews", inline links.

### Rating
- **Rating Amber** (`#E9A100`): The five-star rating fill — light AND dark, identical. The brand's load-bearing color.
- **Star Rail (empty)** (`#D8D2C4` light / `#4A3F2C` dark): Unfilled star track.

### Canvas & Surfaces (Light Mode)
- **Tan Paper** (`#F4F1EA`): Primary canvas — warm, paper-like.
- **Card White** (`#FFFFFF`): Book cards, review cards lifted off the tan.
- **Surface Subtle** (`#EBE6DA`): Hovered/pressed rows, segmented control track.
- **Divider** (`#DDD6C7`): Hairline rules between reviews and list rows.

### Canvas & Surfaces (Dark Mode)
- **Dark Canvas** (`#161310`): Primary dark canvas — warm ink-brown, NOT pure black.
- **Dark Surface 1** (`#211C16`): Cards, review blocks, the rate strip.
- **Dark Surface 2** (`#2C261D`): Pressed rows, segmented track, want-shelf pill.
- **Dark Divider** (`#3A3226`): Hairline rules on dark.
- **Brown-on-Dark Accent** (`#C9883D`): Brown reads as muddy on dark, so the active tab/accent shifts to a warm tan-gold.
- **Link Teal (Dark)** (`#6FB3AD`): Lightened link/author color for dark contrast.

### Text
- **Text Primary Light** (`#382110`): Body & titles on tan — warm brown-black, not pure black.
- **Text Secondary Light** (`#6B5E47`): Metadata, ratings count, captions.
- **Text Tertiary Light** (`#988B6F`): Disabled, low-emphasis hints.
- **Text Primary Dark** (`#EDE6D8`): Primary text on dark canvas.
- **Text Secondary Dark** (`#B3A88F`): Secondary text on dark.
- **Text Tertiary Dark** (`#7C725E`): Disabled / overline on dark.

### Semantic
- **Error Red** (`#D9534F`): Remove-from-shelf confirm, error toast.
- **Success Green** (`#409D69`): "Added to your Want to Read shelf" toast (reuses the shelf green).
- **Reading Progress** (`#409D69`): Progress bar fill on Currently Reading.
- **Warning Amber** (`#E9A100`): Reuses rating amber for non-blocking warnings.

## 3. Typography Rules

### Font Family
- **Headline / Editorial**: `Merriweather` (Sorkin Type, Google Fonts, SIL OFL) — a sturdy transitional serif designed for screen reading; used for book titles, section headers, descriptions, and review prose. Goodreads' product voice is editorial — titles and reviews are typeset like a book page.
- **UI / Chrome**: `Lato` (Łukasz Dziedzic, SIL OFL) — a warm humanist sans; used for buttons, labels, metadata, tab bar, and all small-size chrome.
- **iOS Fallback**: When custom faces are not bundled, serif falls back to `Georgia` / `'Times New Roman'`; sans falls back to `-apple-system, SF Pro Text`.
- **Rationale**: People read long reviews and synopses on Goodreads, so editorial content uses a true reading serif; functional chrome uses a clean grotesque so it never competes with the prose.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | Merriweather | 30pt | 900 | 1.2 | -0.3pt | "My Books", "Home" large nav title |
| Book Title (detail) | Merriweather | 23pt | 900 | 1.22 | -0.2pt | The hero title on book detail |
| Section Header | Merriweather | 19pt | 700 | 1.3 | 0pt | "Readers also enjoyed", shelf names |
| Subsection | Merriweather | 17pt | 700 | 1.35 | 0pt | "Community Reviews", "About the author" |
| Button | Lato | 17pt | 900 | 1.0 | 0.1pt | "Want to Read", primary CTAs |
| Body / Review / Synopsis | Merriweather | 15pt | 400 | 1.6 | 0pt | The long-form reading text |
| Author / Bold Label | Lato | 14pt | 700 | 1.3 | 0pt | Author name, list labels |
| Metadata | Lato | 13pt | 400 | 1.4 | 0pt | Ratings count, page count, dates |
| Link | Lato | 13pt | 700 | 1.4 | 0pt | "See all reviews", inline teal links |
| Caption / Review meta | Lato | 12pt | 400 | 1.4 | 0pt | "1,204 reviews · 38 followers" |
| Overline | Lato | 11pt | 700 | 1.2 | 0.6pt | "CURRENTLY READING" eyebrow, all-caps |
| Tab Label | Lato | 10pt | 700 | 1.0 | 0.1pt | Bottom tab labels |
| Star Caption | Lato | 12pt | 700 | 1.0 | 0pt | "it was amazing", rating tooltip text |

### Principles
- **Serif reads, sans operates**: any text the user *reads* (titles, synopsis, reviews) is Merriweather; any text the user *acts on* (buttons, labels, tabs) is Lato.
- **Generous reading rhythm**: body/review text uses 1.6 line-height — reviews are long and must stay comfortable.
- **Heavy serif headings**: book titles and screen titles are weight 900 — they should feel like an embossed jacket title.
- **Numerals are sans**: ratings counts, page counts, percentages always use Lato for tabular clarity.
- **Dynamic Type first-class**: titles, section headers, body, review prose scale; tab labels, overlines, star captions stay fixed.

## 4. Component Stylings

### Buttons

**Primary Shelf Button ("Want to Read")**
- Shape: Rounded rectangle, 4pt corner radius, full content width
- Height: 44pt
- Background: `#409D69` (Shelf Green) / same on dark
- Text: `#FFFFFF`, Lato 17pt weight 900
- Leading icon (when shelved): `checkmark` 16pt white
- Pressed: background `#348355` + scale 0.98
- Shelved state: label becomes the shelf name (e.g., "Read"), background stays green with a check; tapping opens the shelf-change sheet

**Secondary Brown Button ("Shelve ▾")**
- Background: `#382110` (Brown) / `#C9883D` text-inverted on dark
- Text: `#F4F1EA` (Tan), Lato 14pt weight 700
- Padding: 12pt vertical, 22pt horizontal
- Corner radius: 4pt
- Trailing chevron `▾` indicates it opens a sheet

**Outline Button ("Write a Review")**
- Background: transparent
- Border: 1pt `#DDD6C7` light / `#3A3226` dark
- Text: `#382110` light / `#EDE6D8` dark, Lato 13pt weight 700
- Corner radius: 4pt
- Pressed: background `#EBE6DA` / `#2C261D`

**Text / Link Button ("See all 412,556 reviews")**
- Font: Lato 13pt weight 700
- Color: `#00635D` light / `#6FB3AD` dark
- No underline; underline on press
- Used for "…more", "see all editions", "see all reviews"

**Segmented Control (Rate / Review / Shelve)**
- Track: 1pt `#DDD6C7` border, 4pt radius, divided into equal segments
- Inactive segment: Lato 12pt weight 700 `#6B5E47`
- Active segment text: `#E9A100` (amber) with no fill — selection by color, not pill

### Book Cover (the core atom)

The cover is Goodreads' fundamental visual unit.
- Aspect ratio: ~2:3 (e.g., 104×158pt on detail, 96×144pt in grids, 56×84pt in list rows)
- Corner radius: 3pt (books have nearly square corners)
- Shadow: `rgba(0,0,0,0.18) 0 4px 10px` light / `rgba(0,0,0,0.55) 0 6px 16px` dark
- Spine hint: a 6pt dark gradient strip on the left edge for realism
- Always full-color photographic jacket art; never tinted or desaturated by the UI
- In grids: 3 columns on iPhone with 12pt gutters; horizontal shelves scroll with 12pt spacing

### Star Rating

**Display rating (read-only)**
- Five 15pt amber stars; partial fills use a clipped half-star for fractional averages
- Filled `#E9A100`; empty `#D8D2C4` light / `#4A3F2C` dark
- Adjacent: average number in Lato 14pt weight 700, then count in 13pt secondary

**Interactive "Rate this book"**
- Five 28pt stars in a centered strip inside a card (`#FFFFFF` / `#211C16`, 6pt radius)
- Tap or drag to set; lit stars animate fill left-to-right with a soft scale-bounce
- Selecting a rating reveals an optional "Add a review" expander below
- Label above: "Rate this book" in Lato 11pt weight 700 all-caps `#6B5E47`

### Shelf Status Pill
- Pill, 999pt radius, Lato 13pt weight 700
- **Read**: background `#382110`, text `#F4F1EA`
- **Currently Reading**: background `#409D69` @ 18% , text `#409D69`, 1pt border `#409D69` @ 40%
- **Want to Read**: background `#EBE6DA` / `#2C261D`, text `#6B5E47` / `#B3A88F`, 1pt divider border

### Navigation

**Top Nav Bar**
- Height: 46pt + safe area
- Leading: back chevron + previous-screen label in Link Teal, Lato 15pt weight 700
- Trailing: share icon + add/network icon, 18pt, `#6B5E47` / `#B3A88F`
- Background: tan `#F4F1EA` / dark `#161310`, 0.5pt bottom divider
- Large title collapses from Merriweather 30pt to a centered 17pt on scroll

**Bottom Tab Bar**
- Height: 50pt + safe area
- 5 tabs: Home, My Books, Browse, Updates, Profile
- Icon size: 23pt; labels Lato 10pt weight 700, always shown
- Active: `#382110` light / `#C9883D` dark (filled glyph); inactive: `#988B6F` / `#7C725E`
- No tint pill — active state is glyph fill + color
- Background: tan / dark with 0.5pt top divider

### Input Fields

**Search Bar (Browse)**
- Height: 40pt
- Background: `#FFFFFF` / `#211C16`, 1pt `#DDD6C7` / `#3A3226` border
- Corner radius: 8pt
- Leading `magnifyingglass` 16pt `#988B6F`
- Placeholder: "Search books, authors, ISBN" Lato 14pt `#988B6F`
- Focus: 1.5pt `#382110` / `#C9883D` border

**Review Composer**
- Full-screen sheet; star strip pinned at top, multiline `TextEditor` in Merriweather 15pt 1.6 line-height
- "Post" primary green button in nav bar trailing
- Spoiler toggle + "I'm currently on page __" progress field

### Distinctive Components

**Book Detail Header**
- Cover (104×158pt, 3pt radius, shadow) at leading
- Trailing column: serif title 23pt/900, author teal link 14pt/700, star row + average + count, format/pages/year meta line

**Want-to-Read CTA Block**
- Full-width green button + split segmented control beneath (Rate / Review / Shelve)
- This is the single most important interactive moment in the app

**Community Review Card**
- Avatar circle 36pt (brown gradient initials fallback), name Lato 14pt/700, "1,204 reviews · 38 followers" caption
- The reviewer's own 13pt amber star row
- Review prose in Merriweather 13–15pt, 1.55 line-height, "…more" expander
- Like / Comment row beneath in Lato 12pt teal
- Separated by an 8pt `#FFFFFF`/`#211C16` block, not a hairline

**Reading Challenge Widget (Home)**
- Card with circular progress ring (amber `#E9A100` on `#D8D2C4` rail)
- "You've read 24 of 50 books" in Merriweather 17pt; "+ Update progress" teal link

**Currently-Reading Progress Bar**
- 4pt rail, fill `#409D69`, percent label Lato 12pt; "on page 142 of 304"

**Shelf Picker Sheet**
- Bottom sheet, 12pt top radius
- Exclusive shelves (radio): Read / Currently Reading / Want to Read
- Custom shelves (checkbox list) below a divider
- "Done" green button pinned bottom

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48
- Screen content inset: 16pt horizontal
- Review block separation: 8pt solid surface band (not a hairline)
- Cover grid gutter: 12pt

### Grid & Container
- iPhone: 16pt insets; cover grids are 3-up with 12pt gutters
- Horizontal shelves: edge-bleed scroll, 16pt leading inset, 12pt inter-cover spacing
- iPad: content max-width 760pt centered; cover grids expand to 5–6 columns
- List rows: 56×84pt cover thumbnail + text column, 88pt row height

### Whitespace Philosophy
- **Covers breathe in a tight grid**: the art is dense but gutters keep each jacket distinct
- **Reviews get reading room**: 16pt internal padding, 1.6 line-height — long prose must not feel cramped
- **Brown bands organize**: shelf headers and section dividers use brown/tan tone shifts instead of heavy borders
- **One accent only**: amber appears for ratings; everything else is brown/tan/teal/green — restraint is the brand

### Border Radius Scale
- Square (0pt): Full-bleed banners, dividers
- Cover (3pt): Book covers — nearly square, like real books
- Standard (4pt): Buttons, segmented controls, cards-on-tan
- Comfortable (6–8pt): Rate card, search field, info cards
- Sheet (12pt): Bottom sheets, modals
- Pill (999pt): Shelf status pills, filter chips
- Circle (50%): Avatars, progress rings

## 6. Depth & Elevation

Goodreads uses shadow sparingly and warmly — the book cover is the one element allowed a real drop shadow because physical books cast shadows.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Tan canvas, list rows, review prose |
| Cover (Level 1) | `rgba(0,0,0,0.18) 0 4px 10px` light / `rgba(0,0,0,0.55) 0 6px 16px` dark | Book covers — the only always-shadowed element |
| Card (Level 2) | `rgba(56,33,16,0.08) 0 2px 8px` | Rate-this-book card, Reading Challenge widget |
| Sheet (Level 3) | `rgba(0,0,0,0.18) 0 -4px 24px` | Shelf picker, review composer bottom sheets |
| Scrim | `rgba(0,0,0,0.4)` | Behind modals and sheets |

**Shadow Philosophy**: Shadows say "this is a physical book on a shelf." The cover shadow is warm-tinted (brown-black, not neutral grey) to sit naturally on tan paper. UI cards get only a faint lift; the canvas itself is flat. On dark mode shadows deepen to 0.55 opacity so covers still detach from `#161310`.

### Motion
- **Shelve action**: tapping "Want to Read" → green button does a 1.0→0.96→1.0 spring (180ms) and a soft success haptic; a "Added to Want to Read" toast slides up 250ms ease-out
- **Star rating set**: tapping the Nth star fills stars 1..N left-to-right with a 60ms stagger, each a small scale-bounce (1.0→1.2→1.0, 200ms spring); medium haptic on commit
- **"…more" expander**: description height 0→auto over 220ms ease-out; chevron not used (text link only)
- **Cover tap → detail**: iOS native push, 300ms; cover does a subtle shared-element scale into the detail header
- **Pull to refresh (Updates feed)**: brown spinner, standard iOS rubber-band
- **Reading Challenge ring**: on appear, ring sweeps from 0 to current percent over 700ms ease-out
- **Tab switch**: instant; selected glyph fills with a 120ms crossfade
- **Haptic feedback**: soft impact on shelve, medium on star commit, light on pull-to-refresh trigger
- **Auto-save (progress update)**: silent; a small "Saved" toast only on manual update

## 7. Do's and Don'ts

### Do
- Use warm tan paper (`#F4F1EA`) as the light canvas — Goodreads is a library, not a dashboard
- Use warm ink-brown (`#161310`) for dark mode — NOT true black
- Reserve amber (`#E9A100`) almost exclusively for the five-star rating — it is the load-bearing signal
- Render book covers full-color with a 3pt radius and a real (warm-tinted) drop shadow
- Set book titles, synopses, and reviews in a reading serif (Merriweather) at 1.6 line-height for body
- Make "Want to Read" the dominant green CTA on every book detail
- Use the shelf model (Read / Currently Reading / Want to Read + custom) consistently
- Use teal (`#00635D`) for author names and links — quiet, never bright blue
- Show ratings count and reviews count in sans numerals next to the stars
- Keep chrome bookish: brown bands and tan tone-shifts instead of heavy borders

### Don't
- Don't use pure white as the light canvas — the warm tan paper IS the brand
- Don't use pure black for dark mode or for text — warm brown-black `#382110` is correct
- Don't tint, desaturate, or crop book covers with UI overlays — the jacket art is sacred
- Don't introduce a second saturated accent — amber is the only star; green is only for shelving
- Don't set long review text in a UI sans — reading content is serif
- Don't use blue for links — Goodreads links are teal
- Don't add a Material-style tint pill to the active tab — it is glyph fill + color only
- Don't round covers heavily — books have nearly square (3pt) corners
- Don't bury the "Want to Read" action — it is the primary conversion on every book
- Don't animate aggressively — motion is gentle, 180–300ms, library-quiet
- Don't show stars in any color but amber — half-stars clip the same amber, never grey-fill a partial

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Cover grid 3-up at 100pt covers; detail cover 88×134pt |
| iPhone 13/14/15 | 390pt | Standard layout; detail cover 104×158pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island respected by top nav |
| iPhone 15/16 Pro Max | 430pt | Cover grid 4-up; larger detail cover 112×170pt |
| iPad (portrait) | 768pt | Content max-width 760pt centered; cover grid 5-up |
| iPad (landscape) | 1024pt+ | 2-column: shelf sidebar + content; cover grid 6-up |

### Dynamic Type
- Book titles, section headers, body, review prose: full scale
- Tab labels, overlines, star captions, shelf pills: FIXED (layout-sensitive)
- Star glyph size scales modestly with body text

### Orientation
- All screens rotate
- iPad landscape adds a persistent shelf sidebar (Read / Currently Reading / Want to Read / custom)
- Cover grids reflow column count to device width

### Touch Targets
- Cover tap target: full cover + 8pt padding
- Star (interactive): 28pt glyph, 44pt hit slop per star
- Tab bar icons: 23pt glyph, 49pt hit
- Primary CTA: 44pt tall minimum
- Shelf pill / segmented segment: 32pt tall minimum

### Safe Area Handling
- Top: nav respects Dynamic Island and safe area
- Bottom: tab bar + home indicator respected
- Keyboard: review composer scrolls above keyboard; star strip stays pinned
- Sides: 16pt content inset

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#F4F1EA`
- Canvas (dark): `#161310`
- Brand brown: `#382110`
- Tan / text-on-brown: `#F4F1EA`
- Rating amber: `#E9A100`
- Shelf green (CTA): `#409D69`
- Link / author teal: `#00635D` light / `#6FB3AD` dark
- Card (light): `#FFFFFF` / (dark) `#211C16`
- Text primary: `#382110` light / `#EDE6D8` dark
- Text secondary: `#6B5E47` light / `#B3A88F` dark
- Divider: `#DDD6C7` light / `#3A3226` dark
- Brown-on-dark accent: `#C9883D`
- Error: `#D9534F`

### Example Component Prompts
- "Build the Goodreads book detail header in SwiftUI: leading book cover 104×158pt, 3pt corner radius, `rgba(0,0,0,0.18) 0 4px 10px` shadow, 6pt dark gradient spine strip on the left edge. Trailing column: title in Merriweather 23pt weight 900 `#382110`; author in Lato 14pt weight 700 `#00635D`; a row of five 15pt amber `#E9A100` stars (empty `#D8D2C4`) with average '4.01' in Lato 14pt/700 and '3,284,991 ratings' in Lato 13pt `#6B5E47`; a meta line 'Fiction · 304 pages · 2020'."

- "Create the Goodreads 'Want to Read' CTA block: a full-width 44pt button, 4pt radius, background `#409D69`, label 'Want to Read' in Lato 17pt weight 900 white; on shelve, prepend a 16pt white checkmark and a 1.0→0.96→1.0 spring (180ms) + soft success haptic. Below it, an equal-3 segmented control 'Rate / Review / Shelve ▾' with a 1pt `#DDD6C7` border, 4pt radius; inactive segments Lato 12pt/700 `#6B5E47`, active segment text `#E9A100` with no fill."

- "Render the Goodreads interactive star rating: five 28pt stars centered in a card (`#FFFFFF` light / `#211C16` dark, 6pt radius, 14pt padding). On tap/drag of the Nth star, fill stars 1..N amber `#E9A100` left-to-right with a 60ms stagger, each scale-bounce 1.0→1.2→1.0 over 200ms spring; empty stars `#D8D2C4` / `#4A3F2C`. Label above: 'RATE THIS BOOK' Lato 11pt weight 700 `#6B5E47`. Medium haptic on commit; reveal an 'Add a review' link below after selection."

- "Build a Goodreads community review card: 36pt circular avatar (fallback: initials on a `#58371F`→`#382110` gradient, `#F4F1EA` text), name in Lato 14pt weight 700, caption '1,204 reviews · 38 followers' in Lato 12pt `#988B6F`. Below: the reviewer's five 13pt amber stars, then review prose in Merriweather 13pt weight 400 line-height 1.55 with a '…more' teal expander. Separate cards with an 8pt solid `#FFFFFF`/`#211C16` band, not a hairline."

- "Render the Goodreads shelf status pill: 999pt radius, Lato 13pt weight 700. 'Read' = background `#382110` text `#F4F1EA`; 'Currently Reading' = background `#409D69` @ 18% text `#409D69` 1pt border `#409D69` @ 40%; 'Want to Read' = background `#EBE6DA` (dark `#2C261D`) text `#6B5E47` (dark `#B3A88F`) 1pt `#DDD6C7` border."

- "Build the Goodreads bottom tab bar: 5 tabs Home / My Books / Browse / Updates / Profile, 23pt glyphs, Lato 10pt weight 700 labels always shown. Active = filled glyph `#382110` light / `#C9883D` dark; inactive = outline glyph `#988B6F` / `#7C725E`. No tint pill — color + fill only. Tan `#F4F1EA` / dark `#161310` background, 0.5pt top divider `#DDD6C7` / `#3A3226`."

### Iteration Guide
1. Canvas is warm tan paper `#F4F1EA` in light, ink-brown `#161310` in dark — NEVER pure white or true black
2. Book covers are the hero: full-color, 3pt radius, a warm-tinted drop shadow, never UI-tinted
3. Amber `#E9A100` is the single load-bearing accent — five-star ratings only; held identical light/dark
4. "Want to Read" green `#409D69` is the dominant CTA on every book detail
5. Reading content (titles, synopses, reviews) is Merriweather serif; chrome (buttons, labels, tabs) is Lato sans
6. Body/review line-height is 1.6 — reviews are long and must stay comfortable
7. Links and author names are teal `#00635D` (lighten to `#6FB3AD` on dark) — never blue
8. Brand brown `#382110` structures the chrome; on dark it shifts to tan-gold `#C9883D` for active states
9. Shelf model is Read / Currently Reading / Want to Read + unlimited custom shelves
10. Restraint is the brand: brown, tan, teal, green, and one amber — no rainbow, ever
