# Design System Inspiration of Disney+ (iOS)

## 1. Visual Theme & Atmosphere

Disney+'s iOS app is a cinema lobby rendered in deep space-navy. The canvas is `#0A0E2A` — a rich midnight blue, not black — that evokes a darkened theater and the night sky over a castle. Surfaces step up to `#12152E` and `#1A1F3D`, with `#2A3050` hairline dividers. The mood is premium, magical, and aspirational: the app is a portal into five storytelling universes (Disney, Pixar, Marvel, Star Wars, National Geographic), and every screen is staged like a film billboard. The signature visual is the starfield — a subtle scatter of faint white points layered behind the hero billboard so the whole interface feels like it's floating in the same space as the streaming logo animation.

The accent system is a confident Disney Blue. `#0063E5` is the primary brand and CTA color; a brighter glow blue (`#1A75FF`) is used for focus halos, selected brand-portal tiles, and the play-button bloom. There is no second brand hue — the cinematic color comes from full-bleed content artwork, not UI chrome. White (`#FFFFFF`) is primary text; a cool periwinkle-gray (`#A0A6C0`) carries metadata and descriptions, tuned to harmonize with the navy. The restraint keeps the focus on the billboards and the brand portals — the content is the spectacle.

Typography is a clean geometric sans — Disney's product type is an Avenir-family humanist sans (Inter is the precise web/system fallback). Weights stay at 400 / 600 / 700. The type is cinematic but unobtrusive: it sits over imagery, so it's set in confident bold for titles and quiet regular for synopsis. The most expressive typographic moment is the hero billboard, where a series' logo *artwork* (not text) dominates, with a one-line "genre · rating · year" strip and a metadata blurb beneath in 14-15pt.

**Key Characteristics:**
- Deep space-navy canvas (`#0A0E2A`) with a subtle starfield behind the hero — a darkened theater
- Disney Blue (`#0063E5`) primary, glow blue (`#1A75FF`) for focus halos and selected tiles
- The starfield hero billboard — full-bleed key art with logo artwork and an auto-playing trailer
- Brand-portal tile row — Disney / Pixar / Marvel / Star Wars / Nat Geo as five gateway tiles
- 16:9 landscape content cards (the streaming/TV aspect, not square music tiles)
- Hero auto-trailer — the billboard transitions to muted video after a beat
- Avenir-family sans (Inter fallback), weights 400/600/700
- Periwinkle-gray secondary text (`#A0A6C0`) tuned to the navy

## 2. Color Palette & Roles

### Primary
- **Disney Blue** (`#0063E5`): Primary CTA, "Play" button, active tab, primary brand accent, progress fill.
- **Glow Blue** (`#1A75FF`): Focus halo, selected brand-portal tile border, play-button bloom, hover-equivalent emphasis.
- **Blue Pressed** (`#0052BD`): Tap-down state for blue CTAs.

### Canvas & Surfaces
- **Canvas Navy** (`#0A0E2A`): Primary canvas — the deep midnight-blue theater background.
- **Surface 1** (`#12152E`): Cards, rows, the metadata panel.
- **Surface 2** (`#1A1F3D`): Elevated panels, sheets, the search field, pressed rows.
- **Divider** (`#2A3050`): Hairline dividers between sections and rows.

### Text
- **Text Primary** (`#FFFFFF`): Titles, screen headlines, primary UI text.
- **Text Secondary** (`#A0A6C0`): Synopsis, metadata, genre/rating/year strip — periwinkle-gray, tuned to the navy.
- **Text Tertiary** (`#5A6080`): Disabled state, very low-emphasis labels.

### Semantic
- **Progress Blue** (`#0063E5`): Continue-watching progress bar and the scrubber fill.
- **Live Red** (`#E5484D`): "LIVE" badge on live events / sports (ESPN-style tiles).
- **New Badge Blue** (`#1A75FF`): "NEW" / "PREMIERE" tags on tiles.
- **Download Blue** (`#0063E5`): Completed-download checkmark.
- **Starfield** (`rgba(255,255,255,0.0–0.5)`): Faint scattered points behind the hero billboard.
- **Error Red** (`#E5484D`): Playback errors, connectivity issues.

