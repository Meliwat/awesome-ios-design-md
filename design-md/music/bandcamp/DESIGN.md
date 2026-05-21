# Design System Inspiration of Bandcamp (iOS)

## 1. Visual Theme & Atmosphere

Bandcamp's iOS app is a paper-first, editorial music marketplace whose entire identity is built on one belief: the album artwork — uploaded by the artist — is the brand, and the UI exists to get out of its way. The canvas is near-white (`#FFFFFF` with `#F4F4F4` for grouped/elevated zones), text is near-black ink (`#1A1A1A`), and the single brand color, Bandcamp Teal (`#1DA0C3`, with a deeper `#629AA9`), is rationed almost exclusively to the things that connect a fan to an artist: the **artist name link**, the **buy/support price**, and the **fan collection**. Open the app and you land on a feed of releases your friends and the people you follow have bought, but the screen everything builds toward is the **Album Page**: an enormous square cover at the very top, the album title in heavy type, "by {Artist}" in teal, a buy card with a "name your price" affordance, an inline player with a teal waveform, and a clean numbered tracklist.

The signature affordance is the **buy/support card**. Unlike a streaming app, Bandcamp's primary action is *purchase* — a bordered, paper-gray card showing a price ("$9 USD"), an "or more" / "name your price" hint, a solid teal "Buy Digital Album" button, and an "Add to wishlist" link. This commerce-forward block is the heart of Bandcamp's UX because the whole product exists to pay artists directly. The second signature is the **big square album art**: covers are full-bleed at the top of the album page and edge-to-edge in a grid in the collection — never cropped to a circle, never decorated, always 1:1. The third is the **fan collection feed**: a social, editorial stream of "{Fan} bought {Album}" cards with the cover, title, and artist, plus the buyer's note — Bandcamp is as much a fan network as a store.

The color system is deliberately minimal and warm-neutral. There is one paper canvas, one paper-gray surface, hairline gray dividers, near-black ink text, a secondary gray, and exactly one brand color (teal, with one deeper variant). There is no gradient system, no secondary accent, no dark-by-default. Album art is allowed to be as loud as the artist made it; the chrome is quiet, gray, and typographic. This restraint is the point: on a near-white editorial page, teal reads instantly as "this is the link / the price / the way you support this artist."

Typography is clean and editorial. Bandcamp's product uses a humanist sans (referenced here with **DM Sans** as the faithful fallback) at confident weights: screen and album titles at heavy 700, artist links at 600 in teal, body at 400, prices at 600, tracklist and metadata at small 400/600. Hierarchy reads like a well-set record sleeve — title, artist, release info, then a clean tracklist — not like a product dashboard. Numerals are tabular for track numbers, durations, and player times.

**Key Characteristics:**
- Paper-first canvas (`#FFFFFF` / `#F4F4F4`) — light, editorial, the artwork is the brand
- **Bandcamp Teal** (`#1DA0C3`) rationed to artist links, the buy price, and collection accents
- **Buy/Support card** — bordered paper-gray block with price + "name your price" + teal Buy button
- **Big square album art** — full-bleed 1:1 covers, never cropped to a circle, never decorated
- **Fan collection feed** — social "{Fan} bought {Album}" cards with cover, title, artist, note
- **Inline player** with a teal waveform/scrubber and tabular time
- Clean numbered tracklist — hairline-separated rows, teal for the playing track
- Editorial typography — humanist sans (DM Sans fallback) at weights 400/600/700
- Small corner radii — 4pt buttons/cards, 2–4pt art tiles; restrained, paper-like
- Hairline gray dividers (`#E2E2E2`) instead of cards/shadows wherever possible
- Bottom tab strip: Feed / Discover / Collection / Profile, teal active tint
- Dark mode is a true inversion (cool charcoal `#101417`), NOT the default

## 2. Color Palette & Roles

### Primary (Interactive)
- **Bandcamp Teal** (`#1DA0C3`): The brand color — artist links, the buy price, primary "Buy" button, active tab, playing track, collection accents.
- **Teal Deep** (`#629AA9`): Muted teal — visited links, secondary teal text, subtle accents.
- **Teal Pressed** (`#17819E`): Pressed state for the Buy button and teal fills.
- **Dark Teal** (`#3DB5D6`): Teal shifted lighter for contrast in dark mode only.

