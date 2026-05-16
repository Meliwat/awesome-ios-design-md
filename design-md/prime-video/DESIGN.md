# Design System Inspiration of Prime Video (iOS)

## 1. Visual Theme & Atmosphere

Prime Video's iOS app is a cinema-store hybrid built on a dark navy canvas (`#0F171E`) — not a neutral black, but Amazon's deep blue-slate that signals the brand without shouting it. The background carries a faint cool-navy temperature, and elevated surfaces (`#1A242F`, `#232F3E`) step up within the same blue-gray family so the billboard, content rows, and the store separate cleanly without visible borders. The interface is content-forward and information-rich: a large auto-still billboard leads, dense rows of artwork follow, and "Channels" and category rows mix subscription storefronts with included content. The chrome stays restrained so artwork and the famous Amazon smile-arrow accent carry the identity.

The accent system is a single confident hue. Prime Blue (`#00A8E1`) — Amazon's bright cyan-blue — owns the verb: the primary "Play" / "Resume" CTA, the active tab, the X-Ray button, watchlist toggles, and focus. The smile-arrow motif (the Amazon swoosh) appears as the brand mark and subtle directional accents. Everything else is white, blue-gray, or deep navy. The restraint around the blue is deliberate — against full-color artwork it reads unmistakably as "go."