### Light Mode
Disney+'s iOS app is dark-only by design — the deep space-navy and starfield are the brand identity and the optimal stage for full-bleed cinematic key art. There is no light variant.

## 3. Typography Rules

### Font Family
- **Primary**: Disney's product sans (an Avenir-family humanist geometric sans)
- **Web / System Fallback**: `Inter` — the closest free humanist grotesque
- **Production Fallback**: `'Avenir Next', -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerics**: Tabular figures for runtime and episode counts

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Billboard Title (text fallback) | Avenir Next | 30pt | 700 | 1.1 | -0.3pt | Used when no logo artwork is available |
| Detail Title | Avenir Next | 26pt | 700 | 1.15 | -0.2pt | Series / film detail hero title |
| Section Header | Avenir Next | 20pt | 700 | 1.2 | -0.1pt | "Recommended For You", "Originals" |
| Row Header (Brand) | Avenir Next | 18pt | 700 | 1.2 | 0pt | "Because you watched…" |
| Card Title | Avenir Next | 14pt | 600 | 1.3 | 0pt | Title under a 16:9 card (when shown) |
| Synopsis / Body | Avenir Next | 15pt | 400 | 1.5 | 0pt | Detail-screen description |
| Metadata Strip | Avenir Next | 13pt | 600 | 1.3 | 0.3pt | "Action · PG-13 · 2024 · 2h 11m" |
| Subtitle / Caption | Avenir Next | 14pt | 400 | 1.3 | 0pt | "S2:E4 · The Mandalorian" |
| Meta / Count | Avenir Next | 12pt | 400 | 1.3 | 0pt | "24 episodes", tabular |
| Badge (UPPER) | Avenir Next | 10pt | 700 | 1.0 | 1.0pt | "NEW", "PREMIERE", "LIVE" |
| Button (Primary) | Avenir Next | 16pt | 700 | 1.0 | 0.3pt | Blue "Play" / "Resume" |
| Button (Secondary) | Avenir Next | 14pt | 600 | 1.0 | 0.2pt | Outline "Watchlist" / "Trailer" |
| Tab Label | Avenir Next | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |

### Principles
- **Weights 400 / 600 / 700**: regular, semibold, bold — no thin, no black
- **Negative tracking on large titles** (-0.1 to -0.3pt); the metadata strip uses small positive tracking (+0.3pt)
- **Logo artwork over type wherever possible**: billboards and detail heroes prefer the series' branded logo image; the text title is a fallback
- **All-caps badges with +1.0pt tracking** — "NEW", "PREMIERE", "LIVE"
- **Tabular numerals on runtime/episode counts**
- **Dynamic Type respected on titles, synopsis, metadata**; badges and tab labels stay fixed

## 4. Component Stylings

### Buttons

**Primary Play Button (Detail / Billboard)**
- Shape: Pill (rounded rect, full width on detail; auto-width on billboard)
- Background: `#FFFFFF` (white play button on the navy — the cinematic standard)
- Content: SF Symbol `play.fill` in `#0A0E2A` + label "Play" / "Resume S2:E4" in `#0A0E2A`
- Height: 48pt, corner radius: 8pt
- Font: Avenir Next 16pt weight 700, +0.3pt tracking
- Pressed: background `#E8EAF2`, scale 0.98
- A subtle `rgba(26,117,255,0.30)` glow can sit behind it on the billboard

**Secondary Action (Watchlist / Trailer)**
- Background: `rgba(255,255,255,0.12)` glass over the key art
- Text + icon: `#FFFFFF`
- Border: 1pt `rgba(255,255,255,0.24)`
- Height: 48pt, corner radius: 8pt
- Font: Avenir Next 14pt weight 600
- Pressed: background `rgba(255,255,255,0.20)`

