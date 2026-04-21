# Design System Inspiration of Apple Music (iOS)

## 1. Visual Theme & Atmosphere

Apple Music's iOS app is the flagship showcase of Apple's own design language — a first-party experience that leans heavily into SF Pro, SF Symbols, system materials (blurs and vibrancy), and haptics. It exists to do one thing: put album art front and center, wrap it in a light-and-air clean interface, and let you fall into an album. The canvas is pure white (`#FFFFFF`) in light mode and true black (`#000000`) in dark — unusually Apple uses the full-contrast black instead of the softer `#121212` most other media apps favor, because the Now Playing screen's album-art-derived gradient needs the deepest canvas to pop.

The accent system centers on Apple Music Red (`#FA2D48`) — sometimes rendered as a slightly warmer brand coral (`#FC3C44`) depending on the surface. It's the color of the Play button on Now Playing, the Apple Music logomark, and the "Now Playing" indicator on the mini-player. Apple Music is more generous with its accent than Spotify or Netflix are — the red is also used for the scrubber fill, the heart like state, the "Shuffle" toggle when active, and the "Join Apple Music" CTA. The result is a more classically-iOS feel: the red is a first-party accent rather than a single sacred verb, and it plays nicely with the iOS system blue for links and the palette of Album-art-derived colors on the Now Playing screen.

Typography is 100% SF Pro — Apple's native font — at the full spectrum of sizes and weights the system offers. There's no custom typeface; Apple Music leans into SF Pro's built-in optical variants (SF Pro Display at 20pt+, SF Pro Text below). The hierarchy is wide: from giant 34pt large titles on "Listen Now" cards to 11pt tertiary metadata, all rendered in SF Pro weights from 400 to 700. The most expressive type moments are the **hero cards** on Listen Now (24–34pt bold over album-art hero imagery) and the **time-synced lyrics** view, where each line of lyrics scales larger as it becomes the current line being sung.

**Key Characteristics:**
- SF Pro throughout — no custom font — Apple Music leans fully into iOS system language
- Light canvas `#FFFFFF` / dark canvas `#000000` — full-contrast black, not warm `#121212`
- **Apple Music Red** (`#FA2D48`) as primary accent — Play button, Now Playing indicator, Shuffle active, heart liked
- **Listen Now hero cards** — large editorial cards with full-bleed album art, 24–34pt titles
- **Larger album tiles** with 12pt corner radius — signature softer-than-Spotify corner
- **Dolby Atmos badge** — golden glow indicator on supported tracks/albums
- **Time-synced lyrics view** with word-by-word highlight — a defining Apple Music feature
- **Mini-player** sits above the tab bar with artwork, title, and play/pause
- Now Playing expands to full-screen with album art as hero, album-art-derived gradient background
- Bottom tab bar: Home (Listen Now), New, Radio, Library, Search — 5 tabs
- System materials everywhere: `.regularMaterial`, `.thinMaterial` blurs throughout
- SF Symbols for all icons; native iOS haptics on all actions

## 2. Color Palette & Roles

### Primary
- **Apple Music Red** (`#FA2D48`): Primary brand accent — Play button, Now Playing indicator, Shuffle active state, heart liked icon, scrubber fill, section badges like "NEW", links in certain contexts.
- **Apple Music Coral** (`#FC3C44`): Slightly warmer variant — used in alternative marketing surfaces and in some templated album art.
- **Red Pressed** (`#D4213B`): Pressed / active-state variant for red CTAs.
- **Red Dimmed** (`#FA2D48` at 60% opacity): Inactive heart outline on some surfaces.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas — Home, Library, Search, Radio.
- **Surface 1** (`#F2F2F7`): iOS system secondary background — grouped list backgrounds, section cards.
- **Surface 2** (`#FFFFFF`): Card foreground on secondary background.
- **Surface 3** (`#E5E5EA`): Tertiary surface — pressed rows, divider-adjacent areas.
- **Divider** (`#C6C6C8`): iOS system separator at 1/3 alpha; 1pt hairline between list rows.
- **Input Fill** (`#E5E5EA`): Search bar fill, text field backgrounds.

### Canvas & Surfaces (Dark Mode)
- **Canvas Black** (`#000000`): Primary dark canvas — full-contrast black, unusual for a media app.
- **Surface 1 Dark** (`#1C1C1E`): iOS system secondary dark background — card backgrounds, grouped list.
- **Surface 2 Dark** (`#2C2C2E`): Tertiary — raised cards, sheets.
- **Surface 3 Dark** (`#3A3A3C`): Elevated — context menus, selected row.
- **Divider Dark** (`#38383A`): Hairlines in dark mode.
- **Input Fill Dark** (`#1C1C1E`): Search bar dark fill.

