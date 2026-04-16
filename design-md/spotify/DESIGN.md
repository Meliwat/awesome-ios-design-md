# Design System Inspiration of Spotify (iOS)

## 1. Visual Theme & Atmosphere

Spotify's iOS app is a dark canvas that treats album art as the sole source of color. The interface fades into a near-black background (`#121212`) so every piece of cover art — saturated pop, moody jazz, stark classical — can set the emotional temperature of the moment without competing with UI chrome. This is the design equivalent of a turntable: the player recedes, the record speaks. Shelves of playlists, albums, and podcasts scroll horizontally as tall square tiles, each a direct reproduction of the artist's cover art, giving the home screen the feeling of a record-store browsing session on a phone.

The accent system is minimal and ritual. Spotify Green (`#1DB954`) — or its slightly lighter logo tint (`#1ED760`) — is reserved for one thing: the primary action pill ("Play", "Follow", the big green button on the Now Playing screen) and the active-state checkmark for "liked" / downloaded items. Everything else is white, gray, or black. No teal, no purple, no system blue. The restraint is what makes the green iconic — when you see it, you know it's a verb.

Typography is Spotify Mix (as of May 2024; previously Circular). It's a custom geometric sans with warm, humanist curves — softer than a pure grotesque, more modern than Helvetica. The hierarchy is compact: 12-32pt with weights concentrated at 400 / 600 / 700. There are no hero-size headlines in the app because the album art *is* the headline. Text plays a supporting role: track titles at 16pt weight 600, artist names at 14pt weight 400 in gray, section headers at 22pt weight 700. The most expressive type moment is the Now Playing screen, where the currently-playing track title gets 22-28pt bold over the dominant color of the album art (extracted dynamically).

**Key Characteristics:**
- True-dark canvas (`#121212`) with Spotify Green (`#1DB954`) as the only accent
- Album art as the primary chromatic element — the UI borrows color from content
- Dynamic color extraction on Now Playing — the screen gradient is derived from the album cover
- Square shelves of tiles scrolling horizontally — "Made for You", "Recently Played", "Your Top Mixes"
- Large green circular Play button as the signature CTA on Now Playing (56-72pt diameter)
- Spotify Mix font (2024 onward) — warm geometric sans, 400/600/700 weights
- Bottom Now Playing mini-bar — persistent, draggable up to full-screen player
- Haptics on play/pause, skip, like, and long-press on tracks

## 2. Color Palette & Roles

### Primary
- **Spotify Green** (`#1DB954`): Primary CTA, "Follow" button, "Liked Songs" heart active state, active tab indicator, download checkmark.
- **Logo Green** (`#1ED760`): Slightly lighter — used in the Spotify logotype itself, NOT in UI chrome.
- **Green Pressed** (`#169C46`): Active/pressed state for green CTAs.

### Canvas & Text
- **Canvas Black** (`#121212`): Primary dark canvas — the signature "not-quite-black" that reduces pure-black eye strain.
- **Deep Black** (`#000000`): Now Playing background when no color extraction is available, lock screen media.
- **Surface 1** (`#181818`): Card backgrounds, elevated shelves.
- **Surface 2** (`#282828`): Higher elevation — sheets, modals, active list rows.
- **Surface 3** (`#3E3E3E`): Hover / pressed state on dark surfaces.
- **Divider** (`#2A2A2A`): Hairline dividers between list rows.
- **Text Primary** (`#FFFFFF`): Track titles, screen headlines, primary UI text.
- **Text Secondary** (`#B3B3B3`): Artist names, play counts, metadata, section subtitles.
- **Text Tertiary** (`#6A6A6A`): Disabled state, very low-emphasis labels.

### Dynamic Album-Art Colors
- **Extracted Dominant**: The Now Playing screen background transitions from the dominant color of the current album cover to `#121212` over a vertical gradient. This is per-track and calculated at playback time.
- **Gradient Vignette**: Full-screen vertical gradient from extracted color (top, 100% opacity) to canvas black (bottom, 100% opacity) over ~60% viewport height.

### Semantic
- **Explicit Tag Gray** (`#8B8B8B`): "E" explicit marker background.
- **Download Green** (`#1DB954`): Download-complete checkmark (same as primary green).
- **Podcast Accent** (`#A0C3FF`): Light blue tint used sparingly in podcast-specific UI.
- **Error Red** (`#F15E6C`): Playback errors, connection-lost states.