### Canvas & Surfaces (Light — primary, Bandcamp's true identity)
- **Paper** (`#FFFFFF`): Primary canvas — the album page, lists, most screens.
- **Paper Gray** (`#F4F4F4`): Grouped/elevated zones — the buy card, settings groups, search bar.
- **Hover Gray** (`#EDEDED`): Pressed/selected rows.
- **Divider** (`#E2E2E2`): Hairline separators between tracklist rows and sections.
- **Track Divider** (`#F0F0F0`): Lighter hairline inside dense tracklists.

### Canvas & Surfaces (Dark — true inversion, NOT the default)
- **Dark Canvas** (`#101417`): Cool charcoal — Bandcamp's dark canvas, slightly blue-gray.
- **Dark Surface 1** (`#1A2024`): Cards, buy card, raised zones.
- **Dark Surface 2** (`#232B30`): Hovered/pressed rows, chips.
- **Dark Divider** (`#2C353B`): Hairline separators on dark.

| Role | Light Mode (primary) | Dark Mode |
|------|----------------------|-----------|
| Canvas | `#FFFFFF` | `#101417` |
| Grouped Surface | `#F4F4F4` | `#1A2024` |
| Pressed Surface | `#EDEDED` | `#232B30` |
| Divider | `#E2E2E2` | `#2C353B` |
| Text Primary | `#1A1A1A` | `#EDF1F3` |
| Text Secondary | `#767676` | `#93A1A8` |
| Brand Teal | `#1DA0C3` | `#3DB5D6` |

### Text
- **Ink** (`#1A1A1A`): Primary text — album titles, track names, body — a near-black, not pure black.
- **Text Secondary** (`#767676`): Release info, durations, captions, secondary metadata.
- **Text Tertiary** (`#9A9A9A`): Track numbers, very low-emphasis labels, disabled.
- **Link Teal** (`#1DA0C3`): Artist names, tags, in-text links, "Add to wishlist".

### Commerce
- **Buy Teal** (`#1DA0C3`): The "Buy Digital Album" / "Buy Now" button background.
- **Price Ink** (`#1A1A1A`): The price figure ("$9") in heavy weight.
- **NYP Gray** (`#767676`): "or more" / "name your price" / "pay what you want" hint text.
- **Owned Teal** (`#1DA0C3`): "In your collection" / purchased badge.

### Semantic
- **Success Green** (`#4CAF50`): Purchase complete, "added to collection" confirm.
- **Error Red** (`#E45858`): Payment failed, download error, destructive confirm.
- **Warning Amber** (`#E0A030`): Low-quality stream / preview-only notice.

## 3. Typography Rules

### Font Family
- **Primary**: Bandcamp's product humanist sans (a clean grotesque-humanist face used across web + app).
- **Faithful Fallback**: `DM Sans` (by Colophon Foundry, SIL OFL) — geometric-humanist, low-contrast, near-identical editorial feel; the recommended substitute.
- **System Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for track numbers, durations, prices, player times (`font-variant-numeric: tabular-nums`).

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | DM Sans | 32pt | 700 | 1.15 | -0.6pt | "Collection", "Feed" page titles |
| Album Title | DM Sans | 22pt | 700 | 1.2 | -0.3pt | Album/track title on the album page |
| Section Header | DM Sans | 22pt | 700 | 1.25 | -0.2pt | "Recently collected", shelf titles |
| Artist Link | DM Sans | 18pt | 600 | 1.3 | 0pt | "by {Artist}" — always teal `#1DA0C3` |
| Subhead | DM Sans | 18pt | 600 | 1.3 | -0.1pt | Card group titles, sheet headers |
| Body | DM Sans | 16pt | 400 | 1.55 | 0pt | Descriptions, buyer notes, paragraph copy |
| Price | DM Sans | 22pt | 700 | 1.1 | -0.2pt | The "$9" figure in the buy card |
| Price Unit | DM Sans | 13pt | 600 | 1.2 | 0pt | "USD", "or more" |
| Row / Collection Title | DM Sans | 15pt | 600 | 1.3 | 0pt | Collection card title, list primary line |
| Meta | DM Sans | 14pt | 400 | 1.4 | 0pt | "Released June 4 · 10 tracks", durations |
| Track Name | DM Sans | 14pt | 500 | 1.35 | 0pt | Tracklist row (700 + teal when playing) |
| Track Number / Caption | DM Sans | 12pt | 600 | 1.0 | 0.3pt | Track index, small captions |
| Tab Label | DM Sans | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Button | DM Sans | 15pt | 700 | 1.0 | 0pt | Buy / primary buttons |