**Brand-Portal Tile (Signature)**
- A landscape rounded card (≈ 160 × 96pt) carrying a brand logo centered on a brand-tinted gradient
- Default: 1pt `#2A3050` border
- Selected/focused: 2pt `#1A75FF` border + a `rgba(26,117,255,0.35)` outer glow + scale 1.04
- The five portals: Disney, Pixar, Marvel, Star Wars, National Geographic

**Icon Actions (Add, Download, Share, Rate, More)**
- Size: 22pt glyph, 44pt hit area
- Default: `#FFFFFF`
- Active: `#0063E5` (added to watchlist, downloaded)
- Layout: a labeled icon row on the detail screen ("Watchlist", "Download", "Share")

**Text Button ("See All", "Details")**
- Font: Avenir Next 14pt weight 700
- Color: `#A0A6C0`
- No underline, 44pt hit area

### Cards & Containers

**16:9 Content Card (Horizontal shelf)**
- Width: 220pt, aspect 16:9 (the streaming/TV ratio — never square)
- Corner radius: 6pt
- Key art only (no title text by default — Disney+ shows branded key art that includes the title)
- Gap between cards: 12pt
- Continue-watching cards carry a 3pt `#0063E5` progress bar pinned to the bottom edge of the art + a small play overlay
- Focused/pressed: scale 1.04 + a `rgba(26,117,255,0.30)` glow + 2pt `#1A75FF` border

**Brand-Portal Row**
- A horizontal row of the five brand tiles directly below the hero billboard
- Each tile ≈ 160 × 96pt landscape, 10pt radius, brand-tinted gradient + centered logo
- This is Disney+'s defining navigation element — the "five universes" gateway

**Hero Billboard**
- Full-bleed key art at the top of Home, ≈ 62% of viewport height
- A vertical gradient scrim from transparent (top) to `#0A0E2A` (bottom) so foreground UI reads
- Series **logo artwork** centered-low (not a text title)
- Metadata strip (13pt 600 `#A0A6C0`): "Action · PG-13 · 2024 · 2h 11m"
- Primary white "Play" pill + a glass "Watchlist" / "Trailer" secondary
- Auto-trailer: after ~3s the still cross-fades to a muted, looping trailer; a mute toggle sits bottom-right
- A faint **starfield** layer sits behind/within the scrim

**Detail Screen Hero**
- Full-bleed key art + scrim, logo artwork, metadata strip, synopsis (15pt 400 `#A0A6C0`)
- Full-width white "Play" / "Resume S2:E4" pill
- Labeled icon row: Watchlist · Download · Share · Rate
- Tabs below: "Episodes", "Suggested", "Extras", "Details"

**Episode Row**
- Height: 104pt
- Layout: 160 × 90pt 16:9 thumbnail (6pt radius) with a small play overlay + a `#0063E5` progress bar if partially watched → stacked "E4 · Title" (14pt 600) + synopsis (13pt 400 `#A0A6C0`, 2-line) + runtime (12pt 400, tabular) + download icon
- Background: `#12152E` card, 8pt radius

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(10,14,42,0.96)` with `.regularMaterial` blur
- Tabs: Home, Search, Watchlist, Downloads, Profile
- Icon size: 24pt
- Active color: `#FFFFFF` with a small `#0063E5` underline/dot indicator; inactive: `#A0A6C0`
- Labels: Avenir Next 10pt weight 600, shown always
- Active icon: filled SF Symbol variant; inactive: outlined

**Top Nav (Home Entry)**
- Height: 44pt + safe area, transparent over the billboard
- Disney+ wordmark/logo centered (or left); profile avatar (28pt) top-right
- Scrolls to a solid `#0A0E2A` bar with a hairline `#2A3050` divider on content scroll

**Detail Nav**
- A back chevron (28pt, white) in a subtle glass circle top-left over the key art
- A cast/AirPlay icon top-right

### Input Fields