### Text (Light Mode)
- **Label Primary** (`#000000`): Track titles, headlines — full iOS `.label` token.
- **Label Secondary** (`#3C3C43` at 60% opacity): Subtitles, artist names — iOS `.secondaryLabel`.
- **Label Tertiary** (`#3C3C43` at 30% opacity): Timestamps, metadata — iOS `.tertiaryLabel`.
- **Label Quaternary** (`#3C3C43` at 18% opacity): Disabled, very low emphasis.

### Text (Dark Mode)
- **Label Primary Dark** (`#FFFFFF`): Main text on dark.
- **Label Secondary Dark** (`#EBEBF5` at 60% opacity): Artist names on dark.
- **Label Tertiary Dark** (`#EBEBF5` at 30% opacity): Metadata on dark.

### Dynamic Album-Art Gradient
- **Now Playing Extracted Gradient**: The Now Playing screen background transitions from the dominant color of the album cover through a subtle gradient to a slightly darker variant over ~70% of the viewport
- **Lyrics View Background**: Similar extraction, using the album art's secondary color for contrast with the highlighted lyric line
- **Mini-Player Hint**: On the mini-player, a very subtle gradient tint pulls from the current track's album art, applied at low opacity

### Semantic
- **Dolby Atmos Gold** (`#D4A857`): The distinctive Atmos badge color — a rich metallic gold
- **Lossless Silver** (`#8E8E93`): The "Lossless" quality badge
- **iOS System Blue** (`#007AFF`): Links to artists, albums from inline text (e.g. in "About" section)
- **iOS System Green** (`#34C759`): Download complete indicator
- **Error Red** (`#FF3B30`): iOS system red for playback errors (not Apple Music red — these are separate)

## 3. Typography Rules

### Font Family
- **Primary**: `SF Pro` — Apple's native font stack (SF Pro Display at 20pt+, SF Pro Text below)
- **Rounded variant**: `SF Pro Rounded` — used sparingly in Listen Now hero cards and certain editorial contexts
- **Fallback Stack**: None needed — SF Pro is guaranteed on all iOS devices; system font stack applies automatically
- **CJK / Non-Latin**: Apple's system fonts — PingFang SC, Hiragino Sans, Apple SD Gothic Neo, Apple Color Emoji for emoji runs
- Apple Music uses Dynamic Type at almost every level — the app scales readably across Dynamic Type sizes from Extra Small to AX5

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Large Title | SF Pro Display | 34pt | 700 | 1.1 | 0.37 | "Listen Now", "Library" large navigation titles |
| Title 1 | SF Pro Display | 28pt | 700 | 1.15 | 0.36 | Secondary large headings |
| Listen Now Hero | SF Pro Display | 24pt | 700 | 1.2 | -0.2pt | Title on hero editorial cards |
| Title 2 | SF Pro Display | 22pt | 700 | 1.2 | -0.26 | Playlist / album hero titles |
| Title 3 | SF Pro Display | 20pt | 600 | 1.2 | -0.15 | Section headers "Recently Played" |
| Now Playing Title | SF Pro Display | 18pt | 600 | 1.25 | -0.1 | Current track title on Now Playing |
| Headline | SF Pro Text | 17pt | 600 | 1.25 | -0.43 | Track title in playlist list |
| Body | SF Pro Text | 17pt | 400 | 1.45 | -0.43 | Description, about |
| Callout | SF Pro Text | 16pt | 400 | 1.25 | -0.32 | Emphasized body text |
| Subheadline | SF Pro Text | 15pt | 400 | 1.3 | -0.24 | Artist names, secondary info |
| Footnote | SF Pro Text | 13pt | 400 | 1.3 | -0.08 | Timestamps, play counts |
| Caption 1 | SF Pro Text | 12pt | 400 | 1.3 | 0 | Badges, minor metadata |
| Caption 2 | SF Pro Text | 11pt | 400 | 1.2 | 0.07 | "Dolby Atmos" badge, "Lossless" label |
| Button | SF Pro Text | 17pt | 600 | 1.0 | -0.43 | Primary pill button labels |
| Tab Label | SF Pro Text | 10pt | 500 | 1.0 | -0.24 | Bottom tab bar labels |
| Lyrics Current | SF Pro Display | 28pt | 700 | 1.2 | -0.3 | The currently-singing lyric line (scales up and fades in) |
| Lyrics Other | SF Pro Display | 22pt | 600 | 1.3 | -0.2 | Other visible lyric lines (non-current) |