### Principles
- **Editorial, record-sleeve rhythm**: Title → artist → release info → tracklist, set like a printed sleeve — not a product dashboard.
- **Teal is for connection**: Artist links and the price are teal; everything informational is ink/gray. Don't tint generic UI teal.
- **Confident but not loud**: Titles at 700, artist/price at 600–700; body at a comfortable 400 with generous 1.55 line height for buyer notes and descriptions.
- **Tabular numerals**: Track numbers, durations, prices, and player times all use tabular figures so columns align.
- **Dynamic Type**: Titles, artist link, body, prices scale; tab labels, track numbers, captions stay fixed (layout-sensitive).

## 4. Component Stylings

### Buttons

**Primary Buy Button (Buy Digital Album / Buy Now)**
- Shape: Rectangle, 4pt corner radius (restrained — paper, not pill)
- Background: `#1DA0C3` (Bandcamp Teal)
- Text: `#FFFFFF`, DM Sans 15pt weight 700
- Padding: 13pt vertical, full-width inside the buy card
- Pressed: background `#17819E` + scale 0.99
- Disabled: teal at 40% opacity, text `#FFFFFF` at 60%

**Outline Button (Follow / Buy Track)**
- Background: transparent
- Border: 1pt `#1DA0C3`
- Text: `#1DA0C3`, DM Sans 14pt weight 600
- Corner radius: 4pt
- Padding: 11pt vertical, 20pt horizontal
- Pressed: background `#1DA0C3` at 8%, text/border to `#17819E`

**Secondary Button (Share / Gift)**
- Background: `#F4F4F4` (light) / `#232B30` (dark)
- Border: 1pt `#E2E2E2` (light) / `#2C353B` (dark)
- Text: `#1A1A1A` / `#EDF1F3`, DM Sans 14pt weight 600
- Corner radius: 4pt
- Pressed: background `#EDEDED`

**Text Button / Link (Add to wishlist / artist link)**
- Text: `#1DA0C3`, DM Sans 14pt weight 600
- Underline on inline links; no underline on standalone actions
- Pressed: color `#17819E`

### Core Atoms

**Big Square Album Art**
- Aspect ratio: strictly 1:1, never cropped to a circle
- Album page: full-bleed at the top edge, 0pt corner radius (touches the screen edges)
- Collection / feed grid: 2–4pt corner radius, no border, no shadow
- Loading: a flat `#F4F4F4` / `#1A2024` placeholder square (no shimmer gradient)
- The art is never tinted, darkened, or overlaid with chrome — content is sovereign

**Buy / Support Card (signature)**
- Background: `#F4F4F4` (light) / `#1A2024` (dark)
- Border: 1pt `#E2E2E2` / `#2C353B`
- Corner radius: 6pt
- Padding: 16pt all
- Contents: price row (`$9` in DM Sans 22pt w700 ink + "USD" 13pt w600 gray, "or more" right-aligned), a "name your price · pay what you want" hint (12pt `#767676`), a full-width teal Buy button (4pt radius), and an "Add to wishlist" teal text link centered below

**Inline Player Bar**
- Layout: 36pt circular ink play button + track title (13pt w600 ink) + "elapsed / total" (11pt `#767676` tabular) + a small teal waveform/scrubber
- Borders: 1pt `#EAEAEA` top and bottom (a banded strip, not a card)
- The waveform bars are teal `#1DA0C3`; the played portion is solid, the unplayed portion at 35% opacity

**Tracklist Row**
- Height: ~42pt; layout: track number (12pt `#9A9A9A` tabular, 18pt column) + track name (14pt w500 ink) + duration (12pt `#767676` tabular)
- Divider: 1pt `#F0F0F0` between rows (none on last)
- Playing: track name turns `#1DA0C3` weight 700
- Pressed: row background `#EDEDED`

**Collection Card (fan feed)**
- Square art (2–4pt radius) + title (13pt w600 ink, 1 line) + artist (12pt `#767676`, 1 line)
- Often grouped 2-up or 3-up in a row; the feed adds a "{Fan} bought this" line and an optional buyer note above the card

### Navigation

**Bottom Tab Bar**
- Height: 50pt + safe area
- Background: `#FFFFFF` (light) / `#101417` (dark), 1pt top border `#EAEAEA` / `#2C353B`
- Tabs (4): Feed, Discover, Collection, Profile
- Icon size: 22pt; active fills solid, inactive is stroked
- Active color: `#1DA0C3` (teal) icon + label
- Inactive color: `#9A9A9A` / `#5F6E75`
- Labels: DM Sans 10pt weight 600, always shown