**Search Bar**
- Background: `#1A1F3D`
- Height: 44pt
- Corner radius: 8pt
- Leading SF Symbol `magnifyingglass`, 18pt, `#A0A6C0`
- Placeholder: "Search by title, character, or genre", 16pt 400 `#A0A6C0`
- Focus: 1pt `#0063E5` border, cursor `#0063E5`
- Text in field: `#FFFFFF`

**Filter Chip**
- Background: `#1A1F3D` default / `#0063E5` selected
- Text: `#FFFFFF` (both states)
- Padding: 8pt vertical, 18pt horizontal
- Corner radius: 8pt
- Font: Avenir Next 14pt weight 600
- Examples: "Movies", "Series", "Originals", "Kids"

### Distinctive Components

**The Brand-Portal Tiles (Signature)**
- Five landscape gateway tiles — Disney, Pixar, Marvel, Star Wars, National Geographic — in a row beneath the billboard
- Each is a brand-tinted gradient with the brand's logo centered (Disney = blue/castle, Pixar = blue/lamp, Marvel = red, Star Wars = black/gold, Nat Geo = yellow frame)
- Tapping one opens that universe's curated hub; the tile lifts (scale 1.04) with a `#1A75FF` glow on focus
- This "choose your universe" row is the most recognizable Disney+ pattern

**Starfield Hero Billboard with Auto-Trailer**
- The hero is full-bleed key art over a faint scatter of white star points (`rgba(255,255,255,0.0–0.5)`, 1–2px, parallax-subtle)
- A bottom-up `#0A0E2A` scrim grounds the foreground UI
- After ~3s the still cross-fades to a muted looping trailer; a `speaker.slash` / `speaker` mute toggle sits bottom-right
- The starfield reinforces the brand's "magic / night sky / cinema" identity

**Tile Glow + Scale Focus**
- Any card or portal tile, when focused or pressed, scales to 1.04, gains a 2pt `#1A75FF` border and a `rgba(26,117,255,0.30)` outer glow over ~180ms
- This is the unified selection language across Home, brand hubs, and search

**Continue-Watching Progress Bar**
- 16:9 cards in the "Continue Watching" row carry a 3pt `#0063E5` progress bar pinned to the bottom of the art, plus a centered play affordance — a clear resume cue across the catalog

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Standard margin: 16pt horizontal, 24pt between content rows
- Row header to cards: 12pt; card-to-card: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal shelves: 16pt left inset, last 16:9 card peeks at the right edge
- The brand-portal row: 5 tiles, horizontally scrollable, 12pt gap
- Search grid: 2-column 16:9 grid, 12pt gap

### Whitespace Philosophy
- **Cinematic full-bleed**: the billboard and key art bleed edge-to-edge; chrome is minimal so content fills the frame
- **Horizontal browsing rhythm**: rows of 16:9 cards scroll sideways like a film-poster wall
- **The portals get a stage**: the brand-tile row sits in its own band with breathing room — it's a featured choice, not a list

### Border Radius Scale
- Soft (6pt): 16:9 content cards, episode thumbnails
- Comfortable (8pt): buttons, cards, search, chips, the metadata panel
- Portal (10pt): brand-portal tiles
- Sheet (16pt): bottom sheets
- Circle (50%): avatars, icon hit targets, the mute toggle

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Rows, canvas, content shelves |
| Card (Level 1) | `rgba(0,0,0,0.4) 0 6px 18px` | 16:9 cards lift subtly off the navy |
| Billboard Scrim | Vertical gradient transparent → `#0A0E2A` | Grounds foreground UI over key art |
| Focus Glow (Level 2) | `rgba(26,117,255,0.30) 0 0 24px` + 2pt `#1A75FF` border | Focused/selected cards and portal tiles |
| Play Bloom | `rgba(26,117,255,0.30) 0 6px 22px` | Soft bloom behind the billboard Play button |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -14px 44px` | Bottom sheets, episode pickers |
| Tab Bar Material | `.regularMaterial` over 96%-opaque navy | Bottom tab bar over scrolling content |

**Shadow Philosophy**: Disney+ expresses depth cinematically — the dominant "shadow" is the billboard scrim (a gradient to navy, not a drop shadow) that makes UI legible over key art. Cards carry a soft dark lift so the poster wall feels layered. The expressive accent is the **blue focus glow** (`#1A75FF` at 30%): selecting a card or brand portal blooms a halo around it, the spotlight-on-a-marquee feel. Conventional gray shadows are minimal; the navy canvas and blue glow do the work.