### Principles
- **Respect Dynamic Type everywhere**: Apple Music is one of the best-behaved apps for Dynamic Type — scale headlines, body, and metadata; keep Play button icon fixed, keep the mini-player fixed geometry
- **Use iOS text styles when available**: Headline, Body, Subheadline, Footnote, Caption 1/2 — these map 1:1 to iOS style tokens and respect accessibility automatically
- **SF Pro Display vs. Text switchover at 20pt**: Display for large, Text for small — the system handles this automatically if you use `.system` font, but custom sizes need explicit specification
- **Rounded is a hero moment only**: SF Pro Rounded appears on Listen Now editorial cards as a friendlier, more approachable vibe; the rest of the app stays on regular SF Pro
- **Letter spacing follows iOS defaults**: iOS ships pre-tuned tracking values for each style; don't override unless hero-sized (20pt+)
- **No italic**: Apple Music does not use italics anywhere — all emphasis comes from weight, color, or size
- **Lyrics type scales dynamically**: Current line is 28pt bold, adjacent lines are 22pt semibold, distant lines fade toward 18pt regular; this is driven by a scroll-sync that matches playback timing

## 4. Component Stylings

### Buttons

**Primary Play Button (The Red Circle)**
- Shape: Circle, diameter 64pt on Now Playing, 44pt on inline contexts (track row, album hero)
- Background: `#FA2D48`
- Icon: SF Symbol `play.fill` in `#FFFFFF`, 28pt on 64pt button, 20pt on 44pt button
- Pressed: scale 0.92, `.impact(.soft)` haptic
- Paused state: `pause.fill` icon
- Position on Now Playing: centered between prev/next, large 64pt size

**Primary Pill ("Play", "Shuffle Play", "Join Apple Music")**
- Background: `#FA2D48` (brand red) for most CTAs; occasionally iOS system blue `#007AFF` for "Join Apple Music"
- Text: `#FFFFFF` SF Pro Text 17pt weight 600
- Leading SF Symbol: often `play.fill` 15pt
- Padding: 10pt vertical, 24pt horizontal
- Corner radius: 8pt (rounded rectangle, not full pill — iOS Music's signature)
- Pressed: scale 0.97 with `.impact(.light)` haptic
- Width: often full-width in a section, or content-fit

**Secondary Button ("Shuffle", "Download", "Share")**
- Background: `#F2F2F7` light / `#2C2C2E` dark (system secondary)
- Text: label primary color SF Pro Text 15pt weight 500
- Leading SF Symbol 15pt
- Corner radius: 8pt
- Padding: 10pt vertical, 16pt horizontal
- Pressed: darker tint with `.impact(.light)`

**Icon Button (Heart / Shuffle / Repeat / More)**
- Size: 20-24pt SF Symbol glyph, 44pt hit area
- Default: label secondary color
- Active: `#FA2D48` (heart liked, shuffle on, repeat on)
- Pressed: scale 0.9 with `.impact(.soft)`

**Scrubber**
- Track: 4pt tall, `rgba(0,0,0,0.1)` light / `rgba(255,255,255,0.2)` dark
- Fill: `#FA2D48` (Apple Music Red) — left side of the scrubber from start to thumb
- Thumb: 16pt circle `#FFFFFF` with `rgba(0,0,0,0.2)` shadow, scale 1.2× during drag
- Time labels: 13pt weight 400 label secondary, "0:48" left, "-3:12" right

### Cards & Containers

**Album / Playlist Tile (Standard)**
- Width: 160pt on most iPhones, 180pt on Pro Max
- Aspect: 1:1 square album art top, stacked text below
- Corner radius: **12pt** on the art — Apple Music's signature softer corner (vs. Spotify's 6pt)
- Gap between tiles: 16pt
- Structure:
  - Art with 12pt radius, `rgba(0,0,0,0.08)` 0 4 16 shadow (light only — dark mode has no shadow)
  - Title: SF Pro Text 15pt weight 600 primary label, 2-line clamp
  - Subtitle (artist or description): SF Pro Text 13pt weight 400 secondary label, 1-line
- Tap: scale 0.96 with `.selection` haptic, opens album / playlist detail