**Top Navigation (Album page)**
- Height: 44pt + safe area
- Leading: back chevron, 22pt `#1A1A1A`
- Center: compact "Album" / context title, DM Sans 13pt w700
- Trailing: ⋯ overflow (share, gift, report), 22pt `#1A1A1A`
- No large translucent blur — a clean white bar with a hairline bottom border on scroll

### Input Fields

**Search Bar**
- Height: 40pt, background `#F4F4F4` / `#1A2024`, corner radius 6pt, no border
- Leading magnifier 16pt `#767676`; placeholder "Artists, albums, tracks…" 14pt `#767676`
- Focus: 1pt `#1DA0C3` border

**Name-Your-Price Field**
- An inline numeric field inside the buy flow: `#FFFFFF` field, 1pt `#E2E2E2` border, 4pt radius, leading "$", DM Sans 16pt ink
- Helper: "Minimum $X — pay what you want" in 12pt `#767676`

### Distinctive Components

**Album Page (signature screen)**
- Full-bleed 1:1 cover at the very top → album title (22pt w700 ink) → "by {Artist}" (18pt w600 teal) → release meta (14pt gray) → the bordered buy/support card → the banded inline player → the hairline-separated numbered tracklist. Editorial, top-to-bottom, no hero gradient.

**Buy / Support Card**
- The commerce heart of Bandcamp: price + "name your price" + a solid teal Buy button + wishlist link. This is the primary CTA on every release — Bandcamp exists to pay artists directly.

**Fan Collection Feed**
- A social editorial stream: "{Fan} bought {Album}" with the cover, title, artist, and the fan's note. Bandcamp is a fan network as much as a store; the collection is identity, not just history.

**Inline Teal Waveform Player**
- A banded strip (not a card) with a teal waveform/scrubber and tabular times — restrained, paper-like, lets you preview before you buy.

**Big Square Art Everywhere**
- 1:1 covers, never circular, never decorated, never tinted — the artwork the artist uploaded is the visual identity of every screen.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 48, 64
- Screen horizontal inset: 20pt (album body, lists)
- Album art: full-bleed (0pt inset) at the top of the album page
- Tracklist row vertical padding: 9pt; section gap: 24pt

### Grid & Container
- iPhone: single column, 20pt side insets for text; full-bleed art
- Collection: 2-up grid on phone (3-up on Plus/Max), 12pt gutter, square cells
- iPad: content max-width 720pt centered; collection 4–5-up grid
- Album page is a single editorial column, never multi-column

### Whitespace Philosophy
- **Paper, not panels**: the page is white with hairline dividers — content flows like a record sleeve, not stacked cards
- **The art owns the top**: the cover is full-bleed and the single largest element on the album page
- **Generous reading measure**: buyer notes and descriptions get 1.55 line height and a comfortable 20pt inset
- **Borders over shadows**: zones (the buy card) use a 1pt hairline border, not a drop shadow — keep the page flat and editorial

### Border Radius Scale
- Square (0pt): full-bleed album art on the album page, dividers
- Hairline (2pt): collection grid art tiles
- Small (4pt): buttons, art tiles, name-your-price field
- Card (6pt): the buy/support card, search bar
- Comfortable (12pt): bottom sheets, modals
- Pill (500pt): rarely used — small filter chips only
- Circle (50%): the inline play button, avatars

## 6. Depth & Elevation

Bandcamp is flat and editorial. It does not use drop shadows as a style — zones are defined by hairline borders and surface-color steps. The only elevation is the system bottom sheet.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow; `#FFFFFF` | Page content, tracklist, album art |
| Bordered (Level 1) | 1pt `#E2E2E2` border, no shadow | Buy card, search bar, grouped zones |
| Banded (Level 1) | 1pt top+bottom hairline | Inline player strip |
| Sheet (Level 2) | `rgba(0,0,0,0.12) 0 -2px 16px` | Bottom sheets (share, gift, payment) |
| Modal Overlay | `rgba(0,0,0,0.35)` | Dim behind modals & sheets |

**Shadow Philosophy**: Bandcamp uses borders and surface steps, not shadows, to organize the page — appropriate for an editorial, paper-like marketplace. The only place a shadow appears is the system bottom sheet (share/gift/checkout), where it signals "this is a temporary layer, tap outside to dismiss." Album art never gets a shadow — it sits flat on the page, sovereign.

