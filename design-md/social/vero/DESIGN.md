# Design System Inspiration of Vero (iOS)

## 1. Visual Theme & Atmosphere

Vero's iOS app is the anti-feed. Its entire reason to exist is a reaction against algorithmic timelines, engagement-bait, and infinite ad walls — so the design philosophy is *calm, chronological, and deliberate*. The canvas is pure true-black (`#000000`), not a charcoal, because Vero presents content like a gallery wall: an OLED-deep void around full-bleed photography so the image is the only light in the room. There are no trending modules, no "for you" reshuffling, no read-receipts pressure. The feed is **strictly chronological — newest first, never reordered** — and that promise is itself a design principle: nothing in the UI implies an algorithm is choosing for you.

The signature creation moment is the **7 post-type selector**. Unlike single-purpose social apps, Vero treats a "post" as one of seven distinct, first-class media types — **Photo, Video, Link, Music, Film, Book, Place** — each with its own icon, its own composer, and its own card rendering in the feed. A book post shows a cover + author + star rating; a music post shows album art + track; a place post shows a pin + map; a film post shows a poster + your rating. This typed-post system is the structural soul of Vero and the most recognizable interaction: the horizontal row of seven icons that opens the compose flow. The second signature surface is the **chronological feed itself** — uniform, quiet post cards separated by hairline `#232325` dividers, each headed by a circular avatar, the author's name, and a small colored post-type chip that tells you *what kind* of thing you're looking at before you read a word.

Color is austere and singular. There is exactly one brand expression: a **teal→blue gradient** (`#00D1C1 → #0079D3`) that appears on the wordmark, the primary "Post" CTA, the compose ring in the tab bar, and active states. A flat teal (`#00C2B8`) carries small accents — the liked heart, the active post-type icon, links. There is no second brand color; the only other saturated hue is a restrained camera-amber (`#E8B23A`) for star ratings on book/film posts, nodding to Vero's photographer-and-film-buff audience. Text is pure white (`#FFFFFF`) for names and titles, a soft gray (`#9A9AA0`) for captions, and a quiet gray (`#5E5E64`) for metadata. Typography is **Manrope** — a clean, modern geometric sans with a slightly humanist warmth — sized for unhurried reading: 18pt author names, 14pt captions, generous line height, nothing aggressive.

**Key Characteristics:**
- True-black canvas (`#000000`) — OLED-deep, gallery-wall presentation; surfaces lift only to `#0E0E10` / `#161618`
- **Strictly chronological feed** — newest first, never reordered, no algorithm, no trending — a design promise
- **7 first-class post types** — Photo / Video / Link / Music / Film / Book / Place — each with its own icon, composer, and card
- Post-type chip on every post — a small teal label that says *what kind* of content before you read it
- Single brand expression — a teal→blue gradient (`#00D1C1 → #0079D3`) on wordmark, Post CTA, compose ring, active states
- Flat teal (`#00C2B8`) for small accents — liked heart, active type icon, links
- Camera-amber (`#E8B23A`) star ratings — the only other saturated hue, for book/film posts
- Manrope typography sized for unhurried reading — 18pt author, 14pt caption, generous leading
- Uniform quiet post cards separated by hairline `#232325` dividers — content, not chrome
- A center compose action in the tab bar with the gradient ring — opens the 7-type picker

## 2. Color Palette & Roles

### Primary (Interactive)
- **Brand Gradient** (`#00D1C1 → #0079D3`, 135°): The single brand expression — wordmark, primary "Post" CTA, compose ring, gradient fills.
- **Vero Teal** (`#00C2B8`): Flat accent — liked hearts, active post-type icons, active tab tint, links, focus rings.
- **Vero Blue** (`#0098E6`): Secondary flat accent / the blue end of the gradient when a flat blue is needed.
- **Gradient Stop A** (`#00D1C1`): Teal end (top-left).
- **Gradient Stop B** (`#0079D3`): Blue end (bottom-right).
- **Teal Pressed** (`#00A89F`): Pressed state for flat teal elements.

### Canvas & Surfaces (Dark — primary mode)
- **True Black** (`#000000`): The app background — pure black, gallery void, OLED-deep.
- **Surface 1** (`#0E0E10`): Embedded cards (book/music/place post inner cards), sheets — barely lifted.
- **Surface 2** (`#161618`): Composer fields, pickers, pressed list rows.
- **Divider** (`#232325`): Hairline separators between posts and sections — the primary structural line.
- **Overlay Scrim** (`rgba(0,0,0,0.6)`): Dim behind modals/sheets.