**Hero Card (Listen Now)**
- Width: full width − 32pt horizontal margin (hero is 358pt wide on 390pt iPhone)
- Height: 380-420pt depending on content
- Structure:
  - Full-bleed editorial image with 16pt corner radius and a subtle shadow
  - Overlay gradient if text needs contrast: `rgba(0,0,0,0.3)` bottom 40%
  - Micro-label on top-left (uppercase caption 2 "NEW RELEASE" in white)
  - Centered or bottom-aligned hero text: SF Pro Rounded 24-34pt bold
  - Optional CTA pill at bottom
- Tap: opens editorial feature or album

**Track Row**
- Height: 56pt
- Layout: 44×44pt square album thumbnail (8pt corner radius) → stacked title/artist → trailing ellipsis
- Pressed: background `rgba(0,0,0,0.05)` light / `rgba(255,255,255,0.1)` dark
- Title: SF Pro Text 17pt weight 400 primary
- Artist: SF Pro Text 15pt weight 400 secondary
- Currently-playing track: title stays primary, but a 3-bar red equalizer animation appears to the left of the thumbnail (same motif as Spotify but Apple Music red)
- Explicit tag: small `E` in a gray 18pt rounded rectangle before the title if explicit
- Dolby Atmos / Lossless badge: trailing right of the title on supported tracks

**Album / Playlist Detail Hero**
- Top: 240pt album artwork centered with `rgba(0,0,0,0.1)` 0 8 24 shadow
- Title below: SF Pro Display 22pt weight 700 primary
- Artist: SF Pro Text 17pt weight 400 `#FA2D48` (red — this is the one place artist names go red, and it's an interactive link)
- Metadata: "Album · 2024 · Apple Digital Master" 13pt weight 400 tertiary
- Action row:
  - Large red Play button (44pt circle) with "Play" label SF Pro Text 17pt weight 600
  - Shuffle Play secondary button (44pt, same treatment but gray background)
  - Download / More icons trailing
- Below: track list scrolling down

**Now Playing (Full Screen)**
- Background: album-art-derived gradient (see Depth section)
- Large album art centered: 340pt × 340pt with `rgba(0,0,0,0.3)` 0 20 40 shadow
- When track changes: artwork cross-fades with a subtle scale animation
- Below artwork:
  - Track title SF Pro Display 18pt weight 600
  - Artist name SF Pro Text 15pt weight 400 at 70% opacity on primary label
- Scrubber with timestamps
- Control row: previous, 64pt red Play button, next — 32pt gap between controls
- Below: Volume slider, then AirPlay button, Lyrics button, share
- Bottom: up-next queue sheet indicator

**Mini-Player**
- Position: floats above tab bar, full width, 64pt tall
- Background: `.regularMaterial` blur with `rgba(255,255,255,0.8)` light / `rgba(28,28,30,0.8)` dark tint
- 44×44pt album art (8pt radius) leading, 12pt inset
- Center: track title 15pt weight 500 primary (1-line), artist 13pt weight 400 secondary (1-line)
- Trailing: play/pause 22pt SF Symbol (`play.fill` / `pause.fill`), then forward 22pt SF Symbol — 16pt gap between
- Tap anywhere: opens full Now Playing screen with matched-geometry artwork transition
- Swipe down while Now Playing is open: dismisses back to mini-player

**Lyrics View (The Signature Moment)**
- Activated from the Now Playing lyrics button
- Vertical scrolling stream of lyric lines
- Background: album-art-derived gradient, heavier than Now Playing
- Current line: SF Pro Display 28pt weight 700 bright primary (scales up from 22pt as it becomes active)
- Upcoming lines: 22pt weight 600 at 60% opacity, scrolled from below
- Past lines: 22pt weight 600 at 30% opacity, faded above
- Word-by-word highlight: within the current line, each word brightens as it's being sung (feature introduced for Apple's time-synced lyrics)
- Auto-scroll follows the song; tap on a line to jump to that timestamp
- Tap outside lyrics to return to Now Playing

**Station / Radio Card**
- Unique rectangular card (not square) with 16pt radius
- Height: 180pt
- Background: the station's branded artwork, full-bleed
- Overlay: gradient + station name + description in white
- Examples: Apple Music 1, Apple Music Country, personal stations

**Browse / Genre Grid**
- 2-column grid of genre tiles
- Each tile: rectangular (1.4:1 aspect), 16pt radius, genre-specific gradient background
- Centered type: SF Pro Rounded 22pt bold white

### Navigation