### Motion
- **Page navigation**: native iOS push, 300ms horizontal slide; album art shared-element scales up from the feed tile when available
- **Buy button press**: scale 0.99 + background `#1DA0C3` → `#17819E` over 100ms; soft haptic on tap
- **Add to collection / wishlist**: a quick teal check or heart fill (150ms ease-out) + soft haptic; toast "Added to collection"
- **Player scrub / waveform**: 1:1 finger tracking on the waveform; played bars fill teal as time advances; no spring, just linear progress
- **Tracklist row tap**: row highlights `#EDEDED` for 120ms, then the player updates and the track name turns teal
- **Bottom sheet (checkout/share)**: slides up 300ms ease-out with the modal dim fading in
- **Tab switch**: instant content swap; active icon fills + tints teal with a 120ms ease
- **Pull to refresh (feed)**: standard iOS refresh; new collection cards fade in
- **Haptics**: soft impact on Buy tap, add-to-collection, and wishlist toggle

## 7. Do's and Don'ts

### Do
- Use paper-white (`#FFFFFF` / `#F4F4F4`) as the canvas — Bandcamp is light and editorial
- Make album art big, square (1:1), and full-bleed on the album page — the art is the brand
- Reserve Bandcamp Teal (`#1DA0C3`) for artist links, the buy price, the Buy button, and collection accents
- Lead every release with a clear buy/support card — price + "name your price" + teal Buy button
- Show the fan collection as a social feed ("{Fan} bought {Album}" + note) — it's a network, not history
- Use hairline gray dividers (`#E2E2E2`) and surface steps instead of shadows and cards
- Set the page like a record sleeve: title → artist (teal) → release info → tracklist
- Use small corner radii (4pt buttons, 2–4pt art) — restrained and paper-like
- Make the playing track teal + 700 weight in the tracklist
- Use tabular numerals for track numbers, durations, prices, and player times
- Invert to a true dark charcoal (`#101417`) for dark mode — shift teal to `#3DB5D6`

### Don't
- Don't go dark by default — Bandcamp's identity is the white editorial page
- Don't crop album art to a circle or decorate/tint it — covers are always square and untouched
- Don't tint generic UI chrome teal — teal means "artist link / price / support"
- Don't hide or de-emphasize the price — purchasing is the primary action, not a streaming play
- Don't use a streaming-style giant gradient hero — the album page is editorial and flat
- Don't use drop shadows to separate content — use hairline borders and surface steps
- Don't use pill-shaped buttons everywhere — Bandcamp's geometry is restrained (4pt)
- Don't use pure black text — `#1A1A1A` ink is correct
- Don't add a second brand accent or a gradient system — there is one teal (plus one deep variant)
- Don't bury the collection — it's the fan's identity and a primary tab
- Don't dim or overlay album art for "contrast" — content stays sovereign; only chrome dims in dark mode

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Collection 2-up; buy card and player at 16pt insets |
| iPhone 13/14/15 | 390pt | Standard layout, 20pt text insets, full-bleed art |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in the top nav |
| iPhone 15/16 Pro Max | 430pt | Collection 3-up; larger album art |
| iPad (portrait) | 768pt | Content max-width 720pt centered; collection 4-up |
| iPad (landscape) | 1024pt+ | Two-pane: collection grid left, album detail right; 5-up grid |

### Dynamic Type
- Screen/album titles, artist link, body, prices, track names: full scale
- Tab labels, track numbers, captions, price unit: FIXED (layout-sensitive)
- Album art and waveform are graphic, unaffected by type scale

### Orientation
- iPhone album page is portrait-friendly (editorial column); rotation supported
- iPad supports landscape split (collection grid + album detail)
- Album art maintains 1:1 aspect in all orientations

### Touch Targets
- Buy button: full-width, 13pt vertical padding (≥ 44pt tall)
- Inline play button: 36pt visual, 44pt hit
- Tracklist row: full-row tappable (~42pt)
- Tab bar items: 22pt glyph, 48pt hit
- Artist link / wishlist: 44pt hit area around the text

