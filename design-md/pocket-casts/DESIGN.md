# Design System Inspiration of Pocket Casts (iOS)

## 1. Visual Theme & Atmosphere

Pocket Casts is the connoisseur's podcast app, and its iOS design reflects that: a confident, dark-first interface where the **circular cover-art motif**, the **single bold Red accent** (`#F43E37`), and a **per-podcast color theme** sampled from each show's artwork combine into something instantly recognizable. Open the app and you land on a grid of subscribed podcasts — square artwork tiles on a near-black canvas (`#1A1A1A`, not pure black) — then tap any show and the entire screen subtly re-tints to a color drawn from that podcast's cover. Pocket Casts Red is the brand constant (tab-bar selection, the Subscribe button, unplayed badges), but the moment you start listening, the Now Playing screen becomes a small bespoke environment colored by the show you chose.

The signature screen is the **full-screen Now Playing player**: a large rounded-corner cover image (often shown with the brand's circular "play ring" motif), the episode title and show name, a thin scrubber with a circular knob, and a transport row anchored by a big circular play button. Below the transport sits a row of action chips — Up Next, playback speed, Trim Silence, Star, Mark Played, Effects, AirPlay. The scrubber fill, the play button, and any active chip all take the **theme tint** (here `#E0533C`), while the canvas and chrome stay monochrome so the cover art and the colored controls do the talking. The second-most-iconic surface is the **episode list** — date-stamped rows with a small artwork thumbnail, a one-line title, duration/remaining metadata, and a circular play/queue button on the trailing edge.

Pocket Casts' personality is "precise, premium, dark." There are no playful gradients in the chrome, no skeuomorphism — just crisp Inter-style type (SF Pro on device), generous tap targets, pill-shaped buttons, and a disciplined Red. The most distinctive structural decisions are: (1) **circular geometry** — the play button, the speed/skip controls, episode-row play buttons, and the historic logo are all circles; (2) **per-podcast theming** — the app extracts a dominant color from cover art and applies it as the screen accent so each show feels like its own space; (3) **the Up Next queue** — a persistent, reorderable play queue surfaced from the Now Playing screen and the mini-player; and (4) **Trim Silence + Volume Boost** — power-user playback effects exposed as first-class toggles, a defining Pocket Casts feature.

**Key Characteristics:**
- Near-black canvas (`#1A1A1A`) dark / paper-white (`#FFFFFF`) light — dark is the canonical theme
- Pocket Casts Red (`#F43E37`) as the single brand accent — tab selection, Subscribe, unplayed badge
- Per-podcast theme tint sampled from cover art — re-colors scrubber, play button, active chips
- Circular geometry everywhere — play button, transport controls, episode-row play buttons, logo
- Full-screen Now Playing player with large cover art + thin scrubber + circular transport
- Up Next queue — persistent, reorderable, surfaced from player and mini-player
- Trim Silence + Volume Boost — power-user playback effects as first-class toggles
- Episode list rows — date eyebrow, artwork thumb, one-line title, duration meta, circular action
- Filters — smart episode lists (New Releases, In Progress, Starred) as a dedicated tab
- Mini-player — a persistent bottom strip above the tab bar with artwork, title, play/pause
- Pill-shaped buttons (Subscribe, Play Episode) with full 999pt corner radius
- Discover tab — curated podcast charts, networks, and categories

## 2. Color Palette & Roles

### Primary (Interactive)
- **Pocket Casts Red** (`#F43E37`): The brand accent — tab-bar selected state, Subscribe button, unplayed-episode dot, primary CTAs, brand logomark.
- **Red Pressed** (`#D32B25`): Pressed/active state of Red buttons.
- **Theme Tint (per-podcast)** (`#E0533C` example): A color extracted from the open podcast's cover art. Applied to the Now Playing scrubber fill, the circular play button, and active action chips. Varies per show — this doc uses a warm coral as the representative example.

### Canvas & Surfaces (Dark Mode — canonical)
- **Dark Canvas** (`#1A1A1A`): Primary dark canvas — Pocket Casts' signature near-black, NOT pure `#000000`.
- **Dark Surface 1** (`#232323`): Cards, list backgrounds, settings groups, elevated rows.
- **Dark Surface 2** (`#2E2E2E`): Scrubber track, inactive toggle track, pressed rows, chips.
- **Dark Divider** (`#383838`): Hairline separators between episode rows and setting rows.

### Canvas & Surfaces (Light Mode)
- **Canvas White** (`#FFFFFF`): Primary light canvas.
- **Surface Gray Light** (`#F2F2F4`): Cards, grouped list backgrounds.
- **Surface Pressed Light** (`#E6E6E8`): Pressed rows.
- **Divider Light** (`#E2E2E4`): Hairline separators.

### Text
- **Text Primary Dark** (`#FFFFFF`): Episode titles, screen titles, transport icons on dark.
- **Text Secondary Dark** (`#B8B8B8`): Show names, duration, supporting metadata.
- **Text Tertiary Dark** (`#757575`): Date eyebrows, disabled, very low-emphasis.
- **Text Primary Light** (`#1A1A1A`): Primary text on light.
- **Text Secondary Light** (`#6B6B6B`): Secondary text on light.

### Accent & Semantic
- **Info Blue** (`#03A9F4`): Links, informational highlights, "New" network labels.
- **Played Green** (`#78D549`): Played/complete checkmark, success states, download-complete tick.
- **Star Gold** (`#FBC02D`): Starred-episode indicator.
- **Error Red** (`#F43E37`): Reuses brand Red for destructive/error (delete download, remove from Up Next).
- **Warning Amber** (`#FFB300`): Storage-warning, sync-conflict banners.
- **Download Blue** (`#03A9F4`): Active-download progress ring.

### Theme Tint Examples (per-podcast accents — illustrative)
Pocket Casts derives a tint from each show's cover art. Representative palette:

| Show Type | Theme Tint | Used For |
|-----------|-----------|----------|
| Warm / news | `#E0533C` | Scrubber, play button, active chips |
| Cool / tech | `#3E7BC2` | Scrubber, play button, active chips |
| Green / nature | `#3FA66A` | Scrubber, play button, active chips |
| Purple / culture | `#7E57C2` | Scrubber, play button, active chips |
| Pink / lifestyle | `#D6457E` | Scrubber, play button, active chips |

## 3. Typography Rules

### Font Family
- **iOS System**: `SF Pro Text` / `SF Pro Display` (San Francisco) — Pocket Casts uses the system face on iOS for native rendering and Dynamic Type.
- **Web / Documentation Fallback**: `Inter` (by Rasmus Andersson, SIL OFL) — the closest free face for static specs and previews.
- **Fallback Stack**: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'Inter', 'Segoe UI', Roboto, Helvetica, Arial, sans-serif`
- **Numerals**: Tabular figures for timecodes and durations so digits don't shift while scrubbing.

### Hierarchy

| Role | Font | Size | Weight | Line Height | Letter Spacing | Notes |
|------|------|------|--------|-------------|----------------|-------|
| Screen Title | SF Pro Display | 32pt | 800 | 1.2 | -0.4pt | "Podcasts", "Discover" large nav title |
| Now Playing Episode | SF Pro Display | 22pt | 700 | 1.25 | -0.3pt | Episode title on player |
| Section Header | SF Pro Display | 22pt | 700 | 1.3 | -0.2pt | "Up Next", "New Releases" |
| Episode Title | SF Pro Text | 18pt | 700 | 1.3 | -0.1pt | Episode-detail title |
| List Row Title | SF Pro Text | 15pt | 600 | 1.35 | 0pt | Episode-row title (1 line, truncated) |
| Body | SF Pro Text | 16pt | 400 | 1.5 | 0pt | Show notes, descriptions |
| Show Name | SF Pro Text | 14pt | 500 | 1.4 | 0pt | Show name under episode title (theme tint) |
| Metadata | SF Pro Text | 14pt | 400 | 1.4 | 0pt | Duration, "42 min left", "Downloaded" |
| Date Eyebrow | SF Pro Text | 12pt | 700 | 1.3 | 0.6pt | "SUN · OCT 13" uppercase row eyebrow |
| Eyebrow / Player Label | SF Pro Text | 12pt | 700 | 1.0 | 1.0pt | "NOW PLAYING" uppercase |
| Timecode | SF Mono / SF Pro | 12pt | 500 | 1.0 | 0pt | "14:22", "-19:48" tabular |
| Button | SF Pro Text | 16pt | 700 | 1.0 | 0pt | Subscribe / Play Episode |
| Chip Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | "Up Next", "Trim", "1.2×" |
| Tab Label | SF Pro Text | 10pt | 600 | 1.0 | 0.1pt | Bottom tab labels |
| Caption | SF Pro Text | 12pt | 400 | 1.4 | 0pt | Small print, sync timestamps |

### Principles
- **Tabular timecodes**: All durations and scrubber timecodes use tabular figures so they don't jitter as time advances.
- **One-line episode titles in lists**: Episode-row titles truncate to a single line with ellipsis; the full title appears on the detail/player screen.
- **Weight concentrated at 400 / 500 / 600 / 700 / 800**: Regular body, medium for show names, semibold for row titles, bold for episode/section titles, heavy for the large nav title.
- **Uppercase eyebrows**: Date stamps and the "NOW PLAYING" label are uppercase with positive tracking — the only uppercase type in the system.
- **Dynamic Type first-class**: Titles, body, metadata scale; tab labels, chip labels, timecodes stay fixed for layout stability.

## 4. Component Stylings

### Buttons

**Primary Button (Subscribe)**
- Shape: Pill, 999pt corner radius
- Background: `#F43E37` (Pocket Casts Red)
- Text: `#FFFFFF`, SF Pro Text 16pt weight 700
- Padding: 14pt vertical, 28pt horizontal
- Pressed: background `#D32B25` + scale 0.98
- After subscribe: collapses to a "Subscribed ✓" ghost/outline state

**Theme-Tint Button (Play Episode)**
- Shape: Pill, 999pt corner radius
- Background: per-podcast theme tint (e.g. `#E0533C`)
- Text: `#FFFFFF`, SF Pro Text 16pt weight 700
- Used for the primary play action on a podcast/episode detail screen
- Pressed: tint darkened 12% + scale 0.98

**Secondary / Outline Button (Mark Played / Download)**
- Background: transparent
- Border: 1.5pt `#B8B8B8` (dark) / `#9A9A9A` (light)
- Text: `#FFFFFF` dark / `#1A1A1A` light, SF Pro Text 14pt weight 600
- Corner radius: 999pt (pill)
- Pressed: 8% white overlay (dark) / 6% black overlay (light)

**Text Button (Show all episodes)**
- Text: `#F43E37`, SF Pro Text 14pt weight 700
- No background, no underline
- Pressed: opacity 0.6

**Circular Transport Button (Play / Pause)**
- Size: 68pt diameter (main play), 44pt (skip/effects)
- Background: theme tint (play), transparent (skip)
- Icon: 26pt white triangle/pause-bars (play); 30pt stroked skip glyph
- Shadow: `rgba(theme, 0.55) 0 8px 24px -6px` under the play button only
- Pressed: scale 0.94

**Episode-Row Play Button**
- Size: 30pt circle
- Border: 1.5pt `#B8B8B8`
- Icon: 12pt filled triangle `#FFFFFF`
- On play: morphs to a small pause/progress ring in theme tint

### Cards & Tiles

**Podcast Grid Tile**
- Square cover art, 8pt corner radius
- Grid: 3 columns on iPhone, 8pt gutter
- Unplayed badge: `#F43E37` count pill, top-right, 18pt circle
- Pressed: scale 0.97 + 8% dim

**Episode List Row**
- Height: ~80pt
- Leading: 52pt artwork thumbnail, 6pt corner radius
- Date eyebrow: 12pt w700 uppercase `#757575`
- Title: 15pt w600 `#FFFFFF`, one line, ellipsis
- Metadata: 14pt w400 `#B8B8B8` ("34 min · ↓ Downloaded")
- Trailing: 30pt circular play/queue button
- Divider: 1pt `#383838` between rows
- Swipe actions: leading = Up Next (theme tint); trailing = Archive / Download

**Up Next Queue Row**
- Drag-reorder handle (≡) leading, 24pt `#757575`
- 40pt artwork thumb
- Title (1 line) + show name
- Trailing: remove (×) button
- Active reorder: row lifts with `rgba(0,0,0,0.4) 0 8px 16px` shadow

### Now Playing Player

**Layout (top → bottom)**
1. Top bar: collapse chevron (leading), "NOW PLAYING" eyebrow (center), ⋯ menu (trailing)
2. Cover art: large rounded-corner image, ~70% screen width, 8pt corner radius, theme-tint glow shadow
3. Episode title (22pt w700) + show name (14pt w500 theme tint)
4. Scrubber: 4pt track `#2E2E2E`, fill = theme tint, 13pt circular white knob; timecodes below (current / -remaining)
5. Transport row: skip-back-15, ⏮ previous, ◉ circular play (68pt, theme tint), ⏭ next, skip-forward-30
6. Action chip row: Up Next · Speed (1.2×) · Trim Silence · Star · Effects

**Scrubber**
- Track: 4pt height, `#2E2E2E`, 2pt corner radius
- Fill: theme tint, 2pt corner radius
- Knob: 13pt circle `#FFFFFF`, `rgba(0,0,0,0.5) 0 1px 4px` shadow
- Scrubbing: knob grows to 18pt; haptic tick on 1s boundaries

**Action Chip**
- Vertical icon (19pt stroked) + label (10pt w600) stack
- Inactive: `#B8B8B8`
- Active (Trim on, Up Next has items): theme tint
- Speed chip shows the numeric rate ("1.2×") instead of an icon when non-default

### Playback Effects (signature)

**Trim Silence Toggle**
- Row: label (15pt w600) + sub ("Shortens silent gaps in talk audio", 12pt `#B8B8B8`)
- Switch: 50×30pt, ON track `#F43E37`, OFF track `#2E2E2E`, 24pt white knob
- Three intensity levels when expanded: Mild / Medium / Mad Max

**Volume Boost Toggle**
- Same switch styling; sub "Levels quiet voices for noisy rooms"

**Playback Speed Control**
- Stepper from 0.5× to 3.0× in 0.1 increments; current value in a theme-tint pill

### Navigation

**Bottom Tab Bar**
- Height: 52pt + safe area
- Background: `rgba(26,26,26,0.94)` with `blur(20px)` + 0.5pt top border `#383838`
- Tabs (4): Podcasts, Filters, Discover, Profile
- Icon size: 22pt
- Active: `#F43E37` (filled glyph), Inactive: `#757575` (stroked glyph)
- No tint pill — color + fill change only
- Labels: SF Pro Text 10pt w600, always shown

**Mini-Player**
- Persistent strip directly above the tab bar (~64pt tall)
- Background: theme tint at full or as a slim accent bar; artwork thumb (44pt) + title + play/pause
- Tap: expands to full Now Playing with a 300ms upward sheet transition
- Swipe down on full player: collapses back to mini-player

**Top Nav (Podcast Detail)**
- Large title collapses to compact on scroll
- Leading: back chevron; Trailing: Settings gear + ⋯ menu

### Input Fields

**Search Bar (Discover / Filters)**
- Height: 40pt
- Background: `#2E2E2E` (dark) / `#EDEDED` (light)
- Corner radius: 10pt
- Leading `magnifyingglass` 16pt `#757575`
- Placeholder: "Search shows & episodes" 15pt `#757575`
- Focus: 1.5pt `#F43E37` border

**Filter Builder**
- Smart-filter rules (Episode status, Download status, Media type, Release date)
- Each rule a tappable row opening a sheet of options
- Filter color picker: choose accent for that custom filter (one of ~12 swatches)

### Distinctive Components

**Per-Podcast Theme Tint Engine**
- On opening a podcast, Pocket Casts extracts a dominant accent from the cover art
- That tint colors: scrubber fill, circular play button, active chips, links on the detail screen, the mini-player accent
- Falls back to brand Red `#F43E37` when extraction yields low-contrast color

**Circular Play Ring**
- The brand's recurring motif: a thick white ring with a play triangle — appears in the logo, empty states, and as a loading affordance

**Up Next Queue**
- Reorderable list; "Play Next" vs "Play Last" insert options from any episode's swipe/long-press
- Surfaced from the Now Playing chip and the mini-player

**Filters (Smart Episode Lists)**
- Prebuilt: New Releases, In Progress, Starred; plus user-built custom filters
- Each filter has its own accent color and icon, shown as colored cards in the Filters tab

**Listening History & Stats**
- "You've listened for 312 hours" style stat cards in Profile
- Time-saved-by-Trim-Silence and speed counters — a signature Pocket Casts flex

## 5. Layout Principles

### Spacing System
- Base unit: 4pt
- Scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64
- Episode-row vertical padding: 14pt
- Player horizontal inset: 36pt (cover art breathing room)
- Screen content inset: 16pt horizontal (lists), 20pt (player chrome)

### Grid & Container
- iPhone: podcast grid = 3 columns, 8pt gutter, 16pt outer margin
- iPad: podcast grid = 5–6 columns; player centered max-width 520pt
- Episode lists: full-width rows with 16pt inset, 1pt dividers
- Mini-player: full-width strip, edge-to-edge above tab bar

### Whitespace Philosophy
- **Art-forward**: cover artwork is given maximum size and breathing room; chrome is minimal so artwork and the theme tint dominate
- **Dense but tappable lists**: episode rows pack date + title + meta + action in ~80pt, but every actionable element has ≥44pt hit area
- **Player calm**: the Now Playing screen is uncluttered — art, title, scrubber, transport, chips, nothing else

### Border Radius Scale
- Square (0pt): dividers, full-bleed art on some headers
- Subtle (4pt): scrubber track/fill, small badges
- Small (6pt): episode-row artwork thumbnails
- Standard (8pt): cover art tiles, large Now Playing cover, cards
- Comfortable (12–16pt): bottom sheets, modals, search field
- Pill (999pt): all buttons (Subscribe, Play Episode, outline)
- Circle (50%): play button, transport controls, episode-row play, logo, avatars

## 6. Depth & Elevation

Pocket Casts is mostly flat; shadow is reserved for the play button glow, floating sheets, and drag-reorder.

| Level | Treatment | Use |
|-------|-----------|-----|
| Flat (Level 0) | No shadow | Canvas, list rows, settings groups |
| Tint Glow (Level 1) | `rgba(themeTint, 0.55) 0 8px 24px -6px` | Circular play button, large cover art glow |
| Card (Level 2) | `rgba(0,0,0,0.4) 0 8px 16px` | Drag-reorder lift in Up Next |
| Sheet (Level 3) | `rgba(0,0,0,0.5) 0 -6px 28px` | Bottom sheets (Effects, Up Next, sort menu) |
| Mini→Full | sheet slide 300ms | Mini-player expands to full Now Playing |
| Modal Overlay | `rgba(0,0,0,0.55)` | Dim behind sheets/modals |

**Shadow Philosophy**: The only "glow" in the app is the theme-tinted shadow under the circular play button and large cover — it makes the player's focal point feel alive and on-brand. Everything else is flat: dark canvas with hairline `#383838` dividers. On the dark theme, sheet shadows are weak, so sheets also get a slightly lighter `#232323` top surface to read as elevated.

### Motion
- **Play/Pause**: triangle↔bars icon morph 200ms ease-in-out; button scale 0.94 on press
- **Scrubber drag**: knob scales 13→18pt over 120ms; 1:1 finger tracking; soft haptic tick at boundaries
- **Mini-player → Full player**: upward sheet slide 300ms ease-out; artwork hero-transitions to the large cover
- **Theme-tint cross-fade**: on opening a new podcast, accent colors cross-fade over 250ms ease-out (no hard cut)
- **Episode-row swipe**: 1:1 reveal of swipe actions; commit threshold 50%; spring snap 250ms damping 0.85
- **Up Next reorder**: 60fps drag; neighbors shift 200ms ease-out; drop snaps 150ms + soft haptic
- **Tab change**: instant icon fill swap to Red; no slide
- **Download progress**: circular ring fills 0→360° tied to bytes; completes with a `#78D549` tick pop (150ms)
- **Pull-to-refresh (episode list)**: theme-tint spinner; 200ms settle
- **Haptic feedback**: soft impact on play, scrubber boundary ticks, reorder pickup/drop, toggle flip

## 7. Do's and Don'ts

### Do
- Use near-black `#1A1A1A` for the dark canvas — NOT pure `#000000`
- Keep Pocket Casts Red `#F43E37` as the single brand accent (tab selection, Subscribe, unplayed badge)
- Sample a per-podcast theme tint from cover art and apply it to scrubber, play button, active chips
- Make the play button and transport controls circular — circular geometry is core brand
- Give cover art maximum size and a subtle theme-tint glow on the Now Playing screen
- Use pill (999pt) corner radius for all buttons
- Show durations and timecodes in tabular figures so digits don't jitter
- Expose Trim Silence and Volume Boost as first-class toggles — they're signature features
- Surface Up Next from the player and mini-player as a reorderable queue
- Truncate episode-row titles to one line; show the full title on detail/player
- Cross-fade the theme tint when switching podcasts — never a hard color cut

### Don't
- Don't use pure black `#000000` as the canvas — `#1A1A1A` is the signature
- Don't introduce a second brand accent — Red is it; per-podcast tint is the only other color
- Don't make the play button rectangular — it must be a circle
- Don't add a tint pill behind the active tab — Pocket Casts uses color + fill change only
- Don't let chrome compete with cover art — keep surfaces monochrome
- Don't use sharp corners on buttons — everything is pill-shaped
- Don't hard-cut the theme tint when changing shows — always cross-fade 250ms
- Don't bury Trim Silence in deep settings — it belongs on the player chip row
- Don't wrap episode-row titles to multiple lines in lists — one line, ellipsis
- Don't animate aggressively — motion is quiet, 150–300ms ease ranges
- Don't use proportional figures for timecodes — they shift while scrubbing

## 8. Responsive Behavior

### Device Sizes
| Device | Width | Key Changes |
|--------|-------|-------------|
| iPhone SE (3rd gen) | 375pt | Podcast grid 3 cols; cover art ~62% width on player |
| iPhone 13/14/15 | 390pt | Standard layout; cover art ~70% width |
| iPhone 15/16 Pro | 393pt | Dynamic Island accommodated in player top bar |
| iPhone 15/16 Pro Max | 430pt | Larger cover art; grid stays 3 cols with wider tiles |
| iPad (portrait) | 768pt | Podcast grid 5 cols; player centered max 520pt with side padding |
| iPad (landscape) | 1024pt+ | Sidebar nav (Podcasts/Filters/Discover/Profile) + detail pane; player as right-side panel |

### Dynamic Type
- Screen title, episode title, body, metadata: full scale
- Tab labels, chip labels, timecodes, date eyebrows: FIXED (layout-sensitive)
- Scrubber and transport stay fixed-size; only text scales

### Orientation
- Player and lists support rotation
- iPad landscape adopts a 2-column split (list + detail) and a persistent right-edge player
- Cover art re-centers on rotation; aspect ratio preserved

### Touch Targets
- Circular play button: 68pt (well above minimum)
- Transport skip/effects: 44pt hit
- Episode-row play button: 30pt glyph, 44pt hit
- Tab bar icons: 22pt glyph, 44pt hit
- Toggle switches: 50×30pt control, 44pt row hit

### Safe Area Handling
- Top: player top bar respects safe area and Dynamic Island
- Bottom: mini-player + tab bar + home indicator respected
- Mini-player sits directly above the tab bar inside the safe area
- Sides: 16pt list inset; player chrome 20pt

## 9. Agent Prompt Guide

### Quick Color Reference
- Canvas (dark): `#1A1A1A`
- Canvas (light): `#FFFFFF`
- Surface 1: `#232323`
- Surface 2: `#2E2E2E`
- Divider: `#383838`
- Pocket Casts Red: `#F43E37`
- Red pressed: `#D32B25`
- Theme tint (example): `#E0533C`
- Text primary: `#FFFFFF` dark / `#1A1A1A` light
- Text secondary: `#B8B8B8`
- Text tertiary: `#757575`
- Info blue: `#03A9F4`
- Played green: `#78D549`
- Star gold: `#FBC02D`

### Example Component Prompts
- "Build the Pocket Casts Now Playing screen in SwiftUI: near-black `#1A1A1A` canvas. Top bar — collapse chevron (leading), 'NOW PLAYING' eyebrow SF Pro 12pt w700 tracking 1pt `#B8B8B8` (center), ⋯ menu (trailing). Large cover art ~70% width, 8pt corner radius, with a `rgba(224,83,60,0.55) 0 8px 24px -6px` glow. Episode title SF Pro 22pt w700 `#FFFFFF`, show name SF Pro 14pt w500 in theme tint `#E0533C`. Scrubber: 4pt track `#2E2E2E`, fill `#E0533C`, 13pt white circular knob, timecodes below in tabular 12pt `#B8B8B8`. Transport row: skip-back-15, previous, 68pt circular play button (`#E0533C` bg, white triangle), next, skip-forward-30."

- "Create a Pocket Casts episode list row: 80pt tall, leading 52pt artwork thumbnail 6pt corner radius. Date eyebrow SF Pro 12pt w700 uppercase tracking 0.6pt `#757575` ('SUN · OCT 13'). Title SF Pro 15pt w600 `#FFFFFF`, one line truncated. Metadata SF Pro 14pt w400 `#B8B8B8` ('34 min · ↓ Downloaded'). Trailing 30pt circular play button with 1.5pt `#B8B8B8` border and 12pt white filled triangle. 1pt `#383838` bottom divider. Leading swipe = Up Next (theme tint), trailing swipe = Archive."

- "Render the Pocket Casts bottom tab bar: 52pt + safe area, `rgba(26,26,26,0.94)` background with 20px blur and 0.5pt `#383838` top border. 4 tabs — Podcasts, Filters, Discover, Profile. 22pt icons. Active = `#F43E37` filled glyph; inactive = `#757575` stroked glyph. No tint pill. Labels SF Pro 10pt w600 always shown."

- "Build the Pocket Casts Trim Silence row: label 'Trim Silence' SF Pro 15pt w600 `#FFFFFF`, sub 'Shortens silent gaps in talk audio' SF Pro 12pt `#B8B8B8`. Trailing switch 50×30pt: ON track `#F43E37`, OFF track `#2E2E2E`, 24pt white knob, 200ms slide. 1pt `#383838` bottom divider."

- "Create the Pocket Casts mini-player: persistent strip ~64pt above the tab bar, theme-tint `#E0533C` accent. Leading 44pt artwork thumb, episode title SF Pro 14pt w600 `#FFFFFF` (1 line) + show name SF Pro 12pt `#B8B8B8`, trailing circular play/pause 22pt. Tap expands to full Now Playing with a 300ms upward sheet slide; artwork hero-transitions to the large cover."

- "Render a Pocket Casts podcast grid: 3 columns, 8pt gutter, 16pt outer margin. Each tile = square cover art, 8pt corner radius. Unplayed count badge: `#F43E37` 18pt circle, top-right, white SF Pro 11pt w700. Press = scale 0.97 + 8% dim."

### Iteration Guide
1. Canvas is near-black `#1A1A1A` in dark (canonical), pure white `#FFFFFF` in light — NEVER pure black
2. Pocket Casts Red `#F43E37` is the single brand accent — tab selection, Subscribe, unplayed badge
3. The Now Playing screen re-tints to a per-podcast color sampled from cover art (e.g. `#E0533C`) — scrubber, play button, active chips
4. Circular geometry is core — play button, transport, episode-row play, logo are all circles
5. Buttons are pill-shaped (999pt corner radius), not rounded rectangles
6. Cover art is given maximum size with a subtle theme-tint glow; chrome stays monochrome
7. Trim Silence + Volume Boost are first-class toggles on the player chip row — signature features
8. Up Next is a reorderable queue surfaced from the player and the mini-player
9. Timecodes and durations use tabular figures so digits don't jitter while scrubbing
10. The theme tint cross-fades 250ms when switching shows — never a hard color cut