**Top Nav (Standard)**
- Height: 44pt + safe area (uses iOS large-title transition — 96pt when expanded at top, collapses to 44pt on scroll)
- Background: transparent at top, `.regularMaterial` with tint on scroll
- Large title (when expanded): SF Pro Display 34pt weight 700 primary
- Compact title (when scrolled): SF Pro Text 17pt weight 600 center
- Leading: settings / profile gear icon 28pt circle avatar
- Trailing: search icon or contextual action

**Bottom Tab Bar**
- Height: 49pt + safe area (standard iOS tab bar)
- Background: `.regularMaterial` over `rgba(255,255,255,0.8)` light / `rgba(0,0,0,0.8)` dark
- 5 tabs: **Home** (Listen Now) → **New** (Browse) → **Radio** → **Library** → **Search**
- Icon size: 25pt SF Symbol, 49pt hit area
- Active: `#FA2D48` (Apple Music Red — interesting choice, since the tab indicator uses the brand red not system blue)
- Inactive: label secondary color
- Labels: SF Pro Text 10pt weight 500, shown always

**Search Bar (Search Tab)**
- Top of Search tab, large-title style
- Input: 36pt tall, corner radius 10pt, background `#E5E5EA` light / `#1C1C1E` dark
- Leading `magnifyingglass` SF Symbol 17pt label secondary
- Placeholder: "Artists, Songs, Lyrics, and More" SF Pro Text 17pt weight 400 label secondary

**Library Folder / Category Row**
- Icon-based list: each row has a leading 28pt SF Symbol (circle filled colored background)
- Examples: Playlists, Artists, Albums, Songs, Made for You, Downloaded
- 56pt row height, 8pt corner radius on the icon's colored square background
- Trailing: chevron `chevron.right` 15pt label secondary

### Input Fields

**Search Bar**
- See Navigation above

**Comment / Message (in Apple Music social features)**
- Light fill, 10pt corner radius, SF Pro Text 17pt input type

### Distinctive Components

**Dolby Atmos Badge**
- Small pill, background `#D4A857` (gold) with a subtle gradient shine
- Text "Dolby Atmos" SF Pro Text 11pt weight 600 white
- Appears on track rows, album detail, and Now Playing for Atmos-supported tracks
- Padding: 4pt vertical, 8pt horizontal, 500pt corner radius (full pill)
- Optional glow: `rgba(212,168,87,0.5)` 0 0 8 around the badge on Now Playing

**Lossless Badge**
- Similar to Atmos but silver: `#8E8E93` background, white text "Lossless" 11pt weight 600
- Shown alongside Atmos or solo

**Time-Synced Lyrics View**
- The signature Apple Music feature
- Covered in "Lyrics View" above
- Word-by-word highlight uses per-word timestamps from Apple's lyric provider; the implementation crossfades an overlay on the word being sung

**Equalizer Animation**
- Same 3-bar motif as Spotify but rendered in `#FA2D48`
- Appears on currently-playing track rows to the left of the album thumbnail
- Pauses when playback is paused

**Now Playing Expanding Transition**
- Mini-bar artwork shares geometry with Now Playing artwork using matched-geometry
- Now Playing scales up from bottom to fill screen over 0.35s spring
- Canvas fades from tab bar canvas to album-art gradient

**Album-Art Color Extraction**
- Apple uses a Core Image `CIAreaAverage` extraction plus a second-pass to identify a complementary color
- The Now Playing gradient uses both: primary color at top, complementary at bottom
- Extraction happens at track-change time and is cached per track

**"Up Next" Queue Sheet**
- From Now Playing, swipe up on the track title or tap the queue icon
- Opens a sheet showing the upcoming queue
- Each row: album art + title/artist + drag handle for reordering
- Swipe left on a row: "Delete" action
- "Add to Library" button below each track

## 5. Layout Principles

### Spacing System
- Base unit: 4pt (iOS standard)
- Scale: 4, 8, 12, 16, 20, 24, 32, 44, 56, 72
- Standard margin: 16pt horizontal on content, 20pt on grouped list sections
- Row heights: 44pt (minimum touch), 56pt (track row), 64pt (mini-player), 80pt+ (editorial cards)
- Between horizontal tiles in a shelf: 16pt

### Grid & Container
- Grid: full width with 16pt margins; content edges at 16pt inset
- Horizontal shelves: 16pt inset with last-tile peek
- Album/playlist tile grid: 2-column square grid, 16pt gap
- iPad: 3-column on portrait, 4-column on landscape

### Whitespace Philosophy
- **Generous in Library and Listen Now**: The editorial experience — hero cards, section headers with room to breathe
- **Tight in track lists**: Playlist detail shows 56pt rows tightly packed
- **Extra-generous on Now Playing**: The centered artwork uses ~65% of viewport; breathing room everywhere else