### Light Mode (Limited Use)
Spotify's iOS app is effectively dark-only. A light variant exists for specific contexts (printable, embeds) but the primary experience is dark.
- **Light Canvas** (`#FFFFFF`)
- **Light Text** (`#121212`)

## 3. Typography Rules

### Font Family
- **Primary (2024+)**: `Spotify Mix` (proprietary) with UI and Title optical variants
- **Previous (2014-2024)**: `Circular` (by Lineto) — still referenced in older parts of the product
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **CJK/Non-Latin**: Extended fallback includes Arabic, Hebrew, Cyrillic, Greek, Devanagari, and CJK fonts — Spotify ships globally

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Now Playing Track | Spotify Mix Title | 22pt | 700 | 1.15 | -0.3pt | Current song on Now Playing full screen |
| Section Header | Spotify Mix Title | 22pt | 700 | 1.2 | -0.3pt | "Made for You", "Your Library" |
| Screen Title (Large) | Spotify Mix Title | 28pt | 700 | 1.15 | -0.4pt | Home, Search nav large title |
| Playlist Title | Spotify Mix Title | 24pt | 700 | 1.2 | -0.3pt | On playlist detail screen hero |
| Track Title | Spotify Mix UI | 16pt | 600 | 1.25 | -0.1pt | Track row in playlist |
| Card Title | Spotify Mix UI | 15pt | 600 | 1.3 | -0.1pt | Album/playlist tile title |
| Artist / Subtitle | Spotify Mix UI | 14pt | 400 | 1.3 | 0pt | Secondary info on rows |
| Body | Spotify Mix UI | 15pt | 400 | 1.4 | 0pt | Descriptions, About sections |
| Meta / Count | Spotify Mix UI | 12pt | 400 | 1.3 | 0pt | "2,340,115 plays", "34 songs" |
| Label (UPPER) | Spotify Mix UI | 11pt | 700 | 1.2 | 0.5pt | "MADE FOR YOU" section labels |
| Button (Primary) | Spotify Mix UI | 16pt | 700 | 1.0 | 0pt | Green "Play", "Follow" buttons |
| Button (Secondary) | Spotify Mix UI | 14pt | 600 | 1.0 | 0pt | Outline/transparent buttons |
| Tab Label | Spotify Mix UI | 11pt | 700 | 1.0 | 0.2pt | Bottom tab bar labels |
| Timer / Timestamp | Spotify Mix UI | 11pt | 500 | 1.0 | 0pt | Track duration on row, 1:23 on scrubber |

### Principles
- **Two optical variants**: Spotify Mix ships with UI (smaller, tighter) and Title (larger, more open) — switch at 18pt threshold
- **Weight concentrated at 400 / 600 / 700**: Regular, semibold, bold — no thin, no black
- **No tracking exaggeration**: Negative tracking at most -0.4pt on titles; body sits at 0pt
- **Dynamic Type respected on Latin; fixed size on CJK**: Non-Latin scripts have their own optical logic
- **Green text exists only in the active tab label**; everywhere else green is a background color

## 4. Component Stylings

### Buttons

**Primary Play CTA (The Big Green Button)**
- Shape: Circle, diameter 56-72pt depending on context (72pt on Now Playing, 56pt on headers)
- Background: `#1DB954`
- Icon: SF Symbol `play.fill` in `#000000`, 24-32pt
- Pressed: scale 0.92, background `#169C46`, with haptic
- Paused state: `pause.fill` icon

**Primary Pill (Follow / Subscribe)**
- Background: `#1DB954`
- Text: `#000000` (intentionally black for contrast, not white)
- Padding: 10pt vertical, 32pt horizontal
- Corner radius: 500pt (full pill)
- Font: Spotify Mix UI, 16pt, weight 700
- Pressed: `#169C46`, scale 0.97
- Used for: "Follow", "Premium", "Subscribe"

**Outline Pill ("Following" / "Downloaded")**
- Background: transparent
- Text: `#FFFFFF`
- Border: 1pt solid `#B3B3B3`
- Padding: 10pt vertical, 20pt horizontal
- Corner radius: 500pt
- Font: Spotify Mix UI, 14pt, weight 600
- Pressed: border `#FFFFFF`

**Icon Actions (Heart, Shuffle, Repeat, Download, Share)**
- Size: 24pt glyph, 44pt hit area
- Default: `#B3B3B3`
- Active: `#1DB954` (heart liked, shuffle on, repeat on, downloaded)
- Spacing: 24pt between icons in the action bar row above the scrubber