### Canvas & Surfaces (Light — secondary, rare)
Vero is true-black-first; a light mode exists for accessibility/parity.

| Role | Light Mode | Dark Mode |
|------|-----------|-----------|
| Canvas | `#FFFFFF` | `#000000` |
| Surface 1 | `#F6F6F7` | `#0E0E10` |
| Surface 2 | `#ECECEE` | `#161618` |
| Divider | `#E4E4E6` | `#232325` |
| Text Primary | `#0B0B0C` | `#FFFFFF` |
| Text Secondary | `#6A6A70` | `#9A9AA0` |

### Text
- **Text Primary** (`#FFFFFF`): Author names, post titles, screen titles — pure white.
- **Text Secondary** (`#9A9AA0`): Captions, descriptions, body — soft gray.
- **Text Tertiary** (`#5E5E64`): Timestamps, counts, metadata, inactive tab labels — quiet gray.
- **Text on Gradient** (`#FFFFFF`): On the teal→blue gradient (white reads well on both ends).

### Accent & Ratings
- **Camera Amber** (`#E8B23A`): Star ratings on Book and Film posts only — a nod to the film-buff audience. Never a generic warning.
- **On-Amber Ink** (`#1A1206`): Text/glyph on amber chips.

### Semantic
- **Success** (`#2ECC71`): Post-published toast, "Connected" confirmation.
- **Error** (`#FF4D4F`): Failed post, destructive confirm, validation.
- **Warning** (`#E8B23A`): Reuses Camera Amber for soft warnings (consistent palette discipline).
- **Connection Teal** (`#00C2B8`): "Connection request" / relationship-tier accents (Vero's Close Friends / Friends / Acquaintances / Followers model).

## 3. Typography Rules

### Font Family
- **Primary**: `Manrope` (by Mikhail Sharanda / Mirko Velimirović, SIL OFL) — a clean modern geometric sans with subtle humanist warmth and excellent screen rendering. Closest free Google Font to Vero's calm, modern product type.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for counts and timestamps so the post action row doesn't shift.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Display | Manrope | 32pt | 800 | 1.15 | -0.4pt | Top-level "Feed" / wordmark scale |
| Screen Title | Manrope | 26pt | 700 | 1.2 | -0.3pt | "Discover", section landings |
| Section Header | Manrope | 22pt | 700 | 1.25 | -0.2pt | "People you follow" |
| Post Author | Manrope | 18pt | 700 | 1.3 | -0.1pt | Author name in the post head (often shown 14pt in compact lists) |
| Post Author (Compact) | Manrope | 14pt | 700 | 1.3 | 0pt | Author name in feed card |
| Title | Manrope | 15pt | 600 | 1.35 | 0pt | Book/film/music titles inside typed cards |
| Caption / Body | Manrope | 14pt | 500 | 1.5 | 0pt | Post captions, descriptions |
| Body (Reading) | Manrope | 14pt | 400 | 1.6 | 0pt | Long captions, comments |
| Metadata | Manrope | 13pt | 400 | 1.4 | 0pt | "Photo · 14m ago", counts |
| Button | Manrope | 15pt | 700 | 1.0 | 0.2pt | Primary/secondary buttons |
| Type Label | Manrope | 11pt | 600 | 1.0 | 0.4pt | Post-type chip / selector labels |
| Star Rating | Manrope | 11pt | 600 | 1.0 | 1.0pt | ★★★★★ amber rating row |
| Tab Label | Manrope | 10pt | 500 | 1.0 | 0pt | Bottom tab labels |
| Overline | Manrope | 11pt | 600 | 1.0 | 0.4pt | Uppercase section overlines |

### Principles
- **Unhurried reading**: type is calm — author at 14–18pt, captions at 14pt with 1.5 line height, never crowded.
- **Weight for hierarchy**: 700 for names/titles, 500 for captions, 400 for long reading; 800 reserved for the single Display.
- **Metadata recedes**: 13pt `#5E5E64` keeps "Photo · 14m" present but never competing with the image.
- **The type chip is meaningful**: the 11pt teal post-type label is a functional signpost, not decoration.
- **Dynamic Type**: titles, captions, body scale; type labels, tab labels, star ratings stay fixed (layout-critical).

## 4. Component Stylings

### Buttons

**Primary Button (Post / Publish)**
- Shape: Full pill, 999pt corner radius
- Background: linear gradient `#00D1C1 → #0079D3` (135°)
- Text: `#FFFFFF`, Manrope 15pt weight 700
- Padding: 13pt vertical, 28pt horizontal
- Pressed: `filter: brightness(0.94)` + scale 0.98
- Disabled: gradient at 40% opacity, text at 60%

**Outline Button (Connect / Follow primary)**
- Background: transparent
- Border: 1.5pt `#00C2B8`
- Text: `#00C2B8`, Manrope 14pt weight 700
- Padding: 11pt vertical, 22pt horizontal
- Corner radius: 999pt
- Pressed: background `rgba(0,194,184,0.10)`

**Ghost / Surface Button (Follow secondary)**
- Background: `#161618`
- Border: 1pt `#232325`
- Text: `#FFFFFF`, Manrope 14pt weight 600
- Corner radius: 999pt (Vero favors full pills)
- Pressed: background `#232325`

**Text Button (See all post types)**
- Text: `#00C2B8`, Manrope 14pt weight 600
- No background, no underline
- Pressed: 70% opacity

### Core Atoms

**Avatar**
- Circle, 38pt in feed (28pt in lists, 80pt on profile)
- Fallback: brand gradient `#00D1C1 → #0079D3`
- Relationship ring (optional): 1.5pt — teal for Close Friends, gray otherwise

**Post Card**
- No card border or background — posts sit directly on `#000000`, separated only by a 0.5pt `#232325` divider
- Padding: 16pt horizontal, 16pt top / 14pt bottom
- Media corner radius: 10pt
- This "no card chrome" choice is core to the gallery-wall feel

**Post-Type Chip (in post head)**
- Inline, no background
- Leading 11pt type icon + label, both `#00C2B8`
- Manrope 10–11pt weight 700
- Followed by " · {timestamp}" in `#5E5E64`

**Typed Inner Card (Book / Music / Film / Place)**
- Background: `#0E0E10`, 1pt `#232325` border, 10pt radius, 12pt padding
- Book: 56×80pt cover + title (15pt w600) + author (12pt `#9A9AA0`) + amber stars
- Music: 56×56pt album art + track + artist + play glyph
- Place: map thumbnail + place name + teal pin
- Film: poster + title + your rating (amber stars)

**Media (Photo / Video)**
- Aspect ratio: respects original (commonly 4:5 portrait), 10pt corners
- Video: teal play affordance center, duration chip bottom-right
- Never algorithmically cropped — Vero respects the photographer's frame

### Navigation

**Top Bar**
- Height: 44pt + safe area
- Leading: "VERO" wordmark — gradient-filled text, letter-spaced
- Trailing: search glyph + notifications bell, 21pt `#FFFFFF`, 1.8pt stroke
- Background: `#000000`, 0.5pt `#232325` bottom border only when scrolled

**Post-Type Selector Row (under top bar OR as compose sheet)**
- Horizontal row of all 7 types: Photo, Video, Link, Music, Film, Book, Place
- Each: 19pt icon + 9–11pt label, evenly distributed
- Inactive: `#5E5E64`; active/selected: `#00C2B8`
- 0.5pt `#232325` bottom border

**Bottom Tab Bar**
- Height: 50pt + safe area (66pt visual)
- Background: `rgba(0,0,0,0.96)` + `saturate(180%) blur(20px)`
- Top border: 0.5pt `#232325`
- 5 slots: Feed, Discover, **Compose (center, gradient ring)**, Inbox, Profile
- Icon size: 22pt (Compose center 27pt with gradient stroke ring)
- Active color: `#00C2B8` (icons fill); inactive: `#5E5E64`
- Labels: Manrope 10pt weight 500 (Compose label blank — icon-only)

**Profile Header**
- 80pt avatar, name 22pt weight 700, relationship-tier control (Close Friends / Friends / Acquaintances / Followers)
- Tab strip: "Posts", "Collections", "About" — underlined 2pt teal active

### Input Fields

**Search Field**
- Height: 44pt
- Background: `#161618`
- Border: none (focus: 1.5pt `#00C2B8`)
- Corner radius: 999pt (pill search) or 12pt
- Leading magnifier 16pt `#5E5E64`
- Placeholder: "Search people, posts" Manrope 14pt `#5E5E64`

**Caption Composer**
- Background: `#000000` (full-screen compose) with `#232325` field outline
- Text Manrope 16pt `#FFFFFF`
- Placeholder varies by type: "Write a caption…", "What did you think of this book?"

### Distinctive Components

**7 Post-Type Selector (the signature)**
- Compose entry shows a grid/row of seven equally weighted cells: Photo, Video, Link, Music, Film, Book, Place
- Cell: icon (20pt) + label (10pt w600), `#0E0E10` bg, 1pt `#232325` border, 12pt radius
- Selected cell: border `#00C2B8`, content `#00C2B8`, background `rgba(0,194,184,0.08)`
- This is the structural heart of Vero — all seven are first-class, none is "primary"

**Chronological Feed Promise**
- The feed is strictly newest-first; surface a quiet "Caught up" / chronological indicator rather than any "suggested for you" rail
- No engagement-driven reordering, no algorithmic insertions

**Post Action Row**
- Like (heart), Comment (bubble), Share (tray) — 19pt icons `#9A9AA0`, count Manrope 12pt w600
- Liked: heart fills `#00C2B8`
- Evenly spaced with 22pt gaps, no background

**Relationship Tier Selector** (Vero's signature privacy model)
- When following: choose Close Friends / Friends / Acquaintances / Followers
- A segmented or list control; selected tier highlighted teal
- Governs who sees each post — surfaced at post time too

**Compose Ring (tab bar center)**
- A circle with a gradient stroke (`#00D1C1 → #0079D3`) and a `+` glyph
- Tapping opens the 7-type selector
- The brand gradient's most prominent placement

**Book / Film Rating Stars**
- Five stars, filled in Camera Amber `#E8B23A`
- Manrope-spaced, 11pt, 1pt letter-spacing
- The only amber in the product — film-buff/reader signal

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 14, 16, 20, 24, 32, 40, 48
- Screen horizontal inset: 16pt
- Post top padding 16pt / bottom 14pt; media-to-caption gap 12pt
- Post-type selector cell gap: 10pt

### Grid & Container
- iPhone: single column, 16pt insets, full-width media
- Posts: no card frame — full-bleed within insets, divided by hairlines
- iPad: single centered column (max-width ~620pt) — Vero stays single-stream even on large screens (anti-grid, pro-reading)
- Profile collections: 3-up thumbnail grid

### Whitespace Philosophy
- **Gallery wall**: generous black around every image; the photo is the light source
- **No card chrome**: posts are separated by hairlines, not boxes — content is the interface
- **One brand expression, used sparingly**: the gradient appears on the wordmark, Post CTA, and compose ring only
- **Calm rhythm**: uniform post structure, consistent quiet metadata — nothing competes for attention

### Border Radius Scale
- Square (0pt): dividers, full-bleed media edges
- Subtle (4pt): book/album-cover thumbnails, small tags
- Standard (8pt): small chips
- Media (10pt): photo/video, typed inner cards
- Comfortable (14pt): composer surfaces, pickers
- Sheet (20pt): bottom sheets, modals
- Pill (500pt): all buttons, search, type chips — Vero strongly favors pills
- Circle (50%): avatars, the compose ring

## 6. Depth & Elevation

Vero is profoundly flat — true-black with hairline structure. There is essentially no elevation in content; depth exists only for transient floating surfaces.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow, hairline `#232325` only | Posts, feed, profile content |
| Inset Card (Level 0.5) | `#0E0E10` fill + 1pt `#232325` border, no shadow | Book/music/film/place inner cards |
| Floating (Level 1) | `rgba(0,0,0,0.6) 0 8px 24px` + 1pt `#232325` | Context menus, type picker popover, toasts |
| Sheet (Level 2) | `rgba(0,0,0,0.7) 0 -6px 28px` + 20pt top radius | Compose sheet, share sheet, relationship picker |
| Modal Scrim | `rgba(0,0,0,0.6)` | Dim behind sheets/modals |

**Shadow Philosophy**: On a true-black canvas, shadows are invisible — so Vero structures the screen with *hairlines and barely-lifted surfaces* (`#0E0E10`), never elevation. Floating elements get a heavy `0.6–0.7` shadow plus a `#232325` hairline so they're distinguishable against the void. The product's flatness is intentional: a calm, document-like surface with no Material-style layering.

### Motion
- **Post-type select**: cell border + content tint to teal over 160ms ease-out; soft haptic
- **Compose sheet**: slides up 300ms ease-out from the gradient ring; scrim fades in parallel
- **Like**: heart scales 1.0 → 1.2 → 1.0 over 240ms spring, fills `#00C2B8`; soft haptic
- **Feed refresh**: pull triggers a teal spinner; new posts insert at top with a 220ms fade (never reshuffle existing)
- **Tab switch**: cross-fade content 200ms; active icon tints teal instantly
- **Relationship tier change**: segmented selection slides 180ms; selected pill tints teal
- **Image load**: cross-fade from `#0E0E10` placeholder to image over 220ms
- **Connect/Follow**: button label cross-fades 180ms; outline → filled state
- **Haptics**: soft impact on post-type select, like, connect; selection tick on tab change

## 7. Do's and Don'ts

### Do
- Use pure true-black (`#000000`) as the canvas — Vero is a gallery wall, OLED-deep
- Keep the feed strictly chronological, newest-first — it's a design promise, not just a setting
- Treat all 7 post types (Photo/Video/Link/Music/Film/Book/Place) as equal, first-class citizens
- Show the teal post-type chip on every post so the *kind* of content is legible before reading
- Use the teal→blue gradient (`#00D1C1 → #0079D3`) sparingly — wordmark, Post CTA, compose ring, active
- Separate posts with hairline `#232325` dividers — no card boxes
- Use flat teal (`#00C2B8`) for small accents — liked hearts, active icons, links
- Reserve Camera Amber (`#E8B23A`) for book/film star ratings only
- Favor full pills (999pt) for buttons, search, chips
- Keep type calm — Manrope, 14pt captions with 1.5 line height, unhurried

### Don't
- Don't use charcoal (`#0E0E10` is for inset cards, not the canvas) — the canvas is pure `#000000`
- Don't add a "trending", "for you", or "suggested" rail — Vero is explicitly anti-algorithm
- Don't reorder the feed by engagement — newest-first, always; new posts insert at top
- Don't privilege one post type over the others — all seven are co-equal
- Don't add a second brand color — there is only the teal→blue gradient and flat teal
- Don't reuse Camera Amber for warnings or decoration — it means "rating", nothing else
- Don't wrap posts in card boxes/shadows — hairline dividers on true-black only
- Don't use heavy drop shadows on content — they're invisible on black; use hairlines + barely-lifted surfaces
- Don't crowd captions — 14pt with generous 1.5 leading; calm over dense
- Don't crop photos algorithmically — respect the photographer's original frame

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Post-type selector scrolls horizontally if cramped; 14pt author |
| iPhone 13/14/15 | 390pt | Standard layout — all 7 type icons fit the row |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top bar |
| iPhone 15/16 Pro Max | 430pt | Larger media; 38pt avatars; comfortable 7-type row |
| iPad (portrait) | 768pt | Single centered stream (max ~620pt) — NOT a grid; Vero stays single-column |
| iPad (landscape) | 1024pt+ | Same centered single stream; optional left rail for nav only |

### Dynamic Type
- Scales: screen titles, author names, captions, body, descriptions
- Fixed: post-type labels, tab labels, star ratings, type chips (layout-critical)
- Long captions expand inline with a "more" toggle

### Orientation
- Portrait-first (a reading/gallery app)
- Landscape supported; media scales to fit, single stream maintained
- iPad Split View supported; stream stays centered and single-column

### Touch Targets
- Post-type selector cell: full cell tappable, ≥ 56pt
- Tab bar items: 22pt glyph, 48pt hit; compose ring 27pt glyph / 56pt hit
- Like/Comment/Share: 44pt hit each
- Buttons: ≥ 36pt tall (pills)

### Safe Area Handling
- Top: top bar + Dynamic Island respected
- Bottom: tab bar + home indicator respected; compose sheet respects bottom inset
- Sides: 16pt content inset; media full-bleed within insets
- Keyboard: caption composer rises above keyboard; feed scrolls clear

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#000000`
- Surface 1: `#0E0E10`
- Surface 2: `#161618`
- Divider: `#232325`
- Text primary: `#FFFFFF`
- Text secondary: `#9A9AA0`
- Text tertiary / metadata: `#5E5E64`
- Brand gradient: `#00D1C1 → #0079D3` (135°)
- Vero Teal (flat accent): `#00C2B8`
- Vero Blue: `#0098E6`
- Teal pressed: `#00A89F`
- Camera Amber (ratings only): `#E8B23A`
- Success: `#2ECC71` · Error: `#FF4D4F`

### Example Component Prompts
- "Build the Vero 7 post-type selector in SwiftUI: a row (or 4×2 grid) of seven equally weighted cells — Photo, Video, Link, Music, Film, Book, Place. Each cell: a 20pt SF Symbol + a Manrope 10pt weight 600 label, `#0E0E10` background, 1pt `#232325` border, 12pt corner radius, content color `#5E5E64`. The selected cell: border `#00C2B8`, content `#00C2B8`, background `rgba(0,194,184,0.08)`. No type is primary — all seven are co-equal. Tapping a cell tints it over 160ms ease-out with a soft haptic."

- "Create a Vero feed post card: no card box — it sits directly on `#000000`, separated from the next post only by a 0.5pt `#232325` bottom divider, 16pt horizontal padding, 16pt top / 14pt bottom. Head: 38pt circular avatar (brand-gradient `#00D1C1 → #0079D3` fallback), author name Manrope 14pt weight 700 `#FFFFFF`, and a meta line: a teal post-type chip (11pt icon + 10pt weight 700 label, both `#00C2B8`) followed by ' · 14m' in `#5E5E64`, with a trailing '···' more glyph. Media at 10pt corner radius respecting original aspect. Caption Manrope 14pt weight 500 `#9A9AA0`, 1.5 line height. Action row: Like / Comment / Share, 19pt `#9A9AA0` icons with 12pt counts, 22pt gaps; liked heart fills `#00C2B8`."

- "Build a Vero Book post inner card: `#0E0E10` background, 1pt `#232325` border, 10pt corner radius, 12pt padding. Left: a 56×80pt cover (4pt radius). Right: title Manrope 15pt weight 600 `#FFFFFF`, author Manrope 12pt `#9A9AA0`, and a 5-star rating row in Camera Amber `#E8B23A` (Manrope 11pt, 1pt letter-spacing)."

- "Create the Vero bottom tab bar: 50pt + safe area, background `rgba(0,0,0,0.96)` with `saturate(180%) blur(20px)`, 0.5pt `#232325` top border. Five slots: Feed, Discover, Compose (center — a 27pt circle with a 2pt gradient stroke `#00D1C1 → #0079D3` and a `+` glyph, no label), Inbox, Profile. Inactive icons 22pt `#5E5E64`; active icon fills `#00C2B8`. Labels Manrope 10pt weight 500. Tapping Compose opens the 7 post-type selector sheet."

- "Create the Vero primary 'Post' button: a full pill (999pt radius) filled with a 135° linear gradient `#00D1C1 → #0079D3`, white Manrope 15pt weight 700 text, 13pt vertical / 28pt horizontal padding. Pressed: brightness 0.94 + scale 0.98. Disabled: gradient at 40% opacity."

- "Build the Vero 'VERO' wordmark: uppercase Manrope weight 800, ~2pt letter-spacing, filled with the 135° teal→blue gradient `#00D1C1 → #0079D3` (gradient-clipped text), left-aligned in the top bar."

### Iteration Guide
1. Canvas is PURE true-black `#000000` — NOT charcoal, NOT `#0E0E10` (that's for inset cards) — a gallery void
2. The feed is strictly chronological — newest-first, never reordered; no "for you", no trending — this is a design promise
3. There are exactly 7 first-class post types (Photo/Video/Link/Music/Film/Book/Place) — none is primary, all co-equal
4. Every post shows a teal post-type chip so the *kind* of content is legible before reading
5. There is ONE brand expression: the teal→blue gradient `#00D1C1 → #0079D3` — wordmark, Post CTA, compose ring, active
6. Flat teal `#00C2B8` carries small accents — liked hearts, active icons, links
7. Camera Amber `#E8B23A` is for book/film star ratings ONLY — never a warning or decoration
8. Posts have NO card box — hairline `#232325` dividers on true-black; content is the interface
9. Favor full pills (999pt) for buttons, search, chips; Manrope at calm, unhurried sizes
10. No heavy shadows — they're invisible on black; structure with hairlines + barely-lifted `#0E0E10` surfaces