### Motion
- **Tile glow + scale (signature)**: focus/press scales 1.0 → 1.04, fades in a 2pt `#1A75FF` border + `rgba(26,117,255,0.30)` glow over 180ms ease-out
- **Hero auto-trailer**: still → muted video cross-fade over ~600ms after a ~3s dwell
- **Play button tap**: scale 0.98 spring (damping 0.8), `.impactOccurred(.medium)` haptic
- **Brand-portal open**: tile scales up and cross-fades into the universe hub (shared-element, 0.35s)
- **Row scroll**: smooth horizontal paging with a slight peek; no bounce-heavy physics
- **Detail open**: key art shared-element rise from the tapped card, scrim fades in over 300ms
- **Starfield**: a very slow parallax drift (sub-pixel), effectively ambient

## 7. Do's and Don'ts

### Do
- Use the deep space-navy canvas (`#0A0E2A`) with a faint starfield behind the hero
- Reserve Disney Blue (`#0063E5`) for primary actions; use glow blue (`#1A75FF`) for focus halos and selected tiles
- Make the brand-portal tile row (Disney/Pixar/Marvel/Star Wars/Nat Geo) a prominent, defining element
- Use 16:9 landscape cards — the streaming/TV aspect, never square music tiles
- Stage the hero as a full-bleed billboard with logo artwork and an auto-playing muted trailer
- Use a white "Play" pill with a dark glyph over key art (the cinematic standard)
- Apply the unified tile glow + scale 1.04 focus language everywhere
- Use an Avenir-family sans (Inter fallback) at weights 400/600/700
- Keep secondary text periwinkle-gray (`#A0A6C0`), tuned to the navy

### Don't
- Don't use pure black or a flat gray canvas — Disney+ is rich navy `#0A0E2A` with a starfield
- Don't add a second brand hue — content artwork supplies the color
- Don't use square (1:1) content tiles — Disney+ is 16:9 landscape
- Don't replace logo artwork with plain text titles when branded logos exist
- Don't drop the brand-portal row — it's the signature navigation
- Don't use heavy neutral drop shadows — depth is the scrim + the blue focus glow
- Don't use thin or black font weights — 400/600/700 only
- Don't animate focus with bounce — the glow + scale is a smooth 180ms ease-out
- Don't omit the bottom-up scrim over key art — foreground text needs it

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 200pt 16:9 cards; 3.5 brand tiles visible |
| iPhone 13/14/15 | 390pt | Standard 220pt cards; ~4 brand tiles visible |
| iPhone 15/16 Pro | 393pt | Dynamic Island clears the transparent top bar |
| iPhone 15/16 Pro Max | 430pt | 240pt cards; taller billboard; 5 brand tiles visible |
| iPad | 768pt+ | 3–4 cards per row; billboard ~50% height; portals as a larger band |

### Dynamic Type
- Detail title, synopsis, metadata strip, episode titles: full scale
- Badges ("NEW", "LIVE"): fixed 10pt (exact geometry)
- Runtime/episode counts: scale but stay tabular
- Tab labels: fixed 10pt
- Logo artwork: scales proportionally with the billboard (image, not type)

### Orientation
- Home / Search / Watchlist / Downloads: **portrait-locked**
- Detail: **portrait-locked**
- Video player: **landscape** (full-screen cinematic playback)

### Touch Targets
- Play pill: full-width, 48pt tall — unmissable
- Brand-portal tiles: ≈ 160 × 96pt — large, easily tappable
- 16:9 cards: full-card tap, ≥ 90pt tall
- Icon actions: 22pt glyph, 44pt hit area
- Tab icons: 24pt glyph, 44pt effective hit