**Text Button ("See all", "Show more")**
- Font: Spotify Mix UI, 14pt, weight 700
- Color: `#B3B3B3`
- No underline, no background, just text with 44pt hit area

### Cards & Containers

**Album / Playlist Tile (Horizontal shelf)**
- Width: 150pt (standard), 180pt (hero)
- Aspect: 1:1 square album art top, text below
- Corner radius: 6pt on the art (slight softening but not rounded)
- Gap between tiles: 16pt
- Structure: art → title (15pt w600, 2-line clamp) → subtitle (12pt w400 `#B3B3B3`, 1-line)
- Tap: scale 0.96 with subtle haptic

**Track Row**
- Height: 56pt
- Layout: 44pt × 44pt square album thumbnail → stacked title/artist → trailing 20pt ellipsis (⋯)
- Background: transparent (canvas `#121212`)
- Pressed: background `#282828`
- Title: Spotify Mix UI 16pt w600, white; Artist: 13pt w400 `#B3B3B3`
- Currently-playing track: title and artist text turn `#1DB954`, small equalizer animation to the left of the thumbnail

**Playlist Hero**
- Height: 320pt (top of playlist screen)
- Background: vertical gradient from extracted album color to `#121212`
- Centered large playlist image (200pt × 200pt) with shadow `rgba(0,0,0,0.5) 0 12px 32px`
- Title below, 28pt w700
- Creator + song count + total duration in 13pt w400 `#B3B3B3`
- Action row: shuffle icon + download icon + ellipsis + large green play button (72pt)

**Now Playing (Full Screen)**
- Full-bleed album art centered at ~ 340pt × 340pt
- Dynamic gradient background from extracted dominant color to `#121212`
- Title + artist stacked below art
- Progress scrubber with 1pt track `#6A6A6A` and `#FFFFFF` fill, 16pt thumb handle (circular white)
- Large 72pt green Play/Pause button flanked by 32pt previous/next SF Symbols
- Below: heart, device selector, share, more

### Navigation

**Bottom Tab Bar**
- Height: 56pt + safe area
- Background: semi-transparent `rgba(18,18,18,0.92)` with `.regularMaterial` blur
- Tabs: Home, Search, Your Library, Create (Premium) or Premium
- Icon size: 24pt
- Active color: `#FFFFFF` (NOT green — tab indicator is white weight)
- Inactive: `#B3B3B3`
- Labels: Spotify Mix UI 11pt w700, shown always
- Active tab icon: filled SF Symbol variant; inactive: outlined

**Now Playing Mini Bar**
- Sits directly above the tab bar, full width, 56pt tall
- Background: `#282828`
- Left: 40pt square album art
- Center: track title 14pt w600 white + artist 11pt w400 `#B3B3B3` (horizontally scrolling on long titles)
- Right: `play.fill` / `pause.fill` SF Symbol 20pt, then device speaker icon
- Tap anywhere: opens full Now Playing screen with fluid share-transition animation
- Swipe down to collapse, swipe up to expand

**Top Nav (Profile / Search Entry)**
- Height: 44pt + safe area
- Profile photo (28pt circular) top-left
- Title center (22pt w700 white)
- Settings icon top-right (24pt)
- Semi-transparent blur on scroll
- Filter chips row below title (pill chips at 32pt height)

### Input Fields

**Search Bar**
- Background: `#FFFFFF` (stark — this is intentional, light input on dark canvas)
- Height: 44pt
- Corner radius: 6pt
- Leading SF Symbol `magnifyingglass`, 18pt, `#121212`
- Placeholder: "What do you want to listen to?", 16pt w400 `#121212`
- Focus: no border change, cursor appears
- Text color in field: `#121212` (black on white)

**Filter Chip**
- Background: `#2A2A2A` default / `#FFFFFF` selected
- Text: `#FFFFFF` default / `#000000` selected
- Padding: 8pt vertical, 16pt horizontal
- Corner radius: 500pt (full pill)
- Font: Spotify Mix UI, 14pt, weight 600
- Examples on Home: "Music" / "Podcasts & Shows"

### Distinctive Components

**Home Shelves**
- Section title (22pt w700 white) + "Show all" button trailing right
- Horizontal scroll of 1:1 square tiles
- Ends with peek-edge (the last tile is half-visible to signal scroll)