### Border Radius Scale
- Square (0pt): Not used — Apple Music always softens
- Signature (4pt): Rarely used; Apple Music prefers ≥ 8pt
- Standard (8pt): Small buttons, badges, small covers on track rows
- Pronounced (10pt): Search bar, cards
- Signature (12pt): Album / playlist tiles — THE Apple Music corner
- Hero (16pt): Listen Now editorial cards, Now Playing large art on expansion
- Full Pill (500pt): Dolby Atmos badge, filter chips
- Circle (50%): Play button, profile avatars, downloaded progress ring

## 6. Depth & Elevation

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Track rows, list content |
| Subtle Card (Level 1) | `rgba(0,0,0,0.05) 0 2px 8px` (light only) | Album tiles, small cards |
| Standard Card (Level 2) | `rgba(0,0,0,0.08) 0 4px 16px` (light only) | Album detail hero, playlist art |
| Prominent Card (Level 3) | `rgba(0,0,0,0.1) 0 8px 24px` (light only) | Listen Now hero cards, Now Playing artwork |
| Now Playing Art | `rgba(0,0,0,0.3) 0 20px 40px` | Centered album art on Now Playing full screen |
| Sheet (Level 4) | `rgba(0,0,0,0.25) 0 -12px 32px` | Bottom sheets, Queue sheet |
| Mini-Player Material | `.regularMaterial` blur + `rgba(255,255,255,0.8)` tint | Mini-player floating above tab bar |
| Tab Bar Material | `.regularMaterial` + soft tint | Floating translucent tab bar |
| Modal Dim | `rgba(0,0,0,0.4)` + backdrop-blur | Modal dimming |

**Shadow Philosophy**: Apple Music uses shadows more generously than other media apps because the light mode canvas is pure `#FFFFFF` — tiles need shadow to read as raised cards. In dark mode, shadows disappear entirely; instead, surface-color steps (`#000000` → `#1C1C1E` → `#2C2C2E`) carry the elevation. The Now Playing artwork carries the most prominent shadow in the app — a 40pt blur at 30% opacity that makes the art feel like it's floating in space. Materials (blurs) are used extensively for nav, tab bar, and mini-player, giving the app that signature iOS translucency feel.

### Motion
- **Play button tap**: Scale 0.92 spring + `.impact(.soft)` haptic
- **Track change on Now Playing**: Artwork cross-fades with subtle scale (0.98 → 1.0) over 400ms
- **Mini-player → Full Now Playing**: Shared-element artwork scales from 44pt → 340pt, 0.35s spring damping 0.8
- **Equalizer bars**: Continuous animation on currently-playing row (same as Spotify but red)
- **Lyrics scroll**: Each new line scales from 22pt to 28pt and brightens from 60% to 100% over 300ms, synchronized with audio
- **Like heart tap**: Heart fills red, 1.0 → 1.2 → 1.0 bounce over 300ms with `.success` haptic
- **Shuffle toggle**: Icon color transitions from secondary to red with 200ms ease; `.selection` haptic
- **Download progress**: Circular arc sweeps around the download icon, fills as download progresses
- **Large title to compact title**: Standard iOS large-title scroll behavior — 34pt shrinks to 17pt as user scrolls up
- **Long press on tile**: Scale 0.96 + haptic, context menu springs in with blur dim
- **Browse tab color swap**: Tapping a genre tile animates a full-screen color wash transitioning to the genre's theme

## 7. Do's and Don'ts

### Do
- Use SF Pro exclusively — Apple Music is Apple's flagship first-party app; resist adding custom fonts
- Use `#000000` as the dark canvas — full-contrast black, not `#121212`
- Use 12pt corner radius on album art tiles — larger than most media apps, Apple Music's signature
- Reserve Apple Music Red (`#FA2D48`) for the Play button, Now Playing indicator, heart, shuffle active, and scrubber fill
- Use iOS system materials (`.regularMaterial`) for nav, tab bar, and mini-player backgrounds
- Use SF Symbols for every icon — never ship a custom glyph that has an SF Symbol equivalent
- Respect Dynamic Type at all sizes — Apple Music is one of the best-behaved apps for AX5
- Extract album-art colors for Now Playing gradient using Core Image
- Implement time-synced lyrics with word-by-word highlight when lyric data is available
- Show Dolby Atmos and Lossless badges on supported tracks
- Use iOS haptics: `.soft` on Play, `.success` on heart like, `.selection` on shuffle / tab
- Use iOS large-title navigation with scroll-collapse behavior
- Use system blue `#007AFF` for inline links (artist names in descriptions), NOT Apple Music red

