# Design System Inspiration of Hulu (iOS)

## 1. Visual Theme & Atmosphere

Hulu's iOS app is a content-dense streaming surface built on a near-black canvas (`#0B0C0F`) that pushes thumbnail artwork forward and lets the chrome disappear. Where some streamers chase cinematic minimalism, Hulu leans into density: the home screen is a stack of horizontally-scrolling rails, each packed with 16:9 landscape tiles, so the eye is always scanning a grid of shows rather than resting on a single hero. The background is a touch warmer and lighter than pure black to keep the dark UI from crushing the edges of poster art, and elevated surfaces (`#151619`, `#1E2024`) step up just enough to separate sheets and cards without introducing visible borders.

The accent system is singular and loud. Hulu Green (`#1CE783`) — the brand's electric, almost neon signature — is reserved for the verb: the primary "Watch" / "Play" CTA, the active tab indicator, progress fill on continue-watching tiles, and toggles. It is intentionally the only saturated color in the interface. Against the desaturated grays and full-color thumbnails, the green reads as "go" — when you see it, it's an action. Everything else is white, gray, or near-black. There is no secondary brand hue competing for attention.

Typography is Graphik (Hulu's brand typeface), rendered here with an Inter fallback that preserves its clean, slightly condensed grotesque character. The hierarchy is compact and utilitarian: 11-32pt with weights at 400 / 600 / 800. There are no oversized editorial headlines — the artwork is the headline. Type's job is to label: show titles at 15-16pt weight 600, metadata (season, rating, year) at 12-13pt weight 400 in gray, rail headers at 20-22pt weight 800. The most expressive type moment is the details hero, where the show's logo art (when present) or title sits at 26-32pt weight 800 over a darkened backdrop still.

**Key Characteristics:**
- Near-black canvas (`#0B0C0F`) — slightly warm/lifted off pure black to protect thumbnail edges
- Hulu Green (`#1CE783`) as the ONLY accent — the electric "Watch" verb
- 16:9 landscape content tiles in dense horizontal rails — a browse-heavy grid
- Hub chip row near the top (Disney, FX, HBO, etc.) for branded content destinations
- Green progress bar on continue-watching tiles — resume-state is always visible
- Graphik typeface (Inter fallback), weights 400/600/800
- Details hero: large darkened backdrop still with a green "Watch" CTA pinned over it
- Tile scale-up on press and a subtle focus lift on the active rail
- Bottom tab bar: Home, Hubs, My Stuff, Search, Account

## 2. Color Palette & Roles

### Primary
- **Hulu Green** (`#1CE783`): Primary "Watch" / "Play" CTA, active tab icon + label, progress-bar fill, active toggle, "in My Stuff" check.
- **Green Pressed** (`#15B869`): Pressed/active state for the green CTA and toggles.
- **Green Glow** (`rgba(28,231,131,0.30)`): Soft drop-shadow under the primary CTA and around focused tiles.

### Canvas & Surfaces
- **Canvas** (`#0B0C0F`): Primary near-black app background — warm-lifted to protect artwork edges.
- **Deep Black** (`#000000`): Full-screen video player background, splash.
- **Surface 1** (`#151619`): Card backgrounds, sheets, elevated rails, details metadata panel.
- **Surface 2** (`#1E2024`): Higher elevation — modals, active list rows, chip default background.
- **Surface 3** (`#2A2D33`): Pressed state on dark surfaces, hairline divider tone.
- **Divider** (`#2A2D33`): 1pt separators between settings rows and metadata blocks.

### Text
- **Text Primary** (`#FFFFFF`): Show titles, screen headers, primary labels.
- **Text Secondary** (`#A0A4AB`): Metadata (year · rating · seasons), episode descriptions, rail subtitles.
- **Text Tertiary** (`#6B6F77`): Disabled labels, very low-emphasis captions, placeholder text.

### Semantic
- **Live Red** (`#F0476A`): "LIVE" badge on sports/news live tiles and the live-TV guide now-playing marker.
- **Rating Pill Gray** (`#2A2D33`): "TV-MA" / "R" maturity-rating chip background, text `#FFFFFF`.
- **New Badge Green** (`#1CE783`): "NEW EPISODE" badge (same green as primary).
- **Error Red** (`#F0476A`): Playback errors, download failures, offline state.

### Light Mode (Limited Use)
Hulu's iOS app is effectively dark-only — the streaming-lounge atmosphere depends on the dark canvas. A light variant is not part of the core product.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#0B0C0F`)

## 3. Typography Rules

### Font Family
- **Primary**: `Graphik` (Hulu brand typeface) — clean grotesque with a slightly condensed, modern character
- **Fallback Stack**: `'Inter', -apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for runtime, episode counts, and live-guide times

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Details Hero Title | Graphik | 32pt | 800 | 1.1 | -0.5pt | Show title on details screen (when no logo art) |
| Screen Title (Large) | Graphik | 28pt | 800 | 1.15 | -0.4pt | "My Stuff", "Hubs" large nav title |
| Rail Header | Graphik | 22pt | 800 | 1.2 | -0.3pt | "Continue Watching", "TV You Might Like" |
| Section Header | Graphik | 20pt | 800 | 1.2 | -0.3pt | Sub-rails inside a hub |
| Episode Title | Graphik | 16pt | 600 | 1.25 | -0.1pt | Episode row title in details |
| Tile Title | Graphik | 15pt | 600 | 1.3 | -0.1pt | Show title under a 16:9 tile |
| Body | Graphik | 15pt | 400 | 1.45 | 0pt | Synopsis, episode description |
| Metadata | Graphik | 13pt | 400 | 1.3 | 0pt | "2024 · TV-MA · 3 Seasons" |
| Tile Subtitle | Graphik | 12pt | 400 | 1.3 | 0pt | "S3 · E4" / "New episode" under tile |
| Label (UPPER) | Graphik | 11pt | 800 | 1.2 | 0.6pt | "CONTINUE WATCHING" eyebrow labels |
| Button (Primary) | Graphik | 16pt | 800 | 1.0 | 0.2pt | Green "Watch" / "Play" button |
| Button (Secondary) | Graphik | 15pt | 600 | 1.0 | 0pt | "Details", "Trailer", outline buttons |
| Tab Label | Graphik | 10pt | 600 | 1.0 | 0.2pt | Bottom tab bar labels |
| Badge | Graphik | 11pt | 800 | 1.0 | 0.4pt | "LIVE", "NEW", maturity rating pill |

### Principles
- **Weights concentrated at 400 / 600 / 800**: Regular for body/metadata, semibold for titles, extrabold for headers and CTAs — no thin, no medium
- **Density over drama**: No hero-size display type; the tile grid is the visual interest
- **Negative tracking on big type, neutral on small**: -0.5pt at 32pt, easing to 0pt at body sizes
- **All-caps only for eyebrows and badges**: "CONTINUE WATCHING", "LIVE" — never for titles
- **Green text is structural**: green appears only on the active tab label and progress; never as decorative body color

## 4. Component Stylings

### Buttons

**Primary Watch CTA (The Green Button)**
- Shape: Rounded rectangle, height 52pt, full-width within content margins (or auto-width on details)
- Background: `#1CE783`
- Text: `#0B0C0F` (intentionally near-black for contrast on the bright green, not white)
- Icon: SF Symbol `play.fill` 18pt leading the label, also `#0B0C0F`
- Corner radius: 8pt
- Font: Graphik 16pt weight 800, letter-spacing 0.2pt
- Shadow: `rgba(28,231,131,0.30) 0 8px 24px` — a soft green glow
- Pressed: background `#15B869`, scale 0.97, with `.impactOccurred(.light)` haptic

**Secondary Button ("Details" / "Trailer")**
- Background: `rgba(255,255,255,0.12)`
- Text: `#FFFFFF`
- Height: 52pt, corner radius 8pt
- Font: Graphik 15pt weight 600
- Pressed: background `rgba(255,255,255,0.20)`, scale 0.98

**Icon Actions (My Stuff +, Download, Share)**
- Size: 24pt glyph, 44pt hit area
- Default: `#FFFFFF`
- Active: `#1CE783` (added to My Stuff, download complete)
- Layout: vertical icon-over-label stacks on the details action row, 24pt gap

**Hub Chip (branded destinations)**
- Background: `#1E2024` (or the hub brand fill when entered)
- Text/Logo: `#FFFFFF`
- Height: 40pt, corner radius 8pt, horizontal padding 16pt
- Font: Graphik 13pt weight 600
- Pressed: background `#2A2D33`, scale 0.97

**Text Button ("See All")**
- Font: Graphik 13pt weight 600
- Color: `#A0A4AB`
- No underline, 44pt hit area, trailing each rail header

### Cards & Containers

**16:9 Content Tile (Horizontal rail)**
- Width: 200pt (standard), 260pt (spotlight rail)
- Aspect: 16:9 landscape thumbnail
- Corner radius: 6pt on the artwork
- Gap between tiles: 12pt
- Structure: thumbnail → optional title (15pt w600, 1-line clamp) → subtitle (12pt w400 `#A0A4AB`)
- Continue-watching variant: 4pt green progress bar pinned to the bottom edge of the thumbnail, track `rgba(255,255,255,0.25)`
- Press: scale 1.04 with focus lift `rgba(0,0,0,0.5) 0 12px 28px` and subtle green ring

**Poster Tile (2:3, used in some hubs)**
- Width: 130pt, aspect 2:3
- Corner radius: 6pt
- Used inside movie-forward hubs; same press treatment as 16:9

**Continue Watching Rail**
- Rail header "Continue Watching" 22pt w800 + "See All" trailing
- 16:9 tiles with green progress bar + "S3 · E4 · 24 min left" subtitle
- A circular play overlay (`rgba(0,0,0,0.45)` 40pt with white `play.fill`) centered on hover/focus

**Hub Chip Row**
- Horizontal scroll directly under the top nav
- 40pt-tall chips with brand wordmarks (Disney, FX, Hulu Originals, HBO, etc.)
- 12pt gap, peek last chip at right edge to signal scroll

**Details Hero**
- Full-bleed 16:9 backdrop still, height ~ 280pt, darkened with a bottom-up gradient `rgba(11,12,15,0) → #0B0C0F`
- Show logo art (or 32pt w800 title) anchored bottom-left over the gradient
- Metadata line "2024 · TV-MA · 3 Seasons · Drama" in 13pt w400 `#A0A4AB`
- Action row: full-width green "Watch" CTA, then `+ My Stuff`, `Download`, `Share` vertical icon stacks
- Below: synopsis (15pt w400, 3-line clamp + "More"), then episode list

**Episode Row**
- Height: 96pt
- Layout: 140pt × 79pt (16:9) thumbnail with green progress bar if partially watched → stacked title (16pt w600) + "S3 · E4 · 48 min" meta (13pt w400 `#A0A4AB`) + 2-line description → trailing download icon
- Background: transparent (`#0B0C0F`)
- Pressed: background `#151619`

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(11,12,15,0.94)` with `.regularMaterial` blur
- Tabs: Home, Hubs, My Stuff, Search, Account
- Icon size: 24pt
- Active color: `#1CE783` (icon + label both turn green — green IS the indicator here)
- Inactive: `#A0A4AB`
- Labels: Graphik 10pt w600, always shown
- Active tab icon: filled SF Symbol; inactive: outlined

**Top Nav (Home)**
- Height: 48pt + safe area
- Hulu wordmark (green) top-left, 22pt
- Profile avatar (28pt circular) top-right
- Transparent over the hero, fades to `rgba(11,12,15,0.94)` blur on scroll

**Player Controls Overlay**
- Tap video → controls fade in over a `rgba(0,0,0,0.5)` scrim
- Center: 64pt circular `rgba(255,255,255,0.15)` button with white `play.fill` / `pause.fill`, ±10s skip flanking at 28pt
- Bottom: green scrubber (2pt track `rgba(255,255,255,0.3)`, `#1CE783` fill, 14pt white thumb), elapsed/remaining 12pt
- Top: title + close (✕), bottom-right: captions, audio, AirPlay icons

### Input Fields

**Search Bar**
- Background: `#1E2024`
- Height: 44pt, corner radius 8pt
- Leading SF Symbol `magnifyingglass` 18pt `#A0A4AB`
- Placeholder: "Search shows, movies, networks", 16pt w400 `#A0A4AB`
- Text color: `#FFFFFF`
- Focus: 1pt `#1CE783` ring

**Filter / Genre Chip**
- Background: `#1E2024` default / `#1CE783` selected
- Text: `#FFFFFF` default / `#0B0C0F` selected
- Height: 36pt, corner radius 500pt (full pill), horizontal padding 16pt
- Font: Graphik 14pt weight 600

### Distinctive Components

**Green Play CTA over Content-Dense Rows**
- The signature Hulu move: a single bright-green "Watch" button (with soft green glow) pinned over the details backdrop, immediately above stacked, information-rich episode and recommendation rows. The contrast between the one electric CTA and the dense gray content grid is the brand's core tension.

**Continue-Watching Progress Bar**
- 4pt green bar on the bottom edge of every in-progress 16:9 tile; track is `rgba(255,255,255,0.25)`. Resume state is never hidden — the grid always tells you where you left off.

**Hub Chip Row**
- A horizontally-scrolling row of branded destination chips (Disney, FX, Hulu Originals, Adult Swim…) sitting just below the top nav. Entering a hub re-skins the rail headers with the hub's accent while keeping Hulu Green for the CTA.

**Live Badge + Sports Tiles**
- Live content tiles carry a `#F0476A` "● LIVE" pill top-left and a pulsing dot; the live-TV guide marks the now-playing program with the same red.

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 52, 64
- Standard margin: 16pt horizontal
- Between rails: 28pt vertical
- Tile gap: 12pt

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal rails: begin at 16pt left inset, peek last tile at right edge
- My Stuff grid: 3-column 16:9 grid (or 3-column 2:3 for movies), 12pt gap
- Live guide: fixed channel column (88pt) + horizontally-scrolling timeline

### Whitespace Philosophy
- **Dense vertical rhythm**: Rails stack at 28pt apart with multi-line metadata — this is a scan-heavy, choice-rich app
- **Tight horizontal tile gaps**: 12pt between 16:9 tiles maximizes how many shows are visible per rail
- **Hero gets room**: The details backdrop owns ~280pt before any text, the one place the layout breathes

### Border Radius Scale
- Sharp (0pt): Full-bleed details backdrop, video surface
- Soft (6pt): Content tiles, posters, hero thumbnails
- Standard (8pt): Buttons, hub chips, search bar, cards
- Comfortable (12-16pt): Bottom sheets, modals
- Full Pill (500pt): Genre/filter chips, "LIVE" badge
- Circle (50%): Profile avatar, player center button

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, rails, list rows |
| Tile Focus (Level 1) | `rgba(0,0,0,0.5) 0 12px 28px` | 16:9 tile lifted on press/focus |
| Card (Level 2) | `rgba(0,0,0,0.4) 0 6px 18px` | Sheets, metadata panels |
| Green Glow (Accent) | `rgba(28,231,131,0.30) 0 8px 24px` | Under the primary "Watch" CTA, focused-tile ring |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 44px` | Bottom sheets, episode-options menu |
| Tab Bar Material | `.regularMaterial` over `rgba(11,12,15,0.94)` | Bottom tab bar over scrolling content |

**Shadow Philosophy**: On the near-black canvas, neutral shadows must be deep (0.4-0.5 opacity, 18-28pt blur) to register at all. The signature elevation cue, however, is chromatic: the green glow under the "Watch" CTA and the soft green ring on a focused tile do the lifting that neutral shadows can't on dark. Everything else is flat — depth is communicated by surface-step color, not borders.

### Motion
- **Tile press**: scale 1.0 → 1.04 over 180ms ease-out, focus shadow + green ring fade in, `.impactOccurred(.light)` haptic
- **Watch CTA tap**: scale 0.97 spring (damping 0.7), green glow pulses, `.impactOccurred(.light)`
- **Rail scroll**: native momentum, last tile peeks; no snap
- **Details enter**: backdrop cross-dissolves in over 250ms, content rows slide up 12pt + fade
- **LIVE dot**: continuous 1.4s opacity pulse 1.0 → 0.4 → 1.0
- **Player controls**: fade in/out over 200ms with the scrim

## 7. Do's and Don'ts

### Do
- Use `#0B0C0F` as the canvas — warm-lifted near-black, NOT pure `#000000` (reserve that for the player)
- Reserve Hulu Green (`#1CE783`) for the "Watch" verb, active tab, and progress — it's the only saturated color
- Use near-black text (`#0B0C0F`) on the green CTA — intentional for contrast on the bright green
- Keep 16:9 landscape tiles at 6pt corner radius — soften, don't round
- Always show the green progress bar on in-progress tiles — resume state is core
- Stack rails densely (28pt apart) with full metadata — Hulu is browse-heavy by design
- Put the green glow under the primary CTA — it's the chromatic elevation cue on dark
- Make the active tab GREEN (icon + label) — green is the tab indicator here

### Don't
- Don't use pure black `#000000` for the app canvas — it crushes thumbnail edges; it's player-only
- Don't add a second accent hue — the single electric green is the entire brand voice
- Don't use white text on the green button — near-black is the intentional contrast choice
- Don't use thin or medium weights — Graphik here is 400 / 600 / 800 only
- Don't round content tiles heavily — 6pt max keeps the 16:9 integrity
- Don't hide continue-watching progress — the green bar must always be visible on resumable tiles
- Don't introduce visible card borders — elevation is surface-step color, not strokes
- Don't over-animate — tile scale is subtle (1.04), springs stay in the 180-300ms range

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 170pt 16:9 tiles instead of 200pt |
| iPhone 13/14/15 | 390pt | Standard 200pt tiles |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in transparent top nav |
| iPhone 15/16 Pro Max | 430pt | 230pt tiles, details backdrop scales to 320pt |
| iPad | 768pt+ | 4-column My Stuff grid, larger details backdrop, rails show 4-5 tiles |

### Dynamic Type
- Show titles, episode titles, synopsis: full scale
- Tile subtitle / metadata: scales but clamps at +2 steps (layout-sensitive in dense rails)
- Watch CTA label: scales to 18pt max; button height fixed at 52pt
- Tab labels, badges, scrubber times: fixed (never scale)

### Orientation
- Home / Hubs / My Stuff / Search / Details: **portrait-locked**
- Video player: **rotates to landscape** — auto-enters full-screen landscape on play
- Live guide: portrait, horizontally-scrolling timeline

### Touch Targets
- Watch CTA: 52pt height, full-width — impossible to miss
- 16:9 tiles: full-tile tappable, ≥ 110pt tall hit
- Episode rows: 96pt height, full-row tappable
- Tab bar icons: 24pt icon, 44pt effective hit
- Icon actions: 24pt glyph, 44pt hit area

### Safe Area Handling
- Top: transparent nav respects safe area / Dynamic Island over the hero
- Bottom: tab bar floats above home indicator; player controls inset from it
- Sides: 16pt content margins; rails bleed to the right edge intentionally

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#0B0C0F`
- Surface 1 (cards): `#151619`
- Surface 2 (sheets/chips): `#1E2024`
- Surface 3 / Divider: `#2A2D33`
- Primary text: `#FFFFFF`
- Secondary text: `#A0A4AB`
- Tertiary text: `#6B6F77`
- Hulu Green (action): `#1CE783`
- Green pressed: `#15B869`
- Live red: `#F0476A`
- Error red: `#F0476A`

### Example Component Prompts
- "Create a SwiftUI Hulu 16:9 content tile: 200×113pt thumbnail, 6pt corner radius, with a 4pt Hulu Green (#1CE783) progress bar pinned to the bottom edge over a rgba(255,255,255,0.25) track. Below: title 'The Bear' in Graphik 15pt weight 600 #FFFFFF (1-line) and subtitle 'S3 · E4 · 24 min left' in 12pt weight 400 #A0A4AB. On press: scale 1.04 with shadow rgba(0,0,0,0.5) 0 12px 28px and a subtle green ring."
- "Build the Hulu primary Watch button: full-width, 52pt tall, 8pt corner radius, background #1CE783, leading SF Symbol 'play.fill' 18pt + label 'Watch' in Graphik 16pt weight 800, both in #0B0C0F (intentionally near-black, not white). Soft green glow shadow rgba(28,231,131,0.30) 0 8px 24px. Pressed: background #15B869, scale 0.97, light haptic."
- "Design the Hulu details hero: full-bleed 16:9 backdrop still 280pt tall with a bottom-up gradient from rgba(11,12,15,0) to #0B0C0F. Show title 'Shōgun' bottom-left in Graphik 32pt weight 800 #FFFFFF, metadata '2024 · TV-MA · 1 Season · Drama' in 13pt weight 400 #A0A4AB below it. Then an action row: full-width green Watch CTA, then '+ My Stuff', 'Download', 'Share' as vertical icon-over-label stacks in #FFFFFF."
- "Create a Hulu continue-watching rail: header 'Continue Watching' in Graphik 22pt weight 800 #FFFFFF + trailing 'See All' link in 13pt weight 600 #A0A4AB. Horizontal scroll of 200×113pt 16:9 tiles (6pt radius, 12pt gap), each with a green progress bar and a centered rgba(0,0,0,0.45) 40pt circular play overlay."
- "Build the Hulu bottom tab bar: 52pt + safe area, background rgba(11,12,15,0.94) with regularMaterial blur. Tabs Home, Hubs, My Stuff, Search, Account. Active icon + label both #1CE783 (green IS the indicator), inactive #A0A4AB. Labels Graphik 10pt weight 600, filled SF Symbol when active, outlined when not."

### Iteration Guide
1. Canvas is `#0B0C0F` — warm-lifted near-black, NOT pure `#000000` (that's the player only)
2. Hulu Green (`#1CE783`) is the only saturated color — Watch CTA, active tab, progress bar; never decorative
3. Content tiles are 16:9 landscape at 6pt radius — density over drama, 12pt gaps
4. Near-black text on the green button — intentional contrast (`#0B0C0F` on `#1CE783`)
5. Continue-watching progress bar is always visible on resumable tiles — it's a core pattern
6. The active tab is GREEN (icon + label) — unlike many streamers, green is the tab indicator
7. Elevation on dark = surface-step color + the green glow under the CTA, never borders
8. Typography is Graphik (Inter fallback), weights 400/600/800 only — no thin, no medium