### Safe Area Handling
- Top: transparent nav respects safe area; Dynamic Island floats over the billboard scrim
- Bottom: tab bar respects the home indicator; the billboard bleeds full under the status bar
- Sides: 16pt content insets; key art and billboard bleed edge-to-edge

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas navy: `#0A0E2A`
- Surface 1 (cards): `#12152E`
- Surface 2 (sheets/search): `#1A1F3D`
- Divider: `#2A3050`
- Text primary: `#FFFFFF`
- Text secondary (periwinkle-gray): `#A0A6C0`
- Text tertiary: `#5A6080`
- Disney Blue (action): `#0063E5`
- Glow Blue (focus/selected): `#1A75FF`
- Blue pressed: `#0052BD`
- Live red: `#E5484D`

### Example Component Prompts
- "Create the SwiftUI Disney+ hero billboard: full-bleed 16:9 key art at ~62% viewport height with a faint starfield (scattered rgba(255,255,255,0.0–0.5) 1–2px points) and a vertical scrim from transparent to #0A0E2A (bottom). Centered-low series logo artwork (image, not text), a metadata strip 'Action · PG-13 · 2024 · 2h 11m' in Avenir Next 13pt weight 600 #A0A6C0, a white 'Play' pill (48pt, 8pt radius, play.fill + label in #0A0E2A) with a rgba(26,117,255,0.30) bloom, and a glass 'Watchlist' secondary. After ~3s cross-fade the still to a muted looping trailer with a speaker.slash mute toggle bottom-right."
- "Build a Disney+ brand-portal row: a horizontally scrollable row of five 160×96pt landscape tiles (10pt radius) — Disney, Pixar, Marvel, Star Wars, National Geographic — each a brand-tinted gradient with the brand logo centered, 1pt #2A3050 border. On focus/press: scale 1.04, 2pt #1A75FF border, and a rgba(26,117,255,0.35) outer glow over 180ms ease-out."
- "Design a Disney+ 16:9 content card: 220pt wide, 16:9 aspect, 6pt corner radius, branded key art only (no separate title). Continue-watching variant adds a 3pt #0063E5 progress bar pinned to the bottom edge + a centered play overlay. Focused: scale 1.04 + 2pt #1A75FF border + rgba(26,117,255,0.30) glow."
- "Create a Disney+ episode row: 104pt tall #12152E card (8pt radius), a 160×90pt 16:9 thumbnail (6pt radius) with a play overlay and a #0063E5 progress bar, 'E4 · The Siege' in Avenir Next 14pt weight 600 #FFFFFF, a 2-line synopsis in 13pt weight 400 #A0A6C0, runtime '48m' in 12pt weight 400 tabular, and a download icon."
- "Build the Disney+ bottom tab bar: 52pt + safe area, rgba(10,14,42,0.96) with .regularMaterial blur. Tabs Home / Search / Watchlist / Downloads / Profile, 24pt icons, active = #FFFFFF with a small #0063E5 dot indicator, inactive = #A0A6C0, labels Avenir Next 10pt weight 600."

### Iteration Guide
1. Canvas is rich space-navy `#0A0E2A` with a faint starfield behind the hero — never pure black or flat gray
2. Disney Blue (`#0063E5`) for actions; glow blue (`#1A75FF`) for focus halos and selected brand tiles
3. The brand-portal tile row (Disney/Pixar/Marvel/Star Wars/Nat Geo) is the signature navigation — make it prominent
4. Content cards are 16:9 landscape (streaming/TV aspect), never square; prefer branded key art over text titles
5. The hero is a full-bleed billboard with logo artwork + a bottom-up scrim + a muted auto-trailer after ~3s
6. Apply the unified tile glow + scale 1.04 focus language everywhere (180ms ease-out, no bounce)
7. Typography: Avenir-family sans (Inter fallback), weights 400/600/700; periwinkle-gray secondary tuned to the navy
8. Depth is the scrim + the blue focus glow, not neutral gray shadows; dark-only — there is no light mode