### Safe Area Handling
- Top: top nav respects safe area + Dynamic Island; album art may extend under the status bar with the nav floating
- Bottom: tab bar and any mini player respect the home indicator
- Bottom sheets (checkout/share) extend to the safe area with internal padding
- Sides: 20pt content inset for text; album art is full-bleed

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (light): `#FFFFFF`
- Grouped surface: `#F4F4F4`
- Divider: `#E2E2E2`
- Text primary (ink): `#1A1A1A`
- Text secondary: `#767676`
- Bandcamp Teal: `#1DA0C3` (artist link, price, Buy button, active tab, playing track)
- Teal deep: `#629AA9`
- Teal pressed: `#17819E`
- Canvas (dark): `#101417`
- Dark surface: `#1A2024`
- Dark divider: `#2C353B`
- Dark teal: `#3DB5D6`
- Success: `#4CAF50`
- Error: `#E45858`

### Example Component Prompts
- "Build the Bandcamp album page in SwiftUI: full-bleed 1:1 album art at the very top (0pt corner radius, touches screen edges). Below at 20pt insets: album title in DM Sans 22pt w700 `#1A1A1A`, then 'by {Artist}' in DM Sans 18pt w600 `#1DA0C3`, then release meta 'Released June 4 · 10 tracks' in DM Sans 14pt `#767676`. Then the buy/support card."
- "Create the Bandcamp buy/support card: `#F4F4F4` background, 1pt `#E2E2E2` border, 6pt corner radius, 16pt padding. Top row: '$9' in DM Sans 22pt w700 `#1A1A1A` + 'USD' 13pt w600 `#767676`, with 'or more' right-aligned 13pt `#767676`. Below: 'Name your price · Pay what you want' 12pt `#767676`. Then a full-width 'Buy Digital Album' button — `#1DA0C3`, white DM Sans 15pt w700, 4pt radius, 13pt vertical padding, pressed `#17819E`. Below, centered: an 'Add to wishlist' teal text link."
- "Render the Bandcamp inline player: a banded strip with 1pt `#EAEAEA` top and bottom borders. Leading 36pt circular `#1A1A1A` play button (white ▶ glyph). Then track title DM Sans 13pt w600 `#1A1A1A` over '1:42 / 5:08' DM Sans 11pt `#767676` tabular. Trailing: a small teal waveform — `#1DA0C3` bars, played portion solid, unplayed at 35% opacity."
- "Build a Bandcamp tracklist: rows ~42pt, each = track number (DM Sans 12pt `#9A9A9A` tabular, 18pt column) + track name (DM Sans 14pt w500 `#1A1A1A`) + duration (DM Sans 12pt `#767676` tabular). 1pt `#F0F0F0` divider between rows. The currently-playing track's name is `#1DA0C3` weight 700. Pressed row background `#EDEDED`."
- "Create a Bandcamp collection card: square art (1:1, 4pt corner radius, no border/shadow) + title DM Sans 13pt w600 `#1A1A1A` (1 line) + artist DM Sans 12pt `#767676` (1 line). Group 2-up on phone with a 12pt gutter; in the feed add a '{Fan} bought this' line above."
- "Create the Bandcamp bottom tab bar: 50pt + safe area, `#FFFFFF`, 1pt top border `#EAEAEA`. Tabs: Feed, Discover, Collection, Profile — 22pt icons, labels DM Sans 10pt w600. Active: teal `#1DA0C3` filled icon + label; inactive: `#9A9A9A` stroked."

### Iteration Guide
1. Canvas is paper-white (`#FFFFFF` / `#F4F4F4`) — Bandcamp is light and editorial; dark is a true inversion, NOT the default
2. Album art is big, strictly square (1:1), full-bleed on the album page, never circular/decorated/tinted
3. Bandcamp Teal `#1DA0C3` is scarce — artist links, the buy price, the Buy button, active tab, playing track
4. Every release leads with a buy/support card: price + "name your price" + teal Buy button + wishlist link
5. The fan collection is a social feed ("{Fan} bought {Album}" + note), a primary tab, the fan's identity
6. Use hairline gray borders (`#E2E2E2`) and surface steps — NOT shadows or cards — to organize the page
7. Typography is editorial record-sleeve rhythm: title (700) → artist (600 teal) → meta (gray) → tracklist
8. Geometry is restrained: 4pt buttons, 2–4pt art tiles, 6pt the buy card — no pills, no big radii
9. The playing track is teal + 700 in the tracklist; numerals are tabular everywhere
10. Text is `#1A1A1A` ink, never pure black; in dark mode invert to `#101417` / `#EDF1F3` and shift teal to `#3DB5D6` — art stays full-color