### Don't
- Don't use a custom font — SF Pro covers every need in Apple Music
- Don't use `#121212` or warmer dark canvases — Apple Music goes full `#000000`
- Don't use 6pt corner radius on album art — 12pt is signature, softer
- Don't use Apple Music Red for secondary actions, borders, or generic accents — use system blue, gray, or label colors
- Don't roll your own Play button when SF Symbol `play.fill` exists
- Don't disable Dynamic Type — Apple Music is a poster-child app for accessibility
- Don't flatten materials to opaque backgrounds — the translucency is part of the app's identity
- Don't hide the mini-player during active playback — it's persistent UX
- Don't use italic text — Apple Music never uses italic; emphasis comes from weight / color / size
- Don't animate aggressively — iOS motion is subtle, 250–400ms ranges, damping 0.7–0.85
- Don't show the Dolby Atmos badge on non-Atmos tracks — it's a capability indicator, not a theme
- Don't use red for the link-to-artist text globally — artist name on album detail IS red, but inline links in description should be system blue

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Tile width 150pt; large title shrinks to 28pt faster on scroll |
| iPhone 13/14/15 | 390pt | Standard tile 160pt; hero cards 358pt wide |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in top nav |
| iPhone 15/16 Pro Max | 430pt | Tile 180pt; Now Playing art scales to 360pt |
| iPad portrait | 768pt | 3-column grid, hero cards 2-up, Now Playing sheet covers ~70% height |
| iPad landscape | 1024pt+ | 4-column grid, Now Playing overlay instead of full-screen |

### Dynamic Type
- **Full Dynamic Type support from XS to AX5**: every text style except the Play button icon, scrubber thumb, and mini-player geometry scales with user preference
- Listen Now hero titles: scale up to ~40pt at AX5
- Track titles / artist: scale with headline / subheadline text styles
- Mini-player: has a fallback layout that shows only artwork + play when at AX5 (text becomes too large to fit)
- Lyrics: current-line text scales up to 36pt at AX5

### Orientation
- Home / Search / Library / Radio: **portrait-locked** on iPhone
- Now Playing: **rotates to landscape** — album art centers, controls shift to the right side
- Lyrics view: **portrait-locked** (lyric rendering is vertical)
- Video content (Apple Music TV / music video): rotates to landscape
- iPad: supports all orientations with adaptive layouts

### Touch Targets
- Play button (Now Playing): 64pt — way over minimum
- Play button (inline): 44pt (iOS minimum)
- Track rows: 56pt tall, full-width tappable
- Tab bar icons: 25pt SF Symbol, 49pt hit area
- Ellipsis / overflow: 44pt hit

### Safe Area Handling
- Top: standard iOS large-title nav respects safe area / Dynamic Island
- Bottom: mini-player floats above tab bar; both respect home indicator
- Now Playing: artwork respects safe area top, controls respect bottom
- Lyrics view: top safe area for header, bottom safe area respected

## 9. Agent Prompt Guide

### Quick Color Reference
- Light canvas: `#FFFFFF`
- Dark canvas: `#000000`
- Surface 1 (light): `#F2F2F7`
- Surface 1 (dark): `#1C1C1E`
- Surface 2 (dark): `#2C2C2E`
- Divider (light): `#C6C6C8`
- Divider (dark): `#38383A`
- Label primary (light): `#000000`
- Label primary (dark): `#FFFFFF`
- Label secondary (light): `#3C3C43` at 60% opacity
- Label secondary (dark): `#EBEBF5` at 60% opacity
- Apple Music Red: `#FA2D48`
- Apple Music Coral: `#FC3C44`
- Red pressed: `#D4213B`
- Dolby Atmos gold: `#D4A857`
- Lossless silver: `#8E8E93`
- iOS system blue: `#007AFF`