Typography is Amazon Ember (Amazon's brand typeface), rendered here with an Inter fallback that preserves its open, legible humanist-grotesque character. The hierarchy is compact and utilitarian: 11-30pt at weights 400 / 600 / 700. There are no oversized editorial headlines — the billboard art and the X-Ray cast strip are the spectacle. Type labels and merchandises: titles at 15-16pt weight 600, metadata (year · rating · IMDb) at 12-13pt weight 400 in blue-gray, row headers at 20-22pt weight 700. The most expressive type moment is the details page, where the title sits at 28-30pt weight 700 above an X-Ray-enabled metadata bar.

**Key Characteristics:**
- Dark navy canvas (`#0F171E`) — Amazon blue-slate, not neutral black
- Prime Blue (`#00A8E1`) as the single accent — the "Play" verb, active tab, X-Ray, watchlist
- Hero billboard — full-bleed auto-still with the blue Play CTA and watchlist plus
- X-Ray cast overlay — tap during playback to slide up a live cast/scene panel (the signature feature)
- Channel & category rows — storefront subscriptions interleaved with included content rows
- Smile-arrow accent — the Amazon swoosh as brand mark and subtle directional cue
- Watchlist plus — a `+` toggle that fills blue when an item is added
- Amazon Ember typeface (Inter fallback), weights 400/600/700
- Row scroll snap — rows paginate with a gentle snap; X-Ray slides up over video
- Bottom tab bar: Home, Store, Live, Find, Downloads

## 2. Color Palette & Roles

### Primary
- **Prime Blue** (`#00A8E1`): Primary "Play" / "Resume" CTA, active tab icon + label, X-Ray button, watchlist-added state, focus ring, smile-arrow accent.
- **Prime Blue Pressed** (`#008CBD`): Pressed/active state for blue CTAs and toggles.
- **Prime Blue Glow** (`rgba(0,168,225,0.32)`): Soft glow under the primary CTA and around a focused tile.

### Canvas & Surfaces
- **Canvas** (`#0F171E`): Primary dark-navy app background — Amazon blue-slate.
- **Deep Black** (`#000000`): Full-screen video player background only.
- **Surface 1** (`#1A242F`): Card backgrounds, sheets, elevated rows, details metadata panel.
- **Surface 2** (`#232F3E`): Higher elevation — modals, active rows, X-Ray panel, chip default background.
- **Surface 3** (`#2E3B47`): Pressed state on navy surfaces, hairline divider tone.
- **Divider** (`#2E3B47`): 1pt separators between settings rows and metadata blocks.

### Text
- **Text Primary** (`#FFFFFF`): Titles, screen headers, primary labels.
- **Text Secondary** (`#AAB7C4`): Blue-gray — metadata (year · rating · IMDb), descriptions, row subtitles.
- **Text Tertiary** (`#6E7B89`): Disabled labels, very low-emphasis captions, placeholder text.

### Semantic
- **IMDb Yellow** (`#F5C518`): IMDb rating badge ("★ 8.4 IMDb") — Amazon owns IMDb; this is a deliberate brand-family accent.
- **Live Red** (`#E50914`): "LIVE" badge on Prime live sports/events and the live now-playing marker.
- **Included Badge** (`#00A8E1`): "Included with Prime" / "Watchlist" affordances reuse Prime Blue.
- **Error Red** (`#E50914`): Playback errors, download failures, offline state.

### Light Mode (Limited Use)
Prime Video's iOS app is effectively dark-only — the cinema-store atmosphere depends on the dark-navy canvas. A light variant is not part of the core product.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#0F171E`)

## 3. Typography Rules

### Font Family
- **Primary**: `Amazon Ember` (Amazon brand typeface) — open humanist grotesque, highly legible
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for runtime, year, IMDb score, episode counts

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Details Title | Amazon Ember | 30pt | 700 | 1.1 | -0.4pt | Title on details page (when no logo art) |
| Screen Title (Large) | Amazon Ember | 28pt | 700 | 1.15 | -0.4pt | "Store", "Downloads" large nav title |
| Row Header | Amazon Ember | 22pt | 700 | 1.2 | -0.3pt | "Included with Prime", "Continue Watching" |
| Section Header | Amazon Ember | 20pt | 700 | 1.2 | -0.3pt | Sub-rows inside a category |
| Episode Title | Amazon Ember | 16pt | 600 | 1.25 | -0.1pt | Episode row title in details |
| Tile Title | Amazon Ember | 15pt | 600 | 1.3 | -0.1pt | Show title under a tile |
| Body | Amazon Ember | 15pt | 400 | 1.45 | 0pt | Synopsis, episode description |
| Metadata | Amazon Ember | 13pt | 400 | 1.3 | 0pt | "2024 · 16+ · 1 Season" |
| Tile Subtitle | Amazon Ember | 12pt | 400 | 1.3 | 0pt | "Included with Prime" / "S2 E1" |
| Label (UPPER) | Amazon Ember | 11pt | 700 | 1.2 | 0.6pt | "CONTINUE WATCHING" eyebrow labels |
| Button (Primary) | Amazon Ember | 16pt | 700 | 1.0 | 0.2pt | Blue "Play" / "Resume" button |
| Button (Secondary) | Amazon Ember | 15pt | 600 | 1.0 | 0pt | "Watchlist", "Trailer", outline buttons |
| Tab Label | Amazon Ember | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Badge | Amazon Ember | 11pt | 700 | 1.0 | 0.4pt | "LIVE", IMDb, "X-Ray" pill |

### Principles
- **Weights concentrated at 400 / 600 / 700**: Regular for body/metadata, semibold for titles, bold for headers and CTAs — no thin, no medium, no black
- **Legibility over drama**: Amazon Ember is engineered for clarity; no decorative display type — billboard art and the X-Ray strip are the spectacle
- **Negative tracking on big type, neutral on small**: -0.4pt at 30pt easing to 0pt at body
- **All-caps only for eyebrows and badges**: "CONTINUE WATCHING", "X-RAY" — never for titles
- **Blue text is structural**: Prime Blue appears only on the active tab label and the X-Ray pill; never as decorative body color

## 4. Component Stylings

### Buttons

**Primary Play CTA (The Blue Button)**
- Shape: Rounded rectangle, height 52pt, full-width within content margins (auto-width on billboard)
- Background: `#00A8E1`
- Text: `#0F171E` (intentionally dark-navy for contrast on the bright cyan-blue, not white)
- Icon: SF Symbol `play.fill` 18pt leading the label, also `#0F171E`
- Corner radius: 8pt
- Font: Amazon Ember 16pt weight 700, letter-spacing 0.2pt
- Shadow: `rgba(0,168,225,0.32) 0 8px 24px` — a soft blue glow
- Pressed: background `#008CBD`, scale 0.97, with `.impactOccurred(.light)` haptic

**Watchlist Toggle (the `+` button)**
- Shape: Rounded rectangle, height 52pt (auto-width) or a 44pt circle in compact contexts
- Default: background `rgba(255,255,255,0.14)`, `plus` glyph + "Watchlist" label `#FFFFFF`
- Added: glyph becomes `checkmark`, tint `#00A8E1`, label "Watchlisted"
- Corner radius: 8pt; Font: Amazon Ember 15pt weight 600
- Pressed: background `rgba(255,255,255,0.22)`, scale 0.98, success haptic on add

**X-Ray Button / Pill**
- Background: `rgba(255,255,255,0.16)` (over video) or `#232F3E` (in details)
- Content: "X-Ray" in Amazon Ember 13pt weight 700, optional small `info.circle` glyph
- Height: 32pt, corner radius 6pt, horizontal padding 14pt
- Pressed: background brightens; on tap, slides the X-Ray panel up

**Secondary Button ("Trailer" / "More")**
- Background: `rgba(255,255,255,0.14)`
- Text: `#FFFFFF`, height 52pt, corner radius 8pt
- Font: Amazon Ember 15pt weight 600
- Pressed: background `rgba(255,255,255,0.22)`, scale 0.98

**Text Button ("See more")**
- Font: Amazon Ember 13pt weight 600
- Color: `#AAB7C4`
- No underline, 44pt hit area, trailing each row header

### Cards & Containers

**Content Tile (Horizontal row)**
- Width: 130pt (2:3 poster, the Prime default) or 200pt (16:9 for continue-watching)
- Aspect: 2:3 or 16:9
- Corner radius: 6pt on the artwork
- Gap between tiles: 12pt
- Optional "Included with Prime" ribbon (Prime Blue text, 11pt) under the tile, or a small `prime` checkmark badge top-left
- Continue-watching variant: thin blue progress bar (3pt) on the bottom edge over `rgba(255,255,255,0.25)`
- Press: scale 1.04 with focus lift `rgba(0,0,0,0.5) 0 12px 28px` and a subtle blue ring

**Channel Tile (storefront)**
- Width: 150pt, the channel's logo art on a brand-tinted card, 8pt corner radius
- "Subscribe" / "Included" affordance below in 12pt; pressed scales 0.97

**Hero Billboard**
- Full-bleed, height ~ 440pt (top of Home)
- Auto-still (or short muted clip) with a bottom-up gradient `rgba(15,23,30,0) → #0F171E`
- Show logo art (or 30pt w700 title) bottom-left
- Metadata line "2024 · 16+ · 1 Season · ★ 8.4 IMDb" 13pt w400 `#AAB7C4` (IMDb badge in `#F5C518`)
- Action row: blue "Play" CTA + watchlist `+` + "Trailer"

**Details Hero**
- Full-bleed 16:9 backdrop still, height ~ 280pt, darkened bottom-up to `#0F171E`
- Logo art (or 30pt w700 title) anchored bottom-left
- Metadata + IMDb badge + an **X-Ray** pill
- Action row: blue "Play" CTA + watchlist `+` + "Download" + "Share"
- Below: synopsis (15pt w400, 3-line clamp + "More"), then episode list / "More like this" rows

**Episode Row**
- Height: 96pt
- Layout: 140pt × 79pt (16:9) thumbnail (blue progress if partially watched) → stacked title (16pt w600) + "S2 E1 · 58 min" meta (13pt w400 `#AAB7C4`) + 2-line description → trailing download icon
- Background: transparent (`#0F171E`)
- Pressed: background `#1A242F`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(15,23,30,0.94)` with `.regularMaterial` blur
- Tabs: Home, Store, Live, Find, Downloads
- Icon size: 24pt
- Active color: `#00A8E1` (icon + label both turn blue — blue is the indicator)
- Inactive: `#AAB7C4`
- Labels: Amazon Ember 10pt w600, always shown
- Active tab icon: filled SF Symbol; inactive: outlined

**Top Nav (Home)**
- Height: 48pt + safe area
- Prime Video wordmark / smile-arrow mark top-left
- Profile avatar (28pt circular) + cast icon top-right
- Transparent over the billboard, fades to `rgba(15,23,30,0.94)` blur on scroll

**Player Controls Overlay**
- Tap video → controls fade in over a `rgba(0,0,0,0.5)` scrim
- Center: 64pt circular `rgba(255,255,255,0.16)` play/pause, ±10s skip flanking at 28pt
- Bottom: blue scrubber (2pt track `rgba(255,255,255,0.3)`, `#00A8E1` fill, 14pt white thumb), elapsed/remaining 12pt
- Top: title + close (✕); bottom-right: **X-Ray**, audio/subtitles, AirPlay
- X-Ray tap: slides the X-Ray panel up from the bottom over the lower third

### Input Fields

**Search Bar (Find)**
- Background: `#232F3E`
- Height: 44pt, corner radius 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#AAB7C4`
- Placeholder: "Search Prime Video", 16pt w400 `#AAB7C4`
- Text color: `#FFFFFF`
- Focus: 1.5pt `#00A8E1` ring

**Filter / Genre Chip**
- Background: `#232F3E` default / `#00A8E1` selected
- Text: `#FFFFFF` default / `#0F171E` selected
- Height: 36pt, corner radius 500pt (full pill), horizontal padding 16pt
- Font: Amazon Ember 14pt weight 600

### Distinctive Components

**X-Ray Cast Overlay**
- The signature Prime Video feature: tapping "X-Ray" during playback slides a panel up from the bottom (over the lower ~40% of the video, on `rgba(35,47,62,0.96)` with blur) showing the actors in the current scene (circular headshots in a horizontal strip), the playing song, and trivia. The video keeps playing above it; a downward chevron dismisses it. This is "who is this actor?" answered in-context, the brand's defining interaction.

**Hero Billboard + Watchlist Plus**
- Full-bleed auto-still with the blue Play CTA; the watchlist `+` toggles to a blue checkmark with a success haptic — the core "save for later" gesture, present on the billboard, tiles, and details.

**Channel / Category Storefront Rows**
- Rows interleave subscription channel tiles (brand-tinted logo cards with "Subscribe") with included-content rows ("Included with Prime" ribbon), making Home a hybrid cinema + store.

**Smile-Arrow Accent**
- The Amazon swoosh appears as the brand mark and as a subtle directional accent (e.g., curving under the wordmark, on the splash) — a quiet brand signature rather than a loud graphic.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard margin: 16pt horizontal
- Between rows: 28pt vertical
- Tile gap: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal rows: begin at 16pt left inset, **snap-paginate** with a gentle stop; peek last tile at right edge
- Find grid: 3-column 2:3 poster grid, 12pt gap
- X-Ray panel: full-width bottom sheet over the video's lower portion

### Whitespace Philosophy
- **Information-dense but legible**: Rows sit 28pt apart with multi-line metadata and IMDb badges — Prime is a browse + evaluate app
- **Billboard dominance**: The hero billboard owns ~440pt before any rows — the marquee leads
- **X-Ray is contextual, not modal**: The overlay covers only the lower portion so the scene stays visible — the layout preserves video while layering data

### Border Radius Scale
- Sharp (0pt): Full-bleed billboard, details backdrop, video surface
- Soft (6pt): Content tiles, posters, hero thumbnails, X-Ray pill
- Standard (8pt): Buttons, channel tiles, search bar, cards
- Comfortable (12-16pt): Bottom sheets, modals, X-Ray panel top corners
- Full Pill (500pt): Genre/filter chips, "LIVE" badge, IMDb badge
- Circle (50%): Profile avatar, X-Ray cast headshots, player center button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, rows, list rows |
| Tile Focus (Level 1) | `rgba(0,0,0,0.5) 0 12px 28px` | Tile lifted on press/focus |
| Card (Level 2) | `rgba(0,0,0,0.4) 0 6px 18px` | Sheets, metadata panels, channel tiles |
| Blue Glow (Accent) | `rgba(0,168,225,0.32) 0 8px 24px` | Under the Play CTA, focused-tile blue ring |
| X-Ray Panel | `rgba(35,47,62,0.96)` + backdrop-blur 24, `0 -16px 44px rgba(0,0,0,0.6)` | Slides up over video lower portion |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 44px` | Bottom sheets, episode-options menu |
| Tab Bar Material | `.regularMaterial` over `rgba(15,23,30,0.94)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On the dark-navy canvas, neutral shadows must be deep (0.4-0.5 opacity, 18-28pt blur) to register. The signature elevation is the X-Ray panel — a blurred 96%-opaque navy sheet that floats up over the video — and the blue glow under the Play CTA. Everything else is flat — depth is blue-gray surface-step color within the navy family, never borders.

### Motion
- **Tile press**: scale 1.0 → 1.04 over 180ms ease-out, focus shadow + blue ring fade in, `.impactOccurred(.light)`
- **Play CTA tap**: scale 0.97 spring (damping 0.7), blue darkens, `.impactOccurred(.light)`
- **Row scroll snap**: rows decelerate and snap to a tile boundary (paged feel), last tile peeks
- **X-Ray slide-up**: panel translates up from the bottom over 280ms ease-out; chevron-down dismisses with reverse
- **Watchlist add**: `+` morphs to `checkmark`, scale 1.0 → 1.15 → 1.0 over 250ms, `.notificationOccurred(.success)`
- **Player controls**: fade in/out over 200ms with the scrim

## 7. Do's and Don'ts

### Do
- Use `#0F171E` dark navy as the canvas — Amazon blue-slate, NOT neutral black (`#000000` is player-only)
- Reserve Prime Blue (`#00A8E1`) for the "Play" verb, active tab, X-Ray, and watchlist-added — the single accent
- Use dark-navy text (`#0F171E`) on the blue CTA — intentional for contrast on the bright cyan-blue
- Lead Home with the full-bleed billboard; snap-paginate the rows below
- Make X-Ray a contextual slide-up that preserves the video above it — never a full modal
- Show the IMDb badge in `#F5C518` (brand-family — Amazon owns IMDb)
- Make the active tab BLUE (icon + label) — blue is the indicator
- Toggle the watchlist `+` to a blue checkmark with a success haptic

### Don't
- Don't use pure black `#000000` for the app canvas — that's the player surface; navy `#0F171E` is the brand
- Don't add a second accent hue — the single Prime Blue is the whole voice (IMDb yellow is a badge, not an accent)
- Don't use white text on the blue button — dark-navy is the intentional contrast choice
- Don't use thin or medium weights — Amazon Ember here is 400 / 600 / 700 only
- Don't round content tiles heavily — 6pt max keeps poster integrity
- Don't make X-Ray a full-screen modal — it must preserve the scene above it
- Don't introduce visible card borders — elevation is blue-gray surface-step color
- Don't over-animate — tile scale is subtle (1.04); the signature motion is the X-Ray slide-up and row snap

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 110pt 2:3 posters; billboard 400pt |
| iPhone 13/14/15 | 390pt | Standard 130pt posters; billboard 440pt |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | 150pt posters; billboard scales to 500pt |
| iPad | 768pt+ | 5-6 col Find grid; larger billboard; X-Ray as a side panel in landscape |

### Dynamic Type
- Titles, episode titles, synopsis: full scale
- Tile subtitle / metadata / IMDb badge: scales but clamps at +2 steps (layout-sensitive in rows)
- Play CTA label: scales to 18pt max; button height fixed at 52pt
- Tab labels, badges, scrubber times: fixed (never scale)

### Orientation
- Home / Store / Find / Downloads / Details: **portrait-locked**
- Video player: **rotates to landscape** — auto-enters full-screen landscape on play
- X-Ray: in portrait slides up over the lower video; in landscape becomes a right-side panel

### Touch Targets
- Play CTA: 52pt height, full-width — impossible to miss
- Content tiles: full-tile tappable, ≥ 110pt tall hit
- Episode rows: 96pt height, full-row tappable
- X-Ray pill / watchlist `+`: 44pt hit area
- Tab bar icons: 24pt icon, 44pt effective hit

### Safe Area Handling
- Top: transparent nav respects safe area / Dynamic Island over the billboard
- Bottom: tab bar floats above home indicator; X-Ray panel and player controls inset from it
- Sides: 16pt content margins; rows bleed to the right edge intentionally

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0F171E`
- Surface 1 (cards): `#1A242F`
- Surface 2 (sheets/X-Ray/chips): `#232F3E`
- Surface 3 / Divider: `#2E3B47`
- Primary text: `#FFFFFF`
- Secondary text: `#AAB7C4`
- Tertiary text: `#6E7B89`
- Prime Blue (action): `#00A8E1`
- Prime Blue pressed: `#008CBD`
- IMDb yellow (badge): `#F5C518`
- Live / error red: `#E50914`

### Example Component Prompts
- "Create a SwiftUI Prime Video primary Play button: full-width, 52pt tall, 8pt corner radius, background #00A8E1, leading SF Symbol 'play.fill' 18pt + label 'Play' in Amazon Ember 16pt weight 700, both in #0F171E (intentionally dark-navy, not white). Soft blue glow shadow rgba(0,168,225,0.32) 0 8px 24px. Pressed: background #008CBD, scale 0.97, light haptic."
- "Build the Prime Video X-Ray overlay: a panel that slides up from the bottom over the lower 40% of a playing video, background rgba(35,47,62,0.96) with backdrop blur 24, top corners 16pt. Header 'In this scene' in Amazon Ember 13pt weight 700 uppercase #AAB7C4, then a horizontal strip of 56pt circular actor headshots with names in 12pt weight 600 #FFFFFF below, then a 'Now playing: <track>' row. A downward chevron at top-right dismisses it; the video keeps playing above."
- "Design the Prime Video hero billboard: full-bleed 440pt auto-still with a bottom-up gradient from rgba(15,23,30,0) to #0F171E. Title 'The Citadel Files' bottom-left in Amazon Ember 30pt weight 700 #FFFFFF, metadata '2024 · 16+ · 1 Season · ★ 8.4 IMDb' in 13pt weight 400 #AAB7C4 (the '★ 8.4 IMDb' segment in #F5C518). Action row: blue Play CTA, a 'Watchlist' + toggle, and 'Trailer'."
- "Create a Prime Video 2:3 poster tile: 130×195pt artwork, 6pt corner radius, an 'Included with Prime' ribbon in Prime Blue 11pt weight 600 under it. On press: scale 1.04 with shadow rgba(0,0,0,0.5) 0 12px 28px and a subtle blue ring. The parent row snap-paginates."
- "Build the Prime Video bottom tab bar: 52pt + safe area, background rgba(15,23,30,0.94) with regularMaterial blur. Tabs Home, Store, Live, Find, Downloads. Active icon + label both #00A8E1 (blue is the indicator), inactive #AAB7C4. Labels Amazon Ember 10pt weight 600, filled SF Symbol when active, outlined when not."

### Iteration Guide
1. Canvas is `#0F171E` dark navy — Amazon blue-slate, NOT neutral black (`#000000` is player-only)
2. Prime Blue (`#00A8E1`) is the single accent — Play CTA, active tab, X-Ray, watchlist-added; never decorative
3. Dark-navy text on the blue button — intentional contrast (`#0F171E` on `#00A8E1`)
4. X-Ray is a contextual slide-up over the lower video — never a full modal; the scene stays visible
5. Lead Home with the billboard; rows snap-paginate; IMDb badge uses brand-family `#F5C518`
6. The active tab is BLUE (icon + label); the watchlist `+` toggles to a blue checkmark with a success haptic
7. Elevation on dark = blue-gray surface-step color + the X-Ray panel + blue glow, never borders
8. Typography is Amazon Ember (Inter fallback), weights 400/600/700 only — no thin, no medium