**Now Playing Expanding Transition**
- Mini bar expands into full-screen player via a shared-element animation (album art scales from 40pt → 340pt in 0.3s spring)
- Canvas transitions from tab background to extracted gradient

**Equalizer Animation**
- On currently-playing track row, replace the thumbnail corner with a 3-bar animated equalizer
- Bar widths: 3pt each, spacing 2pt
- Color: `#1DB954`
- Bars animate in sync with playback — if paused, they freeze

**Spotify Wrapped / Yearly Review**
- Full-screen immersive moments with dynamic gradient backgrounds per-slide
- Huge display type (48-72pt)
- Reserved for seasonal campaigns — not part of the core app's standard layout

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 32, 40, 56, 72
- Standard margin: 16pt horizontal, 24pt between shelves
- Track row spacing: 0pt between (they touch, rely on hairline dividers when used)

### Grid & Container
- Content width: full device width with 16pt horizontal margins
- Horizontal shelves: begin at 16pt left inset, peek the last tile at right edge
- Playlist grid view: 2-column square grid, 12pt gap

### Whitespace Philosophy
- **Tight vertical density**: Track rows at 56pt each pack many songs into the viewport — this is a browse-heavy app
- **Generous horizontal breathing on shelves**: 16pt gaps between square tiles, letting each album art have room
- **Now Playing is sacred**: The full-screen player uses 60%+ of the viewport for the album art alone

### Border Radius Scale
- Square (0pt): Profile photo when rectangular, very rare
- Soft (4-6pt): Album art thumbnails, playlist covers
- Standard (8pt): Filter chips (when not full-pill), cards in podcast UI
- Comfortable (12pt): Modal sheets, bottom grabber sheets
- Full Pill (500pt): Primary CTAs (Follow, Premium), filter chips, genre chips
- Circle (50%): Play button, profile avatar, tab icons

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | List rows, shelves, canvas |
| Card (Level 1) | `rgba(0,0,0,0.3) 0 2px 8px` | Album tiles lifted on hover-equivalent states |
| Now Playing Art (Level 2) | `rgba(0,0,0,0.5) 0 12px 32px` | The centered album art on full-screen player |
| Sheet (Level 3) | `rgba(0,0,0,0.6) 0 -16px 48px` | Bottom sheets, context menus |
| Blur Overlay | `rgba(0,0,0,0.6)` + backdrop-blur 40 | Modal dim, device picker sheet |
| Tab Bar Material | `.regularMaterial` over `rgba(18,18,18,0.92)` | Bottom tab bar floats above scrolling content |

**Shadow Philosophy**: Shadows are large, soft, and very dark — because the canvas itself is dark, shadows need more depth to register. The Now Playing album art shadow is the most prominent: a 32pt blur at 50% opacity that makes the art feel like it's floating in space. Everything else in the app is essentially flat.

### Motion
- **Play button tap**: scale 0.92 → 1.0 spring (damping 0.7), paired with `.impactOccurred(.soft)` haptic
- **Track swipe**: 60fps horizontal paging on the Now Playing track view
- **Pull-to-refresh**: Minimal spinner, no custom animation
- **Equalizer bars**: Continuous vertical scale animation tied to playback
- **Mini-bar to Full Player**: Shared-element 0.3s spring, album art is the anchor
- **Like heart**: Fill + subtle scale bounce 1.0 → 1.15 → 1.0 over 300ms

## 7. Do's and Don'ts

### Do
- Use `#121212` as the canvas — NOT true black, NOT `#191414`
- Reserve Spotify Green (`#1DB954`) for the primary "play/follow" action and active toggles — it's the verb
- Use black text (`#000000`) on green CTA buttons — not white, it's intentional for contrast
- Render album art with 6pt corner radius — softening without rounding
- Use Spotify Mix (or Circular as legacy fallback) at weights 400/600/700 only
- Keep the Now Playing mini-bar persistent above the tab bar whenever playback is active
- Apply dynamic gradient extraction on the Now Playing full screen from the album art dominant color
- Use white active state on tab bar icons — green is not the tab indicator
- Use equalizer animation on currently-playing track rows