### Example Component Prompts
- "Create a SwiftUI Apple Music Play button: 64pt circular button, background `#FA2D48`. SF Symbol `play.fill` in white 28pt weight bold. Pressed: scale 0.92 with `.impact(.soft)` haptic. Used as the primary playback control on the Now Playing screen, flanked by 32pt skip-backward and skip-forward SF Symbols with 32pt spacing."
- "Build an Apple Music album tile: 1:1 square image with 12pt corner radius (Apple Music's signature softer corner, not Spotify's 6pt). Light mode shadow `rgba(0,0,0,0.08) 0 4px 16px`, no shadow in dark mode. Below the art: title in SF Pro Text 15pt weight 600 primary label (2-line clamp), subtitle artist in 13pt weight 400 secondary label (1-line). 16pt gap between tiles. Tap: scale 0.96 with `.selection` haptic."
- "Design the Apple Music mini-player: 64pt tall, positioned above the tab bar, full width. Background: `.regularMaterial` blur with `rgba(255,255,255,0.8)` tint in light mode / `rgba(28,28,30,0.8)` in dark. 44×44pt album art (8pt radius) leading with 12pt inset. Center: track title SF Pro Text 15pt weight 500 primary (1-line) + artist 13pt weight 400 secondary (1-line). Trailing: 22pt `play.fill` / `pause.fill` SF Symbol, then 22pt `forward.fill` SF Symbol, 16pt gap. Tap: opens Now Playing with shared-element artwork transition."
- "Build the Apple Music Now Playing full-screen: background is a vertical linear gradient extracted from the album art's dominant color at top transitioning to a darker complementary at bottom. 340×340pt album art centered with `rgba(0,0,0,0.3) 0 20px 40px` shadow. Below: track title SF Pro Display 18pt weight 600 primary, artist 15pt weight 400 at 70% opacity. Scrubber with 4pt track `rgba(0,0,0,0.1)`, `#FA2D48` fill, 16pt white circular thumb. Controls row: `backward.fill` 32pt + 64pt red Play button + `forward.fill` 32pt with 32pt gaps. Below controls: volume slider and row of secondary icons (AirPlay, lyrics, share)."
- "Create the Apple Music time-synced lyrics view: vertical stream of lyric lines over album-art gradient background. Current line: SF Pro Display 28pt weight 700 bright primary label (scales up from 22pt as it becomes active). Upcoming lines: 22pt weight 600 at 60% opacity. Past lines: 22pt weight 600 at 30% opacity. Within the current line, each word highlights from secondary to primary as it's sung (word-by-word time-synced). Auto-scroll follows the song; tap a line to jump to that timestamp."
- "Design a Dolby Atmos badge: small pill, background `#D4A857` with a subtle internal gradient for shine, text 'Dolby Atmos' SF Pro Text 11pt weight 600 white. Padding 4pt vertical / 8pt horizontal, 500pt corner radius. On Now Playing, add a gentle `rgba(212,168,87,0.5) 0 0 8` glow. Only show on tracks that support Dolby Atmos — this is a capability indicator, not a theme."
- "Build the Apple Music bottom tab bar: 49pt + safe area, `.regularMaterial` blur over `rgba(255,255,255,0.8)` tint light / `rgba(0,0,0,0.8)` dark. 5 tabs: Home (house), New (music.note.list), Radio (dot.radiowaves.left.and.right), Library (square.stack), Search (magnifyingglass). Icon size 25pt SF Symbol. Active tint: `#FA2D48` (Apple Music Red — distinctive choice). Inactive: label secondary. Labels SF Pro Text 10pt weight 500 always shown."

### Iteration Guide
1. SF Pro throughout — never add a custom font; use iOS text styles (Headline, Body, Subheadline, Footnote, Caption) where possible
2. Dark canvas is true black `#000000`, not `#121212` — unique among media apps
3. Apple Music Red `#FA2D48` is the primary accent — Play button, heart, shuffle active, scrubber fill, tab indicator
4. Album tiles use 12pt corner radius — signature softer-than-Spotify corner
5. Hero cards on Listen Now use 16pt radius and SF Pro Rounded type for editorial feel
6. Dolby Atmos badge is gold `#D4A857`, Lossless is silver `#8E8E93` — capability indicators, not themes
7. Time-synced lyrics with word-by-word highlight is a defining feature — implement when lyric data is available
8. iOS system materials (`.regularMaterial`) for nav, tab bar, mini-player — lean into iOS translucency
9. Mini-player persists above tab bar during playback — matches iOS platform convention
10. Now Playing uses album-art-extracted gradient background — Core Image `CIAreaAverage` + complementary-color derivation
11. Haptics: `.soft` on Play, `.success` on heart like, `.selection` on shuffle / tab / chip, `.light` on button tap
12. Respect Dynamic Type from XS to AX5 — Apple Music is a best-in-class accessibility app
13. Large-title nav pattern is standard — 34pt shrinks to 17pt on scroll
14. Shadows are generous in light mode on tiles and cards; dark mode uses surface-color steps instead