### Don't
- Don't use pure black `#000000` as the canvas — it kills Now Playing gradient contrast
- Don't add accent colors beyond green — the app's restraint is what makes the green pop
- Don't use light weights — Spotify Mix starts at 400
- Don't use large display-size headlines — content (album art, track titles) is the hero
- Don't round album art heavily — 6pt max, keep the square integrity
- Don't use green text outside of the active tab label — green is structural, not decorative
- Don't use skeuomorphic controls — play button is flat, not glossy
- Don't animate aggressively — spring motion is subtle, 250-400ms range
- Don't hide the mini-bar during active playback

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | 130pt shelf tiles instead of 150pt |
| iPhone 13/14/15 | 390pt | Standard 150pt tiles |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in nav |
| iPhone 15/16 Pro Max | 430pt | 170pt shelf tiles, Now Playing art scales to 360pt |
| iPad | 768pt+ | 3-column grid, larger Now Playing at 480pt |

### Dynamic Type
- Track titles, artist names, section headers: full scale
- Track duration / timestamp: fixed 11pt (never scales — layout-sensitive)
- Play buttons: icon size fixed (72pt)
- Now Playing track title: scales to 28pt max

### Orientation
- Home / Search / Library: **portrait-locked**
- Now Playing: **rotates to landscape** — album art centers, controls shift to one side
- Video podcasts: **rotate to landscape**

### Touch Targets
- Primary Play button: 72pt — impossible to miss
- Track rows: 56pt height, full-row tappable
- Ellipsis menus: 44pt hit area
- Tab bar icons: 24pt icon, 44pt effective hit

### Safe Area Handling
- Top: safe area honored on nav
- Bottom: Now Playing mini-bar floats above tab bar; both respect home indicator
- Sides: 16pt content insets

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas: `#121212`
- Surface 1 (cards): `#181818`
- Surface 2 (sheets): `#282828`
- Divider: `#2A2A2A`
- Primary text: `#FFFFFF`
- Secondary text: `#B3B3B3`
- Tertiary text: `#6A6A6A`
- Spotify Green (action): `#1DB954`
- Green pressed: `#169C46`
- Logo Green: `#1ED760` (logotype only)
- Error red: `#F15E6C`

### Example Component Prompts
- "Create a SwiftUI Spotify track row: 56pt height, 44×44pt album art (6pt corner radius) on the leading side, stacked title 'Blinding Lights' in Spotify Mix 16pt weight 600 #FFFFFF and artist 'The Weeknd' in 13pt weight 400 #B3B3B3. Trailing 20pt ellipsis icon. Entire row tap area; pressed background #282828."
- "Build the Now Playing play button: 72pt circular button, #1DB954 background, SF Symbol 'play.fill' at 32pt in #000000 (intentionally black not white). Pressed: scale 0.92 with soft haptic, background shifts to #169C46."
- "Design the Now Playing full screen: background uses a vertical linear gradient from the extracted dominant color of the album cover (top, 100% opacity) to #121212 (bottom). 340×340pt album art centered with shadow rgba(0,0,0,0.5) 0 12px 32px. Below: track title 22pt weight 700 white, artist 14pt weight 400 #B3B3B3. Scrubber with 1pt #6A6A6A track and white fill. Icon row: heart (active green), device, 72pt play button, skip back/forward."
- "Create a horizontal shelf: section title 'Made for You' in Spotify Mix Title 22pt weight 700 #FFFFFF + trailing 'Show all' link in 14pt weight 700 #B3B3B3. Below: horizontal scroll of 150×150pt square album tiles with 6pt corner radius, 16pt gap. Each tile: art on top, title 15pt weight 600 white (2-line clamp), subtitle 12pt weight 400 #B3B3B3 below."
- "Build the Now Playing mini bar: fixed above tab bar, 56pt tall, background #282828 opaque. 40×40pt album art (4pt radius) left, track title 14pt weight 600 white + artist 11pt weight 400 #B3B3B3 (horizontally scrolls if overflowing), trailing play/pause 20pt SF Symbol + device speaker icon."

### Iteration Guide
1. Canvas is `#121212` — NOT black, NOT `#191414`
2. Spotify Green only for primary action buttons and active toggles — never for text, borders, or accents
3. Album art is the color story — let it drive the Now Playing gradient via dynamic extraction
4. Typography: Spotify Mix, weights 400/600/700 only, no thin or black
5. Black text on green button — intentional contrast choice (`#000000` on `#1DB954`)
6. Keep the mini Now Playing bar present whenever playback is active
7. Tab bar active state is WHITE, not green — green is reserved for structural primary actions
8. Shadows are large and dark (0.5 opacity, 32pt blur) — the dark canvas swallows subtle shadows
